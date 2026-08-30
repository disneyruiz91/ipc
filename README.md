# Flujo de trabajo reproducible: IPC Colombia (DANE)

Trabajo aplicado individual — Preparación digital 

## Objetivo

Construir un flujo de trabajo reproducible de extremo a extremo (adquisición → limpieza →
análisis exploratorio → visualización) sobre el **Índice de Precios al Consumidor (IPC)**
publicado por el DANE, y plantear una idea de intervención a partir de los hallazgos.

## Fuente de datos

- **Entidad:** DANE (Departamento Administrativo Nacional de Estadística)
- **Serie:** Índice de Precios al Consumidor (IPC) — boletines técnicos mensuales (archivo "Anexo")
- **Periodo cubierto:** enero a julio de 2026 (7 archivos, uno por mes)
- **Página oficial:** https://www.dane.gov.co/index.php/estadisticas-por-tema/precios-y-costos/indice-de-precios-al-consumidor-ipc/ipc-historico
- **Formato:** Excel (.xlsx), descarga directa, sin necesidad de API ni credenciales
- **Nota:** cada archivo anexo trae, entre otras hojas, la variación del IPC total
  (hoja "1") y el detalle por división de bienes y servicios (hoja "2")
  correspondientes a ese mes. El notebook combina los 7 archivos para construir
  la serie mensual enero-julio 2026.

## Estructura de carpetas

```
ipc/
├── ipc.Rproj                      <- proyecto de RStudio (ábrelo con doble clic)
├── README.md                      <- este archivo
├── datos/
│   ├── crudos/                    <- los 7 archivos .xlsx descargados del DANE, SIN modificar
│   │                                  (anex-IPC-ene2026.xlsx ... anex-IPC-jul2026.xlsx)
│   └── procesados/                <- datos limpios y combinados (salida del notebook):
│                                      serie_total_ipc.csv, divisiones_ipc.csv
├── notebook/
│   └── analisis_ipc.Rmd           <- flujo completo: carga, limpieza, EDA, visualización
├── documentacion/
│   └── informe_tecnico.Rmd        <- informe técnico (máx. 6 páginas), con las 6 secciones
└── salidas/
    └── (gráficos .png generados por el notebook: 01_tendencia_ipc.png,
         02_divisiones_julio.png, 03_promedio_divisiones.png)
```

## Qué contiene cada carpeta

- **`datos/crudos/`**: los archivos originales del DANE, exactamente como se descargaron,
  sin ninguna edición manual. Son la única fuente de verdad de los datos crudos.
- **`datos/procesados/`**: los datos ya limpios, combinados en dos tablas (`serie_total_ipc.csv`
  con la variación mensual del IPC total, y `divisiones_ipc.csv` con el detalle por división
  de gasto). Se generan automáticamente al correr el notebook — no se editan a mano.
- **`notebook/`**: el código en R que hace todo el trabajo: lee los archivos crudos, los limpia,
  calcula estadísticas descriptivas y genera las visualizaciones.
- **`documentacion/`**: el informe técnico final en formato Rmd (y su versión en PDF una vez
  generado), con la interpretación de los resultados y la idea de intervención.
- **`salidas/`**: las imágenes de los gráficos generados por el notebook, usadas tanto en el
  informe técnico como en el dashboard/visualización final.

## Pasos exactos para reproducir el flujo completo

1. **Descargar los datos crudos**
   - Ir a la página oficial del DANE (enlace arriba) → boletines técnicos mensuales del IPC
     → descargar el archivo "Anexo" (.xlsx) de cada mes que se quiera incluir.
   - Guardar los 7 archivos, sin modificarlos, en `datos/crudos/` con sus nombres originales
     (ej. `anex-IPC-ene2026.xlsx`, `anex-IPC-feb2026.xlsx`, ..., `anex-IPC-jul2026.xlsx`).

2. **Abrir el proyecto en RStudio**
   - **Importante:** abrir el proyecto haciendo doble clic en `ipc.Rproj` (no abrir el .Rmd
     suelto desde el explorador de archivos). Esto asegura que las rutas a los datos
     funcionen igual sea que se ejecuten los chunks uno por uno o que se use "Knit".
   - Instalar los paquetes necesarios (solo la primera vez; si R avisa que algún paquete
     está "en uso", reiniciar la sesión con Session → Restart R y reintentar):
     ```r
     install.packages(c("here", "readxl", "dplyr", "tidyr", "purrr", "stringr", "ggplot2", "scales"))
     ```
   - Para generar el informe técnico en PDF también se necesita una distribución de LaTeX
     (ya instalada en este equipo vía `tinytex::install_tinytex()`).

3. **Ejecutar el notebook de análisis**
   - Abrir `notebook/analisis_ipc.Rmd`.
   - Ejecutar todos los chunks en orden (Run → Run All), o hacer clic en "Knit".
   - Este script:
     - Carga los 7 archivos crudos desde `datos/crudos/` y los combina en una sola serie mensual.
     - Limpia y estructura los datos (extrae el mes desde el nombre del archivo, filtra el año
       2026, corrige nombres de columnas y de divisiones).
     - Guarda el resultado limpio en `datos/procesados/serie_total_ipc.csv` y
       `datos/procesados/divisiones_ipc.csv`.
     - Calcula estadísticas descriptivas (resumen de la variación mensual y anual).
     - Genera las tres visualizaciones y las guarda como .png en `salidas/`.

4. **Generar el informe técnico**
   - Abrir `documentacion/informe_tecnico.Rmd`.
   - Revisar y completar cada sección con los resultados obtenidos en el paso 3.
   - Hacer clic en "Knit to PDF" para generar el PDF final (máx. 6 páginas, sin contar
     portada ni referencias).

5. **Dashboard / visualizaciones finales**
   - Los tres gráficos generados en `salidas/` (tendencia del IPC, variación por división en
     julio, y promedio por división del periodo) cumplen el mínimo de dos visualizaciones
     distintas que pide la guía, con títulos, ejes y unidades claros para una persona sin
     formación técnica.

## Historial de versiones

Este proyecto se entrega en dos momentos de avance (ver control de versiones / Git, o los
archivos fechados si no se usa Git):

- **Entrega 1:** adquisición + limpieza de datos (carpeta `datos/` completa, notebook
  ejecutado hasta la sección de análisis exploratorio).
- **Entrega 2:** análisis exploratorio + visualizaciones + idea de intervención + informe
  técnico completo.

## Alcance y limitaciones

Este ejercicio evalúa el proceso de construcción de un flujo de datos reproducible y el
planteamiento de una idea de intervención — **no** la estimación de un modelo econométrico,
ni el diseño o evaluación formal de la intervención propuesta (eso se profundiza en
Econometría Básica y en Evaluación de Impacto). El periodo analizado (enero-julio de 2026)
es corto, por lo que no permite observar estacionalidad ni tendencias de largo plazo.
