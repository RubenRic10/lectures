# MongoDB


Este documento contiene el código de ejemplo utilizado en el bloque 2 del módulo:


### 6.3 Agregaciones

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
db.medals.aggregate({$group: {"_id": {"año": "$Year", "ciudad": "$City"}, "medallas" : {$count: {}}}}, {$out: {db: "masterbigdata", coll: "eventos"}})

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
