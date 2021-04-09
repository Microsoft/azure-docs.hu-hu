---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 6a09d435613a715d4ae4e162f42f59402d0bc5cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102444503"
---
Ismerkedjen meg a .NET-hez készült Custom Vision ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az objektum-észlelési modell felépítési példájának kódját. Létrehoz egy projektet, címkéket ad hozzá, betanítja a projektet a minta lemezképeken, és a projekt előrejelzési végpontjának URL-címét használja a programozott teszteléshez. Ez a példa sablonként használható a saját rendszerkép-felismerő alkalmazás létrehozásához.

> [!NOTE]
> Ha kód írása _nélkül_ szeretne létrehozni és betanítani egy objektum-észlelési modellt, tekintse meg a [böngészőalapú útmutatást](../../get-started-build-detector.md) .

A .NET-hez készült Custom Vision ügyféloldali kódtára a következőre használható:

* Új Custom Vision-projekt létrehozása
* Címkék hozzáadása a projekthez
* Képek feltöltése és címkézése
* A projekt betanítása
* Az aktuális iteráció közzététele
* Az előrejelzési végpont tesztelése

[Referenciák dokumentációja](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | Könyvtár forráskódja [(képzés)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(előrejelzés)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Csomag (NuGet) [(képzési)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(előrejelzési)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)  |  [minták](/samples/browse/?products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/) vagy a [.net Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" hozzon létre egy Custom Vision erőforrást egy "  target="_blank"> Custom Vision erőforrás létrehozásához </a> a Azure Portal egy képzési és előrejelzési erőforrás létrehozásához, valamint a kulcsok és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Az alkalmazás Custom Visionhoz való összekapcsolásához szüksége lesz a létrehozott erőforrások kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Hozzon létre egy új .NET Core-alkalmazást a Visual Studióval. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat úgy, hogy a **megoldáskezelő** , majd a **NuGet-csomagok kezelése** lehetőségre kattint a jobb gombbal a projekt megoldásra. A megnyíló csomagkezelő válassza a **Tallózás** lehetőséget, jelölje be az **előzetes verzió** használata jelölőnégyzetet, és keressen rá a és a kifejezésre `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` . Válassza ki a legújabb verziót, majd **telepítse** a programot. 

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `custom-vision-quickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: *program. cs*. 

```console
dotnet new console -n custom-vision-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Az alkalmazás könyvtárában telepítse az Custom Vision .NET-hez készült ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

A projekt könyvtárában nyissa meg a *program. cs* fájlt, és adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_imports)]

Az alkalmazás **fő** metódusában hozzon létre változókat az erőforrás kulcsa és végpontja számára.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Custom Vision erőforrások telepítése sikeresen megtörtént, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsokat és végpontokat az **Erőforrás-kezelés** területen találja az erőforrások **kulcs és végpont** oldalain. A betanítási és előrejelzési kulcsokat is meg kell kapnia, valamint a képzési erőforrások végpontját.
>
> Ne felejtse el eltávolítani a kulcsokat a kódból, amikor elkészült, és soha ne tegye közzé őket nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

Az alkalmazás **fő** metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később fogja megvalósítani.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objektummodell

|Név|Leírás|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ez az osztály kezeli a modellek létrehozását, betanítását és közzétételét. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ez az osztály kezeli a modellek lekérdezését az objektum-észlelési előrejelzésekhez.|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Ez az osztály egyetlen objektumra vonatkozó előrejelzést definiál egyetlen rendszerképben. Az objektum AZONOSÍTÓját és nevét, az objektum határoló mező helyét és egy megbízhatósági pontszámot tartalmaz.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat az Custom Vision .NET-hez készült ügyféloldali kódtára használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Új Custom Vision-projekt létrehozása](#create-a-new-custom-vision-project)
* [Címkék hozzáadása a projekthez](#add-tags-to-the-project)
* [Képek feltöltése és címkézése](#upload-and-tag-images)
* [A projekt betanítása](#train-the-project)
* [Az aktuális iteráció közzététele](#publish-the-current-iteration)
* [Az előrejelzési végpont tesztelése](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új metódusban hozza létre a képzési és előrejelzési ügyfeleket a végpont és a kulcsok használatával.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Új Custom Vision-projekt létrehozása

Ez a következő metódus egy objektum-észlelési projektet hoz létre. A létrehozott projekt megjelenik a [Custom Vision webhelyén](https://customvision.ai/). Tekintse meg a [CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) metódust a projekt létrehozásakor a további beállítások megadásához (lásd: a detektor webportáljának [összeállítása](../../get-started-build-detector.md) útmutató).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Címkék hozzáadása a projekthez

Ez a módszer határozza meg azokat a címkéket, amelyeken a modellt be fogja tanítani.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Először töltse le a projekthez tartozó mintaképeket. Mentse a [Sample images mappa](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) tartalmát a helyi eszközre.

> [!NOTE]
> Szüksége van egy szélesebb körű rendszerképekre a képzés befejezéséhez? A Microsoft garázs-projekt, amely lehetővé teszi, hogy beszerezze a betanítási célokra szánt lemezképek készleteit. A képek összegyűjtése után letöltheti őket, majd a szokásos módon importálhatja őket a Custom Vision-projektbe. További információért látogasson el az [adattárház oldalára](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) .

Ha képeket címkéz az objektum-észlelési projektekben, az egyes címkézett objektumok régióját normalizált koordináták használatával kell megadnia. A következő kód a mintául szolgáló rendszerképeket társítja a címkézett régiójával.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

> [!NOTE]
> Ha saját projektjei esetében nem rendelkezik a régiók koordinátáinak megjelölésére szolgáló kattintással és húzással, akkor a [Custom Vision webhelyén](https://www.customvision.ai/)található webes felhasználói felületet használhatja. Ebben a példában a koordináták már meg vannak biztosítva.

Az egyes mintaképek és régiókoordinátáik ezután ezzel a társítási térképpel tölthetők fel. Egyetlen kötegben akár 64 képet is feltölthet. Előfordulhat, hogy módosítania kell az `imagePath` értéket, hogy a megfelelő mappa helyeire mutasson.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Ekkor feltöltötte az összes minta lemezképet, és címkézi őket (**elágazás** vagy **olló**) egy társított pixel téglalapmal.

## <a name="train-the-project"></a>A projekt betanítása

Ez a metódus létrehozza az első betanítási iterációt a projektben. A szolgáltatás lekérdezi a szolgáltatást, amíg a képzés be nem fejeződik.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Betanítás kiválasztott címkékkel
>
> Igény szerint betaníthatja az alkalmazott címkék egy részhalmazát. Ezt akkor érdemes megtenni, ha még nem alkalmazta elég bizonyos címkéket, de másokkal is rendelkezik. A [TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) -hívásban használja a *trainingParameters* paramétert. Hozzon létre egy [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) , és állítsa be a **SelectedTags** tulajdonságát a használni kívánt címkék azonosítóinak listájára. A modell betanításával csak a listán szereplő címkéket ismeri fel a rendszer.

## <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Ezzel a módszerrel elérhetővé válik a modell aktuális iterációja a lekérdezéshez. A modell neve hivatkozásként használható az előrejelzési kérelmek küldéséhez. Meg kell adnia a saját értékét `predictionResourceId` . Az előrejelzési erőforrás azonosítója az erőforrás **Áttekintés** lapján, az **előfizetés-azonosítóként** felsorolt Azure Portalban található.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Az előrejelzési végpont tesztelése

Ez a módszer betölti a teszt képét, lekérdezi a modell végpontját, és előrejelzési adatokat küld a konzolnak.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Futtassa az alkalmazást az IDE-ablak felső részén található **Debug (hibakeresés** ) gombra kattintva.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

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

Most végrehajtotta az objektum-észlelési folyamat minden lépését a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran több alkalommal kell betanítania és tesztelni a modellt, hogy pontosabb legyen. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* A minta forráskódja megtalálható a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [Az SDK dokumentációja](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
