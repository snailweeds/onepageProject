# 프로젝트 서버에 올리기 - 배포
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
정보 입력하고 OK 누르면 서버의 파일 볼 수 있음(Host는 내 EC2서버의 IP, User는 "ubuntu") >
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

Robo3T 이용해 내 컴퓨터에서 서버에 있는 mongoDB에 접속: 

```

* 다시 파일 실행
```
python 파일명.py 이라고 입력해도 실행가능
```
## flask 서버 실행
## AWS에서 포트 열어주기
## 프로젝트 업로드
## 포트포워딩
## nohup 설정하기
## 도메인 구입
## og 태그
