
## **Práctica 3 SWAP: "BALANCEO DE CARGA"** ##

 - Jose Manuel Martínez de la Insua
 - Ángel Valera Motos

Para nuestra práctica vamos a utilizar una máquina con **nginx** como balanceador y 2 máquinas conectadas al balanceador por una red interna (ambas máquinas con **Ubuntu Server**).

## Instalación de "nginx" en Ubuntu Server. ##

Importamos la clave del repositorio software:

> $ cd /tmp/
> 
> $ wget http ://nginx.org/keys/nginx_signing.key apt-get add /tmp/nginx_signing.key

> $ rm -f /tmp/nginx_signing.key

Asegurar que el balanceador no tiene **Apache** instalado ya que ocupa el **puerto 80** y éste lo necesitaremos para instalar el software de balanceo (si lo tiene instalado hay que matar los procesos **Apache**, por problemas de conflictos entre máquinas) y asegurar también que el balanceador puede ver a las otras máquinas en una red interna.

El esquema sería el siguiente: una máquina actuando como balanceador (con acceso a Internet para recibir las peticiones) y otras dos máquinas sin acceso a Internet pero conectadas por red interna con el balanceador y entre ellas.

## Modificar el fichero etc/nginx/conf.d/default.conf ##

    upstream apaches {
 		server 192.168.56.101;
		server 192.168.56.102;	
	}

	server{
 		listen 80;
 		server_name m3lb;
 		access_log /var/log/nginx/m3lb.access.log;
 		error_log /var/log/nginx/m3lb.error.log;
 		root /var/www/;
 		location /
 		{
 			proxy_pass http://apaches;
 			proxy_set_header Host $host;
 			proxy_set_header X-Real-IP $remote_addr;
 			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 			proxy_http_version 1.1;
 			proxy_set_header Connection "";
 		}
	}


A continuación reiniciamos el servicio con el comando:

> $ service restart nginx

Comprobamos que balancea la carga con el comando:

> $ curl http ://192.168.56.103/hola.html 

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr3/imgpr3/Balanceo_NGINX.PNG)

Una vez comprobado que balancea, vamos a añadirle **peso a cada máquina** para establecer la **prioridad** con la que se asignan tareas; en nuestro caso:

    upstream apaches {
 		server 192.168.56.101 weight=1;
		server 192.168.56.102 weight=2;	
	}

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr3/imgpr3/Balanceo_Nginx_peso.PNG)

Ahora hemos probado el **balanceo por IP** de la siguiente forma:

    upstream apaches {
		ip_hash;
 		server 192.168.56.101;
		server 192.168.56.102;	
	}

Ahora el balanceador nos asigna la máquina 2 y siempre nos atiende la misma...

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr3/imgpr3/B_Nginx_iphash.PNG)


## Instalación de balanceador haproxy ##

Antes de empezar con la instalación, debemos parar el servicio **Nginx** con el comando:

> $ service nginx stop
> 
> $ apt-get install haproxy joe

Configuración de **haproxy**:

> $ cd /etc/haproxy

Modificamos el fichero *haproxy.cfg* (usando joe) de la siguiente forma:

    global
	daemon
	maxconn 256

    defaults
    	mode http
    	contimeout 4000
    	clitimeout 42000
    	srvtimeout 43000
    	frontend http-in
    	bind *:80
    	default_backend servers

    backend servers
    	server m1 192.168.56.101:80 maxconn 32
    	server m2 192.168.56.102:80 maxconn 32 


Una vez configurado, lanzamos el servicio con el comando:

> $ sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg

Ahora probamos que nuestro balanceador **haproxy** funciona con el comando *"curl"*, y podemos ver el resultado en la captura:

![enter image description here](https://raw.githubusercontent.com/insua1990/SWAP/master/PRACTICAS/pr3/imgpr3/B_Haproxy.PNG)


