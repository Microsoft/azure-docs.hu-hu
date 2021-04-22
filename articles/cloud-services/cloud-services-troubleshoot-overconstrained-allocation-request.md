---
title: Az OverconstrainedAllocationRequest hibaelhárítása felhőszolgáltatás (klasszikus) Azure-beli virtuális | Microsoft Docs
description: Ez a cikk bemutatja, hogyan oldható meg egy OverconstrainedAllocationRequest kivétel egy (klasszikus) felhőszolgáltatás Azure-ban való üzembe helyezésekor.
services: cloud-services
documentationcenter: ''
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1a5880107aaa414da42fe5e36e0cb3315071d8a0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877425"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>Az OverconstrainedAllocationRequest hibaelhárítása felhőszolgáltatások (klasszikus) Azure-ban való üzembe helyezésekor

Ebben a cikkben a korlátozott foglalási hibák elhárítását fogja elhárítani, amelyek megakadályozzák a (klasszikus) Azure Cloud Services telepítését.

Amikor példányokat helyez üzembe egy Cloud Service-ben, vagy új webes vagy feldolgozói szerepkörpéldányokat ad hozzá, a Microsoft Azure számítási erőforrásokat foglal le.

Előfordulhat, hogy hibaüzenet jelenik meg ezen műveletek során, még az Azure-előfizetés korlátjának elérését megelőzően is.

> [!TIP]
> Ezek az információk a szolgáltatások üzembe helyezésének megtervekor is hasznosak lehetnek.

## <a name="symptom"></a>Hibajelenség

![A képen a Műveleti napló (klasszikus) panel látható.](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Kivétel típusa  |Hibaüzenet  |
|---------|---------|
|OverconstrainedAllocationRequest |Az üzembe helyezéshez szükséges virtuálisgép-méret (vagy a virtuálisgép-méretek kombinációja) üzembe helyezési kérelemkorlátozások miatt nem építhető ki. Ha lehetséges, próbálkozzon az olyan korlátozások korlátozásával, mint a virtuális hálózati kötések, az üzembe helyezés egy üzemeltetett szolgáltatásban, más üzembe helyezés nélkül, egy másik affinitáscsoportban vagy affinitáscsoport nélkül, vagy próbálkozzon az üzembe helyezéssel egy másik régióban.|

## <a name="cause"></a>Ok

A kiváltó ok eltérő lehet, ha a **felhőszolgáltatás** rögzített vagy **nem rögzített.**

- [**Nincs rögzített:** Új felhőszolgáltatás első üzembe helyezésének (klasszikus) hibája](#not-pinned-to-a-cluster)
- [**Rögzített:** Hibák egy meglévő felhőszolgáltatásból (klasszikus)](#pinned-to-a-cluster)

> [!NOTE]
> Amikor az első példányt üzembe helyez egy felhőszolgáltatásban (akár előkészítési, akár éles környezetben), a felhőszolgáltatás egy fürthöz lesz tűzve.
>
> Idővel a fürt erőforrásai teljes mértékben ki vannak használva. Ha egy felhőszolgáltatás (klasszikus) további erőforrásokra vonatkozó foglalási kérést tesz, ha nem áll rendelkezésre elegendő erőforrás a rögzített fürtben, a kérés foglalási [hibát fog eredményezni.](cloud-services-allocation-failures.md)

## <a name="solution"></a>Megoldás

Kövesse az alábbi forgatókönyvekben a foglalási hibákra vonatkozó útmutatást.

### <a name="not-pinned-to-a-cluster"></a>Nincs kitűzve egy fürtre

Amikor első alkalommal helyez üzembe egy felhőszolgáltatást (klasszikus), a fürt még nincs kiválasztva, így a felhőszolgáltatás nincs *kitűzve.* Előfordulhat, hogy az Azure üzembe helyezési hibát ad vissza, mert:

- Olyan méretet választott, amely nem érhető el a régióban.
- A különböző szerepkörökhöz szükséges méretek kombinációja nem érhető el a régióban.

Ha foglalási hibát tapasztal ebben a forgatókönyvben, az ajánlott megoldás a régióban elérhető méretek ellenőrzése és a korábban megadott méret módosítása.

1. A régiónként elérhető méreteket a Felhőszolgáltatás [(klasszikus)](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) termékek oldalán ellenőrizheti.

    > [!NOTE]
    > A *Termékek* lapon nem érhetők el a rendelkezésre álló kapacitások. Minden új foglalás esetén az Azure-nak képesnek kell lennie kiválasztani az optimális fürtöt az Ön régiójában az adott időpontban.

1. Frissítse a felhőszolgáltatás (klasszikus) szolgáltatásdefiníciós [](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) fájlját, hogy a régiótól eltérő termékméretet adjon meg.

### <a name="pinned-to-a-cluster"></a>Fürthöz rögzített

A meglévő felhőszolgáltatások *egy fürthöz* vannak tűzve. A felhőszolgáltatás (klasszikus) további üzembe helyezései ugyanabban a fürtben történnek.

Ha foglalási hibát tapasztal ebben a forgatókönyvben, az ajánlott megoldás az új felhőszolgáltatásba (klasszikus) történő ismételt üzembe kiosztás (és a *CNAME frissítése).*

> [!TIP]
> Valószínűleg ez a megoldás a legeredményesebb, mivel lehetővé teszi a platform számára, hogy az adott régióban lévő összes fürt közül válasszon.

> [!NOTE]
> Ennek a megoldásnak nulla állásidővel kell csökkennie.

1. Üzembe helyezheti a számítási feladatot egy új felhőszolgáltatásban (klasszikus).
    - További útmutatásért tekintse meg [a Felhőszolgáltatás (klasszikus)](cloud-services-how-to-create-deploy-portal.md) létrehozása és üzembe helyezése útmutatót.

    > [!WARNING]
    > Ha nem szeretné elveszteni az üzembe helyezési helyhez társított IP-címet, használhatja a 3. megoldást – Tartsa meg az [IP-címet.](cloud-services-allocation-failures.md#solutions)

1. Frissítse a *CNAME vagy* *az A* rekordot úgy, hogy a forgalmat az új felhőszolgáltatásra (klasszikus) mutasson.
    - További útmutatásért tekintse meg az Egyéni [tartománynév konfigurálása azure-felhőszolgáltatáshoz (klasszikus)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) útmutatót.

1. Ha nincs forgalom a régi webhelyre, törölheti a régi felhőszolgáltatást (klasszikus).
    - További útmutatásért tekintse meg [az üzemelő példányok törlését és a felhőszolgáltatás (klasszikus)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) útmutatóját.
    - A (klasszikus) felhőszolgáltatás hálózati forgalmának ellenőrzésével lásd: [Introduction to Cloud service (classic) monitoring (A felhőszolgáltatás (klasszikus) monitorozásának bemutatása).](cloud-services-how-to-monitor.md)

Lásd: [Felhőszolgáltatás (klasszikus) foglalási hibáinak elhárítása | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) a további javítási lépésekért.

## <a name="next-steps"></a>Következő lépések

További foglalási hibamegoldások és háttérinformációk:

> [!div class="nextstepaction"]
> [Foglalási hibák – Felhőszolgáltatás (klasszikus)](cloud-services-allocation-failures.md)

Ha az Azure-ral kapcsolatos problémája nem foglalkozik ebben a cikkben, látogasson el az AZURE fórumaira az [MSDN-ről,](https://azure.microsoft.com/support/forums/)és Stack Overflow. A problémát közzétenheti ezeken a fórumokon, vagy közzétenheti a [ @AzureSupport Twitteren.](https://twitter.com/AzureSupport) Egy kérést is Azure-támogatás küldhet. Támogatási kérés elküldését a támogatási [Azure-támogatás](https://azure.microsoft.com/support/options/) oldalon válassza a *Támogatás kérése lehetőséget.*
