---
title: I want my vim back.
date: 2010-08-30 22:03
category: dev
tags: administration, linux, bash, servers, ssh, sshfs, vim 
---

Desde hace unos dias en el trabajo debo editar ficheros situados en
un servidor antiguo, en concreto la máquina corre una RedHat de
2002 con un Kernel viejuno atrasado unas cuantas versiones... y sin
vim instalado.   
  
Ya está, la hemos liado. ¿Como voy a trabajar bien con ficheros
remotos sin vim?   
Si, lo sé,  está vi, pero una vez acostumbrado a la buena vida (que
si macros, el modo visual, indentación, plugin aquí, autocompletado
allá...) aquello es, digamos, poco atractivo, sobre todo al
trabajar con html antiguo y susio generado con  Frontpage, que si
no lo coloreas e indentas, no hay Dios que lea ná.   
Tablas y bloques sin cerrar, atributos de estilo ad infinitum,
identación nula, elementos repetidos, frases cortadas a mitad de
párrafo del tipo

	code-block::html
	<p>Esto es un párrafo</p><p>cortado por la mitad</p>
	<p>por un editor automático de html</p>
	<strong>Yo te maldigo</strong>

  
Entonces que hago ¿Me bajo todos esos html y los subo continuamente
via scp a cada edición? Que coñazo.   
  
Una opción mucho mejor sería instalar vim... aunque sin privilegios
de administrador, tendría que poner el runtime en el home. Y a ello
fuí.   
  
Se va uno a [vim.org][], se descarga el código de *vim72* y hala a
compilarlo:   

	code-block::bash
	$ tar -zvjf vim72.tar.bz2 
	$ cd vim72
	$ ./configure && make
  
Nah, ni para atrás, el `configure` me echaba por tierra la
compilación quejándose de una versión antigua del gcc. Bueno, pues
lo intentamos con la 6.4, que para eso están todas las versiones
disponibles en la página.   

	code-block::bash
	$ tar -xjvf vim64.tar.bz2 
	$ cd vim64 
	$ ./configure && make
  
Y *vualá*, aunque el configure decia que no a muchas cosas, supongo
que ninguna sería vital, pues compiló y acabé con un ejecutable en
`vim64/src/vim`. 
  
Añadimos el ejecutable al PATH en nuestro `~/.bashrc`:   
  
	code-block::bash
	PATH:=${PATH}:/home/arl/vim64/src/vim 
  
Recargamos el ficero *.bashrc* con:   
  
	code-block::bash
	$ bash
  
Listo. Está todo de medio ganchete y no tiene ni la mitad de
funcionalidades, pero oye... lo básico funciona.   
  
Aún así no estaba contento. No me hayaba en plenitud. Quería editar
ese código con **MI** `vim`, con **MI** `.vimrc`, mis alias, mis
plugins y manías.   
  
Y me dije, *estaría bien* poder montar el sistema de ficheros del
servidor en el mio propio, y así editar los archivos como si
estuviesen en local, con mis propias herramientas.   
  
Dicho y Googleado, la maravilla se llama `sshfs` y sirve para...
(¡TACHAN!) montar un sistema de ficheros remoto sobre ssh.   
  
La sintaxis
es:

	code-block::bash
	sshfs user@servidor:/ruta-a-montar   carpeta
  
De manera que en mi sistema creo la *carpeta* servidor, y monto
allí la ruta remota en la que tengo que trabajar. Así la tengo
directamente en mi sistema y hago con los ficheros lo que me da la
gana, que transparentemente por debajo se transmiten via ssh.   
  
Ojo que hay que tener en mente que las operaciones con ficheros y
directorios implican comunicación por red, y por ejemplo un grep
recursivo puede tardar cierto tiempo ya que tiene que transmitir el
contenido de TODO por la red. Para ese tipo de tareas sigo
accediendo por el prompt ssh. También me resulta un poco confuso el
modo en que se manejan los permisos, ya que el servidor web está
basado en grupos, y esos grupos como no existen en mi sistema se
simulan de una manera extraña.   
  
Para desmontar podemos usar:   
  
	code-block::bash
	fusermount -u carpeta
  
Y, como es un coñazo montar y desmontar todo el rato, nos marcamos
unas funciones para nuestro *.bashrc*:   
  
	code-block::bash
	function mservidor ()  {  mkdir -p carpeta && sshfs user@servidor:/ruta-servidor  carpeta  }
  
y para desmontar:   
  
	code-block::bash
	function uservidor () {  fusermount -u carpeta && rmdir carpeta  }
  
  
Totalmente recomendado, oigan, edito ficheros antiguos en un
servidor antiguo más a gusto que un arbusto.

[vim.org]: http://www.vim.org
