---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "72329590"
---
Már létrehozott egy Function alkalmazást az Azure-ban, a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az alkalmazás beállításait a fájl local.settings.js. 

A projekt gyökeréből futtassa a következő Azure Functions Core Tools parancsot, hogy letöltse a beállításokat a local.settings.jsbe, lecserélve a `<APP_NAME>` függvény alkalmazásának nevére az előző cikkből:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Előfordulhat, hogy be kell jelentkeznie az Azure-fiókjába.

> [!IMPORTANT]  
> Ez a parancs felülírja a meglévő beállításokat az Azure-beli Function alkalmazás értékeivel.  
>
> Mivel titkokat tartalmaz, a fájl local.settings.jssoha nem kerül közzétételre, és ki kell zárni a forrás-vezérlőelemből.  
> 

Szüksége lesz az értékre `AzureWebJobsStorage` , amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.
