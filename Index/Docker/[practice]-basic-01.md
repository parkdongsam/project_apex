### Volume Share
```
#docker run -d --name db_volume -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -v /home/wordpress_db:/var/lib/mysql mysql:5.7
```
```
docker run -d -e WORDPRESS_DB_PASSWORD=password --name wordpress_volume --link db_volume:mysql -p 80 wordpress
```
> -v 옵션을 이용하여 호스트 볼륨과 컨테이너 볼륨을 연결  
> -v|--volume[=[[HOST-DIR:]CONTAINER-DIR[:OPTIONS]]]   
> ex)  -v /home/wordpress_db:/var/lib/mysql
> 호스트의 디렉토리를 클라이언트에 마운트 한다.
> --volume-from 으로 새로운 컨테이너에 볼륨 공유가 가능하다.
> --mount 옵션도 동일한 기능

```
#docker volume create --name test_volume
test_volume
// 볼륨 생성

#docker volume ls
DRIVER              VOLUME NAME
local               test_volume
```
```
#docker run -i -t --name myvolume-1 -v test_volume:/root/ centos:centos7
#docker run -i -t --name myvolume-2 -v test_volume:/root/ centos:centos7
```
> myvolume-1, 2 컨테이너 모두 Host의 test_volume 볼륨을 /root 디렉토리에 마운트하여 사용한다.
---

```
#docker inspect --type volume test_volume
[
    {
        "CreatedAt": "2020-04-19T21:53:58+09:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/test_volume/_data",
        "Name": "test_volume",
        "Options": {},
        "Scope": "local"
    }

```
> 생성한 Docker 볼륨이 실제 저장되는 경로를 확인 한다.

```
#docker volume prune          // 볼륨 전체 삭제
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N]
```
