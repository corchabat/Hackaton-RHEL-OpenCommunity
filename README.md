# Hackaton-RHEL-OpenCommunity: Equipo Lxs_a_cecinxs
Ejercicios de conocimiento basico RHEL

**NOTA: el que las capturas de pantalla que muestran una fecha posterior a la de la sesión se debe a que la documentación se hizo después. Lo mismo aplica a los ejercicios que fueron completados fuera de tiempo. Los ejercicios que se completaron durante la sesión son: 1,2,3,4,5,6,7,8,9,10,11,12,13,18,23,24 y 26. Los demás fueron resueltos en días posteriores.**

* Cambiar el nombre de host a myhost.opencommunity.org 

     **#hostnamectl set-hostname myhostacecinxs.opencommunity.org**

* Configure su nombre de host, dirección IP, puerta de enlace y DNS.

     **#nmcli connection down eth0**

     **#nmcli connection modify eth0 ipv4.addresses 172.24.40.41/24 ipv4.gateway 172.24.40.1 ipv4.dns 172.24.40.1**
     
     **#nmcli connection up eth0**
     
     ![hackaton_1](https://user-images.githubusercontent.com/40834361/44108447-0d1ba6dc-9fc0-11e8-9d65-1c979eafa7d3.png)
     
     
* Descargue una Imagen (imagen.iso) y móntelo automáticamente bajo /media/cdrom y que surten efecto automáticamente en el arranque-inicio. 

     Se agrega el iso como device CDROM a la máquina virtual
     
     ![hackaton_4a](https://user-images.githubusercontent.com/40834361/44108932-67afd91e-9fc1-11e8-9303-0b72bba49c68.png)
     
     En este caso, la carpeta en la que vamos a montar se encuentra en /mnt/cdrom     
     
     **#mkdir /mnt/cdrom**
     
     **#echo "/dev/sr0 /mnt/cdrom iso9660 ro,nosuid,nodev,relatime 0 0" > /etc/fstab**
     
     **#cat /mnt/cdrom/media.repo >> /etc/yum.repos.d/local.repo**
     
     ![hackaton_4e](https://user-images.githubusercontent.com/40834361/44108936-67f852fc-9fc1-11e8-9596-2bcad3fd5970.png)
     
     ![hackaton_4f](https://user-images.githubusercontent.com/40834361/44108937-6816e7d0-9fc1-11e8-8392-7e0af6cfd07f.png)
     
 * Cree dos usuarios: OpenCommunity con uid / gid igual a 8000, contraseña: password, buenestudiante con un uid / gid igual a  3333, contraseña: biendificil  y malestudiante con uid / gid igual a 6666, contraseña: refacilita. 
 
    **#useradd opencommunity -u 8000 -p password**
    **#useradd buenestudiante -u 3333 -p biendificil**
    **#useradd malestudiante -u 6666 -p refacilita**
 
    ![hackaton_5a](https://user-images.githubusercontent.com/40834361/44109537-ec401418-9fc2-11e8-886d-5efa3e2f4562.png)
 
 * 7:  Haga que la validez de la cuenta de malestudiante se bloque en un mes.
 
     ![hackaton_5b](https://user-images.githubusercontent.com/40834361/44109709-53bec256-9fc3-11e8-8681-f012b61d7c50.png)
     
 * 8: Permita que buenestudiante (y solo buenestudiante) tenga acceso completo al directorio de inicio (home) de OpenCommunity.
     
     **#setfacl -m u:buenestudiante:rwx /home/opencommunity/**

* 9: Crea un directorio llamado /patodos. Permita que buenestudiante, malestudiante y OpenCommunity compartan documentos en el directorio /patodos usando un grupo llamado OpenCommunityGroup. 
    
     **#usermod buenestudiante -aG opencommunitygroup**
     **#usermod malestudiante -aG opencommunitygroup**
     **#usermod opencommunity -aG opencommunitygroup**
     **#setfacl -m g:opencommunity:rwx /patodos**
     

* 10: Todos pueden leer, escribir y eliminar documentos del otro en este directorio, pero ningún usuario que no sea miembro del grupo puede hacerlo.

     **#chmod 770 /patodos**
     
* 11: Crea un catálogo en /home llamado hackaton. Se solicita a su grupo respectivo que sea el grupo de opencommuity. Los usuarios del grupo pueden leer y escribir, mientras que otros usuarios no pueden
     
     **#mkdir /home/hackaton**
     **#chown :opencommunity /home/hackaton**
     **#chmod 760 /home/hackaton**
     
     ![hacaton_10](https://user-images.githubusercontent.com/40834361/44064038-c87b1f2c-9f28-11e8-8c10-4d5e20674dca.png)
     
* 12: Busque los archivos propiedad de OpenCommunity y cópielos al catálogo: /opt/dir
     
     **#find / -group opencommunity -exec cp -Rf {} /opt/dir \;**
     
     ![hackaton_12](https://user-images.githubusercontent.com/40834361/44064039-c8922dfc-9f28-11e8-957d-e9065e02c86f.png)
     
* 13: Cree un grupo de volúmenes y configure 8M como una extensión. Dividir un grupo de volúmenes que contiene 50 y que se extienda en el grupo de volúmenes lv (lvshare), convertirlo en un sistema de archivos ext4 que se monte automáticamente bajo /mnt/data con tamaño variable en un rango entre 380M y 400M.

     **#fdisk /dev/vdb**
      n: partición nueva
      p: partición primaria
      inicio default
      +400M
      t: tipo Linux LVM (8e)
      w: guardar cambios
      
     **#partprobe**
     **#pvcreate /dev/vdb1**
     **#vgcreate vg-opencom -s 8m /dev/vdb1**
     **#lvcreate /dev/vg-opencom -n lvshare -l 50**
     **#mkfs.ext4 /dev/vg-opencom/lvshare**
     
     ![hackaton_13](https://user-images.githubusercontent.com/40834361/44064040-c8c9098a-9f28-11e8-9d32-9bc015f8155b.png)
     
* 14: Cree un grupo de volúmenes y configure 16M como una extensión. Dividir el grupo de volúmenes que contenía 50MB para montar  automaticamente en mnt/opencommunity sin rango variable.
    

* 15: Crear un sistema de archivos xfs en un nuevo volumen lógico de 100 MB llamado lv_openc_xfs. Montarlo permanentemente con uuid bajo /xfs.
    
    **#mkdir /xfs**
    
    ![hackaton_15a](https://user-images.githubusercontent.com/40834361/44172419-c7152d00-a0a2-11e8-999a-fcca0b9b7f4b.png)
    
    ![hackaton_15b](https://user-images.githubusercontent.com/40834361/44172420-c7152d00-a0a2-11e8-850a-37b37c21d05b.png)
    
    ![hackaton_15c](https://user-images.githubusercontent.com/40834361/44172422-c7152d00-a0a2-11e8-9cf5-374507ca2392.png)

* 16: Extienda el sistema de archivos xfs existente a un tamaño total de 200 MB y agregue una etiqueta llamada myFS.

    ![hackaton_16a](https://user-images.githubusercontent.com/40834361/44172858-fd06e100-a0a3-11e8-86ac-40a4e7af45fe.png)
    
    ![hackaton_16b](https://user-images.githubusercontent.com/40834361/44172859-fd9f7780-a0a3-11e8-8465-1f84593b40d2.png)

* 17: Asigne los mismos contextos de SELinux utilizados por los directorios de inicio, al directorio /xfs permanentemente.



* 18: Escriba un script Bash llamado virusrompemaquinas.sh en el directorio /root que crea 40 archivos de 2MB cada uno con el comando fallocate en el directorio montado /xfs. Cada archivo tiene un nombre como sigue: .fileOpenC_N donde N es un número del 1 al 40.
    
    ![hackaton_18a](https://user-images.githubusercontent.com/40834361/44174039-6c320480-a0a7-11e8-9bd1-2dd5c4b85b07.png)
    
    ![hackaton_18b](https://user-images.githubusercontent.com/40834361/44174040-6c320480-a0a7-11e8-84ed-7f962be496f8.png)
    
* 19: Cree un sistema de archivos ext4 en un nuevo volumen lógico de 100 MB llamado lv_openc_ext4. Móntelo de forma permanente en el directorio /ext4. Copie los archivos creados previamente en este nuevo espacio.
    
    **#mkdir /ext4**
   
   ![hackaton_19a](https://user-images.githubusercontent.com/40834361/44174751-b0be9f80-a0a9-11e8-8a66-e68862856877.png)
    
   ![hackaton_19b](https://user-images.githubusercontent.com/40834361/44174753-b0be9f80-a0a9-11e8-94df-9118d53a1008.png)
   
   ![hackaton_19c](https://user-images.githubusercontent.com/40834361/44174754-b0be9f80-a0a9-11e8-97d1-34c48c9ff3d0.png)
    
   ![hackaton_19d](https://user-images.githubusercontent.com/40834361/44174756-b0be9f80-a0a9-11e8-81ff-01b1c6e88f39.png)
    
* 20: Cree un volumen lógico de 200 MB llamado lv_swap2 y agréguelo permanentemente al espacio de intercambio actual.
    
    
    
* 21: Cree una partición swap 2G que entre en vigor automáticamente al inicio y al inicio, y no debería afectar la partición de intercambio original.


* 22: Cree una tarea cron ejecutándose como OpenCommunity, comenzando a las 8PM todos los días y escribiendo un informe sobre el consumo diario de recursos del sistema en el archivo /var/log/loquehacemos.log.

    **#crontab -e**
    
    ![hackaton_22a](https://user-images.githubusercontent.com/40834361/44176333-06e21180-a0af-11e8-884c-ef10ad511ca0.png)

* 23: Configure una tarea: planee ejecutar el comando echo "El OpenCommunity es el mejor" a las 18:18 todos los días.


    **#crontab -e**
    
    ![hackaton_23](https://user-images.githubusercontent.com/40834361/44176455-7bb54b80-a0af-11e8-97f4-0a8ee43588d2.png)
    

* 24: Crear un archivo de configuración de repositorio en Yum, apuntando a http://server.example.com/pub/centos7/repo/



* 25: Agregar una nueva tarjeta de red y configurar teaming/bonding



* 26: Configurar autenticación de usuarios usando LDAP y Kerberos



* 27: Restringir el acceso al equipo virtual mediante firewalld (o iptables)



* 28: Compartir directorios en server mediante NFS y/o Samba y configurarlos en labo usando Kerberos



* 29: Instalar  Apache (http server). Permitir que obtenga documentos almacenados en directorios montados en (NFS si es posible).



* 30: Configurar un servidor HTTP, al que se puede acceder a través de http://myhost(equiṕo).opencommunity.org



* 31: Configure el modo de verificación de su cuenta de usuario y la contraseña como LDAP. Después de que el usuario inicia sesión, el usuario no deberá tener un directorio de host a menos que configure los auto fs.



* 32: Crear un certificado auto firmado.








