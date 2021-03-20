---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77474232"
---
### <a name="query-the-storage-queue"></a>A tárolási várólista lekérdezése

A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) paranccsal megtekintheti a fiókban lévő tárolási várólistákat, ahogy az alábbi példában is látható:

```azurecli-interactive
az storage queue list --output tsv
```

A parancs kimenete tartalmaz egy nevű várólistát `outqueue` , amely a függvény futtatásakor létrehozott üzenetsor.

Ezután a parancs használatával [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) tekintse meg az ebben a várólistában lévő üzeneteket, az alábbi példában látható módon:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A visszaadott karakterláncnak meg kell egyeznie a függvény teszteléséhez küldött üzenettel.

> [!NOTE]  
> Az előző példa dekódolja a visszaadott karakterláncot a Base64-ből. Ennek az az oka, hogy a várólista-tárolási kötések az Azure Storage-ban [Base64-karakterláncként](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)írnak és olvashatók.