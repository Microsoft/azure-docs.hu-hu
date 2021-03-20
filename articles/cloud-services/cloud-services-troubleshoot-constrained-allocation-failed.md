---
title: A ConstrainedAllocationFailed hibáinak megoldása a Cloud Service (klasszikus) Azure-ba történő telepítésekor | Microsoft Docs
description: Ez a cikk bemutatja, hogyan oldható fel a ConstrainedAllocationFailed kivétel a Cloud Service (klasszikus) Azure-ba történő telepítésekor.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738283"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>A ConstrainedAllocationFailed hibáinak megoldása a Cloud Service (klasszikus) Azure-ba történő telepítésekor

Ebben a cikkben azokat a foglalási hibákat fogja elhárítani, amelyekben az Azure Cloud Services (klasszikus) nem telepíthető a foglalási megkötések miatt.

Ha a példányokat egy felhőalapú szolgáltatásba (klasszikus) telepíti, vagy új webes vagy feldolgozói szerepkör-példányokat ad hozzá, Microsoft Azure kioszt egy számítási erőforrást.

Időnként előfordulhat, hogy a műveletek során még az Azure-előfizetési korlát elérése előtt is hibákat fog kapni.

> [!TIP]
> Az információk akkor is hasznosak lehetnek, ha a szolgáltatások üzembe helyezését tervezi.

## <a name="symptom"></a>Hibajelenség

A naplók megtekintéséhez a Azure Portal navigáljon a Cloud Service-be (klasszikus), és az oldalsávon válassza a *műveleti napló (klasszikus)* lehetőséget.

![A képen a művelet napló (klasszikus) panel látható.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Ha megvizsgálja a Cloud Service naplóit (klasszikus), a következő kivétel jelenik meg:

|Kivétel típusa  |Hibaüzenet  |
|---------|---------|
|ConstrainedAllocationFailed     |A (z) "" Azure-művelet `{Operation ID}` végrehajtása sikertelen volt, kód: számítás. ConstrainedAllocationFailed. Részletek: a foglalás nem sikerült; nem lehet kielégíteni a megkötéseket a kérelemben. A kért új szolgáltatás üzembe helyezése egy affinitáscsoporthoz van kötve, egy virtuális hálózatot céloz, vagy már van egy üzemelő példány az üzemeltetett szolgáltatásban. Ezen feltételek bármelyike korlátozza az új üzembe helyezést bizonyos Azure-erőforrásokra. Próbálkozzon újra később, vagy próbálja meg csökkenteni a virtuális gép méretét vagy a szerepkör-példányok számát. Ha lehetséges, alternatív lehetőségként távolítsa el a fentebb említett korlátozásokat, vagy végezze el az üzembe helyezést egy másik régión.|

## <a name="cause"></a>Ok

Ha az első példány üzembe helyezése egy felhőalapú szolgáltatásban (átmeneti vagy éles üzemben) történik, a felhőalapú szolgáltatás egy fürthöz lesz rögzítve.

Idővel az ebben a fürtben lévő erőforrások teljes mértékben kihasználhatók lesznek. Ha egy felhőalapú szolgáltatás (klasszikus) kiosztási kérést biztosít további erőforrásokhoz, ha nincs elegendő erőforrás a rögzített fürtben, a kérés foglalási hibát eredményez. További információ: a [foglalási hibák gyakori problémái](cloud-services-allocation-failures.md#common-issues).

## <a name="solution"></a>Megoldás

A meglévő felhőalapú szolgáltatások egy fürthöz vannak *rögzítve* . A Cloud Service (klasszikus) további telepítései ugyanazon a fürtön történnek.

Ha ebben a forgatókönyvben lefoglalási hibát tapasztal, a javasolt művelet az új felhőalapú szolgáltatás (klasszikus) újratelepítése (és a *CNAME* frissítése).

> [!TIP]
> Valószínűleg ez a megoldás a legeredményesebb, mivel lehetővé teszi a platform számára, hogy az adott régióban lévő összes fürt közül válasszon.

> [!NOTE]
> Ennek a megoldásnak nulla állásidőt kell fizetnie.

1. A számítási feladatok üzembe helyezése egy új felhőalapú szolgáltatásban (klasszikus).
    - További útmutatásért tekintse meg a [Cloud Service (klasszikus) létrehozásával és üzembe helyezésével](cloud-services-how-to-create-deploy-portal.md) kapcsolatos útmutatót.

    > [!WARNING]
    > Ha nem szeretné elveszíteni az üzembe helyezési ponthoz tartozó IP-címet, használja [a 3. megoldást: tartsa meg az IP-címet](cloud-services-allocation-failures.md#solutions).

1. Frissítse a *CNAME* -t vagy *egy* rekordot úgy, hogy az az új Cloud Service-be (klasszikus) irányítsa a forgalmat.
    - További útmutatásért tekintse meg az [Egyéni tartománynév konfigurálása Azure Cloud Service (klasszikus)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) útmutatóhoz című témakört.

1. Ha nulla forgalom lesz a régi helyre, akkor törölheti a régi Cloud Service-t (klasszikus).
    - További útmutatásért tekintse meg a [központi telepítések és a Cloud Service (klasszikus)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) című útmutatót.
    - A felhőalapú szolgáltatás (klasszikus) hálózati forgalmának megtekintéséhez tekintse meg a [Cloud Service (klasszikus) monitorozásának bemutatása](cloud-services-how-to-monitor.md)című témakört.

Lásd: a [Cloud Service (klasszikus) lefoglalási hibáinak elhárítása | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) a további szervizelési lépésekhez.

## <a name="next-steps"></a>Következő lépések

További foglalási hibákra vonatkozó megoldások és háttér-információk:

> [!div class="nextstepaction"]
> [Foglalási hibák – Cloud Service (klasszikus)](cloud-services-allocation-failures.md)

Ha az Azure-beli probléma nem szerepel ebben a cikkben, látogasson el az MSDN webhelyen található Azure-fórumokra, [és stack overflow](https://azure.microsoft.com/support/forums/). Felteheti a problémát ezekben a fórumokon, vagy közzéteheti a [ @AzureSupport Twitteren](https://twitter.com/AzureSupport). Azure-támogatási kérelmet is küldhet. Ha támogatási kérést szeretne küldeni, az [Azure-támogatás](https://azure.microsoft.com/support/options/) lapon válassza a *támogatás* lekérése lehetőséget.