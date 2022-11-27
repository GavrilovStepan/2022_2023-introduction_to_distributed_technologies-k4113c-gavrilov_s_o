
University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2022/2023  
Group: K4113c  
Author: Gavrilov Stepan Olegovich
Lab: Lab1  
Date of create: 25.11.2022  
Date of finished: 27.11.2022

# Лабораторная работа №1 "Установка Docker и Minikube, мой первый манифест."

## Описание
В данной лабораторной работе необходимо протестировать Docker, установить Minikube и развернуть свой первый "под".

## Цель работы
Ознакомиться с инструментами Minikube и Docker, развернуть свой первый "под".

---

## Ход работы


### Создание контейнера vault
Скачиваем образ vault командой `docker pull vault` и проверяем, что появился образ vault - `docker images`.

![Образ vault](images/vault.png 'Образ vault')

Создаем контейнер на основе образа vault - `docker run -d --name vault vault`.  
Проверяем, что появился контейнер vault - `docker ps -a`.  

![Контейнер vault](images/vault_container.png 'Контейнер vault')

### Создание Pod
Запускаем minikube - `minikube start`.  
Проверяем, что появился узел - `kubectl get nodes`.  

![узел get nodes](images/nodes.png 'узел get nodes')

Создадим **manifest file**, в котором будет описан наш под.  

Для создания корректного описания манифеста в YAML-формате достаточно знать только два типа структур:

* списки (lists)
* мапы (maps)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: vault
  labels:
    environment: dev
    tier: vault
spec:
  containers:
    - name: vault
      image: vault
      ports:
        - containerPort: 8200
```
Переходим в папку с .yaml файлом и выполняем команду `kubectl create -f vault_pod.yaml`.
Проверяем, что появился Pod - `kubectl get pods`.

![Pod vault](images/vault_pod.png 'Pod vault')

### Создание сервиса
Создаем сервис для доступа к Pod - `minikube kubectl -- expose pod vault --type=NodePort --port=8200`.

Перенаправляем трафик с Pod на локальный - `minikube kubectl -- port-forward service/vault 8200:8200`.

Открываем страницу авторизации Vault `http://localhost:8200`.

![Vault page](images/localhost.png 'Vault page')

### Поиск токена
Чтобы найти токен для авторизации, открываем второй терминал и используем команду ~~`docker logs vault`~~ или `minikube kubectl -- logs service/vault`.

> Root Token: hvs.rbHyqj8rhe9QNQLskUEUqSXk

![Root Token](images/Root_Token.png 'Root Token')

![Successful authorization](images/success.png 'Successful authorization')

Работа выполнена - останавливаем узел командой `minikube stop`.

### Диаграмма
Схема организации контейнера и сервиса, нарисованная в [draw.io](https://app.diagrams.net/).

![Диаграмма](https://github.com/AnatoliyBr/2022_2023-introduction_to_distributed_technologies-k4111c-briushinin_a_a/blob/master/lab1/images/lab1_diagram.png 'Диаграмма')

