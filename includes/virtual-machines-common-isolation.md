---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: styli365
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: sttsinar
ms.custom: include file
ms.openlocfilehash: e22c2b7cb561e30e84ea5ede5481fbdc35be8cdf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100514927"
---
Az Azure-beli számítások olyan virtuálisgép-méreteket biztosítanak, amelyek egy adott hardvereszközhöz vannak elkülönítve, és egyetlen ügyfélhez vannak hozzárendelve. Az elkülönített méretek élőak és működnek az adott hardveres generáción, és a hardveres generáció kivonásakor elavulttá válik.

Az elkülönített virtuálisgép-méretek a legmegfelelőbbek olyan számítási feladatokhoz, amelyek nagy fokú elkülönítést igényelnek a többi ügyfél munkaterheléséhez olyan okokból, amelyek megfelelnek a megfelelőségi és szabályozási követelményeknek.  Az elkülönített méret kihasználása garantálja, hogy a virtuális gép az adott kiszolgálópéldány esetében csak egy fut. 


Továbbá, mivel az elkülönített méretű virtuális gépek nagy méretűek, az ügyfelek dönthetnek úgy, hogy az Azure-támogatás használatával osztják el a [virtuális gépek](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)erőforrásait.

A jelenlegi elkülönített virtuálisgép-ajánlatok a következők:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5


> [!NOTE]
> Az elkülönített virtuálisgép-méretek hardveres korlátozott élettartammal rendelkeznek. Részletekért lásd alább

## <a name="deprecation-of-isolated-vm-sizes"></a>Elkülönített virtuálisgép-méretek elavulása

Az elkülönített virtuálisgép-méretek hardveres korlátozott élettartammal rendelkeznek. Az Azure a méretek hivatalos elavult időpontját megelőzően 12 hónappal visszaküldi az emlékeztetőket, és egy frissített elkülönített ajánlatot biztosít a megfontoláshoz.

| Méret | Elkülönítés megszüntetésének dátuma | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | Május 15., 2020 |
| Standard_D15_v2<sup>1</sup>  | Május 15., 2020 |

<sup>1</sup>  a Standard_DS15_v2 és Standard_D15_v2 elkülönítési program részleteit lásd: gyakori kérdések


## <a name="faq"></a>GYIK
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>K: a méret kivonásra kerül, vagy csak az "elkülönítés" funkciót?
**A**: Ha a virtuális gép mérete nem rendelkezik az "i" alparancsfájllal, akkor a rendszer csak az "elkülönítés" szolgáltatást fogja kivonni. Ha nincs szükség elkülönítésre, nem kell végrehajtania a műveletet, és a virtuális gép továbbra is a várt módon fog működni. Ilyenek például a következők: Standard_DS15_v2, Standard_D15_v2, Standard_M128ms stb. Ha a virtuális gép mérete magában foglalja az "i" alszkriptet, akkor a méret megszűnik.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>K: van olyan állásidő, amikor a virtuális gép nem elszigetelt hardveren landol?
**A**: Ha nincs szükség az elkülönítésre, nincs szükség beavatkozásra, és nem lesz leállás.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>K: a nem elkülönített virtuális gépekre való áttéréshez bármilyen díjas különbözet vonatkozik?
**A**: nem

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>K: Mikor kell kivonni a többi elszigetelt méretet?
**A**: az elkülönített méret hivatalos elavulása előtt 12 hónappal az emlékeztetőket is biztosítjuk.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>K: Azure Service Fabric ügyfél vagyok az ezüst vagy az arany tartóssági szintjein. Hatással van ez a változás?
**A**: nem. A Service Fabric [tartóssági szintjei](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) által biztosított garanciák még a változás után is működni fognak. Ha más okokból fizikai hardveres elkülönítésre van szüksége, akkor továbbra is szükség lehet a fent ismertetett műveletek egyikére. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>K: milyen mérföldkövek vannak a D15_v2 vagy DS15_v2 elkülönítésének megszüntetéséhez? 
**A**: 
 
| Date | Művelet |
|---|---| 
| 2019. november 18. | D/DS15i_v2 rendelkezésre állása (TB, 1 éves RI) | 
| Május 14., 2020 | Az elmúlt nap, hogy megvásárolja a D/DS15i_v2 1 éves RI | 
| Május 15., 2020 | D/DS15_v2 elkülönítési garancia eltávolítva | 
| Május 15., 2021 | A D/DS15i_v2 kivonása (az összes ügyfél, kivéve, ha a D/DS15_v2 3 éves RI-t vásárolt, 2019. november 18. előtt)| 
| November 17., 2022 | A (z) D/DS15i_v2 kivonása a 3 éves beérkező 2019 DS15_v2 példányok esetében |

## <a name="next-steps"></a>Következő lépések

Az ügyfelek emellett dönthetnek úgy is, hogy a [beágyazott virtuális gépek Azure-támogatásának](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)használatával tovább alcsoportba helyezik az elkülönített virtuális gépek erőforrásait.
