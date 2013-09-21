---
title: Problema con Guake en Ubuntu Maverick 10.10
date: 2010-10-07 17:06
category: dev
tags: fix, guake, linux
---

<div class="img-right">

![image][]

</div>

Ayer me fijé que mi consola de Guake no estaba igual
que siempre, sino que fallaba al ejecutar ciertas aplicaciones y la
pantalla se desdibujaba cuando empleaba el comando man. En concreto
recibía el siguiente error:   
  
`WARNING: terminal is not fully functional`
  
Trasteando, comparé la variable de entorno TERM de la consola de
guake con la de la gnome-console normal, que funcionaba bien, y en
efecto la consola de Guake mostraba una terminal diferente   
  
En Guake:   
`echo $TERM  dumb`   
  
En gnome-terminal:   
`echo $TERM  xterm`   
  
El error se podía solucionar temporalmente simplemente cambiando el
valor de la variable TERM a xterm   
`export TERM=xterm`   
de manera que inicialmente puse esa línea en mi .bashrc y listo.   
  
Sin embargo, buscando más información sobre el problema, encontré
el [track del bug][] en launchpad, donde se daba una solución
alternativa que me gusta más.   
  
Guake es un programa en python que se lanza mediante un script que
está en /usr/bin/guake de manera que lo único que hay que hacer es
modificar ese último script y añadir el export ahí.   
  
  	code-block::bash
	GUAKEPATH="/usr/lib/guake"  
	PYTHONPATH="$PYTHONPATH:$GUAKEPATH"  
	PYTHON="/usr/bin/python"  
	export TERM=xterm    
	exec -a guake $PYTHON -OO $GUAKEPATH/guake.py "$@"
  
  
Y santas pascuas.

  [image]: ./img/guake_logo.png "Guake"
  [track del bug]: https://bugs.launchpad.net/ubuntu/+source/guake/+bug/621927
