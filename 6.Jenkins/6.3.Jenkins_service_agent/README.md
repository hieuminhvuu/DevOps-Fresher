# Tự động kết nối lab-server tới jenkins

- Trong bài lab này ta sẽ config lab-server sao cho mỗi khi bật-tắt/restat server thì lab-server sẽ tự động kết nối tới jenkins

- Chúng ta sẽ viết thành 1 service để khi service này sẽ bật tắt cùng hệ thống luôn
- Chuyển về user root, tạo 1 file jenkins-agent như sau :

```
vim /etc/systemd/system/jenkins-agent.service
```

```
[Unit]
Description=Jenkins Agent Service
After=network.target

[Service]
Type=simple
WorkingDirectory=/var/lib/jenkins
ExecStart=/bin/bash -c 'java -jar agent.jar -url http://192.168.123.160:8080/ -secret @secret-file -name "lab-server" -workDir "/var/lib/jenkins" > nohup.out 2>&1 &'
User=jenkins
Restart=always

[Install]
WantedBy=multi-user.target
```

<img src= images/001.png>


- Để sử khởi chạy service, dùng lệnh :

```
systemctl daemon-reload
systemctl start jenkins-agent
```

- Kiểm tra status
```
systemctl status jenkins-agent
```