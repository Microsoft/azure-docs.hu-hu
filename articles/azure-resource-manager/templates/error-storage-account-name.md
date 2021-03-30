---
title: A Storage-fiók neve hibák
description: Ismerteti a Storage-fiók nevének megadásakor előforduló hibákat.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76153972"
---
# <a name="resolve-errors-for-storage-account-names"></a>A Storage-fiókok neveivel kapcsolatos hibák elhárítása

Ez a cikk a Storage-fiókok telepítésekor esetlegesen felmerülő elnevezési hibákat ismerteti.

## <a name="symptom"></a>Hibajelenség

Ha a Storage-fiók neve tiltott karaktereket tartalmaz, a következőhöz hasonló hibaüzenetet kap:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

A Storage-fiókok esetében meg kell adnia az Azure-ban egyedi erőforrás nevét. Ha nem egyedi nevet ad meg, ilyen hibaüzenet jelenik meg:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Ha az előfizetéshez tartozó meglévő Storage-fiókkal azonos nevű Storage-fiókot telepít, de más helyet is megadhat, akkor hibaüzenet jelenik meg, amely jelzi, hogy a Storage-fiók már létezik egy másik helyen. Törölje a meglévő Storage-fiókot, vagy adja meg a meglévő Storage-fiókkal megegyező helyet.

## <a name="cause"></a>Ok

A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon. A névnek egyedinek kell lennie.

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a Storage-fiók neve egyedi. Egyedi nevet hozhat létre az elnevezési konvenció összefűzésével a [uniqueString](template-functions-string.md#uniquestring) függvény eredményével.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Győződjön meg arról, hogy a Storage-fiók neve nem hosszabb 24 karakternél. A [uniqueString](template-functions-string.md#uniquestring) függvény 13 karaktert ad vissza. Ha egy előtagot vagy a Postfix-t ÖSSZEFŰZ a **uniqueString** eredményéhez, adjon meg egy 11 karakternél rövidebb értéket.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The value to use for starting the storage account name."
    }
  }
}
```

Győződjön meg arról, hogy a Storage-fiók neve nem tartalmaz nagybetűket vagy speciális karaktereket.