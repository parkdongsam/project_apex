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
