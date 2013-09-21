---
title: Problemas con syslinux y usb-creator-gtk
date: 2010-10-06 04:48
category: linux
tags: install, linux, syslinux, ubuntu, usb, usb-creator-gtk
---

<div class="img-right">

![El causante de mis penas][]

<sub>El causante de mis penas</sub>
</div>

Hoy decidí liarme la manta a la cabeza e instalar la beta de ubuntu
10.10, y para ello intenté hacer un usb autoarrancable empleando
usb-creator-gtk, con el que es muy sencillo crear uno a partir de
una imagen .iso de una distribución.   
  
Una vez que terminaba el proceso de crear el pendrive
autoarrancable, reiniciaba y me encontraba con el siguiente error:
  
`Unknown keyword in configuration file`   
  
Y no arrancaba.   
  
Tras [leer un poco][] parece ser que es un error debido a una
versión antigua de syslinux, así que me lancé a buscar los deb de
una versión más nueva que funcionase.   
  
Desinstalé syslinux (también desinstala syslinux-common,
usb-creator-gtk y usb-creator-common)   
`sudo apt-get remove syslinux`   
  
Instalé a partir de los deb más nuevos de [syslinux][] y
[syslinux-common][]   
  
Y por último volví a instalar usb-creator-gtk   
  
`sudo apt-get install usb-creator-gtk`   
  
Y ahora si, ya funcionó el arranque convenientemente.   
  
Todo esto me trajo un buen rato de cabeza, pues antes comprobé el
hash de la imagen para saber que no estaba corrupta, cambié de usb,
lo formateé, miré la bios de mi portátil y además cada vez que se
genera un usb autoarrancable a partir de una iso el proceso tarda
media vida. Si a ello le unimos una conexión a internet muy
deficiente, el proceso completo me cabreó lo que no hay escrito y
me llevó 4 horas.


  [El causante de mis penas]: ./img/300px-Usb-creator1-287x300.png "Usb Creator"
  [leer un poco]: http://www.khattam.info/solved-syslinux-unknown-keyword-in-configuration-file-2010-09-01.html#more-1070
  [syslinux]: http://mirrors.kernel.org/ubuntu/pool/main/s/syslinux/syslinux_4.01+dfsg-3ubuntu1_amd64.deb
  [syslinux-common]: http://mirrors.kernel.org/ubuntu/pool/main/s/syslinux/syslinux-common_4.01+dfsg-3ubuntu1_all.deb
