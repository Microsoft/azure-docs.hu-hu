---
title: fájl belefoglalása
description: fájl belefoglalása
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 04/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 712b70960e09a9c2b0e7a998bc0bddbc28c1e112
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765880"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális gépek [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> régiónként. |25 000 régiónként. |
| Virtuálisgép-magok összesen, [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> régiónként. | Forduljon a támogatási szolgálathoz. |
| Virtuálisgép-sorozat, például a Dv2 és az F-magok [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> régiónként. | Forduljon a támogatási szolgálathoz. |
| [Társrendszergazdák](../articles/billing-add-change-azure-subscription-administrator.md) előfizetésenként |Korlátlan számú. |Korlátlan számú. |
| [Storage-fiókok](../articles/storage/common/storage-quickstart-create-account.md) régiónként és előfizetésenként |250 |250 |
| [Erőforráscsoportok](../articles/azure-resource-manager/resource-group-overview.md) előfizetésenként |980 |980 |
| [A rendelkezésre állási csoportok](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) előfizetésenként |2000 régiónként. |2000 régiónként. |
| Az Azure Resource Manager API-kérések mérete |4,194,304 bájtok száma. |4,194,304 bájtok száma. |
| Címkék előfizetésenként<sup>2</sup> |Korlátlan számú. |Korlátlan számú. |
| Egyedi címkeszámítások előfizetésenként<sup>2</sup> | 10,000 | 10,000 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Előfizetés-szintű telepítések](../articles/azure-resource-manager/deploy-to-subscription.md) / hely | 800<sup>4</sup> | 800 |

<sup>1</sup>alapértelmezett korlátai szerint eltérőek lehetnek, az ajánlat kategóriája ingyenes próbaverziója és az utólagos elszámolású csomagok, például és például Dv2, F és a g sorozat

<sup>2</sup>címkék előfizetésenként korlátlan számú alkalmazhat. Az erőforrásonként vagy erőforráscsoportonként alkalmazható címkék száma legfeljebb 15 lehet. Erőforrás-kezelő adja vissza egy [egyedi címkenevek és értékek listájáról](/rest/api/resources/tags) csak akkor, ha a címkék száma 10 000 vagy kevesebb az előfizetésben. Továbbra is keresheti erőforrás címkét, ha a szám meghaladja a 10 000-et.  

<sup>3</sup>ezek a funkciók nem lesznek az Azure-erőforráscsoportot és a Resource Manager szükséges.

<sup>4</sup>eléri a korlátot, 800 központi telepítések, ha törli a központi telepítések az előzményekben tekintheti át, hogy már nincs rá szükség. Az előfizetés-szintű telepítések törölheti [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) vagy [az üzembe helyezés törlése](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Virtuálisgép-magokra egy régió szerinti teljes korlát. Regionális mérete sorozat, mint a Dv2 és a F. határértéket is rendelkeznek Ezek a korlátok külön-külön érvényben vannak. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés 30 A1 virtuális gépet, vagy 30 D1 virtuális gépet, vagy a kettő kombinációját nem haladja meg az összesen 30 magot telepítheti. Együttes például 10 A1 virtuális gép és 20 D1 virtuális gép.  
> <!-- -->
> 
> 

