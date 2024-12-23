
### Шаг 1: Создание Swarm-кластера
Создание Swarm-кластера из 3 нод:
```
$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
pq1nloctyi7tk4tws65qe8ss8 *   node1      Ready     Active         Leader           24.0.7
y2cuf5qxiumu10ex8q24gh9ri     node2      Ready     Active                          24.0.7
1zl2sebaak2znlkzht0tnrzjl     node3      Ready     Active                          24.0.7
```

### Шаг 2: Развертывание тестового сервиса
Создание тестовой конфигурации nginx:
```
version: "3.9"
services:
  nginx:
    image: nginx:alpine
```
Выполнение первого деплоя и просмотр расположения контейнера:
```
$ docker service ps test_stack_nginx 
ID             NAME                 IMAGE          NODE      DESIRED STATE   CURRENT STATE            ERROR     PORTS
344y6bdg9xz7   test_stack_nginx.1   nginx:alpine   node1     Running         Running 10 minutes ago
```

### Шаг 3: Преобразование сервиса и добавление контейнера
Добавление сервиса whoami к стеку:
```
version: "3.9"
services:
  nginx:
    image: nginx:alpine
    container_name: my_nginx
    ports:
      - "80:80"
  whoami:
    image: containous/whoami
    container_name: whoami
    ports:
      - "81:80"
```
Запуск стека:
```
$ docker stack deploy --compose-file docker-compose.yml name_stack
```
Просмотр состояния кластера:
```
$ docker service ls
ID             NAME                MODE         REPLICAS   IMAGE                      PORTS
uewk38422p9q   name_stack_nginx    replicated   1/1        nginx:alpine               *:82->80/tcp
vda86clllli1   name_stack_whoami   replicated   1/1        containous/whoami:latest   *:81->80/tcp
```

### Шаг 4: Использование секции deploy и секретов
Удаление существующих сервисов:
```
$ docker stack rm name_stack
```
Создание секрета и добавление его к контейнеру с базой данных:
```
$ echo "my_password" | docker secret create db_password -
$ docker run -d --name db --secret db_password mysql:latest
$ docker exec -it db bash
$ cat /run/secrets/db_password
```
Использование секции deploy в docker-compose.yml:
```
services:
  frontend:
    image: example/webapp
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 50M
        reservations:
          cpus: '0.25'
          memory: 20M
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
      update_config:
        parallelism: 2
        delay: 10s
        order: stop-first
```



