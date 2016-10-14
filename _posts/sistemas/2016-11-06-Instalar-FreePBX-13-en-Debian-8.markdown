---
layout: post
title:  "Instalación de FreePBX 13 en Debian 8"
date:   2016-11-06 18:39:22
categories: sistemas
tags:
- voz
comments: true
---
Información obtenidad de [aquí](https://www.powerpbx.org/asterisk-freepbx-install-guide-debian-v8-asterisk-v13-freepbx-v13-v1).


Configuración inicial
---------------------

{% highlight bash %}
$ sudo sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config
$ sudo service sshd restart
{% endhighlight %}

Probado con:
------------

**Debian 8 (Jessie)**
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

Será necesario isntalarlo en caso de que sea necesario el empleo de tarjetas.

{% highlight bash %}
cd /usr/src
wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz
tar zxvf dahdi-linux-complete*
cd /usr/src/dahdi-linux-complete*/
make all && make install && make config
systemctl start dahdi
{% endhighlight %}

Si falla reiniciar y tratar de isntalar las cabeceras.

{% highlight bash %}
apt-get install linux-headers*
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

make uninstall && ldconfig && make dep && make && make install && ldconfig
{% endhighlight %}

Para verificar la instalación `ldconfig -p | grep pj` que deberá mostrar varios ficheros `*.so` el el directorio `/usr/local/lib`.

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
make && make install && chown -R asterisk. /var/lib/asterisk
{% endhighlight %}

Freepbx
-------

{% highlight bash %}
cd /usr/src
git clone -b release/13.0 --single-branch https://github.com/FreePBX/framework.git freepbx

cd /usr/src/freepbx
./start_asterisk start
{% endhighlight %}

Reemplazar "contraseña" con la contraseña del usuario root en MySQL.
{% highlight bash %}
./install -n --dbpass contraseña
{% endhighlight %}

Instalación del los módulos mínimos.
{% highlight bash %}
fwconsole ma upgrade framework core voicemail sipsettings infoservices \
featurecodeadmin logfiles callrecording cdr dashboard

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
systemctl restart apache2
{% endhighlight %}


Configurar para que `Freepbx` comience al arrancar el sistema.

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

{% highlight bash %}
{% endhighlight %}

Tareas a realizar tras la instalación
-------------------------------------

Lo primero es bloquer el servidor de bases de datos.

{% highlight bash %}
mysql_secure_installation
{% endhighlight %}

Introduciremos la contraseña de `root` y diremos que sí (y) a todo.

Configuración de la rotación de los logs para que no se acumulen.

{% highlight bash %}
nano /etc/logrotate.d/asterisk

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

If the deprecated cdr_mysql.so module is installed then this is optional, but still recommended.

nano /etc/odbcinst.ini

[MySQL]
Description = ODBC for MySQL
Driver = /usr/lib/x86_64-linux-gnu/odbc/libmyodbc.so
Setup = /usr/lib/x86_64-linux-gnu/odbc/libodbcmyS.so
FileUsage = 1

nano /etc/odbc.ini
[MySQL-asteriskcdrdb]
Description=MySQL connection to 'asteriskcdrdb' database
driver=MySQL
server=localhost
database=asteriskcdrdb
Port=3306
Socket=/var/run/mysqld/mysqld.sock
option=3

Test ODBC driver

odbcinst -s -q

Use username & password in /etc/asterisk/res_odbc_additional.conf to test connectivity to the DB via ODBC.

isql -v MySQL-asteriskcdrdb freepbxuser 12e7c1f0c041ee853085624ec3bba112=

TFTP

If you plan to use hardware SIP phones you will probably want to enable the tftp server.

Create tftp configuration file.

nano /etc/xinetd.d/tftp

service tftp
{
protocol        = udp
port            = 69
socket_type     = dgram
wait            = yes
user            = nobody
server          = /usr/sbin/in.tftpd
server_args     = /tftpboot
disable         = no
}

Make the directory and restart the daemon to start tftp.

mkdir /tftpboot
chmod 777 /tftpboot
systemctl restart xinetd

Misc. settings

Change the “upload_max_filesize” from 2M to 20M to allow larger music on hold files.

nano +810 /etc/php5/apache2/php.ini

Add AllowOverride All under DocumentRoot so that .htaccess files in web folders are active.

nano +14 /etc/apache2/sites-available/000-default.conf

<Directory /var/www/html>
    AllowOverride All
    </Directory>

service apache2 restart
