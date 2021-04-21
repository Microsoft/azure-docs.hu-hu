---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 642989df8dca9d4ae121d80e30a9fb6d8dd06286
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799925"
---
### <a name="query-the-storage-queue"></a>A Storage-üzenetsor lekérdezése

Az paranccsal megtekintheti a fiókjában lévő [`az storage queue list`](/cli/azure/storage/queue#az_storage_queue_list) Storage-üzenetsorokat, az alábbi példában láthatóak szerint:

```azurecli-interactive
az storage queue list --output tsv
```

A parancs kimenete tartalmaz egy nevű üzenetsort, amely a függvény futtatáskor `outqueue` létrehozott üzenetsor.

Ezután a paranccsal tekintse meg az üzenetsorban lévő üzeneteket [`az storage message peek`](/cli/azure/storage/message#az_storage_message_peek) az alábbi példában láthatóak szerint:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A visszaadott sztringnek ugyanaznak kell lennie, mint a függvény tesztelésére küldött üzenetnek.

> [!NOTE]  
> Az előző példa dekódolja a base64 által visszaadott sztringet. Ennek az az oka, hogy a Queue Storage-kötések base64 sztringként írnak az Azure Storage-ba, és onnan [olvasnak.](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)