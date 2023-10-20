---
layout: post
title: Remote Desktop 이용해 AWS EC2 Ubuntu GUI 연결
subtitle: Remote Desktop 이용해 AWS EC2 Ubuntu GUI 연결
author: useok
banner:
  image: assets/images/banners/home.jpeg
  opacity: 0.618
  background: "#000"
  height: "40vh"
  min_height: "10vh"
  heading_style: "font-size: 2.5rem; font-weight: bold;"
  subheading_style: "color: gold"
categories: [AWS]
tags: [AWS, EC2, ubuntu, remote desktop, GUI]
sidebar: [article-menu,category-list] 
---
## Remote Desktop 이용해 AWS EC2 Ubuntu GUI 연결

```
💡 개발환경
Server - AWS EC2 Ubuntu 22.04
Local PC - MacOS(Intel)

```

### [01.] EC2 Server

```bash
# 패키지 매니저 업데이트
sudo apt update

## 패키지 설치
sudo apt install ubuntu-desktop
sudo apt install xrdp

## 접속하려는 계정 암호 설정
sudo passwd ubuntu
```

AWS EC2 보안그룹 인바운드 규칙에서 RDP 3389 포트 뚫기

![Untitled](/assets/images/postImages/2023-10-20-115207-Remote Desktop 이용해 AWS EC2 Ubuntu GUI 연결/Untitled.png)

```
💡 (참고) xrdp 관련 설정
xrdp 설정파일 위치는 `/etc/xrdp/sesman.ini` 
해당 파일에서 포트, 루트로그인, 세션 등 설정

```

### [02.] Local PC

App Store에서 Microsoft Remote Desktop 설치

Add PC 클릭 후 접속하려는 서버 IP 기입 → 계정과 패스워드 입력 후 연결

![Untitled](/assets/images/postImages/2023-10-20-115207-Remote Desktop 이용해 AWS EC2 Ubuntu GUI 연결/Untitled 1.png)

![Untitled](/assets/images/postImages/2023-10-20-115207-Remote Desktop 이용해 AWS EC2 Ubuntu GUI 연결/Untitled 2.png)