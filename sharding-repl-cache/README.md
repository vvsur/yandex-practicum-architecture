# pymongo-api

## Как запустить

### Как избежать ошибок переконфигурирования предыдущего шага

К сожалению, совсем нет времени разбираться, как аккуратно перенастроить то, что сделано в предыдущем задании. В идеале конечно применять rs.reconfig() для нод mongodb, а еще лучше rs.add(). Это касается инициализации shard1 и shard2. С остальным проблем не испытывал, но именно с переконфигом этих двух наборов реплик вечно какие-то несостыковки. Возможно недоочистил что-то.

Предлагаю поступить проще и перед запуском этой части решения удалить все контейнеры и volumes предыдущего задания. В какой-то степени это даже более корректно с точки зрения CI/CD, т.к. является запуском на чистой среде.

### Запуск приложения и БД
Запускаем mongodb и приложение

```shell
docker compose up -d
```

### Инициализация сервера конфигурации

```shell
	docker exec -it configSrv mongosh --port 27017
	
	rs.initiate(
	  {
		_id : "config_server",
		   configsvr: true,
		members: [
		  { _id : 0, host : "configSrv:27017" }
		]
	  }
	);

	exit();
```

### Инициализация shard1

```shell
	docker exec -it shard1 mongosh --port 27018
	
	rs.initiate({_id: "shard1", members: [
		{_id: 0, host: "shard1:27018"},
		{_id: 1, host: "shard1-r2:27018"},
		{_id: 2, host: "shard1-r3:27018"}
	]});
	
	exit();
```

### Инициализация shard2

```shell
	docker exec -it shard2 mongosh --port 27019
	
	rs.initiate({_id: "shard2", members: [
		{_id: 0, host: "shard2:27019"},
		{_id: 1, host: "shard2-r2:27019"},
		{_id: 2, host: "shard2-r3:27019"}
	]});
	
	exit();
```

### Инициализация и наполнение роутера тестовыми данными

```shell

	docker exec -it mongos_router mongosh --port 27020

	sh.addShard( "shard1/shard1:27018");
	sh.addShard( "shard2/shard2:27019");

	sh.enableSharding("somedb");
	sh.shardCollection("somedb.helloDoc", { "name" : "hashed" } )

	use somedb

	for(var i = 0; i < 1000; i++) db.helloDoc.insert({age:i, name:"ly"+i})

	db.helloDoc.countDocuments() 
	exit(); 
	
```

## Как проверить

Откройте в браузере http://localhost:8080