---
title: Az Azure Storage integrálása az értesítések és a modell biztonsági mentéséhez
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan integrálhatja az Azure Storage-t leküldéses értesítések fogadására Custom Vision modellek betanítása vagy exportálása során. Az exportált modellek biztonsági másolata is menthető.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: 2afac0e25c394c9bf265daf1fe3ded3d763a7c61
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508503"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Az Azure Storage integrálása értesítésekhez és biztonsági mentésekhez

A Custom Vision-projektet egy Azure Blob Storage-várólistával integrálva leküldéses értesítéseket kaphat a projekt betanítási/exportálási tevékenységéről és a közzétett modellek biztonsági másolatáról. Ez a funkció akkor hasznos, ha el szeretné kerülni a szolgáltatás folyamatos lekérdezését az eredményekhez, amikor a hosszú műveletek futnak. Ehelyett a tárolási üzenetsor-értesítéseket integrálhatja a munkafolyamatba.

Ez az útmutató bemutatja, hogyan használhatja ezeket a REST API-kat a cURL használatával. HTTP-kérelmeket is használhat, például a Poster szolgáltatást a kérések kiadásához.

> [!NOTE]
> A leküldéses értesítések a **CreateProject** API opcionális _notificationQueueUri_ paramétere alapján változnak, és a modell biztonsági mentése megköveteli, hogy a választható _exportModelContainerUri_ paramétert is használja. Ez az útmutató mind a funkciók teljes készletét fogja használni.

## <a name="prerequisites"></a>Előfeltételek

- Egy Custom Vision erőforrás az Azure-ban. Ha még nem rendelkezik ilyennel, lépjen a Azure Portalra, és [hozzon létre egy új Custom Vision-erőforrást](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). Ez a funkció jelenleg nem támogatja a kognitív szolgáltatás erőforrásait (mindezt egyetlen kulcsban).
- Egy blob-tárolóval rendelkező Azure Storage-fiók. Ha segítségre van szüksége ennek a lépésnek a végrehajtásához, kövesse [Az Azure Storage Lab 1. gyakorlatát](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) .
* A [PowerShell 6.0](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)-s vagy újabb verziója, vagy egy hasonló parancssori alkalmazás.

## <a name="set-up-azure-storage-integration"></a>Az Azure Storage-integráció beállítása

Nyissa meg a Custom Vision képzési erőforrást a Azure Portal, válassza ki az **identitás** lapot, és engedélyezze a rendszerhez rendelt felügyelt identitást.

Ezután nyissa meg a tárolási erőforrást a Azure Portal. Lépjen a **hozzáférés-vezérlés (iam)** lapra, és vegyen fel egy szerepkör-hozzárendelést az egyes integrációs szolgáltatásokhoz:
* Válassza ki Custom Vision képzési erőforrását, és rendelje hozzá a **Storage blob adatközreműködői** szerepkört, ha azt tervezi, hogy a modell biztonsági mentési szolgáltatását használja. 
* Ezután válassza ki a Custom Vision képzési erőforrást, és rendelje hozzá a **Storage-várólista Adatközreműködőjét** , ha azt tervezi, hogy az értesítési várólista szolgáltatást használja.

> [!div class="mx-imgBorder"]
> ![Storage-fiók szerepkör-hozzárendelés hozzáadása lap](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>Integrációs URL-címek beolvasása

Ezután megkapja azokat az URL-címeket, amelyek lehetővé teszik a Custom Vision erőforrás számára a végpontok elérését.

Az értesítési várólista integrációs URL-címéhez nyissa meg a Storage-fiók **Queues (várólisták** ) lapját, adjon hozzá egy új várólistát, és mentse az URL-címet egy ideiglenes helyre.

> [!div class="mx-imgBorder"]
> ![Azure Storage-várólista lapja](./media/storage-integration/queue-url.png) 

A modell biztonsági mentési integrációs URL-címéhez nyissa meg a Storage-fiók **tárolók** lapját, és hozzon létre egy új tárolót. Ezután válassza ki, majd nyissa meg a **Tulajdonságok** lapot. Másolja az URL-címet egy ideiglenes helyre.
 
> [!div class="mx-imgBorder"]
> ![Azure Storage-tároló tulajdonságai lap](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Custom Vision projekt integrálása

Most, hogy már rendelkezik az integrációs URL-címekkel, létrehozhat egy új Custom Vision projektet, amely integrálja az Azure Storage szolgáltatásait. Egy meglévő projektet is frissíthet a szolgáltatások hozzáadásához.

### <a name="create-new-project"></a>Új projekt létrehozása

Ha meghívja a [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) API-t, adja hozzá a _ExportModelContainerUri_ és a _notificationQueueUri_ választható paramétereket. Rendelje hozzá az előző szakaszban kapott URL-értékeket. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

Ha `200/OK` választ kap, ez azt jelenti, hogy az URL-címek beállítása sikeresen megtörtént. A JSON-válaszban az URL-értékeket is látnia kell:

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Meglévő projekt frissítése

Meglévő projekt Azure Storage szolgáltatásbeli integrációval való frissítéséhez hívja meg a [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) API-t a frissíteni kívánt projekt azonosítójának használatával. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

Állítsa be a kérelem törzsét ( `body` ) a következő JSON formátumra, és töltse ki a megfelelő értékeket a _exportModelContainerUri_ és a _notificationQueueUri_:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

Ha `200/OK` választ kap, ez azt jelenti, hogy az URL-címek beállítása sikeresen megtörtént.

## <a name="verify-the-connection"></a>A kapcsolat ellenőrzése 

Az előző szakaszban szereplő API-hívásnak már új adatokat kellett elindítania az Azure Storage-fiókjában. 

A kijelölt tárolóban egy **CustomVision-TestPermission** mappában lévő tesztelési blobnak kell lennie. Ez a blob csak átmenetileg fog létezni.

Az értesítési várólistában a következő formátumban kell megjelennie a teszt értesítéseknek:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Esemény-értesítések beolvasása

Ha elkészült, hívja meg a [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API-t a projekten, hogy egy normál betanítási műveletet végezzen.

A Storage értesítési várólistájában értesítést kap a betanítás befejezése után:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"`Lehetséges, hogy a mező `"TrainingCompleted"` vagy `"TrainingFailed"` . A `"iterationId"` mező a betanított modell azonosítója.

## <a name="get-model-export-backups"></a>Modell-exportálási biztonsági másolatok letöltése

Ha elkészült, hívja meg a [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API-t egy betanított modell exportálásához egy adott platformon.

A kijelölt tárolóban megjelenik az exportált modell biztonsági másolata. A blob nevének formátuma a következőket fogja tartalmazni:

```
{projectId} - {iterationId}.{platformType}
```

Emellett értesítést fog kapni a várólistában az Exportálás befejeződése után. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"`Lehetséges, hogy a mező `"ExportCompleted"` vagy `"ExportFailed"` . A `"modelUri"` mező tartalmazza a tárolóban tárolt biztonsági mentési modell URL-címét, feltéve, hogy az elején az integrált üzenetsor-értesítések szerepelnek. Ha nem, akkor a `"modelUri"` mező a Custom Vision modell blobjának sas URL-címét jeleníti meg.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan másolhat és helyezhet át egy projektet Custom Vision erőforrások között. Ezután tekintse meg az API-referenciák dokumentációját, hogy megtudja, mit tehet a Custom Vision.
* [REST API dokumentáció (képzés)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [REST API dokumentáció (előrejelzés)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)
