# Sección 4. Interpretación biológica de los resultados

[Anterior: conteo y expresión diferencial](03-expresion-diferencial.md) · [Inicio](../README.md)

Hasta ahora hemos evaluado las lecturas, realizado el alineamiento y obtenido una tabla de expresión diferencial. El siguiente paso es relacionar esos resultados con la biología de las células endocrinas pancreáticas y la alteración de *pax6b*.

En esta sección incorporaremos nombres de genes a la tabla, separaremos genes con mayor y menor expresión en MUT y exploraremos sus funciones y el enriquecimiento de procesos biológicos. Al finalizar, elaborarás conclusiones apoyadas por genes, estadísticas y anotaciones.

## Paso 1. Identificar las tablas de entrada

Necesitarás las salidas de la sección anterior:

| Archivo | Para qué lo utilizaremos |
|---|---|
| `DESeq2_MUT_vs_WT_resultados` | Identificadores, cambios log2 y significación estadística |
| `DESeq2_conteos_normalizados` | Abundancia normalizada de cada gen en las seis muestras |

Confirma que la comparación sea **MUT frente a WT**: un cambio positivo indica mayor expresión en MUT y uno negativo, menor expresión en MUT.

Conserva siempre una copia de la tabla completa, incluidos los genes no significativos y los resultados ausentes. No filtres todavía la única copia disponible.

## Paso 2. Obtener nombres de genes con Ensembl BioMart

Los identificadores Ensembl permiten reconocer genes de manera consistente, pero sus nombres facilitan la lectura y búsqueda de funciones. Utilizaremos **BioMart** para obtener la correspondencia entre ambos.

1. Abre **[Ensembl BioMart](https://jun2026.archive.ensembl.org/biomart/martview/8436712ae1c2aa8b82f2bf5248248750)**.
2. En **Choose database**, selecciona **Ensembl Genes**. 
3. En **Choose dataset**, selecciona **Zebrafish genes (Danio rerio)**.
4. Abre **Filters** y busca la selección por lista de identificadores, habitualmente **GENE → Input external references ID list**.
5. Selecciona **Gene stable ID** y pega los identificadores de la tabla completa de DESeq2, uno por línea y sin encabezado. Puedes extraer esa columna con **Cut columns from a table** en Galaxy y descargarla como texto.
6. En **Attributes**, selecciona únicamente **Gene stable ID** y **Gene name**. Desmarca atributos de transcritos o proteínas que no necesites.
7. Abre **Results** y exporta todos los resultados a un archivo **TSV**. Si se ofrece **Unique results only**, actívalo para evitar filas idénticas repetidas.
8. Importa el TSV a Galaxy con **Upload Data → Choose local files**, asigna formato `tabular` y nómbralo `Gene_names`.

Al seleccionar **Gene stable ID** como filtro, introduce identificadores de genes, no nombres de genes ni identificadores de transcritos. Consulta la [guía de BioMart](https://jun2026.archive.ensembl.org/info/data/biomart/how_to_use_biomart.html) si necesitas reconocer filtros y atributos.

Un gen puede no tener un nombre disponible. Conserva su identificador para seguir analizándolo. Si utilizas una versión diferente de Ensembl, registra ese cambio y revisa las correspondencias que falten.

## Paso 3. Construir una tabla anotada

### Incorporar los nombres

1. Busca **Join two datasets** o una herramienta equivalente de unión de tablas en Galaxy.
2. Selecciona como primera tabla los **resultados completos de DESeq2**.
3. Selecciona su columna de identificador de gen como clave de unión.
4. Selecciona `Gene_names` como segunda tabla y **Gene stable ID** como clave correspondiente.
5. Configura la unión para **conservar las filas de la primera tabla aunque no tengan coincidencia** en la segunda. Si la herramienta elegida no ofrece esa posibilidad, usa otra que permita una unión izquierda (*left join*).
6. Configura el tratamiento de los encabezados según el contenido real de las tablas y ejecuta.
7. Renombra la salida `DESeq2_MUT_vs_WT_anotado`.

Comprueba el número de filas antes y después. La anotación no debería eliminar genes ni multiplicar sus resultados. **Unique results only** elimina filas idénticas, pero no garantiza que cada identificador aparezca una sola vez si tiene varias correspondencias. Revisa esos casos antes de continuar.

### Incorporar los conteos normalizados

Repite la unión utilizando la tabla anotada como primera entrada y los conteos normalizados como segunda, siempre por **identificador de gen**. Conserva todas las filas de la tabla anotada y renombra la salida `DESeq2_MUT_vs_WT_tabla_completa`.

La tabla final debe contener:

| Información | Columnas esperadas |
|---|---|
| Identidad | Identificador Ensembl y nombre del gen |
| Expresión diferencial | baseMean, log2FoldChange, lfcSE, stat, pvalue y padj |
| Abundancia por muestra | Conteos normalizados de WT1, WT2, WT3, MUT1, MUT2 y MUT3 |

Revisa los encabezados para reconocer el orden real de las muestras. Una unión puede añadir columnas duplicadas de identificadores: conserva una clave identificable, sin eliminar columnas por su letra o número de manera automática.

### Abrir la tabla en una hoja de cálculo

Descarga el TSV e impórtalo en Excel o una herramienta equivalente utilizando **tabulador**, codificación **UTF-8** y separador decimal **punto**. Importa los identificadores y nombres como texto para evitar conversiones automáticas a fechas. Confirma que los conteos y valores estadísticos se reconozcan como números, incluidos los escritos en notación científica.

## Paso 4. Seleccionar y separar los genes diferenciales

Trabaja sobre una copia de la tabla completa:

1. Selecciona genes con **padj válido y menor que 0.05**. Excluye `NA` y valores vacíos de esta selección; no los conviertas en cero.
2. Conserva esta tabla como `DE_genes`.
3. Separa los genes según el cambio estimado:

| Grupo | Criterio, además de padj < 0.05 | Interpretación |
|---|---|---|
| Mayor expresión en MUT | log2FoldChange > 0 | Aumentados en MUT respecto de WT |
| Menor expresión en MUT | log2FoldChange < 0 | Disminuidos en MUT respecto de WT |

Puedes crear hojas llamadas `Aumentados_MUT` y `Disminuidos_MUT`. Si realizas el filtrado en Galaxy con una herramienta que utiliza `c1`, `c2`, etc., identifica primero qué columna corresponde a `padj` y cuál a `log2FoldChange` en **tu tabla actual**.

4. Cuenta los genes de cada grupo.
5. Ordena los aumentados por cambio log2 de mayor a menor y los disminuidos de menor a mayor.
6. Selecciona hasta **diez genes por grupo** para una primera exploración de funciones. Si hay menos de diez, utiliza los disponibles.

Revisa también la abundancia y la incertidumbre: un cambio grande a baja abundancia puede ser inestable. Estas listas describen los extremos del cambio estimado; no establecen por sí solas cuáles genes son los más importantes biológicamente.

## Paso 5. Explorar funciones de genes individuales con PANTHER

[Gene Ontology (GO)](https://geneontology.org/) organiza anotaciones en tres aspectos:

| Aspecto | Qué describe |
|---|---|
| Biological Process | Procesos biológicos en los que participa el producto del gen |
| Molecular Function | Actividades moleculares que realiza |
| Cellular Component | Localización o estructura celular asociada |

1. Ingresa a **[PANTHER](https://www.pantherdb.org/)**.
2. Copia los identificadores de los diez genes disminuidos, uno por línea, y pégalos en la entrada de lista de genes.
3. Selecciona **Danio rerio** como organismo.
4. Elige **Functional classification**, en formato de lista o gráficos, según las opciones disponibles, y envía la lista.
5. Revisa cuántos identificadores se reconocen y explora las funciones, familias y clases de proteínas asociadas.
6. Repite con los genes aumentados.

Puedes complementar la información consultando cada gen en Ensembl. Registra la fuente y distingue anotaciones respaldadas por evidencia experimental de aquellas inferidas por similitud.

**Clasificar funciones no es lo mismo que demostrar enriquecimiento.** Que una categoría aparezca entre diez genes no indica que esté sobrerrepresentada respecto de una referencia.

## Paso 6. Evaluar enriquecimiento funcional

Una prueba de sobrerrepresentación pregunta si una función aparece con mayor frecuencia en nuestra lista que lo esperado en un **universo de referencia**. Para este análisis utiliza **todos los genes significativos de cada grupo**, no solo los diez seleccionados para describir funciones.

### Preparar el universo de referencia

Para este práctico, utiliza los identificadores de los genes que tienen **padj válido** en la tabla completa. Representan los genes que pudieron seleccionarse con el criterio aplicado. El universo debe contener tanto genes significativos como no significativos.

Extrae una lista de identificadores únicos y guárdala como `Universo_DESeq2`. Las listas aumentadas y disminuidas deben estar incluidas en ese universo y usar el mismo tipo de identificador.

### Ejecutar la prueba en PANTHER

1. Introduce la lista completa de genes aumentados y selecciona **Danio rerio**.
2. Elige **Statistical overrepresentation test**.
3. Selecciona **GO Biological Process** como conjunto de anotaciones para comenzar.
4. En la selección de lista de referencia, habitualmente **Reference list / Change**, carga `Universo_DESeq2` como referencia personalizada.
5. Utiliza la prueba de **Fisher** y el ajuste **FDR** cuando estén disponibles. Registra las opciones efectivamente utilizadas.
6. Ejecuta, revisa los identificadores reconocidos en la lista y en el universo y descarga la tabla.
7. Repite con la lista completa de genes disminuidos, utilizando el mismo universo y configuración.

Consulta el [manual de PANTHER](https://pantherdb.org/help/PANTHER_user_manual.pdf) para reconocer las opciones de la prueba.

Interpreta términos con **FDR < 0.05** y revisa el número de genes, el enriquecimiento respecto de lo esperado y qué genes contribuyen. Varios términos GO pueden compartir genes y representar procesos relacionados, por lo que no deben tratarse automáticamente como descubrimientos independientes.

Si no aparecen términos significativos, informa ese resultado: puede relacionarse con el tamaño de la lista, la anotación o la profundidad del subconjunto. No cambies el fondo o el umbral únicamente para obtener significación.

## Paso 7. Explorar rutas con DAVID — actividad complementaria

**[DAVID](https://davidbioinformatics.nih.gov/)** permite explorar anotaciones funcionales y rutas asociadas a una lista de genes. Su interfaz puede variar; inicia desde **Functional Annotation**.

1. Carga una de tus listas completas de genes significativos.
2. Selecciona **Danio rerio** y el tipo de identificador correspondiente; para identificadores Ensembl de genes, utiliza **ENSEMBL_GENE_ID** si esa opción está disponible.
3. Define la lista como **Gene List** y carga `Universo_DESeq2` como **Background**.
4. Revisa el número de identificadores reconocidos y confirma que el fondo personalizado esté activo.
5. Explora **Functional Annotation Chart**, las categorías GO y las rutas disponibles, por ejemplo KEGG.
6. Descarga los resultados y registra qué columna de ajuste por comparaciones múltiples utilizas. Selecciona términos con valor ajustado menor que 0.05; no confundas el valor p sin ajustar con FDR.
7. Abre una ruta de interés, identifica los genes de tu lista representados y consulta sus funciones.

Una ruta enriquecida no está necesariamente activada o inhibida en su conjunto. Para proponer una interpretación, considera las funciones de sus genes, la dirección de sus cambios y cómo se relacionan dentro de la ruta.

Extensión opcional. GSEA con fgsea

A diferencia de la sobrerrepresentación, **GSEA** evalúa si los genes de un conjunto se concentran hacia alguno de los extremos de una lista ordenada. No utiliza únicamente genes significativos.

### Importar los archivos proporcionados para esta actividad

Utilizaremos los dos archivos proporcionados para el ejercicio: una **lista ordenada de genes con identificadores Entrez** y un archivo de **conjuntos de genes de MSigDB**. Son entradas preparadas para esta actividad; la lista ordenada no es una salida nueva de tu ejecución de DESeq2.

En **Upload Data**, presiona **Paste/Fetch data**, pega una URL por cuadro y reemplaza **New file** por el nombre indicado. Presiona nuevamente **Paste/Fetch data** para añadir el segundo archivo y luego **Start**.

**Nombre: `DE_Entrez_final`**  
**Tipo de dato:** `tabular`.

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b5a88abd1e79013e18/display?to_ext=tabular
```

**Nombre: `msigdb.v7.5.1.entrez.gmt`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b55590ea31a85fc760/display?to_ext=tabular
```

El segundo archivo contiene los conjuntos de genes y se proporciona mediante una URL de descarga tabular. Al importarlo, utiliza el tipo de dato admitido por el campo **Gene Sets** de fgsea; el nombre del archivo y la extensión solicitada en la URL no sustituyen revisar su formato.

### Ejecutar fgsea en Galaxy

1. Busca **fgsea** en el buscador de herramientas.
2. En **Ranked Genes**, selecciona `DE_Entrez_final`.
3. En **File has header**, selecciona **Yes** si la primera fila contiene nombres de columnas; si contiene directamente un identificador y su valor, selecciona **No**.
4. En **Gene Sets**, selecciona `msigdb.v7.5.1.entrez.gmt`.
5. Activa **Output Plots** y registra los demás parámetros utilizados.
6. Ejecuta y abre la tabla y los gráficos de enriquecimiento. Consulta el [tutorial de fgsea](https://bioconductor.org/packages/release/bioc/vignettes/fgsea/inst/doc/fgsea-tutorial.html) para interpretar las salidas.

Revisa `NES`, `padj`, tamaño del conjunto y los genes de la fracción principal (*leading edge*). Antes de interpretar el signo del NES, identifica qué métrica ordena la lista proporcionada y qué comparación representa. Si los valores positivos corresponden a mayor expresión en MUT, un NES positivo indica concentración hacia ese extremo y uno negativo hacia el extremo opuesto.

### Relacionar este ejercicio con tus resultados

Para realizar GSEA sobre **tu propia ejecución**, debes preparar un ranking con los genes de la tabla completa que tengan estadístico Wald (`stat`) finito, sin limitarlo a genes significativos ni convertir valores ausentes a cero. La lista debe contener identificadores únicos y conservar la dirección MUT/WT.

Los identificadores del ranking deben corresponder a los utilizados por los conjuntos de genes, incluida la especie o una conversión de ortólogos documentada. [MSigDB](https://www.gsea-msigdb.org/gsea/msigdb/index.jsp) ofrece colecciones humanas y murinas: **convertir identificadores Ensembl de pez cebra a Entrez de pez cebra no los convierte en genes humanos**. La procedencia y conversión de la lista proporcionada determinan qué conclusiones biológicas pueden extraerse de este ejercicio.


## Registro del análisis

Copia las tablas en tu documento de trabajo.

### Resumen de selección y anotación

| Dato | Resultado |
|---|---|
| Comparación | MUT frente a WT |
| Versión de Ensembl utilizada para nombres | |
| Genes en la tabla completa | |
| Genes con nombre recuperado | |
| Genes con padj válido: universo del análisis | |
| Genes con padj < 0.05 | |
| Genes con mayor expresión en MUT | |
| Genes con menor expresión en MUT | |

### Genes seleccionados para explorar funciones

Añade hasta diez filas por grupo.

| Grupo | Identificador | Nombre | log2FoldChange | padj | Función y fuente consultada |
|---|---|---|---|---|---|
| Mayor expresión en MUT | | | | | |
| Menor expresión en MUT | | | | | |

### Configuración del enriquecimiento

| Parámetro | Valor utilizado |
|---|---|
| Herramienta y versión o fecha de consulta | |
| Organismo | Danio rerio |
| Tipo de identificador | |
| Fuente y versión de anotaciones | |
| Definición del universo | Genes con padj válido |
| Identificadores del universo enviados / reconocidos | |
| Identificadores aumentados enviados / reconocidos | |
| Identificadores disminuidos enviados / reconocidos | |
| Prueba estadística | |
| Ajuste por comparaciones múltiples | |

### Resultados funcionales

Añade filas para los términos que discutirás. Si no hay enriquecimiento significativo, indícalo.

| Grupo | Término o ruta | Genes de la lista asociados | Valor ajustado | Interpretación |
|---|---|---|---|---|
| Mayor expresión en MUT | | | | |
| Menor expresión en MUT | | | | |

## Actividad final. Responder la pregunta biológica

En grupos de tres personas, preparen una presentación breve que responda:

**¿Cómo cambia la expresión génica en células endocrinas pancreáticas de pez cebra cuando se altera pax6b?**

Incluyan:

1. Un resumen del diseño experimental y de la calidad de los datos analizados.
2. El número de genes con mayor y menor expresión en MUT bajo el criterio padj < 0.05.
3. Ejemplos de genes de ambos grupos, con cambios, significación y funciones.
4. Los procesos o rutas enriquecidos y los genes que respaldan la interpretación; informen también si no hubo resultados significativos.
5. **Dos conclusiones biológicas y dos limitaciones** del análisis.

Distingan un resultado observado de una hipótesis explicativa. Los cambios pueden relacionarse con regulación génica o con diferencias en la composición de las células estudiadas. Este análisis no demuestra por sí solo que un gen sea una diana directa de Pax6b ni que la abundancia de su RNA equivalga a actividad de su proteína.

## Antes de finalizar

- [ ] Conservé la tabla completa y construí una versión anotada sin perder o duplicar genes silenciosamente.
- [ ] Separé los genes aumentados y disminuidos según MUT/WT y padj < 0.05.
- [ ] Exploré funciones y distinguí clasificación de enriquecimiento.
- [ ] Utilicé un universo explícito y registré los identificadores reconocidos.
- [ ] Conservé las tablas, gráficos y parámetros necesarios para respaldar mis conclusiones.
- [ ] Respondí la pregunta biológica considerando las limitaciones del subconjunto analizado.

Has completado el práctico de análisis de RNA-seq en Galaxy: desde las lecturas originales hasta la interpretación biológica de la expresión diferencial.

[Volver al inicio del práctico](../README.md)

