# Flujo de trabajo reproducible: IPC Colombia (DANE)

Trabajo aplicado individual — Preparación digital

## De qué se trata esto

Quería armar, de principio a fin, un flujo de trabajo con el que cualquiera
pueda repetir lo que hice descargar los datos, limpiarlos, explorarlos y
sacarles unos gráficos. Trabajé con el Índice de Precios al Consumidor
(IPC) que publica el DANE cada mes, y con lo que fui encontrando, se me
ocurrió una idea de intervención que también dejo planteada aquí.

## De dónde saqué los datos

Usé los boletines técnicos que publica el DANE cada mes con el IPC  los
llaman anexos y se pueden descargar directamente de su página, sin
necesidad de ninguna clave ni permiso especial

https://www.dane.gov.co/index.php/estadisticas-por-tema/precios-y-costos/indice-de-precios-al-consumidor-ipc/ipc-historico

Tomé los siete meses que iban de enero a julio de 2026. Cada anexo viene en
Excel y trae, entre otras cosas, la variación del IPC total (en una hoja) y
el detalle por cada tipo de gasto - vivienda, alimentos, transporte, etc.
en otra. Junté los siete archivos para armar una sola serie mensual.

## Cómo dejé organizada la carpeta

```
ipc/
├── ipc.Rproj                      <- el proyecto de RStudio (ábrelo con doble clic)
├── README.md                      <- este archivo
├── datos/
│   ├── crudos/                    <- los 7 Excel tal como los bajé del DANE, sin tocar
│   │                                  (anex-IPC-ene2026.xlsx ... anex-IPC-jul2026.xlsx)
│   └── procesados/                <- ya limpios: serie_total_ipc.csv, divisiones_ipc.csv
├── notebook/
│   └── analisis_ipc.Rmd           <- todo el trabajo: cargar, limpiar, explorar, graficar
├── documentacion/
│   └── informe_tecnico.Rmd        <- el informe escrito
├── salidas/
│   └── (los gráficos en .png que salen del notebook)
└── entrega/
    └── entrega_informe_ipc.pdf    <- el informe ya terminado, listo para entregar
```

## Qué hay en cada carpeta

- **`datos/crudos/`**: los Excel originales, exactamente como los descargué,
  sin editarlos a mano.
- **`datos/procesados/`**: los datos ya limpios y juntos, listos para usar.
  Estos los genera solo el notebook, no los toco directamente.
- **`notebook/`**: el código en R, donde está todo el trabajo  leer los
  archivos, limpiarlos, sacar estadísticas y hacer los gráficos.
- **`documentacion/`**: el informe escrito, con lo que encontré y la idea
  de intervención.
- **`salidas/`**: las imágenes de los gráficos, las mismas que uso en el
  informe y en la sustentación.
- **`entrega/`**: la versión final del informe, ya en PDF.

## Cómo repetir lo que hice, paso a paso

1. **Bajar los datos**
   Entrar a la página del DANE (el enlace de arriba) y descargar el archivo
   "Anexo" de cada mes que se quiera incluir. Guardarlos, sin tocarlos, en
   `datos/crudos/`, con sus nombres originales.

2. **Abrir el proyecto en RStudio**
   Ojo con esto: hay que abrir `ipc.Rproj` con doble clic, no el `.Rmd`
   suelto desde el explorador de archivos — así las rutas a los datos
   funcionan bien. La primera vez hay que instalar los paquetes que uso:
   ```r
   install.packages(c("here", "readxl", "dplyr", "tidyr", "purrr", "stringr", "ggplot2", "scales"))
   ```
   Para sacar el informe en PDF también hace falta tener LaTeX instalado
   (yo lo instalé con `tinytex::install_tinytex()`).

3. **Correr el notebook**
   Abrir `notebook/analisis_ipc.Rmd` y correr todos los chunks en orden, o
   darle "Knit". Esto lee los 7 archivos, los limpia, guarda las tablas
   limpias en CSV, saca unas estadísticas rápidas y genera los tres
   gráficos en `salidas/`.

4. **Armar el informe**
   Abrir `documentacion/informe_tecnico.Rmd`, completar cada parte con lo
   que salió del notebook, y darle "Knit to PDF". La versión que terminé
   entregando quedó guardada en `entrega/entrega_informe_ipc.pdf`.

## Qué hace cada parte del notebook (`analisis_ipc.Rmd`)

**Chunk 1 — Cargar las librerías.** Acá cargo todo lo que voy a necesitar:
`here` para que las rutas funcionen sin importar en qué computador esté
trabajando, `readxl` para leer los Excel del DANE, `dplyr` y `tidyr` para
acomodar los datos, `purrr` para no repetir el mismo código 7 veces (una
por archivo), `stringr` para limpiar texto, y `ggplot2` con `scales` para
los gráficos.

**Chunk 2 — Ver dónde estoy parada.** Solo corro `getwd()` para confirmar
en qué carpeta está trabajando R antes de seguir.

**Chunk 3 — Buscar los archivos.** Busco los 7 Excel dentro de
`datos/crudos/`. También armo una tablita que traduce la abreviatura del
nombre del archivo —tipo "jul"— al nombre completo del mes, y una función
que saca esa abreviatura del nombre del archivo, para no escribirla a mano
cada vez.

**Chunk 4 — Leer la serie total.** Armo una función que lee la hoja donde
está la serie total del IPC de cada Excel, la aplico a los 7 archivos de
una vez, y junto todo en una sola tabla ordenada por fecha.

**Chunk 5 — Leer el detalle por división.** Lo mismo, pero para la hoja
que trae el detalle por cada división de gasto. Tuve que apuntar a un
rango de celdas específico porque esa hoja trae más columnas de las que
necesito.

**Chunk 6 — Armar la tabla de divisiones.** Aplico la función anterior a
los 7 archivos, limpio algunos nombres que traían espacios de más, y armo
la tabla final con el detalle de las 12 divisiones en los 7 meses.

**Chunk 7 — Revisar que no falte nada.** Reviso que las dos tablas tengan
el número de filas que deberían tener y que no haya ningún dato vacío. Ya
con eso, guardo ambas como CSV para no tener que releer los Excel cada vez.

**Chunk 8 — Mirar los números en general.** Saco mínimo, máximo, promedio
y cuartiles de la variación mensual y anual, solo para tener una idea
general antes de graficar.

**Chunk 9 — Primer gráfico.** Muestra cómo se mueve la variación mensual
comparada con la anual a lo largo de los 7 meses. Lo guardo como imagen.

**Chunk 10 — Segundo gráfico.** Me quedo solo con julio, el mes más
reciente, y muestro cuánto subió o bajó cada división ese mes.

**Chunk 11 — Tercer gráfico.** Ahora miro el promedio de los 7 meses
completos, para ver qué divisiones subieron más en promedio y no solo en
el último mes.

## Hasta dónde llega esto

Este trabajo se queda en armar el flujo de datos y plantear la idea de
intervención — no llegué a estimar ningún modelo, ni a diseñar o evaluar
la intervención a fondo. El periodo que miré (enero-julio de 2026) es
corto, así que no alcanza para ver patrones de estacionalidad o
tendencias de más largo plazo.
