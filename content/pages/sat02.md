<h1>Entendimiento y visualización del proceso de instalación</h1>
<p align="left"><img src="../images/sat.png?raw=true"></p>
<p>
<strong>Meta:</strong>
<br>- Entender el proceso de instalación de la solución Red Hat Satellite así como el diseño sugerido para una correcta implementación.
</p>
<p>
<strong>Objetivos:</strong>
<br>- Instalación de Red Hat Satellite (Demostración)
</p>
<p>
<strong>Secciones:</strong>
<br>-  .(Teórico - Practico)
<br>- Validación de pre-requisitos para la instalación de Red Hat Satellite.(Teórico)
<br>- Instalación de Red Hat Satellite. (Demostración)
</p>
<p>a
<strong>Laboratorios:</strong>
<br>- Instalación de Red Hat Satellite
</p>

# Validación de pre-requisitos para la instalación de Red Hat Satellite

Los siguientes requisitos se aplican al sistema operativo base:
* arquitectura x86_64
* La última versión de Red Hat Enterprise Linux 9 Server
* CPU de 4 núcleos a 2,0 GHz como mínimo
* Se requiere un mínimo de 20 GB de RAM para que el servidor satélite funcione. Además, también se recomienda un mínimo de 4 GB de RAM para SWAP. En ambientes de desarrollo y pruebas se puede usar menos recursos.
* Un nombre de host único, que siga los estandares de DNS
* Una suscripción actual a Red Hat Satellite
* Acceso de usuario administrativo (root)
* Una máscara de sistema de 0022 (umask 0022)
* Resolución DNS completa registro A y PTR usando un nombre de dominio completamente calificado

NOTA: El servidor en el cual se instalara satellite deberá ser dedicado para este servicio.

**Requerimientos de almacenamiento**
<p align="left"><img src="../images/sat08.png?raw=true"></p>

**Directrices para sistema de archivos**
* Utilice el sistema de archivos XFS para Red Hat Satellite 6 porque no tiene las limitaciones de inodo que tiene ext4. Debido a que Satellite Server usa muchos enlaces simbólicos, es probable que su sistema se quede sin inodos si usa ext4 con su configuracion por defecto.
* No use NFS con MongoDB porque MongoDB no usa I/O convencional para acceder a archivos de datos y ocurren problemas de rendimiento cuando tanto los archivos de datos como los archivos de diario están alojados en NFS. Si es necesario para usar NFS, monte el volumen con las siguientes opciones en el archivo /etc/fstab: bg, nolock y noatime.
* No utilice NFS para el almacenamiento de datos de Pulp. El uso de NFS para Pulp tiene un impacto negativo en el rendimiento de la sincronización de contenido.
* Do not use the GFS2 file system as the input-output latency is too high.

**Almacenamiento de archivos de log**
* Los archivos de log son escritos en `/var/log/messages/`, `/var/log/httpd/`, and `/var/lib/foreman-proxy/openscap/content/`

NOTA: La cantidad exacta de almacenamiento que necesita para los mensajes de registro depende de su instalación y configuración

**Navegadores soportados**
Satellite soporta versiones recientes de: Firefox y Google Chrome.


*El tamaño del tiempo de ejecución se midió con los repositorios de Red Hat Enterprise Linux 7, 8 y 9 sincronizados.

**Requerimientos de puertos y firewall**

Puertos para la comunicación de satélite a Red Hat CDN
| Puerto | Protocolo | Servicio | Requerido para |
| --- | --- | --- | --- |
| 443 | TCP |HTTPS|Servicios de administración de suscripción (access.redhat.com) y conexión a Red Hat CDN (cdn.redhat.com).|

Puertos para el acceso de la interfaz de usuario basada en navegador al satélite
| Puerto | Protocolo | Servicio | Requerido para |
| --- | --- | --- | --- |
| 443 | TCP |HTTPS|Acceso a la interfaz de usuario basada en navegador para Satellite.|
| 80 | TCP |HTTP|Redirección a HTTPS para acceder a la interfaz de usuario web a satélite (opcional).|

Puertos para la comunicación de cliente a satélite
| Puerto | Protocolo | Servicio | Requerido para |
| --- | --- | --- | --- |
| 80 | TCP |HTTP|Anaconda, yum, para obtener certificados, plantillas de Katello y para descargar el firmware de iPXE.|
| 443 | TCP |HTTPS|Servicios de administración de suscripción, yum, servicios de telemetría y para la conexión al agente de Katello.|
| 5646 | TCP |AMQP|El enrutador de despacho Capsule Qpid al enrutador de despacho Qpid en Satellite.|
| 5647 | TCP |AMQP|Agente de Katello para comunicarse con el enrutador de despacho Qpid de Satellite.|
| 8000 | TCP |HTTP|Anaconda para descargar plantillas kickstart a hosts y para descargar firmware iPXE.|
| 8140 | TCP |HTTPS|Conexiones de Puppet agent a Puppet master.|
| 9090 | TCP |HTTPS|Envío de informes SCAP a la cápsula integrada, para la imagen de descubrimiento durante el aprovisionamiento para comunicarse con Satellite Server y copiar las claves SSH para la configuración de ejecución remota (Rex).|
| 7 | TCP,UDP |ICMP|DHCP externo en una red de cliente a satélite, ICMP ECHO para verificar que la dirección IP esté libre (opcional).|
| 53 | TCP,UDP |DNS| Consultas de DNS del cliente al servicio de DNS cápsula integrado de un satélite (opcional).|
| 67 | UDP |DHCP| Transmisiones de cápsula integradas de cliente a satélite, transmisiones DHCP para aprovisionamiento de clientes desde una cápsula integrada de satélite (opcional).|
| 69 | UDP |TFTP| Clientes que descargan archivos de imagen de arranque PXE desde una cápsula integrada de satélites para aprovisionamiento (opcional).|
| 5000 | TCP|HTTPS| Connection to Katello for the Docker registry (Optional).|


Puertos para comunicación satélite a cápsula
| Puerto | Protocolo | Servicio | Requerido para |
| --- | --- | --- | --- |
| 443 | TCP |HTTPS|Conexiones al servidor Pulp en la cápsula.|
| 9090 | TCP |HTTPS|Conexiones al proxy en la cápsula.|
| 80| TCP |HTTP|Descarga de un disco de arranque (opcional)

Puertos de red opcionales
| Puerto | Protocolo | Servicio | Requerido para |
| --- | --- | --- | --- |
| 22 | TCP |SSH|Comunicaciones basadas en satélite y cápsula, para ejecución remota (Rex) y Ansible.|
| 443 | TCP |HTTPS|Comunicaciones basadas en satélites, para recursos informáticos de vCenter.|
| 5000| TCP |HTTP|Comunicaciones por satélite, para recursos informáticos en OpenStack o para ejecutar contenedores.|
|22,16514| TCP |SSH,SSL/TLS|Comunicaciones por satélite, para recursos informáticos en libvirt.|
|389,636|TCP|LDAP,LDAPS|Comunicaciones satellite, para LDAP y fuentes de autenticación LDAP seguras.|
|5900 to 5930|TCP|SSL/TLS|Comunicaciones satellite, para la consola NoVNC en la interfaz de usuario web a los hipervisores.|

# Instalación de Red Hat Satellite. (Demostración)
**Verificando conectividad y resolución DNS**
```
[root@satellite ~]# ping -c1 localhost
```
```
[root@satellite ~]# ping -c1 $(hostname -f)
```
```
[root@satellite ~]# ping -c1 $(hostname -s)
```

Es buena idea indicar en el archivo /etc/hosts en caso de tener problemas de resolución DNS, en el caso del alumno 9
<br>**\<IP\> \<FQDN Satellite\> \<Short name Satellite\>**
```
192.168.109.10 satellite.migraciones09.gob.pe satellite
```

**Particionamiento Red Hat Satellite**

Revisar el particionamiento de instalación del sistema operativo.

```
[root@satellite ~]# vgs
  VG   #PV #LV #SN Attr   VSize   VFree
  rhel   1   5   0 wz--n- 248.41g 182.41g
[root@satellite ~]# lvs
  LV      VG   Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root    rhel -wi-ao---- 20.00g
  swap    rhel -wi-ao----  6.00g
  usr     rhel -wi-ao---- 10.00g
  var     rhel -wi-ao---- 20.00g
  var_log rhel -wi-ao---- 10.00g
[root@satellite ~]# df -h
Filesystem                Size  Used Avail Use% Mounted on
devtmpfs                  4.0M     0  4.0M   0% /dev
tmpfs                     4.8G     0  4.8G   0% /dev/shm
tmpfs                     1.9G  9.0M  1.9G   1% /run
/dev/mapper/rhel-root      20G  198M   20G   1% /
/dev/mapper/rhel-usr       10G  1.3G  8.8G  13% /usr
/dev/nvme0n1p2            960M  220M  741M  23% /boot
/dev/nvme0n1p1            599M  7.1M  592M   2% /boot/efi
/dev/mapper/rhel-var       20G  268M   20G   2% /var
/dev/mapper/rhel-var_log   10G  113M  9.9G   2% /var/log
tmpfs                     966M     0  966M   0% /run/user/0

```
Considerando la tabla de particiones recomendada por el fabricante
<p align="left"><img src="../images/sat08.png?raw=true"></p>

Se crearan los LV restantes
```
[root@satellite ~]# lvcreate -L 20G -n var_lib_pgsql rhel
WARNING: xfs signature detected on /dev/rhel/var_lib_pgsql at offset 0. Wipe it? [y/n]: y
  Wiping xfs signature on /dev/rhel/var_lib_pgsql.
  Logical volume "var_lib_pgsql" created.
[root@satellite ~]# lvcreate -L 500M -n opt_puppetlabs rhel
  Logical volume "opt_puppetlabs" created.
[root@satellite ~]# lvcreate -L 30G -n var_lib_containers rhel
  Logical volume "var_lib_containers" created.
[root@satellite ~]# lvcreate -L 100G -n var_lib_pulp rhel
  Logical volume "var_lib_pulp" created.
[root@satellite ~]# lvs
  LV                 VG   Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  opt_puppetlabs     rhel -wi-a----- 500.00m
  root               rhel -wi-ao----  20.00g
  swap               rhel -wi-ao----   6.00g
  usr                rhel -wi-ao----  10.00g
  var                rhel -wi-ao----  20.00g
  var_lib_containers rhel -wi-a-----  30.00g
  var_lib_pgsql      rhel -wi-a-----  20.00g
  var_lib_pulp       rhel -wi-a----- 100.00g
  var_log            rhel -wi-ao----  10.00g

```

Luego se formatea en XFS los nuevos LV
```
[root@satellite ~]# mkfs.xfs /dev/rhel/var_lib_pgsql
meta-data=/dev/rhel/var_lib_pgsql isize=512    agcount=4, agsize=1310720 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=5242880, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@satellite ~]# mkfs.xfs /dev/rhel/opt_puppetlabs
meta-data=/dev/rhel/opt_puppetlabs isize=512    agcount=4, agsize=32000 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=128000, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@satellite ~]# mkfs.xfs /dev/rhel/var_lib_containers
meta-data=/dev/rhel/var_lib_containers isize=512    agcount=4, agsize=1966080 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=7864320, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@satellite ~]# mkfs.xfs /dev/rhel/var_lib_pulp
meta-data=/dev/rhel/var_lib_pulp isize=512    agcount=4, agsize=6553600 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=26214400, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

Se crean los puntos de montaje para los nuevos filesystems
```
[root@satellite ~]# mkdir -p /var/lib/pgsql
[root@satellite ~]# mkdir -p /opt/puppetlabs
[root@satellite ~]# mkdir -p /var/lib/containers
[root@satellite ~]# mkdir -p /var/lib/pulp
```
Procedemos a realizar los montajes permanentes modificando el archivo /etc/fstab agregando las siguientes lineas al final
```
[root@satellite ~]# vi /etc/fstab
/dev/rhel/var_lib_pgsql /var/lib/pgsql xfs defaults 0 0
/dev/rhel/opt_puppetlabs /opt/puppetlabs xfs defaults 0 0
/dev/rhel/var_lib_containers /var/lib/containers xfs defaults 0 0
/dev/rhel/var_lib_pulp /var/lib/pulp xfs defaults 0 0
```

Se actualiza los montajes y se verifica
```
[root@satellite ~]# mount -a
mount: (hint) your fstab has been modified, but systemd still uses
       the old version; use 'systemctl daemon-reload' to reload.
[root@satellite ~]# systemctl daemon-reload
[root@satellite ~]# mount -a
[root@satellite ~]# df -h
Filesystem                           Size  Used Avail Use% Mounted on
devtmpfs                             4.0M     0  4.0M   0% /dev
tmpfs                                4.8G     0  4.8G   0% /dev/shm
tmpfs                                1.9G  9.0M  1.9G   1% /run
/dev/mapper/rhel-root                 20G  198M   20G   1% /
/dev/mapper/rhel-usr                  10G  1.3G  8.8G  13% /usr
/dev/nvme0n1p2                       960M  220M  741M  23% /boot
/dev/nvme0n1p1                       599M  7.1M  592M   2% /boot/efi
/dev/mapper/rhel-var                  20G  268M   20G   2% /var
/dev/mapper/rhel-var_log              10G  113M  9.9G   2% /var/log
tmpfs                                966M     0  966M   0% /run/user/0
/dev/mapper/rhel-var_lib_pgsql        20G  175M   20G   1% /var/lib/pgsql
/dev/mapper/rhel-opt_puppetlabs      436M   29M  408M   7% /opt/puppetlabs
/dev/mapper/rhel-var_lib_containers   30G  247M   30G   1% /var/lib/containers
/dev/mapper/rhel-var_lib_pulp        100G  746M  100G   1% /var/lib/pulp
```

Considerando que la particion /var/lib/pukp fue mal dimensionada y se desea aumentar su espacio, se verifica que existe espacio libre en el VG
```
[root@satellite ~]# vgs
  VG   #PV #LV #SN Attr   VSize   VFree
  rhel   1   9   0 wz--n- 248.41g 31.92g
```

Podemos aumentar el espacio hasta en 31.92G, decidimos aumentarla en 24GB
```
[root@satellite ~]# lvresize -L +28G /dev/rhel/var_lib_pulp
  Size of logical volume rhel/var_lib_pulp changed from 100.00 GiB (25600 extents) to 128.00 GiB (32768 extents).
  Logical volume rhel/var_lib_pulp successfully resized.
[root@satellite ~]# xfs_growfs /dev/rhel/var_lib_pulp
meta-data=/dev/mapper/rhel-var_lib_pulp isize=512    agcount=4, agsize=6553600 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=26214400, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 26214400 to 33554432
```
Se verifica el nuevo espacio de /var/lib/pulp
```
[root@satellite ~]# df -h
Filesystem                           Size  Used Avail Use% Mounted on
devtmpfs                             4.0M     0  4.0M   0% /dev
tmpfs                                4.8G     0  4.8G   0% /dev/shm
tmpfs                                1.9G  9.0M  1.9G   1% /run
/dev/mapper/rhel-root                 20G  198M   20G   1% /
/dev/mapper/rhel-usr                  10G  1.3G  8.8G  13% /usr
/dev/nvme0n1p2                       960M  220M  741M  23% /boot
/dev/nvme0n1p1                       599M  7.1M  592M   2% /boot/efi
/dev/mapper/rhel-var                  20G  268M   20G   2% /var
/dev/mapper/rhel-var_log              10G  113M  9.9G   2% /var/log
tmpfs                                966M     0  966M   0% /run/user/0
/dev/mapper/rhel-var_lib_pgsql        20G  175M   20G   1% /var/lib/pgsql
/dev/mapper/rhel-opt_puppetlabs      436M   29M  408M   7% /opt/puppetlabs
/dev/mapper/rhel-var_lib_containers   30G  247M   30G   1% /var/lib/containers
/dev/mapper/rhel-var_lib_pulp        128G  946M  128G   1% /var/lib/pulp
```

<br>**Configurar firewall de sistema operativo**
<br>Se verifica que para esta instalacion, se crean 2 grupos de reglas de firewall, primero para el cliente
```
[root@satellite ~]# firewall-cmd \
--add-port="8000/tcp" \
--add-port="9090/tcp"
success
```

Se agrega las reglas del servidor
```
[root@satellite ~]# firewall-cmd \
--add-service=dns \
--add-service=dhcp \
--add-service=tftp \
--add-service=http \
--add-service=https \
--add-service=puppetmaster
success
```

Se graba y valida las reglas
```
[root@satellite ~]# firewall-cmd --runtime-to-permanent
success
[root@satellite ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens160
  sources:
  services: cockpit dhcp dhcpv6-client dns http https puppetmaster ssh tftp
  ports: 8000/tcp 9090/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

<br>**Configurar repositorios de sistema operativo**
<br>El sistema Satellite puede ser instalado con una suscripción con acceso a internet, o usar repositorios locales basados en ISOs descargadas del portal del fabricante, para este ejercicio se instalara el Satellite en modo sin suscripción usando repositorios locales y el iso descargada.

Se configura el archivo /etc/yum.repos.d/rhel.repo con los 3 repositorios indicados
```
[root@satellite ~]# vi /etc/yum.repos.d/rhel.repo
[baseos]
name=baseos
baseurl=ftp://ftp.migraciones.gob.pe/rhel94/BaseOS
enabled=1
gpgheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
[appstream]
name=appstream
baseurl=ftp://ftp.migraciones.gob.pe/rhel94/AppStream
enabled=1
gpgheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
[extras]
name=extras
baseurl=ftp://ftp.migraciones.gob.pe/extras
enabled=1
gpgheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```

Se valida los repositorios instalando el rpm de wget
```
[root@satellite ~]# yum install -y wget
Running transaction
  Preparing        :                                                                           1/1
  Installing       : wget-1.21.1-7.el9.x86_64                                                  1/1
  Running scriptlet: wget-1.21.1-7.el9.x86_64                                                  1/1
  Verifying        : wget-1.21.1-7.el9.x86_64                                                  1/1
Installed products updated.
Installed:
  wget-1.21.1-7.el9.x86_64
Complete!
```

<br>**Configurar la hora del sistema**
<br>Es una buena practica sincronizar la hora del sistema operativo con un servidor corporativo NTP, para ello configuraremos el sistema para sincronizar la hora con ntp.migraciones.gob.pe o idm.migraciones.gob.pe que tienen la misma IP
```
[root@satellite ~]# yum install -y chrony
[root@satellite ~]# vi /etc/chrony.conf
pool ntp.migraciones.gob.pe iburst
[root@satellite ~]# systemctl enable chronyd --now
[root@satellite ~]# chronyc sources
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^? idm.migraciones.gob.pe        0   6     0     -     +0ns[   +0ns] +/-    0ns
```

**Nota: Para evitar problemas de perdida de conectividad hacia la terminar virtual se recomienda ejecutar el procedimiento de instalación por screen**
<br>**Instalar screen**
```
[root@satellite ~]# yum install ftp://ftp.migraciones.gob.pe/screen-4.8.0-6.el9.x86_64.rpm
```
**Generar un alias de conexión screen**
```
[root@satellite ~]# screen -S jaime 
```
**Listar conexión screen, identificar el id ejemplo: 3081**
```
[root@satellite ~]# screen -ls
There is a screen on:
        3081.jaime      (Attached)
1 Socket in /var/run/screen/S-root.
```
**En caso de des-conexión abrir otro terminal y ejecutar el comando indicado para restablecer la conexión, recordar poner el id que se genere en su terminal**
```
[root@satellite ~]# screen -r 3081
```

### **Instalacion de Satellite 6.17 en modo offline**

Considerando que los requisitos de red, almacenamiento, repositorios y conexion asegurada se ha cumplido, procedemos a realizar la descarga del ISO de Satellite 6.17
**El ejercicio inicia con la instalacion en version 6.17 para luego actualizarla a 6.18, si desea puede instalar directamente en version 6.18 solo cambiando el ISO a descargar, es el mismo procedimiento**

Se descarga el iso
```
[root@satellite ~]# wget ftp://ftp.migraciones.gob.pe/Satellite-6.17-rhel-9-x86_64.dvd.iso
==> SYST ... done.    ==> PWD ... done.
==> TYPE I ... done.  ==> CWD not needed.
==> SIZE Satellite-6.17-rhel-9-x86_64.dvd.iso ... 1739003904
==> PASV ... done.    ==> RETR Satellite-6.17-rhel-9-x86_64.dvd.iso ... done.
Length: 1739003904 (1.6G) (unauthoritative)
Satellite-6.17-rhel-9-x8 100%[=================================>]   1.62G   187MB/s    in 8.2s
2026-10-02 15:00:28 (203 MB/s) - ‘Satellite-6.17-rhel-9-x86_64.dvd.iso’ saved [1739003904]
```

Creamos un punto de montaje para el iso descargado
```
[root@satellite ~]# mkdir sat617
[root@satellite ~]# mount -o loop Satellite-6.17-rhel-9-x86_64.dvd.iso sat617
mount: /root/sat617: WARNING: source write-protected, mounted read-only.
```
Se ingresa al punto de montaje y se ejecuta el script de instalación
```
[root@satellite ~]# cd sat617/
[root@satellite sat617]# ./install_packages
This script will install the satellite packages on the current machine.
   - Ensuring we are in an expected directory.
   - Copying installation files.
   - Creating Satellite Repository File
   - Creating Maintenance Repository File
   - Checking to see if Satellite is already installed.
   - Importing the gpg key.
   - Installation repository will remain configured for future package installs.
   - Installation media can now be safely unmounted.

Install is complete. Please run satellite-installer --scenario satellite
```
Finalmente se ejecuta el programa de configuración de satellite, donde debemos indicar algunos parametros básicos como la organización, ubicación, usuario y clave de admin
```
[root@satellite ~]# satellite-installer --scenario satellite \
--foreman-initial-organization "Migraciones" \
--foreman-initial-location "Lima" \
--foreman-initial-admin-username admin \
--foreman-initial-admin-password redhat
```

Como el sistema tiene recursos menores, podemos usar el perfil de desarrollo para instalar el sistema con menos recursos, ideal para ambientes de prueba y laboratorio

```
[root@satellite ~]# satellite-installer --scenario satellite \
--foreman-initial-organization "Migraciones" \
--foreman-initial-location "Lima" \
--foreman-initial-admin-username admin \
--foreman-initial-admin-password redhat \
--tuning development
```

```
2026-10-02 15:18:43 [NOTICE] [checks] System checks passed
2026-10-02 15:18:47 [NOTICE] [configure] Starting system configuration.
2026-10-02 15:19:01 [NOTICE] [configure] 250 configuration steps out of 1583 steps complete.
2026-10-02 15:20:36 [NOTICE] [configure] 500 configuration steps out of 1584 steps complete.
2026-10-02 15:20:41 [NOTICE] [configure] 750 configuration steps out of 1587 steps complete.
2026-10-02 15:20:48 [NOTICE] [configure] 1000 configuration steps out of 1611 steps complete.
2026-10-02 15:20:49 [NOTICE] [configure] 1250 configuration steps out of 1613 steps complete.
2026-10-02 15:25:05 [NOTICE] [configure] 1500 configuration steps out of 1613 steps complete.
2026-10-02 15:26:37 [NOTICE] [configure] System configuration has finished.
  Success!
  * Satellite is running at https://satellite.migraciones09.gob.pe
      Initial credentials are admin / redhat

  * To install an additional Capsule on separate machine continue by running:

      capsule-certs-generate --foreman-proxy-fqdn "$CAPSULE" --certs-tar "/root/$CAPSULE-certs.tar"
  * Capsule is running at https://satellite.migraciones09.gob.pe:9090

The full log is at /var/log/foreman-installer/satellite.log
Package versions are being locked.
```

Verificamos los servicios de Satellite y que la organización Migraciones se creara con la instalación
```
[root@satellite ~]# satellite-maintain service status -b
- displaying redis                                 [OK]
- displaying postgresql                            [OK]
\ displaying pulpcore-api                          [OK]
\ displaying pulpcore-content                      [OK]
\ displaying pulpcore-worker@1.service             [OK]
\ displaying pulpcore-worker@2.service             [OK]
\ displaying tomcat                                [OK]
\ displaying dynflow-sidekiq@orchestrator          [OK]
\ displaying foreman                               [OK]
\ displaying httpd                                 [OK]
\ displaying dynflow-sidekiq@worker-1              [OK]
\ displaying dynflow-sidekiq@worker-hosts-queue-1  [OK]
\ displaying foreman-proxy                         [OK]
\ All services are running                                            [OK]

[root@satellite ~]# hammer organization list
---|-------------|-------------|-------------|------------
ID | TITLE       | NAME        | DESCRIPTION | LABEL
---|-------------|-------------|-------------|------------
1  | Migraciones | Migraciones |             | Migraciones
---|-------------|-------------|-------------|------------
```

Tambien examinar el sistema usando el url con el navegador

### **Actualizar el sistema Satellite a 6.18**
<br>Con el sistema funcionando en version 6.17, procedemos a eliminar el repositorio de la version anterior
```
[root@satellite ~]# rm -rf /etc/yum.repos.d/satellite-local.repo
```

Mapear un grupo nuevo de repositorios para las actualizaciones en el archivo /etc/yum.repos.d/updates.repo
```
[root@satellite ~]# vi /etc/yum.repos.d/updates.repo
[sat618-base]
name=sat618-base
baseurl=ftp://ftp.migraciones.gob.pe/sat618-base
enabled=1
gpgheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
[sat618-maintenance]
name=sat618-maintenance
baseurl=ftp://ftp.migraciones.gob.pe/sat618-maintenance
enabled=1
gpgheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
[updates]
name=updates
baseurl=ftp://ftp.migraciones.gob.pe/updates
enabled=1
gpgheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```

Sin realizar ningun otro cambio en el satellite, ejecutamos los procedimientos de actualización, empezando con el upgrade del aplicativo satellite-maintain
```
[root@satellite ~]# satellite-maintain self-upgrade --maintenance-repo-label=sat618-maintenance
Running Enables the specified version's maintenance repository and,
updates the satellite-maintain packages
================================================================================
Update package(s) rubygem-foreman_maintain, satellite-maintain: |      [OK]
--------------------------------------------------------------------------------
```
Ahora verificamos si se cumplen todos los requisitos de actualización sin errores graves
```
[root@satellite ~]# satellite-maintain upgrade check --whitelist="repositories-validate,repositories-setup"
Checking for new version of satellite-maintain, rubygem-foreman_maintain...
Nothing to update, can't find new version of satellite-maintain, rubygem-foreman_maintain.
Running preparation steps required to run the next scenarios
================================================================================
Check whether all services are running:                               [OK]
--------------------------------------------------------------------------------
Check whether system has any non Red Hat repositories (e.g.: EPEL) enabled:
| Checking repositories enabled on the system                         [OK]
--------------------------------------------------------------------------------


Running Checks before upgrading
================================================================================
Check number of fact names in database:                               [OK]
--------------------------------------------------------------------------------
Clean old Kernel and initramfs files from tftp-boot:                  [OK]
--------------------------------------------------------------------------------
Check for verifying syntax for ISP DHCP configurations:               [OK]
--------------------------------------------------------------------------------
Check whether all services are running:                               [OK]
--------------------------------------------------------------------------------
Check whether all services are running using the ping call:           [OK]
--------------------------------------------------------------------------------
Check for paused tasks:                                               [OK]
--------------------------------------------------------------------------------
Check to verify no empty CA cert requests exist:                      [OK]
--------------------------------------------------------------------------------
Check whether system is self-registered or not:                       [OK]
--------------------------------------------------------------------------------
Check to verify if any hotfix installed on system:
\ Checking for presence of hotfix(es). It may take some time to verify.
                                                                      [OK]
--------------------------------------------------------------------------------
Check if TMOUT environment variable is set:                           [OK]
--------------------------------------------------------------------------------
Check if any upstream repositories are enabled on system:
| Checking for presence of upstream repositories                      [OK]
--------------------------------------------------------------------------------
Check whether podman needs to be logged in to the registry:           [OK]
--------------------------------------------------------------------------------
Check to make sure root(/) partition has enough space:                [OK]
--------------------------------------------------------------------------------
Check to make sure /var/lib/candlepin has enough space:               [OK]
--------------------------------------------------------------------------------
Check to make sure PostgreSQL data is not on an own mountpoint:       [OK]
--------------------------------------------------------------------------------
Make sure server is running on required database version:             [OK]
--------------------------------------------------------------------------------
Check that external databases have proper EVR extension permissions:  [OK]
--------------------------------------------------------------------------------
Check for roles that have filters with multiple resources attached:   [OK]
--------------------------------------------------------------------------------
Check for duplicate permissions from database:                        [OK]
--------------------------------------------------------------------------------
Check if system requirements match current tuning profile:            [OK]
--------------------------------------------------------------------------------
Check whether reports have correct associations:                      [OK]
--------------------------------------------------------------------------------
Check for running tasks:                                              [OK]
--------------------------------------------------------------------------------
Check for old tasks in paused/stopped state:                          [OK]
--------------------------------------------------------------------------------
Check for pending tasks which are safe to delete:                     [OK]
--------------------------------------------------------------------------------
Check for tasks in planning state:                                    [OK]
--------------------------------------------------------------------------------
Check for running pulpcore tasks:                                     [OK]
--------------------------------------------------------------------------------
Check if system has any non Red Hat RPMs installed (e.g.: Fedora):    [WARNING]
Found 1 unexpected non Red Hat Package(s) installed!
Package : Vendor
screen-4.8.0-6.el9.x86_64 : (none)
--------------------------------------------------------------------------------
Check to validate dnf configuration before upgrade:                   [OK]
--------------------------------------------------------------------------------
Check whether system has any non Red Hat repositories (e.g.: EPEL) enabled:
| Checking repositories enabled on the system                         [OK]
--------------------------------------------------------------------------------
Check if ipv6.disable=1 is set at kernel level:                       [OK]
--------------------------------------------------------------------------------
Check if server certificate authority is sha1 signed:                 [OK]
--------------------------------------------------------------------------------
Validate availability of repositories:                                [SKIPPED]
--------------------------------------------------------------------------------
Scenario [Checks before upgrading] failed.

The following steps ended up in warning state:

  [non-rh-packages]

The steps in warning state itself might not mean there is an error,
but it should be reviewed to ensure the behavior is expected
```

La salida de la revisión mostro algunos warnings, pero ningun error, por ello, ahora ejecutamos el upgrade, el cual confirmaremos
```
[root@satellite ~]# satellite-maintain upgrade run --whitelist="repositories-validate,repositories-setup"
Checking for new version of satellite-maintain, rubygem-foreman_maintain...
Nothing to update, can't find new version of satellite-maintain, rubygem-foreman_maintain.
Running preparation steps required to run the next scenarios
================================================================================
Check whether all services are running:                               [OK]
--------------------------------------------------------------------------------
Check whether system has any non Red Hat repositories (e.g.: EPEL) enabled:
- Checking repositories enabled on the system                         [OK]
--------------------------------------------------------------------------------


Running Checks before upgrading
================================================================================
Check number of fact names in database:                               [OK]
--------------------------------------------------------------------------------
Clean old Kernel and initramfs files from tftp-boot:                  [OK]
--------------------------------------------------------------------------------
Check for verifying syntax for ISP DHCP configurations:               [OK]
--------------------------------------------------------------------------------
Check whether all services are running:                               [OK]
--------------------------------------------------------------------------------
Check whether all services are running using the ping call:           [OK]
--------------------------------------------------------------------------------
Check for paused tasks:                                               [OK]
--------------------------------------------------------------------------------
Check to verify no empty CA cert requests exist:                      [OK]
--------------------------------------------------------------------------------
Check whether system is self-registered or not:                       [OK]
--------------------------------------------------------------------------------
Check to verify if any hotfix installed on system:
- Checking for presence of hotfix(es). It may take some time to verify.
                                                                      [OK]
--------------------------------------------------------------------------------
Check if TMOUT environment variable is set:                           [OK]
--------------------------------------------------------------------------------
Check if any upstream repositories are enabled on system:
\ Checking for presence of upstream repositories                      [OK]
--------------------------------------------------------------------------------
Check whether podman needs to be logged in to the registry:           [OK]
--------------------------------------------------------------------------------
Check to make sure root(/) partition has enough space:                [OK]
--------------------------------------------------------------------------------
Check to make sure /var/lib/candlepin has enough space:               [OK]
--------------------------------------------------------------------------------
Check to make sure PostgreSQL data is not on an own mountpoint:       [OK]
--------------------------------------------------------------------------------
Make sure server is running on required database version:             [OK]
--------------------------------------------------------------------------------
Check that external databases have proper EVR extension permissions:  [OK]
--------------------------------------------------------------------------------
Check for roles that have filters with multiple resources attached:   [OK]
--------------------------------------------------------------------------------
Check for duplicate permissions from database:                        [OK]
--------------------------------------------------------------------------------
Check if system requirements match current tuning profile:            [OK]
--------------------------------------------------------------------------------
Check whether reports have correct associations:                      [OK]
--------------------------------------------------------------------------------
Check for running tasks:                                              [OK]
--------------------------------------------------------------------------------
Check for old tasks in paused/stopped state:                          [OK]
--------------------------------------------------------------------------------
Check for pending tasks which are safe to delete:                     [OK]
--------------------------------------------------------------------------------
Check for tasks in planning state:                                    [OK]
--------------------------------------------------------------------------------
Check for running pulpcore tasks:                                     [OK]
--------------------------------------------------------------------------------
Check if system has any non Red Hat RPMs installed (e.g.: Fedora):    [WARNING]
Found 1 unexpected non Red Hat Package(s) installed!
Package : Vendor
screen-4.8.0-6.el9.x86_64 : (none)
--------------------------------------------------------------------------------
Check to validate dnf configuration before upgrade:                   [OK]
--------------------------------------------------------------------------------
Check whether system has any non Red Hat repositories (e.g.: EPEL) enabled:
| Checking repositories enabled on the system                         [OK]
--------------------------------------------------------------------------------
Check if ipv6.disable=1 is set at kernel level:                       [OK]
--------------------------------------------------------------------------------
Check if server certificate authority is sha1 signed:                 [OK]
--------------------------------------------------------------------------------
Validate availability of repositories:                                [SKIPPED]
--------------------------------------------------------------------------------
Scenario [Checks before upgrading] failed.

The following steps ended up in warning state:

  [non-rh-packages]

The steps in warning state itself might not mean there is an error,
but it should be reviewed to ensure the behavior is expected



Continue with [Procedures before migrating], [y(yes), n(no), q(quit)] y
Running preparation steps required to run the next scenarios
================================================================================
Check whether all services are running:                               [OK]
--------------------------------------------------------------------------------


Running Procedures before migrating
================================================================================
disable active sync plans:
\ Total 0 sync plans are now disabled.                                [OK]
--------------------------------------------------------------------------------
Add maintenance_mode tables/chain to nftables/iptables:               [OK]
--------------------------------------------------------------------------------
Stop cron service:

Stopping the following service(s):
crond
- All services stopped                                                [OK]
--------------------------------------------------------------------------------
Stop systemd timers:                                                  [OK]
--------------------------------------------------------------------------------


Running Migration scripts
================================================================================
Setup repositories:                                                   [SKIPPED]
--------------------------------------------------------------------------------
Download package(s) :
--------------------------------------------------------------------------------
Update IoP containers:                                                [OK]
--------------------------------------------------------------------------------
Stop applicable services:

Stopping the following service(s):
redis, postgresql, pulpcore-api, pulpcore-content, pulpcore-worker@1.service, pulpcore-worker@2.service, tomcat, pulpcore-api.socket, pulpcore-content.socket, dynflow-sidekiq@orchestrator, foreman, httpd, foreman.socket, dynflow-sidekiq@worker-1, dynflow-sidekiq@worker-hosts-queue-1, foreman-proxy
\ All services stopped                                                [OK]
--------------------------------------------------------------------------------
Update package(s) :                                                   [OK]
--------------------------------------------------------------------------------
Running satellite-installer : 2026-10-02 17:09:11 [NOTICE] [root] Loading installer configuration. This will take some time.
2026-10-02 17:09:14 [NOTICE] [root] Running installer with log based terminal output at level NOTICE.
2026-10-02 17:09:14 [NOTICE] [root] Use -l to set the terminal output log level to ERROR, WARN, NOTICE, INFO, or DEBUG. See --full-help for definitions.
2026-10-02 17:09:16 [NOTICE] [checks] System checks passed
Package versions are locked. Continuing with unlock.
2026-10-02 17:09:22 [NOTICE] [configure] Starting system configuration.
2026-10-02 17:09:31 [NOTICE] [configure] 250 configuration steps out of 1546 steps complete.
2026-10-02 17:09:32 [NOTICE] [configure] 500 configuration steps out of 1549 steps complete.
2026-10-02 17:09:36 [NOTICE] [configure] 750 configuration steps out of 1551 steps complete.
2026-10-02 17:09:36 [NOTICE] [configure] 1000 configuration steps out of 1578 steps complete.
2026-10-02 17:09:37 [NOTICE] [configure] 1250 configuration steps out of 1578 steps complete.
2026-10-02 17:13:47 [NOTICE] [configure] 1500 configuration steps out of 1578 steps complete.
2026-10-02 17:14:03 [NOTICE] [configure] System configuration has finished.
  Success!
  * Satellite is running at https://satellite.migraciones09.gob.pe

  * To install an additional Capsule on separate machine continue by running:

      capsule-certs-generate --foreman-proxy-fqdn "$CAPSULE" --certs-tar "/root/$CAPSULE-certs.tar"
  * Capsule is running at https://satellite.migraciones09.gob.pe:9090

The full log is at /var/log/foreman-installer/satellite.log
Package versions are being locked.
                                        [OK]
--------------------------------------------------------------------------------
Execute upgrade:run rake task:                                        [OK]
--------------------------------------------------------------------------------


Running preparation steps required to run the next scenarios
================================================================================
Check whether all services are running:                               [OK]
--------------------------------------------------------------------------------


Running Procedures after migrating
================================================================================
Refresh detected features:                                            [OK]
--------------------------------------------------------------------------------
Start applicable services:

Starting the following service(s):
redis, postgresql, pulpcore-api, pulpcore-content, pulpcore-worker@1.service, pulpcore-worker@2.service, tomcat, dynflow-sidekiq@orchestrator, foreman, httpd, dynflow-sidekiq@worker-1, dynflow-sidekiq@worker-hosts-queue-1, foreman-proxy
\ All services started                                                [OK]
--------------------------------------------------------------------------------
Rename ContentArtifact relative_paths to match `{N-V-R.A.rpm}`:
| Running pulpcore-manager rpm-datarepair 4073                        [OK]
--------------------------------------------------------------------------------
Start cron service:

Starting the following service(s):
crond
| All services started                                                [OK]
--------------------------------------------------------------------------------
Start systemd timers:                                                 [OK]
--------------------------------------------------------------------------------
re-enable sync plans:
/ Total 0 sync plans are now enabled.                                 [OK]
--------------------------------------------------------------------------------
Remove maintenance mode table/chain from nftables/iptables:           [OK]
--------------------------------------------------------------------------------
Prune unused IoP container images:                                    [OK]
--------------------------------------------------------------------------------


Running preparation steps required to run the next scenarios
================================================================================
Check whether all services are running:                               [OK]
--------------------------------------------------------------------------------


Running Checks after upgrading
================================================================================
Check number of fact names in database:                               [OK]
--------------------------------------------------------------------------------
Clean old Kernel and initramfs files from tftp-boot:                  [OK]
--------------------------------------------------------------------------------
Check for verifying syntax for ISP DHCP configurations:               [OK]
--------------------------------------------------------------------------------
Check whether all services are running:                               [OK]
--------------------------------------------------------------------------------
Check whether all services are running using the ping call:           [OK]
--------------------------------------------------------------------------------
Check for paused tasks:                                               [OK]
--------------------------------------------------------------------------------
Check to verify no empty CA cert requests exist:                      [OK]
--------------------------------------------------------------------------------
Check whether system is self-registered or not:                       [OK]
--------------------------------------------------------------------------------
Check if system needs reboot:                                         [WARNING]
Updating Subscription Management repositories.
Unable to read consumer identity

This system is not registered with an entitlement server. You can use "rhc" or "subscription-manager" to register.

Core libraries or services have been updated since boot-up:
  * dbus-broker
  * glibc
  * kernel
  * linux-firmware
  * microcode_ctl
  * systemd

Reboot is required to fully utilize these updates.
More information: https://access.redhat.com/solutions/27943
--------------------------------------------------------------------------------
Initialize and expose container image metadata in the pulpcore db:
- Adding image metadata to pulp.                                      [OK]
--------------------------------------------------------------------------------
Import container manifest metadata:
\ Adding image metadata to Katello.                                   [OK]
--------------------------------------------------------------------------------


--------------------------------------------------------------------------------
Upgrade finished.
```

Reinciamos el servidor
```
[root@satellite ~]# reboot
```

Una vez reiniciado el servidor, esperamos unos minutos para que los servicios inicien y verificamos algunos detalles de actualización
```
[root@satellite ~]# rpm -q satellite
satellite-6.18.0-3.el9sat.noarch
[root@satellite ~]# satellite-maintain service status -b
Running Status Services
================================================================================
Get status of applicable services:

/ displaying redis                                 [OK]
/ displaying postgresql                            [OK]
- displaying pulpcore-api                          [OK]
- displaying pulpcore-content                      [OK]
- displaying pulpcore-worker@1.service             [OK]
- displaying pulpcore-worker@2.service             [OK]
- displaying tomcat                                [OK]
- displaying dynflow-sidekiq@orchestrator          [OK]
- displaying foreman                               [OK]
- displaying httpd                                 [OK]
- displaying dynflow-sidekiq@worker-1              [OK]
- displaying dynflow-sidekiq@worker-hosts-queue-1  [OK]
- displaying foreman-proxy                         [OK]
- All services are running                                            [OK]
--------------------------------------------------------------------------------

[root@satellite ~]# hammer organization list
---|-------------|-------------|-------------|------------
ID | TITLE       | NAME        | DESCRIPTION | LABEL
---|-------------|-------------|-------------|------------
1  | Migraciones | Migraciones |             | Migraciones
---|-------------|-------------|-------------|------------

```

Tambien podemos verificar con el navegador


# Laboratorio: Instalando Red Hat Satellite

<br>**1. Instalar Red Hat Satellite**
<br>- Utilizar el procedimiento indicado en la explicación en la version 6.17.
<br>- Instalar usando la organizacion Migraciones y la ubicación Lima con la credenciales de administrador admin/redhat
<br>- Una vez finalizada la instalación logearse con el usuario: admin password: redhat
<br>**2. Actualizar Red Hat Satellite**
<br>- Utilizar el procedimiento indicado en la explicación para pasar de la version 6.17 a la 6.18.
<br>- Valide el acceso al servidor actualizado y confirme que la organización, ubicación y credenciales se mantengan de la version anterior.

<p><br><a href="sat.md">volver</a></p>
