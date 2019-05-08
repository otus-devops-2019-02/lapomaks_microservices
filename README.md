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
