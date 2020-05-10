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
> db.users.updateMany({ "hobbies.title": "Sports" }, { $set: { isSporty: true} }) - добавить новое поле по критерию
> db.users.updateOne({ _id: ObjectId("5eb820a35c5ae5d88acd9cbd") }, { $inc: { age: 1 } }) - инкремент значение
> db.users.updateOne({ _id: ObjectId("5eb820a35c5ae5d88acd9cbd") }, { $inc: { age: -1 } }) - декремент значения
> db.users.updateOne({ _id: ObjectId("5eb820a35c5ae5d88acd9cbd") }, { $min: { age: 30 } }) - установить значение в 30, если оно ниже текущего
> db.users.updateOne({ _id: ObjectId("5eb820a35c5ae5d88acd9cbd") }, { $max: { age: 30 } }) - установить значение в 30, если оно выше текущего
> db.users.updateOne({ _id: ObjectId("5eb820a35c5ae5d88acd9cbd") }, { $mul: { age: 1.1 } }) - умножить значение на 1.1
> db.users.updateOne({}, { $unset: { phone: "" } }) - удалить поля phone с null значениями
> db.users.updateMany({}, { $rename: { age: "totalAge" } }) - переименовать поле age в totalAge
> db.users.updateOne({ name: "Kate" }, $set: { age: 25 }, { upsert: true }) - обновляет пользователя Kate, если его нет, то создает
> db.users.updateMany({ hobbies: { $elemMatch: { title: "Sports", frequency: 4}} }, { $set: { "hobbies.$.newField": true } }) - добавить новое поле в объект из массива объектов hobbies, по критерию поиска
> db.users.updateMany({}, { $inc: { "hobbies.$[].frequency": -1 } }) - уменьшить значение frequency у всех элементов массива
```js
// обновить значение в массиве только для элементов, где hobbies.frequency больше 4. Добавляет новое поле goodFrequency
db.users.updateMany({ 
  "hobbies.frequency": { 
    $gt: 4
  } 
}, { 
  $set: { 
    "hobbies.$[el].goodFrequency": true 
  } 
}, { 
  arrayFilters: [
    { "el.frequency": { $gt: 4} }
  ] 
})

// добавить новый элемент в массив
db.users.updateOne({ name: "Kate" }, { $push: { hobbies: { title: "new hobbie", frequency: 1 } } })


// добавить 2 новых элемента в массив
// отсортировать по frequency
// и оставить только 2 элемента
db.users.updateOne({ name: "Kate" }, {
  $push: { 
    hobbies: {
      $each: [{ title: "new hobbie", frequency: 1 }, { title: "new hobbie 2", frequency: 2 }],
      $sort: {
        frequency: -1
      },
      $slice: 2
    }
  }
}) 

// удалить элемент из массива по критерию
db.users.updateOne({ name: "Kate" }, {
  $pull: {
    hobbies: {
      title: "new hobbie"
    }
  }
})

// удаляет последний элемент массива
db.users.updateOne({ name: "Kate" }, { $pop: { hobbies: 1 } })

// удаляет первый элемент массива
db.users.updateOne({ name: "Kate" }, { $pop: { hobbies: -1 } })

// добавляет элемент в массив, но дублируют элементы
db.users.updateOne({ name: "Kate" }, { $addToSet: { hobbies: { title: "new hobbie", frequency: 1 } } })
```

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

### сортировка курсора
```js
// 1 по возрастанию , -1 по убыванию
db.collectionName.find().sort({ field: 1, field2: 1}) 
// сортировка -> skip -> limit
db.collectionName.find().sort({ field: 1, field2: 1}).skip(100).limit(10)
```

## projection (выборка полей)
```js
// получить только поля name, genres, runtime, rating.average
db.movies.find({}, {name: 1, genres: 1, runtime: 1, "rating.average": 1}).pretty()
db.movies.find({ genres: "Drama" }, { "genres.$": 1}).pretty()

// получить первый жанр из массива
db.movies.find({ genres: {$all: ["Horror", "Drama"]}  }, { "genres.$": 1}).pretty()

// получить 2 жанра из массива [skip, length]
db.movies.find({ "rating.average": {$gt: 7}  }, { "genres": {$slice: 2}, name: 1  })
// получить 2 и 3 жанра из массива
db.movies.find({ "rating.average": {$gt: 7}  }, { "genres": {$slice: [1, 2]}, name: 1  })
```
