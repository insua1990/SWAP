
## **PRÁCTICA 6 SWAP** ##

En ésta práctica crearemos **dos discos RAID** que enviarán información entre ellos de modo que si uno de los dos fallase , el otro está de soporte para **evitar fallos** en nuestro sistema.

Primero creamos dos discos duros virtuales en nuestra máquina virtual con Ubuntu 12.04 Server.

## Configuración del RAID por software ##

Primero instalamos el software necesario para configurar el **RAID**:

> sudo apt-get install mdadm
> sudo fdisk -l 

Ahora podemos crear el **RAID**:

> sudo mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr6/imgpr6/Captura1.PNG)

Ahora le damos formato:

> sudo mkfs /dev/md0

Ahora creamos el directorio donde se montará la unidad de **RAID**:

> sudo mkdir /datos
> sudo mount /dev/md0 /datos

Para comprobar el estado del **RAID**:

> sudo mdadm --detail /dev/md0

![enter image description here](https://github.com/insua1990/SWAP/blob/master/PRACTICAS/pr6/imgpr6/Captura2%20%281%29.PNG?raw=true)

## Automatizar el proceso al arrancar el sistema ##

Abrimos el fichero "*fstab*" para modificarlo:

> sudo nano /etc/fstab 

y añadimos la línea : 

    