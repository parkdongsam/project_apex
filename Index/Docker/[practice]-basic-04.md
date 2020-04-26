### Environment Variable
- https://docs.docker.com/engine/reference/builder/  

#### ENV
- Dockerfile에서 사용될 환경변수를 지정한다.  
- 설정된 환경변수는 $(NAME), $NAME 형식으로 사용한다.
- Dockerfile뿐 아니라 이미지에도 저장되어, 빌드된 이미지로 컨테이너를 생성하면, 해당 환경변수를 사용할 수 있다.

```
#cat Dockerfile
FROM centos:7
ENV test /home
WORKDIR $test
RUN touch $test/testfile
```
```
#docker build -t envtest:0.0 ./
#docker run -i -t --name test envtest:0.0
[root@de7f98174d9d home]# ls
testfile
[root@de7f98174d9d home]# echo $test
/home
```
> 컨테이너 시작 시 **-e** 옵션을 사용해 같은 이름의 환경변수를 사용하면 기존의 값은 덮어 쓰여진다.

#### ARG
- build 명령어를 실행할 때 추가로 인자를 입력 받아 Dockerfile 내에서 사용될 변수의 값을 설정한다.

```
#cat Dockerfile
FROM centos:7
ARG arg1
RUN touch ${arg1}/testfile
```

```
#docker build --build-arg arg1=/root -t argtest:0.0 ./
#docker run -i -t --name arg_test1 argtest:0.0
[root@fef7914dd8c7 /]# cd /root
[root@fef7914dd8c7 ~]# ls
anaconda-ks.cfg  testfile
```
