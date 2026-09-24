# Práctico N°4: Exploración e integración de información genómica

## Introducción

Los proyectos de secuenciación han generado una enorme cantidad de información sobre genomas, genes, transcritos, proteínas y variantes genéticas. Sin embargo, estos datos no se encuentran aislados: están conectados mediante identificadores, coordenadas genómicas y distintos tipos de evidencia experimental y computacional.

En este práctico utilizaremos **Ensembl** como punto de entrada para explorar esa información. Ensembl es una plataforma que integra genomas de referencia, anotaciones génicas, transcritos, variantes, información comparativa y otros datos genómicos de múltiples especies.

Nuestro objetivo no será memorizar dónde se encuentra cada botón. Las plataformas bioinformáticas cambian constantemente y una ruta de navegación puede quedar obsoleta después de una actualización. En cambio, aprenderemos a reconocer **qué información necesitamos, cómo encontrarla, cómo registrar su procedencia y qué conclusiones podemos obtener a partir de ella**.

> [!IMPORTANT]
> Ensembl se encuentra en un proceso de actualización de su plataforma. Algunos nombres, secciones o funciones pueden cambiar. Si la interfaz que observas no coincide exactamente con este práctico, utiliza el objetivo biológico de cada actividad para localizar la información y registra cualquier diferencia relevante.

---

## Objetivos de aprendizaje

Al finalizar este práctico serás capaz de:

* Distinguir entre genoma, ensamblaje, anotación y release.
* Utilizar identificadores y coordenadas para localizar información genómica.
* Reconocer la estructura de un gen y comparar sus transcritos.
* Diferenciar secuencia genómica, cDNA, CDS y proteína.
* Explorar el contexto genómico de un gen de interés.
* Interpretar las consecuencias anotadas para una variante genética.
* Integrar información procedente de distintas bases de datos.
* Diferenciar asociación estadística, consecuencia molecular predicha y causalidad biológica.
* Registrar la procedencia y versión de los datos utilizados.

---

## Recursos que utilizaremos

* [Ensembl](https://www.ensembl.org/): genomas, genes, transcritos y variantes.
* [UniProt](https://www.uniprot.org/): secuencias y anotación funcional de proteínas.
* [Expression Atlas](https://www.ebi.ac.uk/gxa/home): expresión génica en tejidos y condiciones experimentales.
* [GWAS Catalog](https://www.ebi.ac.uk/gwas/): asociaciones entre variantes y rasgos o enfermedades.
* [dbSNP](https://www.ncbi.nlm.nih.gov/snp/): anotación funcional de variantes.
* [ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/): relaciones entre variantes y relevancia clínica.

No todas las preguntas requieren consultar todos los recursos. Parte del ejercicio consiste en decidir qué base de datos contiene la evidencia apropiada.

---

## Antes de comenzar: conceptos fundamentales

| Concepto       | ¿Qué representa?                                                                                                   |
| -------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Genoma**     | Conjunto de secuencias genéticas de un organismo.                                                                  |
| **Ensamblaje** | Reconstrucción computacional de las secuencias que componen un genoma.                                             |
| **Anotación**  | Identificación y descripción de genes, transcritos y otros elementos dentro de un ensamblaje.                      |
| **Release**    | Versión fechada de los datos y herramientas disponibles en una plataforma.                                         |
| **Gen**        | Región genómica a partir de la cual se produce uno o más productos funcionales.                                    |
| **Transcrito** | Molécula de RNA o modelo anotado derivado de un gen.                                                               |
| **CDS**        | Región codificante de un transcrito, desde el codón de inicio hasta el codón de término.                           |
| **UTR**        | Región transcrita que no forma parte de la secuencia codificante.                                                  |
| **Variante**   | Diferencia de secuencia respecto de un genoma de referencia.                                                       |
| **Biotipo**    | Clasificación de una entidad según su naturaleza o potencial funcional, por ejemplo, codificante o no codificante. |

### Pregunta inicial

Explica con tus propias palabras por qué un ensamblaje genómico y su anotación pueden actualizarse de manera independiente.

---

## Parte I. Reconociendo el origen de los datos

### Actividad 1. Selección del genoma

Ingresa a [Ensembl](https://www.ensembl.org/) y abre el **Genome Selector**. Busca el genoma humano y revisa la información disponible para el ensamblaje de referencia.

Registra la siguiente información:

| Elemento                           | Resultado |
| ---------------------------------- | --------- |
| Fecha de consulta                  |           |
| Release de Ensembl                 |           |
| Nombre científico de la especie    |           |
| Nombre del ensamblaje              |           |
| Accesión del ensamblaje            |           |
| Versión de la anotación            |           |
| Proveedor de la anotación          |           |
| Fecha de liberación del ensamblaje |           |

#### Analiza

1. ¿Por qué es necesario registrar la fecha de consulta y la release utilizada?
2. ¿Qué diferencia existe entre el nombre del ensamblaje y su accesión?
3. ¿Por qué dos análisis realizados en diferentes versiones de un genoma podrían producir resultados distintos?

### Actividad 2. Diversidad de genomas disponibles

Utiliza el Genome Selector para identificar tres animales domésticos o de interés productivo que tengan un genoma disponible en Ensembl.

| Nombre común | Nombre científico | Ensamblaje | Accesión |
| ------------ | ----------------- | ---------- | -------- |
|              |                   |            |          |
|              |                   |            |          |
|              |                   |            |          |

Compara los tres registros y responde:

1. ¿Todos los genomas presentan el mismo nivel o tipo de anotación?
2. ¿Qué información utilizarías para evaluar si un ensamblaje es apropiado para un análisis genómico?

---

## Parte II. Del gen a sus productos

En esta sección investigaremos **BRCA2**, un gen asociado con reparación del DNA y predisposición hereditaria a distintos tipos de cáncer.

### Actividad 3. Identificación del gen BRCA2

Busca `BRCA2` en el genoma humano y abre su registro en **Feature Explorer**. Asegúrate de seleccionar el gen humano y no un transcrito, una proteína o un ortólogo de otra especie.

Completa la siguiente ficha:

| Característica                           | Resultado |
| ---------------------------------------- | --------- |
| Símbolo oficial                          |           |
| Nombre del gen                           |           |
| Ensembl gene ID                          |           |
| Versión del identificador, si se informa |           |
| Cromosoma                                |           |
| Coordenadas genómicas                    |           |
| Hebra                                    |           |
| Biotipo                                  |           |
| Fuente o método de anotación             |           |

#### Identificadores estables y versiones

Un identificador de Ensembl permite seguir una entidad entre releases. Sin embargo, el modelo biológico asociado puede cambiar. Cuando un identificador incluye un número después de un punto —por ejemplo, `ENSGXXXXXXXXXXX.5`— ese número representa la **versión del modelo**.

Responde:

1. ¿Qué parte del identificador corresponde al ID estable?
2. ¿Qué podría provocar un cambio en su número de versión?
3. ¿Por qué deberías conservar el identificador completo al documentar un análisis?

### Actividad 4. Estructura del gen

Examina la representación de BRCA2 y sus transcritos.

1. ¿Cómo puedes reconocer exones e intrones en la representación?
2. ¿Cómo se diferencia una región codificante de una UTR?
3. ¿Cómo influye la hebra del gen en la dirección en que debe interpretarse el modelo?
4. ¿Todos los transcritos poseen la misma estructura? Describe al menos dos diferencias observables.

> [!NOTE]
> El número y la estructura de los transcritos pueden cambiar entre releases. Registra lo que observas en la versión consultada; no busques reproducir un número histórico.

---

## Parte III. Comparando isoformas

### Actividad 5. Selección de transcritos

Identifica el transcrito principal, canónico o recomendado para BRCA2 según la información disponible. Luego selecciona un transcrito alternativo que presente diferencias estructurales claras.

Registra sus identificadores completos y compáralos:

| Característica                    | Transcrito principal | Transcrito alternativo |
| --------------------------------- | -------------------- | ---------------------- |
| Ensembl transcript ID             |                      |                        |
| Versión                           |                      |                        |
| Biotipo                           |                      |                        |
| Número de exones                  |                      |                        |
| Longitud del transcrito           |                      |                        |
| Presencia de CDS                  |                      |                        |
| Longitud de la CDS                |                      |                        |
| Longitud de la proteína           |                      |                        |
| Evidencia o criterio de selección |                      |                        |

#### Interpreta

1. ¿Qué evento o diferencia de procesamiento podría explicar las estructuras observadas?
2. ¿Un transcrito anotado implica necesariamente que su producto proteico haya sido detectado experimentalmente?
3. ¿Por qué una variante puede recibir consecuencias diferentes según el transcrito considerado?
4. ¿Qué evidencia utilizarías para escoger un transcrito representativo en un análisis funcional?

---

## Parte IV. Contexto genómico

### Actividad 6. Navegación por la región de BRCA2

Abre BRCA2 en el **Genome Browser** y explora la región genómica. Ajusta el nivel de zoom hasta observar el gen y su vecindad inmediata.

Identifica:

* El gen localizado inmediatamente corriente arriba de BRCA2.
* El gen localizado inmediatamente corriente abajo de BRCA2.
* La orientación de cada gen.
* La presencia de genes codificantes y/o no codificantes en la región.
* Las capas de información o *tracks* disponibles en la versión actual del navegador.

| Elemento             | Identificador o nombre | Coordenadas | Hebra/Orientación |
| -------------------- | ---------------------- | ----------- | ----------------- |
| BRCA2                |                        |             |                   |
| Gen corriente arriba |                        |             |                   |
| Gen corriente abajo  |                        |             |                   |

Adjunta **un pantallazo anotado** de la región. La imagen debe indicar claramente:

* BRCA2.
* Los dos genes vecinos seleccionados.
* La dirección de transcripción.
* El ensamblaje utilizado.

#### Analiza

1. ¿Qué significa que dos genes sean vecinos en el genoma?
2. ¿La proximidad física permite concluir que ambos genes están corregulados? Fundamenta.
3. ¿Qué información adicional necesitarías para proponer una relación regulatoria?

---

## Parte V. Recuperación de secuencias

### Actividad 7. Descarga del transcrito principal

Desde el registro del transcrito principal de BRCA2, descarga cuando estén disponibles:

* La secuencia de cDNA.
* La secuencia codificante o CDS.
* La secuencia de proteína.

Guarda los archivos utilizando nombres informativos. Por ejemplo:

```text
BRCA2_<transcript_ID>_cDNA.fasta
BRCA2_<transcript_ID>_CDS.fasta
BRCA2_<transcript_ID>_protein.fasta
```

### Registro y verificación de las longitudes

Para cada secuencia, registra la longitud informada en Ensembl. La longitud de la proteína también puede comprobarse en el registro correspondiente de UniProt.

Si la longitud no aparece explícitamente o deseas verificarla de manera independiente:

1. Ingresa a [EMBOSS infoseq](https://www.bioinformatics.nl/cgi-bin/emboss/infoseq).
2. Abre el archivo FASTA descargado o copia su contenido.
3. Pega la secuencia en el campo de entrada o carga el archivo.
4. Ejecuta el análisis.
5. Registra la longitud informada.

Al copiar una secuencia, asegúrate de incluir solamente un registro FASTA y comprueba que el encabezado comience con `>`.

| Archivo | Tipo de secuencia | Longitud | Fuente de la longitud | Identificador |
|---|---|---:|---|---|
| | cDNA | | Ensembl/infoseq | |
| | CDS | | Ensembl/infoseq | |
| | Proteína | | Ensembl/UniProt/infoseq | |

#### Comprueba la relación entre la CDS y la proteína

Utiliza las longitudes registradas para evaluar si se cumple la relación esperada:

Longitud de la CDS = (número de aminoácidos) + 3


Los tres nucleótidos adicionales corresponden al codón de término, que forma parte de una CDS completa, pero no codifica un aminoácido.

Responde:

1. ¿La relación se cumple para el transcrito analizado?
2. ¿La CDS descargada incluye el codón de término?
3. Si las longitudes no son consistentes, ¿estás comparando el mismo transcrito y la misma versión?

---

## Parte VI. Integración de bases de datos

Una plataforma no contiene toda la evidencia necesaria para interpretar un gen. Ahora conectaremos el registro de BRCA2 con recursos especializados.

### Actividad 8. De Ensembl a UniProt

Identifica el registro de UniProt correspondiente a la proteína principal codificada por BRCA2.

| Elemento                                   | Resultado |
| ------------------------------------------ | --------- |
| UniProt accession                          |           |
| Nombre de la proteína                      |           |
| Longitud                                   |           |
| Estado de revisión                         |           |
| Dominios o regiones funcionales destacadas |           |
| Evidencia experimental disponible          |           |

Responde:

1. ¿Coinciden la longitud y la secuencia de la proteína de UniProt con las del transcrito seleccionado en Ensembl?
2. Si existen diferencias, ¿qué explicaciones biológicas o técnicas podrían justificarlas?
3. ¿Qué información funcional aporta UniProt que no estaba disponible en el registro genómico?

### Actividad 9. Expresión génica

Busca `BRCA2` en Expression Atlas y selecciona un conjunto de datos apropiado para examinar su expresión basal en tejidos humanos.

Registra:

* Nombre y procedencia del conjunto de datos.
* Tecnología utilizada para medir expresión.
* Unidad o escala de expresión.
* Tres tejidos con expresión relativamente alta.
* Tres tejidos con expresión baja o no detectada.

#### Analiza

1. ¿La ausencia de señal permite afirmar que un gen no se expresa en un tejido?
2. ¿Qué factores técnicos y biológicos pueden influir en la expresión observada?
3. ¿Por qué no deben compararse directamente valores procedentes de tecnologías o estudios diferentes sin una normalización apropiada?

---

## Parte VII. Del genotipo al fenotipo

### Contexto del caso

La obesidad y la diabetes tipo 2 son rasgos complejos. Su desarrollo depende de la interacción entre múltiples variantes genéticas, factores ambientales, estado metabólico y estilo de vida. Una asociación entre una variante y una enfermedad no demuestra por sí sola que la variante sea causal ni identifica necesariamente el gen mediante el cual actúa.

Analizaremos tres variantes relacionadas con fenotipos metabólicos:

* `rs1801282`
* `rs9939609`
* `rs7903146`

### Actividad 10. Anotación de variantes

Busca cada variante en Ensembl y complementa la búsqueda con GWAS Catalog. Utilizando el link "Genes and regulation" puedes navegar con el Genome Browser y conocer el contexto genético de cada variación. Explora dbSNP y utiliza ClinVar solamente cuando exista un registro pertinente.

Completa la tabla:

| Evidencia                                        | rs1801282 | rs9939609 | rs7903146 |
| ------------------------------------------------ | --------- | --------- | --------- |
| Cromosoma y posición                             |           |           |           |
| Alelo de referencia/alternativo                  |           |           |           |
| Clase de variante                                |           |           |           |
| Gen más cercano                                  |           |           |           |
| Ubicación respecto del gen                       |           |           |           |
| Consecuencia más relevante anotada               |           |           |           |
| ¿La consecuencia cambia entre transcritos?       |           |           |           |
| Frecuencia alélica en una población seleccionada |           |           |           |
| Rasgo o enfermedad asociada                      |           |           |           |
| Base de datos que respalda la asociación         |           |           |           |
| Evidencia regulatoria o funcional disponible     |           |           |           |

> [!CAUTION]
> No utilices como sinónimos **gen más cercano**, **gen afectado** y **gen causal**. Si la evidencia no permite identificar un gen diana, indícalo explícitamente.

#### Evalúa la evidencia

Para cada variante responde:

1. ¿La variante modifica directamente una secuencia codificante?
2. ¿La consecuencia molecular está observada experimentalmente o es una predicción?
3. ¿La asociación proviene de un estudio poblacional, de evidencia clínica o de un experimento funcional?
4. ¿Existe evidencia suficiente para proponer un mecanismo?
5. ¿Qué información adicional necesitarías para evaluar causalidad?

---

## Parte VIII. Desafío integrador

Selecciona una de las tres variantes y construye una explicación que conecte los distintos niveles de información:

```text
Variante → contexto genómico → consecuencia molecular → gen o proceso candidato → fenotipo
```

Tu explicación debe incluir:

* La evidencia que respalda cada conexión.
* La base de datos o publicación desde la cual obtuviste la información.
* Una separación clara entre resultados observados, predicciones e hipótesis.
* Al menos dos limitaciones de la interpretación.
* Una propuesta experimental que permita evaluar el mecanismo planteado.

La propuesta experimental no necesita describir un protocolo completo, pero debe indicar:

* La pregunta que se quiere responder.
* El modelo o tipo de muestra.
* La variable que se manipularía.
* La respuesta que se mediría.
* El resultado esperado si la hipótesis fuera correcta.

---

## Entregables

Entrega un informe en formato PDF que contenga:

1. Las tablas completadas.
2. Las respuestas fundamentadas.
3. El pantallazo anotado de la región genómica.
4. Los identificadores y versiones de los registros consultados.
5. La fecha de consulta de cada plataforma.
6. La interpretación del desafío integrador.
7. Las referencias utilizadas.

Además, adjunta por separado los tres archivos FASTA descargados en la Actividad 7.

No se evaluará la coincidencia literal con un número predefinido de genes, transcritos o variantes. Se evaluará que el resultado sea correcto para la versión consultada, que su procedencia esté documentada y que la interpretación sea coherente con la evidencia.

---

## Criterios de evaluación

| Criterio                                                            | Ponderación |
| ------------------------------------------------------------------- | ----------: |
| Registro de ensamblaje, release, identificadores y procedencia      |        15 % |
| Interpretación de la estructura génica y comparación de transcritos |        20 % |
| Navegación genómica y recuperación de secuencias                    |        15 % |
| Integración de bases de datos                                       |        20 % |
| Interpretación crítica de variantes                                 |        20 % |
| Claridad, fundamentación y presentación                             |        10 % |

---

## Consideraciones finales

Las bases de datos biológicas no son catálogos estáticos. Sus registros dependen de nuevos ensamblajes, cambios de anotación, evidencia experimental y decisiones curatoriales. Por ello, una respuesta bioinformática debe indicar no solo **qué se encontró**, sino también **dónde, cuándo y bajo qué versión se encontró**.

La navegación de un genoma es el punto de partida. El verdadero desafío consiste en integrar distintos niveles de evidencia sin confundir una anotación con una demostración experimental ni una asociación estadística con un mecanismo causal.

---

## Recursos y documentación

* [Ensembl](https://www.ensembl.org/)
* [Introducción al nuevo Ensembl Genome Browser - EMBL-EBI Training](https://www.ebi.ac.uk/training/events/introducing-new-ensembl-genome-browser/)
* [Comparación de funciones entre la plataforma nueva y la plataforma anterior de Ensembl](https://www.ensembl.info/2026/07/21/feature-profile-between-new-and-legacy-ensembl-july-2026-update/)
* [UniProt](https://www.uniprot.org/)
* [Expression Atlas](https://www.ebi.ac.uk/gxa/home)
* [GWAS Catalog](https://www.ebi.ac.uk/gwas/)
* [dbSNP](https://www.ncbi.nlm.nih.gov/snp/)
* [ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/)

---

[Volver al índice de prácticos](../README.md) · [Volver a la página principal](../../README.md)
