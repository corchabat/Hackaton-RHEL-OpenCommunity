# Hackaton-RHEL-OpenCommunity
Ejercicios de conocimiento basico RHEL

* Cambiar el nombre de host a myhost.opencommunity.org 

     #hostnamectl set-hostname myhostacecinxs.opencommunity.org

* Configure su nombre de host, dirección IP, puerta de enlace y DNS.

     #nmcli connection down eth0

     #nmcli connection modify eth0 ipv4.addresses 172.24.40.41/24 ipv4.gateway 172.24.40.1 ipv4.dns 172.24.40.1
     
     #nmcli connection up eth0
     
     
* Descargue una Imagen (imagen.iso) y móntelo automáticamente bajo /media/cdrom y que surten efecto automáticamente en el arranque-inicio. 

     Se agrega el iso como device CDROM a la máquina virtual
     
     En este caso, la carpeta en la que vamos a montar se llama "iso"
     
     #mkdir /mnt/iso
     
     #echo "/dev/sr0 /mnt/iso iso9660 ro,nosuid,nodev,relatime 0 0" > /etc/fstab
     
     #cat /mnt/iso/media.repo >> /etc/yum.repos.d/local.repo
     
 * Cree dos usuarios: OpenCommunity con uid / gid igual a 8000, contraseña: password, buenestudiante con un uid / gid igual a  3333, contraseña: biendificil  y malestudiante con uid / gid igual a 6666, contraseña: refacilita. 
 
 #useradd opencommunity -u 8000 -p password
 #useradd buenestudiante -u 3333 -p biendificil
 #useradd malestudiante -u 6666 -p refacilita
 
 
 * Haga que la validez de la cuenta de malestudiante se bloque en un mes.
 
     #chage -M 30 malestudiante
 
 * Permita que buenestudiante (y solo buenestudiante) tenga acceso completo al directorio de inicio (home) de OpenCommunity.
     
     #setfacl -m u:buenestudiante:rwx /home/opencommunity/

* Crea un directorio llamado /patodos. Permita que buenestudiante, malestudiante y OpenCommunity compartan documentos en el directorio /patodos usando un grupo llamado OpenCommunityGroup. 
    
     #usermod buenestudiante -aG opencommunity
     #usermod malestudiante -aG opencommunity
     #setfacl -m g:opencommunity:rwx /patodos
     

* Todos pueden leer, escribir y eliminar documentos del otro en este directorio, pero ningún usuario que no sea miembro del grupo puede hacerlo.

     #chmod 700 /patodos
     
* Crea un catálogo en /home llamado hackaton. Se solicita a su grupo respectivo que sea el grupo de opencommuity. Los usuarios del grupo pueden leer y escribir, mientras que otros usuarios no pueden
     
     #mkdir /home/hackaton
     #chown :opencommunity /home/hackaton
     #chmod 760 /home/hackaton
     
     ![hacaton_10](https://user-images.githubusercontent.com/40834361/44064038-c87b1f2c-9f28-11e8-8c10-4d5e20674dca.png)
     
* Busque los archivos propiedad de OpenCommunity y cópielos al catálogo: /opt/dir
     
     #find . -group opencommunity -exec cp -Rf {} /opt/dir \;
     
     ![hackaton_12](https://user-images.githubusercontent.com/40834361/44064039-c8922dfc-9f28-11e8-957d-e9065e02c86f.png)
     
* Cree un grupo de volúmenes y configure 8M como una extensión. Dividir un grupo de volúmenes que contiene 50 y que se extienda en el grupo de volúmenes lv (lvshare), convertirlo en un sistema de archivos ext4 que se monte automáticamente bajo /mnt/data con tamaño variable en un rango entre 380M y 400M.

     #fdisk /dev/vdb
      n: partición nueva
      p: partición primaria
      inicio default
      +400M
      t: tipo Linux LVM (8e)
      w: guardar cambios
      
     #partprobe
     #pvcreate /dev/vdb1
     #vgcreate vg-opencom -s 8M /dev/vdb1
     #lvcreate /dev/vg-opencom -n lvshare -l 50
     #mkfs.ext4 /dev/vg-opencom/lvshare
     
     ![hackaton_13](https://user-images.githubusercontent.com/40834361/44064040-c8c9098a-9f28-11e8-9d32-9bc015f8155b.png)
     
* 
