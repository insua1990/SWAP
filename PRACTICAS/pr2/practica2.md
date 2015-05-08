
## **PRÁCTICA 2 SWAP** ##

 - Ángel Valera Motos
 - Jose Manuel Martínez de la Insua

El objetivo de la práctica es tener una segunda máquina disponible por si falla la primera, para ello lo que debemos hacer es tenerla actualizada y con la información clonada de la primera a la segunda.

Podemos hacerlo de varias formas: 

La primera forma es crear un **tar.gz** con la información de la primera máquina y copiarlo con **ssh** en la segunda con el comando:


> $	tar czf prueba | ssh angel2@192.168.1.101 'cat > ~/tar.tgz'

Podemos ver la copia en la otra máquina en el escritorio raiz (ls).

La segunda forma es usar la herramienta *"rsync"*  y para ello debemos instalarla (si no se tiene) :

> $	apt-get install rsync

Una vez instalada clonamos por ejemplo el directorio **/var/www** (sitio web de donde apache toma la información a servir), el comando desde la máquina B (máquina de reserva) y siempre en **modo root**:

> $	rsync -avz -e ssh angel@192.168.1.100:/var/www/ /var/www/

La línea de comandos anterior clona el contenido completo de **/var/www** , para copiar sólamente aquellos archivos que nos interesen usamos el comando:

> $	rsync -avz --delete --exclude= ** /stats --exclude=** /error --exclude=**/files/pictures -e "ssh -l angel" angel@192.168.1.100:/var/www/ /var/www/


Si en el **/var/www** de la máquina1 se ha borrado algún archivo con el comando anterior, se borraría también en la máquina 2, en nuestro caso no se produce ningún cambio.

La tercera forma es el uso de *"rsync"* en la máquina 2 utilizando **ssh** sin necesidad de contraseñas, para ello creamos una contraseña utilizando para ello:

> $	ssh-keygen -t dsa     (el parámetro -t indica el tipo de contraseña)

Dicha contraseña la copiamos a la máquina 1.

> $	ssh-copy-id -i .ssh/id_dsa.pub angel@192.168.1.100

Ahora nos conectamos mediante **ssh** a la máquina1 desde la 2 no nos solicita contraseña.

La tarea de actualización con **crontab** :

    1	*	*	*	*	angel2	rsync -avz -e ssh angel@192.168.1.100:/var/www/ /var/www/

En el minuto 1 de cada hora ejecuta la línea de comandos: 

> rsync -avz -e ssh angel@192.168.1.100:/var/www/ /var/www/

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr2/crontab_p2.PNG)