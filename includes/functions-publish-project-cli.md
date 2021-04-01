---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93425046"
---
## <a name="deploy-the-function-project-to-azure"></a>A Function projekt üzembe helyezése az Azure-ban

Miután sikeresen létrehozta a Function alkalmazást az Azure-ban, most már készen áll a helyi functions-projekt üzembe helyezésére az functions [Azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) parancs használatával.  

A következő példában cserélje le az `<APP_NAME>` alkalmazást az alkalmazás nevére.

```console
func azure functionapp publish <APP_NAME>
```

A közzétételi parancs a következő kimenethez hasonló eredményeket jelenít meg (egyszerűség kedvéért csonkítva):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
