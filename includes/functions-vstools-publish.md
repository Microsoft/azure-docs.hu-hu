---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5b537b88052ce4042e346732f3dc63aaec6621cc
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100529679"
---
1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza a **Közzététel** lehetőséget, majd a **cél** területen válassza az **Azure** , majd a **tovább** lehetőséget.

1. Az **adott cél** esetében válassza az **Azure függvényalkalmazás (Windows)** lehetőséget, amely létrehoz egy Windows rendszeren futó Function alkalmazást.

1. A **Function példányban** válassza az **új Azure-függvény létrehozása... lehetőséget.** 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="Új Function App-példány létrehozása":::

1. Hozzon létre egy új példányt a következő táblázatban megadott értékek használatával:

    | Beállítás      | Érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. Fogadja el ezt a nevet, vagy adjon meg egy új nevet. Érvényes karakterek: `a-z` , `0-9` és `-` . |
    | **Előfizetés** | Az Ön előfizetése | A használandó előfizetés. Fogadja el ezt az előfizetést, vagy válasszon újat a legördülő listából. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** | Az erőforráscsoport neve |  Az erőforráscsoport, amelyben létre szeretné hozni a Function alkalmazást. Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új** lehetőséget egy új erőforráscsoport létrehozásához.|
    | **[Csomag típusa](../articles/azure-functions/functions-scale.md)** | Használat | Ha a projektet egy [felhasználási](../articles/azure-functions/consumption-plan.md)csomagban futó Function alkalmazásban teszi közzé, csak a functions-alkalmazás végrehajtásához kell fizetnie. Más üzemeltetési csomagok magasabb költségekkel járnak. |
    | **Hely** | Az App Service helye | Válasszon egy **helyet** a közeli [régióban](https://azure.microsoft.com/regions/) vagy más, a funkciókhoz hozzáférő szolgáltatásokhoz. |
    | **[Azure Storage](../articles/azure-functions/storage-considerations.md)** | Általános célú Storage-fiók | A functions futtatókörnyezet megköveteli egy Azure Storage-fiók megírását. Válassza az **új** lehetőséget az általános célú Storage-fiók konfigurálásához. Kiválaszthat egy meglévő fiókot is, amely megfelel a [Storage-fiókra vonatkozó követelményeknek](../articles/azure-functions/storage-considerations.md#storage-account-requirements).  |

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Válassza a **Létrehozás** lehetőséget, és hozzon létre egy Function alkalmazást és kapcsolódó erőforrásait az Azure-ban. Az erőforrás-létrehozás állapota az ablak bal alsó részén látható. 

1. Vissza a **functions-példányban** ellenőrizze, hogy be van-e jelölve **a Futtatás a csomagból fájl** . A Function alkalmazás üzembe helyezése a [zip-telepítéssel](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) , a [csomaghoz való futtatási](../articles/azure-functions/run-functions-from-deployment-package.md) móddal engedélyezve. Ez az ajánlott üzembe helyezési módszer a functions-projekthez, mert jobb teljesítményt eredményez. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Profil létrehozásának befejezése":::

1. Válassza a **Befejezés** lehetőséget, majd a közzététel lapon válassza a **Közzététel** lehetőséget, hogy az Azure-ban lévő új Function alkalmazásba telepítse a Project-fájlokat tartalmazó csomagot. 

    Miután az üzembe helyezés befejeződött, az Azure-beli Function alkalmazás gyökerének URL-címe megjelenik a **Közzététel** lapon. 
    
1.  A közzététel lapon válassza a **kezelés a Cloud Explorerben** lehetőséget. Ekkor megnyílik az új Function app Azure-erőforrás a Cloud Explorerben. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Sikeres közzétételt jelző üzenet":::
    
    A Cloud Explorer használatával megtekintheti a hely tartalmát, elindíthatja és leállíthatja a Function alkalmazást, és közvetlenül böngészhet az Azure-ban és a Azure Portalban használható alkalmazás-erőforrásokkal. 
