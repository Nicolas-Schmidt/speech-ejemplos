
## Ejemplos en español del paquete `speech` <img src='figures/logo.png' align="right" height="180" />

*Nicolas Schmidt, Diego Lujan, Juan Andres Moraes*

### Descripción

El paquete `speech` permite convertir diarios de sesión del parlamento
uguguayo en formato PDF a bases de datos ordenadas en la que cada fila
es la intervencion de cada uno de los legisladores que intervino en esa
sesión.

### Ejemplo

Es importante tener presente que la lectura del texto de los archivos
PDF de los diarios de sesion no es siempre clara. Muchos de los diarios
de sesión que están en la página
[web](https://parlamento.gub.uy/documentosyleyes/documentos/diarios-de-sesion)
del parlamento están escaneados.

En algunas ocasiones antes de convertir el documento PDF que contiene un
diario de sesión mediante el uso de la función `speech_build()` va a ser
necesario pasar el PDF por un OCR (reconocimiento óptico de caracteres
por su sigla en ingles).

``` r
library(speech)
url <- "https://parlamento.gub.uy/documentosyleyes/documentos/diario-de-sesion/comisionpermanente/6084/IMG/0?width=800&height=600&hl=en_US1&iframe=true&rel=nofollow"
text <- speech::speech_build(file = url)
text
#> # A tibble: 24 x 6
#>    legislator speech         chamber  date                legislature id        
#>    <chr>      <chr>          <chr>    <dttm>                    <dbl> <chr>     
#>  1 BORDABERRY SEÑOR BORDABE~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#>  2 BORDABERRY SEÑOR BORDABE~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#>  3 AVIAGA     SEÑORA AVIAGA~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#>  4 AVIAGA     SEÑORA AVIAGA~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#>  5 GOI        SEÑOR GOÑI. P~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#>  6 GOI        SEÑOR GOÑI. E~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#>  7 MAHIA      SEÑOR MAHIA. ~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#>  8 MAHIA      SEÑOR MAHIA. ~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#>  9 ABDALA     SEÑOR ABDALA.~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#> 10 ASTI       SEÑOR ASTI. O~ COMISIO~ 2019-09-17 00:00:00          48 0?width=8~
#> # ... with 14 more rows
```
