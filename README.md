* * * * *

🔥 Análisis de impacto del fuego con Sentinel-2 y geemap
--------------------------------------------------------

Este flujo de trabajo aprovecha el potencial de Sentinel-2 y el índice NBR (Normalized Burn Ratio) para evaluar la severidad de incendios forestales, desde la detección del área afectada hasta la exportación del perímetro del incendio. Todo el proceso se apoya en `geemap`, `folium` y la potencia de Google Earth Engine.

* * * * *

### 🛰️ Comparativa Pre y Post Incendio con NBR

Se seleccionan dos imágenes de Sentinel-2, una previa y otra posterior. Se calcula el **NBR** para ambas fechas, utilizando la fórmula clásica:

`NBR = (B8 - B12) / (B8 + B12)`

Luego, se genera el **dNBR** restando el NBR post-fuego al NBR pre-fuego, lo que permite cuantificar la magnitud del cambio:

`dNBR = NBR_pre - NBR_post`

* * * * *

### 📊 Clasificación de Severidad del Fuego

La dNBR resultante se clasifica en cinco clases de severidad, siguiendo los umbrales propuestos por **Key & Benson (2006)**:

-   **< 0.1**: Cambio insignificante o recuperación

-   **0.1 -- 0.27**: Quemado de baja severidad

-   **0.27 -- 0.44**: Moderado-bajo

-   **0.44 -- 0.66**: Moderado-alto

-   **> 0.66**: Alta severidad

Estos valores se aplican directamente sobre el raster dNBR, generando una capa categórica.

* * * * *

### 🗺️ Visualización Interactiva con geemap & folium

Se implementan dos tipos de visualización:

#### 🔁 Split Map Interactivo

Utilizando `geemap.split_map`, se presenta un mapa interactivo lado a lado con las imágenes pre y post-incendio, lo que facilita la inspección visual del cambio.

![alt text](https://github.com/cmlg96/practice/blob/main/1.JPG)

#### 🖍️ Mapas de Severidad

Con `folium`, se visualiza la capa de severidad con una **leyenda personalizada**, donde cada categoría tiene su color asociado, haciendo más intuitiva la interpretación.

![alt text](https://github.com/cmlg96/practice/blob/main/3.JPG)

* * * * *

### 🧹 Identificación de Elementos Relevantes

Tres elementos clave para refinar el análisis:

1.  **Polígonos más grandes afectados**: tras vectorizar el raster dNBR clasificado, se calculan áreas y se filtran los 10 polígonos más extensos (en hectáreas).

2.  **Polígonos aislados (ruido)**: se aíslan píxeles sin vecinos contiguos mediante filtros morfológicos o análisis de conectividad para detectar posibles falsos positivos.

3.  **Selección manual por área**: el usuario puede elegir un polígono específico según el área afectada estimada (útil para análisis de caso puntual).

![alt text](https://github.com/cmlg96/practice/blob/main/4.JPG)

* * * * *

### 📏 Vectorización y Cálculo de Áreas

La capa dNBR clasificada se convierte a vectores (shapefiles temporales en memoria). Luego:

-   Se calcula el área de cada polígono con `ee.Geometry(area).area().divide(10000)` para expresarlo en hectáreas.

-   Se ordenan los polígonos por área descendente.

-   Se visualizan en el mapa y se preparan para exportación.

<img src="https://github.com/cmlg96/practice/blob/main/5.JPG" width="400"/>

![alt text](https://github.com/cmlg96/practice/blob/main/6.JPG)
* * * * *

### 📤 Exportación y Suavizado

El polígono o grupo de polígonos seleccionados pueden suavizarse con un `buffer` inverso o `simplify`, para mejorar su geometría antes de exportarlos.

Finalmente, se usa:

`geemap.ee_export_vector(featureCollection, filename='nombre_archivo.geojson')`

Esto permite al usuario descargar el archivo `.geojson` directamente a su entorno local para posteriores análisis en QGIS, ArcGIS o cualquier herramienta SIG.

<img src="https://github.com/cmlg96/practice/blob/main/7.JPG" width="600"/>
