# Java on Linux
- Trong bài lab này, mình sẽ thực hành triển khai dự án Java có tên shoeshop lên máy ảo linux.
- Về tài nguyên thì mình sẽ sử dụng máy ảo Ubuntu được tạo local trên máy tính mình bằng VMware, việc thực hiện trên máy ảo môi trường cloud hoàn toàn tương tự

# Table of Contents

## [1. Tạo máy ảo mới, config ssh](#1.1)
## [2. Tạo thư mục, user cho dự án](#1.2)
## [3. Cài đặt các công cụ cần thiết để build và run dự án](#1.3)
## [4. Cài đặt và config database](#1.4)
## [5. Tiến hành build dự án](#1.5)
## [6. Tiến hành run dự án](#1.6)

<a name='1.1'>

# 1. Tạo máy ảo mới, config ssh

- Trên VMware của mình đã có sẵn 1 snapshoot Ubuntu base rồi, việc của mình đơn giản là clone từ máy ảo đó thêm 1 vm mới, mình sẽ đặt tên cho vm mới này là Java (tên bất kỳ)

<img src= images/001.png>
<img src= images/002.png>

- Khởi động vm đó lên nào

<img src= images/003.png>

- Vì giao diện điều khiển máy ảo ko thuận tiện (không thể dùng lăn chuột, không thể dùng các lệnh copy, paste), mình sẽ dùng terminal từ mac ssh tới vm này để thuận tiện hơn. Trước tiên cần kiểm tra địa chỉ ip của vm

<img src= images/004.png>

- Tiếp theo cần chỉnh lại permission để vm chấp nhận việc ssh tới root user của vm, chúng ta sẽ sửa lại file /etc/ssh/sshd_config bằng vim :

```
vim /etc/ssh/sshd_config
```

- Sửa dòng **PermitRootLogin without-password** thành **PermitRootLogin yes** và lưu lại

<img src= images/005.png>

- Chạy lệnh

```
systemctl restart sshd
```

- Mở terminal của mac lên rồi ssh tới vm thôi

<img src= images/006.png>

<a name='1.2'>

# 2. Tạo thư mục, user cho dự án

- Đầu tiên, chúng ta sẽ copy file zip của dự án tới vm mới tạo, hãy chuẩn bị file zip và tìm đúng đường dẫn file, sau đó chạy lệnh sau:

```
scp Downloads/shoeshop-ecommerce.zip ubuntu@192.168.123.136:~
```

<img src= images/007.png>

- Kiểm tra xem vm đã nhận được chưa, ở câu lệnh trên thì file sẽ được lưu tới thư mục **/home/ubuntu** 

<img src= images/008.png>

- Giải nén file zip đó ra bằng unzip, sau khi giải nén xong chúng ta thu được thư mục shoeshop chứa toàn bộ source code của dự án này :

<img src= images/009.png>

- Chúng ta sẽ tạo ra thư mục chuyên chứa các dự án trên vm này là /projects và dư chuyển (mv) thư mục shoeshop tới đó :

<img src= images/010.png>

- Tạo 1 user mới để có quyền thực thi với dự án này, mình sẽ đặt là shoeshop luôn

<img src= images/011.png>

- Thay đổi quyền và sở hữu của thư mục (chown và chmod) :

<img src= images/012.png>
<img src= images/013.png>

<a name='1.3'>

# 3. Cài đặt các công cụ cần thiết để build và run dự án

- Mỗi dự án khác nhau viết bằng các ngôn ngữ khác nhau thì sẽ có các công cụ để build và run khác nhau. Tuỳ vào mỗi dự án hãy search để tìm và cài đặt công cụ chính xác. Dự án java Spring Boot này sẽ cần đến 2 công cụ là java và maven

- Cài đặt java 17 và kiểm tra

```
apt install openjdk-17-jdk openjdk-17-jre
```

<img src= images/014.png>

- Cài đặt maven và kiểm tra

```
sudo apt install maven
```

<img src= images/015.png>


<a name='1.4'>

# 4. Cài đặt và config database

- Các công nghệ khác nhau sẽ có các cách kết nối tới database khác nhau. Đối với Java Spring Boot chúng ta sẽ phải chỉnh sửa trong file có tên là **application.propeties** . Vim vào file này để xem chúng ta cần config những gì

<img src= images/016.png>

- Như vật chúng ta cần biết address_server, port, database_name và user, password. Hiện tại chúng ta chưa có database. Trong bài lab này mình sẽ cài đặt database trên vm này luôn cho đơn giản hoá, mn hoàn toàn có thể cài db ở 1 server khác.

- Trong bài lab này, mình sẽ sử dụng mariadb, để cài đặt chạy lệnh :

```
sudo apt install mariadb-server
```

- Kiểm tra mariadb đã được mở ở cổng nào 

```
netstat -tlpun
```

<img src= images/017.png>

- Như thấy, mariadb đã mở ở cổng 3306, nhưng chỉ được truy cập từ local (127.0.0.1), chúng ta sẽ không thể truy cập từ các server khác nếu cài đặt be và db ở 2 server khác nhau. Để thay đổi, chúng ta cần sửa thông số bind_address ở file **/etc/mysql/mariadb.conf.d/50-server.cnf**, sửa phần bind_address thành 0.0.0.0 :

<img src= images/018.png>

- Sau đó, khởi động lại mariadb bằng lệnh : 

```
systemctl restart mariadb
```

- Kiểm tra lại thấy rằng mariadb đã được config thành công r :

<img src= images/019.png>

- Truy cập vào db bằng root

```
mysql - u root
```

<img src= images/020.png>

- Tạo database mới có tên là shoeshop :

```
create database shoeshop;
```

<img src= images/021.png>

- Tạo user mới, gán cho user này có quyền truy cập tới mọi tài nguyên của database **shoeshop** :

```
create user 'shoeshop'@'%' identified by 'shoeshop';
grant all privileges on shoeshop.* to 'shoeshop'@'%';
flush privileges;
```

<img src= images/022.png>

- Sau khi tạo xong user, thoát khỏi user root và truy cập và db bằng user **shoeshop** bằng lệnh : 

```
mysql -h 192.168.123.136 -P 3306 -u shoeshop -p
```

<img src= images/023.png>

- Dùng lệnh **show databases;** chúng ta thấy user này đã (chỉ) có quyền truy cập với database shoeshop

<img src= images/024.png>

- Trong source code dự án này chúng ta có 1 file shoe_shopdb.sql dùng để tạo ra những bảng, những record cho db. Dùng lệnh source để import file này vào db :

<img src= images/025.png>
<img src= images/026.png>
<img src= images/027.png>

- Sau khi đã hoàn tất cài đặt, import dữ liệu các thứ vào db, tiến hành config để backend có thể đọc dữ liệu từ db, chỉnh sửa trong file có tên là **application.propeties** như ở bước trước đã nói

<img src= images/028.png>

<a name='1.5'>

# 5. Tiến hành build dự án

- Để build dự án này, chúng ta sẽ dùng câu lệnh **mvn install** , trước khi build phải chuyển qua user **shoeshop**, ta có thể thêm cờ -h để xem có những tuỳ chọn nào sử dụng kèm với mvn install không, ở đây mình sẽ dùng thêm cờ -DskipTests=true để bỏ qua phần test (vì chưa cần thiết)

```
mvn install -DskipTests=true
```

<img src= images/029.png>

- Sau khi build xong, maven sẽ tạo thêm cho chúng ta 1 thư mục có tên là target, trong đó có chứa file **shoe-ShoppingCart-0.0.1-SNAPSHOT.jar**, đây chính là thứ chúng ta cần để run dự án này

<img src= images/030.png>

<a name='1.6'>

# 6. Tiến hành run dự án

- Để run dự án này, chúng ta cần xác định đường dẫn tới file .jar và chạy lệnh :

```
java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

<img src= images/031.png>

- Như vậy dự án được chạy thành công dưới port 8080, truy cập từ browser thông qua ip address của server và qua cổng 8080 :

<img src= images/032.png>

- Như vậy dự án được chạy thành công. Lưu ý rằng chúng ta đang chạy trực tiếp dự án ở terminal, trong khi chạy ko thể thao tác j khác với server nữa cả, vì vậy ta sẽ sửa lại câu lệnh 1 chút để dự án được run với 1 process chạy ngầm dưới nền và lưu log vào 1 file bên ngoài : 

```
nohup java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar 2>&1 &
```

<img src= images/033.png>

- Khi này logs sẽ được lưu vào file **nohup.out**

<img src= images/034.png>

- Để đọc log từ file này, dùng lệnh tail :

```
tail -f nohup.out
```

<img src= images/035.png>

- Khi cần tắt dự án đi, ta cần tìm process nào đang chạy dự án và kill process đó đi là được, ví dụ :

<img src= images/036.png>

- Sau khi kill process đi thì dự án đã được tắt đi. Như vậy chúng ta đã hoàn thành bài Lab rồi. **Happy coding!**
