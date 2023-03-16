---
layout: post
title: ROS2 기초 [1] - Docker 환경에서 ROS2 Turtlesim 실행
subtitle: Docker 환경에서 ROS2 Turtlesim 실행
author: useok
banner:
  image: assets/images/banners/home.jpeg
  opacity: 0.618
  background: "#000"
  height: "40vh"
  min_height: "10vh"
  heading_style: "font-size: 2.5rem; font-weight: bold;"
  subheading_style: "color: gold"
categories: [ROS2]
tags: [ROS2, docker, turtlesim, XQuartz]
sidebar: [article-menu,category-list] 
---
## ROS2 기초 [1] - Docker 환경에서 ROS2 Turtlesim 실행

### 01. XQuartz(X11) 설치 및 설정 (Intel MacOS 기준)

- 해당 포스트는 실제 로봇이 아닌 Tutlesim 시뮬레이터를 이용해 실제 로봇을 제어하는 것처럼 GUI를 통해 로봇을 제어
- X11을 이용하여 Docker 환경에서 동작하는 GUI를 Local PC에서 확인

```bash
# xquartz 설치
brew install --cask xquartz
```

`open -a XQuartz` 또는 런치패드에서 직접 실행

설치한 xquartz를 실행후 설정의 보안 탭에서 “연결 인증”과 “네트워크 클라이언트에서의 연결을 허용” 모두 체크

![Untitled](/assets/images/postImages/2023-03-16-203614-ROS2 기초 [1] - Docker 환경에서 ROS2 Turtlesim 실행/Untitled.png)

X11 재실행 또는 PC 재부팅(재실행 후 적용이 안 되면 PC재부팅 하는걸로!)

터미널에 `xhost +localhost` 명령어를 입력해 localhost에서의 접속을 허용

### 02. docker container 생성

```bash
docker run -itd -e "DISPLAY=host.docker.internal:0" --name ros2 osrf/ros:foxy-desktop
```

이후 모든 터미널 명령어는 docker container 내부에서 실행

### 03. source setup.bash 자동화

터미널을 실행할 때마다 `source /opt/ros/foxy/setup.bash` 명령어를 입력해야 ROS2 명령어를 실행시킬 수 있음. 매번 입력하기 번거로우니, 해당 명령어를 .bashrc 에 추가하여 사용자가 새 쉘을 열 때마다 실행되게 함

#### 3-1. 방법 1 - 직접 파일 수정

```bash
docker exec -it ros2 /bin/bash

apt-get update
apt-get install vim

vim ~/.bashrc

## .bashrc 파일에 밑의 명령어 작성후 저장
source /opt/ros/foxy/setup.bash
```

#### 3-2. 방법 2 - 코드를 통해 파일 수정

```bash
docker exec -it ros2 /bin/bash

echo "source /opt/ros/foxy/setup.bash">>~/.bashrc
```

![Untitled](/assets/images/postImages/2023-03-16-203614-ROS2 기초 [1] - Docker 환경에서 ROS2 Turtlesim 실행/Untitled 1.png)

### 04. 작동 확인(선택사항)

Publisher와 Subscriber가 제대로 데이터를 주고 받는지 확인

터미널 두 개를 열고, 하나는 `ros2 run demo_nodes_cpp talker`, 다른 하나는 `ros2 run demo_nodes_py listener` 를 입력해 메시지 발행과 구독이 정상적으로 이루어지고 있는 지 확인

![Untitled](/assets/images/postImages/2023-03-16-203614-ROS2 기초 [1] - Docker 환경에서 ROS2 Turtlesim 실행/Untitled 2.png)

### 05. turtlesim 설치 및 제어

- turtlesim이란 ROS 및 ROS 패키지를 교육하기 위해 만들어진 툴로, 실제 로봇이 없더라도 2D 시뮬레이션을 통해 로봇의 동작을 확인할 수 있음

```bash
#tutlesim 설치
apt-get install ros-foxy-turtlesim

## 실행
ros2 run turtlesim turtlesim_node

## 거북이 로봇을 제어하기위한 노드 실행 
ros2 run turtlesim turtle_teleop_key
```

`ros2 run turtlesim turtle_teleop_key` 를 실행한 터미널에서 방향키를 이용해 로봇 제어 가능

![Untitled](/assets/images/postImages/2023-03-16-203614-ROS2 기초 [1] - Docker 환경에서 ROS2 Turtlesim 실행/Untitled 3.png)