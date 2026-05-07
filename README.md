# PHÁT TRIỂN ỨNG DỤNG VỚI MÃ NGUỒN MỞ
# BÀI TẬP 3: WORDPRESS
# Họ tên: Đậu Văn Khánh
# MSSV: K225480106099
# Lớp: K58KTP

# ĐỀ BÀI
## SỬ DỤNG WORDPRESS ĐỂ TẠO WEB SITE
### 1. SỬ DỤNG DOCKER TRÊN UBUNTU ĐỂ TẠO docker ccompose chứa:
- Mariadb: sử dụng image: mariadb:latest để làm hệ quản trị csdl cho wordpress.
- Phpmyadmin: sư dụng image: phpmyadmin:latest để đăng nhập vào mariadb rồi tạo csdl trống (chỉ để xem, ko cần tạo bảng từ đây, wordpress sẽ làm hết).
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
- Tạo thư mục: ```mkdir wordpress-project```
- Di chuyển vào thư mục: ```cd wordpress-project```
<img width="492" height="90" alt="image" src="https://github.com/user-attachments/assets/2ef03425-1b8b-40a6-9cdf-3733b03b0b99" />

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
<img width="1476" height="753" alt="image" src="https://github.com/user-attachments/assets/ec079bd1-400f-4554-898e-8ce9e5e23d8c" />

## 5. Khởi động hệ thống
- Chạy Docker Compose: ```docker-compose up -d```
<img width="856" height="122" alt="image" src="https://github.com/user-attachments/assets/42b31c05-9e0b-4c7a-9b43-dffba4fe4e4c" />

- Kiểm tra container: ```docker ps```
<img width="1471" height="380" alt="image" src="https://github.com/user-attachments/assets/5b2feb1a-2191-4cd7-87d8-14aaabc5dd8c" />

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
<img width="1017" height="536" alt="1" src="https://github.com/user-attachments/assets/4af0d280-4bfb-4132-a066-ed9ebd13c8ba" />

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

- Đăng nhập
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/5f7575f9-c422-41f0-b107-6f44ab39c68f" />
