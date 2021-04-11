---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed5f94bdc372e317e9e312db07665d233b1b4139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467278"
---
Nyissa meg a Azure Portal, és <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" hozzon létre egy új űrlap-felismerő erőforrást " target="_blank"> egy új űrlap-felismerő erőforrás létrehozásával </a> . A **Létrehozás** ablaktáblán adja meg a következő adatokat:

| Projekt részletei   | Leírás   |
|--|--|
| **Előfizetés** | Válassza ki azt az Azure-előfizetést, amelyhez hozzáférés van megadva. |
| **Erőforráscsoport** | Az az [Azure-erőforráscsoport](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , amely az erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |
| **Régió** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
| **Név** | Az erőforrás leíró neve. Javasoljuk, hogy használjon leíró nevet, például *MyNameFormRecognizer*. |
| **Tarifacsomag** | Az erőforrás díja a kiválasztott árképzési szinttől és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.

> [!NOTE]
> Általában amikor a Azure Portal egy kognitív szolgáltatásbeli erőforrást hoz létre, lehetősége van egy több szolgáltatásból álló előfizetési kulcs (több kognitív szolgáltatásban használt) vagy egy egyszolgáltatásos előfizetési kulcs (csak egy adott kognitív szolgáltatás használatával) létrehozására. A többszolgáltatásos előfizetés azonban jelenleg nem tartalmaz űrlap-felismerőt.

Ha az űrlap-felismerő erőforrás üzembe helyezése befejeződött, keresse meg és válassza ki a portál **minden erőforrás** listájából. A kulcs és a végpont az erőforrás kulcs és végpont lapján, az erőforrás-kezelés területen található. A továbbítás előtt mentse mindkettőt egy ideiglenes helyre.
