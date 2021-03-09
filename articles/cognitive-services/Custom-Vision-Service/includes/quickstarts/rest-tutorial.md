---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: 76441e6c5627f4e48d77d67409ef05891c7cc1ac
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511086"
---
Ismerkedjen meg a Custom Vision REST APIával. Az alábbi lépéseket követve meghívhatja az API-t, és felépítheti a rendszerkép besorolási modelljét. Létrehoz egy projektet, címkéket ad hozzá, betanítja a projektet, és a projekt előrejelzési végpontjának URL-címét használja a programozott teszteléshez. Ez a példa sablonként használható a saját rendszerkép-felismerő alkalmazás létrehozásához.

> [!NOTE]
> A Custom Vision a legegyszerűbben az ügyféloldali kódtár SDK-n vagy a [böngészőalapú útmutatón](../../get-started-build-detector.md)keresztül lehet használni.

A .NET-hez készült Custom Vision ügyféloldali kódtára a következőre használható:

* Új Custom Vision-projekt létrehozása
* Címkék hozzáadása a projekthez
* Képek feltöltése és címkézése
* A projekt betanítása
* Az aktuális iteráció közzététele
* Az előrejelzési végpont tesztelése

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" hozzon létre egy Custom Vision erőforrást egy "  target="_blank"> Custom Vision erőforrás létrehozásához </a> a Azure Portal egy képzési és előrejelzési erőforrás létrehozásához, valamint a kulcsok és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Az alkalmazás Custom Visionhoz való összekapcsolásához szüksége lesz a létrehozott erőforrások kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* A [PowerShell 6.0](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)-s vagy újabb verziója, vagy egy hasonló parancssori alkalmazás.


## <a name="create-a-new-custom-vision-project"></a>Új Custom Vision-projekt létrehozása

A rendszerkép-besorolási projekt létrehozásához a következőhöz hasonló parancsot kell használnia. A létrehozott projekt megjelenik a [Custom Vision webhelyén](https://customvision.ai/).


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Másolja a parancsot egy szövegszerkesztőbe, és végezze el a következő módosításokat:

* A helyére írja `{subscription key}` be az érvényes Face előfizetési kulcsot.
* A helyére írja `{endpoint}` be az előfizetési kulcsnak megfelelő végpontot.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* Cserélje le a helyére `{name}` a projekt nevét.
* Más URL-paraméterek megadásával beállíthatja, hogy a projekt milyen típusú modellt fog használni. A lehetőségekért tekintse meg a [CREATPROJECT API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) -t.

A következőhöz hasonló JSON-választ fog kapni. Mentse a `"id"` projekt értékét egy ideiglenes helyre.

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

## <a name="add-tags-to-the-project"></a>Címkék hozzáadása a projekthez

A következő paranccsal adhatja meg azokat a címkéket, amelyeken a modellt be fogja tanítani.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Ismét szúrja be a saját kulcs és végpont URL-címét.
* Cserélje le a változót `{projectId}` a saját projekt-azonosítójával.
* Cserélje le a helyére a `{name}` használni kívánt címke nevét.

Ismételje meg a folyamatot a projektben használni kívánt összes címkénél. Ha a példaként megadott képeket használja, adja hozzá a címkéket `"Hemlock"` és a-t `"Japanese Cherry"` .

A következőhöz hasonló JSON-választ fog kapni. Mentse az `"id"` egyes címkék értékét egy ideiglenes helyre.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ezután töltse le a projekthez tartozó mintaképeket. Mentse a [Sample images mappa](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) tartalmát a helyi eszközre.

> [!NOTE]
> Szüksége van egy szélesebb körű rendszerképekre a képzés befejezéséhez? A Microsoft garázs-projekt, amely lehetővé teszi, hogy beszerezze a betanítási célokra szánt lemezképek készleteit. A képek összegyűjtése után letöltheti őket, majd a szokásos módon importálhatja őket a Custom Vision-projektbe. További információért látogasson el az [adattárház oldalára](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) .

A következő parancs használatával töltse fel a képeket, és alkalmazza a címkéket; egyszer a "Hemlock" és a "Japanese Cherry" rendszerképekhez. További lehetőségekért tekintse meg a [képek létrehozása az adatok API-ból](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) című témakört.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Ismét szúrja be a saját kulcs és végpont URL-címét.
* Cserélje le a változót `{projectId}` a saját projekt-azonosítójával.
* Cserélje le `{tagArray}` egy címke azonosítóját.
* Ezután töltse fel a kérelem törzsét a címkével ellátni kívánt képek bináris adataival.

## <a name="train-the-project"></a>A projekt betanítása

Ez a módszer a modellt a feltöltött címkézett képekre, és az aktuális projekt-iteráció AZONOSÍTÓját adja vissza.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Ismét szúrja be a saját kulcs és végpont URL-címét.
* Cserélje le a változót `{projectId}` a saját projekt-azonosítójával.
* Cserélje le `{tagArray}` egy címke azonosítóját.
* Ezután töltse fel a kérelem törzsét a címkével ellátni kívánt képek bináris adataival.
* Igény szerint más URL-paramétereket is használhat. A lehetőségekért tekintse meg a [Train Project](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API-t.

> [!TIP]
> Betanítás kiválasztott címkékkel
>
> Igény szerint betaníthatja az alkalmazott címkék egy részhalmazát. Ezt akkor érdemes megtenni, ha még nem alkalmazta elég bizonyos címkéket, de másokkal is rendelkezik. Adja hozzá a nem kötelező JSON-tartalmat a kérelem törzséhez. Töltse fel a `"selectedTags"` tömböt a használni kívánt címkék azonosítói alapján.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

A JSON-válasz a betanított projektre vonatkozó információkat tartalmaz, beleértve az iterációs azonosítót ( `"id"` ). Mentse ezt az értéket a következő lépéshez.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Ezzel a módszerrel elérhetővé válik a modell aktuális iterációja a lekérdezéshez. A visszaadott modell neve hivatkozásként használható az előrejelzési kérelmek küldéséhez. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Ismét szúrja be a saját kulcs és végpont URL-címét.
* Cserélje le a változót `{projectId}` a saját projekt-azonosítójával.
* Cserélje le az értéket az `{iterationId}` előző lépésben visszaadott azonosítóra.
* A helyére írja be `{publishedName}` azt a nevet, amelyet hozzá szeretne rendelni az előrejelzési modellhez.
* Cserélje le a értékét `{predictionId}` a saját előrejelzési erőforrás-azonosítójával. Az előrejelzési erőforrás **Áttekintés** lapján található az **előfizetés-azonosítóként** felsorolt Azure Portal.
* Igény szerint más URL-paramétereket is használhat. Lásd a [közzétételi iteráció](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5) API-t.

## <a name="test-the-prediction-endpoint"></a>Az előrejelzési végpont tesztelése

Végezetül ezzel a paranccsal tesztelheti a betanított modellt úgy, hogy feltölt egy új rendszerképet a címkékre való besoroláshoz. Használhatja a korábban letöltött mintaadatok "teszt" mappájában található rendszerképet.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Ismét szúrja be a saját kulcs és végpont URL-címét.
* Cserélje le a változót `{projectId}` a saját projekt-azonosítójával.
* Cserélje le az `{publishedName}` nevet az előző lépésben használt névre.
* Adja hozzá a helyi rendszerkép bináris adatait a kérés törzséhez.
* Igény szerint más URL-paramétereket is használhat. Lásd a [rendszerkép besorolása](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3) API-t.

A visszaadott JSON-válasz legalább az összes olyan címkét megadja, amelyet a modell alkalmazott a képre, valamint az egyes címkékhez tartozó valószínűségi pontszámokat. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy elvégezte a képbesorolási folyamat minden lépését a REST API használatával. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran több alkalommal kell betanítania és tesztelni a modellt, hogy pontosabb legyen.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* [API-referenciák dokumentációja (képzés)](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [API-referenciák dokumentációja (előrejelzés)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
