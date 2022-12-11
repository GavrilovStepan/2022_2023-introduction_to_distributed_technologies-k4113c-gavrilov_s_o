University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2022/2023  
Group: K4113c  
Author: Gavrilov Stepan Olegovich
Lab: Lab2  
Date of create: 11.12.2022  
Date of finished: 27.11.2022

# Лабораторная работа №2 "Развертывание веб сервиса в Minikube, доступ к веб интерфейсу сервиса. Мониторинг сервиса."

## Цель работы
Ознакомиться с типами "контроллеров" развертывания контейнеров, ознакомится с сетевыми сервисами и развернуть свое веб приложение.

---

## Ход работы

### Создание контейнера frontend-container
Скачиваем образ itdt-contained-frontend командой `docker pull ifilyaninitmo/itdt-contained-frontend:master`.

![](images/1.jpg)

Проверяем, что появился образ itdt-contained-frontend - `docker images`.

![](images/2.jpg)

Создаем контейнер на основе образа itdt-contained-frontend - `docker run -d --name frontend-container ifilyaninitmo/itdt-contained-frontend:master`.

![](images/3.jpg)

Проверяем, что появился контейнер frontend_container - `docker ps -a`.  

![](images/4.jpg)

### Создание Deployment

Запускаем minikube - `minikube start`. 

![](images/5.jpg)

Чтобы запустить 2 экземпляра пода, используем свойства `replicas: 2`.

Шаблон пода задается в объекте `Template`. С помощью свойства `env` объявляем внутри подов переменные окружения `REACT_APP_USERNAME` и `REACT_APP_COMPANY_NAME` со значениями `Stepan` и `ITMO`, соответственно.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend_pod
  template:
    metadata:
      labels:
        app: frontend_pod
    spec:
      containers:
      - name: frontend-container
        image: ifilyaninitmo/itdt-contained-frontend:master
        ports:
        - containerPort: 3000
        env:
        - name: REACT_APP_USERNAME
          value: Stepan
        - name: REACT_APP_COMPANY_NAME
          value: ITMO
```

Переходим в папку с .yaml файлом и выполняем команду `kubectl create -f frontend-deployment.yaml`.

![](images/6.jpg)


### Создание сервиса frontend-service

Создаем сервис для доступа к развертыванию - `minikube kubectl -- expose deployment frontend --port=3000 --target-port=3000 --name=frontend-service --type=LoadBalancer`.

![](images/8.jpg)


Пробрасываем локальный порт на порт контейнера - `minikube kubectl -- port-forward service/frontend-service 3000:3000`.

![](images/9.jpg)


Открываем страницу `http://localhost:3000`.

![](images/10.jpg)


### Логи подов

Посмотрим список всех подов - `minikube kubectl get pods`. Как и должно было быть, deployment запустил 2 пода.

![](images/11.jpg)


Смотрим логи первого пода 

![](images/12.jpg)


Смотрим логи второго пода

![](images/13.jpg)


Чтобы удалить развертывание используем команду - `kubectl delete deployments/frontend`.

![](images/14.jpg)


Проверяем - `kubectl get deployments` 

![](images/15.jpg)

Останавливаем minikube командой `minikube stop`

![](images/16.jpg)


### Итог
С помощью deployment создали 2 пода на основе одного контейнера. Сервис типа LoadBalancer - эта абстракция, которая позволяет нам воспринимать группу подов (с одинаковой меткой) как единую сущность и работать с ними, используя сервис как единую точку доступа к ним.

    Именно поэтому логи первого и второго пода идентичны.

### Диаграмма
Схема организации контейнера и сервиса, нарисованная в [draw.io](https://app.diagrams.net/).

![](images/1.png)