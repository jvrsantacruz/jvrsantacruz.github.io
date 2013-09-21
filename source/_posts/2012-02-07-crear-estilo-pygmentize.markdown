---
title: Generar CSS con Pygmentize
date: 2012-02-07 20:40
category: dev
tags: css, pygments, pygmentize, highlight, html, webdev
---

Para el coloreado de código de este blog empleo la herramienta [pygmentize][pyg], la
cual es capaz de generar un fichero css con el estilo con el que quiero que
salga el texto de los programas.

Para generar un estilo usar:

	code-block::bash
	pygmentize -f html -S borland -a .codehilite > pygment.css

Donde `borland` es el estilo y `.codehilite` es la clase a emplear para
localizar el código en la página posteriormente. 
[Otras cosas][pyg-cmd] que pueden hacerse con la línea de comandos.

[pyg]: http://pygments.org
[pyg-cmd]: http://pygments.org/docs/cmdline/
