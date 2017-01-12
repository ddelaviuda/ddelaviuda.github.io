---
layout: post
title:  "Instalación de FreePBX 13 en Debian 8"
date:   2016-11-06 18:39:22
categories: sistemas
tags:
- voz
comments: true
---
Información obtenidad de [aquí](https://www.powerpbx.org/asterisk-freepbx-install-guide-debian-v8-asterisk-v13-freepbx-v13-v1) y [aquí](http://wiki.freepbx.org/display/FOP/Installing+FreePBX+13+on+Debian+8.1#InstallingFreePBX13onDebian8.1-CompileandinstallDAHDI..)


Configuración inicial
---------------------

{% highlight bash %}
$ sudo sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config
$ sudo service sshd restart
{% endhighlight %}

Probado con:
------------

**Debian 8.6 (Jessie)**
**Asterisk v13**
**Freepbx v13**

Se sobre entiende que el modo texto de la consola es multi-user.target y que la ejecución de los comando es realizada como superusuario (root) `sudo su`.

Instalación de prerequisitos
---------------------------

{% highlight bash %}
apt-get update && apt-get upgrade -y
apt-get install -y build-essential openssh-server apache2 mysql-server mysql-client bison flex php5 php5-curl php5-cli php5-mysql php-pear php5-gd curl sox libncurses5
-dev libssl-dev libmysqlclient-dev mpg123 libxml2-dev libnewt-dev sqlite3 libsqlite3-dev pkg-config automake libtool autoconf git subversion uuid uuid-dev libiksemel-d
ev libjansson-dev tftpd postfix mailutils nano ntp chkconfig libspandsp-dev libcurl4-gnutls-dev unixodbc unixodbc-dev libmyodbc xinetd e2fsprogs dbus sudo linux-headers*
{% endhighlight %}

Durante la instalación de MySQL se nos solicitará la contraseña del usuario root.

Instalación requisitos pear
---------------------------

{% highlight bash %}
pear install Console_getopt
{% endhighlight %}

Configuración de la zona horaria
--------------------------------

Para ello emplearemos el paquete `tzselect`.

{% highlight bash %}
## EJEMPLO
timedatectl set-timezone Europe/Madrid
timedatectl status
{% endhighlight %}

Instalación de DAHDI
--------------------

Será necesario instalarlo en caso de que sea necesario el empleo de tarjetas.

{% highlight bash %}
cd /usr/src
wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz
tar zxvf dahdi-linux-complete*
cd /usr/src/dahdi-linux-complete*/
make all && make install && make config
{% endhighlight %}

Si falla `make all` hay que reiniciar y tratar de instalar las cabeceras.

{% highlight bash %}
apt-get install linux-headers*
{% endhighlight %}

Podemos comprobar is `dahdi` está funcionando con:

{% highlight bash %}
lsmod | grep dahdi
dahdi_voicebus         53442  1 wctdm24xxp
dahdi                 212264  2 wctdm24xxp,dahdi_voicebus
crc_ccitt              12347  2 wctdm24xxp,dahdi
{% endhighlight %}

Con `dahdi_hardware` comprobaremos el harware conectado.

{% highlight bash %}
dahdi_hardware 
pci:0000:04:05.0     wctdm24xxp+  d161:8005 Wildcard TDM410P
{% endhighlight %}

Finalmente habrá que hacer propietario de los siguientes archivos al usuario `asterisk`, si no existen también habrá que crearlos.
{% highlight bash %}
touch /etc/dahdi/system.conf
chown asterisk /etc/dahdi/system.conf
touch /etc/modprobe.d/dahdi.conf
chown asterisk /etc/modprobe.d/dahdi.conf
touch /etc/dahdi/modules
chown asterisk /etc/dahdi/modules
touch /etc/asterisk/chan_dahdi_general.conf
touch /etc/asterisk/chan_dahdi_groups.conf
touch /etc/asterisk/chan_dahdi.conf
chown asterisk /etc/asterisk/chan_dahdi_general.conf
chown asterisk /etc/asterisk/chan_dahdi_groups.conf
chown asterisk /etc/asterisk/chan_dahdi.conf
mkdir wanpipe
touch wanpipe/global.conf
chown -R asterisk wanpipe/
{% endhighlight %}

PJSIP
-----

{% highlight bash %}
cd /usr/src
wget http://www.pjsip.org/release/2.5.5/pjproject-2.5.5.tar.bz2
tar -xjvf pjproject-2.5.5*
cd /usr/src/pjproject-2.5.5*/
make distclean


./configure --enable-shared --disable-sound --disable-resample \
--disable-video --disable-opencore-amr CFLAGS='-O2 -DNDEBUG'

make uninstall
ldconfig
make dep
make
make install
ldconfig
{% endhighlight %}

Su falla la instalación (make install) repetir de nuevo desde `ldconfig`. Depués, para verificar la instalación `ldconfig -p | grep pj` que deberá mostrar varios ficheros `*.so` el el directorio `/usr/local/lib`.

Asterisk
--------
{% highlight bash %}
cd /usr/src
wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-13-current.tar.gz
tar zxvf asterisk-13-current.tar.gz
cd /usr/src/asterisk-13*/
make distclean

./configure
{% endhighlight %}

Para verificar la instalación ejectuamos `nano -v config.log`

Y

{% highlight bash %}
cd /usr/src/asterisk-13*/
make menuselect.makeopts
{% endhighlight %}

Para compilar las opciones manualmente ejecutaremos `make menuselect`.
Para mostrar las opciones ejecutaremos `menuselect/menuselect --list-options`.
Si Asterisk falla a ejecutarse en una máquina virtual se añade la opción `--disable BUILD_NATIVE`.
Para aplicaciones de tiempo real como `A2billing` añadimos la opción `--enable res_config_mysql`.

{% highlight bash %}
menuselect/menuselect --enable cdr_mysql --enable EXTRA-SOUNDS-EN-GSM menuselect.makeopts
{% endhighlight %}

Creación de los usuarios de asterisk
------------------------------------

{% highlight bash %}
adduser asterisk --disabled-password --shell /sbin/nologin --gecos "Asterisk User"
chown -R asterisk. /var/lib/asterisk
chown -R asterisk. /etc/asterisk
chown -R asterisk. /var/{lib,log,spool}/asterisk
chown -R asterisk. /usr/lib/asterisk
{% endhighlight %}

Freepbx
-------

Haremos lo siguiente.

{% highlight bash %}
cd /usr/src
wget http://mirror.freepbx.org/modules/packages/freepbx/freepbx-13.0-latest.tgz
tar vxfz freepbx-13.0-latest.tgz
rm -f freepbx-13.0-latest.tgz
cd freepbx
./start_asterisk start
./install -n --dbpass contraseña_mysql
{% endhighlight %}

Reemplazar "contraseña_mysql" con la contraseña del usuario root en MySQL.

Instalación del los módulos mínimos.
{% highlight bash %}
fwconsole ma upgrade framework core voicemail sipsettings infoservices featurecodeadmin logfiles callrecording cdr dashboard
fwconsole restart
fwconsole reload
fwconsole chown
{% endhighlight %}

Configuración de Apache
-----------------------

Cambiarmos ahora el usuario de ejecución al nombre asterisk.

{% highlight bash %}
sed -i 's/\(APACHE_RUN_USER=\)\(.*\)/\1asterisk/g' /etc/apache2/envvars
sed -i 's/\(APACHE_RUN_GROUP=\)\(.*\)/\1asterisk/g' /etc/apache2/envvars
chown asterisk. /run/lock/apache2
mv /var/www/html/index.html /var/www/html/index.html.disable
{% endhighlight %}

Y la configuración...

{% highlight bash %}
cp /etc/apache2/apache2.conf /etc/apache2/apache2.conf_orig
sed -i 's/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf
ln -s /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled/rewrite.load
sed -i 's/\= 128M/\= 256M/g' /etc/php5/apache2/php.ini
{% endhighlight %}

Reiniciamos apache2.

{% highlight bash %}
systemctl restart apache2
{% endhighlight %}

Configurar FreePBX en arranque
-------------------------------

Configurar para que `Freepbx` comience al arrancar el sistema requiere las siguientes operaciones.

{% highlight bash %}
nano /etc/systemd/system/freepbx.service
[Unit]
Description=Freepbx
After=mysql.service

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/usr/sbin/fwconsole start
ExecStop=/usr/sbin/fwconsole stop

[Install]
WantedBy=multi-user.target
{% endhighlight %}

Y ahora se habilita.

{% highlight bash %}
systemctl enable freepbx

reboot
{% endhighlight %}

Ahora podremos accedemos al GUI de FreePBX en `http://iP_del_servidor`

Tareas a realizar tras la instalación
-------------------------------------

Lo primero es asegurar el servidor de bases de datos.

{% highlight bash %}
mysql_secure_installation
{% endhighlight %}

Introduciremos la contraseña de `root` y diremos que sí (y) a todo.

Configuración de la rotación de los logs para que no se acumulen editando `nano /etc/logrotate.d/asterisk` y añadiendo:

{% highlight bash %}
/var/spool/mail/asterisk
/var/log/asterisk/*log
/var/log/asterisk/messages
/var/log/asterisk/full
/var/log/asterisk/dtmf
/var/log/asterisk/freepbx_dbug
/var/log/asterisk/fail2ban {
        weekly
        missingok
        rotate 4
        #compress
        notifempty
        sharedscripts
        create 0640 asterisk asterisk
        postrotate
        /usr/sbin/asterisk -rx 'logger reload' > /dev/null 2> /dev/null || true
        endscript
        su root root
}
{% endhighlight %}

CDR ODBC
--------

Se recomienda la instalación de este módulo aunque si `crd_mysql.so` está instalado esta parte es opcional.

Añadimos al fichero de configuración mediante `nano /etc/odbcinst.ini` el siguiente texto.

{% highlight bash %}
[MySQL]
Description = ODBC for MySQL
Driver = /usr/lib/x86_64-linux-gnu/odbc/libmyodbc.so
Setup = /usr/lib/x86_64-linux-gnu/odbc/libodbcmyS.so
FileUsage = 1
{% endhighlight %}

y también `nano /etc/odbc.ini`:

{% highlight bash %}
[MySQL-asteriskcdrdb] 
Description=MySQL connection to ‘asteriskcdrdb’ database 
driver=MySQL 
server=localhost
database=asteriskcdrdb 
Port=3306 
Socket=/var/run/mysqld/mysqld.sock 
option=3
{% endhighlight %}

**Prueba del driver ODBC**

Ejecutamos:

{% highlight bash %}
odbcinst -s -q
{% endhighlight %}

Debemos usar "usuario" y "clave" en `/etc/asterisk/res_odbc_additional.conf` para comprobar la conexión con la base de datos vía ODBC.

{% highlight bash %}
isql -v MySQL-asteriskcdrdb usuario_freepbx clave_freepbx
{% endhighlight %}

Servidor TFTP
-------------

En caso de emplear teléfonos IP es interesante configurar un servidor TFTP para cargar la configuración.

Creamos y editamos archivo con `nano /etc/xinetd.d/tftp` para incluir.

{% highlight bash %}
service tftp { 
protocol = udp 
port = 69 
socket_type = dgram 
wait = yes 
user = nobody 
server = /usr/sbin/xinetd
server_args = /tftpboot 
disable = no 
*}
{% endhighlight %}

Creamos el directorio `/tftpboot` si no esta creado, cambiamos permisos y reiniciamos el servico.

{% highlight bash %}
mkdir /tftpboot 
chmod 777 /tftpboot 
systemctl restart xinetd
{% endhighlight %}

Toques finales
--------------

Cambiamos “upload_max_filesize” de 2M a 20M para permitir ficheros de audio mayores editanado el siguiente fichero `nano +810/etc/php5/apache2/php.ini`. Si va mos a usar dahdi, es conveniente ejecutar `dahdi_genconf`.