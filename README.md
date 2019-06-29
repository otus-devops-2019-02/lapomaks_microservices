# lapomaks_microservices
lapomaks microservices repository

ДЗ: Docker-1
1. На локальную машину установлен docker
2. Изучены способы запуска контейнеров
  2.1. Создание и запуск контейнера docker run
  2.2. Отдельно создание, запуск и подключение к контейнеру
    docker create
    docker start
    docker attach
3. Освоены команды для просмотра сведений о запущенных контейнерах и созданных образах
  docker ps
  docker images
4. Просмотр информации о дисковом пространстве, занимаемом образами и контейнерами
  docker system df
5. Изучены команды для остановки и удаления контейнеров
  docker kill
  docker stop
  docker rm
  docker rmi

ДЗ: Docker-2
1. В GCP создан новый проект docker-239918 и локальная утилита gcloud настроена для работы с ним
2. На локальную машину установлен docker-machine и с его помощью в GCP создана ВМ с docker engine
3. Написаны и выполнены скрипты для создания docker image с установленым приложением reddit и настроеной БД Mongo
4. Созданный образ залит в docker hub

ДЗ: Docker-3
1. Приложение разбито на микросервисы post-py, comment, ui. Для каждого из которых собран свой образ.
2. Создана сеть reddit для взаимодействия между сервисами приложения. Проверена работа контейнеров внутри этой сети.
3. Создан volume для постоянного хранения информации, независимо от статуса контейнера. Созданный volume подключн к контейнеру с БД Mongo

ДЗ: Docker-4
1. Последовательно запущены контейнеры с разными типами сетей: none, host, bridge
2. Произведено разнесение контейнеров с сервисами UI и DB по разным сетям, чтобы они не имели доступа друг к другу, при этом контейнерам с сервисами Comment и Post назначены сетевые интерфейсы в обеих сетях
3. Создание, запуск и конфигурирование всех сервисов приложения собрано в единственном файле docker-compose.yml. Это позволяет запускать все необходимые сервисы одной командой.

В качестве базового имени проекта docker-compose по умолчанию берет имя папки, в которой находится docker-compose.yml. Переопределить это имя можно либо установив переменную окружения COMPOSE_PROJECT_NAME, либо передав имя проекта в качестве параметра при запуске docker-compose:  docker-compose -p <project_name> up -d

ДЗ: Gitlab-CI-1
1. В GCP создан инстанс на который установлен Gitlab-CI
2. В Gitlab-CI создан проект example и pipeline для него
3. В pipeline проекта добвлен вызов тестов из файла simpletest.rb
4. В проекте определены окружения для Dev, Stage и Production. Шаги Stage и Production сконфигурированы так, чтобы запускаться вручную и только если у комита проставлен тег версии.
5. В описании pipeline определено динамическое окружение, которое используется при комите изменений в ветку отличную от master

ДЗ: Monitoring-1
1. Запущен контейнер с Prometheus и изучены основные возможности этой системы мониторинга
2. Собран свой образ Prometheus с поддержкой мониторинга метрик приложения reddit
3. С помощью поднятого образа Prometheus произведено наблюдение за сервисами приложения reddit
4. С помощью node-экспортер произведен сбор метрик с хоста, на котором запущен контейнер с Prometheus

ДЗ: Monitoring-2
1. В Prometheus добавлен таск для наблюдения за метриками docker-контейнеров с помощью cAdvisor
2. Для визуализации собираемых с контейнеров метрик, к Prometheus подключен сервис Grafana
3. В Grafana созданы dashboards для визуализации кол-ва запросов и кол-ва запросов закончившихся ошибкой к сервисам приложения
4. Также в Grafana на созданный dashboard добавлена гистограмма для мониторинга времени ответа на запрос сервиса UI
5. В Grafana создан dashboard для наблюдения за бизнес-метриками приложения (кол-во постов и кол-во комментариев)
6. Подготовлен отдельный docker-image для компонента Alertmanager. Запуск созданного образа добавлен в docker-compose-monitoring.yml.
7. Alermanager сконфигурирован для отсылки сообщений в канал Slack в случае недоступности одного из микросервисов приложения

Репозиторий на DockerHub: https://hub.docker.com/u/lapomaks

ДЗ: Logging-1
1. В проект добавлен файл docker-compose-logging.yml, для запуска сервисов работы с логами приложения - fluend (для сбора логов), ElsaticSearch (для хранения логов) и Kibana (для визуализации логов)
2. Сервис приложения Post сконфигурирован для отправки логов с использованием Fluend
3. Произведен разбор логов сервиса Post с использованием средства визуализации логов Kibana
4. Сервис приложения UI настроен на отправку логов с использованием Fluend
5. В конфигурации fluentd описаны шаблоны для парсинга неструктурированных логов сервиса UI
6. В инфраструктуру логирования приложения (docker-compose-logging.yml) добавлен запуск сервиса распределенного трейсинга Zipkin. Также поддержка этого сервиса включена для сервисов приложения post, comment, ui.

ДЗ: Kubernetes-3
1. Подробно рассмотрен сетевой доступ к pod'ам кластера с помощью абстракции Service и использованием ClusterIP и kube-dns
2. Для внешнего доступа к подам с сервисом UI использован LoadBalancer от GCP
3. Организован доступ к приложению снаружи кластера с использованием балансировщика нагрузки от GCP в качестве Ingress Controller и описания Ingress-правил для доступа к сервису UI приложения
4. Для ingress сервиса UI создан SSL сертификат. Созданный сертификат использован для организации доступа к сервису UI только по HTTPS
5. Для изоляции сервисов приложения UI и MongoDb друг от друга использован NetworkPolicy
6. Для хранения данных независимо от подов в GCP создан диск. Созданный диск подключен к сервису MongoDb
7. В кластере создан PersistentVolume для использования ранее созданного диска GCP в качестве хранилища для всех сервисов этого кластера. Сделан запрос PersistentVolumeClaim для предоставления дискового пространства сервису MongoDb в рамках этого PersistentVolume
8. Для динамического создания дисков создан StorageClass. Добавлен PersistentVolumeClaim для запроса сервисом UI места в этом StorageClass

ДЗ: Kubernetes-4
1. На локальную машину установлена клиентская часть Helm, в кластер Kubernetes в GCP добавлена серверная часть Tiller
2. Подготовлены пакеты (чарты) Helm для каждой из частей приложения reddit. Также подготовлен общий чарт, который разворачивает сразу все приложение в кластере
3. Приложение reddit развернуто в кластере Kubernetes с помощью пакетного менеджера Helm
4. Используя пакетный менеджер Helm установил в кластер Gitlab
5. В Gitlab созданы pipelines для сборки каждого из проектов приложения c этапами Build, Test, Review, Release
6. Стадия Review в каждом из созданных pipeline настроена так, чтобы при выполнении разворачивать пакет с приложением в кластере Kubernetes и удалять его при остановке этапа Review
7. В проект добавлены окружения staging и production, которые используются для разворачивания всего приложения reddit

ДЗ: Kubernetes-5
1. В кластер установлен prometheus
2. С помощью ServiceDiscovery настроен сбор метрик с контейнеров запущенных в кластере
3. С помощью сервисов kube-state-metrics и node-exporter настроен сбор метрик объектов Kubernetes
4. Настроен сбор метрик с запущенного в кластере приложения reddit. Запущенные в кластере приложения также находятся при помощи ServiceDiscovery
