---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 329db6b3fc0bd6d11e5fbac9472aa03899caec2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050226"
---
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz egy paramétert, amely az összes paraméter értékét tartalmazza.
Meg kell határoznia a paramétert azokhoz az értékekhez, amelyek a telepített projekttől függően változnak, vagy amely az Ön által telepített környezet alapján változik. Ne definiáljon paramétereket olyan értékekhez, amelyek mindig azonosak maradnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

A paraméterek meghatározásakor a **allowedValues** mezőben adhatja meg, hogy a felhasználó milyen értékeket biztosíthat az üzembe helyezés során. Ha a telepítés során nem ad meg értéket, a **defaultValue** mező használatával rendeljen hozzá egy értéket a paraméterhez.

A sablon minden paraméterét le fogjuk írni.

### <a name="sitename"></a>siteName
A létrehozni kívánt webalkalmazás neve.

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hostingPlanName
A webalkalmazás üzemeltetéséhez használni kívánt App Service-csomag neve.

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>SKU
A üzemeltetési csomag díjszabási szintje.

```config
"sku": {
  "type": "string",
  "allowedValues": [
    "F1",
    "D1",
    "B1",
    "B2",
    "B3",
    "S1",
    "S2",
    "S3",
    "P1",
    "P2",
    "P3",
    "P4"
  ],
  "defaultValue": "S1",
  "metadata": {
    "description": "The pricing tier for the hosting plan."
  }
}
```

A sablon meghatározza a paraméter számára engedélyezett értékeket, és az alapértelmezett értéket (S1) rendeli hozzá, ha nincs megadva érték.

### <a name="workersize"></a>workerSize
A üzemeltetési csomag példányának mérete (kis, közepes vagy nagy).

```config
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

A sablon meghatározza a paraméter számára engedélyezett értékeket (0, 1 vagy 2), és az alapértelmezett értéket (0) rendeli hozzá, ha nincs megadva érték. Az értékek kis, közepes és nagy értéknek felelnek meg.

