# Setup dự án trên GitLab

- Trong bài lab này, chúng ta sẽ tìm hiểu các cách cài đặt và vận hành kho git của một dự án thực tế, mình sẽ sử dụng source code của 2 dự án ở phần Linux để thực hành luôn.
- Tương tự đối vs các dự án khác thì cách vận hành là tương đương

# Table of Contents

## [1. Thao tác tạo repo trên GitLab](#1.1)
## [2. Đẩy code từ local lên server GitLab](#1.2)

<a name='1.1'>

# 1. Thao tác tạo repo trên GitLab

- Đầu tiên hãy truy cập vào GitHub, chọn vào tạo group mới, vì một dự án thường sẽ không chỉ có 1 repo không mà sẽ gồm những repo về backend, frontend và các service khác nữa, nên việc lưu riêng lẻ là không tối ưu. Điền các thông số như trong ảnh hướng dẫn, ta sẽ tạo ra 1 group private mới.

<img src= images/001.png>
<img src= images/002.png>
<img src= images/003.png>
<img src= images/004.png>

- Sau khi tạo xong Group, ta tiến hành tạo repo cho project. Tuỳ vào project của bạn đã sẵn sàng hay là project mới nguyên ta sẽ chọn add file README hoặc không, ở đây project của mình đã có sẵn readme nên mình sẽ bỏ chọn đi

<img src= images/005.png>
<img src= images/006.png>
<img src= images/007.png>

- Sau khi tạo xong ta nhận được repo của project mới và các hướng dẫn như sau :

<img src= images/008.png>
<img src= images/009.png>

<a name='1.2'>

# 2. Đẩy code từ local lên server GitLab

- Đầu tiên, hãy truy cập vào server Backend (đã tạo từ bài Linux), tạo thư mục /data mới để làm việc với git

<img src= images/010.png>

- Theo hướng dẫn sẵn của git, tới bước Git global setup 

<img src= images/011.png>

- Sau đó clone project về theo hướng dẫn, nhớ nhập tk, mật khẩu chính xác :

<img src= images/012.png>

- Như vậy ta đã kéo thành công project từ gitlab về local server. Ờ bài lab trước, file zip và thư mục shoeshop được đặt ở đường dẫn **/home/ubuntu/**, dùng cp để cp source code tới thưc mục hiện tại này :

<img src= images/013.png>

- Kiểm tra xem status, hiện đang làm việc ở branch nào :

<img src= images/014.png>

- Dùng **git add** để đưa những file thay đổi vào staging area :

<img src= images/015.png>

- Tiếp theo sử dụng câu lệnh commit để lưu những thay đổi này vào localrepo. Lưu ý để commit tường minh hơn cho tất cả, hãy lưu ý cấu trúc viết commit như sau :

<img src= images/016.png>

- Dựa vào cấu trúc, hiện tại vai trò mình đang là 1 ông dev đang lưu lại base project nên mình sẽ thực hiện commit này như sau :

<img src= images/017.png>

- Tuỳ vào bối cảnh commit khác nhau hãy viết commit một cách tường minh và khoa học nhất. Sau khi commit thành công thì code đã được lưu lại tại localrepo. Giờ ta sẽ dùng lệnh git push để đẩy lên remote repo, chính là gitlab server của chúng ta :

<img src= images/018.png>

- Refresh lại GitLab, chúng ta thấy rằng code đã được đẩy tới GitLab thành công

<img src= images/019.png>

- Như vậy là bài lab đã được hành thành. Ta đã tạo repo mới và up code lên thành công.