# PHÁT TRIỂN ỨNG DỤNG VỚI MÃ NGUỒN MỞ
# BÀI TẬP 3: WORDPRESS
# Họ tên: Đậu Văn Khánh
# MSSV: K225480106099
# Lớp: K58KTP

# ĐỀ BÀI
## SỬ DỤNG WORDPRESS ĐỂ TẠO WEB SITE
### 1. SỬ DỤNG DOCKER TRÊN UBUNTU ĐỂ TẠO docker ccompose chứa:
- Mariadb: sử dụng image: mariadb:latest để làm hệ quản trị csdl cho wordpress.
- Phpmyadmin: sử dụng image: phpmyadmin:latest để đăng nhập vào mariadb rồi tạo csdl trống (chỉ để xem, ko cần tạo bảng từ đây, wordpress sẽ làm hết).
- WordPress: Sử dụng image: wordpress:latest, truyền các tham số môi trường cho wordpress là các thông tin truy cập csdl mariadb, tạo bởi Phpmyadmin.
### 2. Yêu cầu: sau khi có 3 service này trong file docker-compose.yml :
- Cấu hình để hệ thống chạy.
- Sử dụng cloudflare tunnel để public web này lên 1 sub-domain.
- Tạo 1 bài viết trong wordpress giới thiệu về bản thân sinh viên: thông tin cá nhân, sở thích, ... bài viết có thể chứa hình ảnh, âm thanh, video, ...
- Tạo 1 bài viết trong wordpress giới thiệu về ngành học mà em yêu thích trong trường TNUT. bài viết phải chứa hình ảnh, video, ...
- Nhận xét việc sử dụng mã nguồn mở wordpress để tạo website (tốn công sức thế nào, dễ/khó dùng ra sao, tốn kém tài nguyên(ssh/ram) của máy chủ ra sao,....).

# BÀI LÀM
## 1. Kiến trúc hệ thống
```
Internet
   |
Cloudflare Tunnel
   |
Ubuntu Server
   |
Docker Compose
 ├── WordPress
 ├── MariaDB
 └── phpMyAdmin
```
## 2. Cài Docker
- Kiểm tra phiên bản Docker và Docker Compose
```
docker --version
docker-compose version
```
<img width="658" height="101" alt="image" src="https://github.com/user-attachments/assets/7a58d725-ba69-4aa1-8861-7c49f62b9aca" />

## 3. Tạo thư mục dự án
- Tạo thư mục: ```mkdir wordpress```
- Di chuyển vào thư mục: ```cd wordpress```
<img width="572" height="82" alt="image" src="https://github.com/user-attachments/assets/40ec5674-9aeb-4ca2-afa6-7e14c59c698d" />

## 4. Tạo file Docker-compose.yml
- Tạo file: ```nano docker-compose.yml```
- Nội dung file:
```
services:

  mariadb:
    image: mariadb:latest
    container_name: mariadb
    restart: always

    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: wordpressdb
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wp123

    volumes:
      - mariadb_data:/var/lib/mysql

    ports:
      - "3306:3306"

  phpmyadmin:
    image: phpmyadmin:latest
    container_name: phpmyadmin
    restart: always

    depends_on:
      - mariadb

    environment:
      PMA_HOST: mariadb
      MYSQL_ROOT_PASSWORD: root123

    ports:
      - "8081:80"

  wordpress:
    image: wordpress:latest
    container_name: wordpress
    restart: always

    depends_on:
      - mariadb

    environment:
      WORDPRESS_DB_HOST: mariadb:3306
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wp123
      WORDPRESS_DB_NAME: wordpressdb

    ports:
      - "8082:80"

    volumes:
      - wordpress_data:/var/www/html

volumes:
  mariadb_data:
  wordpress_data:
```
<img width="1472" height="756" alt="image" src="https://github.com/user-attachments/assets/b77d3e60-7516-4c09-a71d-1461bcfd3d72" />

## 5. Khởi động hệ thống
- Chạy Docker Compose: ```docker-compose up -d```
<img width="630" height="98" alt="image" src="https://github.com/user-attachments/assets/eb3efdd1-1e77-4820-84d6-4de2b846b039" />

- Kiểm tra container: ```docker ps```
<img width="1480" height="426" alt="image" src="https://github.com/user-attachments/assets/4e52c80e-a13a-4a83-8216-199e3a018bfe" />

- Giải thích:
```
| Container  | Chức năng        |
| ---------- | ---------------- |
| mariadb    | lưu database     |
| phpmyadmin | quản lý database |
| wordpress  | website          |
```

## 6. Kiểm tra Website Wordpress
- Xem IP Ubuntu: ```ip a```
<img width="1176" height="605" alt="q" src="https://github.com/user-attachments/assets/c4cf58e0-e494-40f8-8680-b04ce32c4ec6" />

- Mở trình duyệt, truy cập: http://192.168.91.154:8082
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/26a0772e-4b14-4391-b281-8e2e61783be0" />

- Chọn ngôn ngữ: Tiếng Việt -> Tiếp tục
- Điền thông tin website
  + Tiêu đề trang web: Website cá nhân của Khánh
  + Tên người dùng: khanh
  + Mật khẩu: Tùy ý
  + Email

👉 Nhấn Cài đặt Wordpress
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/a8ef0db2-130f-428e-85cc-e76d5e61ef36" />

- Cài đặt thành công
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/68e67493-a3db-4e43-9445-14fd437eaeff" />

- Đăng nhập vào WordPress
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/5f7575f9-c422-41f0-b107-6f44ab39c68f" />

- Giao diện WordPress
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/fdee5474-358c-4355-8444-5cd042f5de19" />

## 7. Kiểm tra PHPAdmin
- Mở trình duyệt, truy cập: http://192.168.91.154:8081/
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/16e2da47-a91e-402c-8c94-94c8a2b5c19e" />

- Đăng nhập vào PHPAdmin
```
| User | Password |
| ---- | -------- |
| root | root123  |
```

- Giao diện PHPAdmin sau khi đăng nhập
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/5264a6d5-e97b-4e36-9e43-cd59578eb8a8" />

- Kiểm tra Database wordpressdb
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/e4be7339-bf4d-4387-a207-9dda883b6464" />

## 8. Cài đặt Cloudflare
- Thêm respository
```
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloudflare-main.gpg
echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflare-main noble main' | sudo tee /etc/apt/sources.list.d/cloudflare-main.list
```
<img width="1472" height="102" alt="image" src="https://github.com/user-attachments/assets/27ddc49c-8bc0-44b6-8e15-00f9cfad34c2" />

- Cài Cloudflare
```
sudo apt update
sudo apt install cloudflared -y
```
<img width="1472" height="635" alt="image" src="https://github.com/user-attachments/assets/069d513c-2feb-496b-9ece-7bcdd077b143" />

- Kiểm tra: ```cloudflared --version```
<img width="712" height="82" alt="image" src="https://github.com/user-attachments/assets/ffcba35b-c310-47b7-b38c-d44fd9dc58bc" />

## 9. Đăng nhập Cloudflare
- Chạy lệnh: ```cloudflared tunnel login```
- Ubuntu hiện link: https://dash.cloudflare.com/argotunnel?aud=&callback=https%3A%2F%2Flogin.cloudflareaccess.org%2FZqlA8KB8WO_UVqOi8H7vXaZRhgoQO5uusbxzSIkdIn8%3D
- Copy link và mở trình duyệt chạy.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/d06a8c73-3cd7-4e4f-b758-e02f23dcba38" />

- Cloudflare sẽ hỏi: Authorize Cloudflared -> chọn domain -> nhấn Authorize
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0dbfc86d-7759-4c67-a16f-e15c86c7ddcd" />

## 10. Tạo Cloudflare
- Chạy lệnh: ```cloudflared tunnel create wordpress-tunnel```
<img width="1477" height="175" alt="image" src="https://github.com/user-attachments/assets/56011794-98e4-4d94-b5ef-e767c8111c2c" />

- Kiểm tra Tunnel: ```cloudflared tunnel list```
<img width="1220" height="173" alt="image" src="https://github.com/user-attachments/assets/8c335bf2-e029-4e65-97a0-7f34a1731315" />

## 11. Tạo file config
- Chạy lệnh: ```nano ~/.cloudflared/config.yml```
- Nội dung file:
```
tunnel: 2d42d3bb-3030-4ef2-ab57-bbf37e86a863

credentials-file: /home/khanh/.cloudflared/2d42d3bb-3030-4ef2-ab57-bbf37e86a863.json

ingress:
  - hostname: wordpress.khanh123.id.vn
    service: http://localhost:8082

  - service: http_status:404
```

- Tao DNS cho Tunnel
  + Chạy lệnh: ```cloudflared tunnel route dns wordpress-tunnel wordpress.khanh123.id.vn```
<img width="1473" height="102" alt="image" src="https://github.com/user-attachments/assets/b9fd924b-8706-4084-84b8-b90c3c46affe" />

- Chạy Tunnel: ```cloudflared tunnel run wordpress-tunnel```
<img width="1477" height="672" alt="image" src="https://github.com/user-attachments/assets/84f2e324-0b95-44f4-b857-9551b64aa314" />

- Lưu ý:
  + Không được tắt Terminal.
  + Nếu tắt -> Web sẽ off.
