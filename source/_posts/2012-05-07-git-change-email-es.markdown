---
title: Cambiar email en muchos commits de git
date: 2012-05-07 18:12
category: linux, git
lang: es
---

Quería *replicar* un antiguo repositorio git en el cual había usado mi anterior email de cuando
curraba en la universidad. Para ello debía reescribir todos los commits y cambiarles el email. Es
bastante fácil, pero es mejor hacerlo sobre una copia del repositorio, y si metes la pata siempre
puedes hacer un `git reset --hard HEAD` y listo.

Para el rewrite solo hay que usar `git filter-branch`, que permite este tipo de cosas peligrosas
como son las reescrituras en masa.

	code-block::bash
	git filter-branch --env-filter '
	 GIT_AUTHOR_EMAIL=<new.email@gmail.com>
	 GIT_COMMITER_EMAIL=<new.email@gmail.com>' --all

Digo que son peligrosas porque esto le pondrá tu email a *absolutamente todos* los commits, si
tienes código de colaboradores, te cargarás sus emails. Si ese es tu caso, pon alguna condición en
el filtro para evitar sobreescribir lo que no quieres.
