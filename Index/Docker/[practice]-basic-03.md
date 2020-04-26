### dockerfile
Docker 이미지를 빌드할때 사용하는 파일이다. 빌드, 배포, 내용을 자세히 확인할 수 있다. 도커 엔진은 현재 디렉토리에있는 Dockerfile을 선택한다.

```
#vi Dockerfile
FROM ubuntu:14.04
MAINTAINER parkdongsam
LABEL "purpose"="test"
RUN apt-get update
RUN apt-get install apache2 -y
ADD test.html /var/www/html
WORKDIR /var/www/html
#RUN ["/bin/bash", "-c" "echo hello >> test2.html"] // 에러
EXPOSE 80
cmd apachectl -DFOREGROUND
```
> **FROM** : 생성할 이미지에 베이스가 될 이미지를 지정한다.  

> **MAINTAINER** : 이미지를 생성한 개발자의 정보를 나타낸다. (E-Mail) 도커 1.13.0 버전 이후로는 **LABEL maintaniner "name <e-
mail>"** 형식으로 사용할 수 있다.  

> **RUN** : 이미지를 만들기 위해 컨테이너 내부에서 실행하는 명령어이다. Shell과 Interactive하지 않도록 해야한다. (ex -y 옵션)   

> **LABEL** : 이미지에 메타데이터를 추가한다. 키:값 형태로 저장되며, 여러개를 지정할 수 있다. 추 후 docker inspect로 확인할 수 있다.

> **ADD** : Dockerfile이 위치한 디렉토리에 있는 파일을 이미지에 추가한다. (정확히는 Context에서 가져온다.) JSON 배열 형식으로 ["파일명"  ... "위치"] 형식으로 사용할 수 있다. 추가할 파일명을 나열하고 마지막에는 컨테이너에 위치를 지정한다.

> **WORKDIR** : 명령어를 실행할 디렉토리를 나타낸다. 예를 들어 /etc/ 라면 cd /etc/ 이 후 명령어를 실행하는 것과 같다.

> **EXPOSE** : Dockerfile의 빌드로 생성된 이미지에서 노출할 포트를 설정한다. 호스트 포트와 바인되는 것은 아니며, 컨테이너에서 80번 포트를 사용한다는 의미이다.

> **CMD** : 컨테이너가 시작될 때마다 실행할 명령어를 설정한다.

### Build
```
#docker build -t build_test:0.0 ./
```
> **-t** 생성될 이미지의 이름을 지정, [Dockerfile 위치]

```
#docker run -d -P --name build_image build_test:0.0
```
> **-P** 옵션은 이미지에 설정된 EXPOSE의 모든 포트를 호스트에 연결하도록 설정한다. (-p, --publish)  
Examples: -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT.  

```
#docker port build_image
80/tcp -> 0.0.0.0:32771
```
> **--filter** 옵션으로 LABEL로 명시한 정보를 필터링하여 컨테이너나 이미지를 쉽게 찾을 수 있다.

```
#elinks 127.0.0.1:32771/test.html
```
ADD로 추가한 test.html 파일의 내용을 확인할 수 있다.

이미지 빌드 과정  
- Build Context를 읽어들인 후, 이미지를 생성한다.
> Build Context는 이미지를 생성하는데 필요한 각종 파일, 소스 코드, 메타데이터 등이 위치한 디렉토리를 의미하며, Dockerfile이 위치한 디렉토리가 Build Context가 된다. Build Context에 위치한 디렉토리 및 파일들이 모두 포함될 수 있기때문에 불필한 파일이 없도록 해야한다. (.dockerignre 파일로 Context에서 제외 시킬 수 있다.)

```
Sending build context to Docker daemon  3.072kB
Step 1/9 : FROM ubuntu:14.04
 ---> 6e4f1fe62ff1
Step 2/9 : MAINTAINER parkdongsam #명령어 마다 이미지가 생성된다. (Commit)
 ---> Using cache
 ---> 199a0186fd9f
Step 3/9 : LABEL "purpose"="test" # Dockerfile의 명령어 줄 수 만큼, 레이어가 존재하게 된다.
 ---> Using cache
 ---> 7284950ac806
Step 4/9 : RUN apt-get update
 ---> Using cache
 ---> d1928488b3b7
Step 5/9 : RUN apt-get install apache2 -y
 ---> Using cache
 ---> 54f414480894
Step 6/9 : ADD test.html /var/www/html
 ---> Using cache
 ---> 125f332f6925
Step 7/9 : WORKDIR /var/www/html
 ---> Using cache
 ---> 8c04a5884f6a
Step 8/9 : EXPOSE 80
 ---> Running in e1204b41ff4a
Removing intermediate container e1204b41ff4a #중간에 생성된 임시 컨테이너를 삭제하고 새롭게 만든다.
 ---> a5d69816976f
Step 9/9 : cmd apachectl -DFOREGROUND
 ---> Running in b9828499fc9f
Removing intermediate container b9828499fc9f
 ---> b0aabf04d89d

```

> **-f** 옵션으로 Dockerfile을 지정할 수 있다.

이미지를 빌드한 내용은 캐시에 저장되어, 다음 번 빌드 시 일부 중복된 명령어를 수행하지 않고 건너뛴다. 해당 기능은 유용할 수 있으나, 변경사항을 제대로 인식하지 못할 수 도 있기때문에 build 명령어에 --no-cahce 옵션을 추가하면, Dockerfile을 처음부터 다시 빌드하게된다.

애플리케이션을 빌드할 떄는 많은 의존성 패키지와 라이브러리를 필요로 한다. (Ex Go)
```bash
# pwd
/root/dockerfile01

#ls
Dockerfile  main.go

#cat Dockerfile
FROM golang
ADD main.go /root
WORKDIR /root
RUN go build -o mainAPP main.go
CMD ["./mainAPP"]

#cat main.go
package main

import "fmt"

func main() {
	fmt.Println("Hello, world.")
	}
```
```Bash
#docker build -t go_image ./
Sending build context to Docker daemon  3.072kB
Step 1/5 : FROM golang
 ---> 2421885b04da
Step 2/5 : ADD main.go /root
 ---> c541deefa5fb
Step 3/5 : WORKDIR /root
 ---> Running in 2492638d53b9
Removing intermediate container 2492638d53b9
 ---> 88bf5a086fb8
Step 4/5 : RUN go build -o mainAPP main.go
 ---> Running in e18406933c71
Removing intermediate container e18406933c71
 ---> de0ee3082e95
Step 5/5 : CMD ["./mainAPP"]
 ---> Running in 6a3c2e742570
Removing intermediate container 6a3c2e742570
 ---> 496925e9b160
Successfully built 496925e9b160
Successfully tagged go_image:latest
```
```bash
#docker images
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
go_image                                 latest              496925e9b160        4 minutes ago       811MB
```
애플리케이션의 크기는 매우작지만, 관련 패키지 및 라이브러리가 이미지의 크기를 차지하고 있다. (811MB)

멀티스테이지는 Dockerfile 안에 여러개의 FROM 이미지를 정의하여 빌드 후 생성되는 이미지의 용량을 줄일 수 있다.

```Bash
#cat Dockerfile
FROM golang
ADD main.go /root
WORKDIR /root
RUN go build -o mainAPP main.go

FROM alpine:latest
WORKDIR /root
COPY --from=0 /root/mainAPP ./
CMD ["./mainAPP"]
```
> **COPY** 명령어는 첫반째 FROM에서 사용된 이미지의 최종상태에 존재하는 mainAPP 파일을 두번째 이미지인 alpine:latest로 복사한다. **--from=0** 은 첫 번째 FROM에서 빌드된 이미지의 최종 상태를 의미한다. (alpine은 유명한 Linux 배포판보다 크기가 매우작지만 기본적인 프로그램을 실행에 필요한 필수 요소들이 포함되어있는 Linux 배포판이다.)

```bash
#docker build ./ -t go_image:multi-stage
Sending build context to Docker daemon  3.072kB
Step 1/8 : FROM golang
 ---> 2421885b04da
Step 2/8 : ADD main.go /root
 ---> Using cache
 ---> c541deefa5fb
Step 3/8 : WORKDIR /root
 ---> Using cache
 ---> 88bf5a086fb8
Step 4/8 : RUN go build -o mainAPP main.go
 ---> Using cache
 ---> de0ee3082e95
Step 5/8 : FROM alpine:latest
latest: Pulling from library/alpine
cbdbe7a5bc2a: Pull complete
Digest: sha256:9a839e63dad54c3a6d1834e29692c8492d93f90c59c978c1ed79109ea4fb9a54
Status: Downloaded newer image for alpine:latest
 ---> f70734b6a266
Step 6/8 : WORKDIR /root
 ---> Running in f47a7d0c9380
Removing intermediate container f47a7d0c9380
 ---> ad95e47a9b72
Step 7/8 : COPY --from=0 /root/mainAPP ./
 ---> 72630d3471a1
Step 8/8 : CMD ["./mainAPP"]
 ---> Running in 59f348760710
Removing intermediate container 59f348760710
 ---> 0c1cb47fa8b5
Successfully built 0c1cb47fa8b5
Successfully tagged go_image:multi-stage
```
```
#docker images
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
go_image                                 multi-stage         0c1cb47fa8b5        41 seconds ago      7.68MB
go_image                                 latest              496925e9b160        13 minutes ago      811MB

#docker run -i -t go_image:multi-stage
Hello, world.
```
이미지의 크기가 매우 작아진것을 확인할 수 있다.
