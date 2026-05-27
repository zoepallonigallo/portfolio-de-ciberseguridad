write-up detallado de la resolución de una máquina virtual vulnerable (RetoCTF2019-2020), documentando el proceso completo desde la fase de reconocimiento hasta la escalada de privilegios, logrando capturar las 5 flags ocultas en el sistema

-Reconocimiento (Reconnaissance);
Una vez que la máquina atacante (Kali Linux) y la máquina víctima estaban en la misma red, el primer paso fue identificar la IP del objetivo y los servicios expuestos.
Se realizó un escaneo con nmap hacia la IP objetivo (192.168.1.139) revelando dos puertos abiertos:  Puerto 22: SSH (OpenSSH 7.2p2).  Puerto 80: HTTP (Apache httpd 2.4.18).}



-Enumeración Web y Flag 1Sabiendo que había un servidor web en el puerto 80, utilizamos dirb para aplicar fuerza bruta y descubrir directorios ocultos utilizando el diccionario por defecto de Kali 
(/usr/share/wordlists/dirb/common.txt).  El escaneo reveló varios directorios interesantes, destacando /admin_area/ y /flag/.
Al navegar directamente a http://192.168.1.139/flag/, encontramos la primera bandera:  
FLAG 1: {8734509128730458630012095} 

Análisis de Código Fuente y Flag 2
Posteriormente, exploramos el directorio /admin_area/, el cual presentaba un formulario de inicio de sesión.
Al inspeccionar el código fuente (HTML) de la página web, encontramos un comentario oculto por el desarrollador que contenía la segunda bandera y credenciales de acceso:  
FLAG 2: {7412574125871236547895214} Credenciales obtenidas: admin / 3v11_H@ck3r 

Explotación (Reverse Shell) y Flag 3
Utilizando las credenciales descubiertas, accedimos a una "Plataforma de subida de ficheros".
Esto nos dio un vector de ataque claro para subir una Reverse Shell en PHP.  
Se utilizó el conocido script php-reverse-shell de PentestMonkey, configurándolo con la IP de nuestra máquina atacante (192.168.1.138) y el puerto de escucha 1234. 
Antes de subir el archivo, pusimos el puerto en escucha usando Netcat: nc -lvnp 1234.  Subimos el archivo y lo ejecutamos navegando a su ruta en el servidor web.
La conexión se estableció con éxito. 
Para estabilizar la terminal (TTY), importamos bash usando Python: python3 -c 'import pty; pty.spawn("/bin/bash")'. 
Navegando por los archivos del servidor web (/var/www/html/), encontramos y leímos el archivo hint.txt, el cual contenía la tercera bandera: 
FLAG 3: {7645110034526579012345670} 

Movimiento Lateral y Flag 4
Para buscar información sensible de otros usuarios, utilizamos el comando find buscando archivos que pertenecieran al usuario demouser, 
redirigiendo los errores a /dev/null para limpiar la salida:  Bashfind / -user demouser 2>/dev/null
Este escaneo reveló la existencia del archivo /etc/mysql/conf.d/credentials.txt. 
Al inspeccionarlo con cat, obtuvimos la cuarta bandera y credenciales del sistema: 
FLAG 4: {7845658974123568974185412} Credenciales de sistema: demouser / 3vilH@ksor

Escalada de Privilegios y Flag 5
Con las nuevas credenciales, cambiamos al usuario demouser mediante el comando su demouser.
Para comprobar los privilegios de este usuario, ejecutamos sudo -l. La salida mostró una configuración vulnerable:
(ALL : ALL) ALL  Esto indicaba que el usuario podía ejecutar cualquier comando como administrador sin restricciones. 
Ejecutamos sudo su para convertirnos en root.
Siendo dueños absolutos del sistema, nos dirigimos a la ruta web original y leímos el archivo restringido flag.txt que nos había rebotado al inicio: 
FLAG 5: {5473215946785213456975249} 
