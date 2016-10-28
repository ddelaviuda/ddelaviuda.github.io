---
layout: post
title:  "Primarios de voz"
date:   2016-07-14 10:39:22
categories: redes
---


Configuración en extremo cliente
--------------------------------

La centralita debe tomar la señal de reloj del circuito del proveedor
                                                          
Protocolo EuroRDSI-DSS1

Plan de numeración ISDN E.164

Tipo de número (TON) National

Longitud 9 dígitos

CRC4 -- ON

CRC4-multiframe

Alarmas físicas en par de cobre
-------------------------------

{% highlight bash %}
%%DSP E1PORT: FN=3, SN=1, PN=29;%%
RETCODE = 0  accomplished

E32/T32/E63/T63 Port Status
---------------------------
               Working Mode = E1
    Send Line Coding Scheme = HDB3
 Receive Line Coding Scheme = HDB3
                  Loop Mode = NO LOOP
       Send Frame Structure = CRC4_MULTIFRAME
    Receive Frame Structure = CRC4_MULTIFRAME
          Highway Data Rate = 16.384Mbps
          System Clock Rate = 16.384MHz
           System Multiplex = Y
            Interleave Mode = Byte
             Line Impedance = 120 Ohm
     Communication Distance = SHORT
                 Clock Mode = SLAVE
         RAI alarm standard = ITUT
       Code Violation Count = 0
        Framing Error Count = 0
            CRC Error Count = 0
          E-Bit Error Count = 0
        Sa6-Bit Error Count = 0
    Multiframe Period Count = 0
                 COFA Count = 0
                        LOS = OFF
                        AIS = OFF
                        LFA = OFF
                        RRA = OFF
                       LMFA = OFF
                        XLO = OFF
                        XLS = OFF
                        NMF = OFF
                    TS16LOS = OFF
                    TS16AIS = OFF
                    TS16LFA = OFF
                     TS16RA = OFF

---    END
{% endhighlight %}

Canales en el primario
----------------------

{% highlight bash %}
+++    2107        2016-06-16 14:55:40+02:00 DST
O&M    #8900761
%%LST TG:TGN="CCLO001MAD01C",SSR=YES,SRT=YES,SOF=YES,SL=YES,SC=YES,SS=YES,SOT=YES,CLRDSP=YES,STGAP=YES,SCAC=YES,DIFF=YES,DCMI=YES;%%
RETCODE = 0  Operation succeeded

Basic Information
-----------------
                 Trunk group number  =  4780
                   Trunk group name  =  CCLO001MAD01C
                       Equipment ID  =  10.254.224.1:2944
                       Circuit type  =  PRA
                    Signalling link  =  4780
                    Group direction  =  Bidirectional trunk
             Circuit selection mode  =  Cyclic
                 Call-out authority  =  Intra-office
                                     =  Local
                                     =  Local toll
                                     =  National toll
                                     =  International toll
                                     =  Intra-office national toll
                                     =  Intra-office international toll
                                     =  Intra-office local toll
                                     =  Custom callout right1
                                     =  Custom callout right2
                                     =  Custom callout right3
                                     =  Custom callout right4
                                     =  Custom callout right5
                                     =  Custom callout right6
                                     =  Custom callout right7
                                     =  Custom callout right8
                                     =  Custom callout right9
                                     =  Custom callout right10
                                     =  Custom callout right11
                                     =  Custom callout right12
                                     =  Custom callout right13
                                     =  Custom callout right14
                                     =  Custom callout right15
                                     =  Custom callout right16
                  Call-in authority  =  Intra-office
                                     =  Local
                                     =  Local toll
                                     =  National toll
                                     =  International toll
                                     =  Intra-office national toll
                                     =  Intra-office international toll
                                     =  Intra-office local toll
                                     =  Custom callin right1
                                     =  Custom callin right2
                                     =  Custom callin right3
                                     =  Custom callin right4
                                     =  Custom callin right5
                                     =  Custom callin right6
                                     =  Custom callin right7
                                     =  Custom callin right8
                                     =  Custom callin right9
                                     =  Custom callin right10
                                     =  Custom callin right11
                                     =  Custom callin right12
                                     =  Custom callin right13
                                     =  Custom callin right14
                                     =  Custom callin right15
                                     =  Custom callin right16
                 Audio codec prefer  =  None
            EnhancedVoice Attribute  =  NULL
          Maximum Simultaneous Call  =  None
 Maximum Simultaneous Outgoing Call  =  None
Maximum Simultaneous Incomming Call  =  None
                         Codec List  =  G711_A
                                     =  G711_u
                                     =  G723_1
                                     =  G726
                                     =  G728
                                     =  G729
                                     =  MPEG4Audio
                                     =  G726_40
                                     =  G726_32
                                     =  G726_24
                                     =  G726_16
                                     =  H261
                                     =  H263
                                     =  MPEG4Video
                                     =  T38
                                     =  AMR
                                     =  CLEARMODE
                    Meter bill flag  =  No
                      Type of usage  =  Network usage
                  Master/Slave flag  =  Master

(Number of results = 1)

Sub-Route Related
-----------------
Trunk group number  =  4780
  Sub-route number  =  4780
    Sub-route name  =  CCLO001MAD01C
    Priority level  =  0
      Availability  =  255
 Master/Slave type  =  Master

(Number of results = 1)

Route Related
-------------
Trunk group number  =  4780
  Trunk group name  =  CCLO001MAD01C
         Route No.  =  4780
        Route Name  =  CCLO001MAD01C
 Master/Slave type  =  Master

(Number of results = 1)

Office Related
--------------
       Sub-route number  =  4780
         Sub-route name  =  CCLO001MAD01C
Office direction number  =  4780
  Office direction name  =  CCLO001MAD01C
       Peer office type  =  PBX

(Number of results = 1)

Show ClassMark info
-------------------
            Trunk group number  =  4780
        Incoming Trunk CM Flag  =  Not Configure
Incoming Trunk Class Mark Code  =  NULL

(Number of results = 1)

Show ClassMark info
-------------------
            Trunk group number  =  4780
        Outgoing Trunk CM Flag  =  Not Configure
Outgoing Trunk Class Mark Code  =  NULL

(Number of results = 1)

Trunk Group Load Related
------------------------
 Bearer index  Trunk seizure point  Caller sending change index  Callee sending change index 

 151           1                    0                            51                          
(Number of results = 1)

Circuit Distributing
--------------------
 Trunk group number  Startcircuit  Endcircuit  MN      Circuit type  Master/Slave flag 

 4780                13280         13295       22      PRA           Master            
 4780                13297         13311       22      PRA           Master            
(Number of results = 2)

TotalCircuitNumber
------------------
 TotalNumber 

 31          
(Number of results = 1)

Other Info
----------
                         Connect satellite circuit  =  False
                                International call  =  False
                                 Number incomplete  =  False
                                  Call source code  =  2721
                                  Call source name  =  Madrid
                                              DOD2  =  False
                                         CLIR flag  =  NULL
                                 Send initial tone  =  True
                                 Initial tone type  =  Recorded 0
                             Send exceptional tone  =  False
                             Exceptional tone type  =  Recorded 0
                                        Tone Group  =  65534
                              Charging source code  =  0
               Outgoing trunk charging source code  =  65535
                                Use opposite pulse  =  False
                       Centralized accounting flag  =  False
                                       EC use mode  =  Always ON
                        Support EC in hairpin call  =  False
                      Caller number provision mode  =  Default number
                             Default caller number  =  911820695
Calling number address attribute transmitting mode  =  Not transparently transmit
                         Call barring group number  =  65535
                           Upper seizure threshold  =  0
                           Lower seizure threshold  =  0
                    Network management source code  =  65535
           Idle circuits for weak call restriction  =  0
         Idle circuits for forced call restriction  =  0
             Software parameter of service control  =  Service 8
                                                    =  Service 18
      Second software parameter of service control  =  NULL
       Third software parameter of service control  =  NULL
                               Circuit pool number  =  0
                        Overlap send called number  =  False
         Reserved circuit number for priority user  =  0
         Reserved circuit number for incoming call  =  0
                                 Timer group index  =  0
                         Can request caller number  =  True
               Connect without valid caller number  =  True
                                Connect by default  =  True
             Whether request caller number for CCB  =  False
                                      Interface ID  =  None
                                            PRA ID  =  NULL
                             Preselect policy code  =  65535
                                        Gain value  =  0
                                 Master/Slave flag  =  Master

(Number of results = 1)

PRA Trunk Group Differences
---------------------------
             Default_Current_Settings  =  Current Settings:
                    Send initial tone  =  True
Software parameter of service control  =  Service 8
                                       =  Service 18
           Overlap send called number  =  False

             Default_Current_Settings  =  Default Settings:
                    Send initial tone  =  False
Software parameter of service control  =  NULL
           Overlap send called number  =  True

(Number of results = 2)

---    END
>>-----------------------------------------------------------------------------------------------------
{% endhighlight %}

Estado de los canales
---------------------

{% highlight bash %}

+++    2107        2016-06-16 14:53:57+02:00 DST
O&M    #8900760
%%DSP N1CCN:MN=22,CM=PRA,SC=13280,EC=13311;%%
RETCODE = 0  Operation succeeded

SoftSwitch Dual-Home Work Mode
------------------------------
 DH Work Mode

 Assist Deactive

N1CC status
-----------
 Line type     Module number   Port number   Channel number   Status              Master/Slave Flag

 PRA           22              13280         0                Uninstalled         Master
 PRA           22              13281         1                Idle                Master
 PRA           22              13282         2                Idle                Master
 PRA           22              13283         3                Idle                Master
 PRA           22              13284         4                Idle                Master
 PRA           22              13285         5                Idle                Master
 PRA           22              13286         6                Idle                Master
 PRA           22              13287         7                Idle                Master
 PRA           22              13288         8                Idle                Master
 PRA           22              13289         9                Idle                Master
 PRA           22              13290         10               Idle                Master
 PRA           22              13291         11               Idle                Master
 PRA           22              13292         12               Idle                Master
 PRA           22              13293         13               Idle                Master
 PRA           22              13294         14               Idle                Master
 PRA           22              13295         15               Idle                Master
 PRA           22              13296         16               Uninstalled         NULL
 PRA           22              13297         17               Idle                Master
 PRA           22              13298         18               Idle                Master
 PRA           22              13299         19               Idle                Master
 PRA           22              13300         20               Idle                Master
 PRA           22              13301         21               Idle                Master
 PRA           22              13302         22               Idle                Master
 PRA           22              13303         23               Idle                Master
 PRA           22              13304         24               Idle                Master
 PRA           22              13305         25               Idle                Master
 PRA           22              13306         26               Idle                Master
 PRA           22              13307         27               Idle                Master
 PRA           22              13308         28               Idle                Master
 PRA           22              13309         29               Idle                Master
 PRA           22              13310         30               Idle                Master
 PRA           22              13311         31               Idle                Master
(Number of results = 32)

---    END   
{% endhighlight %}
