---
layout: post
title: Jetson AGX Xavier - SSD를 부팅 디스크로 만들기
subtitle: Jetson AGX Xavier - SSD를 부팅 디스크로 만들기
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
tags: [disk format, boot disk, jetson]
sidebar: [article-menu,category-list] 
---
## Jetson AGX Xavier - SSD를 부팅 디스크로 만들기

Jetson AGX Xavier는 기본 스토리지 32GB를 제공하지만, 매우 적은 용량이기 때문에 SSD를 추가하고, 그 SSD를 부팅 디스크로 사용하고자 함

### [00.] 준비사항

- Jetson AGX Xavier
- M.2 NVMe SSD

### [01.] SSD 장착

- Jetson AGX Xavier 팬 부분을 분리하면 보드 중간쯤에 M.2 NVMe 형식의 SSD를 장착

### [02.] 디스크 포맷

- 젯슨에서 Disks를 찾아 실행
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled.png)
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled 1.png)
    
<br><br><br><br>  
- 왼쪽 디스크 리스트에서, 추가한 SSD를 찾아 선택하고, 오른쪽 상단의 … 표시를 눌러 Format Disk 클릭
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled 2.png)
    
<br><br><br><br>  
- Erase와 Partitioning은 디폴트 값인 Quick과 GPT 선택 후 포맷
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled 3.png)
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled 4.png)
    

### [03.] 파티션 생성

- 포맷이 완료된 상태에서 “+” 버튼을 눌러 파티션 생성
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled 5.png)
    
<br><br><br><br>  
- 파티션 크기는 SSD 용량 모두를 사용해도 무관하지만, 여유분 16GB를 남겨둠
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled 6.png)
    
<br><br><br><br>  
- Volume 이름을 지정하고 디폴트값 그대로 Ext4를 선택해 생성
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled 7.png)
    
<br><br><br><br>  
- “+” 버튼이 “▶︎” 버튼으로 바뀌었다면 성공
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled 8.png)
    

### [04.] 부팅디스크로 변경

직접 하려면 꽤 많은 시간을 들이고 고생을 했을 것 같지만, 이 과정을 스크립트로 작성해준 복 받을 분이 계심 - [https://github.com/jetsonhacks/rootOnNVMe](https://github.com/jetsonhacks/rootOnNVMe)

- 소스 다운로드
    
    `git clone https://github.com/jetsonhacks/rootOnNVMe.git`
    
    ![Untitled](/assets/images/postImages/2023-12-08-180339-Jetson AGX Xavier - SSD를 부팅 디스크로 만들기/Untitled 9.png)
    

- 다운받은 경로로 이동하여 스크립트 실행
    
    ```bash
    # 경로 이동
    cd rootOnNVMe
    
    # 실행(1)
    ./copy-rootfs-ssd.sh
    
    # 실행(2)
    ./setup-service.sh
    ```
    
- 재부팅
    
    `sudo reboot now`