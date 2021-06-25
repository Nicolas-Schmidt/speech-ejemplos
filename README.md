
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
```

Con esta función lo que hacemos es modificar la base construida (es
decir el objeto `text`) por eso debemos sobrescribirla.

Ahora debemos chequear que los cambios se realizaron de manera correcta:

``` r
speech_check(text)
#> $C
#>   legislator
#> 1   CIGLIUTI
#> 2    CORDOSO
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

Los problemas no se acaban acá. Exploremos los discursos propiamente
dichos:

``` r
text$speech[1]
#> [1] "SEÑOR PER. EYRA. Señor Presidente, señores legisladores: la bancada de legisladores de'l Partido Nacional me ha conferido el inmenso honor de designarme para hacer uso de la palabra en este acto en que el pueblo uruguayo simboliza adecuadamente 'la restauración de sus mejores tradiciones, honor inmenso que !Jrocuraré cumplir en la medida de mis posibilidades. Cualquiera de los otros ,compañeros quizás Y sin quizá~~. estoy segurolo hubieran hecho mejor que yo, Naturalmente quien debió estar en esta banca pam dirigir la palabra en nombre de todo el partido en este día singula~ no puede hacerlo porque la arbitraz:iedad de la dictadura lo tuvo proscripto, preso, impidi·é'Ildole ser candidato: me refiero al señor Wilson Ferreira Aldunate. ( Ap'lausos en la Sala y en la Barra). Tal vez entonces, además de esa razón haya decidido a mis compañeros el hecho de que soy uno de los qm\" tuve o tengoel privilegio naturalmente en un comienzo doloroso y en un final triunfal como el de esta nochede haber estado presente en el episodio de la madrugada amarga del 27 de junio de 1973 y hoy en la •inauguración de esta Legislatura. Aquella noche dijimos, recordando palabras que en circunstancias parecidas habían dicho otros legisladores que esperábamos que pronto volvieran a resonar en el recinto de'l Palacio de las Leyes la voz de los hombres libres. Hoy están de nuevo aquí los hombres libres, los que se sientan aquí no por la designación arbitraria de ningún mandón, sino, por· el voto libre de un pueblo que elige en ellos a sus auténticos representantes. (Aplausos en la Sala y en la Barra). No alteremos el orden de las cosas. Aquí nosotros somos representantes del pueblo; y él es el verdadero protagonista. En el ciclo que hoy se cierra el verdadero triunfador ha sido el pueblo uruguayo en una lucha de doce años, jalonada por inmensos sacrificios y padecimientos. Doce años proyectados en la historia de la humanidad parecen poco tiempo, pero doce años de sufrimiento, de dolor, de amargura, de persecusión de todo tipo como los que arrojó la dictadura castigando a vn pueblo 'libre, es demasiado tiempo. Por eso en esta hora tenemos que señalar que gracias a la persevPran.!ia y al espíritu de lucha de ese pueblo uruguayo retornan hoy aquí sus auténticos representantes. Esto no es un regalo de la dictadura; esta elección y esta inauguración de una nueva etapa democrática en el país es la consecuencia directa de la lucha permanente del pueblo urllguayo por reconquistar sus libertades y sus derechos. (Aplausos en la Sala y en la Barra). Recordemos si no, la jornada del plebiscito de 1980 cuando la dictadura militar buscó, mediante una reforma constitucional, 'institucionalizarse y con todos los medios a su alcance, con toda la maquinaria oficial en juego, con toda la impl1nidad que 'le daba el eclipse de la Constitución y de las leyes del país, con todo el poder que tenía en sus manos, con todo el terror que había pretendido infundir én el ánimo de cada uno de los ·,uuguayos, el pueblo con el arma civilizada del sufragio, derrotó a todo el peso de las armas de la dictadura, ciejando abierto el camino para la institucionalidad democrática del país. ( kpla usos en 'la Sala y en la Barra) . Y fue el pueblo el que en las elecciones internas de 1982, cuando los amigos del régimen creyeron que podían apoderarse de la dirección de los partidos políticos, fue e'l pueblo democrático de los partidos el que dijo no a ese nuevo intento de la dictadura de mantenerse en el poder. Y fue el pueblo el que en aquella jornada memorable en torno al Obelisco a los Constituyentes de 1830, repito, se comprometió, como se comprometi·cron todos 'los partidos políticos, a la restauración plena de la democracia, de las libertades y derechos que e aracterizan la vida civlilizada. (Aplausos en la Sala y en la Barra). Durante estos doce años los políticos fuimos atacados y difamados groseramente. No había oracior .. Gfic.ial que no eligiera, precisamente, los días en que se rinde homenaje a la Patl\"ia para denostar a los políticos. Nosotros éramos los corruptos y los culpables de toda la desgracia nacional, según 1os gobernantes de facto. Artigas decia: \"Déjelos que hablen, que el tiempo es el mejor testigo\". Y naturalmente que hóy comprobamos que el tiempo es el mejor testigo cuando le muestra a los agentes de la dictadura que e'l pueblo uruguayo, libre de ellos, hoy sienta aquí a sus auténticos y legítimos representantes para que, !interpretando su voluntad y su sentir, emprendan la hermosa tarea de la reconstrucción nacional. (Aplausos en 'la Sala y en la Barra) . Ante tanto denuesto para con los p~lticos creo interpretar el sentir de todos los hombres público; que están sentados en esta Sala, cualquiera sea su ideologia, si leo el pensamiento de un gran legislador me refiero Senado, en un debate trascendental sobre el valor de la al doctor Javier Barrios Amorinque en el seno_ del política y de los políticos, expresara: \"~i sobre la tierra se produjeran milagros, si fuera posible al hombre volver a vivir el tiempo que ha vivido y si fuera posible elegir el destino a cumplir de nuevo, e'legiría otra vez el del .político\". Creo que eso vale, señores .legislad?res, para cada uno de nosotros que estamos aqui, cumphendo con nuestra vocación y con un deber ciudadano, para cada uno de nosotros que estamos aquí tratando de responder, en la medida de nuestras posibilidades, a los requerimientos de los má:s altos intereses de la Nación. y bien; estamos entonces en esta augusta Sala para volver a levantar a través de nuestra voz, la de aquellos que nos han designado sus representantes. Es en este momento que venimos a reafirmar nuestra fe _Pn _la \"libertad como la única forma de sostener la dignidad del ho~bre· ven:imos a reafirmar nuestra fe en la J·Jsticia, como sostén indispensable de esa libertad; venimos a reafirmar el triunfo de la augusta voluntad del Derecho sobre la fuerza, el imperio de la razón. sobre la ceguera de las pasiones sedientas de poderes mcontrolados. Por eso en esta hora,somos los mensajeros de la civilización frei:lte al oscurantismo y la barbarie. En un mundo enfermo de violencia, venimos a proclamar la pacífica convivencia de los seres humanos, cualesquiera sean sus ideales. Ve~imos a condenar .. toda expresión de terrorismo, mensaJero de la destrucc10n Y de 'la muerte. El terrorismo debe ser cond~nado, cualquiera sea su signo: aquél que suelen practicar algunos invocando la defensa de los oprimidos Y aquél de los que ahogar la legítima razón de éstos a alcanzar la plenitud de sus derechos. confiemos a los hombres la dilucida~!ón de sus. diferencias por medio de la libre c·onfrontacion .~e las ~de~, lo que siempre debe culminar en 'la expreSIOJ?autentica y soberana del sufragio popular. No será fácil v:encer a los mensajeros del terrorismo de uno u otro signo, en un mundo reitero enfermo de violencia, que además está sujeto a 1~ lucha' por la hegemonía mundial entre dos imperialismos políticos que no vacilan en avasallar co~­ tantemente la soberanía de los pueblos;, ~n un mundo, !Inalmente, donde 'los imperialismos pollticos s~elen ~eJ_ar su sitio a otro no menos peligroso, que es e~ rmpenahsmo económico, nueva fuerza que procura sOJ~zgar a los pueblos, no con las armas sino con el ~mpuJe formidable de la asociación de 'los grandes capitales mundiales que se unen para expoliar a los pueblos débiles. Y bien, señores legisladores: ha terminado la larga noche de 'la dictadura, la larga noche sembrada de terror y de muerte, de crímenes y de tortura. Natur:Imente, entre las vidas segadas, que son muchas, el senor PrP.sidente ha evocado, con toda razón Y yo no puedo dejar de hacerlolos nombres queridos de Zelmar Michelini y de Héctor Gutiérrez Ruiz. (Aplausos en la Sala y en 'la Barra). No alentamos ningún deseo de venganza pero creemos que estos crímenes no pueden quedar impunes. (Aplausos en la Sala y en la Barra). Por lo tanto, bregaremos para que este Parlamento designe una Comisión Investigadora que procure establecer la verdad y encontrar a los culpables para que tengan el condigno castigo. Entretanto, naturalmente, aqm sentiremos siempre la ausencia de aquellos queridos amigos: la simpatía rebosante, la sonrisa atrayente, la voz grave de Héctor Gutiérrez Ruiz y el repiqueteo bril'lantísimo que caracterizaba a aquellos discursos, dichos con una rapidez vertiginosa y profundos conceptos de Zelmar Michelini. (Aplausos en la Sala y en la Barra). Aquí faltará su presenoia física, pero cada vez que en este Parlamento se hable de libertad, cada vez que se hable de reafirmar los derechos del hombre, cada vez que haya que evocar a las víctimas de esta dictadura, estará presente en nuestro espíritu, junto a nuestra Mesa de trabajo, el recuerdo imborrable de Zelmar Michelini y de Héctor Guttérrez Ruiz. (Aplausos en la Sala y en la Barra). También habrá que investigar otros hechos, muchos, seguramente. En este momento viene a mi memoria aquel atentado de 'la botella de vino envenenado que cortara la vida de Cecilia Fontana de Heber, causa, entre otras, de la muerte de aquel querido legislador que fuera Mario Heber. (Aplausos en la Sala y en la Barra). En esta hora, además, queremos expresar nuestra so·lida!\"lidad con todos los perseguidos; cualquiera sea la forma de persecusión que hayan sufrido, porque ello está reñido con el más elemental respeto por los derechos del hombre. Para quienes están presos por razones políticas vamos a reclamar amnistía total ... (Aplausos en la Sala y en la Barra). ... y para aque11os que por sus ideas políticas fueron sancionados con la pérdida de su trabajo eastigo preferido de la dictadura pará los hombres libres de nuestro pueblopues para ellos tendrá que venir y cuanto antes la legítima reparación que implica la primera etapa, por lo menos, de la restitución. (Aplausos en la Sala y en la Barra). Dentro de las cosas que, además de los daños ya señalados, tenemos la obligación de recalcar hoy, es el perjuicio que ha sufrido nuestra enseñanza que fue, un tiempo, motivo de legítimo orgullo para todos nosotros. Una enseñanza que ha sido deshecha en todos sus aspectos, con profesores perseguidos en razón de sus ideas políticas, destituidos y presos por ese motivo; sus lugares ocupados por mediocres o incapaces, ·causando así el más tremendo de los daños: el daño moral, que puede repercutir sobre las nuevas generaciones. Una enseñanza que tantas veces oímos atacar en este Parlamento, diciendo que en las aulas se hacía proselitismo político y, naturalmente, no negamos que alguna vez ello suced1ió, pero sucedía clandestinamente, en forma oculta, en tanto que en estos 12 años de dictadura el proselitismo se hizo por parte del Gobierno ... (Aplausos en la Sala y en la Barra). ... rompiendo así la tradición del laicismo en la enseñanza y del respeto por la personalidad del educando. También hemos padecido en grado inconcebib'le, facilitado y traído por la dictadura, el mal de la extranjerización del país que abarca todos los aspectos; inclusi ve algunos que en su hora ya denunciamos en la época de la normallidad institucional, como es la enajenación de nuestra principal fuente de riquezas, que es la tierra y que está pasando paulatinamente a manos extránjeras. (Aplausos en la Barra). Naturalmente, a ello se suma la extranjerización de la banca y de todos 'los aspectos de la vida financiera y económica del país. Y hemos padecido, naturalmente, la profundísima crisis económica en que hoy está inmersa la República y cuyos males tenemos la obligación de procurar comenzar a corregir, cumpliendo así nuestra tarea como representes de'l pueblo. Una obstinada y deshumanizada política económica nos: deja una desdichada herencia de carencias y empobrec~miento popular. La tarea, pues, de este Parlamento, es compleja, y no sólo del Parlamento, del país entero. Como lo decía el señor Presidente, 'las sombras de la dictadura no sólo oscurecieron al Uruguay sino a toda América Latina. La conjuración de las espadas pretendió someter y sometió por largo tiempo a los pueblos de Amélrica Latina, aumentando aún más: la cuota de sacrificio que éstos ya padecían. A esta conspiración de las espadas debemos responder con la unidad de los ·gobiernos democráticos, de los partidos democráticos y de los hombres libres de toda América. Hagamos nosotros también un esfuerzo internacional, para defender los legítimos derechos de los hombres y los pueb'los frente al avasallamiento que hacen de sus derechos aquellos que reciben las armas para custodiarlas y las emplean para aplastar los derechos de su pueblo. (Aplausos en la Sala y en la Barra). No venimos hoy a proclamar nuestra victoria, pero sí a exaltar los ideales que han alimentado el espíritu de'l pueblo uruguayo para resistir a esta dictadura, vencerla y abrir las puertas a la institucionalidad democrática. No venimos tampoco, a proclamar la derrota de nadie, persona o instiluc1ión, pero sí a condenar los sentimientos y las pasiones que se proyectaron por encima de los derechos sagrados de los hombres, avasallando su dignidad y todos los aspectos que caracterizan la vida ciVIilizada. Venimos a restaurar un país desecho; venimos a reiterar el espíritu de lucha ·contra toda forma de despotismo, cumpliendo así con la patria del presente y con un mandato histórico. Aque'lla noche del 27 de junio de 1973 dijimos también que el pueblo uruguayo tenía un VJiejo pacto con la libertad, tan viejo como la existencia misma del pueblo oriental y que, por lo tanto, no iban a poder romper ese pacto las fuerzas desatadas de 'la violencia. De la vigencia de ese pacto habla la inauguración de esta Legislatura y habla, con más elocuencia que nosotros aquella frase que pertenece al Jefe de los Orienta'les y que engalana la parte más alta de esta Sala, señalando a todas las generaciones que no existe por encima de 1a voluntad popular ninguna otra en la vida democrática: \"Mi autol'lidad emana de vosotros y ella cesa ante vuestra presencia soberana\". (Aplausos en la Sala y en la Barra) . Que la lean los mandones y que traten de aprender cuánta grandeza define esa expresión de un hombt tl y ·cuánto ha significado ella en toda la historia nacional. Señores !Representantes, señores Legisladores: tenemos ante nosotros un tremendo desafío, quizá el más grande que haya tenido genera'ción alguna de uruguayos y con nuestra presencia aquí, estamos aceptando ese desafío. Cada hombre debe de responder a los problemas de su tiempo. Nosotros somos llamados hoy, todos nosotros, los integrantes de todos los partidos políticos del Uruguay, los representantes de todos los sectores de opinión del Uruguay, a la tarea de reconstrucción de la República. Vamos, entonces, hacia el encuentro con el porvenir; vamos. con el paso 1lirme de los hombres libres; vamos con la fe inquebrantable que emana de un pasado glorioso y de un llamado que viene desde el porvenir, que nos obliga imperativamente a responder a él; vamos a consagrar nuestros esfuerzos, a construir una patria grande y generosa para que sobre ella reinen definitivamente la libertad, la justicia y la paz. (Aplausos en la Sala y en la Barra). La Asamblea se pone de pie."
text$speech[2]
#> [1] "SEÑOR CORDOSO. Señor Presidente de la Asamblea General; señores Diputados; señores Senadores: debo formular una declaración en nombre de la bancada de legisladores del Frente Ampl!io y al comenzarla saludo la significación política y moral de este acto, que es como el sello institucional del fin de la dictadura. Esta significación alcanza un relieve especial, cuando por inicativa de'l señor Presidente hemos rendido homenaje a dos mártires tan vinculados a la vida de esta Casa: Zelmar Michelini y Héctor Gutténez Ruiz. (Aplausos en la Sala y en la Barra). ¿En qué tramo, en qué aspecto de un período funesto de nuestra historia podría incursionar nuestro pensamiento en esta singular circunstancia? ¿En sus causas? ¡,En una discriminación de culpas? ¿En 'los multiplicados sufrimientos !individuales y colectivos? ¿En el quebrantamiento de los valores éticos? ¿En los derechos humanos? Hay un punto de partida en el que los uruguayos deberíamos ·convenir: en una significativa unanimidad que tendría mucho de fecunda, de fermenta!, con valor de jUJicio histórico sobre un pasado reciente y de concepción política aplicable al porvenir. Ello podría expresarse con una frase de dramática sencillez, casi de dramática simplicidad. La nación ha sufrido un tremendo infortunio: 'la dictadura; con ella, una gran desgracia se abatió sobre el Uruguay. Si sus hijos no mantuVIiéramos viva esta convicción de nuestras conciencias, co·rreríamos el riesgo de no ver claro el camino que ha de conducirnos a restañar las heridas y a prevenir la recidiva del mal. Al pensar en los factores determinantes del drama, cuidémonos de esquematismos fáciles. Pero afirmemos por lo menos lo afirmamos nosotros:que no !o comprenderíamos si no admitiésemos, en pr1mer térmmo, que la política económico social que cayó como un azote sobre las famflias uruguayas, especialmente sobre las clases más desposeídas no hubiera podido ser aplica:aa, no hubiera podido ser sostenida si no hubiera sido a sangre y fuego por el poder político en manos de las Fuerzas Armadas. (Aplausos en la Sala y en la Barra). A los sufrimientos de'l llamado costo social, a la brutal caída del salario, a la desocupación creciente, a la sangría de la emigración, a la deuda externa colonialista se sumaron 'los otros sufrimientos, los otros dolores: '¡a destrucción de derechos. de libertades y de garantías; los miles de presos; la tortura como rutina; los muertos; los desaparecidos; las destituciones; los exilios; la burla de una supuesta ley sindical; la proscripción de rpartidos y de hombres, y con ellos no hace falta seguir la enumeración. Séame permitido como colofón de lo que acabo de decir, agregar una frase aunque sea repetición de un concepto ya vertidoque no puedo omitir: ¡No olvlide mos nunca, no olvidemos nunca 'los uruguayos que la política e·conómica antinac1onal y antisocial, fue posible porque fue destruída la democrac'ia! (Aplausos en 'la Sala y en la Barra). La funesta asociación fue cada día más evidente para la conciencia nacional, aleccionada por la resistencia del movimiento obrero y popular a las normas fondomonetaristas y por la violenta represión de que esa resistencia era objeto. Y cuando los regímenes de la seudolega:lidad predictatorial desembocaron en el golpe, la huelga general fue el enfrentamiento con un aparato cív1ico militar llamada así por sus creadoresque unía en sí la acción liberticida y el ahondamiento de la injusticia social. Fue, si invocamos la clásica bandera de las reivindicaciones proletarias la defensa de la libertad y del pan. Fue, aquella huelga, general, la defensa de los instrumentos po'líticos que la clase obrera necesita para. sus conquistas, para ejercer el derecho a luchar por otras más trascendentes y liberadoras. La dictadura apeló a la doctrina de la seguridad nacional con el pretexto y en un intento de demostrar al pueblo los peligros de las penetraciones foráneas. Se produjo así otra alianza: la de la doctrina de la seguridad nacional que de nacional sólo tiene el nombrecon el neoliberalismo de las transnacionales para consagrar como dijo cierta vez un pubNcista chilenoel derecho de'l zorro libre en el gallinero libre. Fue aplicada a peligros inexistentes, viniendo ella sí la doctrina de la seguridad nacionaldesde fuera hacia adentro y no desde dentro hacia fuera para enfrentar la expoliación y la dependenC'ia que comprometían la soberanía ~~ hasta nuestra propia naciona'lidad. Bajo e'l régimen autoritario se abrieron abismos de diferencias y separaciones en la sociedad nacional. El Uruguay vivió como hace un rato decía el señor Senador Pereyrauna larga noche; una larga noche poblada de incertidumbres, de carencias esenciales, extraña a sus mejores tradiciones y al carácter de un pueb'lo solidario y generoso, que no cree que en su país los caminos de la violencia y de la opresión sean los que conduzcan al cambio social que eleve las condiciones materiales y culturades de la vida de los hombres. Comenzó entonces el andar y andar de nuestro pueblo por un camino que, a lo largo de los años, habría de conducir a estos días augurales. Antes de proseguir con la evocación de la lucha liberadora, queremos expresar desde aquí nuestro vivo agradecimiento a la actitud de numerosos pueblos amigos y hermanos que, a través de organizaciones de diversa índole, siempre hicieron presente su so'lidaridad con el afán democrático del pueblo uruguayo. Eso no lo olvidaremos jamás. (Aplausos en la Sala y en la Barra) Para el Frente Amplio desde el primer día el camino trazado fue el no interrumpido esfuerzo para derrotar a la dictadura. Y bien, así como hemos procurado sintetizar casi al extremo, a1gunos conceptos básicos acerca de los lineamientos y de la conducta de la dictadura, también sintéticamente señalaremos la conducta de nuestra gente, si es que puede caber en una frase la riql.leza, la inmensa riqueza cívica y moral de un pueblo que se trazó un camino hacia la libertad y supo recorrerlo con unidad solidaria, pacíficamente, pero con ur. gran esfuerzo cívico, valerosamente decidido a luchar y a triunfar como ha triunfado ahora. Por lo tanto, se han enfrentado largamente dos conductas. Por un 'lado, el autoritarismo cumplía su doble objetivo: introducir en un molde rígido las más variadas manifestaciones de la vida nacional, aplicando la misma dureza en la represión de las reivindicaciones democráticas. Y, por otro lado, la conducta de'l pueblo uruguayo. Nuestro pueblo veía cada día más claramente, a medida que se sucedían los avances y los retrocesos, que era necesario unir todo lo unible para conquistar el derecho a V'ivir y a trabajar, a luchar por so'luciones de justicia y libertad. Y lo que podía Sei' posible, señores, fue realmente posi!ble, para honor de los uruguayos y de la República y se expresó en multitudinarios acontecimientos, cada día más orgánicos, en entendimientos po'líticos y sociales y en una firrríe lealtad a la concepción de la unidad nacional. Consecuentemente, el Frente Amplio ha mantenido ese designio, designio de unirse para triunfar y construir la dem'ocracia, y considera ·que para que nunca más vuelva a caer sobre el pueblo uruguayo el azote de una tiranía es imprescindible consolidar y profundizar la democracia. !Sólo la justicia es garantía de la paz y uña democracia fortalecida por la justicia pondrá trabas efectivas a toda nueva aventura golpista. La modalidad orgánica del Frente Amplio, su vasta red de organizaoiones de base, la integración plura'Iista de sus organismos, le han facilitado la tarea de llegar a importantes corrientes de opinión nacional y a difundir el namado unitario. En este momento tan decisivo en la vida del país, tenemos que nombrar a un hombre que ha sido y es e1 gran propulsor de la unidad del pueblo uruguayo para lograr la organización democrática del Estado y la concertación de las fuerzas políticas y sociales para con e! esfuerzo de todas ellas rescatar al país, A este hombre, al Presidente del Frente AmpHo, Genera'l Líber Seregni ... (Aplausos en la Sala y en la Barra. Los señores Legisladores se ponen de pie.) ... que sufrió nueve años de cárcel por el delito de amar a su pueblo, a la democracia y a la libertad. A este compañero que no pudo ser candidato a la Presidencia de la República, porque se le mantuvo proscripto, 'los legisladores del Frente Amplio, en este momento, le rendimos homenaje. Es un homenaje al militar patriota, al estadista, al líder político abnegado, lúcido yfraterno. (Aplausos en la Sala y en 'la Barra.) Por su parte, el movimiento obrero, junto con instituciones sodales de diversa índole y con la organización de una juventud que bien podría calificarse de maravillosa una juventud cuya pujanza y madurez son prendas del reverdecer nacional, afirmando precisamente el movimiento obrero sindicalmente constituidollegaron a la coincidencia de los intereses de su clase con la vigencia real de las libertades democráticas hecho fundamental en la vida del país y en la tarea que nos aguarday convinieron, luego, impulsar una acción común que despué·s fue concertada y que tuvo carácter programático. Quedan definidas así, dos directrices fundamentales: una, la adopción de actitudes y condudas políticas que. sobre la base indispensable de la moviJiizaclón popular apresurase el fin del régimen dictatorial; la otra, la preparación de bases programáticas comunes a través de múltiples grupos de trabajo debidamente organizados para ·integrarse a la dura tarea de la recuperación económica, social, educacional, sanitaria de la Nación; un aporte que no debe interrumpirse, en nuestro concepto y en nuestra opinión; un aporte al cumplimiento de la función pública que ·emprenden ahora los órganos constitucionales, como éste que integramos, enriquecida por el afán constructivo de los sectores más representativos que ofrecen un ejemplo del que no será fácil encontrar similares en todo el ámbito internacional, y este es otro motivo de orgullo para 'los uruguayos. Ha habido diferencias y desacuerdos, y para algunos proyectos elaborados o en vías de elaborarse en el ámbito de la concertaciónno se ha logrado consenso concertante. Es el caso del proyecto de amistía general e irrestricta. (Aplausos en la Sala y en la Barra.) Y al no lograrse el acuerdo necesario, ha sido presentado esta tarde por nosotros, en ambas Cámaras del Poder Legislativo. (Aplausos en la Sala y en la Barra.) Ante una situación similar, hemos presentado también hoy, el proyecto !e restitución de los destJ!tuidos. (Aplausos en la Sala y en la Barra.) Integrarán esos Jlroyectos la vanguardia de nuestros planteamientos, sin desmedro quiero subrayarlo especia]mentede nuestra disposición a concertar en todos los nivel·es de los problemas del país, en torno a planes de auténtico desarrollo demoorático. Para esto, las concertaciones programáticas ya alcanzadas constituirán para todos nosotros, para todos \"los partidos, bases muy útiles para el futuro trabajo parlamentario. En cuanto a la otra linea directriz, la de la concordancia de los partidos en el campo de la táctica y la estrategia política, el Frente Amplio como es notorio, asumió la responsabflidad de participar en las negociaciones con las Fuerzas Armadas, para la vuelta a la normalidad institucional. No vamos a proponer en esta circunstancia tan especial y única, un debate en torno a polémicas, planteos po'líticos que se desarrollaron en distintas tribunas, especialmente durante la campaña electoral. Pero al mencionar un capítulo tan importante de un período gravemente crítico. es nuestro claro deber creemos que todos entenderán que era un deber que no podíamos eludírhacer constar que el Frente Amplio tiene hoy el mismo criterio político que lo llevó a asumir entonces la posición que asumió, convencido como hoy, de que así lo reclamaba la crisis integral y el destino de la democracia. Al decir esto no estamos negando quiero destacarlo especialmenteque el mismo móvil de servir el intei\"és nacional, pesó en 'la definición política de quienes asumieron la posición contraria. Señor Presidente, señores Legisladores: comenzamos hoy, como representantes de un pueblo que ama la libertad y la justicia una tarea ardua, con obstáculos heredados, cuya magnitud no conocemos cabalmente todavía. Pero esta incierta realidad ·compromete 'los estímulos más profundos de nuestra voluntad militante, de nuestra obligación con el presente y el futuro del Uruguay. Conociendo el elevado espíl\"itu, 'la definición ideológica y la vocación de lucha del pueblo frenteamplista que representamos, decimos que si los que vamos a actuar aquí en su nombre somos fieles Y lo seremosa sus principios de redención socia\"!. a su lealtad a la democracia, a una democracia sin falsificaciones, a su esperanza en el futuro nacional, entonces podremos decir ante cada etapa de nuestro trabajo: estamos cumpliendo nuestro deber con el país. He dicho. (Aplausos en la Sala y en la Barra.) SEN\"OR PRESIDENTE. Ha 11egado un proyecto de declaración del Cuerpo, que la Mesa pondrá a consideración a posterior! de hacer uso de la palabra el señor legislador tRossi Passina, que tiene la pa\"labra."
```

``` r
text <- speech::speech_build(file = url, 
                             rm.error.leg = "PIRIESIDENTE", 
                             add.error.sir = 'SEN\"OR')


text$speech[2]
#> [1] "SEÑOR CORDOSO. Señor Presidente de la Asamblea General; señores Diputados; señores Senadores: debo formular una declaración en nombre de la bancada de legisladores del Frente Ampl!io y al comenzarla saludo la significación política y moral de este acto, que es como el sello institucional del fin de la dictadura. Esta significación alcanza un relieve especial, cuando por inicativa de'l señor Presidente hemos rendido homenaje a dos mártires tan vinculados a la vida de esta Casa: Zelmar Michelini y Héctor Gutténez Ruiz. (Aplausos en la Sala y en la Barra). ¿En qué tramo, en qué aspecto de un período funesto de nuestra historia podría incursionar nuestro pensamiento en esta singular circunstancia? ¿En sus causas? ¡,En una discriminación de culpas? ¿En 'los multiplicados sufrimientos !individuales y colectivos? ¿En el quebrantamiento de los valores éticos? ¿En los derechos humanos? Hay un punto de partida en el que los uruguayos deberíamos ·convenir: en una significativa unanimidad que tendría mucho de fecunda, de fermenta!, con valor de jUJicio histórico sobre un pasado reciente y de concepción política aplicable al porvenir. Ello podría expresarse con una frase de dramática sencillez, casi de dramática simplicidad. La nación ha sufrido un tremendo infortunio: 'la dictadura; con ella, una gran desgracia se abatió sobre el Uruguay. Si sus hijos no mantuVIiéramos viva esta convicción de nuestras conciencias, co·rreríamos el riesgo de no ver claro el camino que ha de conducirnos a restañar las heridas y a prevenir la recidiva del mal. Al pensar en los factores determinantes del drama, cuidémonos de esquematismos fáciles. Pero afirmemos por lo menos lo afirmamos nosotros:que no !o comprenderíamos si no admitiésemos, en pr1mer térmmo, que la política económico social que cayó como un azote sobre las famflias uruguayas, especialmente sobre las clases más desposeídas no hubiera podido ser aplica:aa, no hubiera podido ser sostenida si no hubiera sido a sangre y fuego por el poder político en manos de las Fuerzas Armadas. (Aplausos en la Sala y en la Barra). A los sufrimientos de'l llamado costo social, a la brutal caída del salario, a la desocupación creciente, a la sangría de la emigración, a la deuda externa colonialista se sumaron 'los otros sufrimientos, los otros dolores: '¡a destrucción de derechos. de libertades y de garantías; los miles de presos; la tortura como rutina; los muertos; los desaparecidos; las destituciones; los exilios; la burla de una supuesta ley sindical; la proscripción de rpartidos y de hombres, y con ellos no hace falta seguir la enumeración. Séame permitido como colofón de lo que acabo de decir, agregar una frase aunque sea repetición de un concepto ya vertidoque no puedo omitir: ¡No olvlide mos nunca, no olvidemos nunca 'los uruguayos que la política e·conómica antinac1onal y antisocial, fue posible porque fue destruída la democrac'ia! (Aplausos en 'la Sala y en la Barra). La funesta asociación fue cada día más evidente para la conciencia nacional, aleccionada por la resistencia del movimiento obrero y popular a las normas fondomonetaristas y por la violenta represión de que esa resistencia era objeto. Y cuando los regímenes de la seudolega:lidad predictatorial desembocaron en el golpe, la huelga general fue el enfrentamiento con un aparato cív1ico militar llamada así por sus creadoresque unía en sí la acción liberticida y el ahondamiento de la injusticia social. Fue, si invocamos la clásica bandera de las reivindicaciones proletarias la defensa de la libertad y del pan. Fue, aquella huelga, general, la defensa de los instrumentos po'líticos que la clase obrera necesita para. sus conquistas, para ejercer el derecho a luchar por otras más trascendentes y liberadoras. La dictadura apeló a la doctrina de la seguridad nacional con el pretexto y en un intento de demostrar al pueblo los peligros de las penetraciones foráneas. Se produjo así otra alianza: la de la doctrina de la seguridad nacional que de nacional sólo tiene el nombrecon el neoliberalismo de las transnacionales para consagrar como dijo cierta vez un pubNcista chilenoel derecho de'l zorro libre en el gallinero libre. Fue aplicada a peligros inexistentes, viniendo ella sí la doctrina de la seguridad nacionaldesde fuera hacia adentro y no desde dentro hacia fuera para enfrentar la expoliación y la dependenC'ia que comprometían la soberanía ~~ hasta nuestra propia naciona'lidad. Bajo e'l régimen autoritario se abrieron abismos de diferencias y separaciones en la sociedad nacional. El Uruguay vivió como hace un rato decía el señor Senador Pereyrauna larga noche; una larga noche poblada de incertidumbres, de carencias esenciales, extraña a sus mejores tradiciones y al carácter de un pueb'lo solidario y generoso, que no cree que en su país los caminos de la violencia y de la opresión sean los que conduzcan al cambio social que eleve las condiciones materiales y culturades de la vida de los hombres. Comenzó entonces el andar y andar de nuestro pueblo por un camino que, a lo largo de los años, habría de conducir a estos días augurales. Antes de proseguir con la evocación de la lucha liberadora, queremos expresar desde aquí nuestro vivo agradecimiento a la actitud de numerosos pueblos amigos y hermanos que, a través de organizaciones de diversa índole, siempre hicieron presente su so'lidaridad con el afán democrático del pueblo uruguayo. Eso no lo olvidaremos jamás. (Aplausos en la Sala y en la Barra) Para el Frente Amplio desde el primer día el camino trazado fue el no interrumpido esfuerzo para derrotar a la dictadura. Y bien, así como hemos procurado sintetizar casi al extremo, a1gunos conceptos básicos acerca de los lineamientos y de la conducta de la dictadura, también sintéticamente señalaremos la conducta de nuestra gente, si es que puede caber en una frase la riql.leza, la inmensa riqueza cívica y moral de un pueblo que se trazó un camino hacia la libertad y supo recorrerlo con unidad solidaria, pacíficamente, pero con ur. gran esfuerzo cívico, valerosamente decidido a luchar y a triunfar como ha triunfado ahora. Por lo tanto, se han enfrentado largamente dos conductas. Por un 'lado, el autoritarismo cumplía su doble objetivo: introducir en un molde rígido las más variadas manifestaciones de la vida nacional, aplicando la misma dureza en la represión de las reivindicaciones democráticas. Y, por otro lado, la conducta de'l pueblo uruguayo. Nuestro pueblo veía cada día más claramente, a medida que se sucedían los avances y los retrocesos, que era necesario unir todo lo unible para conquistar el derecho a V'ivir y a trabajar, a luchar por so'luciones de justicia y libertad. Y lo que podía Sei' posible, señores, fue realmente posi!ble, para honor de los uruguayos y de la República y se expresó en multitudinarios acontecimientos, cada día más orgánicos, en entendimientos po'líticos y sociales y en una firrríe lealtad a la concepción de la unidad nacional. Consecuentemente, el Frente Amplio ha mantenido ese designio, designio de unirse para triunfar y construir la dem'ocracia, y considera ·que para que nunca más vuelva a caer sobre el pueblo uruguayo el azote de una tiranía es imprescindible consolidar y profundizar la democracia. !Sólo la justicia es garantía de la paz y uña democracia fortalecida por la justicia pondrá trabas efectivas a toda nueva aventura golpista. La modalidad orgánica del Frente Amplio, su vasta red de organizaoiones de base, la integración plura'Iista de sus organismos, le han facilitado la tarea de llegar a importantes corrientes de opinión nacional y a difundir el namado unitario. En este momento tan decisivo en la vida del país, tenemos que nombrar a un hombre que ha sido y es e1 gran propulsor de la unidad del pueblo uruguayo para lograr la organización democrática del Estado y la concertación de las fuerzas políticas y sociales para con e! esfuerzo de todas ellas rescatar al país, A este hombre, al Presidente del Frente AmpHo, Genera'l Líber Seregni ... (Aplausos en la Sala y en la Barra. Los señores Legisladores se ponen de pie.) ... que sufrió nueve años de cárcel por el delito de amar a su pueblo, a la democracia y a la libertad. A este compañero que no pudo ser candidato a la Presidencia de la República, porque se le mantuvo proscripto, 'los legisladores del Frente Amplio, en este momento, le rendimos homenaje. Es un homenaje al militar patriota, al estadista, al líder político abnegado, lúcido yfraterno. (Aplausos en la Sala y en 'la Barra.) Por su parte, el movimiento obrero, junto con instituciones sodales de diversa índole y con la organización de una juventud que bien podría calificarse de maravillosa una juventud cuya pujanza y madurez son prendas del reverdecer nacional, afirmando precisamente el movimiento obrero sindicalmente constituidollegaron a la coincidencia de los intereses de su clase con la vigencia real de las libertades democráticas hecho fundamental en la vida del país y en la tarea que nos aguarday convinieron, luego, impulsar una acción común que despué·s fue concertada y que tuvo carácter programático. Quedan definidas así, dos directrices fundamentales: una, la adopción de actitudes y condudas políticas que. sobre la base indispensable de la moviJiizaclón popular apresurase el fin del régimen dictatorial; la otra, la preparación de bases programáticas comunes a través de múltiples grupos de trabajo debidamente organizados para ·integrarse a la dura tarea de la recuperación económica, social, educacional, sanitaria de la Nación; un aporte que no debe interrumpirse, en nuestro concepto y en nuestra opinión; un aporte al cumplimiento de la función pública que ·emprenden ahora los órganos constitucionales, como éste que integramos, enriquecida por el afán constructivo de los sectores más representativos que ofrecen un ejemplo del que no será fácil encontrar similares en todo el ámbito internacional, y este es otro motivo de orgullo para 'los uruguayos. Ha habido diferencias y desacuerdos, y para algunos proyectos elaborados o en vías de elaborarse en el ámbito de la concertaciónno se ha logrado consenso concertante. Es el caso del proyecto de amistía general e irrestricta. (Aplausos en la Sala y en la Barra.) Y al no lograrse el acuerdo necesario, ha sido presentado esta tarde por nosotros, en ambas Cámaras del Poder Legislativo. (Aplausos en la Sala y en la Barra.) Ante una situación similar, hemos presentado también hoy, el proyecto !e restitución de los destJ!tuidos. (Aplausos en la Sala y en la Barra.) Integrarán esos Jlroyectos la vanguardia de nuestros planteamientos, sin desmedro quiero subrayarlo especia]mentede nuestra disposición a concertar en todos los nivel·es de los problemas del país, en torno a planes de auténtico desarrollo demoorático. Para esto, las concertaciones programáticas ya alcanzadas constituirán para todos nosotros, para todos \"los partidos, bases muy útiles para el futuro trabajo parlamentario. En cuanto a la otra linea directriz, la de la concordancia de los partidos en el campo de la táctica y la estrategia política, el Frente Amplio como es notorio, asumió la responsabflidad de participar en las negociaciones con las Fuerzas Armadas, para la vuelta a la normalidad institucional. No vamos a proponer en esta circunstancia tan especial y única, un debate en torno a polémicas, planteos po'líticos que se desarrollaron en distintas tribunas, especialmente durante la campaña electoral. Pero al mencionar un capítulo tan importante de un período gravemente crítico. es nuestro claro deber creemos que todos entenderán que era un deber que no podíamos eludírhacer constar que el Frente Amplio tiene hoy el mismo criterio político que lo llevó a asumir entonces la posición que asumió, convencido como hoy, de que así lo reclamaba la crisis integral y el destino de la democracia. Al decir esto no estamos negando quiero destacarlo especialmenteque el mismo móvil de servir el intei\"és nacional, pesó en 'la definición política de quienes asumieron la posición contraria. Señor Presidente, señores Legisladores: comenzamos hoy, como representantes de un pueblo que ama la libertad y la justicia una tarea ardua, con obstáculos heredados, cuya magnitud no conocemos cabalmente todavía. Pero esta incierta realidad ·compromete 'los estímulos más profundos de nuestra voluntad militante, de nuestra obligación con el presente y el futuro del Uruguay. Conociendo el elevado espíl\"itu, 'la definición ideológica y la vocación de lucha del pueblo frenteamplista que representamos, decimos que si los que vamos a actuar aquí en su nombre somos fieles Y lo seremosa sus principios de redención socia\"!. a su lealtad a la democracia, a una democracia sin falsificaciones, a su esperanza en el futuro nacional, entonces podremos decir ante cada etapa de nuestro trabajo: estamos cumpliendo nuestro deber con el país. He dicho. (Aplausos en la Sala y en la Barra.)"
```
