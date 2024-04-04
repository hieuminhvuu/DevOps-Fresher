# Dockerize dự án Shoeshop
- Ở bài lab này mình sẽ tạo docker file để dockerize dự án backend shoeshop.

- Đầu tiên thì dự án này được viết bởi java, vì thế ta cần chọn image maven để có thể build được. Mình sẽ chọn 1 image alpine nhẹ nhàng maven:3.5.3-jdk-8-alpine. Tiếp theo để run thì chúng ta cần 1 image ko cần maven, chỉ cần java thôi. Mình chọn được 1 image của amazon, alpine có jre 8.

- Truy cập tới thư mục chứa dự án (/data/shoeshop). Tiến hành tạo ra 1 Dockerfile và tiến hành viết :
```
vim Dockerfile
```

- Nội dung file version 1 sẽ như sau :

```
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine as build

WORKDIR /app
COPY . .
RUN mvn install -DskipTests=true

## Run stage ##
FROM amazoncorretto:8-alpine-jre

WORKDIR /run
COPY --from=build /app/target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar

EXPOSE 8080

ENTRYPOINT java -jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

- Như thấy, Dockerfile này được chia ra làm 2 stage, build và run. Khi chia ra làm 2 stage build và run riêng biệt như vậy thì image tổng thể ko bị ảnh hưởng bởi bước run, từ đó image sinh ra sẽ không bị nặng nề, được tối ưu.

- Nếu cảm thấy image sẵn không đáng tin cậy, chúng ta có thể tự tạo image từ alpine gốc, thay đổi file như sau :

```
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine as build

WORKDIR /app
COPY . .
RUN mvn install -DskipTests=true

## Run stage ##
FROM alpine:3.19

RUN apk add openjdk8

WORKDIR /run
COPY --from=build /app/target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar

EXPOSE 8080

ENTRYPOINT java -jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

- Dockerfile này thì chúng ta đã thay đổi image ở run stage thành alpine gốc, tiến hành cài đặt java 8 và run như bình thường.

- Như ở tư duy đã nói, chúng ta sẽ sử dụng non user root để chạy dự án, tránh dùng root user, thay đổi Dockerfile như sau :

```
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine as build

WORKDIR /app
COPY . .
RUN mvn install -DskipTests=true

## Run stage ##
FROM alpine:3.19

RUN adduser -D shoeshop

RUN apk add openjdk8

WORKDIR /run
COPY --from=build /app/target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar

RUN chown -R shoeshop:shoeshop /run

USER shoeshop

EXPOSE 8080

ENTRYPOINT java -jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

- Ở file này chúng ta đã tạo ra 1 user là shoeshop và chạy dự án dưới user này. Dockerfile tới đây đã khá là tối ưu rồi.