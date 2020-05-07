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
```js
db.collectionName.insertMany(
[
  {
    data: 1 
  }, 
  { 
    data: 2 
  }
], options) 
/*
создание нескольких объектов
  options - объект
  ordered: false - продолжить добавлять, если предыдущий добавился с ошибкой
*/
```

> db.collectionName.updateOne({ distance: 12000 }, {$set: { marker: 'delete' }}) - добавление нового поля marker для 1 объекта
> db.collectionName.updateMany({}, { $set: { marker: 'toDelete' } }) - добавить / обновить поле marker всем объектам
> db.collectionName.deleteOne({ data: 1 }) - удаление объекта с data=1
> db.collectionName.deleteMany({marker: 'toDelete'}) - удаление объектов с marker'ом
> db.collectionName.replaceOne({ data: 1}, {data: 2}) - заменить объект по критерию data=1

> db.collectionName.find({}).toArray()
> db.collectionName.find({}).forEach()
> db.collectionName.find({}, {name: 1,_id: 0}) - фильтрация полей при выборке
> db.collectionName.find({ "status.details.responsible": "Dmitry Trofimov"}).pretty() - поиск во вложенных объектах

### Модификаторы поиска
```
$exists - существование
$gt, gtem, $lt, $lte - greater, less then equal
db.collectionName.find({ name: { $type: ["number", "string"] }}).pretty() - проверки типа поля (number или string)
$jsonSchema - поиск по json схеме, если разные объекты в коллекции
db.movies.find({ summary: { $regex: /musical/}}) - поиск в тексте по регулярке
db.products.find({ $expr: { $lt: ["$compare_price", "$price"] }}) - найти товары где скидочная цена меньше обычной
```
### Поиск по массиву
```
db.collectionName.find({ "field.property": value }) - поиск по полю property в массиве field
db.collectionName.find({ "field": { $size: 3 } }) - записи, где длинна массива field равна 3
db.movies.find({ "genre": { $all: ["fantasy", "horror"]} }) - записи, где есть жанры fantasy и horror без учета их порядка
db.user.find({ "hobbies": { $elemMatch: { "title": "sports", "frequancy": { $gt: 3 } } }}) - пользователи, которые занимаются спортом больше чем 3 раза
```

### Агрегация таблиц (отношения)
```js
db.news.aggregate([
   {
       $lookup: {
           from: "author",
           localField: "author",
           foreignField: "_id",
           as: "authorInfo"
       }
   } 
])
```

### Validation schema
создание валидации
```js
db.createCollection('posts', {
  validator: {
    $jsonSchema: {
      // json схема
    } 
  }
})
```

редактирование текущей валидации. Валидацию выкидывает предупреждения, вместо ошибок
```js
db.runCommand({
  collMod: 'posts',
  validator: {
    ...
  },
  validationAction: 'warn'
})
```

импорт данных из json файла
```
mongoimport home/original.json --authenticationDatabase admin -u tdmitriiv -p "537831" -d movieData -c movies --jsonArray --drop
home/original.json - путь к файлу
--authenticationDatabase admin - использование аутентификации
-u tdmitriiv -p "537831" - логин / пароль
-d movieData - база данных
-c movies - коллекция
--jsonArray - массив json
--drop - дробнуть коллекцию, если уже существует
```
