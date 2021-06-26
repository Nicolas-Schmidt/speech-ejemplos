
## Manual y ejemplos en español del paquete `speech` <img src='figures/logo.png' align="right" height="180" />

*Nicolás Schmidt, Diego Luján, Juan Andrés Moraes*

### Descripción

El paquete
[`speech`](https://nicolas-schmidt.github.io/speech/index.html) permite
convertir diarios de sesión del parlamento uruguayo en formato PDF a
bases de datos ordenadas en la que cada fila es la intervención de cada
uno de los legisladores que interviene en esa sesión. Asimismo, dado que
los diarios de sesión muchas veces son imágenes escaneadas y que en el
proceso de OCR (reconocimiento óptico de caracteres por su sigla en
inglés) se puede perder o dañar la información recuperada, el paquete
provee un conjunto de funciones que ayudan a mejorar problemas de este
tipo.

##### Característias

> Este paquete ordena todas las intervenciones por sesión de cada uno de
> los **legisladores**. Esto quiere decir que todas las intervenciones
> de los *presidentes*, *secretarios*, *ministros* o personas invitadas
> no identificadas como legisladores no van a ser recuperadas por el
> algoritmo de recuperación del texto del PDF. Muchas veces puede
> suceder que el algoritmo no identifique claramente alguna de estas
> intervenciones (que se identifican y se eliminan). En esos casos la
> función con la que se pasa de un archivo con extensión `.pdf` a un
> `data.frame`
> ([`speech_build()`](https://nicolas-schmidt.github.io/speech/reference/speech_build.html))
> tiene un argumento (`rm.error.leg`) que permite incorporar esos casos
> que se desean eliminar y no fueron identificados por la función. Algo
> similar puede suceder con los legisladores. Una opción es que se use
> la función y que cada fila de la base de datos sea cada una de las
> intervenciones de cada legislador en cada momento. Esto quiere decir
> que se va a tener tantas intervenciones por legislador como cuantas
> intervenciones haya realizado en esa sesión. La otra opción (usando
> [`speech_build(., compiler =
> TRUE)`](https://nicolas-schmidt.github.io/speech/reference/speech_build.html)
> o
> [`speech_recompiler()`](https://nicolas-schmidt.github.io/speech/reference/speech_recompiler.html))
> es que cada fila de la base de datos sen todas las intervenciones
> juntas en una misma sesión de cada legislador o legisladora. El
> problema que puede surgir en con esta opción es que la **unión** de
> intervenciones se hace por legislador y si en el proceso de
> recuperación del texto el nombre de algún legislador no aparece
> escrito correctamente la compilación no va a reconocer a ese
> legislador que es el mismo, pero está escrito de distintas maneras.
> Para solucionar estos problemas y poder compilar el paquete tiene una
> función que permite arreglar este problema:
> [`speech_legis_replace()`](https://nicolas-schmidt.github.io/speech/reference/speech_recompiler.html).

## Ejemplos

Es importante tener presente que la lectura del texto de los archivos
PDF de los diarios de sesión no es siempre clara. Muchos de los diarios
de sesión que están en la página
[web](https://parlamento.gub.uy/documentosyleyes/documentos/diarios-de-sesion)
del parlamento están escaneados.

En algunas ocasiones antes de convertir el documento PDF que contiene un
diario de sesión mediante el uso de la función `speech_build()` va a ser
necesario pasar el PDF por un OCR (reconocimiento óptico de caracteres
por su sigla en inglés).

Lo primero que hay que hacer en caso de no tener instalado el paquete es
instalarlo. En la web del paquete debe verificar cual es la última
versión. Si en número de versión de CRAN coincide con la de GitHub
instale la de CRAN caso contrario la de GitHub. Esto lo puede verificar
las etiquetas (badges) que aparecen abajo a la derecha.

Versión de CRAN:

``` r
install.packages("speech")
```

Versión de GitHub:

``` r
if (!require("remotes")) install.packages("remotes")
remotes::install_github("Nicolas-Schmidt/speech")
```

Una vez instalado el paquete debe cargarlo en la sesión de trabajo:

``` r
library(speech)
```

#### Ejemplo 1.

###### El primer diario de sesión de la Asamblea General desde la reapertura democrática: 15 de febrero de 1985.

Lo primero que vamos a hacer es buscar la url al diario de sesión con el
que queremos trabajar o los descargamos en el argumento `file` ponemos
el nombre del archivo pdf con el que lo guardamos. Hecho eso vamos
directo a la función principal que es por donde se empieza. Vamos a
usarla sin el argumento `compiler` para detectar posibles problemas como
los comentados en la sección anterior.

``` r
url <- "https://parlamento.gub.uy/documentosyleyes/documentos/diario-de-sesion/asambleageneral/1/IMG/0?width=800&height=600&hl=en_US1&iframe=true&rel=nofollow"

text <- speech::speech_build(file = url)

print(text, n = nrow(text))
#> # A tibble: 28 x 7
#>    legislator  speech          chamber  date       legislature id            sex
#>    <chr>       <chr>           <chr>    <date>           <int> <chr>       <dbl>
#>  1 PEREYRA     "SEÑOR PER. EY~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  2 CORDOSO     "SEÑOR CORDOSO~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  3 ROSSI PASS~ "SEÑOR ROSSI P~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  4 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  5 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  6 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  7 FERREIRA    "SEÑOR FERREIR~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  8 FElRREIRA ~ "SEÑOR FElRREI~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  9 RODRIGUEZ ~ "SEÑOR RODRIGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 10 RODRIGUEZ ~ "SEÑOR RODRIGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 11 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 12 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 13 PAZ AGUIRRE "SEÑOR PAZ AGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 14 PAZ AG      "SEÑOR PAZ AGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 15 CIGLIUTI    "SEÑOR CIGLIUT~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 16 CIGLIUTI    "SEÑOR CIGLIUT~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 17 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 18 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 19 PIRIESIDEN~ "SEÑOR PIRIESI~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 20 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 21 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 22 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 23 PAZ AGUIRRE "SEÑOR PAZ AGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 24 PAZ AGUIRRE "SEÑOR PAZ AGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 25 V AILLANT   "SEÑOR V AILLA~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 26 VAILLANT    "SEÑOR VAILLAN~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 27 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 28 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
```

Si bien la base de datos llamada `text` luce bien (hay legisladores, se
reconoció la cámara, la fecha…) se aprecian varios problemas que son
producto de la lectura del documento. Este documento está escaneado y
hay caracteres que no se leen correctamente: por ejemplo, legislador 7 y
8 parecen ser el mismo, pero al compilar los datos no se van a juntar ya
que no están escritos igual; el legislador 25 y 26 es el mismo solo que
en el primer caso hay un espacio en medio del apellido de uno de los
legisladores. El legislador 13, 23 y 24 parecen ser el mismo que el 14.

También se aprecia que hay un *‘legislador’* que no es legislador y que
no debería aparecer: el 19: *‘PIRIESIDENTE’*.

Comencemos por el ultimo problema. La función `speech_build()` tiene un
argumento que permite eliminar ‘legisladores’ que no queremos en nuestra
base de datos: `rm.error.leg`

``` r
text <- speech::speech_build(file = url, rm.error.leg = "PIRIESIDENTE")

## verificamos que ya no esté

print(text, n = nrow(text))
#> # A tibble: 27 x 7
#>    legislator  speech          chamber  date       legislature id            sex
#>    <chr>       <chr>           <chr>    <date>           <int> <chr>       <dbl>
#>  1 PEREYRA     "SEÑOR PER. EY~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  2 CORDOSO     "SEÑOR CORDOSO~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  3 ROSSI PASS~ "SEÑOR ROSSI P~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  4 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  5 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  6 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  7 FERREIRA    "SEÑOR FERREIR~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  8 FElRREIRA ~ "SEÑOR FElRREI~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#>  9 RODRIGUEZ ~ "SEÑOR RODRIGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 10 RODRIGUEZ ~ "SEÑOR RODRIGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 11 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 12 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 13 PAZ AGUIRRE "SEÑOR PAZ AGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 14 PAZ AG      "SEÑOR PAZ AGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 15 CIGLIUTI    "SEÑOR CIGLIUT~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 16 CIGLIUTI    "SEÑOR CIGLIUT~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 17 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 18 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 19 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 20 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 21 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 22 PAZ AGUIRRE "SEÑOR PAZ AGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 23 PAZ AGUIRRE "SEÑOR PAZ AGU~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 24 V AILLANT   "SEÑOR V AILLA~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 25 VAILLANT    "SEÑOR VAILLAN~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 26 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
#> 27 TOURNE      "SEÑOR TOURNE.~ ASAMBLE~ 1985-02-15          42 0?width=80~     1
```

Como se puede ver en la nueva base de datos `text` ya no aparece la fila
19 con los datos anteriores.

Ahora deberíamos solucionar el problema de los nombres de los
legisladores que si queremos que permanezcan en nuestra base de datos y
que debemos corregir para poder compilarlos correctamente.

El paquete `speech` tiene una función que permite chequear los nombres
de los legisladores
([`speech_check()`](https://nicolas-schmidt.github.io/speech/reference/speech_check.html))
y otra fundción que permite modificar fácilmente los nombres de los
legisladores: `speech_legis_replace`.

Vamos a chequear los nombres:

``` r
speech_check(text)
#> $C
#>   legislator
#> 1   CIGLIUTI
#> 2    CORDOSO
#> 
#> $F
#>      legislator
#> 1 FElRREIRA Señ
#> 2      FERREIRA
#> 
#> $P
#>    legislator
#> 1      PAZ AG
#> 2 PAZ AGUIRRE
#> 3     PEREYRA
#> 
#> $R
#>          legislator
#> 1 RODRIGUEZ CAMUSSO
#> 2     ROSSI PASSINA
#> 
#> $T
#>   legislator
#> 1     TOURNE
#> 
#> $V
#>   legislator
#> 1  V AILLANT
#> 2   VAILLANT
```

La función `speech_check()` devuelve una lista de nombres ordenados por
inicial. Esta función permite visualizar los problemas de un modo más
amigable y fácil. Lo que se puede apreciar es que hay distintos tipos de
errores: en los legisladores que inicial con **F**, con **P** y con
**V**.

Ahora vamos a cambiar los nombres que están mal:

``` r
text <- speech_legis_replace(text, old = "FElRREIRA Señ",  new = "FERREIRA")
text <- speech_legis_replace(text, old = "V AILLANT",  new = "VAILLANT")
text <- speech_legis_replace(text, old = "PAZ AG",  new = "PAZ AGUIRRE")
text <- speech_legis_replace(text, old = "CORDOSO",  new = "CARDOSO")
```

Con esta función lo que hacemos es modificar la base construida (es
decir el objeto `text`) por eso debemos sobrescribirla.

Ahora debemos chequear que los cambios se realizaron de manera correcta:

``` r
speech_check(text)
#> $C
#>   legislator
#> 1    CARDOSO
#> 2   CIGLIUTI
#> 
#> $F
#>   legislator
#> 1   FERREIRA
#> 
#> $P
#>    legislator
#> 1 PAZ AGUIRRE
#> 2     PEREYRA
#> 
#> $R
#>          legislator
#> 1 RODRIGUEZ CAMUSSO
#> 2     ROSSI PASSINA
#> 
#> $T
#>   legislator
#> 1     TOURNE
#> 
#> $V
#>   legislator
#> 1   VAILLANT
```

Una vez que consideramos que ya no hay más cambios por realizar podemos
recompilar el diario de sesión. Tenemos que recompilarlo ya que hemos
modificado la compilacion inicial.

``` r
text2 <- speech_recompiler(tidy_speech = text)
print(text2, n = nrow(text2))
#> # A tibble: 9 x 7
#>   legislator  legislature chamber   date       id            sex speech         
#>   <chr>             <int> <chr>     <date>     <chr>       <dbl> <chr>          
#> 1 CARDOSO              42 ASAMBLEA~ 1985-02-15 0?width=80~     1 "SEÑOR CORDOSO~
#> 2 CIGLIUTI             42 ASAMBLEA~ 1985-02-15 0?width=80~     1 "SEÑOR CIGLIUT~
#> 3 FERREIRA             42 ASAMBLEA~ 1985-02-15 0?width=80~     1 "SEÑOR FERREIR~
#> 4 PAZ AGUIRRE          42 ASAMBLEA~ 1985-02-15 0?width=80~     1 "SEÑOR PAZ AGU~
#> 5 PEREYRA              42 ASAMBLEA~ 1985-02-15 0?width=80~     1 "SEÑOR PER. EY~
#> 6 RODRIGUEZ ~          42 ASAMBLEA~ 1985-02-15 0?width=80~     1 "SEÑOR RODRIGU~
#> 7 ROSSI PASS~          42 ASAMBLEA~ 1985-02-15 0?width=80~     1 "SEÑOR ROSSI P~
#> 8 TOURNE               42 ASAMBLEA~ 1985-02-15 0?width=80~     1 "SEÑOR TOURNE.~
#> 9 VAILLANT             42 ASAMBLEA~ 1985-02-15 0?width=80~     1 "SEÑOR V AILLA~
```

Una vez que tenemos la base final (`text2`) ahora podemos proceder a
usar el paquete [`puy`]() que permite agregar la etiqueta partidaria a
cada legislador.

``` r
library(puy)

datos <- add_party(text2)
datos
#> # A tibble: 9 x 12
#>   legislator legislature chamber date       id    speech   sex legislator2 party
#>   <chr>            <int> <chr>   <date>     <chr> <chr>  <dbl> <chr>       <chr>
#> 1 CARDOSO             42 ASAMBL~ 1985-02-15 CARD~ "SEÑO~     1 CARDOSO, J~ Fren~
#> 2 CIGLIUTI            42 ASAMBL~ 1985-02-15 CIGL~ "SEÑO~     1 CIGLIUTI, ~ Part~
#> 3 FERREIRA            42 ASAMBL~ 1985-02-15 FERR~ "SEÑO~     1 FERREIRA, ~ Part~
#> 4 PAZ AGUIR~          42 ASAMBL~ 1985-02-15 PAZ ~ "SEÑO~     1 PAZ AGUIRR~ Part~
#> 5 PEREYRA             42 ASAMBL~ 1985-02-15 PERE~ "SEÑO~     1 PEREYRA, C~ Part~
#> 6 RODRIGUEZ~          42 ASAMBL~ 1985-02-15 RODR~ "SEÑO~     1 RODRIGUEZ ~ Fren~
#> 7 ROSSI PAS~          42 ASAMBL~ 1985-02-15 ROSS~ "SEÑO~     1 ROSSI PASS~ Unio~
#> 8 TOURNE              42 ASAMBL~ 1985-02-15 TOUR~ "SEÑO~     1 TOURNE, Ur~ Part~
#> 9 VAILLANT            42 ASAMBL~ 1985-02-15 VAIL~ "SEÑO~     1 VAILLANT, ~ Part~
#> # ... with 3 more variables: party_acron <chr>, indicator <int>, words <int>
```
