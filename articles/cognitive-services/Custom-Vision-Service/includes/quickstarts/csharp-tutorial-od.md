---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: c28cfbbdff9e3fbf6cfe8d53ab7529c2ce9bd96e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725159"
---
Első lépések a Custom Vision .NET-hez való ügyféloldali kódtárával. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az objektumészlelési modell építéséhez szükséges példakódot. Létre fog hozni egy projektet, címkéket fog hozzáadni, mintaképeken betanítja a projektet, majd a projekt előrejelzési végpontjának URL-címével programozott módon teszteli azt. Ezt a példát sablonként használhatja saját képfelismerő alkalmazás építéséhez.

> [!NOTE]
> Ha kódírás nélkül szeretne objektumészlelési modellt felépíteni és betaníteni, tekintse meg inkább a [böngészőalapú útmutatót.](../../get-started-build-detector.md) 

Használja a Custom Vision .NET-hez használható ügyféloldali kódtárát a következőre:

* Új Custom Vision-projekt létrehozása
* Címkék hozzáadása a projekthez
* Képek feltöltése és címkézése
* A projekt betanítása
* Az aktuális iteráció közzététele
* Az előrejelzési végpont tesztelése

[Referenciadokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | Kódtár [forráskódja (betanítás)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(előrejelzés)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Csomag (NuGet) [(betanítás)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(előrejelzés)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)  |  [minták](/samples/browse/?products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) vagy a [.NET Core aktuális verziója.](https://dotnet.microsoft.com/download/dotnet-core)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Custom Vision-erőforrást, és hozzon létre egy Custom Vision-erőforrást a Azure Portal-ban egy betanítás és előrejelzési erőforrás létrehozásához, valamint a kulcsok és a végpont lekért <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> létrehozásához. Várja meg az üzembe helyezést, és kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrások kulcsra és végpontra ahhoz, hogy az alkalmazást a Custom Vision. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

A Visual Studio hozzon létre egy új .NET Core-alkalmazást. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat. Kattintson a jobb gombbal a projektmegoldásra a Megoldáskezelő majd válassza a **Manage NuGet Packages (NuGet-csomagok** kezelése) gombra.  A megnyíló csomagkezelőben válassza a **Tallózás** lehetőséget, jelölje be **az Include prerelease**(Előzetes) jelölőnégyzetet, majd keresse meg a és a `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` et. Válassza ki a legújabb verziót, majd kattintson a **Telepítés gombra.** 

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Egy konzolablakban (például cmd, PowerShell vagy Bash) az paranccsal hozzon létre egy új konzolalkalmazást `dotnet new` `custom-vision-quickstart` néven. Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájllal: *program.cs*. 

```console
dotnet new console -n custom-vision-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappába. Az alkalmazást a következővel építheti fel:

```console
dotnet build
```

A build kimenete nem tartalmazhat figyelmeztetéseket vagy hibákat. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Telepítse a .NET-hez Custom Vision ügyféloldali kódtárat az alkalmazáskönyvtárban a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)amely a rövid útmutatóban található példakódokat tartalmazza.

A projektkönyvtárból nyissa meg a *program.cs fájlt,* és adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_imports)]

Az alkalmazás **Main** metódusában hozzon létre változókat az erőforrás kulcsához és végpontjához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Custom Vision szakaszban létrehozott erőforrás-erőforrások üzembe helyezése sikeresen megtörtént, kattintson az **Erőforrás** ugrás gombra a **Következő lépések területen.**  A kulcsokat és a végpontot  az erőforrások kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.** Le kell szereznie a betanítás és az előrejelzési erőforrások kulcsait, valamint a betanító erőforrás API-végpontját.
>
> Ne felejtse el eltávolítani a kulcsokat a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információért tekintse meg a Cognitive Services [cikkét.](../../../cognitive-services-security.md)

Az alkalmazás **Main** metódusában adja hozzá az ebben a rövid útmutatóban használt metódusok hívásait. Ezeket később fogja megvalósítani.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objektummodell

|Név|Leírás|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ez az osztály kezeli a modellek létrehozását, betanítását és közzétételét. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ez az osztály kezeli a modellek objektumészlelési előrejelzésekhez való lekérdezését.|
|[PredictionModel (Előrejelzési modell)](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Ez az osztály egyetlen objektum-előrejelzést határoz meg egyetlen képen. Ez tartalmazza az objektumazonosító és -név tulajdonságait, a határolókeret helyét és a megbízhatósági pontszámot.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következő feladatok elvégzését mutatják be a .NET-hez Custom Vision ügyfélkódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Új Custom Vision-projekt létrehozása](#create-a-new-custom-vision-project)
* [Címkék hozzáadása a projekthez](#add-tags-to-the-project)
* [Képek feltöltése és címkézése](#upload-and-tag-images)
* [A projekt betanítása](#train-the-project)
* [Az aktuális iteráció közzététele](#publish-the-current-iteration)
* [Az előrejelzési végpont tesztelése](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új metódusban példányosithatja a betanító és előrejelzési ügyfeleket a végpont és a kulcsok használatával.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Új Custom Vision-projekt létrehozása

A következő metódus egy objektumészlelési projektet hoz létre. A létrehozott projekt a következő webhelyen [fog Custom Vision:](https://customvision.ai/). A projekt létrehozásakor további beállítások megadásához tekintse meg a [CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) metódust (ezt [a Detektor](../../get-started-build-detector.md) webportál létrehozása útmutatóban ismertetjük).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Címkék hozzáadása a projekthez

Ez a metódus határozza meg azokat a címkéket, amelyeken a modellt betanítja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Először töltse le a projekthez a mintaképeket. Mentse a Képek [mintamappa tartalmát](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) a helyi eszközre.

> [!NOTE]
> A betanítás befejezéséhez szélesebb képkészletre van szüksége? A Microsoft Sets-projekt, a Trove lehetővé teszi képek gyűjtését és megvásárlását betanítás céljából. Miután összegyűjtötte a képeket, letöltheti őket, majd a szokásos módon importálhatja Custom Vision saját projektjére. További [információért látogasson el a Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) oldalára.

Ha képeket címkéz az objektum-észlelési projektekben, az egyes címkézett objektumok régióját normalizált koordináták használatával kell megadnia. A következő kód a mintául szolgáló rendszerképeket társítja a címkézett régiójával.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

> [!NOTE]
> Ha a saját projektjeihez nem áll rendelkezésre kattintással és húzással használható segédprogram a régiók koordinátáinak megjelöléséhez, használhatja a webes felhasználói felületet a Custom Vision [webhelyén.](https://www.customvision.ai/) Ebben a példában a koordináták már meg vannak téve.

Az egyes mintaképek és régiókoordinátáik ezután ezzel a társítási térképpel tölthetők fel. Egyetlen kötegben legfeljebb 64 képet tölthet fel. Előfordulhat, hogy módosítania kell `imagePath` az értéket, hogy a megfelelő mappahelyekre mutasson.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Ekkor feltöltötte az összes minta lemezképet, és címkézi őket (**elágazás** vagy **olló**) egy társított pixel téglalapmal.

## <a name="train-the-project"></a>A projekt betanítása

Ez a metódus hozza létre az első betanítási iterációt a projektben. A betanítás befejezéséig lekérdezi a szolgáltatást.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Betanítás a kiválasztott címkékkel
>
> Igény szerint az alkalmazott címkéknek csak egy részkészletét is betaníthatja. Ezt akkor lehet jó, ha még nem alkalmazott elég címkét, de van elég más is. A [TrainProject hívásban](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) használja a *trainingParameters paramétert.* Állítson össze [egy TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) tulajdonságot, és állítsa be **a SelectedTags** tulajdonságát a használni kívánt címkék azonosítóinak listájára. A modell betanítja, hogy csak a listán található címkéket ismerje fel.

## <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Ez a metódus elérhetővé teszi a modell aktuális iterációját a lekérdezéshez. A modell nevét használhatja hivatkozásként az előrejelzési kérések elküldését. A saját értékét kell `predictionResourceId` megadnia. Az előrejelzési erőforrás azonosítóját az erőforrás **Áttekintés** lapján találja a Azure Portal előfizetés azonosítója **listában.**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Az előrejelzési végpont tesztelése

Ez a metódus betölti a tesztképet, lekérdezi a modell végpontját, és előrejelzési adatokat ad ki a konzolon.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Futtassa az alkalmazást  az IDE ablakának tetején található Hibakeresés gombra kattintva.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa az alkalmazást az alkalmazás könyvtárában az `dotnet run` paranccsal.

```dotnet
dotnet run
```

---

Az alkalmazás futtatásakor meg kell nyitnia egy konzolablak ablakát, és meg kell írnia a következő kimenetet:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Ezt követően ellenőrizheti, hogy a rendszerkép ( **images/test/** ) címkéje megfelelően van-e megjelölve, és hogy az észlelési régió helyes-e. Ezen a ponton bármelyik billentyű lenyomásával kiléphet az alkalmazásból.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Következő lépések

Ezzel az objektumészlelési folyamat minden lépését megtette a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran többször is be kell majd képezni és tesztelni kell a modellt, hogy pontosabb legyen. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* A minta forráskódja a [GitHubon található](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [SDK-referenciadokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
