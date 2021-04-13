---
title: 'Rövid útmutató: Image Analysis ügyféloldali kódtár a .NET-hez'
description: Ebben a rövid útmutatóban az Image Analysis .NET-hez való ügyféloldali kódtárával fog kezdeni.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 03/29/2021
ms.author: pafarley
ms.openlocfilehash: 407ef167ca05f08d349a017c60164e2fe67977a6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327200"
---
<a name="HOLTop"></a>

A Képelemzés ügyféloldali kódtára segítségével címkéket, szövegleírásokat, arcokat, felnőtt tartalmakat és egyéb tartalmakat elemezhet egy képen.

[Referenciadokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Minták](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) vagy a [.NET Core aktuális verziója.](https://dotnet.microsoft.com/download/dotnet-core)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Computer Vision-erőforrást, Computer Vision erőforrást a Azure Portal a kulcs és a <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontjára az alkalmazás a Computer Vision csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

A Visual Studio hozzon létre egy új .NET Core-alkalmazást. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat. Kattintson a jobb gombbal a projektmegoldásra a Megoldáskezelő majd válassza a **Manage NuGet Packages (NuGet-csomagok** kezelése) gombra.  A megnyíló csomagkezelőben válassza a **Tallózás** lehetőséget, jelölje be **az Include prerelease**(Előzetes) jelölőnégyzetet, és keressen rá a következőre: `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Válassza a `7.0.0` verziót, majd a **Telepítés lehetőséget.** 

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Egy konzolablakban (például cmd, PowerShell vagy Bash) az paranccsal hozzon létre egy új konzolalkalmazást `dotnet new` `computer-vision-quickstart` néven. Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
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

Telepítse a .NET-hez Computer Vision ügyféloldali kódtárat az alkalmazáskönyvtárban a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ezt a [GitHubon találhatja](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)meg, amely ebben a rövid útmutatóban található példakódokat tartalmazza.

A projektkönyvtárból nyissa meg a *ComputerVisionQuickstart.cs* fájlt az előnyben részesített szerkesztőben vagy IDE-ban. Adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

Az alkalmazás **Program** osztályában hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Computer Vision szakaszban létrehozott erőforrás  sikeresen üzembe lett stb., kattintson az **Erőforrás** ugrás gombra a **Következő lépések területen.** A kulcsot és a végpontot az  erőforrás kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.** 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információt Cognitive Services [biztonsági cikk](../../../cognitive-services-security.md) tartalmaz.

Az alkalmazás metódusában adja hozzá az ebben a rövid útmutatóban használt metódusok `Main` hívásait. Ezeket később fogja létrehozni.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

> [!div class="nextstepaction"]
> [Beállítottam az ügyfelet,](?success=set-up-client#object-model) [amely problémát jelent](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek a Image Analysis .NET SDK néhány fő funkcióját kezelik.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Erre az osztályra az összes Computer Vision szükség. Példányosodhat az előfizetés adataival, és a legtöbb rendszerkép-művelethez használhatja.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Ez az osztály további metódusokat tartalmaz a **ComputerVisionClient számára.**|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Ez az enum határozza meg a képelemzés különböző típusait, amelyek egy standard Elemzés műveletben végrehajtásához végrehajtásához szükséges. A VisualFeatureTypes értékeket az igényeitől függően adhatja meg. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következő feladatok elvégzését mutatják be a .NET Image Analysis ügyféloldali kódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató [](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) feltételezi, hogy létrehozott környezeti változókat a Computer Vision kulcshoz és végponthoz, a neve `COMPUTER_VISION_SUBSCRIPTION_KEY` és a `COMPUTER_VISION_ENDPOINT` .

A **Program** osztály egy új metódusában példányosíthat egy ügyfelet a végponttal és a kulccsal. Hozzon létre **[egy ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** objektumot a kulccsal, és használja a végponttal egy **[ComputerVisionClient objektum létrehozásához.](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Hitelesítettem az ügyfelet,](?success=authenticate-client#analyze-an-image) [amely problémát jelent](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A következő kód egy metódust határoz meg, amely az ügyfélobjektummal elemez egy távoli képet, és `AnalyzeImageUrl` kinyomtatja az eredményeket. A metódus egy szöveges leírást, kategorizálást, címkék listáját, észlelt arcokat, felnőtt tartalomjelölőket, fő színeket és képtípust ad vissza.

> [!TIP]
> Helyi képet is elemezhet. Lásd: [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) metódusok, például **AnalyzeImageInStreamAsync.** Vagy tekintse meg a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) a helyi rendszerképekkel kapcsolatos forgatókönyveket tartalmazó mintakódot.

### <a name="set-up-test-image"></a>Tesztkép beállítása

A **Program osztályban** mentse az elemezni kívánt kép URL-címére mutató hivatkozást.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Vizualizációs funkciók megadása

Definiálja az új képelemzési módszert. Adja hozzá az alábbi kódot, amely meghatározza azokat a vizuális jellemzőket, amelyeket ki szeretnenyerni az elemzésben. A teljes **[listát a VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)** felsorolásban láthatja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

Szúrja be az alábbi kódblokkok bármelyikét az **AnalyzeImageUrl metódusba** a funkcióik megvalósításához. Ne felejtsen el záró zárójelet hozzáadni a véghez.

```csharp
}
```

### <a name="analyze"></a>Elemzés

Az **AnalyzeImageAsync** metódus egy **ImageAnalysis** objektumot ad vissza, amely az összes kinyert információt tartalmazza.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

A következő szakaszok ismertetik, hogyan elemezze részletesen ezt az információt.

### <a name="get-image-description"></a>Kép leírásának lekérte

A következő kód lekérte a képhez létrehozott feliratok listáját. További [részleteket a Képek leírásában](../../concept-describing-images.md) talál.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Képkategória lekérte

A következő kód lekérte a kép észlelt kategóriáját. További részletekért lásd: [Képek kategorizálása.](../../concept-categorizing-images.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Képcímkék lekérte

A következő kód lekérte a képen észlelt címkék készletét. További [részletekért lásd:](../../concept-tagging-images.md) Tartalomcímkék.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Objektumok észlelése

Az alábbi kód észleli a képen látható gyakori objektumokat, és kinyomtatja őket a konzolon. További [részletek:](../../concept-object-detection.md) Objektumészlelés.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a képen látható vállalati márkákat és emblémákat, és kiírja őket a konzolra. További [részletekért lásd:](../../concept-brand-detection.md) Márkaészlelés.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

Az alábbi kód visszaadja a képen észlelt arcokat a téglalap koordinátáival, és kiválasztja az arcattribútumokat. További [részletek: Arcfelismerés.](../../concept-detecting-faces.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtteknek szóló, racy vagy gory tartalom észlelése

A következő kód a képen látható felnőtt tartalom észlelését nyomtatja ki. További [részletekért lásd: Felnőtteknek szóló, racy- és gory-tartalmak.](../../concept-detecting-adult-content.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Kép színsémának leállítása

A következő kód kinyomtatja a képen észlelt színattribútumokat, például a domináns színeket és az ékezet színét. További [részletekért lásd: Színsémák.](../../concept-detecting-color-schemes.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Tartományspecifikus tartalom lekérte

A Képelemzés speciális modellekkel további elemzéseket képes végzett képeken. További [részletekért lásd:](../../concept-detecting-domain-content.md) Tartományspecifikus tartalom. 

Az alábbi kód a képen észlelt hírességek adatait elemezi.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

Az alábbi kód a képen észlelt nevezetességek adatait elemezi.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>A rendszerkép típusának lekért típusa

Az alábbi kód a kép típusával kapcsolatos információkat nyomtatja ki, legyen az &mdash; ClipArt vagy vonalrajz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [Elemeztem egy képet,](?success=analyze-image#run-the-application) [amely problémát jelent](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=analyze-image)



## <a name="run-the-application"></a>Az alkalmazás futtatása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Futtassa az alkalmazást  az IDE ablak tetején található Hibakeresés gombra kattintva.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa az alkalmazást az alkalmazáskönyvtárból az `dotnet run` paranccsal.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan telepítheti az Image Analysis ügyféloldali kódtárat, és hogyan kezdeményezhet alapszintű képelemzési hívásokat. A következő lépés az API-funkciók elemzése.


> [!div class="nextstepaction"]
>[Az Analyze API hívása](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Képelemzés áttekintése](../../overview-image-analysis.md)
* A minta forráskódja a [GitHubon található.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)
