---
layout: post
title: docker [3] - Dockerfile 작성을 위한 주요 명령
subtitle: Dockerfile 작성을 위한 주요 명령
author: useok
banner:
  image: assets/images/banners/home.jpeg
  opacity: 0.618
  background: "#000"
  height: "40vh"
  min_height: "10vh"
  heading_style: "font-size: 2.5rem; font-weight: bold;"
  subheading_style: "color: gold"
categories: [docker]
tags: [docker, dockerfile]
sidebar: [article-menu,category-list] 
---
## Dockerfile 작성을 위한 주요 명령

### Dockerfile이란

- docker 이미지를 작성할 수 있는 기능
- Dockerfile 문법으로 이미지 생성을 위한 스크립트를 작성할 수 있고, 이를 기반으로 이미지를 생성할 수 있음
- 나만의 이미지를 생성할 수 있고, 배포를 위해서도 많이 활용하는 기능

### Dockerfile 기본 문법

- Dockerfile은 텍스트 파일 형식이므로, 각자 사용하는 어떤 에디터로든 작성할 수 있음
- Dockerfile 기본 문법
    - 기본적으로는 간단히 명령과 인자로 이루어짐
    - 명령은 통상적으로는 대분자로 작성함

### Dockerfile 주요 명령

| 명령 | 설명 | 예시 |
| --- | --- | --- |
| FROM  | 베이스 이미지 지정 명령 | FROM httpd:alpine |
| LABEL | 버전 정보, 작성자와 같은 이미지 설명을 작성하기 위한 명령 | LABEL version=”1.0.0” |
| CMD | docker 컨테이너가 시작할 때, 실행하는 쉘 명령을 지정하는 명령.<br>RUN과 비슷하지만, RUN은 이미지 작성시 실행하는 명령이고, <br>CMD는 컨테이너를 시작할 때 실행하는 명령임 | CMD [’python’.’app.py’] |
| RUN | 쉘 명령을 실행하는 명령 <br>RUN은 이미지 작성시 실행되며, 일종의 새로운 이미지 layer를 만드는 역할을 함 | RUN [”apt-get”, “install”, ”nginx”] |
| ENTRYPOINT | docker 컨테이너가 시작할 때, 실행하는 쉘 명령을 지정하는 명령.<br>docker run 커맨드 실행시, 별도 명령어도 넣을 수 있는데, 이 때 CMD 명령은 해당 명령으로 덮어씌워짐.<br>ENTRYPOINT로 지정한 명령은 docker run 커맨드 실행시 함께 넣어진 별도 명령어가 있더라도, 덮어씌워지지 않고 실행됨 |  |
| EXPOSE | docker 컨테이너 외부에 오픈할 포트 설정 | EXPORT 8080 |
| ENV | docker 컨테이너 내부에서 사용할 환경 변수 지정 | ENV PATH /usr/bin:$PATH |
| WORKDIR | docker 컨테이너에서의 작업 디렉토리 설정 |  |
| COPY | 파일 디렉토리를 docker 컨테이너에 복사. ADD와 달리 URL은 지정할 수 없으며, 압축 파일을 자동으로 풀어주지 않음 | COPY http://test.sh /root/test.sh |


#### 참고로 알아둘 명령어

| 명령 | 설명 | 예시 |
| --- | --- | --- |
| ADD | ADD와 COPY 명령은 유사하지만, COPY 명령이 보다 명시적이므로, COPY 명령을 사용하도록 함<br>파일, 디렉토리 또는 특정 URL의 데이터를 docker 이미지에 추가<br>추가할 파일이 tar 압축 파일의 경우, 자동으로 압축을 풀어줌( 이 기능이 압축파일을 그대로 넣고 싶을 때 문제가 됨)<br>동일한 이름의 파일 또는 디렉토리가 이미 docker 이미지에 있을 시에는 덮어씌우지 않음 | ADD file /var/www/html<br>ADD http://test.sh /root/test.sh |
| SHELL | 쉘 프로그램 지정 명령이지만, CMD 등으로 대체 가능하므로 참고로만 알아둠 | SHELL [’/bin/bash’, ‘-c’] |
| ARG | dockerfile 내에서 필요한 변수 설정. <br>docker 이미지/컨테이너에서 사용하는 환경변수를 설정하는 ENV와 달리, dockerfile 스크립트 작성을 위해 필요한 변수를 설정하는 것임 | ARG env=dev |
| USER | docker 이미지 및 컨테이너에서 작업을 하는 사용자 ID를 지정함 | USER useok |
| ONBUILD | 생성한 이미지를 기반으로, 새로운 이미지를 생성시 실행하는 명령을 지정 | ONBUILD ADD myweb.tar /var/www/html |
| VOLUME | 이미지를 위한 볼륨 생성 |  |

- Dockerfile 에서도 주석을 사용할 수 있음
    - Dockerfile에서 #을 쓰면, 해당 라인은 주석처리

---

---

---

## 주요 명령어 상세

### 1. FROM

- 베이스 이미지 지정 명령(dockerfile에 필수로 작성)
ex) FROM alpine

#### Dockerfile로 이미지 작성

```bash
docker build {옵션} {Dockerfile경로}
```

| 옵션 | 설명 |
| --- | --- |
| -t / --tag | 이미지 이름 설정. 이미지 이름은 저장소(DockerHub ID)/이미지이름:태그 와 같이 작성할 수 있음<br>(저장소 이름 및 태그 이름은 작성하지 않아도 되며, 태그 이름이 없을 경우, 디폴트로 latest가 태그로 붙여짐) |
| -f | 이미지 빌드시 디폴트로 Dockerfile 파일명으로 된 파일을 찾아서 이미지 빌드함. <br>그 외에 파일명으로 이미지를 빌드할 경우 해당 옵션을 이용해서 파일명을 지정할 수 있음<br>ex) docker build -f myDockerfile ./ |
| --pull | FROM 으로 지정된 이미지는 한 번 다운로드 받으면, 이미지 생성시마다 새로 다운로드받지 않고 다운로드 받은 이미지를 사용<br>해당 옵션은 이미지 생성시마다 새로 다운로드를 받으라는 옵션으로, --pull=true 와 같이 작성하여 사용.<br>DockerHub에 베이스 이미지를 수시로 업데이트하고, 이를 기반으로 새로운 이미지 생성시 자주 사용할 수 있는 옵션 |


### 2. LABEL

- LABEL은 {key} = {value} 형식으로 메타 데이터를 넣을 수 있는 기능
보통 저자, 버전, 설명, 작성일자등을 각각 key 이름을 정하고, 값을 넣음

```bash
#Dockerfile 예시

FROM alpine
LABEL maintainer="useok"
LABEL version="1.0.0"
LABEL description="Test for docker"
```

### 3. COPY

- 호스트 PC의 파일 또는 폴더를 컨테이너에 복사하여 추가
- COPY {호스트 PC 경로} {컨테이너 내부 경로}

```
💡 VOLUME 명령도 있지만, 호스트 PC의 특정 폴더를 컨테이너 내부 폴더에 연결하는 옵션은 -v 옵션으로만 가능하며, VOLUME 명령은 컨테이너 내부의 특정 폴더를 위한 볼륨을 생성하기 위해서만 사용됨
ex) VOLUME /mydata
ex) VOLUME [”/mydata1”,”/mydata2”]

```

```bash
#Dockerfile 예시

FROM httpd:alpine

LABEL maintainer="useok"
LABEL version="1.0.0"
LABEL description="Test for docker"

COPY ./00_FINAL_CODE/2021_DEV_HTML /usr/local/apache2/htdocs
```

### 4. CMD

- CMD는 하나의 Dockerfile에서 한 가지만 설정되며, 만약 CMD 설정이 여러개일 경우, 맨 마지막에 설정된 CMD 설정만 적용됨(기존 이미지에 CMD 명령이 있더라도 Dockerfile 작성 시, 또는 docker run 터미널 명령어에 CMD 명령어를 적어주면, 기존의 CMD 명령어는 덮어씌워짐)
- 다음 세 가지 형태로 CMD 명령을 작성할 수 있음
    - 명령어, 인자를 리스트처럼 작성하는 형태(해당 방식을 docker에서는 추천함)
        - 다음 예에서 echo 명령만 써줄 경우, 쉘에서 실행하지 않고, 직접 해당 명령이 실행되므로, 쉘에서 실행할 때, 적용되는 쉘의 환경변수 등 적용되지 않음(따라서, 가장 정확하게는 명확히 쉘까지 지정해서 명령을 실행해주는 것이 좋음)
        - /bin/sh -c 명령은 /bin 디렉토리에 있는 sh프로그램(기본 쉘 프로그램)을 실행하되,
            - -c 옵션은 쉘 명령을 터미널상에서 받지 않고, 인자로 받겠다라는 의미
            - 따라서, /bin/sh -c echo 라고 하면, echo라는 명령을 sh 프로그램 상에서 실행하겠다는 의미
        - 쌍따옴표로 써야 함(홑따옴표를 쓰면 안 됨)
        
        ```bash
        CMD ["executable", "param1", "param2", ...]
        #ex
        CMD ["/bin/sh", "-c", "echo", "Hello"]
        ```
        
    
    - ENTRYPOINT 명령어에 인자를 리스트처럼 작성하여 넘겨주는 형태
        - ex) ENTRYPOINT에 “/bin/sh” 명령어를 넣고, CMD에는 인자인 [”-c” , “echo”, “Hello”]를 넣어줌
        
        ```bash
        CMD ["param1", "parma2", ...]
        ```
        
    - 쉘 명령처럼 작성하는 형태
        
        ```bash
        CMD <command> <parma1>, <param2> ...]
        #ex
        CMD /bin/sh -c echo Hello
        ```
        

```bash
#Dockerfile 예시

FROM httpd:alpine

LABEL maintainer="useok"
LABEL version="1.0.0"
LABEL description="Test for docker"

COPY ./00_FINAL_CODE/2021_DEV_HTML /usr/local/apache2/htdocs

CMD ["/bin/sh", "-c", "httpd-foreground"]
```

### 5. ENTRYPOINT

- ENTRYPOINT는 docker run 시에 함께 넣어지는 CMD 명령에 덮어씌워지지 않고, 반드시 실행해야 하는 명령을 기입할 때 사용
    - 이 때, docker run 시 함께 넣어지는 명령은 ENTRYPOINT에 작성된 명령의 인자로 넣어지게 됨
    - 따라서, ENTRYPOINT에 컨테이너 실행시, 반드시 실행돼야 하는 명령을 넣고, 별도로 각 컨테이너 생성시 필요한 인자는 docker run에 넣는 식으로도 활용하기도 함
- 즉, Dockerfile에 CMD 명령어 넣는 경우나 docker run 터미널 명령어에 CMD 명령어 넣는 경우 모두 ENTRYPOINT 명령 뒤에 쓰여지는 인자의 역할을 함

```bash
#Dockerfile 예시

FROM httpd:alpine

LABEL maintainer="useok"
LABEL version="1.0.0"
LABEL description="Test for docker"

COPY ./00_FINAL_CODE/2021_DEV_HTML /usr/local/apache2/htdocs

ENTRYPOINT ["/bin/echo" ,"hello"]
```

```bash
# 이미지 생성
docker build --tag myweb ./

## 컨테이너 작성
docker run -itd -p 9999:80 --name httpdweb myweb /bin/sh hi

## 출력 확인 
# 명령어 /bin/echo 의 인자 hello가 출력되고, 
# CMD 명령어로 넣은 /bin/sh hi가 인자의 역할을 해 /bin/sh hi가 출력
docker logs httpdweb
=> hello /bin/sh hi
```

### 6. RUN

- docker는 이미지 생성시, 각 단계를 layer로 나누어 작성함
    - 이를 통해 특정 단계 변경시, 전체 이미지를 다시 다운로드받지 않아도 됨
- RUN 명령은 이미지 생성시, 일종의 layer를 만들 수있는 명령으로, 보통 베이스 이미지에 패키지(프로그램)을 설치하여, 새로운 이미지를 만들 때 많이 사용

```bash
#Dockerfile 예시

FROM ubuntu:18.04
LABEL maintainer="useok"

RUN apt-get update # 패키지(프로그램) 정보 업데이트
RUN apt-get install -y apache2 apt-utils # apache2, apt-utils 패키지(프로그램) 설치

## apache2 디폴트 웹서버 설정은 /var/www/html/ 폴더의 웹페이지를 보여줌
COPY ./00_FINAL_CODE/2021_DEV_HTML /var/www/html

## apache2 웹서버 구동 명령은 다음과 같음
ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

### 7. EXPOSE

- docker 컨테이너의 특정 포트를 외부에 오픈하는 설정, 보통 컨테이너끼리의 통신에 사용
    - docker run -p 옵션으로 설정할 수도 있음
        - docker run -p 옵션은 컨테이너의 특정 포트를 외부에 오픈하고, 해당 포트를 호스트 PC의 특정 포트와 매핑시킴
    - 이에 반해, EXPOSE는 컨테이너 생성시, 특정 포트를 외부에 오픈하는 것만 설정하는 것임
        - 따라서, 독립적으로 실행시는 EXPOSE 옵션을 넣는다고 해서, 호스트 PC에서 해당 컨테이너에 포트번호로 접속할 수 있는 것음 아님
        

```bash
#Dockerfile 예시

FROM ubuntu:18.04
LABEL maintainer="useok"

RUN apt-get update # 패키지(프로그램) 정보 업데이트
RUN apt-get install -y apache2 apt-utils # apache2, apt-utils 패키지(프로그램) 설치

EXPOSE 80 #80번 포트 오픈

## apache2 디폴트 웹서버 설정은 /var/www/html/ 폴더의 웹페이지를 보여줌
COPY ./00_FINAL_CODE/2021_DEV_HTML /var/www/html

## apache2 웹서버 구동 명령은 다음과 같음
ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

```
💡 EXPOSE 를 설정하고 docker run -P 옵션을 주면
EXPOSE로 오픈된 포트에 호스트PC의 랜덤 포트가 연결됨
```
![Untitled](/assets/images/postImages/2023-01-26-154720-docker [3] - Dockerfile 작성을 위한 주요 명령/Untitled.png)


### 8. ENV

- 컨테이너 내의 환경변수 설정
- 설정한 환견변수는 RUN, CMD, ENTRYPOINT 명령에도 적용됨

```bash
# Dockerfile 예시

FROM mysql:5.7

## mysql 슈퍼관리자인 root ID에 대한 password 란에 원하는 패스워드 설정
ENV MYSQL_ROOT_PASSWORD=password

## dbname 란에 원하는 데이터베이스 이름 설정
ENV MYSQL_DATABASE=dbname

## 필요시 다음 설정도 가능
# ENV MYSQL_USER=user #user 란에 mysql 추가 사용자 ID 설정
# ENV MYSQL_PASSWORD=pw #pw 란에 mysql 추가 사용자 PW 설정
```

```bash
# 이미지 생성
docker build --tag mysqldb -f Dockerfile-mysql ./

## 컨테이너 작성
docker run -d --name mydb mysqldb

## 컨테이너&DB 접속
docker exec -it mydb /bin/bash
mysql -u root -p => password # ENV로 패스워드로 입력한 정보 입력
show databases; => dbname # ENV로 설정한 DB이름 출력
```

### 9. WORKDIR

- RUN, CMD, ENTRYPOINT 명령이 실행될 디렉토리 설정

```bash
#Dockerfile 예시

FROM httpd:alpine

WORKDIR /usr/local/apache2/htdocs

CMD /bin/cat index.html
```

⇒ /usr/local/apache2/htdocs 에서 /bin/cat index.html명령어 실행