## 데이터베이스 서비스 정의하기
- `docker-compose.yml` 파일에 접근

```
version:"3" services:
  app:
    build: .
    depends_on:
      - mariadb
    ports:
      - "8080:80"
    volumes:
      - .:/srv/app
    links:
      - mariadb:mariadb
  mariadb:
    image: mariadb:10.1.21
    ports:
      - "13306:3306"
    environment:
      - MYSQL_DATABASE=dockerphp
      - MYSQL_ROOT_PASSWORD=password
```

```
image: mariadb:10.1.21
```
- 마리아디비의 버전을 지정한다. 태그 부분을 적지 않는다면 이미지의 최신 버전을 다운로드 한다.

```
    ports:
      - "13306:3306"
```
- 13306을 컨테이너 내부의 3306에 로컬로 매핑
