Загрузим образ ubuntu с помощью команды `docker pull ubuntu`, создадим и запустим контейнер для бекапа backup_ubuntu с помощью команды `docker run -itd --name=backup_ubuntu ubuntu:latest `, войдем в оболочку bash контейнера и изменим его изнутри обновлениями.

```
vitya@vitya-VirtualBox:~/Рабочий стол$ docker pull ubuntu
Using default tag: latest
latest: Pulling from library/ubuntu
de44b265507a: Already exists 
Digest: sha256:80dd3c3b9c6cecb9f1667e9290b3bc61b78c2678c02cbdae5f0fea92cc6734ab
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest

vitya@vitya-VirtualBox:~/Рабочий стол$ docker run -itd --name=backup_ubuntu ubuntu:latest
c014ffda4079d7d7458459494e79f8086faa456ef8df7d3a74412bf49a2a776b

vitya@vitya-VirtualBox:~/Рабочий стол$ docker exec -it backup_ubuntu bash
root@c014ffda4079:/# apt update -y && apt upgrade -y
Get:1 http://security.ubuntu.com/ubuntu noble-security InRelease [126 kB]
Get:2 http://archive.ubuntu.com/ubuntu noble InRelease [256 kB]
Get:3 http://archive.ubuntu.com/ubuntu noble-updates InRelease [126 kB]
Get:4 http://archive.ubuntu.com/ubuntu noble-backports InRelease [126 kB]
Get:5 http://archive.ubuntu.com/ubuntu noble/restricted amd64 Packages [117 kB]
Get:6 http://archive.ubuntu.com/ubuntu noble/universe amd64 Packages [19.3 MB]
Get:7 http://security.ubuntu.com/ubuntu noble-security/main amd64 Packages [719 kB]
Get:8 http://security.ubuntu.com/ubuntu noble-security/universe amd64 Packages [1028 kB]
Get:9 http://security.ubuntu.com/ubuntu noble-security/multiverse amd64 Packages [15.3 kB]
Get:10 http://archive.ubuntu.com/ubuntu noble/main amd64 Packages [1808 kB]   
Get:11 http://archive.ubuntu.com/ubuntu noble/multiverse amd64 Packages [331 kB]
Get:12 http://archive.ubuntu.com/ubuntu noble-updates/restricted amd64 Packages [724 kB]
Get:13 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 Packages [1254 kB]
Get:14 http://security.ubuntu.com/ubuntu noble-security/restricted amd64 Packages [707 kB]
Get:15 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 Packages [968 kB]
Get:16 http://archive.ubuntu.com/ubuntu noble-updates/multiverse amd64 Packages [19.7 kB]
Get:17 http://archive.ubuntu.com/ubuntu noble-backports/universe amd64 Packages [11.9 kB]
Fetched 27.7 MB in 4s (6500 kB/s)                            
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
root@c014ffda4079:/# exit
exit

```
Видим, что на данный момент образом у нас считается только ubuntu, запуленный в самом начале. Теперь нужно сделать с помощью команды `docker commit backup_ubuntu ubuntu_update_23_12_24`. Посмотрим, что получилось. Заметим, что увеличился вес образа.
```
vitya@vitya-VirtualBox:~/Рабочий стол$ docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED         STATUS         PORTS     NAMES
c014ffda4079   ubuntu:latest   "/bin/bash"   6 minutes ago   Up 6 minutes             backup_ubuntu

vitya@vitya-VirtualBox:~/Рабочий стол$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
mongo         latest    f08e39122805   2 weeks ago     855MB
nginx         alpine    91ca84b4f577   3 weeks ago     52.5MB
nginx         latest    66f8bdd3810c   3 weeks ago     192MB
ubuntu        latest    b1d9df8ab815   4 weeks ago     78.1MB
hello-world   latest    d2c94e258dcb   20 months ago   13.3kB

vitya@vitya-VirtualBox:~/Рабочий стол$ docker commit backup_ubuntu ubuntu_update_23_12_24
sha256:582eaff706b12c0d89e051a967afc9b16d62789f1425f212b9eeb786ce9f5dde

vitya@vitya-VirtualBox:~/Рабочий стол$ docker images
REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
ubuntu_update_23_12_24   latest    582eaff706b1   8 seconds ago   123MB
mongo                    latest    f08e39122805   2 weeks ago     855MB
nginx                    latest    66f8bdd3810c   3 weeks ago     192MB
nginx                    alpine    91ca84b4f577   3 weeks ago     52.5MB
ubuntu                   latest    b1d9df8ab815   4 weeks ago     78.1MB
hello-world              latest    d2c94e258dcb   20 months ago   13.3kB

```
Теперь создаем директорию delete_test, в которой создадим пустой файл delete.py, переестим файл внутрь контейнера backup_ubuntu, там переименуем его и скопируем обратно из контейнера.
```
vitya@vitya-VirtualBox:~/Рабочий стол$ mkdir delete_test && cd delete_test && touch delete.py && ls -la
итого 8
drwxrwxr-x 2 vitya vitya 4096 дек 23 19:24 .
drwxr-xr-x 3 vitya vitya 4096 дек 23 19:24 ..
-rw-rw-r-- 1 vitya vitya    0 дек 23 19:24 delete.py

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker cp ./delete.py backup_ubuntu:/home
Successfully copied 1.54kB to backup_ubuntu:/home

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker exec backup_ubuntu sh -c "ls /home"
delete.py
ubuntu

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker exec backup_ubuntu sh -c "mv /home/delete.py /home/delete1.py && ls /home"
delete1.py
ubuntu

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker cp backup_ubuntu:/home/delete1.py . && ls
Successfully copied 1.54kB to /home/vitya/Рабочий стол/delete_test/.
delete1.py  delete.py

```
Создадим новый образ с помощью команды tag, выполним бэкап с помощью команды save в файл ubuntu_save.tar
```
vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker tag ubuntu ubuntu_save:v1

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker save ubuntu_save:v1 > ubuntu_save.tar && ls
delete1.py  delete.py  ubuntu_save.tar

```

Удалим образ ubuntu_save:v1, чтобы в последующем его восстановить, после чего создадим образ из контейнера со всеми изменениями с помощью команды export

```
vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker rmi ubuntu_save:v1
Untagged: ubuntu_save:v1

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker export backup_ubuntu > ubuntu_export.tar && ls
delete1.py  delete.py  ubuntu_export.tar  ubuntu_save.tar

```
Теперь восстановим контейнер save и импортируем сохраненный образ в ubuntu_export
```
vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker load < ubuntu_save.tar
Loaded image: ubuntu_save:v1

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ sudo docker import ubuntu_export.tar ubuntu_import
[sudo] пароль для vitya: 
sha256:33dff3421eb3679d67145f53546c841a9dbd78e013b895797c7576d740ef1956

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ 
vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker images
REPOSITORY               TAG       IMAGE ID       CREATED          SIZE
ubuntu_import            latest    33dff3421eb3   22 seconds ago   123MB
ubuntu_update_23_12_24   latest    582eaff706b1   25 minutes ago   123MB
mongo                    latest    f08e39122805   2 weeks ago      855MB
nginx                    alpine    91ca84b4f577   3 weeks ago      52.5MB
nginx                    latest    66f8bdd3810c   3 weeks ago      192MB
ubuntu                   latest    b1d9df8ab815   4 weeks ago      78.1MB
ubuntu_save              v1        b1d9df8ab815   4 weeks ago      78.1MB
hello-world              latest    d2c94e258dcb   20 months ago    13.3kB

```
Резервное копирование docker volume

создадим volume nginx, просмотрим и изменим его index.html

```
vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ docker run  -p 80:80 --name nginx -v nginx:/usr/share/nginx/html -d nginx
6e67f10732f01fbc2cfe084e2161f650d7d0b6804008bc5f7ad3204f38110e48

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ sudo docker volume ls
DRIVER    VOLUME NAME
local     40762a99c0de3a3e944c98a8611c939bfb202d46b2acbd242652583305536e92
local     e655652b0715e0c2031a187bde58173f23586c757348fcf77e2cd7a86e1d37bf
local     nginx

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ sudo ls /var/lib/docker/volumes/nginx/_data
50x.html  index.html

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ curl localhost:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ curl localhost:80
<!DOCTYPE html>
<html>
<head>
<title>HELLO!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>HELLO!</h1>


<p><em>Thank you for using nginx.</em></p>
</body>
</html>


```
Извлекаем в известную директорию, после чего посредствой контейнера восстанавливаем данные

```
vitya@vitya-VirtualBox:~/Рабочий стол/delete_test$ cd ..

vitya@vitya-VirtualBox:~/Рабочий стол$ cd ..

vitya@vitya-VirtualBox:~$ sudo docker run --rm --volumes-from nginx -v $(pwd):/backup busybox tar cvf /backup/backup.tar /usr/share/nginx/html
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
9c0abc9c5bd3: Pull complete 
Digest: sha256:2919d0172f7524b2d8df9e50066a682669e6d170ac0f6a49676d54358fe970b5
Status: Downloaded newer image for busybox:latest
tar: removing leading '/' from member names
usr/share/nginx/html/
usr/share/nginx/html/index.html
usr/share/nginx/html/50x.html

vitya@vitya-VirtualBox:~$ docker inspect nginx | grep Destination
                "Destination": "/usr/share/nginx/html",

vitya@vitya-VirtualBox:~$ tar -tf backup.tar
usr/share/nginx/html/
usr/share/nginx/html/index.html
usr/share/nginx/html/50x.html

vitya@vitya-VirtualBox:~$ sudo tar -xvf backup.tar --directory ./'Рабочий стол'/delete_test
usr/share/nginx/html/
usr/share/nginx/html/index.html
usr/share/nginx/html/50x.html

vitya@vitya-VirtualBox:~$ docker run -p 81-80 --name nginx1 -v nginx1:/usr/share/nginx/html -d nginx
docker: invalid containerPort: 81-80.
See 'docker run --help'.

vitya@vitya-VirtualBox:~$ docker run -p 81:80 --name nginx1 -v nginx1:/usr/share/nginx/html -d nginx
0cbe7ee9ebfe223e0eebae0a142ed527dd3ec35ecf114f5dd940292e1cd2090d

vitya@vitya-VirtualBox:~$ sudo docker run --rm --volumes-from nginx1 -v $(pwd):/backup busybox tar xvf /backup/backup.tar -C .
usr/share/nginx/html/
usr/share/nginx/html/index.html
usr/share/nginx/html/50x.html

```

Создание docker-compose файла:

```
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - nginx-data:/usr/share/nginx/html

volumes:
  nginx-data:
```

Запуск контейнера

```
Для запуста файлика .yml нужно воспользоваться командой `docker-compose up -d`