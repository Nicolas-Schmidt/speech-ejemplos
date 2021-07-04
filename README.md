
## Ejemplos y usos del paquete `speech` :page\_with\_curl: <img src='figures/logo.png' align="right" height="180" />

*Nicolás Schmidt*

### Índice

  - [Descripción del paquete `speech`](#descripción)
  - [Características del paquete `speech`](#características)
  - [Ejemplos](#ejemplos)
      - [Ejemplo 1: Primer sesión desde la restauración
        democrática](#ejemplo-1)
      - [Ejemplo 2: Sesión sobre creación de Consejos de
        Salarios](#ejemplo-2)
      - [Ejemplo 3: Sesión reciente de la Comisión
        Permanente](#ejemplo-3)
  - [Usos de `speech`](#usos)
  - [Aplicación web de `speech` y `puy`](#aplicación-web)

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

### Características

Este paquete ordena todas las intervenciones por sesión de cada uno de
los **legisladores**. Esto quiere decir que todas las intervenciones de
los *presidentes*, *secretarios*, *ministros* o personas invitadas no
identificadas como legisladores no van a ser recuperadas por el
algoritmo de recuperación del texto del PDF. Muchas veces puede suceder
que el algoritmo no identifique claramente alguna de estas
intervenciones (que se identifican y se eliminan). En esos casos la
función con la que se pasa de un archivo con extensión `.pdf` a un
`data.frame`
([`speech_build()`](https://nicolas-schmidt.github.io/speech/reference/speech_build.html))
tiene un argumento (`rm.error.leg`) que permite incorporar esos casos
que se desean eliminar y no fueron identificados por la función.

Algo similar puede suceder con los legisladores. Una opción es que se
use la función y que cada fila de la base de datos sea cada una de las
intervenciones de cada legislador en cada momento. Esto quiere decir que
se va a tener tantas intervenciones por legislador como cuantas
intervenciones haya realizado en esa sesión. La otra opción (usando
[`speech_build(., compiler =
TRUE)`](https://nicolas-schmidt.github.io/speech/reference/speech_build.html)
o
[`speech_recompiler()`](https://nicolas-schmidt.github.io/speech/reference/speech_recompiler.html))
es que cada fila de la base de datos sen todas las intervenciones juntas
en una misma sesión de cada legislador o legisladora. El problema que
puede surgir en con esta opción es que la **unión** de intervenciones se
hace por legislador y si en el proceso de recuperación del texto el
nombre de algún legislador no aparece escrito correctamente la
compilación no va a reconocer a ese legislador que es el mismo, pero
está escrito de distintas maneras. Para solucionar estos problemas y
poder compilar el paquete tiene una función que permite arreglar este
problema:
[`speech_legis_replace()`](https://nicolas-schmidt.github.io/speech/reference/speech_recompiler.html).

## Ejemplos

Es importante tener presente que la lectura del texto de los archivos
PDF de los diarios de sesión no es siempre clara. Muchos de los diarios
de sesión que están en la página
[**web**](https://parlamento.gub.uy/documentosyleyes/documentos/diarios-de-sesion)
del parlamento están escaneados.

En algunas ocasiones antes de convertir el documento PDF que contiene un
diario de sesión mediante el uso de la función
[`speech_build()`](https://nicolas-schmidt.github.io/speech/reference/speech_build.html)
va a ser necesario pasar el PDF por un OCR (reconocimiento óptico de
caracteres por su sigla en inglés).

Lo primero que hay que hacer en caso de no tener instalado el paquete es
instalarlo. En la web del paquete debe verificar cual es la última
versión. Si el número de versión de CRAN coincide con la de GitHub
instale la de CRAN caso contrario instale la versión de GitHub. Esto lo
puede verificar en las etiquetas (badges) que aparecen abajo a la
derecha en la web del
[paquete](https://nicolas-schmidt.github.io/speech/index.html).

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

Vamos a ver 3 ejemplos que presentan distintos problemas y niveles de
complejidad en su procesamiento:

  - El diario de sesión de la Asamblea General de la reapertura
    democrática: 15 de febrero de 1985.  
  - Un diario de sesión sobre la creación de los consejos de salarios
    del 13 de octubre de 1941.  
  - Un diario de sesión reciente de una sesión convencional de la
    Comisión Permanente del 17 de setiembre de 2019

### Ejemplo 1

###### :arrow\_forward: El primer diario de sesión de la Asamblea General desde la reapertura democrática: 15 de febrero de 1985.

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

Comencemos por el ultimo problema. La función
[`speech_build()`](https://nicolas-schmidt.github.io/speech/reference/speech_build.html)
tiene un argumento que permite eliminar ‘legisladores’ que no queremos
en nuestra base de datos: `rm.error.leg`

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
modificado la compilación inicial.

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
usar el paquete [`puy`]() (:boom::exclamation:) que permite agregar la
etiqueta partidaria y el nombre completo de cada legislador.

``` r
library(puy)

datos <- add_party(text2)
datos
#> # A tibble: 9 x 12
#>   legislator legislature chamber date       id    speech   sex legislator2 party
#>   <chr>            <int> <chr>   <date>     <chr> <chr>  <dbl> <chr>       <chr>
#> 1 CARDOSO             42 ASAMBL~ 1985-02-15 0?wi~ "SEÑO~     1 CARDOSO, J~ Fren~
#> 2 CIGLIUTI            42 ASAMBL~ 1985-02-15 0?wi~ "SEÑO~     1 CIGLIUTI, ~ Part~
#> 3 FERREIRA            42 ASAMBL~ 1985-02-15 0?wi~ "SEÑO~     1 FERREIRA, ~ Part~
#> 4 PAZ AGUIR~          42 ASAMBL~ 1985-02-15 0?wi~ "SEÑO~     1 PAZ AGUIRR~ Part~
#> 5 PEREYRA             42 ASAMBL~ 1985-02-15 0?wi~ "SEÑO~     1 PEREYRA, C~ Part~
#> 6 RODRIGUEZ~          42 ASAMBL~ 1985-02-15 0?wi~ "SEÑO~     1 RODRIGUEZ ~ Fren~
#> 7 ROSSI PAS~          42 ASAMBL~ 1985-02-15 0?wi~ "SEÑO~     1 ROSSI PASS~ Unio~
#> 8 TOURNE              42 ASAMBL~ 1985-02-15 0?wi~ "SEÑO~     1 TOURNE, Ur~ Part~
#> 9 VAILLANT            42 ASAMBL~ 1985-02-15 0?wi~ "SEÑO~     1 VAILLANT, ~ Part~
#> # ... with 3 more variables: party_acron <chr>, indicator <int>, words <int>
```

### Ejemplo 2

###### :arrow\_forward: Un diario de sesión sobre la creación de los consejos de salarios del 13 de octubre de 1941.

``` r
text <- speech::speech_build(file = "speech_example.pdf")
print(text)
#> # A tibble: 130 x 7
#>    legislator  speech                chamber date       legislature id       sex
#>    <chr>       <chr>                 <chr>   <date>           <int> <chr>  <dbl>
#>  1 ITURBIDE    "SEÑOR ITURBIDE. — H~ <NA>    NA                  NA speec~     1
#>  2 DAMBORIARE~ "SEÑOR DAMBORIARENA.~ <NA>    NA                  NA speec~     1
#>  3 MOREIRA     "SEÑOR MOREIRA. — Se~ <NA>    NA                  NA speec~     1
#>  4 GOMEZ       "SEÑOR GOMEZ. — Se h~ <NA>    NA                  NA speec~     1
#>  5 BRUNO       "SEÑOR BRUNO, — Seño~ <NA>    NA                  NA speec~     1
#>  6 CERSOSIMO   "SEÑOR CERSOSIMO. —=~ <NA>    NA                  NA speec~     1
#>  7 PUIG        "SEÑOR PUIG. — Señor~ <NA>    NA                  NA speec~     1
#>  8 SALGADO     "SEÑOR SALGADO. — Pi~ <NA>    NA                  NA speec~     1
#>  9 SALGADO     "SEÑOR SALGADO. ~ Te~ <NA>    NA                  NA speec~     1
#> 10 SALGADO     "SEÑOR SALGADO. — Ya~ <NA>    NA                  NA speec~     1
#> # ... with 120 more rows
```

Lo primero que vemos es que aparecen errores en nombres, la variable
cámara, fecha y legislatura no fueron identificadas. Esto nos da la
pauta de que el documento no es de muy buena calidad. Vamos a chequear
los nombres de los legisladores previo a compilar:

``` r
speech_check(text)
#> $A
#>   legislator
#> 1    ANTUNEZ
#> 2  ARISMENDI
#> 3  ARISMENDT
#> 
#> $B
#>                 legislator
#> 1                    BKANE
#> 2                    BREÑA
#> 3 BRUNEREAU DES HOUILLERES
#> 4                    BRUNO
#> 5               BTJRANELLI
#> 6                 BURANELL
#> 7                BURANELLI
#> 
#> $C
#>      legislator
#> 1 CAl l e r IZA
#> 2     CALLERIZA
#> 3     CALLERTZA
#> 4       CARDOSO
#> 5     CAUTERIZA
#> 6     CERSOSIMO
#> 7     CERSOSTMO
#> 8 CHOTJHY TERRA
#> 9  CHOUHY TERRA
#> 
#> $D
#>     legislator
#> 1 DAMBORIARENA
#> 
#> $E
#>   legislator
#> 1   ESPALTER
#> 
#> $F
#>         legislator
#> 1 FERNANDEZ CRESPO
#> 2            FUJLG
#> 
#> $G
#>   legislator
#> 1     GARLON
#> 2     GARZON
#> 3      GOMEZ
#> 4   GONZALEZ
#> 
#> $I
#>   legislator
#> 1   ITURBIDE
#> 
#> $J
#>   legislator
#> 1 JUiHANELLl
#> 
#> $M
#>        legislator
#> 1        MARTINEZ
#> 2     miRBTDE Pid
#> 3         MOREIRA
#> 4  MORENO ERADLOS
#> 5   MORENO RALLOS
#> 6 MORENO ZEBALLOS
#> 
#> $O
#>   legislator
#> 1        OIS
#> 2      OLEHO
#> 3      OTERO
#> 
#> $P
#>           legislator
#> 1 P R E S ID E N T E
#> 2         PRENOTENTE
#> 3        PRPJSIDENTE
#> 4         PUaU tSiio
#> 5               PUIG
#> 6               PUTG
#> 
#> $R
#>            legislator
#> 1                  RO
#> 2     RODRIGLEZ ROCHA
#> 3 RODRIGUEZ R O C H A
#> 4     RODRIGUEZ ROCHA
#> 5                 ROT
#> 
#> $S
#>   legislator
#> 1    SALGADO
#> 
#> $T
#>   legislator
#> 1    TIJBINO
#> 2      TROIT
#> 3   TROITIÑO
#> 4   TTERBIDE
#> 5    TTJBINO
#> 6    TTTBTNO
#> 7     TUBINO
#> 8     TURINO
```

Vemos que hay errores de dos tipos. El primero es de presidentes que no
han sido eliminados, y el segundo es que muchos nombres de los
legisladores están mal escritos. Esto se debe exclusivamente a la mala
calidad del OCR que tiene este documento. Sin embargo, como vamos a ver
el paquete `text2` proporciona las funciones adecuadas para lidiar con
este tipo de problemas. Vamos a comenzar eliminado a los presidentes.

``` r
text <- speech::speech_build(file = "speech_example.pdf", 
                             rm.error.leg = c("P R E S ID E N T E", 
                                              "PRPJSIDENTE", 
                                              "PRPJSIDENTE", 
                                              "PRENOTENTE"))
speech_check(text)
#> $A
#>   legislator
#> 1    ANTUNEZ
#> 2  ARISMENDI
#> 3  ARISMENDT
#> 
#> $B
#>                 legislator
#> 1                    BKANE
#> 2                    BREÑA
#> 3 BRUNEREAU DES HOUILLERES
#> 4                    BRUNO
#> 5               BTJRANELLI
#> 6                 BURANELL
#> 7                BURANELLI
#> 
#> $C
#>      legislator
#> 1 CAl l e r IZA
#> 2     CALLERIZA
#> 3     CALLERTZA
#> 4       CARDOSO
#> 5     CAUTERIZA
#> 6     CERSOSIMO
#> 7     CERSOSTMO
#> 8 CHOTJHY TERRA
#> 9  CHOUHY TERRA
#> 
#> $D
#>     legislator
#> 1 DAMBORIARENA
#> 
#> $E
#>   legislator
#> 1   ESPALTER
#> 
#> $F
#>         legislator
#> 1 FERNANDEZ CRESPO
#> 2            FUJLG
#> 
#> $G
#>   legislator
#> 1     GARLON
#> 2     GARZON
#> 3      GOMEZ
#> 4   GONZALEZ
#> 
#> $I
#>   legislator
#> 1   ITURBIDE
#> 
#> $J
#>   legislator
#> 1 JUiHANELLl
#> 
#> $M
#>        legislator
#> 1        MARTINEZ
#> 2     miRBTDE Pid
#> 3         MOREIRA
#> 4  MORENO ERADLOS
#> 5   MORENO RALLOS
#> 6 MORENO ZEBALLOS
#> 
#> $O
#>   legislator
#> 1        OIS
#> 2      OLEHO
#> 3      OTERO
#> 
#> $P
#>   legislator
#> 1 PUaU tSiio
#> 2       PUIG
#> 3       PUTG
#> 
#> $R
#>            legislator
#> 1                  RO
#> 2     RODRIGLEZ ROCHA
#> 3 RODRIGUEZ R O C H A
#> 4     RODRIGUEZ ROCHA
#> 5                 ROT
#> 
#> $S
#>   legislator
#> 1    SALGADO
#> 
#> $T
#>   legislator
#> 1    TIJBINO
#> 2      TROIT
#> 3   TROITIÑO
#> 4   TTERBIDE
#> 5    TTJBINO
#> 6    TTTBTNO
#> 7     TUBINO
#> 8     TURINO
```

Solucionado este problema ahora pasamos al problema de los nombres de
los legisladores que son varios:

``` r
text <- speech_legis_replace(text, old = "ARISMENDT",     new = "ARISMENDI")
text <- speech_legis_replace(text, old = "BURANELL",      new = "BURANELLI")
text <- speech_legis_replace(text, old = "BTJRANELLI",    new = "BURANELLI")
text <- speech_legis_replace(text, old = "JUiHANELLl",    new = "BURANELLI")
text <- speech_legis_replace(text, old = "BKANE",         new = "BURANELLI")
text <- speech_legis_replace(text, old = "CAl l e r IZA", new = "CALLERIZA")
text <- speech_legis_replace(text, old = "CALLERTZA",     new = "CALLERIZA")
text <- speech_legis_replace(text, old = "CAUTERIZA",     new = "CALLERIZA")
text <- speech_legis_replace(text, old = "CERSOSTMO",     new = "CERSOSIMO")
text <- speech_legis_replace(text, old = "CHOTJHY TERRA", new = "CHOUHY TERRA")
text <- speech_legis_replace(text, old = "GARLON",        new = "GARZON")
text <- speech_legis_replace(text, old = "PUTG",          new = "PUIG")
text <- speech_legis_replace(text, old = "PUaU tSiio",    new = "PUIG")
text <- speech_legis_replace(text, old = "FUJLG",         new = "PUIG")
text <- speech_legis_replace(text, old = "TIJBINO",       new = "TUBINO")
text <- speech_legis_replace(text, old = "TTJBINO",       new = "TUBINO")
text <- speech_legis_replace(text, old = "TTTBTNO",       new = "TUBINO")
text <- speech_legis_replace(text, old = "TURINO",        new = "TUBINO")
text <- speech_legis_replace(text, old = "TTERBIDE",      new = "ITURBIDE")
text <- speech_legis_replace(text, old = "miRBTDE Pid",   new = "ITURBIDE")
text <- speech_legis_replace(text, old = "TROIT",         new = "TROITIÑO")
text <- speech_legis_replace(text, old = "MORENO ERADLOS",new = "MORENO ZEBALLOS")
text <- speech_legis_replace(text, old = "MORENO RALLOS", new = "MORENO ZEBALLOS")
text <- speech_legis_replace(text, old = "OLEHO",         new = "OTERO")
text <- speech_legis_replace(text, old = "RO",            new = "RODRIGUEZ ROCHA")
text <- speech_legis_replace(text, old = "BREÑA",         new = "BRENA")
text <- speech_legis_replace(text, old = "RODRIGUEZ R O C H A",       new = "RODRIGUEZ ROCHA")
text <- speech_legis_replace(text, old = "RODRIGLEZ ROCHA",           new = "RODRIGUEZ ROCHA")
text <- speech_legis_replace(text, old = "ROT",                       new = "RODRIGUEZ ROCHA")
text <- speech_legis_replace(text, old = "BRUNEREAU DES HOUILLERES",  new = "BRUNERAU DES HOUILLERES")


speech_check(text)
#> $A
#>   legislator
#> 1    ANTUNEZ
#> 2  ARISMENDI
#> 
#> $B
#>                legislator
#> 1                   BRENA
#> 2 BRUNERAU DES HOUILLERES
#> 3                   BRUNO
#> 4               BURANELLI
#> 
#> $C
#>     legislator
#> 1    CALLERIZA
#> 2      CARDOSO
#> 3    CERSOSIMO
#> 4 CHOUHY TERRA
#> 
#> $D
#>     legislator
#> 1 DAMBORIARENA
#> 
#> $E
#>   legislator
#> 1   ESPALTER
#> 
#> $F
#>         legislator
#> 1 FERNANDEZ CRESPO
#> 
#> $G
#>   legislator
#> 1     GARZON
#> 2      GOMEZ
#> 3   GONZALEZ
#> 
#> $I
#>   legislator
#> 1   ITURBIDE
#> 
#> $M
#>        legislator
#> 1        MARTINEZ
#> 2         MOREIRA
#> 3 MORENO ZEBALLOS
#> 
#> $O
#>   legislator
#> 1        OIS
#> 2      OTERO
#> 
#> $P
#>   legislator
#> 1       PUIG
#> 
#> $R
#>        legislator
#> 1 RODRIGUEZ ROCHA
#> 
#> $S
#>   legislator
#> 1    SALGADO
#> 
#> $T
#>   legislator
#> 1   TROITIÑO
#> 2     TUBINO
```

Como podemos ver fueron varias las correcciones que se hicieron. Pero al
volver a chequear podemos verificar que la base de datos luce
perfectamente limpia. Previo a compilar es necesario cargar los datos
faltantes de dos variables claves: la legislatura (`legislature`) y la
cámara (`chamber`). Luego podemos pasar a compilar el diario de sesión.

``` r
text$legislature <- 33
text$chamber     <- "CAMARA DE REPRESENTANTES"


text2 <- speech_recompiler(text)
#> Warning: Variables that are in 'compiler_by' contain NA values: date
print(text2, n = Inf)
#> # A tibble: 27 x 7
#>    legislator    legislature chamber    date       id       sex speech          
#>    <chr>               <int> <chr>      <date>     <chr>  <dbl> <chr>           
#>  1 ANTUNEZ                33 CAMARA DE~ NA         speec~     1 "SEÑOR ANTUNEZ.~
#>  2 ARISMENDI              33 CAMARA DE~ NA         speec~     1 "SEÑOR ARISMEND~
#>  3 BRENA                  33 CAMARA DE~ NA         speec~     1 "SEÑOR BREÑA. ,~
#>  4 BRUNERAU DES~          33 CAMARA DE~ NA         speec~     1 "SEÑOR BRUNEREA~
#>  5 BRUNO                  33 CAMARA DE~ NA         speec~     1 "SEÑOR BRUNO, —~
#>  6 BURANELLI              33 CAMARA DE~ NA         speec~     1 "SEÑOR B0KANEHl~
#>  7 CALLERIZA              33 CAMARA DE~ NA         speec~     1 "SEÑOR CAl l e ~
#>  8 CARDOSO                33 CAMARA DE~ NA         speec~     1 "SEÑOR CARDOSO.~
#>  9 CERSOSIMO              33 CAMARA DE~ NA         speec~     1 "SEÑOR CERSOSIM~
#> 10 CHOUHY TERRA           33 CAMARA DE~ NA         speec~     1 "SEÑOR CHOTJHY ~
#> 11 DAMBORIARENA           33 CAMARA DE~ NA         speec~     1 "SEÑOR DAMBORIA~
#> 12 ESPALTER               33 CAMARA DE~ NA         speec~     1 "SEÑOR ESPALTER~
#> 13 FERNANDEZ CR~          33 CAMARA DE~ NA         speec~     1 "SEÑOR FERNANDE~
#> 14 GARZON                 33 CAMARA DE~ NA         speec~     1 "SEÑOR GARZON. ~
#> 15 GOMEZ                  33 CAMARA DE~ NA         speec~     1 "SEÑOR GOMEZ. —~
#> 16 GONZALEZ               33 CAMARA DE~ NA         speec~     1 "SEÑOR GONZALEZ~
#> 17 ITURBIDE               33 CAMARA DE~ NA         speec~     1 "SEÑOR ITURBIDE~
#> 18 MARTINEZ               33 CAMARA DE~ NA         speec~     1 "SEÑOR MARTINEZ~
#> 19 MOREIRA                33 CAMARA DE~ NA         speec~     1 "SEÑOR MOREIRA.~
#> 20 MORENO ZEBAL~          33 CAMARA DE~ NA         speec~     1 "SEÑOR MORENO Z~
#> 21 OIS                    33 CAMARA DE~ NA         speec~     1 "SEÑOR OIS. — P~
#> 22 OTERO                  33 CAMARA DE~ NA         speec~     1 "SEÑOR OTERO. —~
#> 23 PUIG                   33 CAMARA DE~ NA         speec~     1 "SEÑOR PUIG. — ~
#> 24 RODRIGUEZ RO~          33 CAMARA DE~ NA         speec~     1 "SEÑOR RODRIGUE~
#> 25 SALGADO                33 CAMARA DE~ NA         speec~     1 "SEÑOR SALGADO.~
#> 26 TROITIÑO               33 CAMARA DE~ NA         speec~     1 "SEÑOR TROITIÑO~
#> 27 TUBINO                 33 CAMARA DE~ NA         speec~     1 "SEÑOR TUBINO. ~
```

Como se observa, el objeto `text2` que contiene nuestro diario de sesión
en formato base de datos luce ordenado y limpio. Lo único que resta
hacer es agregar la etiqueta partidaria con el paquete `puy`.

``` r

library(puy)

text2 <- add_party(text2)

print(text2, n = nrow(text2))
#> # A tibble: 31 x 12
#>    legislator  legislature chamber  date       id     speech     sex legislator2
#>    <chr>             <int> <chr>    <date>     <chr>  <chr>    <dbl> <chr>      
#>  1 ANTUNEZ              33 CAMARA ~ NA         speec~ "SEÑOR ~     1 ANTUNEZ MA~
#>  2 ARISMENDI            33 CAMARA ~ NA         speec~ "SEÑOR ~     1 ARISMENDI,~
#>  3 BRENA                33 CAMARA ~ NA         speec~ "SEÑOR ~     1 BRENA, Tom~
#>  4 BRUNERAU D~          33 CAMARA ~ NA         speec~ "SEÑOR ~     1 BRUNERAU D~
#>  5 BRUNO                33 CAMARA ~ NA         speec~ "SEÑOR ~     1 BRUNO, Jos~
#>  6 BURANELLI            33 CAMARA ~ NA         speec~ "SEÑOR ~     1 BURANELLI,~
#>  7 CALLERIZA            33 CAMARA ~ NA         speec~ "SEÑOR ~     1 CALLERIZA,~
#>  8 CARDOSO              33 CAMARA ~ NA         speec~ "SEÑOR ~     1 CARDOSO, J~
#>  9 CERSOSIMO            33 CAMARA ~ NA         speec~ "SEÑOR ~     1 CERSOSIMO,~
#> 10 CHOUHY TER~          33 CAMARA ~ NA         speec~ "SEÑOR ~     1 CHOUHY TER~
#> 11 CHOUHY TER~          33 CAMARA ~ NA         speec~ "SEÑOR ~     1 CHOUHY TER~
#> 12 DAMBORIARE~          33 CAMARA ~ NA         speec~ "SEÑOR ~     1 DAMBORIARE~
#> 13 ESPALTER             33 CAMARA ~ NA         speec~ "SEÑOR ~     1 ESPALTER, ~
#> 14 FERNANDEZ ~          33 CAMARA ~ NA         speec~ "SEÑOR ~     1 FERNANDEZ ~
#> 15 GARZON               33 CAMARA ~ NA         speec~ "SEÑOR ~     1 GARZON, Ex~
#> 16 GOMEZ                33 CAMARA ~ NA         speec~ "SEÑOR ~     1 GOMEZ, Eug~
#> 17 GONZALEZ             33 CAMARA ~ NA         speec~ "SEÑOR ~     1 GONZALEZ, ~
#> 18 GONZALEZ             33 CAMARA ~ NA         speec~ "SEÑOR ~     1 GONZALEZ, ~
#> 19 GONZALEZ             33 CAMARA ~ NA         speec~ "SEÑOR ~     1 GONZALEZ, ~
#> 20 GONZALEZ             33 CAMARA ~ NA         speec~ "SEÑOR ~     1 GONZALEZ, ~
#> 21 ITURBIDE             33 CAMARA ~ NA         speec~ "SEÑOR ~     1 ITURBIDE, ~
#> 22 MARTINEZ             33 CAMARA ~ NA         speec~ "SEÑOR ~     1 MARTINEZ, ~
#> 23 MOREIRA              33 CAMARA ~ NA         speec~ "SEÑOR ~     1 MOREIRA, J~
#> 24 MORENO ZEB~          33 CAMARA ~ NA         speec~ "SEÑOR ~     1 MORENO ZEB~
#> 25 OIS                  33 CAMARA ~ NA         speec~ "SEÑOR ~     1 OIS, Martin
#> 26 OTERO                33 CAMARA ~ NA         speec~ "SEÑOR ~     1 OTERO, Juan
#> 27 PUIG                 33 CAMARA ~ NA         speec~ "SEÑOR ~     1 PUIG, Vent~
#> 28 RODRIGUEZ ~          33 CAMARA ~ NA         speec~ "SEÑOR ~     1 RODRIGUEZ ~
#> 29 SALGADO              33 CAMARA ~ NA         speec~ "SEÑOR ~     1 SALGADO, R~
#> 30 TROITIÑO             33 CAMARA ~ NA         speec~ "SEÑOR ~     1 TROITIÑO, ~
#> 31 TUBINO               33 CAMARA ~ NA         speec~ "SEÑOR ~     1 TUBINO, Ar~
#> # ... with 4 more variables: party <chr>, party_acron <chr>, indicator <int>,
#> #   words <int>
```

Lo que surge de agregar la etiqueta partidaria es que la mayoría de los
políticos están en la base de `politicos` del paquete `puy`. Pero el
problema es que hay datos duplicados con distinto nombre de legislador.
Es decir, en dos casos hay más de un legislador que tiene el mismo
apellido en la misma legislatura. En estos casos la solución es ir al
diario de sesión y verificar en asistencia quien es el político que
actuó en esa sesión. En este caso el nombre de los dos políticos que
son de esta sesión son: `CHOUHY TERRA, Jose L.` y `GONZALEZ, Carmelo R`.
De esta manera procedemos a eliminar a los otros legisladores y podemos
dar por terminado el procesamiento de este diario de sesión.

``` r
text2 <- text2[-c(11, 17, 18, 19),] # marcamos las filas en las que están los legisladores que se deben ir
print(text2[, c(1,2, 7:ncol(text2))], n = Inf)
#> # A tibble: 27 x 8
#>    legislator  legislature   sex legislator2  party  party_acron indicator words
#>    <chr>             <int> <dbl> <chr>        <chr>  <chr>           <int> <int>
#>  1 ANTUNEZ              33     1 ANTUNEZ MAC~ Parti~ PCGR                2   334
#>  2 ARISMENDI            33     1 ARISMENDI, ~ Parti~ PC                  1   221
#>  3 BRENA                33     1 BRENA, Tomas Union~ UC                  1   271
#>  4 BRUNERAU D~          33     1 BRUNERAU DE~ Parti~ PC                  1    11
#>  5 BRUNO                33     1 BRUNO, Jose~ Parti~ PN                  1   475
#>  6 BURANELLI            33     1 BURANELLI, ~ Parti~ PN                  1   401
#>  7 CALLERIZA            33     1 CALLERIZA, ~ Parti~ PC                  1   556
#>  8 CARDOSO              33     1 CARDOSO, Jo~ Parti~ PS                  1   167
#>  9 CERSOSIMO            33     1 CERSOSIMO, ~ Parti~ PC                  1   969
#> 10 CHOUHY TER~          33     1 CHOUHY TERR~ Parti~ PC                  1   180
#> 11 DAMBORIARE~          33     1 DAMBORIAREN~ Parti~ PN                  1   506
#> 12 ESPALTER             33     1 ESPALTER, A~ Parti~ PC                  1    48
#> 13 FERNANDEZ ~          33     1 FERNANDEZ C~ Parti~ PN                  1  1405
#> 14 GARZON               33     1 GARZON, Exe~ Parti~ PC                  1   636
#> 15 GOMEZ                33     1 GOMEZ, Euge~ Parti~ PCU                 1   374
#> 16 GONZALEZ             33     1 GONZALEZ, C~ Parti~ PN                  1    58
#> 17 ITURBIDE             33     1 ITURBIDE, J~ Parti~ PC                  1   980
#> 18 MARTINEZ             33     1 MARTINEZ, E~ Parti~ PC                  1   332
#> 19 MOREIRA              33     1 MOREIRA, Ju~ Parti~ PC                  1   743
#> 20 MORENO ZEB~          33     1 MORENO ZEBA~ Parti~ PC                  1   822
#> 21 OIS                  33     1 OIS, Martin  Parti~ PN                  1   424
#> 22 OTERO                33     1 OTERO, Juan  Parti~ PC                  1   105
#> 23 PUIG                 33     1 PUIG, Ventu~ Parti~ PN                  1  1129
#> 24 RODRIGUEZ ~          33     1 RODRIGUEZ R~ Parti~ PC                  1  1053
#> 25 SALGADO              33     1 SALGADO, Ra~ Parti~ PN                  1  1285
#> 26 TROITIÑO             33     1 TROITIÑO, L~ Parti~ PS                  1  1084
#> 27 TUBINO               33     1 TUBINO, Arm~ Parti~ PN                  1   540
```

### Ejemplo 3

#### :arrow\_forward: Un diario de sesión reciente de una sesión convencional ce la Comisión Permanente del 17 de setiembre de 2019

Tomamos un diario de sesión reciente y aplicamos el mismo criterio que
con los diarios anteriores: usamos
[`speech_build()`](https://nicolas-schmidt.github.io/speech/reference/speech_build.html)
sin compilar para chequear que los nombres de los legisladores sean
correctos para luego compilar.

``` r
url <- "https://parlamento.gub.uy/documentosyleyes/documentos/diario-de-sesion/comisionpermanente/6084/IMG/0?width=800&height=600&hl=en_US1&iframe=true&rel=nofollow"

text <- speech::speech_build(file = url)

print(text, n = nrow(text))
#> # A tibble: 24 x 7
#>    legislator speech          chamber   date       legislature id            sex
#>    <chr>      <chr>           <chr>     <date>           <int> <chr>       <dbl>
#>  1 BORDABERRY SEÑOR BORDABER~ COMISION~ 2019-09-17          48 0?width=80~     1
#>  2 BORDABERRY SEÑOR BORDABER~ COMISION~ 2019-09-17          48 0?width=80~     1
#>  3 AVIAGA     SEÑORA AVIAGA.~ COMISION~ 2019-09-17          48 0?width=80~     0
#>  4 AVIAGA     SEÑORA AVIAGA.~ COMISION~ 2019-09-17          48 0?width=80~     0
#>  5 GOÑI       SEÑOR GOÑI. Pi~ COMISION~ 2019-09-17          48 0?width=80~     1
#>  6 GOÑI       SEÑOR GOÑI. El~ COMISION~ 2019-09-17          48 0?width=80~     1
#>  7 MAHIA      SEÑOR MAHIA. P~ COMISION~ 2019-09-17          48 0?width=80~     1
#>  8 MAHIA      SEÑOR MAHIA. G~ COMISION~ 2019-09-17          48 0?width=80~     1
#>  9 ABDALA     SEÑOR ABDALA. ~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 10 ASTI       SEÑOR ASTI. Ob~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 11 AVIAGA     SEÑORA AVIAGA.~ COMISION~ 2019-09-17          48 0?width=80~     0
#> 12 BORDABERRY SEÑOR BORDABER~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 13 GOÑI       SEÑOR GOÑI. Vo~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 14 LAZO       SEÑORA LAZO. V~ COMISION~ 2019-09-17          48 0?width=80~     0
#> 15 MAHIA      SEÑOR MAHIA. V~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 16 MERONI     SEÑOR MERONI. ~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 17 PEREYRA    SEÑORA PEREYRA~ COMISION~ 2019-09-17          48 0?width=80~     0
#> 18 TOURNE     SEÑORA TOURNE.~ COMISION~ 2019-09-17          48 0?width=80~     0
#> 19 VIERA      SEÑOR VIERA. V~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 20 BORDABERRY SEÑOR BORDABER~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 21 BORDABERRY SEÑOR BORDABER~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 22 BORDABERRY SEÑOR BORDABER~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 23 ABDALA     SEÑOR ABDALA. ~ COMISION~ 2019-09-17          48 0?width=80~     1
#> 24 ABDALA     SEÑOR ABDALA. ~ COMISION~ 2019-09-17          48 0?width=80~     1
```

Este diario de sesión no presenta problemas en los nombres de los
legisladores por lo que podemos perfectamente ir directo a la
compilación:

``` r
text <- speech::speech_build(file = url, compiler = TRUE)

print(text, n = nrow(text))
#> # A tibble: 11 x 7
#>    legislator legislature chamber   date       id           speech           sex
#>    <chr>            <int> <chr>     <date>     <chr>        <chr>          <dbl>
#>  1 ABDALA              48 COMISION~ 2019-09-17 0?width=800~ SEÑOR ABDALA.~     1
#>  2 ASTI                48 COMISION~ 2019-09-17 0?width=800~ SEÑOR ASTI. O~     1
#>  3 AVIAGA              48 COMISION~ 2019-09-17 0?width=800~ SEÑORA AVIAGA~     0
#>  4 BORDABERRY          48 COMISION~ 2019-09-17 0?width=800~ SEÑOR BORDABE~     1
#>  5 GOÑI                48 COMISION~ 2019-09-17 0?width=800~ SEÑOR GOÑI. P~     1
#>  6 LAZO                48 COMISION~ 2019-09-17 0?width=800~ SEÑORA LAZO. ~     0
#>  7 MAHIA               48 COMISION~ 2019-09-17 0?width=800~ SEÑOR MAHIA. ~     1
#>  8 MERONI              48 COMISION~ 2019-09-17 0?width=800~ SEÑOR MERONI.~     1
#>  9 PEREYRA             48 COMISION~ 2019-09-17 0?width=800~ SEÑORA PEREYR~     0
#> 10 TOURNE              48 COMISION~ 2019-09-17 0?width=800~ SEÑORA TOURNE~     0
#> 11 VIERA               48 COMISION~ 2019-09-17 0?width=800~ SEÑOR VIERA. ~     1
```

Ahora podemos agregarle la etiqueta partidaria y el nombre completo a
los legisladores con el paquete [`puy`]():

``` r
library(puy)
text <- add_party(text)

print(text, n = nrow(text))
#> # A tibble: 11 x 12
#>    legislator legislature chamber  date       id      speech     sex legislator2
#>    <chr>            <int> <chr>    <date>     <chr>   <chr>    <dbl> <chr>      
#>  1 ABDALA              48 COMISIO~ 2019-09-17 0?widt~ SEÑOR A~     1 ABDALA, Pa~
#>  2 ASTI                48 COMISIO~ 2019-09-17 0?widt~ SEÑOR A~     1 ASTI, Alfr~
#>  3 AVIAGA              48 COMISIO~ 2019-09-17 0?widt~ SEÑORA ~     0 AVIAGA, Ca~
#>  4 BORDABERRY          48 COMISIO~ 2019-09-17 0?widt~ SEÑOR B~     1 BORDABERRY~
#>  5 GOÑI                48 COMISIO~ 2019-09-17 0?widt~ SEÑOR G~     1 GOÑI ROMER~
#>  6 LAZO                48 COMISIO~ 2019-09-17 0?widt~ SEÑORA ~     0 LAZO, Sand~
#>  7 MAHIA               48 COMISIO~ 2019-09-17 0?widt~ SEÑOR M~     1 MAHIA, Jos~
#>  8 MERONI              48 COMISIO~ 2019-09-17 0?widt~ SEÑOR M~     1 <NA>       
#>  9 PEREYRA             48 COMISIO~ 2019-09-17 0?widt~ SEÑORA ~     0 PEREYRA, S~
#> 10 TOURNE              48 COMISIO~ 2019-09-17 0?widt~ SEÑORA ~     0 TOURNE, Da~
#> 11 VIERA               48 COMISIO~ 2019-09-17 0?widt~ SEÑOR V~     1 VIERA, Tab~
#> # ... with 4 more variables: party <chr>, party_acron <chr>, indicator <int>,
#> #   words <int>
```

Debemos chequear si tenemos la etiqueta partidaria para todos los
legisladores:

``` r
text[, c(1,2, 7:ncol(text))]
#> # A tibble: 11 x 8
#>    legislator legislature   sex legislator2   party  party_acron indicator words
#>    <chr>            <int> <dbl> <chr>         <chr>  <chr>           <int> <int>
#>  1 ABDALA              48     1 ABDALA, Pablo Parti~ PN                  1   400
#>  2 ASTI                48     1 ASTI, Alfredo Frent~ FA                  1    46
#>  3 AVIAGA              48     0 AVIAGA, Carol Parti~ PN                  1   107
#>  4 BORDABERRY          48     1 BORDABERRY, ~ Parti~ PC                  1   963
#>  5 GOÑI                48     1 GOÑI ROMERO,~ Parti~ PN                  2   100
#>  6 LAZO                48     0 LAZO, Sandra  Frent~ FA                  3   103
#>  7 MAHIA               48     1 MAHIA, Jose ~ Frent~ FA                  1   128
#>  8 MERONI              48     1 <NA>          <NA>   <NA>               NA    12
#>  9 PEREYRA             48     0 PEREYRA, Sus~ Frent~ FA                  1    12
#> 10 TOURNE              48     0 TOURNE, Daisy Frent~ FA                  1   111
#> 11 VIERA               48     1 VIERA, Tabare Parti~ PC                  1     8
```

Como se puede observar hay un solo diputado o senador que la función
[`puy::add_party()`]() no logró identificar. Seguramente sea un diputado
o senador suplente. Dependiendo del análisis posterior que se quiera
hacer es si se busca por fuentes alternativas la filiación de este
legislador o se lo puede descartar. La función [`puy::add_party()`]()
además de agregar el partido y el nombre completo del legislador agrega
una variable a partir de la función del paquete `speech` que hace un
conteo de palabras:
[`speech_word_count()`](https://nicolas-schmidt.github.io/speech/reference/speech_word_count.html).
Esta variable puede ser de utilidad para estos casos por ejemplo, es
decir, descartar a este legislador puede que no tenga un impacto
significativo ya que solo dice 10 palabras (las cuales seguramente sean:
“Si voto…”).

## Usos

En esta sección se deja constancia del uso que distintos usuarios hacen
o han hecho del paquete `speech`.

  - **La temática de género en el Parlamento uruguayo**:
    [aquí](https://www.elinagomez.com/blog/2020-09-25-parlamento-genero/#fnref2)
      - [Elina Gómez](https://www.elinagomez.com/)

## Aplicación web

:construction: :construction: :construction: :construction:
