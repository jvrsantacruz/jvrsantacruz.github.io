---
title: Android Screencast
date: 2011-02-03 23:12
category: android
tags: android, avidemux, development, game, screencast, video, videogame
---

<div class="img-center">

![image][]   

</div>

Estas navidades para una asignatura, *"Mobile Technology"*
desarrollé un pequeño videojuego de prueba para **Android**. Desde
hace unos días vengo buscando la manera de grabar un vídeo del
juego sin necesidad de emplear una cámara externa (cutre y baja
calidad) o grabar el emulador (el cual va lentísimo).
**Aún no lo he conseguido**, pero sin embargo he tenido una
aproximación interesante mediante la aplicación
**[Android Screencast][]** con la cual he podido grabar un video
pero que es insuficiente para las necesidades de un videojuego, que
funciona a un framerate alto.   

<div class="img-center">

<iframe src="http://player.vimeo.com/video/19533141?title=0&amp;byline=0&amp;portrait=0" width="590" height="330" frameborder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe><p><a href="http://vimeo.com/19533141">Android Screencast showing a tiny test videogame</a> from <a href="http://vimeo.com/user5909254">Javier Santacruz</a> on <a href="http://vimeo.com">Vimeo</a>.</p>

</div>
  
La aplicación **Android Screencast** sirve para manejar el teléfono
desde el ordenador. Permite navegación de archivos, manejo de
teclado y ratón (solo root), screenshots y **grabación de video**.
Los pasos para utilizarlo son:
Tener el sdk de android instalado
Tener Java SDK 6 instalado
Tener conectado el teléfono y reconocido por el server del sdk
Ejecutar el archivo jnlp del programa
  
El programa automáticamente intenta 6 conexiones al teléfono
conectado y tras eso desiste. Mis pasos para instalarlo fueron:

	arl@lap$ cd android-sdk/platform-tools
	arl@lap$ ./adb stop-server
	arl@lap$ sudo ./adb start-server
	arl@lap$ ./adb devices
	List of devices attached
	HT066P800146    device

  
Con la secuencia de comandos anterior conecto mi *Nexus One* al pc.
Tengo que **iniciar el demonio como root** o de otra manera no me
coge el teléfono. Un día de estos debería escribir una regla para
que se monte solo con los permisos adecuados para el sdk.   
  
Una vez conectado, ejecutamos AndroidScreencast:
    gnome-open androidscreencast.jnlp

  
Y debería abrirse y detectar el teléfono solo, mostrando la
siguiente pantalla en mi escritorio:   
  
<div class="img-right">

![image][1]

<sub>Escritorio mostrado en pantalla </sub>

</div>
  
Ahí podemos ver todo lo que sucede en la pantalla del teléfono en
'tiempo real', tenemos acceso al arbol de fichero y un par de cosas
más. Sin embargo no va fluido. Probablemente porque se basa en la
toma de screenshots, que es horriblemente lenta. Para tomar una
screen de la pantalla, el teléfono se toma su tiempo, tardando
varios milisegundos. El refresco de la pantalla es mucho más
rápido, de manera que todas las animaciones que se muestren en el
teléfono saldrán cortadas o no saldrán en la pantalla.   
  
En la parte superior izquierda podemos ver el botón **Grabar** que
al pulsar nos preguntará donde queremos guardar el vídeo y
comenzará el vídeo, quedando el boton pulsado. Para parar la
grabación simplemente pulsar de nuevo sobre el botón y parará.   

<div class="img-left">
  
![image][2]

<sub>Captura del juego</sub>

</div>
  
El formato de salida es .mov y empleé **Avidemux** para editarlo.
Debo rectificar una pequeña injusticia que cometí contra
**Avidemux** en mi anterior entrada. No es tan limitado como me
pareció la otra vez, soporta una buena serie de filtros que hacen
que para ediciones menores sea mucho más que suficiente. En este
caso lo único que quería era rotar la imagen y suavizarla
ligeramente, para lo cual funcionó muy bien.   
  
La única dificultad fué encontrar un encoder que me aceptase .mov
como entrada para generar la salida. Menos mal que **Avidemux**
soporta bastantes formatos y el MPEG4 (xvid) me sirvió.   
  
<div class="img-center">

![Editando el vídeo con Avidemux][]
<sub>"Editando el video con
Avidemux"</sub>
</div>
  
Finalmente, el vídeo quedó subido a **Vimeo** y
[podeis verlo aquí][].   
  
[Android Screencast showing a tiny test videogame][podeis verlo aquí]
from [Javier Santacruz][] on [Vimeo][].


  [image]: ./img/header-android-screencast.png "header-android-screencast"
  [Android Screencast]: http://code.google.com/p/androidscreencast/
  [1]: ./img/android2-173x300.png "android-screencast-1"
  [2]: ./img/screencast-android-173x300.png "android-screencast-2"
  [Editando el vídeo con Avidemux]: ./img/android-screencast-avidemux-265x300.png "android-screencast-avidemux"
  [podeis verlo aquí]: http://vimeo.com/19533141
  [Javier Santacruz]: http://vimeo.com/user5909254
  [Vimeo]: http://vimeo.com
