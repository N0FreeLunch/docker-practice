## 데이터베이스 컨테이너
- 공식(Official) 도커 허브 이미지를 사용해서 데이터베이스 이미지를 받아서 사용한다.
- 여기서의 예제는 [마리아디비](https://hub.docker.com/_/mariadb/)를 사용한다.

## docker compose에 데이터베이스 컨테이너 실행 코드 작성하기
```
version:"3" 
services:
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

### 태그
- `image: mariadb:10.1.21`부분은 `image: <name>:<tag>`형식이다.
- `<tag>`부분에 태그를 지정하지 않으면 디폴트로 최신버전을 태그로 지정한다. `image: <name>:lastest`를 지정한 것과 같다. lastest 버전을 사용한 경우 버전 변경에 따른 예기치 못한 문제가 발생할 수 있기 때문에 특정한 버전을 지정하는 편이 좋다.

### 포트
```
ports:
  - "13306:3306"
```
- 같은 그룹의 도커 컨테이너들(docker-compose에 함께 정의된 컨테이너들) 간에도 서로 통신을 하기 위해서는 도커를 실행하는 호스트 컴퓨터의 포트를 통해서 통신을 주고 받아야 한다.
- 사용하려는 응용프로그램의 종류에 따라 기본적인 포트들이 정해져 있고 도커 컨테이너 내부의 포트를 호스트 컴퓨터에 노출하게 되면 호스트 컴퓨터 상의 열려있는 특정 포트와 충돌할 수 있다. 따라서 도커 컨테이너 내부에는 애플리케이션의 표준 포트를 사용하고(mysql, mariadb등의 경우 3306포트를 연다.) 호스트 컴퓨터에 노출시키는 컨테이너의 포트는 비표준 포트를 여기서 `13306`포트를 쓴 것과 같이 정의할 수 있다.
- 컨테이너의 13306 포트를 컨테이너 내부 곧 OS의 3306포트와 매핑한다.

### 환경변수
```
environment:
  - MYSQL_DATABASE=dockerphp
  - MYSQL_ROOT_PASSWORD=password
```
- environment는 도커 OS 컨테이너의 환경변수를 설정하는 부분이다.
- 이 부분에서 정의한 환경변수는 dockerfile 등 도커를 실행을 정의하는 코드들에 전달된다.
- 실제 도커 내부 리눅스 OS의 환경변수로 정의 되는지는 확인할 필요가 있음.

### 링크
```
links:
  - mariadb:mariadb
```
- 링크의 형식은 `<service>:<alias>`으로 `서비스이름:별칭`으로 구성된다. 별칭을 지정하면 다른 컨테이너에서 이 컨테이너의 호스트명을 가리키도록 지정할 때 별칭을 사용할 수 있다.
- 곧, 컨테이너에 담긴 OS의 호스트네임이 된다.
- 참고 : 컨테이너 간 통신에 링크를 정의하지 않아도 통신이 가능하다. 도커를 실행하고 있는 호스트 컴퓨터에서 접근할 때는 링크로 접근할 수 없다.
- `- mariadb:mariadb`라는 표현은 `- mariadb`으로 축약 가능하다.

## 데이터베이스 컨테이너에 접근하기
- 데이터베이스 컨테이너에 접근하는 방법은 호스트 컴퓨터의 데이터베이스 툴을 이용하여 접근할 수도 있으며, 호스트 컴퓨터의 포트를 통해 접근할 수 있는 라라벨 등과 같은 웹 애플리케이션을 통해서 접속할 경우도 있다.

```
# Let's delete and restart the containers
$ docker-compose stop
$ docker-compose rm -v # Remove anonymous volumes attached
$ docker-compose up --build
```
- docker compose 파일에 새로 정의한 데이터베이스 컨테이너를 실행하기 위해서 도커를 정지 후 빌드 된 이미지를 삭제한 후 새로 빌드하여 컨테이너를 실행한다.

```
# Once the database container finishes starting,
# open another tab and connect via mysql-client or a GUI
$ mysql -u root -h 127.0.0.1 -P13306 -ppassword
$ mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| dockerphp          |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)
```
- 호스트 컴퓨터에서 데이터베이스 컨테이너가 호스트 컴퓨터의 13306 포트를 정유하고 있으며, 호스트 컴퓨터에서 해당 포트로 mysql 명령어로 데이터베이스 콘솔에 접근하였다.

## 라라벨에서 데이터베이스 컨테이너 접속
```
APP_ENV=local
APP_DEBUG=true
APP_KEY=
APP_TIMEZONE=UTC

DB_CONNECTION=mysql
DB_HOST=mariadb
DB_PORT=3306
DB_DATABASE=dockerphp
DB_USERNAME=root
DB_PASSWORD=password
```

### 호스트이름
- `DB_HOST=mariadb`
- 라라벨 애플리케이션은 컨테이너에 정의한 상태이다. 라라벨을 정의한 컨테이너에서 데이터베이스를 정의한 컨테이너로 연결하는 것이다.
- 같은 그룹의 도커 컨테이너 집합에 속해있는 컨테이너 간에 서로 연결을 할 때 호스트네임은 링크에 정의한 이름을 우선한다. 별칭을 정의했다면 별칭으로 연결할 수 있다. 링크가 정의되지 않았다면 서비스 키 이름을 사용하여 연결할 수 있다. 

```
# Example using localhost
$ mysql -u root -h 127.0.0.1 -ppassword
```
- 컨테이너 내부에서 로컬 호스트의 아이피를 지정하여 접속한다.

```
# Using the Docker network alias
$ mysql -u root -h mariadb -ppassword
```
- 컨테이너 내부에서 링크 키를 이용하여 접속한다.

```
$ mysql -u root -h db -ppassword
```
- 컨테이너 내부에서 링크 별칭을 이요하여 접속한다.
