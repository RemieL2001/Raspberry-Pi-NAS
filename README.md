# Raspberry-Pi-NAS
Raspberry Pi 4를 이용한 NAS(Network Attached Storage) 구축

NAS의 정의 : Network Attached Storage로 HDD 또는 SSD를 연결한 개인용 파일서버로서, 네트워크로 접속하여 데이터에 접근하는 용도의 저장장치 시스템이다.

NAS의 장점 : 보안측면(기업의 데이터센터에 데이터가 저장되는 것보다 자체 서버를 구성해서 직접 관리함으로써 보안에 강하다.), 비용측면(초기 비용은 클라우드 서비스 대비 다소 비싼 점이 있으나 장기적으로 봤을때 비용을 효율적으로 관리할 수 있다. 클라우드 서비스의 비용 증가도 원인), 효율성과 편의성(데이터를 자체적으로 관리함으로써 필요한 시점에 빠르게 데이터에 접근하고 공유가 가능하다.)

NAS의 단점 : 네트워크 연결이 필수적이다., 복잡한 관리(하드디스크 수명, 데이터 손실에 관한 대비 등 필요), 해킹의 취약성 등

단점이 존재하는데도 NAS를 구현하는 이유 : 자체 관리 용이성, 원격 액세스 및 공유, 중앙 집중화된 데이터 관리

구축 방법을 설명하기에 앞서 OMV 6 와 Nextcloud에 대해서 알아보자.

OpenMediaVault : NAS를 구축하기 위한 오픈소스 기반의 운영체제(OMV는 Linux Debian을 기반으로 하며 웹 인터페이스를 통해 사용자가 NAS시스템을 설정, 관리할 수 있다.)

NextCloud : 오픈소스 기반의 자체 호스팅 가능한 클라우드 서비스 플랫폼으로 파일 공유, 협업, 문서 편집 등이 가능하다.(개인 서버로 클라우드화 시키는 것임을 명심할 것!)

------ 설치 및 설정------
1. 라즈베리파이 OS 설치 (Raspbian) : OMV6를 설치하기 위해서는 64비트 Bullseye Raspbian OS Lite로 설치해야만 한다.(OS Lite는 Desktop버전이 아님) Desktop 버전은 OMV6에서 지원하지 않음. 참고로 설치 전에 Raspberry Pi Imager 설정에서 ssh설정과 와이파이 연결 설정을 하자.

설치가 다 되었으면 패키지 업데이트를 진행한다.

sudo apt update && sudo apt upgrade 
sudo apt-get update && sudo apt-get upgrade

그리고나서 vim 에디터를 설치하자(시스템 설정파일 수정을 위함)

sudo apt install vim

vim 에디터를 설치한 다음 ip를 고정해주자. (필자는 무선랜을 이용했기때문에 무선랜 연결은 ip가 유동적인 특성이 있어 임의로 ip를 고정해야함.)

sudo vim /etc/dhcpcd.conf 에서 

interface wlan0
static ip_address = <고정 할 ip주소>/24
static routers=<Gateway address>
static domain_name_servers=<DNS>

설정을 적용하고 변경사항 적용을 위해 재부팅한다.

sudo reboot

재부팅후 iP가 고정된 ip로 변경되므로  ifconfig을 입력해서 제대로 변경되었는지 확인하자.

ifconfig

설정한 ip로 변경되었으면 변경된 ip로 ssh 원격 접속을 하면 된다.(cmd, putty 등)

ssh 사용자이름@ip address -p 포트번호(기본은 22번)

보안 강화를 위해서 포트번호를 변경하고 싶으면 

sudo vim /etc/ssh/sshd_config 파일로 들어가서

상단부분에 Port 22라고 되어있는 부분에서 22를 원하는 포트번호로 변경해준다. 그리고 저장하고 빠져나온다.

그리고나서, 아래 명령어로 services 파일을 열어준다.

sudo vim /etc/services

ssh 부분에 22/tcp를 원하는 포트번호와 동일한 포트로 숫자만 바꾸고 저장하고 빠져나온다.

그런다음 재부팅 한다.

sudo reboot

재부팅이 되면 변경된 포트로 ssh 원격접속이 가능하다.

지금까지 작업한 것은 네트워크 내부망에 관한 설정만 했기 때문에 내부에서만 접속이 가능하고 외부에서는 접속 자체가 불가능하다. 내부 접속도 가능하게 하기 위해서는 포트포워딩이란 것을 해야하는데 구글에 검색하면 많은 정보를 확인할 수 있다. 그래서 자세한 설명은 생략한다.

포트포워딩을 해서 외부접속까지 되는 것을 확인했으면 OMV6를 설치해보도록 하자.

터미널에 
sudo wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash

를 입력하면 알아서 패키지가 설치된다.

웹서버(apache2 or nginx), PHP가 자동으로 같이 설치되어 세팅되기때문에 따로 설치할 필요는 없다.

설치가 다 되었으면 웹 브라우저에 ip를 입력하여 WebGUI에 접속한다.
