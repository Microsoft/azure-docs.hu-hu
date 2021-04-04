---
title: Helyszíni virtuális gépek kvótája
description: Növelje a kvóták korlátait a helyszíni virtuális gépek számára, amelyek az Azure-használati modellt biztosítanak, amely lehetővé teszi, hogy az Azure-beli virtuális gépek igény szerinti eltávolításával alacsonyabb költségekkel számoljon.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745485"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Helyszíni kvóta: az összes virtuálisgép-sorozat korlátainak emelése

A helyszíni virtuális gépek (VM-EK) az Azure-használat egy másik modelljét nyújtják. Lehetővé teszik, hogy alacsonyabb költségekkel járjon el az Azure-ban, ha az utólagos elszámolású vagy a fenntartott virtuálisgép-példányok esetében igénybe veszi a virtuális gépeket. A helyszíni virtuális gépekkel kapcsolatos további információkért lásd: Azure-beli [virtuális gépek a virtuálisgép-méretezési csoportokhoz](../../virtual-machine-scale-sets/use-spot.md).

A Azure Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez:

* Az utólagos elszámolású *virtuális gépek* és a *fenntartott virtuálisgép-példányok* *standard vCPU-kvóta* alá esnek.
* A *helyszíni virtuális gépekre* a *helyszínen vCPU kvóta* vonatkozik.

A helyszíni vCPU-kvóta esetében a Resource Manager-vCPU kvótái az összes rendelkezésre álló virtuálisgép-sorozatra érvényesek egyetlen regionális korlátként.

Amikor új helyszíni virtuális gépet telepít, az összes új és meglévő vCPU-használat az összes helyszíni virtuálisgép-példány esetében nem haladhatja meg a jóváhagyott helyszíni vCPU-kvóta korlátját. Ha a helyszíni kvóta túllépve, a helyszíni virtuális gép üzembe helyezése nem engedélyezett.

Ez a cikk azt ismerteti, hogyan lehet megemelni a helyszíni vCPU-kvóta korlátozását a Azure Portal használatával.

A standard vCPU-kvótákkal kapcsolatos további információkért lásd: a [virtuális gépek vCPU kvótái](../../virtual-machines/windows/quotas.md) és az [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Ha többet szeretne megtudni a vCPU-korlát régiónként való növeléséről, tekintse meg a [standard kvóta: korlátok növelése régiónként](regional-quota-requests.md)című témakört.

## <a name="request-a-quota-limit-increase-from-help--support"></a>Kvóta korlátozásának kérése a Súgó és támogatás szolgáltatásban

Ha az összes virtuálisgép-sorozathoz a **Súgó + támogatás** használatával szeretne helyszíni kvótát korlátozni, használja a következőt:

> [!NOTE]
> Egyetlen támogatási esettel több régió esetében is igényelhet kvóta-korlátozást. Részletekért lásd: 8. lépés.

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás** lehetőséget.

   ![A Súgó + támogatás hivatkozása](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. A **Súgó és támogatás** területen válassza az **új támogatási kérelem** lehetőséget.

    ![Új támogatási kérelem létrehozása](./media/resource-manager-core-quotas-request/new-support-request.png)

1. A **probléma típusa** beállításnál válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.

   ![Probléma típusának kiválasztása](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Az **előfizetés** mezőben válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Válassza ki az előfizetést egy nagyobb kvótához](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. A **kvóta típusa** beállításnál válassza a **számítási-VM (magok-vCPU) előfizetési korlát növekszik** lehetőséget.

   ![Adja meg a kvóta típusát](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Kattintson a Next (tovább) gombra **: megoldások** a **probléma részleteinek** megnyitásához. További információk megadásához válassza a **részletek** megadása lehetőséget.

   ![A "részletek megadása" hivatkozás](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. A **kvóta részletei** területen hajtsa végre a következő lépéseket:

   1. Az **üzembe helyezési modell** esetében válassza ki a megfelelő modellt, és a **helyek** területen válasszon egy helyet.

      ![További kvóta részleteinek megadása](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. A kiválasztott **hely típus területén**, a **típus kiválasztása** területen válassza a **hely** lehetőséget.

      ![Adja meg a hely típusát](./media/resource-manager-core-quotas-request/select-spot-type.png)

       A **types (típusok**) területen a standard és a direktszín típusú kvótákat egyetlen támogatási esetről is kérheti a többszörös kijelölés támogatásával.

       További információt a [standard kvóta: a korlátozások virtuálisgép-sorozatok szerinti növelését](per-vm-quota-requests.md)ismertető témakörben talál.

   1. Adja meg az előfizetéshez használni kívánt új kvóta-korlátot.

      ![Új kvóta kiválasztása a helyszíni virtuális gép számára](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Ha egynél több helyhez szeretne kvótát emelni, válasszon egy további helyet a **helyek** területen, majd válasszon ki egy megfelelő virtuálisgép-típust. Ezután megadhat egy korlátot, amely a további helyre vonatkozik.

   ![További telephelyek megadása a kvóta részletei között](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Kvóta korlátozásának kérése az előfizetések panelről

Ha az **előfizetések** ablaktáblán az összes virtuálisgép-sorozathoz a helyszíni kvóta korlátozását szeretné kérni:

> [!NOTE]
> Egyetlen támogatási esettel több régió esetében is igényelhet kvóta-korlátozást. Részletekért lásd: 7. lépés.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **előfizetések** elemet.

   ![Előfizetések a Azure Portal keresési szolgáltatásban](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![A módosítások kiválasztására szolgáló előfizetések](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. A bal oldali ablaktáblán válassza a **használat + kvóták** lehetőséget.

   ![A "használat + kvóták" hivatkozás](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. A jobb felső sarokban válassza a **kérelem növekedés** lehetőséget.

   ![Válassza ki a kvóta növelését](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. A **kvóta típusa** beállításnál válassza a **számítási-VM (magok-vCPU) előfizetési korlát növekszik** lehetőséget.

   ![Adja meg a kvóta típusát](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Kattintson a Next (tovább) gombra **: megoldások** a **probléma részleteinek** megnyitásához. További információk megadásához válassza a **részletek** megadása lehetőséget. A **kvóta részletei** területen adja meg a következő adatokat:

   1. Az **üzembe helyezési modell** esetében válassza ki a megfelelő modellt, és a **helyek** területen válasszon egy helyet.

      ![Kvóta részleteinek megadása](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. A kiválasztott **hely típus területén**, a **típus kiválasztása** területen válassza a **hely** lehetőséget.

      ![Adja meg a hely típusát](./media/resource-manager-core-quotas-request/select-spot-type.png)

      További információt a [standard kvóta: a korlátozások virtuálisgép-sorozatok szerinti növelését](per-vm-quota-requests.md)ismertető témakörben talál.

   1. Adja meg az előfizetéshez használni kívánt új kvóta-korlátot.

      ![Adja meg a vCPU-korlát új értékét](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Ha egynél több helyhez szeretne kvótát emelni, válasszon egy további helyet a **helyek** területen, majd válasszon ki egy megfelelő virtuálisgép-típust. Ezután megadhat egy korlátot, amely a további helyre vonatkozik.

   ![Válassza ki a további helyet a kvóta részletei között.](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.
