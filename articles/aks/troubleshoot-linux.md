---
title: Linux-teljesítménynövelő eszközök
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan használhatja a linuxos teljesítményű eszközöket az Azure Kubernetes szolgáltatás (ak) használata során felmerülő gyakori problémák elhárítására és megoldására.
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90982036"
---
# <a name="linux-performance-troubleshooting"></a>A Linux teljesítményének hibaelhárítása

A Linux rendszerű gépek erőforrás-kimerülése gyakori probléma, és a tünetek széles körének megnyilvánulása. Ez a dokumentum magas szintű áttekintést nyújt az ilyen problémák diagnosztizálásához rendelkezésre álló eszközökről.

Ezen eszközök közül sok olyan intervallumot fogad el, amelyen a működés közbeni kimenet hozható létre. Ez a kimeneti formátum általában sokkal egyszerűbbé teszi a bepecsételés mintázatát. Ha elfogadták, a példa a meghívást fogja tartalmazni `[interval]` .

Ezeknek az eszközöknek a széles körű előzményei és számos konfigurációs lehetősége van. Ezen a lapon a hívások egyszerű részhalmaza látható a gyakori problémák kiemeléséhez. Az információk kanonikus forrása mindig az egyes eszközök dokumentációja. Ez a dokumentáció sokkal alaposabban fog megjelenni, mint amit itt biztosítunk.

## <a name="guidance"></a>Útmutató

A teljesítménnyel kapcsolatos problémák kivizsgálásának módszere a következő: Két gyakori módszer van HASZNÁLATban (kihasználtság, telítettség, hibák) és piros (sebesség, hibák, időtartam). A RED-t általában a szolgáltatások környezetében használják a kérelmeken alapuló figyeléshez. A HASZNÁLATot általában az erőforrások figyelésére használják: a gépek minden erőforrása esetében a kihasználtság, a telítettség és a hibák figyelhetők. Bármely gépen a négy fő típusú erőforrás a CPU, a memória, a lemez és a hálózat. Ezeknek az erőforrásoknak a magas kihasználtsága, telítettsége vagy hibái aránya a rendszerrel kapcsolatos lehetséges problémát jelzi. Ha probléma van, vizsgálja meg a kiváltó okot: Miért magas a lemez i/o-késése? A lemezek vagy a virtuális gép SKU-jának szabályozása megtörtént? Milyen folyamatokat írunk az eszközökre és milyen fájlokra?

Néhány példa a gyakori problémákról és a mutatók diagnosztizálására:
- IOPS szabályozása: az iostat mérése eszközönként IOPS. Győződjön meg arról, hogy egyetlen lemez sem éri el a korlátot, és az összes lemez összege kisebb, mint a virtuális gép korlátja.
- Sávszélesség-szabályozás: használja a iostat-t a IOPS, de az írási/olvasási sebesség méréséhez. Győződjön meg arról, hogy az Eszközönként és az összesített átviteli sebesség a sávszélesség határain kívül van.
- SNAT-kimerültség: Ez a SAR magas aktív (kimenő) kapcsolatként is megnyilvánulhat. 
- A csomagok elvesztése: a küldési/fogadási számhoz viszonyítva a TCP-újraküldési számlálón keresztül is mérhető. Mindkettő `sar` és `netstat` képes megjeleníteni ezeket az információkat.

## <a name="general"></a>Általános kérdések

Ezek az eszközök általános célúak, és alapszintű rendszeradatokra vonatkoznak. Ez jó kiindulási pont a további vizsgálathoz.

### <a name="uptime"></a>üzemidő

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

a üzemidő a rendszerüzemidőt és az 1, 5 és 15 perces terhelési átlagot biztosítja. A terhelés átlaga nagyjából megfelel a munkafolyamatoknak, vagy a nem megszakított munka befejeződésére vár. Az abszolút értékben ezeket a számokat nehéz lehet értelmezni, de az idő múlásával a hasznos információk megadását is elmondhatja:

- 1 perces átlagos > 5 perces átlag azt jelenti, hogy a terhelés egyre növekszik.
- 1 perces átlagos < 5 perces átlag azt jelenti, hogy a terhelés csökken.

a üzemidő azt is megvilágítja, hogy miért nem érhető el az információ: a probléma saját maga vagy újraindítással is megoldható, mielőtt a felhasználó hozzáférhessen a számítógéphez.

A rendelkezésre álló CPU-szálak száma nagyobb, mint az adott számítási feladattal kapcsolatos teljesítménnyel kapcsolatos probléma.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

a dmesg kiírja a kernel pufferét. Az olyan események, mint a OOMKill, adjon hozzá egy bejegyzést a kernel pufferéhez. A OOMKill vagy más erőforrás-kimerülési üzenetek megkeresése a dmesg-naplókban a probléma erős mutatója.

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top` átfogó áttekintést nyújt az aktuális rendszerállapotról. A fejlécek hasznos összesített információkat tartalmaznak:

- feladatok állapota: fut, alvó, leállítva.
- CPU-kihasználtság, ebben az esetben többnyire a tétlen időt mutatja.
- a teljes, az ingyenes és a felhasznált rendszermemória.

`top` rövid élettartamú folyamatokat hagyhat ki; `htop` `atop` az ilyen jellegű hiányosságok kijavítása közben hasonló felületeket biztosítanak.

## <a name="cpu"></a>CPU

Ezek az eszközök biztosítják a CPU-kihasználtsági adatokat. Ez különösen hasznos a működés közbeni kimenetben, ahol a mintázatok könnyen megoldhatók.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat` a hasonló CPU-adatokat a legfelülre nyomtatja, de CPU-szál szerint lebontva. Ha egyszerre látja az összes magot, akkor hasznos lehet a nagy mértékű egyensúlyú CPU-használat észleléséhez, például ha egy szálon futó alkalmazás 100%-os kihasználtságot használ. Ez a probléma nehezebben jelenhet meg, ha a rendszeren lévő összes CPU-ra összesítve van.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` hasonló információkat nyújt `mpstat` , valamint a `top` processzor (r oszlop), a memória statisztikái és az egyes munkaállapotokban ELtöltött CPU-idő százalékos arányának enumerálása.

## <a name="memory"></a>Memória

A memória nagyon fontos, és szerencsére könnyen nyomon követhető az erőforrás. Egyes eszközök a PROCESSZORt és a memóriát is jelenthetik, például a következőt: `vmstat` . `free`A gyors hibakereséshez azonban hasonló eszközök is hasznosak lehetnek.

### <a name="free"></a>ingyenes

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` a teljes memóriával, valamint a felhasznált és szabad memóriával kapcsolatos alapvető információkat mutatja be. `vmstat` hasznos lehet még az alapszintű memória elemzéséhez is, mivel a működés közbeni kimenet is biztosítható.

## <a name="disk"></a>Lemez

Ezek az eszközök mérik a lemez IOPS, a várakozási sorok és a teljes átviteli sebesség mértékét. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat` mélyreható betekintést nyújt a lemez kihasználtságára. Ezt a meghívást `-x` a kibővített statisztikákhoz kell átadni, `-y` hogy kihagyják a kezdeti kimeneti nyomtatási rendszer átlagait a rendszerindítás óta, és `1 1` meg kell határozni, hogy a kimenet egy blokk után véget ér. 

`iostat` számos hasznos statisztikát tesz elérhetővé:

- `r/s` a másodpercenkénti `w/s` olvasások és az írások másodpercenkénti száma. Ezeknek az értékeknek az összege IOPS.
- `rkB/s``wkB/s`a és a másodpercenként olvasható/írható. Ezeknek az értékeknek az összege az átviteli sebesség.
- `await` a várólistára helyezett kérelmek átlagos iowait-ideje ezredmásodpercben.
- `avgqu-sz` a várólista átlagos mérete a megadott intervallumban.

Azure-beli virtuális gépen:

- az `r/s` `w/s` egyes blokkos eszközök összege és száma nem haladhatja meg a lemez SKU-korlátait.
- az `rkB/s` `wkB/s`  egyes blokkos eszközök összege és száma nem haladhatja meg a lemez SKU-korlátait
- a `r/s` és `w/s` az összes blokkos eszköz összege nem lépheti túl a virtuális gép SKU-jának korlátait.
- a (  `rkB/s` z) és az összes blokkos eszközhöz tartozó wkB/s összege nem lépheti túl a virtuális gép SKU-jának korlátait.

Vegye figyelembe, hogy az operációsrendszer-lemez a kapacitásának megfelelő legkisebb SKU felügyelt lemezének számít. Egy 1024GB operációsrendszer-lemez például egy P30-lemeznek felel meg. Az elmúló operációsrendszer-lemezek és az ideiglenes lemezek nem rendelkeznek egyedi lemezzel; ezeket csak a teljes VM-korlátok korlátozzák.

A várakozási vagy avgqu-sz érték nem nulla értékű, és az i/o-tartalom is jó mutató.

## <a name="network"></a>Network (Hálózat)

Ezek az eszközök mérik a hálózati statisztikát, például az átviteli sebességet, az átviteli hibákat és a kihasználtságot. A mélyebb elemzések részletes TCP-statisztikát tehetnek elérhetővé a torlódások és az eldobott csomagok esetében.

### <a name="sar"></a>SAR

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar` hatékony eszköz az elemzések széles köréhez. Habár ez a példa a hálózati statisztika mérésére szolgál, egyformán hatékony a CPU és a memória használatának méréséhez. Ez a példa `sar` a `-n` jelzővel hívja meg a `DEV` (hálózati eszköz) kulcsszót, amely az eszköz hálózati átviteli sebességét jeleníti meg.

- `rxKb/s` `txKb/s` Egy adott eszköz összege és teljes átviteli sebessége. Ha ez az érték meghaladja a kiépített Azure hálózati adapterek korlátját, akkor a gépen felmerülő terhelések nagyobb hálózati késést tapasztalnak.
- `%ifutil` egy adott eszköz kihasználtságának mértéke. Mivel ez az érték a 100%-ot közelíti meg, a munkaterhelések nagyobb hálózati késést tapasztalnak.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Ez `sar` a hívás a kulcsszavakat használja a `TCP,ETCP` TCP-kapcsolatok vizsgálatához. A "retrans" utolsó sor harmadik oszlopa a TCP-újraküldések másodpercenkénti száma. A mező magas értékei megbízhatatlan hálózati kapcsolatokat jeleznek. Az első és a harmadik sorban az "Active" a helyi eszközről származik, míg a "távoli" a bejövő kapcsolatokat jelzi.  Az Azure-ban gyakran előfordul, hogy SNAT a portok kimerülése, ami `sar` segíthet az észlelésben. A SNAT-portok kimerülése magas "aktív" értékként nyilvánul meg, mivel a probléma oka a kimenő, helyileg kezdeményezett TCP-kapcsolatok magas aránya.

Ahogy `sar` az egy intervallumot vesz igénybe, a rendszer megjeleníti a működés közbeni kimenetet, majd kinyomtatja a meghívásból származó átlagos eredményeket tartalmazó kimenet végső sorait.

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat` a számos hálózati statisztikát képes betekinteni, itt az összefoglalás kimenetével meghívott. A probléma függvényében számos hasznos mező van. A TCP szakaszban található egyik hasznos mező a "sikertelen csatlakozási kísérletek". Ez jelezheti a SNAT-portok kimerülését vagy a kimenő kapcsolatokkal kapcsolatos egyéb problémákat. A nagy sebességű újraküldött szegmensek (a TCP szakaszban is) jelezhetik a csomagok kézbesítésével kapcsolatos problémákat. 
