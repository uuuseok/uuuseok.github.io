---
layout: post
title: Jetson AGX Xavier 초기화 및 JetPack 설치
subtitle: Jetson AGX Xavier 초기화 및 JetPack 설치
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
tags: [jetson, jetpack]
sidebar: [article-menu,category-list] 
---
## Jetson AGX Xavier 초기화 및 JetPack 설치

### [00.] 준비사항

- Jetson AGX Xavier
- ubuntu PC
- CtoC 또는 CtoA 케이블

### [01.] 초기화

- 젯슨이 꺼진 상태에서 진행(불이 안 들어와야 함)
- 밑 사진의 가운데 버튼인 리커버리 버튼을 누른 상태에서 왼쪽 전원버튼을 누름
- 불이 들어오는 것 확인
    
    ![Untitled](/assets/images/postImages/2023-12-08-160247-Jetson AGX Xavier 초기화 및 JetPack 설치/Untitled.png)
    
- 불이 들어오는 곳 옆의 C Type 포트와, ubuntu PC의 C type 또는 USB로 연결

### [02.] SDK Manager 설치 및 실행

- ubuntu PC에서 [https://developer.nvidia.com/embedded/jetpack](https://developer.nvidia.com/embedded/jetpack) 링크 접속해 SDK Manager 설치(nvidia 로그인 필요)
    
    ![Untitled](/assets/images/postImages/2023-12-08-160247-Jetson AGX Xavier 초기화 및 JetPack 설치/Untitled 1.png)
<br><br><br><br>    
- SDK Manager 실행(로그인 필요)
    
    ![Untitled](/assets/images/postImages/2023-12-08-160247-Jetson AGX Xavier 초기화 및 JetPack 설치/Untitled 2.png)
<br><br><br><br>    
- 가지고 있는 기기에 맞게 Target Hardware 설정
    
    ![Untitled](/assets/images/postImages/2023-12-08-160247-Jetson AGX Xavier 초기화 및 JetPack 설치/Untitled 3.png)
    
    
    
<br><br><br><br>
- 설치 진행
    
    ![Untitled](/assets/images/postImages/2023-12-08-160247-Jetson AGX Xavier 초기화 및 JetPack 설치/Untitled 4.png)
    다운로드 경로 확인 후 라이센트 동의 체크
<br><br><br><br>
    설치 중 몇 가지 설치 옵션이 나오는데,
    
    ![Untitled](/assets/images/postImages/2023-12-08-160247-Jetson AGX Xavier 초기화 및 JetPack 설치/Untitled 5.png)

    다른 옵션은 디폴트로 두고, user와 password 설정
<br><br><br><br>
    ![Untitled](/assets/images/postImages/2023-12-08-160247-Jetson AGX Xavier 초기화 및 JetPack 설치/Untitled 6.png)
    
    이 과정에서 오류가 발생했으나, 몇 번의 Retry 후 오류가 사라짐
<br><br><br><br>    
- 설치 완료
    
    ![Untitled](/assets/images/postImages/2023-12-08-160247-Jetson AGX Xavier 초기화 및 JetPack 설치/Untitled 7.png)
    
    FINISH AND EXIT를 누르면 SDK Manager가 종료되고, 젯슨 접속해보면 성공적으로 설치가 된 것을 볼 수 있음