Администрирование Docker Swarm – это важный навык для разработчиков и системных администраторов, которые хотят эффективно управлять контейнеризованными приложениями в распределенной среде. Docker Swarm предоставляет инструменты для создания и управления кластером из нескольких Docker-хостов, что позволяет легко масштабировать и управлять контейнерами на разных узлах.

### Шаг 1: Инициализация Swarm
1. Инициализация Docker Swarm на главном узле:
    ```
    docker swarm init
    ```
2. Присоединение рабочих узлов к кластеру:
    ```
    docker swarm join --token <TOKEN> <MANAGER-IP>:2377
    ```

### Шаг 2: Повышение и понижение статуса узлов
1. Повышение ноды до статуса менеджер:
    ```
    docker node promote <name_node>
    ```
2. Понижение ноды до воркер:
    ```
    docker node demote <name_node>
    ```

### Шаг 3: Инспекция нод
1. Инспектирование ноды:
    ```
    docker node inspect <name_node>
    ```

### Шаг 4: Удаление ноды из кластера
1. Удаление ноды из кластера:
    ```
    docker node rm <name_node>
    ```
2. Принудительное удаление ноды:
    ```
    docker node rm -f <name_node>
    ```

### Шаг 5: Развертывание приложений
1. Развертывание сервиса с использованием Docker Compose:
    ```
    version: "3"
    services:
      web:
        image: containous/whoami
        deploy:
          replicas: 6
          update_config:
            parallelism: 1
            order: start-first
            failure_action: rollback
            delay: 10s
          rollback_config:
            parallelism: 0
            order: stop-first
          restart_policy:
            condition: any
            delay: 5s
            max_attempts: 3
            window: 120s
          placement:
            constraints:
              - "node.role == worker"
    ```

### Шаг 6: Управление масштабированием и обновлением сервисов
1. Использование команд для управления:
    ```
    docker service scale <service_name>=<replicas>
    docker service update <service_name>
    ```

### Шаг 7: Мониторинг и управление кластером
1. Мониторинг состояния кластера:
    ```
    docker node ls
    docker node ps <name_node>
    ```
2. Управление сервисами и обновлениями:
    ```
    docker service ps <service_name>
    ```


