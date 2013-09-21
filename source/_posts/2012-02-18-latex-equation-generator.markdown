---
title: Generate LaTeX Equations
date: 2012-02-18 22:00
category: linux
tags: linux, latex, pdf, svg
lang: en
---

![latex-logo][]

While rebuilding this blog with [pelican][] from a xml export of the previous Wordpress
version, I had to face how to regenerate the LaTeX equations inserted within some
posts.

Wordpress used to take care of the equation generation through the use of a plugin, taking as input the code between `[latex]` labels like this:

	code-block::latex
	[latex size="4"]
		\frac { \sum{(nota \cdot creditos)} }
			  { \sum{creditos} } 
	[/latex]

Generating an image with the LaTeX result and placing it whithin the post:

<img src="./img/latex-example-equation.svg" width="250px"/>

In order to replicate the same (command-line way) so LaTeX equations could be
inserted in my posts as images, I came up with the following script:

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

Which generates a LaTeX document on the fly with no styles at all, compiles it
to *pdf* using `pdflatex`, eliminates all margins using `pdfcrop` and converts
it to the final svg image with `pdf2svg`. 

The result can be inserted in the post as an svg image. I'm still wondering if this could be a desirable extension for the markdown reader of [pelican][]...

[latex-logo]: ./img/LaTeX_logo.png
[pelican]: http://pelican.readthedocs.org/en/2.7.2/index.html 
