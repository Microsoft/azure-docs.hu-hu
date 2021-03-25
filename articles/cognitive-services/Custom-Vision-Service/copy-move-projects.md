---
title: Custom Vision projektek másolása és áthelyezése
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan másolhatja és helyezheti át Custom Vision projektjeit a ExportProject és a ImportProject API használatával.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 1def60843df55a80e8b7d28d7eb61e262a560111
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044150"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Custom Vision projektek másolása és áthelyezése

Miután létrehozta és betanított egy Custom Vision projektet, érdemes lehet átmásolni a projektet egy másik erőforrásba. Tegyük fel például, hogy egy projektet a fejlesztésből éles környezetbe kíván áthelyezni, vagy egy projektről egy másik Azure-régióban lévő fiókra szeretne biztonsági másolatot készíteni az adatbiztonság növelése érdekében.

A **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** és a **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** API-k ezt a forgatókönyvet teszik lehetővé azáltal, hogy lehetővé teszik projektek másolását egy Custom Vision fiókból másokba. Ez az útmutató bemutatja, hogyan használhatja ezeket a REST API-kat a cURL használatával. HTTP-kérelmeket is használhat, például a Poster szolgáltatást a kérések kiadásához.

## <a name="business-scenarios"></a>Üzleti forgatókönyvek

Ha az alkalmazás vagy az üzlet egy Custom Vision-projekttől függ, javasoljuk, hogy egy másik régióba másolja a modellt egy másik Custom Vision-fiókba. Ha pedig regionális leállás következik be, a projekthez a másolt régióban férhet hozzá.

##  <a name="prerequisites"></a>Előfeltételek

- Két Azure Custom Vision-erőforrás. Ha nem rendelkezik ezekkel, lépjen a Azure Portalra, és [hozzon létre egy új Custom Vision-erőforrást](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- A Custom Vision erőforrások betanítási kulcsainak és végpontjának URL-címei. Ezek az értékek az erőforrás **Áttekintés** lapján találhatók a Azure Portal.
- Létrehozott Custom Vision projekt. Ehhez útmutatást az [osztályozó](./getting-started-build-a-classifier.md) létrehozása című cikkben talál.
* A [PowerShell 6.0](/powershell/scripting/install/installing-powershell-core-on-windows)-s vagy újabb verziója, vagy egy hasonló parancssori segédprogram.

## <a name="process-overview"></a>Folyamat áttekintése

A projektek másolásának folyamata a következő lépésekből áll:

1. Először szerezze be a projekt AZONOSÍTÓját a másolni kívánt forrás fiókban.
1. Ezt követően hívja meg a **ExportProject** API-t a forrás fiókjának projekt azonosítója és a betanítási kulcsa alapján. Egy ideiglenes jogkivonat-karakterláncot kap.
1. Ezután a **ImportProject** API-t hívja meg a jogkivonat-karakterlánc és a célként megadott fiók betanítási kulcsa alapján. A projekt ekkor megjelenik a célként megadott fiókban.

## <a name="get-the-project-id"></a>A projekt AZONOSÍTÓjának beolvasása

Először hívja meg a **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** , és tekintse meg a meglévő Custom Vision projektek listáját és azonosítóit. Használja a forrás fiókjának betanítási kulcsát és végpontját.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

A rendszer választ kap a `200\OK` projektekről és azok metaadatairól a törzsben. Az `"id"` érték a következő lépésekhez másolandó karakterlánc.

```json
[
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
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>A projekt exportálása

Hívja meg a **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** a projekt azonosítója és a forrásként szolgáló betanítási kulcs és végpont használatával.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

`200/OK`Az exportált projektre és egy hivatkozási sztringre vonatkozó metaadatokkal kapcsolatos választ kaphat `"token"` . Másolja a jogkivonat értékét.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>A projekt importálása

Hívja meg a **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** a cél betanítási kulcs és végpont, valamint a hivatkozási token használatával. Megadhatja a projekt nevét is az új fiókjában.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

Választ kaphat az `200/OK` újonnan importált projekt metaadataira.

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
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan másolhat és helyezhet át egy projektet Custom Vision erőforrások között. Ezután tekintse meg az API-referenciák dokumentációját, hogy megtudja, mit tehet a Custom Vision.
* [REST API dokumentáció](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)