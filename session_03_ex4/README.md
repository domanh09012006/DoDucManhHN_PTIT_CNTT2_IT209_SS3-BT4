# Bài 4: Chạy song song nhiều cổng dịch vụ với Nginx

## 1. Mục tiêu

Cấu hình Nginx chạy đồng thời hai website trên cùng một server và cùng một địa chỉ IP nhưng sử dụng hai port khác nhau:

* **Port 8080:** Beta App
* **Port 8090:** Internal App

## 2. Cấu trúc thư mục trên server

```text
/var/www/
├── beta-app/
│   └── html/
│       └── index.html
└── internal-app/
    └── html/
        └── index.html
```

## 3. Cấu hình Nginx

File cấu hình: `/etc/nginx/sites-available/multi-port.conf`

Cấu hình gồm hai server block:
* Port 8080 sử dụng thư mục `/var/www/beta-app/html`
* Port 8090 sử dụng thư mục `/var/www/internal-app/html`

Tạo symbolic link kích hoạt site:

```bash
ln -s /etc/nginx/sites-available/multi-port.conf /etc/nginx/sites-enabled/multi-port.conf
```

Kiểm tra cấu hình:

```bash
nginx -t
```

Reload lại dịch vụ Nginx:

```bash
systemctl reload nginx
```

## 4. Cấu hình Firewall

Mở hai port 8080 và 8090 bằng UFW:

```bash
ufw allow 8080/tcp
ufw allow 8090/tcp
```

Kiểm tra trạng thái tường lửa:

```bash
ufw status verbose
```

**Kết quả hiển thị:**

```text
8080/tcp    ALLOW IN    Anywhere
8090/tcp    ALLOW IN    Anywhere
```

## 5. Kiểm tra hoạt động

### Kiểm tra Beta App

```bash
curl http://221.121.3.177:8080
```

**Kết quả:**

```text
Welcome to Beta App
This is the Beta testing application.
```

### Kiểm tra Internal App

```bash
curl http://221.121.3.177:8090
```

**Kết quả:**

```text
Welcome to Internal App
This is the Internal application.
```

## 6. Truy cập trên trình duyệt

* **Beta App:** [http://221.121.3.177:8080](http://221.121.3.177:8080)
* **Internal App:** [http://221.121.3.177:8090](http://221.121.3.177:8090)

## 7. Kết quả

Hai website đã chạy song song trên cùng một server:

| Website | Port | Thư mục gốc |
| :--- | :--- | :--- |
| **Beta App** | `8080` | `/var/www/beta-app/html` |
| **Internal App** | `8090` | `/var/www/internal-app/html` |

Cấu hình hoàn thành và đã kiểm tra thành công bằng `curl` và `ufw`.