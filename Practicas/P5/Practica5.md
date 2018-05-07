# Practica 5: Replicación de bases de datos MySQL
  
**Realizado por : _Natalia María Mártir Moreno y Francisco Antonio Martínez Blanco_**

## Objetivo 1: Crear una BD.

Para ello usaremos mysql. Siguiendo estas ordenes:
	
Entrar a mysql:

	mysql -uroot -p

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.1.png)  

Creamos la base de datos practica5, y entraremos en ella:
	
		> create database practica5;
		> use practica5;
		
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.2.png) 

Ahora, dentro de practica 5 vamos a crear una tabla llamada parte1, que pueda almacenar nombre, apellido1 y apellido2:

		> create table parte1(nombre varchar(100),apellido1 varchar(100), apellido2 varchar(100));
		
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.3.png) 

Si le pedimos que nos muestre las tablas veremos que ahi se encuentra parte1

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.4.png) 

Ahora añadimos a la tabla nuestros nombres con: 

		> insert into parte1...
		
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.5.png) 

Ahora diremos que nos muestre toda nuestra tabla parte1

		
		> select * from parte1;
		
Y vemos que se ha creado perfectamente sin ningun error.

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.6.png) 

## Objetivo 2: Realizar la copia de seguridad de la Base de Datos usando mysqldump y copiarla en la maquina secundaria.

Primero en la maquina principal (swap1) debemos entrar en mysql y escribir lo siguiente: 
		
		> FLUSH TABLES WITH READ LOCK;
		
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.7.png) 

Ahora es cuando usaremos mysqldump para guardar la Base de Datos ejecutando:

		mysql practica5 -u root -p > /tmp/practica5.sql
		
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.8.png) 

Desbloqueamos las tablas que hemos bloqueado anteriormente, desde mysql.

		> UNLOCK TABLES
		
Y ahora desde la maquina 2 copiaremos la base de datos con scp

		scp swap1@192.168.1.11:/tmp/practica5.sql /tmp/
		
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.9.png) 

## Objetivo 3: Restaurar dicha copia de seguridad en la segunda maquina, de forma que ambas máquinas contengan la misma BD.

Desde la máquina 2, donde tenemos guardado en tmp la base de datos, la vamos a restaurar en mysql.

Entramos a mysql y creamos otra base de datos igual a la anterior

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.10.png) 

Ahora con
		
		mysql -u root -p practica5 < /tmp/practica5.sql
		
copiamos los datos de la base de datos guardada en la base de datos recien creada.

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.11.png) 

Y ahora comprobamos que todo este en orden

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.12.png) 

Como podemos ver tenemos en ambas maquinas la misma base de datos, hemos realizado correctamente la restauracion de la base de datos.

## Objetivo 4: Realizar la cofiguracion maestro-esclavo de los servidores MySQL para que la replicacion de datos se realice automaticamente.

Debemos de modificar el archivo /etc/mysql/mysql.conf.d/mysqld.confd de las dos maquinas, la esclava y la maestra.

En la maestra tenemos que comentar la linea que pone

		#bind-adrees 127.0.0.1
		
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.13.png) 

Ahora en ambas debemos de descomentar la linea que pone server-id y dejarlo tal que asi

		Maestra --> server-id=1
		Esclava --> server-id=2
y descomentar en ambas la linea 
		
		log_bin = /var/log/mysql/mysql-bin.log
		
Quedando tal que asi

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.14.png) 

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.15.png) 

Despues de guardar los archivos tenemos que reiniciar mysql.

Ahora en la maquina principal, tenemos que añadir un usuario y darle privilegios para poder replicar datos.

Desde mysql ejecutamos lo siguente

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.16.png) 

Y con 

		SHOW MASTER STATUS;
		
para comprobar

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P5/Imagenes/Foto5.17.png) 

Ahora tenemos que modificar la maquina secundaria: