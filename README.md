# PRÁCTICA 2 IAW

# Instalación de la pila LAMP en una máquina Ubuntu Server

## Configuración previa
Lo primero que debemos hacer es crear una máquina virtual Ubuntu 20.04 en VirtualBox, en mi caso dejé el tamaño de disco duro y de memoria RAM de forma predeterminada, la red en modo adaptador puente y cargamos la iso.

Configuramos las primeras cosas que van saliendo, idioma, zona horaria, etc... Y una vez instalado el SO al completo lo que hacemos es reiniciar y quitar el disco. Después de esto iniciamos sesión y establecemos una dirección IP estática.

Instalé SSH para poder conectarme desde mi portátil y tener más facilidad a la hora de copiar y pegar comandos.


## Instalación Apache HTTP Server
Comprobamos que tenemos la lista de repositorios y paquetes actualizado mediante el comando: "sudo apt update"

En caso de que hubiera nuevos paquetes para instalar, usamos el siguiente comando: "sudo apt upgrade"

Una vez hayamos actualizado los paquetes instalamos el servidor Apache: "sudo apt install apache2 -y"

Añadimos "-y" a final de los comandos con el fin de que se omita cualquier interacción con el ususario y la instalación sea directa.

Para comprobar que se ha instalado correctamente lo que hacemos es poner en el navegador nuestra dirección IP de la máquina servidor y vemos que se nos muestra la página principal de Apache.


## Instalación PHP

Para instalar PHP y sus herramientas usaremos: "sudo apt install php libapache2-mod-php php-mysql"

Para comprobar que se ha instalado vamos al entramos al archivo /var/www/html/info.php y con algún editor de texto añadimos las siguientes líneas:

```php
<?php
phpinfo();
?>
```

Ahora abrimos el navegador y ponemos nuestra dirección IP/info.php, si todo ha salido bien veremos un documento en el que se nos enseñará toda la información php del servidor.


## Instalación MySQL Server

Introducimos el comando: "sudo apt install mysql-server -y"

Para comprobar que se ha instalado correctamente podemos acceder al servidor MySQL a través del comando: "sudo mysql -u root"

Si todo está bien accederemos sin ningún problema.


## Instalación de la aplicación web propuesta

Lo primero que tenemos que hacer es ir a la carpeta en la que vamos a instalar la aplicación con el comando: "cd /var/www/html"

Para futuras reinstalaciones y demás, nos aseguraremos de que el directorio de la aplicación no existe eliminándola en caso de que esté creado con el comando: "sudo rm -rf iaw-practica-lamp"

Ahora descargamos el repositorio de Github: "git clone https://github.com/josejuansanchez/iaw-practica-lamp.git"

Posteriormente movemos el contenido del repositorio a la carpeta de nuestro apache: "mv /var/www/html/iaw-practica-lamp/src/* /var/www/html/"

Después eliminamos el archivo index.html de apache para que al entrar al servidor se nos muestre la aplicación web: "rm -rf /var/www/html/index.html"

Por último en este apartado crearemos el script para la base de datos de la aplicación, y eliminaremos el directorio iaw-practica-lamp ya que ya no lo necesitaremos:

"mysql -u root -p$DB_ROOT_PASSWD < /var/www/html/iaw-practica-lamp/db/database.sql"
"rm -rf /var/www/html/iaw-practica-lamp/"


## Instalación Adminer

Lo primero que debemos hacer es crear el directorio de instalación: "mkdir /var/www/html/adminer"

Nos movemos al directorio y una vez allí descargamos su repositorio de Github con el siguiente comando
"wget https://github.com/vrana/adminer/releases/download/v4.7.7/adminer-4.7.7-mysql.php"

Movemos los ficheros php al directorio de apache: "mv adminer-4.7.7-mysql.php index.php"

Lo último que haremos en esta instalación será cambiarle los permisos al directorio ade apache: "chown www-data:www-data * -R"


## Instalación GoAccess

Para esto tenemos que abrir el archivo de repositorios de ubuntu y agregarlo al siguiente repositorio: "deb http://deb.goaccess.io/"

Tenemos que conseguir la clave de la aplicación, que se hace así: "wget -O - https://deb.goaccess.io/gnugpg.key | sudo apt-key add -"

Por último actualizamos nuestra lista de paquetes e instalamos GoAccess: "apt update -y"
                                                                         "apt install goaccess -y"


## Configuración del control de acceso

Creamos un directorio para instalar en él el stats de apache: "mkdir /var/www/html/stats"

Dejamos que GoAcces se ejecute en segundo plano: "nohup goaccess /var/log/apache2/access.log -o /var/www/html/stats/index.html --log-format=COMBINED --real-time-html &"

Creamos un archivo en el que estarán las contraseñas del usuario: "htpasswd -b -c $HTTPASSWD_DIR/.htpasswd $HTTPASSWD_USER $HTTPASSWD_PASSWD"

Sustituimos las cadenas correspondientes de PATH así: "sed -i 's#REPLACE_THIS_PATH#$HTTPASSWD_DIR#g' $HTTPASSWD_DIR/000-default.conf"

Copiamos el archivo de configuración: "cp $HTTPASSWD_DIR/000-default.conf /etc/apache2/sites-available/"

Y para terminar reiniciamosapache: "systemctl restart apache2"


## Instalación  AWStats

Ejecutamos el siguiente comando: "apt install awstats -y"

Cambiamos los valores LogFormat y SiteDomain del archivo de configuración: "sed -i 's/LogFormat=4/LogFormat=1/g' /etc/awstats/awstats.conf"
                                                                           "sed -i 's/SiteDomain=""/SiteDomain="practicaiaw.com"/g' /etc/awstats/awstats.conf"

Copiamos el archivo editado al directorio: "/etc/apache2/conf-available/: cp $HTTPASSWD_DIR/awstats.conf /etc/apache2/conf-available/"

Activamos el servicio AWStats con los siguientes comandos: "a2enconf awstats serve-cgi-bin"
                                                           "a2enmod cgi"

Reiniciamos apache, modificamos los permisos y actualizamos los logs: "systemctl restart apache2"
                                                                    "sed -i -e "s/www-data/root/g" /etc/cron.d/awstats /usr/share/awstats/tools/update.sh"
