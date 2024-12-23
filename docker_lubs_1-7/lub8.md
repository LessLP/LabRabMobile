1. Docker pause, unpause
позволяет приостановить процесс и возобносвить его
docker info отображает общесистемную информацию о состоянии и настройке Docker на хосте, включая: версию ядра, количество уникальных контейнеров и образов, установленные плагины, используемый репозиторий контейнеров и т. д. Представленные сведения говорят нам о том, в какой спецификации работает Docker на хосте.
```
Client:
 Version:    26.1.3
 Context:    default
 Debug Mode: false

Server:
 Containers: 1
  Running: 0
  Paused: 0
  Stopped: 1
 Images: 2
 Server Version: 26.1.3
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Using metacopy: false
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 Swarm: inactive
 Runtimes: runc io.containerd.runc.v2
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 
 runc version: 
 init version: 
 Security Options:
  apparmor
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.11.0-13-generic
 Operating System: Ubuntu 24.10
 OSType: linux
 Architecture: x86_64
 CPUs: 4
 Total Memory: 12.96GiB
 Name: vitya-VirtualBox
 ID: 27284d87-f71e-435a-b0b1-ceec0b42a165
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false

```
2. Запустить произвольный контейнер, в моем случае будет nginx, порт проброшенный на 8080 с 80. Выполните тестирование производительности. Оценить общую производительность во время выполения теста системы. Заморозьте контейнер. Разморозить контейнер, после чего удалить его.

создадим контейнер командой `docker run -d --name my_nginx -p 8080:80 nginx`, проверим, что контейнер работает:
```
vitya@vitya-VirtualBox:~/Рабочий стол$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                   NAMES
d53c7a58945d   nginx     "/docker-entrypoint.…"   6 minutes ago   Up 6 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   my_nginx

```
сам код сайта можно посмотреть с помощью команды curl:
```
vitya@vitya-VirtualBox:~/Рабочий стол$ curl http://localhost:8080
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

```
С помощью комманды `docker stats` получим значения производительности контейнеров, в нашем случае, nginx:
```
CONTAINER ID   NAME       CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O         PIDS
d53c7a58945d   my_nginx   0.00%     4.582MiB / 12.96GiB   0.03%     27.5kB / 1.27kB   61.4kB / 12.3kB   5
^C

```
После этого заморозим выполнение контейнера, просмотрим его логи, остановим и удалим его

```
vitya@vitya-VirtualBox:~/Рабочий стол$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                  PORTS                                   NAMES
d53c7a58945d   nginx     "/docker-entrypoint.…"   7 minutes ago   Up 7 minutes (Paused)   0.0.0.0:8080->80/tcp, :::8080->80/tcp   my_nginx

vitya@vitya-VirtualBox:~/Рабочий стол$ docker logs my_nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/12/23 18:09:46 [notice] 1#1: using the "epoll" event method
2024/12/23 18:09:46 [notice] 1#1: nginx/1.27.3
2024/12/23 18:09:46 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/12/23 18:09:46 [notice] 1#1: OS: Linux 6.11.0-13-generic
2024/12/23 18:09:46 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:524288
2024/12/23 18:09:46 [notice] 1#1: start worker processes
2024/12/23 18:09:46 [notice] 1#1: start worker process 29
2024/12/23 18:09:46 [notice] 1#1: start worker process 30
2024/12/23 18:09:46 [notice] 1#1: start worker process 31
2024/12/23 18:09:46 [notice] 1#1: start worker process 32
172.17.0.1 - - [23/Dec/2024:18:10:24 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/8.9.1" "-"

vitya@vitya-VirtualBox:~/Рабочий стол$ docker unpause my_nginx
my_nginx


```
Не удаляя контейнер my_nginx включим контейнер с mongo db:
```
vitya@vitya-VirtualBox:~/Рабочий стол$ docker run -d --name my_mongodb -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=123 mongo
Unable to find image 'mongo:latest' locally
latest: Pulling from library/mongo
de44b265507a: Pull complete 
add2cfa32b4d: Pull complete 
0d3422d31c84: Pull complete 
e9869afb5187: Pull complete 
9284108c06f8: Pull complete 
17351a831ef1: Pull complete 
2613644e011d: Pull complete 
05cc0f1cded4: Pull complete 
Digest: sha256:4f93a84f7d4d8b1b6cb7e0c172d8a44b0bed9b399f207165ea19473bdb5a36b0
Status: Downloaded newer image for mongo:latest
8f489b9880efcf1d2894f4ce794e6df0f3066d6157d8118cb24e93e7b54d79db

vitya@vitya-VirtualBox:~/Рабочий стол$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                           NAMES
8f489b9880ef   mongo     "docker-entrypoint.s…"   6 seconds ago    Up 5 seconds    0.0.0.0:27017->27017/tcp, :::27017->27017/tcp   my_mongodb
d53c7a58945d   nginx     "/docker-entrypoint.…"   21 minutes ago   Up 21 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp           my_nginx

```
Посмотрим статистику этих контейнеров с использованием `--format`

```
vitya@vitya-VirtualBox:~/Рабочий стол$ docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}" --no-stream
NAME         CPU %     MEM USAGE / LIMIT     NET I/O
my_mongodb   3.21%     84.93MiB / 12.96GiB   18.5kB / 291B
my_nginx     0.00%     4.598MiB / 12.96GiB   39.1kB / 1.27kB

```

Теперь остановим и удалим эти контейнеры:
```
vitya@vitya-VirtualBox:~/Рабочий стол$ docker stop my_mongodb, my_nginx
my_nginx
Error response from daemon: No such container: my_mongodb,
vitya@vitya-VirtualBox:~/Рабочий стол$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                           NAMES
8f489b9880ef   mongo     "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes   0.0.0.0:27017->27017/tcp, :::27017->27017/tcp   my_mongodb
vitya@vitya-VirtualBox:~/Рабочий стол$ docker stop my_mongodb
my_mongodb
vitya@vitya-VirtualBox:~/Рабочий стол$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                      PORTS     NAMES
8f489b9880ef   mongo     "docker-entrypoint.s…"   5 minutes ago    Exited (0) 8 seconds ago              my_mongodb
d53c7a58945d   nginx     "/docker-entrypoint.…"   27 minutes ago   Exited (0) 47 seconds ago             my_nginx
vitya@vitya-VirtualBox:~/Рабочий стол$ docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
8f489b9880efcf1d2894f4ce794e6df0f3066d6157d8118cb24e93e7b54d79db
d53c7a58945da07e29cb3ffab6d17cef2acbfc000924356421b3b9602af37e63

Total reclaimed space: 1.093kB

```