---
title: Hálózatkezelési korlát növelése
description: Hálózatkezelési korlát növelése
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 801f0424e7ec15fbde58f35975f4c7eca4c9a5de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775564"
---
# <a name="networking-limit-increase"></a>Hálózatkezelési korlát növelése

A [Azure Portal](https://portal.azure.com) növelheti a hálózati kvótát.

Az aktuális hálózathasználat és kvóta megtekintéséhez nyissa meg Azure Portal előfizetését, majd válassza a **Használat + kvóták lehetőséget.** A hálózathasználat és a korlátozások megtekintéséhez az alábbi lehetőségeket is használhatja.

* [Használati CLI](/cli/azure/network#az_network_list_usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [A hálózathasználati API](/rest/api/virtualnetwork/virtualnetworks/listusage)

A növekedés igénylését a Súgó **+** támogatás vagy a Portál **Használat + kvóták lehetőségében** kérheti.

> [!Note]
> A nyilvános IP-előtagok alapértelmezett méretének beállításához **válassza** a Minimális nyilvános **IP-cím hálózati** előtaghossza lehetőséget a legördülő listából.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Hálózatra vonatkozó kvóta növelésének kérése előfizetési szinten a Súgó és támogatás használatával

Az alábbi utasításokat követve hozzon létre egy támogatási kérést a súgó **és** támogatás használatával a Azure Portal.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)majd válassza a Súgó **és** támogatás lehetőséget Azure Portal menüben, vagy keresse meg és válassza a **Súgó + támogatás lehetőséget.**

    ![Súgó + támogatás](./media/networking-quota-request/help-plus-support.png)

1. Válassza az **Új támogatási kérelem** lehetőséget.

    ![Új támogatási kérelem](./media/networking-quota-request/new-support-request.png)

1. A **Probléma típusaként** válassza a **Szolgáltatási és előfizetési korlátok (kvóták) lehetőséget.**

    ![Előfizetési korlátok kiválasztása a problématípus legördülő menüből](./media/networking-quota-request/select-quota-issue-type.png)

1. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Előfizetés kiválasztása newSR](./media/networking-quota-request/select-subscription-support-request.png)

1. A Kvóta **típusa alatt** válassza a **Hálózat lehetőséget.** Válassza **a Tovább: Megoldások lehetőséget.**

    ![Kvótatípus kiválasztása](./media/networking-quota-request/select-quota-type-network.png)

1. A **PROBLÉMA RÉSZLETEI részen** válassza a **Részletek megszabadása** lehetőséget, és adjon meg további információkat a kérés feldolgozásához.

    ![Adja meg a részleteket](./media/networking-quota-request/provide-details-link.png)

1. A Kvóta **részletei panelen** válassza ki az üzembe helyezési modellt, a helyet és a kérésbe foglalni kívánt erőforrásokat.

    ![Kvóta részletei DM](./media/networking-quota-request/quota-details-network.png)

1. Adja meg az előfizetésre vonatkozó új korlátokat. Egy sor eltávolításához törölje az erőforrás kijelölését az **Erőforrások menüből,** vagy válassza az "x" elvetés ikont. Miután megadta az egyes erőforrásokra vonatkozó kvótát, válassza a **Mentés lehetőséget,** és folytassa a támogatási kérelem létrehozását.

    ![Új korlátok](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Hálózathasználati kvóta növelésének kérése előfizetési szinten a Használat + kvóták használatával

Kövesse ezeket az utasításokat egy támogatási kérés létrehozásához a használati **adatok + kvóta** Azure Portal.

1. A https://portal.azure.com alatt keresse meg és válassza az **Előfizetések lehetőséget.**

    ![Előfizetések](./media/networking-quota-request/search-for-suscriptions.png)

1. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Előfizetés kiválasztása](./media/networking-quota-request/select-subscription-change-quota.png)

1. Válassza a **Használat + kvóták lehetőséget**

    ![Használat és kvóták kiválasztása](./media/networking-quota-request/select-usage-plus-quotas.png)

1. A jobb felső sarokban válassza a **Kérés növelése lehetőséget.**

    ![Kérés növelése](./media/networking-quota-request/request-increase-from-subscription.png)

1. Kövesse a Hálózati kvóta növelésének kérése előfizetési szinten 3. [lépésével kezdődő lépéseket.](#request-networking-quota-increase-at-subscription-level-using-help--support)

## <a name="about-networking-limits"></a>A hálózati korlátok

A hálózati korlátokkal kapcsolatos további [](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) információkért tekintse meg a Korlátok oldal Hálózat szakaszát vagy a Hálózati korlátok – gyakori kérdések szakaszt.
