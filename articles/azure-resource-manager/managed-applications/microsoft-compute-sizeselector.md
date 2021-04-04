---
title: SizeSelector FELHASZNÁLÓIFELÜLET-elem
description: Ismerteti a Microsoft. számítás. SizeSelector felhasználói felületi elemét Azure Portal. A virtuális gép méretének kiválasztásához használható.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d6408f8c08694ae681d302ae35f5778894091733
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063664"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft. számítás. SizeSelector FELHASZNÁLÓIFELÜLET-elem

Egy vagy több virtuálisgép-példány méretének kiválasztására szolgáló vezérlőelem.

## <a name="ui-sample"></a>Felhasználói felület mintája

A felhasználó az elem definíciójában alapértelmezett értékekkel látja el a választót.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector.png)

A vezérlő kiválasztása után a felhasználó az elérhető méretek kibővített nézetét láthatja.

![Microsoft. számítás. SizeSelector kibontva](./media/managed-application-elements/microsoft-compute-sizeselector-expanded.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

```json
"Standard_D1"
```

## <a name="remarks"></a>Megjegyzések

- `recommendedSizes` legalább egy méretnek kell lennie. A rendszer az első ajánlott méretet használja alapértelmezettként. Az elérhető méretek listája nem a javasolt állapot szerint van rendezve. A felhasználó az ajánlott állapot alapján kiválaszthatja az adott oszlopot.
- Ha a kiválasztott helyen nem érhető el javasolt méret, a rendszer automatikusan kihagyja a méretet. Ehelyett a következő javasolt méretet használjuk.
- `constraints.allowedSizes` és `constraints.excludedSizes` mindkettő nem kötelező, de nem használható egyszerre. Az elérhető méretek listája az [előfizetéshez tartozó elérhető virtuálisgép-méretek](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)meghívásával határozható meg. A nem megadott méret `constraints.allowedSizes` rejtett, és a nem megadott méret `constraints.excludedSizes` jelenik meg.
- `osPlatform` kötelező megadni, és lehet **Windows** vagy **Linux**. A virtuális gépek hardveres költségeinek meghatározására szolgál.
- `imageReference` a rendszer kihagyja az első féltől származó rendszerképeket, de a harmadik féltől származó képekhez van megadva. A virtuális gépek szoftveres költségeinek meghatározására szolgál.
- `count` az elem megfelelő szorzójának beállítására szolgál. Egy statikus értéket, például **2** vagy egy másik elemből származó dinamikus értéket támogat, például: `[steps('step1').vmCount]` . Az alapértelmezett érték **1**.
- A `numAvailabilityZonesRequired` lehet 1, 2 vagy 3.
- Alapértelmezés szerint `hideDiskTypeFilter` **hamis**. A lemez típusa szűrő lehetővé teszi a felhasználó számára az összes lemez típusának vagy csak SSD megjelenítését.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
