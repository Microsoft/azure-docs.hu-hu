---
title: Az Azure Cloud Servicest érintő Azure-szolgáltatások megszakadásának kezelését
titleSuffix: Azure Cloud Services
description: Ismerje meg, mi a teendő olyan Azure-szolgáltatás megszakadásakor, amely hatással van az Azure Cloud Servicesra.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: d4f869c8b4ae6e90cfe64a2bf3d13839d72727be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84015299"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Mi a teendő az Azure Cloud Servicest befolyásoló Azure szolgáltatás kiesése esetén?
A Microsoftnál keményen dolgozunk, hogy a szolgáltatások mindig elérhetők legyenek, amikor szüksége van rájuk. A szabályozáson kívüli erők időnként a nem tervezett szolgáltatások megszakadását okozó módokat érintik.

A Microsoft szolgáltatói szerződés (SLA) szolgáltatást biztosít a szolgáltatásai számára az üzemidő és a kapcsolat iránti elkötelezettségként. Az egyes Azure-szolgáltatásokra vonatkozó SLA-t az [Azure-szolgáltatói szerződésekben](https://azure.microsoft.com/support/legal/sla/)találhatja meg.

Az Azure már számos beépített platformot kínál, amelyek támogatják a magas rendelkezésre állású alkalmazásokat. A szolgáltatásokkal kapcsolatos további információkért olvassa el a vész [-helyreállítást és a magas rendelkezésre állást az Azure-alkalmazásokhoz](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Ez a cikk egy valós vész-helyreállítási forgatókönyvet mutat be, amikor egy egész régió jelentős természeti katasztrófák vagy széleskörű szolgáltatás-megszakítás miatt leáll. Ezek ritkán előfordulnak, de fel kell készülnie arra, hogy a teljes régió leálljon. Ha egy teljes régió a szolgáltatás megszakadását tapasztalja, az adatai helyileg redundáns másolatai átmenetileg elérhetetlenné válnak. Ha engedélyezte a földrajzi replikálást, az Azure Storage-blobok és-táblák három további példánya egy másik régióban tárolódik. Ha egy teljes regionális leállás vagy egy olyan katasztrófa következik be, amelyben az elsődleges régió nem helyreállítható, az Azure az összes DNS-bejegyzést átképezi a földrajzilag replikált régióba.

> [!NOTE]
> Ügyeljen arra, hogy a folyamat felett ne legyen vezérlés, és csak az adatközpont-alapú szolgáltatásokkal kapcsolatos fennakadások esetén fog történni. Emiatt a legmagasabb szintű rendelkezésre állás elérése érdekében más alkalmazásspecifikus biztonsági mentési stratégiákra is támaszkodnia kell. További információ: a vész [-helyreállítás és a magas rendelkezésre állás a Microsoft Azure-ra épülő alkalmazásokhoz](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Ha szeretné, hogy hatással legyen a saját feladatátvételre, érdemes figyelembe vennie az [olvasási hozzáférésű geo-redundáns tárolás (ra-GRS)](../storage/common/storage-redundancy.md)használatát, amely egy másik régióban hozza létre az adatainak írásvédett másolatát.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>1. lehetőség: biztonsági mentési telepítés használata az Azure Traffic Manager
A legmegbízhatóbb vész-helyreállítási megoldás az alkalmazás különböző régiókban való több üzembe helyezésének fenntartását jelenti, majd az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) használatával irányítja át a forgalmat. Az Azure Traffic Manager több [útválasztási módszert](../traffic-manager/traffic-manager-routing-methods.md)is biztosít, így kiválaszthatja, hogy az üzemelő példányokat elsődleges/biztonsági mentési modellel vagy a közöttük lévő forgalom felosztásával szeretné-e kezelni.

![Azure-Cloud Services terheléselosztása régiók között az Azure-ban Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Egy adott régió elvesztésével kapcsolatos leggyorsabb válasz esetén fontos, hogy konfigurálja Traffic Manager [végpontjának figyelését](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>2. lehetőség: az alkalmazás üzembe helyezése egy új régióban
Több aktív központi telepítés fenntartása az előző beállításban leírtak szerint további folyamatos költségekkel jár. Ha a helyreállítási idő célkitűzése (RTO) elég rugalmas, és rendelkezik az eredeti kóddal vagy lefordított Cloud Services csomaggal, akkor az alkalmazás új példányát egy másik régióban is létrehozhatja, és frissítheti a DNS-rekordokat, hogy az új központi telepítésre mutasson.

A Cloud Service-alkalmazások létrehozásáról és üzembe helyezéséről a [felhőalapú szolgáltatás létrehozása és üzembe](cloud-services-how-to-create-deploy-portal.md)helyezése című témakörben olvashat részletesebben.

Az alkalmazás adatforrásaitól függően előfordulhat, hogy ellenőriznie kell az alkalmazás adatforrásának helyreállítási eljárásait.

* Az Azure Storage-adatforrások esetében tekintse meg az [Azure Storage redundancia](../storage/common/storage-redundancy.md) című témakört, amely az alkalmazáshoz választott redundancia-modell alapján elérhető beállításokat vizsgálja.
* SQL Database-források esetében olvassa el az [Áttekintés: felhőalapú Üzletmenet-folytonosság és az adatbázis](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) vész-helyreállítása a SQL Database segítségével, hogy az alkalmazáshoz választott replikációs modell alapján elérhető lehetőségek közül a rendelkezésre álló lehetőségeket is megtekintheti.


## <a name="option-3-wait-for-recovery"></a>3. lehetőség: várakozás a helyreállításra
Ebben az esetben nincs szükség beavatkozásra, de a szolgáltatás addig nem lesz elérhető, amíg vissza nem állítja a régiót. A szolgáltatás aktuális állapotát a [Azure Service Health irányítópulton](https://azure.microsoft.com/status/)tekintheti meg.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a vész-helyreállítási és a magas rendelkezésre állási stratégia megvalósításáról, tekintse meg a vész [-helyreállítási és magas rendelkezésre állású Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)című témakört.

A felhőalapú platform képességeinek részletes technikai megismeréséhez lásd: az [Azure rugalmasságával kapcsolatos technikai útmutató](/azure/architecture/checklist/resiliency-per-service).