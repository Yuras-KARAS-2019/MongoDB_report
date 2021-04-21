# Commands

**MongoDB Compass** – це прикладна програма з графічним інтерфейсом, яка дозволяє не лише спостерігати за змінами в базах даних, 
а й вносити зміни в них: створювати, редагувати, видаляти як складові баз даних так і самі бази.  

## Для створення бази даних достатньо використати команди:  
База даних з назвою:  
```js
use database_name
 ```
Колекція з назвою:   
```js
db.createCollection("collection_name")
``` 

### Для модифікації колекцій можна використовувати:  
Додати поле до колекції:
```js
db.insertOne({some fields})
```  
Додати множинні поля до колекції:
```js
db.insertMany{[{}, {}, {}]}
```  

### Для фільтрації об’єктів можна використовувати такі команди:  
Всі поля з колекції:  
```js
db.collection_name.find()
```  
Всі об’єкти з колекції «collection.name».  
```js
db.collection_name.find().limit(number)
```  
Всі об’єкти з колекції «collection.name» без поля id.  
```js
db.collection_name.find({}, {_id: 0})
```  
Сортування лімітованого результату виводу по "age" та "name":  
```js
db.collection_name.find().sort({age: 1, name: 1}).limit(number)
```  
Об’єкти, які мають поле "age" = 20 або "status" = "is studying".  
```js
db.collection_name.find({$or: [{age: 20}, {status: "is studying"}]})
```  
Об’єкти, які мають поле "age" < 40 або "status" = "is studying".  
```js
db.collection_name.find($or: [{age: {$lt: 40}}, {status: "is studying"}])
```  
Знайти об’єкти в яких присутнє ім’я “name_1” або  “name_2” та вивести їх без поля ідентифікатора.  
```js
db.collection_name.find({name: {$in: ["name_1", "name_2"]}, {_id: 0}})
```  

### На місці $lt могли стояти наступні модифікатори:  
```js
$lte (less than equal: "<=").  
$gt (greater than: ">").  
$gte (greater than equal: ">=").  
$eq (equal: "==").  
$ne (not equal: "!=").
```  

##### На місці $in міг стояти модифікатор $nin (не присутнє ім’я):  
Всі об’єкти які мають поле birthday.  
```js
db.collection_name.find({birthday: {$exists: true}}, {_id: 0})
```  
Об’єкти, що містять у полі friends елементи, які починаються з “Z”(код символу) або молодших символів.  
```js
db.collection_name.find({friends: {$elemMatch: {$lte: "Z"}} }, {_id: 0})
```  

### Для проведення операцій над об’єктами (модифікація, видалення) можна використовувати наступні команди:  
Знаходить ***перший!*** об’єкт з age: 21 і модифікує поля course: 3, status: “is studying”:  
```js
db.collection_name.updateOne({age: 21}, {$set: {course: 3, status: "is studying"}})
```  
Знаходить ***всі!***
об’єкти з age: 21 і модифікує поля course: 3, status: “is studying”.  
```js
db.collection_name.updateMany({age: 21}, {$set: {course: 3, status: "is studying"}})
```  
Замінити перший об’єкт (в базі) із age: 23, якому надають поля: name: “Harry”, “last name”: ”Potter”, course: 6, “special skills”: “magic”, age: 23.  
```js
db.collection_name.replaceOne({age: 23}, {name: “Harry”, “last name”: ”Potter”, course: 6, “special skills”: “magic”, age: 23})
```  
Видалити перший об’єкт у базі даних в якому вік входить в задані рамки, а саме: 23 < age < 35.  
```js
db.collection_name.deleteOne({age: {$gt: 23}, age: {$lt: 35}})
```  

### Для проведення множини операцій над об’єктами (модифікація, видалення) можна використовувати наступні команди:  
```js
db.collection_name.bulkWrite([{insertOne: {“document”: {name: "David", age: 25, birthday: "20.03.1999"}}},
                              {deleteOne: {filter: {name: "Yura"}}}, 
                              {updateOne: {filter: {name: "Roman"}, update: {$set: {name: "Romashka"}}}},
                              {replaceOne: {filter: {name: "Andrew"}}}])
```  

### Для пошуку записів по певним полям використовують такі команди:
```js
db.collection_name.createIndex({some field 1: "some text", some field 2: "some text"}, some field n: "some text")
```  
Пошук об'єктів в полях яких містяться слова із "some text". Після пошуку об'єкти будуть виводитись в порядку спадання релевантності до пошукового слова. 
Наприклад, "some text" = "жителі Києва", тоді виведуться всі об'єкти, які в "some field" мають слово "жителі" або - "Києва", 
або - "жителі Києва" або подібні слова в аналогії з роботою пошукової системи "Google".  
```js
db.collection_name.find({$text: {$search: "some text"}}, {$score: {$meta: "textScore"}}).sort({score: {$meta: "textScore"}})
```  

***Корисні команди:***  
Загальна кількість об'єктів з полем **age = 18** в поточній базі.  
```js
db.collection_name.count({age: 18})
```   
Отримати список із унікальних значень полів **age** в базі даних.  
```js
db.collection_name.distinct("age")
```   
Об'єднати всі об'єкти з полем **name = "Elena"** в один об'єкт по полю **name**, а на місці **age** записати суму поля **age** всіх вибраних об'єктів.  
```js
db.collection_name.aggregate([{$match: {name: "Elena"}}, {$group: {_id: "$name", age: {$sum: "$age"}}}])
```   
