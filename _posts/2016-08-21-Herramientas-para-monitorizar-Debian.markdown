---
layout: post
title:  "5 herramientas para monitorizar Debian"
date:   2016-08-21 10:39:22
categories: sistemas
---

Lista preliminar de herramientas y su instalación
-------------------------------------------------

Es muy probable que algunas de las cinco herramientas que vamos a comentar en este artículo no estén instaladas en el sistema Debian que estamos manejando. A saber: **top**, **iostat**, **vmstat**, **free** y **sar**.

Las herramientas que darían problemas son **iostat** y **sar** que están incluidas en el paquete **sysstat**, para instalar los programas procedemos como siempre según se indica.

{% highlight bash %}
sudo apt-get install sysstat
{% endhighlight %}

top
---

Para ejecutar *top* bastará con introducir el comando en el terminal.

{% highlight bash %}
$ top

top - 21:10:00 up 20 days, 23:23,  1 user,  load average: 0.00, 0.01, 0.05
Tasks:  65 total,   1 running,  64 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.7 sy,  0.0 ni, 99.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem:    120872 total,    93916 used,    26956 free,    10748 buffers
KiB Swap:   102396 total,        0 used,   102396 free,    50516 cached

  PID USER      PR  NI  VIRT  RES  SHR S  %CPU %MEM    TIME+  COMMAND           
14944 yo        20   0  4688 2416 2088 R   1.0  2.0   0:00.28 top               
 1577 root      20   0     0    0    0 S   0.3  0.0  18:41.03 RTW_CMD_THREAD    
    1 root      20   0  2164 1372 1268 S   0.0  1.1   1:07.70 init              
    2 root      20   0     0    0    0 S   0.0  0.0   0:00.69 kthreadd          
    3 root      20   0     0    0    0 S   0.0  0.0   3:10.52 ksoftirqd/0       
    5 root       0 -20     0    0    0 S   0.0  0.0   0:00.00 kworker/0:0H      
    7 root       0 -20     0    0    0 S   0.0  0.0   0:00.00 khelper           
    8 root      20   0     0    0    0 S   0.0  0.0   0:00.01 kdevtmpfs         
    9 root       0 -20     0    0    0 S   0.0  0.0   0:00.00 netns             
   10 root       0 -20     0    0    0 S   0.0  0.0   0:00.00 perf              
   11 root      20   0     0    0    0 S   0.0  0.0   0:01.55 khungtaskd        
   12 root       0 -20     0    0    0 S   0.0  0.0   0:00.00 writeback         
   13 root       0 -20     0    0    0 S   0.0  0.0   0:00.00 crypto            
   14 root       0 -20     0    0    0 S   0.0  0.0   0:00.00 bioset
{% endhighlight %}

La herramiento muestra un listado de los programas e hilos en ejecución, mientras está ejecutándose podemos pulsar 'h' para mostrar la ayuda y 'q' para salir.

iostat
------

Ejecutando los siguiente:

{% highlight bash %}
$ iostat
Linux 4.1.19+ (U) 	14/06/16 	_armv6l_	(1 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.13    0.00    0.15    0.01    0.00   99.70

Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
mmcblk0           0.06         0.35         0.30     611375     528484

{% endhighlight %}

Obtendremos el uso de la CPU y la tasa de copiado en KB a los discos. Este comando es muy útil si queremos mejorar la tasa de escritura y lectura y su equilibrio con el uso del la CPU.

vmstat
------

Ejecutando esto:

{% highlight bash %}
$ vmstat 
procs -----------memory---------- ---swap-- -----io---- -system-- ----cpu----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa
 0  0      4  34008  12372  40608    0    0     0     0    5   11  0  0 100  0

{% endhighlight %}

Podremos obtener un resumen del uso de los bloques más importante del sistema.

free
----

{% highlight bash %}
$ free -h
             total       used       free     shared    buffers     cached
Mem:          118M        84M        33M         0B        12M        39M
-/+ buffers/cache:        33M        84M
Swap:          99M         4K        99M
{% endhighlight %}

sar
---

Finalmente a continuación ejecutamos un ejemplo de uso de *sar*.

{% highlight bash %}
$ sar -u ALL 2 5
Linux 4.1.19+ (U) 	14/06/16 	_armv6l_	(1 CPU)

21:19:18        CPU      %usr     %nice      %sys   %iowait    %steal      %irq     %soft    %guest     %idle
21:19:20        all      0.00      0.00      1.55      0.00      0.00      0.00      0.00      0.00     98.45
21:19:22        all      0.52      0.00      0.52      1.56      0.00      0.00      0.52      0.00     96.88
21:19:24        all      0.00      0.00      1.55      0.00      0.00      0.00      0.00      0.00     98.45
21:19:26        all      1.04      0.00      0.52      0.00      0.00      0.00      0.00      0.00     98.44
21:19:28        all      0.00      0.00      1.04      0.00      0.00      0.00      0.00      0.00     98.96
Average:        all      0.31      0.00      1.04      0.31      0.00      0.00      0.10      0.00     98.24
{% endhighlight %}

Sar es una herramienta algo más compleja y para sacar el máximo partido será necesario configurar un almacenamiento periódico para sacar estadísticas.
