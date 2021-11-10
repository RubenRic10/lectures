# MongoDB


Este documento contiene el código de ejemplo utilizado en el bloque 2 del módulo:


### 3.2 Docker de Mongo
```
docker run -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=mypassword mongo:latest
```

## 4. Operaciones básicas

Conectando al shell

```
docker exec -it container_id mongosh -u root
```

### 4.5 Insert

```
use concesionario

db.coches.insertOne({marca: "Seat", modelo: "Ibiza", precio: 12453})

db.coches.insertMany([{marca: "Seat", modelo: "Leon", precio: 21433}, {modelo: "Megane", precio: 15200}])
```

### 4.6 Find

```
db.coches.find()

db.coches.find({marca: "Seat"})

db.coches.find({precio: {$gte: 15000}}).pretty()
 
db.coches.find({precio: {$gte: 15000}, modelo: "Megane"}).pretty()
 
db.coches.find({$where: function() {return this.marca == 'Seat' || this.precio > 1500;} })
 
db.coches.find({$where: "this.marca == 'Seat' || this.precio > 1500"})
  
db.coches.find({$or : [{precio: {$gte: 15000}}, {marca: "Seat"}]}, {_id:0, marca: 1})
 
```

### 4.7 Update

```
db.coches.insertOne({marca: 'Opel', modelo: 'Astra'}, {$set: {precio: 15000}})

db.coches.updateOne({marca: 'Opel', modelo: 'Astra'}, {$set: {precio: 60000}})
``

### 4.8 Upsert

```
db.coches.updateOne({marca: 'Toyota', modelo: 'Rav4'}, {$set: {precio: 42000}}, {upsert: true})
```

### 4.9 Delete

```
db.coches.deleteOne({marca: "Seat"})
```

### 4.10 Drop
```
db.coches.drop()
```

## 5. Indexado


```
db.coches.createIndex({marca: 1)

db.coches.getIndexes()

db.coches.dropIndex({marca: 1})
```

## Agregaciones

### 6.1 Count

```
db.coches.countDocuments()

db.coches.countDocuments({precio: {gt: 20000}})

db.coches.estimatedDocumentCount()

```

### 6.2 Distinct

```
db.coches.distinct('marca')

db.coches.distinct('marca', {precio: {$gt: 25000}})
```

### 6.3 Aggregate

```
curl -LJO https://raw.githubusercontent.com/acanalesg/lectures/main/data/olympics_medals.csv

docker cp olympics_medals.csv 76ac327f175a:/tmp/olympics_medals.csv

docker exec -it  76ac327f175a mongoimport --db masterbigdata --collection=medals 
             --type=csv --headerline --file=/tmp/olympics_medals.csv -u root -p mypassword --authenticationDatabase admin
```

$match

```
 db.medals.aggregate({$match: {"Athlete.Name": "Rafael Nadal Parera"}})
 
 db.medals.aggregate({$match: {"Athlete.Name": {$regex: "Nadal"}}})
```

$group
```
 db.medals.aggregate({$group: {"_id": "$Year", "medallas" : {$count: {}}}})
 
 db.medals.aggregate({$group: {"_id": {"año": "$Year", "ciudad": "$City"}, "medallas" : {$count: {}}}})
```

$out

```
db.medals.aggregate({$group: {"_id": {"año": "$Year", "ciudad": "$City"}, "medallas" : {$count: {}}}}
                    , {$out: {db: "masterbigdata", coll: "eventos"}})

```

$sort
```
db.eventos.aggregate({$sort: {"_id.año": -1, "_id.ciudad": 1}})
```

Pipeline completo
```
db.medals.aggregate({$match: {"Medal": "Gold"}}
                  , {$group: {"_id": {"atleta": "$Athlete.Name", "equipo": "$Team", "año": "$Year", "ciudad": "$City"}, "oros": {$count: {}}}}
                  , {$sort: {"oros": -1}}
                  , {$group: {"_id": {"año": "$_id.año", "ciudad": "$_id.ciudad"}, "atleta": {$first: "$_id.atleta"}, "oros": {$first: "$oros"}}}
                  , {$sort: {"_id.año": -1}}
                  , {$project: {"olimpiadas": "$_id", "atleta.nombre": "$atleta", "atleta.oros": "$oros", "_id": 0}}
                  , {$out: {"db": "masterbigdata", "coll": "mejores_atletas"}}
                  )
```
