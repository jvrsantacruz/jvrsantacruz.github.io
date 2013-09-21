---
title: Establecer entorno de desarrollo JAVA ME en Linux
date: 2010-10-16 17:08
category: dev
tags: java, linux
---

Ahora que estoy metiéndome poco a poco en el mundo Java, estoy
aprendiendo a programar para plataformas móviles usando Java Mobile
Edition con la versión 2.5 (la 3.0 no es compatible con Linux).   
Aunque es muy sencillote, me ha costado un poco encontrar
información completa y clara sobre como establecer el entorno de
desarrollo, tanto usando un IDE como para programar a pelo. Estos
son los pasos que yo he seguido:   
  
Tenemos dos opciones:
Instalar solo el SDK y usar nuestro propio editor (vim).
Instalar un IDE con todo integrado (NetBeans).
  
Inicialmente opino que merece mucho la pena usar NetBeans ya que la
ayuda es tremenda a la hora de empezar, con los esqueletos,
diferentes apis, chequeo de tipos etc... pero el editor de texto
del NetBeans me pone de los nervios, de manera que se que en algún
momento, en cuanto adquiera cierto control sobre lo que estoy
haciendo, me pasaré a vim, así que **cubriré las dos opciones** a
la hora de realizar el tutorial.   
  
Ambas partes tienen en común que se necesita la maquina virtual
Java de Sun de **32 bits** de manera que si el sistema es de 64
tienes que instalarla e indicar donde se encuentra.
### Instalar Sun Java SE (Standar Edition) 32 bits (i586)

Descargar la versión de Java SE de 32 bits de la [página de Sun][]
Descomprimir el bin:   

	chmod +x jdk-6u22-linux-i586.bin  ./jdk-6u22-linux-i586.bin

Una vez descomprimido, yo lo coloqué en `/opt/jdk1.6.0\_22-32bit`
pero ya es cosa tuya.
  
Recuerda donde lo instalas porque vas a necesitar usar la ruta más
tarde.   
  
Ahora describiré el proceso completo de instalación para cada modo.
### Solo el SDK

Nos descargamos el Wireless Toolkit (WTK) de la [página de Sun][1]:
Ejecutamos el script:   

`./sun_java_wireless_toolkit-2.5.2_01-linuxi486.bin.sh`   

Donde tendremos que aceptar la licencia y
**nos preguntará la ruta del entorno java** que en mi caso como
dije antes es `/opt/jdk1.6.0\_22-32bit`. 
Después nos preguntará la ruta a donde queremos instalarlo. Yo lo
hice en `/usr/local/WTK2.5.2` pero puedes tenerlo donde quieras.
  
Ya debería estar instalado el WTK. Para crear proyectos,
compilarlos y ejecutarlos en el simulador se utiliza una aplicación
gráfica en java que trae el WTK llamada ktoolbar cuyo ejecutable se
encuentra en el directorio de instalación del WTK, en mi caso en
`/usr/local/WTK2.5.2/bin/ktoolbar`. 
  
Desde la aplicación se maneja ya todo. Solo tienes que crear un
proyecto, editar los fuentes con tu editor favorito y ejecutarlo
desde ahi. DONE.
### NetBeans

Descarga la versión de NetBeans que incluye el WTK y trae soporte
para desarrollo móvil [desde la página][].
La descomprimimos e instalamos.   

	code-block::bash
	chmod +x netbeans-6.9.1-ml-java-linux.sh  
	./netbeans-6.9.1-ml-java-linux.sh
  
Por defecto yo lo instalé en mi directorio home.  El emulador probablemente
fallará al tratar de ejecutar lo que compiles, de manera que tienes que
establecer la ruta del entorno java. En concreto se hace editando el script
`~/netbeans-6.9.1/mobility/WTK2.5.2/bin/emulator` y estableciendo la variable
javapathtowtk con la ruta del entorno.

	code-block::bash
	#!/bin/sh
	javapathtowtk=/opt/jdk1.6.0_22-32bit/bin/
  
Hay que tener especial cuidadito con que la ruta termine en
(..)bin/. Y con esto ya deberíamos tener instalado NetBeans con
soporte para móviles. Puedes crear un proyecto de ejemplo y
probarlo convenientemente.   
  
Espero que sirva :D   
Otras referencias:   
  
Solucionar error para 64b de NetBeans:
[http://forums.sun.com/thread.jspa?threadID=5447892][]   
Establecer y usar ktoolbar:
[http://www.linux.com/archive/articles/122050][]

  [página de Sun]: https://cds.sun.com/is-bin/INTERSHOP.enfinity/WFS/CDS-CDS_Developer-Site/en_US/-/USD/ViewProductDetail-Start?ProductRef=jdk-6u22-oth-JPR@CDS-CDS_Developer
  [1]: https://cds.sun.com/is-bin/INTERSHOP.enfinity/WFS/CDS-CDS_Developer-Site/en_US/-/USD/ViewProductDetail-Start?ProductRef=sun_java_wireless_toolkit-2.5.2_01b-oth-JPR@CDS-CDS_Developer
  [desde la página]: http://netbeans.org/downloads/
  [http://forums.sun.com/thread.jspa?threadID=5447892]: http://http://forums.sun.com/thread.jspa?threadID=5447892
  [http://www.linux.com/archive/articles/122050]: http://http://www.linux.com/archive/articles/122050
