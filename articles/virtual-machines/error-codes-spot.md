---
title: Az Azure spot Virtual Machines és a méretezési csoport példányainak hibakódai
description: Ismerkedjen meg az Azure spot Virtual Machines és a méretezési csoport példányainak használatakor esetlegesen megjelenő hibakódokkal.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 6d43935365580233063deb0e523d222351c22d54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670607"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Hibaüzenetek az Azure spot Virtual Machines és a méretezési csoportokhoz

Íme néhány lehetséges hibakód, amelyet az Azure spot Virtual Machines és a méretezési csoportok használatakor kaphat.


| Kulcs | Üzenet | Leírás |
|-----|---------|-------------|
| SkuNotAvailable | A (z) "" erőforrás kért szintje \<resource\> jelenleg nem érhető el a (z) "" előfizetéshez a (z) "" helyen \<location\> \<subscriptionID\> . Próbáljon ki egy másik szintet, vagy helyezzen üzembe egy másik helyet. | Ezen a helyen nincs elegendő Azure-beli virtuálisgép-kapacitás a virtuális gép vagy a méretezési csoport példányának létrehozásához. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  A kizárási szabályzat csak az Azure spot Virtual Machines állítható be. | Ez a virtuális gép nem Azure-beli helyként szolgáló virtuális gép, ezért nem állíthatja be a kizárási szabályzatot. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Az Azure spot virtuális gép nem támogatott a rendelkezésre állási csoporton belül. | Válasszon egy Azure-beli helyszíni virtuális gépet, vagy használjon egy virtuális gépet egy rendelkezésre állási csoportban, nem választhat mindkettőt. |
| AzureSpotFeatureNotEnabledForSubscription  |  Az előfizetés nincs engedélyezve az Azure spot Virtual Machine szolgáltatással. | Használjon olyan előfizetést, amely támogatja az Azure spot Virtual Machines. |
| VMPriorityCannotBeApplied  |  A megadott prioritási érték ("") {0} nem alkalmazható a (z) "" virtuális gépre, {1} mert nem lett megadva prioritás a virtuális gép létrehozásakor. | A virtuális gép létrehozásakor válassza ki a prioritást. |
| SpotPriceGreaterThanProvidedMaxPrice  |  A (z) "" művelet nem hajtható végre, {0} mert a megadott maximális díj (USD) {1} alacsonyabb, mint a jelenlegi "USD" ár az Azure-beli {2} virtuális gép méretének ( {3} ""). | Válasszon magasabb maximális árat. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) vagy a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)díjszabási információi.|
| MaxPriceValueInvalid  |  A maximális ár értéke érvénytelen. A maximális ár értéke csak-1, a nullánál nagyobb tizedes tört. A-1 érték maximális értéke azt jelzi, hogy az Azure spot virtuális gép árát nem lehet kizárni az árak miatt. | Érvényes maximális árat adjon meg. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) vagy a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)díjszabása. |
| MaxPriceChangeNotAllowedForAllocatedVMs | A maximális árváltozás nem engedélyezett, ha a (z {0} ) "" virtuális gép jelenleg le van foglalva. Szabadítson fel, és próbálkozzon újra. | Stop\Deallocate a virtuális gépet, hogy meg lehessen változtatni a maximális árat. |
| MaxPriceChangeNotAllowed | Az árváltozás maximális értéke nem engedélyezett. | A virtuális gép maximális ára nem módosítható. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Az Azure spot virtuális gép nem támogatott ehhez az API-verzióhoz. | Az API-verziót 2019-03-01-re kell állítani. |
| AzureSpotIsNotSupportedForThisVMSize  |  Ez a virtuálisgép-méret nem támogatja az Azure spot virtuális gépet {0} . | Válasszon másik virtuális gép méretét. További információ: [Azure Spot Virtual Machines](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  A maximális díj csak az Azure spot Virtual Machines esetén támogatott. | További információ: [Azure Spot Virtual Machines](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Az erőforrások áthelyezése kérelem egy Azure-beli helyszíni virtuális gépet tartalmaz. Nem támogatott. A virtuális gépek azonosítóinak részleteiről itt tájékozódhat. | Az Azure spot Virtual Machines nem helyezhető át. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Az erőforrások áthelyezése kérelem egy Azure-beli virtuálisgép-méretezési készletet tartalmaz. Nem támogatott. A virtuálisgép-méretezési csoport azonosítóinak részleteit itt találja. | Az Azure direkt virtuálisgép-méretezési csoport példányai nem helyezhetők át. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | A virtuálisgép-méretezési csoport virtuálisgép-méretezési módban nem támogatja az Azure-beli helyszíni virtuális gépeket. | Az Azure spot virtuálisgép-példányok használatához állítsa a hangelőkészítési módot a virtuálisgép-méretezési csoportra. |


**További lépések** További információ: [spot Virtual Machines](./spot-vms.md).