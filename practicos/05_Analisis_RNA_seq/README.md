# Práctico N°5: Análisis de RNA-seq en Galaxy
**Autora:** Estefanía Tarifeño-Saldivia, TarifenoLab.

En este práctico aprenderás a analizar datos de secuenciación de RNA (**RNA-seq**), desde el control de calidad de las lecturas hasta la interpretación biológica de los resultados. Utilizaremos **Galaxy**, una plataforma web que permite realizar análisis bioinformáticos mediante una interfaz gráfica, sin necesidad de programar.

## La pregunta biológica

**¿Cómo cambia la expresión génica en células endocrinas pancreáticas de pez cebra cuando se altera el gen *pax6b*?**

Para responder esta pregunta, analizaremos seis muestras de células endocrinas pancreáticas: tres obtenidas de animales de tipo silvestre (*wild-type*, **WT**) y tres de animales mutantes para *pax6b* (**MUT**). Cada condición cuenta con **tres réplicas biológicas**.

La secuenciación es pareada (*paired-end*): se leen ambos extremos de cada fragmento y las lecturas se guardan en dos archivos por muestra, **R1** y **R2**. Por lo tanto, trabajaremos con **seis muestras y doce archivos FASTQ**.

Los datos provienen del estudio [Lavergne, Tarifeño-Saldivia y colaboradores, publicado en BMC Biology (2020)](https://doi.org/10.1186/s12915-020-00840-1). Para facilitar el trabajo durante las sesiones prácticas, utilizaremos un subconjunto de las lecturas originales. Esto reduce el tiempo de análisis, aunque también limita la detección de genes y uniones de empalme; los resultados pueden diferir de los obtenidos con el conjunto completo.

## Recorrido del práctico

| Sección | Qué aprenderás | Resultados que obtendrás |
|---|---|---|
| [0. Cuenta y datos](docs/00-cuenta-y-datos.md) | Crear una cuenta en Galaxy, importar los datos e identificar las muestras y sus pares de lecturas | Un historial con los doce archivos FASTQ, el genoma de referencia y sus anotaciones |
| [1. Calidad y preprocesamiento](docs/01-calidad.md) | Evaluar la calidad con FastQC y procesar las lecturas con Trimmomatic | Lecturas procesadas e informes de calidad antes y después del recorte |
| [2. Alineamiento](docs/02-alineamiento.md) | Alinear las lecturas al genoma con HISAT2 y evaluar la calidad del alineamiento | Seis archivos BAM, estadísticas de alineamiento y perfiles de cobertura |
| [3. Conteo y expresión diferencial](docs/03-expresion-diferencial.md) | Obtener conteos por gen, normalizar y comparar MUT frente a WT con DESeq2 | Una tabla de expresión diferencial, conteos normalizados y gráficos para explorar las muestras |
| [4. Interpretación biológica](docs/04-interpretacion.md) | Incorporar nombres de genes, explorar sus funciones y analizar el enriquecimiento funcional | Una tabla anotada y conclusiones biológicas fundamentadas en los resultados |
