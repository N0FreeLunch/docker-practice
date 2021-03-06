## 도커로 hello world 출력해 보기

도커가 설치되었다면 다음 명령어를 실행 해 보자.
```
docker run hello-world
```

위 명령을 실행하면 다음 단계가 진행된다.
1. 도커 클라이언트가 도커 데몬에 접속한다.
2. 도커 데몬이 도커 허브에서 “hello-world”란 이미지를 다운로드 한다.
3. 도커 데몬은 도커 이미지를 빌드하여 컨테이너를 생성한다.
4. 빌드된 컨테이너가 실행되고 컨테이너에서 보내는 출력을 도커 클라이언트로 스트리밍하여 사용자의 CLI 창에 메시지를 띄운다.

“Hello from Docker!”라는 메시지가 CLI 창에 나온다.


## 생성된 컨테이너 삭제하기
도커 이미지를 삭제하기 위해서는 도커 이미지의 해쉬 아이디를 확인해야 한다.
```
docker images | grep hello-world
```
도커 이미지를 삭제하기 위해서는 `rmi` (remove image)명령을 사용한다.
```
docker rmi 해쉬-아이디
```
하지만 도커 이미지가 컨테이너로 실행된 상태라면 컨테이너를 제거하기 전까지는 도커 이미지를 제거 할 수 없다.


## 도커 컨테이너 제거하기
도커 이미지를 삭제하기 위해서는 컨테이너를 먼저 제거해야 한다.
도커 컨테이너를 제거하기 위해서 실행된 컨테이너의 프로세스 아이디를 획득해야 한다.

프로세스 아이디를 포함한 실행된 도커 컨테이너 리스트
```
docker ps
```
실행된 도커 컨테이너 뿐만 아니라 실행되지 않은 도커 컨테이너의 리스트까지 확인하고자 할 때
```
docker ps -a
```

### 도커 컨테이터 제거하기
```
docker rm 도커_컨테이너_아이디
```

### 도커 이미지 제거하기
```
docker rmi 도커_이미지_해쉬_아이디
```


### 여러 도커 이미지 한 번에 제거하기
```
docker rmi 도커_이미지_해쉬_아이디_1 도커_이미지_해쉬_아이디_2 도커_이미지_해쉬_아이디3
```
- 공백 문자로 구분하여 이미지의 해쉬 아이디를 나열한다.

### 이미지 이름과 태그를 통해서 도커 이미지 제거하기
```
docker rmi name:tag
```
