# ISE 22/23 - Tema 3: Monitorización de servicios y programas

###### ¿Cómo medir el rendimiento de mi servidor?

> **Objetivos:**
>
> - Entender el concepto de monitor, sus utilidades e implementaciones
> - Conocer características básicas de un monitor a nivel de SO y a nivel de aplicación concreta (profilers)
> - Comprender el trabajo del monitor para evaluar el rendimiento de un servidor
> - Saber interpretar la información que aporta un monitor

## 3.1. Concepto de monitor de actividad

### ¿Para qué monitorizar un servidor?

Administrador/Ingeniero 

- Conocer cómo se usan los recursos para saber: 
  - Qué hardware hay que reconfigurar / sustituir/ añadir (cuello de botella). 
  - Qué parámetros del sistema hay que ajustar. 
- Obtener un modelo de un componente o de todo el sistema para poder deducir qué pasaría si... 
- Poder predecir cómo va a evolucionar la carga con el tiempo (capacity planning). 
- Tarificar a los clientes (cloud computing). 

Programador 

- Conocer las partes críticas de una aplicación de cara a su optimización (hot spots). 

Sistema Operativo 

- Adaptarse dinámicamente a la carga.

### Carga y actividad de un servidor

- Carga (workload): conjunto de tareas que ha de realizar el servidor (todo aquello que demande recursos del servidor.) 

- Actividad de un servidor: conjunto de operaciones que se realizan en el servidor como consecuencia de la carga que soporta.

  Algunas variables que reflejan la actividad de un servidor:

  - Procesadores: Utilización, temperatura, fCLK, nº procesos, nº interrupciones, cambios de contexto, etc. 
  - Memoria: nº de accesos, memoria utilizada, fallos de caché, fallos de página, uso de memoria de intercambio, latencias, anchos de banda, voltajes, etc. 
  - Discos: lecturas/escrituras por unidad de tiempo, longitud de las colas de espera, tiempo de espera medio por acceso, etc. 
  - Red: paquetes recibidos/enviados, colisiones por segundo, sockets abiertos, paquetes perdidos, etc. 
  - Sistema global: nº de usuarios, nº de peticiones, etc.

### Definición de monitor de actividad

Herramienta diseñada para medir la actividad de un sistema informático y facilitar su análisis. Acciones típicas de un monitor: 

- Medir alguna/s variables que reflejen la actividad. 
- Procesar y almacenar la información recopilada. 
- Mostrar los resultados.

![image-20230115164436446](/home/clara/.config/Typora/typora-user-images/image-20230115164436446.png)

### Tipos de monitores según distintas clasificaciones

#### ¿Cuándo se mide?

- Cada vez que ocurre un evento (**monitor por eventos**). 

  - Mide el nº de ocurrencias de uno o varios eventos.
  - Información exacta. 

  Evento: Cambio en el estado del sistema. Ejemplos de eventos: 

  - Abrir/cerrar un fichero. 
  - Fallo en memoria cache. 
  - Interrupción de un dispositivo periférico.

- Cada cierto tiempo (**monitor por muestreo**) 

  - Cada T segundos, siendo T el periodo de muestreo, el monitor realiza una medida. 
  - La cantidad de información recogida depende de T. 
  - T puede ser, a su vez, variable. 
  - Información estadística.

#### ¿Cómo se mide?

- **Software** - Programas instalados en el sistema
- **Hardware** - Dispositivos físicos de medida (menor sobrecarga)
- **Híbridos**

![image-20230115170120773](/home/clara/.config/Typora/typora-user-images/image-20230115170120773.png)

![image-20230115170131129](/home/clara/.config/Typora/typora-user-images/image-20230115170131129.png)

#### ¿Existe interacción analista-administrador?

- **No existe**. La consulta sobre los resultados se realiza aparte mediante otra herramienta independiente al proceso de monitorización: monitores tipo batch, por lotes o en segundo plano (batch monitors).
- **Sí existe**. Durante el propio proceso de monitorización se pueden consultar los valores monitorizados y/o interactuar con ellos realizando representaciones gráficas diversas, modificando parámetros del propio monitor, etc.: monitores en primer plano o interactivos (on-line monitors).

### Atributos más importantes

#### Atributos más importantes que caracterizan un monitor de actividad

- **Anchura de Entrada (Input Width):** ¿Cuánta información (p.ej. nº de bytes) se almacena por cada medida que toma el monitor? 
- **Sobrecarga (Overhead):** ¿Qué recursos le “roba” el monitor al sistema? El instrumento de medida puede perturbar el funcionamiento del sistema.

![image-20230115172143732](/home/clara/.config/Typora/typora-user-images/image-20230115172143732.png)

- Ejemplo: Sobrecarga de CPU de un monitor software por muestreo. La ejecución de las instrucciones del monitor se lleva a cabo utilizando recursos del sistema monitorizado. Supongamos que el monitor se activa cada 5s y que cada activación del mismo usa el procesador durante 6 ms.

![image-20230115172235319](/home/clara/.config/Typora/typora-user-images/image-20230115172235319.png)

#### Atributos más importantes que caracterizan cualquier herramienta de medida

- **Exactitud del sensor** **(Accuracy, offset):** ¿Cómo se aleja el valor medido del valor real que se quiere medir? 

- **Precisión del sensor**: Cuando se mide varias veces el mismo valor real, ¿se mide siempre lo mismo?, ¿cuál es la dispersión de las medidas?

  > TÍPICA PREGUNTA: exactitud vs. precisión
  >
  > ![image-20230115172356407](/home/clara/.config/Typora/typora-user-images/image-20230115172356407.png)

- **Resolución del sensor:** ¿Cuánto tiene que cambiar el valor a medir para detectar un cambio?

## 3.2. Monitorización a nivel de sistema

### El directorio /proc

Es un directorio virtual utilizado por el núcleo de Linux para facilitar el acceso del usuario a las estructuras de datos del S.O. A través de /proc podemos: 

- Acceder a información global sobre el S.O.: loadavg, uptime, cpuinfo, meminfo, mounts, net, kmsg, cmdline, slabinfo, filesystems, diskstats, devices, interrupts, stat, swap, version, vmstat, ... 
- Acceder a la información de cada uno de los procesos del sistema (/proc/[pid]): stat, status, statm, mem, smaps, cmdline, cwd, environ, exe, fd, task... 
- Acceder y, a veces, modificar algunos parámetros del kernel del S.O. (/proc/sys): dentry_state, dir-notify-enable, dquot-max, dquot-nr, file-max, file-nr, inode-max, inode- nr, lease-break-time, mqueue, super-max, super-nr, acct, domainname, hostname, panic, pid_max, version, net, vm... 

En Linux, la mayoría de los monitores de actividad a nivel de sistema usan como fuente de información este directorio.

### Comando uptime

Tiempo que lleva el sistema en marcha y la “carga media” que soporta.

![image-20230116004230880](/home/clara/.config/Typora/typora-user-images/image-20230116004230880.png)

#### Carga del sistema según Linux

Estados básicos de un proceso: 

- En ejecución (running) o esperando que haya un núcleo (core) libre para poder ser ejecutado (runnable). La cola de procesos (run queue) está formada por aquellos que se están ejecutando y los que pueden ejecutarse (runnable + running). 
- Bloqueado esperando a que se complete una operación de E/S para continuar (uninterruptible sleep = I/O blocked). 
- Durmiendo esperando a un evento del usuario o similar (p.ej. una pulsación de tecla) (interruptible sleep). 

“Carga del sistema” (system load): número de procesos en modo running, runnable o I/O blocked

![image-20230116004336526](/home/clara/.config/Typora/typora-user-images/image-20230116004336526.png)

#### ¿Cómo mide la carga media el SO?

Experimento: Ejecutamos 1 único proceso (bucle infinito). Llamamos a uptime cada cierto tiempo y representamos los resultados.

![image-20230116004403602](/home/clara/.config/Typora/typora-user-images/image-20230116004403602.png)

Según sched.c, sched.h (kernel de Linux): 

LA(t) = c· load(t) + (1-c)·LA(t-5) 

- LA (t) = Load Average en el instante t. 
- Se actualiza cada 5 segundos su valor. 
- load(t) es la “carga del sistema” en el instante t. 
- c es una constante. A mayor valor, más influencia tiene la carga actual en el valor medio (c1>c5>c15). Si c = c1 calculamos LA_1(t), etc.

### Comando ps (process status)

Información sobre el estado actual de los procesos del sistema.

![image-20230116004827830](/home/clara/.config/Typora/typora-user-images/image-20230116004827830.png)

- USER: Usuario que lanzó el proceso. 
- %CPU, %MEM: Porcentaje de procesador y memoria física usada. 
- RSS (resident set size): Memoria (KiB) física ocupada por el proceso. 
- STAT. Estado en el que se encuentra el proceso: 
  - R (running or runnable), D (I/O blocked), 
  - S (interruptible sleep), T (stopped), 
  - Z (zombie: terminated but not died). 
  - N (lower priority = running niced), 
  - < (higher priority = not nice). 
  - s (session leader), 
  -  (in the foreground process group), 
  - W (swapped/paging).

![image-20230116004938459](/home/clara/.config/Typora/typora-user-images/image-20230116004938459.png)

### Comando top

Muestra cada T segundos: carga media, procesos, consumo de memoria... Normalmente se ejecuta en modo interactivo (se puede cambiar T, las columnas seleccionadas, la forma de ordenar las filas, etc.)

![image-20230116004958950](/home/clara/.config/Typora/typora-user-images/image-20230116004958950.png)

- wa: %tiempo ocioso esperando E/S. 
- st: %tiempo robado por el hipervisor.

### Comando vmstat (virtual memory stats)

Paging (paginación), swapping, interrupciones, cpu. La primera línea no sirve para nada (info desde el inicio del sistema).

![image-20230116005052227](/home/clara/.config/Typora/typora-user-images/image-20230116005052227.png)

- Procesos: r (running o runnable), b (I/O blocked) 
- Bloques por segundo transmitidos: bi (blocks in), (blocks out) 
- KB/s entre memoria y disco: si (swapped in), so (swapped out) 
- in (interrupts per second), cs (context switches per second)

Con otros argumentos, puede dar información sobre acceso a discos (en concreto la partición de swap) y otras estadísticas de memoria.

### El monitor sar (system activity reporter)

Forma parte del paquete de monitorización de la actividad Sysstat, mantenido por Sebastien Godard. Recopila información sobre la actividad del sistema.

- Actual: qué está pasando ahora mismo en el sistema (modo interactivo). 
- Histórica: qué ha pasado en el sistema (modo no interactivo). 
  - Ficheros históricos en /var/log/sysstat/saDD, donde los dígitos DD indican el día del mes.

Esquema de funcionamento:

- sadc (system-accounting data collector): Recoge los datos estadísticos (lectura de contadores) y construye un registro en formato binario (back-end). 
- sar: Lee los datos binarios que recoge sadc y las traduce a texto plano (front-end).

![image-20230116005419844](/home/clara/.config/Typora/typora-user-images/image-20230116005419844.png)

#### Parámetros de sar y ejemplos de uso

Gran cantidad de parámetros (puede funcionar en modo batch o en modo interactivo)

![image-20230116005446298](/home/clara/.config/Typora/typora-user-images/image-20230116005446298.png)

EJ. Utilización global del procesador (que puede ser multi-núcleo) recopilada durante el día de hoy:

![image-20230116005527463](/home/clara/.config/Typora/typora-user-images/image-20230116005527463.png)

EJ. Utilización desglosada de cada núcleo (core) recopilada de forma interactiva cada 1s:

![image-20230116005540644](/home/clara/.config/Typora/typora-user-images/image-20230116005540644.png)

EJ. UNIDADES DE ALMACENAMIENTO: Estadísticas globales del sistema de E/S (sin incluir la red) recopiladas entre las 10 y las 12h del día 6 de este mes:

![image-20230116005703070](/home/clara/.config/Typora/typora-user-images/image-20230116005703070.png)

EJ. UNIDADES DE ALMACENAMIENTO: Información de prestaciones de cada disco recopilada de forma interactiva cada 10s (2 muestras):

![image-20230116005711208](/home/clara/.config/Typora/typora-user-images/image-20230116005711208.png)

EJ. MONITORIZACIÓN DE LA RED: Mostramos información recopilada de forma interactiva cada 1s sobre todo el tráfico TCP, incluyendo errores en los paquetes, de todos los dispositivos de red.

![image-20230116005957567](/home/clara/.config/Typora/typora-user-images/image-20230116005957567.png)

#### Almacenamiento de los datos muestreados por sadc

Se utiliza un fichero histórico de datos por cada día. Se programa la ejecución de sadc un número de veces al día con la utilidad “cron” de Linux. Por ejemplo, una vez cada 5 minutos comenzando a las 0:00 de cada día. 

Cada ejecución de sadc añade un registro binario con los datos recogidos al fichero histórico del día.

![image-20230116010204398](/home/clara/.config/Typora/typora-user-images/image-20230116010204398.png)

### Cálculo de la anchura de entrada del monitor

Datos de partida, extracto de `ls /var/log/sysstat`:

![image-20230116010918777](/home/clara/.config/Typora/typora-user-images/image-20230116010918777.png)

Suponemos que la primera muestra se toma a las 0:00 de cada día y que sadc se ejecuta con un tiempo de muestreo constante.

Solución: 

- El fichero histórico de un día ocupa 3.049.952 bytes (aproximadamente 3,05 MB o 2,91MiB). 
  - La orden sadc se ejecuta cada 5 minutos. 
  - Cada hora se recogen 12 muestras. 
  - Al día se recogen 24 x 12 = 288 muestras. 
  - Anchura de entrada del monitor: Cada registro ocupa, de media, 10590,1 bytes (aproximadamente 10,59 KB o 10,34KiB).
  - Así que, 288*10590,1 = 3.049.952 bytes

### Otras herramientas de monitorización a nivel de sistema

![image-20230116011013239](/home/clara/.config/Typora/typora-user-images/image-20230116011013239.png)

### Procedimiento sistemático de monitorización: método USE (Utilization, Saturation, Errors)

Para cada recurso del servidor (CPU, memoria, almacenamiento, red) comprobamos, para un intervalo de tiempo dado:

- Utilización media: Fracción de uso del recurso (tiempo de CPU, tamaño de memoria, ancho de banda de cada disco, tarjeta de red o módulo de memoria DRAM, etc.)
- Saturación: Ocupación media de las colas de aquellas tareas que quieren hacer uso de ese recurso.
- Errores: Mensajes de error sobre el uso de dichos recursos.

Ejemplo: 

- Utilización: si el recurso es un determinado núcleo del procesador: fracción de ese intervalo de tiempo en la que el núcleo ha estado ocupado ejecutando instrucciones. Se puede medir con sar –P y sumo todos los campos del núcleo que me interesa excepto %idle y %wa.
- Saturación: para un determinado disco duro, ejecuto sar –d y leo avgqu-sz.
- Errores: para DRAM, con dmesg puedo ver errores físicos.

#### Herramientas de monitorización de servidores distribuidos

Cuando tenemos que monitorizar varios servidores de forma simultánea, es más conveniente utilizar herramientas especiales de monitorización de equipos distribuidos en red como Nagios, Naemon, Shinken, Ganglia, Munin, Zabbix o Elastic.

![image-20230116010421172](/home/clara/.config/Typora/typora-user-images/image-20230116010421172.png)

Algunas características deseables para monitorizar servidores distrubuidos:

- Compatibilidad con múltiples protocolos para enviar el valor medido: SNMP, IPMI, HTTP, JMX. Servidores independientes para la base de datos y el front-end. API para aplicaciones secundarias (p.ej. Grafana). 
- Escalabilidad: servidores monitores secundarios encargados de subconjuntos de servidores. Auto-descubrimiento de nuevos servidores. 
- Extensibilidad: permitir plugins para ampliar la funcionalidad (p.ej. añadir nuevas medidas por medio de scripts, añadir nuevos protocolos, etc.) 
- Prestaciones: Permitir tanto polling como pushing, con agente o sin él. 
- Envío por lotes. Compresión. 
- Fiabilidad: Validación de los valores monitorizados. 
- Disponibilidad: Gestión de alarmas y envío de notificaciones (email, Telegram, etc.) si se detectan elementos caídos. 
- Seguridad: Encriptación de las comunicaciones, tablas hash para evitar suplantación de servidores.

## 3.3. Monitorización a nivel de aplicación

Objetivo de un profiler: monitorizar la actividad generada por una aplicación concreta con el fin de obtener información para poder optimizar su código. 

Información que sería interesante que nos proporcionara un profiler: 

- ¿Cuánto tiempo tarda en ejecutarse el programa? ¿Qué parte de ese tiempo es de usuario y cuál de sistema? ¿Cuánto tiempo se pierde por las operaciones de E/S? 
- ¿En qué parte del código pasa la mayor parte de su tiempo de ejecución (=hot spots)? 
- ¿Cuánto tiempo tarda en ejecutarse cada función (procedimiento o subrutina) a la que llama el programa? 
- ¿Cuántas veces se llama a cada función del programa y desde dónde? 
- ¿Cuántos fallos de caché/página genera cada línea del programa? 
- ¿Qué cantidad de memoria está ocupada en cada momento del programa? Etc.

### Primera aproximación: /usr/bin/time

El programa /usr/bin/time mide el tiempo de ejecución de un programa y muestra algunas estadísticas sobre su ejecución.

![image-20230116012055240](/home/clara/.config/Typora/typora-user-images/image-20230116012055240.png)

- User time: tiempo de CPU ejecutando en modo usuario. 
- System time: tiempo de CPU ejecutando código del núcleo del S.O. 
- Elapsed (wall clock) time: tiempo que tarda el programa en ejecutarse. 
- Major page faults: fallos de página que requieren acceder al almacenamiento permanente. 
- Cambios de contexto voluntarios: Cuando acaba el programa o al tener que esperar a una operación de E/S cede la CPU a otro proceso. 
- Cambios involuntarios: Expira su “time slice”.

> Para time: 
>
> - **real**: tiempo real. Desde que lanzas la orden hasta que acaba.
> - **user**: uso de la CPU en user mode
> - **system**: uso de la CPU en kernel mode

### Profiler gprof

Estima el tiempo de CPU que consume cada función de un proceso/hilo. También calcula el número de veces que se ejecuta cada función y cuántas veces una función llama a otra. Utilización de gprof para programas escritos en C, C++:

- Instrumentación en la compilación: 

  ```
  gcc prog.c –o prog –pg -g 
  ```
- Ejecución del programa y recogida de información: 

  - Directorio ./prog 
  - La información recogida se guarda en el fichero gmon.out 

- Visualización de la información referida a la ejecución del programa:

  ```
  gprof prog
  ```


![image-20230116012259747](/home/clara/.config/Typora/typora-user-images/image-20230116012259747.png)

#### Funcionamiento de un programa instrumentado por gprof

- Arranque del programa: 
  - Genera una tabla con la dirección física en memoria de cada función del programa. 
  - Se inicializan contadores de cada función del programa a 0. Hay dos contadores por función: c1 para medir el número de veces que se ejecuta y c2 para estimar su tiempo de CPU. 
  - El S.O. programa un temporizador (por defecto 0,01s) que irá decrementándose cada vez que se ejecute código del programa. 
- Durante la ejecución del programa: 
  - Cada vez que se ejecuta una función se incrementa el contador c1 asociado a la función. De paso, se mira a través de la pila qué función la ha llamado y se guarda esa información. 
  - Cada vez que el temporizador llega a 0s, se interrumpe el programa y se incrementa el contador c2 de la función interrumpida. Se re-inicia el temporizador. 
- Al terminar el programa: 
  - Teniendo en cuenta el tiempo total de CPU del programa y los contadores c2, se estima el tiempo de CPU de cada función. 
  - Se generan el flat profile y el call profile a partir de la información recopilada.

#### Ejemplo

![image-20230116013454032](/home/clara/.config/Typora/typora-user-images/image-20230116013454032.png)

![image-20230116013501818](/home/clara/.config/Typora/typora-user-images/image-20230116013501818.png)

![image-20230116013301783](/home/clara/.config/Typora/typora-user-images/image-20230116013301783.png)

![image-20230116013327853](/home/clara/.config/Typora/typora-user-images/image-20230116013327853.png)

### Otros profilers

#### Perf

Perf es un conjunto de herramientas para el análisis de rendimiento en Linux basadas en eventos software y hardware (hacen uso de contadores hardware disponibles en los últimos microprocesadores de Intel y AMD). Permiten analizar el rendimiento de 

- a) un hilo individual
- b) un proceso + sus hijos
- c) todos los procesos que se ejecutan en una CPU concreta, 
- d) todos los procesos que se ejecutan en el sistema. 

Uso: `perf [--version] [--help] COMMAND [ARGS]`. Algunos de los comandos que proporciona:

- list: Lista todos los eventos disponibles. 
- stat: Cuenta el número de eventos. 
- record: Recolecta muestras cada vez que se produce un determinado conjunto de eventos. Fichero de salida: perf.data. 
- report: Analiza perf.data y muestra las estadísticas generales. 
- annotate: Analiza perf.data y muestra los resultados a nivel de código ensamblador y código fuente (si está disponible).

Algunos tipos de eventos, `perf list`:

![image-20230116013712506](/home/clara/.config/Typora/typora-user-images/image-20230116013712506.png)

##### Ejemplos de perf como profiler

- Ejemplo 1: Cuento el tiempo de CPU, número total de ciclos, instrucciones, cambios de contexto y fallos de página del proceso matr_multiplication. Repito el experimento 3 veces:

  ![image-20230116014013083](/home/clara/.config/Typora/typora-user-images/image-20230116014013083.png)

- Ejemplo 2: Recolecto muestras del programa ./bucles cada cierto número de ocurrencias (por defecto 4000) del evento task-clock. Guardo la información en perf.data.

  ![image-20230116014117246](/home/clara/.config/Typora/typora-user-images/image-20230116014117246.png)

  Y ahora muestro las funciones de mi programa que más ciclos de reloj consumen:

  ![image-20230116014135699](/home/clara/.config/Typora/typora-user-images/image-20230116014135699.png)

- Ejemplo 3: Muestro las funciones de mi programa que más ciclos de reloj consumen y las que más fallos de página provocan:

  ![image-20230116014031249](/home/clara/.config/Typora/typora-user-images/image-20230116014031249.png)

- Ejemplo 4: Muestro las líneas concretas de mi programa que más ciclos de reloj consumen:

  ![image-20230116014042535](/home/clara/.config/Typora/typora-user-images/image-20230116014042535.png)

  

#### Valgrind

Valgrind es un conjunto de herramientas para el análisis y mejora del código de un programa (sólo disponible en Linux). Entre éstas, encontramos herramientas para la detección de errores de memoria (memcheck), detección de bloqueos o carreras en hebras (hellgrind), análisis del uso del heap (massif) y un profiler de memoria caché y de fallos de predicción de saltos (cachegrind).

- Valgrind analiza cualquier programa ya compilado (no necesita instrumentar el programa a partir de su código fuente). 
- Valgrind actúa, esencialmente, como una máquina virtual que emula la ejecución de un programa ejecutable en un entorno aislado. Por tanto, la información suministrada es exacta y no estadística. 
- Como desventaja, el sobrecoste computacional es muy alto. La emulación del programa ejecutable puede tardar decenas de veces más que la ejecución directa del programa de forma nativa.

#### V-tune (Intel) y CodeXL (AMD)

Al igual que Perf, pueden hacer uso tanto de eventos software como hardware. Ambos programas funcionan tanto para Windows como para Linux, y permiten obtener información sobre los fallos de caché, fallos de TLB, bloqueos/rupturas del cauce, fallos en la predicción de saltos, cerrojos y esperas entre hebras, etc. asociados a cada línea del programa (tanto en código fuente como en código ensamblador).

También se pueden usar como depuradores (debuggers), permiten la ejecución remota y son capaces de medir también el rendimiento de GPU, controlador de memoria, conexiones internas a la CPU, etc.