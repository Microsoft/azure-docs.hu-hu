---
title: Klasszikus Azure üzembe helyezési modell
description: A Resource Manager-modell által felülírt klasszikus üzemi modell kikényszeríti a virtuális gépek és a virtuálisgép-méretezési csoportok globális vCPU-kvótájának korlátját.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745604"
---
# <a name="classic-deployment-model"></a>Klasszikus üzemi modell

A klasszikus üzembe helyezési modell a régebbi generációs Azure-telepítési modell. A virtuális gépek és a virtuálisgép-méretezési csoportok esetében a globális vCPU-kvóta korlátozását alkalmazza. A klasszikus üzembe helyezési modell már nem ajánlott, és a Resource Manager-modell már felülírja őket.

Ha többet szeretne megtudni a két üzembe helyezési modellről és a Resource Manager használatának előnyeiről, tekintse meg a [Resource Manager és a klasszikus üzembe helyezés](../../azure-resource-manager/management/deployment-models.md)című témakört.

Új előfizetés létrehozásakor a rendszer a vCPU alapértelmezett kvótáját rendeli hozzá. Amikor új virtuális gépet kíván üzembe helyezni a klasszikus üzemi modell használatával, az összes régióban az új és a meglévő vCPU-használat összege nem haladhatja meg a klasszikus üzemi modellhez jóváhagyott vCPU-kvótát.

A kvótákkal kapcsolatos további tudnivalókért tekintse meg az [Azure-előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md)című témakört.

A klasszikus üzemi modellre vonatkozó vCPU-kvóta növelését is kérheti. Használja a **Súgó + támogatás** vagy a **használat + kvóták** használatát a Azure Portalban.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Kérelem/virtuálisgép-sorozat vCPU-kvótájának növekedése az előfizetés szintjén a Súgó + támogatás használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést a Azure Portal **Súgó és támogatás** szolgáltatásával.

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás** lehetőséget.

   ![Válassza a Súgó + támogatás lehetőséget a Azure Portal](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Válassza az **Új támogatási kérelem** lehetőséget.

   ![Új támogatási kérelem létrehozása a Azure Portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. A **probléma típusa mezőben** válassza a **szolgáltatás és előfizetés korlátai (kvóták)** lehetőséget.

   ![Válassza ki a kvótákat a probléma típusaként.](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. A **kvóta típusa** beállításnál válassza a **számítási-VM (magok-vCPU) előfizetési korlát növekszik** lehetőséget.

   ![Válassza ki a kvóta típusát a növeléshez](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Kattintson a Next (tovább) gombra **: megoldások** a **probléma részleteinek** megnyitásához. További információk megadásához válassza a **részletek megadása** lehetőséget.

   ![Adja meg a kérését a következő címen](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. A **kvóta részletei** területen válassza a **klasszikus** lehetőséget, és válasszon egy **helyet**.

   ![Részletek hozzáadása, beleértve a telepítési modellt és a helyet](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. **SKU-család** esetében jelöljön ki egy vagy több SKU-családot a növeléshez.

   ![A növekedni kívánt SKU-család meghatározása](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához szüntesse meg az SKU- **család** kijelölését, vagy válassza az Elvetés "X" ikont. Miután megad egy kvótát az egyes SKU-családokhoz, válassza a mentés lehetőséget, **majd folytassa** a **kvóta részleteiben** a támogatási kérelem folytatásához.

   ![Új korlátok kérése](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Kérelem/virtuálisgép-sorozat vCPU-kvóta növelése az előfizetési szinten a használat + kvóták használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést a **használat + kvóták** használatával a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **előfizetések** elemet.

   ![Ugrás az előfizetések Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Válassza ki a módosítandó előfizetést](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Válassza a **használat + kvóták** lehetőséget.

   ![Az előfizetéshez tartozó használati és kvóták kiválasztása](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. A jobb felső sarokban válassza a **kérelem növekedés** lehetőséget.

   ![Válassza ki a kvóta növelését](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Válassza a **számítási-VM (magok-vCPU) előfizetési korlátot** a **kvóta típusaként**.

   ![Kvóta típusának kiválasztása](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Kattintson a Next (tovább) gombra **: megoldások** a **probléma részleteinek** megnyitásához. További információk megadásához válassza a **részletek megadása** lehetőséget.

   ![Adja meg a kérés részleteit](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. A **kvóta részletei** területen válassza a **klasszikus** és a **hely** lehetőséget.

   ![Válassza ki a kvóta részleteit, beleértve a telepítési modellt és a helyet](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Válasszon ki egy vagy több SKU-családot a növekedéshez.

   ![Válassza ki az SKU-családot a növekedéshez](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához szüntesse meg az SKU- **család** kijelölését, vagy válassza az Elvetés "X" ikont. Miután megad egy kvótát az egyes SKU-családokhoz, válassza a mentés lehetőséget, **majd folytassa** a **kvóta részleteiben** a támogatási kérelem folytatásához.

   ![Új kvóta megadása](./media/resource-manager-core-quotas-request/new-limits-classic.png)

