# Table of Contents

## [1. Triển khai user, thư mục và quyền truy cập cho dự án](#1.1)
## [2. Cài đặt nodejs và npm](#1.2)
## [3. Sử dụng Nginx làm web server](#1.3)


# Triển khai project Portfolio frontend React lên server Ubuntu

<a name='1.1'>

## 1. Triển khai user, thư mục và quyền truy cập cho dự án

- Kiểm tra địa chỉ ip của server
```
ip a
```
<img src= images/001.png>

- Copy file zip của project tới server
```
scp /path/to/file user@ip-address-server:/path/to/save
```
<img src= images/002.png>
<img src= images/003.png>


- Install unzip để giải nén file zip
<img src= images/004.png>

- Giải nén file
```
unzip [name-of-file]
```
<img src= images/005.png>

- Tạo thư mục mới và user mới cho dự án
```
- Mỗi dự án có thư mục riêng.
- Mỗi dự án có user riêng.
```

<img src= images/006.png>
<img src= images/007.png>

- Chuyển chủ sở hữu và nhóm sở hữu của thư mục dự án sang cho user mới

<img src= images/008.png>

- Chính xác lúc này dự án đã có chủ sở hữu và nhóm sử hữu là portfolio, nhưng lúc này còn 1 điều cần thay đổi về quyền cho thư mục nữa, chủ sở hữu đang có quyền rwx, nhóm sở hữu đang có quyền r-x, other có quyền r-x, ta cần thay đổi như sau :
```
chmod -R 750 portfolio-master
```

<img src= images/009.png>


<a name='1.2'>

## 2. Cài đặt nodejs và npm

- Vì đây là project Frontend viết bởi React, chúng ta cần cài đặt nodejs và npm lên server

- Cài đặt nodejs và npm theo hướng dẫn ở [link này](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04)

## 3. Build và Run project

- Đầu tiên hãy chuyển qua user **portfolio** để thao tác với project
- Chuyển đường dẫn tới thư mục của project, build dự án bằng câu lệnh có trong file package.json
- Đầu tiên chạy lệnh **npm install** để cài đặt toàn bộ dependencies của project
```
npm install
```

- Sau đó chúng ta build bằng câu lệnh
```
npm run build
```

- Giờ hãy thử chạy thử nó nào
```
npm run start
```

<img src= images/010.png>

- Như vậy project đã chạy, hãy truy cập theo url được hướng dẫn

<img src= images/011.png>


<a name='1.3'>

## 3. 3. Sử dụng Nginx làm web server

- Cài đặt nginx, sau khi cài đặt thành công thì nginx sẽ được mặc định mở trên cổng 80 (nhớ phải chuyển qua user root để tiến hành cài đặt)
```
apt install nginx
```

- Sử dụng câu lệnh sau để kiểm tra các cổng được mở trên server, kiểm tra nginx đã ở cổng 80 chưa
```
netstat -ltpun
```
<img src= images/012.png>

- Giờ ta có thể tới trang home của nginx thông qua cổng 80 của server

<img src= images/013.png>

- Tất cả các file cấu hình của nginx được nằm ở đường dẫn /etc/nginx

<img src= images/014.png>

- File trang home của nginx sẽ nằm ở /etc/nginx/site-available/default, ví dụ bây giờ ta sẽ đổi cổng 80 mặc định sang cổng 8008 xem
 
<img src= images/015.png>

- Test syntax hoặc file có vấn đề j không bằng lệnh **nginx -t** :
```
nginx -t
```

<img src= images/016.png>

- Nếu kết quả là ok như hình, ta có thể restart lại nginx bằng lệnh :
```
systemctl restart nginx
```

- Kiểm tra các cổng đang mở :

<img src= images/017.png>

- Như vậy là cổng 80 mặc định được chuyển thành 8008, test lại qua browser nè:

<img src= images/018.png>

- Để dự án React chạy được với nginx, đầu tiên ta tạo file config trong config.d
```
root@arthurserver:/etc/nginx# vim conf.d/portfolio.conf
```

- Nội dung của file đó sẽ như sau :

<img src= images/019.png>

- Kiểm tra syntax của nginx, nếu syntax ok thì restart lại nginx
```
nginx -t

systemctl restart nginx
```

- Sau khi restart nginx, hãy truy cập cổng chúng ta đã chỉ định trong file conf lúc nãy
<img src= images/020.png>

- Opps, tại sao lại lỗi 500 ? Chúng ta đã chỉ định đúng đường dẫn tới thư mục build rồi mà? Hãy nhớ lại, thư mục **portfolio-master** chúng ta đã chmod 750, vì vậy các user khác sẽ không có quyền truy cập vào, đương nhiên nginx cũng không có quyền này. Hãy kiểm tra nginx là user nào bằng cách vào kiểm tra file nginx.conf
```
vim /etc/nginx/nginx.conf
```
<img src= images/021.png>

- Ngay ở dòng đầu tiên, chúng ta có thể nhận thấy nginx là user **www-data**, vậy thì để project có thể chạy được bằng nginx, tối thiểu user này phải nằm trong Group **portfolio**. Công việc đơn giản là hãy add user này vào bằng câu lệnh sau :
```
usermod -aG portfolio www-data
```

- Sau đó lại restart nginx lại nào
```
systemctl restart nginx
```

- Tuyệt vời, mọi thứ đều hoạt động
<img src= images/022.png>

- Lúc này, nếu muốn đổi cổng, ta đơn giản lại thay đổi config ở file **/etc/nginx/conf.d/portfolio.conf** thôi, sau đó restart lại nginx, nhưng nếu muốn restart lại nginx mà ko làm ảnh hưởng tới các cổng khác thì ta dùng lệnh :
```
nginx -s reload
```