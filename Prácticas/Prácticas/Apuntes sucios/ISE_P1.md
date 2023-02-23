## P1L1

- Crear máquina, disco duro crear... Almacenamiento CD vivo ubuntu, crear otro disco con todo default
- Español, sin actualizar, teclado esp, red default, proxy default, archive error default, custom storage layout
  1. D1 add partition 400M sin formato (automáticamente se guarda como /boot)
  2. D2 add as another boot device
  3. D2 add partition 400M sin formato
  4. Create software RAID, RAID1 md0, seleccionar las dos particiones de 400
  5. D1 add partition, sin tam sin formato
  6. D2 add partition, sin tam sin formato
  7. Create software RAID, RAID1 md1, seleccionar las dos particiones nuevas
  8. Md0 format ext4 /boot
  9. Crear VG md1, vgraid1, md1m cifrado
  10. vgraid1 create LV
      1. swap 1g swap
      2. hogar 500m ext4 mount /home
      3. raiz sin tam ext4 mount /
- clararl, ubuntu, clararl, practicas,ISE
- sin openssh ni ningún paquete. Cancelar actualización y reiniciar. Retirar CD vivo.
- pedirá passwd si está bien hecho. Lsblk



## P1L2

- Crear máquina fedora, todo por defecto, cargar disco imagen NO VIVO

- Instalación rocky seleccionar root y disco. Apagar y quitar el disco de arranque. Añadir disco. lsblk

- Crear usuario con permisos:

  1. useradd clararl
  2. passwd clararl
  3. usermod -aG wheel clararl
  4. su - clararl

- Hacer partición al disco nuevo

  1. sudo fdisk /dev/sdb
     1. m ver opciones
     2. p ver particiones
     3. n nueva particion
     4. p primaria
     5. 1
     6. default x2
     7. p comprobar nueva partición
     8. w escribir

- lvm (tab tab ver opciones)

  1. pvdisplay
  2. pvs
  3. pvcreate /dev/sdb1
  4. pvs
  5. pvdisplay
  6. vgs
  7. vgdisplay
  8. vgextend cl /dev/sdb1
  9. vgs
  10. lvs
  11. lvdisplay
  12. lvcreate -n new_var -L 3g cl
  13. lvdisplay
  14. lvs

- hacer fs para el lv, acceder al lv (montarlo)

  1. mkdir /new_var
  2. mkfs -t ext4 /dev/cl/new_var
  3. mount dev/cl/new_var /new_var

- deshabilitar escritura

  1. systemctl isolate rescue
  2. systemctl status

- copiar info de /var a lv

  1. cp -a /var/. /new_var |

- indicar al so donde está /var

  1. cd /etc

  2. less fstab

  3. vi /etc/fstab

     /dev/mapper/cl-new_var	/var	ext4	defaults 0 0

  4. umount /new_var

  5. lsblk

  6. mount -a

  7. lsblk

- liberar espacio

  1. vi /etc/fstab

     comentar la última línea, la q habíamos añadido

  2. mount -a

  3. lsblk

  4. umount /dev/cl/new_var

  5. lsblk

  6. vi /etc/fstab

     descomentar línea

  7. mv /var /var_old

  8. mkdir /var

  9. restorecon /var

  10. mount -a

  11. lsblk



## P1L3

- Máquina rocky limpia con su
- Añadir dos discos, lsbk
- Instalar herramientas/comprobar internet
  1. ip addr, ping www.google.com
  2. si no, sudo ifup enp0s3. Repetir paso anterior
  3. dnf install cryptsetup -y
  4. dnf install mdadm -y
- crear raid1 mdadm
  1. Antes formatear sdb sdc
     1. sudo fdisk /dev/sdb
     2. p
     3. n
     4. p
     5. w
     6. repetir con sdc
  2. mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
  3. ls /dev
- pv sobre md0
  1. pvs
  2. pvcreate /dev/md0
  3. pvs
- vg sobre md0
  - vgs
  - vgcreate vg-raid1 /dev/md0
  - vgs
- lv /var
  - lvs
  - lvcreate -n new_var -L 1.8G vg_raid1
  - lvs
- formatear y cifrar lv
  - cryptsetup luksFormat /dev/vg_raid1/new_var
  - cryptsetup luksOpen /dev/vg_raid1/new_var vg_radi1-new_var_crypt
  - ls /dev/mapper
- crear fs y todo lo demás q hicimos en la l2
  - mkfs -t xfs /dev/mapper/vg_raid1-new_var_crypt
  - systemctl isolate rescue.target
  - systemctl status
  - mkdir /new_var
  - mount /dev/mapper/vg_raid1-new_var_crypt /new_var
  - cp -a /var/. /new_var/
  - ls -laZ /new_var
  - vi /etc/fstab
    - /dev/mapper/vg_raid1-new_var_crypt	/var	xfs	defaults 	0 0
  - blkid | grep crypt > /etc/crypttab
    - vg_raid1-new_var_crypt UUID:(sin comillas) none
- liberar espacio
  - mv /var /var_old
  - mkdir /var
  - restorecon /var
  - reboot y lsblk 