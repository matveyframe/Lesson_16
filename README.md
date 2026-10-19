1) Запустить приложение https://github.com/anestesia001/dos-36-js как systemd-демон
 ```
[Unit]
Description= note_py
After=network-online.target

[Service]
User=matvey
Group=matvey
WorkingDirectory=/home/matvey/home_works/lesson_16/dos-36-js
ExecStart=/usr/bin/node server.js
Restart=always

[Install]
WantedBy=multi-user.target
```
![](https://github.com/matveyframe/Lesson_16/blob/main/app%20status.PNG "Logo Title Text 1")

2)Настроить nginx в роли reverse proxy с TLS для этого приложения
 ```
upstream backend {
server 127.0.0.1:3000 weight=2;
server 127.0.0.1:3001 weight=3;
}

server {
    listen 80;
    listen [::]:80;
    server_name localhost;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name localhost;

    ssl_certificate /etc/nginx/ssl/note-app.crt;
    ssl_certificate_key /etc/nginx/ssl/note-app.key;

    ssl_protocols TLSv1.2 TLSv1.3;

    access_log /var/log/nginx/note_app_access.log;
    error_log /var/log/nginx/note_app_error.log;

    location / {
        proxy_pass http://127.0.0.1:3000;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }


    location /api/notes {
        default_type application/json;
        add_header X-Demo-Location "/api/notes";
        return 200 ' {"status": "ok"}';
}

    location /notes {
        add_header X-Demo-Location "/notes";
        default_type application/json;
        return 200 ' {"status": "ok"}';
}

    location /add {
        add_header X-Demo-Location "/add";
        default_type application/json;
        return 200 ' {"status": "ok"}';
}
        }
```
![](https://github.com/matveyframe/Lesson_16/blob/main/cert%20info.PNG "Logo Title Text 1")
![](https://github.com/matveyframe/Lesson_16/blob/main/cert%20result.PNG "Logo Title Text 1")

3)Поднять второй экземпляр приложения из п1 на другом порту

```
[Unit]
Description= noteV2_py
After=network-online.target

[Service]
User=matvey
Group=matvey
WorkingDirectory=/home/matvey/home_works/lesson_16/dos-36-js
Environment=PORT=3001
ExecStart=/usr/bin/node server.js
Restart=always

[Install]
WantedBy=multi-user.target
```
![](https://github.com/matveyframe/Lesson_16/blob/main/app%20statusV2.PNG "Logo Title Text 1")

4)Настроить на nginx балансировку запросов (round robin)

```
upstream backend {
server 127.0.0.1:3000 weight=2;
server 127.0.0.1:3001 weight=3;
}

```
5)Провести нагрузочное тестирование приложения с помощью locust

![](https://github.com/matveyframe/Lesson_16/blob/main/locust%20test.PNG "Logo Title Text 1")
