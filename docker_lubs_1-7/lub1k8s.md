
В данной лабораторной работе рассматривается процесс установки и настройки Minikube - инструмента для локального запуска Kubernetes кластеров. Основной задачей работы является приобретение практических навыков по установке и настройке Minikube, а также знакомство с базовыми операциями Kubernetes.

### Установка Minikube
#### Загрузка бинарного файла
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

#### Установка Minikube
```
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### Шаг 2: Запуск Minikube
#### Инициализация кластера
```
minikube start
```
Эта команда создаст виртуальную машину и запустит Kubernetes кластер.

#### Проверка статуса
```
minikube status
```

### Шаг 3: Установка kubectl
#### Проверка наличия kubectl
```
kubectl version --client
```

#### Установка kubectl (при необходимости)
```
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

#### Проверка конфигурации kubectl
```
kubectl config view
```
Убедитесь, что `kubectl` настроен для работы с кластером Minikube.

### Шаг 4: Доступ к Dashboard
#### Запуск Dashboard
```
minikube dashboard
```
Эта команда откроет веб-интерфейс Kubernetes Dashboard в браузере.

#### Альтернативный доступ
Если команда не открывает браузер автоматически, получите URL-адрес:
```
minikube dashboard --url
```
Перейдите по полученному URL в браузере.

### Шаг 5: Создание и управление ресурсами
#### Создание Deployment
```
kubectl create deployment my-app --image=nginx
```
Это создаст Deployment с именем `my-app`, использующий образ `nginx`.

#### Проверка статуса Pod
```
kubectl get pods
```
Убедитесь, что Pod запущен и работает корректно.

#### Создание Service
```
kubectl expose deployment my-app --type=NodePort --port=80
```
Это создаст Service, доступный на порту 80.

#### Получение URL доступа
```
minikube service my-app --url
```


### Шаг 6: Остановка и удаление кластера
#### Остановка Minikube
```
minikube stop
```
Эта команда остановит виртуальную машину и кластер Kubernetes.

#### Удаление Minikube
```
minikube delete
```
Удалит виртуальную машину и все связанные ресурсы.
