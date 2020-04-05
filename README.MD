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
> db.dropDatabase() - удалить бд
> db.collectionName.drop() - удалить коллекцию

### CRUD
> db.collectionName.findOne({}) - поиск 1 элемента
> db.collectionName.find({}) - поиск всех
> db.collectionName.find({}).pretty() - поиск с форматированием
> db.collectionName.insertOne({ data: 1 }) - создание объекта
> db.collectionName.insertMany([{ data: 1 }, { data: 2 }]) - создание нескольких объектов
> db.collectionName.updateOne({ distance: 12000 }, {$set: { marker: 'delete' }}) - добавление нового поля marker для 1 объекта
> db.collectionName.updateMany({}, { $set: { marker: 'toDelete' } }) - добавить / обновить поле marker всем объектам
> db.collectionName.deleteOne({ data: 1 }) - удаление объекта с data=1
> db.collectionName.deleteMany({marker: 'toDelete'}) - удаление объектов с marker'ом
> db.collectionName.replaceOne({ data: 1}, {data: 2}) - заменить объект по критерию data=1

> db.collectionName.find({}).toArray()
> db.collectionName.find({}).forEach()
> db.collectionName.find({}, {name: 1,_id: 0}) - фильтрация полей при выборке
> db.collectionName.find({ "status.details.responsible": "Dmitry Trofimov"}).pretty() - поиск во вложенных объектах


