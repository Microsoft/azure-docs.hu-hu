---
title: Azure-beli virtuálisgép-hálózat teljesítményének tesztelése
titlesuffix: Azure Virtual Network
description: A NTTTCP használatával megcélozhatja a hálózatot a teszteléshez, és csökkentheti a teljesítményt befolyásoló egyéb erőforrások használatát.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: 27cc55978582468c759603571bd8b0e69f29db5d
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226573"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Sávszélesség/átviteli sebesség tesztelése (NTTTCP)

Az Azure-ban a hálózati teljesítmény teljesítményének tesztelésekor érdemes olyan eszközt használni, amely a hálózat tesztelésére és a teljesítményre gyakorolt hatásának minimalizálására használható. A NTTTCP használata ajánlott.

Másolja az eszközt két azonos méretű Azure-beli virtuális gépre. Egy virtuális gép KÜLDŐként és a másikat FOGADÓként működik.

#### <a name="deploying-vms-for-testing"></a>Virtuális gépek üzembe helyezése teszteléshez
Ebben a tesztben a két virtuális gépnek ugyanabban a [közelségi elhelyezési csoportban](../virtual-machines/co-location.md) vagy ugyanazon a rendelkezésre állási csoporton kell lennie, hogy a belső IP-címeket használják, és kizárja a terheléselosztó a tesztből való kizárását. A virtuális IP-címen tesztelhető, de az ilyen típusú tesztelés kívül esik a jelen dokumentum hatókörén.

Jegyezze fel a fogadó IP-címét. Nevezzük ezt az IP-címet: "a. b. c. r"

Jegyezze fel a virtuális gép magok számát. Hívjuk ezt a " \# NUM \_ mag" kifejezést

Futtassa a NTTTCP tesztet 300 másodpercig (vagy 5 percben) a küldő virtuális gépen és a fogadó virtuális gépen.

Tipp: Ha első alkalommal állítja be ezt a tesztet, próbálkozzon rövidebb próbaidőszaktal, hogy hamarabb visszajelzést kapjon. Ha az eszköz a várt módon működik, a legpontosabb eredmények érdekében a tesztelési időszakot 300 másodpercre kell kiterjeszteni.

> [!NOTE]
> A küldőnek **és** a fogadónak **ugyanazt a** tesztelési időtartam paramétert (-t) kell megadnia.

Egy TCP-adatfolyam tesztelése 10 másodpercig:

Fogadó paraméterei: ntttcp-r-t 10-P 1

Feladó paraméterei: ntttcp-s 10.27.33.7-t 10-n 1-P 1

> [!NOTE]
> Az előző mintát csak a konfiguráció megerősítésére kell használni. A dokumentum későbbi részében érvényes tesztelési példákat talál.

## <a name="testing-vms-running-windows"></a>WINDOWS rendszerű virtuális gépek tesztelése:

#### <a name="get-ntttcp-onto-the-vms"></a>NTTTCP beolvasása a virtuális gépekre.

Töltse le a legújabb verziót: https://github.com/microsoft/ntttcp/releases/download/v5.35/NTttcp.exe

Vagy tekintse meg a legfelső szintű GitHub oldalt: <https://github.com/microsoft/ntttcp>\

Fontolja meg a NTTTCP különálló mappába helyezését, például a c: \\ Tools

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>NTTTCP engedélyezése a Windows tűzfalon keresztül
A FOGADÓn hozzon létre egy engedélyezési szabályt a Windows tűzfalon, hogy a NTTTCP-forgalom megérkezik. A legegyszerűbben úgy engedélyezheti a teljes NTTTCP program nevét, hogy nem engedélyezi a bejövő TCP-portok használatát.

A ntttcp engedélyezése a Windows tűzfalon keresztül:

netsh advfirewall Firewall Add Rule program = \<PATH\> \\ntttcp.exe Name = "ntttcp" protokoll = bármely dir = in Action = Enable engedélyezése = Yes Profile = any

Ha például a "c: Tools" mappába másolta ntttcp.exe \\ , akkor ez a következő lesz: 

netsh advfirewall Firewall Add Rule program = c: \\ eszközök \\ntttcp.exe név = "ntttcp" protokoll = bármely dir = in Action = Allow Enable = Yes Profile = any

#### <a name="running-ntttcp-tests"></a>NTTTCP-tesztek futtatása

NTTTCP elindítása a FOGADÓn (**Futtatás a cmd-ből**, nem a powershellből):

ntttcp-r – m [2 \* \# NUM \_ mag], \* , a. b. c. r-t 300

Ha a virtuális gépnek négy magja van, és a 10.0.0.4 IP-címe van, a következőképpen fog kinézni:

ntttcp-r – m 8, \* , 10.0.0.4-t 300


NTTTCP elindítása a KÜLDŐn (**Futtatás a cmd-ből**, nem a powershellből):

ntttcp-s – m 8, \* , 10.0.0.4-t 300 

Várjon az eredményekre.


## <a name="testing-vms-running-linux"></a>LINUX rendszerű virtuális gépek tesztelése:

A nttcp-for-Linux használata. Elérhető innen: <https://github.com/Microsoft/ntttcp-for-linux>

A Linux rendszerű virtuális gépeken (a küldő és a FOGADÓn is) futtassa ezeket a parancsokat a ntttcp-for-Linux rendszerre való előkészítéséhez a virtuális gépeken:

CentOS – a git telepítése:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu – a git telepítése:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Mindkettőn végezze el és telepítse a következőt:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

A Windows példához hasonlóan feltételezzük, hogy a linuxos fogadó IP-címe 10.0.0.4

Indítsa el a NTTTCP-for-Linux szolgáltatást a FOGADÓn:

``` bash
ntttcp -r -t 300
```

És a KÜLDŐn futtassa a következőt:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
A teszt hossza alapértelmezett értéke 60 másodperc, ha nincs megadva a Time paraméter

## <a name="testing-between-vms-running-windows-and-linux"></a>Windows és LINUX rendszerű virtuális gépek közötti tesztelés:

Ebben a forgatókönyvben engedélyeznie kell a nem szinkronizált módot, hogy a teszt fusson. Ez a Linux **-N** , a **-NS jelölővel** pedig a Windows esetében történik.

#### <a name="from-linux-to-windows"></a>Linuxról Windowsra:

Fogadó \<Windows> :

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Küldő \<Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Windowsról Linuxra:

Fogadó \<Linux> :

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Küldő \<Windows> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Cloud Service-példányok tesztelése:
A következő szakaszt hozzá kell adnia a ServiceDefinition. csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Következő lépések
* Az eredményektől függően előfordulhat, hogy a forgatókönyvhöz a [hálózati átviteli sebességű gépeket optimalizálni](virtual-network-optimize-network-bandwidth.md) kell.
* További információ arról [, hogyan vannak lefoglalva a sávszélesség a virtuális gépekhez](virtual-machine-network-throughput.md)
* További információ az [Azure Virtual Network gyakori kérdéseiről (GYIK)](virtual-networks-faq.md)