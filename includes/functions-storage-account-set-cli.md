---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839009"
---
Nyissa meg a local. Settings. JSON fájlt, és másolja a `AzureWebJobsStorage` értéket, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. Állítsa be a `AZURE_STORAGE_CONNECTION_STRING` környezeti változót a kapcsolódási sztringre a következő bash-parancs használatával:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Ha a kapcsolati karakterláncot a `AZURE_STORAGE_CONNECTION_STRING` környezeti változóban állítja be, akkor anélkül férhet hozzá a Storage-fiókhoz, hogy minden alkalommal hitelesítést kellene biztosítania.