Docker Swarm (или режим роя Docker) — это инструмент оркестрации контейнеров, упрощенный аналог Kubernetes. Он позволяет управлять несколькими контейнерами, развернутыми на нескольких хостах, на которых запущен Docker. В данном отчете мы рассмотрим основные команды управления контейнерами в режиме оркестрации и выполним практические задания.

### Инициализация кластера Docker Swarm

1. Инициализация кластера:
   ```
   docker swarm init --advertise-addr <IP add>
   ```

2. Пример вывода:
   ```
   $ docker swarm init --advertise-addr 192.168.0.8
   Swarm initialized: current node (jun7nndweicewg5i2cn42cwgo) is now a manager.

   To add a worker to this swarm, run the following command:

   docker swarm join --token SWMTKN-1-09nquu39t1znacthpl51omntawc958nre7q1fb2vllebz997cz-7l5yb7c3gvyrql5c2p281f2m4 192.168.0.8:2377
   ```

### Добавление узлов

1. Команда для добавления worker узла:
   ```
   docker swarm join --token <token> <manager-IP>:2377
   ```

2. Пример вывода команды для получения токена:
   ```
   $ docker swarm join-token worker
   ```

### Управление узлами

1. Команда для просмотра состава узлов:
   ```
   $ docker node ls
   ```

2. Удаление ноды из кластера:
   ```
   docker swarm leave
   docker swarm rm <node_name>
   ```

### Развертывание сервисов

1. Создание сервиса:
   ```
   docker service create --replicas 1 --name helloworld1 alpine ping vk.com
   ```

2. Просмотр существующих сервисов:
   ```
   $ docker service ls
   ```

3. Просмотр информации о сервисе:
   ```
   $ docker service inspect --pretty helloworld1
   ```

4. Удаление сервиса:
   ```
   docker service rm helloworld1
   ```

### Масштабирование и обновление сервиса

1. Создание сервиса с одной репликой:
   ```
   docker service create --replicas 1 --name whoami containous/whoami
   ```

2. Увеличение количества реплик до 5:
   ```
   docker service scale whoami=5
   ```

3. Обновление сервиса:
   ```
   docker service update --image stefanscherer/whoami whoami
   ```

