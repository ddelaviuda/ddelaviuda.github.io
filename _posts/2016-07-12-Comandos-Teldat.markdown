---
layout: post
title:  "Router Teldat 3G, comandos básicos" 
date:   2016-07-12 18:39:22
categories: redes
---

Acceso inicial
--------------

El escenario que se va a suponer es un ordenador conectado a una de las interfaces del Router (IP 192.168.1.1).

Desde la máquina linux ejecutamos **telnet**.

{% highlight C %}
telnet 192.168.1.1
{% endhighlight %}

El router nos pedirá usuario y contraseña.

{% highlight C %}
User:usuario
Password:***********
{% endhighlight %}

Una vez que hemos accedido se nos mostrará el modelo del router y la versión de *software*, para finalmente disponer del *prompt* para introducir comandos.

{% highlight C %}
router *
{% endhighlight %}

Pulsando '?' veremos el las opciones disponibles.

{% highlight C%}
router *?
  config            Edit configuration parameters, without altering the
                    operation
  flush             Clears all the messages stored up to that moment in the
                    events buffer
  intercept         Change the procedures escape character
  load              Reload the program from the flash memory
  logout            Ends the Telnet connection established with the device
  monitor           Monitor the state of the system
  process           Access to a different device procedure
  restart           Restart the device
  running-config    Edit configuration parameters, altering the operation
  status            Displays the names and identifiers of each procedure
  telnet            Do a telnet
  vrf-telnet        Do a telnet from a secondary vrf
{% endhighlight %}

Para cambiar el modo de operación podemos usar *process ?*

{% highlight C %}
router *process ?
  1    Starting point on booting a console session
  2    Display events produced in the system
  3    Monitor the state of the system
  4    Edit configuration parameters, without altering the operation
  5    Edit configuration parameters, altering the operation
  6    Process information
{% endhighlight %}


Modo monitorización (*process 3*)
---------------------------------

Para acceder al modo monitorización pulsamos 

{% highlight C %}
router *process 3
Console Operator
router +
{% endhighlight %}

El signo '+' indica que hemos cambiado de modo de operación. Ahora obtendremos los comandos disponibles de un modo similar al anterior.

{% highlight C %}
router +?
  buffer                 Packet buffers assigned to each interface
  clear                  Clear network statistics
  configuration          List status of current protocols and interfaces
  device                 List statistics for the specified interface
  error                  List error counters
  event                  Event Logging System environment
  feature                Access to monitoring commands for router features
  last-config-changes    Display the last changes made in the configuration
  log                    Dump log data
  malloc-monitor         Malloc monitor information
  memory                 Display memory, buffer and packet data
  network                Enter the console environment of a specified network
  node                   Enter the node monitoring environment
  protocol               Enter the commands environment for a specified
                         protocol
  queue                  Display buffer statistics for a specified interface
  quick                  Access the quick menu monitoring
  statistics             Display statistics for a specified interface
  system                 Permit monitoring of the systems memory and stacks
  telephony              Monitoring environment for the telephony functions
  uci                    Encryption statistics
  web-probe              Access the Web poll monitoring
{% endhighlight %}

Podemos empezar haciendo una prueba de ping para ello.

{% highlight C %}
router +ping
IP destination []? 8.8.8.8
IP source (192.168.1.1 suggested, 0.0.0.0 for dynamic) [0.0.0.0]? 0.0.0.0
Number of data bytes[56]?
Differentiated Services Field[0]?
Time between pings(>=10ms)[1000]?
Number of pings[0]?
Time out(>=10ms)[0]?
Avoid fragmentation[no](Yes/No)  [N]?
{% endhighlight %}

Para cancelar el ping debemos pulsar intro o **exit** e intro desde otros modos.

El comando **device** muestra los errores en las interfaces.
{% highlight C %}
router +device
                                       Auto-test   Auto-test     Maintenance
Interface               CSR    Vect      valids    failures        failures
ethernet0/0               0       0           4           0               0
cellular1/0               0       0           2           1               0
cellular1/1               1       0           2           1               0
ppp1                      0       0          11          10               0
{% endhighlight %}

Es posible explorar más en profundiad una interfaz con **network 'interfaz'**

{% highlight C %}
router +network ethernet0/0
router ethernet0/0 ETH+?
  bdescs             List descriptors
  bitrate            Bit rate monitor
  clear              Clear information
  counters           List device counters
  dot1x              Access to 802.1X monitoring
  dump               Dump internal stats
  llc                Access to llc monitoring
  oam                Ethernet OAM monitoring
  repeater-switch    Access to switch monitoring
  status             List interface status
  exit
{% endhighlight %}

Cada interfaz tiene distintas opciones.

{% highlight C %}
router +network cellular1/1
router cellular1/1 AT+?
  at-mode         Send AT commands directly to the module
  bdescs          List descriptors
  bitrate         Bit rate monitor
  buffer          Display saved commands and answers
  clear           Clear interface and module parameters
  command         Send AT command to the module
  dump            Dump internal stats
  list            List interface and module parameters
  power-module    Module power control
  qmi             QMI options
  sms             SMS menu
  statistics      Interface statistics
  exit
{% endhighlight %}

Para ver la potencia en una conexión radio
{% highlight C %}
router cellular1/1 AT+network status
Querying...Please wait...
Registration state: Home network
PLMN Public Land Mobile Network code: xxxxx
PLMN Public Land Mobile Network name: operador
Cell Location Area Code 0xXXXX (xxxx), Identification 0xXXXX (xxx)
System Mode WCDMA, Service domain: PS
Network technology currently in use: HSDPA/HSUPA
Receive signal code power of the active set's strongest cells(RSCP):  -59 dBm
Total energy per chip per power density value of set's strongest cells(EcIo):  -2.0 dB
Last EcIo measured in WCDMA DATA mode:  -3.5 dB
Primary Scrambling Code (PSC) 0xXXXX (xx)
Operational status
Current Time:  140100           Temperature: 40
Bootup Time:   516              Mode:        ONLINE
System mode:   WCDMA            PS state:    Attached
WCDMA band:    IMT2000          GSM band:    Unknown
WCDMA channel: 10713            GSM channel: 65535
GMM (PS) state:REGISTERED       NORMAL SERVICE
MM (CS) state: IDLE             NORMAL SERVICE

WCDMA L1 State:L1M_PCH_SLEEP    RRC State:   DISCONNECTED

RX level (dBm):-57
Coverage level: 5 (*****)
{% endhighlight %}
**Protocolo IP**
Accedemos mediante este comando.

{% highlight C %}
router +protocol ip
{% endhighlight %}

Notese que cambia el *prompt*.

{% highlight C %}
router IP+?
  access-controls        IP access control mode and configured access control
                         records
  aggregation-route      Configured aggregation routes
  bping                  Broadcast ping
  cache                  Cached routing table
  counters               IP statistics
  dump-routing-table     Routing table
  interface-addresses    IP interface addresses
  ipsec                  IPSec monitoring
  nat                    NAT monitoring
  ping                   Send ping queries to any other host
  pool                   Address pool established in the router and ranges of
                         addresses reserved
  proxy-igmp             Proxy IGMP monitoring
  route-given-address    Existing routes for a specific destination IP address
  sizes                  Size of specific IP parameters
  static-routes          Configured static routes
  tcp-list               List of TCP connections
  traceroute             Complete path to a particular destination
  tvrp                   TVRP monitoring
  udp-list               List of registered UDP ports
  vrf                    IP monitoring in a VPN Routing/Forwarding instance
  vrrp                   VRRP monitoring
  exit
{% endhighlight %}

El comando **cache** es similar a **ip accounting** de Cisco.

{% highlight C %}
router IP+cache ?

Destination           Usage        Next hop
 
207.46.193.115    98           207.46.193.115   (ppp1)
160.46.253.107    147          160.46.253.107   (ppp1)
50.22.210.154     3            50.22.210.154    (ppp1)
10.96.223.22      4            10.96.223.22     (ethernet0/0)
157.55.231.252    10           157.55.231.252   (ppp1)
213.199.148.230   5            213.199.148.230  (ppp1)
10.96.223.32      424          10.96.223.32     (ethernet0/0)
10.96.223.50      75           10.96.223.50     (ethernet0/0)
{% endhighlight %}

Para ver las IP de las interfaces ejecutamos los que sigue.

{% highlight C %}
router IP+interface-addresses
Interface IP Addresses:
-----------------------
ethernet0/0        192.168.1.1/24
ppp1               unnumbered - using global-address (1.1.1.1)

Special IP Addresses:
---------------------
internal-address    0.0.0.0
management-address  10.0.0.1
router-id           0.0.0.0
global-address      8.8.8.8

{% endhighlight %}

Para mostrar la tabla de rutas.

{% highlight C %}
router IP+dump-routing-table
Type                Dest net/Mask  Cost Age  Next hop(s)

stat(1)[0]          0.0.0.0/0  [ 60/1 ] 0   ppp1
sbnt(0)[0]         2.0.0.0/8  [240/1 ] 0   none
 dir(0)[1]      1.0.0.1/32 [  0/1 ] 0   snk
 dir(0)[1]      192.168.1.1/24 [  0/1 ] 0   ethernet0/0

Default gateway in use.
Type Cost Age  Next hop
stat 1    0   ppp1

Routing table size: 7 nets (728 bytes), 4 nets known, 4 shown
{% endhighlight %}

Protocolo HSRP/TVRP

{% highlight C %}
router IP+tvrp
router TVRP+list all

              ===== Global TVRP Parameters =====

  TVRP is currently: ENABLED
  TVRP port (UDP):   1985
  Virtual redirects: ENABLED
  Unknown packets:   4
  Authentication Failed packets:   0


                ===== List of TVRP groups =====

 +------------------------------------------------------------+
 |                      TVRP GROUP:   1                       |
 +------------------------------------------------------------+
   Virtual IP:  192.168.1.3
   Virtual MAC: 00-00-0c-07-ac-01
   Current local IP/Interface: 192.168.1.1
   ACTIVE Router:  192.168.1.1
   STANDBY Router: 192.168.1.2
   Hellotime: 3               Holdtime: 10
   TVRP state:  STANDBY       Previous state:    SPEAK
   Currently  RUNNING         Last event: HELO_EXP
   Initial: 1           Learn: 0           Listen: 1
   Speak: 2           Standby: 2           Active: 1
     Hello messages -->  sent:   46660,   received:   51616
     Coup messages --->  sent:       0,   received:       2
     Resign messages ->  sent:       1,   received:       0
{% endhighlight %}


**Protocolo ARP**

De forma similar al protocolo IP.

{% highlight C %}
router +protocol arp
router ARP+dump ethernet0/0

ARP entries for IP protocol
MAC address           IP address       Timeout(min)
00-00-00-00-00-00     192.168.1.2      200
{% endhighlight %}

Modo configuración
-------------------

Para salir de un modo de configuración previo pulsaremos **Ctrl+p**

{% highlight C %}
router Config>?
  add                    Create a virtual interface
  autoinstall            Set autoinstall parameters
  backup-files           Perform backup router system files
  banner                 Define a banner
  config-media           Select the active storage device
  confirm-cfg            Confirms current configuration
  confirm-cfg-needed     Enables the need of configuration confirmation
  copy                   Copies Running Configuration to Static Configuration
  description            Configuration description
  disable                Disable specific features
  dump-command-errors    View command line errors
  enable                 Enable specific features
  event                  Record those events you wish to be stored
  feature                Additional features of the router
  file                   Access the files present in the device
  firmware-checking      Enables cheching when firmware files are required
  format                 Format a storage unit in the device
  global-profiles        Defines the router PPP, ATM etc profiles
  licence-change         Changes current licence
  list                   Lists all kind of information
  log-command-errors     Start logging command line errors
  network                Access the commands menu to configure a specific
                         interface
  no                     Negate a command or set its defaults
  node                   Access the X25 or XOT node configuration
  privilege              Defines command privilege parameters
  protocol               Access the configuration environment of a specific
                         protocol
  save                   Store the configuration in the active storage unit
  set                    Configure various general parameters
  strong-password        Enable strong password
  telephony              Set VoIP telephony parameters
  time                   Change and check the date and time
  uci                    UCI configuration
  unset-demo-licence     Disable demo licence and return to base licence
  user                   Create a registered user
  end                    Configuration end
{% endhighlight %}

Para mostrar la configuración.

{% highlight C %}
router Config>show config
.
.
.
{% endhighlight %}

Con esto termina la entrada de introducción.
