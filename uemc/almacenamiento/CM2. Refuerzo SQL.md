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

### Select

```
select * from catalogo;

select id_producto, pvp from catalogo;

select * from catalogo where id_producto = 3203;
```

### Update

```
update catalogo
set
descripcion = 'televisor economico, oferta',
pvp = 220
where
id_producto = 3202;

update catalogo
set
marca = 'Nintendo'
where
marca like 'Ni%';
```


### Delete

```
insert into catalogo
values
(3208, 'ASDSJAKL', '', '', '2021-01-01', 'fjlaskñjdfas', null,0);


delete from catalogo where precio_coste is null;
```

### Distinct, count, max, ...

```
select marca from catalogo;

select distinct marca from catalogo;


select count(1) from catalogo;

select count(1) as cuenta, max(pvp) as pvp_maximo, avg(precio_coste) as coste_medio from catalogo;
```

### Group by 

```
select marca, count(1) as num_productos, avg(precio_coste) as coste_medio
from catalogo
group by marca;
```

### Join 

Preparación:
```
CREATE TABLE IF NOT EXISTS ventas (
    id_venta INT AUTO_INCREMENT PRIMARY KEY
    , id_producto int
    , cliente varchar(100)
    , precio_venta int
    , fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

insert into ventas(id_producto, cliente, precio_venta)
values
(3201, 'Jose Perez', 695);

insert into ventas(id_producto, cliente, precio_venta)
values
(3203, 'Francisco Martin', 699)
, (3204, 'Francisco Martin', 600)
, (3203, 'Maria Guerras', 720)
, (3207, 'Maria Guerras', 300)
, (3207, 'Martin Gonzalez', 300)
, (3212, 'Martin Gonzalez', 560)
, (3204, 'Francisco Martin', 600)
, (3206, 'Maria Guerras', 500);
```
Joins:
```
select * from ventas v join catalogo c on v.id_producto = c.id_producto;

select * from ventas v left join catalogo c on v.id_producto = c.id_producto;

select * from ventas v right join catalogo c on v.id_producto = c.id_producto;

select * from ventas v cross join catalogo c;

select v.cliente, c.nombre, c.seccion, c.pvp, v.precio_venta
                , (c.pvp - v.precio_venta)/c.pvp as descuento
from ventas v 
join catalogo c on v.id_producto = c.id_producto;

select cliente, avg(descuento), count(distinct seccion)
from 
(
	select v.cliente, c.nombre, c.seccion, c.pvp, v.precio_venta
			, (c.pvp - v.precio_venta)/c.pvp as descuento
	from ventas v 
	join catalogo c on v.id_producto = c.id_producto
) d
group by cliente
```


### Primary Key

```
insert into catalogo
values
(3201, 'Lavavajillas', 'x', 'Electrodomesticos', '2019-12-02', 'blablabal', 345, 480);

delete from catalogo where marca = 'x'
```

```
ALTER TABLE catalogo ADD PRIMARY KEY (id_producto);
```

```
insert into catalogo
values
(3201, 'Lavavajillas', 'x', 'Electrodomesticos', '2019-12-02', 'blablabal', 345, 480);

delete from catalogo where marca = 'x'
```

### Foreign Key

```
alter table ventas
add foreign key (id_producto) references catalogo(id_producto);
```

```
insert into ventas(id_producto, cliente, precio_venta) values(3212, 'Martin Gonzalez', 560)
```
