
# Practica 4: Asegurar la granja web  
  
**Realizado por : _Natalia María Mártir Moreno y Francisco Antonio Martínez Blanco_**
  
## Objetivo 1: Instalar un certificado SSL autofirmado para configurar el acceso HTTPS a los servidores.

Para generar un certificado autofirmado SSL en Ubuntu server debemos activar el [módulo SSL de Apache](https://httpd.apache.org/docs/2.4/mod/mod_ssl.html) mediante la orden:

	 a2enmod ssl
	 service apache2 restart.
	 

Acontinuación procedemos a especificar la ruta a los certificados de configuración:
	
	openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout 
	/etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt

Aquí te pedirán una serie de datos que nosotros hemos rellenado de esta forma:

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.1.jpg) 

Una vez generados los certificados debemos especificar dónde se encuentra el archivo del certificado y el archivo con la clave `ssl` , en el archivo de configuración `/etc/apache2/sites-avilable/default-ssl.conf` mediante las siguientes líneas:

	SSLCertificateFile /etc/apache2/ssl/apache.crt
	SSLCertificateKeyFile /etc/apache2/ssl/apache.key

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.2.jpg) 

Mediante ` a2ensite default-ssl` activamos el default que hemos configurado con la ubicación de los certificados.

Nos pide que hagamos ` service apache2 reload` 

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.3.jpg) 

Ahora transpasamos lo realizado en el servidor al resto de servidores y al balanceador mediante la herramienta scp:

	sudo scp swap1@192.168.1.11:/etc/apache2/ssl/apache.key /etc/apache2/ssl
	
	sudo scp swap1@192.168.1.11:/etc/apache2/ssl/crt /etc/apache2/ssl

Una vez hecho esto, tenemos que configurar nginx para aceptar y balancear correctamente tanto el tráfico HTTP como el HTTPS:

Para ello añadimos al archivo /etc/nginx/conf.d/default.conf:

    listen 443 ssl;
    ssl_certificate /etc/apache2/ssl/apache.crt;
    ssl_certificate_key /etc/apache2/ssl/apache.key;
    
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.4.jpg) 

Realizamos las peticiones mediante:
	
	curl -k https://192.168.1.13/hola.html

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.5.png) 

## Objetivo 2: Configuración del cortafuegos

Un cortafuegos (firewall) es una parte de un sistema o una red que está diseñada para bloquear el acceso no autorizado, permitiendo al mismo tiempo comunicaciones autorizadas.

Todos los paquetes TCP/IP que entran o salen de nuestra granja web tienen que pasar por el cortafuegos, que los examina y bloquea aquellos que no cumplen los criterios de seguridad que establecemos. Estos criterios se configuran mediante un conjunto de reglas:

    Rango de puertos
    Direcciones IP
    Rangos de IP
    Tráfico TCP o UDP
    Etc

**Configuración mediante iptables**

Iptables es una herramienta de cortafuegos, de espacio de usuario, con la que el superusuario define reglas de filtrado de paquetes, de traducción de direcciones de red y mantiene registros de log. Esta herramienta está construida sobre Netfilter, una parte del núcleo de Linux que permite interceptar y manipular paquetes.

Para no tener que introducir cada una de las ordenes, hacemos un script:
   
  ![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.6.png) 
 
 Ejecutamos el script, antes de todo hay que darle permisos con:
	
	chmod +x script.sh
	./script.sh
 
 Comprobamos con el cortafuegos con:
 
   
  	iptables -L -n -v
  	
  	
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.7.png) 
  	
  	
 Ahora solo tenemos que decirle al sistema que lo ejecute cada vez que se inicie:
 
     mv /home/ubuntus/scriptiptables.sh /etc/init.d/
    update-rc.d scriptiptables.sh defaults
    
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.8.png) 
 
 
  Al hacer update-rec.d nos da un error, para solucionarlo añadimos al script lo siguiente:
  
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.9.png) 
Volvemos a ejecutar
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.10.png) 
Y ahora no nos dara ningun error.

 Reiniciamos la maquina y volvemos a comprobar
 ![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P4/Imagenes/P4.11.png) 