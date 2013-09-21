---
title: Generar Ecuaciones LaTeX 
date: 2012-02-19 10:00
category: linux
tags: linux, latex, pdf, svg
lang: es
---

![latex-logo][]

Mientras reconstruia este blog en [pelican][] a partir de una exportación del
blog antiguo en Wordpress, tuve que resolver como volver a generar las
ecuaciones en LaTeX que había insertado en esos posts.

Wordpress me generaba esas ecuaciones como imágenes de forma automática,
simplemente poniendo el código entre llaves `[latex]`, de esta manera:

	code-block::latex
	[latex size="4"]
		\frac { \sum{(nota \cdot creditos)} }
			  { \sum{creditos} } 
	[/latex]

Generando una imagen como resultado:

<img src="./img/latex-example-equation.svg" width="250px"/>

Para poder hacer lo mismo (preferiblemente en consola) y poder insertar
ecuaciones en LaTeX en los posts como imágenes, escribí este pequeño script:

	code-block::bash
	#!/bin/bash

	TMPNAME=tmpfile
	TMP=/tmp/$TMPNAME

	echo "\documentclass[14pt,english]{article}" > $TMP
	echo "\usepackage[utf8]{inputenc}" >> $TMP
	echo "\thispagestyle{empty}" >> $TMP
	echo "\begin{document}" >> $TMP
	echo "$ ${1} $" >> $TMP
	echo "\end{document}" >> $TMP

	pdflatex $TMP
	pdfcrop $TMPNAME.pdf $TMPNAME.pdf
	pdf2svg $TMPNAME.pdf $2
	rm -f $TMPNAME.pdf $TMP

El cual genera un documento LaTeX al vuelo, sin ningún tipo de estilo, lo
compila en *pdf* usando `pdflatex` y elimina todos los márgenes usando
`pdfcrop`, convirtiendo lo que queda (solo la ecuación) a *svg* con `pdf2svg`,
que es la imagen que finalmente coloco.

[latex-logo]: ./img/LaTeX_logo.png
[pelican]: http://pelican.readthedocs.org/en/2.7.2/index.html 
