---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 733fc9620cbd17e5e4d8bb101c54ff646a06d6a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050219"
---
### <a name="app-service-plan"></a>App Service-csomag
Létrehozza a webalkalmazás üzemeltetéséhez szükséges szolgáltatási csomagot. Adja meg a csomag nevét a **hostingPlanName** paraméterrel. A csomag helye megegyezik az erőforráscsoport helyével. Az árképzési szintet és a feldolgozói méretet a **SKU** és a **workerSize** paraméterben határozzák meg

```config
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('hostingPlanName')]",
  "type": "Microsoft.Web/serverfarms",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[parameters('sku')]",
    "capacity": "[parameters('workerSize')]"
  },
  "properties": {
    "name": "[parameters('hostingPlanName')]"
  }
},
```
