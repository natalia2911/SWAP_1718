
# Practica 3: Balanceo de carga  
  
**Realizado por : _Natalia María Mártir Moreno y Francisco Antonio Martinez Blanco_**  
  
En esta práctica configuraremos una red entre varias máquinas de forma que tengamos un balanceador que reparta la carga entre varios servidores finales.  
El problema a solucionar es la sobrecarga de los servidores. Se puede balancear cualquier protocolo, pero dado que esta asignatura se centra en las tecnologías web, balancearemos los servidores HTTP que tenemos configurados.  
De esta forma conseguiremos una infraestructura redundante y de alta disponibilidad.  
  
  
  
  
## Procedimiento  
  
**Paso previo :**  
  
	Tenemos dos máquinas:
	swap1 : con IP 192.168.1.11
	swap2 : con IP 192.168.1.10  
	
En este caso crearemos una máquina balanceadora para nginx y otra para haproxy con la misma IP (ya que no vamos a ejecutarlas las dos a la vez):
	
	swap3 : con IP 192.168.1.13
	Tambien creamos una de pruebas con IP:  
	swap4: con ip 192.168.1.14  
  
Hemos creado una nueva máquina, y como la hemos clonado apartir de otra, hemos modifica la IP, como hicimos en la P1, y como tiene configurado apache, tendremos que dejar el puerto 80 libre, por lo que :  
  
	Habilito el puerto 22 y 80, con:  
  
	ufw allow 20
	utf allow 80  
  
En el caso de que tengamos que tengamos instalado apache deberemos ponerlo en pausa:  
  
	service apache2 stop



**Objetivo 1: Configurar una máquina e instalarle el nginx como balanceador de carga**  
  
Tenemos que instalar **Nginx** con  
  
	apt-get install nginx  
  
  
Una vez instalado procedemos a modificar el archivo de configuración:  
  
	/etc/nginx/conf.d/default.conf

Una vez configurado, lo iniciamos tal que:  
  
	sudo systemctl start nginx  
  
El archivo debe quedar de la siguiente forma:  
  
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P3/Imagenes/3.1.png)  
  
Tras la configuración tenemos que deshabilitar la opción de que funcione como servidor web, para ello hay que comentar la línea  
  
	#include /etc/nginx/sites-enabled/*  
	dentro del archivo: /etc/nginx/nginx.conf.  
  
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P3/Imagenes/3.2.png)  
  
Por último reiniciamos el servicio:  
  
service nginx restarty desde una máquina externa y con la herramienta curl probamos que funcione bien:  
  
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P3/Imagenes/3.6.png)  
  
Tenemos que tener en cuenta, que si cogemos las maquinas anteriores tenemos que dejar que 'swap2' deje de actualizar /var/www/ de 'swap1' (Como lo hicimos en prácticas anteriores con crontab, si seguimos usando esas máquinas, puede que esta opción la tengamos todavia activa)  
  
Para configurar Nginx con prioridad hay que modificar el archivo de configuración **/etc/nginx/conf.d/default.conf** , quedando así:  
  
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P3/Imagenes/3.4.png)  
  
**Objetivo 2: Configurar una máquina e instalarle el haproxy como balanceador de carga**  
  
Primero hemos creado una nueva máquina, que solo se dedique a ser balanceador con haproxyTenemos que instalar Haproxy: **apt-get install haproxy**  
  
Tras esto debemos configurar el archivo: **/etc/haproxy/haproxy.cfg** quedando de la siguiente forma  
  
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P3/Imagenes/3.5.png)  
  
  
La configuración que deberá usar este balanceador es la de Round-Robin, cada vez que mandamos una petición a la máquina balanceadora, esta manda una al servidor 1, y otra al servidor 2  
  
Después iniciamos el servicio con el archivo de configuración que hemos creado mediante **_sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg_**, cargando el archivo de configuración que hemos creado.  
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P3/Imagenes/3.6.png)  
  
En ese caso también podemos modificar con weight la configuración.  
  
###**Objetivo 3: Sometiendo a la granja web a alta carga**  
  
Con la herramienta de benchmarking Apache Benchmark (ab) someteremos a los diferentes servidores que actúan como balanceadores a una carga alta. El algoritmo de balanceo será Round Robin.  
  
En concreto se ha ejecutado **ab -n 10000 -c 10 http://ipMaquinaBalanceadora/index.html**, esto lo ejecutaremos desde una máquina externa  
  
-n indica el número de peticiones-c indica que las peticiones se harán concurrentemente de 10, en 10  
  
Creo un fichero llamado archivo.txt que guarde la salida de ab.  
  
(Esto lo tienes que hacer, primero de una maquina exterior a nginx, y luego desde la maquina exterior, a haproxy)  
  
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P3/Imagenes/3.7.PNG)  
  
  
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P3/Imagenes/3.8.png)
