---
title: Backups en imágenes simuladas mediante el uso de enlaces duros
date: 2010-09-04 19:26
category: dev
tags: backup, cp, differential, filesystem, hard links, image, incremental, inode, linux, rsync, snapshot, tar, update, zip
---

<div style="float: left; margin: 5px;"> 

![Disco Duro][]  

</div>

Enfrentado una vez a la situación de tener que hacer backups de una aplicación
con muchos gigas en datos importantes, acabé adoptando una manera interesante
pero no excesivamente conocida de backup.  Antes de meterme a ello, para
comprender mejor el problema que resuelve y las ventajas que aporta, haré un
repaso a las formas más comunes de hacer backup en entornos unix y las
herramientas que usaremos en los ejemplos: tar y rsync. Que son viejos
conocidos de este entorno y sencillos de utilizar o de automatizar desde
scripts para poner en el cron.


### Herramientas

#### Tar:

*(De **T**ape **Ar**chiver)*: Programa antediluviano
originalmente pensado para realizar copias de seguridad en cinta
magnética. Crea un solo archivo a partir de una jerarquia de
directorios, adicionalmente usando compresión.

#### Rsync:

Simplemente transmite ficheros. Es como un cp muy mejorado. Se basa
en un algoritmo que le permite calcular las diferencias entre dos
ficheros y mandar **solo eso** lo cual lo hace ideal para
transmitir backups por red.

A continuación haremos un breve repaso de los tipos de backup más
empleados.

### 1. Imagen o Snapshot

El sistema más sencillo probablemente fuese hacer un paquete
(comprimido o no) con cp,  zip o tar del directorio del que
deseamos hacer backup.  
Por ejemplo:

	$ tar -cvf dir-fecha.tar dir  
	$ cp -r dir dir-fecha  
	$ zip dir-fecha.zip dir

Así se obtiene una imagen del directorio... que ocupa lo mismo que
el contenido directorio (según compresión). Muchas veces no es una
opción. Por ejemplo, si el directorio dir ocupa 1 Terabyte y son
datos críticos que necesitan copias varias veces al día, no es
abordable guardar tantos Terabytes de información *CADA DÍA*, y
quizá durante *MUCHO TIEMPO*. Alguno dirá: "Podemos simplemente
borrar las imágenes más antiguas". Pero... ¿Y si necesitamos esas
imágenes, y si necesitamos garantizar datos durante tanto tiempo
que nos quedemos sin espacio?. Tenemos un problema. El método no
nos sirve.

### 2. Diferenciales

Lo normal es que cuando se tienen grandes volúmenes de datos, solo
cambien unos pocos ficheros de un día a otro. Si se piensa, resulta
ridículo copiarlo **todo** cada vez, replicando un montón de
ficheros iguales. Tiene mucho más sentido copiar solo *ESA* pequeña
parte que cambia, porque lo demás permanece inalterado, ya lo
tenemos.  
Estamos hablando de un backup diferencial, en el que cada día se
calcula la diferencia (△) con respecto al backup anterior y se
copia solo eso.  
Por ejemplo:

> Día1: dir --\> dir.copia  \# Primer día se copia todo.  
> Día2: dir --\> △dir-dia2  \# Siguientes días, se copia solo lo que
> ha cambiado.  
> Día3: dir --\> △dir2-dia3

Después, para recuperar tal y como estaba dir en un día
determinado, deberíamos empezar con la copia completa del día 1, e
ir añadiendo diferencias siguiendo la cadena hasta el día deseado.
Descompriendo dir.copia y aplicándole encima las △ de cada día una
tras otra hasta el 3.

Esto podríamos hacerlo empleando tar o aún mejor, rsync

#### Usando tar

Por ejemplo con tar podríamos usar un listado list.txt que nos
sirva para saber que ficheros cambian de una vez a otra. La primera
vez lo copiará todo:

	# A realizar cada día.  
	$ tar --listed-incremental list.txt -cvf dir-$DIA dir

Con esto conseguiríamos ficheros diferenciales con lo que cambia de
cada día:

> dir-1.tar  
> dir-2.tar  
> dir-3.tar

Siendo el primero el que sirve de base y contiene una imagen
completa. Para rescatarlas, tendríamos que descomprimir primero la
base y luego el resto encima por orden:  

	# Descomprime dir-1, luego dir-2, luego dir-3 uno encima de otro.  
	for i in dir-*; do  
	  tar -xvf $i  
	done

#### Usando rsync

Con rsync podríamos tener un directorio destino con una copia de lo
que hay en dir y emplear las opciones combinadas --backup y
--backup-dir=dir-$DIA para crear los △ diferenciales. De esta forma
tendríamos la última versión de dir en el directorio destino, y un
directorio △ dir-$DIA con los ficheros que cambiaron en cada
copia.

	# Copia dir en dest, poniendo las diferencias en dir-$DIA  
	$ rsync --backup --backup-dir dir-$DIA dir dest 

Para recuperarla, deberíamos ir copiando y sobreescribiendo los
ficheros por orden en un directorio aparte recup.  

	# Copia machacando en recup el contenido de los dir dia-1, dia-2 ...
	for i in dia-*; do  
		cp -R $i recup  
	done

Tras hacer esto, en recup tendremos el estado del directorio dir en
el último día del que hayamos descomprimido.

Aunque parezca bonito, este sistema conlleva dos problemas
fundamentales:

1. Es costoso acceder a los datos tal y como estaban un día
determinado.  
Los datos no están disponibles para que el administrador entre y
los vea directamente, sino que hay que procesarlos aparte y puede
llevar mucho tiempo.
2. Es un sistema extremadamente peligroso... ¿Que ocurre si se
corrompe la △ de un solo día intermedio? Pues que TODOS los datos
posteriores son irrecuperables.  
Repito, si se corrompe dia-2, desde dia-3 a dia-10000 serán
irrecuperables. Por lo general, se hace una imagen completa cada
cierto tiempo, de manera que la cadena de diferencias no sea
demasiado larga (y se multipliquen las probabilidades de hecatombe)
pero de nuevo estamos hablando de grandes imágenes que ocupan
mucho, que es de lo que veníamos huyendo.
Demasiado peligroso.

### 3. Imágenes "diferenciales"

Y por fin, mi solución favorita.

Viendo que los diferenciales son más peligrosos que un sudo rm -rf,
volvamos a la idea de las imágenes. Era una idea bonita. Un día, un
paquetito completo con todo. Sencillo, directo. ¿El problema? El
horrible consumo de espacio debido a la replicación de ficheros.

En ese punto, enfrentado a mis backups de 40Gigas diarios con
discos por alrededor de 150G, un día pensé... ¿No podrían compartir
los ficheros que no cambian los mismos datos, es decir, ser enlaces
duros unos de otros? Y comencé a buscar [documentación][] animado
por esa idea.

¿Enlace duro? Se hace necesario presentar algún concepto de los
sistemas de ficheros.

#### Ficheros y Enlaces Duros

![Es solo un fichero normal...][]

Tiene sus distintas partes:

Descriptor: Mantiene el nombre del fichero, los permisos, fecha de
creación... y guarda la dirección donde se encuentran los inodes y
datos.
datos: Los datos se encuentran divididos en bloques de cierto
tamaño en el disco duro, enlazados en cadena osea cada uno cogido
de la mano del siguiente y del anterior.
inode: El inode es bloque cabecera que hace de índice de estos
bloques de datos y permite acceder a ellos así como guardar
información de más bajo nivel sobre el fichero.
Quedamos entonces en que un fichero tiene un descriptor con
información sobre el y por otra parte sus bloques de datos,
indizados por el inode. El descriptor es lo que "vemos" del
fichero, lo que se lista en los directorios etc...  
Entonces, espera. ¿Un fichero tiene un descriptor de fichero o...
...o uno o más?. En efecto, puede darse que un solo fichero tenga
varios descriptores apuntando por debajo a los mismos datos, a la
manera de un animal con muchas cabezas y un solo cuerpo.

![image][]

A esto lo llamamos un *enlace duro*, desde "fuera" vemos lo que
parecen dos ficheros, pero que por debajo es uno solo; En "la
superficie" no nos enteramos de mucho y a nivel conceptual
manejaremos ficheros que están ligados entre si:

Si accedemos y modificamos los datos empleando cualquiera de los
descriptores, los modificamos para todos los demás.
Si movemos uno de sitio, el resto no es afectado (dentro de la
misma partición).
Si se borra uno, los datos no se borran hasta que no desaparezca el
último enlace duro.
Es muy fácil cargarse datos sin querer sobreescribiendo a traves de
un enlace duro, hay aplicaciones (como rsync) que lo que hacen por
defecto al sobreescribir un archivo que es un enlace duro es
"romper" el enlace, esto es eliminar el enlace duro y crear un
archivo nuevo para no sobreescribir el anterior que es referenciado
desde otra parte y que probablemente nos sea problemático si
cambia.  
Este comportamiento de rsync y otros programas es importante y será
utilizado en el backup.

Bien, volvamos a la copia de seguridad.

La idea era hacer backups que pareciesen completos, pero en los que
los ficheros que no cambian se comparten, de manera que evitásemos
el horrible consumo de espacio de las imágenes y a la vez la
dispersión de tener archivos diferenciales y tener que tratar con
ellos.

El procedimiento es:

1. Copiar los ficheros que cambian a un directorio llamado Actual
2. Crear un directorio para ese dia en concreto llamado dir.$DIA
De esta manera, tenemos una carpeta para cada día con la imagen
exacta del estado del directorio dir en ese momento. La copia en el
directorio Actual la haremos mediante rsync de manera que cuando
vaya a sobreescribir un fichero, rompa el enlace y no modifique la
información de los antiguos, que la queríamos tal y como está.

Un ejemplo práctico para mejor comprensión:

1. Sincronizamos todos los datos del directorio dir al directorio
 Actual.

![Sincronizar ficheros a un directorio][]

		$ rsync --delete dir Actual

2. Creamos una carpeta específica para el día y enlazamos todos
 los contenidos.

![Aprovechando espacio...][]

		$ cp -rl Actual dir-$DIA

¡Lo hemos conseguido! Tenemos lo que parece son imágenes completas
de dir, cuando en realidad no ocupamos más espacio que con una sola
imagen.

Ha sido un poco simple, de manera que veamos que ocurre cuando
lanzamos el backup al día siguiente, tras realizarse cambios en los
ficheros a guardar.

3. Sincronizamos de nuevo con los cambios del día siguiente.

Esta vez, rsync trae un fichero nuevo llamado File3 y modifica
File2 que ha sufrido cambios. Fijaos en cómo rsync rompe los
enlaces duros que había entre los ficheros a sobreescribir mediante
la creación de uno nuevo con un inode diferente.

![image][1]

		$ rysnc --delete dir Actual

4. Creamos otra carpeta específica para el día y enlazamos los
 contenidos cambiados.

Ahora volvemos a crear otra carpeta que hará la ilusión de ser una
imagen completa de dir.

![Aprovechando espacio... siguiente día][]

		$ cp -rl Actual dir-$DIA

¡Así funciona! Cada vez que se sincroniza Actual, creamos una
carpeta nueva y enlazamos los contenidos. Al final, el espacio
ocupado es el tamaño de la imagen inicial más los incrementos
(exactamente igual que en el backup diferencial) pero manteniendo
un fácil acceso a los datos y la apariencia de imágenes completas
(como en el backup mediante imágenes).

A aquella aplicación que pesaba tanto, al menos para los medios de
los que disponía, se le hacían dos copias externamente desde dos
sitios distintos mediante este sistema, empleando rsync a través
ssh, resultando en una imagen de unos \~50Gb en cada sitio, por un
total de `\~100Gb`. Respiré tranquilo :D

Existe información en este post, referida sobre todo a la
estructura del sistema de ficheros y a las órdenes de consola
puestas como ejemplo que no son del todo exactas o bien están
incompletas (las órdenes acaban siendo más complejas a medida que
se les añade flags y datos). Si alguien detecta errores de
concepto, que no dude en hablar y señalarlo.


  [Disco Duro]: ./img/backup-300x176.jpg "harddiskplate"
  [documentación]: http://www.mikerubel.org/computers/rsync_snapshots
  [Es solo un fichero normal...]: ./img/file1-300x100.png "file1"
  [image]: ./img/file2-300x112.png "file2"
  [Sincronizar ficheros a un directorio]: ./img/copy1-300x189.png "copy1"
  [Aprovechando espacio...]: ./img/copy2-300x136.png "copy2"
  [1]: ./img/copy3-300x109.png "copy3"
  [Aprovechando espacio... siguiente día]: ./img/copy4-300x99.png "copy4"
