---
title: Edición de PDF
date: 2011-02-08 22:52
category: dev
tags: burocracia, editor, flpsed, formularios, linux, pdf, pdfedit, scribus
---

A la hora de lidiar con burocracia y rellenar formularios, lo ideal
es escribir directamente sobre el fichero, de manera que al
imprimirlo sea perfecto y no tengamos siquiera un machón de
bolígrafo. Es mucho más cómodo, ya que de esta manera pueden
editarse en caso de error y volver a imprimir tranquilamente,
evitando correcciones y tachones poco estéticos.   
  
Cuando el formulario está en un formato editable (.doc, .odt) no
hay problema alguno, pero si está en PDF la cosa se complica. Lo
que hacía antes era tomar capturas del PDF como imágenes, editarlas
con GIMP y después imprimirlas. Sin embargo el procedimiento es
primitivo, lento, dificultoso y debes guardar ficheros aparte de
GIMP si necesitas corregir cualquier error. Lo ideal es escribir
directamente en el PDF y generar uno nuevo que puedes editar otra
vez y transportar cómodamente.   
  
Todo el software que comento aquí está cómodamente disponible en
los repositorios de Debian. De manera que con un sudo apt-get
install lo tendremos funcionando rápidamente.
### Herramientas de edición PDF

#### pdfedit
  
**pdfedit** es una herramienta grande y compleja que permite editar
muchos aspectos de un PDF, incluyendo el añadir texto nuevo que es
lo que buscamos.   
Sin embargo su interfaz es un desastre, es complicado y sobre todo
tan lento que es inusable. Cada operación podía tardar cerca de 10
segundos, y cuanto más lo usaba, iba a peor. El formulario de mi
Erasmus lo rellené con este programa y tardé cerca de una hora. No
lo dejaba porque ya iba por la mitad y había invertido demasiado
tiempo como para tirarlo a la basura. Muy frustrante.   
  

<div class="img-center">

![Usando pdfedit][]

<sub>Usando pdfedit con mi formulario. La interfaz es
demasiado... 'ingenieril'.</sub>

</div>

#### Scribus
  
**Realmente no he probado *Scribus* * ya que **no llegó a abrir los PDF que
quería** editar. Se supone que el programa acepta como entrada el formato pdf y
permite editarlos y exportarlos de nuevo, sin embargo no le gustó los que tenía
y el muy señorito se negó a abrirlos aduciendo que se encontraban en un formato
inaceptable. Aún así,**su interfaz tenía muy buena pinta**, de manera que
quizás sea una opción para más adelante con otros ficheros.   

<div class="img-center">
  
![Error al iniciar scribus][]

<sub>Error al iniciar Scribus con el pdf que quería editar</sub>

</div>

#### flpsed
  
Con **flpsed** tenemos un ganador. Es exactamente lo que buscaba,
un editor de PDF/PostScript **muy ligero, ágil y sencillo**. Solo
permite abrir PS/PDF y añadir/editar texto, pero es que es
precisamente lo que buscaba. Solo permite cambiar el tamaño y el
color de la letra, de manera que para muchos puede quedarse corto
si pretenden realizar algo complejo o visual, pero para el uso para
el que busco un editor de PDFs es sencillamente perfecto. En menos
de 5 minutos tenía todos mis datos puestos sobre las casillas que
se dejan para escribir en el formulario. Después, permite exportar
tanto a PS o a PDF.   
  

<div class="img-center">

![flpsed en acción][]

<sub>"Editando un formulario PDF con flpsed"</sub>

</div>
  
Otra opción interesante de **flpsed** es que también exporta a PS,
de manera que podemos exportar nuestro PDF a PS, abrirlo con GIMP,
editar algún campo si necesita tachaduras o algún tipo de grafismo
más complejo (¿Estampar una firma?), guardarlo en PS y volverlo a
abrir con **flpsed** para terminar la edición y volverlo a pasar a
PDF.   
Este [post][] me ayudó mucho a la hora de orientarme y probar estos
programas.


  [Usando pdfedit]: ./img/pdfedit-300x239.png "pdfedit"
  [Error al iniciar scribus]: ./img/scribus-300x239.png "scribus"
  [flpsed en acción]: ./img/flpsed-300x125.png "flpsed"
  [post]: http://www.cyberciti.biz/tips/open-source-linux-pdf-writer.html
