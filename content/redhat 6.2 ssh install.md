---
title: redhat 6.2 ssh install
draft: "false"
---
# Overview
시스템 해킹을 배울 때 redhat 6.2를 사용하여 실습을 진행하는 경우가 많습니다. 그러나 일반적인 x86 CPU를 쓰는 윈도우 컴퓨터가 아니라 맥북을 쓸 땐 VMware나 redhat 6.2를 설치하기 어렵습니다.
그래서 보통 집에 있는 윈도우 데스크탑에 redhat 6.2를 설치 후 ssh를 통해서 접속하려고 하지만, 해당 redhat 6.2에는 ssh가 설치되어 있지 않고 패키지 매니저도 없기 때문에 ssh를 설치하기도 어렵습니다. 
이번에는 redhat 6.2에 ssh 서버를 설치하고 외부 환경에서 접속하는 실습을 진행하겠습니다. 

제 환경은 다음과 같습니다.
- 윈도우11 데스크탑 - VMware redhat 6.2
- Macbook pro m4
# 1. ssh2 설치
## 1.1 ssh 파일을 iso로 굽기
https://packetstorm.news/files/id/23994/
위 주소에서 `ssh-2.4.0.tar.gz` 파일을 다운로드합니다. VMware로 실행한 redhat 6.2에 해당 파일을 전달하려면 iso로 구워야합니다. 
먼저 아무 디렉터리로 이동하여 ssh 디렉터리를 생성 후 아까 받은 `ssh-2.4.0.tar.gz` 파일을 ssh 디렉터리 내에 복사합니다. 
![[Pasted image 20251112232702.png]]
위 이미지와 같이 Folder2ISO라는 프로그램을 사용하여 iso 파일로 구워줍니다. 해당 프로그램이 아니더라도 iso 파일로 구울 수 있으면 어떤 걸 써도 상관없습니다. 
## 1.2 ssh2 설치
이전에 생성한 `ssh.iso` 파일을 VMware redhat 6.2에 전달해야합니다. 
![[Pasted image 20251112233615.png|450]]
위 이미지와 같이 VMware의 redhat 6.2 인스턴스의 설정화면으로 들어갑니다. 그리고 해당 화면에서 CD/DVD 설정으로 들어갑니다. 

![[Pasted image 20251112233706.png|450]]
위 이미지와 같이 **Use ISO image file 설정**에 `ssh.iso` 파일의 경로를 저장해줍니다. **Device status** 또한 위 이미지처럼 **모두 활성화**했습니다. 

이제 `/dev/cdrom` 경로에 ssh 파일이 VMware를 통해 전달되었습니다. 
![[Red Hat Linux test-2025-11-12-23-42-32.png|450]]
위 이미지와 같이 `mount /dev/cdrom /mnt` 명령어를 통해 `/mnt` 경로에 마운트를 해주었습니다. 그리고 해당 경로로 가보니 ssh 파일이 존재하는 것을 확인할 수 있습니다. 

![[Red Hat Linux test-2025-11-12-23-44-56.png|450]]
`cp ssh-2.4..gz ~/ssh-2.4.0.tar.gz` 명령어를 통해 해당 ssh 파일을 홈 디렉터리로 복사했습니다. 그리고 `umount /mnt`로 마운트 해제했습니다. 

![[Red Hat Linux test-2025-11-12-23-47-23.png|450]]
`tar -xvzf ssh-2.4.0.tar.gz`를 실행하여 압축을 풀어주었습니다. 위 이미지는 압축 해제 결과를 나타냅니다. 

![[Red Hat Linux test-2025-11-12-23-51-55.png|450]]
`cd ssh-2.4.0`을 실행하여 해당 경로로 이동합니다. 그리고 다음 명령어들을 실행하여 ssh 설치를 진행합니다. 위 이미지는 설치 완료 이후를 나타냅니다.
```sh
./configure
make
make install
```

![[Red Hat Linux test-2025-11-12-23-54-01.png|450]]
설치 이후 위 이미지와 같이 `sshd2.startup` 파일이 생성됩니다. 

![[Red Hat Linux test-2025-11-12-23-54-18.png|450]]
해당 파일을 실행해보면 위와 같이 사용법이 출력됩니다. 

![[Red Hat Linux test-2025-11-12-23-54-38.png|450]]
`./sshd2.startup start` 를 통해 ssh를 실행시킨 결과입니다. 
# 2. 포트포워딩 설정
## 2.1 VMware 포트포워딩 설정
![[Pasted image 20251113000205.png|450]]
VMware의 상단 메뉴바에서 Edit > Virtual Network Editor에 접속합니다. 포트포워딩 설정 추가에 앞서, **Change Settings 버튼**을 눌러 권한을 부여해줍니다. 

![[Pasted image 20251113000411.png|450]]
권한 부여 이후에 VMnet8을 클릭 후 NAT Settings 버튼을 누릅니다. 

![[Pasted image 20251113000627.png|450]]
Add 버튼을 누릅니다. 

![[Red Hat Linux test-2025-11-12-23-59-08.png|450]]
![[Pasted image 20251113000747.png]]
redhat 6.2에서 `ifconfig`로 내부 ip를 알아낼 수 있습니다. 위 이미지에서는 `192.168.150.136`을 쓰는 것으로 나타났습니다. 이를 포트포워딩 추가할 때 **Virtual machine IP address** 입력칸에 써줍니다. **Virtual machine port**에는 22번을 써줍니다. 
**Host port**에는 1234번을 입력했습니다. 다른 포트 번호를 써도 무방합니다. 
포트포워딩을 추가했다면, Apply 후 OK를 눌러 화면을 나오면 됩니다. 
## 2.2 공유기 포트포워딩 설정
각 공유기마다 포트포워딩 설정 인터페이스가 다르므로 상세히 설명하진 않겠습니다. 

![[Pasted image 20251113001541.png|500]]
보통 이러한 방식으로 공유기 포트포워딩 규칙을 추가할 수 있습니다. 여기서 **서비스 포트**는 1234를 썼으나 다른 번호를 써도 무방합니다.
그러나 **내부 IP 주소**는 위의 redhat 6.2에서 확인하였던 ip 주소가 아닌, **윈도우 데스크탑**에서 확인할 수 있는 내부 ip를 입력해주어야합니다. 그리고 **내부 포트**는 위의 VMware 포트포워딩 설정에서 **Host port**에 썼던 값을 그대로 써야합니다. 따라서 1234를 써주었습니다. 
## 2.3 윈도우 방화벽 설정
![[Pasted image 20251113002010.png|450]]
위와 같이 윈도우의 **방화벽 및 네트워크 보호** 설정 화면으로 들어옵니다. 여기서 **고급 설정**에 접속합니다. 

![[Pasted image 20251113002210.png|450]]
위 이미지와 같이 **Inbound Rules**에서 **New Rule**에 접속해 새로운 규칙을 생성합니다. 

![[Pasted image 20251113002323.png|450]]
해당 화면에서는 **Port**를 선택합니다.

![[Pasted image 20251113002348.png|450]]
해당 화면에서는 공유기 포트포워딩에서 설정했던 **서비스 포트**의 번호를 입력합니다. 

![[Pasted image 20251113002402.png|450]]
![[Pasted image 20251113002417.png|450]]
![[Pasted image 20251113002439.png|450]]
그 외 설정 화면입니다. 설명은 생략하겠습니다. 
이렇게 새로운 방화벽 규칙을 생성했습니다. 이제 외부 환경에서 redhat 6.2로 접속할 준비가 끝났습니다. 
# 3. Termius로 ssh 접속하기
redhat 6.2와 redhat 6.2에 설치했던 ssh 모두 구버전이라서 현대 ssh client로는 잘 들어가지지 않는 경우가 있습니다. 
그래서 제가 찾았던 ssh client는 **Termius**입니다. 

https://termius.com/download/macos
맥북 사용자의 경우 위 링크를 참조하여 termius를 설치합니다. 

![[Pasted image 20251113003323.png|450]]
termius의 메인 화면입니다. **NEW HOST** 버튼을 눌러줍니다. 

![[Pasted image 20251113003637.png|450]]
**Address**에는 공유기의 외부 ip 주소를 입력하면 됩니다. **포트 번호** 또한 공유기 포트포워딩 과정에서 설정하였던 서비스 포트의 번호를 입력합니다. 그 외 **Credentials** 또한 각자 계정에 맞게 입력해줍니다. 

![[Pasted image 20251113003947.png|450]]
정상적으로 잘 접속된 모습입니다. 

![[Pasted image 20251113004034.png|450]]
`clear` 명령어를 실행한 결과, **'xterm-256color': unknown terminal type**이라는 에러가 출력됩니다. 이 외에도 안되는 명령어가 많을겁니다.

![[Pasted image 20251113004229.png|450]]
다시 해당 호스트의 설정 화면으로 들어옵니다. Startup Command에 다음 코드를 입력합니다.
```
export TERM=xterm
```

![[Pasted image 20251113004429.png|450]]
저장 이후에는 `clear` 명령어가 정상적으로 실행될 것입니다. 

여기까지만 따라해도 ssh로 접속해서 시스템 해킹의 거의 모든 실습을 진행할 수 있습니다.
# 4. 그 외 예외 상황을 위한 대처
## 4.1 redhat 6.2 재실행
기본적으로 redhat 6.2 부팅시 `linux-up`을 입력해야 redhat 6.2가 켜집니다. 따라서 원격 접속 상태에서 redhat 6.2를 재실행하게 되면 `linux-up`을 입력하지 못하고 결국 redhat 6.2가 완전히 실행되지 않습니다. 

재실행을 하더라도 자동으로 `linux-up`을 실행하도록 설정해야합니다. 
![[Red Hat Linux test-2025-11-13-00-53-04.png|450]]
`vi /etc/lilo.conf`를 실행하여 `/etc/lilo.conf` 파일을 편집해야합니다. 
`default=linux`라고 써진 라인을 `default=linux-up`으로 수정해주고 저장합니다. 

![[Red Hat Linux test-2025-11-13-01-18-55.png|450]]
그리고 `/sbin/lilo` 명령어를 실행합니다. 

![[Red Hat Linux test-2025-11-13-01-23-50.png|450]]
이제 재실행을 하더라도 위 이미지와 같이 자동으로 `linux-up`이 실행되어 redhat 6.2가 정상적으로 실행될겁니다.
## 4.2 ssh2 start-up 프로그램으로 등록
redhat 6.2를 재실행할 때마다 ssh server 또한 수동으로 실행해야합니다. 이를 자동화할 수 있도록 start-up 프로그램으로 등록해두는 것이 효율적입니다. 
간단하게 다음 명령어를 실행하여 start-up 프로그램으로 등록할 수 있었습니다. 
```sh
echo "/root/ssh-2.4.0/sshd2.startup start" >> /etc/rc.d/rc.local
```