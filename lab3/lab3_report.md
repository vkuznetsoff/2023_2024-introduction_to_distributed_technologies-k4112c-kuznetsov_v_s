University: [ITMO University](https://itmo.ru/ru/) \
Faculty: [FICT](https://fict.itmo.ru) \
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies) \
Year: 2023/2024\
Group: K4112C\
Author: Kuznetsov Vyacheslav Sergeevich \
Lab: Lab2 \
Date of create: 06.12.2023 \
Date of finished: \

# Лабораторная работа №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных."

# Ход работы

## 1. Cоздание манифеста для Config Map:

Создаем ConfigMap декаларативным способом через создание манифеста, в котором прописываем
`kind: ConfigMap` и в поле `data` прописываем пары ключ-значение для того, чтобы в дальнейшем передать эти данные в манифест нашего Deployment:
`apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  username: 'VKuznetsov'
  company_name: 'ITMO`

Создадим ConfigMap c помощью команды `kubectl apply -f configmap.yaml` и посмотрим. что получилось:
![configmap](img/1.PNG)

## 2. Cоздание манифеста для Deployment:

Для развертывания `Deployment` я взял за основу манифест из `lab2` ![appyaml](app.yaml) , в котором в разделе описания переменных окружения `env` указал, что значения для переменных брать из `configMap` по соответсвующим ключам.

## 3. Разворачиваем Deployment

`kubectl apply -f app.yaml`

![manifest](img/2.PNG)

Убедимся, что переменные переданны корректно:

- Проверим созданные `pod'ы`:
  
  ![pods](img/3.PNG)

- С помощью команды `exec`, чтобы посмотреть содержимое запущенных подов:
  
  ![podsexec](img/4.PNG)

Переменные REACT_APP_USERNAME и REACT_APP_COMPANY_NAME переданы корректно!

## 4. Проверка работоспособности Сервиса:

Перенаправим порты и запустим сервис в браузере

![service](img/6.PNG)

ГОТОВО!

![localhost](img/7.PNG)

## 5. Создание TLS сертификата:

Создаем сертификат и ключ с помощью `openssl`:
![openssl](img/8.PNG)

## 6. Создание "секрета" с TLS сертификатом:

Создаем секрет с помощью команды
` kubectl create secret tls sertificate --cert="tsl_cert.crt" --key="tsl_key.key"`
![secret](9.PNG)

## 7. Запуск Ingress:

- Добавим Ingress контроллера в кластер Minikube
- 
  ![ingress](img/5.PNG)

- Сформируем Ingress для нашего сервиса с помощью  ![манифеста](https://github.com/vkuznetsoff/2023_2024-introduction_to_distributed_technologies-k4112c-kuznetsov_v_s/blob/master/ingress.yaml)
- 
  ![ingressscreen](img/10.PNG)

## 8. Запуск сервиса через host:

Я прописал в файлe `etc\host` доступ для хоста сервиса: `127.0.0.1 lab3kuznetsov.info` (Под Windows).
Далее переходим в браузере по адресу моего сервиса и видим, что сервис работает:

![browser](img/11.PNG)

Посмотрим сертификат:

![cert](img/12.PNG)

## 9. Схема организации контейеров и сервисов:

![scheme](img/14.png)
