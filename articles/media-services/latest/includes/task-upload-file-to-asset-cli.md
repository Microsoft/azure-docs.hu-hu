---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88658418"
---
<!-- ### Upload files with the CLI -->

A következő parancs egyetlen fájlt tölt fel.  

Módosítsa a következő értékeket:

1. Váltson a `/path/to/file` fájl helyi elérési útjára.  
1. Váltson `MyContainer` át egy olyan eszközre, amelyet korábban az eszköz azonosítója (nem a név) alapján hozott létre.
1. Váltson `MyBlob` arra a névre, amelyet a feltöltött fájlhoz használni szeretne.
1. Váltson `MyStorageAccountName` át a használt Storage-fiók nevére.
1. Váltson `MyStorageAccountKey` a Storage-fiók hozzáférési kulcsára.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
