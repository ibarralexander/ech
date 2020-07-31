
<!-- README.md is generated from README.Rmd. Please edit that file -->
ech <img align="right" src="man/figures/ech_logo.png" alt="logo" width="160">
=============================================================================

<!-- badges: start -->
<!-- [![CRAN/METACRAN Version](https://www.r-pkg.org/badges/version/ech)](https://CRAN.R-project.org/package=ech) -->
<!-- [![CRAN/METACRAN Total downloads](http://cranlogs.r-pkg.org/badges/grand-total/ech?color=blue)](https://CRAN.R-project.org/package=ech)  -->
<!-- [![CRAN/METACRAN downloads per month](http://cranlogs.r-pkg.org/badges/ech?color=orange)](https://CRAN.R-project.org/package=ech) -->
[![Travis build status](https://travis-ci.org/calcita/ech.svg?branch=master)](https://travis-ci.org/calcita/ech) [![AppVeyor build status](https://ci.appveyor.com/api/projects/status/github/calcita/ech?branch=master&svg=true)](https://ci.appveyor.com/project/calcita/ech) [![Codecov test coverage](https://codecov.io/gh/calcita/ech/branch/master/graph/badge.svg)](https://codecov.io/gh/calcita/ech?branch=master) <!-- badges: end -->

Caja de herramientas para el procesamiento de la [Encuesta Continua de Hogares de Uruguay](http://www.ine.gub.uy/encuesta-continua-de-hogares1) realizada por en Instituto Nacional de Estadística (INE). Pretendiendo contribuir a la comunidad de usuaries de R en Uruguay, facilitando el uso de una de las encuestas socioeconómicas más importantes del país. En este sentido, el paquete implementa una serie de funciones que permiten el cálculo de los principales indicadores socioeconómicos que permite la encuesta.

Instalación
===========

<!-- Para la versión estable -->
Para la versión en desarrollo

``` r

# install.packages('devtools') 
# si no tiene instalado devtools

devtools::install_github("calcita/ech")
library(ech)
```

Modo de uso
===========

El paquete ech permite descargar los microdatos oficiales desde la web del INE o bien utilizar microdatos ya procesados. En el primer caso los datos se obtiene a través de la función `get_microdata()`. En el segundo caso, si la persona ha modificado los nombres originales de las variables deberá indicarlo en cada función que utilice.

Ejemplo de descarga de microdatos:

``` r

# Cargo la libreria
library(ech)

# Cargo la base
ech16 <- get_microdata(year = "2016", # Año/s a descargar (2011-2019)
              folder = tempdir(), # Carpeta para descarga   
              toR = TRUE) # Si lo cargamos al entorno de R
```

Como un segundo paso, recomendamos utilizar la función `organize_names()` que conserva las variables que se utilizan por las funciones de `ech`con referencia a 2017. Esto fuera de corregir nombres de variables que en diferentes años tienen diferentes nombres, ayuda a que mediante el aporte de los usuarios al proyecto la grilla de dicionarios `ech::dic` vaya completandose y mejoran esta función.

``` r
# Organizamos nombres de variables

ech16 <- organize_names(ech16, 2016)
```

Pasando ahora a lo importante, esta librería aporta variadas funciones para el cálculo de nuevas variables como por ejemplo `employment`, que calcula PEA, PET, PO y PD.

``` r
# Calculamos variables de empleo

ech16 <- employment(data = ech16)
```

O como `income_constant_prices()` que no solo agrega las variables calculas sino que muestra a su vez la vinculación de la ech con otra fuente de información adicional como son las tablas de IPC.

``` r
# Organizamos nombres de variables

ech16 <- income_constant_prices(data = ech16, # Data.frame de una ech
                                base_month = 6, # Mes base 
                                base_year = 2016, # Año base
                                ipc = "G") # Puede ser General ("G") o Regional ("R")
```

Por otro lado, esta librería tiene cuatro funciones que nos permiten hacer estimaciones:

-   `get_estimation_mean()`:
-   `get_estimation_total()`:
-   `get_estimation_ratio()`:
-   `get_estimation_gini()`:

Un ejemplo aplicando una de estas Ejemplo de estimación:

``` r
# Genero una estimaciónfunciones sería:

pobre_x_dpto <- get_estimation_mean(data = ech::toy_ech_2018, # Indico el data.frame
                           variable = "pobre06", # La variable a estimar
                           by.x = "dpto", # La variable de agrupación
                           by.y = NULL, # Se permite otra variable de agrupación
                           domain = NULL, # Se podría indicar un dominio
                           level = "h", # Defino que lo haga a nivel de hogar
                           name = "Pobreza") 
```

El uso de este paquete se potencia al usarlo en conjunto con [geouy](https://github.com/RichDeto/geouy) posibilitando mapear utilizando diferentes capas geográficas de Uruguay. Por ejemplo para agregarle geometrías a esta estimación para poder hacer un mapa con estos datos.

``` r
# Agrego geometrías

pobre_x_dpto_geo <- add_geom(data = pobre_x_dpto, # Los datos en una unidad geográfica de entre las opciones
                            unit = "Departamentos", # Unidad de agregación de los datos
                            variable = "dpto") # Variable correspondiente a los códigos a la unidad
```

Y con esto, podemos hacer un mapa con `ggplot2` muy facilmente de estas estimaciones.

``` r
# Hago un mapa

geouy::plot_geouy(pobre_x_dpto_geo, "Pobreza")
```

Aportes de la comunidad
-----------------------

Este paquete se propone incorporar cualquier función de propósitos generales que utilice como base le Encuesta Continua de Hogares (ECH) de Uruguay. Todos los aportes en este sentido son bienvenidos.

Si trabajas con la ECH de Uruguay y querés agregar tu función o tus datos, te recomendamos que leas los siguientes consejos de como colaborar [aquí](https://github.com/calcita/ech/issues/2).
