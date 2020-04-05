#### запускаем docker контейнер
> docker-compose up -d

#### заходим в контейнер
> docker exec -it [наименование контейнера] bash

#### например 
> docker exec -it mongo-learn bash

#### авторизуемся
> mongo admin -u tdmitriiv -p 537831

#### выполняем комманды
> show dbs - список баз данных  
> use mongoLearn - создание или переключение на базу mongoLearn


