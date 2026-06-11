# Proyecto Open Data: Análisis Comparativo de Sistemas Educativos (Paraguay vs. Chile 2023)

## Objetivo del Proyecto
Este proyecto realiza un análisis comparativo utilizando datos abiertos gubernamentales sobre la infraestructura y matrícula escolar de Paraguay y Chile durante el año 2023. El objetivo es identificar diferencias en los modelos de gestión (público vs. subvencionado/privado), la distribución demográfica (ruralidad) y el nivel de cobertura poblacional en la educación de ambos países.

## Fuentes de Datos
Los datos fueron obtenidos de los portales oficiales de datos abiertos de cada país. 
*( Fecha de descarga de los datos: 8 de Mayo 2026)*

**1. Chile**
* **Portal:** Ministerio de Educación (Mineduc) - [https://datosabiertos.mineduc.cl/](https://datosabiertos.mineduc.cl/)
* **Archivos descargados:** * `Directorio-Oficial-EE-2023-1.rar`
  * `Resumen-matricaula-EE-2023.rar`

**2. Paraguay**
* **Portal:** Ministerio de Educación y Ciencias (MEC) - [https://datos.mec.gov.py/data](https://datos.mec.gov.py/data)
* **Archivos descargados:**
  * `establecimientos_20260507.csv`
  * `matriculaciones_departamentos_distritos_20260507.csv`

## Herramientas Utilizadas
* **Microsoft Power BI:** Para el modelado de datos y visualización.
* **Power Query:** Para la limpieza y transformación de datos (ETL).
* **Lenguaje DAX:** Para la creación de medidas calculadas.

## Instrucciones de Ejecución y Transformación de Datos (Metodología)

Para replicar este análisis en Power BI, se siguieron los siguientes pasos técnicos:

### Paso 1: Carga y Relación de Datos (Power Query)
**Para Paraguay:**
1. Se cargaron los archivos CSV de establecimientos y matriculaciones.
2. Se creó una tabla intermedia (Dimensión) llamada `dim_establecimiento` copiando la tabla de establecimientos.
3. En esta nueva tabla, se borraron todas las columnas excepto `codigo_departamento` y `nombre_departamento`.
4. Se seleccionó la columna de código y se aplicó la función **"Quitar duplicados"** para obtener valores únicos.
5. Finalmente, se unieron (relacionaron) la tabla de establecimientos y la de matriculaciones a través del `codigo_departamento`.

**Para Chile:**
1. Tras extraer los archivos `.rar`, se cargaron los datos en Power BI.
2. El sistema detectó y unió automáticamente las tablas basándose en sus llaves relacionales.
3. **Traducción de Datos (Normalización):** El sector o rubro de las instituciones chilenas venía representado por códigos numéricos. Para solucionar esto, se creó una **Columna Condicional** que asocia dichos códigos con los nombres descriptivos de los sectores (Ej: 1 = Municipal, 2 = Particular Subvencionado, etc.).

### Paso 2: Creación de Medidas Calculadas (DAX)
Una vez estructurado el modelo, se crearon medidas "espejo" para comparar ambos países utilizando tablas auxiliares de población (Año 2023). Las medidas principales implementadas fueron:
* `% Mujer CL: % Mujeres CL = DIVIDE( SUM('_Matricula_CL'[MAT_MUJ_TOT]), [Total_Matricula_CL], 0)` / 
`% Mujer PY:  % Mujeres PY DIVIDE( SUM(matriculaciones_departamentos_distritos_PY[Total mujer PY]), [Total Matricula PY],0)`

* `% Hombres CL: % Hombres CL = DIVIDE( SUM('_Matricula_CL'[MAT_HOM_TOT]), [Total_Matricula_CL], 0)` / 
`% Hombres PY: % Hombres PY = DIVIDE( SUM(matriculaciones_departamentos_distritos_PY[Total hombres PY]),  [Total Matricula PY], 0)`
* `% Escuelas rurales CL: Total hombres CL = SUM('_Matricula_CL'[MAT_HOM_TOT]) ` / 
`% Escuelas rurales PY`
* `Total mujeres CL:Total mujeres CL = SUM('_Matricula_CL'[MAT_MUJ_TOT])` / `Total mujeres PY`
* `Total matricula CL:Total_Matricula_CL = SUM('_Matricula_CL'[MAT_TOTAL])` / 
`Total matricula PY: Total Matricula PY = SUM(matriculaciones_departamentos_distritos_PY[Total hombres PY]) + SUM(matriculaciones_departamentos_distritos_PY[Total mujer PY])`
* `Promedio alumnos x escuela CL:Promedio Alumnos  x Escuela CL = DIVIDE([Total_Matricula_CL], [Cant Escuelas CL])` / 
`Promedio alumnos x escuela PY: Promedio Alumnos x Escuela PY = DIVIDE(matriculaciones_departamentos_distritos_PY[Total Matricula PY], [Cant Escuelas PY])`
* `% Cobertura poblacional CL: % Cobertura Poblacional CL = DIVIDE([Total_Matricula_CL], _Matricula_CL[poblacion Chile])` / `% Cobertura poblacional PY:% Cobertura Poblacional PY = DIVIDE([Total Matricula PY], [Poblacion Paraguay])` (Cruzando la matrícula total con la población total de cada país).
