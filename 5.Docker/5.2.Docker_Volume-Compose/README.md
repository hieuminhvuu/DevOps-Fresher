# I. Cách sử dụng volume build các dự án mà ko cần cài đặt thư viện local

## 1. Dự án Java Shoeshop


- Ví dụ đầu tiên là dự án shoeshop, với docker, chúng ta có thể build và run dự án không cần cài đặt maven trên máy local. Hãy tìm hiểu câu lệnh sau :

```
docker run --rm -v `pwd`:/app --workdir="/app" maven:3.5.3-jdk-8-alpine mvn install -DskipTests=true
```

- Ở ví dụ này, 
    - chúng ta sẽ run 1 docker container có base image là maven:3.5.3-jdk-8-alpine
    - với lệnh **mvn install -DskipTests=true**
    - sau đó chỉ định workdir trong container này là **/app**
    - sử dụng docker volume với cờ **-v**
    - **-v `pwd`:/app** : chỉ định thư mục liên kết giữa container và máy local, ở đây sử dụng pwd để lấy thư mục hiện tại luôn
    - **--rm** : sau khi chạy xong container tự động xoá đi luôn
    - Kết luận : sau khi chạy câu lệnh này, docker sử dụng image maven để build dự án, sau khi build thì sẽ thu được thư mục targer, chỉ định liên kết từ thư mục hiện tại đến workdir của container, từ đó copy được thư mục target đó.

- Kết quả :

<img src= images/001.png>

- Thu được thư mục target ở thư mục hiện tại mà ko cần build bằng maven trên máy local, sau khi hoàn thành nhiệm vụ thì container đã được xoá luôn.

## 2. Dự án React Portfolio

- Để build được dự án react, ta cần cài đặt nodejs trên local, nhưng với docker ta không cần tới điều này. Câu lệnh :

```
docker run --rm -v $(pwd):/app -w "/app" node:13.12.0-alpine sh -c "npm install && npm run build"
```

- Ở ví dụ này :
    - chúng ta sẽ run 1 docker container có base image là node:13.12.0-alpine
    - câu lệnh để build khi exec vào container: **npm install && npm run build**
    - sau đó chỉ định workdir trong container này là **/app**
    - sử dụng docker volume với cờ **-v**
    - **--rm** : sau khi chạy xong container tự động xoá đi luôn
    - Kết luận : sau khi chạy câu lệnh này, docker sử dụng image maven để build dự án, sau khi build thì sẽ thu được thư mục build và node_module, chỉ định liên kết từ thư mục hiện tại đến workdir của container, từ đó copy được thư mục build đó.

- Kết quả :

<img src= images/002.png>

## 3. Sử dụng docker volume để tạo container mariadb

- Khi sử dụng docker để tạo các container database, nếu container gặp vấn đề và bị xoá đi, sau đó tất cả dữ liệu sẽ mất hết. Nếu không có phương án lưu giữ lại data thì rất nguy hiểm. Vì thế ta cần sử dụng tới volume

```
docker run -v /db/mariadb-1/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -p 3307:3306 --name mariadb-1 -d mariadb:10.6
```

- Ở ví dụ này khi khởi tạo container mariadb này, tất cả dữ liệu từ **/var/lib/mysql** được lưu hết lại ở local với đường dẫn **/db/mariadb-1**

- Ta sẽ thử nghiệm tạo data trong database này, xoá đi rồi tạo container khác cũng mount với thư mục **/db/mariadb-1** xem dữ liệu có bị mất không :

- truy cập mysql :

<img src= images/003.png>

- tạo 1 database mới có tên demo

<img src= images/004.png>

- thoát khỏi container, truy cập đường dẫn /db/mariadb-1 xem có dữ liệu chưa (đã có) :

<img src= images/005.png>

- xoá container mariadb-1 hiện tại :

<img src= images/006.png>

- tạo ra 1 container khác có tên là mariadb-2, mount lại với thư mục **/db/mariadb-1**

<img src= images/007.png>

- truy cập vào container mới này và kiểm tra dữ liệu :

<img src= images/008.png>

- như vậy là database demo vẫn còn nguyên. Chúng ta đã thành công đạt được mục tiêu.

II. Cách sử dụng docker-compose

- Nếu mỗi lần chúng ta khởi tạo chạy 1 container như trên thì sẽ rất là phiền phức, ta phải nhớ hết tất cả tên, cờ, option... vì vậy để chúng ta có thể nhớ được toàn bộ những thông tin về các container, chúng ta sẽ viết hết vào 1 file docker-compose.

- Ta sẽ hiểu hơn qua ví dụ này, chúng ta sẽ chạy 1 docker-compose để khởi tại 2 container bao gồm 1 container mariadb, 1 container backend java shoeshop.

- Đầu tiên tạo 1 file có tên **docker-compose.yml**, nội dung cần thiết sẽ như sau :

```
version: '3.8'
services:
  db1:
    image: mariadb:10.6
    volumes:
      - /db/mariadb-1:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "3307:3306"
    container_name: mariadb-1
    restart: always

  app-backend:
    image: shoeshop:v3
    ports:
      - "8081:8080"
    container_name: shoeshop-1
    restart: always
```

- Chúng ta có thể thấy hoàn toàn các option là giống với câu lệnh docker lúc nãy. Để chạy file docker-compose này ta dùng lệnh :

```
docker-compose up -d
```

- Các thao tác khác :

```
docker-compose down
docker-compose ps
```