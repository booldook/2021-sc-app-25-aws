# AWS 설정
## ec2 설정
1. ec2 에서 **새로운 인스턴스** 버튼 클릭하여 ubuntu LTS 20.14 선택
2. 만들어진 ec2에서 freetier ec2micro 선택
3. 서버 실행되면...
4. 보안그룹 설정

![설정1](./img/02.jpg)

![설정1](./img/03.jpg)

![설정1](./img/04.jpg)

![설정1](./img/13.jpg)

5. IP 할당

![설정1](./img/05.jpg)

![설정1](./img/06.jpg)

![설정1](./img/07.jpg)

![설정1](./img/08.jpg)

![설정1](./img/09.jpg)

![설정1](./img/10.jpg)

## 서버 접속
```bash
# bash창을 열고
cd key
ssh -i default.pem ubuntu@xxx.xxx.xxx.xxx
# 서버 접속
```

## 서버 설정 - node, npm
```bash
# apt registry update
sudo apt update

# nvm 설치
sudo apt-get install build-essential libssl-dev
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.39.0/install.sh | bash
nvm install 14
nvm use 14

# node 설치 확인
node -v

# npm 설치
sudo apt install npm

# npm 버전 확인
npm -v

# npm registry update
npm config set registry http://registry.npmjs.org
```

## Express project 만들어 보기
```bash
# express-generator 설치
sudo npm i -g express-generator

# nodemon 설치
sudo npm i -g nodemon

# pm2 설치
sudo npm i -g pm2


# webroot 폴더 만들기
cd ~
mkdir webroot

cd webroot

# express sample 프로젝트 만들기
express --view=ejs sample

# express sample 실행하기
cd sample
node ./bin/www

# 확인
```
![그림](./img/11.jpg)

## nginx 설치
```bash
# nginx 설치
sudo apt install nginx

# nginx 시작
sudo service nginx start

# nginx 멈춤
sudo service nginx stop

# nginx 재시작
sudo service nginx restart
```

## nginx 80접근 -> 3000이동
```bash
# /etc/nginx/sites-avaiable 에 book 설정파일 만들기
sudo vi /etc/nginx/sites-available/book

# vi가 실행되면 아래사항 입력하고 **esc** **:wq**
server {
  listen 80;
  server_name xxx.xxx.xxx.xxx;
  location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    proxy_pass http://127.0.0.1:3000/;
    proxy_redirect off;
  }
}

# sites-available의 설정을 sites-enabled로 심볼릭 링크(바로가기)로 링크하기
sudo ln -s /etc/nginx/sites-available/book /etc/nginx/sites-enabled/book

# nginx 문법 오류 테스트
sudo nginx -t

# 이상 없다면 nginx 재구동
sudo service nginx restart
```


## MySQL(MariaDB) 설치
```bash
sudo apt install mariadb-server
```

## MySQL(MariaDB) 설정
```bash
# mysql native connection 접근(비밀번호 x)
sudo mysql
```
```sql
-- mysql database change
use mysql;

-- Local사용자(서버에서 직접(Shell) 접근)의 접근 방식을 password입력으로 바꿈
UPDATE user SET plugin='mysql_native_password' WHERE user='root';

-- root사용자의 비밀번호 변경
UPDATE user SET authentication_string=PASSWORD('비밀번호') WHERE user='root';

-- 권한 즉시 적용
flush privileges;

-- 종료
quit

-- 재접속
sudo mysql -u root -p
-- 패스워드는 나타나지 않지만 입력되고 있음(당황하지말기)

-- 외부에서 접근할 사용자 만들기
GRANT all privileges ON *.* TO '계정이름'@'%' identified by '패스워드';

flush privileges;
```

## MySQL 데이터베이스 설정
```sql
-- book database 생성
create database book;

-- book에 접근할 book사용자 생성
GRANT all privileges ON book.* TO 'book'@'%' identified by '000000';
```


## MySQL(MariaDB) 외부접속허용
```bash
# mysql 환경설정파일 열기
sudo vi /etc/mysql/mariadb.conf.d/50-server.cnf

# vi - 아래처럼 주석처리 할것 - 주석처리 하면 외부에서도 나의 DB에 접근 가능함.
# bind_address = 127.0.0.1

# MySQL 재시작
sudo service mysql restart
```

## 서버 시간대 변경
```bash
sudo timedatectl set-timezone Asia/Seoul
sudo service mysql restart
```
