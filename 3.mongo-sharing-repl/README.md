# Cпринт 4 (приложение сервиса "Мобильный мир")

## Задание 3. Репликация
Реализация репликации в MongoDB

  1. Запуск контейнеров:
    ``` docker-compose -f compose.yaml up -d ```

  2. Подключение к серверу конфигурации и инициализация
  ``` docker exec -it config-srv mongosh --port 27017 ```

  инициализация:
    ``` rs.initiate({ _id : "config-server", configsvr: true, members: [{ _id : 0, host : "config-srv:27017" }] }); ```
    ``` exit();```

  3. Подключение к шардам и инициализация:
  шард №1
  ``` docker exec -it shard-1-primary mongosh --port 27018 ```
  ``` rs.initiate({ _id : "shard-1", members: [{ _id: 0, host: "shard-1-primary:27018" }, { _id: 1, host: "shard-1-replica-1:27018" }, { _id: 2, host: "shard-1-replica-2:27018" } ]}); ```
   ``` exit() ```

  шард №2
  ``` docker exec -it shard-2-primary mongosh --port 27019 ```
  ``` rs.initiate({ _id : "shard-2", members: [{ _id: 0, host: "shard-2-primary:27019" }, { _id: 1, host: "shard-2-replica-1:27019" }, { _id: 2, host: "shard-2-replica-2:27019" }] }); ```
  ``` exit() ```
  
  4. Подлючение и инициализация роутера:
    ``` docker exec -it mongos-router mongosh --port 27020 ```
  ``` sh.addShard("shard-1/shard-1-primary:27018,shard-1-replica-1:27018,shard-1-replica-2:27018"); ```
  ``` sh.addShard("shard-2/shard-2-primary:27019,shard-2-replica-1:27019,shard-2-replica-2:27019"); ```
  
  5. Создание БД и документа:
    ``` docker exec -it mongos-router mongosh --port 27020 ```
  
  ``` sh.enableSharding("somedb");  ```
  ``` sh.shardCollection("somedb.helloDoc", { "name" : "hashed" } ); ```
  
  заполнение тестовыми данными:
  ``` use somedb; ```
  ``` for(var i = 0; i < 1000; i++) db.helloDoc.insert({age:i, name:"ly"+i}); ```
  ``` exit() ```

-----
ТЕСТИРОВАНИЕ 

1. Проверка работы сервиса осуществляется через swagger:
     отображение информации о развертывании монго в браузере ``` http://localhost:8082 ``` 
     [картинка хоста монги](/images/отображение%20информации%20о%20развертывании%20монго%20в%20браузере.png)
     
     запуск свагера ``` http://localhost:8082/docs ```
     [свагер](/images/свагер.png)

2. Выполнение запроса на получение списка документов в шардах (общее кол-во записей) ``` http://localhost:8082/docs/count/helloDoc ```
   [общее кол-во записей в БД](/images/запрос%20общего%20кол-ва%20записей%20в%20бд.png)

   [общая информация](/images/общая%20информация.png)
   Видно, что имеются два шарда (**shard-1**, **shard-2**), 
   в каждом шарде есть две реплики, 
   а общее количество записей **1000**.

3. Проверка реплицирования

последовательно для всех трех реплик первого шарда выполнить команды:

``` docker exec -it <имя контейнера> mongosh --port <порт контейнера> ```
``` use somedb ```
``` db.helloDoc.countDocuments() ```

   [запрос кол-ва документов в 1-ом шарде, но у второй реплики](/images/количество%20записей%20в%201-ом%20шарде%20с%20учетом%20репликации.png)
   [запрос кол-ва документов в 2-ом шарде, но у первой реплики](/images/количество%20записей%20в%202-ом%20шарде%20с%20учетом%20репликации.png)

## Вывод: реплицирование работает корректно