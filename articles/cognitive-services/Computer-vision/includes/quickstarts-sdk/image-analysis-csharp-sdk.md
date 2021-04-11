---
title: 'Rövid útmutató: a .NET-hez készült rendszerkép-elemzés ügyféloldali kódtára'
description: Ebben a rövid útmutatóban megismerheti a .NET-hez készült rendszerkép-elemzési ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 03/29/2021
ms.author: pafarley
ms.openlocfilehash: 2b5828df0c0f29de04acfd87faf39c6392217ea7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073384"
---
<a name="HOLTop"></a>

A képelemzési ügyféloldali kódtár segítségével elemezheti a címkéket, a szöveges leírást, az arcokat, a felnőtt tartalmakat és egyebeket.

[Dokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Példák](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/) vagy a [.net Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Hozzon létre egy új .NET Core-alkalmazást a Visual Studióval. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat úgy, hogy a **megoldáskezelő** , majd a **NuGet-csomagok kezelése** lehetőségre kattint a jobb gombbal a projekt megoldásra. A megnyíló csomagkezelő válassza a **Tallózás** lehetőséget, jelölje be az **előzetes verzió** használata jelölőnégyzetet, és keressen rá `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Válassza `6.0.0-preview.1` a verzió, majd a **telepítés** lehetőséget. 

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `computer-vision-quickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: *program. cs*.

```console
dotnet new console -n computer-vision-quickstart
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

Az alkalmazás könyvtárában telepítse az Computer Vision .NET-hez készült ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

A projekt könyvtárában nyissa meg a *ComputerVisionQuickstart. cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

Az alkalmazás **program** osztályában hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Computer Vision erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

Az alkalmazás `Main` metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később fogja létrehozni.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

> [!div class="nextstepaction"]
> [Megállítottam az ügyfelet](?success=set-up-client#object-model) , hogy [egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a rendszerkép-elemzés .NET SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Ez az osztály minden Computer Vision funkcióhoz szükséges. Ezt az előfizetési adatokkal hozza létre, és a legtöbb képművelet végrehajtásához használja.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Ez az osztály további metódusokat tartalmaz a **ComputerVisionClient**.|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Ez az enumerálás a képelemzés különböző típusait határozza meg, amelyeket szabványos elemzési műveletekben lehet elvégezni. Az igényeinek megfelelően adja meg a VisualFeatureTypes-értékek készletét. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a rendszerkép-elemzés ügyféloldali kódtára a .NET-hez:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [létrehozott egy környezeti változót](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a Computer Vision kulcshoz és a végponthoz `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

A **program** osztály új metódusában hozza létre az ügyfelet a végponttal és a kulccsal. Hozzon létre egy **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** objektumot a kulccsal, és használja a végpontján egy **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** objektum létrehozásához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Hitelesítettem a-ügyfelet](?success=authenticate-client#analyze-an-image) [, hogy egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A következő kód egy metódust határoz meg, `AnalyzeImageUrl` amely az ügyfél-objektumot használja egy távoli rendszerkép elemzéséhez és az eredmények kinyomtatásához. A metódus a szöveges leírást, a kategorizálást, a címkék listáját, az észlelt arcokat, a felnőtt tartalom jelzőit, a fő színeket és a képtípust adja vissza.

> [!TIP]
> Elemezheti a helyi rendszerképet is. Tekintse meg a [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) metódusokat, például a **AnalyzeImageInStreamAsync**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) található mintakód-kódot.

### <a name="set-up-test-image"></a>Tesztelési rendszerkép beállítása

A **program** osztályban mentse az elemezni kívánt rendszerkép URL-címére mutató hivatkozást.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Vizuális funkciók meghatározása

Adja meg az új módszert a képelemzéshez. Adja hozzá az alábbi kódot, amely meghatározza, hogy mely vizuális funkciókat szeretné kibontani az elemzésbe. A teljes listát a **[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)** enumerálásban tekintheti meg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

A funkciók megvalósításához szúrja be az alábbi kódrészletek egyikét a **AnalyzeImageUrl** metódusba. Ne felejtse el felvenni a záró szögletes zárójelet.

```csharp
}
```

### <a name="analyze"></a>Elemzés

A **AnalyzeImageAsync** metódus egy **ImageAnalysis** objektumot ad vissza, amely az összes kinyert információt tartalmazza.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

Az alábbi részek azt mutatják be, hogyan elemezheti ezeket az információkat részletesen.

### <a name="get-image-description"></a>Rendszerkép lekérése – Leírás

A következő kód beolvassa a rendszerképhez létrehozott feliratok listáját. További részletekért tekintse meg a [képek leírása](../../concept-describing-images.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Rendszerkép beolvasása kategória

A következő kód lekéri a rendszerkép észlelt kategóriáját. További részletekért tekintse meg a [képek kategorizálása](../../concept-categorizing-images.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Képcímkék beolvasása

A következő kód beolvassa az észlelt címkék készletét a képen. További részletekért tekintse meg a [tartalom címkéit](../../concept-tagging-images.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő kód észleli a rendszerképben szereplő általános objektumokat, és kiírja azokat a konzolra. További részletekért lásd az [objektum észlelése](../../concept-object-detection.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a rendszerképben, és kinyomtatja őket a konzolra. További részletekért lásd a [márka észlelését](../../concept-brand-detection.md) ismertető témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő kód az észlelt arcokat adja vissza a képen a téglalap koordinátáival, majd a Face attribútumok elemet. További részletekért tekintse meg az [Arcfelismerés](../../concept-detecting-faces.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, zamatos vagy véres tartalom észlelése

A következő kód kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További részletekért tekintse meg a [felnőtt, a zamatos és a véres tartalmat](../../concept-detecting-adult-content.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Rendszerképek színsémájának beolvasása

A következő kód az észlelt színattribútumokat nyomtatja ki a képen, például a domináns színeket és a kiejtés színét. További részletekért lásd: [Színsémák](../../concept-detecting-color-schemes.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Tartományhoz tartozó tartalom beolvasása

A képelemzés speciális modelleket használhat a lemezképek további elemzéséhez. További részletekért tekintse meg a [tartományra vonatkozó tartalmat](../../concept-detecting-domain-content.md) . 

A következő kód az észlelt hírességek adatait elemzi a képen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

A következő kód az észlelt tereptárgyak adatait elemzi a képen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>A rendszerkép típusának beolvasása

Az alábbi kód a képtípussal kapcsolatos információkat jeleníti &mdash; meg, függetlenül attól, hogy a ClipArt vagy egy vonalas rajzról van szó.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [Elemeztem egy olyan képet](?success=analyze-image#run-the-application) [, amelyet egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=analyze-image)



## <a name="run-the-application"></a>Az alkalmazás futtatása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Futtassa az alkalmazást az IDE-ablak felső részén található **Debug (hibakeresés** ) gombra kattintva.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan telepítheti a rendszerkép-elemzési ügyféloldali kódtárat, és hogyan készíthet alapszintű képelemzési hívásokat. Következő lépésként tekintse meg az API-funkciók elemzését ismertető témakört.


> [!div class="nextstepaction"]
>[Az elemzési API meghívása](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Képek elemzése – áttekintés](../../overview-image-analysis.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)található.
