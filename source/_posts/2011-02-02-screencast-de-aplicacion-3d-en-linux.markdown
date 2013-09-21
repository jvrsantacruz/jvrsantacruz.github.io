---
title: Screencast de aplicación 3D en Linux
date: 2011-02-02 20:09
category: projects
tags: avidemux, behaviour, game, ia, istanbul, kino, lives, video, vimeo, xvidecap
---

<div class="img-center">

<iframe src="http://player.vimeo.com/video/19469223?title=0&amp;byline=0&amp;portrait=0" width="590" height="230" frameborder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe><p><a href="http://vimeo.com/19469223">Flocking Behaviour 2D Demo</a> from <a href="http://vimeo.com/user5909254">Javier Santacruz</a> on <a href="http://vimeo.com">Vimeo</a>.</p>

</div>

Tras realizar un trabajo para una asignatura en la que escribí una
pequeña aplicación en 3D, quería hacer un pequeño video de demo de
su funcionamiento. El objetivo era grabar la aplicación mientras
corría y añadirle al video algún que otro diagrama explicativo
conforme iban sucediéndose las imágenes, de forma que quedase claro
que es lo que realmente realicé en el trabajo y cuales son sus
objetivos.   
  
Lo que acabé usando fue:   
**Xvidcap** Para la grabación del escritorio.
**Avidemux** y **LiVe** Para la edición del video.
  
Sin embargo por el camino probé algunas alternativas, ya que el
campo de la edición de video parece estar bastante verde y no me
resultó nada sencillo poder montar lo que quería.   
### Grabar la aplicación funcionando

Como herramienta de grabación de escritorio mi primera opción fué
**[Istanbul][]**, el cual es un programa de captura de video de
escritorio muy sencillo e integrado con GNOME que puede encontrarse
en los repositorios.   
Prácticamente no tiene interfaz. Cuando se arranca se queda en la
barra de tareas como un botón rojo. Si pulsas sobre él, comienza a
grabar, y para al volver a pulsar.   
Tenía muy buena pinta y probablemente haga muy bien su trabajo para
grabar simplemente el escritorio, pero mi aplicación tiene gráficos
en 3D que sencillamente no aparecían en el video resultante, de
manera que lo descarté como opción.   
  
El siguiente fué el que me salió bueno: **[Xvidcap][]**. Muy
sencillo también consiste en una pequeña barra y un cuadrado rojo
que marca el área de la pantalla que va a ser grabada. En mi
sistema tenía un bug importante que no me permitía abrir el panel
de opciones, sin embargo, afortunadamente, las opciones por defecto
me funcionaron bien. Tras grabar el video, me generó un mpg a 10fps
suficientemente bueno como para evitar trompicones. La parte
negativa es que grabar el escritorio es una operación terriblemente
costosa que degrada el rendimiento del sistema hasta el punto en
que la aplicación se notaba que renqueaba.   
  
### Editar el video grabado

A la hora de editar el video pensaba emplear el editor web que trae
Youtube, que es razonablemente rápido y muy efectivo para poner
textos simples. Ya lo usé antes en este video de mi Proyecto de Fin
de Carrera **[idiginBPEL][]** con buen resultado. Sin embargo,
tenía ganas de probar la plataforma de **[Vimeo][]**, que no trae
tales cosas, de manera que comencé la búsqueda de mi herramienta de
edición de video.   
#### Avidemux

Primero probé **[Avidemux][]** que funciona muy bien y se ve
software bastante robusto, pero que no permite otra cosa salvo
cortar, pegar, redimensionar y poco más. En realidad, que haga esto
y lo haga bien, no es poco.   
#### Petazos con Kino

  
Mi segunda opción fué **[Kino][]** que tiene una buena interfaz y
parece plagado de opciones avanzadas; Sin embargo fué un fracaso
total, la aplicación me lanzaba excepciones con errores de coma
flotante y se caia completamente cuando intentaba exportar video,
fuese al formato que fuese. Esto es lo que yo llamaría una función
vital para un programa de video, de manera que me obligó a
descartarlo.   
#### Funcionalidad con sufrimiento en LiVe

  
Mi tercera y última ha sido **[LiVe][]**. LiVe tiene una interfaz
horrorosa y completamente antiintuitiva, sin embargo tras unos
minutos de horror intenso he aprendido a manejarlo y a hacer lo que
pretendía con el. La interfaz, en serio, es un obstáculo. Menús en
popups con barras de desplazamiento que incomodan, scrolls que no
funcionan, inputs numéricos sin opciones de sincronización en los
que hay que ir editando los valores uno a uno. También tiene una
completa ausencia de interacción con el ratón a la hora de aplicar
los efectos como mover una imagen.   
  
A pesar de ser tan feo e inusable, parecía funcionar. Eso sí, no a
la primera. Inicialmente el programa petaba cuando intentaba abrir
los mpg generados con **Xvidcap** de manera que los convertí usando
**Avidemux** a avi y eso si pareció gustarle.   
#### Más lento que el caballo del malo

  
Una vez comencé a editar, me di cuenta con horror, que no soporta
deshacer acciones más allá de la última realizada. Esto significa
que tienes que ir realizando backups cada poco tiempo. Un backup
significa copiar totalmente el vídeo realizado y en mi caso tardaba
entre 2 o 3 minutos. Después, cada efecto aplicado es escrito en
disco directamente y procesa el video completo, de manera que
cualquier acción me tomaba también entre 40 segundos y 2 minutos. A
ese ritmo, tardé unas **4 o 5 horas** en cortar el video, incluir
un par de imágenes que entrasen y saliesen animadamente con efectos
de alpha. Es cierto que estaba haciendo otras cosas como
escribiendo funciones recursivas a petición de compañeros y
comiendo galletas, pero fué realmente desesperante.   
#### Añadir imágenes

  
Los pasos que seguía con el programa para añadir las imágenes .png
generadas con Inkscape era realizar una selección de frames y
aplicar un efecto de tipo 'image overlay' en el cual se me permitía
poner un tamaño, posición y alpha inicial y final, pudiendo dejar
la imagen fija o animándola. A la hora de realizar animaciones de
entrada-salida, me cabreó bastante que las posiciones para las
imágenes no soportasen valores negativos. Esto hace que no puedas
mover una imagen por el borde superior-izquierdo hasta hacerla
desaparecer de la pantalla y me fastidió lo que tenía en mente.
Para generar nuevos frames elegía 'generate colour frames' y añadía
frames de color blanco que insertaba antes o después de la
selección.   

#### Texto sin anti-aliasing
  
Hacia el final del video pretendía introducir mi nombre y la
dirección de mi blog. Sin embargo el texto que añade al vídeo va
sin anti-aliasing de manera que mostraba bordes tan irregulares que
me dio vergüenza y lo sustituí por una imagen (que no me encajaba y
terminé descartando).   

#### Fallos al exportar
  
A la hora de exportar, **LiVe** si que está bien equipado y soporta
múltiples encoders y muchos formatos. Sin embargo, como en cada
paso debe haber problemas, dolor y desconcierto, cada formato tiene
sus restricciones en cuanto a framerate y resolución. Primero lo
intenté con el formato libre OGG Theora codificándolo en un .ogv
pero al visualizar el video codificado, mostraba problemas graves
con las transparencias de algunas imágenes, que salían en amarillo.
Fueron momentos muy frustrantes porque ya estaba realmente cansado,
el video se reproducía bien en el programa de edición, pero al
exportar mostraba cosas extrañas que no podía obviar porque eran
demasiado llamativas.   
  
Ahí comenzó mi búsqueda de un encoder y formato adecuados. Cada vez
que quería probar un formato diferente, debía cambiar el framerate
y la resolución. En uno de los cambios, cambiar la resolución llevó
20 minutos largos más el tiempo de codificación posterior que
depende del formato destino pero que no baja de 5 minutos.   
  
Finalmente probé con el encoder 'multi\_encoder' que es el que
viene preseleccionado por defecto y vídeo en MPEG a la más alta
calidad posible. Tras 35 minutos de espera, el video por fin salió
limpio.   
#### Vimeo

  
La subida a **Vimeo** fué bastante sencilla y rápida y por una vez,
no me falló el sistema de subida en flash, que normalmente no suele
funcionar bien con Firefox en Linux. Lo cierto es que la página
tiene un excelente diseño, es agradable de usar y va como un tiro.
Todo bien sin ningún problema y el video
[quedó subido aquí][Flocking Behaviour 2D Demo].   
  
### Conclusión

Esta ha sido la primera 'demo' que he editado yo mismo sobre mi
propio trabajo, y definitivamente necesito encontrar herramientas
nuevas para la próxima vez. **Xvidcap** funciona bien, pero
empleando **LiVeS** me sentí como si estuviese editando el video
empleando un palo y piedras de silex afiladas.   
  
Con respecto al video, si hubiese podido deshacer/rehacer con
comodidad, podría haber pulido mucho mejor los efectos,
reencuadrado el tamaño y haberlo tocado hasta que hubiese quedado
bien, pero con lo trabajoso que era cada paso, cometí un par de
errores pero así se han quedado.   
  
A la hora de elegir herramientas para realizar la grabación y la
edición estos dos posts me fueron de mucha ayuda:

- **Screencast para linux:** [http://www.linuxhaxor.net/?p=815][]
- **Editores de video para linux:**
- [http://www.cyberciti.biz/faq/top5-linux-video-editing-system-software][]
- **Xvidcap:** [http://xvidcap.sourceforge.net/][Xvidcap]
- **Avidemux:** [http://fixounet.free.fr/avidemux/][Avidemux]
- **LiVeS:** [http://lives.sourceforge.net/][LiVe]

  [Flocking Behaviour 2D Demo]: http://vimeo.com/19469223
  [Javier Santacruz]: http://vimeo.com/user5909254
  [Vimeo]: http://vimeo.com
  [Istanbul]: http://live.gnome.org/Istanbul
  [Xvidcap]: http://xvidcap.sourceforge.net/
  [idiginBPEL]: http://www.youtube.com/watch?v=xMHqlmfxLjc
  [Avidemux]: http://fixounet.free.fr/avidemux/
  [Kino]: http://www.kinodv.org/
  [LiVe]: http://lives.sourceforge.net/
  [http://www.linuxhaxor.net/?p=815]: http://www.linuxhaxor.net/?p=815
  [http://www.cyberciti.biz/faq/top5-linux-video-editing-system-software]: http://www.cyberciti.biz/faq/top5-linux-video-editing-system-software
