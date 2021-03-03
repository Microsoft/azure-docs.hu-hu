---
title: A Cloud Service (klasszikus) foglalási hibáinak elhárítása | Microsoft Docs
description: Lefoglalási hiba hibaelhárítása Az Azure Cloud Services telepítésekor. Megtudhatja, hogyan működik a foglalás, és miért sikertelen a kiosztás.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 95fe4a8e1f6c6ee5f519311f8e756be89a09acf8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738310"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>Foglalási hibák elhárítása az Azure-beli Cloud Services (klasszikus) telepítésekor

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.


## <a name="summary"></a>Összefoglalás

Ha példányokat helyez üzembe egy felhőalapú szolgáltatásban, vagy új webes vagy feldolgozói szerepkört ad hozzá, a Microsoft Azure kiosztja a számítási erőforrásokat. Időnként előfordulhat, hogy az Azure-előfizetések korlátainak megkezdése előtt ezeket a műveleteket is elvégezheti. Ez a cikk ismerteti a gyakori lefoglalási hibák okait, és javaslatot tesz a lehetséges szervizelésre. Az információk akkor is hasznosak lehetnek, ha a szolgáltatások üzembe helyezését tervezi.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Háttér – a foglalás működése

Az Azure-adatközpontokban lévő kiszolgálók fürtökre vannak particionálva. Egy új felhőalapú szolgáltatás foglalási kérelme több fürtön is próbálkozik. Ha az első példány üzembe helyezése egy felhőalapú szolgáltatásban (átmeneti vagy éles üzemben) történik, a felhőalapú szolgáltatás egy fürthöz lesz rögzítve. A felhőalapú szolgáltatás további telepítései ugyanazon a fürtön történnek. Ebben a cikkben a következőképpen fogunk hivatkozni: "fürtbe rögzítve". Az alábbi ábra egy normál kiosztást mutat be, amely több fürtön próbálkozik. A 2. diagram azt szemlélteti, hogy a 2. fürthöz rögzített foglalások esetében hol található a meglévő Cloud Service CS_1.

![Foglalási diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Miért történik a foglalási hiba

Ha egy foglalási kérelem egy fürtre van rögzítve, nagyobb eséllyel nem talál szabad erőforrásokat, mivel a rendelkezésre álló erőforráskészlet egy fürtre korlátozódik. Továbbá, ha a foglalási kérelem egy fürthöz van rögzítve, de a kért erőforrás típusa nem támogatott az adott fürtben, a kérés sikertelen lesz, még akkor is, ha a fürt rendelkezik szabad erőforrással. Az alábbi ábra szemlélteti azt az esetet, amikor egy rögzített foglalás meghiúsul, mert az egyetlen jelölt fürtnek nincs szabad erőforrása. A 4. ábra azt szemlélteti, hogy a rögzített foglalás meghiúsul, mert az egyetlen jelölt fürt nem támogatja a kért virtuálisgép-méretet, annak ellenére, hogy a fürt rendelkezik szabad erőforrásokkal.

![Rögzített foglalási hiba](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>A Cloud Services foglalási hibáinak elhárítása

### <a name="error-message"></a>Hibaüzenet

A Azure Portalban navigáljon a Cloud Service-hez, és az oldalsávon válassza ki a *műveleti naplókat (klasszikus)* a naplók megtekintéséhez.

Tekintse meg az alábbi kivételek további megoldásait:

|Kivétel típusa  |Hibaüzenet  |Megoldás  |
|---------|---------|---------|
|FabricInternalServerError     |A művelet nem sikerült, hibakód: "InternalError" és errorMessage "a kiszolgáló belső hibát észlelt. Próbálkozzon újra a kéréssel. "|[A FabricInternalServerError hibáinak megoldása](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |A művelet nem sikerült, hibakód: "InternalError" és errorMessage "a kiszolgáló belső hibát észlelt. Próbálkozzon újra a kéréssel. "|[A ServiceAllocationFailure hibáinak megoldása](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocationNotFoundForRoleSize     |A (z `{Operation ID}` ) "" művelet sikertelen volt: a kért virtuálisgép-platform jelenleg nem érhető el a (z) régióban () ehhez az `{Region ID}` előfizetéshez. Próbálkozzon másik szinten, vagy helyezzen üzembe egy másik helyet.|[A LocationNotFoundForRoleSize hibáinak megoldása](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrainedAllocationFailed     |A (z) "" Azure-művelet `{Operation ID}` végrehajtása sikertelen volt, kód: számítás. ConstrainedAllocationFailed. Részletek: a foglalás nem sikerült; nem lehet kielégíteni a megkötéseket a kérelemben. A kért új szolgáltatás üzembe helyezése egy affinitáscsoporthoz van kötve, egy virtuális hálózatot céloz, vagy már van egy üzemelő példány az üzemeltetett szolgáltatásban. Ezen feltételek bármelyike korlátozza az új üzembe helyezést bizonyos Azure-erőforrásokra. Próbálkozzon újra később, vagy próbálja meg csökkenteni a virtuális gép méretét vagy a szerepkörpéldányok számát. Ha lehetséges, alternatív lehetőségként távolítsa el a fentebb említett korlátozásokat, vagy végezze el az üzembe helyezést egy másik régión.|[A ConstrainedAllocationFailed hibáinak megoldása](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverconstrainedAllocationRequest     |A központi telepítéshez szükséges virtuálisgép-méret (vagy virtuálisgép-méretek kombinációja) nem építhető ki a telepítési kérelmek korlátozásai miatt. Ha lehetséges, próbálkozzon olyan pihentető korlátozásokkal, mint például a virtuális hálózati kötések, üzembe helyezés egy üzemeltetett szolgáltatásban, amely nem rendelkezik más központi telepítéssel, és egy másik affinitási csoportba, vagy nem rendelkezik affinitási csoporttal, vagy próbálja meg telepíteni egy másik régiót.|[A OverconstrainedAllocationRequest hibáinak megoldása](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

Példa hibaüzenetre:

> "A (z) {Operation ID} Azure-művelet meghiúsult a Code számítás. ConstrainedAllocationFailed. Részletek: a foglalás nem sikerült; nem lehet kielégíteni a megkötéseket a kérelemben. A kért új szolgáltatás üzembe helyezése egy affinitáscsoporthoz van kötve, egy virtuális hálózatot céloz, vagy már van egy üzemelő példány az üzemeltetett szolgáltatásban. Ezen feltételek bármelyike korlátozza az új üzembe helyezést bizonyos Azure-erőforrásokra. Próbálkozzon újra később, vagy próbálja meg csökkenteni a virtuális gép méretét vagy a szerepkörpéldányok számát. Ha lehetséges, távolítsa el a fenti korlátozásokat, vagy próbálja meg egy másik régióba telepíteni. "

### <a name="common-issues"></a>Gyakori problémák

Az alábbi gyakori foglalási forgatókönyvek azt okozzák, hogy a foglalási kérelmek egyetlen fürthöz legyenek rögzítve.

* Üzembe helyezés átmeneti tárolóhelyre – ha egy felhőalapú szolgáltatásban van üzembe helyezés bármelyik tárolóhelyen, akkor a teljes felhőalapú szolgáltatás egy adott fürthöz van rögzítve.  Ez azt jelenti, hogy ha egy üzemelő példány már létezik a termelési helyen, akkor egy új átmeneti üzemelő példányt csak ugyanabban a fürtben lehet lefoglalni, ahol a termelési hely is található. Ha a fürt eléri a kapacitást, előfordulhat, hogy a kérelem meghiúsul.
* Méretezés – Új példányok egy meglévő felhőszolgáltatáshoz való hozzáadása esetén az új példányokat ugyanabban a fürtben kell lefoglalni.  A kisebb méretezési kérések általában lefoglalhatóak, de nem mindig. Ha a fürt eléri a kapacitást, előfordulhat, hogy a kérelem meghiúsul.
* Affinitási csoport – az üres felhőalapú szolgáltatás új üzembe helyezését a háló bármely, a régióban található fürtben kioszthatja, kivéve, ha a felhőalapú szolgáltatás egy affinitási csoportba van rögzítve. Ugyanazon a fürtön ugyanazon az affinitási csoporton üzemelő példányok fognak próbálkozni. Ha a fürt eléri a kapacitást, előfordulhat, hogy a kérelem meghiúsul.
* Az affinitási csoport vNet – a régebbi virtuális hálózatok a régiók helyett affinitási csoportokhoz voltak kötve, és ezekben a virtuális hálózatokban található felhőalapú szolgáltatások az affinitási csoport fürtjébe lesznek rögzítve. Az ilyen típusú virtuális hálózatra irányuló központi telepítések a rögzített fürtön lesznek megkísérelve. Ha a fürt eléri a kapacitást, előfordulhat, hogy a kérelem meghiúsul.

## <a name="solutions"></a>Megoldások

1. Új felhőalapú szolgáltatás újbóli üzembe helyezése – ez a megoldás valószínűleg a legsikeresebb, mivel lehetővé teszi a platform számára, hogy az adott régióban lévő összes fürtből kiválassza a platformot.

   * A számítási feladatok üzembe helyezése egy új felhőalapú szolgáltatásban  
   * Frissítse a CNAME-t vagy egy rekordot az új felhőalapú szolgáltatás felé irányuló forgalom kimutatása érdekében
   * Ha nulla forgalom lesz a régi helyre, törölheti a régi felhőalapú szolgáltatást. Ennek a megoldásnak nulla állásidőt kell fizetnie.
2. Az éles és az előkészítési tárolóhelyek törlése – ez a megoldás megőrzi a meglévő DNS-nevet, de az alkalmazáshoz való állásidőt is okozhatja.

   * Törölheti egy meglévő felhőalapú szolgáltatás éles és átmeneti tárolóhelyeit, hogy a felhőalapú szolgáltatás üres legyen, majd
   * Hozzon létre egy új központi telepítést a meglévő felhőalapú szolgáltatásban. Ez a művelet újrapróbálkozik a régió összes fürtjén való kiosztással. Győződjön meg arról, hogy a felhőalapú szolgáltatás nem kötődik affinitási csoporthoz.
3. Fenntartott IP – ez a megoldás megőrzi a meglévő IP-címét, de az alkalmazás leállását okozhatja.  

   * Foglalt IP létrehozása meglévő telepítéshez a PowerShell használatával

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * Kövesse #2 a fentiekben leírtak szerint, és győződjön meg arról, hogy az új foglalt IP meg van határozva a szolgáltatás CSCFG.
4. Az új központi telepítések affinitási csoportjának eltávolítása – az affinitási csoportok már nem ajánlottak. Új felhőszolgáltatás üzembe helyezéséhez kövesse a fenti 1-es pont lépéseit. Győződjön meg arról, hogy a felhőalapú szolgáltatás nem egy affinitási csoportban van.
5. Átalakítás regionális Virtual Networkra – lásd: az [áttelepítést az affinitási csoportokból egy regionális Virtual Networkra (VNet)](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet).
