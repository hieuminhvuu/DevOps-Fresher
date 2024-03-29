# Triển khai CI/CD cho dự án 

- CI/CD là viết tắt của Continuous Integration/Continuous Deployment (Tích hợp liên tục/ Triển khai liên tục). Đây là một phương pháp phát triển phần mềm nhằm tối ưu hóa quy trình phát triển và triển khai ứng dụng.

- Continuous Integration (CI) đề cập đến việc liên tục tích hợp các thay đổi vào mã nguồn của ứng dụng. Khi một nhóm phát triển làm việc cùng nhau, CI đảm bảo rằng các phiên bản mới nhất của mã nguồn được tích hợp vào một kho lưu trữ chung một cách tự động. Việc này giúp phát hiện sớm các lỗi hợp nhất và xung đột giữa các thành viên trong nhóm, đồng thời đảm bảo mã nguồn luôn ổn định.

- Continuous Deployment (CD) liên quan đến việc triển khai tự động các phiên bản mới nhất của ứng dụng vào môi trường sản phẩm hoặc môi trường thử nghiệm. CD giúp giảm thời gian và công sức cần thiết để triển khai ứng dụng, đồng thời tăng tính nhất quán và độ tin cậy của quy trình triển khai.

<img src= images/001.png>

- Về bản chất của việc triển khai CI/CD thì chỉ gồm 2 bước :
    - 1. Cài đặt công cụ tự động (GitLab Runner, Jenkin, ...)
    - 2. Viết file cấu hình công việc

- Trong bài lab này, mình sẽ sử dụng công cụ GitLab Runner triển khai lên trên server làm lab Linux (shoeshop)

# Table of Contents

## [1. Cài đặt GitLab Runner lên server](#1.1)
## [2. Kết nối Gitlab Runner với dự án](#1.2)
## [3. Viết kịch bản](#1.3)

<a name='1.1'>

# 1. Cài đặt GitLab Runner lên server

- Truy cập vào server cần cài đặt GitLab Runner, chạy các câu lệnh sau :

```
$ apt-get update

$ curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | bash

$ apt-get install gitlab-runner

$ apt-cache madison gitlab-runner

$ gitlab-runner -version
```

<img src= images/002.png>

- Sau khi cài đặt xong, biết rằng mỗi công cụ sẽ có 1 user riêng, ta kiểm tra bằng lệnh :

```
cat /etc/passwd
```

<img src= images/003.png>

- Thấy rằng gitlab runner đã tự động tạo ra 1 user mới có tên là Gitlab Runner, có thư mục làm việc tại /home/gitlab-runner

<a name='1.2'>

# 2. Kết nối Gitlab Runner với dự án

- Trước tiên để thuận tiện trong việc nhìn mặt gọi tên, mình sẽ đổi tên server làm lab thành **lab-server** :

<img src= images/004.png>

- Truy cập tới dự án **shoeshop** sau đó thao tác các bước :

<img src= images/005.png>
<img src= images/006.png>

- Tới đây ta cần lưu ý 2 dữ liệu cần dùng là URL và registration token. Qua lab-server chạy lệnh **gitlab-runner register** và điền các thông tin cần thiết

<img src= images/007.png>

- Sau khi kết nối xong, một file config của gitlab-runner được tạo ra :

<img src= images/008.png>

- Truy cập vào file theo đường dẫn đó

<img src= images/009.png>

- Sửa lại một tham số theo hình, cho phép con runner chạy nhiều dự án 1 lúc. Các thông tin còn lại y hệt lúc chúng ta đã cấu hình

- Rồi giờ thì con runner này đã sẵn sàng để chạy rồi, chạy câu lệnh gitlab-runner run để khởi động lên :

```
nohup gitlab-runner run --working-directory /home/gitlab-runner/ --config /etc/gitlab-runner/config.toml --service gitlab-runner --user gitlab-runner 2>&1 &
```

<img src= images/010.png>

- Tới GitLab kiểm tra, chúng ta đã thấy một runner đang chạy có tên là lab-server

<img src= images/011.png>

- Để cài đặt, nhấn theo nút :

<img src= images/012.png>

- Giao diện cài đặt sẽ như sau :

<img src= images/013.png>

- Lưu lại, chúng ta đã có một runner sẵn sàng :

<img src= images/014.png>

<a name='1.3'>

# 3. Viết kịch bản

- Để viết kịch bản CI, mình sẽ trực tiếp tạo ra 1 file config ở trên nhánh develop, ở cấp cao nhất :

<img src= images/015.png>

- Đặt tên file config này là **.gitlab-ci.yml** :

<img src= images/016.png>

- Nội dung của file :

```
stages:
    - build
    - deploy
    - checklog

build:
    stage: build
    script:
        - whoami
        - pwd
        - ls
    tags:
        - lab-server
```

<img src= images/017.png>

- Sau khi tạo file và commit xong, ta có thể tới CI/CD pipeline để kiểm tra đã tạo ra được 1 pipeline và chạy chưa :

<img src= images/018.png>
<img src= images/019.png>

- Truy cập vào pipeline để nghiên cứu nào :

<img src= images/020.png>
<img src= images/021.png>
<img src= images/022.png>

- Như vậy chúng ta đã có được 1 job thành công. Giờ hãy cũng phân tích từng bước :

<img src= images/023.png>

- Giờ hãy thay đổi stages build để cho gitlab-runner build dự án tự động cho chúng ta :

<img src= images/024.png>
<img src= images/025.png>
<img src= images/026.png>

- Sau khi commit lại thì 1 pipeline mới được tạo ra và đã được thực hiện thành công. Dự án đã được build

<img src= images/027.png>

- Truy cập vào lab-server theo đường dẫn lúc nãy để kiểm tra :

<img src= images/028.png>

- Như vậy thư mục terget đã xuất hiện rồi. Để chạy dự án lên thì ta chỉ cần chạy file .jar ở trong thư mục target. Nhưng như ở phần trước đã nói, ta sẽ không chạy dự án bằng user gitlab-runner, ta cần chạy bằng user riêng (shoeshop), mỗi dự án có 1 thư mục riêng (/datas/shoeshop). Ta cùng tiến hành như sau :

- User shoeshop đã có sẵn rồi, cần tạo thư mục **/datas/shoeshop/** :

<img src= images/029.png>

- Chúng ta cần cài đặt để user gitlab-runner sử dụng lệnh sudo mà không cần password bằng cách :

```
visudo
```

- Sau khi chạy câu lệnh này, chính ta access vào file config này, tiếp theo ta cần thêm 3 dòng config sau vào file :

```
gitlab-runner ALL=(ALL) NOPASSWD: /bin/cp*
gitlab-runner ALL=(ALL) NOPASSWD: /bin/chown*
gitlab-runner ALL=(ALL) NOPASSWD: /bin/su shoeshop*
```

<img src= images/030.png>

- Ý nghĩa là chúng ta sẽ cho user gitlab-runner chạy 3 câu lệnh trên mà ko cần password của sudo(root)

- Tiếp theo, ta sẽ viết tiếp pipeline bằng cách sửa lại file .gitlab-ci.yml như sau :

```
stages:
    - build
    - deploy
    - checklog

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    script:
        - mvn install -DskipTests=true
    tags:
        - lab-server

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    script:
        - sudo cp target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /datas/shoeshop/
        - sudo chown -R shoeshop. /datas/shoeshop
        - sudo su shoeshop -c 'pids=$(ps -ef | grep "shoe-ShoppingCart-0.0.1-SNAPSHOT.jar" | grep -v "grep" | awk "{print \$2}"); if [ -n "$pids" ]; then kill -9 $pids; fi'
        - sudo su shoeshop -c "cd /datas/shoeshop/ ; nohup java -jar shoe-ShoppingCart-0.0.1-SNAPSHOT.jar > nohup.out 2>&1 &"
    tags:
        - lab-server

```

<img src= images/031.png>

- Sau khi commit lại, xuất hiện thêm 1 pipeline và hãy xem kết quả :

<img src= images/032.png>
<img src= images/033.png>

- Hãy dành chút thời gian chúng ta cùng nhau đi phân tích file config ci/cd trên :

<img src= images/034.png>

    - 1: khối stage build
    - 3: khối stage deploy
    - 2 & 4: khối lệnh này để xác định chỉ stage build mới clone code về, stage delpoy không thực hiện clode code lại mà giữ nguyên code của stage build, đương nhiên là giữ lại thư mục target ở bước build
    - 5: copy file jar target/....jar tới thư mục /datas/shoeshop/
    - 6: chỉnh lại chủ sở hữu cho thư mục /datas/shoeshop/ thành user shoeshop
    - 7: nếu trên server đang tồn tại 1 pid chạy dự án shoeshop này rồi thì ta cần xoá process đó đi bằng cách tìm ra pid của process đó rồi kill -9. nếu trên server không tồn tại thì lệnh này sẽ không cần thiết và được skip ko gây lỗi
    - 8: chạy dự án dưới quyền của user shoeshop, viết hết logs vào file nohup.out

- Dự án đã được tự động deploy sau khi commit code rồi, ta hãy truy cập tới dự án thông qua cổng 8080

<img src= images/035.png>

- Oops, có lỗi 500 và không thể lên. Lỗi 500 là lỗi phía server. Sau khi tìm lỗi thì mình nhận ra là quên chưa chỉnh lại file kết nối tới database. Biết lỗi rồi thì mình sẽ đi sửa lại file config đó thôi, và mình sẽ commit lại và để pipeline tự động build, deploy lại thôi.

<img src= images/036.png>
<img src= images/037.png>

- Chờ pipeline chạy xong, nếu ko xuất hiện lỗi gì mới lại cả thì ta sẽ đi kiểm tra dự án đã lên chưa nào :

<img src= images/038.png>

- Ok vậy là đã lên rồi. Nhưng để cho file config pipeline tối ưu hơn, ta sẽ tiến hành đặt biến cho những thứ sử dụng lặp đi lặp lại :

```
variables:
    projectName: shoe-ShoppingCart
    projectVersion: 0.0.1-SNAPSHOT
    projectPath: /datas/shoeshop/
    projectUser: shoeshop


stages:
    - build
    - deploy
    - checklog

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    script:
        - mvn install -DskipTests=true
    tags:
        - lab-server

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    script:
        - sudo cp target/$projectName-$projectVersion.jar $projectPath
        - sudo chown -R $projectUser. $projectPath
        - sudo su $projectUser -c 'pids=$(ps -ef | grep "$projectName-$projectVersion.jar" | grep -v "grep" | awk "{print \$2}"); if [ -n "$pids" ]; then kill -9 $pids; fi'
        - sudo su $projectUser -c "cd $projectPath ; nohup java -jar $projectName-$projectVersion.jar > nohup.out 2>&1 &"
    tags:
        - lab-server
```

- Như vậy khi cần chỉnh sửa name, path, version ... ta chỉ cần chỉnh sửa đúng 1 chỗ variables thôi. Và hoàn toàn có thể áp dụng cho các dự án tương tự khác.

- Tiếp theo, ta sẽ viết thêm 1 stage nữa dành để show logs từ file nohup.out :

```
showlog:
    stage: showlog
    variables:
        GIT_STRATEGY: none
    script:
        - sudo su $projectUser -c "cd $projectPath ; tail -n 10000 nohup.out"
    tags:
        - lab-server
```

<img src= images/039.png>

- Ta sẽ show 10000 dòng cuối cùng của file nohup.out đó ra. Ta có thể truy cập vào job đó để đọc logs trực tiếp trên GitLab

<img src= images/040.png>
<img src= images/041.png>

- Như vậy là gần như pipeline Ci/CD (deployment) của chúng ta đã xong rồi. Nhưng còn 1 vấn đề là hiện tại pipeline đang được chạy khi có bất kỳ 1 commit nào, kể cả là commit nhỏ xíu liên quan đến file README.md thôi. Ta cần chỉnh lại config sao cho chỉ khi nào ta đánh tags cho nhánh thì pipeline mới chạy. Ở mỗi stage ta sẽ thêm đoạn code :

```
...
    only:
        - tags
```

<img src= images/042.png>

- Sau khi commit lại thì đương nhiên pipeline sẽ không tự động chạy nữa rồi, giờ hãy đi tạo 1 tags để xem pipeline có chạy không nào :

<img src= images/043.png>
<img src= images/044.png>
<img src= images/045.png>
<img src= images/046.png>

- Sau khi tạo tags thành công, kiểm tra thì pipeline đã được khởi động khi tạo tags

<img src= images/047.png>

- Như vật bài lab này đã thực hiện thành công.