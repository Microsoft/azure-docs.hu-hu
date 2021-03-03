---
title: 'Rövid útmutató: Computer Vision ügyféloldali kódtár a Go-hoz'
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg az Computer Vision ügyféloldali függvénytárával, ahol ez a rövid útmutató is elérhető.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 37c1b81f2396eff1613a6af604b2490b18b93ea8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750869"
---
<a name="HOLTop"></a>

Az Computer Vision ügyféloldali kódtár használata a következőhöz:

* Elemezheti a címkéket, a szöveges leírást, az arcokat, a felnőtt tartalmakat és egyebeket.
* Nyomtatott és kézzel írt szöveg olvasása az olvasási API-val.

[Dokumentáció](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  [Csomag](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Go](https://golang.org/dl/) legújabb verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* [Hozzon létre környezeti változókat](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a kulcs és a végpont URL-címéhez, illetve a nevet `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

## <a name="setting-up"></a>Beállítás

### <a name="create-a-go-project-directory"></a>Go-projekt könyvtárának létrehozása

A konzol ablakban (cmd, PowerShell, Terminal, bash) hozzon létre egy új munkaterületet a go-projekt számára, `my-app` és keresse meg a nevet.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

A munkaterület három mappát fog tartalmazni:

* **src** – ez a könyvtár a forráskódot és a csomagokat fogja tartalmazni. A paranccsal telepített csomagok `go get` ebben a könyvtárban lesznek.
* **pkg** – ez a könyvtár tartalmazni fogja a lefordított go Package objektumokat. Ezek a fájlok mindegyike rendelkezik `.a` bővítménnyel.
* **bin** – ez a könyvtár fogja tartalmazni a futtatásakor létrehozott bináris végrehajtható fájlokat `go install` .

> [!TIP]
> Ha többet szeretne megtudni a go-munkaterület struktúrájáról, tekintse meg a [Go Language dokumentációját](https://golang.org/doc/code.html#Workspaces). Ez az útmutató a és a beállításával kapcsolatos információkat tartalmaz `$GOPATH` `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Az ügyféloldali kódtár telepítése a Go-hoz

Ezután telepítse az ügyféloldali kódtárat a Go-hoz:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

vagy ha a DEP-t használja a tárházon belül, futtassa a következőket:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Go-alkalmazás létrehozása 

Következő lépésként hozzon létre egy fájlt a (z) nevű **src** könyvtárban `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Nyissa meg az `sample-app.go` előnyben részesített ide-vagy szövegszerkesztőben. Ezután adja hozzá a csomag nevét, és importálja a következő könyvtárakat:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Továbbá deklaráljon egy kontextust a szkript gyökerében. Erre az objektumra szüksége lesz a legtöbb Computer Vision függvényhívás végrehajtásához:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Ezután megkezdheti a kód hozzáadását a különböző Computer Vision műveletek végrehajtásához.

> [!div class="nextstepaction"]
> [Megállítottam az ügyfelet](?success=set-up-client#object-model) , hogy [egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Computer Vision go SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Ez az osztály az összes Computer Vision funkcióhoz szükséges, például a képek elemzéséhez és a szöveges olvasáshoz. Ezt az előfizetési adatokkal hozza létre, és a legtöbb képművelet végrehajtásához használja.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Ez a típus egy **AnalyzeImage** függvény hívásának eredményét tartalmazza. A kategória-specifikus függvények hasonló típusúak.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Ez a típus a Batch olvasási műveletének eredményét tartalmazza. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Ez a típus a képelemzés különböző fajtáit határozza meg, amelyeket szabványos elemzési műveletekben lehet elvégezni. Az igényeinek megfelelően adja meg a VisualFeatureTypes-értékek készletét. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat az Computer Vision ügyféloldali kódtár for go használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)
* [Nyomtatott és kézzel írt szöveg olvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a lépés azt feltételezi, hogy [létrehozott egy környezeti változót](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a Computer Vision kulcshoz és a végponthoz, a nevet `COMPUTER_VISION_SUBSCRIPTION_KEY` és a-t `COMPUTER_VISION_ENDPOINT` .

Hozzon létre egy `main` függvényt, és adja hozzá a következő kódot egy ügyfél létrehozásához a végponttal és a kulccsal.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Hitelesítettem a-ügyfelet](?success=authenticate-client#analyze-an-image) [, hogy egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A következő kód a Client objektum használatával elemzi a távoli rendszerképeket, és kinyomtatja az eredményeket a-konzolra. Lekérheti a szöveges leírást, a kategorizálást, a címkék listáját, az észlelt objektumokat, az észlelt márkákat, az észlelt arcokat, a felnőtt tartalom jelzőit, a fő színeket és a képtípust.

### <a name="set-up-test-image"></a>Tesztelési rendszerkép beállítása

Először mentse a hivatkozást az elemezni kívánt rendszerkép URL-címére. Ezt a függvényen belül helyezheti el `main` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> Elemezheti a helyi rendszerképet is. Tekintse meg a [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) metódusokat, például a **DescribeImageInStream**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) található mintakód-kódot.

### <a name="specify-visual-features"></a>Vizuális funkciók meghatározása

A következő függvény a minta rendszerképből származó különböző vizualizációs funkciók kinyerését kéri. Ezeket a függvényeket a következő részben definiáljuk.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Rendszerkép lekérése – Leírás

A következő függvény beolvassa a rendszerképhez létrehozott feliratok listáját. További információ a lemezképek leírásáról: [képek](../../concept-describing-images.md)leírása.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Rendszerkép beolvasása kategória

A következő függvény beolvassa a rendszerkép észlelt kategóriáját. További információt a [képek kategorizálása](../../concept-categorizing-images.md)című témakörben talál.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Képcímkék beolvasása

A következő függvény beolvassa az észlelt címkék készletét a képen. További információ: [tartalom címkék](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő függvény észleli a rendszerképben lévő általános objektumokat, és kiírja azokat a konzolra. További információ: [objektumok észlelése](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a rendszerképben, és kinyomtatja őket a konzolra. További információ a [márka észlelése](../../concept-brand-detection.md).

Először deklaráljon egy hivatkozást egy új rendszerképre a `main` függvényen belül.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

A következő kód a márka észlelési függvényét határozza meg.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő függvény az észlelt arcokat adja vissza a képen a téglalap koordinátáival és bizonyos arc-attribútumokkal. További információ: [Arcfelismerés](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, zamatos vagy véres tartalom észlelése

A következő függvény kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További információkért tekintse meg a [felnőtt, a zamatos és a véres tartalom](../../concept-detecting-adult-content.md)című témakört.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Rendszerképek színsémájának beolvasása

A következő függvény kinyomtatja az észlelt színattribútumokat a képen, például a domináns színeket és a kiejtés színét. További információ: [Színsémák](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Tartományhoz tartozó tartalom beolvasása

A Computer Vision speciális modelleket használhat a képek további elemzéséhez. További információ: [tartományra vonatkozó tartalom](../../concept-detecting-domain-content.md). 

A következő kód az észlelt hírességek adatait elemzi a képen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

A következő kód az észlelt tereptárgyak adatait elemzi a képen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>A rendszerkép típusának beolvasása

A következő függvény adatokat nyomtat ki a kép típusáról, &mdash; legyen szó ClipArt vagy vonalas rajzról.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [Elemeztem egy olyan képet](?success=analyze-image#read-printed-and-handwritten-text) [, amelyet egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

A Computer Vision a képen látható szöveget olvashatja, és átalakíthatja a karakteres adatfolyamba. Az ebben a szakaszban szereplő kód egy olyan függvényt határoz meg, `RecognizeTextReadAPIRemoteImage` amely az ügyfél objektumával azonosítja és Kinyeri a nyomtatott vagy kézírásos szöveget a képen.

Adja hozzá a minta rendszerkép-referenciát és a függvény hívását a `main` függvényhez.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Helyi rendszerképből is kinyerheti a szöveget. Tekintse meg a [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) metódusokat, például a **BatchReadFileInStream**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) található mintakód-kódot.

### <a name="call-the-read-api"></a>Az olvasási API meghívása

Adja meg az új függvényt a szöveg olvasásához `RecognizeTextReadAPIRemoteImage` . Adja hozzá az alábbi kódot, amely meghívja a **BatchReadFile** metódust az adott képhez. Ez a metódus egy műveleti azonosítót ad vissza, és egy aszinkron folyamatot indít el a rendszerkép tartalmának olvasásához.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Olvasási eredmények beolvasása

Ezután kérje le a **BatchReadFile** -hívás által visszaadott művelet azonosítóját, és használja a **GetReadOperationResult** metódussal, hogy lekérdezze a szolgáltatást a művelet eredményeihez. A következő kód ellenőrzi a műveletet egy másodperces időközönként, amíg az eredmények vissza nem állnak. Ezután kinyomtatja a kinyert szöveges adatát a konzolon.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Olvasási eredmények megjelenítése

Adja hozzá a következő kódot a beolvasott szöveges adat elemzéséhez és megjelenítéséhez, majd fejezze be a függvény definícióját.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Beolvastam](?success=read-printed-handwritten-text#run-the-application) a [problémát](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `go run` paranccsal.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Futtattam az alkalmazást a](?success=run-the-application#clean-up-resources) [probléma megoldásához](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [](?success=clean-up-resources#next-steps) [Egy hibába ütközött az](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources) erőforrások

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Computer Vision API referenciája (go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)


* [Mi a Computer Vision?](../../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)található.
