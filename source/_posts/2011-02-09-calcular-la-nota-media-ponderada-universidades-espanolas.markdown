---
title: Calcular la nota media ponderada Universidades Españolas.
date: 2011-02-09 15:13
category: dev
tags: calculos, csv, españa, medias, notas, python, uca, universidad
---

<div class="img-center">

![image][]

</div>
  
Los alumnos de la universidad de Cádiz (y andaluces en general)
podemos consultar este dato en la sección de información académica
de la plataforma para prácticas de empresa [Ícaro][] o acudiendo al
Vicerrectorado de Alumnos donde pueden calculárnosla y extendernos
un certificado de nota.   
  
Sin embargo, aunque lo podamos consultar o nos lo puedan hacer,
nunca está de mas comprender como funciona el sistema, y aparte,
calcular otras medias que siendo reales nos convengan más, ya que
el sistema por bloques que se emplea es manifiestamente injusto y
no refleja la nota real del alumno. Según el el cálculo actual,
**un alumno con 6,9 es dos veces peor que un alumno con un 7** en
lugar de haber entre ellos una diferencia de una décima.   
  
Este esquema supongo que ha sido heredado de cuando en las
facultades no era obligatorio asignar una nota numérica sino tan
solo APROBADO, NOTABLE, SOBRESALIENTE y MATRÍCULA DE HONOR y en mi
opinión ya está más que superado; en todas las facultades se
utiliza un sistema numérico que permite saber con exactitud la nota
del alumno. Y aunque no se pudiese porque siguiesen existiendo
facultades 'chapadas a la antigua', no es conveniente tener un
estadístico que 'destruye' información como este, que no es
representativo de la nota del alumno y expresa fatal el valor del
mismo. Tiene 'truco'.   
  
Entre estos cálculos para la nota media y algunos
profesores/aplicaciones informáticas en la universidad que
truncaban notas como 6,7 a 6, no me parece raro que los españoles
tengamos una nota media tan ridículamente baja en el currículum, la
cual nos dificulta y mucho la búsqueda de empleo en el extranjero,
donde las universidades si que inflan las finales que da gusto.
Cuando te juegas plazas, becas, puestos de trabajo y tu vida en
general, estas pequeñas idioteces y tristes clasificaciones saltan
a un primer plano y adquieren tanta importancia que me sorprende
que se use este método desde mi punto de vista tan malo. Tener
egresados con notas bajas **no** debería ser un signo de prestigio.
Pero bueno que me enredo.   
  
La nota media del expediente académico universitario es un número
del 1 al 4, siendo 1 aprobado y 4 matrícula de honor.   
La relación es la siguiente, solo se tienen en cuenta las
asignaturas aprobadas:

<table>
	<th>
		<td>Nota 1-10</td>
		<td>Nota 1-4</td>
		<td>Nombre</td>
	</th>
	<tr>
		<td>1</td>
		<td>[5-7)</td>
		<td>Aprobado</td>
	</tr>

	<tr>
		<td>2</td>
		<td>[7-9)</td>
		<td>Notable</td>
	</tr>

	<tr>
		<td>3</td>
		<td>[9-10]</td>
		<td>Sobresaliente</td>
	</tr>

	<tr>
		<td>4</td>
		<td>10</td>
		<td>Matrícula de Honor</td>
	</tr>
</table>
 
La manera de calcularla es aplicar la siguiente fórmula, tomando la
nota de cada asignatura, pasándola a la escala anterior y
multiplicándola por sus créditos:   

<img src="./img/media_1_4.svg" width="250px"/>

Como comenté antes, este es un estadístico terrible para resumir el
expediente académico de un alumno, debido a los 'saltos' que tiene
en el 7, el 9 y el 10. Una medida, con mucho, más representativa,
sería calcular la media aritmética ponderada con los créditos y
aplicar una 'regla de tres' para pasarlo al intervalo 1-4.   
  
Esto es precisamente lo que también hice, calcular esta y otras
medias para ver que estadístico representaba mejor mi expediente
académico. Con mejor no solo quiero decir que saliese más
favorecido, sino más adecuadamente en vista a los valores de mis
notas (que tienden al 6).   
  
Para calcular la nota a partir de una cantidad numérica 1-10 y
obtener un porcentaje, empleé esta fórmula. Nótese que nota\_min y
nota\_max son la nota mínima para aprobar y la máxima que es
posible obtener en la Universidad correspondiente.   
  
<img src="./img/media_normalizada.svg" width="400px"/>
  
O la media aritmética normal, sin ponderar con los créditos:   

<img src="./img/media_suma.svg" width="150px"/>
  
  
En la Universidad de Cádiz tenemos disponibles nuestro expediente
completo a traves del [XXI Portal][] de donde nos podemos bajar un
xls con las notas de las asignaturas aprobadas.   

<div class="img-center">
  
![Screenshot de calc con el csv de las notas][]

<sub>"Las notas copiadas, pegadas y con las líneas sin nota y cabeceras
eliminadas."</sub> 
</div>
  
  
Lo que hago es seleccionar solo las asignaturas, eliminar las que
no tienen nota (normalmente créditos de libre provinentes de
prácticas de empresa, seminarios o conferencias) y pegarlo sin
cabecera en un csv plano con caracter separador la coma **,** y
delimitador de texto la doble comilla **"**.   
  
Tras hacer esto lo que tenemos es un notas.csv con la siguiente
estructura:

	code-block::csv
    "1711014 ","ESTRUCTURA Y TECNOLOGÍA DE COMPUTADORES","9","TRONCAL","2006-07","JUNIO","APROBADO","6,6"
    
    "1711013 ","FUNDAMENTOS DE SISTEMAS DIGITALES","6","TRONCAL","2006-07","FEBRERO","SOBRESALIENTE","9,6"
    
    "1711022 ","INFORMÁTICA GENERAL","6","OBLIGATORIA","2006-07","FEBRERO","APROBADO","6,5"

  
Los campos que nos interesan son el tercero (créditos) y el octavo
y último (nota). Empleando python y su procesador de csv (baterías
incluidas!) podemos fácilmente parsear el fichero y obtener
nuestras notas.   
Para ello creé un script que calcula todo esto, siguiendo una
plantilla para una beca del CSIC (Centro Superior de
Investigaciones Científicas) a la hora de realizar los cálculos. La
nota final coincide con la que obtengo en Ícaro, de manera que
supongo que es correcta.   
  
El script lo llamo con ./media.py notas.csv e imprime tanto los
resultados como los cálculos intermedios. Tened en cuenta que la
formula del porcentaje emplea los valores de nota mínima para
aprobar y nota máxima a recibir, que en Cádiz son 5 y 10 pero que
de centro a centro pueden variar.   
  
Para terminar, agradecería muchísimo cualquier tipo de
**corrección o aviso de errores**. Lo más probable es que haya
metido la pata en algún momento, que ignore algo relacionado con el
cálculo de notas o sobre la organización Universitaria, de manera
que si ves algo que no cuadre, por favor, deja un comentario :)   

    #! /usr/bin/env python
    # -*- coding: utf-8 -*-
    
    import sys
    import csv
    
    def error(text):
        print "Error:" + text
        sys.exit()
    
    def nota_14(nota):
        """
        Recibe una nota del 5 al 10
        Devuelve la nota del 1-4 según la siguiente tabla del CSIC:
            [5-7)   -> 1
            [7-9)   -> 2
            [9-10)  -> 3
            10      -> 4
    
        Devuelve None si la nota no está en el intervalo [5-10]
        """
        if nota < 5: return None
        elif nota < 7: return 1.0
        elif nota < 9: return 2.0
        elif nota < 10: return 3.0
        elif nota == 10: return 4.0
    
    def nota_percent(nota):
        """
        Recibe una nota del 0 al 10
        Devuelve la nota en porcentaje según la fórmula del CSIC
        """
        nota_min = 5.0   # nota minima para aprobar en Cádiz
        nota_max = 10.0  # nota máxima en Cádiz
    
        return 50.0 + ((50.0 * (nota - nota_min)) / (nota_max - nota_min))
    
    def media(csvfile):
        """
        Toma un csv con las notas y devuelve la media de todas las asignaturas.
        Asume que la nota de las asignaturas está en el último campo de cada línea.
        """
    
        try:
            reader = csv.reader(open(csvfile, 'rb'), delimiter=",", quotechar='"')
        except:
            error("No se puede abrir el fichero %s" % csvfile)
    
        # cabecera de la tabla
        print "Nota, Expediente, Percent, 1-4, Creditos, creditos x 1-4"
    
        # totales
        creditos_total = 0.0
        nota_total = 0.0
        ponderada_14_total = 0.0
        ponderada_100_total = 0.0
        nasignaturas = 0.0
        n = 0
    
        for row in reader:
    
            n += 1
    
            if len(row) < 8 :
                print "La linea: %s no tiene el número de campos necesarios."   
                     % str(row)
                next
    
            # El signo decimal es . no , como en españa
            row = [f.replace(',', '.') for f in row]
    
            # Obtener los campos
            curso = row[4]
            try: 
                creditos = float(row[2]) 
            except ValueError: 
                print "En la línea linea %d el campo 2 (creditos) %s no es un número"   
                        % (n,row[2])
                next
    
            try:
                nota = float(row[7])
            except ValueError:
                print "En la línea linea %d el campo 7 (notas) %s no es un número"   
                        % (n,row[2])
                next
    
            # Convertir entre escalas
            percent = nota_percent(nota)
            nota14 = nota_14(nota)
            ponderada_100 = creditos * percent
            ponderada_14 = creditos * nota14
    
            # Actualizar totales
            creditos_total += creditos
            nota_total += nota
            ponderada_14_total += ponderada_14
            ponderada_100_total += ponderada_100
            nasignaturas += 1
    
            # curso,  nota, percent, 14, creditos, creditos x 14
            print "%s   %d      %d       %d      %d      %d"   
                    % (curso, nota, percent, nota14, creditos, ponderada_14)
    
        # calcular
        media_ponderada_14 = ponderada_14_total / creditos_total
        media_ponderada_100 = ponderada_100_total / creditos_total
        media_ponderada_100_14 = media_ponderada_100 * (4.0 / 100.0)
        media_normal = nota_total / nasignaturas
    
        #imprimir totales y resultado
        print "     Total Créditos: %d   Total creditos x 14: %d"   
                % (creditos_total, ponderada_14_total)
        print "     Media ponderada 1-4:         %.2f" % media_ponderada_14
        print "     Media ponderada 100:         %.2f" % media_ponderada_100
        print "     Media ponderada 100 en 1-4:  %.2f" % media_ponderada_100_14
        print "     Media sin ponderar:          %.2f" % media_normal
    
    
    def main():
        if len(sys.argv) < 1:
            error("Numero incorrecto de argumentos. \n\n Use: media.py file.csv")
    
        media(sys.argv[1])
    
    main()

  [image]: ./img/cabecera_media-600px.png "cabecera_media"
  [Ícaro]: http://icaro.ual.es
  [XXI Portal]: http://www.uca.es/web/servicios/servicio_alumnos/automatricula/Paginas/uxxiportal
  [Screenshot de calc con el csv de las notas]: ./img/notas_csv-300x136.png "notas_csv"
  [media_1_4]: ./img/media_1_4.svg
