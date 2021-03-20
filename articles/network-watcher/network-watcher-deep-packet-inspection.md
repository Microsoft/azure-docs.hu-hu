---
title: Csomagok ellenőrzése az Azure Network Watcher | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Network Watcher a virtuális gépekről összegyűjtött részletes csomagok ellenőrzéséhez
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3bd85d6faf05fcf659e9335ee9de3d64198dfa08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011169"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Csomagok ellenőrzése az Azure Network Watcher

A Network Watcher csomag rögzítési funkciójával elindíthatja és kezelheti az Azure-beli virtuális gépeken a portálról, a PowerShellből, a CLI-ből és programozott módon, az SDK-ban és REST API keresztül. A csomagok rögzítése lehetővé teszi, hogy olyan forgatókönyveket adjon meg, amelyek a csomagok szintjének megfelelő adatokat igényelnek, mivel az információkat könnyen használható formátumban kell megadni. A szabadon elérhető eszközöket kihasználva ellenőrizheti az adatokat, és megvizsgálhatja a virtuális gépekről érkező és onnan érkező kommunikációt, és betekintést nyerhet a hálózati forgalomba. A csomagok rögzítése többek között a következőkből áll: a hálózat vagy az alkalmazások problémáinak kivizsgálása, a hálózati visszaélések észlelése és a behatolási kísérletek, illetve a szabályozások megfelelőségének fenntartása Ebben a cikkben bemutatjuk, hogyan nyitható meg a Network Watcher által biztosított csomag-rögzítési fájl egy népszerű nyílt forráskódú eszköz használatával. Olyan példákat is ismertetünk, amelyek bemutatják a kapcsolati késés kiszámítását, a rendellenes forgalom azonosítását és a hálózati statisztikák vizsgálatát.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a korábban futtatott csomagok rögzítésének egyes előre konfigurált forgatókönyveit ismerteti. Ezek a forgatókönyvek olyan képességeket mutatnak be, amelyek a csomagok rögzítésének áttekintésével érhetők el. Ez a forgatókönyv a [WireShark](https://www.wireshark.org/) használatával vizsgálja meg a csomagok rögzítését.

Ez a forgatókönyv feltételezi, hogy már futtatta a csomagok rögzítését egy virtuális gépen. Ha meg szeretné tudni, hogyan hozhat létre egy csomagot rögzítő látogatást a csomagok rögzítésének [kezelése a portálon](network-watcher-packet-capture-manage-portal.md) vagy a REST-mel a [csomagok rögzítésének REST API használatával történő kezelésével](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Eset

Ebben az esetben a következőket kell tennie:

* Csomagok rögzítésének áttekintése

## <a name="calculate-network-latency"></a>Hálózati késés kiszámítása

Ebben a forgatókönyvben bemutatjuk, hogyan lehet megtekinteni a két végpont között bekövetkező Transmission Control Protocol (TCP) beszélgetés kezdeti oda-és RTT.

TCP-kapcsolatok létrehozásakor a rendszer az első három, a csatlakozás során küldött csomagot követ egy, a háromutas kézfogásnak nevezett mintát. Ha megvizsgálja az ebben a kézfogásban elküldött első két csomagot, az ügyféltől érkező első kérést és a kiszolgáló válaszát, a kapcsolat létrejötte után kiszámíthatja a késést. Ezt a késést a kerekítési idő (RTT) nevezik. A TCP protokollal és a háromutas kézfogással kapcsolatos további információkért tekintse meg a következő erőforrást. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>1. lépés

WireShark elindítása

### <a name="step-2"></a>2. lépés

Töltse be a **. Cap** fájlt a csomag rögzítéséről. Ez a fájl megtalálható a blobban, amelyet a virtuális gépen helyileg mentettek, attól függően, hogy hogyan konfigurálta.

### <a name="step-3"></a>3. lépés

Ha meg szeretné tekinteni a TCP-beszélgetések kezdeti oda-és beosztási idejét (RTT), akkor csak a TCP-kézfogásban érintett első két csomagot tekintjük meg. A háromutas kézfogásban az első két csomagot fogjuk használni, amelyek a [SYN], [SYN, ACK] csomagok. Ezek neve a TCP-fejlécben beállított jelzők. A kézfogásban ([ACK]) lévő utolsó csomag nem lesz használatban ebben a forgatókönyvben. Az ügyfél elküldi a [SYN] csomagot. A kérést követően a kiszolgáló elküldi a [ACK] csomagot, amely visszaigazolja, hogy fogadja a SYN-t az ügyféltől. Annak a ténynek a kihasználása, hogy a kiszolgáló válasza nagyon kis terhelést igényel, a RTT kiszámításával kiszámítjuk, hogy az ügyfél a [SYN, ACK] csomagot fogadta el a-ügyfél által küldött Time [SYN] csomaggal.

A WireShark használata esetén ez az érték számít a számunkra.

Ahhoz, hogy könnyebben megtekinthesse az első két csomagot a TCP háromutas kézfogásban, a WireShark által biztosított szűrési funkciót fogjuk használni.

A szűrő WireShark való alkalmazásához bontsa ki a rögzítésben a [SYN] csomag "Transmission Control Protocol" szegmensét, és vizsgálja meg a TCP-fejlécben beállított jelzőket.

Mivel a (z) [SYN] és a [SYN, ACK] csomagok szűrését keresjük, a jelzők területen ellenőrizze, hogy a SYN bit értéke 1, majd kattintson a jobb gombbal a SYN bites > alkalmazza szűrő-> kiválasztva.

![7. ábra][7]

### <a name="step-4"></a>4. lépés

Most, hogy kiszűrte az ablakot, hogy csak a [SYN] bites csomagokat jelenítse meg, egyszerűen kiválaszthatja a kívánt beszélgetéseket, hogy megtekintse a kezdeti RTT. A RTT WireShark való megtekintésének egyszerű módja egyszerűen kattintson a "SEQ/ACK" elemzésre. Ekkor megjelenik a RTT. Ebben az esetben a RTT 0,0022114 másodperc vagy 2,211 MS volt.

![8. ábra][8]

## <a name="unwanted-protocols"></a>Nemkívánatos protokollok

Az Azure-ban üzembe helyezett virtuálisgép-példányon több alkalmazás is fut. Számos ilyen alkalmazás kommunikál a hálózaton keresztül, például explicit engedélye nélkül. A hálózati kommunikációt a csomagok rögzítése használatával vizsgálhatja meg, hogy az alkalmazás hogyan kommunikál a hálózaton, és megkeresi az esetleges problémákat.

Ebben a példában áttekintjük a nem kívánt protokollok korábban futtatott csomagjainak rögzítését, amely arra utalhat, hogy jogosulatlan kommunikációt jelez a gépen futó alkalmazástól.

### <a name="step-1"></a>1. lépés

Ha ugyanazt a rögzítést használja az előző forgatókönyvben, kattintson a **statisztika**  >  **protokoll-hierarchia** elemre.

![protokoll-hierarchia menü][2]

Megjelenik a protokoll-hierarchia ablak. Ez a nézet a rögzítési munkamenet során használt összes protokoll listáját, valamint a protokollok használatával továbbított és fogadott csomagok számát tartalmazza. Ez a nézet hasznos lehet a virtuális gépek vagy hálózatok nemkívánatos hálózati forgalmának megállapításához.

![protokoll-hierarchia megnyitva][3]

Ahogy az alábbi képernyőfelvételen is látható, a BitTorrent protokollt használó forgalom a társ-fájlmegosztás számára használt. Rendszergazdaként nem várható, hogy az adott virtuális gépeken a BitTorrent-forgalom látható. Most, hogy már ismeri ezt a forgalmat, eltávolíthatja a virtuális gépre telepített társközi szoftvert, vagy hálózati biztonsági csoportokkal vagy tűzfallal is blokkolhatja a forgalmat. Emellett dönthet úgy is, hogy a csomagok rögzítését ütemezett módon futtatja, így rendszeresen áttekintheti a protokollok használatát a virtuális gépeken. Példa a hálózati feladatok automatizálására az Azure-ban: [hálózati erőforrások figyelése az Azure Automation](network-watcher-monitor-with-azure-automation.md) használatával

## <a name="finding-top-destinations-and-ports"></a>Legfelső szintű célhelyek és portok keresése

A forgalom, a végpontok és az átadott portok megértése fontos a hálózatban lévő alkalmazások és erőforrások figyelése és hibaelhárítása során. A csomag rögzítési fájlját a fentiek alapján gyorsan megtudhatja, hogy a virtuális gép mely célállomásokkal kommunikál, és milyen portokat használ.

### <a name="step-1"></a>1. lépés

Ugyanazon rögzítés használata az előző forgatókönyvben kattintson a **statisztika**  >  **IPv4 statisztikai**  >  **Célhelyek és portok** elemre.

![csomag rögzítése ablak][4]

### <a name="step-2"></a>2. lépés

Ahogy megvizsgáljuk, hogy az eredmények egy sor kiemelkedik, több kapcsolat van a 111-as porton. A leggyakrabban használt port 3389, amely távoli asztal, a fennmaradó pedig RPC dinamikus portok.

Habár ez a forgalom nem jelent semmit, a sok kapcsolathoz használt port, amely ismeretlen a rendszergazda számára.

![5. ábra][5]

### <a name="step-3"></a>3. lépés

Most, hogy meghatározta a porton kívüli portot, a port alapján szűrheti a rögzítést.

Az ebben a példában szereplő szűrő a következő:

```
tcp.port == 111
```

A szűrő szövegmezőbe írja be a szűrési szöveget, majd nyomja meg az ENTER billentyűt.

![6. ábra][6]

Az eredményekből láthatjuk, hogy az összes forgalom az azonos alhálózaton található helyi virtuális gépről érkezik. Ha továbbra sem értjük a forgalom okait, továbbra is ellenőrizheti a csomagokat, hogy megtudja, miért teszi ezeket a hívásokat a 111-as porton. Ezekkel az adatokkal elvégezheti a megfelelő műveletet.

## <a name="next-steps"></a>Következő lépések

További információ a Network Watcher egyéb diagnosztikai funkcióiról: az [Azure hálózati monitorozásának áttekintése](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













