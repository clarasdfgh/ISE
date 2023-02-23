# ISE 22/23 - Práctica 4: Benchmarking y ajuste del sistema

###### Clara María Romero Lara

![https://transparente.ugr.es/themes/custom/ugr/screenshot.png](https://transparente.ugr.es/themes/custom/ugr/screenshot.png)





## Ejercicio 1: Phoronix

> Una vez que haya indagado sobre los benchmarks disponibles en Phoronix, seleccione como mínimo dos de ellos y proceda a ejecutarlos en Ubuntu y CentOS. Comente las diferencias.

### Instalación de Phoronix

Seguiremos [estas instrucciones](https://linuxconfig.org/how-to-install-the-latest-phoronix-test-suite-on-ubuntu-18-04-bionic-beaver). Para instalar Phoronix vamos a necesitar wget y, como dependencia, PHP. Así que nos aseguraremos de instalarlas en ambas máquinas antes de seguir.

Luego, descargaremos con wget el paquete de Phoronix. Podemos comprobar que se ha instalado correctamente ejecutando el comando `phoronix-test-suite list-available-tests` , que lista los benchmark disponibles.

##### UbuntuServer

```
> wget https://phoronix-test-suite.com/releases/repo/pts.debian/files/phoronix-test-suite_10.8.4_all.deb
> sudo apt install ./phoronix-test-suite_10.8.4_all.deb
> phoronix-test-suite list-available-tests
```

![image-20230113050726703](/home/clara/.config/Typora/typora-user-images/image-20230113050726703.png)

![image-20230113050737361](/home/clara/.config/Typora/typora-user-images/image-20230113050737361.png)

##### Rocky

```
> wget https://phoronix-test-suite.com/releases/phoronix-test-suite-10.8.4.tar.gz
> tar -xf phoronix-test-suite-10.8.4.tar.gz 
> cd phoronix-test-suite
> sudo ./install-sh
> phoronix-test-suite list-available-tests
```

![image-20230113050747447](/home/clara/.config/Typora/typora-user-images/image-20230113050747447.png)

![image-20230113050803156](/home/clara/.config/Typora/typora-user-images/image-20230113050803156.png)

### Ejecución benchmark 1: CacheBench

[CacheBench](https://openbenchmarking.org/test/pts/cachebench) es el benchmark más descargado de Phoronix. Es un test para medir el ancho de banda de la memoria caché.

```
> phoronix-test-suite benchmark cachebench
```

![image-20230113053353838](/home/clara/.config/Typora/typora-user-images/image-20230113053353838.png)

![image-20230113054048758](/home/clara/.config/Typora/typora-user-images/image-20230113054048758.png)

![image-20230113054106621](/home/clara/.config/Typora/typora-user-images/image-20230113054106621.png)

### Ejecución benchmark 2: Sudoku

El test [Sudoku](https://openbenchmarking.org/result/2212056-NE-SUDOKUTES70) mide el tiempo que tarda el sistema en resolver 100 sudokus.

```
 > phoronix-test-suite benchmark 2212056-NE-SUDOKUTES70
```

![image-20230113052347411](/home/clara/.config/Typora/typora-user-images/image-20230113052347411.png)

![image-20230113052456673](/home/clara/.config/Typora/typora-user-images/image-20230113052456673.png)

![image-20230113052527916](/home/clara/.config/Typora/typora-user-images/image-20230113052527916.png)