# ISE 22/23 - Tema 4: Análisis comparativo del rendimiento

###### ¿Qué servidor tiene mejor rendimiento?

> **Objetivos:**
>
> - Entender la problemática inherente al diseño de un índice de rendimiento cualquiera.
> - Interpretar los índices clásicos de rendimiento usados en el ámbito de los procesadores.
> - Entender el concepto de benchmark y sus distintos tipos.
> - Conocer ejemplos reales de benchmarks
> - Conocer diferentes estrategias de análisis para hacer comparaciones de rendimiento así como las condiciones para hacer una comparación de rendimiento lo más ecuánime posible.

### Características de un buen índice de rendimiento de un sistema informático

- Repetibilidad: Siempre que se mida el índice en las mismas condiciones, el valor de éste debe ser el mismo. 
- Representatividad y fiabilidad: Si un sistema A siempre presenta un índice de rendimiento mejor que el sistema B, es porque siempre el rendimiento real de A es mejor que el de B. 
- Consistencia: El índice se debe poder medir en cualquier sistema informático, independientemente de su arquitectura o de su S.O. 
- Facilidad de medición: Sería deseable que las medidas necesarias para obtener el índice sean fáciles de tomar. 
- Linealidad: Sería deseable que, si el índice de rendimiento aumenta, el rendimiento real del sistema aumente en la misma proporción.

### Algunos índices de rendimiento: ¿valen la pena?

#### Tiempo de ejecución, frecuencia de reloj y CPI

¿Pueden ser la frecuencia de reloj (fRELOJ) o el número medio de ciclos por instrucción (CPI) buenos índices de rendimiento?

![image-20230116021238413](/home/clara/.config/Typora/typora-user-images/image-20230116021238413.png)

No lo son. Es posible encontrar ejemplos de sistemas con fRELOJ (o CPI) peores que otros, pero con mejores prestaciones. Además, CPI depende del programa que se ejecute. 

¿Y si usamos directamente el tiempo de ejecución (TEJEC) de un determinado programa? 

- ¿Consistencia? El programa debería estar descrito en un lenguaje de alto nivel. Ok. 
- ¿Repetibilidad? El programa debería ejecutarse en un entorno muy controlado. Ok. 
- ¿Representatividad y fiabilidad? ¡¡¡Dependería del programa a ejecutar!!!

#### MIPS (millones de instrucciones por segundo)

En principio, parece una medida prometedora ya que representa cómo de rápido ejecuta las instrucciones un microprocesador.

![image-20230116021341639](/home/clara/.config/Typora/typora-user-images/image-20230116021341639.png)

Inconvenientes: 

- ¿Repetibilidad? Los MIPS medidos varían incluso entre diferentes programas en el mismo computador. 
- ¿Representatividad y fiabilidad? Depende del juego de instrucciones (ej. RISC vs CISC).

#### MFLOPS (millones de operaciones en coma flotante por segundo)

Basado en operaciones y no en instrucciones.

![image-20230116021415600](/home/clara/.config/Typora/typora-user-images/image-20230116021415600.png)

Inconvenientes:

- No todas las operaciones de coma flotante tienen la misma complejidad. 

  - Posible solución: MFLOPS normalizados: Cada operación se multiplica por un peso que es proporcional a su complejidad. 

    Ejemplo de asignación de pesos: 

    - ADD, SUB, COMPARE, MULT ⇒ 1 operación normalizada 
    - DIVIDE, SQRT ⇒ 4 operaciones normalizadas 
    - EXP, SIN, ATAN, ... ⇒ 8 operaciones normalizadas 

- ¿Consistencia? El formato de los números en coma flotante puede variar de una arquitectura a otra y, por tanto, los resultados de las operaciones podrían tener diferente exactitud. 

- Además, ¿y si no son importantes las operaciones en coma flotante en mi servidor?

En conclusión: no nos vale, ni tampoco ninguno de los índices anteriores, no quedan candidatos. Nos contentaremos con el tiempo de ejecución (TEJEC) de un determinado programa o conjunto de programas 

El índice de rendimiento va a depender de la carga que se use para hacer la comparación.

### Carga real

Difícil de utilizar para la comparación de rendimiento de un servidor por las siguientes razones: 

- Varía a lo largo del tiempo: ¿Uso la carga de hoy, la de ayer…? 
- Resulta complicado reproducirla: Si la uso con un servidor muy lento, ¡¡es posible que llegue una solicitud basada en una respuesta que todavía no ha llegado!! 
- Interacciona con el sistema informático: Si un servidor contesta muy rápido, es posible que reciba las solicitudes antes o incluso más solicitudes que si fuera muy lento.

![image-20230116021829845](/home/clara/.config/Typora/typora-user-images/image-20230116021829845.png)

Es más conveniente utilizar un modelo de la carga real como carga de prueba (test workload) para hacer comparaciones.

### Modelo de carga

#### Representatividad

Los modelos de carga son representaciones aproximadas de la carga que recibe un sistema informático. El modelo de la carga: 

- Debe ser lo más representativo posible de la carga real. 
- Debe ser lo más simple/compacto que sea posible (tiempos de medición y espacio en memoria razonables).

![image-20230116021903220](/home/clara/.config/Typora/typora-user-images/image-20230116021903220.png)

#### Estrategias para obtener modelos de carga

- Ajustar un modelo paramétrico “personalizado” a partir de la monitorización del sistema ante la carga real **(caracterización de la carga).**

  La forma más fácil para obtener un modelo de la carga a la que está sometido un servidor durante un determinado periodo de tiempo consiste en: 

  - Identificar los recursos que más demande la carga (CPU, memoria, discos, red, etc.) 
  - Elegir los parámetros característicos de dichos recursos (utilización de CPU, lecturas/escrituras que hay que hacer en cada disco, lecturas/escrituras a memoria, número de accesos a la red, etc.) 
  - Medir el valor de dichos parámetros usando monitores de actividad (muestreo). 
  - Analizar los datos: medias, histogramas, agrupamiento o clustering, etc. 
  - Generar el modelo de carga seleccionando representantes de la carga (=solicitudes al servidor) junto con información estadística sobre su distribución temporal.

  Ventajas:

  - La carga de prueba es muy representativa de la carga real.

  Desventajas:

  - El proceso de obtención de la carga de prueba es bastante tedioso. 
  - Desconocemos el rendimiento de otros servidores distintos al nuestro con esa carga de prueba.

- Usar cargas de prueba que usen un modelo genérico de carga lo más similar posible al que se quiere reproducir **(referenciación o benchmarking).**

## 4.1. Referenciación (benchmarking)

Consiste en utilizar un programa o un conjunto de programas (benchmark programs) estándar con el fin de comparar alguna característica del rendimiento (también llamada “métrica”) entre equipos informáticos. Hay dos características principales que definen a un benchmark:

- La carga de prueba (test workload) específica con la que estresa el sistema evaluado. 
- El conjunto de reglas que se deben seguir para la correcta ejecución, obtención y validación de los resultados.

Algunas ventajas de usar benchmarking:

- Existen muchos benchmarks diferentes para distintos tipos de servidores y cargas. Hay una alta probabilidad de encontrar uno que reproduzca unas condiciones parecidas a las que experimenta nuestro servidor. 
- Las comparaciones entre el rendimiento de varios servidores son justas ya que todas las ejecuciones se realizan de forma idéntica siguiendo las reglas del benchmark. 
- Muchos benchmarks permiten ajustar la carga de tal forma que podemos medir la escalabilidad de nuestro servidor.
- Al poder conocer tanto el rendimiento para un determinado benchmark que obtienen diferentes servidores como cómo están diseñados y configurados dichos servidores, obtenemos una información muy valiosa sobre cómo diseñar y/o configurar nuestros propios servidores.

### Tipos de programas benchmark

#### Según la estrategia de medida

- Programas que miden el tiempo necesario para ejecutar una cantidad preestablecida de tareas. 
  - La mayoría de benchmarks. 
- Programas que miden la cantidad de tareas ejecutadas para un tiempo de cómputo pre-establecido. 
  - SLALOM: Mide la exactitud de la solución de un determinado problema que se puede alcanzar en 1 minuto de ejecución. 
  - TPC-C: Calcula cuántas consultas por segundo se realizan, de media, a un servidor de base de datos permitiendo aumentar tanto el nº de usuarios como el tamaño de la base de datos. Exige un tiempo mínimo de respuesta para un determinado tanto por ciento de peticiones.

#### Según la generalidad del test

- Microbenchmarks o benchmarks para componentes: estresan componentes o agrupaciones de componentes concretos del sistema: procesador, caché, memoria, discos, red, procesador+caché, procesador+compilador+memoria virtual, etc. 
- Macrobenchmarks o benchmarks de sistema completo o de aplicación real: la carga intenta imitar situaciones reales (normalmente servidores con muchos clientes) típicas de algún área. P.ej. comercio electrónico, servidores web, servidores de ficheros, servidores de bases de datos, sistemas de ayuda a la decisión, paquetes ofimáticos + correo electrónico + navegación, etc.

##### Ejemplos de microbenchmarks

- Whetstone (1976) - Mide el rendimiento de las operaciones en coma flotante por medio de pequeñas aplicaciones científicas que usan sumas, multiplicaciones y funciones trigonométricas. 
- Linpack (1983) - Mide el rendimiento de las operaciones en coma flotante a través de un algoritmo para resolver un sistema denso de ecuaciones lineales. El benchmark incorpora una rutina para comprobar que la solución a la que se llega es la correcta con un grado de exactitud prefijado. Se utiliza para confeccionar la lista de los 500 mejores supercomputadores del mundo. 
- Dhrystone (1984) - Mide el rendimiento de operaciones con enteros, esencialmente por medio de operaciones de copia y comparación de cadenas de caracteres.
- Stream: para medir el ancho de banda de las memoria DRAM y las cachés del microprocesador (SRAM).
- IOzone: rendimiento del sistema de ficheros (p.ej. lecturas y escrituras a/desde el disco duro). Igualmente HD Tune (Windows), Iometer, fio (Linux) o el programa ‘hdparm –tT’ (Linux). 
- Iperf: rendimiento TCP y UDP (Linux y Windows). Debe estar instalado tanto en el cliente como en el servidor. O el programa pchar (Linux), calcula el ancho de banda por cada salto de IP hasta alcanzar el destino. 
- También existen aplicaciones que incorporan varios paquetes de microbenchmarks para poder realizar diversos tests de forma cómoda:
  - AIDA64 (Windows) 
  - Sandra (Windows)
  - Phoronix Test Suite (Multiplataforma, código abierto)
    - Permite la instalación, ejecución y la generación de informes tanto de tests de rendimiento (test profiles) como de agrupaciones de éstos (test suites) de forma automatizada. 
    - Permite recopilar automáticamente en tiempo de ejecución la información de sensores del sistema (consumo de energía, temperaturas, voltajes, frecuencias CPU, velocidad ventiladores, utilización de CPU/GPU/discos/memoria, etc.) 
    - Permite visualizar el resultado de forma gráfica en html y/o exportarlo a https://openbenchmarking.org para compartirlo con otros y/o compararlo con otros resultados.

###### El paquete de microbenchmarks SPEC CPU 2017

SPEC (Standard Performance Evaluation Corporation), es una corporación sin ánimo de lucro cuyo propósito es establecer, mantener y respaldar la estandarización de benchmarks y herramientas para evaluar el rendimiento y la eficiencia energética de los equipos informáticos. 

Algunos miembros de la corporación: Acer, AMD, Amazon Web Services, Apple,  Google, HP, IBM, Intel, Microsoft, Oracle...

Compuesto por cuatro conjuntos de microbenchmarks distintos:

- Speed: Cuánto tarda en ejecutarse un programa (tiempo de respuesta)
  - SPECspeed®2017 Integer (rendimiento en aritmética entera) 
  - SPECspeed®2017 Floating Point (rendimiento en coma flotante) 
- Rate: Cuántos programas puedo ejecutar por unidad de tiempo (productividad)
  - SPECrate®2017 Integer (rendimiento en aritmética entera) 
  - SPECrate®2017 Floating Point (rendimiento en coma flotante)

SPEC CPU2017 se distribuye como una imagen ISO que contiene: 

- Código fuente de todos los programas de benchmark. 
- Data sets que necesitan algunos benchmarks para su ejecución. 
- Herramientas varias para compilación, ejecución, obtención de resultados, validación y generación de informes. 
- Documentación, incluyendo reglas de ejecución y de generación de informes. 

El tiempo de ejecución depende del índice a obtener, la máquina en la que se ejecuta y cuántas copias o subprocesos se eligen.

Los programas dentro del benchmark SPECspeed 2017 deben ser aplicaciones reales portables a múltiples arquitecturas. Algunos son:

- SPECspeed®2017 Integer - 10 programas, mayoría en C/C++
  - Intérprete de Perl 
  - Utilidad de compresión 
  - Compilador de C 
  - Conversión XML a HTML
- SPECspeed®2017 Floating Point - 10 programas, la mayoría en Fortran/C
  - Dinámica de fluidos 
  - Predicción meteorológica 
  - Procesamiento de imágenes

Los índices de prestaciones de SPEC, también llamados genéricamente, índices SPEC son: 

- Aritmética entera: CPU2017IntegerSpeed_peak, CPU2017IntegerSpeed_base. 
- Aritmética en coma flotante: CPU2017FP_Speed_peak, CPU2017FP_Speed_base.

El significado de base y peak en los índices SPEC:

- Base: Compilación en modo conservador: todos los programas escritos en el mismo lenguaje usan las mismas opciones de compilación. 
- Peak: Rendimiento pico, permitiendo que cada uno escoja las opciones de compilación óptimas para cada programa.

Cada programa del benchmark se ejecuta 3 veces y se escoge el resultado intermedio (se descartan los 2 extremos). El índice SPEC es la media geométrica de las ganancias en velocidad con respecto a una máquina de referencia (en SPEC CPU2017 una Sun Fire V490).

Ejemplo: Si llamamos ti al tiempo que tarda la máquina a evaluar en ejecutar el programa de benchmark i-ésimo y tiREF lo que tardaría la máquina de referencia para ese programa (y hay 10 programas en el benchmark):

![image-20230116040149538](/home/clara/.config/Typora/typora-user-images/image-20230116040149538.png)

![image-20230116040202235](/home/clara/.config/Typora/typora-user-images/image-20230116040202235.png)

![image-20230116040207732](/home/clara/.config/Typora/typora-user-images/image-20230116040207732.png)

![image-20230116040219937](/home/clara/.config/Typora/typora-user-images/image-20230116040219937.png)

![image-20230116040231255](/home/clara/.config/Typora/typora-user-images/image-20230116040231255.png)

##### Ejemplos de benchmarks de sistema completo: TPC

TPC (Transactions Processing Performance Council). Organización sin ánimo de lucro especializada en benchmarks relacionados con servidores que procesan datos (data-driven servers). Empresas que hay detrás de TPC: AMD, Cisco, Dell, Fujitsu, HP, Huawei, IBM, Intel, Microsoft, Nvidia, Oracle…

Principales tipos de benchmarks:

- OLTP (on-line transaction processing): Se trata de realizar pequeñas transacciones (compras, consultas,…) con unos requisitos exigentes de tiempos de respuesta. 
- DS (decision support): Se trata de realizar consultas complejas que suelen requerir acceso a buena parte de una gran base de datos y un procesamiento posterior. 
- IoT (Internet of Things): Se trata de procesar una gran cantidad de datos procedentes de una gran cantidad de dispositivos diferentes. 
- Big Data: Se trata de procesar una gran cantidad de información usando clusters de computadores y Apache Hadoop. 
- Artificial Intelligence: Se trata de ejecutar algoritmos de aprendizaje utilizados en machine learning (p.ej. redes neuronales profundas) a partir de datos. 
- Virtualization: Se trata de ejecutar los benchmarks de los tipos anteriores en un entorno virtualizado en lugar de en una máquina “física” (bare metal).

**EJ. Benchmark TPC-C:** Es de tipo OLTP (on-line transaction processing). Simula una gran compañía que vende 100.000 productos y que tiene varios almacenes (configurable), cada uno a cargo de 10 zonas, con 3000 clientes/zona. 

Las peticiones involucran acceso a las bases de datos tanto locales como distribuidas (a veces el producto no está en el almacén más cercano), ejecución simultánea de consultas y acceso no uniforme a las bases de datos.

- Índice de rendimiento utilizado: transacciones procesadas por segundo, minuto u hora (tps, tpm o tph) superando unos ciertos requisitos de tiempos de respuesta (ej. el 90% deben tener un tiempo de respuesta inferior a 5s). También suelen proporcionar tanto el consumo de potencia como el coste por transacción procesada (incluido mantenimiento de 3 años).

**EJ. Benchmark TPC-H:** Es de tipo DS (decision support). Contiene un total de 22 tipos de consultas diferentes que requieren examinar grandes volúmenes de datos para poder contestar a preguntas complejas. También incluyen modificaciones concurrentes de los datos. Algunos de estos tipos de consultas son: 

- Realizar un informe detallado sobre las ventas en un determinado periodo de tiempo. 
- Buscar el proveedor más adecuado según un conjunto determinado de criterios. 
- Cuantificar el aumento de ingresos que habría resultado de eliminar ciertos descuentos en toda la empresa en un rango de fechas determinado.

Aunque es escalable, la base de datos cuenta con la información de un mínimo de 10000 proveedores con un mínimo de 10 millones de filas.

- Índice de rendimiento utilizado (métrica): TPC-H Composite Query-per-Hour Performance Metric (QphH@Size), donde Size es el tamaño de la base de datos utilizado (factor de escala). Tiene en cuenta la productividad (consultas por unidad de tiempo) en dos escenarios diferentes: cuando las consultas se realizan por un único usuario y cuando se permite concurrencia entre varios usuarios. También suelen proporcionar tanto el consumo de potencia como el coste por QphH@Size (incluido mantenimiento de 3 años).

##### Otros benchmark de sistema completo

- Servidores de ficheros: 
  - SPECstorage Solution 2020. 
  - SPEC SFS2014. 
- JAVA Cliente/Servidor: 
  - SPECjEnterprise2010: Java Enterprise Edition (JEE). 
  - SPECjms2007: Java Message Service (JMS).  SPECjvm2008: Java Runtime Environment (JRE). 
- High Performance Computing, OpenMP, MPI, OpenCL: 
  - SPEC MPI2007: Message Passing Interface (MPI). 
  - SPEC OMP2012: Open MultiProcessing (OpenMP). 
  - SPEC ACCEL: OpenCL y OpenACC.
- Para el mundo del PC: 
  - SPECworkstation® 3.1: Sistemas basados en Windows. Usa programas de código abierto. 
  - SYSmark25: De la empresa BAPco. Sistemas basados en Windows. Usa programas propietarios.

## 4.2. Análisis de los resultados de un test de rendimiento

### ¿Cómo expresar el resultado final tras la ejecución de un test de rendimiento?

Muchos tests de rendimiento se basan en la ejecución de diferentes programas y, por tanto, producen diferentes medidas de rendimiento. Sin embargo, estos tests suelen resumir todas estas medidas en un único valor: el índice de rendimiento de dicho test. 

![image-20230116025501734](/home/clara/.config/Typora/typora-user-images/image-20230116025501734.png)

¿Cómo concentrar todos los índices en uno solo? Método habitual de síntesis: uso de algún tipo de media.

#### Media aritmética

Dado un conjunto de n medidas t1,...,tn, definimos su media aritmética como el sumatorio de t1..tn entre n:

![image-20230116025700922](/home/clara/.config/Typora/typora-user-images/image-20230116025700922.png)

Si no todas las medidas tienen la misma importancia, se puede asociar a cada medida tk un peso wk, obteniéndose la media aritmética ponderada:

![image-20230116025727564](/home/clara/.config/Typora/typora-user-images/image-20230116025727564.png)

Si tk es el tiempo de ejecución del programa de benchmark k-ésimo en la máquina a testar, wk podría escogerse, por ejemplo, inversamente proporcional a dicho tiempo de ejecución en una determinada máquina de referencia:

![image-20230116025745423](/home/clara/.config/Typora/typora-user-images/image-20230116025745423.png)

#### Media geométrica

Dado un conjunto de n medidas, S1,...,Sn, definimos su media geométrica como la raíz n-ésima del multiplicatorio de S1..Sn :

![image-20230116025801140](/home/clara/.config/Typora/typora-user-images/image-20230116025801140.png)

Propiedad: cuando las medidas son ganancias en velocidad (speedups) con respecto a una máquina de referencia, este índice mantiene el mismo orden en las comparaciones independientemente de la máquina de referencia elegida (siempre que sea la misma). Usado en los benchmarks de SPEC y SYSmark25.

![image-20230116030145607](/home/clara/.config/Typora/typora-user-images/image-20230116030145607.png)

### Ejemplo de comparación con tiempos

![image-20230116030823310](/home/clara/.config/Typora/typora-user-images/image-20230116030823310.png)

La máquina más rápida es “A” ya que es la que tarda menos en ejecutar, uno tras otro, todos los programas del benchmark (1839,1 segundos).

#### Comparación según tiempo total

Ordenación con el tiempo total: 

- De más rápida a más lenta: A, B, C, D 
- Esto no significa que A sea siempre la más rápida (depende del programa), aunque, en conjunto, sí que lo es.

![image-20230116030910335](/home/clara/.config/Typora/typora-user-images/image-20230116030910335.png)

#### Comparación según media aritmética

La máquina que ejecuta los programas del benchmark, uno tras otro, en menor tiempo es la de menor media aritmética de los tiempos de ejecución.

![image-20230116030949780](/home/clara/.config/Typora/typora-user-images/image-20230116030949780.png)

#### Comparación según media aritmética ponderada

Según este criterio, la máquina “más rápida” sería la de menor tiempo medio ponderado de ejecución. Nótese que esta ponderación depende, en este ejemplo, de la máquina de referencia.

![image-20230116031137807](/home/clara/.config/Typora/typora-user-images/image-20230116031137807.png)

#### Comparación según media geométrica

Calculamos la ganancia en velocidad de cada máquina con respecto a la máquina de referencia (tal y como lo hacen SPEC y SYSmark25):

![image-20230116031336171](/home/clara/.config/Typora/typora-user-images/image-20230116031336171.png)

El speedup es un índice a maximizar. Según este índice, la “mejor máquina” es ¡¡¡la D!!!

#### ¿A quién beneficia la decisión de usar la media geométrica de speedups?

Se premian las mejoras sustanciales. No se castigan empeoramientos no tan sustanciales. Debemos ser MUY cuidadosos con las comparaciones y saber qué estamos haciendo realmente.

![image-20230116031528848](/home/clara/.config/Typora/typora-user-images/image-20230116031528848.png)

### Conclusiones de este análisis

Intentar reducir un conjunto de medidas de un test de rendimiento a un solo “valor medio” final no es una tarea trivial. 

La media aritmética de los tiempos de ejecución es una medida fácilmente interpretable e independiente de ninguna máquina de referencia. El menor valor nos indica la máquina que ha ejecutado el conjunto de programas del test, uno tras otro, en un tiempo menor. 

La media aritmética ponderada nos permite asignar más peso a algunos programas que a otros. Esa ponderación debería realizarse, idealmente, según las necesidades del usuario. Si se hace de forma dependiente de los tiempos de ejecución de una máquina de referencia, la elección de ésta puede influir significativamente en los resultados. 

La media geométrica de las ganancias en velocidad con respecto a una máquina de referencia es un índice de interpretación compleja cuya comparación no depende de la máquina de referencia. Premia mejoras sustanciales con respecto a algún programa del test y no castiga al mismo nivel los empeoramientos.

## 4.3. Comparación de prestaciones en presencia de aleatoriedad

### Repaso de estadística

Independientemente de qué índice se escoja, un buen ingeniero debería, en primer lugar, determinar si las diferencias entre las medidas obtenidas por un test de rendimiento en presencia de aleatoriedad son estadísticamente significativas Necesitaremos repasar algunos conceptos de estadística.

- **Distribución normal:** Es una distribución de probabilidad caracterizada por su media µ y su varianza σ2 cuya función de probabilidad viene dada por:

  ![image-20230118005344837](/home/clara/.config/Typora/typora-user-images/image-20230118005344837.png)

- **Teorema del límite central:** la media de un conjunto grande de muestras aleatorias de cualquier distribución e independientes entre sí pertenece una distribución normal.

- Si extraemos N muestras {d1..dN} pertenecientes a una distr normal, cuya media es dreal, y calculo la siguiente media:

  ![image-20230118005629537](/home/clara/.config/Typora/typora-user-images/image-20230118005629537.png)

  ![image-20230118010105477](/home/clara/.config/Typora/typora-user-images/image-20230118010105477.png)

  y repito muchas veces, veremos que los texp obtenidos pertenecen a una distribución t de estuden con N-1 grados de libertad (df, degrees of freedom)

###### Ejemplo

> Tenemos los tiempos de ejecución en segundos de 6 programas P1..P6 en dos máquinas diferentes, A y B, en condiciones en las que puede haber aleatoriedad.
>
> ![image-20230118005836551](/home/clara/.config/Typora/typora-user-images/image-20230118005836551.png)
>
> Las medias de tA y tB son 144.5s y 120.2s, la tercera columna D es la diferencia entre las dos, y su media es 24.3s 
>
> La desviación típica (fórmula arriba, nos la suelen dar tho) es de 19.9s, así que el error estándar es 19.9/sqrt(6)=8.12s
>
> ¿Es una diferencia significativa entre las dos máquinas, o sólo la aleatoriedad haciendo de las suyas?

Si partimos de la hipótesis "nula" H0 de que las máquinas tienen rendimientos equivalentes, entonces las diferencias D se producen enteramente por una suma de elementos aleatorios independientes. En tal caso, Di serían muestras de una distribución normal [Teorema del límite central] de media 0 (Dreal = 0).

Por tanto:

![image-20230118010612723](/home/clara/.config/Typora/typora-user-images/image-20230118010612723.png)

pertenecerá a una distribución t de Student con N-1 = 6-1 =5 grados de libertad. **¿Qué probabilidad hay de que esto sea realmente así?** Vamos a verlo con el grado de significatividad

- **P value:** la probabilidad de obtener un valor cuyo valor absoluto es 2.99 (Texp^^^) en una distr TStudent con 5 grados de libertad (de -5 a +5) es de 0.03 [nos darían el valor o la tabla]
  - ¿Esto es mucho o poco? Definimos un umbral, el **grado de significatividad α.** También está el **grado de confianza**, 1-α (y nos queda en forma de %).
  - Un valor típico para α es 0.05 así que tomamos eso. Grado de confianza entonces 95%.
- ENTONCES, si **pvalue < α**  (0.03 < 0.05) diremos que, para un grado de significatividad α o para un grado de confianza del 95%, las máquinas tienen rendimientos distintos, o sea, DESCARTAMOS LA H0 DE QUE SON IGUALES.
  - En caso contrario, simplemente NO PODEMOS DESCARTAR LA H0. Ni se confirma ni se desmiente.
- Así que, sabiendo esto, podemos hacer la proporción: tiempo medio a/tiempo medio b = 144.5 / 120.2 = 1.2, la máquina B es un 20% más rápida que la máquina A

![image-20230118012224453](/home/clara/.config/Typora/typora-user-images/image-20230118012224453.png)



## 4.4. Diseño de experimentos de comparación de rendimiento

![image-20230118012715570](/home/clara/.config/Typora/typora-user-images/image-20230118012715570.png)