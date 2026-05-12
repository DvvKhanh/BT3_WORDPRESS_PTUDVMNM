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

- Kết quả: http:http://192.168.91.154:8082/?p=13
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/749994ab-f8df-4d92-9c2d-6070d49b5963" />

- Sửa giao diện
  + Chọn giao diện -> sửa giao diện
<img width="1920" height="1200" alt="d" src="https://github.com/user-attachments/assets/1c59d38d-084b-4f0c-8674-34119322ca63" />

- Chỉnh sửa giao diện
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/c3084146-b7f1-4ac9-946c-d5adee1c3b02" />

- Kết quả sau khi chỉnh sửa: http://192.168.91.154:8082
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/ac4b2459-2d0f-49e8-b1c4-baccc0615032" />

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

<img width="1480" height="312" alt="image" src="https://github.com/user-attachments/assets/02bce41c-6040-4ac4-8bb9-b8fc264aece9" />

### Tạo Tunel
- Chạy lệnh: ```cloudflared tunnel create wordpress```
- Kết quả hiển thị
<img width="1476" height="173" alt="image" src="https://github.com/user-attachments/assets/e5ec2587-93b3-4dbb-a493-7ce5b38a8efe" />

### Tạo file config
- Tạo file: ```mkdir -p ~/.cloudflared```
- Chạy lệnh: ```nano ~/.cloudflared/config-bai3.yml```
- Nội dung file:
```
tunnel: d73954b9-b9bd-4e09-8de7-a3b70402a1ff

credentials-file: /home/khanh/.cloudflared/d73954b9-b9bd-4e09-8de7-a3b70402a1ff.json

ingress:
  - hostname: wordpress.khanh123.id.vn
    service: http://localhost:8082

  - service: http_status:404
```
- Kiểm tra file: ```cat ~/.cloudflared/config-bai3.yml```
<img width="1037" height="257" alt="image" src="https://github.com/user-attachments/assets/17057aab-a1b2-45d7-b2fb-5df6d963129f" />

### Tạo DNS
- Chạy lệnh: ```cloudflared tunnel route dns wordpress wordpress.khanh123.id.vn```
<img width="1472" height="101" alt="image" src="https://github.com/user-attachments/assets/df18d367-532e-4882-bd6b-358b5178f848" />

- Kiểm tra DNS trên Cloudflare
<img width="1536" height="960" alt="e" src="https://github.com/user-attachments/assets/8911f7c4-57cf-49ec-9590-37b15f35d3d1" />

### Chạy Tunnel
- Chạy tunnel: ```cloudflared tunnel --config ~/.cloudflared/config-bai3.yml run wordpress```
<img width="1476" height="755" alt="image" src="https://github.com/user-attachments/assets/828edf26-353d-4df2-8a7f-41275bceae52" />

 ## 9. Truy cập website
 - Truy cập: https://wordpress.khanh123.id.vn
 - Bài viết giới thiệu bản thân
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/77dce527-6feb-4b21-aaed-23d8db800e95" />

 - Bài viết giới thiệu ngành học yêu thích
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/985aabd2-a58f-4263-b170-c7805e5871a0" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/791450a5-5657-4336-8fab-8977d212f75f" />

- Video giới thiệu ngành
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/72526d98-909c-4980-963b-6857f98ab7f2" />

 ## 10. Nhận xét
 WordPress là một hệ quản trị nội dung (CMS – Content Management System) mã nguồn mở rất phổ biến hiện nay. Trong quá trình thực hiện bài tập triển khai WordPress bằng Docker trên Ubuntu, em nhận thấy WordPress có nhiều ưu điểm nổi bật nhưng cũng tồn tại một số hạn chế nhất định.
### 1. Về mức độ dễ sử dụng
- WordPress có giao diện quản trị trực quan, dễ thao tác và thân thiện với người dùng. Sau khi cài đặt xong, người dùng có thể tạo website mà gần như không cần phải lập trình nhiều. Các chức năng như tạo bài viết, thêm hình ảnh, video, chỉnh sửa giao diện hay tạo menu đều được thực hiện thông qua giao diện web đơn giản.
- Ngoài ra, WordPress có kho plugin và theme rất phong phú. Người dùng có thể dễ dàng cài đặt thêm các chức năng như:
  + Tạo form liên hệ.
  + Tạo slideshow.
  + Tối ưu SEO.
  + Tạo giao diện đẹp.
  + Bảo mật website.
  + Tích hợp mạng xã hội,…
- Chỉ với vài thao tác cài plugin là website đã có thêm nhiều chức năng mới mà không cần viết code từ đầu.
- Tuy nhiên, đối với người mới bắt đầu thì việc cấu hình Docker, MariaDB, phpMyAdmin và Cloudflare Tunnel ban đầu có thể hơi khó vì phải làm việc với Linux terminal và các lệnh Docker. Sau khi hệ thống chạy ổn định thì việc quản trị WordPress lại khá dễ dàng.

### 2. Về công sức triển khai
- Nếu cài WordPress theo cách thông thường thì phải cài riêng:
  + Apache/Nginx.
  + PHP.
  + MySQL/MariaDB,.
  + phpMyAdmin.
- Quá trình này tương đối mất thời gian và dễ xảy ra lỗi cấu hình.
- Tuy nhiên, khi sử dụng Docker Compose thì việc triển khai trở nên nhanh hơn rất nhiều. Chỉ cần viết file docker-compose.yml là có thể khởi tạo toàn bộ hệ thống bằng một lệnh:
```docker-compose up -d```
- Docker giúp:
  + Quản lý service dễ hơn.
  + Dễ backup.
  + Dễ di chuyển sang máy khác.
  + Hạn chế lỗi xung đột môi trường.
- Trong quá trình thực hiện, phần tốn nhiều công sức nhất là:
  + Xử lý lỗi container.
  + Xử lý xung đột port.
  + Cấu hình Cloudflare Tunnel.
  + Chỉnh sửa giao diện website.
- Nhìn chung, việc triển khai bằng Docker giúp giảm đáng kể thời gian cài đặt so với cách thủ công.

### 3. Về hiệu năng và tài nguyên hệ thống
- WordPress không quá nặng nếu chỉ dùng các chức năng cơ bản. Tuy nhiên, khi chạy cùng:
  + MariaDB
  + phpMyAdmin
  + Docker Engine

-> thì hệ thống sẽ tiêu tốn một lượng RAM và CPU nhất định.

- Trong quá trình chạy thực tế:
  + MariaDB sử dụng khoảng vài trăm MB RAM.
  + WordPress container sử dụng khoảng 200–400MB RAM.
  + phpMyAdmin khá nhẹ.
  + Docker Engine cũng sử dụng thêm tài nguyên hệ thống.
- Nếu cài thêm nhiều plugin hoặc theme nặng thì RAM và CPU sẽ tăng lên đáng kể. Máy cấu hình thấp có thể bị chậm khi:
  + Cài plugin.
  + Upload media.
  + Chỉnh sửa giao diện.
  + Chạy nhiều container cùng lúc.
- Đối với máy ảo Ubuntu, nên cấp:
  + Tối thiểu 2GB RAM.
  + 2 CPU cores

-> để hệ thống hoạt động ổn định.

### 4. Về ưu điểm của WordPress
- Một số ưu điểm nổi bật:
  + Miễn phí và mã nguồn mở.
  + Cộng đồng sử dụng rất lớn.
  + Có nhiều tài liệu hướng dẫn.
  + Dễ tạo website nhanh chóng.
  + Hỗ trợ nhiều plugin và theme.
  + Có thể tạo blog, website cá nhân, bán hàng, tin tức, học tập,...
  + Dễ quản trị nội dung.
  + Có thể triển khai bằng Docker rất thuận tiện.
- Ngoài ra, việc sử dụng Cloudflare Tunnel giúp public website lên Internet dễ dàng mà không cần:
  + Mở port modem.
  + Mua VPS.
  + Cấu hình NAT phức tạp.

### 5. Về nhược điểm của WordPress
- Bên cạnh ưu điểm, WordPress cũng có một số hạn chế:
  + Dễ bị lỗi nếu plugin xung đột nhau.
  + Nếu cài quá nhiều plugin sẽ làm website chậm.
  + Cần cập nhật thường xuyên để tránh lỗ hổng bảo mật.
  + Một số theme và plugin đẹp yêu cầu trả phí.
  + Tốn RAM hơn website tĩnh HTML đơn giản.
  + Khi dùng Docker cần hiểu cơ bản về Linux và container.
- Ngoài ra, nếu website có lượng truy cập lớn thì cần:
  + Tối ưu database
  + Cache
  + CDN
  + Hosting mạnh hơn.

### 6. Đánh giá tổng quan
- Qua bài thực hành, em nhận thấy WordPress là một giải pháp rất phù hợp để xây dựng website nhanh chóng, đặc biệt với người không chuyên lập trình web. Việc kết hợp WordPress với Docker giúp quá trình triển khai hiện đại, dễ quản lý và thuận tiện hơn rất nhiều.
- Mặc dù vẫn có một số khó khăn trong quá trình cấu hình ban đầu, nhưng nhìn chung WordPress là nền tảng mạnh mẽ, dễ sử dụng và phù hợp cho nhiều mục đích khác nhau như:
  + Website cá nhân.
  + Blog.
  + Website trường học.
  + Bán hàng.
  + Giới thiệu doanh nghiệp.
- Đây là một công cụ rất hữu ích trong học tập cũng như triển khai website thực tế.
