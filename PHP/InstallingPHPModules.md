## php의 pdo 확장을 설치한다.
```
RUN docker-php-ext-install pdo_mysql
```

```
FROM php:7.1.9-apache

LABEL maintainer="N0FreeLunch"
COPY .docker/php/php.ini /usr/local/etc/php/
COPY . /srv/app
COPY .docker/apache/vhost.conf /etc/apache2/sites-available/000-default.conf
RUN docker-php-ext-install pdo_mysql
```


## RUN 명령어의 특징
- 레이어를 생성한다.
- 레이어는 이전 레이어가 변경 되지 않는 한 저장되어 다음 빌드 시 새로 명령을 입력하지 않고 저장된 레이어를 사용하여 빌드 시간을 단축한다.
- 명령어를 태스트 해야 하는 경우에는 명령어를 잘게 쪼개어 레이어를 나눠서 각 명령어를 태스트한다.
- 태스트가 끝나 프로덕션에 사용하는 명령어는 하나로 합쳐서 레이어를 적게 만드는 편이 좋다.

명령어 별로 레이어를 나누기
```
RUN cp /srv/.env.example /srv/.env
RUN touch /tmp/foo
```

여러 명령어를 하나의 레이어에 두기
```
RUN cp /srv/.env.example /srv/.env  
&& touch /tmp/foo
```


## 수정된 레이어 적용하기
레이어를 적용하려면 컨테이너를 다시 빌드 해야 한다.
```
$ docker-compose stop
$ docker-compose build
```
