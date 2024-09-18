# Instalación de la pila LAMP en Manjaro

Este este readme vamos a aprender a como intalar la pila LAMP en nuestra distribución de Manjaro.
LAMP significa lo siguiente:
- Linux
- Apache
- MariaDB (MySQL)
- PHP

Actualizaremos nuestro sistema
```
sudo pacman -Syu
```

Ingresaremos como root
```
su -l root
```

## Apache
Instalamos Apache
```
sudo pacman -S apache
```

Ingresamos al archivo httpd.conf
```
nano /etc/httpd/conf/httpd.conf
```
Comentamos la siguiente linea si no esta
```
[....]
#LoadModule unique_id_module modules/mod_unique_id.so
[....]
```
Buscamos el ServerName y colocamos la IP que deseemos, en mi caso sera local (por eso el 127.0.0.1)
```
[....]
ServerName 127.0.0.1:80
[....]
```

Activamos el httpd
```
systemctl enable --now httpd
```
Para verificar que todo este bien podemos usar el siguiente comando
```
systemctl status httpd
```

Creamos un archivo html para verificar que este corriendo nuestro servidor Apache
```
nano /srv/http/index.html
```
Escribimos dentro del archivo que acabamos de crear 
```
<h3>Funciona</h3>
```
Para verificar que funciona colocan su IP anteriormente asignada en el archivo httpd.conf y deberia cargar una pagina con el texto "Funciona".
```
127.0.0.1 / localhost o su IP
```

## MariaDB
Instalamos MariaDB
```
sudo pacman -S mariadb
```
Activamos el servicio de mariadb
```
systemctl enable --now mariadb
```
Inicializamos el directorio de datos de mariadb
```
mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

Verificamos que el servicio este corriendo correctamente 
```
systemctl status mariadb
```

La siguiente instrucción sirve para proteger nuestra base de datos. Lea bien la información
```
mariadb-secure-installation
```

## PHP
Instalamos PHP
```
sudo pacman -S php php-apache
```
Ingresamos al archivo httpd.conf
```
nano /etc/httpd/conf/httpd.conf
```
Buscamos las siguientes lineas y deben estar igual a estas
```
[...]
#LoadModule mpm_event_module modules/mod_mpm_event.so
LoadModule mpm_prefork_module modules/mod_mpm_prefork.so
[...]
```
Hasta el final del documento insertamos lo siguiente

```
[...]
LoadModule php_module modules/libphp.so
AddHandler php-script .php
Include conf/extra/php_module.conf
```
Verificamos que todo este bien
```
apachectl configtest
```

Creamos un archivo para verificar la información de php
```
nano /srv/http/info.php
```

Pegamos lo siguiente en el nuevo archivo
```
<?php phpinfo();?>
```
Apagamos y encendemos nuestro servicio httpd
```
systemctl stop httpd
systemctl start httpd
```
Verificamos que todo este funcionando correctamente
```
systemctl status httpd
```
Si queremos visualizar el archivo info de php
```
localhost/info.php
```

Listo ya tendrian su servidor web funcionando con los servicios correspondientes.
