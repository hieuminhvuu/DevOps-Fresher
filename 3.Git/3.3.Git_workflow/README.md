# Tìm hiểu về Git Workflow và áp dụng và GitLab Server

- Trong bài lap này chúng ta sẽ tìm hiểu về Workflow của git, sau đó sẽ thiết lập các branch như develop, feature, release, hotfix

# Table of Contents

## [1. Tìm hiểu Git Workflow](#1.1)
## [2. Tạo các nhánh cần thiết trên GitLab](#1.2)
## [3. Thao tác với các nhánh](#1.3)
## [4. Bảo vệ các nhánh chính](#1.4)
## [5. Tạo tag](#1.5)

<a name='1.1'>

# 1. Tìm hiểu Git Workflow

<img src= images/001.png>

- Gitflow chỉ là một ý tưởng trừu tượng về quy trình sử dụng Git, Nó chỉ ra cách thức setup các loại branchs khác nhau và cách thức để merge chúng lại với nhau.

- Main(Master) branch : có sẵn trong git và là branchs chứa mã nguồn của ứng dụng và các version đã sẵn sàng để chạy trên sản phẩm cuối cho người dùng có thể sử dụng (đặt Tag trên mỗi version). Thường cấu hình chỉ cho manage tương tác.

<img src= images/002.png>

- Hotfix: Được base trên nhánh Main(Master) để sửa nhanh những lỗi trên UIT hoặc sửa những cấu hình đặc biệt chỉ có trên môi trường productions. Thường thi nhánh hotfix sẽ được tạo ra và nhanh chóng sửa chữa code rồi merge lại nhánh Main ngay khi có lỗi xảy ra (thường thì sẽ không được thay đổi trược tiếp mã nguồn trên branch master nên phải làm cách này).

<img src= images/003.png>

- Release: Trước khi Release một phần mềm dev team cần được tạo ra để kiểm tra lại lần cuối trước đi release sản phần để người dùng có thể sử dụng (Thuông thường mã nguồn tại thời điểm này sẽ tạo ra bản build để test và kiểm tra lại bussiness). Đây chính là nhánh làm việc chính của Tester, QA. Code phải vượt qua một loạt bài test thì mới được merge lên nhánh Main.

<img src= images/004.png>

- Develop: Được khởi tạo từ Main(Master) branches để lưu lại tất cả lịch sử thay đổi của mã nguồn. Develop branchs là merge code của tất cả các branchs feature. Khi dev team hoàn thành hết feature của một topic, teamlead sẽ review ứng dụng và merge đến branchs release để tạo ra bản một bản release cho sản phẩm.

<img src= images/005.png>

- Feature: Được base trên branchs Develop. Mỗi khi phát triển một feature mới chúng ta cần tạo một branchs để code mã nguồn cho từng feature. Khi có một feature mới dev tạo một branchs mới (thường đặt theo tên feature/<tên feature đó>) base trên branchs Develop để code cho feature đó. Sau khi code xong, dev tạo merge request đến branchs develop để teamlead review mà merge lại vào branchs Develop.

<img src= images/006.png>

<a name='1.2'>

# 2. Tạo các nhánh cần thiết trên GitLab

- Đầu tiên ta sẽ tạo nhánh develop từ nhánh main

<img src= images/007.png>
<img src= images/008.png>
<img src= images/009.png>

- Nhánh develop được tạo thành công, tương tự ta sẽ tạo thêm 1 nhánh là feature/?? từ nhánh develop. Tuỳ và nhiệm vụ thì nhánh sẽ được đặt tên khác nhau. Ví dụ nếu ở frontend cần phát triển chức năng login thì ta sẽ đặt tên nhánh là **feature/frontend/login**. Đặt tên nhánh cần logic và tường minh.

<img src= images/010.png>
<img src= images/011.png>

- Như vậy ta đã có 3 nhánh cần thiết này.

<a name='1.3'>

# 3. Thao tác với các nhánh

- Trước tiên, ta không thể dùng quyền admin mang đi dev được. Ta sẽ tạo cho anh Nguyễn Văn Dev một tài khoản để ảnh có thể thao tác với source code một cách được kiểm soát (chỉ được thực hiện một số hành động cho phép). Các bước tạo tài khoản mới như sau :

<img src= images/012.png>
<img src= images/013.png>
<img src= images/014.png>
<img src= images/015.png>
<img src= images/016.png>
<img src= images/017.png>
<img src= images/018.png>
<img src= images/019.png>
<img src= images/020.png>

- Ái chà chà, như vậy ta đã tạo cho anh Dev này 1 tài khoản của hệ thống chúng ta rồi. Giờ chúng ta hoá thân thành anh Dev này đăng nhập vào thôi :v

<img src= images/020.png>
<img src= images/021.png>
<img src= images/022.png>
<img src= images/023.png>

- Sau khi thành công, giờ thì chuyển lại admin để invite member cho anh Dev này được quyền nhìn thấy và thao tác với repo project của chúng ta.

<img src= images/024.png>
<img src= images/025.png>
<img src= images/026.png>
<img src= images/027.png>
<img src= images/028.png>

- Chúng ta đã thành công thêm anh Dev này vào project, và anh ta đang ở dưới danh nghĩa là developer. Giờ anh Dev đã có thể clone code để viết mã, phát triển như một developer rồi.

<img src= images/029.png>

- Giờ hãy giả sử anh Dev sẽ code tính năng login cho project. Ta sẽ làm một ví dụ nhỏ đơn giản nhất đó chính là ở trên giao diện GitLab chuyển qua nhánh feature/frontend/login, tạo ra một file gì đó rồi commit.

<img src= images/030.png>
<img src= images/031.png>
<img src= images/032.png>
<img src= images/033.png>
<img src= images/034.png>

- Lúc này nhánh feature đã được thay đổi. Để merge nhánh này vào nhánh develop, ta cần tạo merge request 

<img src= images/035.png>
<img src= images/036.png>
<img src= images/037.png>
<img src= images/038.png>
<img src= images/039.png>

<a name='1.4'>

# 4. Bảo vệ các nhánh chính

- Nhưng có 1 vấn đề ở đây là anh Dev này có quyền tự merge luôn. Điều này là không đảm bảo vì chỉ admin mới nên có quyền merge thôi. Ta phải bảo vệ các nhánh lại :

<img src= images/040.png>
<img src= images/041.png>
<img src= images/042.png>
<img src= images/043.png>

- Như vậy dev đã không thể tự ý merge vào các nhánh main và develop

<img src= images/044.png>

- Để tiến hành merge, phải dùng tài khoản có quyền admin. Thao tác để merge như sau :

<img src= images/045.png>
<img src= images/046.png>
<img src= images/047.png>

- Sau khi merge thì ở nhánh develop đã xuất hiện những file mới đẩy từ nhánh feature/

<img src= images/048.png>

- Tương tự như vậy, các nhánh feature khác cũng sẽ được vận hành tương tự. Sau khi nhánh develop đã ổn rồi thì code sẽ được merge sang nhánh release. Ta sẽ đi tạo nhánh release, bảo vệ nhánh sau đó tạo merge request, các thao tác tương tự như đã làm.

<img src= images/049.png>
<img src= images/050.png>
<img src= images/051.png>
<img src= images/052.png>
<img src= images/053.png>
<img src= images/054.png>

- Tuỳ vào chính sách của team, ta có thể để 2 option rằng không ai có thể push tới các nhánh develop, release hoặc để các DevOps (maintain) có thể push tới các nhánh này. Để thuận tiện trong bài lab hơn thì mình sẽ để nhánh develop và release các DevOps có quyền push tới :

<img src= images/065.png>

- Giờ giả sử DevOps cần config gì đó trên nhánh develop, commit sau đó tạo merge request, sau đó merge tới nhánh release. 

<img src= images/055.png>
<img src= images/056.png>
<img src= images/057.png>
<img src= images/058.png>
<img src= images/059.png>
<img src= images/060.png>
<img src= images/061.png>
<img src= images/062.png>
<img src= images/063.png>
<img src= images/064.png>

- Thành công, nhánh release được được merge.
- 
<img src= images/066.png>

<a name='1.5'>

# 5. Tạo tag

- Tiếp theo, hãy cùng tạo thử 1 tag ở nhánh release.

<img src= images/067.png>
<img src= images/068.png>
<img src= images/069.png>

