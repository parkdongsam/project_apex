### version check
```
#docker -v  
Docker version 19.03.8, build afacb8b
```

### Command
```
#docker images   // 도커 엔진에 존재하는 이미지의 목록 출력
```
> 무작위 16진수 Hash 값은 컨테이너의 고유 ID, 너무 길어 앞 12자리정도만 사용.

```
#docker start [image]    // 컨테이너 시작
#docker attach [image]   // 컨테이너 내부로 들어가기
```

```
#docker create -i -t name test centos:7
#docker run -i -t name test centos:7
```
-i interactive  
-t tty

> run과 create의 차이  
run     : 이미지가 없는 경우 PULL -> create -> start -> attach (-i -t 사용 시)  
create  : 이미지가 없는 경우 PULL -> create  

### 컨테이너 목록 확인
```
#docker ps
#docker ps -a // 정지된 컨테이너 포함 확인
```
```
#docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                  PORTS                NAMES
a0f914725191        ubuntu:14.04        "/bin/bash"         3 days ago          Up 3 days               0.0.0.0:80->80/tcp   webserver
77cf269593b5        ubuntu:14.04        "/bin/bash"         3 days ago          Exited (0) 3 days ago   
```
```
#docker rename [old] [new]
```
### Remove
```
#docker rm [container]    // 중지 후 사용 가능
#docker rm -f [container] // 중지 후 제거
#docker container prune   // 모든 컨테이너 제거
```
```
#docker ps -a -q    // -q ID만 출력  
#docker stop $(docker ps -a -q)   
#docker rm $(docker ps -a -q)     // 변수 활용 모든 컨테이너 제거
```
