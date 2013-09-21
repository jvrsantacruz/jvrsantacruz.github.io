---
title: Migrar de gmail a gmail
date: 2012-02-19 22:00
category: dev
tags: gmail, python, script, gmail-backup, imap, imapsync, contacts, omp, reader, google
lang: es
---

<img src="./img/migrar-gmail-header.svg" width="580px"/>

Intento migrar mi cuenta de gmail, de una antigua a otra más seria, llevándome
todos los contactos, feeds, filtros, calendarios, chats y, por supuesto, los
correos.
Los correos deben mantener:

- Leidos
- Preferidos
- fechas 
- Origen y destino (no ser un reenvio)
- Etiquetas (múltiples)

## Migrar correo

Probé 4 opciones, [gyb][], [imapsync][im], [gmail-backup][gb] y [thunderbird][thun], decantándome finalmente por [gyb][].
Todas ellas sincronizan el correo mediante *IMAP* entre las dos cuentas de google, por el procedimiendo de descargar primero el correo de la cuenta antigua al disco duro, y enviarlo posteriormente a la nueva.

### Activar *imap*

Debe irse a las opciones de ambas cuentas, a `Forwarding and POP/IMAP` y activar IMAP en ambas cuentas.

![activate-imap][]

## Con [Got-your-back][gyb]

Es el más rápido con diferencia, hace backup y restaura todo (etiquetas multiples, marca de importante, leidos...) y solo envía cada correo una vez.
Emplea autenticación mediante token como app Google. Al ejecutarlo escribirá una url a la que tendrás que acceder y logearte con la cuenta de gmail para permitirle acceso.

1. Descargar el [script](http://code.google.com/p/got-your-back/downloads/list)
2. Realizar el backup de la cuenta antigua:

		python gyb.py --email antigua@gmail.com --backup --compress

	Creará el directorio `GYB-Gmail-Backup-antigua@gmail.com` con el correo guardado.

3. Restaurar el correo en la nueva

		python gyb.py --email nueva@gmail.com --restore 
		    --compress --folder GYB-Gmail-Backup-antigua@gmail.com
	

El proceso de restore lleva mucho más tiempo.
Es la mejor forma con diferencia para hacer backup a una cuenta gmail.
De hecho me estoy pensando en mantener un backup offline de mi cuenta metiendo la orden de backup en el cron.

-----------------------------------------

## Con [gmail-backup][gb]

[gmail-backup][gb] se ve que esté mantenido desde el 2009,
y solo está disponible en formato binario, no tiene licencia libre y está
compilado con una versión antigua de python. Aún así, es bastante más rápido
que [imapsync][im] y es más sencillo de usar.

### Instalar [gmail-backup][gb]

1. Descargar el binario [de su página oficial][gb-dwn] y descomprimir.
2. Descargar e instalar [python2.5][py2.5] si no se encuentra en el sistema (intenta ejecutar `python2.5` en consola).

		code-block::bash
		tar -xvzf Python-2.5.6.tgz
		cd Python-2.5.6
		./configure
		make
		sudo make altinstall

  **¡Ojo!**: Usad `altinstall` y no simplemente `install`, o sobreescribirá la
  versión actual por defecto de Python en el sistema.

### backup & restore

Primero haremos un backup completo de nuestra cuenta *antigua* para a continuación restaurarla en la cuenta *nueva*.
Haciendo de esto, tendremos de paso una copia de seguridad de todo nuestro correo. Eso sí, tardará, aunque menos que [imapsync][im].

	code-block::bash
	./gmail-backup.sh backup ~/gbackup antigua@gmail.com p4sw0rd \
	&& ./gmail-backup.sh restore ~/gbackup nueva@gmail.com p4sw0rd

---------------------------

## Con imapsync

[imapsync][im] es una herramienta escrita en *Perl*, que sincroniza dos servidores
*IMAP*. Se le indica la cuenta de origen y de destino, y manda todos los
correos de orige a destino, sincronizándolos.

### Instalar imapsync

	sudo apt-get install imapsync

### Sincronizar

Para ello se llama a imapsync *dos veces*. Una para sincronizar los labels (folders) y otra para enviar el correo. 

	imapsync\
	    –user1 antigua@gmail.com\
		–user2 nueva@domain.com\
		-passfile1 ./antigua-pass\
		-passfile2 ./nueva-pass\
		–host1 imap.gmail.com\
		–host2 imap.gmail.com\
		-authmech1 LOGIN -authmech2 LOGIN\
		-port1 993 -port2 993\
		-split1 100 -split2 100\
		-ssl1 -ssl2\
		–syncinternaldates\
	    –justfolders\


	imapsync \
		–user1 antigua@gmail.com\
		–user2 nueva@domain.com\
		-passfile1 ./antigua-pass\
		-passfile2 ./nueva-pass\
		–host1 imap.gmail.com\
		–host2 imap.gmail.com\
		-authmech1 LOGIN -authmech2 LOGIN\
		-port1 993 -port2 993\
		-split1 500 -split2 100\
		-ssl1 -ssl2\
		--fastio1 --fastio2
		-syncinternaldates\
		-allowsizemismatch\
		--nofoldersizes\
		--skipsize\
		--fast\
		--useheader 'Message-Id' \
		--useheader 'X-Gmail-Received'
		--exclude "(Trash|Spam|Publicidad)"
		--timeout 100

Explicación de las opciones:

- **passfile**: es un fichero de texto plano que contiene solo la clave, para
  no ponerla directamente en la consola.
- **syncinternaldates**: Evita que se cargue la fecha de los emails.
- **justfolders**: Crea solo los directorios/labels.
- **allowsizemismatch**: Se quejaba de error por diferente tamaño en los correos
  bajados, esto hace que los ignore.
- **exclude**: Carpetas/Labels a excluir de la sincronización.
- **useheader**: El imap de gmail omite esas cabeceras, forzarlas.
- **split**: Tamaño de petición a cada servidor. Al de subida pedimos menos.
- **fastio** **fast**: Realiza las operaciones más rápido 
- **nofoldersizes** **skipsize**: No calcula estadísticas ni tamaños, que es lento.
- **timeout**: Abortar tras *x* segundos de inactividad.

Aviso que tarda muchísimo, ya que debe descargar todo el correo y volverlo a
enviar, incluyendo attachments, **para cada etiqueta**, lo que hace que envie
`L` veces cada correo, donde `L` es el número de etiquetas que tiene. **No se
duplica** ya que gmail los detecta como el mismo y simplemente les añade la
etiqueta correspondiente en lugar de copiarlo de nuevo.

Cuando el proceso se para, es conveniente volverlo a ejecutar añadiendo el
parámetro: `--maxage int` donde `int` indica la fecha del el último correo que
enviamos, y así descarta evaluar los más antiguos.

Para reiniciar la conexión cuando se pilla usé un bucle infinito y la opción `--timeout 100`, para no tener que estar cuidando yo del proceso. 

	code-block::bash
	while [ 1 -eq 1 ] 
	 do
		imapsync\
		-host1 imap.gmail.com -port1 993 -user1 antigua@gmail.com -passfile1\
		./antiguapass -ssl1 -host2 imap.gmail.com -port2 993 -user2\
		nueva@gmail.com -passfile2 ./nuevapass -ssl2 -syncinternaldates -split1\
		 500 -split2 500 --fastio1 --fastio2 -authmech1 LOGIN -authmech2 LOGIN\
		 -useheader 'Message-Id' -useheader 'X-Gmail-Received'\
		--exclude "(Junk|Spam|Trash|Publicidad|All Mail|dspace-list|wp-admin|spam)"\
		 --allowsizemismatch --nofoldersizes --skipsize --fast --timeout 100

		sleep 5
	  done

-----------

## Migrar Contactos

En la página de gmail, en la cuenta *antigua*, vamos a *Contactos*, en *Más
Opciones* seleccionamos *Exportar* y lo descargamos en formato Google CSV.

![export-import-contacts][]

En la cuenta nueva, realizamos el proceso inverso, *Más opciones*->*Importar* y seleccionamos el CSV.

## Migrar feeds Google Reader

Los feeds suscritos en Google Reader pueden exportarse también en un fichero.
Dentro del Reader, logeado con la cuenta *antigua*, vamos a *Opciones de Reader*->*Importar/Exportar* y descarga el fichero tipo **OPML**.

![export-reader][]

Una vez conseguido ese fichero, vamos al reader de la cuenta *nueva*, también a
*Opciones de Reader*->*Importar/Exportar*  y subimos el fichero **OPML** que antes
descargamos.

## Migrar calendarios

Los calendarios realmente no están *dentro* de tu cuenta, sino que pueden ser
compartidos por varias personas con privilegios similares a los del usuario que
los creó, de forma que lo único que hay que hacer realmente es compartirlo con
la cuenta de correo nueva con privilegios de administrador.

![share-calendar][]

## Migrar filtros

Debe activarse la importación/exportación de filtros en el labs para ambas cuentas:

![export-filter][]


Después de activarla solo tenemos que exportar en la página de la cuenta
antigua e importar en la de la nueva.

![import-filter][]


## Intentos fallidos

Lo intenté antes con [thunderbird][thun], como hacen [aquí][thun-meth],
sincronizando ambos correos y arrastrando los directorios de una a otra cuenta,
y lo hace bien, pero no mantiene más de una etiqueta/carpeta por correo. Y
luego con [gmail-backup][gb], que no me conservó las etiquetas (y aparte es
propietario, te dan solo el binario y necesita Python 2.5, un peñazo).


[gb]: http://gmail-backup.com
[gb-dwn]: http://www.gmail-backup.com/gmail-backup-0-104
[thun]: http://getthunderbird.com
[thun-meth]: http://www.twistermc.com/27915/move-gmail-to-gmail/
[py2.5]: http://www.python.org/download/releases/2.5.6/
[im]: http://imapsync.lamiral.info/README
[filters]: http://lifehacker.com/5164463/import-and-export-your-gmail-filters
[gyb]: http://code.google.com/p/got-your-back/

[activate-imap]: ./img/activate-imap.png
[export-import-contacts]: ./img/export-import-contacts.png
[export-reader]: ./img/export-reader.png
[share-calendar]: ./img/share-calendar.png
[export-filter]: ./img/export-filter.png
[import-filter]: ./img/import-filter.png
