# **Achitechture**
## **Kiến trúc web**
Web sử dụng:\
    - `PHP`: Backend\
    - `Apache`: Web server\
    - `MariaDB`: Database

## **Kiến trúc thư mục**
Ta sẽ xem kiến trúc thư mục dưới dạng cây bằng lệnh 

```bash
cd /var/www/html/DVWA
tree -L 2
```

```text
.
├── about.php
├── CHANGELOG.md
├── compose.yml
├── config/
│   ├── config.inc.php
│   ├── config.inc.php.bak
│   └── config.inc.php.dist
├── COPYING.txt
├── database/
│   ├── bac_setup.sql
│   ├── create_mssql_db.sql
│   ├── create_oracle_db.sql
│   ├── create_postgresql_db.sql
│   ├── create_sqlite_db.sql
│   ├── sqli.db
│   └── sqli.db.dist
├── Dockerfile
├── docs/
│   ├── DVWA_v1.3.pdf
│   ├── graphics/
│   └── pdf.html
├── dvwa/
│   ├── css/
│   ├── images/
│   ├── includes/
│   └── js/
├── external/
│   └── recaptcha/
├── favicon.ico
├── hackable/
│   ├── flags/
│   ├── uploads/
│   └── users/
├── index.php
├── instructions.php
├── login.php
├── logout.php
├── phpinfo.php
├── php.ini
├── README.ar.md
├── README.es.md
├── README.fa.md
├── README.fr.md
├── README.id.md
├── README.it.md
├── README.ko.md
├── README.md
├── README.pl.md
├── README.pt.md
├── README.ru.md
├── README.tr.md
├── README.uk.md
├── README.vi.md
├── README.zh.md
├── robots.txt
├── SECURITY.md
├── security.php
├── security.txt
├── setup.php
├── tests/
│   ├── README.md
│   └── test_url.py
└── vulnerabilities/
    ├── api/
    ├── authbypass/
    ├── bac/
    ├── brute/
    ├── captcha/
    ├── cryptography/
    ├── csp/
    ├── csrf/
    ├── exec/
    ├── fi/
    ├── help.css
    ├── help.js
    ├── javascript/
    ├── open_redirect/
    ├── sqli/
    ├── sqli_blind/
    ├── upload/
    ├── view_help.php
    ├── view_source.php
    ├── view_source_all.php
    ├── weak_id/
    ├── xss_d/
    ├── xss_r/
    └── xss_s/
```

## **Khám phá từng thư mục, file của web**

### 1. about.php
<img src="./images/2026-07-09-20-36-25.png" style="display: block; margin: 0 auto;">

Là trang giới thiệu của web\
Đây là trang web dễ bị tấn công, dành riêng cho việc kiểm thử và học tập

### 2. CHANGELOG.md
<img src="./images/2026-07-09-20-38-26.png" style="display: block; margin: 0 auto;">

Đây là file ghi lại chi tiết các bản cập nhật của ứng dụng

### 3. compose.yml
<img src="./images/2026-07-09-20-39-56.png" style="display: block; margin: 0 auto;">

Đây là file cấu hình dành cho `DOCKER`\
Nó dùng để khai báo cách chạy DVWA bằng Docker, ví dụ:
```text
- Chạy container web DVWA
- Chạy database cần thiết
- Mở port để truy cập web
- Gắn volume nếu cần lưu dữ liệu
- Khai báo biến môi trường
```

Nếu dùng Docker để cài thì có thể dùng lệnh:
```bash
docker compose up -d
```

> Nhưng khi cài trực tiếp trên Ubuntu thì file này không quan trọng cho lắm

### 4. config
#### 4.1 config.inc.php

<img src="./images/2026-07-09-20-52-32.png" style="display: block; margin: 0 auto;">


**Những biến trong này sẽ có luồng hoạt động:**
1. Apache nhận request.
2. Apache thấy đây là file `.php`
3. Apache chuyển file cho `PHP`
4. PHP thực thi `login.php`
5. `login.php` include `config.inc.php`
6. `config.inc.php` tạo biến: `$DBMS = 'MySQL';`
7. `DVWA` dùng `$DBMS` để kết nối database.
8. `PHP` trả `HTML` về cho `Apache`
9. `Apache` gửi `HTML` cho trình duyệt.

---

```PHP
$DBMS = getenv('DBMS') ?: 'MySQL';
```
- Dòng này gán cho biến `$DBMS` bằng biến môi trường có tên là `DBMS` nếu không có thì lấy trực tiếp `MySQL`
- Dù web được cài bằng `MariaDB` nhưng nó lại tự động tương thích với `MySQL` nên ứng dụng vẫn có thể chạy bình thường

- Dùng lệnh 
```bash
grep -rn "$DVWA" /var/www/html/DVWA
```
có thể tìm được những file nào chứa biến `$DVWA`

---
```bash
$_DVWA = array();
$_DVWA[ 'db_server' ]   = getenv('DB_SERVER') ?: '127.0.0.1';
$_DVWA[ 'db_database' ] = getenv('DB_DATABASE') ?: 'dvwa';
$_DVWA[ 'db_user' ]     = getenv('DB_USER') ?: 'dvwa';
$_DVWA[ 'db_password' ] = getenv('DB_PASSWORD') ?: 'dvwa123';
$_DVWA[ 'db_port']      = getenv('DB_PORT') ?: '3306';
```

- Tạo 1 mảng `_DVWA` chứa các thông tin như **IP/Domain server**, **tên DB**, **user/pass**, **port**

- Những file khác sẽ có chỗ sử dụng:
```php
mysqli_connect(
    $_DVWA['db_server'],
    $_DVWA['db_user'],
    $_DVWA['db_password'],
    $_DVWA['db_database'],
    $_DVWA['db_port']
);
```
---

```php
$_DVWA[ 'recaptcha_public_key' ]  = getenv('RECAPTCHA_PUBLIC_KEY') ?: '';
$_DVWA[ 'recaptcha_private_key' ] = getenv('RECAPTCHA_PRIVATE_KEY') ?: '';
```

Phần này dùng cho phần `CAPTCHA`

---
```php
$_DVWA[ 'default_security_level' ] = getenv('DEFAULT_SECURITY_LEVEL') ?: 'low';
```
- Phần này để set level mặc định 

---
```php
$_DVWA[ 'default_locale' ] = getenv('DEFAULT_LOCALE') ?: 'en';
```
- Set ngôn ngữ mặc định

---
```php
$_DVWA[ 'disable_authentication' ] = getenv('DISABLE_AUTHENTICATION') ?: false;
```
- Bật tắt xác thực khi vào `DVWA`

---
```php
$_DVWA['SQLI_DB'] = getenv('SQLI_DB') ?: MYSQL;
```

Thay đổi DB của web chỉ áp dụng với 2 bài `SQLi` và `Blind SQLi` mà không thay đổi DB của những bài Lab khác

## 5. COPPY.txt
<img src="./images/2026-07-09-21-41-16.png" style="display: block; margin: 0 auto;">

File này viết về phần bản quyền

## 6. database
### 6.1 bac_setup.sql
<img src="./images/2026-07-09-21-43-35.png" style="display: block; margin: 0 auto;">

Phần này tạo DB của lab `Broken Access Control`

### 6.2 create_mssql_db.sql
<img src="./images/2026-07-09-21-45-32.png" style="display: block; margin: 0 auto;">

File này tạo bảng `users` chứa các user trong Lab với `MS SQL`

### 6.3 create_oracle_db.sql
<img src="./images/2026-07-09-21-47-15.png" style="display: block; margin: 0 auto;">

Cũng tạo bảng `users` nhưng trong **Oracle**

### 6. 4 sqli.db
<img src="./images/2026-07-09-21-50-17.png" style="display: block; margin: 0 auto;">

File này cũng tạo `users` nhưng trong **SQLite**\
> Vì nó là byte nhị phân nên không thể xem trực tiếp bằng các trình edit văn bản thông thường

## 7. Dockerfile

<img src="./images/2026-07-09-21-57-51.png" style="display: block; margin: 0 auto;">

Dùng để tự động hóa việc cài môi trường, Lab

## 8. docs
<img src="./images/2026-07-09-21-58-41.png" style="display: block; margin: 0 auto;">

Folder này chủ yếu để giới thiệu về DVWA

## 9. recaptcha.php
<img src="./images/2026-07-09-22-01-21.png" style="display: block; margin: 0 auto;">

- Nhận token reCAPTCHA từ form.
- Gửi token đó cùng Private Key tới Google.
- Nhận kết quả xác minh từ Google.
- Trả về true hoặc false để DVWA quyết định có cho phép người dùng tiếp tục hay không.

## 10.favicon.ico
Đây là ảnh biểu tượng của trang web hiển thị

## 11. hackable/
### 11.1 /flag/fi.php
<img src="./images/2026-07-14-21-20-24.png" style="display: block;>

- `fi` có thể hiểu là File Inclusion.
- Chứa nhiều flag phục vụ bài thực hành.
- Không cho truy cập trực tiếp.
- Yêu cầu được include từ trang khác.
- Minh họa nhiều cách ẩn dữ liệu:
  - Plain text
  - PHP echo
  - Ghi đè biến
  - Base64
  - HTML comment

### 11.2 hackable/uploads/dvwa_email.png
<img src="./images/2026-07-14-21-25-05.png" style="display: block; margin: 0 auto;">

- Thư mục này chứa những file được người dùng upload lên thành công

### 11.3 /users
<img src="./images/2026-07-14-21-26-10.png" style="display: block; margin: 0 auto;">

Thư mục này chứa tài nguyên ảnh của các tài khoản tương ứng khi ta khai thác và đăng nhập thành công vào người dùng đó

## 12. index.php
<img src="./images/2026-07-14-21-27-22.png" style="display: block; margin: 0 auto;">

## 13. instructions.php
<img src="./images/2026-07-14-21-28-11.png" style="display: block; margin: 0 auto;">

## 14. login.php
<img src="./images/2026-07-14-21-28-49.png" style="display: block; margin: 0 auto;">

## 15. phpinfo.php
<img src="./images/2026-07-14-21-29-30.png" style="display: block; margin: 0 auto;">

## 16. php.ini
<img src="./images/2026-07-14-21-30-23.png" style="display: block; margin: 0 auto;">

## 17. security.php
<img src="./images/2026-07-14-21-31-03.png" style="display: block; margin: 0 auto;">

Cho phép chỉnh level của lab

## 18. setup.php
<img src="./images/2026-07-14-21-32-08.png" style="display: block; margin: 0 auto;">

Dùng để xem những cấu hình cho DVWA

## 19. vulnerabilities/ 
Chứa những source của các lỗ hổng

