---
title: Mover el último archivo...
date: 2010-10-11 22:05
category: dev

tags: bash, linux, python, script, utilities
---

Una de las cosas tontas en las que perdía más tiempo era a la hora
de rescatar un archivo descargado con el navegador. La situación
típica era la de estar trabajando en consola, ver que ha terminado
una descarga en la barra de tareas (usualmente música) y no poder
mover el archivo fácilmente.

  
Usualmente lo que descargo se va a la carpeta \~/Downloads de
manera que si me descargaba pongamos un comprimido, por ejemplo
music.zip tenía que mirar en el directorio \~/Downloads el último
archivo modificado (rebuscando porque siempre acaba lleno de
basura) y traérmelo al directorio de trabajo.

  
  
	ls -t ~/Descargas | head -n 1 
	mv ~/Descargas/nombre_tecleado.zip .
  
Con el coñazo de que si hay un directorio, ls lo lista primero y ya
me tenía que poner a mirar. Aunque os parezca poco para teclear,
cuando se realiza repetidamente es simplemente demasiado. Intenté
acortarlo poniendo un enlace simbólico, como hago con muchos otros
directorios. Lo acorté a \~/dwn pero ni por esas, seguia siendo
demasiado farragoso.   
  
Friki como es uno, me hice un pequeño script para sh que movía al
directorio de trabajo el último archivo modificado en el directorio
de descargas. Le puse por defecto la ruta. \~/dwn
  
    
    #!/bin/sh
    # Francisco Javier Santacruz López-Cepero
    # 3 - 12 - 2009 
    # Move the most recent modified file from SOURCE DIR TO USR DIR
    # If called without arguments SRC is /home/user/dwn y destination work
    # directory
    # mvlast [ SOURCE DIR ] [ DESTINATION DIR ]
    
    SRC='/home/'$USER'/dwn'
    DEST=$(pwd)
    
    # Take arguments 
    if [ $# -gt 0 ] 
    then
        SRC=$1
        if [ $# -eq 2 ] 
        then
            DEST=$2
        else
            echo "mvlast [ SOURCE DIR ] [ DESTINATION DIR ]"
        fi
    fi

    FILE=$(ls -t1 $SRC | head -n 1)
    /bin/mv "$SRC/$FILE"  "$DEST" && echo "$SRC/$FILE"
  

Lo situé en mi home como \~/.mvlast.sh y creé un alias. (No veía
muy bien meterlo en \~/usr/bin/mvlast):
  
	alias mvlast='~/./.mvlast.py'

El script tenía varios inconvenientes:   

No sabía lo que iba a mover hasta haberlo hecho.
Si por error mueves un archivo .part, te cargas una descarga.
Cuando descargaba varios ficheros, tenía que repetir el comando.
A veces no quería exactamente la última descarga, sino otra en
concreto.
  
  
Sin embargo el otro día, mientras me bajaba una *.iso* muy pesada, descargué
también unos apuntes, tra lo cual raudo y veloz ejecuté mvlast lo que me trajo
el .part de la iso, deteniendo la descarga y dejándome absorto un rato durante
el cual miraba la consola extrañado con cara de imbécil.

En ese momento me replanteé reescribir el script... y ya de paso lo
hice en python.   
Le añadí algunas cosillas que me hacen la vida mucho más fácil:   

Listado de ficheros a mover (opción -l)
Mover más de un fichero (opción -n)
**No** mover .part salvo explícitamente.
Mover solo ficheros que cumplan un patrón (por ejemplo el último
\*.pdf)
  
Aquí el script. No me he parado mucho a comentarlo ni a mirar las
burradas. De hecho durante el uso supongo que algo tendré que
depurar ya que no está tan probado como el otro, que llevaba
conmigo casi un año.
  
  
Son pequeñas cositas que vamos usando en el día a día. Supongo que
habrá otro método para listar las cosas y MUY probablemente el mio
no sea el más óptimo. De hecho, me animo a postear esto en busca de
comentarios. Si teneis pegas decidlo :-D
