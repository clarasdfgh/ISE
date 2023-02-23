# ISE 22/23 - Práctica 3: Monitorización, Automatización y Profiling

###### Clara María Romero Lara

![https://transparente.ugr.es/themes/custom/ugr/screenshot.png](https://transparente.ugr.es/themes/custom/ugr/screenshot.png)





## Ejercicio 1: Zabbix

> Realice una instalación de Zabbix 5.0 en su servidor con Ubuntu Server20.04 y configure para que se monitorice a él mismo y para que monitorice a la máquina con CentOS. Puede configurar varios parámetros para monitorizar, uso de CPU, memoria, etc. pero debe configurar de manera obligatoria la monitorización de los servicios SSH y HTTP. 

### Instalación en UbuntuServer

Partimos de la configuración previa de la P1L1, Ubuntu 20.04 con configuración de red y de ssh completa. Nos conectaremos a la máquina virtual desde nuestra máquina host mediante SSH, para mayor comodidad.

Lo primero será acceder a la [página oficial de Zabbix y rellenar nuestras especificaciones](https://www.zabbix.com/download?zabbix=5.0&os_distribution=ubuntu&os_version=20.04&components=server_frontend_agent&db=mysql&ws=apache). La página generará automáticamente las instrucciones para la instalación en el sistema.

- Instalar el repositorio:

  ```
  > wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1%2Bfocal_all.deb
  > dpkg -i zabbix-release_5.0-1+focal_all.deb
  > apt update 
  ```

  ![image-20230104123944154](/home/clara/.config/Typora/typora-user-images/image-20230104123944154.png)

  ![image-20230104124010262](/home/clara/.config/Typora/typora-user-images/image-20230104124010262.png)

- Instalar el server, frontend y agente:

  ```
  > apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent
  ```

- Crear base de datos inicial

  - Nuestra primera incidencia: tenemos que hacer un pequeño inciso para [instalar MySQL](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-20-04), ya que en la práctica 2 la pila LAMP se trabajó en CentOS. 

    ```bash
    > sudo apt install mysql-server
    > sudo systemctl start mysql.service
    > sudo mysql
    >> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
    >> exit
    > mysql-secure-installation
    > mysql -u root -p
    >> ALTER USER 'root'@'localhost' IDENTIFIED WITH auth_socket;
    ```

    ![image-20230104124033808](/home/clara/.config/Typora/typora-user-images/image-20230104124033808.png)

  - Una vez instalado, continuamos con la creación de la base de datos inicial.

  ```
  > mysql -u root -p
  >> create database zabbix character set utf8 collate utf8_bin;
  >> create user zabbix@localhost identified by 'password';
  >> grant all privileges on zabbix.* to zabbix@localhost;
  >> set global log_bin_trust_function_creators = 1;
  >> quit;
  ```

  ![image-20230104124051800](/home/clara/.config/Typora/typora-user-images/image-20230104124051800.png)

- Importar el esquema inicial del server de Zabbix. Tardará un poco. Para comprobar si se ha importado correctamente, podemos volver a ejecutar el comando.

  ```
  > zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix 
  ```

- Deshabilitamos  `log_bin_trust_function_creators`

  ```
  > sudo mysql
  >> set global log_bin_trust_function_creators = 0;
  >> quit;
  ```

- Configuramos la base de datos, cambiamos la contraseña en `etc/zabbix/zabbix_server.conf`. Descomentamos la línea pertinente y escribimos la contraseña elegida.

  ```
  # DBPassword=password
  ```

- Configuramos /etc/zabbix/apache.conf y escogemos la timezone adecuada.						

  ```
  # php_value date.timezone Europe/Madrid 
  ```

  ![image-20230104124131876](/home/clara/.config/Typora/typora-user-images/image-20230104124131876.png)

- Finalmente, arrancamos los servicios con `systemctl`

  ```
  systemctl restart zabbix-server zabbix-agent apache2
  systemctl enable zabbix-server zabbix-agent apache2
  ```

  ![image-20230104124303341](/home/clara/.config/Typora/typora-user-images/image-20230104124303341.png)

### Inicio rápido

Primero [accedemos a la interfaz web de Zabbix](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-zabbix-to-securely-monitor-remote-servers-on-centos-7) mediante un navegador, accediendo a 192.168.56.105/zabbix (la IP correspondiente a UbuntuServer)

![image-20230104124315399](/home/clara/.config/Typora/typora-user-images/image-20230104124315399.png)

Y seguimos la [guía de inicio rápido](https://www.zabbix.com/documentation/5.0/en/manual/quickstart/login) provista en la documentación:

![image-20230104124403859](/home/clara/.config/Typora/typora-user-images/image-20230104124403859.png)

![image-20230104124411415](/home/clara/.config/Typora/typora-user-images/image-20230104124411415.png)

![image-20230104124415364](/home/clara/.config/Typora/typora-user-images/image-20230104124415364.png)

![image-20230104124419689](/home/clara/.config/Typora/typora-user-images/image-20230104124419689.png)

![image-20230104124430066](/home/clara/.config/Typora/typora-user-images/image-20230104124430066.png)

![image-20230104124437202](/home/clara/.config/Typora/typora-user-images/image-20230104124437202.png)

Continuamos con la configuración de nuestro ejercicio:

- Introducimos las credenciales, incluyendo la contraseña tal y como la configuramos anteriormente:

  ![image-20230104124457621](/home/clara/.config/Typora/typora-user-images/image-20230104124457621.png)

  - Este paso tuvo un par de incidencias:  

    - El servicio de zabbiz no iniciaba (En el panel de control: `Zabbix server is running:	No`). Se solucionó [profundizando en el concepto del server](https://www.zabbix.com/documentation/current/en/manual/concepts/server) e iniciándolo:

      ```
      service zabbix-server start
      ```

      Además, en el archivo de configuración hubo una errata con la contraseña, por lo que los primeros login no tuvieron éxito. Finalmente, pudimos acceder al panel de control.

      ![image-20230104124510910](/home/clara/.config/Typora/typora-user-images/image-20230104124510910.png)

- [Para monitorizarnos a nosotros mismos](https://www.zabbix.com/documentation/5.0/en/manual/quickstart/host), tenemos que crear un nuevo host. Nos vamos a configuración, hosts y create host.

  ![image-20230104124957749](/home/clara/.config/Typora/typora-user-images/image-20230104124957749.png)

- Todos los cambios hasta el momento se han de reflejar en el archivo de configuración del agente de Zabbix `/etc/zabbix/zabbix_agentd.conf`. 

  - Añadimos la ip del server desde el que vamos a *trackear* (la de ubuntu, puesto que se está monitorizando a sí mismo). Está en la parte del archivo correspondiente a server.
  - En la parte de host name, ponemos el nombre que hemos puesto en la interfaz web (UbuntuServer).
  - Aplicamos los cambios reiniciando los servicios.



### Instalación rocky

De nuevo, seguiremos las instrucciones que nos proporciona la [página de Zabbix según nuestros parámetros](https://www.zabbix.com/la/download?zabbix=5.0&os_distribution=rocky_linux&os_version=9&components=agent&db=&ws=).

-  Instalar el repositorio de Zabbix, pero primero deberíamos deshabilitar los paquetes de Zabbix que nos ofrezca yum por precaución.								

  ```
> excludepkgs=zabbix* 
  > rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/9/x86_64/zabbix-release-5.0-3.el9.noarch.rpm 
  >dnf clean all 
  ```

  ![image-20230104125039768](/home/clara/.config/Typora/typora-user-images/image-20230104125039768.png)

- Creamos un host mediante la interfaz web, en el grupo linux servers y con la IP de Rocky. Lo llamamos RockyServer.

  ![image-20230104125014997](/home/clara/.config/Typora/typora-user-images/image-20230104125014997.png)

  -  En el archivo de configuración del agente de Zabbix `/etc/zabbix/zabbix_agentd.conf` añadimos la IP de Rocky. En la parte de *host name*, ponemos el nombre que hemos puesto en la interfaz web (RockyServer).

  -  Aplicamos los cambios reiniciando los servicios

- Finalmente, configuramos el firewall para habilitar los puertos que vamos a emplear

  ```
  > sudo firewall-cmd --add-port=10050/tcp --permanent
  > sudo firewall-cmd --add-port=22/tcp --permanent
  > sudo firewall-cmd --add-port=80/tcp --permanent
  > sudo firewall-cmd --reload
  ```

  

### Monitorización de SSH y HTTP

Nos vamos a Configuración>Hosts y entramos al host UbuntuServer. Seleccionamos la opción Templates, y añadimos las de ssh y http.

![image-20230104125118584](/home/clara/.config/Typora/typora-user-images/image-20230104125118584.png)

![image-20230104125123852](/home/clara/.config/Typora/typora-user-images/image-20230104125123852.png)



Accedemos al menú Configuración>Templates, y buscamos las Templates recién añadidas. Seleccionamos Items, y añadimos las siguientes *keys*:

- En SSH: `net.tcp.service[ssh,,22022]`
- En HTTP: `net.tcp.service[http,,80]`

![image-20230104125325212](/home/clara/.config/Typora/typora-user-images/image-20230104125325212.png)



Para comprobar si realmente está funcionando la monitorización, instalaremos y ejecutaremos `zabbix-get`. 

Durante la instalación de zabbix-get, hemos tenido [problemas con el DNS](https://www.tecmint.com/resolve-temporary-failure-in-name-resolution/). Estos se resolvieron añadiendo el server DNS de Google al archivo `/etc/resolv.conf` y reiniciando el servicio `systemd-resolved`.

Ejecutamos `zabbix-get` pasando como parámetros la IP, el puerto (10050, el que asignamos a Zabbix) y el servicio. Un 0 indica que el servicio está caído, y un 1 que está activo.

```
> zabbix_get -s 192.168.56.105 -p 10050 -k net.tcp.service[ssh,,22022]
> zabbix_get -s 192.168.56.105 -p 10050 -k net.tcp.service[http,,80]
> zabbix_get -s 192.168.56.110 -p 10050 -k net.tcp.service[ssh]
> zabbix_get -s 192.168.56.110 -p 10050 -k net.tcp.service[http]
```

![image-20230104125726748](/home/clara/.config/Typora/typora-user-images/image-20230104125726748.png)

Podemos tirar y reiniciar un servicio y comprobar desde el panel de control de Zabbix la gráfica de monitorización del servicio, en este caso, SSH desde UbuntuServer:

![image-20230104125805634](/home/clara/.config/Typora/typora-user-images/image-20230104125805634.png)



## Ejercicio 2: Ansible

> Usted deberá saber cómo instalar y configurar Ansible para poder hacer un ping a las máquinas virtuales de los servidores y ejecutar un comando básico (p.ej. el script de monitorización del RAID1). También debe ser consciente de la posibilidad de escribir acciones más complejas mediante playbooks escritos con YAML como, por ejemplo, asegurarse de que tenemos la última verisón instalada de httpd y que está en ejecución.

### Instalación de Ansible y conexión con las máquinas virtuales

Seguiremos la [guía de instalación](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) provista en la documentación oficial.

- Primero tenemos que comprobar que Python está instalado tanto en la máquina host (Ubuntu Server) como en los nodos que vamos a configurar. Podemos comprobarlo accediendo a la consola de python ejecutando `python` o `python3`. 

  - Además, necesitaremos el módulo pip. Para instalarlo:

    ```
    > curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
    > python3 get-pip.py --user
    ```

- A continuación, instalaremos el paquete Ansible

  ```
  > sudo apt install ansible
  ```

  ![image-20230106125351665](/home/clara/.config/Typora/typora-user-images/image-20230106125351665.png)

- También instalamos el módulo python de Ansible mediante pip

  ```
  > python3 -m pip install --user ansible
  ```

  ![image-20230106125326839](/home/clara/.config/Typora/typora-user-images/image-20230106125326839.png)
  
  - Durante la instalación de pip y Ansible hemos tenido un warning que indica que la ruta en la que se instalan los módulos no pertenece al path de Python. Podemos ignorar el warning y continuar con la tarea, ya que no es un problema grave: simplemente, tendremos que indicar a Python la ruta completa para usar los módulos.

Una vez instalado, podemos continuar con la [guía de inicio](https://docs.ansible.com/ansible/latest/getting_started/index.html) de Ansible. Primero nos familiarizamos con los términos básicos de Ansible, que son el **nodo de control** (la máquina que tiene Ansible), **nodo controlado** (un sistema remoto o el propio host que es controlado por Ansible) e **inventario** (una lista de nodos controlados).

- Creamos un inventario. Lo hacemos añadiendo en el nodo de control las IP de los nodos controlados, Ubuntu Server y Rocky, en el archivo `/etc/ansible/hosts`

  ```
  [miinventario]
  192.168.56.105
  192.168.56.110
  ```

- Podemos comprobar que se han añadido correctamente listando los host:

  ```
  > sudo ansible all --list-hosts
  ```

  ![image-20230106170534578](/home/clara/.config/Typora/typora-user-images/image-20230106170534578.png)

- A continuación, vamos a configurar la conexión SSH entre el nodo de control y los nodos controlados. Desde el nodo controlador, generamos y añadimos las claves públicas de cada nodo a las `authorized_keys`:

  ```
  > ssh-keygen
  > ssh-copy-id 192.168.56.105 -p 22022 -f
  > ssh-copy-id 192.168.56.110
  ```

  - Nos topamos con que nos falta espacio en el home. Vamos a extender el /home con un disco virtual de 15GB (el cual hemos creado desde VirtualBox) 

    ![image-20230113034215897](/home/clara/.config/Typora/typora-user-images/image-20230113034215897.png)

    - Comprobamos primero el estado actual de nuestras particiones:

      ```
      > lsblk -f
      ```

      ![image-20230113033824570](/home/clara/.config/Typora/typora-user-images/image-20230113033824570.png)

      Vemos que el nuevo disco de 15GB aparece como sdc, abajo del todo.

    - Extendemos el grupo de volúmenes vd0, y luego el volumen lógico que queremos ampliar,  `/dev/vg0/hogar`

      ```
      > sudo vgextend vg0 /dev/sdc
      > sudo lvextend -l +100%FREE /dev/vg0/hogar
      ```

      ![image-20230113033759141](/home/clara/.config/Typora/typora-user-images/image-20230113033759141.png)

      ![image-20230113033805202](/home/clara/.config/Typora/typora-user-images/image-20230113033805202.png)

      

    - Finalmente hacemos el resize 

      ```
      > sudo resize2fs /dev/vg0/hogar
      ```


  Y ya habríamos expandido nuestro LVM. Podemos continuar con las claves SSH.

  ![image-20230113033715581](/home/clara/.config/Typora/typora-user-images/image-20230113033715581.png)

  ![image-20230113033701745](/home/clara/.config/Typora/typora-user-images/image-20230113033701745.png)

- Finalmente, hacemos ping a todas las máquinas:

  ```
  > ansible all -m ping
  ```

  - Hemos tenido que modificar el archivo `/etc/ansible/hosts` para especificar el puerto 22022
  
  ![image-20230113033628274](/home/clara/.config/Typora/typora-user-images/image-20230113033628274.png)

