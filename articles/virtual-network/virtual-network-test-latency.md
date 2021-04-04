---
title: Azure-beli virtuális gépek hálózati késésének tesztelése Azure-beli virtuális hálózatokban | Microsoft Docs
description: Megtudhatja, hogyan tesztelheti a hálózati késést egy virtuális hálózat Azure-beli virtuális gépei között
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 7dc8aac730fdf46cab47a3297b8c001cb0b8e314
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226405"
---
# <a name="test-vm-network-latency"></a>Virtuális gép hálózati késleltetésének tesztelése

A legpontosabb eredmények eléréséhez mérje fel az Azure virtuális gép (VM) hálózati késését a feladathoz tervezett eszközzel. A nyilvánosan elérhető eszközök, például a SockPerf (Linux esetében) és a latte.exe (Windows rendszeren) képes elkülöníteni és mérni a hálózati késést más típusú késések, például az alkalmazások késésének kizárásával. Ezek az eszközök olyan hálózati forgalomra összpontosítanak, amelyek hatással vannak az alkalmazások teljesítményére (azaz Transmission Control Protocol [TCP] és a User Datagram Protocol [UDP] forgalomra). 

Más gyakori kapcsolódási eszközök, például a pingelés, a késést okozhatják, de előfordulhat, hogy az eredmények nem a valós munkaterhelésekben használt hálózati forgalmat jelölik. Ennek oka, hogy a legtöbb ilyen eszköz a Internet Control Message Protocol (ICMP) alkalmazást alkalmazza, amely az alkalmazások forgalmával eltérően kezelhető, és amelynek eredményei nem vonatkoznak a TCP és UDP protokollt használó munkaterhelésekre. 

A legtöbb alkalmazás által használt protokollok pontos hálózati késésének teszteléséhez a SockPerf (Linux esetében) és a latte.exe (Windows rendszeren) a legrelevánsabb eredményeket hozza létre. Ez a cikk mindkét eszközt magában foglalja.

## <a name="overview"></a>Áttekintés

Két virtuális gép, egy feladó és egy fogadó használata esetén hozzon létre egy kétirányú kommunikációs csatornát. Ezzel a módszerrel mindkét irányban elküldheti és fogadhatja a csomagokat, és megméri az oda-és visszaút idejét (RTT).

Ezzel a módszerrel mérhető a hálózati késés két virtuális gép között, vagy akár két fizikai számítógép között is. A késési mérések a következő esetekben lehetnek hasznosak:

- Hozzon létre egy teljesítménytesztet a központilag telepített virtuális gépek közötti hálózati késéshez.
- A hálózati késésben történt változások hatásainak összehasonlítása a kapcsolódó módosítások után:
  - Operációs rendszer (OS) vagy hálózati verem szoftver, beleértve a konfigurációs módosításokat.
  - Virtuálisgép-telepítési módszer, például üzembe helyezés egy rendelkezésre állási zónában vagy a közelségi elhelyezési csoport (PPG) számára.
  - A virtuális gép tulajdonságai, például a gyorsított hálózatkezelés vagy a méret módosítása.
  - Egy virtuális hálózat, például az Útválasztás vagy a szűrés módosítása.

### <a name="tools-for-testing"></a>Tesztelési eszközök
A késés méréséhez két különböző eszköz-lehetőség közül választhat:

* Windows-alapú rendszerek esetén: [latte.exe (Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* Linux-alapú rendszerek esetén: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Ezeknek az eszközöknek a használatával biztosíthatja, hogy csak a TCP-vagy UDP-adatátviteli időt mérjük, ne ICMP (ping) vagy más, az alkalmazások által nem használt csomagok, és ne befolyásolják a teljesítményüket.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Tippek a virtuális gépek optimális konfigurációjának létrehozásához

A virtuális gép konfigurációjának létrehozásakor vegye figyelembe az alábbi javaslatokat:
- Használja a Windows vagy Linux legújabb verzióját.
- A legjobb eredmények érdekében engedélyezze a gyorsított hálózatkezelést.
- Virtuális gépek üzembe helyezése [Azure Proximity-elhelyezési csoporttal](../virtual-machines/co-location.md).
- A nagyobb méretű virtuális gépek általában jobb teljesítményt biztosítanak a kisebb virtuális gépeknél.

### <a name="tips-for-analysis"></a>Elemzési tippek

A tesztek eredményeinek elemzésekor vegye figyelembe az alábbi javaslatokat:

- Hozzon létre egy alapkonfigurációt, amint az üzembe helyezés, a konfigurálás és az optimalizálás elkészült.
- Mindig hasonlítsa össze az új eredményeket egy alapértékkel, vagy ha nem, akkor az egyik tesztből a másikba az ellenőrzött módosításokat.
- Ismételje meg a teszteket, amikor változások figyelhetők meg vagy tervezettek.


## <a name="test-vms-that-are-running-windows"></a>Windows rendszerű virtuális gépek tesztelése

### <a name="get-latteexe-onto-the-vms"></a>latte.exe beolvasása a virtuális gépekre

Töltse le a [latte.exelegújabb verzióját ](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Fontolja meg latte.exe külön mappába helyezését, például *c:\Tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>latte.exe engedélyezése a Windows Defender-tűzfalon keresztül

A *fogadón* hozzon létre egy engedélyezési szabályt a Windows Defender-tűzfalon, hogy a latte.exe forgalom megérkezik. A legegyszerűbben úgy engedélyezheti a teljes latte.exe program nevét, hogy nem engedélyezi a megadott TCP-portok bejövő elérését.

A következő parancs futtatásával engedélyezze latte.exe a Windows Defender-tűzfalon keresztül:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Ha például átmásolta latte.exet a *c:\Tools* mappába, a parancs a következő lesz:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Késési tesztek futtatása

* A *fogadón* indítsa el latte.exe (futtassa a parancsot a cmd ablakból, ne a powershellből):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Körülbelül 65 000 iteráció elég hosszú a reprezentatív eredmények visszaküldéséhez.

    Minden elérhető portszám rendben van.

    Ha a virtuális gép IP-címe 10.0.0.4, a parancs a következőképpen fog kinézni:

    `latte -a 10.0.0.4:5005 -i 65100`

* A *feladón* indítsa el latte.exe (futtassa a parancsot a cmd ablakból, ne a powershellből):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Az eredményül kapott parancs megegyezik a fogadóval, kivéve a &nbsp; *-c* hozzáadásával, amely azt jelzi, hogy ez az *ügyfél* vagy a *Feladó*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Várjon az eredményekre. Attól függően, hogy a virtuális gépek milyen távol vannak, a teszt eltarthat néhány percig. A már elvégzett tesztek futtatása előtt érdemes lehet kevesebb iterációt kezdenie a sikeres teszteléshez.

## <a name="test-vms-that-are-running-linux"></a>Linux rendszerű virtuális gépek tesztelése

A Linux rendszerű virtuális gépek teszteléséhez használja a [SockPerf](https://github.com/mellanox/sockperf)-t.

### <a name="install-sockperf-on-the-vms"></a>A SockPerf telepítése a virtuális gépeken

A Linux rendszerű virtuális gépeken, a *küldőn* és a *fogadón* futtassa a következő parancsokat a SockPerf előkészítéséhez a virtuális gépeken. A fő disztribúciók parancsai vannak megadva.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Red Hat Enterprise Linux (RHEL)/CentOS

Futtassa az alábbi parancsot:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Ubuntu esetén

Futtassa az alábbi parancsot:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Minden disztribúcióhoz

Másolja, fordítsa le és telepítse a SockPerf az alábbi lépések szerint:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>SockPerf futtatása a virtuális gépeken

A SockPerf telepítésének befejezése után a virtuális gépek készen állnak a késési tesztek futtatására. 

Először indítsa el a SockPerf a *fogadón*.

Minden elérhető portszám rendben van. Ebben a példában a 12345-es portot használjuk:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Most, hogy a kiszolgáló figyel, az ügyfél megkezdheti a csomagok küldését a kiszolgálónak azon a porton, amelyen a figyel (ebben az esetben 12345).

Körülbelül 100 másodperc elég hosszú a reprezentatív eredmények visszaküldéséhez, ahogy az az alábbi példában is látható:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Várjon az eredményekre. Attól függően, hogy a virtuális gépek milyen távolságban vannak, az ismétlések száma eltérő lesz. Ha a hosszabb tesztek futtatása előtt szeretné tesztelni a sikerességet, érdemes lehet 5 másodpercnél rövidebb teszteket kezdeni.

Ez a SockPerf példa egy átlagos csomagra jellemző 350 bájtos üzenet méretét használja. Nagyobb vagy alacsonyabb méretet is megadhat, hogy az eredmények pontosabban képviseljék a virtuális gépeken futó számítási feladatokat.


## <a name="next-steps"></a>Következő lépések
* Növelje a késést egy [Azure közelségi elhelyezési csoporttal](../virtual-machines/co-location.md).
* Útmutató a [virtuális gépek hálózatkezelésének optimalizálásához](../virtual-network/virtual-network-optimize-network-bandwidth.md) a forgatókönyvhöz.
* Olvassa el [, hogy a sávszélesség hogyan legyen lefoglalva a virtuális gépekhez](../virtual-network/virtual-machine-network-throughput.md).
* További információ: [Azure Virtual Network GYIK](../virtual-network/virtual-networks-faq.md).