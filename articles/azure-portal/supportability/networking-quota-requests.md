---
title: Hálózatkezelési korlát növelése
description: Hálózatkezelési korlát növelése
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e945dec2ce8514c3e4f1edecdecd13c5c43f7c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745451"
---
# <a name="networking-limit-increase"></a>Hálózatkezelési korlát növelése

A hálózati kvóta növeléséhez használja a [Azure Portal](https://portal.azure.com) .

Ha meg szeretné tekinteni az aktuális hálózatkezelési használatot és kvótát Azure Portalban, nyissa meg az előfizetését, majd válassza a **kihasználtságok és kvóták** lehetőséget Az alábbi beállításokkal is megtekintheti a hálózat használatát és korlátozásait.

* [Használati CLI](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [A hálózati használati API](/rest/api/virtualnetwork/virtualnetworks/listusage)

A portálon a **Súgó + támogatás** vagy a **használat + kvóták** használatával növelheti a növelést.

> [!Note]
> A **nyilvános IP-** előtagok alapértelmezett méretének módosításához válassza a **minimális nyilvános IP-alhálózati előtag hossza** elemet a legördülő listából.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Hálózati kvóta növelésének kérése az előfizetés szintjén a Súgó és támogatás használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést a Azure Portal **Súgó és támogatás** szolgáltatásával.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com), majd válassza a **Súgó + támogatás** lehetőséget a Azure Portal menüben, vagy keresse meg a **Súgó + támogatás** elemet.

    ![Súgó + támogatás](./media/networking-quota-request/help-plus-support.png)

1. Válassza az **Új támogatási kérelem** lehetőséget.

    ![Új támogatási kérelem](./media/networking-quota-request/new-support-request.png)

1. A **probléma típusa** beállításnál válassza a **szolgáltatás és előfizetés korlátai (kvóták)** lehetőséget.

    ![Válassza ki az előfizetési korlátok elemet a probléma típusa legördülő listából](./media/networking-quota-request/select-quota-issue-type.png)

1. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Előfizetés-hírolvasó kiválasztása](./media/networking-quota-request/select-subscription-support-request.png)

1. A **kvóta típusa** területen válassza a **hálózatkezelés** lehetőséget. Válassza a Next (tovább) lehetőséget **: megoldások**.

    ![Kvóta típusának kiválasztása](./media/networking-quota-request/select-quota-type-network.png)

1. A **probléma részletei** lapon válassza a **részletek megadása** lehetőséget, és adja meg a kérés feldolgozásához szükséges további információkat.

    ![Részletek megadása](./media/networking-quota-request/provide-details-link.png)

1. A **kvóta részletei** panelen válasszon ki egy telepítési modellt, egy helyet és a kérésbe felvenni kívánt erőforrásokat.

    ![Kvóta részletei – DM](./media/networking-quota-request/quota-details-network.png)

1. Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához szüntesse meg az erőforrás kiválasztását az **erőforrások** menüből, vagy válassza az Elvetés "x" ikont. Az egyes erőforrások kvótájának megadása után válassza a **Mentés** lehetőséget, és folytassa a támogatási kérések létrehozásával.

    ![Új korlátok](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>A hálózati kvóta növelésének kérése az előfizetési szinten a használat és a kvóták használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést a Azure Portal **használati és kvótájának** használatával.

1. A verzióban https://portal.azure.com Keresse meg és válassza ki az **előfizetéseket**.

    ![Előfizetések](./media/networking-quota-request/search-for-suscriptions.png)

1. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Előfizetés kiválasztása](./media/networking-quota-request/select-subscription-change-quota.png)

1. **Használat + kvóták** kiválasztása

    ![Használat és kvóták kiválasztása](./media/networking-quota-request/select-usage-plus-quotas.png)

1. A jobb felső sarokban válassza a **kérelem növekedés** lehetőséget.

    ![Kérések növekedése](./media/networking-quota-request/request-increase-from-subscription.png)

1. Kövesse a 2. lépéssel kezdődő lépéseket az [előfizetés hálózati kvótájának növeléséhez az előfizetési szinten](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Tudnivalók a hálózati korlátokról

A hálózati korlátokkal kapcsolatos további tudnivalókért tekintse meg a korlátozások lap [hálózatkezelés szakaszát](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) , vagy a hálózat korlátozásai – gyakori kérdések című témakört.
