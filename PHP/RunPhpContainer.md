
## 컨테이너의 커멘드 라인으로 접속하기
- 컨테이너의 커멘드 라인으로 접속하기 위해서는 도커 컨테이너의 아이디를 알아야 한다.
- 도커 컨테이너의 아이디를 얻기 위해서는 컨테이너가 실행 상태이어야 한다.

### 도커 컨테이너 실행하기 (실행 후 커멘드라인 상태 백그라운드로 전환)
```
docker-compose up –d
```

### 도커 컨테이너 아이디 얻기
```
docker ps
```

### 도커 컨테이너의 베쉬 쉘로 접속하기
```
docker exec –it 컨테이너_아이디 bash
```


## php.ini 파일 만들어서 도커 컨테이너에 넣기
php.ini 파일을 만들 config 폴더를 만든다.
```
mkdir config/
```

폴더 내부에 php.ini 파일을 만든다.
```
echo "date.timezone = Asia/Tokyo" >> config/php.ini
```
또는
```
echo "date.timezone = Asia/Seoul" >> config/php.ini
```

## php.ini 파일 컨테이너에 배치
COPY 명령어를 통해서 php.ini 파일을 도커 컨테이너 내부의 php가 설치된 곳인 /usr/local/etc/php/ 경로에 배치
```
FROM php:7.1.9-apache

LABEL maintainer="N0FreeLunch"
COPY index.php /var/www/html
```
에서 
```
FROM php:7.1.9-apache

LABEL maintainer="N0FreeLunch“
COPY config/php.ini /usr/local/etc/php/
COPY index.php /var/www/html
```
이렇게 추가
