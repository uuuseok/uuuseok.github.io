---
layout: post
title: 아파치 카프카 기초 [3] - 브로커, 복제, ISR(In-Sync-Replication)
subtitle: 브로커, 복제, ISR(In-sync-Replication)
author: useok
banner:
  image: assets/images/banners/home.jpeg
  opacity: 0.618
  background: "#000"
  height: "40vh"
  min_height: "10vh"
  heading_style: "font-size: 2.5rem; font-weight: bold;"
  subheading_style: "color: gold"
categories: [apache kafka]
tags: [apache kafka, broker, replication, ISR]
sidebar: [article-menu,category-list] 
---
## 브로커, 복제, ISR(In-Sync-Replication)

복제(replication)는 카프카 아키텍쳐의 핵심으로, 클러스터에서 서버가 장애가 생길 때 가용성을 보장하는 가장 좋은 방법이 복제

카프카 브로커는 카프카가 설치되어 있는 서버 단위를 말하며, 보통 3개 이상의 broker로 구성하여 사용하는 것을 권장

![Untitled](/assets/images/postImages/2023-02-27-152721-아파치 카프카 기초 [3] - 브로커, 복제, ISR(In-Sync-Replication)/Untitled.png)

만약 파티션이 1개이고 replication이 1인 topic이 존재하고 브로커가 3대라면, 브로거 3대중 1대에 해당 토픽의 데이터가 저장

replication은 파티션의 복제를 뜻함

- replication이 1이라면 파티션은 1개만 존재한다는 것이고,
- replication이 2라면 파티션은 원본 1개와 복제본 1개로 총 2개가 존재,
- replication이 3이라면 원본 1개와 복제본 2개로 총 3개가 존재

![Untitled](/assets/images/postImages/2023-02-27-152721-아파치 카프카 기초 [3] - 브로커, 복제, ISR(In-Sync-Replication)/Untitled 1.png)

브로커 개수에 따라 replication 개수가 제한됨. 최대 replication의 수는 broker의 수와 같음

원본 1개의 파티션은 Leader Partition이라고 부르며, 프로듀서가 토픽으로 데이터를 전달할 때, 데이터를 받는 파티션

나머지 복제본 파티션은 Follower Partition이라고 부름

이 Leder, Follower Partition을 합쳐서 ISR(In Sync Replica)라고 볼 수 있음

replication은 파티션의 고가용성을 위해 사용됨

- 브로커가 3개인 카프카에서 replication이 1이고 파티션이 1인 토픽이 존재한다고 가정하고, 브로커가 어떠한 이유로 사용불가하게 된다면 더이상 해당 파티션은 복구할 수 없음
- replication이 2인 경우, 브로커 한 개가 죽더라도 복제본(follower partition)이 존재하므로 복구 가능(나머지 1개가 남은 Follower Partition이 Leader Partition 역할을 승계)

프로듀서에는 ack라는 상세 옵션이 있는데, ack를 통해 고가용성을 유지할 수 있음

ack는 0, 1, all 옵션 3개중 하나를 선택해 사용할 수 있음

- 0 : 프로듀서는 Leader Partition에 데이터를 전송하고, 응답값은 받지 않음
- 1 : 프로듀서는 Leader Partition에 데이터를 전송하고, 응답값을 받음
- all : 프로듀서는 Leader Partition에 데이터를 전송하고,  Leader Partion 뿐만 아니라, 복제가 잘 이루어졌는지 Follower Partition에게도 응답값을 받음

파티션 수를 결정할 때, 지연율과 안정성 trade-off 고려해야 함
<br><br>3개 이상의 브로커를 사용할 때 replication은 3으로 설정하는 것을 추천


---

참고 강의 : [https://www.inflearn.com/course/아파치-카프카-입문/dashboard](https://www.inflearn.com/course/%EC%95%84%ED%8C%8C%EC%B9%98-%EC%B9%B4%ED%94%84%EC%B9%B4-%EC%9E%85%EB%AC%B8/dashboard)

