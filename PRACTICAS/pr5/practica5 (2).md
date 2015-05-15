
## **PRÁCTICA 5 SWAP** ##

 - Ángel Valera Motos
 - Jose Manuel Martínez de la Insua

## Crear una base de datos MySQL  ##

Creamos una **base de datos** en la máquina 1 llamada *"SWAP"* con una tabla llamada *"datos"* con dos columnas (nombre, edad) e insertamos 2 tuplas.

> create database swap;
> use swap;
> create table datos (nombre varchar(100),edad int);
> insert into datos(nombre,edad) values ("angel",20);
> insert into datos(nombre,edad) values ("jose",80);

En la máquina 2 hemos creado una **base de datos** con el mismo nombre pero vacía. Ahora, volcaremos la base de datos de la máquina 1 en la máquina 2.

Antes de volcar el contenido debemos **bloquear la escritura** en las tablas de la máquina 1 con el comando : 

> FLUSH TABLES WITH READ LOCK;

Ahora podemos hacer el **MySQLdump** para guardar los datos. Ejecutamos en la máquina 1 : 

> mysqldump swap -u root -p > /root/swap.sql

Ahora podemos desbloquear las tablas: 

> UNLOCK TABLES;
> quit

## Replicar una BD con MySQLDump ##

Copiamos el archivo swap.sql a la máquina 2:

> scp root@192.168.56.101:/root/swap.sql /root/

Podemos ver el resultado de la operación en la siguiente captura:

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr5/imgpr5/cp1.PNG)

El siguiente paso es importar los datos a la **base de datos** de la máquina 2 usando el comando :

> mysql -u root -p swap < /root/swap.sql

Podemos ver la base de datos recientemente copiada en la máquina 2:

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr5/imgpr5/Captura2.PNG)

## Replicación de una BD Maestro/Esclavo ##

Primero entramos en el archivo de configuración de la máquina **maestro** (máquina 1). Realizamos los siguientes pasos:

 1. Comentamos el parámetro **bind-address** que sirve para que escuche a un servidor.

	> bind-address 127.0.0.1

 2. Le indicamos el archivo donde almacenar el **log de errores**.

	> log_error = /var/log/mysql/error.log

 3. Establecemos el identificador del servicio.

	> server-id = 1

 4. Establecemos el **registro binario**.

	> log_bin = /var/log/mysql/bin.log

 5. Finalmente guardamos los cambios y reiniciamos el servicio.

	>/etc/init.d/mysql restart

Entramos en el archivo de configuración del **esclavo** (máquina 2):

 1. Establecemos el identificador del servicio.
 
	> server-id = 2

 2. Como tenemos una versión superior a la *"5.5"* (5.5.24), lo que hacemos es reiniciar el servicio.
 
Volvemos al **maestro** y creamos un usuario y le damos permiso de acceso a la duplicación.

> CREATE USER esclavo IDENTIFIED BY 'esclavo';
> GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';
> FLUSH PRIVILEGES;
> FLUSH TABLES;
> FLUSH TABLES WITH READ LOCK;
> SHOW MASTER STATUS;
 
![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr5/imgpr5/Captura3.PNG)

Volvemos a la máquina **esclavo** (máquina 2), entramos en **MySQL** y le damos los datos del maestro.

> CHANGE MASTER TO MASTER HOST= '192.168.56.101',MASTER_USER = 'esclavo',MASTER_PASSWORD='esclavo',MASTER_LOG_FILE='mysql-bin.000001,MASTER_LOG_POS =501,MASTER_PORT=3306;

Por último arrancamos el **esclavo**:

> START SLAVE;

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr5/imgpr5/Captura4.PNG)

Por último vamos a insertar una tupla en la máquina **maestro** y vemos si se ha duplicado el contenido en el **esclavo**:


![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr5/imgpr5/captura5.PNG)
