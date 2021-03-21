---
title: Gyakori üzembehelyezési hibák elhárítása
titleSuffix: Azure Load Balancer
description: Ismerteti, Hogyan oldhatók fel a gyakori hibák az Azure Load Balancer telepítésekor
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: ae3899a61e942695fed1e3da5fc543b298a42e24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94695554"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Az Azure üzembe helyezésével kapcsolatos gyakori hibák elhárítása Azure Load Balancer

Ez a cikk néhány gyakori Azure Load Balancer telepítési hibát ismertet, és információt nyújt a hibák megoldásához. Ha egy hibakódra vonatkozó információt keres, és ez a cikk nem tartalmaz információt, tudassa velünk. A lap alján elhagyhatja a visszajelzést. A visszajelzéseket a GitHub-problémák követik nyomon.

## <a name="error-codes"></a>Hibakódok

| Hibakód | Részletek és enyhítés |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| A nyilvános IP-SKU-nak és a Load Balancer SKU-nak is egyeznie kell. Győződjön meg arról, Azure Load Balancer és a nyilvános IP-címek megfelelnek. A standard SKU használata éles számítási feladatokhoz ajánlott. További információ az [SKU-beli különbségekről](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | A virtuálisgép-méretezési csoportok alapértelmezett értéke az alapszintű terheléselosztó, ha az SKU nincs meghatározva, vagy standard nyilvános IP-címek nélkül van üzembe helyezve. Telepítse újra a virtuálisgép-méretezési csoport standard nyilvános IP-címekkel való üzembe helyezését az egyes példányokon, hogy standard Load Balancer legyen kiválasztva, vagy egyszerűen válassza ki a standard LB-t a virtuálisgép-méretezési csoport Azure Portal való telepítésekor. |
|MaxAvailabilitySetsInLoadBalancerReached | Egy Load Balancer háttér-készlete legfeljebb 150 rendelkezésre állási készletet tartalmazhat. Ha nem rendelkezik explicit módon meghatározott rendelkezésre állási csoportokkal a virtuális gépekhez a háttér-készletben, minden egyes virtuális gép a saját rendelkezésre állási csoportba kerül. Így a 150 önálló virtuális gépek üzembe helyezése azt jelentené, hogy 150 rendelkezésre állási készletekkel rendelkezett volna, így a korlátot megtalálhatja. Lehetőség van egy rendelkezésre állási csoport üzembe helyezésére és további virtuális gépek hozzáadására megkerülő megoldásként. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | Az alapszintű SKU Load Balancer esetében a hálózati adapternek és a Load balancernek ugyanabban a rendelkezésre állási csoportba kell esnie. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Egy adott terheléselosztó-típuson (belső, nyilvános) nem lehet egynél több szabályt létrehozni ugyanazzal a háttérbeli porttal és protokollal, amelyre ugyanezen virtuálisgép-méretezési csoport hivatkozik. Módosítsa a szabályt az ismétlődő szabály létrehozásának megváltoztatásához. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Egy adott terheléselosztó-típuson (belső, nyilvános) nem lehet egynél több szabályt létrehozni ugyanazzal a háttérbeli porttal és protokollal, amelyre ugyanezen virtuálisgép-méretezési csoport hivatkozik. Módosítsa a szabály paramétereit az ismétlődő szabály létrehozásának megváltoztatásához. |
|AnotherInternalLoadBalancerExists| Csak egy belső típusú Load Balancer lehet a Load Balancer háttérbeli virtuális gépek/hálózati adapterek azonos készletében. Frissítse az üzemelő példányt, és győződjön meg arról, hogy csak egy azonos típusú Load Balancer hoz létre. |
|CannotUseInactiveHealthProbe| Nem használhat olyan mintavételt, amelyet a virtuálisgép-méretezési csoport állapotára konfigurált szabályok nem használnak. Győződjön meg arról, hogy a beállított mintavétel aktívan használatban van. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Ugyanahhoz a típushoz (belső, nyilvános) nem tartozhat több terheléselosztó. Legfeljebb egy belső és egy nyilvános Load Balancer lehet. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Az alapszintű Load Balancer nem támogatott a több elhelyezést biztosító virtuálisgép-méretezési csoportokban vagy a rendelkezésre állási zóna virtuálisgép-méretezési csoportjában. Ehelyett használja a standard Load Balancer. |
|MarketplacePurchaseEligibilityFailed | Váltson a megfelelő rendszergazdai fiókra, hogy engedélyezze a vásárlást, mert az előfizetés EA-előfizetés. Erről [itt](../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase) olvashat bővebben. |
|ResourceDeploymentFailure| Ha a terheléselosztó hibás állapotban van, az alábbi lépésekkel állítsa vissza a hibás állapotból:<ol><li>Nyissa meg a t https://resources.azure.com , és jelentkezzen be a Azure Portal hitelesítő adataival.</li><li>Válassza az **Olvasás/írás** lehetőséget.</li><li>A bal oldalon bontsa ki az **Előfizetések** elemet, majd bontsa ki a frissíteni kívánt terheléselosztót tartalmazó előfizetést.</li><li>A bal oldalon bontsa ki a **ResourceGroups** elemet, majd bontsa ki a frissíteni kívánt terheléselosztót tartalmazó erőforráscsoportot.</li><li>Válassza a **Microsoft. Network**  >  **LoadBalancers** lehetőséget, majd válassza ki a frissíteni kívánt Load Balancer **LoadBalancer_1**.</li><li>A **LoadBalancer_1** megjelenítése lapon válassza a Szerkesztés **beolvasása** elemet  >  .</li><li>A **ProvisioningState** értékének frissítése **sikertelen volt** **.**</li><li>Válassza a **PUT** lehetőséget.</li></ol>|
|  |  |

## <a name="next-steps"></a>Következő lépések

* Tekintse át az Azure Load Balancer [SKU összehasonlító táblázatát](./skus.md)
* További tudnivalók a [Azure Load Balancer korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)