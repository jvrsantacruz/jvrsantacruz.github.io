Title: Exportación de m3u en Python y Bash
Date: 2011-06-05 06:20
Category: Dev
Tags: python, script, m3u, mp3, music, playlist, Nexus One

<div class="img-center">

![image][]
</div>
  
Tengo un reproductor MP3 muy sencillo que empleo para correr. No
tiene ná, la música se carga en una carpeta interna llamada 'MUSIC'
y eso es todo lo que hay que hacer.   
El problema es que a Banshee no le da la gana de manejarlo, lo que
me obliga a copiar los archivos a mano. Con la pereza que me da,
últimamente siempre tengo la misma música.   
Lo mismo me pasa con mi Nexus One. Cuando a todo le falla la
batería, tiro de él para amenizar camino andado, pero siempre tengo
lo mismo.

  
  
Aburrido, decidí hacer algo que tomase una lista de reproducción de
Banshee y pusiese esos archivos de música en un directorio. Esto me
permitiría las siguientes cosas:

  
Hacer listas de reproducción en Banshee y exportarlas a un
directorio concreto sin buscar archivos a mano.
Elegir canciones sin el reproductor conectado, enchufarlo y
sincronizar con un boton.
  
Hacer listas con esas canciones y tenerlas en un directorio me
viene perfecto también para pasarle música a colegas. Usaría para
esto enlaces duros en lugar de copias y así podría tener muchos
directorios preparados para que se los llevasen, o en Dropbox, sin
ocupar espacio.

  
  
En este punto, me surge la eterna duda que me asaltaba cuando
trabajaba metiendo mano en servidores. ¿Script en *Bash* o en
*Perl/Python*? Los scripts en *Bash* son muy cortos y directos, me
encantan para soluciones rápidas. Pero luego, *luego*, empiezan a
crecer y crecer, mantenerlos es una pesadilla, y acababa
reescribiéndolos en *Python*.

  
Ante la duda de si elegir uno u otro, decidí... implementar ambos y
compararlos.

  
### Al lío:

  
  
*Banshee* exporta a m3u, pls y xspf. Por ahora solo he soportado
m3u que era el más sencillo, pero lo he hecho todo pensando en
poder ampliarlo fácilmente.   
Un m3u tiene la siguiente pinta:

  
  
    
    #EXTM3U
    
    #EXTINF:153,Go Back To The Zoo - Beam me up
    
    ../../home/arl/Music/Ofcourse/Benny Blisto/Go Back To The Zoo/03-go_back_to_the_zoo-beam_me_up.mp3
    
    #EXTINF:224,Joy Division - No Love Lost
    
    ../../home/arl/Music/Ofcourse/Substance/Substance/11 No Love Lost.mp3
    
    #EXTINF:211,Pony Bravo - Noche de Setas
    
    ../../home/arl/Music/Ofcourse/Pony Bravo/Un Gramo de Fe/02 Noche de Setas - PonyBravo.mp3

  
  
Comentario con el título, y ruta relativa al fichero de música, en
base a donde se guardó la lista de reproducción.

  
  
El script recibe la lista de reproducción y el directorio donde
quiero que se copie toda la música. Como a veces es mucha y por USB
va muy lento, no copia de nuevo la que ya está allí.

  
Tiene un par de opciones extra, *--delete* para que borre los
ficheros que no estén en la playlist, sincronizando el directorio.
*--force* que hace que sobreescriba las canciones que ya están en
lugar de saltarlas y *--link* que en lugar de copiar hace enlaces
duros, útil para no ocupar espacio extra y muy veloz.

  
  
Soy consciente de que mi script *Python* copia burdamente el
comportamiento de herramientas unix estándar ya existentes, y como
se verá, el script en *Bash* me lleva mucho menos que las 180
líneas de *Python*, de hecho son 64, la mitad. Sin embargo el
programa en *Python* es más fácilmente mantenible y ampliable con
nuevas funcionalidades que se me vayan ocurriendo; Como he hecho
con otros scripts a lo largo de los años.

  
Podéis comparar ambas versiones, *Bash* y *Python*. Es bastante
descorazonador que en *Bash* sea casi todo código de control de
errores y argumentos, el trabajo real se hace en *2 líneas*

  
### Versión en *Bash*

  
  
### Versión en *Python*

  
  
En *Python* podría haberlo parseado la lista m3u con un bucle
readline y listo, sin embargo pretendo ampliarlo luego al resto de
formatos, así que creé una clase implementando la interfaz iterable
de contenedores de Python. La lectura secuencial permite no tener
que cargar todo en memoria si no quieres y el poder leer la
playlist así:
    
    playlist = M3u(path_playlist)
    for title,path in playlist:
        print "%s at %s" % (title, path)
  
Una clase que implemente la interfaz iterable debe implementar el
método *\_\_iter\_\_()* y *next()*.
    
    class Iterable:
    
       def __init__(self):
           self.list = []
           self.index = 0
    
       def __iter__(self):
           return self
    
       def next(self):
        if index >= len(list):
            raise StopIteration
        else:
            return objeto[index]
            index += 1 


  [image]: ./img/cabecera1-1024x192.png "cabecera"
