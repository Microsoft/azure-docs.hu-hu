---
title: A OverconstrainedAllocationRequest hibáinak megoldása a Cloud Service (klasszikus) Azure-ba történő telepítésekor | Microsoft Docs
description: Ez a cikk bemutatja, hogyan oldható meg egy OverconstrainedAllocationRequest-kivétel a Cloud Service (klasszikus) Azure-ba történő telepítésekor.
services: cloud-services
documentationcenter: ''
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1b50ded166b3f62b38830b4c2d18da7c4c4f0d35
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745640"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>A OverconstrainedAllocationRequest hibáinak megoldása a Cloud Services (klasszikus) Azure-ba történő telepítésekor

Ebben a cikkben az Azure Cloud Services (klasszikus) üzembe helyezését megakadályozó korlátozott foglalási hibákkal kapcsolatos hibák elhárítása.

Ha példányokat helyez üzembe egy felhőalapú szolgáltatásban, vagy új webes vagy feldolgozói szerepkört ad hozzá, a Microsoft Azure kiosztja a számítási erőforrásokat.

Időnként előfordulhat, hogy a műveletek során még az Azure-előfizetési korlát elérése előtt is hibákat fog kapni.

> [!TIP]
> Az információk akkor is hasznosak lehetnek, ha a szolgáltatások üzembe helyezését tervezi.

## <a name="symptom"></a>Hibajelenség

![A képen a művelet napló (klasszikus) panel látható.](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Kivétel típusa  |Hibaüzenet  |
|---------|---------|
|OverconstrainedAllocationRequest |A központi telepítéshez szükséges virtuálisgép-méret (vagy virtuálisgép-méretek kombinációja) nem építhető ki a telepítési kérelmek korlátozásai miatt. Ha lehetséges, próbálkozzon olyan pihentető korlátozásokkal, mint például a virtuális hálózati kötések, üzembe helyezés egy üzemeltetett szolgáltatásban, amely nem rendelkezik más központi telepítéssel, és egy másik affinitási csoportba, vagy nem rendelkezik affinitási csoporttal, vagy próbálja meg telepíteni egy másik régiót.|

## <a name="cause"></a>Ok

A kiváltó ok akkor változik, ha a felhőalapú szolgáltatás **rögzítve** van vagy **nincs rögzítve**.

- [**Nincs rögzítve:** Új felhőalapú szolgáltatás első üzembe helyezésének hibái (klasszikus)](#not-pinned-to-a-cluster)
- [**Rögzített:** Meglévő felhőalapú szolgáltatás hibái (klasszikus)](#pinned-to-a-cluster)

> [!NOTE]
> Ha az első példány üzembe helyezése egy felhőalapú szolgáltatásban (átmeneti vagy éles üzemben) történik, a felhőalapú szolgáltatás egy fürthöz lesz rögzítve.
>
> Az idő múlásával előfordulhat, hogy a fürt erőforrásai teljes mértékben kihasználva lesznek. Ha egy felhőalapú szolgáltatás (klasszikus) kiosztási kérést biztosít további erőforrásokhoz, ha nincs elegendő erőforrás a rögzített fürtben, a kérés [foglalási hibát](cloud-services-allocation-failures.md)eredményez.

## <a name="solution"></a>Megoldás

A következő esetekben kövesse a foglalási hibákra vonatkozó útmutatást.

### <a name="not-pinned-to-a-cluster"></a>Nem rögzített fürthöz

Amikor először telepít egy felhőalapú szolgáltatást (klasszikus), a fürt még nincs kiválasztva, így a Cloud Service nincs *rögzítve*. Előfordulhat, hogy az Azure üzembe helyezési hibája a következőket okozza:

- Kiválasztott egy adott méretet, amely nem érhető el a régióban.
- A különböző szerepkörökhöz szükséges méretek kombinációja nem érhető el a régióban.

Ha ebben a forgatókönyvben lefoglalási hibát tapasztal, a javasolt művelet a régióban elérhető méretek ellenőrzése, és a korábban megadott méret módosítása.

1. A felhőben elérhető méreteket a [Cloud Service (klasszikus termékek)](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) lapon tekintheti meg.

    > [!NOTE]
    > A *termékek* lap nem jeleníti meg a rendelkezésre álló kapacitást. Bármely új foglalás esetén az Azure-nak képesnek kell lennie arra, hogy a régiójában lévő optimális fürtöt kiválassza az adott időpontban.

1. Frissítse a Service definition-fájlt a Cloud Service-hez (klasszikus), hogy az adott régiótól eltérő [méretű terméket](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) határozzon meg.

### <a name="pinned-to-a-cluster"></a>Fürthöz rögzítve

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
