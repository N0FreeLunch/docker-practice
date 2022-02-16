composer는 설치되었다고 가정한다. (composer의 설치 과정은 생략)

### 로컬에 laravel lumen을 설치
```
composer create-project --prefer-dist laravel/lumen docker-lamp
```
로컬에서 설치한 루멘 프로젝트 내부로 cd 명령을 통해 들어간다.
```
cd lumen docker-lamp
```

빈 Dockerfile과 docker-compose.yml 파일을 생성한다.
```
touch Dockerfile docker-compose.yml
```

.docker 파일 내에 php 폴더와 apache 폴더를 만든다.
-p 옵션은 필요한 경로의 폴더가 없다면 생성하는 명령이다.
```
mkdir –p .docker/{php,apache}
```

.docker 폴더 내에 생성한 php 폴더 내에 php.ini 파일을 만들고
```
touch .docker/php/php.ini
```

.docker 폴더 내에 생성한 apache 폴더 내에 vhost.conf 파일을 만든다.
```
touch .docker/apache/vhost.conf
```

참고 : .docker는 .으로 시작하는 폴더이기 때문에 숨김 처리 된다.


## 생성한 폴더 구조를 도커 컨테이너에 세팅하기
```
FROM php:7.1.9-apache

LABEL maintainer="N0FreeLunch“
COPY config/php.ini /usr/local/etc/php/
COPY index.php /var/www/html
```
파일에서

```
COPY config/php.ini /usr/local/etc/php/
```
를 
```
COPY .docker/php/php.ini /usr/local/etc/php/
```
으로 바꾼다.


현재 폴더(.)를 컨테이너 내부의 /srv/app 폴더에 넣는다. (/var/www/html 대신 사용하는 것으로 함)
```
COPY . /srv/app
```

아파치 설정파일은 다음과 같이 세팅한다.
```
COPY .docker/apache/vhost.conf /etc/apache2/sites-available/사이트명-default.conf
```

예를 들어 다음으로 하자.
```
COPY .docker/apache/vhost.conf /etc/apache2/sites-available/000-default.conf
```

수정한 전체 코드는
```
FROM php:7.1.9-apache

LABEL maintainer="N0FreeLunch"
COPY .docker/php/php.ini /usr/local/etc/php/
COPY . /srv/app
COPY .docker/apache/vhost.conf /etc/apache2/sites-available/000-default.conf
```
이다.


## docker/apache/vhost.conf 를 설정하기
```
<VirtualHost *:80>
# ServerName www.example.com

ServerAdmin webmaster@localhost
DocumentRoot /srv/app/public

<Directory "/srv/app/public">
AllowOverride all
Require all granted
</Directory>

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### DocumentRoot
소스코드는 /srv/app 파일로 복사 되었다. 라라벨(루멘)의 경우 보안을 위해 public 폴더만 apache에 연결한다. 따라서 DocumentRoot를 /srv/app/public으로 설정해 주었다.

### ServerName
로컬 호스트를 사용할 거라서 ServerName 부분을 \#으로 주석


## docker-compose.yml 파일 정의
```
version: "3"
services:
app:
build: .
ports:
- "8080:80"
volumes:
- .:/srv/app
```
ports는 로컬의 8080포트에 도커의 80 포트를 할당한다는 설정
volumes는
도커 이미지로 복사할 대상이 들어 있는 폴더


## 도커 빌드하고 실행하기
```
docker-compose up —build
```

## 접속하기
도커 컨테이너가 빌드되고 실행 되었다면
로컬의 http://localhost:8080으로 접속
