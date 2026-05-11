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

👉 Tạo bài viết 1: Giới thiệu bản thân
- Tạo nội dung: Chọn menu Bài viết (Posts) -> Thêm bài viết -> Xuất bản
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/514e8be1-b538-480d-8e5f-4dd6e788945d" />

<img width="1536" height="960" alt="w" src="https://github.com/user-attachments/assets/fd99bb3e-cae1-42ad-baaf-28d97825a727" />

- Kết quả: http://192.168.91.154:8082/?p=7
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/711aaa1b-9148-4fce-928b-aaae3bb4f44c" />

👉 Tạo bài viết 2 — Giới thiệu ngành học
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/8486114e-8fa6-4123-a3c4-fe258fb78067" />

- Kết quả: http://192.168.91.154:8082/?p=12
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/749994ab-f8df-4d92-9c2d-6070d49b5963" />

- Sửa giao diện
  + Chọn giao diện -> sửa giao diện
<img width="1920" height="1200" alt="d" src="https://github.com/user-attachments/assets/1c59d38d-084b-4f0c-8674-34119322ca63" />

- Chỉnh sửa giao diện
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/b7015ff7-81d5-4535-a74d-60b5811a63e4" />

- Kết quả sau khi chỉnh sửa: http://192.168.91.154:8082
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/2b6c3745-e072-43d7-9284-6052dc386432" />

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

## 8. Public bằng Cloudflare Tunnel
### Cài Cloudflare trên Ubuntu
- Download về: ```wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb```
<img width="1470" height="755" alt="image" src="https://github.com/user-attachments/assets/61cce9d7-9823-48d1-92b6-c215d5344b25" />

- Cài: ```sudo dpkg -i cloudflared-linux-amd64.deb```
- Kiểm tra: ```cloudflared --version```
<img width="931" height="242" alt="image" src="https://github.com/user-attachments/assets/782a3b04-6e00-4102-a74d-2deaafa393d4" />

### Đăng nhập Cloudflare
- Chạy: ```cloudflared tunnel login```
- Ubuntu hiện link: https://dash.cloudflare.com/argotunnel?aud=&callback=https%3A%2F%2Flogin.cloudflareaccess.org%2FZqlA8KB8WO_UVqOi8H7vXaZRhgoQO5uusbxzSIkdIn8%3D
- Copy link và mở trình duyệt chạy.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0c1d785e-ad1e-4e79-ab0b-8fded807c84c" />

- Cloudflare sẽ hỏi: Authorize Cloudflared -> chọn domain -> nhấn Authorize
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/7639c85d-9408-4ec4-8699-ed8e421d7c62" />

### Tạo Tunel
- Chạy lệnh: ```cloudflared tunnel create pawnshop```
- Kết quả hiển thị
<img width="1476" height="173" alt="image" src="https://github.com/user-attachments/assets/e5ec2587-93b3-4dbb-a493-7ce5b38a8efe" />

### Tạo file config
- Tạo file: ```mkdir -p ~/.cloudflared```
- Chạy lệnh: ```nano ~/.cloudflared/config-bai3.yml```
- Nội dung file:
```
tunnel: 2f70c1f5-d203-4b62-b635-de2fc5fa4561

credentials-file: /home/khanh/.cloudflared/2f70c1f5-d203-4b62-b635-de2fc5fa4561.json

ingress:
  - hostname: wordpress.khanh123.id.vn
    service: http://localhost:8082

  - service: http_status:404
```
- Kiểm tra file: ```cat ~/.cloudflared/config-bai3.yml```
<img width="1065" height="266" alt="image" src="https://github.com/user-attachments/assets/471c9014-84fd-4c44-9d96-5f391632a45c" />

### Tạo DNS
- Chạy lệnh: ```cloudflared tunnel route dns wordpress wordpress.khanh123.id.vn```
<img width="1472" height="101" alt="image" src="https://github.com/user-attachments/assets/df18d367-532e-4882-bd6b-358b5178f848" />

- Kiểm tra DNS trên Cloudflare
<img width="1536" height="960" alt="e" src="https://github.com/user-attachments/assets/8911f7c4-57cf-49ec-9590-37b15f35d3d1" />

### Chạy Tunnel
- Chạy tunnel: cloudflared tunnel --config ~/.cloudflared/config-bai3.yml run wordpress
