---
title: Az Azure regionális vCPU-kvótáinak növelésére vonatkozó kérelmek
description: A Azure Portal egy régiójának vCPU-kvótájának növelését kéri.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745417"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standard kvóta: korlátok növekedése régiónként

A Azure Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez:

* Az utólagos elszámolású *virtuális gépek* és a *fenntartott virtuálisgép-példányok* *standard vCPU-kvóta* alá esnek.
* A *helyszíni virtuális gépekre* a *helyszínen vCPU kvóta* vonatkozik.

Az utólagos elszámolású és a fenntartott virtuálisgép-példányok esetében a standard szintű vCPU kvóta minden egyes régióban az egyes előfizetések esetén két szinten érvényes:

* Az első szint a *teljes regionális vCPU korlát*, az összes virtuálisgép-sorozaton belül.
* A második réteg a *VM-sorozat vCPU korlátja*, például a D sorozat vCPU.

Amikor új helyszíni virtuális gépet telepít, az adott virtuálisgép-sorozat összes új és meglévő vCPU-használata nem lépheti túl az adott virtuálisgép-sorozat jóváhagyott vCPU-kvótáját. Emellett az összes virtuálisgép-sorozatban üzembe helyezett új és meglévő vCPU teljes száma nem haladhatja meg az előfizetés teljes jóváhagyott regionális vCPU vonatkozó kvótáját. Ha túllépi a kvóták valamelyikét, a virtuális gép üzembe helyezése nem engedélyezett.

A virtuálisgép-sorozat vCPU-kvótájának növekedését a Azure Portal használatával kérheti le. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel.

Ha új előfizetést hoz létre, akkor előfordulhat, hogy a regionális vCPU alapértelmezett száma nem egyezik meg az összes egyedi virtuálisgép-sorozat alapértelmezett vCPU-kvótájának összegével. Ez az eltérés olyan előfizetést eredményezhet, amely elegendő kvótával rendelkezik minden egyes telepíteni kívánt virtuálisgép-sorozathoz. Előfordulhat azonban, hogy nem áll rendelkezésre elegendő kvóta az összes üzemelő példány regionális vCPU befogadásához. Ebben az esetben kérelmet kell benyújtania, hogy explicit módon növelje a regionális vCPU teljes számának korlátját. A regionális vCPU teljes korlátja nem lépheti túl a régió összes virtuálisgép-sorozatának jóváhagyott teljes kvótáját.

A standard vCPU-kvótákkal kapcsolatos további információkért lásd: a [virtuális gépek vCPU kvótái](../../virtual-machines/windows/quotas.md) és az [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Ha többet szeretne megtudni a virtuális gépek vCPU korlátainak növeléséről, tekintse meg a következőt [: a helyszíni kvóta: az összes virtuálisgép-sorozat korlátainak növelése](low-priority-quota.md).

A vCPU standard kvóta növelését kétféle módon, régiónként is megteheti.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Kvóta növelésének kérése régiónként a Súgó + támogatás szolgáltatásban

A vCPU kvóta növelésének **megkönnyítése a Súgó + támogatás** területen:

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás** lehetőséget.

   ![A "Súgó + támogatás" hivatkozás](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. A **Súgó és támogatás** területen válassza az **új támogatási kérelem** lehetőséget.

    ![Új támogatási kérelem](./media/resource-manager-core-quotas-request/new-support-request.png)

1. A **probléma típusa** beállításnál válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.

   ![Probléma típusának kiválasztása](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Az **előfizetés** mezőben válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. A **kvóta típusa** beállításnál válassza az **egyéb kérések** lehetőséget.

   ![Adja meg a kvóta típusát](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Kattintson a Next (tovább) gombra **: megoldások** a **probléma részleteinek** megnyitásához. A **Leírás** mezőben adja meg a következő információkat:

    1. A **telepítési modellhez** a **Resource Managert** kell megadni.  
    1. A **régió** mezőben adja meg a szükséges régiót (például: **USA 2. keleti** régiója).  
    1. Az **új korlát** beállításnál a régióhoz új vCPU-korlátot kell megadni. Ez az érték nem haladhatja meg az előfizetéshez tartozó egyedi SKU-sorozatokra vonatkozó jóváhagyott kvóták összegét.

    ![Adja meg a kvóta kérésének részleteit](./media/resource-manager-core-quotas-request/regional-details.png)

1. A támogatási kérelem létrehozásának folytatásához válassza a **felülvizsgálat + létrehozás** lehetőséget.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Kvóta növelésének kérése régiónként az előfizetések között

VCPU-kvóta növelésének kérése régiónként az **előfizetések**:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **előfizetések** elemet.

   ![Ugrás az előfizetések Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Válassza ki a módosítani kívánt előfizetést](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. A bal oldali ablaktáblán válassza a **használat + kvóták** lehetőséget.

   ![Használati és kvóták hivatkozásának követése](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. A jobb felső sarokban válassza a **kérelem növekedés** lehetőséget.

   ![Válassza ki a kvóta növelését](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. A **kvóta típusa** területen válassza az **egyéb kérések** lehetőséget.

   ![Válassza ki a kvóta típusát](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Kattintson a Next (tovább) gombra **: megoldások** a **probléma részleteinek** megnyitásához. A **Leírás** mezőben adja meg a következő további információkat:

    1. A **telepítési modellhez** a **Resource Managert** kell megadni.  
    1. A **régió** mezőben adja meg a szükséges régiót (például: **USA 2. keleti** régiója).  
    1. Az **új korlát** beállításnál a régióhoz új vCPU-korlátot kell megadni. Ez az érték nem haladhatja meg az előfizetéshez tartozó egyedi SKU-sorozatokra vonatkozó jóváhagyott kvóták összegét.

    ![Adja meg az adatokat a részletek között](./media/resource-manager-core-quotas-request/regional-details.png)

1. A támogatási kérelem létrehozásának folytatásához válassza a **felülvizsgálat + létrehozás** lehetőséget.
