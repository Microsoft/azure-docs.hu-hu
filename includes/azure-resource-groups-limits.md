---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: 543aa50d72de5a06a9a1c7ac88ac5ecae993bc9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98697954"
---
| Erőforrás | Korlát |
| --- | --- |
| Erőforrások [erőforrás-csoportonként](../articles/azure-resource-manager/management/overview.md#resource-groups) | Az erőforrások nem korlátozódnak az erőforráscsoporthoz. Ehelyett egy erőforráscsoport erőforrástípus van korlátozva. Lásd a következő sort. |
| Erőforrások erőforrás-csoportonként, erőforrás-típus szerint |800 – egyes erőforrástípusok túllépik a 800 korlátot. Tekintse [meg az erőforrásokat, amelyek erőforráscsoport esetén nem korlátozódnak 800 példányra](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Üzemelő példányok erőforráscsoport szerint az üzembe helyezési előzményekben |800<sup>1</sup> |
| Erőforrások üzembe helyezése |800 |
| Felügyeleti zárolások egyedi [hatókör](../articles/azure-resource-manager/management/overview.md#understand-scope) szerint  |20 |
| Címkék száma erőforrás vagy erőforráscsoport szerint |50 |
| Címke kulcsának hossza |512 |
| Címke értékének hossza |256 |

<sup>1</sup> Az üzembe helyezések automatikusan törlődnek az előzményekből a korlát közelében. Az üzembe helyezési előzményekből származó bejegyzések törlése nem befolyásolja az üzembe helyezett erőforrásokat. További információ: [automatikus törlések az üzembe helyezési előzményekből](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Sablonok korlátai

| Érték | Korlát |
| --- | --- |
| Paraméterek |256 |
| Változók |256 |
| Erőforrások (beleértve a példányszámot is) |800 |
| Kimenetek |64 |
| Sablon kifejezése |24 576 karakter |
| Exportált sablonok erőforrásai |200 |
| Sablon mérete |4 MB |
| Paraméter fájlmérete |64 KB |

Az egyes sablonokra vonatkozó korlátokat egy beágyazott sablon használatával lehet meghaladni. További információ: a [csatolt sablonok használata az Azure-erőforrások telepítésekor](../articles/azure-resource-manager/templates/linked-templates.md). A paraméterek, változók és kimenetek számának csökkentése érdekében több értéket is egyesítheti egy objektumban. További információ: [objektumok paraméterként](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).