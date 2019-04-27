---
title: 'Gyors útmutató: A Custom Vision készült SDK-val objektum észlelési projekt létrehozásaC#'
titlesuffix: Azure Cognitive Services
description: Projekt létrehozása, címkék hozzáadása, képek feltöltése, projekt betanítása és objektumok észlelése a .NET SDK és a C# együttes használatával.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: cc66630f57af32e18916e0662a400b38f27000a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608919"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Gyors útmutató: A Custom Vision .NET SDK-val objektum észlelési projekt létrehozása

Ez a cikk ahhoz biztosít információt és mintakódot, hogy megismerkedhessen a Custom Vision SDK és a C# együttes használatával egy objektumészlelési modell létrehozása céljából. Miután elkészült, adhat hozzá címkézett régiókat, tölthet fel képeket, betaníthatja a projektet, megkaphatja a projekt alapértelmezett előrejelzési végpont URL-címét és ezt a végpontot felhasználhatja kép programozott tesztelésére. Használja sablonként a példát a saját .NET-alkalmazása létrehozásához. 

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>A Custom Vision SDK és a mintakód letöltése

A Custom Visiont használó .NET-alkalmazás megírásához a Custom Vision NuGet-csomagokra lesz szüksége. A csomagokat tartalmazza a mintaprojekt, amelyet le fog tölteni, azonban külön is elérheti őket itt.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klónozza vagy töltse le a [Cognitive Services .NET-mintákat](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) tartalmazó projektet. Keresse meg a **CustomVision/ObjectDetection** mappát, és nyissa meg _ObjectDetection.csproj_ a Visual Studióban.

Ez a Visual Studio-projekt létrehoz egy új, __My New Project__ nevű Custom Vision-projektet, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána képeket tölt fel az objektumészlelési modell betanítására és tesztelésére. Ebben a projektben a modell betanítása a képeken látható villák és ollók észlelésére irányul.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>A kód értelmezése

Nyissa meg a _Program.cs_ fájlt, és tekintse át a kódot. Szúrja be az előfizetési kulcsokat a megfelelő definíciókba a **Main** metódusban.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

A végpont paramétert kell mutatnia a régió, ahol a Custom Vision erőforrásokat tartalmazó Azure-erőforráscsoportot létrehozták. Ebben a példában azt, hogy az USA déli középső régiójában pedig használja:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

### <a name="create-a-new-custom-vision-service-project"></a>Új Custom Vision Service-projekt létrehozása

A következő kódrészlet létrehoz egy objektumészlelési projektet. A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>Címkék hozzáadása a projekthez

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ha képeket címkéz meg az objektumészlelési projektekben, meg kell adnia a címkével ellátott objektumok régióját a normalizált koordináták használatával. Az alábbi kód a címkével ellátott régiójához társítja mindegyik mintaképet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

Az egyes mintaképek és régiókoordinátáik ezután ezzel a társítási térképpel tölthetők fel.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

Megtörtént a mintaképek mindegyikének feltöltése, és mindegyik kép rendelkezik egy címkével (**villa** vagy **olló**), illetve a címkéhez társított képponttéglalappal.

### <a name="train-the-project"></a>A projekt tanítása

Ez a kód létrehozza az első betanítási iterációt a projektben.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Név, a közzétett iteráció előrejelzési kérelmek küldésére használható. Egy iteráció nem áll rendelkezésre előrejelzési végpontját, amíg közzé van téve.

```csharp
// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="create-a-prediction-endpoint"></a>Előrejelzési végpont létrehozása

```csharp
// Create a prediction endpoint, passing in the obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="use-the-prediction-endpoint"></a>Az előrejelzési végpont használata

A szkript ezen része betölti a képet, lekérdezi a modell végpontját, majd megjeleníti az előrejelzési adatokat a konzolon.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var imageFile = Path.Combine("Images", "test", "test_image.jpg");
using (var stream = File.OpenRead(imageFile))
{
        var result = endpoint.DetectImage(project.Id, publishedModelName, File.OpenRead(imageFile));

        // Loop over each prediction and write out the results
        foreach (var c in result.Predictions)
        {
                Console.WriteLine($"\t{c.TagName}: {c.Probability:P1} [ {c.BoundingBox.Left}, {c.BoundingBox.Top}, {c.BoundingBox.Width}, {c.BoundingBox.Height} ]");
        }
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A futtatása során az alkalmazásnak meg kell nyitnia egy konzolablakot, és az alábbi kimenetet kell megjelenítenie:

```
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Ezután ellenőrizheti, hogy a tesztkép (az **Images/Test/** mappában található) megfelelően lett-e megcímkézve, és helyes-e az észlelési régió. Az alkalmazásból való kilépéshez nyomja le bármelyik billentyűt.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>További lépések

Láthatta, hogyan hajthatók végre az objektumészlelési folyamat lépései kódok használatával. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran előfordulhat, hogy a nagyobb pontosság érdekében többször is be kell tanítania és tesztelnie kell a modellt. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)
