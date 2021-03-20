---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 733fc9620cbd17e5e4d8bb101c54ff646a06d6a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
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
