# Sección 0. Crear una cuenta en Galaxy y obtener los datos

[Inicio](../README.md) · [Siguiente: calidad y preprocesamiento](01-calidad.md)

En esta sección prepararás tu espacio de trabajo en Galaxy e importarás los datos que utilizaremos durante el práctico. Al finalizar, tendrás un historial con **doce archivos de lecturas y tres archivos de referencia**.

## Paso 1. Crear una cuenta en Galaxy

[Galaxy](https://usegalaxy.org/) es una plataforma web que permite analizar datos biológicos mediante una interfaz gráfica. Tu cuenta te permitirá guardar los datos, ejecutar herramientas y retomar el trabajo en las siguientes sesiones.

1. Ingresa a **[https://usegalaxy.org/](https://usegalaxy.org/)**.
2. Abre la opción de acceso **Login or Register** y selecciona el registro de una nueva cuenta, que puede aparecer como **Register Here**.
3. Completa el formulario con tu correo electrónico y los datos solicitados, y crea la cuenta.
4. Revisa tu correo y confirma el registro mediante el enlace de activación que recibirás. Si no encuentras el mensaje, revisa la carpeta de correo no deseado.
5. Inicia sesión en Galaxy.

Los nombres y la ubicación de algunos botones pueden variar. Durante el práctico mantendremos los nombres de las herramientas en inglés para que puedas encontrarlas en el buscador.

## Paso 2. Crear y nombrar tu historial

El **historial** (*History*) es el espacio donde Galaxy guarda los archivos que importas y los resultados de tus análisis. También conserva información sobre las herramientas y los parámetros utilizados.

1. En el panel del historial, crea un historial nuevo o utiliza uno vacío.
2. Cambia su nombre a `RNAseq_pax6b_Nombre_Apellido`, reemplazando el nombre y apellido por los tuyos.
3. Comprueba que este sea el historial activo antes de importar los archivos.

Usa este mismo historial durante las siguientes secciones. Renombrar los resultados a medida que avanzas te ayudará a reconocer qué muestra analizaste y qué herramienta utilizaste.

## Paso 3. Conocer los datos y el diseño experimental

Analizaremos células endocrinas pancreáticas de pez cebra (*Danio rerio*) obtenidas de animales de tipo silvestre (**WT**) y mutantes para el gen *pax6b* (**MUT**). Los datos proceden del estudio [Lavergne, Tarifeño-Saldivia y colaboradores, BMC Biology (2020)](https://doi.org/10.1186/s12915-020-00840-1).

Para el práctico utilizaremos un subconjunto de las lecturas originales, de modo que los análisis puedan realizarse en menos tiempo.

| Muestra | Condición | Réplica biológica | Archivos de lecturas |
|---|---|---|---|
| WT1 | Tipo silvestre | 1 | WT1_R1 y WT1_R2 |
| WT2 | Tipo silvestre | 2 | WT2_R1 y WT2_R2 |
| WT3 | Tipo silvestre | 3 | WT3_R1 y WT3_R2 |
| MUT1 | Mutante para pax6b | 1 | MUT1_R1 y MUT1_R2 |
| MUT2 | Mutante para pax6b | 2 | MUT2_R1 y MUT2_R2 |
| MUT3 | Mutante para pax6b | 3 | MUT3_R1 y MUT3_R2 |

La secuenciación es **pareada** (*paired-end*). Cada fragmento se lee desde ambos extremos: las primeras lecturas se guardan en **R1** y las segundas en **R2**. Ambos archivos pertenecen a la misma muestra y deben mantenerse asociados.

**En total: dos condiciones × tres réplicas × dos archivos = doce archivos FASTQ, correspondientes a seis muestras.**

Por ejemplo, `WT1_R1` se analizará junto con `WT1_R2`, nunca junto con `WT2_R2`.

## Paso 4. Importar las lecturas a Galaxy

Importaremos los archivos directamente desde sus direcciones. **No necesitas descargarlos a tu computador.** Las direcciones aparecen en bloques de texto para que puedas copiarlas y pegarlas en Galaxy.

**Añade un archivo por cuadro**, para asignarle su nombre antes de iniciar la importación:

1. Abre **Upload Data** en Galaxy y selecciona la pestaña **Regular**.
2. Presiona **Paste/Fetch data** para abrir un cuadro de importación.
3. Copia la URL completa del primer archivo que aparece más abajo y pégala en ese cuadro. Puedes usar el botón de copiar del bloque de texto, si aparece, o seleccionar la dirección y copiarla.
4. En el campo que muestra **New file** (o **Newfile**), reemplaza ese texto por el nombre indicado encima de la URL. Por ejemplo, escribe `WT1_R1`.
5. Selecciona `fastqsanger` como tipo de dato para las lecturas.
6. Presiona nuevamente **Paste/Fetch data** para abrir **un cuadro nuevo**. Pega allí la URL del siguiente archivo y escribe su nombre en el campo **New file**.
7. Repite el procedimiento hasta añadir los doce archivos, cada uno con su propia URL y nombre. **No pegues varias URLs en un mismo cuadro ni reemplaces la URL del archivo anterior.**
8. Revisa los nombres y presiona **Start** para iniciar la importación. Puedes cerrar la ventana con **Close** mientras Galaxy continúa trabajando.

### Lecturas de las muestras WT

**Nombre del archivo: `WT1_R1`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b59b48b4f8acc1afe6/display?to_ext=fastqsanger
```

**Nombre del archivo: `WT1_R2`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b56ffd26f53292268a/display?to_ext=fastqsanger
```

**Nombre del archivo: `WT2_R1`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b5dc135cfdc48d8089/display?to_ext=fastqsanger
```

**Nombre del archivo: `WT2_R2`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b56a8d627c4f6b68ab/display?to_ext=fastqsanger
```

**Nombre del archivo: `WT3_R1`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b581858f854fe86cd5/display?to_ext=fastqsanger
```

**Nombre del archivo: `WT3_R2`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b58fb3d9efb1be923e/display?to_ext=fastqsanger
```

### Lecturas de las muestras MUT

**Nombre del archivo: `MUT1_R1`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b5f83d87abc4a3b84c/display?to_ext=fastqsanger
```

**Nombre del archivo: `MUT1_R2`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b5a5f986baf67ac388/display?to_ext=fastqsanger
```

**Nombre del archivo: `MUT2_R1`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b5ff7f3c6c38ed670d/display?to_ext=fastqsanger
```

**Nombre del archivo: `MUT2_R2`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b56c392c8d8434b417/display?to_ext=fastqsanger
```

**Nombre del archivo: `MUT3_R1`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b59e7978168e44e2af/display?to_ext=fastqsanger
```

**Nombre del archivo: `MUT3_R2`**

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b53ac2bb3768dccef1/display?to_ext=fastqsanger
```

## Paso 5. Importar el genoma y las anotaciones

Además de las lecturas, utilizaremos tres archivos de referencia. Abre **Upload Data** y sigue el mismo procedimiento: presiona **Paste/Fetch data** para crear un cuadro por archivo, pega su URL y reemplaza **New file** por el nombre indicado. Selecciona el formato correspondiente y presiona **Start** cuando hayas añadido los tres.

**Nombre del archivo: `Zebrafish_Genome`**  
**Tipo de dato:** `fasta`. Contiene las secuencias del genoma de referencia que utilizaremos para el alineamiento.

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b5176d6ddf4983730f/display?to_ext=fasta
```

**Nombre del archivo: `Zebrafish_Annotation`**  
**Tipo de dato:** `gtf`. Describe la ubicación de genes y exones y se utilizará para el conteo.

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b5cf0d676e02c3abfa/display?to_ext=gtf
```

**Nombre del archivo: `Zebrafish_Annotation_bed`**  
**Tipo de dato:** `bed`. Proporciona el modelo génico que usaremos para evaluar la cobertura con RSeQC.

```text
https://usegalaxy.org/datasets/bbd44e69cb8906b5e26bf9236db9796c/display?to_ext=bed
```

## Paso 6. Explorar y organizar el historial

Al completar la importación, tendrás **quince datasets**: doce FASTQ y tres referencias.

1. Comprueba que todos hayan terminado de importarse. En Galaxy, los datasets completados correctamente suelen aparecer en **verde**.
2. Revisa los nombres y confirma que cada muestra tenga un archivo R1 y uno R2.
3. Abre los detalles de un dataset haciendo clic en su nombre. Identifica su formato y la información disponible sobre el archivo.
4. Usa la opción de visualización, habitualmente representada por un **ojo**, para explorar el contenido de `WT1_R1`.

Un archivo FASTQ contiene una entrada de cuatro líneas por lectura:

| Línea | Contenido |
|---|---|
| 1 | Identificador de la lectura, precedido por `@` |
| 2 | Secuencia de nucleótidos |
| 3 | Separador, que comienza con `+` |
| 4 | Caracteres que representan la calidad de cada base |

No es necesario interpretar todavía los caracteres de calidad: los evaluaremos con **FastQC** en la siguiente sección.

## Antes de continuar

- [ ] Mi historial tiene un nombre que permite identificarlo.
- [ ] Tengo los doce archivos FASTQ con los nombres de la tabla.
- [ ] Cada muestra tiene sus dos archivos: R1 y R2.
- [ ] Tengo el genoma FASTA y las anotaciones GTF y BED.
- [ ] Todos los archivos terminaron de importarse.

**Para discutir:** ¿cuántas muestras y cuántas réplicas biológicas por condición tenemos? ¿Por qué R1 y R2 no son réplicas independientes?

Ya puedes continuar con la [sección 1: calidad y preprocesamiento](01-calidad.md).
