---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 397dd3d16fa994df29a08ff9095b4c7c6c4af815
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510765"
---
| Erőforrás | Korlát |
| --- | --- |
| Virtuális gépek [előfizetésenként](https://azure.microsoft.com/pricing/) |25 000<sup>1</sup> régiónként. |
| Virtuálisgép-magok összesen, [előfizetésenként](https://azure.microsoft.com/pricing/) |régiónként 20<sup>1</sup> . A korlát növeléséhez forduljon az ügyfélszolgálathoz. |
| Azure spot VM-alapú teljes mag/ [előfizetés](https://azure.microsoft.com/pricing/) |régiónként 20<sup>1</sup> . A korlát növeléséhez forduljon az ügyfélszolgálathoz. |
| VM/sorozat, például Dv2 és F, mag/ [előfizetés](https://azure.microsoft.com/pricing/) |régiónként 20<sup>1</sup> . A korlát növeléséhez forduljon az ügyfélszolgálathoz. |
| [Rendelkezésre állási](../articles/virtual-machines/availability-set-overview.md) csoportok/előfizetés |2 500 régiónként. |
| Virtuális gépek rendelkezésre állási készlete | 200 |
| [Proximity elhelyezési csoportok](../articles/virtual-machines/windows/proximity-placement-groups-portal.md) / [erőforráscsoport](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 | 
| Tanúsítvány/rendelkezésre állási csoport | 199<sup>2</sup> |
| Tanúsítványok/előfizetés |Korlátlan<sup>3</sup> |

<sup>1</sup> az alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverziós és az utólagos elszámolású, valamint az adatsorozatok, például a Dv2, az F és a G. A Nagyvállalati Szerződés-előfizetések alapértelmezett értéke például 350.

<sup>2</sup> a tulajdonságok, például az SSH nyilvános kulcsok is tanúsítványként lesznek leküldve, és a korlátnak számítanak. A korlát megkerüléséhez használja a [Windows Azure Key Vault bővítményét](../articles/virtual-machines/extensions/key-vault-windows.md) vagy a Linux rendszerhez készült [Azure Key Vault-bővítményt](../articles/virtual-machines/extensions/key-vault-linux.md) a tanúsítványok telepítéséhez.

<sup>3</sup> Azure Resource Manager a tanúsítványokat a Azure Key Vault tárolja. Az előfizetéshez tartozó tanúsítványok száma korlátlan. Üzemelő példányok 1 MB-os korlátja, amely egyetlen virtuális gépről vagy rendelkezésre állási csoportból áll.



> [!NOTE]
> A virtuális gépek magjai a regionális teljes korláttal rendelkeznek. Emellett a régión belüli méretre (például Dv2 és F) is korlátozva van. Ezeket a korlátokat külön kényszeríti a rendszer. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés 30 a1-es virtuális gépet vagy 30 D1 virtuális gépet telepíthet, vagy a kettő kombinációja nem haladhatja meg az összes 30 magot. Egy kombináció például 10 a1 virtuális gép és 20 D1 virtuális gép.  
> <!-- -->
>