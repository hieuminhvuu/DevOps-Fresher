# Cài đặt Jenkins

- Trong bài lab này chúng ta sẽ tìm hiểu cách cài đặt Jenkins
- Đầu tiên chúng ta cần chuẩn bị 1 vm mới để làm server chạy Jenkins, hostname sẽ để là jenkins-server
- Tạo ra 1 thư mục /tools/jenkins để chứa file cài đặt jenkins, tạo ra 1 file nội dung như sau :

jenkins-install.sh
```
#!/bin/bash

apt install openjdk-11-jdk -y
java --version
wget -p -O - https://pkg.jenkins.io/debian/jenkins.io.key | apt-key add -
sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5BA31D57EF5975CA
apt-get update
apt install jenkins -y
systemctl start jenkins
systemctl enable jenkins
ufw allow 8080
```

- Để chạy file cài đặt này, ta cần cấp quyền thực thi, sau đó chạy với câu lệnh sh :

```
chmod +x jenkins-install.sh
sh jenkins-install.sh
```

- Sau khi cài đặt, để kiểm tra jenkins đã chạy thành công chưa dùng lệnh :

```
systemctl status jenkins
```

<img src= images/001.png>

- Khi này jenkins đã chạy thành công, ta truy cập theo cổng 8080 mặc định của jenkins

<img src= images/002.png>

- Sau khi truy cập đúng cổng jenkins thì đã có hướng dẫn mặc định của jenkins để lấy password, công việc của chúng ta là tìm đến đúng đường dẫn này và cat password ra thôi :

<img src= images/003.png>

- Lấy password rồi điền vào thôi

<img src= images/004.png>

- Để đơn gian khi bắt đầu thì chúng ta sẽ chọn cài đặt những plugin theo recommand của jenkins:

<img src= images/005.png>
<img src= images/006.png>

- Đến bước cài đặt account, mình sẽ để full admin

<img src= images/007.png>

- Bước tiếp theo là điền đúng địa chỉ của server :

<img src= images/008.png>

- Như vậy là jenkins đã sẵn sàng sử dụng :

<img src= images/009.png>
<img src= images/010.png>

- Hiện tại ta phải truy cập đúng địa chỉ xong còn phải chỉ định đúng port 8080, cảm giác ko được chuyên nghiệp cho lắm. Vì vậy mình sẽ sử dụng web server nginx để revert proxy. Để cài đặt nginx, chạy lệnh :

```
apt install nginx -y
```

- Ta truy cập tới /etc/nginx, tạo 1 file trong thư mục conf.d có tên là jenkins.conf :

```
cd /etc/nginx
vim conf.d/jenkins.conf
```

```
server {
        listen 80;
        server_name 192.168.123.160;
        location / {
                proxy_pass http://192.168.123.160:8080;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection keep-alive;
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
        }
}
```

- Sau khi lưu lại rồi chỉ cần restart lại nginx là được :

```
systemctl restart nginx
```

- Giờ ta có thể truy cập jenkins qua port mặc định 80 rồi, nginx sẽ làm nhiệm vụ revert proxy từ port 80 sang port 8080 của jenkins:

<img src= images/011.png>

- Như vậy là chúng ta đã cài thành công jenkins rồi.