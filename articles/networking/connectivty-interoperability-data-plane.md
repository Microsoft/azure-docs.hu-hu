---
title: 'Együttműködés az Azure-ban: adatsíkok elemzése'
description: Ez a cikk a ExpressRoute, a helyek közötti VPN-kapcsolat és az Azure-beli virtuális hálózatok közötti együttműködés elemzésére használható, a teszt beállításának adatsíkon történő elemzését tartalmazza.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4797fca5b4a756ca502147172e6f1590b9eacbe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234171"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Együttműködés az Azure-ban: adatsíkok elemzése

Ez a cikk a [teszt telepítésének][Setup]adatsíkon végzett elemzését ismerteti. Emellett áttekintheti a [tesztelési beállítások konfigurációját][Configuration] és a tesztelési célú telepítés [vezérlési sík elemzését][Control-Analysis] is.

Az adatsík-elemzés megvizsgálja az egyik helyi hálózatról (LAN vagy virtuális hálózatról) áthaladó csomagok útvonalát a topológián belül. A két helyi hálózat közötti adatelérési út nem szükségszerűen szimmetrikus. Ezért ebben a cikkben a továbbítási útvonalat egy helyi hálózatról egy másik, a fordított útvonaltól eltérő hálózatra elemezzük.

## <a name="data-path-from-the-hub-vnet"></a>A hub VNet származó adatok útvonala

### <a name="path-to-the-spoke-vnet"></a>A küllő VNet elérési útja

A virtuális hálózat (VNet) társítása a hálózati híd működését emulálja a két virtuális hálózatok között. Itt látható a VNet-ből egy virtuális gépre irányuló traceroute kimenet a küllő VNet:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.11.30.4

Trace complete.
```

Az alábbi ábra a hub VNet grafikus kapcsolatok nézetét és a küllős VNet mutatja be az Azure Network Watcher szempontjából:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Az ág VNet elérési útja

Itt jelennek meg a traceroute kimenete egy hub-VNet az ág VNet lévő virtuális gépre:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms     1 ms     1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     2 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

Ebben a traceroute-ben az első ugrás az Azure-beli VPN-átjáró VPN Gateway a hub-VNet. A második Ugrás a fiókiroda VNet VPN-átjárója. A fiókiroda VNet VPN-átjárójának IP-címe nincs meghirdetve a hub VNet. A harmadik ugrás az ág VNet található virtuális gép.

Az alábbi ábra a hub-VNet grafikus kapcsolatok nézetét és a Network Watcher perspektívájában lévő ág VNet mutatja be:

![2][2]

Ugyanezen a kapcsolatban az alábbi ábrán a rács nézet látható Network Watcherban:

![3][3]

### <a name="path-to-on-premises-location-1"></a>A helyszíni hely elérési útja 1

Itt látható a traceroute kimenet egy hub-VNet egy virtuális gépre a helyszíni helyen 1.

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     2 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

Ebben a traceroute-ben az első ugrás az Azure ExpressRoute Gateway Tunnel végpontja egy Microsoft Enterprise Edge-útválasztóra (MSEE). A második és a harmadik ugrás az ügyfél peremhálózati (CE) útválasztója és a helyszíni hely 1 hálózati IP-címe. Ezeket az IP-címeket a rendszer nem hirdeti meg a hub VNet. A negyedik Ugrás a virtuális gép a helyszíni helyen 1.


### <a name="path-to-on-premises-location-2"></a>A helyszíni hely elérési útja 2

Itt látható a (z)-ben a helyszíni VNet-ből egy virtuális gépre irányuló traceroute kimenet a következő helyen:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    75 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

Ebben a traceroute-ben az első Ugrás a ExpressRoute átjáró alagút végpontja egy MSEE. A második és a harmadik Ugrás a CE-útválasztó és a helyszíni 2. hely helyi hálózati IP-címei. Ezeket az IP-címeket a rendszer nem hirdeti meg a hub VNet. A negyedik Ugrás a helyszíni 2. helyen található virtuális gép.

### <a name="path-to-the-remote-vnet"></a>A távoli VNet elérési útja

Itt jelennek meg a traceroute kimenete egy központi VNet a távoli VNet lévő virtuális gépre:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    69 ms  10.17.30.4

Trace complete.
```

Ebben a traceroute-ben az első Ugrás a ExpressRoute átjáró alagút végpontja egy MSEE. A második Ugrás a távoli VNet átjárójának IP-címe. A második ugrás IP-tartománya nincs meghirdetve a hub VNet. A harmadik Ugrás a virtuális gép a távoli VNet.

## <a name="data-path-from-the-spoke-vnet"></a>A küllő VNet származó adatok elérési útja

A küllős VNet megosztja a hub-VNet hálózati nézetét. A küllős VNet a VNet-közvetítésen keresztül a hub VNet távoli átjárójának kapcsolatát használja úgy, mintha közvetlenül a küllős VNet csatlakozik.

### <a name="path-to-the-hub-vnet"></a>A hub VNet elérési útja

Itt látható a küllős VNet és a hub VNet lévő virtuális géphez a traceroute kimenete:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet"></a>Az ág VNet elérési útja

Itt látható a küllős VNet a VNet-ben lévő virtuális géphez a traceroute kimenete.

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

Ebben a traceroute-ben az első Ugrás a hub VNet VPN-átjárója. A második Ugrás a fiókiroda VNet VPN-átjárója. Az ág VNet VPN-átjárójának IP-címe nincs meghirdetve a hub/küllő VNet belül. A harmadik ugrás az ág VNet található virtuális gép.

### <a name="path-to-on-premises-location-1"></a>A helyszíni hely elérési útja 1

Itt látható a küllős VNet és a helyszíni helyen található virtuális géphez a traceroute kimenete:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    24 ms     2 ms     3 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     3 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

Ebben a traceroute-ben az első Ugrás a hub VNet ExpressRoute Gateway alagút-végpontja egy MSEE. A második és a harmadik Ugrás a CE-útválasztó és a helyszíni hely 1 hálózati IP-címe. Ezeket az IP-címeket a rendszer nem hirdeti meg a hub/küllő VNet. A negyedik Ugrás a virtuális gép a helyszíni helyen 1.

### <a name="path-to-on-premises-location-2"></a>A helyszíni hely elérési útja 2

Itt látható a küllős VNet és a helyszíni 2. helyen található virtuális géphez a traceroute kimenete:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    76 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

Ebben a traceroute-ben az első Ugrás a hub VNet ExpressRoute Gateway alagút-végpontja egy MSEE. A második és a harmadik Ugrás a CE-útválasztó és a helyszíni 2. hely helyi hálózati IP-címei. Ezeket az IP-címeket a rendszer nem hirdeti meg a hub/küllő virtuális hálózatok. A negyedik Ugrás a helyszíni 2. helyen található virtuális gép.

### <a name="path-to-the-remote-vnet"></a>A távoli VNet elérési útja

Itt látható a küllős VNet és a távoli VNet lévő virtuális géphez a traceroute kimenete:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.10.30.133
  2     *        *        *     Request timed out.
  3    71 ms    70 ms    70 ms  10.17.30.4

Trace complete.
```

Ebben a traceroute-ben az első Ugrás a hub VNet ExpressRoute Gateway alagút-végpontja egy MSEE. A második Ugrás a távoli VNet átjárójának IP-címe. A második ugrás IP-tartománya nincs meghirdetve a hub/küllő VNet. A harmadik Ugrás a virtuális gép a távoli VNet.

## <a name="data-path-from-the-branch-vnet"></a>A ág VNet származó adatok elérési útja

### <a name="path-to-the-hub-vnet"></a>A hub VNet elérési útja

Itt jelennek meg az ág VNet és a hub VNet lévő virtuális gépekre vonatkozó traceroute kimenetek:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     3 ms  10.10.30.4

Trace complete.
```

Ebben a traceroute-ben az első Ugrás a ág VNet VPN-átjárója. A második Ugrás a hub VNet VPN-átjárója. A hub VNet VPN-átjárójának IP-címe nincs meghirdetve a távoli VNet. A harmadik Ugrás a hub VNet található virtuális gép.

### <a name="path-to-the-spoke-vnet"></a>A küllő VNet elérési útja

A rendszer az ág VNet származó traceroute kimenetét a küllős VNet lévő virtuális gépre mutatja:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     1 ms    <1 ms     1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     2 ms  10.11.30.4

Trace complete.
```

Ebben a traceroute-ben az első Ugrás a ág VNet VPN-átjárója. A második Ugrás a hub VNet VPN-átjárója. A hub VNet VPN-átjárójának IP-címe nincs meghirdetve a távoli VNet. A harmadik Ugrás a küllős VNet található virtuális gép.

### <a name="path-to-on-premises-location-1"></a>A helyszíni hely elérési útja 1

Itt látható az ág VNet és a helyszíni helyen található virtuális gépre irányuló traceroute kimenete:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.2.30.125
  4     *        *        *     Request timed out.
  5     3 ms     3 ms     3 ms  10.2.30.10

Trace complete.
```

Ebben a traceroute-ben az első Ugrás a ág VNet VPN-átjárója. A második Ugrás a hub VNet VPN-átjárója. A hub VNet VPN-átjárójának IP-címe nincs meghirdetve a távoli VNet. A harmadik Ugrás a VPN-alagút megszakítási pontja az elsődleges CE-útválasztón. A negyedik Ugrás a helyszíni hely 1. belső IP-címe. Ez a helyi hálózati IP-cím nincs meghirdetve a CE-útválasztón kívül. Az ötödik Ugrás a cél virtuális gép a helyszíni helyen 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>A helyszíni hely 2 és a távoli VNet elérési útja

Ahogy a vezérlési sík elemzésében is beszéltünk, a VNet ág nem rendelkezik a helyszíni 2. vagy a hálózati konfiguráción belüli távoli VNet. A következő pingelési eredmények megerősítése: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

C:\Users\rb>ping 10.17.30.4

Pinging 10.17.30.4 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.17.30.4:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

## <a name="data-path-from-on-premises-location-1"></a>A helyszíni helyről származó adatok elérési útja 1

### <a name="path-to-the-hub-vnet"></a>A hub VNet elérési útja

Itt látható a helyszíni hely 1 és a központi VNet lévő virtuális gép számára a traceroute kimenete:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     2 ms     2 ms     2 ms  10.10.30.4

Trace complete.
```

Ebben a traceroute-ben az első két Ugrás a helyszíni hálózat részét képezi. A harmadik ugrás az elsődleges MSEE felület, amely a CE-útválasztóra néz. A negyedik Ugrás a hub-VNet ExpressRoute-átjárója. A hub VNet ExpressRoute-átjárójának IP-tartománya nem jelenik meg a helyszíni hálózatban. Az ötödik Ugrás a cél virtuális gép.

Network Watcher csak Azure-központú nézetet biztosít. Helyszíni szempontból az Azure Network Performance Monitort használjuk. A Network Performance Monitor olyan ügynököket biztosít, amelyeket az Azure-on kívüli hálózatokon is telepíthet az adatelérési utak elemzése céljából.

Az alábbi ábra a helyszíni hely 1 virtuális géphez való kapcsolódásának topológiai nézetét mutatja a hub VNet a ExpressRoute keresztül:

![4][4]

Ahogy korábban már említettük, a teszt telepítője egy helyek közötti VPN-t használ a ExpressRoute a helyszíni hely 1 és a hub VNet közötti kapcsolatokhoz. A biztonsági mentési adatelérési út teszteléséhez hozzon összefüggésbe egy ExpressRoute a helyszíni hely 1 elsődleges CE útválasztója és a megfelelő MSEE között. Ha ExpressRoute-kapcsolati hibát szeretne kiváltani, állítsa le a MSEE a CE-felületet:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     3 ms  10.10.30.4

Trace complete.
```

Az alábbi ábra a helyszíni hely 1 virtuálisgép-kapcsolatának topológiai nézetét mutatja a központi VNet a virtuális géphez a helyek közötti VPN-kapcsolaton keresztül, amikor a ExpressRoute kapcsolat leáll:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>A küllő VNet elérési útja

Itt látható a helyszíni hely 1 és egy virtuális gép között a traceroute kimenet a küllős VNet:

Térjünk vissza a ExpressRoute elsődleges kapcsolata, hogy az adatelérési út elemzése a küllős VNet felé:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     3 ms     2 ms     2 ms  10.11.30.4

Trace complete.
```

Hozza létre az elsődleges ExpressRoute 1 kapcsolattal az adatútvonal-elemzés hátralévő részében.

### <a name="path-to-the-branch-vnet"></a>Az ág VNet elérési útja

Itt látható a helyszíni hely 1 és egy virtuális gép között a traceroute kimenete az alábbi VNet:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

### <a name="path-to-on-premises-location-2"></a>A helyszíni hely elérési útja 2

Ahogy beszélünk a [vezérlési sík elemzésében][Control-Analysis], a helyszíni 1. helynek nincs láthatósága a helyszíni 2. helyen a hálózati konfigurációban. A következő pingelési eredmények megerősítése: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

### <a name="path-to-the-remote-vnet"></a>A távoli VNet elérési útja

Itt látható a helyszíni hely 1 és a távoli VNet lévő virtuális gép között a traceroute kimenete:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2     2 ms     5 ms     7 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5    69 ms    70 ms    69 ms  10.17.30.4

Trace complete.
```

## <a name="data-path-from-on-premises-location-2"></a>Adatútvonal a helyszíni helyről 2

### <a name="path-to-the-hub-vnet"></a>A hub VNet elérési útja

Itt látható a (z) helyszíni 2. hely és a hub VNet lévő virtuális gép között a traceroute kimenete:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.4
  3    <1 ms    <1 ms    <1 ms  192.168.31.22
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>A küllő VNet elérési útja

A következő helyen található traceroute-kimenet a helyi helyről a 2. a küllős VNet lévő virtuális gépre mutat:

```console
C:\Windows\system32>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops
  1    <1 ms    <1 ms     1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.0
  3    <1 ms    <1 ms    <1 ms  192.168.31.18
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Az ág VNet, a helyszíni hely 1 és a távoli VNet elérési útja

Ahogy beszélünk a [vezérlési sík elemzésében][Control-Analysis], a helyszíni 1. helynek nincs láthatósága az ág VNet, a helyszíni helyre 1 vagy a hálózati konfiguráción belüli távoli VNet. 

## <a name="data-path-from-the-remote-vnet"></a>A távoli VNet származó adatok elérési útja

### <a name="path-to-the-hub-vnet"></a>A hub VNet elérési útja

Itt látható a távoli VNet származó traceroute kimenet a hub VNet lévő virtuális gépre:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    65 ms    65 ms    65 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    68 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>A küllő VNet elérési útja

Itt látható a távoli VNet származó traceroute kimenet a küllő VNet lévő virtuális gépre:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    71 ms    69 ms    69 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>A fiókirodai VNet és a helyszíni hely elérési útja 2

Ahogy a [vezérlési sík elemzésében][Control-Analysis]tárgyalunk, a távoli VNet nem látja el az ág VNet vagy a helyszíni 2. helyet a hálózati konfigurációban. 

### <a name="path-to-on-premises-location-1"></a>A helyszíni hely elérési útja 1

Itt látható a távoli VNet származó traceroute kimenet a helyszíni helyen 1 virtuális gépre:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    69 ms    69 ms    69 ms  10.2.30.10

Trace complete.
```

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute és helyek közötti VPN-kapcsolat párhuzamosan

###  <a name="site-to-site-vpn-over-expressroute"></a>Helyek közötti VPN a ExpressRoute-en keresztül

A helyek közötti VPN-t konfigurálhatja úgy, hogy a Microsoft ExpressRoute a saját helyszíni hálózata és az Azure-virtuális hálózatok közötti személyes információcserére használja. Ezzel a konfigurációval az adatok titkosságot, hitelességet és integritást cserélhetnek. Az adatcsere is a visszajátszás elleni védelem. Ha további információt szeretne arról, hogyan konfigurálhat helyek közötti IPsec VPN-t bújtatási módban a Microsoft ExpressRoute használatával, tekintse meg a [helyek közötti VPN-t a ExpressRoute Microsoft-partneri kapcsolaton keresztül][S2S-Over-ExR]. 

A helyek közötti VPN Microsoft-társítást használó konfigurálásának elsődleges korlátozása az átviteli sebesség. Az IPsec-alagút átviteli sebességét a VPN Gateway kapacitása korlátozza. A VPN-átjáró átviteli sebessége kisebb, mint a ExpressRoute átviteli sebessége. Ebben az esetben az IPsec-alagúton keresztül, a biztonságos forgalomhoz és a privát és a többi forgalomhoz való használat révén optimalizálhatja a ExpressRoute sávszélesség-kihasználtságot.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Helyek közötti VPN biztonságos feladatátvételi útvonalként a ExpressRoute

A ExpressRoute redundáns áramköri párokként szolgál a magas rendelkezésre állás biztosításához. Különböző Azure-régiókban is konfigurálhatja a Geo-redundáns ExpressRoute-kapcsolatot. Továbbá, ahogyan azt a tesztelési beállítás is mutatja, egy Azure-régión belül egy helyek közötti VPN használatával létrehozhat egy feladatátvételi útvonalat a ExpressRoute-kapcsolathoz. Ha ugyanazokat az előtagokat hirdeti meg mindkét ExpressRoute és egy helyek közötti VPN-en, az Azure rangsorolja a ExpressRoute-t. Ha el szeretné kerülni a ExpressRoute és a két hálózat közötti pont-pont típusú VPN közötti aszimmetrikus útválasztást, a helyszíni hálózati konfigurációnak a ExpressRoute-kapcsolat használatával is be kell állnia, mielőtt a helyek közötti VPN-kapcsolatot használja.

A ExpressRoute és helyek közötti VPN-hez való egyidejű kapcsolatok konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute és helyek közötti együttélés][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Háttérbeli kapcsolat kiterjesztése küllős virtuális hálózatok és fiókirodák számára

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Küllős VNet-kapcsolat a VNet-társítás használatával

A hub és a küllős VNet architektúra széles körben használatos. A hub egy VNet az Azure-ban, amely központi kapcsolódási pontként szolgál a küllős virtuális hálózatok és a helyszíni hálózat között. A küllők a virtuális hálózatok, és a munkaterhelések elkülönítésére használhatók. A forgalom a helyszíni adatközpont és a hub között egy ExpressRoute vagy VPN-kapcsolaton keresztül áramlik. További információ az architektúráról: [sugaras hálózati topológia implementálása az Azure-ban][Hub-n-Spoke].

Egy adott régión belüli VNet-társítás esetén a küllő virtuális hálózatok az VNet-átjárókat (VPN-és ExpressRoute-átjárók) is használhatják a távoli hálózatokkal való kommunikációhoz.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Ág VNet-kapcsolat a helyek közötti VPN használatával

Előfordulhat, hogy a fiókirodák virtuális hálózatok, amelyek különböző régiókban vannak, és a helyszíni hálózatok egy hub-VNet keresztül kommunikálnak egymással. A konfiguráció natív Azure-megoldása a helyek közötti VPN-kapcsolat VPN használatával. Egy másik lehetőség, hogy hálózati virtuális berendezést (NVA) használ az útválasztáshoz a központban.

További információ: [Mi a VPN Gateway?][VPN] és [telepítsen egy magasan elérhető NVA][Deploy-NVA].


## <a name="next-steps"></a>Következő lépések

Tekintse meg a [ExpressRoute kapcsolatos gyakori kérdéseket][ExR-FAQ] :
-   Megtudhatja, hogy hány ExpressRoute-áramkört tud csatlakozni egy ExpressRoute-átjáróhoz.
-   Megtudhatja, hány ExpressRoute-átjárót tud csatlakozni egy ExpressRoute-áramkörhöz.
-   A ExpressRoute egyéb méretezési korlátainak megismerése.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Network Watcher hub-VNet kapcsolatának megtekintése küllős VNet"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Network Watcher hub-VNet és ág-VNet közötti kapcsolat megtekintése"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "A hub-VNet és egy ág VNet közötti kapcsolat hálózati nézetének Network Watcher"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Network Performance Monitor az 1. helyen található virtuális gép és a hub VNet közötti kapcsolat nézetét az 1. ExpressRoute keresztül"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Network Performance Monitor az 1. helyen található virtuális gép és a hub VNet közötti kapcsolat nézetét a helyek közötti VPN-en keresztül"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md