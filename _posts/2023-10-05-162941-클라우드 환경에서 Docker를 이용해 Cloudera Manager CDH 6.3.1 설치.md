---
layout: post
title: 클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.2 설치
subtitle: 클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.2 설치
author: useok
banner:
  image: assets/images/banners/home.jpeg
  opacity: 0.618
  background: "#000"
  height: "40vh"
  min_height: "10vh"
  heading_style: "font-size: 2.5rem; font-weight: bold;"
  subheading_style: "color: gold"
categories: [data engineering]
tags: [data engineering, AWS, docker, cloudera manager, CDH, data pipeline]
sidebar: [article-menu,category-list] 
---
## 클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.2 설치

```
❗ 해당 포스트는 cdh 6.3.2 설치 파일을 가지고 있다는 점을 전제로 함

더이상 cloudera에서 6.3.2버전을 지원하지 않음
다른 버전의 CDH도 cloudera 구독을 해야 다운 가능한 것으로 보임
(현재 https://archive.cloudera.com/cm7/ 사용에 제한이 있는 7.x.x 버전 몇가지를 제공)
cloudera에서 더이상 CDH 6.3.2버전을 공식적으로 지원하지 않기 때문에 구글링 필요

```

```
❗ (참고) 해당 포스트에서 사용하는 서버 퍼블릭 IP와 각 컨테이너의 프라이빗 IP

서버 퍼블릭 IP :  52.78.103.71
컨테이너1(hadoop01, CM Server & Agent) 프라이빗 IP : 172.17.0.2
컨테이너2(hadoop02, CM Agent) 프라이빗 IP : 172.17.0.3
컨테이너3(hadoop03, CM Agent) 프라이빗 IP : 172.17.0.4

```

## [01] Cloud Server Instance 생성

- AWS EC2 ubuntu 20.04 LTS / t2.xlarge로 인스턴스 생성
- 보안그룹 인바운드규칙에서 22, 80, 7180 포트 열어줘야 하며, 다른 서비스의 사용포트는 밑의 링크에서 확인
    - [cm6.3.x 사용 포트](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/cdh_ports.html)
- 하나의 인스턴스에서 3개의 도커 컨테이너를 만들어 클러스터 구축하고자 함

## [02] CM6.3.2 설치 관련 파일 다운로드

**밑의 파일들이 없다면 CM 설치할 수 없음**

- CDH-6.3.2-1.cdh6.3.2.p0.1605554-el7.parcel
- CDH-6.3.2-1.cdh6.3.2.p0.1605554-el7.parcel.sha1.txt
- CDH-6.3.2-1.cdh6.3.2.p0.1605554-el7.parcel.sha256
- cm6.3.1-redhat7.tar.gz
- manifest.json
- allkeys.asc

위 파일들을 `/home/ubuntu/cm6` 디렉토리에 저장

## [03] Docker 설치

- docker 설치
    - ubuntu에서 docker 설치
        - 패키지 관리 툴 업데이트

            `sudo apt-get update`

        - 필수 패키지 설치

            `sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common`

        - GPG Key 인증

            `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

        - docker repository 등록

            `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
            
        - apt docker 설치

            `sudo apt-get update && sudo apt-get install docker-ce docker-ce-cli containerd.io`

        - 접속 계정에 docker 권한 부여

            `sudo usermod -aG docker $USER`

## [04] CentOS base 이미지 만들기

<span style="color:gray; background-color:#FFFFF0">/* ubuntu terminal */</span>

centos:7 이미지의 컨테이너 생성

`docker run -it --name centos-base centos:7`

생성한 centos-base 컨테이너 내부로 접속

`docker exec -it centos-base /bin/bash`

<br>
<span style="color:gray; background-color:#FFFFF0">/* centos-base container */</span>
```bash
yum update -y

yum install wget -y

yum install vim -y

yum install openssh-server openssh-clients openssh-askpass -y

yum install initscripts -y

yum install net-tools -y`

yum -y install java-1.8.0-openjdk-devel

ssh-keygen -t rsa -P '' -f ~/.ssh/id_dsa

cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys

ssh-keygen -f /etc/ssh/ssh_host_rsa_key -t rsa -N ""

ssh-keygen -f /etc/ssh/ssh_host_ecdsa_key -t ecdsa -N ""

ssh-keygen -f /etc/ssh/ssh_host_ed25519_key -t ed25519 -N ""

echo "/usr/sbin/sshd">>~/.bashrc

source ~/.bashrc
```

`exit`

<br>
<span style="color:gray; background-color:#FFFFF0">/* ubuntu terminal */</span>

위의 적용 사항들을 이미지화

`docker commit centos-base centos:base`

## [05] CM 설치

### [05.01] CM Server & Agent 컨테이너 (hadoop01) 생성 및 기본 세팅

<span style="color:gray; background-color:#FFFFF0">/* ubuntu terminal */</span>

컨테이너 생성 및 실행

`docker run --privileged --name hadoop01 -p 7180:7180 -p 80:80 -itd -h server01.hadoop.com -e container=docker -v /sys/fs/cgroup:/sys/fs/cgroup:ro centos:base /usr/sbin/init`

```
💡 
privileged 옵션이 없으면 ~ctl 명령어 실행이 안 됨
/cys/fs/cgroup 경로를 공유하지만 read only 권한만 줌
```
<br>
<span style="color:gray; background-color:#FFFFF0">/* ubuntu terminal */</span>

서버에 저장해둔 설치관련 파일은 hadoop01 컨테이너 내부로 복사

`docker cp /home/ubuntu/cm6 hadoop01:/root/`

<br>
<span style="color:gray; background-color:#FFFFF0">/* hadoop01 container */</span>

아파치 웹서버 설치 & 활성화 & 부팅시 자동 실행 설정

`yum -y install httpd`

`systemctl start httpd` 

`systemctl enable httpd` 

실행 확인

`systemctl status httpd`

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled.png)

 

브라우저에서 ip주소를 입력해 httpd가 제대로 실행되고 있는지 확인

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 1.png)


#### [05.01.01] 레포지토리 생성
cloudera에서 CM6.3.1 레포지토리를 지원하지 않기 때문에 다운받은 설치파일을 올릴 자체 레포지토리웹서버를 만듦

- cm6 디렉토리 생성

    `mkdir -p /var/www/html/cloudera-repos/cm6`

- /var/www/html/cloudera-repos/cm6 디렉토리에 cm6.3.1-redhat7 압축 해제

    `cd /root/cm6`

    `tar xvfz cm6.3.1-redhat7.tar.gz -C /var/www/html/cloudera-repos/cm6 --strip-components=1`

- cloudera rpm 파일이 /var/www/html/cloudera-repos/cm6/RPMS/x86_64 디렉토리에 있는지 확인

    ![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 2.png)

- /var/www/html/cloudera-repos/cm6 디렉토리에 allkeys.asc 파일 복사

    `cd /root/cm6`

    `cp allkeys.asc /var/www/html/cloudera-repos/cm6`

- repo 파일 생성

    ```
    vi /etc/yum.repos.d/cloudera-manager.repo

    [cloudera-repo]
    name=cloudera-manager
    baseurl=http://52.78.103.71/cloudera-repos/cm6/
    enabled=1
    gpgcheck=0
    ```

- 활성화된 레포지토리 확인

    `yum repolist`

    ![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 3.png)

    `yum list available | grep cloudera`

    ![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 4.png)

- 브라우저에서 http://52.78.103.71/cloudera-repos 에 접속해 레포지토리가 제대로 적용되었는지 확인

    ![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 5.png)

#### [05.01.02] cloudera daemon, server, agent, db 설치

cloudera daemon, server, agent, db 설치

`yum install -y cloudera-manager-daemons cloudera-manager-agent cloudera-manager-server cloudera-manager-server-db-2`

설치확인

`yum list installed | grep cloudera`

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 6.png)

db 실행 및 실행확인

- Cloudera Manager 및 기타 서비스 메타데이터를 저장할 기본 데이터베이스인 cloudera-scm-server-db 실행

    `systemctl start cloudera-scm-server-db`

    `systemctl status cloudera-scm-server-db`

    ![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 7.png)

embedded database 사용 설정

`vi /etc/cloudera-scm-server/db.properties`

com.cloudera.cmf.db.setupType의 값이 EMBEDDED가 아니라면 EMBEDDED로 수정

`com.cloudera.cmf.db.setupType=EMBEDDED`

CM 서버 실행 및 확인

`systemctl start cloudera-scm-server`

`systemctl status cloudera-scm-server`

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 8.png)

(log4j 경고는 무시)

CM 에이전트 실행 및 확인

`systemctl start cloudera-scm-agent`

`systemctl status cloudera-scm-agent`

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 9.png)

브라우저에서 <IP>:7180 을 입력해 CM 웹 로그인 페이지 확인

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 10.png)

cdh 디렉토리 생성

`mkdir /var/www/html/cloudera-repos/cdh`

/var/www/html/cloudera-repos/cdh 디렉토리에 ~.parcel, ~.parcel.sha1.txt, ~parcel.sha256, manifest.json 4개 파일 복사

`cd /root/cm6`

`cp CDH-6.3.2-1.cdh6.3.2.p0.1605554-el7.parcel /var/www/html/cloudera-repos/cdh`

`cp CDH-6.3.2-1.cdh6.3.2.p0.1605554-el7.parcel.sha1.txt /var/www/html/cloudera-repos/cdh/`

`cp CDH-6.3.2-1.cdh6.3.2.p0.1605554-el7.parcel.sha256 /var/www/html/cloudera-repos/cdh/`

`cp manifest.json /var/www/html/cloudera-repos/cdh/`

### [06] CM Agent 컨테이너 (hadoop02 & hadoop03) 생성 및 기본 세팅

<span style="color:gray; background-color:#FFFFF0">/* ubuntu terminal */</span>

`docker run --privileged --name hadoop02 --link hadoop01:hadoop01 -itd -h server02.hadoop.com -e container=docker -v /sys/fs/cgroup:/sys/fs/cgroup:ro centos:base /usr/sbin/init`

`docker run --privileged --name hadoop03 --link hadoop01:hadoop01 -itd -h server03.hadoop.com -e container=docker -v /sys/fs/cgroup:/sys/fs/cgroup:ro centos:base /usr/sbin/init`

<span style="color:gray; background-color:#FFFFF0">/* All container */</span>

로컬 DNS 구성

`vi /etc/hosts`
<br><br>172.17.0.2 [server01.hadoop.com](http://server01.hadoop.com) hadoop01
<br><br>172.17.0.3 [server02.hadoop.com](http://server01.hadoop.com) hadoop02
<br><br>172.17.0.4 [server03.hadoop.com](http://server01.hadoop.com) hadoop03

```
💡 각 컨테이너의 프라이빗 ip 주소는 hostname -i 명령어로 확인

```

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 11.png)

<span style="color:gray; background-color:#FFFFF0">/* All Container */</span>

root 계정 비밀번호 설정

`passwd` → adminuser로 모두 적용

루트 로그인 허용 
`vi /etc/ssh/sshd_config`
PermitRootLogin yes 주석을 풀거나 적어줌

`systemctl restart sshd.service`

<span style="color:gray; background-color:#FFFFF0">/* hadoop02 & hadoop03 container */</span>

repo 파일 생성

`vi /etc/yum.repos.d/cloudera-manager.repo`

```
[cloudera-repo]
name=cloudera-manager
baseurl=http://52.78.103.71/cloudera-repos/cm6/
enabled=1
gpgcheck=0
```

CM daemons, agent 설치

`yum install -y cloudera-manager-daemons cloudera-manager-agent`

CM agent config.ini 파일 수정

`vi /etc/cloudera-scm-agent/config.ini`

server_host=localhost 를 server_host=<server01 IP> 로 변경 (현재 글 기준 ip는 172.17.0.2)

CM 에이전트 실행 및 확인

`systemctl start cloudera-scm-agent`

`systemctl status cloudera-scm-agent`

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 12.png)

브라우저에서 <IP>:7180 에 접속해 로그인 창이 뜨면 ID: admin, PW: admin 을 입력해 로그인

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 13.png)

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 14.png)

cloudera express 선택

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 15.png)

현재 관리되는 호스트에서 세 호스트를 모두 선택

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 16.png)

방식 선택에서 Parcel 사용 체크후 추가옵션 클릭

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 17.png)

원격 Parcel 리포지토리 URL에 http://52.78.103.71/cloudera-repos/cdh/ 입력

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 18.png)

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 19.png)

I understand the risks, let me continue with cluster setup. 선택

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 20.png)

사용자 지정 서비스 선택후, HDFS, YARN, ZooKeeper 선택

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 21.png)

위 사진과 같이 역할 설정

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 22.png)

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 23.png)

다른 옵션은 기본값으로 두고 

DadaNode 데이터 디렉토리 : /dfs/dn

NameNode 데이터 디렉토리 : /dfs/nn

HDFS 체크포인트 디렉토리 : /dfs/snn

NodeManager 로컬 디렉토리 : /yarn/nm

으로 설정

![Untitled](/assets/images/postImages/2023-10-05-162941-클라우드 환경에서 Docker를 이용해 Cloudera Manager CDH 6.3.1 설치/Untitled 24.png)

클러스터 구성 완료

Cloudera Manager의 Cluster에 빨간 느낌표(❗️ ,상태불량)가 떠도 당황하지 말자. 자원부족 등의 문제인데 서비스가 중지상태만 아니면 괜찮다.

Cloudera Management Service는 모니터링의 역할을  한다. 많은 자원을 사용하기 때문에 중지해도 무관(❗️가 사라지는 것을 볼 수 있음)