---
title: Renombrar etiqueta usb linux
date: 2012-02-08 22:00
category: dev
tags: linux, administration, mlabel, mount
lang: es
---

En este caso es una tarjeta *SD* en FAT32 que no tiene label y al montarla
Ubuntu la llama `9016-4EF8`, lo cual no me dice mucho y me gustaría cambiarlo.
La herramienta para cambiar labels de FAT32 se llama `mlabel` y viene en el
paquete `mtools`.

1. Averiguamos que dispositivo es, para llamarlo por su nombre después.

		$ df /media/9016-4EF8
		Filesystem           1K-blocks      Used Available Use% Mounted on
		/dev/sdb1             31263744        32  31263712   1% /media/9016-4EF8
	Se llama `/dev/sdb1`.

1. La desmontamos para trabajar con el a gusto, que estaba en `/media/9016-4EF8`.

		$ sudo umount /media/9016-4EF8

1. Para usar `mlabel` necesitamos que el dispositivo tenga un nombre, asi que
  copiamos la config por defecto de `mtools` y le añadimos nuestro dispositivo.
  Lo llamaremos `i`.

		$ cp /etc/mtools.conf ~/.mtoolsrc
		$ echo 'drive i: file="/dev/sdb1"' >> ~/.mtoolsrc

1. Renombramos la partición con `mlabel`.

		$ sudo mlabel i:mi_tarjeta

**Nota**: si `mlabel` os escupe algo parecido a esto:

	code-block::text
	Total number of sectors (62543872) not a multiple of sectors per track (63)!
	Add mtools_skip_check=1 to your .mtoolsrc file to skip this test

Sed obedientes y simplemente añadid `mtools_skip_check=1` al fichero.

	$ echo 'mtools_skip_check=1' >> ~/.mtoolsrc

Y listo, al conectar el dispositivo se monta solo con el nombre bueno:

	code-block::console
	$ ls /media/
	MI_TARJETA
