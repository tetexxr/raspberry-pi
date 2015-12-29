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
	- [Idioma](#idioma)
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
	- [Compartir ficheros con samba](#compartir-ficheros-con-samba)
		- [Instalar samba](#instalar-samba)
		- [Configurar samba](#configurar-samba)
		- [Compartir carpetas](#compartir-carpetas)
		- [Establecer contraseña](#establecer-contraseña)
	- [Servidor DLNA](#servidor-dlna)
	- [Centro de descargas](#centro-de-descargas)
		- [Instalar Transmission](#instalar-transmission)
		- [Configurar Transmission](#configurar-transmission)
		- [Deluge](#deluge)
	- [Medir ancho de banda](#medir-ancho-de-banda)
	- [Kodi](#kodi)
	- [Plex](#plex)
		- [Requerimientos de pre-instalación](#requerimientos-de-pre-instalación)
		- [Instalación de Plex Media Server](#instalación-de-plex-media-server)
- [Comandos básicos que hay que conocer](#comandos-básicos-que-hay-que-conocer)
	- [Mostrar información sobre el hardware](#mostrar-información-sobre-el-hardware)
	- [Los comandos más importantes](#los-comandos-más-importantes)
	- [Comandos de búsqueda](#comandos-de-búsqueda)
	- [Otros comandos útiles](#otros-comandos-útiles)
- [Alias](#alias)
- [Backup & Restore](#backup--restore)
	- [Reducir la imagen al espacio usado](#reducir-la-imagen-al-espacio-usado)
		- [Crear dispositivo loopback](#crear-dispositivo-loopback)
		- [Cambiar el tamaño de la partición usando GParted](#cambiar-el-tamaño-de-la-partición-usando-gparted)
		- [Encogiendo la imagen](#encogiendo-la-imagen)



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
Para descargarla se puede hacer desde [aquí](https://www.raspberrypi.org/downloads/raspbian/), y seguir los pasos descritos [aquí](https://www.raspberrypi.org/documentation/installation/installing-images/README.md) para preparar la imagen del sistema operativo en la tarjeta SD.

La tarjeta SD debe estar formateada en FAT32.  
Si usamos Mac, ir a *Disk Utility*, buscar la tarjeta SD y ver el número de disco que aparece en la parte inferior izquierda. Será algo como *disk2*.  
Desmontar la partición con *Unmount*. No con *Eject*, o sino deberemos montar la tarjeta de nuevo.  
Y finalmente, desde el terminal ejecutar:  
`sudo dd bs=1m if=path_of_your_image.img of=/dev/rdiskn`  
donde *n* es el número de disco que hemos visto anteriormente.

En caso de obtener un error parecido a `dd: bs: illegal numeric value` poner el valor del parámetro `bs=1M`. Esto puede ocurrir dependiendo de si tenemos *GNU coreutils* instalado o no.

Finalizados estos pasos, ya podemos insertar la tarjeta de memoria en la Raspberry Pi e iniciarla.


Configuración
-------------

### Averiguar la dirección IP de la Raspberry Pi 2

Desde la terminal del Mac:  
`sudo nmap -sP 192.168.1.0/24`  
En caso de no tener instalado nmap, podemos instalar el paquete con Home Brew:  
`brew install nmap`  
Podemos consultar el manual con `man nmap`.


### Conectarse por ssh

El usuario es “pi” y la contraseña inicial es “raspberry”:  
`ssh pi@ip_address_of_the_raspberry`  
Podemos consultar el manual con `man ssh`.


### Cambiar el password del usuario "pi"

No es aconsejable dejar el usuario "pi" con el password por defecto:  
`passwd`
Podemos consultar el manual con `man passwd`.


### Configuración general

Hay un par de opciones de configuración que hay cambiar desde la configuración general:  
`sudo raspi-config`

- **Expand Filesystem**  
Extiende el espacio utilizable por el sistema operativo a toda la capacidad de la microSD. Tras reiniciar, se puede verificar que el espacio es correcto con el comando `df -h`.
- **Internationalisation Options**  
	- **Change Locale**  
	Permite cambiar el idioma. Yo he asignado *en-US.UTF8*.  
	- **Change Timezone**  
	Nuestra Raspberry Pi está configurada para detectar la fecha y hora desde Internet automáticamente cuando se enciende, pero la primera vez que arranca, le tendremos que indicar la zona horaria en la que nos encontramos.
	- **Change Keyboard Layout**  
	Permite asignar el tipo de teclado que tengamos. En mi caso, he puesto teclado español.  
- **Advanced Options > Memory Split**  
Es la memoria que le asignamos a la GPU (procesador de gráficos). Si vamos a instalar Kodi (XBMC), para que funcione perfectamente es aconsejable darle la mitad de la RAM, en la Raspberry Pi 2 tenemos 1 GB y en las demás 512 MB, así que pondremos 256 MB para la Raspberry Pi antigua o 512 MB para la Raspberry Pi 2.
- **Advanced Options > SSH**  
Es fundamental activarlo para poder acceder a la Raspberry Pi por red sin necesidad de teclado ni ratón.


### Idioma

Para evitar que aparezcan continuamente los warnings de perl tales como:

```
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_CTYPE = "UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").
```

lo que podemos hacer es ver las entradas de idioma que faltan por asignar y asignarlas. Con el comando `locale` podemos ver el listado completo, y para asignarlas:

```
export LANGUAGE=en_US.UTF-8
export LC_ALL=en_US.UTF-8
export LC_TYPE=en_US.UTF-8
export LANG=en_US.UTF-8
```

Finalmente, para recrear y configurar los idiomas:

```
sudo locale-gen
sudo dpkg-reconfigure locales
```



### Actualización de software

Para actualizar el software a las últimas versiones:  
`sudo apt-get update && sudo apt-get upgrade`   
Y si además queremos actualizar los posibles cambios de dependencias:  
`sudo apt-get update && sudo apt-get dist-upgrade`  
Podemos consultar el manual con `man apt-get`.

#### Actualización del Kernel

Para ver la versión del Kernel (o del firmware):  
`uname -r`  
Si es muy antigua se puede actualizar:  
`sudo rpi-update`  
Y reiniciar para que los cambios surjan efecto:  
`sudo reboot`  
No se recomienda usar estos comandos, ya que en un 99% de los casos con usar `apt-get upgrade` hay suficiente.


### Red

Para configurar la red, todo dependerá de lo que queramos. En mi caso, tengo un cable ethernet y un Wi-Fi Dongle.  
Para el adaptador Wi-Fi escogí el EDIMAX EW-7811Un, 150 Mbit/s, ya que no requería de drivers adicionales ya que el Kernel ya lo tiene instalado (RTL8192CU) y lo reconoce, y el consumo es tan bajo que no requiere de alimentación adicional.  

#### Configuración Wi-Fi en Raspbian

Raspbian reconoce que el adaptador tan pronto como se inserta. Se puede comprobar esto fácilmente utilizando el comando `dmesg`.  
Después de que el USB se reconoce, debería aparecer un nuevo dispositivo de red, y lo podemos ver con el uso de `ifconfig`.

#### Desactivar ahorro de energía

Antes de establecer una conexión de red inalámbrica, la función de ahorro de energía del conductor Edimax debe ser desactivada, de lo contrario la conexión se interrumpe durante la inactividad. Para evitar esto, hay que crear un archivo de configuración para el controlador:  
`sudo nano /etc/modprobe.d/8192cu.conf`

Y añadir a ese archivo el contenido siguiente:  
`options 8192cu rtw_power_mgnt=0 rtw_enusbss=0`

#### Establecer la conexión (DHCP)

Para establecer una conexión con nuestra red inalámbrica debemos editar el archivo de configuración de red:  
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

Por último, guardamos los cambios y reiniciamos el servicio de red:  
`sudo service networking restart`

En la configuración de red anterior, hay un par de temas importantes a remarcar. Cuando se usa el `auto` para inicializar una interfaz, lo que se indica es que esa interfaz de red se inicializa al iniciar el sistema, y cuando se usa el `allow-hotplug` se inicializa la interfaz de red cuando el Kernel detecta un evento de cambio desde la interfaz, como puede ser conectar un cable de red, o conectar un adaptador de Wi-Fi en el puerto USB.

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
Deberemos modificar el archivo `/boot/config.txt` mediante el comando `sudo nano /boot/config.txt` y añadir `max_usb_current=1` al final de todo.

#### Auto mount

Para montar los discos duros al inicio, hay que modificar el archivo `/etc/fstab` añadiendo las líneas correspondientes.  
`sudo nano /etc/fstab`

En mi caso, he añadido dos discos duros formateados en NTFS, para facilitar la conexión entre diferentes dispositivos y sistemas operativos.  
Para montar un disco, se puede hacer a partir del dispositivo, que encontraremos en `/dev/...` si ejecutamos `diskutil list`.  
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
Como me imagino que terminaré teniendo varios usuarios que necesitarán acceso a estos discos, crearé un grupo *ntfs* para darle permisos, e iré añadiendo usuarios a este grupo conforme los vaya necesitando:

```
sudo groupadd ntfs            # creación del grupo
sudo usermod -a -G ntfs pi    # añado al usuario pi al nuevo grupo
sudo cat /etc/group           # listado de los grupos
id -nG pi                     # grupos a los que pertenece el usuario pi
```

Este grupo solo lo usaré en uno de los discos, que contendrá datos para modificar, el otro, que es solo de lectura, lo montaré con a configuración por defecto.  
A continuación, ya podemos modificar el arthivo `/etc/fstab` añadiendo algo parecido a:

```
UUID=843671A53671993E   /media/disk1 ntfs-3g defaults,nofail 0       0
UUID=8EA0B1A2A0B19167   /media/disk2 ntfs-3g rw,auto,uid=pi,gid=ntfs,fmask=0002,dmask=0000,nofail 0       0
```

Yo he usado `ntfs-3g` y no `ntfs` para tener acceso de lectura y escritura, y he puesto `nofail` para no obtener errores al iniciar en caso de que los discos no estén conectados.  
Para forzar el montaje de los discos podemos ejecutar `sudo mount -a`.

Llegados a este punto, tenemos los dos discos duros montados y funcionando. Pero al reiniciar la Raspberry Pi, nos podemos encontrar que los discos duros ya no están montados. Esto puede ocurrir si el sistema operativo de inicia antes que la detección de discos.  
Para solucionarlo, tenemos la opción de retardar el inicio añadiendo un pequeño delay. Modificaremos el archivo `/boot/cmdline.txt` ejecutando `sudo nano /boot/cmdline.txt` y añadiremos al final de todo `rootdelay=5`. Con esto el problema debería quedar resuelto.



Instalación de software
-----------------------

### Firewall UFW

Instalamos el paquete para el firewall:  
`sudo apt-get install ufw`  
Permitimos el puerto 22 a todo el mundo:  
`sudo ufw allow 22`  
Permitimos todos los puertos de la red local:  
`sudo ufw allow from 192.168.1.0/24`  
Permitimos el puerto web a todo el mundo:  
`sudo ufw allow 80`  
Habilitamos el firewall:  
`sudo ufw enable`  
Podemos consultar el manual con `man ufw`.


### Baneador de IPs

Instalamos fail2ban para banear aquellas IP que intenten acceder a nuestra Raspberry Pi 2 demasiadas veces con los datos de acceso incorrectos.

```
sudo apt-get install fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo service fail2ban restart
```

Podemos consultar el manual con `man fail2ban`.


### Servidor VNC

#### Instalación

Para acceder a la Raspberry Pi 2 mediante un escritorio remoto mediante VNC hay que instalar el servidor VNC:  
`sudo apt-get install tightvncserver`  
Para iniciar una conexión:  
`vncserver :1`  
O bien especificando los valores de geometry a la resolución de nuestra pantalla del ordenador:  
`vncserver :1 -geometry 1024x600 -depth 16`  
El 1 es el número de escritorio que le asignamos. Nos pedirá que pongamos una contraseña entre 6 y 8 caracteres y ponemos **NO** a la visualización de dicha contraseña.

Para detener el servidor VNC:  
`vncserver -kill :1`

#### Auto inicio

Para hacer que se ejecute el VNCServer cada vez que se inicie la Raspberry Pi, deberemos crear el siguiente archivo:

```
cd /home/pi/.config
mkdir -p autostart
cd autostart
sudo nano tightvnc.desktop
```

Ponemos lo siguiente dentro del archivo y guardamos:

```
[Desktop Entry]
Type=Application
Name=TightVNC
Exec=vncserver :1 -geometry 1024x600 -depth 16
StartupNotify=false
```

La próxima vez que enciendas la Raspberry el servidor VNC se reiniciará automáticamente.

#### Conexión remota

Para conectarse, desde un Mac solo hay que ir al Finder, "Go", "Connect to server..." (o Command + K), y escribir:  
`vnc://ip_address_of_the_raspberry:5901`  
O desde el terminal:  
`open vnc://ip_address_of_the_raspberry:5901`

A mi personalmente me gusta más el **RealVNC Viewer** que se puede descargar [aquí](http://www.realvnc.com/download/viewer/).  
En la casilla *VNC Server* pondremos la dirección IP de la Raspberry Pi y el número de escritorio:  
`ip_address_of_the_raspberry:1`  
Aparecerá una ventana informando de que la conexión no está encriptada, damos a *Continue*, y después nos pedirá la contraseña que hemos establecido previamente cuando instalamos el VNCServer en la Raspberry Pi.  
Y finalmente tendremos el escritorio de nuestra Raspberry Pi en pantalla para controlarla con el ordenador.

#### Clipboard

Para habilitar el clipboard y tener funciones como cut, paste, y copy, debemos instalar el paquete autocutsell:  
`sudo apt-get install autocutsel`  
A continuación, hay que modificar el archivo `/home/pi/.vnc/xstartup` con el comando:  
`nano /home/pi/.vnc/xstartup`  
y añadir la línea `autocutsel -fork`. Debe quedar algo parecido a:

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

Podemos consultar el manual con `man vncserver`.


### Compartir ficheros con samba

Hasta ahora para copiar archivos a la Raspberry Pi teníamos que usar el comando `scp` o el protocolo *SFTP*, pero gracias a *samba* no solo podemos acceder más cómodamente a nuestros archivos, sino transformar nuestra Raspberry Pi en un servidor NAS. 

#### Instalar samba

Para instalar los paquetes necesarios ejecutaremos:  
`sudo apt-get install samba samba-common-bin`

#### Configurar samba

Si nos fijamos en el fichero de configuración veremos que está muy bien comentado:  
`sudo nano /etc/samba/smb.conf`

Hay dos parámetros importantes cuyos valores deben ser:

```
workgroup = WORKGROUP
wins support = yes
```

- **workgroup**  
Es el grupo de trabajo de la red, y por defecto viene el que Windows da de serie a sus redes.
- **wins support**  
Aparecerá comentado, con una almohadilla delante. Es muy importante que descomentes esa línea (quita la #). Da igual si vas a utilizarlo o no, eso ya lo decides tú poniendo yes o no, pero la configuración del samba debe leer ese parámetro para que todo funcione correctamente.

#### Compartir carpetas

Para compartir carpetas iremos al final del fichero y escribiremos esto:

```
[UsbDisk1]
	comment=USB HDD 1 in Raspberry Pi
	path=/media/disk1
	browseable=yes
	writeable=yes
	create mask=0775
	directory mask=0775
	only guest=no
```

- Donde la primera línea define el nombre que tendrá nuestra carpeta compartida en la red, en mi caso *UsbDisk1*.
- **comment** puede ser cualquier cosa que quieras que defina a la carpeta.
- **path** es la ruta de la carpeta que quieres compartir, como yo comparto todo el disco duro USB especifico dónde está montado.
- **browseable** para que se pueda navegar por los subdirectorios.
- **writeable** para poder leer y escribir.
- **create mask** y **directory mask** definen los permisos que se aplican a los ficheros y directorios nuevos.
- **only guest** permite acceder como usuario anónimo o no.

Para compartir más carpetas escribir todo el bloque de nuevo con los datos de las nuevas carpetas a compartir.

#### Establecer contraseña

Por último, vamos a darle una contraseña a nuestro usuario *pi*, la lógica diría que es la misma que para acceder al sistema, pero no, samba tiene su propia gestión de contraseñas así que hay asignarle una a cada usuario, puedes utilizar la misma para no liarte u otra diferente por seguridad:  
`sudo smbpasswd -a pi`

Para terminar vamos a reiniciar el servicio para que todos los cambios surjan efecto:  
`sudo service smbd restart`  
Podemos consultar el manual con `man smbd`.


### Servidor DLNA

Uno de los usos que se le puede dar a la Raspberry Pi, es como servidor DLNA, para después podernos conectar desde una Smart TV, o desde cualquier otro dispositivo y ver el contenido multimedia.  
Para ello, existe un servidor muy ligero que podemos instalar:  
`sudo apt-get install minidlna`

Una vez instalado, hay que configurarlo, y para ello debemos editar el archivo:  
`sudo nano /etc/minidlna.conf`  
y modificar las líneas siguientes para que queden así, y el resto de valores los podemos dejar por defecto:

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

Por último, debemos reiniciar el servidor DLNA:  
`sudo service minidlna restart`  
y forzar la búsqueda de contenido:  
`sudo service minidlna force-reload`  
En la documentación, pone que se puede forzar la búsqueda de contenido con el comando `minidlnad -R`, pero yo lo he probado y no funciona.  
Si quisieramos que el servicio no se iniciara al arrancar el sistema deberíamos ejecutar:  
`sudo update-rc.d -f minidlna remove`  
Podemos consultar el manual con `man minidlna`.


### Centro de descargas

Ya que tenemos un ordenador que consume solo 2,5W y que está siempre encendido y disponible, una buena manera de aprovechar su rendimiento es usarlo como centro de descargas.

#### Instalar Transmission

Para ello podemos instalar Transmission, un cliente de torrent:  
`sudo apt-get install transmission-daemon`

#### Configurar Transmission

Primero detenemos el servicio:  
`sudo service transmission-daemon stop`

Creamos los directorios necesarios para cuando se está descargando un archivo, para cuando finaliza la descarga, y para el torrent:

```
sudo mkdir -p /media/hdd/downloading
sudo mkdir -p /media/hdd/sharing
sudo mkdir -p /media/hdd/torrents
```

Para dar permisos, es suficiente con añadir el usuario *debian-transmission* al grupo *ntfs* que he creado previamente:  
`sudo usermod -a -G ntfs debian-transmission`

Para configurar las carpetas de descarga y otros parámetros de funcionamiento, debemos editar el archivo de configuración:  
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

Con toda la configuración terminada, ya se puede iniciar el servicio:  
`sudo service transmission-daemon start`

Para no iniciar el servicio al arrancar el sistema deberíamos editar el archivo:  
`sudo nano /etc/default/transmission-daemon`  
Y asignar la propiedad `ENABLE_DAEMON=0`.  
U otra manera sería deshabilitar el servicio:  
`sudo systemctl disable transmission-daemon.service`

Y se puede acceder vía web a través de la dirección:  
`http://ip_address_of_the_raspberry:9091`  
y el usuario y password por defecto es `transmission:transmission`.  
Podemos consultar el manual con `man transmission-daemon`.

#### Deluge

Otra opción que tenemos a la hora de elegir un cliente de torrent es *Deluge*. Este ofrece algunas características extras de configuración con el mismo consumo de recursos que *Transmission*.  
Para instararlo, debemos ejecutar en nuestra consola:  

```
sudo apt-get update
sudo apt-get install deluge-common deluged deluge-web deluge-console
```

Con esto tenemos Deluge instalado, y para hacer que se ejecute al arrancar el sistema, debemos ejecutar el siguiente comando:  
`sudo nano /etc/systemd/system/deluged.service`

En el editor copiaremos el siguiente código:

```
[Unit]
Description=Deluge Bittorrent Client Daemon
After=network.target

[Service]
Type=simple
User=pi
Group=users
UMask=007

Environment=LC_ALL=en_US.UTF-8
Environment=LANG="en_US.UTF-8"
Environment=LANGUAGE="en_US:en"

ExecStart=/usr/bin/deluged -d

Restart=Always
TimeoutStopSec=300

[Install]
WantedBy=multi-user.target
```

También queremos iniciar automáticamente la interfaz web:  
`sudo nano /etc/systemd/system/deluge-web.service`

Y pegamos el código:

```
[Unit]
Description=Deluge Bittorrent Client Web Interface
After=network.target

[Service]
Type=simple

User=pi
Group=users
UMask=027

ExecStart=/usr/bin/deluge-web

Restart=Always

[Install]
WantedBy=multi-user.target
```

Además, debemos activar el arranque al inicio, para ello ejecutamos estos comandos:  

```
sudo systemctl enable deluged
sudo systemctl enable deluge-web
```

Además, Sam Nazarko nos ha dejado un par de ficheros de lo que probablemente será una instalación desde la App Store en un futuro. Pero ahora nos dan problemas, por lo que debemos borrar los ficheros "deluge*" de los directorios "/etc/init.d" y "/etc/default".

```
sudo rm /etc/init.d/deluged
sudo rm /etc/default/deluged
```

Con esto ya tendremos Deluge instalado y funcionando. Podemos reiniciar la Raspberry Pi y acceder a la dirección:  
`http://ip_address_of_the_raspberry:8112`

Si todo ha ido bien, nos saldrá la interfaz web de Deluge, nos preguntará un password, que por defecto es *deluge* y nos dirá que lo cambiemos en la primera ejecución (nos lleva directamente a la pantalla de cambio de la contraseña).

Para terminar con Deluge, nos falta por hacer un par de configuraciones. Si vamos al directorio `/home/pi/.config/deluge` veremos que nos ha creado un fichero llamado "auth". Editamos dicho fichero con el siguiente comando:  
`sudo nano /home/pi/.config/deluge/auth`

Aquí insertaremos una nueva línea, con la siguiente estructura:  
`user:password:10`

Siendo el usuario y la contraseña los que deseemos para conectarnos a Deluge, ya sea mediante un cliente externo o bien mediante flexget.  
Para terminar, abriremos Deluge para poder conectarnos desde fuera de nuestra red local, mediante el siguiente comando:  
`deluge-console "config -s allow_remote True"`

Si reiniciamos, veremos que ya nos podemos conectar desde fuera o bien desde el cliente de nuestra plataforma (descargable desde la web de Deluge), usando el usuario y la contraseña creados anteriormente.


### Medir ancho de banda

Descargamos un script en Python que se encargará de medir la velocidad de la red:  
`wget -O speedtest-cli https://raw.github.com/sivel/speedtest-cli/master/speedtest_cli.py`  
Damos permisos de ejecución:  
`chmod +x speedtest-cli`  
Y ejecutamos el test:  
`./speedtest-cli`


### Kodi

Kodi (antes conocido como "Xbox Media Center" o XBMC) es un centro multimedia de entretenimiento multiplataforma.  
Kodi soporta una amplia gama de formatos multimedia, e incluye características tales como listas de reproducción, visualizaciones de audio, presentación de diapositivas, reportes del clima y ampliación de funciones mediante plug-ins. Como Media Center, Kodi puede reproducir la mayoría de los formatos de audio y vídeo (además de ver subtítulos y resincronizar éstos y el audio en caso de delay), así como mostrar imágenes prácticamente de cualquier fuente, incluidos CD, DVD, dispositivos de almacenamiento masivo, Internet y LAN shares.

Para instalar Kodi 15.2RC3 para Jessie, hay que ejecutar en el terminal:

```
wget http://steinerdatenbank.de/software/kodi-15-jessie.tar.gz
tar -xzf kodi-15-jessie.tar.gz
cd kodi-15-jessie
sudo ./install
```

Es recomendable excluir Kodi de ser actualizado automáticamente en Raspbian a causa de las nuevas actualizaciones de Kodi que pueden venir por la ejecución del comando `dist-upgrade` ya que estas pueden hacer que Kodi se rompa o deje de iniciarse correctamente.  
Para excluirlo ejecutar:  
`sudo apt-mark hold kodi`

Para poder usar el mando a distancia de la tele hay que instalar libcec3:  
`sudo apt-get install libcec3`

Si quisieramos desinstalarlo todo, deberíamos ejecutar:

```
sudo dpkg -r libplatform1
sudo dpkg -r libcec3
sudo dpkg -r kodi
```

Si estamos instalando Kodi por primera vez, debemos añadir algunas configuraciones a nuestro sistema.  
Hay que añadir el grupo *input* en caso de que no exista:  
`sudo addgroup --system input`
Hay que crear y editar el siguiente archivo:  
`sudo nano /etc/udev/rules.d/99-input.rules`  
Y añadir el contenido:

```
SUBSYSTEM==input, GROUP=input, MODE=0660
KERNEL==tty[0-9]*, GROUP=tty, MODE=0660
```

Crear y editar este otro archivo:  
`sudo nano /etc/udev/rules.d/10-permissions.rules`  
Y añadir el contenido:

```
# input
KERNEL==mouse*|mice|event*,   MODE=0660, GROUP=input
KERNEL==ts[0-9]*|uinput,      MODE=0660, GROUP=input
KERNEL==js[0-9]*,             MODE=0660, GROUP=input
# tty
KERNEL==tty[0-9]*,            MODE=0666
# vchiq
SUBSYSTEM==vchiq,             MODE=0660, GROUP=video
```

Hay que ejecutar estos comandos para el usuario *pi*, en caso de que no lo hayas cambiado:

```
sudo usermod -a -G audio pi
sudo usermod -a -G video pi
sudo usermod -a -G input pi
sudo usermod -a -G dialout pi
sudo usermod -a -G plugdev pi
sudo usermod -a -G tty pi
```

Y asignar más memoria para la GPU editando el archivo:  
`sudo nano /boot/config.txt`  
añadiendo o modificando el siguiente valor:  
`gpu_mem=256`

Para iniciarlo debemos ejecutar:  
`kodi-standalone`  
O para iniciarlo en background:  
`kodi-standalone &`


### Plex

Plex organiza video, música y fotos de librerías de medios personales y hace stream de estos a smart TVs, reproductores de medios y dispositivos móviles.  
Mediante la interfaz web de Plex https://plex.tv/ podemos configurar y ver nuestro contenido desde cualquier sitio.

#### Requerimientos de pre-instalación

Verificar que tenemos el idioma en-US instalado:  
`locale -a`

La salida tiene que ser algo parecido a:

```
C
C.UTF-8
en_US.utf8
POSIX
```

Verificar que tenemos la versión 2.19 o superior de libc6:  
`ldd --version`

#### Instalación de Plex Media Server

Habilitar transporte https:  
`sudo apt-get update && sudo apt-get install apt-transport-https -y --force-yes`  
Añadir la clave gpg para el repositorio uglymaoo:  
`wget -O - https://dev2day.de/pms/dev2day-pms.gpg.key | sudo apt-key add -`  
Añadir el repositorio uglymaoo:  
`echo "deb https://dev2day.de/pms/ jessie main" | sudo tee /etc/apt/sources.list.d/pms.list`  
Actualizar la lista de paquetes:   
`sudo apt-get update`  
Instalar Plex Media Server:  
`sudo apt-get install plexmediaserver -y`  
Cuando salga una nueva version se puede actualizar manualmente con el comando:  
`sudo apt-get update && sudo apt-get upgrade -y`  

Para acceder a Plex se puede hacer desde la url:  
`http://ip_address_of_the_raspberry:32400/web`

Si no se ha iniciado, puede que sea debido a un fallo de systemd / init.d que puede ocurrir si estamos en Debian Jessie, y para iniciar Plex de forma manual podemos ejecutar:  
`sudo bash /usr/lib/plexmediaserver/start.sh &`

Finalmente, si queremos acceder desde fuera de la red local, deberemos abrir los puertos de la Raspberry Pi. En caso de usar *ufw firewall*:  
`sudo ufw allow 32400`




Comandos básicos que hay que conocer
------------------------------------

### Mostrar información sobre el hardware

Para conocer la información hardware general:  
`cat /proc/cpuinfo`  
Para saber el estado de la memoria:  
`cat /proc/meminfo`  
Para ver las particiones de la tarjeta de memoria o el disco duro:  
`cat /proc/partitions`  
Si queremos conocer la versión de nuestra Raspberry Pi:  
`cat /proc/version`  
Visualizar todos los dispositivos USB conectados:  
`lsusb`


### Los comandos más importantes

Para entrar en la ventana de configuración de Raspbian:  
`sudo raspi-config`  
Si estamos en modo línea de comandos y queremos volver al modo gráfico:  
`startx`  
Apagar el dispositivo:  
`sudo poweroff` o `sudo shutdown -h`  
Reiniciar la Raspberry Pi:  
`sudo reboot` o `sudo shutdown -r now`  
Para ver la temperatura:  
`cat /sys/class/thermal/thermal_zone0/temp`  
Y de una forma más *user friendly*:  
`/opt/vc/bin/vcgencmd measure_temp`  
Para ver los paquetes instalados:  
`dpkg -l` o `apt --installed list`


### Comandos de búsqueda

Para buscar un tipo de archivo:  
`find . -name .DS_Store -type f`  
Para contar cuantos archivos hay:  
`find . -name *.jpg -type f | wc -l`  
O si es en el mismo directorio:  
`ls *.jpg | wc -l`  
Y si queremos eliminar archivos a partir de una búsqueda (cuidado con este comando):  
`find . -name .DS_Store -type f -delete`


### Otros comandos útiles

Para crear un link (enlace o acceso directo) a un directorio:  
`ln -s /home/pi/Pictures/ my-pictures`  
Y a un archivo:  
`ln -s /home/pi/Pictures/Background.jpg "My background picture.jpg"`  
Para listar los procesos que se están ejecutando:  
`ps aux`  
Y con información más completa:  
`ps auxwf`  
Para ver el historial de comandos ejecutados en el terminal:  
`history`



Alias
-----

Algunos comandos pueden resultar incómodos de teclear debido al gran número de parámetros, más aún cuando los usamos muy a menudo.  
Los alias son códigos mnemotécnicos para acordarnos mejor.  
Para crearlos, hay que modificar el archivo:  
`sudo nano ~/.bashrc`  
añadiendo nuestros alias al final de todo del archivo. Si por ejemplo nos cuesta acordarnos de como ver la temperatura, o el espacio de las unidades, o los paquetes instalados, podemos tener alias como:

```
alias temperature='/opt/vc/bin/vcgencmd measure_temp'
alias space='df -h'
alias packages='dpkg -l'
```

Y simplemente escribiendo en el terminal `temperature`, `space` o `packages` obtendríamos el mismo resultado que ejecutando la instrucción más compleja.  
Finalmente recargarlo para que los cambios surjan efecto:  
`source ~/.bashrc`



Backup & Restore
----------------

Realizar toda esta configuración y llegar a tener nuestra Raspberry Pi como nos gusta puede ser una inversión de tiempo considerable, con lo que realizar una copia de seguridad de todo el contenido de nuestra tarjeta SD es más que aconsejable.  
Para ello, desde nuestro Mac, tenemos que averiguar el identificador del disco, y para ello o usamos la aplicación *Disk Utility* o el comando `diskutil list`.

Una vez tenemos la tarjeta identificada, realizamos la copia de seguridad con el comando:  
`sudo dd bs=1m if=/dev/rdiskn of=path_of_your_image.img`

Y en caso de querer clonar la tarjeta, o restaurar en caso de fallo, deberíamos tener la nueva tarjeta formateada en FAT32, y para poder formatearla, primero debemos localizar las unidades montadas:  
`diskutil list`  
desmontarlas (repetir para cada unidad montada):  
`diskutil unmount /dev/disknsx # por ejemplo /dev/disk2s5`  
ahora ya podemos formatear:  
`sudo newfs_msdos -F 32 /dev/diskn`  
y para restaurar la imagen usaríamos:  
`sudo dd bs=1m if=path_of_your_image.img of=/dev/rdiskn`

Donde *n* de *rdiskn* es el número de disco que hemos localizado anteriormente.

En caso de obtener un error parecido a `dd: bs: illegal numeric value` poner el valor del parámetro `bs=1M`. Esto puede ocurrir dependiendo de si tenemos *GNU coreutils* instalado o no.  
Si durante el proceso de ejecución del comando `dd` queremos saber el estado, podemos pulsar <kbd>CONTROL</kbd> + <kbd>T</kbd>.


### Reducir la imagen al espacio usado

Al crear una imagen con el comando `dd` el resultado será del mismo tamaño que la tarjeta SD. Es decir, si tenemos una tarjeta SD de 8 GB el resultado será un archivo *img* de 8 GB.  
En el caso de tener una tarjeta de 32 GB o de 64 GB el tema es un poco más crítico, ya que el archivo de imagen ocupará eso mismo y además necesitaremos otra tarjeta del mismo tamaño para poder restaurar la imagen, y muchas veces podemos tener una tarjeta de ese tamaño pero que el contenido de la misma no supere los 4 GB por ejemplo.

Para conseguir esto a partir de una imagen y no morir en el intento, lo vamos a hacer en Linux, ya que todas las herramientas que necesitamos están disponibles allí: GParted, `fdisk` y `truncate`. En este caso, yo he usado la versión 14.14.1 de Ubuntu x64, y para instalar GParted escribimos en el terminal:  
`sudo apt-get install gparted`

#### Crear dispositivo loopback

GParted es una gran aplicación que puede manejar tablas de particiones y archivos de sistema bastante bien. En este caso vamos a usar GParted para reducir el tamaño del sistema de archivos.  
GParted opera en los dispositivos, no en archivos simples como imágenes. Es por eso que primero necesitamos crear un dispositivo para la imagen. Haremos esto mediante la funcionalidad loopback de Linux.

Primero habilitaremos loopback si no estaba ya habilitado:  
`sudo modprobe loop`  
Ahora ya podemos solicitar un nuevo dispositivo (libre) loopback:  
`sudo losetup -f`  
Esto devolverá el path a un dispositivo loopback libre. En este ejemplo es `/dev/loop0`.  
A continuación, creamos un dispositivo de la imagen:  
`sudo losetup /dev/loop0 myimage.img`  
Ahora tenemos un dispositovo `/dev/loop0` que representa a `myimage.img`. Lo que queremos nosotros es acceder a las particiones que hay en la imagen, con lo que tenemos que pedir al Kernel que las cargue también:  
`sudo partprobe /dev/loop0`  
Esto debería darnos el dispositivo `/dev/loop0p1` que representa la primera partición en `myimage.img`. Nosotros no lo necesitamos directamente, pero GParted lo requiere.

#### Cambiar el tamaño de la partición usando GParted

A continuación podemos cargar el dispositivo usando GParted:  
`sudo gparted /dev/loop0`  
Esto debería mostrar una ventana de GParted, en la que veremos:

- Hay una partición.
- La partición ocupa todo el disco/dispositivo/imagen.
- La partición está parcialmente llena.

Queremos cambiar el tamaño de esta partición para que se adapte a su contenido, pero no más que eso.

1. Selecciona la partición y haz clic en *Resize/Move*.  
2. Arrastra la barra de la derecha a la izquierda tanto como sea posible.  
Hay que tener en cuenta que a veces GParted necesitará algunos MB adicionales para colocar algunos datos relacionados con el sistema de archivos.  
3. Finalmente pulsamos *Resize/Move* y volveremos a la ventana de GParted.  
Observa que hay una parte del disco *unallocated*. Esta parte del disco no será utilizada por la partición, con lo que la podremos eliminar de la imagen. GParted es una herramienta para discos, no hace *shrink* de imágenes, solo particiones, con lo que el *shrink* de la imagen deberemos hacerlo nosotros mismos.  
4. Pulsar *Apply* en GParted. Ahora es cuando moverá los archivos y finalmente encogerá la partición, con lo que puede tardar algún minuto. Cuando termine, podemos cerrar GParted.

Ahora ya no necesitamos más el dispositivo loopback, con lo que podemos descargarlo:  
`sudo losetup -d /dev/loop0`

#### Encogiendo la imagen

Ahora que tenemos todos los datos importantes en el comienzo de la imagen es el momento de eliminar ese espacio no usado. Primero tendremos que saber dónde termina nuestra partición y donde empieza el espacio no asignado. Para ello usaremos `fdisk`:  
`fdisk -l myimage.img`

Y tendremos una salida parecida a esta:

```
Disk myimage.img: 32.0 GB, 32010928128 bytes
255 heads, 63 sectors/track, 3891 cylinders, 62521344 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x0009f2d7

      Device Boot      Start         End      Blocks   Id  System
myimage.img1            8192      131071       61440    c  W95 FAT32 (LBA)
myimage.img2          131072     8300543     4084736   83  Linux
```

Hay dos cosas importantes en esta salida:

1. Al principio del disco, hay un pequeño espacio no asignado, y después vienen dos particiones seguidas. La segunda partición termina en el bloque 8300543 (indicado debajo de `End`).  
Antes había dicho que teníamos solo una partición, cuando realmente tenemos dos. Esto es porque la partición que queremos encoger es la segunda, y el espacio no asignado quedará al final.
2. El tamaño de bloque es de 512 bytes (mostradas como `sectors of 1 * 512`).

Estos números son importantes para terminar nuestro proceso. El tamaño de bloque (512) suele ser el mismo, pero el bloque en el que termina la partición será distinto en cada caso. Estos números significan que la partición termina en el byte 8300543 * 512 del archivo. Después de esto empieza el espacio no asignado.  
Para recortar todo este espacio no asignado usaremos el comando `truncate`. A este comando hay que proporcionarle el tamaño del archivo en bytes. El último bloque es el 8300543, y los bloques empiezan a contar en el 0, esto significa que necesitamos (8300543 + 1) * 512 bytes. Esto es importante, ya que sino la partición terminará fuera de la imagen, o estaremos dejando espacio de más. Así que usaremos truncate con estos cálculos:  
`truncate --size=$[(8300543 + 1) * 512] myimage.img`

Ahora ya tenemos la imagen preparada para restaurar en una tarjeta de menor tamaño.
