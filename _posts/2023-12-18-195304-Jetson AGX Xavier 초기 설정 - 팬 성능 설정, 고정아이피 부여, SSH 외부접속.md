---
layout: post
title: Jetson AGX Xavier 초기 설정 - 팬&성능 설정, 고정아이피 부여, SSH 외부접속
subtitle: Jetson AGX Xavier 초기 설정 - 팬&성능 설정, 고정아이피 부여, SSH 외부접속
author: useok
banner:
  image: assets/images/banners/home.jpeg
  opacity: 0.618
  background: "#000"
  height: "40vh"
  min_height: "10vh"
  heading_style: "font-size: 2.5rem; font-weight: bold;"
  subheading_style: "color: gold"
categories: [jetson]
tags: [jetson-stats, jtop, fan, power mode, netplan, RSA, SSH]
sidebar: [article-menu,category-list] 
---
## Jetson AGX Xavier 초기 설정 - 팬&성능 설정, 고정아이피 부여, SSH 외부접속

### [00.] jetson-stats  설치

jetson-stats는 NVIDIA Jetson 시리즈 보드를 분석, 모니터링 및 제어할 수 있도록 도와주는 도구

```bash
sudo apt-get install python3-pip
sudo -H pip3 install -U jetson-stats
```

### [01.] 팬 설정

- 종류 : [quiet, cool, manual]
    

#### [01.01.] 방법 1. nvfancontrol.conf
    
`vi /etc/nvfancontrol.conf` 의 FAN_DEFAULT_PROFILE 값 변경
    

#### [01.02.] 방법 2. jtop

`jtop` 의 CTRL 탭에서 FAN의 profiles 변경

![Untitled](/assets/images/postImages/2023-12-18-195304-Jetson AGX Xavier 초기 설정 - 팬 성능 설정, 고정아이피 부여, SSH 외부접속/Untitled.png)

### [02.] 성능 설정

- 종류 : [0 ~ 7]
    
    ![Untitled](/assets/images/postImages/2023-12-18-195304-Jetson AGX Xavier 초기 설정 - 팬 성능 설정, 고정아이피 부여, SSH 외부접속/Untitled 1.png)
    
    
#### [02.01.] 방법 1. nvpmodel
    
`sudo nvpmodel -m <mode>`
    

#### [02.02.] 방법 2. jtop

`jtop` 의 CTRL 탭에서 power mode 변경

#### [02.03.] 변경사항 확인

`sudo nvpmodel -q`

### [03.] 고정아이피 부여

#### [03.01.] netplan 설치 및 설정

`sudo apt-get install netplan.io`

`sudo vi /etc/netplan/config.yaml`

```yaml
network:
	version: 2
	renderer: networkd
	ethernets:
		eth0:
			addresses:
				- 192.168.50.101/24
			gateway4: 192.168.50.1
			nameservers:
				addresses: [8.8.8.8, 8.8.4.4]
```

```
❗ addresses와 gateway4는 `ifconfig` 또는 `hostname -I` 명령어를 통해 아이피를 조회해보고 자신의 환경에 맞춰 설정

```

#### [03.02.] 설정사항 적용

`sudo netplan apply`

### [04.] SSH 외부접속

#### [04.01.] 보안강화 1. (포트변경)
<br><br>1. 일반적으로 ssh 연결에 22번 포트를 사용. 보안을 위해 포트 변경
    
`vi /etc/ssh/sshd_config`
    

![Untitled](/assets/images/postImages/2023-12-18-195304-Jetson AGX Xavier 초기 설정 - 팬 성능 설정, 고정아이피 부여, SSH 외부접속/Untitled 2.png)
<br><br>2. 기본 설정인 `#Port 22` 라고 되어 있는 부분을 “#” 을 지우고, 22를 변경하고자 하는 포트로 입력 `Port <new port>`
    
![Untitled](/assets/images/postImages/2023-12-18-195304-Jetson AGX Xavier 초기 설정 - 팬 성능 설정, 고정아이피 부여, SSH 외부접속/Untitled 3.png)
    
<br><br>3. ssh 재시작
    
`service ssh restart`
    

#### [04.02.] 보안강화 2. (SSH 키 생성)

```bash
# 2048 비트 RSA키 생성
ssh-keygen -t rsa -b 2048 -f jetson # 명렬어 실행시 jetson, jetson.pub 파일 생성됨

## authorized_keys 파일 생성
mkdir ~/.ssh
chmod 700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

## authorized_keys파일에 jetson.pub에 있는 텍스트 복사해 붙여넣기
vi authorized_keys

## jetson 파일은 jetson.pem으로 확장자를 변경하고 클라이언트에 저장
# 이후 클라이언트는 pem키를 사용해 ssh 연결
```

```bash
# sshd.config 수정
vi /etc/ssh/sshd_config

PermitRootLogin no # 루트계정 로그인 해제
PubkeyAuthentication yes # 공개키인증
PasswordAuthentication no # 패스워드 로그인 해제 
```

#### [04.03.] 포트포워딩

공유기 관리자 페이지에서 포트포워딩 접속

---

가상서버 - 사용함

IP 주소 - 앞서 설정한 고정 아이피 (192.168.50.101)

공인포트 - 새로 설정한 포트 입력 (1234)

사설포트 - 새로 설정한 포트 입력 (1234)

프로토콜 - TCP&UDP

---

```
❗ 나의 경우, LG LTE 라우터를 사용하기 때문에 브라우저에서 192.168.50.1에 접속하여 설정함.
다른 공유기 등을 사용하더라도 관리자 페이지에서 포트포워딩 기능을 제공하며, 설정할 내용도 모두 비슷함
```

 

#### [04.04.] 연결확인

ssh 클라이언트 프로그램을 사용하거나, ssh 명령어를 이용하여 접속

젯슨의 공인아이피, 계정, 새로 설정한 포트, 생성한 pem키를 이용하여 접속

```bash
ssh -i <pem키 경로> -p <포트> <계정>@<공인IP>
ssh -i /your/directory/jetson.pem -p 1234 nvidia@<공인IP>
```