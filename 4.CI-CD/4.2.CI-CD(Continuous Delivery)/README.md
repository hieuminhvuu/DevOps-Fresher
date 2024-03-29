Trong bài lab này, mình sẽ thay đổi chiến lược CI/C Deployemnt sang Delivery. Tức là bước deploy sẽ không để chạy tự động nữa mà sẽ cần check user nào đang deploy và phải thực hiện thủ công.

Ta cần thay đổi file .gitlab-ci.yml, thêm vào phần deploy và phần showlog 1 attribute nữa :

```
when: manual
```

```
variables:
    projectName: shoe-ShoppingCart
    projectVersion: 0.0.1-SNAPSHOT
    projectPath: /datas/shoeshop/
    projectUser: shoeshop


stages:
    - build
    - deploy
    - showlog

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    script:
        - mvn install -DskipTests=true
    tags:
        - lab-server
    only:
        - tags

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    when: manual
    script:
        - sudo cp target/$projectName-$projectVersion.jar $projectPath
        - sudo chown -R $projectUser. $projectPath
        - sudo su $projectUser -c 'pids=$(ps -ef | grep "$projectName-$projectVersion.jar" | grep -v "grep" | awk "{print \$2}"); if [ -n "$pids" ]; then kill -9 $pids; fi'
        - sudo su $projectUser -c "cd $projectPath ; nohup java -jar $projectName-$projectVersion.jar > nohup.out 2>&1 &"
    tags:
        - lab-server
    only:
        - tags

showlog:
    stage: showlog
    variables:
        GIT_STRATEGY: none
    when: manual
    script:
        - sudo su $projectUser -c "cd $projectPath ; tail -n 10000 nohup.out"
    tags:
        - lab-server
    only:
        - tags
```

- Sau khi tạo tag, pipeline mới xuất hiện sẽ như sau :

<img src= images/001.png>

- Khi này ta cần khởi động deploy và showlog 1 cách thủ công.