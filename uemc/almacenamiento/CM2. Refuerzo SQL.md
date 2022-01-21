# Refuerzo de SQL con MySQL

Este documento contiene el código utilizado en la sesión de refuerzo de SQL

### Arrancando docker de MySQL

Primera vez (cambiad el name y el pwd)
```
docker run --name arturo-mysql -p 13306:3306 -e MYSQL_ROOT_PASSWORD=pwd -d mysql:latest
```

Siguientes
```
docker start arturo-mysql
```

### Conectando a mongo por línea de comandos

```
docker exec -it abc-mysql bash

mysql -u root -p
```

o todo de una vez

```
docker exec -it abc-mysql mysql -u root -p

show databases

```

A partir de aquí es recomendable pasar a MySQL Workbench CE (el servidor mySQL estará escuchando en localhost 13306)


### Creación de la base de datos

```
CREATE database tienda;

use tienda;
```


### Creación de tabla

```
CREATE TABLE IF NOT EXISTS catalogo (
        id_producto int
    , nombre VARCHAR(100)
    , marca VARCHAR(100)
    , seccion VARCHAR(100)
    , fecha_entrada DATE
    , descripcion VARCHAR(1000)
    , precio_coste int
    , pvp int
);

show tables;

show columns from catalogo;
```

### Insert

```
insert into catalogo
values
(3201, 'Television Panasonic 40"', 'Panasonic', 'Imagen/Sonido', '2019-12-02', 'Fantastico televisor', 345, 480),
(3202, 'Television Panasonic 38"', 'Panasonic', 'Imagen/Sonido', '2019-01-02', 'televisor blablabla', 200, 310),
(3203, 'Television Sony 45"', 'Sony', 'Imagen/Sonido', '2020-03-02', 'alta calidad de imagen', 450, 800),
(3204, 'Frigorifico combi Beko', 'Beko', 'Electrodomesticos', '2020-03-02', 'bajo consumo', 500, 720) ,
(3205, 'Lavadora ', 'Panasonic', 'Electrodomesticos', '2018-03-02', 'alta potencia', 390, 505) ,
(3206, 'Plastation 5 ', 'Sony', 'Videoconsola', '2021-03-02', 'la mas avanzada de Sony', 390, 500) ,
(3207, 'Nintendo  Switch', 'Ninento', 'Videoconsola', '2021-05-02', 'portatil', 190, 300) ,
(3220, 'Nintendo DS', 'Ninento', 'Videoconsola', '2016-05-02', 'portatil doble pantalla', 100, 150) ;
```
