### Image Create
---
```
#docker commit -a "PARK DongSam" -m "Test Commit" d3_centos7 d3_centos7:7.7.1908

#docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
d3_centos7          7.7.1908            a1d965ff21a9        7 seconds ago       203M
```
> **-a** Author 이미지 작성자를 메타데이터에 포함시킨다.

### Image Upload
---
```
#docker tag d3_centos7:7.7.1908 parkdongsam/project_apex:7.7.1908
```

저장소에 이미지를 업로드하기위해 저장소 이름을 추가한다.

```
#docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
parkdongsam/project_apex   7.7.1908            a1d965ff21a9        7 minutes ago       203MB
d3_centos7                7.7.1908            a1d965ff21a9        7 minutes ago       203MB
```
동일 이미지에 새로운 이름이 추가된다.

```
#docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: parkdongsam
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

```
> Logout은 **#docker logout** 이다.

```
#docker push project_apex/d3_centos7:7.7.1908
The push refers to repository [docker.io/parkdongsam/project_apex]
5606486ccfe0: Pushed
77b174a6a187: Pushed
7.7.1908: digest: sha256:7631e1156d88ed0fe538af11fcff464c25996047c495997ddf3ed41f4fcccb1e size: 73
```

### Private Registry
```
#docker run -d -p 5000:5000 --name registry registry:2
```
> Registry도 컨테이너로 동작하며, Docker에서 공식 Registry 이미지를 받을 수 있다. 기본 포트는 5000번이다. 2버전만 사용 가능하다.

```
#docker tag project_apex/d3_centos7:7.7.1908 localhost:5000/project_apex/d3_centos7:7.7.1908
```
> docker tag [image]:[tag] [Host IP]:5000/[image]:[tag]

Registry 컨테이너에 이미지를 올리려면 이미지를 Registry 컨테이너가 존재하는 Host IP Registry 컨테이너의 5000번 포트와 연결된 호스트의 포트로 설정해야한다.

```
#docker push localhost:5000/project_apex/d3_centos7:7.7.1908
```
Registry 컨테이너에 이미지를 업로드한다.
> Registry 컨테이너는 생성됨과 동시에 컨테이너 내부 디렉토리에 마운트되는 도커 볼륨을 생성한다. push 된 이미지는 해당 볼륨에 저장되며, 컨테이너가 삭제되도 볼륨은 삭제되지 않기 때문에 컨테이너 삭제시 볼륨도 함께 삭제한다. **#docker rm --volume [registry]**

```
#docker pull localhost:5000/project_apex/d3_centos7:7.7.1908
```
이미지를 가져올때도 Registry 컨테이너의 URL형식으로 입력한다.
> 인증서 관련 설정이 필요한 경우도 있다.
> https://docs.docker.com/registry/deploying/

Docker Registry 컨테이너를 제어하기 위해서는 RESTful API를 사용해야한다.

```
#curl localhost:5000/v2/_catalog
{"repositories":["project_apex/d3_centos7"]}
```
> **_catalog** Registry 컨테이너에 저장된 이미지 목록을 확인한다.

```
#curl localhost:5000/v2/project_apex/d3_centos7/tags/list
{"name":"project_apex/d3_centos7","tags":["7.7.1908"]}
```
> 이미지의 태그 리스트를 확인한다.
> Registry 컨테이너는 /etc/docker/registry/config.yml 파일로 설정된다.
