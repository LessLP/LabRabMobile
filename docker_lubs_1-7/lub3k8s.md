
### Первое приложение в Kubernetes

1. **Установка Minikube и kubectl**
    - Установите Minikube и kubectl, если они еще не установлены.
    - Запустите Minikube: `minikube start`

2. **Создание Deployment**
    - Напишите манифест Deployment для развертывания приложения.
    - Пример манифеста:
      ```yaml
      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: my-app
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: my-app
        template:
          metadata:
            labels:
              app: my-app
          spec:
            containers:
            - name: my-app
              image: my-app-image:latest
              ports:
              - containerPort: 80
      ```

3. **Создание сервиса**
    - Напишите манифест сервиса для доступа к приложению.
    - Пример манифеста:
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: my-app-service
      spec:
        selector:
          app: my-app
        ports:
        - protocol: TCP
          port: 80
          targetPort: 80
        type: NodePort
      ```

4. **Применение манифестов**
    - Примените созданные манифесты:
      ```sh
      kubectl apply -f deployment.yaml
      kubectl apply -f service.yaml
      ```

5. **Проверка работы приложения**
    - Проверьте статус подов: `kubectl get pods`
    - Получите URL-адрес сервиса: `minikube service my-app-service --url`
    - Откройте полученный URL в браузере и проверьте работу приложения.


### Файлы манифестов
- `deployment.yaml`
- `service.yaml`
