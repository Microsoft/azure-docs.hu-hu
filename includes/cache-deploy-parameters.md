---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67178928"
---
### <a name="cacheskuname"></a>cacheSKUName

A Redis új Azure cache díjszabási szintje.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

A sablon meghatározza azokat az értékeket, amelyek engedélyezve vannak ehhez a paraméterhez (alapszintű, standard vagy prémium), és ha nincs megadva érték, a rendszer egy alapértelmezett értéket rendel hozzá (alapszintű). Az alapszintű egyetlen csomópontot biztosít, amelynek több mérete 53 GB-ig érhető el. A standard kétcsomópontos elsődleges/replikát biztosít, több méretben, 53 GB és 99,9%-os SLA-val.

### <a name="cacheskufamily"></a>cacheSKUFamily

Az SKU családja.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Az Redis-példányhoz tartozó új Azure cache mérete.

Az alapszintű és a standard családok esetében:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

A prémium érték gyorsítótárának kapacitása azonos, kivéve, ha az engedélyezett értékek értéke 1 és 5 közötti, és nem 0 és 6 közötti.

A sablon a paraméter számára engedélyezett egész értékeket határozza meg (0 – 6 az alapszintű és standard családok esetében, 1 – 5 a prémium család esetében). Ha nincs megadva érték, a sablon az alapszintű és standard, a prémium szintű 1 értékhez rendeli a 0 értéket.

Az értékek a következő gyorsítótár-méreteknek felelnek meg:

| Érték | Alapszintű és standard<br>gyorsítótár mérete | Prémium<br>gyorsítótár mérete |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (alapértelmezett)                 | n.a.                   |
| 1     | 1 GB                             | 6 GB (alapértelmezett)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n.a.                   |
