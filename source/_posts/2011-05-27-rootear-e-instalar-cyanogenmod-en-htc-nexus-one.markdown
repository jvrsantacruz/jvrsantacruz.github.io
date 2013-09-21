Title: Rootear e instalar CyanogenMod en HTC Nexus One
Date: 2011-05-27 19:54
Category: Dev
Tags: Android, Rom, Nexus One, HTC, Cyanogenmod, Vodafone, Rootear


<div class="img-center">

![Rootear e instalar CyanogenMod en un HTC Nexus One][]

</div>

Desde hace casi un año disfruto de un Nexus One con el que me va
genial. Sin embargo, hasta ahora no había trasteado demasiado con
él, y aprovechando las vacaciones he decidido rootearlo y cambiarle
la ROM emigrando hacia un lugar un poco mejor. La ROM original que
traía de Google + Vodafone me daba un par de pequeños fallos en la
pantalla táctil, y con el cambio va bastante fino. De paso le di un
limpiado al teléfono que estaba llenito de basura.

  
La instalación no es difícil pero se puede hacer larga y la mayoría
de información distribuida por los foros es inconexa, antigua o
**incluso incorrecta**. Cada teléfono es un mundo y tiene sus
propiedades especiales. Este tutorial está lleno de apartados
opcionales aplicables *solo si falla* algún paso. Huelga decir que
esos pequeños arreglos esconden varias horas de frustración,
cabezazos contra la pared, foros y mucho, mucho Google.

  
El rooteo del teléfono eliminará tu derecho a la garantía de
fabricante. La cosa es que si tienes algún problema de hardware con
el teléfono y lo tienes que enviar a reparar, con restaurar la rom
original no debería haber problemas.

  
El proceso es el siguiente:
  
1. **Rootear**
	Unlock bootloader
	Rootear
  
2. **Instalar Cyanogen**
	Instalar Recovery
	Backup Nandroid
	Reseteo a valores de fábrica (Wipe)
	Instalar CyanogenMod
  
Mi terminal al momento de comenzar el proceso era un Nexus One con Build Number
*FRG83D* corriendo Android 2.2.1 Froyo.   Podéis consultar esta información en
`Settings-\>About Phone`
  
Durante todo el tutorial se da por supuesto que se tiene el teléfono conectado
al PC vía USB y con el modo Debug activado.   El modo debug se activa en
`Settings-\>Applications-\>Development-\>Debug Mode` y permite comunicarte con
el teléfono por USB.
  
Haz un backup de la información que tengas. Deja la SD lo más limpia que
puedas, necesitarás al menos 550Mb.
  
Cada vez que descargues algo, comprueba el MD5. Puedes llorar mucho
después si instalas algo corrupto.
    
        $ md5sum archivo.zip
    
## ![image][]Rooteo

  
Para rootear hay varias opciones que prometen root en 1 click, pero
una de ellas tenia problemas serios y la otra (SuperOneClik), se me
quedaba colgada en el proceso. La usada finalmente ha sido
Superboot, y ha funcionado muy bien.

  
Lo que hace es cargar una imagen de arranque distinta que cambia la
configuración por defecto del teléfono, permitiéndonos acceso root.
Para ello primero debemos desbloquear el **bootloader**:

<div class="img-left">

![image][1]
</div>
  
### 1. Desbloquear Bootloader [[Referencia Original][]]

Este paso es necesario si tu terminal es retail, esto es, ha sido
modificado por Vodafone, Movistar, Organge...
  
Emplearemos una herramienta llamada fastboot para desbloquear el
**bootloader**.

**Los datos que no estén en la SD serán borrados**. El proceso
realizará un 'wipe' de los datos del teléfono. Aplicaciones
instaladas, SMS, iconos etc... Haz un backup antes.

  
Descarga fastboot [mirror1][] [mirror2][] y descomprímelo.
Apaga el teléfono y enciéndelo manteniendo pulsada la
bolita/trackball para entrar en modo **bootloader**
Ejecuta fastboot:
    
	$ ./fastboot-linux oem unlock

**Si falla:** y se queda parado en 'Waiting Device' inténtalo otra
vez y asegúrate de que el teléfono está en modo **bootloader** y
encendido.
  
<div class="img-left">

![image][2]
</div>

### 2. Rooteo [[Referencia Original][3]]
  
Superboot es una imagen de arranque con una configuración que te
permitirá tener privilegios de root.   
Hay una versión para cada Build Number disponible de Android. El
mio era FRG83D, pero el tuyo puede ser diferente, compruébalo.   
Ve a la [tabla de mirrors de descarga][3], bájate la versión que te
corresponda y extráela.
Pon el teléfono en modo **bootloader** de nuevo. Encendiéndolo
mientras pulsas la bolita/trackball.
Da permisos al script de instalación y ejecútalo:
    
       chmod +x install-superboot-linux.sh
       ./install-superboot-linux.sh
  
Listo, ya deberíamos tener el teléfono rooteado. Reinícialo, y
deberías tener una aplicación nueva con un logotipo pirata. Esta
aplicación hará las veces de 'sudo', se encargará de avisarte y
pedirte confirmación cuando alguna aplicación pida privilegios de
superusuario.   
## ![image][4]Instalar Cyanogen

  
  
Una vez con el terminal rooteado, los pasos son:   
  
1. Instalar Recovery   
2. Backup Nandroid   
3. Resetear el teléfono a los valores de fábrica.   
4. Instalar CyanogenMod   
  
Todos los pasos se harán empleando la aplicación **ROM Manager**,
que dispone de una versión gratuita que se encuentra en el Market
de Android.   
Justo tras terminar la instalación, encontré un
[tutorial extremadamente claro][] aunque en inglés que incluye
screenshots que yo no pude hacer por carecer de cámara.   
### 1. ![image][5]Instalar Recovery [[Referencia Original][6]]

  
  
Antes de instalar un recovery debes asegurarte de que no hay ya uno
de fábrica. Para ello vas a necesitar conectarte con el SDK de
Android a tu teléfono y mirar si existen los archivos
/system/etc/install-recovery.sh y /system/recovery-from-boot.p y si
están, renombrarlos o borrarlos. No te preocupes, podemos utilizar
la infraestructura de fastboot que hemos usado antes para conectar
con el teléfono.   
  
En el directorio de fastboot hay un ejecutable llamado adb-linux.
Podemos usarlo para conectar con el teléfono, estando este en
encendido y con el modo debug activado. Lo que debemos hacer es
entrar, remontar el sistema como escritura y cambiarle el nombre a
los ficheros. Para remontar el sistema de ficheros necesitaremos
permisos de superusuario, que tendremos que autorizar en la
pantalla del teléfono. Me tiré 10 minutos hasta que me di cuenta de
que tenía que habilitar el comando su que ponía en la consola, ya
que tenía el teléfono debajo de unos papeles...   
    
    fastboot$ sudo ./adb-linux start-server
    
    * daemon started successfully *
    
    fastboot$ sudo ./adb-linux devices
    
    List of devices attached 
    
    0123456789ABCDEF    device
    
    fastboot$ sudo ./adb-linux shell
    
    $ su   # [ Autorizamos en el teléfono ]
    
    $$ mount
    
    rootfs on / type rootfs (ro,relatime)
    
    tmpfs on /dev type tmpfs (rw,relatime,mode=755)
    
    devpts on /dev/pts type devpts (rw,relatime,mode=600)
    
    ...
    
    /dev/block/mtdblock3 on /system type yaffs2 (ro,relatime)  # Este es
    
    ...
    
    $$ mount -o rw,remount -t yaffs2 /dev/block/mtdblock3 /system  # Montamos lectura escritura
    
    $$ mv /system/etc/install-recovery.sh  /system/etc/install-recovery-bk.sh 
    
    $$ mv system/recovery-from-boot.p system/recovery-from-boot-bk.p
    
    $$ mount -o ro,remount -t yaffs2 /dev/block/mtdblock3 /system  # Dejamos de nuevo en solo lectura
    
    $$ exit
    
    $ sync
    
    $ exit

  
Abre el ROM Manager.
Dale a **Flash ClockworkMod Recovery** y espera.
Dale a Flash Alternate Recovery e instalará el recovery en el
bootloader.
  
**Si falla:** Asegúrate de que tienes suficiente espacio en la SD.
Te puede ayudar deshacerte del directorio LOST.DIR

**Si sigue fallando:** Prueba con otra recovery como es Amon\_RA.
En ese caso descarga su [imagen][] y la puedes instalar con
flashloader, el que usamos antes, en **bootloader** mode también:
    
        fastboot flash recovery recovery-RA-passion-v2.2.1.img
    
        

  
### 2. ![image][7]Backup Nandroid [[Referencia Original][tutorial extremadamente claro]]

Asegúrate de que tienes espacio suficiente en la SD (+500mb)
Reinicia el teléfono en modo bootloader
Selecciona el modo fastboot con las teclas + y - del volumen y el
botón de encendido.
Al entrar allí, selecciona **backup nandroid**, marca lo que
necesites y haz el backup.
**Si falla:** Lo más probable es que no tengas espacio suficiente
en la SD. Pero asegúrate. Con el teléfono aún en fastboot, debes
conectarte al terminal y mirar el log de errores.

Puedes emplear el
[mismo método de conexión que antes con fastboot][] para entrar en
el teléfono y comprobarlo:
    
        fastboot$ sudo ./adb-linux shell
    
        $ cat cache/recovery/log
    
        $ exit
    
        

  
  
  
### 3. ![image][8]Reseteo a valores de fábrica (Wipe)[[Referencia Original][tutorial extremadamente claro]]

Asegúrate de que tienes el **backup** hecho, porque esto te
limpiará todo, menos las aplicaciones instaladas que se descargarán
de nuevo cuando vuelvas a entrar.
Reinicia el teléfono en modo **bootloader**
Selecciona el modo fastboot
Al entrar allí, selecciona **wipe fabric reset** y acto seguido,
otra vez, **wipe fabric reset**
  
### 4.![image][9] Instalar CyanogenMod[[Referencia Original][10]]

  
Arranca el teléfono, y ve a **ROM Manager**, allí tienes dos
opciones, *1.* pulsar en **Download List of Roms** elegir
*CyanogenMod* y seguir la instalación o bien *2.* bajarte
directamente la imagen e instalarla manualmente. Como solo disponía
de 3G y la imagen pesa lo suyo, decidí bajármela.

  
Ve a la wiki de **CyanogenMod** y descárgate la
[última imagen disponible][]. En mi caso fue *CyanogenMod 7*.
Bájate también las [aplicaciones de Google][], que vienen
empaquetadas aparte por motivos legales.
Comprueba los md5 de ambas imágenes. Es importante, no quieres
tener problemas.
    
        md5sum gaps-gb-2011307-signed.zip update-cm-7.0.3-N1-signed.zip
    
        

Copia ambos *.zip* al directorio raíz de la tarjeta SD del
teléfono.
Arranca en modo **bootloader** y entra en **fastboot**
Elige la opción de *Flash/Install .zip from sdcard*
Selecciona el .zip con **CyanogenMod** y se instalará.
Cuando termine, selecciona el .zip con **GoogleApps** y lo
instalará también.
  
Listo, cuando termines y reinicies el teléfono, deberías ver el
splash nuevo, en tono azul, de **CyanogenMod**.

  
**Si se queda pillado en el arranque:** No te preocupes,
probablemente se te ha pasado hacer el
*Reseteo a valores de fábrica*. Vuelve a modo **bootloader** y
hazlo.

  
  
**Si no funciona en absoluto:** Vuelve a modo **bootloader** y
restaura el backup *Nandroid*.

  
Una vez con la nueva ROM, tendrás que volver a configurar de nuevo
todas las cosas e instalar de nuevo las aplicaciones. El Market
Android recuerda las que tenías instaladas de antes, de manera que
eso no será problema.

  
Mi experiencia con la nueva ROM es excelente. Va con mucho más
rápido y es personalizable hasta el absurdo, de manera que puedo
usar el teléfono como realmente quiero y pienso que es mejor.
Normalmente es al revés, uno se adapta a los teléfonos que se
compra, con mayor o menor fortuna.

  
  
## ![image][11]Referencias:

  
Probablemente os convenga consultar los enlaces que empleé para
instalarlo, ya porque no haya explicado un paso suficientemente o
porque tengáis algún problema que yo no he mencionado. La
información es abundante pero MUY dispersa y en ocasiones demasiado
antigua.

  
  
- [Unlock Bootloader][Referencia Original]
- [Descargar e instalar Superboot][3]
- [Instalar Recovery Mode][6]
- [Instrucciones instalación CyanogenMod (Atención! binarios antiguos!)][]
- [Wiki CyanogenMod. Descarga de ROMs][última imagen disponible]
- [Wiki CyanogenMod. Instrucciones detalladas completas.][]
- [Tutorial instalación Cyanogen muy detallado con screenshots paso a paso.][tutorial extremadamente claro]
- [Problema con recovery por defecto; Soluciones dispersas.][]
- [Montando el sistema de ficheros como lectura/escritura.][]


  [Rootear e instalar CyanogenMod en un HTC Nexus One]: ./img/cabecera-post.png
  [image]: ./img/Lock-64.png
  [1]: ./img/Root-Explorer-128.png
  [Referencia Original]: http://android.modaco.com/content/google-nexus-one-nexusone-modaco-com/299078/how-to-unlock-the-bootloader-on-your-nexus-one/
  [mirror1]: http://www.romraid.com/paul/fastboot.zip
  [mirror2]: http://ragingbit.com/blog/wp-content/uploads/2011/05/fastboot.zip
  [2]: ./img/Unlock-64.png
  [3]: http://android.modaco.com/content/google-nexus-one-nexusone-modaco-com/298782/08-mar-superboot-erd79-gri40-rooting-the-nexus-one/
  [4]: ./img/Sys-Installer_256.png
  [tutorial extremadamente claro]: http://nexusonehacks.net/nexus-one-hacks/step-by-step-guide-on-how-to-install-cyanogen-mod-rom-on-your-nexus-one/
  [5]: ./img/Lifesaver-256.png
  [6]: http://forum.xda-developers.com/showthread.php?t=611829
  [imagen]: http://androidspin.com/2010/11/13/amon_ra-recovery-version-2-0-released-for-the-google-nexus-one/
  [7]: ./img/Bonus-Backup_256.png
  [mismo método de conexión que antes con fastboot]: conexion-fastboot
  [8]: ./img/Windshield-Wiper-256.png
  [9]: ./img/Gnome-System-Software-Installer-64.png
  [10]: http://wiki.cyanogenmod.com/index.php?title=Nexus_One:_Full_Update_Guide
  [última imagen disponible]: http://wiki.cyanogenmod.com/index.php?title=Latest_Version#Nexus_One
  [aplicaciones de Google]: http://wiki.cyanogenmod.com/index.php?title=Latest_Version#Google_Apps
  [11]: ./img/library_64x64.png
  [Instrucciones instalación CyanogenMod (Atención! binarios antiguos!)]: http://forum.cyanogenmod.com/topic/11455-cyanogenmod-61-for-nexus-one-v611-12152010/
  [Wiki CyanogenMod. Instrucciones detalladas completas.]: http://wiki.cyanogenmod.com/index.php?title=Nexus_One:_Full_Update_Guide#Flashing_Radio
  [Problema con recovery por defecto; Soluciones dispersas.]: http://forum.xda-developers.com/showpost.php?p=10467058&postcount=1638
  [Montando el sistema de ficheros como lectura/escritura.]: http://www.pocketmagic.net/?p=757
