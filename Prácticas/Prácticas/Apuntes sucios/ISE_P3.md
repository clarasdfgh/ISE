# ISE 22/23 - Práctica 3

## P3L1 - Ejercicio recuperación RAID

#### Desde P1L3 (CentOs) con fallo software

1. Comprobar RAID 

   ```
   sudo mdadm --query /dev/md0 #o md127, si el SO lo reasigna 
   sudo mdadm --detail /dev/md0 #Para mas detalle
   ```

   ![image-20221130065852711](/home/clara/.config/Typora/typora-user-images/image-20221130065852711.png)

2. Para ver el estado del multidevice (mdstatus)

   ```
   cat /proc/mdstat
   ```

   ![image-20221130065943341](/home/clara/.config/Typora/typora-user-images/image-20221130065943341.png)

   Lo importante son las U: representan los discos activos (up). Un _ representaría un disco caído.

3. Forzar un fallo software

   ```
   sudo mdadm --manage --set-faulty /dev/md0 /dev/sdb1 #Tiramos el disco 1 (su unica partición)
   ```

   ![image-20221130070051451](/home/clara/.config/Typora/typora-user-images/image-20221130070051451.png)

   Podemos comprobarlo con detail o mdstat (ni query ni lsblk nos darán la info necesaria)

   ```
   sudo mdadm --detail /dev/md127 #OJO --query no muestra nada 
   cat /proc/mdstat
   ```

   ![image-20221130070149801](/home/clara/.config/Typora/typora-user-images/image-20221130070149801.png)

   ![image-20221130070202176](/home/clara/.config/Typora/typora-user-images/image-20221130070202176.png)

4. Para recuperarnos del fallo, hay que sacar el disco caído del RAID y luego volverlo a meter

   ```
   sudo mdadm -r /dev/md0 /dev/sdb1 #Quitamos 
   cat /proc/mdstat #Comprobamos
   
   sudo mdadm -a /dev/md0 /dev/sdb1 #Añadimos 
   cat /proc/mdstat #Monitorizamos la recuperacion
   ```

   ![image-20221130070320574](/home/clara/.config/Typora/typora-user-images/image-20221130070320574.png)

   ![image-20221130070329376](/home/clara/.config/Typora/typora-user-images/image-20221130070329376.png)

5. Podemos monitorizar la recuperación del disco con watch

   ```
   watch -n 1 /proc/mdstat #Se sale con Ctrl+C
   ```

   ![image-20221130070506450](/home/clara/.config/Typora/typora-user-images/image-20221130070506450.png)

   Podríamos monitorizarlo con un demonio, pero se recomienda el uso de herramientas específicas de monitorización como zabbix o el resto del guión



#### Desde P1L1 (UbuntuServer) con fallo hardware

1. Forzamos el fallo hardware desde VirtualBox

   ![image-20221130070723992](/home/clara/.config/Typora/typora-user-images/image-20221130070723992.png)

   Intentará cargar el grupo de volúmenes, pero no tiene el disco. Al rato de intentar iniciar, saltará el error:

   ![image-20221130070822794](/home/clara/.config/Typora/typora-user-images/image-20221130070822794.png)

   Nos deja en initramfs. Con doble tab podemos ver los comandos que ofrece.

2. Podemos ver los mensajes del kernel con dmesg

3. Mirar la consola lvm - no tiene nada: discos, particiones...

   ![image-20221130072149126](/home/clara/.config/Typora/typora-user-images/image-20221130072149126.png)

4. Si miramos mdstat vemos que si ha usado los discos, ha arrancado porque ha encontrado boot,  pero están inactivos:

   ![image-20221130072234074](/home/clara/.config/Typora/typora-user-images/image-20221130072234074.png)

5. Forzamos la activación de md0 y md1

   ```
   mdadm -R /dev/md0 
   cat /proc/mdstat 
   mdadm -R /dev/md1 
   cat /proc/mdstat 
   
   ```

   Forzamos el arranque desde initramfs con CTRL+D

   ![image-20221130072329386](/home/clara/.config/Typora/typora-user-images/image-20221130072329386.png)

   y comprobamos qué tenemos con lsblk:

   ![image-20221130072351019](/home/clara/.config/Typora/typora-user-images/image-20221130072351019.png)

   En este estado, no sería correcto dar servicio al cliente porque no estamos cumpliendo el contrato, el RAID prometido, y además si fallara el disco que queda cagadolahemos.

6. Desde VirtualBox volvemos a crear un disco para volver a montarlo todo como lo teníamos (es hotpluggable)

7. Volvemos a configurar sdb tal y como lo teníamos con fdisk

   ![image-20221130075345691](/home/clara/.config/Typora/typora-user-images/image-20221130075345691.png)

8. Le instalamos grub al disco

   ```
   sudo grub-install /dev/sdb
   ```

9. Y volvemos a añadir el disco con sus particiones al RAID

   ```
   sudo mdadm -a /dev/md0 /dev/sdb1 
   sudo mdadm -a /dev/md1 /dev/sdb2 
   watch -n 1 cat /proc/mdstat
   ```

10. lsblk para comprobar otra vez que está bien

#### Preguntas

- **Qué archivo miramos**

  - /proc/mdstat

- **Leer mensajes del kernel**

  - dmesg

- **Eliminar un disco, marcarlo defectuoso, volverlo a añadir a un raid**

  - Marcar defectuoso: sudo mdadm --manage --set-faulty /dev/md0 /dev/sdb1
  - Eliminar: sudo mdadm -r /dev/md0 /dev/sdb1 
  - Volverlo a añadir: sudo mdadm -a /dev/md0 /dev/sdb1 

- **Protocolo cuando falla un raid**

  - Fallo software: eliminar (mdadm -r) y volverlo a añadir (mdadm -a)
  - Fallo hardware, falla boot: nos llevará a initramfs. Desde ahí, siempre lo primero mirar: lvm, dmesg, mdstat... Forzar activación de los discos (mdadm -R) y continuar con el arranque (ctrl+d) con lo que tengamos

- **Qué significa initramfs**

  - > All 2.6 Linux kernels contain a gzipped “cpio” format archive, which is extracted into rootfs when the kernel boots up.  After extracting, the kernel checks to see if rootfs contains a file “init”, and if so it executes it as PID 1.  If found, this init process is responsible for bringing the system the rest of the way up, including locating and mounting the real root device (if any).  If rootfs does not contain an init program after the embedded cpio archive is extracted into it, the kernel will fall through to the older code to locate and mount a root partition, then exec some variant of /sbin/init out of that.

  - > initramfs is the solution introduced for the 2.6 Linux kernel series.   The idea is that there's a lot of initialisation magic done in the  kernel that could be just as easily done in userspace. 

- **Donde encontrar y como interpretar mdstat**

  - Ubicación: /proc/mdstat
  - Significado: multidevice status
  - La parte importante es el estado de los discos: U para up, _ para caído

- **Que hacer en initramfs cuando falla un raid para que el sistema inicie igualmente**

  - Forzar activación de los discos con mdadm -R /dev/mdx (los que tengamos que hacer, mirar en mdstat) y CTRL+D para continuar con el boot

- **Seguir dando acceso o no**

  - No, estaríamos incumpliendo el contrato



## P3L2 - Zabbix

#### Instalación UbuntuServer

1. **Instalación siguiendo las instrucciones de la página de instalación, tiene un selector múltiple que nos da las instrucciones según la versión que queremos, el sistema en el que lo instalamos...**

   1. Instalar repo

      ```
      wget [URL]
      sudo dpkg -i zabbix....
      sudo apt update
      ```

   2. Instalar servidor, interfaz, agente...

      ```
      sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent
      ```

   3. Crear BD

      ```
      sudo mysql -uroot -p 
      mysql> create database zabbix character set utf8 collate utf8_bin; 
      mysql> create user zabbix@localhost identified by 'practicas,ISE'; 
      mysql> grant all privileges on zabbix.* to zabbix@localhost; 
      mysql> quit;
      ```

   4. Importar esquema y los datos al servidor de zabbix

      ```
      sudo zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
      ```

      Comprobar que es correcto intentando ejecutar otra vez, si nos dice que ya existe es que bien

2. **Configuración: /etc/zabbix/zabbix_server.conf**

   1. Contraseña de la BD: en el archivo de conf, buscar DBPassword, descomentar y poner practicas,ISE

   2. PHP para interfaz zabbix

      1. Ir a /etc/zabbix/apache.conf y descomentar las dos lineas de date.timezone. Poner Madrid en vez de Riga.

      2. Reiniciar y habilitar servicios

         ```
         sudo systemctl restart zabbix-server zabbix-agent apache2 
         sudo systemctl enable zabbix-server zabbix-agent apache2
         ```

         Podemos comprobar en el log: /var/log/zabbix/zabbix_server.log

3. **Configuración del servidor usando la guía de inicio rápido**

   1. Abrir en un navegador: http://192.168.56.105/zabbix/setup.php

   2. Configurar la timezone

      ![image-20221130083353841](/home/clara/.config/Typora/typora-user-images/image-20221130083353841.png)

      Reiniciar apache con sudo systemctl restart apache

   3. Configuración BD

      ![image-20221130083439900](/home/clara/.config/Typora/typora-user-images/image-20221130083439900.png)

   4. Configuración puerto del servidor

      ![image-20221130083853740](/home/clara/.config/Typora/typora-user-images/image-20221130083853740.png)

   5. Logear con user: Admin, pass: zabbix

4. **Configurar el agente del servidor**

   1. En /etc/zabbix/zabbix_agentd.conf buscar /server: es donde vamos a ver las IPs que acepta el agente. Añadimos la ip de nuestra máquina y la de rocky
   2. Crear nuevo host cambiando el hostname
   3. Reiniciar el servicio sudo systemctl restart zabbix-agent

#### Instalación Rocky

1. Instalación siguiendo instrucciones de la página

   1. Repositorio

      ```
      sudo rpm [URL]
      ```

   2. Agente

      ```
      sudo dnf install zabbix-agent
      ```

2. Configuración del agente

   ```
   sudo vi /etc/zabbix/zabbix_agentd.conf 
   	Server=192.168.56.105 #Ip del servidor(Ubuntu) Hostname=Host Rocky Linux
   ```

   Añadiendo la ip de ubuntu y la de rocky para poder hacer zabbix-get

   Luego reiniciamos y habilitamos

   ```
   sudo systemctl restart zabbix-agent 
   sudo systemctl enable zabbix-agent
   ```

3. Creamos un host

   1. Vamos al navegador http://192.168.56.105/zabbix/ y Configuration>Hosts>Create host

   2. En interfaz del agente ponemos la ip de rocky

      ![image-20221130090448621](/home/clara/.config/Typora/typora-user-images/image-20221130090448621.png)

   3. IMPORTANTE habilitar los puertos en el cortafuegos

      ```
      sudo firewall-cmd --add-port=10050/tcp --permanent #Permite a zabbix-server 
      sudo firewall-cmd --add-port=22/tcp --permanent #Permite a ssh 
      sudo firewall-cmd --add-port=80/tcp --permanent #Permite a http 
      sudo firewall-cmd --reload
      ```

#### Monitorización

Para monitorizar el ssh y http tenemos 2 opciones: usar Templates o añadir los items a mano. Ambas formas son válidas siempre y cuando en los templates modifiquemos el puerto correspondiente y entendamos como funcionan.

**Templates:**

1. Configuration>Hosts, elegir el host que hemos configurado antes e ir a templates. Añadir las templates de SSH y HTTP

   ![image-20221130102021219](/home/clara/.config/Typora/typora-user-images/image-20221130102021219.png)

2. Configuration>Templates y buscamos las que queremos modificar (ssh, http). Columna Items

   ![image-20221130102143076](/home/clara/.config/Typora/typora-user-images/image-20221130102143076.png)

3. Nombre del item

   ![image-20221130102241669](/home/clara/.config/Typora/typora-user-images/image-20221130102241669.png)

4. Añadir puertos

   1. net.tcp.service[ssh,,22]
   2. net.tcp.service[http,,80]

   ![image-20221130102247643](/home/clara/.config/Typora/typora-user-images/image-20221130102247643.png)

   

**A mano:**

1. Configuration>Hosts, elegimos nuestro Host y vamos a Items. 

2. Creamos un nuevo item

   ![image-20221130102342690](/home/clara/.config/Typora/typora-user-images/image-20221130102342690.png)

   ![image-20221130102347142](/home/clara/.config/Typora/typora-user-images/image-20221130102347142.png)

##### Comprobar la conexión

Podemos tirar un servicio (sudo systemctl stop ssh) y esperar a ver qué pasa en Monitoring>Problems. También cuenta en history con una gráfica del uptime.

#### Preguntas

- **Puertos por defecto zabbix**

  - zabbix: 10051
  - ssh: 22
  - http: 80

- **Cómo utilizar zabbix con consola/sin UI**

  - zabbix-get

- **Qué problema tiene rocky con zabbix**

  - Problema: Tenemos que habilitar los puertos

  - Solución:

    ```
    sudo firewall-cmd --add-port=10050/tcp --permanent #Permite a zabbix-server 
    sudo firewall-cmd --add-port=22/tcp --permanent #Permite a ssh 
    sudo firewall-cmd --add-port=80/tcp --permanent #Permite a http 
    sudo firewall-cmd --reload
    ```

- **Cortafuegos con el agente de zabbix**

  - Tenemos que habilitar los puertos de zabbix, ssh y http con --add-port y luego recargar el cortafuegos.

- **Cómo monitorizar un servicio en zabbix**

  - UI: Monitoring>Latest data
  - Consola: zabbix-get

- **Dónde están los archivos de conf de zabbix**

  - Config: /etc/zabbix/zabbix_server.conf 
  - Apache: /etc/zabbix/apache.conf
  - Agente: /etc/zabbix/zabbix_agentd.conf
  - Log: /var/log/zabbix/zabbix_server.log

- **Proxy de zabbix (zabbixproxy)**

  - > Zabbix proxy is a process that may collect monitoring data from one  or more monitored devices and send the information to the Zabbix server, essentially working on behalf of the server. All collected data is  buffered locally and then transferred to the Zabbix server the proxy  belongs to.
    >
    > Deploying a proxy is optional, but may be very beneficial to  distribute the load of a single Zabbix server. If only proxies collect  data, processing on the server becomes less CPU and disk I/O hungry.

- **Usuario y contraseña de frontend de zabbix**

  - User: Admin
  - Pass: zabbix

- **Que se modifica en la configuración de zabbix**

  - La timezone

- **Relación de LAMP y zabbix**

  - Apache y MySQL 

- **Cosas que hacer en el frontend de zabbix una vez instalado**

  - Añadir los items que queremos monitorizar, en este caso ssh y http. Podemos hacerlo a mano o usando templates.

  

## P3L3 Ansible

#### Instalación y conexión

1. Instalación según la docu

   ```
   sudo apt update 
   sudo apt install software-properties-common 
   sudo add-apt-repository --yes --update ppa:ansible/ansible 
   sudo apt install ansible
   ```

2. Añadir hosts en /etc/ansible/hosts

   ![image-20221130104604740](/home/clara/.config/Typora/typora-user-images/image-20221130104604740.png)

3. Generar y añadir las claves publicas de ambos servidores para que ansible pueda conectarse por ssh.

   ```
   #En ubuntu 
   ssh-keygen 
   ssh-copy-id 192.168.56.105 #Añadimos la key de ubuntu al propio ubuntu 
   ssh-copy-id 192.168.56.110 # Añadimos la key de ubuntu a rocky
   ```

4. Comprobar que ansible se conecta a los servidores con ping

   ```
   ansible all -m ping
   ```

#### Creación de un inventario

Los inventarios nos permiten agrupar hosts en grupos. 

1. Creamos el fichero: 

   ```
   vi inventory.yaml 
   
   virtualmachines: 
       hosts: 
      		ubuntu2004: 
       		ansible_host: 192.168.56.105 
           rocky: 
           	ansible_host: 192.168.56.110 
   ```

   

2. Podemos hacer ping de nuevo con el nuevo grupo 

   ```
   ansible virtualmachines -m ping -i inventory.yaml
   ```

#### Creación de un playbook

Los playbook son listas de tareas que ansible ejecuta de una en una en los servidores que especifiquemos. 

1. Creamos el fichero: 

   ```
   vi playbook.yaml
   
   - name: Libro de prueba #Nombre del libro 
   	hosts: virtualmachines 
   	tasks: #Lista de tareas 
   		- name: Ping a los hosts #1ªTarea 
   		ansible.builtin.ping: 
   		- name: Imprime un mensaje #2ªTarea 
   		ansible.builtin.debug: msg: Hello world
   ```

2. Ejecutando un playbook:

   ```
   ansible-playbook -i inventory.yaml playbook.yaml
   ```

   

#### Preguntas

- **Ansible y su relación con ssh**

  - > Ansible’s main goals are simplicity and ease-of-use. It also has a  strong focus on security and reliability, featuring a minimum of moving  parts, usage of OpenSSH for transport (with other transports and pull  modes as alternatives)

  - Utiliza ssh para conectarse a los servidores. Necesita para ello generar llaves públicas

- **Qué hace comando ping de ansible**

  - Hace ping a los servidores que hayamos añadido en el archivo de hosts

- **Cómo se denominan los archivos de ansible y qué lenguaje utilizan**

  - Playbooks, YAML

- **Cómo instalar paquetes mediante ansible**

  - Mediante playbook (instala sysstat, httpd, mariadb-server):

    ```
    ---
    - hosts: all
      tasks:
      - name: Package installation
        dnf:
          name:
            - sysstat
            - httpd
            - mariadb-server
          state: latest
    ```

  - Mediante terminal:

    ```
    ansible all --user tux --become --module-name dnf -a’name=sysstat state=latest’
    ```

- **Cómo se llama no tener un agente instalado con ansible**

  - Agentless architecture

- **Archivos a modificar para que ansible funcione**

  - /etc/ansible/hosts

- **Cómo comprobar el estado del servicio http mediante un playbook**

  - ```
    - name: checking service status
      hosts: www.linuxfoundation.org
      tasks:
      - name: checking service status
        command: systemctl status httpd
        register: result
        ignore_errors: yes
      - name: showing report
        debug:
         var: result
    ```

- **Cómo instalar httpd en rocky en un playbook**

  - ```
    ---
    - hosts: all
      tasks:
      - name: Package installation
        dnf:
          name:
            - httpd
          state: latest
    ```

    

### Comandos documento de la práctica y otras preguntas

- hddtemp, lm-sensors, lspci, lsusb, lshw -- Monitorización hardware
- dmesg -- Mensajes del kernel
- strace -- traza de llamadas al sistema, útil para cosas que no guardan los logs (/var/log)

#### Preguntas

- **Cómo ver la salida de los timer, cómo ejecutarlos**

  - Ver timers activos: systemctl list-timers
  - Ejecutar: journalctl

- **Cómo automatizar un script con systemd**

  - Podemos automatizar la ejecución de un script en systemd definiendo un timer dentro del directorio /etc/systemd/system/ que se encarga de gestionar un servicio. Por tanto, hemos de crear dos archivos: mon_raid.timer y mon_raid.service

- **Qué hacer no route to host**

  - Comprobar que todos los servicios están corriendo con systemctl status (y si no, arreglarlos)
  - Comprobar los puertos
    - Comprobar que el firewall no está interfiriendo  

- **Szk y para qué se usa**

  - ​									//Será que copiamos mal el nombre porque no encuentro nada de nada

- **lm-sensors y por qué no lo hemos usado**

  - Porque monitoriza aspectos del hardware. No tiene sentido utilizarlo en una máquina virtual, porque monitorizará hardware simulado.

- **Cómo hacer para encontrar un fichero**

  - find

    - Ej. uso: copiar todos los archivos pdf en un directorio

      ```
      find /home/alberto/docs -name ’*pdf’ -exec cp {} ~/PDFs \;
      ```

- **Cómo ver la última vez que inició sesión un user sospechoso, donde estan los logs**

  ```
  grep "Failed password" /var/log/auth.log
  ```

- **Cómo se mira la última vez que se conectó un usuario**

  ```
  grep "[nombre del usuario]" /var/log/auth.log
  ```

  //Habría q afinar un poco más esto para q solo devolviera el último pero jemapelle barbara (me la pela una barbaridad)

- **Monitorización en la disponibilidad, como evitar cosas malas**
- **Problema de usar muchos nodos y soluciones**
- **Que hacer si se cae el servicio de apache en ubuntu server**