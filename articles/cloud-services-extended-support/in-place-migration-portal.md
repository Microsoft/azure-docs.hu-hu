---
title: Migrálás – portál
description: Migrálás Cloud Servicesre (kiterjesztett támogatás) a Azure Portal használatával
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286888"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Migrálás Cloud Servicesre (kiterjesztett támogatás) a Azure Portal használatával

Ez a cikk bemutatja, hogyan használható a Azure Portal a [Cloud Services (klasszikus)](../cloud-services/cloud-services-choose-me.md) rendszerről a [Cloud Services (bővített támogatás)](overview.md)való áttelepítéshez.

> [!IMPORTANT]
> Az áttelepítési eszköz jelenleg nyilvános előzetes verzióban érhető el a Cloud Services (klasszikus) rendszerről a Cloud Servicesra (kiterjesztett támogatás). Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Előkészületek

**Győződjön meg arról, hogy Ön az előfizetés rendszergazdája.**

Az áttelepítés elvégzéséhez hozzá kell adnia az előfizetéshez tartozó [Azure Portal](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **központi** menüben válassza az **előfizetés** lehetőséget. Ha nem látja, válassza a **minden szolgáltatás** lehetőséget.
3. Keresse meg a megfelelő előfizetési bejegyzést, majd tekintse meg a **saját szerepkör** mezőt. A rendszergazdák számára az értéknek a *fiók* rendszergazdája kell lennie.

Ha nem tud felvenni egy társ-rendszergazda szerepkört, forduljon a szolgáltatás rendszergazdájához vagy a [társ-rendszergazdához](../role-based-access-control/classic-administrators.md) , és kérje a hozzáadást.

**Regisztrálás az áttelepítési erőforrás-szolgáltatóra**

1. Regisztrálja az áttelepítési erőforrás-szolgáltatót `Microsoft.ClassicInfrastructureMigrate` és az előzetes verziójú szolgáltatást a `Cloud Services` Microsoft. számítási névtér területen a [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1)használatával.  
1. Várjon öt percet, amíg a regisztráció befejeződik, majd jelölje be a jóváhagyás állapotát. 

## <a name="migrate-your-cloud-service-resources"></a>A Cloud Service-erőforrások migrálása

1. Lépjen a [Cloud Services (klasszikus) portálra](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames). 
2. Válassza ki az áttelepíteni kívánt felhőalapú szolgáltatást.
3. Válassza az **áttelepítés az ARM** panelre lehetőséget.

    > [!NOTE]
    > Ha egy virtuális hálózaton (klasszikuson) belüli Cloud Services (klasszikus) áttelepítését végzi, megjelenik egy szalagcím üzenet, amely felszólítja a virtuális hálózat (klasszikus) panel áthelyezésére.
    > A virtuális hálózat (klasszikus) panelt a virtuális hálózat (klasszikus) és a Cloud Services (klasszikus) telepítésének befejezéséhez fogja elvégezni.
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="A képen a klasszikus virtuális hálózat áthelyezése látható a Azure Portalban.":::
 

4. Ellenőrizze az áttelepítést. 

    Ha az érvényesítés sikeres, akkor minden központi telepítés támogatott, és készen áll a felkészülésre.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="A képen látható az áttelepítés az ARM panelre a Azure Portal.":::

    Ha az ellenőrzés sikertelen, a rendszer megjeleníti a nem támogatott forgatókönyvek listáját, és az áttelepítés folytatása előtt meg kell oldani. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="A rendszerkép érvényesítési hibát jelez a Azure Portalban.":::

5. Felkészülés az áttelepítésre.

    Ha az előkészítés sikeres, az áttelepítés készen áll a végrehajtásra.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="A rendszerkép a Azure Portal érvényesítését jeleníti meg.":::

    Ha az előkészítés sikertelen, tekintse át a hibát, oldja meg a problémákat, és próbálkozzon újra az előkészítéssel. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="A rendszerkép az érvényesítési hiba hibáját mutatja.":::

      Az előkészítést követően a virtuális hálózatban lévő összes Cloud Services a Cloud Services (klasszikus) és Cloud Services (bővített támogatás) Azure Portal paneljén végzett olvasási műveletekhez is elérhető. A felhőalapú szolgáltatás (bővített támogatás) üzembe helyezése mostantól ellenőrizhető, hogy az áttelepítés véglegesítése előtt megfelelő működést biztosítson. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="A képen a portál panelen a tesztelési API-k láthatók.":::

6.  **(Nem kötelező)** Az áttelepítés megszakítása. 
    
    Ha az áttelepítés megszüntetését választotta, a **megszakítás** gombbal állíthatja vissza az előző lépéseket. Az Cloud Services (klasszikus) üzembe helyezése ekkor fel lesz oldva az összes szifilisz-művelethez.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="A képen az érvényesítés átadása látható.":::

    Ha a megszakítás sikertelen, válassza az **Újrapróbálkozás megszakítása** lehetőséget. A probléma megoldásához próbálkozzon újra. Ha nem, forduljon az ügyfélszolgálathoz. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="A rendszerkép az érvényesítési hiba hibaüzenetét jeleníti meg.":::

7.  Áttelepítés véglegesítve.

    >[!IMPORTANT]
    > Ha véglegesíti az áttelepítést, nincs lehetőség a visszaállításra. 
    
    Az "igen" érték beírásával erősítse meg és véglegesítse az áttelepítést. Az áttelepítés már befejeződött. Az áttelepített Cloud Services (bővített támogatás) üzembe helyezése az összes művelet számára fel van oldva. 

## <a name="next-steps"></a>Következő lépések
Tekintse át az [áttelepítés utáni változások](in-place-migration-overview.md#post-migration-changes) szakaszt a központi telepítési fájlok, az automatizálás és az új Cloud Services (bővített támogatás) központi telepítésének egyéb attribútumaival kapcsolatos változások megtekintéséhez. 
