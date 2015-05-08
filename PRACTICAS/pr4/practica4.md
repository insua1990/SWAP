
## **PRÁCTICA 4 SWAP**##

 - Jose Manuel Martínez de la Insua
 - Ángel Valera Motos

Introducción:
----------------

El objetivo de esta práctica es medir el rendimiento de un servidor, para ello usaremos 2 herramientas para generar una carga específica (las herramientas usadas serán **Apache Benchmark** y **Siege**).

El funcionamiento es el siguiente: Se tienen 2 máquinas servidoras (ambas contienen el fichero *"prueba.php"* que realiza una serie de operaciones, además tenemos un balanceador de carga que distribuirá la carga generada por **Siege** o **Apache Benchmark**; al balanceador se accede desde otra máquina no perteneciente a nuestra granja web).

Rendimiento con Apache Benchmark
--------------------------------------------

Nuestra prueba se realizó desde una nueva máquina virtual ajena a la granja web. Se ejecutó en la terminal el siguiente comando:

> ab -n 100 -c 5 IP_BALANCEADOR/prueba.php			

Esto nos muestra el resultado de atender a 100 peticiones en bloques concurrentes de 5 peticiones a *"prueba.php"*.

 Realizando 10 pruebas a una máquina sola (sin balanceador) , 10 pruebas al balanceador **nginx** y otras 10 al balanceador **haproxy** obtenemos las siguientes medidas:

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr4/imgpr4/ab.png)

De éstos datos se obtuvo la media de cada uno de los parámetros a tener en cuenta y se almacenan visualmente en estos gráficos:

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr4/imgpr4/abgraphs.png)


## Rendimiento con Siege ##

Nuestra prueba se realizó desde una nueva máquina virtual ajena a la granja web. Se ejecutó en la terminal el siguiente comando:

> ./siege -b -t60S -v IP_BALANCEADOR/prueba.php			

Esto nos muestra el resultado de atender durante 60 segundos peticiones en bloques concurrentes de 15 peticiones (15 por defecto) a *"prueba.php"*.

 Realizando 10 pruebas a una máquina sola (sin balanceador) , 10 pruebas al balanceador **nginx** y otras 10 al balanceador **haproxy** obtenemos las siguientes medidas:

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr4/imgpr4/siege.png)

De éstos datos se obtuvo la media de cada uno de los parámetros a tener en cuenta y se almacenan visualmente en estos gráficos:

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr4/imgpr4/siegegraphs.png)

Podemos ver la mejoría claramente al usar cualquiera de los balanceadores:

 **1.** El tiempo de respuesta se **reduce un 40%** aproximadamente.
 **2.** Las transacciones por segundo **aumentan un 70%** aproximadamente .
 **3.** La transacción más larga con una máquina sola es de 12 segundos aproximadamente, mientras que la más larga detectada de ambos balanceadores nunca supera los 8 segundos.

