# <5주차 회고록>
* IP주소: 컴퓨터마다 가지는 고유 주소, 네트워크가 가능한 모든 기기가 통신할 수 있도록 가지는 특수한 번호
* 포트: 하나의 IP에는 여러 포트 존재, 하나의 포트에 하나의 프로그램 실행 가능
* URL: IP주소를 알파벳으로 바꾼 것
* DNS: IP주소를 URL로 변환해주는 시스템
## AWS 서버 구매하기
* EC2 서버 구매: (EC2 콘솔페이지) https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-2
```
Launch Instance > 
(Choose an Amazon Machine Image)Ubuntu Server 18.04 LTS(HVM), SSD Volume Type Select > 
(Choose an Instance Type)select t2.mlcro and 'Review and Launce' > 
(Review Instance Launch)Launch
```
* EC2 서버 종료(자동결제 방지)
```
대상 인스턴스에 마우스 우클릭 >
'인스턴스 상태' 클릭 >
중지 또는 종료
```
* EC2에 접속
```
SSH: 다른 컴퓨터에 접속할 때 쓰는 프로그램, 보안이 상대적으로 좋음, 접속할 컴퓨터가 22번 포트가 열려있어야 접속 가능

[Mac OS] Mac은 ssh가 있어서 명령어로 바로 접근 가능
터미널을 열기 (spotlight에 terminal 입력) >
방금 받은 내 Keypair의 접근 권한을 바꿔주기 (sudo chmod 400 받은키페어를끌어다놓기) >
SSH로 접속하기 (ssh -i 받은키페어를끌어다놓기 ubuntu@AWS에적힌내아이피)

[Window] ssh가 없으므로 git bash라는 프로그램을 이용
gitbash를 실행해 입력 (ssh -i 받은키페어를끌어다놓기 ubuntu@AWS에적힌내아이피) >
Key fingerprint 관련 메시지가 나올 경우 Yes를 입력 >
git bash를 종료할 때는 exit 명령어를 입력하여 ssh 접속을 먼저 끊어주기
```
## 서버 세팅
* filezilla
```
파일질라 실행 >
Site Manager에서 New Site 클릭 >
정보 입력하고 OK 누르면 서버의 파일 볼 수 있음(Host: 내 EC2서버의 IP, User: "ubuntu", Protocol: SFTF, Port: 22) >
마우스로 드래그해서 파일 업로드/다운로드
```
* 파일 실행
```
EC2 콘솔창에서 cd ~로 home 디렉토리로 이동 >
python3 파일명.py 입력
```
* 서버 환경 세팅
```
한국시간 세팅: sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime

python3 -> python: sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 10

pip3 -> pip: sudo apt-get update > 
sudo apt-get install -y python3-pip > 
sudo update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1

mongoDB 설치: wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add - >
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 > multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list >
sudo apt-get update >
sudo apt-get install -y mongodb-org

mongoDB 실행: sudo service mongod start

mongoDB 접속 계정 생성: mongo > use admin; > db.createUser({user: "test", pwd: "test", roles:["root"]}); >
exit >
sudo survice mongod restart(mongodb 재시작)

mongoDB 외부에 열어주기: sudo vi /etc/mongod.conf > 아래방향 화살표 키 >
(Vim: a -> 입력, :wq -> 저장)
i(입력모드) > bindIP:0.0.0.0, serucity: authorization: enabled 로 바꾸기 >
esc 누르고 :wq > sudo service mongod restart

Robo3T 이용해 내 컴퓨터에서 서버에 있는 mongoDB에 접속: 좌측 상단 컴퓨터 아이콘(Mongo Connection) 클릭 >
Create > 접속정보 세팅(Type: Direct Connection, Address: 54.180.109.51:27017) >
상단의 Authentication 탭을 클릭해 Perform authentication 체크박스를 클릭하고 생성한 계정의 아이디와 비밀번호 입력 후 'save'

80포트로 들어오는 요청을 5000포트로 넘겨주기: sudo iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 5000
```
* 다시 파일 실행
```
python 파일명.py 이라고 입력해도 실행가능
```
## flask 서버 실행
* filezilla 통해 EC2에 파일 업로드 후 실행 --> ERROR -> pip install flask (flask 패키지 설치 필요)
* flask 실행 --> 작동 불능 -> AWS 설정 추가 필요
```
python app.py
(크롬 브라우저에) http://[내 EC2 IP]:5000/
```
## AWS에서 포트 열어주기
* AWS에서 5000포트 열기
```
EC2 관리 콘솔 > 보안그룹(영문: Security Group) 클릭 > Edit inbound rules를 선택 >
세 가지 포트를 추가(80포트: HTTP 접속을 위한 기본포트, 5000포트: flask 기본포트, 27017포트: 외부에서 mongoDB 접속을 하기위한 포트)
```
* 재접속 --> 정상 작동!
```
http://내아이피:5000
```
## 프로젝트 업로드
* Robo3T를 이용해서, "내 컴퓨터에서"→"서버에 있는 mongoDB"에 접속하기
```
좌측 상단 컴퓨터 아이콘(Mongo Connection) 클릭 >
Create > 접속정보 세팅(Type: Direct Connection, Address: 54.180.109.51:27017) >
상단의 Authentication 탭을 클릭해 Perform authentication 체크박스를 클릭하고 생성한 계정의 아이디와 비밀번호 입력 후 'save'
```
* 원페이지쇼핑몰 완성본을 filezilla로 EC2에 업로드
```
app.py에서 client = MongoClient('mongodb://test:test@localhost', 27017)로 수정 >
(client = MongoClient('mongodb://아이디:비밀번호@localhost', 27017))
파일질라에서 homework 폴더 째로 드래그 드롭으로 EC2 인스턴스의 home/ubuntu 폴더에 업로드
```
* 완성본을 실행
```
python app.py
```
* 접속
```
http://내AWS아이피:5000/
```
## 포트포워딩(Port Forwarding)
* 포트 번호를 떼고 접속
```
http://내AWS아이피/
```
* 포트 번호 없애기 개념
```
http 요청에서는 80포트가 기본이기 때문에, 굳이 :80을 붙이지 않아도 자동으로 연결
포트 번호를 입력하지 않아도 자동으로 접속되기 위해, 우리는 80포트로 오는 요청을 5000 포트로  전달하게 하는 포트포워딩 사용
리눅스에서 기본으로 제공해주는  포트포워딩을 사용
```
## nohup 설정하기
* SSH 접속을 끊어도 서버가 계속 돌게 하기
```
원격 접속을 종료하더라도 서버가 계속 돌아가게 하기: nohup python app.py &
서버 강제 종료: ps -ef | grep 'app.py'(pid 값(프로세스 번호) 확인) -> kill -9 [pid값]
서버 다시 켜기: nohup python app.py &
```
## 도메인 구입
* 도메인 구입/연결
```
네임서버를 운영해주는 업체에, IP와 도메인 매칭 유지비를 내는 것
한국의 '가비아'라는 회사에서 구입해보기
```
* 구입 후 아래 화면을 띄워주기
```
https://my.gabia.com/service#/ >
DNS 관리툴 클릭 > 도메인 연결 클릭 > DNS 설정 클릭 >
호스트 이름에 @, IP주소에 IP주소를 입력
```
* 10분 정도 기다려주기
```
네임서버에 내 도메인-IP가 매칭되는 시간이 필요
```
* IP주소로 접근
```
http://내AWS아이피/
http://내도메인/
```
## og 태그
* 카톡/페이스북/슬랙에 공유했을 때 예쁘게 나오도록 꾸며주기
```
static 폴더 아래에 이미지 파일을 넣고, 각자 프로젝트 HTML의 <head>~</head> 사이에 아래 내용을 작성하면 og 태그를 개인 프로젝트에 사용 가능

1. "내 사이트의 제목" 입력하기
2. "보고 있는 페이지의 내용 요약" 입력하기
3. 적당한 이미지를 만들거나/골라서 static폴더에 ogimage.png로 저장하기!
(사이즈 800x400인 이미지를 구글에서 검색!)

<meta property="og:title" content="내 사이트의 제목" />
<meta property="og:description" content="보고 있는 페이지의 내용 요약" />
<meta property="og:image" content="{{ url_for('static', filename='ogimage.png') }}" />
```
* 이미지를 바꿨는데 이전 ogimage가 그대로 나오는 경우
```
페이스북/카카오톡 등에서 처음 것을 한동안 저장해놓기 때문

페이스북 og 태그 초기화 하기: https://developers.facebook.com/tools/debug/
카카오톡 og 태그 초기화 하기: [https://developers.kakao.com/tool/clear/og](https://developers.kakao.com/tool/clear/og)
```
# 파이널 프로젝트 -OnePage_ShoppingMall
