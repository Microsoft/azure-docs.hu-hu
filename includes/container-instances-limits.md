---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87384827"
---
| Erőforrás | Korlát |
| --- | :--- |
| Standard SKU Container groups régiónként/előfizetés | 100<sup>1</sup> |
| Dedikált SKU Container Group régiónként/előfizetés | 0<sup>1</sup> |
| Tárolók száma tárolócsoportonként | 60 |
| Kötetek száma tárolócsoportonként | 20 |
| Standard SKU magok (CPU)/régió/előfizetés | 10<sup>1, 2</sup> | 
| Standard SKU magok (processzorok) K80 GPU-ra régiónként/előfizetés | 18<sup>1, 2</sup> |
| Standard SKU magok (processzorok) P100 vagy V100 GPU-ra régiónként, előfizetések szerint | 0<sup>1, 2</sup> |
| Portok száma IP-címenként | 5 |
| Tároló-példány naplójának mérete – futó példány | 4 MB |
| Tároló-példány naplójának mérete – leállított példány | 16 KB vagy 1 000 sor |
| Óránként létrehozott tárolók száma |300<sup>1</sup> |
| 5 percenként létrehozott tárolók száma | 100<sup>1</sup> |
| Óránként törölt tárolók száma | 300<sup>1</sup> |
| 5 percenként törölt tárolók száma | 100<sup>1</sup> |


<sup>1</sup> A határérték-növekedés igényléséhez hozzon létre egy [Azure-support Request][azure-support]. Az ingyenes előfizetések, például az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/) és [Az Azure diákoknak](https://azure.microsoft.com/offers/ms-azr-0170p/) nem jogosultak a limit vagy a kvóta növelésére. Ha ingyenes előfizetéssel rendelkezik [, az utólagos](../articles/cost-management-billing/manage/upgrade-azure-subscription.md) elszámolású előfizetésre válthat.<br />
<sup>2</sup> Az [utólagos elszámolású előfizetés alapértelmezett](https://azure.microsoft.com/offers/ms-azr-0003p/) korlátja. Más kategóriájú típusok esetén a korlát eltérő lehet.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
