---
title: 'Távoli munka: hálózati virtuális berendezés (NVA) – megfontolások távoli munkához | Azure-VPN Gateway'
description: Ez a cikk segít megismerni azokat a dolgokat, amelyeket figyelembe kell vennie az Azure-beli hálózati virtuális berendezések (NVA-EK) használatáról az COVID-19 világjárvány során.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 70b5732e1293e35127a19fbe736d8562056a870b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499679"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Távoli munka: hálózati virtuális berendezés (NVA) – megfontolások távoli munkához

>[!NOTE]
>Ez a cikk bemutatja, hogyan használhatja a hálózati virtuális berendezéseket, az Azure-t, a Microsoft-hálózatot és az Azure partner-ökoszisztémát, hogy távolról működjön, és enyhítse a COVID-19-válság miatt megküzdő hálózati problémákat.
>

Egyes Azure-ügyfelek harmadik féltől származó hálózati virtuális berendezéseket (NVA-ket) használnak az Azure piactéren, hogy olyan kritikus fontosságú szolgáltatásokat nyújtsanak, mint például a pont – hely VPN a COVID-19 járvány során otthonról dolgozó alkalmazottak számára. Ez a cikk a távelérési megoldások biztosítására szolgáló NVA az Azure-ban való kihasználása során megfontolandó, magas szintű útmutatást ismerteti.

## <a name="nva-performance-considerations"></a>A NVA teljesítményével kapcsolatos megfontolások

Az Azure Marketplace-en az összes jelentős NVA-gyártónak ajánlásokkal kell rendelkeznie a virtuális gépek méretével és a megoldások üzembe helyezéséhez használt példányok számával kapcsolatban.  A csaknem minden NVA-szállító lehetővé teszi az adott régióban elérhetővé tett méretek kiválasztását, nagyon fontos, hogy kövesse az Azure-beli virtuálisgép-példányok méretére vonatkozó javaslatait, mivel ezek a virtuálisgép-méretek a gyártó által az Azure-ban végzett teljesítmény-tesztelési műveletekkel.  

### <a name="consider-the-following"></a>Vegye figyelembe a következőket

- **Egyidejű felhasználók kapacitása és száma** – ez a szám különösen fontos a pont – hely típusú VPN-felhasználók számára, mivel minden csatlakoztatott felhasználó létrehoz egy titkosított (IPSec-vagy SSL VPN-) alagutat.  
- **Összesített átviteli sebesség** – mi az összesített sávszélesség, amelyeknek meg kell adnia a szükséges felhasználók számát, amelyhez távelérést kell biztosítania.
- **A virtuálisgép-méretre szüksége lesz** – mindig a NVA-gyártó által ajánlott virtuálisgép-méreteket kell használnia.  Pont – hely típusú VPN esetén, ha sok egyidejű felhasználói kapcsolattal rendelkezik, nagyobb méretű virtuálisgép-méreteket kell használnia, például a [Dv2 és a DSv2 sorozatú](../virtual-machines/dv2-dsv2-series.md "Dv2 és Dsv2 sorozat") virtuális gépeket. Ezek a virtuális gépek általában több vCPU rendelkeznek, és több párhuzamos VPN-munkamenetet kezelhetnek.  Amellett, hogy több virtuális mag van, az Azure-ban nagyobb méretű virtuálisgép-méretek több összesített sávszélesség-kapacitással rendelkeznek, mint a kisebb méretű virtuális gépek mérete.
    > **Fontos:** Minden szállító különböző erőforrásokat használ.  Ha nem törli, hogy a becsült felhasználói terhelésnek milyen méretűnek kell lennie, vegye fel a kapcsolatot közvetlenül a szoftver gyártójával, és kérje meg a javaslatot.
- **Példányok száma** – ha várhatóan nagy számú felhasználó és kapcsolat van, akkor a NVA-példányok méretének növelését is elérheti.  Vegye fontolóra több virtuálisgép-példány üzembe helyezését.
- **IPSec VPN és SSL VPN** – az általános IPSec VPN-implementációk jobban teljesítik az SSL VPN-implementációkat.  
- **Licencelés** – győződjön meg arról, hogy a NVA-megoldáshoz megvásárolt szoftverlicenc-szerződés a COVID-19 járvány hirtelen növekedésével fog rendelkezni.  Számos NVA-licencelési program korlátozza a megoldás által képes kapcsolatok vagy sávszélesség mennyiségét.
- **Gyorsított hálózatkezelés** – vegye fontolóra egy olyan NVA-megoldást, amely támogatja a gyorsított hálózatkezelést.  A Gyorsított hálózatkezelés engedélyezi az egygyökerű I/O-virtualizálást (SR-IOV) a virtuális gépekre, nagy mértékben javítva a hálózati teljesítményt. Ez a nagy teljesítményű útvonal megkerüli a gazdagépet az adatelérési útról, csökkenti a késést, a vibrálás és a CPU-kihasználtságot a támogatott virtuálisgép-típusok legnagyobb igényű hálózati munkaterhelésével. A gyorsított hálózatkezelést a legtöbb általános célú és a számítási optimalizált példány mérete támogatja két vagy több vCPU.

## <a name="monitoring-resources"></a>Erőforrások figyelése

Minden NVA-megoldás saját eszközökkel és erőforrásokkal rendelkezik a NVA teljesítményének figyeléséhez.  A gyártók dokumentációjában megtudhatja, hogy megértette-e a teljesítményre vonatkozó korlátozásokat, és felismeri, ha a NVA közel van vagy eléri a kapacitást.  Ezen kívül megtekintheti Azure Monitor hálózati információkat, és megtekintheti a hálózati virtuális berendezésekkel kapcsolatos alapvető teljesítménnyel kapcsolatos információkat, például a következőket:

- Processzor kihasználtsága
- Bejövő hálózat
- Kimenő hálózat
- Bejövő forgalomfolyamok
- Kimenő folyamatok

## <a name="next-steps"></a>Következő lépések

A legtöbb jelentős NVA-partner a COVID-19 során hirtelen, váratlan növekedéssel kapcsolatban tett útmutatást a skálázáshoz. Íme néhány hasznos hivatkozás a partneri erőforrásokhoz.

[A Barracuda lehetővé teszi a munkavégzést otthonról, miközben az adatok védelmét COVID-19 alatt](https://www.barracuda.com/covid-19/work-from-home "Otthoni munka engedélyezése az adatok COVID-19-ben való biztonságossá tétele közben")

[A biztonságos távoli munkaerő engedélyezése a Coronavirus során](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Biztonságos távoli munkaerő a Coronavirus során")

[A Cisco AnyConnect implementációja és teljesítménye/méretezési referenciája a COVID-19 előkészítéshez](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "A Cisco AnyConnect implementációja és teljesítménye/méretezési referenciája a COVID-19 előkészítéshez")

[Citrix COVID-19 Response támogatási központ](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 Response támogatási központ")

[F5-útmutató a távoli feldolgozók drámai növekedésének megoldásához](https://www.f5.com/business-continuity "F5-útmutató a távoli feldolgozók drámai növekedésének megoldásához")

[Fortinet COVID-19 frissítés ügyfelek és partnerek számára](https://www.fortinet.com/covid-19.html "COVID – 19 frissítés ügyfelek és partnerek számára")

[Palo Alto Networks COVID-19 Response Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 Response Center")

[A Kemp lehetővé teszi a távoli munkahelyi és Always-On az üzletmenet folytonosságát](https://kemptechnologies.com/remote-work-always-on-application-experience-business-continuity/ "A Kemp lehetővé teszi a távoli munkahelyi és Always-On az üzletmenet folytonosságát")

