# Anthribidae of Guatemala distribution map in R
K. Samanta Orellana, 26 jun 2023

Distribution map of occurrence records of Anthribidae in Guatemala up to January 2002.

### Maps with ggplot2, sf and tidyverse
References/Referencias: 
- https://github.com/wtesto/SpeciesOccurrenceMapping
- https://r-spatial.org/r/2018/10/25/ggplot2-sf.html
- https://slcladal.github.io/maps.html

### Additional layers
- DivaGis: [https://www.diva-gis.org/data](https://www.diva-gis.org/Data) (Country level data: GTM_adm0.shp (country), GTM_adm1.shp (states), GTM_adm2.shp (counties))

### Packages
Install packages

```
install.packages("tidyverse")
install.packages("sf")
install.packages("ggplot2)
install.packages("raster")
install.packages("rnaturalearth")
install.packages("sf")
install.packages("elevatr")
install.packages("dplyr")
install.packages("magrittr")
install.packages("ggspatial")
install.packages("ggplot2")
install.packages("ggpubr")
```

Install "rnaturalearthhires" from github (outdated)

```
install.packages("githubinstall")
library(githubinstall)
githubinstall("rnaturalearthhires")
```

Load packages

```
library(tidyverse)
library(sf)
library(ggplot2)
library(raster) #for processing some spatial data
library(rnaturalearth) #for downloading shapefiles
library(sf) #for processing shapefiles
library(elevatr) #for downloading elevation data
library(dplyr) #to keep things tidy
library(magrittr) #to keep things very tidy
library(ggspatial) #for scale bars and arrows
library(ggplot2) #for tidy plotting
library(ggpubr) #for easy selection of symbols
```

Define map area

world <- ne_countries(scale = "medium", returnclass = "sf") #Mundo, para construir los mapas

```
library(raster) #for processing some spatial data
map <- ne_countries(scale = 10, returnclass = "sf")
```

### Example with Guatemala layers

-Download "state" layer from DivaGis [(https://www.diva-gis.org/data)](https://www.diva-gis.org/data).
-Save files in our directory (recommended to create a folder directly in C://)

Open folder
```
setwd("C://Coding")
```

To add country, state, county, etc. layers, just add the file with the name you prefer:

```
gua_pais <- st_read("GTM_adm0.shp")
gua_dep <- st_read("GTM_adm1.shp")
gua_mun <- st_read("GTM_adm2.shp")
gua_rios <- st_read("GTM_water_lines_dcw.shp")
gua_lagos <- st_read("GTM_water_areas_dcw.shp")
```

## Map with number of species by state

Add the layer that you want to use (states, counties, etc.)

```
setwd("C://Coding") 
gua_dep <- st_read("GTM_adm1.shp")
```

Add .csv file with 2 columns: state or county (for state use **NAME_1**, for county use **NAME_2**) and number of species per state or county
Check that the names for states or counties are the same than in the DivaGis layer

```
species <- read_csv("species.csv")
species
names(species)
```

Merge the file with the number of species with the map layer

```
gua_dep_ant <- gua_dep %>%
	left_join(species)
```	
  
	###IF the columns in both files have different names, you can replace one of them with the following code:

```
  left_join(especies,
            by = c("NAME_1" = "state"))
```

To check your new file

```
gua_dep_ant
names(gua_dep_ant)
```
---

### Generate map

```
ggplot(data = world) +
	geom_sf(data=gua_dep_ant, aes(fill=especies)) +
	scale_fill_gradient ("Total of species", high = "#A4C61A", low = "white") +
	 	labs( x = "Longitud", y = "Latitud") +
  coord_sf(xlim = c(-92.5, -88.1), ylim = c(13.8, 18.1), expand = T) +
  annotation_scale(location = "bl", width_hint = 0.3) +
  annotation_north_arrow(location = "bl", which_north = "true", 
                         pad_x = unit(0.75, "in"), pad_y = unit(0.3, "in"),
                         style = north_arrow_fancy_orienteering) +
  theme_bw()
```

Save map

```
ggsave("AnthribidaeGuatemalaDepartamentoEspecies.jpg")
```
---

### Map with occurrence data

### Occurrence data
Add .csv file with data (3 columns with: species, lat, long)

Open folder

```
setwd("C://Coding")
```

Add file with occurrence data

```
anthrigt <- read.csv("anthrgtreg.csv")
```

Check column names

```
names(anthrigt)
head(anthrigt)
```

### Generate map

```
ggplot(data = world) +
	geom_sf(data=gua_dep_ant, aes(fill=especies), linetype="solid", size=0.3) +
	scale_fill_gradient ("Species total", high = "#f5e267", low = "white") +
geom_point(data = anthrigt, aes(x=decimalLongitude, y = decimalLatitude, color=Record, pch=Record), cex = 2) + #esta es la línea donde van los puntos, nombrar adecuadamente las columnas de lon y lat
	 scale_color_manual(values=c("purple", "red", "black"))+
	 	labs( x = "Longitude", y = "Latitude") +
  coord_sf(xlim = c(-92.5, -88.1), ylim = c(13.8, 18.1), expand = T) +
  annotation_scale(location = "bl", width_hint = 0.3) +
  annotation_north_arrow(location = "bl", which_north = "true", 
                         pad_x = unit(0.75, "in"), pad_y = unit(0.3, "in"),
                         style = north_arrow_fancy_orienteering) +
  theme_bw()

```

Save

```
ggsave("AnthribidaeGuatemalaPointsDeptoGreen.jpg")

```

Colors: yellow #f5e267 green #9cf536
