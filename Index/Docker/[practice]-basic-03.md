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
