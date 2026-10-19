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
![](https://github.com/matveyframe/Lesson_16/blob/main/cert%20info.PNG "Logo Title Text 1")

2)Настроить nginx в роли reverse proxy с TLS для этого приложения
 ```

```
![](https://github.com/matveyframe/Lesson_16/blob/main/cert%20info.PNG "Logo Title Text 1")
![](https://github.com/matveyframe/Lesson_16/blob/main/cert%20info.PNG "Logo Title Text 1")

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
![](https://github.com/matveyframe/Lesson_16/blob/main/cert%20info.PNG "Logo Title Text 1")

4)Настроить на nginx балансировку запросов (round robin)

```
```
5)Провести нагрузочное тестирование приложения с помощью locust

![](https://github.com/matveyframe/Lesson_16/blob/main/cert%20info.PNG "Logo Title Text 1")
