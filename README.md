Raspberry Pi Start Guide
========================

[comment]: # (Markdown wiki https://es.wikipedia.org/wiki/Markdown and another good explanation http://markdown.es/sintaxis-markdown)


Índice
------

- [Enlaces de interés](#enlaces-de-interés)
- [Instalar sistema operativo](#instalar-sistema-operativo)
- [Configuración](#configuración)
	- [Averiguar la dirección ip de la Raspberry Pi 2](#averiguar-la-dirección-ip-de-la-raspberry-pi-2)
	- [Conectarse por ssh](#conectarse-por-ssh)
	- [Cambiar el password del usuario “pi”](#cambiar-el-password-del-usuario-pi)
	- [Configuración general](#configuración-general)
	- [Actualización de software](#actualización-de-software)
		- [Actualización del Kernel](#actualización-del-kernel)
	- [Red](#red)
		- [Configuración Wi-Fi en Raspbian](#configuración-wi-fi-en-raspbian)
		- [Desactivar ahorro de energía](#desactivar-ahorro-de-energía)
		- [Establecer la conexión (DHCP)](#establecer-la-conexión-dhcp)
		- [Dirección IP estática (opcional)](#dirección-ip-estática-opcional)
	- [Discos duros externos](#discos-duros-externos)
		- [Discos duros sin alimentación externa](#discos-duros-sin-alimentación-externa)
		- [Auto mount](#auto-mount)
			- [Permisos](#permisos)
- [Instalación de software](#instalación-de-software)
	- [Firewall UFW](#firewall-ufw)
	- [Baneador de IPs](#baneador-de-ips)
	- [Servidor VNCServer](#servidor-vnc)
		- [Instalación](#instalación)
		- [Auto inicio](#auto-inicio)
		- [Conexión remota](#conexión-remota)
		- [Clipboard](#clipboard)
	- [Servidor DLNA](#servidor-dlna)
	- [Centro de descargas](#centro-de-descargas)
		- [Instalar Transmission](#instalar-transmission)
		- [Configurar Transmission](#configurar-transmission)
- [Comandos básicos que hay que conocer](#comandos-básicos-que-hay-que-conocer)
	- [Mostrar información sobre el hardware](#mostrar-información-sobre-el-hardware)
	- [Los comandos más importantes](#los-comandos-más-importantes)
	- [Comandos de búsqueda](#comandos-de-búsqueda)
	- [Otros comandos útiles](#otros-comandos-útiles)
- [Alias](#alias)



Enlaces de interés
------------------

Toda esta guía ha sido elaborada pensando en la necesidad que tenía, y haciendo muchas pruebas hasta dar con la solución perfecta, o la que mejor me ha funcionado en cada caso.  
Es imposible poner todos los enlaces de los sitios que he consultado, pero si pondré los que para mi son los más importantes y los que más me han servido.

| Raspberry Pi 																| Linux 											| Otros 													|
|---------------------------------------------------------------------------|---------------------------------------------------|-----------------------------------------------------------|
| [raspberrypi.org](https://www.raspberrypi.org/) 							| [elinux.org](http://elinux.org/) 					| [techjawab.com](http://www.techjawab.com/) 				|
| [raspberrypi.org/forums](https://www.raspberrypi.org/forums/) 			| [help.ubuntu.com](https://help.ubuntu.com/) 		| [howtogeek.com](http://www.howtogeek.com/) 				|
| [raspberrypi.stackexchange.com](http://raspberrypi.stackexchange.com/) 	| [askubuntu.com](http://askubuntu.com/) 			| [wiki.contribs.org](http://wiki.contribs.org/) 			|
| [raspberryparatorpes.net](http://raspberryparatorpes.net/) 				| [esdebian.org](http://www.esdebian.org/) 			| [geekytheory.com](https://geekytheory.com/) 				|
| [raspberryshop.es](http://www.raspberryshop.es/) 							| [wiki.debian.org](https://wiki.debian.org/) 		| [belinuxmyfriend.com](http://www.belinuxmyfriend.com/) 	|
| [raspipc.es](http://www.raspipc.es/)										| [wiki.archlinux.org](https://wiki.archlinux.org/)	| [elotrolado.net](http://www.elotrolado.net/) 				|
| [modmypi.com](http://www.modmypi.com/)									| [ubuntu-mate.org](https://ubuntu-mate.org/)		| [algoentremanos.com](http://algoentremanos.com/)			|
| [raspberry.cat](http://www.raspberry.cat/) 								|													|															|



Instalar sistema operativo
--------------------------

Estos son los pasos básicos que debemos seguir para configurar inicialmente una **Raspberry Pi 2 Model B** con la distribución **Raspbian Jessie** (basado en la distribución de Linux Debian Jessie), y con un mínimo de seguridad para que no nos entren hasta la cocina.  
Para descargarla se puede hacer desde [este enlace](https://www.raspberrypi.org/downloads/raspbian/), y seguir los pasos descritos [aquí](https://www.raspberrypi.org/documentation/installation/installing-images/README.md) para preparar la imagen del sistema operativo en la tarjeta SD.

La tarjeta SD debe estar formateada en FAT32.  
Si usamos Mac, ir a *Disk Utility*, buscar la tarjeta SD y ver el número de disco que aparece en la parte inferior izquierda. Será algo como *disk2*.  
Desmontar la partición con *Unmount*. No con *Eject*, o sino deberemos montar la tarjeta de nuevo.  
Y finalmente, desde el terminal ejecutar  
`sudo dd bs=1m if=path_of_your_image.img of=/dev/rdiskn`  
donde `n` es el número de disco que hemos visto anteriormente.

Finalizados estos pasos, ya podemos insertar la tarjeta de memoria en la Raspberry Pi e iniciarla.


Configuración
-------------

### Averiguar la dirección ip de la Raspberry Pi 2

Desde la terminal del Mac  
`sudo nmap -sP 192.168.1.0/24`  
En caso de no tener instalado nmap, podemos instalar el paquete con Home Brew  
`brew install nmap`


### Conectarse por ssh

El usuario es “pi” y la contraseña inicial es “raspberry”  
`ssh pi@ip_address_of_the_raspberry`


### Cambiar el password del usuario "pi"

No es aconsejable dejar el usuario "pi" con el password por defecto  
`passwd`


### Configuración general

Hay un par de opciones de configuración que hay cambiar desde la configuración general  
`sudo raspi-config`

- **Expand Filesystem**  
Extiende el espacio utilizable por el sistema operativo a toda la capacidad de la microSD. Tras reiniciar, se puede verificar que el espacio es correcto con el comando `df -h`
- **Internationalisation Options > Change Timezone**  
Nuestra Raspberry Pi está configurada para detectar la fecha y hora desde Internet automáticamente cuando se enciende, pero la primera vez que arranca, le tendremos que indicar la zona horaria en la que nos encontramos.
- **Advanced Options > Memory Split**  
Es la memoria que le asignamos a la GPU (procesador de gráficos). Si vamos a instalar Kodi (XBMC), para que funcione perfectamente es aconsejable darle la mitad de la RAM, en la Raspberry Pi 2 tenemos 1 GB y en las demás 512 MB, así que pondremos 256 MB para la Raspberry Pi antigua o 512 MB para la Raspberry Pi 2.
- **Advanced Options > SSH**  
Es fundamental activarlo para poder acceder a la Raspberry Pi por red sin necesidad de teclado ni ratón.



### Actualización de software

Para actualizar el software, usar el comando siguiente. Puede tardar un rato.  
`sudo apt-get update && sudo apt-get upgrade`

#### Actualización del Kernel

Para ver la versión del kernel  
`uname -r`  
Si es muy antigua se puede actualizar  
`sudo rpi-update`  
Y reiniciar para que los cambios surjan efecto  
`sudo reboot`


### Red

Para configurar la red, todo dependerá de lo que queramos. En mi caso, tengo un cable ethernet y un Wi-Fi Dongle.  
Para el adaptador Wi-Fi escogí el EDIMAX EW-7811Un, 150 Mbit/s, ya que no requería de drivers adicionales ya que el Kernel ya lo tiene instalado (RTL8192CU) y lo reconoce, y el consumo es tan bajo que no requiere de alimentación adicional.  

#### Configuración Wi-Fi en Raspbian

Raspbian reconoce que el adaptador tan pronto como se inserta. Se puede comprobar esto fácilmente utilizando el comando `dmesg`  
Después de que el USB se reconoce, debería aparecer un nuevo dispositivo de red, y lo podemos ver con el uso de `ifconfig`  

#### Desactivar ahorro de energía

Antes de establecer una conexión de red inalámbrica, la función de ahorro de energía del conductor Edimax debe ser desactivada, de lo contrario la conexión se interrumpe durante la inactividad. Para evitar esto, hay que crear un archivo de configuración para el controlador:  
`sudo nano /etc/modprobe.d/8192cu.conf`

Y añadir a ese archivo el contenido siguiente:  
`options 8192cu rtw_power_mgnt=0 rtw_enusbss=0`

#### Establecer la conexión (DHCP)

Para establecer una conexión con nuestra red inalámbrica debemos editar el archivo `/etc/network/interfaces`  
`sudo nano /etc/network/interfaces`

Y adaptar el contenido:

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

auto wlan0
allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-ap-scan 1
wpa-scan-ssid 1
wpa-ssid "YOUR-WIFI-NAME"
wpa-psk "YOUR-WIFI-PASSWORD"
```

Por último, guardamos los cambios y reiniciamos el servicio de red.  
`sudo service networking restart`

#### Dirección IP estática (opcional)

Si lo que queremos es que la IP sea estática, el contenido del archivo deberá ser algo parecido a:

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.1.50
netmask 255.255.255.0
gateway 192.168.1.1

auto wlan0
allow-hotplug wlan0
iface wlan0 inet static
address 192.168.1.51
netmask 255.255.255.0
gateway 192.168.1.1
wpa-ap-scan 1
wpa-scan-ssid 1
wpa-ssid "YOUR-WIFI-NAME"
wpa-psk "YOUR-WIFI-PASSWORD"
```


### Discos duros externos

#### Discos duros sin alimentación externa

Para conectar pendrives, no hay problema, pero para conectar un disco duro que requiera alimentación por el puerto USB deberemos realizar un pequeño cambio, ya que por defecto la Raspberry Pi no da suficiente potencia por el puerto USB.  
Deberemos modificar el archivo `/boot/config.txt` y añadir `max_usb_current=1` al final de todo.  
`sudo nano /boot/config.txt`

#### Auto mount

Para montar los discos duros al inicio, hay que modificar el archivo `/etc/fstab` añadiendo las líneas correspondientes.  
`sudo nano /etc/fstab`

En mi caso, he añadido dos discos duros formateados en NTFS, para facilitar la conexión entre diferentes dispositivos y sistemas operativos.  
Para montar un disco, se puede hacer a partir del dispositivo, que encontraremos en `/dev/...` si ejecutamos `sudo fdisk -l`  
Pero para asegurar que la unidad que se va a montar es una en concreto, se puede hacer a partir del UUID del dispositivo. Si ejecutamos `sudo blkid`, o `ls -laF /dev/disk/by-uuid/`, veremos una lista de todos los dispositivos con sus identificadores.

##### Permisos

Para gestionar los permisos en los sistemas UNIX, tenemos varios comandos que debemos conocer:

- **usermod (user modify)**  
Permite modificar una cuenta de usuario.
- **chown (change owner)**  
Permite cambiar el propietario de un archivo o directorio.
- **chmod (change mode)**  
Permite cambiar los permisos de acceso de un fichero o directorio.
- **chgrp (change group)**  
Permite cambiar el grupo de usuarios de un archivo o directorio.

Una parte complicada a la hora de montar discos formateados en NTFS es el tema de los permisos, ya que con `chmod` no se pueden cambiar, y para mi gusto, usar `chown` no es bueno, ya que el propietario de los directorios no tiene porque cambiar por querer asignar permisos.  
Como me imagino que terminaré teniendo varios usuarios que necesitarán acceso a estos discos, crearé un grupo *ntfs* para darle permisos, e iré añadiendo usuarios a este grupo conforme los vaya necesitando

```
sudo groupadd ntfs            # creación del grupo
sudo usermod -a -G ntfs pi    # añado al usuario pi al nuevo grupo
sudo cat /etc/group           # listado de los grupos
id -nG pi                     # grupos a los que pertenece el usuario pi
```

Este grupo solo lo usaré en uno de los discos, que contendrá datos para modificar, el otro, que es solo de lectura, lo montaré con a configuración por defecto.  
A continuación, ya podemos modificar el arthivo `/etc/fstab` añadiendo algo parecido a

```
UUID=843671A53671993E   /media/disk1 ntfs-3g defaults,nofail 0       0
UUID=8EA0B1A2A0B19167   /media/disk2 ntfs-3g rw,auto,uid=pi,gid=ntfs,fmask=0002,dmask=0000,nofail 0       0
```

Yo he usado `ntfs-3g` y no `ntfs` para tener acceso de lectura y escritura, y he puesto `nofail` para no obtener errores al iniciar en caso de que los discos no estén conectados.  
Para forzar el montaje de los discos podemos ejecutar `sudo mount -a`

Llegados a este punto, tenemos los dos discos duros montados y funcionando. Pero al reiniciar la Raspberry Pi, nos podemos encontrar que los discos duros ya no están montados. Esto puede ocurrir si el sistema operativo de inicia antes que la detección de discos.  
Para solucionarlo, tenemos la opción de retardar el inicio añadiendo un pequeño delay. Modificaremos el archivo `/boot/cmdline.txt` ejecutando `sudo nano /boot/cmdline.txt` y añadiremos al final de todo `rootdelay=5`. Con esto el problema debería quedar resuelto.



Instalación de software
-----------------------

### Firewall UFW

Instalamos el paquete para el firewall  
`sudo apt-get install ufw`  
Permitimos el puerto 22 a todo el mundo  
`sudo ufw allow 22`  
Permitimos todos los puertos de la red local  
`sudo ufw allow from 192.168.1.0/24`  
Permitimos el puerto web a todo el mundo  
`sudo ufw allow 80`  
Habilitamos el firewall  
`sudo ufw enable`


### Baneador de IPs

Instalamos fail2ban para banear aquellas IP que intenten acceder a nuestra Raspberry Pi 2 demasiadas veces con los datos de acceso incorrectos.

```
sudo apt-get install fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo service fail2ban restart
```


### Servidor VNC

#### Instalación

Para acceder a la Raspberry Pi 2 mediante un escritorio remoto mediante VNC hay que instalar el servidor VNC  
`sudo apt-get install tightvncserver`  
Para iniciar una conexión  
`vncserver :1`  
O bien especificando los valores de geometry a la resolución de nuestra pantalla del ordenador  
`vncserver :1 -geometry 1024x600 -depth 16`  
El 1 es el número de escritorio que le asignamos. Nos pedirá que pongamos una contraseña entre 6 y 8 caracteres y ponemos **NO** a la visualización de dicha contraseña.

Para detener el servidor VNC  
`vncserver -kill :1`

#### Auto inicio

Para hacer que se ejecute el VNCServer cada vez que se inicie la Raspberry Pi, deberemos crear el siguiente archivo

```
cd /home/pi/.config/autostart
sudo nano tightvnc.desktop
```

Ponemos lo siguiente dentro del archivo y guardamos

```
[Desktop Entry]
Type=Application
Name=TightVNC
Exec=vncserver :1 -geometry 1024x600 -depth 16
StartupNotify=false
```

La próxima vez que enciendas la Raspberry el servidor VNC se reiniciará automáticamente.

#### Conexión remota

Para conectarse, desde un Mac solo hay que ir al Finder, "Go", "Connect to server..." (o Command + K), y escribir  
`vnc://ip_address_of_the_raspberry:5901`  
O desde el terminal  
`open vnc://ip_address_of_the_raspberry:5901`

A mi personalmente me gusta más el **RealVNC Viewer** que se puede descargar [aquí](http://www.realvnc.com/download/viewer/).  
En la casilla *VNC Server* pondremos la dirección IP de la Raspberry Pi y el número de escritorio  
`ip_address_of_the_raspberry:1`  
Aparecerá una ventana informando de que la conexión no está encriptada, damos a Continue, y después nos pedirá la contraseña que hemos establecido previamente cuando instalamos el VNCServer en la Raspberry Pi.  
Y finalmente tendremos el escritorio de nuestra Raspberry Pi en pantalla para controlarla con el ordenador.

#### Clipboard

Para habilitar el clipboard y tener funciones como cut, paste, y copy, debemos instalar el paquete autocutsell  
`sudo apt-get install autocutsel`  
A continuación, hay que modificar el archivo `/home/pi/.vnc/xstartup` con el comando  
`nano /home/pi/.vnc/xstartup`  
y añadir la línea `autocutsel -fork`. Debe quedar algo parecido a

```
#!/bin/sh

xrdb $HOME/.Xresources
xsetroot -solid grey
# Makes copy/paste work
autocutsel -fork
#x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
#x-window-manager &
# Fix to make GNOME work
export XKL_XMODMAP_DISABLE=1
/etc/X11/Xsession
```


### Servidor DLNA

Uno de los usos que se le puede dar a la Raspberry Pi, es como servidor DLNA, para después podernos conectar desde una Smart TV, o desde cualquier otro dispositivo y ver el contenido multimedia.  
Para ello, existe un servidor muy ligero que podemos instalar:  
`sudo apt-get install minidlna`

Una vez instalado, hay que configurarlo, y para ello debemos editar el archivo  
`sudo nano /etc/minidlna.conf`  
y modificar las líneas siguientes para que queden así, y el resto de valores los podemos dejar por defecto

```
user=minidlna

media_dir=V,/media/disk1/Movies
media_dir=A,/media/disk1/Music
media_dir=P,/media/disk1/Pictures
media_dir=V,/media/disk2/Downloads
media_dir=/media/disk2/Shared

db_dir=/var/cache/minidlna
log_dir=/var/log
friendly_name=Raspberry PI
model_number=1
inotify=yes
strict_dlna=no
enable_tivo=no
notify_interval=895
```

De todos estos parámetros, los importantes son:

- **media_dir**  
Son los directorios que se deben rastrear. Podemos indicar con las letras *V (video)*, *A (audio)* y *P (imágenes)* el tipo de contenido que queremos rastrear, o no indicar nada y que se rastree todo.
- **friendly_name**  
Es el nombre que veremos desde nuestro Smart TV u otro dispositivo cuando queramos ver el contenido.

Por último, debemos reiniciar el servidor DLNA  
`sudo service minidlna restart`  
y forzar la búsqueda de contenido  
`sudo service minidlna force-reload`  
En la documentación, pone que se puede forzar la búsqueda de contenido con el comando `minidlnad -R`, pero yo lo he probado y no funciona.


### Centro de descargas

Ya que tenemos un ordenador que consume solo 2,5W y que está siempre encendido y disponible, una buena manera de aprovechar su rendimiento es usarlo como centro de descargas.

#### Instalar Transmission

Para ello podemos instalar Transmission, un cliente de torrent  
`sudo apt-get install transmission-daemon`

#### Configurar Transmission

Primero detenemos el servicio  
`sudo service transmission-daemon stop`

Creamos los directorios necesarios para cuando se está descargando un archivo, para cuando finaliza la descarga, y para el torrent

```
sudo mkdir -p /media/hdd/downloading
sudo mkdir -p /media/hdd/sharing
sudo mkdir -p /media/hdd/torrents
```

Para dar permisos, es suficiente con añadir el usuario *debian-transmission* al grupo *ntfs* que he creado previamente  
`sudo usermod -a -G ntfs debian-transmission`

Para configurar las carpetas de descarga y otros parámetros de funcionamiento, debemos editar el archivo `/etc/transmission-daemon/settings.json` mediante el comando  
`sudo nano /etc/transmission-daemon/settings.json`  
y modificar las siguietes propiedades:

```
...
"blocklist-enabled": true,
"blocklist-url": "http://list.iblocklist.com/?list=ydxerpxkpcfqjaybcssw&fileformat=p2p&archiveformat=gz",
"download-dir": "/media/hdd/sharing",
"incomplete-dir": "/media/hdd/downloading",
"incomplete-dir-enabled": true,
"rpc-whitelist-enabled": false,
"watch-dir": "/media/hdd/torrents",
"watch-dir-enabled": true,
...
```

Que sirven para:

- **blocklist-enabled, blocklist-url**  
Es un listado de IPs bloqueadas. Se puede dejar deshabilitado, tal y como viene por defecto, o añadir una lista, para tener algo de protección.
En mi caso, he añadido una lista de [I-Blocklist](https://www.iblocklist.com/) y he activado el bloqueo.
- **download-dir**  
Directorio en el que se encontrarán los archivos una vez terminada la descarga.
- **incomplete-dir, incomplete-dir-enabled**  
Directorio en el que se realizará la descarga. Esto va a gustos, pero yo prefiero que el directorio de descarga sea distinto al directorio que contiene las descargas finalizadas.
- **rpc-whitelist-enabled**  
Listado de IPs que pueden acceder. En este caso, lo deshabilito para poder acceder libremente desde cualquier IP.
- **watch-dir, watch-dir-enabled**  
Directorio que se observará por si se añaden torrents nuevos.

Con toda la configuración terminada, ya se puede iniciar el servicio  
`sudo service transmission-daemon start`

Y se puede acceder vía web a través de la dirección  
`http://ip_address_of_the_raspberry:9091`  
y el usuario y password por defecto es `transmission:transmission`



Comandos básicos que hay que conocer
------------------------------------

### Mostrar información sobre el hardware

Para conocer la información hardware general  
`cat /proc/cpuinfo`  
Para saber el estado de la memoria  
`cat /proc/meminfo`  
Para ver las particiones de la tarjeta de memoria o el disco duro  
`cat /proc/partitions`  
Si queremos conocer la versión de nuestra Raspberry Pi  
`cat /proc/version`  
Visualizar todos los dispositivos USB conectados  
`lsusb`


### Los comandos más importantes

Para entrar en la ventana de configuración de Raspbian  
`sudo raspi-config`  
Si estamos en modo línea de comandos y queremos volver al modo gráfico  
`startx`  
Apagar el dispositivo  
`sudo poweroff` o `sudo shutdown -h`  
Reiniciar la Raspberry Pi  
`sudo reboot` o `sudo shutdown -r now`  
Para ver la temperatura  
`cat /sys/class/thermal/thermal_zone0/temp`  
Y de una forma más *user friendly*  
`/opt/vc/bin/vcgencmd measure_temp`  
Para ver los paquetes instalados  
`dpkg -l` o `apt --installed list`


### Comandos de búsqueda

Para buscar un tipo de archivo  
`find . -name .DS_Store -type f`  
Para contar cuantos archivos hay  
`find . -name *.jpg -type f | wc -l`  
O si es en el mismo directorio  
`ls *.jpg | wc -l`  
Y si queremos eliminar archivos a partir de una búsqueda (cuidado con este comando)  
`find . -name .DS_Store -type f -delete`


### Otros comandos útiles

Para crear un link (enlace o acceso directo) a un directorio  
`ln -s /home/pi/Pictures/ my-pictures`  
Y a un archivo  
`ln -s /home/pi/Pictures/Background.jpg "My background picture.jpg"`  
Para listar los procesos que se están ejecutando  
`ps aux`  
Y con información más completa  
`ps auxwf`  
Para ver el historial de comandos ejecutados en el terminal  
`history`


Alias
-----

Algunos comandos pueden resultar incómodos de teclear debido al gran número de parámetros, más aún cuando los usamos muy a menudo.  
Los alias son códigos mnemotécnicos para acordarnos mejor.  
Para crearlos, hay que modificar el archivo `~/.bashrc` escribiendo  
`sudo nano ~/.bashrc`  
añadiendo nuestros alias al final de todo del archivo. Si por ejemplo nos cuesta acordarnos de como ver la temperatura, o el espacio de las unidades, o los paquetes instalados, podemos tener alias como

```
alias temperature='/opt/vc/bin/vcgencmd measure_temp'
alias space='df -h'
alias packages='dpkg -l'
```

Y simplemente escribiendo en el terminal `temperature`, `space` o `packages` obtendríamos el mismo resultado que ejecutando la instrucción más compleja.  
Finalmente recargarlo para que los cambios surjan efecto  
`source ~/.bashrc`
