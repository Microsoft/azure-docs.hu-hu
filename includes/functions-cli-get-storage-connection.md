---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673369"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Az Azure Storage-beli kapcsolatok karakterláncának beolvasása

Korábban létrehozott egy Azure Storage-fiókot, amelyet a Function alkalmazás használ. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ha letölti a beállítást a fájl *local.settings.jsjában* , akkor a függvény helyi futtatásakor ugyanazzal a fiókkal tud írni egy tárolási várólistára. 

1. A projekt gyökeréből futtassa a következő parancsot, és cserélje le a `<app_name>` függvényt az előző rövid útmutatóból származó Function alkalmazás nevére. Ez a parancs felülírja a fájlban lévő összes meglévő értéket.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Nyissa meg a *local.settings.jst* , és keresse meg a nevű értéket `AzureWebJobsStorage` , amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. A `AzureWebJobsStorage` jelen cikk más részeiben a nevet és a kapcsolatok karakterláncát kell használnia.

> [!IMPORTANT]
> Mivel *local.settings.jsaz* Azure-ból letöltött titkokat tartalmaz, mindig kizárják ezt a fájlt a forrás vezérlőelemből. A helyi functions projekttel létrehozott *. gitignore* fájl alapértelmezés szerint kizárja a fájlt.