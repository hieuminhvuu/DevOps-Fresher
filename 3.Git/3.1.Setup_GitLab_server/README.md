# Cài đặt GitLab server lên server Linux

- Trong bài lab này chúng ta sẽ tìm hiểu cách cài đặt và vận hành GitLab server trên một máy ảo ubuntu
- Tài nguyên sử dụng là một virtual machine ubuntu được tạo từ VMware, đối với các vps trên cloud thì công việc là khá tương tự

# Table of Contents

## [1. Tạo máy ảo mới, config ssh](#1.1)
## [2. Install GitLab Server](#1.2)
## [3. Config GitLab Server](#1.3)
## [4. Tiến hành tạo user admin mới](#1.4)

<a name='1.1'>

# 1. Tạo máy ảo mới, config ssh

<a name='1.2'>

# 2. Install GitLab

- Đầu tiên hãy tìm trên google cụm từ **gitlab ee packages**, truy cập link đầu tiên chúng ta có rất nhiều các phiên bản gitlab khác nhau, mới nhất hiện tại đã tới phiên bản 16.10. Nhưng các phiên bản mới này khá nặng và chúng ta sẽ không cần sử dụng hết, nên hãy chọn một phiên bản cũ và ổn định hơn. Ở bài lab này mình sẽ chọn bản 15.5 dành cho ubuntu/jammy (22.04) với kiến trúc arm (dành cho macos m1)

<img src= images/001.png>

- Copy câu lệnh đã cho và dán vào terminal sau đó chạy

```
curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
```

- Như vậy ta đã kéo về máy thành công. Tiếp tục theo hướng dẫn chạy lệnh install

```
sudo apt-get install gitlab-ee=15.5.9-ee.0
```

- Sau khi install như hướng dẫn xong, ta cấn sửa lại external_url của gitlab trong thư mục /etc/gitlab/gitlab.rb , ở đây mình sửa thành ip của chính vm luôn, trong trường hợp mn có domain riêng đã mua rồi thì add vào bước này :

- Sau đó chạy lệnh sau để quá trình cài đặt GitLab bắt đầu.

```
sudo gitlab-ctl reconfigure
```

- Quá trình cài đặt gitlab server sẽ bắt đầu, mất chút thời gian

- Sau khi thành công, ta có thể tới browser truy cập bằng chính ip lúc nãy vừa config

<img src= images/002.png>

<a name='1.3'>

# 3. Config GitLab

- Tới đây hãy đăng nhập vào gitlab bằng root user. Username là **root**, còn password ta phải vào đường dẫn **/etc/gitlab/initial_root_password** để tìm

<img src= images/003.png>

- Dán pass từ file kia vào và đăng nhập

<img src= images/004.png>
<img src= images/005.png>

- Như vậy ta đã đăng nhập vào bằng user root thành công. Tiếp theo hãy turn off tính năng đăng ký người dùng, chỉ cho phép user root mới có thể tạo :

<img src= images/006.png>
<img src= images/007.png>

- Bỏ luôn tính năng auto DevOps đi

<img src= images/008.png>

- Nhớ save changes

- Tiếp theo chúng ta hãy đi thay đổi password cho user root

<img src= images/009.png>
<img src= images/010.png>

- Đăng nhập lại

<img src= images/011.png>
<img src= images/012.png>

- Như vậy chúng ta đã thiết lập xong gitlab server

<a name='1.4'>

# 4. Tiến hành tạo user mới

- Chỗ thanh navbar, truy cập vào admin

<img src= images/013.png>

- Ở đây chúng ta có giao diện để quản lý project, user và group. Ở đây chúng ta sẽ chọn **new user** :

<img src= images/014.png>

- Nhập các thông tin cần thiết, cấp quyền admin và chọn **Create user** :

<img src= images/015.png>
<img src= images/016.png>
<img src= images/017.png>

- Sau khi tạo xong chúng ta mới có thể tạo mật khẩu :

<img src= images/017.png>
<img src= images/018.png>
<img src= images/020.png>

- Tiến hành đăng nhập vào user mới này. Trước tiên cần đăng xuất user root ra :

<img src= images/021.png>

- Đăng nhập bằng thông tin đã tạo trước đó

<img src= images/022.png>

- Ta phải thay đổi pass thêm 1 lần

<img src= images/023.png>

- Đăng nhập lại với pass mới

<img src= images/024.png>
<img src= images/025.png>

- Như vậy chúng ta đã thành công tạo và đăng nhập vào user admin.