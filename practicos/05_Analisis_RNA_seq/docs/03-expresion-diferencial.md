# Sección 3. Conteo, normalización y expresión diferencial

[Anterior: alineamiento](02-alineamiento.md) · [Inicio](../README.md) · [Siguiente: interpretación biológica](04-interpretacion.md)

Después del alineamiento, podemos cuantificar cuántos fragmentos se asignan a cada gen y comparar su abundancia entre las condiciones. En esta sección utilizaremos **HTSeq-count** para obtener conteos por gen y **DESeq2** para normalizarlos y evaluar la expresión diferencial entre células de animales mutantes para *pax6b* (**MUT**) y de tipo silvestre (**WT**).

Al finalizar, tendrás seis archivos de conteos, una tabla de resultados de expresión diferencial, conteos normalizados y gráficos para explorar las relaciones entre muestras.

## Paso 1. Preparar los archivos para el conteo

Identifica los **seis BAM ordenados por coordenadas** que utilizaste en la sección anterior y el archivo **`Zebrafish_Annotation`**, en formato GTF.

| Muestra | BAM que utilizaremos | Nombre sugerido para los conteos |
|---|---|---|
| WT1 | BAM de WT1 ordenado por coordenadas | `counts_WT1` |
| WT2 | BAM de WT2 ordenado por coordenadas | `counts_WT2` |
| WT3 | BAM de WT3 ordenado por coordenadas | `counts_WT3` |
| MUT1 | BAM de MUT1 ordenado por coordenadas | `counts_MUT1` |
| MUT2 | BAM de MUT2 ordenado por coordenadas | `counts_MUT2` |
| MUT3 | BAM de MUT3 ordenado por coordenadas | `counts_MUT3` |

Si HISAT2 ya generó los BAM con ese orden, utiliza esas salidas. Si ejecutaste Samtools sort, utiliza los archivos `HISAT2_WT1_sorted`, etc. **No es necesario volver a ordenarlos ni construir manualmente sus índices.**

El GTF proporciona las coordenadas de los exones y sus identificadores de gen. A diferencia del FASTA del genoma, es una anotación que permite relacionar los alineamientos con genes.

## Paso 2. Obtener conteos por gen con HTSeq-count

En datos paired-end, el conteo considera el **fragmento representado por el par**, de modo que R1 y R2 no se cuentan como dos réplicas ni se analizan por separado.

1. Busca **HTSeq-count** en Galaxy.
2. En **Aligned SAM/BAM file**, selecciona **Multiple datasets** y elige los seis BAM. Si prefieres, puedes ejecutar una muestra a la vez.
3. Selecciona `Zebrafish_Annotation` como archivo de anotación.
4. Configura los parámetros de la tabla. Algunas opciones pueden aparecer dentro de **Advanced options**.

| Parámetro | Valor para este práctico |
|---|---|
| GFF/GTF file | `Zebrafish_Annotation` |
| Mode | `union` |
| Stranded | `No` |
| Minimum alignment quality | `10` |
| Feature type | `exon` |
| ID attribute | `gene_id` |
| Orden de los alineamientos, si el formulario lo solicita | `position`, para BAM ordenados por coordenadas |

Mantén las demás opciones predeterminadas y registra sus valores, especialmente el tratamiento de alineamientos múltiples, secundarios y suplementarios. La selección **Stranded: No** corresponde a la biblioteca **Unstranded** utilizada en HISAT2.

5. Presiona **Run Tool** o **Execute**.
6. Renombra cada salida de conteos como `counts_WT1`, `counts_WT2`, etc., confirmando la muestra de entrada en los detalles de la ejecución.

**El orden declarado debe coincidir con el orden real del BAM.** Si una versión de la herramienta exige orden por nombre (`name`), prepara ese orden antes de ejecutar; no selecciones `name` para un BAM ordenado por coordenadas. Consulta la [documentación de HTSeq-count](https://htseq.readthedocs.io/en/latest/htseqcount.html).

### ¿Qué significan exon, gene_id y union?

- **exon:** selecciona las características de tipo exón presentes en la anotación.
- **gene_id:** agrupa las características por su identificador de gen para producir conteos a nivel génico.
- **union:** considera las características solapadas por las posiciones del fragmento. Una asignación compatible con más de un gen puede quedar como ambigua; no reparte automáticamente el fragmento entre esos genes.

**Para discutir:** ¿por qué utilizamos exones para cuantificar transcritos maduros? ¿Qué podría ocurrir con un fragmento que se solapa con dos genes?

## Paso 3. Explorar los archivos de conteos

Abre `counts_WT1` con la opción de visualización e identifica las columnas de **identificador de gen** y **conteo**. Revisa también los archivos de las demás muestras.

Los conteos deben ser enteros no negativos. Un cero indica que no se asignaron fragmentos al gen bajo esta configuración; por sí solo no demuestra que el gen sea biológicamente inexistente o que nunca se exprese.

HTSeq puede incluir filas especiales que resumen fragmentos no asignados a genes:

| Fila especial | Qué indica |
|---|---|
| `__no_feature` | No se pudo asignar a las características seleccionadas |
| `__ambiguous` | La asignación a un gen fue ambigua |
| `__too_low_aQual` | La calidad de alineamiento no alcanzó el umbral |
| `__not_aligned` | Sin alineamiento |
| `__alignment_not_unique` | Alineamiento no único, según las opciones utilizadas |

Estas filas son **contadores de diagnóstico, no genes**. Algunas versiones de Galaxy las presentan en una salida separada. Usa para DESeq2 la salida de conteos génicos indicada por la herramienta; los contadores especiales no deben interpretarse como resultados de expresión ni incorporarse a listas funcionales.

Comprueba que los seis archivos se generaron con el mismo GTF y configuración. Antes de ejecutar DESeq2, observa si tienen **encabezado**: el formato clásico de HTSeq tiene dos columnas sin encabezado, pero la salida puede variar entre versiones.

## Paso 4. Comprender la normalización y la comparación

Los conteos dependen tanto de la abundancia de los transcritos como de la cantidad y composición de los fragmentos secuenciados. DESeq2 estima **factores de tamaño** para hacer comparables las muestras antes de evaluar diferencias entre condiciones.

La entrada será la tabla de **conteos crudos de HTSeq**, no conteos ya normalizados, TPM, FPKM ni datos transformados. Los conteos normalizados y transformados serán salidas del análisis.

Con el método **ratio**, el factor de tamaño de cada muestra se obtiene mediante la **mediana de razones** respecto de una referencia basada en medias geométricas por gen. La alternativa **poscounts** utiliza una estimación modificada que permite trabajar con matrices donde los ceros impiden aplicar el método habitual.

Nuestra comparación será **MUT frente a WT**. Interpretaremos el cambio como:

**log2FoldChange = log2(MUT / WT)**

| Resultado | Interpretación |
|---|---|
| log2FoldChange positivo | Mayor expresión en MUT respecto de WT |
| log2FoldChange negativo | Menor expresión en MUT respecto de WT |
| log2FoldChange cercano a cero | Cambio estimado pequeño entre condiciones |

Por ejemplo, un cambio log2 de `1` representa aproximadamente el doble de expresión en MUT y uno de `−1`, aproximadamente la mitad.

## Paso 5. Ejecutar DESeq2

1. Busca **DESeq2** en Galaxy.
2. Selecciona la opción **Select datasets per level**, o su equivalente, para asignar los archivos a cada condición.
3. Añade un factor y nómbralo `condicion`.
4. Define los niveles de la siguiente manera:

| Nivel del factor | Nombre | Archivos de conteos |
|---|---|---|
| Factor level 1 | `MUT` | counts_MUT1, counts_MUT2, counts_MUT3 |
| Factor level 2 | `WT` | counts_WT1, counts_WT2, counts_WT3 |

El orden de niveles en la herramienta documentada por Galaxy produce la comparación **nivel 1 frente a nivel 2**. Lee la ayuda y la descripción del resultado para confirmar **MUT/WT** en tu ejecución. Puedes consultar el [tutorial de Galaxy sobre RNA-seq](https://training.galaxyproject.org/topics/transcriptomics/tutorials/ref-based/tutorial.html).

5. Configura las opciones de entrada y análisis:

| Opción | Selección |
|---|---|
| Files have header? | `No` si la primera fila contiene un gen y su conteo; `Yes` si contiene los nombres de columnas |
| Choice of input data | Count data |
| Additional batch factors | Sin archivo adicional para este diseño |
| Method for estimateSizeFactors | `ratio` |
| Fit type | `parametric` |
| Turn off outliers replacement | `No` |
| Turn off outliers filtering | `No` |
| Turn off independent filtering | `No` |

Si la estimación de factores de tamaño falla porque todos los genes tienen algún cero, revisa el mensaje y vuelve a ejecutar utilizando **poscounts**. Registra el método que finalmente utilizaste. La presencia de algunos ceros no exige por sí sola cambiar de método.

Mantendremos el control de valores atípicos y el filtrado independiente. Una expresión elevada no es una razón para desactivar estos controles.

6. En **Output options**, solicita las salidas disponibles:

- **Generate plots for visualizing the analysis results**.
- **Output sample size factors**.
- **Output normalised counts**.
- **Output VST normalized table**.

7. Establece `0.05` en **Alpha value for MA-plot**. Utilizaremos también **padj < 0.05** como criterio de significación en la interpretación de la tabla. El campo de coloración del gráfico no sustituye revisar los valores de `padj`.
8. Ejecuta y conserva las salidas.

## Paso 6. Reconocer las salidas de DESeq2

| Salida | Para qué sirve | Nombre sugerido |
|---|---|---|
| Result file | Consultar el cambio estimado y la significación por gen | `DESeq2_MUT_vs_WT_resultados` |
| DESeq2 plots | Explorar relaciones entre muestras y resultados globales | `DESeq2_MUT_vs_WT_graficos` |
| Size factors | Revisar los factores de normalización | `DESeq2_factores_tamano` |
| Normalized counts | Explorar la abundancia normalizada de cada gen por muestra | `DESeq2_conteos_normalizados` |
| VST table | Trabajar con datos transformados para explorar patrones globales | `DESeq2_VST` |

Los **conteos normalizados** y la **tabla VST** cumplen funciones diferentes. Los primeros facilitan explorar abundancias ajustadas por factores de tamaño; VST transforma los datos para reducir la dependencia de la variabilidad respecto de la abundancia. Ninguno se vuelve a introducir como conteo crudo en DESeq2.

## Paso 7. Interpretar la tabla de expresión diferencial

Abre la tabla de resultados e identifica las columnas por su nombre o la descripción de la salida; no asumas su posición.

| Campo | Interpretación |
|---|---|
| Identificador de gen | Gen de la anotación, normalmente un identificador Ensembl |
| baseMean | Promedio de los conteos normalizados entre todas las muestras |
| log2FoldChange | Cambio estimado MUT frente a WT |
| lfcSE | Error estándar del cambio log2 estimado |
| stat | Estadístico de la prueba, normalmente Wald en este análisis |
| pvalue | Valor p sin ajuste por comparaciones múltiples |
| padj | Valor p ajustado por comparaciones múltiples |

Usaremos **padj < 0.05** para identificar genes con expresión diferencial significativa. El ajuste controla la tasa esperada de falsos descubrimientos (**FDR**) bajo los supuestos del procedimiento; no es una probabilidad individual de que cada gen sea un falso positivo.

Un valor **NA** indica que no hay un resultado disponible para ese campo. Puede relacionarse con conteos nulos, filtrado independiente o valores atípicos. **NA no equivale a cero ni demuestra ausencia de cambio.** Puedes ampliar estos conceptos en la [documentación de DESeq2](https://bioconductor.org/packages/release/bioc/vignettes/DESeq2/inst/doc/DESeq2.html).

Selecciona un gen con resultados válidos y compara su cambio estimado con los conteos normalizados de WT y MUT. Esta comprobación ayuda a interpretar el sentido de la comparación.

## Paso 8. Explorar los gráficos

### PCA: relaciones entre muestras

En el análisis de componentes principales (**PCA**), cada punto representa una muestra. Observa qué réplicas están próximas, cómo se distribuyen las condiciones y si alguna muestra se separa del resto. Anota el porcentaje de varianza explicado por cada componente.

La separación entre condiciones puede aparecer en PC1, PC2 u otros componentes. El gráfico permite reconocer patrones, pero no demuestra por sí solo que toda diferencia se deba a la mutación.

### Mapa de calor de distancias entre muestras

Este gráfico complementa el PCA. Revisa la leyenda para reconocer qué colores corresponden a mayor o menor distancia. Compara las distancias entre réplicas de una misma condición y entre condiciones.

El mapa de **distancias entre muestras** no es el mismo que un mapa de expresión de genes estandarizada por z-score. Describe el gráfico que efectivamente generó tu análisis.

### Gráfico MA: abundancia y cambio de expresión

El gráfico MA relaciona la abundancia media con el cambio log2 estimado. Identifica los puntos destacados como significativos según la leyenda y observa cómo se distribuyen a distintas abundancias.

**Para discutir:** ¿los cambios grandes se concentran en genes de baja o alta abundancia? ¿Por qué debemos considerar también la incertidumbre y la significación?

## Actividad. Interpretar los resultados

En grupos de tres personas, revisen la tabla y los gráficos:

1. ¿Las réplicas de cada condición muestran patrones similares en PCA y distancias?
2. ¿Hay una muestra que requiera una revisión adicional?
3. ¿Qué representa un cambio log2 positivo en esta comparación?
4. Elijan un gen con resultado válido y expliquen su cambio, abundancia y significación utilizando la tabla y sus conteos normalizados.
5. ¿Por qué la magnitud del cambio y el valor p ajustado aportan información distinta?

Conserven los gráficos utilizados y completen las siguientes tablas.

## Registro del análisis

Copia las tablas en tu documento de trabajo.

### Parámetros de conteo y expresión diferencial

| Herramienta o parámetro | Valor utilizado |
|---|---|
| Versión de HTSeq-count | |
| Anotación | Zebrafish_Annotation (GTF) |
| Modo de conteo | union |
| Orientación | No / unstranded |
| Calidad mínima de alineamiento | 10 |
| Tipo de característica | exon |
| Atributo de agrupación | gene_id |
| Orden declarado del BAM | |
| Tratamiento de alineamientos múltiples y secundarios | |
| Versión de DESeq2 | |
| Archivos con encabezado | |
| Factor y niveles | condicion: MUT, WT |
| Comparación obtenida | MUT frente a WT |
| Método de factores de tamaño utilizado | |
| Tipo de ajuste | |
| Control de atípicos y filtrado independiente | |
| Umbral de significación | padj < 0.05 |

### Conteos y factores de tamaño

Suma solo las filas de genes para registrar fragmentos asignados. No sumes los contadores especiales de HTSeq como si fueran genes.

| Muestra | Archivo de conteos | Fragmentos asignados a genes | Factor de tamaño |
|---|---|---|---|
| WT1 | counts_WT1 | | |
| WT2 | counts_WT2 | | |
| WT3 | counts_WT3 | | |
| MUT1 | counts_MUT1 | | |
| MUT2 | counts_MUT2 | | |
| MUT3 | counts_MUT3 | | |

### Interpretación de gráficos

| Gráfico | Observaciones de tus resultados | Interpretación |
|---|---|---|
| PCA: varianza explicada por PC1 y PC2 | | |
| PCA: distribución de réplicas y condiciones | | |
| Distancias entre muestras | | |
| MA: abundancia y cambios | | |

### Ejemplo de un gen

| Identificador | baseMean | log2FoldChange | padj | Dirección del cambio e interpretación |
|---|---|---|---|---|
| | | | | |

## Antes de continuar

- [ ] Obtuve seis archivos de conteos con la misma anotación y configuración.
- [ ] Reconocí el formato de entrada y sus encabezados.
- [ ] Asigné correctamente las tres réplicas MUT y las tres WT.
- [ ] Confirmé que el resultado compara MUT frente a WT.
- [ ] Identifiqué las salidas, el significado de las columnas y el tratamiento de NA.
- [ ] Interpreté PCA, distancias y MA usando mis propios resultados.
- [ ] Completé el registro del análisis.

Ya puedes continuar con la [sección 4: interpretación biológica](04-interpretacion.md).

