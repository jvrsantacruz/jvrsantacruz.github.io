---
title: Recuperar un stash borrado en git.
date: 2012-03-21 19:00
category: linux
tags: git
lang: es
---

Si os pasa como a mi que confundís `git stash pop` (aplica y borrar un stash) con `git stash drop`
(que borra directamente), no lloreis aún. `git stash drop` imprime el hash del commit que estaba en
stash y que hemos borrado, y con el puede recuperarse.

	git stash drop
	Dropped refs/stash@{0} (e692be2bc305348380c7c71a80867539babad3d7)

Si tienes el hash aún, puedes hacer simplemente:

	git stash apply e692be2bc305348380c7c71a80867539babad3d7

Y salvarlo.

La verdad es que he pasado mal un ratillo.
