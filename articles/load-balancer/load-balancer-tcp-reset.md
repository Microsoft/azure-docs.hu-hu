---
title: Load Balancer TCP-visszaállítás és Üresjárati időkorlát az Azure-ban
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan Azure Load Balancer a kétirányú TCP első csomagok üresjárati időkorláton.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 0d02b46345af13770f77a7dac452127a665e01fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696744"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer TCP alaphelyzetbe állítása és üresjárati időkorlátja

A [standard Load Balancer](./load-balancer-overview.md) használatával kiszámítható alkalmazás-viselkedést hozhat létre a forgatókönyvek esetében, ha engedélyezi a TCP alaphelyzetbe állítását egy adott szabály esetében. Load Balancer alapértelmezett viselkedése a folyamatok csendes eldobása, amikor a folyamat üresjárati időkorlátja eléri a folyamatot.  Ha engedélyezi ezt a funkciót, a Load Balancer a kétirányú TCP-alaphelyzetbe (TCP első csomag) küldi az üresjárati időkorlátot.  Ez tájékoztatni fogja az alkalmazás-végpontokat arról, hogy a kapcsolatok túllépték az időkorlátot, és már nem használható.  Ha szükséges, a végpontok azonnal létrehozhatnak egy új kapcsolatot.

![Load Balancer TCP alaphelyzetbe állítása](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>TCP alaphelyzetbe állítása

Ezt az alapértelmezett viselkedést kell módosítania, és engedélyezni kell a TCP alaphelyzetbe állítását a bejövő NAT-szabályok, a terheléselosztási szabályok és a [Kimenő szabályok](./load-balancer-outbound-connections.md#outboundrules)üresjárati időkorlátján.  Ha engedélyezve van a szabály, a Load Balancer a kétirányú TCP-visszaállítást (TCP első csomagokat) küldi az ügyfél-és a kiszolgálói végpontoknak az összes egyező folyamat üresjárati időkorlátjának időpontjában.

Az első TCP-csomagokat fogadó végpontok azonnal lezárták a megfelelő szoftvercsatornát. Ez azonnali értesítést küld a végpontokról, amelyeken a kapcsolat megjelent, és az azonos TCP-kapcsolaton folytatott jövőbeli kommunikáció meghiúsul.  Az alkalmazások kitörölhetik a kapcsolatokat, amikor a szoftvercsatorna lezárult, és szükség esetén újra létrehozza a kapcsolatokat, anélkül, hogy a TCP-kapcsolatra kellene várnia.

Számos esetben előfordulhat, hogy a TCP (vagy az alkalmazási réteg) Keepalives egy folyamat üresjárati időkorlátjának frissítéséhez szükséges. 

Ha az üresjárati időtartama meghaladja a konfiguráció által engedélyezett beállításokat, vagy az alkalmazás nem kívánt viselkedést jelenít meg a TCP-visszaállítások engedélyezésével, akkor továbbra is a TCP-Keepalives (vagy az alkalmazás rétegének Keepalives) kell használnia a TCP-kapcsolatok élő állapotának figyeléséhez.  Emellett a Keepalives is hasznos lehet, ha a Kapcsolódás az elérési útban van, különösen az alkalmazás rétegbeli Keepalives.  

Alaposan vizsgálja meg a teljes végpontok közötti forgatókönyvet, és döntse el, hogy kihasználja-e a TCP alaphelyzetbe állítását, az üresjárat időkorlátjának módosítását, és ha további lépések szükségesek a kívánt alkalmazás működésének biztosításához.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurálható TCP Üresjárati időkorlát

A Azure Load Balancer a következő Üresjárati időkorlát-tartománnyal rendelkezik:
-  4 perc – 100 perc a kimenő szabályoknál
-  4 perc és 30 perc a Load Balancer szabályok és a bejövő NAT-szabályok esetében

Alapértelmezés szerint 4 percre van beállítva. Ha egy inaktivitási időtartam hosszabb az időtúllépési értéknél, nem garantálható, hogy a TCP-vagy HTTP-munkamenet az ügyfél és a felhőalapú szolgáltatás között marad.

Ha a csatlakozás be van zárva, az ügyfélalkalmazás a következő hibaüzenetet kaphatja: "az alapul szolgáló csatlakozás bezárult: a kiszolgáló lezárta az életben tartani várt kapcsolatokat."

Gyakori eljárás a TCP Keep-Alive használata. Ez a gyakorlat hosszabb ideig tart a kapcsolatok aktív állapotban. További információkért tekintse meg ezeket a [.net-példákat](/dotnet/api/system.net.servicepoint.settcpkeepalive). Ha a Keep-Alive engedélyezve van, a rendszer a csatlakozáskor inaktivitási időszakokban küld csomagokat. Életben tartási csomagok gondoskodnak arról, hogy az Üresjárati időkorlát értéke ne legyen elérhető, és a kapcsolat hosszabb ideig marad.

A beállítás csak a bejövő kapcsolatok esetében működik. A kapcsolat elvesztésének elkerüléséhez konfigurálja a TCP Keep-Alive értéket az Üresjárati időkorlát beállításnál kisebb intervallumra, vagy növelje az Üresjárati időkorlát értékét. Ezen forgatókönyvek támogatásához konfigurálható Üresjárati időkorlát támogatása lett hozzáadva.

A TCP Keep-Alive olyan forgatókönyvek esetén működik, ahol az akkumulátorok élettartama nem korlátozás. A mobil alkalmazások esetében nem ajánlott. Ha egy TCP Keep-Alive protokollt használ a mobil alkalmazásban, az eszköz akkumulátora gyorsabban kiüríthető.


## <a name="limitations"></a>Korlátozások

- A TCP-visszaállítás csak a TCP-kapcsolatok során lett elküldve a létesített állapotban.
- A TCP alaphelyzetbe állítása nem lesz elküldése a belső terheléselosztó esetében, HA konfigurálva van a HA port.
- A TCP Üresjárati időkorlát nem befolyásolja az UDP protokoll terheléselosztási szabályait.

## <a name="next-steps"></a>Következő lépések

- A [standard Load Balancer](./load-balancer-overview.md)megismerése.
- További információ a [kimenő szabályokról](./load-balancer-outbound-connections.md#outboundrules).
- [A TCP első beállítása üresjárati időkorláton](load-balancer-tcp-idle-timeout.md)