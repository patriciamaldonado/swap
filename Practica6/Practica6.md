> ## Práctica 6. Discos en RAID.
###  **Objetivos**


Los objetivos concretos de esta práctica son:
• Configurar dos discos en RAID 1. Los discos se añadirán a un sistema ya instalado y funcionando, de forma que en total tendremos tres discos (el sistema operativo estará ya instalado en /dev/sda y añadiremos dos discos
más, que serán el /dev/sdb y el /dev/sdc, para configurar el dispositivo de almacenamiento RAID en estos dos discos nuevos de igual tamaño).

• Hacer pruebas de retirar y añadir un disco “en caliente”, y comprobar que el RAID sigue funcionando correctamente.


-------------------------------------------------------------------------------------
 **1. Configuración del RAID por sofware**

 1. Añadimos dos discos a la máquina del mismo tipo y capacidad.

 ![enter image description here](http://i.imgur.com/7KE1Gxm.png)

 2. Configuramos el RAID
 3. Instalación de software necesario para la configuración del RAID.
			sudo apt-get install mdadm

	- Buscamos la identificación asignada por Linux de ambos discos:
			
			sudo fdisk -l
		![enter image description here](http://i.imgur.com/p3BhqnT.png)
 4. Creamos el RAID 1, usando el dispositivo /dev/md0, indicando
 el número  de dispositivos que vamos a utilizar  su ubicación.

			sudo mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc

 5. Le damos formato
		
			sudo mkfs /dev/md0

 6. Creamos el directorio en el que se montará la unidad del RAID.
			
			sudo mkdir /dat
			sudo mount /dev/md0 /dat

![enter image description here](http://i.imgur.com/BVs3Fu3.png)

 7. Comprobamos el estados del RAID
 
![enter image description here](http://i.imgur.com/NnYEbGq.png)

 8.  Por último vamos a configurar el sistemas para que monte  el dispositvo RAID al arrancar el sistema.
 9.  Obtenemos los UUID de los dispositivos
		 
			 	ls -l /dev/disk/by-uuid/
			 	
![enter image description here](http://i.imgur.com/mDVy9e5.png)		

 10. Editamos el archvo /etc/fstab y añadimos la línea siguiente con el UUID obtenido anteriormente:

		UUID=ccbbbbcc-dddd-eeee-ffff-aaabbbcccddd /dat ext2 defaults 0 0

![enter image description here](http://i.imgur.com/dISbl7r.png)
		

 11. Vamos a simular un fallo en uno de los discos y a retirar el dispositivo en caliente.

			sudo mdadm --manage --set-faulty /dev/md0 /dev/sdb
			sudo mdadm --manage --remove /dev/md0 /dev/sdb
			
![enter image description here](http://i.imgur.com/j0Zt3uk.png)
	

 12. Añadimos un nuevo disco  que vendría a reemplazar al disco que hemos retirado y comprobamos que se reconstruye correctamente.
		 
		  sudo mdadm --manage --add /dev/md0 /dev/sdb

![enter image description here](http://i.imgur.com/K5deMV6.png)
![enter image description here](http://i.imgur.com/sEXX9ht.png)
 
