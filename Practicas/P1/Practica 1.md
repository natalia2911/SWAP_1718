
# Practica 1: Preparación de las herramientas

**Realizado por : _Natalia María Mártir Moreno_**

Procedemos a instalar las dos máquinas virtuales con Ubuntu Server, en las que procederemos a realizar una instalación completa de Apache + PHP + MySQL



## Procedimiento

**Paso previo :** 

Tenemos dos máquinas:
	
	swap1 : con IP 192.168.1.11
	swap2 : con IP 192.168.1.10

En el archivo de configuración **_/etc/network/interfaces_** creamos una red interna con los siguientes parametros.

![enter image description here](https://lh3.googleusercontent.com/RXrD7YAWk2J0b63SZJFDB6n3L1hUIaF6aOBU9XQwQM-PHzNa-5pFNRVZu95qljh_IK8R9nXFI5Y)

![enter image description here](https://lh3.googleusercontent.com/KCMxyaTu-0FSLby5ELCJykKHhM5Uw9fSsISolCPHPXDD4IYVusUqFYICFHumZuiMzGNOlbWvRMA)

Tambien podemos ver que se pueden hacer ping entre ellas:

![](https://lh3.googleusercontent.com/lHDQgM6436bhVDxRsr1XBQUPq_g6CeIIJLCkhU52GzAdzcOWyvGRMswbrX69rEiRHOs6hivFz5Q)

![](https://lh3.googleusercontent.com/TMEH3o8WSC2ePhvVUNGgz50WUHzZWzp6yXEzbzL58R0KygX4jj39krHxQ2leIuh550UvxdVTwYI)

**Paso 1 :** empezamos instalando, y en el momento en el que nos aparezca la seleccion de programas debemos seleccionar instalar *OpenSSH server*, y *LAMP server* para evitar tener que instalarlo posteriormente

**Paso 2 :** una vez terminada la instalacion procedemos a instalar apache (Por si antes no lo instalamos) 
	
	$ sudo apt-get install apache2 mysql-server mysql-client

Comprobaremos su versión :
	
	$ apache2 -v

Comprobamos si esta en ejecución :

	$ ps aux | grep apache
	
**Paso 3 :** procedemos a instalar cURL para comprobar que el servidor web está activo.
	
	$ sudo apt-get install curl

Para comprobar que esto funciona, usamos un editor de texto en nuestro caso vi, y en el directorio /var/www/html creamos un archivo llamado hola.html donde ponemos esto :
	
	<HTML>
	<BODY>
	Esto funciona :) 
	</BODY>
	</HTML>
	
Mediante la otra maquina probaremos que podemos acceder a el:
	
	$ curl http://direccionIPdelservidor/hola.html
	
En nuestro caso será de una maquina a otra y viceversa.

![](https://lh3.googleusercontent.com/9wk-7jorm2N_TPJNDTxtoEzuvcNLShu2ZHG5K_7kWCU_skQ49qzCYuCOEOn7GDURlm2LG4yTtjU)

![](https://lh3.googleusercontent.com/_cKVBGDdbsPCHlx8YnVihw7x4LIWbNWKkjRQs_UrIyeYEf_xluuyPRT36BEvRnxzJLzD2zKglYs)


![](https://lh3.googleusercontent.com/ol5iA9LnoqfplYNTOSefXny9Y53NiAuVRNZor55F86x5Mz_320V06uJikkQPdXZHeo0n4DIZCzk)


**Paso 4 :** aqui vemos que se puede hacer ssh entre ellas, creamos algun archivo:

![](https://lh3.googleusercontent.com/rlopRy7sKhf1M_ETz-aLFdIOiRcEZSFQN498xstOrVARYxz6bMCSmcOviy1VnD7sV0yUDaqW0mQ)

![](https://lh3.googleusercontent.com/2ZB5ZooBtReQFeFVsUrFv-3NjM_xyu3cw_sx9A6H7LrIdUy-TDrq1iqQMYHq_9xs5lMYzXxF89k)

![](https://lh3.googleusercontent.com/U89sSrAbazVlJtmykJzywQSvYrveH1rEU9fq_kbf-XOjbxZ9yDO1-VYSFz3Fs8LN5irS47W8LcM)

![](https://lh3.googleusercontent.com/zgi3-vYZfOXyYDjSbHMwxEPbKEirDKCBb9WCtrnA-UgTW9Ru9iFpJGfyIJbp0URXYeMzkwAppj4)
