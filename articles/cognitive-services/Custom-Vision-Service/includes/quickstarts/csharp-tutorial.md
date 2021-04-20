---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3fcc3598348dcfd3e0d0b81bded40161743126d3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725304"
---
Első lépések a Custom Vision .NET-hez való ügyféloldali kódtárával. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki a képbesorolási modell építéséhez szükséges példakódot. Létre fog hozni egy projektet, címkéket fog hozzáadni, betanítja a projektet, és a projekt előrejelzési végponti URL-címével programozott módon teszteli azt. Ezt a példát sablonként használhatja saját képfelismerő alkalmazásának építéséhez.

> [!NOTE]
> Ha kódírás nélkül szeretne besorolási modellt felépíteni és betaníteni, tekintse meg helyette a [böngészőalapú útmutatót.](../../getting-started-build-a-classifier.md) 

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
* Az Azure-előfizetés létrehozása után hozzon létre egy Custom Vision-erőforrást, és hozzon létre egy Custom Vision-erőforrást a Azure Portal-ban egy betanítás és előrejelzési erőforrás létrehozásához, valamint a kulcsok és a végpont lekért <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> létrehozásához. Várja meg az üzembe helyezést, majd kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrások kulcsának és végpontjának létrehozására ahhoz, hogy az alkalmazást a Custom Vision. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

A Visual Studio hozzon létre egy új .NET Core-alkalmazást. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat. Kattintson a jobb gombbal a projektmegoldásra a Megoldáskezelő majd válassza a **Manage NuGet Packages (NuGet-csomagok** **kezelése)** gombra. A megnyíló csomagkezelőben válassza a **Tallózás** lehetőséget, jelölje be az **Include prerelease**(Előzetes) jelölőnégyzetet, majd keresse meg a és a `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` et. Válassza ki a legújabb verziót, majd telepítse a **következőt:**. 

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
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)amely az ebben a rövid útmutatóban található példakódokat tartalmazza.

A projektkönyvtárból nyissa meg a *program.cs fájlt,* és adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_imports)]


Az alkalmazás **Main** metódusában hozzon létre változókat az erőforrás kulcsához és végpontjához. Néhány alapvető objektumot is deklarál, hogy később szükség legyen rá.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Custom Vision szakaszban létrehozott erőforrás-erőforrások telepítése sikeresen megtörtént, kattintson az **Erőforráshoz** ugrás gombra a **Következő lépések területen.**  A kulcsokat és a végpontot az erőforrások kulcs- és végpontoldalán **találja.** Le kell szereznie a betanítás és az előrejelzési erőforrások kulcsait, valamint a betanító erőforrás API-végpontját.
>
> Az előrejelzési erőforrás-azonosító értékét az  erőforrás Tulajdonságok lapján találja, amely **előfizetés-azonosítóként van felsorolva.**
> 
> Ne felejtse el eltávolítani a kulcsokat a kódból, amikor végzett, és soha ne tegye közzé őket nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információért tekintse meg a Cognitive Services [cikkét.](../../../cognitive-services-security.md)

Az alkalmazás **Main** metódusában adja hozzá az ebben a rövid útmutatóban használt metódusok hívásait. Ezeket később fogja megvalósítani.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objektummodell

|Név|Leírás|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ez az osztály kezeli a modellek létrehozását, betanítását és közzétételét. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ez az osztály kezeli a modellek képbesorolási előrejelzésekre való lekérdezését.|
|[PredictionModel (Előrejelzési modell)](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Ez az osztály egyetlen előrejelzést határoz meg egyetlen képen. Tartalmazza az objektumazonosító és -név tulajdonságait, valamint egy megbízhatósági pontszámot.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következő feladatok elvégzését mutatják be a .NET-hez Custom Vision ügyféloldali kódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Új Custom Vision-projekt létrehozása](#create-a-new-custom-vision-project)
* [Címkék hozzáadása a projekthez](#add-tags-to-the-project)
* [Képek feltöltése és címkézése](#upload-and-tag-images)
* [A projekt betanítása](#train-the-project)
* [Az aktuális iteráció közzététele](#publish-the-current-iteration)
* [Az előrejelzési végpont tesztelése](#test-the-prediction-endpoint)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új metódusban példányoshatja a betanító és előrejelzési ügyfeleket a végpont és a kulcsok használatával.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Új Custom Vision-projekt létrehozása

A következő kód egy képosztályozási projektet hoz létre. A létrehozott projekt a következő webhelyen [Custom Vision meg:](https://customvision.ai/). A [createProject metódusban](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) további beállításokat is megadhat a projekt létrehozásakor (lásd [az](../../getting-started-build-a-classifier.md) osztályozó webportáljának összeállítása útmutatót).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Címkék hozzáadása a projekthez

Ez a metódus határozza meg azokat a címkéket, amelyeken a modellt betanítja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_addtags)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Először töltse le a projekthez a mintaképeket. Mentse a Képek [mintamappa tartalmát](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) a helyi eszközre.

> [!NOTE]
> A betanítás befejezéséhez szélesebb képkészletre van szüksége? A Microsoft Garage projekt, a Trove lehetővé teszi képek gyűjtését és megvásárlását betanítás céljából. Miután összegyűjtötte a képeket, letöltheti őket, majd a szokásos módon importálhatja Custom Vision saját projektjére. További [információért látogasson el a Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) oldalára.

Ezután határozzon meg egy segítő metódust, amely ebbe a könyvtárba tölti fel a képeket. Előfordulhat, hogy úgy kell szerkesztenie a **GetFiles** argumentumot, hogy a képek mentésének helyére mutasson.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_loadimages)]

Ezután határozzon meg egy metódust a képek feltöltésének, és alkalmazza a címkéket a mappa helye alapján (a képek már rendezve vannak). A képeket feltöltheti és címkézheti iteratívan vagy kötegben (kötegenként legfeljebb 64-et). Ez a kódrészlet mindkettőre tartalmaz példákat. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]


## <a name="train-the-project"></a>A projekt betanítása

Ez a metódus hozza létre az első betanítási iterációt a projektben. A betanítás befejezéséig lekérdezi a szolgáltatást.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

> [!TIP]
> Betanítás kijelölt címkékkel
>
> Igény szerint az alkalmazott címkéknek csak egy részkészletét is betaníthatja. Akkor is ezt tegye, ha még nem alkalmazott elég címkét, de van elég más címkéből. A [TrainProject hívásban](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) használja a *trainingParameters paramétert.* Állítson össze [egy TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) tulajdonságot, és állítsa be annak **SelectedTags** tulajdonságát a használni kívánt címkék azonosítóinak listájára. A modell betanítja, hogy csak a listában található címkéket ismerje fel.

## <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Ez a metódus elérhetővé teszi a modell aktuális iterációját a lekérdezéshez. A modell nevét használhatja hivatkozásként az előrejelzési kérések elküldését. A saját értékét kell `predictionResourceId` megadnia. Az előrejelzési erőforrás azonosítóját az  erőforrás Áttekintés lapján találja a Azure Portal előfizetés azonosítója **listában.**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Az előrejelzési végpont tesztelése

A szkript ezen része betölti a teszt képét, lekérdezi a modell végpontját, és kiírja az előrejelzési adatokat a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_test)]


## <a name="run-the-application"></a>Az alkalmazás futtatása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Futtassa az alkalmazást  az IDE ablak tetején található Hibakeresés gombra kattintva.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa az alkalmazást az alkalmazáskönyvtárból az `dotnet run` paranccsal.

```dotnet
dotnet run
```

---

Az alkalmazás futtatásakor meg kell nyitnia egy konzolablak ablakát, és meg kell írnia a következő kimenetet:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Ezután ellenőrizheti, hogy a tesztkép (az **Images/Test/** mappában található) megfelelően lett-e megcímkézve. Az alkalmazásból történő kilépéshez nyomjon meg egy billentyűt. Vissza is léphet a [Custom Vision webhelyére](https://customvision.ai), és megtekintheti az újonnan létrehozott projekt aktuális állapotát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Következő lépések

Ezzel a képosztályozási folyamat minden lépését kódban tette meg. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran többször is be kell majd betanítanunk és tesztelnünk kell a modellt, hogy pontosabb legyen.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* A minta forráskódja megtalálható a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [SDK-referenciadokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
