---
title: redhat 6.2 ssh install
draft: "true"
---
# Overview
시스템 해킹을 배울 때 redhat 6.2를 사용하여 실습을 진행하는 경우가 많습니다. 그러나 일반적인 x86 CPU를 쓰는 윈도우 컴퓨터가 아니라 맥북을 쓸 땐 VMware나 redhat 6.2를 설치하기 어렵습니다.
그래서 보통 집에 있는 윈도우 데스크탑에 redhat 6.2를 설치 후 ssh를 통해서 접속하려고 하지만, 해당 redhat 6.2에는 ssh가 설치되어 있지 않고 패키지 매니저도 없기 때문에 ssh를 설치하기도 어렵습니다. 
이번에는 redhat 6.2에 ssh 서버를 설치하고 외부 환경에서 접속하는 실습을 진행하겠습니다. 

제 환경은 다음과 같습니다.
- 윈도우11 데스크탑 - redhat 6.2 호스팅
- Macbook pro m4 - 해당 기기에서 윈도우11 데스크탑의 redhat 6.2로 접속해야합니다. 
# 1. ssh2 설치
## 1.1 ssh 파일을 iso로 굽기
VMware에 띄워져 있는 redhat 6.2에 ssh 파일을 전송하기 위해서 iso로 구워줍니다. 
## 1.2 ssh2 설치
이제 `/dev/cdrom` 경로에 VMware를 통해 전송했던 ssh iso 파일이 있을겁니다. 해당 경로를 mount 해주고 ssh 파일을 홈 디렉터리 경로로 복사해줍니다. 
make
make install . . .
# 2. 포트포워딩 설정
# 3. Termius로 ssh 접속하기
redhat 6.2와 redhat 6.2에 설치했던 ssh2 모두 옛날 버전이라서 현대 ssh 시스템과는 호환성이 떨어집니다. 그래서 일반적인 openssh client로 접속을 하려고 하면 다음과 같이 이상한 에러가 많이 뜹니다. 
사실 client 단에서 옛날 프로토콜로 맞춰서 접속할 수 있게 할 수는 있지만, 번거롭기 때문에 MacOS 환경에서 자주 쓰이는 ssh 클라이언트인 **Termius**를 썼습니다.

# 4. 그 외 예외 상황을 위한 대처
## 4.1 redhat 6.2 재실행
redhat 6.2를 재실행하고 싶은 경우가 있을텐데, 기본적으로 redhat 6.2 접속 시 `linux-up`을 입력해야 redhat 6.2가 켜집니다. 그래서 외부 원격접속한 상태에서 재실행했을 때 `linux-up`을 입력할 수 있는 방법이 없기 때문에, 자동으로 `linux-up`을 통해 redhat 6.2가 켜지도록 설정해야합니다. 
## 4.2 ssh2 start-up 프로그램으로 등록
redhat 6.2를 재실행할 때마다 ssh2 server 또한 수동으로 켜줘야합니다. 이를 자동화할 수 있도록 start-up 프로그램으로 등록해두는 것이 편리합니다.