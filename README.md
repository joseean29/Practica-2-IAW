# PRÁCTICA 2 IAW

# Implantación de una aplicación web LAMP en Amazon Web Services (AWS)

## Acceso a AWS Educate
Lo primero, pero no menos importante, es que tenemos que tener es una cuenta en AWS Educate para poder realizar esta práctica.

Iniciamos sesión en la web de AWS Educate y nos vamos a la sección “My Classroms” una vez allí entramos a nuestro curso pinchando en “Go to classrom” y posteriormente damos a continuar.



## CONSOLA AWS
Ahora tenemos que entrar dentro de nuestra consola AWS y una vez allí en los servicios de AWS debemos elegir los servicios “EC2”.

Cuando elegimos este tipo de servicio entramos en la pestaña de la consola de EC2 y una vez allí tenemos que irnos a las instancias y ya crear las que queramos.



## SSH VISUAL STUDIO CODE

Antes de explicar el tema de las instancias voy a explicar como conectarnos a ellas desde SSH a través de Visual Studio Code.

En Visual Studio debemos ir a la sección de instalar extensiones (se puede hacer mediante Ctrl + Mayús + x). 
En el buscador escribimos ssh y nos instalamos la extensión “Remote - SSH”.

Por ahora esto lo dejamos aquí, pero volveremos a hablar de Visual Studio más tarde.



## CREACIÓN DE LA INSTANCIA

La máquina que vamos a crear para esta práctica será una Community AMI con la última versión de Ubuntu Server.

Cuando estemos creando la instancia debemos configurar los puertos que estarán abiertos, nosotros abrimos el de SSH para poder conectarnos desde Visual Studio Code y los puertos de HTTP y HTTPS para poder acceder.

Creamos una clave nueva para poder conectarnos por SSH a la instancia, descargamos la clave y la guardamos en alguna carpeta en la que tengamos permisos para que luego no nos de problemas. Yo en mi caso tengo Windows y la guardé en la ruta: “C:\Users\Jose Antonio”

Para terminar estre paso creamos un par de claves (pública y privada) para conectar por SSH con su instancia.



## LANZAR INSTANDIA DESDE VISUAL STUDIO CODE

Volvemos a Visual Studio Code y pinchamos en el icono de la esquina inferior izquierda que es el de SSH, ahí se nos abren unas cuantas opciones y nosotros tenemos que elegir la de “Remote SSH: Open configuration file…” y luego elegir la ruta en la que está el SSH. 

Una vez dentro del apartado de configuración debemos escribir el siguiente contenido:

```Host iaw-practica01
    HostName ec2-54-208-223-218.compute-1.amazonaws.com
    User ubuntu
    IdentityFile "C:\Users\Jose Antonio\iaw-amazon.pem"
```

Esto es un ejemplo de lo que habría que poner, quiero dejar claro que el Host es cualquier nombre que le quieras dar a la instancia y que el HostName varía cada vez que inicias la instancia ya que es la dirección DNS pública de la instancia, por otra parte en IdentityFile hay que poner la ruta absoluta en la que se encuentra la clave creada anteriormente.

Ya creado el archivo de configuración volvemos a pinchar sobre el icono del SSH y esta vez elegimos la opción “Remote SSH: Connect to Host…”, elegimos el nombre del host y le damos a continuar y luego a Ubuntu.

Después de esto ya estaríamos conectados por SSH así que lo que tenemos que hacer es abrir la carpeta llamada Ubuntu y en ella pegar el script de bash de la práctica 1.

Para acabar debemos hacer uso del script de bash que diseñamos en la práctica 1 para automatizar la instalación de la aplicación web LAMP.


