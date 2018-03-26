# Practica 2: Práctica 2. Clonar la información de un sitio web

**Realizado por : _Natalia María Mártir Moreno_**

Los objetivos concretos de esta segunda práctica son:
	
- aprender a copiar archivos mediante ssh
-	clonar contenido entre máquinas
-	configurar el ssh para acceder a máquinas remotas sin contraseña 
- establecer tareas en cron



## Procedimiento

**Objetivo 1 : APRENDER A COPIAR ARCHIVOS MEDIANTE SSH:** 

Tenemos dos máquinas:
	
	swap1 : con IP 192.168.1.11
	swap2 : con IP 192.168.1.10

En este caso, y como detalle previo, es hacer que el usuario sea el dueño de la carpeta donde residen los archivos que hay en el espacio web(en ambas máquinas):

	sudo chown swap1:swap1 -R /var/www
	sudo chown swap2:swap2 -R /var/www
		
Para probar el funcionamiento de la copia de archivos por ssh, como podemos ver en la imagen creamos un nuevo archivo en "swap2" llamado objetivo1.html y al ejecutar rsync en "swap1, también se crea en este.

Ejecutando este comando :
		
	rsync -avz -e ssh swap2@192.168.1.10:/var/www/ /var/www/
	
![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P2/Imagenes/objetivo1.png)
	
Como en este caso solo copiaríamos archivos individuales o más pequeños 
lo haríamos con tar, en este caso lo haríamos con un solo fichero 'hola.html':
	
	tar czf - hola.html | ssh swap1@192.168.1.11 'cat > ~/tar.tgz' 
	


**Objetivo 2 : CLONADO DE CARPETAS ENTRE MÁQUINAS
:** 

En la siguiente imagen podemos comprobar que sucede lo mismo con las carpetas, pero en este caso hemos creado la carpeta en "swap1" y la hemos clonado a "swap2"

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P2/Imagenes/objetivo2.png) 

**Objetivo 3 : CONFIGURACIÓN DE SSH PARA ACCEDER SIN QUE SOLICITE LA CONTRASEÑA:** 

Como se ha explicado en la sesión de prácticas, la clave SSH se crea en "swap2" y se copia a "swap1".

Por lo que en swap2 hemos ejecutado:
		
	ssh-keygen -b 4096 -t rsa
	ssh-copy-id swap1@192.168.1.11

Como podemos ver en la siguiente imagen, podemos acceder mediante ssh sin contraseña, y para comprobarlo hemos creado una nueva carpeta, llamada "SIN CONTRASEÑA"

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P2/Imagenes/objetivo3.png)


**Objetivo 4 : PROGRAMAR TAREAS CON CRONTAB:** 

Aquí lo que tenemos que hacer es establecer una tarea en cron que se ejecute cada hora para mantener actualizado el contenido del directorio /var/www entre las dos máquinas.

En la máquina 2 ó "swap2" hemos modificado el archivo :
	
	/etc/crontab
y hemos añadido esta linea:
	
	* * * * *  root rsync -avz -e ssh swap1@192.168.1.11:/var/www/ /var/www/

Como anteriormente permitimos que se hiciera ssh sin contraseña, esto lo hará automaticamente.

![](https://github.com/natalia2911/SWAP_1718/blob/master/Practicas/P2/Imagenes/objetivo4.png) 