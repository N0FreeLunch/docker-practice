## OPcache 설정 해 보기
- php는 인터프리트 언어이다. php 파서가 php 코드를 읽고 이를 실행한다.
- php 파서가 php 코드를 실행할 때는 기계어로 실행이 된다. 이 실행 바이트 코드를 캐싱하여 메모리에 저장하여 파서의 읽는 시간 없이 실행하도록 하는 기술이 OPcache 기술이다.

## OPcache 단점
- 서버는 트레픽이 많은 것에 반해 개발 환경은 트레픽이 거의 없다.
- 서버의 코드는 변경이 거의 없지만 개발 환경은 코드 변경이 계속 이뤄지므로 캐싱비용이 많이 들어간다.
- 따라서 OPcache는 개발환경에서 세팅하지 않고 서버에만 동작 되도록 세팅한다.

## Dockerfile에 옵캐시 설치 설정하기
```
FROM php:7.1.9-apache
LABEL maintainer="N0FreeLunch"
COPY .docker/php/php.ini /usr/local/etc/php/
COPY ./srv/app
COPY .docker/apache/vhost.conf /etc/apache2/sites-available/000-default.conf
RUN docker-php-ext-install pdo_mysql\
    && docker-php-ext-install opcache
```
- `docker-php-ext-install opcache` 옵캐시 php 모듈을 설치 한다.

```
date.timezone=UTC
[opcache]
opcache.enable=1
opcache.revalidate_freq=0
opcache.fast_shutdown=1

;0or1.0isrecommendedinproduction
;andwillrequirearestartwhenfileschange.
opcache.validate_timestamps=1

;Keepthisabovethenumberoffilesinproject
;Youcancheckhowmanyfilesyouhavewith
;`find.-typef-print|grepphp|wc-l`
opcache.max_accelerated_files=6000

;Cachesduplicatestringsintoonesharedimmutablevalue
opcache.interned_strings_buffer=16
```
