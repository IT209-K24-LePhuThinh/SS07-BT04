# BT04 - Cấu hình Reverse Proxy Nginx cho ứng dụng Spring Boot

## Tạo trang tĩnh

```html
<!-- /var/www/html/index.html -->
<h1>Le Phu Thinh</h1>
<p>Ma lop: IT209-K24</p>
```

## Cấu hình Nginx

```nginx
# /etc/nginx/sites-available/spring-proxy.conf
server {
    listen 80;
    server_name _;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:8082/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/spring-proxy.conf /etc/nginx/sites-enabled/spring-proxy.conf
sudo nginx -t
sudo systemctl reload nginx
```

## Kiểm tra

```bash
$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

$ curl -I http://localhost/
HTTP/1.1 200 OK
Server: nginx/1.24.0
Content-Type: text/html

$ curl -I http://localhost/api/health
HTTP/1.1 200 OK
Content-Type: application/json
```