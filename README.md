# Nginx dijadikan reverse proxy di Ubuntu 20.04

1. #### Lakukan update
```sh
  sudo apt update
```

2. #### Install nginx
```sh
  sudo apt install nginx
```

3. #### Hapus default di /etc/nginx/sites-enabled/
```sh
  cd /etc/nginx/sites-enabled/
  sudo rm default
```

4. #### Buat file konfigurasi nginx di /etc/nginx/sites-available/
```sh
  nano /etc/nginx/sites-available/contoh.conf
```

5. #### Isi file konfigurasi
```sh
  upstream web {
  server 172.16.24.252:80 weight=100 max_fails=5 fail_timeout=5;
  }

  server {
  # listen 443;
  server_name app1.contoh.com www.app1.contoh.com;
  listen 443 ssl http2;
  listen [::]:443 ssl http2;
  ssl_certificate         /etc/ssl/cert.pem;
  ssl_certificate_key     /etc/ssl/key.pem;
  
  location / {
  proxy_set_header X-Forwarded-Host $host;
  proxy_set_header X-Forwarded-Server $host;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_pass http://web/;
  }
  }

  upstream web1 {
  server 172.16.24.120:8080 weight=100 max_fails=5 fail_timeout=5;
  }

  server {
  #listen 80;
  server_name app2.contoh.com www.app2.contoh.com;
  listen 443 ssl http2;
  listen [::]:443 ssl http2;
  ssl_certificate         /etc/ssl/cert.pem;
  ssl_certificate_key     /etc/ssl/key.pem;
  
  location / {
  proxy_set_header X-Forwarded-Host $host;
  proxy_set_header X-Forwarded-Server $host;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_pass http://web1/;
  }
  }
```

6. #### Lakukan link
```sh
  ln -s /etc/nginx/sites-available/tomcat.conf /etc/nginx/sites-enabled/
```

7. #### Restart nginx
```sh
  systemctl restart nginx
```
