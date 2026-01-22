# Cпринт 4 (приложение сервиса "Мобильный мир")

## Задание 1. Планирование
Реализация шардирования, репликации и кеширования.

1. Использование шардирования в MongoDB. Двух шардов будет достаточно.

[Cхема](./schemas/Task1/schema1_sharding.drawio)
[Изображение](./schemas/Task1/images/schema1_sharding.png)

- Вместо одной ноды MongoDB создается шардированный кластер.
- Добавляются сервер конфигурации (configSrv), который хранит метаданные о том, на каком шарде какие данные лежат.
- Добавляется маршрутизатор запросов (mongos). Теперь приложение pymongo-api общается не напрямую с базой данных, а с mongos. Маршрутизатор обращается к  configSrv, чтобы понять, на какой шард отправить запрос.
- Данные распределяются по двум шардам: Shard1 и Shard2.

2. Реализация репликации MongoDB для повышения отказоустойчивости.

[Cхема](./schemas/Task1/schema2_sharding_with_replications.drawio)
[Изображение](./schemas/Task1/images/schema2_sharding_with_replications.png)

- Каждый шард теперь преобразуется в набор реплик (replica set).
- Для каждого шарда создается один главный узел (Primary) и два вторичных узла (Secondary). Запись данных всегда производится в узел Primary, а затем асинхронно копируется во все Secondary-узлы. Чтение данных с Secondary-узлов.

3. Реализация кеширования.

[Cхема](./schemas/Task1/schema3_sharding_with_replications_add_caching.drawio)
[Изображение](./schemas/Task1/images/schema3_sharding_with_replications_add_caching.png)

- В архитектуру системы добавляется in-memory база данных Redis.
- Приложение pymongo-api перед обращением к mongos сначала проверяет, нет ли нужных данных в кеше (Redis). Если данные найдены, то они берутся из кеша, если не найдены, то читаются из БД и кешируются в Redis.

## Задание 2. Шардирование

[Шардирование описано в readme проекта 2.mongo-sharding.](/2.mongo-sharding/README.md)
В файле представлены детальные инструкции по развертыванию сервисов.

## Задание 3. Репликация

[Репликация описана в readme проекта 3.mongo-sharding-repl.](/3.mongo-sharing-repl/README.md)
В файле представлены детальные инструкции по развертыванию сервисов.

## Задание 4. Кеширование

[Кеширование описано в readme проекта 4.mongo-sharding-repl-cache.](/4.sharding-repl-cache/)
В файле представлены детальные инструкции по развертыванию сервисов.

## Задание 5. Схема сервисов с учетом api gateway и service discovery

[Схема проекта](/schemas/Task5/schema5_api_gateway_with_service_discovery.drawio)
[Схема проекта на картинке](/schemas/Task5/images/schema5_api_gateway_with_service_discovery.png)

## Задание 6. Схема сервисов с учетом CDN

[Схема проекта](/schemas/Task6/schema6_cdn.drawio)
[Схема проекта на картинке](/schemas/Task6/images/schema6_cdn.png)

## Задание 7. Проектирование схем коллекций для шардирования данных

[Представление коллекций в MongoDB и их операции, и связи в C4](/schemas/Task7/components_for_sharded_cluster.puml)
[Архитектурный документ стратегии шардирования](/schemas/Task7/ADR.md)

## Задание 8. Выявление и устранение «горячих» шардов

[Система мониторинга для шардов и механизмы автоматического перераспределения данных](/schemas/Task8/ADR.md)

## Задание 9. Настройка чтения с реплик и консистентность

[Таблица с операциями чтения для primary или secondary реплик](/schemas/Task9/ADR.md)


