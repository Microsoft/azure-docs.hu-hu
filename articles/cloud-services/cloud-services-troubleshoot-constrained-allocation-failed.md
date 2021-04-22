---
title: A ConstrainedAllocationFailed hibaelhárítása felhőszolgáltatás (klasszikus) Azure-beli | Microsoft Docs
description: Ez a cikk bemutatja, hogyan oldható fel a ConstrainedAllocationFailed kivétel egy (klasszikus) felhőszolgáltatás Azure-ban való üzembe helyezésekor.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 4a92246f81ddd10840bb0dcf7edf2b01853fd148
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876597"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>A ConstrainedAllocationFailed hibaelhárítása felhőszolgáltatás (klasszikus) Azure-ban való üzembe helyezésekor

Ebben a cikkben olyan foglalási hibákat fog elhárítani, amelyek miatt az Azure Cloud Services (klasszikus) foglalási korlátozások miatt nem helyezhető üzembe.

Amikor példányokat helyez üzembe egy felhőszolgáltatásban (klasszikus), vagy új webes vagy feldolgozói szerepkörpéldányokat ad hozzá, a Microsoft Azure erőforrásokat foglal le.

Előfordulhat, hogy hibaüzenet jelenik meg ezen műveletek során, még az Azure-előfizetés korlátjának elérését megelőzően is.

> [!TIP]
> Ezek az információk a szolgáltatások üzembe helyezésének megtervekor is hasznosak lehetnek.

## <a name="symptom"></a>Hibajelenség

A Azure Portal nyissa meg a Felhőszolgáltatást (klasszikus), majd az oldalsávon válassza a Műveleti napló *(klasszikus)* lehetőséget a naplók megtekintéséhez.

![A képen a Műveleti napló (klasszikus) panel látható.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

A (klasszikus) felhőszolgáltatás naplóinak vizsgálatakor a következő kivételt fogja látni:

|Kivétel típusa  |Hibaüzenet  |
|---------|---------|
|ConstrainedAllocationFailed     |A következő `{Operation ID}` Azure-művelet meghiúsult: Compute.ConstrainedAllocationFailed. Részletek: A foglalás sikertelen volt; nem lehet kielégíteni a kérések megkötéseinek. A kért új szolgáltatás üzembe helyezése egy affinitáscsoporthoz van kötve, egy virtuális hálózatot céloz, vagy már van egy üzemelő példány az üzemeltetett szolgáltatásban. Ezen feltételek bármelyike adott Azure-erőforrásokra korlátozza az új üzembe helyezést. Próbálkozzon újra később, vagy próbálja meg csökkenteni a virtuális gép méretét vagy a szerepkörpéldányok számát. Ha lehetséges, alternatív lehetőségként távolítsa el a fentebb említett korlátozásokat, vagy végezze el az üzembe helyezést egy másik régión.|

## <a name="cause"></a>Ok

Amikor az első példányt üzembe helyez egy felhőszolgáltatásban (akár előkészítési, akár éles környezetben), a felhőszolgáltatás egy fürthöz lesz tűzve.

Idővel a fürt erőforrásai teljes mértékben ki vannak használva. Ha egy felhőszolgáltatás (klasszikus) foglalási kérést tesz több erőforrásra, ha nem áll rendelkezésre elegendő erőforrás a rögzített fürtben, a kérés foglalási hibát eredményez. További információkért tekintse meg a foglalási [hibák gyakori problémáit.](cloud-services-allocation-failures.md#common-issues)

## <a name="solution"></a>Megoldás

A meglévő felhőszolgáltatások *egy fürthöz* vannak tűzve. A felhőszolgáltatás (klasszikus) további üzembe helyezései ugyanabban a fürtben történnek.

Ha foglalási hibát tapasztal ebben a forgatókönyvben, az ajánlott megoldás az új (klasszikus) felhőszolgáltatásba való ismételt üzembe ásás (és a *CNAME frissítése).*

> [!TIP]
> Valószínűleg ez a megoldás a legeredményesebb, mivel lehetővé teszi a platform számára, hogy az adott régióban lévő összes fürt közül válasszon.

> [!NOTE]
> Ennek a megoldásnak nulla állásidővel kell lennie.

1. Üzembe helyezheti a számítási feladatot egy új (klasszikus) felhőszolgáltatásban.
    - További útmutatásért tekintse meg a Felhőszolgáltatás [(klasszikus)](cloud-services-how-to-create-deploy-portal.md) létrehozása és üzembe helyezése útmutatót.

    > [!WARNING]
    > Ha nem szeretné elveszteni az üzembe helyezési helyhez társított IP-címet, használhatja a 3. megoldást – [Tartsa meg az IP-címet.](cloud-services-allocation-failures.md#solutions)

1. Frissítse a *CNAME vagy* *az A* rekordot, hogy a forgalom az új felhőszolgáltatásra (klasszikus) mutasson.
    - További útmutatásért tekintse meg az Egyéni tartománynév konfigurálása [azure-felhőszolgáltatáshoz (klasszikus)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) útmutatót.

1. Ha nincs forgalom a régi webhelyre, törölheti a régi felhőszolgáltatást (klasszikus).
    - További útmutatásért tekintse meg [az Üzemelő példányok törlése és a felhőszolgáltatás (klasszikus)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) útmutatóját.
    - A (klasszikus) felhőszolgáltatás hálózati forgalmának ellenőrzésével lásd: Bevezetés a felhőszolgáltatás [(klasszikus) monitorozási szolgáltatásba.](cloud-services-how-to-monitor.md)

Lásd: [Felhőszolgáltatás (klasszikus) foglalási hibáinak elhárítása | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) további javítási lépésekért.

## <a name="next-steps"></a>Következő lépések

További foglalási hibamegoldások és háttérinformációk:

> [!div class="nextstepaction"]
> [Foglalási hibák – Felhőszolgáltatás (klasszikus)](cloud-services-allocation-failures.md)

Ha az Azure-ral kapcsolatos problémája nem foglalkozik ebben a cikkben, látogasson el az AZURE fórumaira az [MSDN-ről,](https://azure.microsoft.com/support/forums/)és Stack Overflow. A problémát közzétenheti ezeken a fórumokon, vagy közzétenheti a [ @AzureSupport Twitteren.](https://twitter.com/AzureSupport) Egy kérést is Azure-támogatás küldhet. Támogatási kérés elküldését a támogatási [Azure-támogatás](https://azure.microsoft.com/support/options/) oldalon válassza a *Támogatás kérése lehetőséget.*