# Sección 1. Control de calidad y preprocesamiento

[Anterior: cuenta y datos](00-cuenta-y-datos.md) · [Inicio](../README.md) · [Siguiente: alineamiento](02-alineamiento.md)

Antes de alinear las lecturas, debemos conocer su calidad. En esta sección utilizaremos **FastQC** para explorar los datos originales y **Trimmomatic** para eliminar adaptadores y recortar regiones de baja calidad. Luego repetiremos el control de calidad para evaluar el efecto del procesamiento.

Al finalizar, tendrás las lecturas pareadas procesadas de las seis muestras y sus reportes de calidad antes y después del recorte.

## Paso 1. Evaluar las lecturas originales con FastQC

FastQC examina distintos aspectos de las lecturas y genera un informe con gráficos y estadísticas. **Esta herramienta evalúa la calidad; no modifica ni recorta las secuencias.**

1. En el buscador de herramientas de Galaxy, escribe **FastQC** y abre la herramienta.
2. En el campo de entrada de lecturas, selecciona **Multiple datasets**.
3. Selecciona los doce archivos originales: `WT1_R1`, `WT1_R2`, `WT2_R1`, `WT2_R2`, `WT3_R1`, `WT3_R2`, `MUT1_R1`, `MUT1_R2`, `MUT2_R1`, `MUT2_R2`, `MUT3_R1` y `MUT3_R2`.
4. Mantén las demás opciones en sus valores predeterminados y presiona **Run Tool** o **Execute**, según la interfaz.
5. Espera a que los resultados terminen de generarse en el historial.

La selección múltiple permite ejecutar FastQC por separado sobre cada archivo. **No selecciones el genoma ni las anotaciones.**

### Reconocer las salidas

Para cada FASTQ, Galaxy genera habitualmente dos salidas:

| Salida | Qué contiene | Cómo la utilizaremos |
|---|---|---|
| Webpage o informe HTML | Gráficos y resumen de los módulos evaluados | Abrir con el icono de visualización para interpretar la calidad |
| RawData o datos del informe | Métricas calculadas por FastQC | Conservar como respaldo de las estadísticas |

La salida **RawData** de FastQC contiene métricas: no es una nueva copia de las lecturas originales.

Para mantener el historial organizado, puedes nombrar el informe visual `FastQC_WT1_R1_original` y la salida de métricas `FastQC_WT1_R1_original_datos`. Sigue el mismo criterio para los demás archivos.

## Paso 2. Interpretar los reportes de calidad

Abre los informes de **WT1 y MUT1**, revisando tanto **R1 como R2**. Luego compara el comportamiento general con las demás muestras.

Los módulos pueden aparecer con tres estados:

- **PASS:** el resultado está dentro del rango esperado por FastQC.
- **WARN:** hay una característica que merece revisión.
- **FAIL:** la métrica supera un umbral de alerta y requiere interpretación.

Estos estados orientan la revisión, pero no determinan por sí solos si una muestra sirve para el análisis. FastQC se utiliza con distintos tipos de secuenciación, y algunos patrones son habituales en RNA-seq. Consulta las explicaciones de cada módulo en el [manual de FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/).

| Módulo del informe | Qué muestra | Pregunta para interpretar tus datos |
|---|---|---|
| Basic Statistics | Número de lecturas, longitud y codificación de calidad | ¿Los archivos de una misma pareja tienen el mismo número de lecturas? |
| Per base sequence quality | Calidad de las bases según su posición en la lectura | ¿La calidad disminuye hacia el final? ¿R1 y R2 se comportan igual? |
| Per sequence quality scores | Distribución de la calidad promedio de las lecturas | ¿Hay un grupo importante de lecturas con baja calidad? |
| Per base sequence content | Proporción de A, C, G y T por posición | ¿Hay un sesgo al inicio o a lo largo de las lecturas? |
| Per base N content | Proporción de bases sin resolver | ¿En qué posiciones aparecen bases N? |
| Sequence Length Distribution | Distribución de longitudes | ¿Las lecturas tienen una longitud uniforme? |
| Sequence Duplication Levels | Frecuencia de secuencias repetidas | ¿El patrón podría relacionarse con transcritos muy abundantes? |
| Overrepresented sequences | Secuencias que aparecen con alta frecuencia | ¿Corresponden a adaptadores, contaminantes o transcritos abundantes? |
| Adapter Content | Evidencia de secuencias de adaptadores | ¿Aumenta su presencia hacia el final de las lecturas? |

### ¿Cómo interpretar la calidad Phred?

Un valor mayor representa menor probabilidad de error en la base. Por ejemplo, **Q20** corresponde a una probabilidad estimada de error de 1 % y **Q30**, de 0,1 %. En el gráfico de calidad por base, revisa tanto la tendencia central como la dispersión; una media aceptable puede ocultar bases de baja calidad en una parte de las lecturas.

En RNA-seq, un transcrito muy abundante puede generar muchas lecturas iguales. Por eso, **una alerta de duplicación no implica que debamos eliminar automáticamente las lecturas duplicadas**. Del mismo modo, un sesgo de composición puede estar asociado al protocolo de biblioteca y no corregirse mediante recorte.

### Actividad 1. Interpretar y proponer un procesamiento

Trabajen en grupos de tres personas. Revisen el/los módulos asignados por la docente y agreguen sus resultados a un [ppt colaborativo](https://docs.google.com/presentation/d/1RE9Z6s7X0kdHDzaGeQ7B2d-dBgFNy6K4wqOkpR0SJPM/edit?usp=sharing) durante **15–20 minutos**.

En cada diapositiva incluyan:

1. Qué evalúa el módulo.
2. Un gráfico o dato de sus reportes, indicando muestra y lectura.
3. Su interpretación y una acción propuesta para el pre-procesamiento, o una explicación de por qué no intervendrían.

Comparen al menos una muestra WT y una MUT. Presenten sus conclusiones en la discusión grupal.

## Paso 3. Procesar las lecturas con Trimmomatic

Trimmomatic permite eliminar adaptadores y recortar regiones de baja calidad. En este práctico trabajaremos con lecturas **paired-end**, por lo que debemos introducir juntos los archivos **R1 y R2 de la misma muestra**.

Las bibliotecas del estudio se prepararon con **Nextera-XT**, según los [métodos del artículo](https://doi.org/10.1186/s12915-020-00840-1). Utilizaremos la opción de adaptadores **Nextera paired-end** y un recorte por ventana deslizante. Para conocer las operaciones disponibles, consulta la [documentación de Trimmomatic](https://github.com/usadellab/Trimmomatic).

### Seleccionar la muestra

1. Busca **Trimmomatic** en Galaxy.
2. En **Single-end or paired-end reads?**, selecciona **Paired-end (two separate input files)**.
3. En **Input FASTQ file (R1/first of pair)**, selecciona `WT1_R1`.
4. En **Input FASTQ file (R2/second of pair)**, selecciona `WT1_R2`.

### Configurar la eliminación de adaptadores

1. Activa **Perform initial ILLUMINACLIP step?** seleccionando **Yes**.
2. En la selección de adaptadores estándar, elige **Nextera (paired-end)** o la etiqueta equivalente que aparezca en la herramienta.
3. Mantén los demás parámetros de eliminación de adaptadores en sus valores predeterminados y anótalos en tu registro.

### Configurar el recorte por calidad

En las operaciones de Trimmomatic, añade o selecciona **Sliding Window trimming (SLIDINGWINDOW)**:

| Parámetro | Valor para este práctico |
|---|---|
| Number of bases to average across | `4` |
| Average quality required | `25` |

Esta operación recorre la lectura con una ventana de cuatro bases y recorta desde el punto donde la calidad promedio cae por debajo de 25. No significa eliminar individualmente todas las bases con calidad menor que 25.

Para esta ejecución utilizaremos **ILLUMINACLIP y SLIDINGWINDOW**, sin añadir otras operaciones. Registra la versión de Trimmomatic y los parámetros utilizados.

Presiona **Run Tool** o **Execute**. Repite el procedimiento para las otras cinco muestras, conservando los mismos parámetros:

| Muestra | Entrada R1 | Entrada R2 |
|---|---|---|
| WT1 | WT1_R1 | WT1_R2 |
| WT2 | WT2_R1 | WT2_R2 |
| WT3 | WT3_R1 | WT3_R2 |
| MUT1 | MUT1_R1 | MUT1_R2 |
| MUT2 | MUT2_R1 | MUT2_R2 |
| MUT3 | MUT3_R1 | MUT3_R2 |

**Realizaremos seis ejecuciones de Trimmomatic, una por muestra.**

## Paso 4. Reconocer y organizar las lecturas procesadas

En modo paired-end, Trimmomatic genera cuatro archivos de lecturas por muestra:

| Salida | Qué contiene | Nombre sugerido para WT1 |
|---|---|---|
| R1 paired | Lecturas R1 que conservan una pareja R2 | `WT1_trim_R1` |
| R2 paired | Lecturas R2 que conservan una pareja R1 | `WT1_trim_R2` |
| R1 unpaired | Lecturas R1 cuya pareja R2 fue descartada | `WT1_trim_R1_unpaired` |
| R2 unpaired | Lecturas R2 cuya pareja R1 fue descartada | `WT1_trim_R2_unpaired` |

Renombra las salidas de las demás muestras siguiendo el mismo criterio. Para el alineamiento de la sección 2 utilizaremos **solo las salidas paired**, es decir, doce archivos procesados organizados en seis pares.

Conserva las salidas unpaired en el historial para reconocer qué ocurrió durante el procesamiento; puedes ocultarlas para facilitar la navegación. Tampoco elimines los archivos originales ni sus reportes de FastQC, porque los utilizaremos en la comparación.

Si el resumen de ejecución informa pares de entrada y pares que conservaron ambas lecturas, registra esos valores. Puedes calcular:

**Retención de pares (%) = 100 × pares con ambas lecturas conservadas / pares de entrada.**

## Paso 5. Repetir FastQC después del procesamiento

1. Abre nuevamente **FastQC**.
2. Selecciona **Multiple datasets** y elige los doce archivos procesados **paired**: `WT1_trim_R1`, `WT1_trim_R2`, y sus equivalentes para las demás muestras.
3. Ejecuta con las mismas opciones que utilizaste para los archivos originales.
4. Renombra los informes para distinguirlos de los anteriores; por ejemplo, `FastQC_WT1_R1_procesado` y `FastQC_WT1_R1_procesado_datos`.
5. Compara cada archivo procesado con su correspondiente original. Por ejemplo, compara `WT1_trim_R1` con `WT1_R1`, no con R2 ni con otra muestra.

Compara primero WT1 y MUT1, tanto R1 como R2, y después revisa si las demás muestras muestran un comportamiento similar.

| Aspecto | Antes del procesamiento | Después del procesamiento |
|---|---|---|
| Número de lecturas | Registrar | Registrar |
| Longitud o rango de longitudes | Registrar | Registrar |
| Calidad por base | Describir tendencia y dispersión | Describir cambios |
| Contenido de adaptadores | Describir | Describir |
| Bases N | Describir | Describir |

Una menor cantidad de lecturas y una distribución de longitudes más variable pueden ser consecuencias del recorte. Evalúa si mejoraron la calidad y el contenido de adaptadores, y cuánto material se perdió. **No es necesario que todos los módulos terminen en PASS para considerar útil el procesamiento.**

### Actividad 2. Evaluar el efecto del procesamiento

En los mismos grupos, respondan:

1. ¿Qué cambió en la calidad por base y en el contenido de adaptadores?
2. ¿Cambió la longitud de las lecturas? ¿Cómo se explica?
3. ¿Qué alertas permanecen y por qué podrían persistir?
4. ¿R1 y R2 se comportaron de forma similar?
5. ¿Qué información perdemos al continuar únicamente con lecturas paired?

Apoyen sus respuestas con los reportes de una muestra WT y una MUT. Conserven los gráficos o capturas que utilizaron y completen las tablas de registro que aparecen a continuación.

## Registro del análisis

Copia estas tablas en tu documento de trabajo y complétalas durante el práctico. En las columnas de observaciones, resume los cambios que identificaste en los reportes; no basta con escribir PASS, WARN o FAIL.

### Parámetros utilizados

Como procesaremos todas las muestras con la misma configuración, registra los parámetros una sola vez. Si modificas una ejecución, identifica la muestra y explica el cambio.

| Herramienta o parámetro | Valor utilizado |
|---|---|
| Versión de FastQC | |
| Versión de Trimmomatic | |
| Tipo de lecturas | Paired-end, dos archivos por muestra |
| Adaptadores seleccionados | Nextera paired-end |
| Parámetros de ILLUMINACLIP | |
| Ventana de SLIDINGWINDOW | 4 bases |
| Calidad promedio mínima de SLIDINGWINDOW | 25 |

### Retención de pares por muestra

Registra los valores del resumen de Trimmomatic. Si no encuentras el total de entrada en ese resumen, utiliza el número de lecturas de **uno** de los archivos originales del par, obtenido en Basic Statistics de FastQC. No sumes R1 y R2: cada lectura de R1 tiene una pareja en R2.

| Muestra | Pares de entrada | Pares con ambas lecturas conservadas | Retención de pares (%) |
|---|---|---|---|
| WT1 | | | |
| WT2 | | | |
| WT3 | | | |
| MUT1 | | | |
| MUT2 | | | |
| MUT3 | | | |

**Retención de pares (%) = 100 × pares con ambas lecturas conservadas / pares de entrada.**

### Comparación de calidad antes y después

Completa una fila por archivo. Anota el número de lecturas y la longitud o rango de longitudes que informa FastQC. Describe en la última columna los cambios en calidad por base, adaptadores y bases N.

| Archivo original | Lecturas antes | Lecturas paired después | Longitud antes (nt) | Longitud después (nt) | Cambios observados en FastQC |
|---|---|---|---|---|---|
| WT1_R1 | | | | | |
| WT1_R2 | | | | | |
| WT2_R1 | | | | | |
| WT2_R2 | | | | | |
| WT3_R1 | | | | | |
| WT3_R2 | | | | | |
| MUT1_R1 | | | | | |
| MUT1_R2 | | | | | |
| MUT2_R1 | | | | | |
| MUT2_R2 | | | | | |
| MUT3_R1 | | | | | |
| MUT3_R2 | | | | | |

El número de lecturas paired después del procesamiento debe coincidir entre R1 y R2 de una misma muestra y corresponder al número de pares con ambas lecturas conservadas.
## Antes de continuar

- [ ] Ejecuté FastQC sobre los doce archivos originales.
- [ ] Revisé los reportes e interpreté sus principales alertas.
- [ ] Ejecuté Trimmomatic sobre las seis muestras con los mismos parámetros.
- [ ] Identifiqué y renombré las salidas paired y unpaired.
- [ ] Ejecuté FastQC sobre los doce archivos procesados paired.
- [ ] Comparé los reportes antes y después del procesamiento.

Ya puedes continuar con la [sección 2: alineamiento al genoma de referencia](02-alineamiento.md).

