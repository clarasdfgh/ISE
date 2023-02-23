# ISE 22/23 - Tema 2: Componentes hardware de un servidor

###### ¿Qué hardware es más adecuado para mi servidor

> **Objetivos:**
>
> - Identificar los componentes hardware de un servidor a nivel de placa base
> - Conocer características básicas de los componentes más usuales de un servidor
> - Conocer las características y prestaciones de los buses e interconexiones entre componentes, en particular de los buses de E/S.
> - Saber identificar las prestaciones principales de los distintos componentes hardware disponibles comercialmente a partir de la información del fabricante.
> - Saber montar un servidor sencillo a partir de sus componentes.

## 2.1. Placa base

Una placa base (o placa madre, motherboard, mainboard) es la tarjeta de circuito impreso (PCB, Printed Circuit Board) principal de un computador. En ella se conectan los componentes hardware del computador y contiene diversos conectores para añadir distintos periféricos adicionales.

> El tablero donde voy a poner componentes: procesador, ram, disco duro... Tarjeta de circuito impreso, una base no conductora con un diseño en máscaras de cobre, pistas de cobre que unen puntos. Se usa el anverso y reverso, y también existen multicapa, que se stackean verticalmente y se unen por vías. 

Una PCB, en general, está hecha de una lámina de un substrato no conductor (normalmente fibra de vidrio con una resina no inflamable) sobre la que se extienden pistas de cobre (material conductor). 

Las placas base actuales son multicapa (multi PCB). A través de unos agujeros (vías) podemos conectar las pistas de una capa a otra. Las placas base se suelen fabricar con distintos tamaños y formas (form factors), según distintos estándares: ATX, BTX, EATX, Mini-ITX, etc.

> Chasis compatible con muchas placas base distintas - el mundo de los servidores no está muy estandarizado, pero el de los chasis sí

![image-20230112181615847](/home/clara/.config/Typora/typora-user-images/image-20230112181615847.png)

> La placa base de un servidor es más básica que la de un PC - en este caso estamos viendo una de PC, pero bastante antigua

## 2.2. Montaje del servidor

A la hora de montar una placa base hay que tener cuidado con las descargas electrostáticas (ESD, electrostatic discharge). No solo por nuestra seguridad, sino que también pueden dañar los chips: conviene descargar la electricidad estática previamente tocando una superficie amplia de metal, usar una muñequera de descarga (ESD wrist strap) o guantes ESD-safe. 

![image-20230112183921047](/home/clara/.config/Typora/typora-user-images/image-20230112183921047.png) ![image-20230112183926848](/home/clara/.config/Typora/typora-user-images/image-20230112183926848.png)

No tocar nunca ningún contacto metálico de ningún componente de la placa ni de ningún conector. Asegurarse de que el equipo esté apagado antes de instalar/quitar cualquier componente (salvo hot plugging/ swapping). 

> Normalmente un componente o un conector solo puede instalarse de una única manera, tienen alguna asimetría o indicador de la posición correcta: no forzar la inserción de componentes/conectores.

## 2.3. Fuente de alimentación, VRM y disipadores de calor

### Fuente de alimentación

Alimenta tanto la placa base como los periféricos. 

- Misión: convierte corriente alterna en corriente continua. 
- Entrada: AC (220V – 50Hz) 
- Salidas: DC(±5V, ±12V, ± 3,3V) 
- Potencia: 250W, 500W…

![image-20230112190506395](/home/clara/.config/Typora/typora-user-images/image-20230112190506395.png)

> Para hacernos una idea de los voltajes de los componentes, un DDRM5 estándar consume 1.1V (aunque ninguno de los que vemos aquí consume eso). Un procesador consume unos 0.8V

> Si un ordenador está apagado, ¿cómo vamos a mandar la señal de que hemos pulsado el botón de encender? La respuesta es el conector 5VSB (5 Voltios Stand By), siempre habrá 5V para encender el ordenador, incluso si está apagado. 
>
> PSON es el conector que manda la señal a la fuente de alimentación para que se encienda, el que se conecta con el botón físico de encendido.

> TÍPICA PREGUNTA: ¿Existen fuentes de alimentación hotswappable? Parece imposible, pero sí. Están para servers con muchas fuentes, coges la que está fallando y la sustituyes por una nueva, mientras lo haces el sistema va a sufrir un poco (porque tiene una fuente de alimentación de menos), pero es posible
>
> Las cosas que son hotswappable tienen un indicador visual, como una palanquita roja.
>
> ![7NVX8 FUENTE DE ALIMENTACION HOT SWAP 870W DELL POWEREDGE R710 SERVER  A870P-00](http://s3.amazonaws.com/englobar/fotos/articulos/0002437M.jpg)



### Módulo regulador del voltaje VRM

Adapta la tensión continua de la fuente de alimentación (5V, 12V) a las tensiones continuas menores que necesitan los diferentes elementos de un computador (CPU, memoria, chipset, etc.), dándoles también estabilidad.

> ¿Sabes en las casas viejas, cuando enchufabas el radiador y parpadeaba la luz, o se quedaba más tenue? Esto no se puede permitir en un ordenador porque fríes los chips. El VRM garantiza una tensión estable. 
>
> Es un conversor continua-continua. Siempre lo veremos cerca del zócalo de la CPU y de las ranuras de memoria

![image-20230112185231853](/home/clara/.config/Typora/typora-user-images/image-20230112185231853.png)

## 2.4. CPU: zócalos, fabricantes y características

### Zócalos

Facilitan la conexión entre el microprocesador y la placa base de tal forma que el microprocesador pueda ser remplazado sin necesidad de soldaduras. 

Los zócalos para micros con un número grande de pines suelen ser de dos tipos:

- PGA-ZIF (pin grid array - zero-insertion force) 

  > Pensado para array de pines, o sea, el zócalo es un array de agujeros.

- LGA (land grid array), que hacen uso de una pequeña palanca (PGA-ZIF) o una pequeña placa de metal (LGA) para fijar el micro al zócalo.

  > Pensado para procesador de lands, o sea, los pines están en el zócalo, aunque son mucho más pequeños que los pines del otro tipo. 
  >
  > En este caso, el procesador se "posa" sobre el zócalo, no hay que encajarlo en los agujeros. Luego una palanca lo sujeta en su sitio.

   De esta forma, se minimiza el riesgo de que se doble alguna patilla durante el proceso de inserción.



![image-20230112193505533](/home/clara/.config/Typora/typora-user-images/image-20230112193505533.png)

### Microprocesadores

#### Evolución histórica de los microprocesadores

Número de transistores, rendimiento y consumo de potencia de microprocesadores de propósito general:

![image-20230112194718270](/home/clara/.config/Typora/typora-user-images/image-20230112194718270.png)

> Esta gráfica parece un aumento lineal, pero si miramos bien el eje Y veremos que son crecimientos en realidad exponenciales - es la ley de Moore

> Diferencia CPU y microprocesador: antes el microprocesador simplemente era un conjunto de chips, de la ALU, de los registros... 
>
> En los 70 surge el primer microprocesador, que está preparado para acceder a la RAM, tomar la instrucción.... Solían ser lo mismo
>
> Sale el Pentium 4: las corrientes de fugas empiezan a ser muy importantes, el crecimiento de la potencia no es sostenible. La solución: en vez de esperar que lo haga todo una hebra, metemos varios núcleos/cores de CPU, y conseguimos que el rendimiento de una hebra en paralelo siga siendo "lineal" (lineal en esta gráfica, exponencial en realidad).
>
> Ahí empieza la diferencia: ahora un chip procesador tiene varias CPU dentro. Pero el problema mayor llega con los procesadores hyperthreading, que hace que se perciba cada núcleo como 2. Así que, el término que preferimos usar es microprocesador, porque CPU es ambiguo: ¿nos estamos refiriendo al procesador como tal, o a una de las múltiples CPU pequeñitas que hay dentro? ¿O incluso a una de las CPU "percibidas" gracias al hyperthreading? . Además, hoy día llevan dentro también otras muchas cosas además de la CPU.
>
> Así que mejor decir microprocesador en vez de CPU.

#### ¿Qué diferencia a un microprocesador para servidores de uno para PC de la misma generación?

> En el tema 1 dijimos que un servidor no tiene por qué ser un pepinaco, es simplemente un suministrador - pero a efectos prácticos, cuando hablamos de servers aquí digamos que sí: estamos hablando de un pepinaco, con alta tolerancia a fallos, etc.

- Mayor número de núcleos (cores). 

- Suelen incorporar soporte para multi- procesamiento (2 o más micros en la misma placa). 

  - > Quizá un procesador pensado para servidores de hace 10 años tiene menos cores que un procesador para PC de hoy, pero una cosa es segura: el de hace 10 años para servidores tenía multiprocesador; y el de hoy para PC, no.

- Más memoria caché. 

- Compatible con tecnologías de memoria RAM con ECC. Mayor fiabilidad en general. 

  - > ECC: Error Correcting Code. No es lo mismo que mi PC tenga un pedo con los números, a que lo tenga el servidor de una entidad bancaria. Bit extra de redundancia para encontrar errores.

- Más canales de memoria RAM. Más líneas de E/S (PCIe, Peripheral Component Interconnect Express). 

- Más controles de calidad. Preparado para estar funcionando 24/7. 

- Más tecnologías dedicadas a facilitar tareas propias de servidores como la encriptación, la virtualización o la ejecución multihebra.

  - > Encriptación, seguridad entre VM (que una no pueda ver a la otra)...

Principales fabricantes de microprocesadores para servidores: Intel, AMD e IBM. **Ejemplo**: Intel Xeon para servidores vs Intel Core para PC (desktop) - Dos procesadores de la misma generación, no tendría sentido comparar de distintas generaciones.

|                       | Intel xeon para servidores (platinum 8380 hl) | intel core para pc (i9-10900T) |
| --------------------- | --------------------------------------------- | ------------------------------ |
| Núcleos e hilos       | 28, 56                                        | 10, 20                         |
| Micros max/placa base | 8 micros max/placa base                       | 1 micro max/placa base         |
| Caché L3              | 38.5 MB                                       | 20MB                           |
| Max RAM               | 2.5 TB RDIMM                                  | 128 GB UDIMM                   |
| Memoria ECC           | Sí                                            | No                             |
| Nº canales de memoria | 6                                             | 2                              |
| Nº líneas PCIe        | 48                                            | 16                             |
| Frecuencia de reloj   | 2.9 GHz (max 4.3 GHz)                         | 1.9 Ghz (max 4.9 GHz)          |
| TDP                   | 250W                                          | 35W                            |
| 4K                    | No                                            | Sí, a 60 Hz                    |
| PVP                   | 13000$                                        | 439$                           |

> **Sobre la frecuencia de reloj:** el procesador de PC puede llegar a más porque tiene menos núcleos, y de hecho sólo un core puede alcanzar ese máximo (si todos los cores llegan a esos ciclos de reloj a la vez, ve llamando a los bomberos)
>
> **Sobre el 4K:** no tiene sentido invertir en gráficos en un procesador para servidores, no tienen microcontrolador de gráficos. Típico de exámenes, es un rasgo "delator" de procesador para PC.

####  Procesadores AMD para servidores

La familia de procesadores de AMD para servidores fue inicialmente denominada Opteron. 

- El primer Opteron, presentado en 2003, fue el primer procesador con el conjunto de instrucciones AMD x86-64. 
- En 2004, los Opteron fueron los primeros procesadores x86 con 2 núcleos.

> Al principio Intel "dejaba" que AMD le plagiara para el repertorio x86 - AMD fue el primero en extender el repertorio a los 64 bits: mientras que Intel se obsesionaba con el Pentium 4 (añadiéndole funcionalidad), AMD le adelantó con los 64bits.

Recientemente, AMD ha modificado el nombre de sus procesadores para servidores, denominándolos EPYC. 

>  EPYC no son siglas de nada, simplemente les gustó el nombre jaja.

- Un procesador EPYC está formado por un módulo multi-chip con varios chips CCD (Core Chiplet Die, ver figura abajo) por cada microprocesador EPYC y un chip con tecnología más barata para E/S y controladores DRAM.

![image-20230113173451669](/home/clara/.config/Typora/typora-user-images/image-20230113173451669.png)

- Cada CCD tiene hasta 8 cores Zen x86-64 más memorias caché.

![image-20230113173501179](/home/clara/.config/Typora/typora-user-images/image-20230113173501179.png)

#### Procesadores IBM POWER

POWER = Performance Optimization With Enhanced RISC. Resultado del trabajo conjunto entre Apple, IBM y Motorola para servidores de gama alta de muy altas prestaciones por vatio, disponibilidad y fiabilidad (mainframes).

> Se habla mucho de Intel y AMD, pero este es el hermano mayor de ellos - IBM fue el primero en hacer el dual core, fueron los primeros en arquitectura 64b (no para x86, pero AMD no podría haberlo hecho sin IBM)...
>
> ![image-20230113173608275](/home/clara/.config/Typora/typora-user-images/image-20230113173608275.png)
>
> La diferencia es que IBM va específicamente a servidores de muy altas prestaciones, un mercado reducido y caro; mientras que Intel y AMD también tienen que estar atentos al mercado PC y de servers medios. 
>
> IBM lo produce TODO para sus servidores carísimos, no hay terceros por los que preocuparse (mientras que los otros dependen de externos para la placa base, componentes, etc.)

Ejemplo: Power 10 (2020):

- Núcleos (cores): 15. 
- Cada núcleo puede ejecutar hasta 8 hilos en paralelo. 
- 16 micros máx. / placa base. 
- 128MB de memoria caché L3. 
- Máx. RAM: 4 TB. 
- Litografía de 7nm.

### Disipadores de calor

-  Pasivos: sin alimentación, estructuras y materiales para mejorar el flujo de aire y enfriamiento. Encima del pasivo pones el activo, el ventilador.

  - Pasta térmica, se pone entre la parte de arriba del procesador y la de abajo del disipador activo porque el aire no es buen conductor. 

    Las más eficientes son metálicas. Eso puede ser un problema porque son conductoras y, si aplicas demasiada y rebosa, puedes cortocircuitar.

- Activos: ventiladores de la CPU, del chasis, refrigeración líquida…

![image-20230113182505369](/home/clara/.config/Typora/typora-user-images/image-20230113182505369.png)

## 2.5. Memoria RAM dinámica

### Ranuras para la memoria DRAM (Dynamic Random Access Memory)

Son los conectores en los que se insertan los módulos de memoria principal: R/W, volátil, necesitan refresco, prestaciones inferiores a SRAM (caché), pero mayor densidad (bits/cm2) y menor coste por bit.

![image-20230113182832779](/home/clara/.config/Typora/typora-user-images/image-20230113182832779.png)

> - R/W: ROM es la única que es solo lectura
>
> - Volátil: si apago la alimentación, el contenido desaparece. RAM y todas las caché son volátiles
>
> - Necesitan refresco: cada celda es un transistor y un condensador. Tienen muchas celdas así que estas tienen que ser muy simples. Cada cierto tiempo perderán carga, con el riesgo de perder el dato. Así que necesitan refresco, cada poco tiempo (milisegundos) volvemos a "cargar" el dato. Todo esto, recordemos, mientras esté encendido porque son volátiles.
>
>   ![image-20230113182825482](/home/clara/.config/Typora/typora-user-images/image-20230113182825482.png)
>
>   La RAM estática (SRAM) es más compleja (a nivel de construcción), lo que significa que sus celdas son más grandes, pero no necesita refresco.
>
> Siempre cerca de la CPU. Dentro de cada core del procesador tenemos L1, L2, L3 (last level caché), mientras que la DRAM va fuera, en la placa base. La RAM no lo es todo, tiene que haber suficiente caché para manejar lo que le pasa la RAM.

![image-20230113182852944](/home/clara/.config/Typora/typora-user-images/image-20230113182852944.png)

>  Esta diapositiva cae mucho

#### Evolución histórica de las tecnologías DRAM

PM = Page Mode; FPM = Fast Page Mode; EDO = Extended Data Out. SDRAM = Synchronous DRAM; DDR = Double Data Rate.

![image-20230113182931356](/home/clara/.config/Typora/typora-user-images/image-20230113182931356.png)

> SDRAM, 2000-2004: surgen RAM dinámicas síncronas - antes le pedías un dato, y ya te llegará. A partir de su invención, se sincronizan con la señal de reloj y es mucho más fácil para la CPU acceder a memoria. Cada vez son más rápidas, tienen una mejor frecuencia de reloj. 
>
> Esto es lo único que hay que saber de esta transparencia

#### Evolución histórica de los módulos de DRAM

- SIPP: Single In-line Pin Package 

- SIMM: Single In-line Memory Module 

  - > Aunque se vea parecido a una DIMM, tener en cuenta que el contacto es el mismo en el anverso y en el reverso.

  ![image-20230113184032191](/home/clara/.config/Typora/typora-user-images/image-20230113184032191.png)

- DIMM: Dual In-line Memory Module

  ![image-20230113184041543](/home/clara/.config/Typora/typora-user-images/image-20230113184041543.png)

> Se puede observar como la ranura va cambiando de lugar, para no meter un módulo no compatible (o sea, más moderno) en un módulo antiguo.



|           | Nº de contactos | voltaje | bus de datos (half-duplex) | ancho de banda típco |
| --------- | --------------- | ------- | -------------------------- | -------------------- |
| **SDRAM** | 168             | 3.3V    | 32b                        | 1.3GB/s              |
| **DDR**   | 184             | 2.5V    | 32b                        | 3.2GB/s              |
| **DDR2**  | 240             | 1.8V    | 64b                        | 8.5GB/s              |
| **DDR3**  | 240             | 1.5V    | 64b                        | 17.1GB/s             |
| **DDR4**  | 288             | 1.2V    | 64b                        | 25.6GB/s             |
| **DD5**   | 288             | 1.1V    | 64b                        | 38.4GB/s             |

> TÍPICA PREGUNTA: El voltaje con el tiempo se reduce. Esto es porque cada vez están más concentrados, y por tanto se necesita menos energía para viajar de un pin a otro.

#### Tipos de DIMM para una tecnología dada

Para PC y portátiles: 

- DIMM ó U-DIMM: Unbuffered (ó Unregistered) DIMM. 
- SO-DIMM: Small Outline DIMM. Tamaño más reducido para equipos portátiles (tienen menos contactos).

Para servidores: 

- EU-DIMM: U-DIMM con Error Correcting Code, ECC (mayor fiabilidad, chip adicional de redundancia para detectar errores). 

- R-DIMM: Registered DIMM. Hay un registro que almacena las señales de control (operación a realizar, líneas de dirección…). 

  - > Mirar siguiente punto para saber más sobre el banco de memoria.
    >
    > No puedo poner todos los módulos que quiera porque están limitados por los canales, y no se pueden poner ilimitados canales porque el reloj no daría abasto.
    >
    > Con la R-DIMM ponemos una señal de a dónde quiero acceder, "aislando" un poco lo que vamos a usar, y permitiendo un poquito más de módulos.

  - Mayor latencia que EU-DIMM pero permiten módulos de mayor tamaño. Tienen ECC. 

  - > Mayor latencia para el primer dato, porque primero hay que poner la dirección en el registro, y luego conseguir el dato

- LR-DIMM: Load Reduced DIMM. Hay un buffer que almacena tanto las señales de control como los datos a leer/escribir. Mayor latencia que R-DIMM, pero son las que permiten los módulos con mayor tamaño. Tienen ECC.

![image-20230113194206392](/home/clara/.config/Typora/typora-user-images/image-20230113194206392.png)

#### Canales y bancos de memoria DRAM

- Un microprocesador accede a los módulos de memoria DRAM a través de alguno de los canales de memoria (memory channels) de que disponga. 
- Un banco de memoria es una agrupación de ranuras de memoria que se comunican con el procesador a través de un mismo canal de memoria. 
- Un microprocesador no puede acceder simultáneamente a dos módulos del mismo banco de memoria ya que usan el mismo canal de memoria para comunicarse con él.

![image-20230113195100131](/home/clara/.config/Typora/typora-user-images/image-20230113195100131.png)

> VENIMOS DEL PUNTO ANTERIOR, R-DIMM:
>
> 4 módulos que se pueden leer a la vez, lo ideal que apunten a cosas diferentes para poder leer/escribir de forma simultánea. Pero queremos más, así que si asignamos varios a una sola ranura, tenemos un canal de memoria. 
>
> - Ventaja: muchos más módulos de memoria. 
>
> - Desventaja: no podemos acceder a varios módulos simultáneamente, porque usan el mismo canal

#### Rangos de memoria DRAM (memory ranks)

Cada módulo de memoria puede estar, a su vez, distribuido en rangos de memoria que no son más que agrupaciones de chips que proporcionan la palabra completa de 64 bits (72 bits en caso de memorias ECC). 

> Podrías hacer un DIMM cada vez más alto para meterle más chips, como el LRDIMM 
>
> Cada fila horizontal de chips realmente es como una memoria independiente

En el caso de un módulo de un solo rango (single rank), todos los chips de memoria del módulo se asocian para dar la palabra de 64 bits (72 si ECC). 

En el caso de n rangos, es como si tuviéramos una agrupación n memorias DRAM independientes en el mismo módulo, cada una con su conjunto diferente de chips. 

- Notación: 1Rx8 : Módulo de 1 rango con chips de 8 bits (64/8=8 chips, 9 si ECC)

> Ej. 1 rango para conseguir 64b:
>
>    1Rx8: 8*8=64, necesito 8 chips para 64
>
>    1Rx4: 4*16=64, necesito 16 chips para 64, estos chips están repartidos en dos caras
>
>    2Rx8: 8*8 en una cara, 8*8 en la otra
>
> TIPICA PREGUNTA: No necesariamente por tener dos caras es de rango dos, ej. 1Rx4
>
> ![image-20230114113443585](/home/clara/.config/Typora/typora-user-images/image-20230114113443585.png)
>
> Vamos a hablar prácticamente siempre de 64 bits
>
> Si hay ECC (todas las memorias para servidores tienen), serán 72bits en vez de 64.

![image-20230114113454628](/home/clara/.config/Typora/typora-user-images/image-20230114113454628.png)

##### Ejemplo memoria DRAM

Crucial 32GB DDR4-2666 LRDIMM CT32G4LFD4266

![image-20230114120104121](/home/clara/.config/Typora/typora-user-images/image-20230114120104121.png)

- Dual ranked: El microprocesador va a verlo como dos módulos de 16GB en el mismo banco de memoria. 
- “x4 based”: cada chip del módulo me da un dato de 4 bits (18 chips proporcionan cada dato de 72 bits). Al ser dual ranked, deducimos que en total habrá 36 chips.
- CL=19: CAS (Column Address Strobe) Latency. Latencia de acceso de 19 ciclos de reloj. Hay que tener en cuenta el periodo de reloj para poder comparar las latencias de memoria.

> Form factor: load reduced dimm - buffer adicional de direcciones y datos, aislamiento máximo, módulos con mucha memoria. Tienen ECC.
>
> Specs:
>
> - DDR4
> - PC4 - otra forma de indicar que DDR4
> - 21300 - ancho de banda máximo
>   - Ancho de banda máximo = num transferencias/seg * 64 bits/transferencia
> - CL - latencia de memoria, cuánto se tarda en acceder al primer dato (hay muchas latencias, pero la del primer dato es la más importante)
> - Dual ranked - doble rango, dos memorias independientes (o sea que este es de 32, pero para el ordenador son dos de 16)
> - x4 based - cada chip da 4 bits. Necesitamos 72.
> - 2666 - frecuencia efectiva de reloj, se mide en MHz. Si pusiera 1, sería un millón. 
>   - O sea que tenemos 2666 datos por segundo - a partir de esta info, debemos poder calcular el ancho de banda máximo
>
> Voltaje: no hay que sabérselo, pero sí hay que saber que a mejor tecnología, menor voltaje.



![image-20230114120559988](/home/clara/.config/Typora/typora-user-images/image-20230114120559988.png)

### Ranuras de expansión

Permiten la conexión a la placa base de otras tarjetas de circuito impreso. Añaden funcionalidades extra.

![image-20230114122048644](/home/clara/.config/Typora/typora-user-images/image-20230114122048644.png)

> ISA: Mini interruptores (jumpers) para interrupción específica, desaparece cuando llegan otras tecnologías más avanzadas. Manualmente cambiabas con una aguja o algo pequeño una serie de interruptores/jumpers para codificar cierta interrupción.

#### Interfaces PCI y AGP

- PCI (Peripheral Component Interconnect). Intel

- > Aún se ven, pero cada vez menos. Bus paralelo de 32b (existe la de 64, pero poco popular) -- bus compartido entre dos tarjetas, es como un banco de memoria de ranuras PCI (así que si pongo dos no puedo hablarles simultáneamente, no son 32R, 32W, son 64 compartidos)

  - Bus PARALELO: 32b /64b. Half-duplex. 

  - Conectores de 128/188 patillas. 

  - Plug and Play. 

  - > No tienen jumpers, no es hotswappable; pero no necesita configuración manual para que el ordenador la vea

  - Capacidad: 

    - 33MHz, 32b (4B) → 133MBps. 
    - 66MHz, 32b (4B) → 266MBps. 
    - 66MHz, 64b (8B) → 533MBps. 

  - Versión PCI-X → SERVIDORES:

    - 64b (8B), 133MHz ⇒ ≅ 1GBps.

- AGP (Accelerated Graphics Port). Intel. 

- > Nuevo estándar para ranuras de expansión, pensado para tarjetas gráficas. Como es para una cosa específica (gráficos), se simplifica el protocolo, y eso permite más capacidad

  - Bus PARALELO: 32b. Half-duplex. 
  - Conectores de 132 patillas. 
  - Uso: tarjeta gráfica. 
  - AGP 8x → 2GBps.

##### Interfaz PCIe

> La PCIe deja obsoletos a PCI y AGP.

- **Características**: 

  - Conexión serie punto a punto (no es un bus con líneas compartidas) por medio de varias “LANES”.

  - > Serie: no es paralelo - ¿cómo es posible que sea más rápido que PCI, que sí es paralelo?
    >
    > Punto a punto: la respuesta a por qué es más rápido - es porque no es compartido, tiene acceso independiente por varias lanes a cada parte.

  - Cada LANE está compuesta por 4 cables, 2 por cada sentido de la transmisión. Full-Duplex. 

  - > Dos cables para enviar, dos para recibir. Es full duplex porque, al existir esta división, puedo enviar y recibir simultáneamente.

  - Transmisión SÍNCRONA estando el reloj embebido en los datos. Hot plug. Virtualización de E/S. 

  - > La transmisión síncrona es lo gordo: no tengo los datos en un lado y la señal de reloj en otro, sino que es uno, reloj+dato
    >
    > Entonces en cada lane hay una señal de reloj independiente

    > Encima es hot pluggable, mejor aún que plug and play

  - Codificación: 8b/10b (versiones 1.x 2.x), y 128b/130b (versiones 3.0 y 4.0). 

  - > Codificación 8b/10b: redundancia adicional, por cada 10b de info transmitidos, 8 son de info (así que 2 son de redundancia) - particularmente importante por aquello de que va en serie la info

  - Escalable. El número de LANES se negocia con el dispositivo.

- Versiones y velocidades (por cada sentido de cada LANE): 

  - PCIe 1.1: hasta 2,5GT/s (250 MB/s) 
  - PCIe 3.0: hasta 8GT/s (~1GB/s) 
  - PCIe 2.0: hasta 5GT/s (500 MB/s) 
  - PCIe 4.0: hasta 16GT/s (~2GB/s)

- Número de LANES habituales: 

  - x1, x2, x4, x8, x16.

- PCIe x16: uso en tarjetas gráficas 

  - PCIe x16 (3.0) : hasta 16GBps en cada sentido.

  - PCIe x16 (4.0) : hasta 32GBps en cada sentido.

    > Mucho mejor ancho de banda que con el AGP, incluso si este era específico y todo - todo eso siendo en serie.
    >
    > Encima es retrocompatible y compatible hacia delante, se negocia la capacidad.
    >
    > 32Gbps es una brutalidad, PCI y AGP simplemente no pueden competir.

Ventajas de una interfaz serie (reloj embebido) con respecto a una paralela (reloj común): Mayor frecuencia de reloj (evita timing skew), menor nº de pistas para un rendimiento similar, mayor escalabilidad, facilita conexiones full duplex.

> Timing skew: no todos los bits llegan al mismo tiempo, porque están físicamente a distintas distancias en la placa base. Si tienen "curvas" tardan más. Se compensa con curvas adicionales, que ralentizan las pistas más rápidas para que todos lleguen igual (segunda imagen)
>
> ![image-20230114125634588](/home/clara/.config/Typora/typora-user-images/image-20230114125634588.png)

## 2.6. Almacenamiento y E/S

### Almacenamiento permanente (no volátil)

- Tipos: 
  - Magnéticos: HDD (Hard Disk Drives), Cintas. 
  - Ópticos: CD, DVD, Blu-Ray (BD). 
  - NVRAM: SSD (Solid State Drives). 
- Factores de forma: (en pulgadas) 
  - 8, 5.25, 3.5, 2.5, 1.8, 1, 0.85 
  - Más utilizados: 3.5, 2.5, 1.8 
- Conexión discos – placa base. 
  - ATA (conector IDE), SATA. 
  - SCSI, SAS. 
  - PCIe, SATAe, M.2, U.2. 
  - Ethernet, USB. 
  - Fibre Channel, Infiniband.

#### Discos duros (HDD Hard Disk Drives)

- Almacenamiento permanente (no volátil) a lo largo de la superficie de unos discos recubiertos de material magnético. 

- La lectura y escritura se realiza a través de unos cabezales magnéticos controlados por un brazo motor y la ayuda del giro de los discos. 

- Los datos se distribuyen en pistas. Cada pista se subdivide en sectores de 512 bytes. Los sectores se agrupan en clusters lógicos. 

- Tiempos de respuesta (latencias) muy variables: dependen de la pista y el sector concretos donde esté el cabezal y el sector concreto al que se quiere acceder. 

  > La latencia depende de donde está el cabezal, donde está el sector y velocidad de rotación -- mucho más lento que la memoria volátil, claro

- Velocidades de rotación más habituales (r.p.m.): 5400, 7200, 10000, 15000.

![image-20230114172121580](/home/clara/.config/Typora/typora-user-images/image-20230114172121580.png)

#### Unidades de estado sólido (SSD, Solid State Drives)

- Almacenamiento no volátil distribuido en varios circuitos integrados (chips) de memoria flash (=basados en transistores MOSFET de puerta flotante).

  > Mismo chasis que el disco duro para mejor compatibilidad
  >
  > No hace falta que sepamos mucho de transistores, pero estos tienen un metal adicional de tal forma que, si le metemos un cierto voltaje, podemos conseguir que los electrones atraviesen la capa de dióxido y se queden atrapados, bloqueando el paso de los electrones (corte), 0s y 1s.
  >
  > Si grabo y regrabo muchas veces, la capa de dióxido se deteriora, así que no son una forma de almacenamiento no volátil muy fiable -- por ejemplo, un pen drive que falla después de 10 años

- Tipos de celdas habituales: SLC (single-level cell), MLC (multi-level cell).

  >  MLC: meter varios niveles lógicos en una celda -- eso lo hace menos fiable con el tiempo (explicado abajo).

- Acceso aleatorio: mismo tiempo de respuesta (latencia) independientemente de la celda de memoria a la que se quiere acceder (NVRAM, Non-volatile RAM). 

  > RAM no volátil, la latencia es la misma sin importar la celda, no como en un disco duro.

- Un controlador se encarga de distribuir la dirección lógica de las celdas de memoria para evitar su desgaste tras múltiples re-escrituras (wear levelling).

  > Si el controlador ve que hay una celda que se escribe demasiadas veces, cambia las direcciones de memoria para distribuir el desgaste entre celdas (wear leveling) - por eso cuanto más vacío está un SSD, mejor y  más fiable: el controlador tiene más libertad para redistribuir las celdas.

![image-20230114172801575](/home/clara/.config/Typora/typora-user-images/image-20230114172801575.png)

##### Comparación HDD SDD de precios similares

|                               | hdd                                                          | ssd                           |
| ----------------------------- | ------------------------------------------------------------ | ----------------------------- |
| Formato                       | 3.5 pulgadas                                                 | 2.5 pulgadas                  |
| Interfaz                      | SATA 6Gb/s                                                   | SATA 6Gb/s                    |
| Capacidad                     | 12TB                                                         | 2TB                           |
| Ancho de banda max            | 255Mb/s                                                      | 560Mb/s                       |
| Latencias medias aprox        | pocos ms                                                     | decenas de µs                 |
| Consumo de potencia           | 5W en reposo, 7W max                                         | 0.056W en reposo,  3.8W max   |
| MTTF                          | 2.5 millones de horas                                        | 1.75 millones de horas        |
| Garantía                      | 5 años                                                       | 3 años                        |
| Temperatura de funcionamiento | 5-60ºC                                                       | 0-70ºC                        |
| Peso                          | 660gr                                                        | 57.9gr                        |
| Otras características         | Velocidad de rotación 7200RPM<br />Ruido 36dbA<br />Caché 256MB | Terabytes written (TBW) 500TB |

> Muchos de estos rasgos son generalizables y, por tanto, susceptibles a ser preguntas de teoría:
>
> - Formato/Tamaño: El HDD suele ser más grande
> - Capacidad: El HDD suele tener más porque el SSD es más caro por bit
> - Latencia: El SSD suele ser más rápido
> - MTTF (Mean Time To Failure), es una medida de tolerancia a fallos: SSD suele tardar más, pero MTTF no es un dato muy fiable
> - Peso: el HDD pesa más
> - Terabytes written (TBW) tampoco es una medida demasiado fiable

> Sobre el ancho de banda máximo: comprobar si el ancho de banda es suficiente para el ancho de banda máximo del almacenamiento. Leer más en el apartado de SATA.

#### Raid (Redundant Array of Independent Disks)

Combinan diversas unidades de almacenamiento (normalmente de idénticas características) para generar unidades de almacenamiento lógicas con mayor tolerancia a fallos, fiabilidad y/o ancho de banda. 

Algunas configuraciones:

![image-20230114182256288](/home/clara/.config/Typora/typora-user-images/image-20230114182256288.png)

Se puede implementar por software o por hardware. RAID por software: Creado por el propio sistema operativo. RAID por hardware: Mediante una tarjeta específica (hay placas base que ya incluyen un controlador de este tipo).

![image-20230114182331106](/home/clara/.config/Typora/typora-user-images/image-20230114182331106.png)

#### Unidades ópticas

Almacenan la información de forma permanente (no volátil) a través de una serie de surcos en un disco que pueden ser leídos por un haz de luz láser. Los discos compactos (CD), discos versátiles digitales (DVD) y discos Blu-ray (BD) son los tipos de medios ópticos más comunes que pueden ser leídos y grabados por estas unidades.

![image-20230114183529985](/home/clara/.config/Typora/typora-user-images/image-20230114183529985.png)

#### Unidades de cinta (tape drives)

> Según lo que queramos almacenar nos convendrá más un tipo de dispositivo u otro. Pongamos backups: tenemos que guardar cientos de logs, que normalmente no usaremos ni revisaremos, pero necesitamos por si hay algún problema.
>
> SSD descartadísimo, porque es caro y además se degrada. Disco duro también, es matar moscas a cañonazos. Unidades ópticas tampoco, escribir un DVD es lento y si son regrabables también se degradan con la reescritura, además mal ancho de banda.
>
> Así que se usan unidades de cinta. Una tecnología que nos parece muy arcaica, pero en realidad sigue siendo muy relevante hoy día.

Almacenan la información de forma permanente (no volátil) a través de una cinta recubierta de material magnético que se enrolla por medio de carretes. Las latencias suelen ser muy altas ya que hay que rebobinar la cinta hasta que el cabezal se encuentre en la posición deseada. 

Es el medio con la mayor densidad de bits para un área dada. Actualmente, permiten almacenamiento de decenas de TB por cinta y velocidades de lectura secuencial en torno a 150 MBps. 

Es el medio de almacenamiento masivo más barato. Se usan normalmente como almacenamiento de respaldo (backup) y archivado.

> Si se rompe un disco duro suele ser el cabezal. Las cintas no valen nada, guardan la información en si mismas, así que la inversión se hace en el lector.
>
> Buen ancho de banda secuencial, pero latencia muy larga (rebobinar la cinta lleva mucho tiempo). Pero el backup no tiene que ser rápido de lectura, tiene que ser fiable en escritura.

### Interfaces

#### Interfaz P-ATA (Parallel ATA)

> No se usa, lo estudiamos por perspectiva histórica

ATA: Advanced Technology Attachment 

- Paralelo: bus de datos de 16b. 
- Half-duplex. 
- 2 dispositivos por conector que comparten el mismo bus. 
- Distancia máxima: 45.7cm 
- Versiones ATA: ATA33, ATA66, ATA100, ATA133 
  - Velocidad de transferencia (máxima): 33MBps, 66MBps, 100MBps, 133MBps.

![image-20230114184710379](/home/clara/.config/Typora/typora-user-images/image-20230114184710379.png)![image-20230114184723057](/home/clara/.config/Typora/typora-user-images/image-20230114184723057.png)

#### Interfaz SATA (Serial ATA)

- Conexión serie punto a punto (1 disco por conector). 
- Reloj embebido en los datos. Half-duplex. Hot-plug.
- Codificación 8b/10b. Longitud del cable: 1m (2m e-SATA). 
  - Protocolo AHCI (Advanced Host Controller Interface): NCQ (Native Command Queueing).

![image-20230114194541388](/home/clara/.config/Typora/typora-user-images/image-20230114194541388.png)

> De nuevo, renunciamos a hacer el tonto con paralelos y nos pasamos al punto a punto.

> Half duplex, no se consideró necesario escribir y leer a la vez un disco duro.
>
> TÍPICA PREGUNTA: no siempre los serie son full duplex, ni los paralelos half duplex

> EL SATA LIMITA EL ANCHO DE BANDA A 600MB/s, si miramos el SDD de la comparativa en puntos anteriores, tiene 560. O sea, al límite.

> En la imagen de la esquina superior derecha, los pines a la izda son los de datos y los de la dcha la alimentación.

#### Interfaces SCSI y SAS

> Interfaces específicas a servidores

- SCSI: Small Computer System Interface. 

  - Paralelo: 16b. HALF-DUPLEX. Hot-plug. 
  - Permite conectar varias unidades en cadena (daisy-chain). 
  - Ultra-SCSI: Hasta 320MBps, 16 dispositivos, 12m cable. 

- SAS (Serial Attached SCSI). 

  > Siglas comúnmente preguntadas

  - Conexión serie punto a punto. 
  - Reloj embebido en los datos. 
  - Full-duplex. Hot-plug. Codificación 8b/10b. 
  - Frecuencias de 3, 6 y 12 GHz: hasta 1200 MB/s (1,2 Gpbs). 
  - Compatible con discos SATA. 
  - Mayores voltajes que SATA. 
    - Longitud del cable máxima: 10m.

  >  SAS no merma las caracteristicas, y es compatible con discos SATA, tienen el mismo conector - el protocolo SAS es compatible con SATA, no al revés.
  >
  > Normalmente la gama baja solo tiene para SATA, mientras que los servers tienen SAS.

  

##### Conexión de múltiples unidades SAS

![image-20230114195314473](/home/clara/.config/Typora/typora-user-images/image-20230114195314473.png)

![image-20230114195935714](/home/clara/.config/Typora/typora-user-images/image-20230114195935714.png)

> SAS expander: es como un switch, gastar un miniSAS para llevarte múltiples unidades SAS
>
> Sobre las unidades de almacenamiento: mínimo que sean hot plug, deseable hot swap - Fáciles de meter y sacar gracias al SAS backplane, te quitas de cables

#### NVME (Non-Volatile Memory Express)

Es un protocolo para el acceso a SSD conectadas a través de PCIe.

> SAS está bien, es el salto que SATA no dio, pero 1200Mb/s puede quedarse corto para algunos SSD
>
> PCIe es sumamente escalable y una sola lane puede llegar a 1Gb/s, así que podemos stackearlos para conseguir una velocidad muy buena
>
> SATA está diseñado para HCI, en SSD no hay pistas y se puede hacer R/W paralelo, lo cual no es posible con HCI - por eso surge el protocolo NVMe, diseñado específicamente para SSD, escalable a cualquier número de colas en paralelo.

![image-20230114200413355](/home/clara/.config/Typora/typora-user-images/image-20230114200413355.png)

> M.2 NVMe: forma alternativa de pinchar un SSD en la placa, sin gastar el PCIe. Ocupa espacio, así que se ponen 1 o 2 en la placa para un uso específico (ej. cargar el SO).
>
> Usa PCIe a no ser que se especifique lo contrario, pero existen m2 NVMe que usan SATA, así que ojito.

#### Conectores panel trasero

>  La placa posterior es una protección contra el polvo. Hay que instalarla al principio, si se te olvida hay que desmontarlo todo.
>
> Todo está estandarizado, el chasis tiene ya el agujero.

> TÍPICA PREGUNTA: ¿este panel trasero pertenece a un servidor? Buscar HDMI, VGA... salidas de vídeo y audio de altas prestaciones son innecesarias en un server. Aunque, a veces, los servidores traen un VGA por si hay que manejar algo, pero nada más.

Panel trasero PC:

![image-20230114193329387](/home/clara/.config/Typora/typora-user-images/image-20230114193329387.png)

Panel trasero server:

![image-20230114193639877](/home/clara/.config/Typora/typora-user-images/image-20230114193639877.png)

#### Ethernet

Familia de tecnologías para crear redes de área local estandarizada desde los años 80 como IEEE 802.3. 

- Permite conexiones a largas distancias: 
  - Unos 100 m con par trenzado (conector RJ-45). 
  - Varios km con fibra óptica (usando adaptadores de señal llamados transceptores o transceivers). 
- Varios estándares, todos ellos compatibles unos con otros y todos pueden ser full-duplex. 
  - Ethernet clásico: 10 Mbit/s (10BASE-T). 
  - Fast-Ethernet: 100 Mbit/s (100BASE-T). 
  - Gigabit-Ethernet: 1Gbit/s (1000BASE-T). 
  - 10G-Ethernet: 10Gbit/s (10GBASE-T). 
- Muchos estándares de comunicación se pueden realizar a través de Ethernet de forma más barata: 
  - iSCSI (Internet SCSI): estándar que permite el uso del protocolo SCSI sobre redes TCP/IP. 
  - FCoE (Fibre Channel over Ethernet): estándar que permite el uso de tramas Fibre Channel sobre TCP/IP.

#### USB (Universal Serial Bus)

- USB 2.0 
  - Puerto serie: 4 pines 
    - 2 datos (diferencial), masa y alimentación. 
  - Velocidad (2.0): hasta 480Mbps (60MBps). 
  - Hasta 127 dispositivos. Hot plug. Half-duplex. 
- USB 3.0 (Superspeed) 
  - 9 pines (compatible con 2.0): 
    - 4 pines: USB 2.0 
    - 5 pines: alta velocidad (datos →2+2, GND): Full-duplex. 
  - Velocidad: hasta 4,8Gbps (600MBps). 
  - Codificación 8b/10b. 
  - Intensidad para recarga de dispositivos: 900mA (500 mA USB 2.0). 
  - Distancias de 5m (cobre) a 50m (fibra óptica). 
  - Su sucesor: USB 3.1 (Superspeed+) hasta 10Gbps, 128/132b encoding.

#### Otros conectores Internos

![image-20230114185204504](/home/clara/.config/Typora/typora-user-images/image-20230114185204504.png)

> PREGUNTA TÍPICA: System panel - conecta el chasis con la placa base 
>
> Todo chasis tiene que tener un botón de encendido, pues esto es lo que conecta la placa y la fuente con el chasis

#### ROM/Flash BIOS (Basic IO System)

Almacena el código de arranque (boot) del computador. Este código se encarga de identificar los dispositivos instalados, instalar drivers básicos para acceder a los mismos, realizar el Power-on self-test (POST) del sistema e iniciar el S.O. 

Los parámetros de configuración de la placa se almacenan en una memoria RAM alimentada por una pila (que también se usa para el reloj en tiempo real) o en una memoria flash. Algunos de esos parámetros se seleccionan mediante jumpers en la propia placa, pero la mayoría se configuran a través de un programa especial al que se puede acceder antes de arrancar el S.O.

![image-20230114185049193](/home/clara/.config/Typora/typora-user-images/image-20230114185049193.png)

## 2.7. Chipset

El chipset es el conjunto de circuitos integrados (chips) de la placa base encargados de controlar la comunicación entre los diferentes componentes de la placa base. Un chipset se suele diseñar para una familia específica de microprocesadores.

El juego de chips suele estar distribuido en dos componentes principales: 

- El puente norte (north bridge), encargado de las transferencias de mayor velocidad (principalmente con el microprocesador, la memoria, la tarjeta gráfica y el puente sur). 
- El puente sur (south bridge), encargado de las transferencias entre el puente norte y el resto de periféricos con menores exigencias de velocidad de la placa.

Esquema básico de un chipset:

![image-20230114163811280](/home/clara/.config/Typora/typora-user-images/image-20230114163811280.png)

Otros posibles esquemas ![image-20230114163839894](/home/clara/.config/Typora/typora-user-images/image-20230114163839894.png)

System on a chip (SoC): desaparece el chipset:

![image-20230114164124227](/home/clara/.config/Typora/typora-user-images/image-20230114164124227.png)

## 2.8. Centros de procesamiento de datos

### La carcasa o chásis

La “unidad rack” (U) se usa para describir la altura del equipamiento preparado para ser montado en un rack (armario) de 19 o 23 pulgadas de ancho (48,26 cm o 58,42 cm). Una unidad rack equivale a 1,75 pulgadas (4,445 cm) de alto.

![image-20230114164246418](/home/clara/.config/Typora/typora-user-images/image-20230114164246418.png)

Algunos servidores de torre disponibles comercialmente:

- IBM/Lenovo
- Fujitsu
- HP
- Azken Muga

#### Placas para servidores 

> Las pongo como referencia de imágenes, en ellas se pueden ubicar los componentes estudiados en el tema

![image-20230114164333006](/home/clara/.config/Typora/typora-user-images/image-20230114164333006.png)

![image-20230114164345810](/home/clara/.config/Typora/typora-user-images/image-20230114164345810.png)

![image-20230114164355280](/home/clara/.config/Typora/typora-user-images/image-20230114164355280.png)

### Blade servers

Se trata de un gran armazón (blade enclosure) en el que se van añadiendo, a modo de “láminas” (blades) computadores “compactos” (server blades) que tienen el espacio estrictamente necesario para procesadores, módulos de DRAM y, según el modelo, un pequeño almacenamiento local. 

Este gran armazón proporciona a los server blades, entre otras cosas, alimentación, refrigeración y conectores externos para USB y Ethernet.

![image-20230114164501110](/home/clara/.config/Typora/typora-user-images/image-20230114164501110.png)

![image-20230114164508368](/home/clara/.config/Typora/typora-user-images/image-20230114164508368.png)

## Algunas preguntas tipo

- HDD o SSD, cuál tiene mayor latencia? 

  - HDD

- Qué papel realizan transistores, bobinas y condensadores que aparecen al lado del zócalo del microprocesador de la placa base? 

  - Módulo regulador del voltaje VRM, convertir la corriente continua a las tensiones continuas que necesitan la CPU y la DRAM. También le da estabilidad a esta tensión continua

- Cómo sabe el chasis de la placa base que el equipo está encendido?

  ​	a. conector nGFF

  ​	**b. System panel**

  ​	c. Bus PCI

- V/F: los módulos de memoria DDR4 tienen un bus de datos de mayor número de bits que los DDR3

  - Falso, mismo numero bits (64). DDR2, en cambio, si que tiene menos que DDR3

- V/F: AHCI está especialmente diseñado para HDD.  

  - Verdadero, es el protocolo de SATA y no tiene en cuenta las ventajas del SSD porque tiene una única cola.

- MUY TÍPICA: 3 diferencias entre DRAM y SRAM:

  ​	1.DRAM necesita refresco

  ​	2.DRAM es mucho más simple que SRAM, así que tiene mayor densidad de bits. Por eso es más barata, pero también más lenta.

  ​	3.DRAM va fuera de microprocesador, SRAM está dentro del microprocesador, es la caché.

- Distinguir tarjetas de expansión, PCI, PCIe... sólo con la imagen