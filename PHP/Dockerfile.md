## Dockerfile 만들기
프로젝트 폴더 내에 도커 파일 만들기
특별히 경로를 지정하지 않아서 현재 CLI 경로에 만들어 지게 된다.
```
touch Dockerfile docker-compose.yml index.php
```

## 도커 파일에 명령어 정의하기
```
FROM php:7.1.9-apache
LABEL maintainer="N0FreeLunch"
COPY index.php /var/www/html
```

### FROM
FROM 명령어를 통해서 미리 만들어진 도커 프로젝트의 확장을 설치한다.
```
FROM php:7.1.9-apache
```

### LABEL
LABEL 명령어를 통해 이미지에 메타 데이터를 추가한다. maintainer라는 키를 만들고 벨류에 이름을 지정 해 준다.
```
LABEL maintainer="N0FreeLunch"
```
- 참고. `docker inspect name|id` 명령어를 통해 지정한 메타 데이터를 확인할 수 있다.

### COPY
현재 폴더의 index.php 파일을 /var/www/html 경로에 복사한다.
```
COPY index.php /var/www/html
```

### FROM으로 상속 받은 php의 정보 확인하기
FROM 명령어를 통해서 이미지를 상속 받았다.
php:7.1.9-apache 이미지를 상속 받았기 때문에 해당 php에 관한 정보를 확인하고 싶다면
로컬의 index.php 파일에 
```
<?php phpinfo(); ?>
```
코드를 써 준 후에 접속을 위한 세팅을 한다.

