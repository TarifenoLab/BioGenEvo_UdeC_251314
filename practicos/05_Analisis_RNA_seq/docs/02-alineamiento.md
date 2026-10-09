# Sección 2. Alineamiento al genoma de referencia

[Anterior: calidad y preprocesamiento](01-calidad.md) · [Inicio](../README.md) · [Siguiente: conteo y expresión diferencial](03-expresion-diferencial.md)

Una vez procesadas las lecturas, necesitamos identificar de qué regiones del genoma provienen. En esta sección utilizaremos **HISAT2** para alinearlas al genoma de referencia de pez cebra y evaluaremos los resultados mediante estadísticas de alineamiento y herramientas de **RSeQC**.

Al finalizar, tendrás **seis archivos BAM**, uno por muestra, sus resúmenes de alineamiento y gráficos para explorar la cobertura del cuerpo génico.

## Paso 1. Comprender el alineamiento de RNA-seq

Los RNA mensajeros maduros contienen exones unidos después del procesamiento del transcrito. Por eso, una lectura puede corresponder a una región contenida dentro de un exón o abarcar una unión entre dos exones.

<img src="https://github.com/TarifenoLab/BioGenEvo_UdeC_251314/raw/refs/heads/main/practicos/05_Analisis_RNA_seq/Images/step2.1.png" width="300" alt="Alineamiento de lecturas dentro de un exón y a través de una unión entre exones">

| Tipo de alineamiento | Cómo se representa en el genoma |
|---|---|
| Lectura contenida en un exón | Se alinea de forma continua en esa región |
| Lectura que abarca una unión entre exones | Se alinea en segmentos separados por un salto sobre el intrón |

Para reconocer estas uniones utilizaremos un alineador que admite empalme (*spliced aligner*), **HISAT2**. Una lectura puede contener errores de secuenciación o variantes respecto de la referencia; el alineamiento no exige que todas las bases sean idénticas.

Antes de ejecutar la herramienta, identifica en tu historial:

- Los **doce FASTQ procesados paired**, nombrados `WT1_trim_R1`, `WT1_trim_R2`, etc.
- El genoma de referencia, `Zebrafish_Genome`.
- La anotación `Zebrafish_Annotation_bed`, que utilizaremos posteriormente con RSeQC.

Para más información, consulta el [manual de HISAT2](https://daehwankimlab.github.io/hisat2/manual/).

## Paso 2. Alinear una muestra con HISAT2

1. Busca **HISAT2** en el buscador de herramientas de Galaxy.
2. Configura la primera muestra, **WT1**, con los siguientes parámetros. Las etiquetas pueden variar ligeramente entre versiones.

| Campo de HISAT2 | Selección |
|---|---|
| Source for the reference genome | Use a genome from history |
| Select the reference genome | `Zebrafish_Genome` |
| Is this a single or paired library? | Paired-end |
| FASTA/Q file #1 | `WT1_trim_R1` |
| FASTA/Q file #2 | `WT1_trim_R2` |
| Specify strand information | Unstranded |
| Paired-end options | Valores predeterminados |

Trabajaremos con biblioteca **sin orientación específica** (*unstranded*), de acuerdo con el protocolo de este práctico. Esta opción se refiere a la relación de las lecturas con la hebra del transcrito; no significa que la secuenciación sea single-end.

3. Abre **Summary Options**. Activa la salida del resumen de alineamiento y, si se ofrece, el resumen en formato nuevo o extendido. Conserva las estadísticas para la comparación entre muestras.
4. Mantén las demás opciones en sus valores predeterminados y registra la versión utilizada.
5. Presiona **Run Tool** o **Execute**.

**Selecciona las lecturas procesadas paired.** No utilices las salidas unpaired ni mezcles archivos de muestras distintas. Si el genoma se utiliza desde el historial, Galaxy puede necesitar construir su índice antes de alinear, por lo que la ejecución puede tardar.

### Repetir para las seis muestras

Ejecuta HISAT2 una vez por muestra, con el mismo genoma y la misma configuración:

| Muestra | Archivo 1 | Archivo 2 |
|---|---|---|
| WT1 | WT1_trim_R1 | WT1_trim_R2 |
| WT2 | WT2_trim_R1 | WT2_trim_R2 |
| WT3 | WT3_trim_R1 | WT3_trim_R2 |
| MUT1 | MUT1_trim_R1 | MUT1_trim_R2 |
| MUT2 | MUT2_trim_R1 | MUT2_trim_R2 |
| MUT3 | MUT3_trim_R1 | MUT3_trim_R2 |

## Paso 3. Reconocer y nombrar los resultados

Las salidas principales son:

| Salida | Qué contiene | Nombre sugerido para WT1 |
|---|---|---|
| Alineamientos en formato BAM | Posición y características de los alineamientos de las lecturas | `HISAT2_WT1` |
| Resumen de alineamiento | Estadísticas de la ejecución | `HISAT2_WT1_resumen` |

Un archivo BAM es una representación binaria de los alineamientos. Lo utilizaremos para evaluar cobertura y contar fragmentos por gen; no se interpreta como un archivo FASTQ.

Para renombrar cada salida:

1. Confirma a qué muestra pertenecen sus archivos de entrada, consultando los detalles de la ejecución.
2. Abre la edición de atributos, habitualmente mediante el icono del **lápiz**.
3. Cambia el campo **Name** y guarda.
4. Repite para todas las muestras, usando `HISAT2_WT2`, `HISAT2_MUT1`, etc., y sus respectivos resúmenes.

## Paso 4. Interpretar las estadísticas de alineamiento

Abre el resumen de cada muestra con la opción de visualización. Identifica el total de pares procesados y cómo se distribuyen los alineamientos.

| Categoría | Interpretación |
|---|---|
| Concordant alignment | Ambos extremos se alinean con una disposición compatible con las reglas del alineador para el par |
| Concordantly exactly 1 time | El par tiene una única ubicación concordante reportada |
| Concordantly more than 1 time | Se reporta más de una ubicación concordante para el par |
| Discordant alignment | Ambos extremos se alinean, pero su disposición no cumple las reglas de concordancia |
| Sin alineamiento concordante ni discordante | HISAT2 puede intentar alinear por separado los extremos restantes, según las opciones utilizadas |
| Overall alignment rate | Porcentaje global de lecturas alineadas, que puede incluir extremos alineados individualmente |

**El porcentaje global de alineamiento no es lo mismo que el porcentaje de pares alineados de forma concordante y única.** Revisa las unidades y los denominadores del resumen: algunos porcentajes se calculan sobre un subconjunto de pares o sobre extremos individuales. No sumes porcentajes de categorías con denominadores distintos.

No existe un porcentaje único que garantice un buen análisis. Compara las muestras y considera calidad de las lecturas, longitud después del recorte, similitud con la referencia y posibles contaminantes. Un porcentaje alto tampoco demuestra que todas las lecturas puedan asignarse a un gen.

**Para discutir:** ¿hay una muestra que se diferencie de las demás? ¿Qué revisarías antes de atribuir esa diferencia a la condición biológica?

## Paso 5. Preparar los BAM para evaluar la cobertura

Para trabajar con herramientas que utilizan posiciones genómicas, necesitaremos los BAM **ordenados por coordenadas**.

1. Revisa en los detalles de las salidas si los BAM de HISAT2 ya están ordenados por coordenadas.
2. Si no lo están, busca **Samtools sort**.
3. Selecciona los seis BAM mediante **Multiple datasets**.
4. En **Primary sort key**, selecciona **coordinate** y ejecuta.
5. Renombra las salidas `HISAT2_WT1_sorted`, `HISAT2_WT2_sorted`, etc.

Si los BAM ya tienen el orden requerido, puedes utilizarlos directamente. Conserva los archivos que elijas: también se utilizarán en la sección de conteo. Galaxy puede generar los índices asociados como parte de la preparación de los datasets.

## Paso 6. Explorar la cobertura del cuerpo génico

La cobertura indica cómo se distribuyen las lecturas a lo largo de los transcritos. Utilizaremos **Gene Body Coverage**, de RSeQC, para comparar las seis muestras y explorar posibles sesgos hacia los extremos **5′ y 3′**.

La herramienta representa el cuerpo génico en **100 posiciones relativas**, lo que permite comparar transcritos de distintas longitudes. Esto no significa que los transcritos tengan 100 nucleótidos.

1. Busca **Gene Body Coverage** o **Body Coverage (BAM)** en Galaxy.
2. Selecciona **Combine multiple samples into one plot**, si la versión ofrece esta opción.
3. Introduce los seis BAM ordenados por coordenadas.
4. En **Reference gene model**, selecciona `Zebrafish_Annotation_bed`. Este análisis utiliza un modelo de transcritos en formato **BED12**.
5. En **Minimum mRNA length**, utiliza `100` nucleótidos.
6. Mantén las demás opciones predeterminadas y ejecuta.

El parámetro de longitud mínima y las 100 posiciones relativas del gráfico cumplen funciones distintas: el primero selecciona los transcritos que se evalúan y el segundo define la escala de representación.

### Interpretar las salidas

Según la versión, se generarán curvas de cobertura, un mapa de calor y una tabla de valores. Abre primero la salida **Curves** o su equivalente.

- Compara la forma de las curvas entre muestras.
- Identifica si la cobertura se concentra hacia 5′, hacia 3′ o presenta un patrón más uniforme.
- Revisa si alguna muestra se diferencia del resto.

Un sesgo hacia 3′ puede relacionarse con degradación del RNA o con el protocolo de biblioteca. El gráfico por sí solo no permite identificar su causa. Además, estas curvas describen la distribución relativa de cobertura; no equivalen a una comparación directa de expresión total entre muestras.

Consulta la [documentación de RSeQC](https://rseqc.sourceforge.net/) para explorar los módulos y sus salidas.

## Paso 7. Explorar la saturación de uniones de empalme — actividad complementaria

Al aumentar el número de lecturas, podemos detectar más uniones entre exones. **Junction Saturation** evalúa cómo cambia el número de uniones detectadas al analizar fracciones crecientes de los alineamientos.

1. Busca **Junction Saturation**, de RSeQC.
2. Selecciona los seis BAM para ejecutarlos por separado mediante **Multiple datasets**, o ejecuta uno a uno.
3. Selecciona `Zebrafish_Annotation_bed` como modelo de referencia.
4. Configura los siguientes parámetros cuando estén disponibles en la versión de la herramienta:

| Parámetro | Valor |
|---|---|
| Minimum intron length | 50 nucleótidos |
| Minimum number of supporting reads | 1 |
| Minimum mapping quality | 30 |
| Fracciones de submuestreo | Valores predeterminados; registrar cuáles se utilizaron |

5. Ejecuta y renombra los resultados indicando la muestra, por ejemplo `JunctionSaturation_WT1`.

El gráfico distingue habitualmente uniones **conocidas**, **nuevas** y **totales**, según su relación con la anotación. Una unión nueva no es automáticamente una nueva isoforma validada: también puede requerir revisar evidencia y errores de alineamiento.

Una curva que se aproxima a una meseta sugiere que añadir lecturas aportaría menos uniones bajo las condiciones evaluadas. Una curva que sigue creciendo indica que podrían detectarse más uniones con mayor profundidad.

**Estamos trabajando con un subconjunto de lecturas.** Por lo tanto, este resultado describe los datos del práctico y no permite concluir que la secuenciación del estudio original haya sido insuficiente. Tampoco demuestra, por sí solo, saturación de todos los genes o isoformas.

## Actividad. Interpretar la calidad del alineamiento

En grupos de tres personas, revisen las estadísticas de HISAT2 y las curvas de cobertura. Si realizaron la actividad complementaria, incluyan también la saturación de uniones.

Durante **15–20 minutos**, preparen respuestas apoyadas por sus resultados:

1. ¿Las seis muestras presentan porcentajes y tipos de alineamiento similares?
2. ¿Qué diferencia hay entre alineamiento global y alineamiento concordante único?
3. ¿La cobertura muestra sesgo hacia algún extremo? ¿El patrón es común a todas las muestras?
4. ¿Qué hipótesis explicaría una muestra diferente y qué información permitiría evaluarla?
5. Si exploraron saturación, ¿las curvas se acercan a una meseta? ¿Qué pueden concluir sobre el subconjunto analizado?

Presenten sus conclusiones y distingan lo que observaron de las posibles explicaciones.

## Registro del análisis

Copia las siguientes tablas en tu documento de trabajo y complétalas durante esta sección.

### Configuración utilizada

| Herramienta o parámetro | Valor utilizado |
|---|---|
| Versión de HISAT2 | |
| Genoma de referencia | Zebrafish_Genome |
| Tipo de lecturas | Paired-end |
| Información de hebra | Unstranded |
| Opciones de resumen | |
| Orden de los BAM utilizados para cobertura | Coordenadas |
| Versión de RSeQC | |
| Modelo génico | Zebrafish_Annotation_bed |
| Longitud mínima de transcrito para cobertura | 100 nucleótidos |

### Estadísticas de HISAT2

Registra **cantidades de pares** en las columnas de concordancia y discordancia, y copia el porcentaje global tal como aparece en el resumen. El total de pares de entrada debe corresponder a los pares conservados después de Trimmomatic.

| Muestra | Pares de entrada | Pares concordantes únicos | Pares concordantes múltiples | Pares discordantes | Alineamiento global (%) |
|---|---|---|---|---|---|
| WT1 | | | | | |
| WT2 | | | | | |
| WT3 | | | | | |
| MUT1 | | | | | |
| MUT2 | | | | | |
| MUT3 | | | | | |

### Cobertura y saturación

Describe la forma de las curvas. Si no realizaste Junction Saturation, escribe «No realizado» en esa columna.

| Muestra | Patrón de cobertura y sesgo 5′/3′ | Saturación de uniones | Observaciones |
|---|---|---|---|
| WT1 | | | |
| WT2 | | | |
| WT3 | | | |
| MUT1 | | | |
| MUT2 | | | |
| MUT3 | | | |

## Antes de continuar

- [ ] Alineé las seis muestras utilizando sus lecturas procesadas paired.
- [ ] Identifiqué y renombré los seis BAM y sus resúmenes.
- [ ] Registré e interpreté las estadísticas de alineamiento.
- [ ] Identifiqué los BAM ordenados por coordenadas que utilizaré en adelante.
- [ ] Comparé las curvas de cobertura entre las muestras.
- [ ] Conservé los parámetros, gráficos y observaciones en mi registro.

Ya puedes continuar con la [sección 3: conteo y expresión diferencial](03-expresion-diferencial.md).

