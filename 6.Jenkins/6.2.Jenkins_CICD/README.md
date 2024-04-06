# CICD với jenkins

- Trong bài lab này chúng ta sẽ đi từng bước để thiết lập được CI-CD với jenkins

# Table of Contents

## [1. Thiết lập ban đầu](#1.1)
## [2. Cài đặt Jenkins agent](#1.2)
## [3. Kết nối Jenkins tới Gitlab](#1.3)
## [4. Config pipeline](#1.4)
## [5. Cấu hình gitlab webhook kết nối tới jenkins](#1.5)
## [6. Viết Jenkins file](#1.6)
## [7. Viết build stage](#1.7)
## [8. Viết deploy stage](#1.8)

<a name='1.1'>

# 1. Thiết lập ban đầu

- Để kết nốt các server tới jenkins server thì trên các server đó cần có java và phải trùng với version của jenkins server

- Kiểm tra version trên jenkins server

```
java -version
```

<img src= images/001.png>

- Ta thấy version của java trên jenkins server là jdk-11, giờ kiểm tra của lab-server

<img src= images/002.png>

- Trên lab-server đang là jdk-17, ta sẽ cài thêm 1 version jdk-11 nữa vào:

```
apt install openjdk-11-jdk -y
```

- Sau khi cài đặt xong, ta cần thay đổi version mặc định sử dụng bằng câu lệnh :

```
update-alternatives --config java
```

- Sau khi chạy câu lệnh, ta cần chỉ định chính xác số thứ tự ứng với jdk-11

<img src= images/003.png>

- Tới đây thì đã thay đổi được version java trên lab-server rồi

<img src= images/004.png>

- Tiếp theo cần tạo 1 user jenkins trên lab-server

<img src= images/005.png>

<a name='1.2'>

# 2. Cài đặt Jenkins agent
- Đầu tiên ta cần mở 1 cổng inbound cho jenkins server :

<img src= images/006.png>
<img src= images/007.png>

- Ta đã thành công mở 1 cổng 8999 trên jenkins server để cho jenkins agent tương tác qua

<img src= images/008.png>

- Tạo 1 folder trên lab-server có tên /var/lib/jenkins

<img src= images/009.png>

- Ta sẽ thao tác lần lượt trên gui của Jenkins như sau để tạo 1 node :

<img src= images/010.png>
<img src= images/011.png>
<img src= images/012.png>
<img src= images/013.png>
<img src= images/014.png>
<img src= images/015.png>
<img src= images/016.png>

- Sau khi tạo thành công, nhấn vào node đó, ta sẽ có những cách để kết nối tới jenkins agent :

<img src= images/017.png>

- Để bắt đầu kết nối tới jenkins server, ta cần chuẩn bị trên lab-server các lệnh sau :

```
mkdir /var/lib/jenkins
chown jenkins. /var/lib/jenkins/
cd /var/lib/jenkins/
su jenkins
```

<img src= images/018.png>

- Chúng ta vừa đổi chủ sở hữu cho thư mục, chuyển tới thu mục đó và dùng user jenkins (tránh dùng user root)
- Chúng ta sử dụng lựa chọn kết nối sau :

<img src= images/019.png>

- Một lưu ý khi chạy là phải thêm đúng workdir /var/lib/jenkins :

```
echo b4bfb5648d2370ef4dc7fd306fff22f87ff3c7029c526616d293eed56ca5acca > secret-file
curl -sO http://192.168.123.160:8080/jnlpJars/agent.jar
java -jar agent.jar -url http://192.168.123.160:8080/ -secret @secret-file -name "lab-server" -workDir "/var/lib/jenkins"
```

<img src= images/020.png>

- Như vậy đã connected, ta có thể truy cập vào node để xem

<img src= images/021.png>

- Hiện tại thì ta vẫn chạy jenkins trực tiếp luôn, để chạy dưới nền và xuất log ra 1 file thì tương tự như các bài trước, ta chỉ cần sửa lệnh như sau :

```
java -jar agent.jar -url http://192.168.123.160:8080/ -secret @secret-file -name "lab-server" -workDir "/var/lib/jenkins" > nohup.out 2>&1 &
```

<img src= images/022.png>

- Như vậy jenkins đã được chạy dưới nền, logs được lưu vào file nohup.out

- Tiếp theo hãy dùng Jenkins tạo thư mục làm việc cho lab-server như sau :

<img src= images/023.png>
<img src= images/024.png>
<img src= images/025.png>
<img src= images/026.png>
<img src= images/027.png>

<a name='1.3'>

# 3. Kết nối Jenkins tới Gitlab

- Ta sẽ dùng plugin để kết nối. Đầu tiên ta cần cài đặt các plugin này trước :

<img src= images/028.png>
<img src= images/029.png>
<img src= images/030.png>

- Sau khi cài đặt xong thì Jenkins sẽ tự động restart lại 

<img src= images/031.png>

- Khi này ta có thể vào phần Installed để kiểm tra. Tiếp theo :

<img src= images/032.png>
<img src= images/033.png>

- Ở bước này ta cần Credential, qua gitlab server để tạo credential cần thiết. Trước tiên ta cần tạo 1 account mới, đặt tên là jenkins, thao tác đã quen thuộc :

<img src= images/034.png>
<img src= images/035.png>
<img src= images/036.png>
<img src= images/037.png>
<img src= images/038.png>
<img src= images/039.png>

- Tiếp theo ta cần đăng nhập vào account này, tiếp tục các bước :

<img src= images/040.png>
<img src= images/041.png>
<img src= images/042.png>

- Lấy token mới được tạo, chuyển qua jenkins theo các bước như sau:

<img src= images/043.png>
<img src= images/044.png>
<img src= images/045.png>
<img src= images/046.png>
<img src= images/047.png>

- Đến đây thì gitlab đã kết nối thành công tới jenkins

<a name='1.4'>

# 4. Config pipeline

- Follow các bước sau :

<img src= images/048.png>
<img src= images/049.png>
<img src= images/050.png>
<img src= images/051.png>
<img src= images/052.png>
<img src= images/053.png>
<img src= images/054.png>
<img src= images/055.png>
<img src= images/056.png>
<img src= images/057.png>
<img src= images/058.png>
<img src= images/058.png>
<img src= images/060.png>
<img src= images/061.png>

<a name='1.5'>

# 5. Cấu hình gitlab webhook kết nối tới jenkins

- Mở 1 outbound requests trên gitlab-server

<img src= images/064.png>
<img src= images/065.png>

- Tiếp tục

<img src= images/062.png>
<img src= images/063.png>

- Ở chỗ này, url chúng ta cần điền sẽ có format như sau :

```
http://<user trên jenkins>:<token user đó trên jenkins>@<địa chỉ jenkins>/project/<đường dẫn dự án trên jenkins>
```

- Token ta lấy như sau :

<img src= images/066.png>
<img src= images/067.png>

- Đường dẫn sẽ như sau trong trường hợp của mình :

```
http://admin:11040f3c0a3dc5fb7896b7ab49f2e8d89b@192.168.123.160/project/Action_in_lab/shoeshop
```

- Thao tác :

<img src= images/068.png>
<img src= images/069.png>

- Khi tạo xong 1 webhook, kết quả sẽ hiện bên dưới

<img src= images/070.png>

- Ta thử tạo 1 test event push

<img src= images/071.png>
<img src= images/072.png>

- Khi đó ở bên Jenkins 1 pipeline được tạo ra, ở đây có lỗi vì trên nhánh chưa có jenkins file

<img src= images/073.png>
<img src= images/074.png>

<a name='1.6'>

# 6. Viết Jenkins file

- Đầu tiên hãy thay đổi config sang nhánh develop

<img src= images/075.png>
<img src= images/076.png>

- Tạo jenkinsfile và commit lại như sau :

<img src= images/077.png>
<img src= images/078.png>

- Ngay sau khi commit thì bên jenkins đã xuất hiện 1 pipeline mới rồi

<img src= images/079.png>

- Ta có thể chọn vào pipeline đó và chọn Console Output để xem chi tiết từng bước:

<img src= images/080.png>

- Hoặc có thể nhấn Open Blue Ocean để chuyển qua giao diện của plugin trực quan hơn:

<img src= images/081.png>
<img src= images/082.png>

<a name='1.7'>

# 7. Viết build stage

- thay đổi Jenkinsfile như sau :

```
pipeline {
    agent {
        label 'lab-server'
    }
    environment {
        appUser = "shoeshop"
        appName = "shoe-ShoppingCart"
        appVersion = "0.0.1-SNAPSHOT"
        appType = "jar"
        processName = "${appName}-${appVersion}.${appType}"
        folderDeploy = "/datas/${appUser}"
        buildScript = "mvn clean install -DskipTests=true"
    }
    stages {
        stage('info') {
            steps {
                sh(script: """ whoami; pwd; ls -la """, label: "first stage")
            }
        }
        stage('build') {
            steps {
                sh(script: """ ${buildScript} """, label: "build with maven")
            }
        }
    }
}

```

<img src= images/083.png>
<img src= images/084.png>
<img src= images/085.png>

<a name='1.8'>

# 8. Viết deploy stage

- Để jenkins có thể deploy, tương tự gitlab-runner, ta phải cấp quyền thực hiện 1 số câu lệnh cho user jenkins trên lab-server có quyền root mà ko cần dùng pass:

```
visudo
```

```
jenkins ALL=(ALL) NOPASSWD: /bin/cp*
jenkins ALL=(ALL) NOPASSWD: /bin/chown*
jenkins ALL=(ALL) NOPASSWD: /bin/kill*
jenkins ALL=(ALL) NOPASSWD: /bin/su shoeshop*
```

<img src= images/086.png>

- Jenkinsfile thêm stage deploy sẽ có nội dung như sau :

```
pipeline {
    agent {
        label 'lab-server'
    }
    environment {
        appUser = "shoeshop"
        appName = "shoe-ShoppingCart"
        appVersion = "0.0.1-SNAPSHOT"
        appType = "jar"
        processName = "${appName}-${appVersion}.${appType}"
        folderDeploy = "/datas/${appUser}"
        buildScript = "mvn clean install -DskipTests=true"
        copyScript = "sudo cp target/${processName} ${folderDeploy}"
        permsScript = "sudo chown -R ${appUser}. ${folderDeploy}"
        killScript = """
            ps -ef | grep ${processName} | grep -v grep | awk '{print \$2}' | while read pid; do
            if [ -n "\$pid" ]; then
                sudo kill -9 \$pid
            fi
            done
            """
        runScript = 'sudo su ${appUser} -c "cd ${folderDeploy}; java -jar ${processName} > nohup.out 2>&1 &"'
    }
    stages {
        stage('build') {
            steps {
                sh(script: """ ${buildScript} """, label: "build with maven")
            }
        }
        stage('deploy') {
            steps {
                sh(script: """ ${copyScript} """, label: "copy the .jar file into deploy folder")
                sh(script: """ ${permsScript} """, label: "set permission folder")
                sh(script: """ ${killScript} """, label: "terminate the running process")
                sh(script: """ ${runScript} """, label: "run the project")
            }
        }
    }
}
```

- Sau khi commit lại, kiểm tra pipeline :

<img src= images/087.png>

- Như vậy pipeline đã thành công
