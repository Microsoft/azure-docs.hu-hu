---
title: StorageAccountSelector FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Storage. StorageAccountSelector felhasználói felületi elemét ismerteti.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: fe70c42387e9dedea8943b5dcce04a1f9ded5190
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87033289"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft. Storage. StorageAccountSelector FELHASZNÁLÓIFELÜLET-elem

Egy új vagy meglévő Storage-fiók kiválasztására szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

A vezérlő megjeleníti az alapértelmezett értéket.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft-storage-storageaccountselector.png)

A vezérlő lehetővé teszi, hogy a felhasználó új Storage-fiókot hozzon létre, vagy válasszon ki egy meglévő Storage-fiókot.

![Microsoft. Storage. StorageAccountSelector új](./media/managed-application-elements/microsoft-storage-storageaccountselector-new.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Megjegyzések

- Ha meg van adva, a `defaultValue.name` rendszer automatikusan érvényesíti az egyediséget. Ha a Storage-fiók neve nem egyedi, a felhasználónak másik nevet kell megadnia, vagy egy meglévő Storage-fiókot kell választania.
- Az alapértelmezett értéke `defaultValue.type` **Premium_LRS**.
- A nem meghatározott típusok `constraints.allowedTypes` rejtettek, és az itt megadott típusok `constraints.excludedTypes` láthatók. `constraints.allowedTypes` és `constraints.excludedTypes` mindkettő nem kötelező, de nem használható egyszerre.
- Ha az `options.hideExisting` értéke **igaz**, a felhasználó nem választhat meglévő Storage-fiókot. Az alapértelmezett érték: **hamis**.

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
