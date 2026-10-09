# Análisis de RNA-seq en Galaxy — práctico de pregrado

**Autora del material original:** Estefanía Tarifeño-Saldivia, TarifenoLab.  
**Revisión editorial y metodológica:** 7 de octubre de 2026.  

## La pregunta biológica

¿Cómo cambia la expresión génica en células endocrinas pancreáticas de pez cebra cuando se altera **pax6b**? Trabajaremos con seis muestras: tres de tipo silvestre (WT) y tres mutantes (MUT). Cada muestra tiene dos archivos de lecturas pareadas, R1 y R2. Son **seis réplicas biológicas, no doce**.

El material original utiliza un subconjunto de lecturas del estudio [Lavergne, Tarifeño-Saldivia y colaboradores, BMC Biology (2020)](https://doi.org/10.1186/s12915-020-00840-1). Reducir los datos facilita la docencia, pero limita la detección de genes y uniones de empalme. No esperamos reproducir exactamente las cifras del artículo.

## Recorrido

| Sección | Qué aprenderás | Resultado que conservarás |
|---|---|---|
| [0. Cuenta y datos](docs/00-cuenta-y-datos.md) | Importar datos y reconocer muestras y pares | 12 FASTQ y referencias compatibles |
| [1. Calidad y preprocesamiento](docs/01-calidad.md) | Interpretar FastQC y recortar con criterio | Lecturas pareadas procesadas y comparación de calidad |
| [2. Alineamiento](docs/02-alineamiento.md) | Alinear con HISAT2 y evaluar cobertura | Seis BAM y estadísticas de alineamiento |
| [3. Conteo y expresión diferencial](docs/03-expresion-diferencial.md) | Contar fragmentos y contrastar MUT frente a WT | Conteos, tabla DESeq2 y gráficos |
| [4. Interpretación biológica](docs/04-interpretacion.md) | Anotar genes y evaluar enriquecimiento | Tabla final y conclusiones justificadas |

Puedes leer todo en GitHub; no necesitas un sitio adicional para seguir el práctico. Los nombres de las herramientas se mantienen en inglés para encontrarlas en Galaxy. Busca por nombre en el buscador de herramientas: las categorías y botones cambian entre versiones.

## Antes de empezar

- Una cuenta en [Galaxy US](https://usegalaxy.org/), correo verificable y navegador actualizado.
- Conocimientos básicos de genes, transcritos, exones, réplicas y pruebas estadísticas.
- Una hoja de registro: usa [esta plantilla](data/registro-analisis.tsv).
- La persona docente debe completar la [preparación y validación](docs/guia-docente.md). No se ha ejecutado todavía un análisis completo con esta revisión.

El curso no requiere programar. Conserva el historial, los parámetros y las versiones de cada herramienta. Un trabajo en cola no equivale a un error: revisa el estado y el mensaje del dataset antes de repetirlo.

## Datos y reproducibilidad

El [manifiesto](data/datasets.tsv) conserva los enlaces originales y distingue datos esenciales de recursos antiguos opcionales. No alojamos FASTQ ni genomas dentro de Git. Los enlaces antiguos no constituyen un depósito permanente: antes de impartir la clase deben verificarse, y conviene depositar el conjunto docente en un repositorio de datos con identificador persistente y sumas SHA-256.

La revisión elimina resultados numéricos presentados como si fueran universales. El alumnado debe interpretar sus propios gráficos, documentar el contraste **MUT/WT** y discutir las limitaciones.

## Publicación

Consulta [cómo publicar](docs/publicacion.md). Esta carpeta puede incorporarse al repositorio original como versión española, conservando la wiki histórica. No se asigna una licencia nueva sin decisión de la autora; los datos y recursos externos conservan sus propios términos.

## Referencias

- [Galaxy Training Network: RNA-seq con referencia](https://training.galaxyproject.org/topics/transcriptomics/tutorials/ref-based/tutorial.html).
- [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/), [Trimmomatic](https://github.com/usadellab/Trimmomatic), [HISAT2](https://daehwankimlab.github.io/hisat2/manual/).
- [HTSeq](https://htseq.readthedocs.io/en/latest/htseqcount.html), [DESeq2](https://bioconductor.org/packages/release/bioc/vignettes/DESeq2/inst/doc/DESeq2.html), [RSeQC](https://rseqc.sourceforge.net/).
- [Ensembl BioMart](https://www.ensembl.org/biomart/martview), [PANTHER](https://www.pantherdb.org/), [Gene Ontology](https://geneontology.org/), [fgsea](https://bioconductor.org/packages/fgsea/).
