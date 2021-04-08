---
title: A felhasználói felületi definícióra hivatkozó függvények létrehozása
description: Ismerteti azokat a függvényeket, amelyeket a rendszer a más objektumokra hivatkozó Azure Portal felhasználói felületi definícióinak összeállításakor használ.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096947"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition-hivatkozó függvények

A CreateUiDefinition-fájl tulajdonságai vagy környezete kimenetekre való hivatkozásakor használandó függvények.

## <a name="basics"></a>alapjai

Az [alapvető beállítások](create-uidefinition-overview.md#basics) lépésben meghatározott elem kimeneti értékeit adja vissza. Adja át az elem nevét paraméterként ehhez a függvényhez.

Ha más lépésekben szeretné beolvasni az elemek kimeneti értékeit, használja a [Steps ()](#steps) függvényt.

A következő példa az alapismeretek lépésben megnevezett elem kimenetét adja vissza `clusterName` :

```json
"[basics('clusterName')]"
```

A visszaadott értékek a beolvasott elem típusától függően változnak.

## <a name="location"></a>location

Az alapismeretek lépésben vagy az aktuális környezetben kiválasztott helyet adja vissza.

A következő példa egy értéket ad vissza, például `"westus"` :

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

Az alapvető beállítások lépésben vagy az aktuális környezetben kiválasztott resourceGroup adatait adja vissza.

A következő példa:

```json
"[resourceGroup()]"
```

A következő tulajdonságokat adja vissza:

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

Egy adott értéket lekérhet a dot jelöléssel.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>lépések

Egy megadott lépés elemeit adja vissza. Adja át a lépés nevét paraméterként a függvényhez. A visszaadott elemek közül bizonyos tulajdonságértékek beolvashatók.

Az alapvető lépések lépésben szereplő elemek kimeneti értékeinek lekéréséhez használja az alapszintű [()](#basics) függvényt.

A következő példa a nevű lépést adja vissza `vmParameters` . Ebben a lépésben egy nevű elem szerepel `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>előfizetést

Az alapvető beállítások lépésben vagy az aktuális környezetben kiválasztott előfizetés tulajdonságait adja vissza.

A következő példa:

```json
"[subscription()]"
```

A következő tulajdonságokat adja vissza:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Következő lépések

* A portál felületének fejlesztéséről az [ Azure által felügyelt alkalmazás létrehozásával kapcsolatosCreateUiDefinition.js](create-uidefinition-overview.md)című témakörben olvashat.
