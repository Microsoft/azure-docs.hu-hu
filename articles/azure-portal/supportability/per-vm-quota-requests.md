---
title: Az Azure-beli virtuálisgép-sorozatokra vonatkozó vCPU-kvóta növelésének kérése
description: Egy virtuálisgép-sorozat vCPU-kvótájának növelése a Azure Portalban, amely megnöveli a teljes regionális vCPU korlátot ugyanazzal az összeggel.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745434"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standard kvóta: Korlátok növelése virtuálisgép-sorozatonként 

A Azure Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez:

* Az utólagos elszámolású *virtuális gépek* és a *fenntartott virtuálisgép-példányok* *standard vCPU-kvóta* alá esnek.
* A *helyszíni virtuális gépekre* a *helyszínen vCPU kvóta* vonatkozik.

Az utólagos elszámolású és a fenntartott virtuálisgép-példányok esetében a standard szintű vCPU kvóta minden egyes régióban az egyes előfizetések esetén két szinten érvényes:

* Az első szint a *teljes regionális vCPU korlát*, az összes virtuálisgép-sorozaton belül.
* A második réteg a virtuálisgép *-sorozat vCPU korlátja*, például a Dv3 sorozat vCPU.

Amikor új helyszíni virtuális gépet telepít, az összes új és meglévő vCPU-használat az összes helyszíni virtuálisgép-példány esetében nem haladhatja meg a jóváhagyott helyszíni vCPU-kvóta korlátját. Ha a helyszíni kvóta túllépve, a helyszíni virtuális gép üzembe helyezése nem engedélyezett.

A virtuálisgép-sorozat vCPU-kvótájának növekedését a Azure Portal használatával kérheti le. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel.

A standard vCPU-kvótákkal kapcsolatos további információkért lásd a [virtuális gépek vCPU kvótáit](../../virtual-machines/windows/quotas.md) és az [Azure-előfizetések és-szolgáltatások korlátozásait](./classic-deployment-model-quota-increase-requests.md).

Ha szeretné megtudni, hogyan növelhető a vCPU korlátja régiónként a standard kvóta alapján, tekintse meg a [standard kvóta: korlátok növelése régiónként](regional-quota-requests.md)című témakört.

Ha többet szeretne megtudni a virtuális gépek vCPU korlátainak növeléséről, tekintse meg a következőt [: a helyszíni kvóta: az összes virtuálisgép-sorozat korlátainak növelése](low-priority-quota.md).

Az alábbi szakaszokban leírtak szerint a virtuálisgép-sorozatokban a standard vCPU-kvóta korlátozásait kétféleképpen lehet megemelni.

## <a name="request-a-standard-quota-increase-from-help--support"></a>A Súgó + támogatás standard kvóta növelésének kérése

Standard vCPU-kvóta növelésének igénylése virtuálisgép-sorozatokhoz a **Súgó + támogatásban**:

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

1. A **kvóta részleteiben** hajtsa végre a következő lépéseket:

   ![TProvide további kvóta részletei](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. A **telepítési modell** esetében válassza ki a megfelelő modellt.

   1. A **helyek** területen válasszon ki egy helyet. A kiválasztott **hely típus területén**, a **típus kiválasztása** területen válassza a **standard** lehetőséget.

      ![Kvóta részletei – kvóta típusai](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      A **types (típusok**) területen a standard és a direktszín típusú kvótákat egyetlen támogatási esetről is kérheti a többszörös kijelölés támogatásával.

      További információ a helyszíni kvóták korlátainak növeléséről: Azure-beli [virtuális gépek a virtuálisgép-méretezési csoportokhoz](../../virtual-machine-scale-sets/use-spot.md).

   1. A **standard** területen válassza ki az SKU-sorozatot a megnövekedett kvóták számára.

      ![Kvóta részletei – SKU-sorozat](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Adja meg az előfizetéshez használni kívánt új kvóta-korlátozásokat. Ha el szeretne távolítani egy SKU-t a listából, törölje az SKU melletti jelölőnégyzet jelölését, vagy válassza az Elvetés "X" ikont.

      ![Új vCPU-korlát kiválasztása](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Ha egynél több helyhez szeretne kvótát emelni, válasszon egy további helyet a **helyek** területen, majd válasszon ki egy megfelelő virtuálisgép-típust. Ezután megadhat egy korlátot, amely a további helyre vonatkozik.

   ![További telephelyek megadása a kvóta részletei között](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Standard kvóta növelésének kérése az előfizetésből

Szabványos vCPU-kvóta növelésének igénylése virtuálisgép-sorozatokhoz az **előfizetések** esetében:

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

1. A **kvóta részleteiben** hajtsa végre a következő lépéseket:

   1. Az **üzembe helyezési modell** esetében válassza ki a megfelelő modellt, és a **helyek** területen válasszon egy helyet.

      ![Kvóta részleteinek megadása](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. A kiválasztott helyen a **típusok** területen válassza **a típus kiválasztása** lehetőséget, majd válassza a **standard** lehetőséget.

      ![Standard típus kiválasztása](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      A **types (típusok**) területen a standard és a direktszín típusú kvótákat egyetlen támogatási esetről is kérheti a többszörös kijelölés támogatásával.

      További információ a helyszíni kvóták korlátainak növeléséről: Azure-beli [virtuális gépek a virtuálisgép-méretezési csoportokhoz](../../virtual-machine-scale-sets/use-spot.md).

   1. A **standard** beállításnál válassza ki azt az SKU-sorozatot, amelynek a kvótáit bővíteni szeretné.

      ![Kvóta részletei – SKU-sorozat](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Adja meg az előfizetéshez használni kívánt új kvóta-korlátozásokat. Ha el szeretne távolítani egy SKU-t a listáról, törölje a jelölést az SKU melletti jelölőnégyzetből, vagy válassza az Elvetés "X" ikont.

      ![Új vCPU-korlát kiválasztása](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Ha egynél több helyhez szeretne kvótát emelni, válasszon egy további helyet a **helyek** területen, majd válasszon ki egy megfelelő virtuálisgép-típust.

   Ezzel a lépéssel előre betöltődik a korábbi helyszínekhez kiválasztott SKU-sorozat. Adja meg a további adatsorozatra alkalmazni kívánt kvóták korlátait.

   ![Válassza ki a további helyet a kvóta részletei között.](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.