---
title: Virtuális gépek elkülönítése az Azure-ban
description: Ismerje meg, hogyan működik a virtuális gép elkülönítése az Azure-ban.
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/18/2019
ms.author: sttsinar
ms.openlocfilehash: d6a77cac049ef32949eeba4cbd6fe9217100a360
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666840"
---
# <a name="virtual-machine-isolation-in-azure"></a>Virtuális gépek elkülönítése az Azure-ban

Az Azure-beli számítások olyan virtuálisgép-méreteket biztosítanak, amelyek egy adott hardvereszközhöz vannak elkülönítve, és egyetlen ügyfélhez vannak hozzárendelve. Az elkülönített méretek élőak és működnek az adott hardveres generáción, és a hardveres generáció kivonásakor elavulttá válik.

Az elkülönített virtuálisgép-méretek a legmegfelelőbbek olyan számítási feladatokhoz, amelyek nagy fokú elkülönítést igényelnek a többi ügyfél munkaterheléséhez olyan okokból, amelyek megfelelnek a megfelelőségi és szabályozási követelményeknek.  Az elkülönített méret kihasználása garantálja, hogy a virtuális gép az adott kiszolgálópéldány esetében csak egy fut. 


Továbbá, mivel az elkülönített méretű virtuális gépek nagy méretűek, az ügyfelek dönthetnek úgy, hogy az Azure-támogatás használatával osztják el a [virtuális gépek](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)erőforrásait.

A jelenlegi elkülönített virtuálisgép-ajánlatok a következők:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2

> [!NOTE]
> Az elkülönített virtuálisgép-méretek hardveres korlátozott élettartammal rendelkeznek. Részletekért lásd alább

## <a name="deprecation-of-isolated-vm-sizes"></a>Elkülönített virtuálisgép-méretek elavulása
Mivel az elkülönített virtuálisgép-méretek hardveres korlátok, az Azure a méretek hivatalos elavulása előtt 12 hónappal előre emlékezteti az emlékeztetőket.  Az Azure a következő hardveres verziónál is frissített elkülönített méretet kínál, amelyet az ügyfél fontolóra vehet a számítási feladatok áthelyezéséhez.

| Méret | Elkülönítés megszüntetésének dátuma | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | Május 15., 2020 |
| Standard_D15_v2<sup>1</sup>  | Május 15., 2020 |

<sup>1</sup>  a Standard_DS15_v2 és Standard_D15_v2 elkülönítési program részleteit lásd: gyakori kérdések


## <a name="faq"></a>GYIK
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>K: a méret megszűnik, vagy csak az "elkülönítés" funkció lesz?
**A**: Ha a virtuális gép mérete nem rendelkezik az "i" alparancsfájllal, akkor a rendszer csak az "elkülönítés" szolgáltatást fogja kivonni. Ha nincs szükség elkülönítésre, nem kell végrehajtania a műveletet, és a virtuális gép továbbra is a várt módon fog működni. Ilyenek például a következők: Standard_DS15_v2, Standard_D15_v2, Standard_M128ms stb. Ha a virtuális gép mérete magában foglalja az "i" alszkriptet, akkor a méret megszűnik.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>K: van olyan állásidő, amikor a virtuális gép nem elszigetelt hardveren landol?
**A**: Ha nincs szükség az elkülönítésre, nincs szükség beavatkozásra, és nem lesz leállás.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>K: a nem elkülönített virtuális gépekre való áttéréshez bármilyen díjas különbözet vonatkozik?
**A**: nem

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>K: Mikor kell kivonni a többi elszigetelt méretet?
**A**: az elkülönített méret hivatalos elavulása előtt 12 hónappal az emlékeztetőket is biztosítjuk.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>K: Azure Service Fabric ügyfél vagyok az ezüst vagy az arany tartóssági szintjein. Hatással van ez a változás?
**A**: nem. A Service Fabric [tartóssági szintjei](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) által biztosított garanciák még a változás után is működni fognak. Ha más okokból fizikai hardveres elkülönítésre van szüksége, akkor továbbra is szükség lehet a fent ismertetett műveletek egyikére. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>K: milyen mérföldkövek vannak a D15_v2 vagy DS15_v2 elkülönítésének megszüntetéséhez? 
**A**: 
 
| Dátum | Művelet |
|---|---| 
| 2019. november 18. | D/DS15i_v2 rendelkezésre állása (TB, 1 éves RI) | 
| Május 14., 2020 | Az elmúlt nap, hogy megvásárolja a D/DS15i_v2 1 éves RI | 
| Május 15., 2020 | D/DS15_v2 elkülönítési garancia eltávolítva | 
| Május 15., 2021 | A D/DS15i_v2 kivonása (az összes ügyfél, kivéve, ha a D/DS15_v2 3 éves RI-t vásárolt, 2019. november 18. előtt)| 
| November 17., 2022 | A (z) D/DS15i_v2 kivonása a 3 éves beérkező 2019 DS15_v2 példányok esetében |

## <a name="next-steps"></a>Következő lépések

Az ügyfelek emellett dönthetnek úgy is, hogy a [beágyazott virtuális gépek Azure-támogatásának](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)használatával tovább alcsoportba helyezik az elkülönített virtuális gépek erőforrásait.
