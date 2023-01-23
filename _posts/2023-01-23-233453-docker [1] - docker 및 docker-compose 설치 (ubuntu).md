---
layout: post
title: docker [1] - docker 및 docker-compose 설치 (ubuntu)
subtitle: docker 및 docker-compose 설치 (ubuntu)
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
tags: [docker, docker-compose]
sidebar: [article-menu,category-list] 
---
## docker 및 docker-compose 설치 (ubuntu)

### Docker 설치
<br><br>1. 최신 패키지 리스트 업데이트

`sudo apt update`
<br><br>2. docker 다운로드를 위해 필요한 https 관련 패키지 설치

`sudo apt install -y apt-transport-https ca-certificates curl software-properties-common`
<br><br>3. docker repository 접근을 위한 GPG key 설정

`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
<br><br>4. docker repository 등록

`sudo add-apt-repository -y "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"`
<br><br>5. 등록한 docker repository 까지 포함하여 최신 패키지 리스트 업데이트

`sudo apt update`
<br><br>6. docker 설치

`sudo apt install -y docker-ce`
<br><br>7. docker 실행 중임을 확인

`sudo systemctl status docker`

#### sudo 명령 없이 docker 명령어 사용하기 설정
<br><br>1. 현 사용자 ID를 docker group에 포함

`sudo usermod -aG docker ${USER}`
<br><br>2. 터미널 끊고, 다시 ssh로 터미널로 접속
<br><br>3. 현 ID가 docker group에 포함되어 있는지를 확인하는 명령

`id -nG`

### docker-compose 설치
<br><br>1. docker compose 설치

`sudo curl -L "https://github.com/docker/compose/releases/download/1.28.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
<br><br>2. 실행 권한 주기

`sudo chmod +x /usr/local/bin/docker-compose`
<br><br>3. 다음 명령 실행시 버전 확인이 가능하면 성공

`docker-compose --version`