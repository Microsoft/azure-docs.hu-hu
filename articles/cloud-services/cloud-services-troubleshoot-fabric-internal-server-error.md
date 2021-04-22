---
title: A FabricInternalServerError vagy a ServiceAllocationFailure hibaelhárítása felhőszolgáltatás (klasszikus) Azure-beli | Microsoft Docs
description: Ez a cikk bemutatja, hogyan oldható fel egy FabricInternalServerError vagy ServiceAllocationFailure kivétel egy (klasszikus) felhőszolgáltatás Azure-ban való üzembe helyezésekor.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0883178779179df2e531123b8a500c62d42530e4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877443"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>A FabricInternalServerError vagy a ServiceAllocationFailure hibaelhárítása felhőszolgáltatás (klasszikus) Azure-ban való üzembe helyezésekor

Ebben a cikkben olyan foglalási hibák elhárítását fogja elhárítani, amelyekben a hálóvezérlő nem tud lefoglalni egy (klasszikus) Azure-felhőszolgáltatás üzembe helyezésekor.

Amikor példányokat helyez üzembe egy Cloud Service-ben, vagy új webes vagy feldolgozói szerepkörpéldányokat ad hozzá, a Microsoft Azure számítási erőforrásokat foglal le.

Előfordulhat, hogy hibaüzenet jelenik meg ezen műveletek során, még az Azure-előfizetés korlátjának elérését megelőzően is.

> [!TIP]
> Ezek az információk a szolgáltatások üzembe helyezésének megtervekor is hasznosak lehetnek.

## <a name="symptom"></a>Hibajelenség

A Azure Portal nyissa meg a Felhőszolgáltatást (klasszikus), majd az oldalsávon válassza a Műveleti napló *(klasszikus)* lehetőséget a naplók megtekintéséhez.

![A képen a Műveleti napló (klasszikus) panel látható.](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

A felhőszolgáltatás (klasszikus) naplóinak vizsgálatakor a következő kivételt fogja látni:

|Kivétel  |Hibaüzenet  |
|---------|---------|
FabricInternalServerError     |A művelet "InternalError" hibakóddal és errorMessage "A kiszolgáló belső hibát észlelt" hibával meghiúsult. Próbálja újra a kérést."|
|ServiceAllocationFailure     |A művelet "InternalError" hibakóddal és errorMessage "A kiszolgáló belső hibát észlelt" hibával meghiúsult. Próbálja újra a kérést."|

## <a name="cause"></a>Ok

*A FabricInternalServerError* és *a ServiceAllocationFailure* kivétel, amelyek akkor fordulhatnak elő, ha a hálóvezérlő nem tud példányokat lefoglalni a fürtben. A kiváltó ok eltérő lehet, ha a **felhőszolgáltatás** rögzített vagy **nem rögzített.**

- [**Nincs kitűzve:** Új felhőszolgáltatás első üzembe helyezése során történt hibák](#not-pinned-to-a-cluster)
- [**Rögzített:** Meglévő felhőszolgáltatásokból származó hibák](#pinned-to-a-cluster)

> [!NOTE]
> Amikor az első példányt üzembe helyez egy felhőszolgáltatásban (akár előkészítési, akár éles környezetben), az a felhőszolgáltatás egy fürthöz lesz tűzve.
>
> Idővel az erőforráskészletben található erőforrások teljes mértékben ki lesznek használva. Ha egy felhőszolgáltatás további erőforrásokra vonatkozó foglalási kérést tesz, ha nem áll rendelkezésre elegendő erőforrás a rögzített erőforráskészletben, a kérés foglalási [hibát fog eredményezni.](cloud-services-allocation-failures.md)

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

1. Ha már nincs forgalom a régi webhelyre, törölheti a régi felhőszolgáltatást (klasszikus).
    - További útmutatásért tekintse meg [az üzemelő példányok törlését és a felhőszolgáltatás (klasszikus)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) útmutatóját.
    - A (klasszikus) felhőszolgáltatás hálózati forgalmának ellenőrzésével lásd: Bevezetés a felhőszolgáltatás [(klasszikus) monitorozási szolgáltatásba.](cloud-services-how-to-monitor.md)

Lásd: [Felhőszolgáltatás (klasszikus) foglalási hibáinak elhárítása | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) a további javítási lépésekért.

## <a name="next-steps"></a>Következő lépések

További foglalási hibák megoldása és háttérinformációk:

> [!div class="nextstepaction"]
> [Foglalási hibák – Felhőszolgáltatás (klasszikus)](cloud-services-allocation-failures.md)

Ha a cikk nem foglalkozik az Azure-ral kapcsolatos probléma megoldásával, látogasson el az [MSDN-ről](https://azure.microsoft.com/support/forums/)az Azure fórumára, és Stack Overflow. A problémát közzétenheti ezeken a fórumokon, vagy közzétenheti a [ @AzureSupport twitteren.](https://twitter.com/AzureSupport) Elküldhet egy Azure-támogatás is. Ha támogatási kérést küld, a Azure-támogatás [válassza](https://azure.microsoft.com/support/options/) a *Támogatás kérése lehetőséget.*
