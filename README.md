# adastraaero_microservices
adastraaero microservices repository


## Docker - 2 
Устанавливаем Docker
https://docs.docker.com/engine/install/ubuntu/


Client: Docker Engine - Community
 Cloud integration: v1.0.24
 Version:           20.10.14
 API version:       1.41
 Go version:        go1.16.15
 Git commit:        a224086
 Built:             Thu Mar 24 01:47:58 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.14
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.15
  Git commit:       87a90dc
  Built:            Thu Mar 24 01:45:50 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.5.11
  GitCommit:        3df54a852345ae127d1fa3092b95168e4a88e2f8
 runc:
  Version:          1.0.3
  GitCommit:        v1.0.3-0-gf46b6ba
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
  
  
  
Устанавливаем docker machine https://github.com/docker/machine/releases


  
Создаем Docker machine


```
yc compute instance create \
  --name docker-host \
  --zone ru-central1-a \
  --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
  --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-lts,size=15 \
  --ssh-key ~/.ssh/id_rsa.pub

```  
  

``` 
docker-machine create \
--driver generic \
--generic-ip-address=51.250.14.22 \
--generic-ssh-user yc-user \
--generic-ssh-key /home/mity/.ssh/id_rsa \
docker-host

```


Переключим docker на удаленный хост:


```
eval $(docker-machine env docker-host)

```

Создаем нужные файлы, собираем образ и запускаем контейнер.

```
docker build -t reddit:latest .
docker run --name reddit -d --network=host reddit:latest
```


Проверяем

docker-machine ls
NAME          ACTIVE   DRIVER    STATE     URL                       SWARM   DOCKER      ERRORS
docker-host   *        generic   Running   tcp://51.250.14.22:2376           v20.10.17


заходим через браузер и проверяем http://51.250.14.22:9292 .


### Docker hub

Отправляем образ в docker репозиторий:

```
docker login

docker tag reddit:latest adastraaero/otus-reddit:1.0
docker push adastraaero/otus-reddit:1.0
```

Проверим:

```
docker run --name reddit -d -p 9292:9292 adastraaero/otus-reddit:1.0
```

### Задания со ⭐
1. [docker-1.log](dockermonolit/docker-1.log)

## Docker - 3

Пересоздаем и запускаем dpcker host:

```
yc compute instance create \
  --name docker-host \
  --zone ru-central1-a \
  --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
  --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-lts,size=15 \
  --ssh-key ~/.ssh/id_rsa.pub


docker-machine create \
--driver generic \
--generic-ip-address=51.250.74.249 \
--generic-ssh-user yc-user \
--generic-ssh-key /home/mity/.ssh/id_rsa \
docker-host

```

Переключим docker на удаленный хост:


```
eval $(docker-machine env docker-host)

```

Скачиваем архив, перемещаем файлы, добавляем в них данные. Используем для сборки Ubuntu.
Ставим и применяем hadolint на наши Dockerfiles.

Собираем приложение:

```
docker pull mongo:latest
docker build -t adastraaero/post:1.0 ./post-py
docker build -t adastraaero/comment:1.0 ./comment
docker build -t adastraaero/ui:1.0 ./ui

```

Проверяем создание образов:

```
docker images
```
Создаем и проверяем сеть для приложения

```
docker network create reddit

docker network ls 

```

Запускаем контейнеры и проверяем работу приложения.


```
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:latest
docker run -d --network=reddit --network-alias=post adastraaero/post:1.0
docker run -d --network=reddit --network-alias=comment adastraaero/comment:1.0
docker run -d --network=reddit -p 9292:9292 adastraaero/ui:1.0
```

http://51.250.74.249:9292/





```
