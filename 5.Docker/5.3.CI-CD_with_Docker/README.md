# CI-CD với docker

- Ở bài lab này chúng ta sẽ thay đổi pipeline thành CI-CD với docker, quá trình build và run dự án sẽ được chạy trên các container

- Đầu tiên ta cần cho user gitlab-runner có quyền sử dụng docker daemon bằng cách thêm user này vào group docker

```
usermod -aG docker gitlab-runner
```

<img src= images/001.png>

- Đầu tiên, ta sẽ cần merge nhánh develop tới nhánh release, ta sẽ thao tác bài lab này trên nhánh release.
-  Tiếp theo ta cần có file Dockerfile trước tiên, từ nhánh release tạo 1 file Dockerfile với nội dung tương tự bài lab 5.1:

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

- Đầu tiên ta cần tạo một vài variable cần thiết cho gitlab server :

<img src= images/002.png>
<img src= images/003.png>

- Các biến cần thiết :

```
REGISTRY_PROJECT: tagname của project
REGISTRY_USER: username của dockerhub
REGISTRY_PASSWORD: password
```

- Sửa lại file .github-ci.yml, chúng ta sẽ test 1 stage build trước như sau :

```
variables:
    # hieuminhvuu/shoeshop:v0.0.1_1234354
    DOCKER_IMAGE: ${REGISTRY_PROJECT}/${CI_PROJECT_NAME}:${CI_COMMIT_TAG}_${CI_COMMIT_SHORT_SHA}
stages:
    - buildandpush
    - deploy
    - showlog
buildandpush:
    stage: buildandpush
    variables:
        GIT_STRATEGY: clone
    before_script:
        - docker login -u ${REGISTRY_USER} -p ${REGISTRY_PASSWORD}
    script:
        - docker build -t $DOCKER_IMAGE .
        - docker push $DOCKER_IMAGE
    tags:
        - lab-server
    only:
        - tags
```

- Sau khi tạo file xong, commit lại vào nhánh đó, tạo tags, sau đó kiểm tra pipeline:

<img src= images/004.png>

- Đã thành công build và push lên docker hub

- Kiểm tra trên dockerhub :

<img src= images/005.png>

- Hoàn thành pipeline :

```
variables:
    # javabackend/shoeshop:v0.0.1_1234354
    DOCKER_IMAGE: ${REGISTRY_PROJECT}/${CI_PROJECT_NAME}:${CI_COMMIT_TAG}_${CI_COMMIT_SHORT_SHA}
    DOCKER_CONTAINER: ${CI_PROJECT_NAME}

stages:
    - buildandpush
    - deploy
    - showlog

buildandpush:
    stage: buildandpush
    variables:
        GIT_STRATEGY: clone
    before_script:
        - docker login -u ${REGISTRY_USER} -p ${REGISTRY_PASSWORD}
    script:
        - docker build -t $DOCKER_IMAGE .
        - docker push $DOCKER_IMAGE
    tags:
        - lab-server
    only:
        - tags

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    before_script:
        - docker login -u ${REGISTRY_USER} -p ${REGISTRY_PASSWORD}
    script:
        - docker pull $DOCKER_IMAGE
        - docker rm -f $DOCKER_CONTAINER
        - docker run --name $DOCKER_CONTAINER -dp 9090:8080 $DOCKER_IMAGE
    tags:
        - lab-server
    only:
        - tags

showlog:
    stage: showlog
    variables:
        GIT_STRATEGY: none
    script:
        - sleep 20
        - docker logs $DOCKER_CONTAINER
    tags:
        - lab-server
    only:
        - tags
```

- Commit, tạo tags và kiểm tra :

<img src= images/006.png>

- Như vậy là pipeline đã chạy thành công.

