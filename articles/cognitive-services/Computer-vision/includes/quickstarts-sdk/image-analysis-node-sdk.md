---
title: 'Gyors útmutató: a Node.js rendszerkép-elemzési ügyféloldali könyvtára'
description: Ismerkedés a rendszerkép-elemzési ügyféloldali kódtár Node.js ezzel a rövid útmutatóval
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: d38b1f20547056c30f19dbbc77589643be1663a4
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073617"
---
<a name="HOLTop"></a>

A képelemzési ügyféloldali kódtár segítségével elemezheti a címkéket, a szöveges leírást, az arcokat, a felnőtt tartalmakat és egyebeket.

[Dokumentáció](/javascript/api/@azure/cognitiveservices-computervision/)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)  |  [Példák](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Node.js](https://nodejs.org/) aktuális verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` .

```console
npm init
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ms-rest-azure` és a `@azure/cognitiveservices-computervision` NPM csomagot:

```console
npm install @azure/cognitiveservices-computervision
```

Telepítse az aszinkron modult is:

```console
npm install async
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

Hozzon létre egy új fájlt, *index.js*, majd nyissa meg egy szövegszerkesztőben. Adja hozzá a következő importálási utasításokat.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Computer Vision erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

> [!div class="nextstepaction"]
> [Megállítottam az ügyfelet](?success=set-up-client#object-model) , hogy [egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a rendszerkép-elemzés Node.js SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Ez az osztály minden Computer Vision funkcióhoz szükséges. Ezt az előfizetési adatokkal hozza létre, és a legtöbb képművelet végrehajtásához használja.|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes)| Ez az enumerálás a képelemzés különböző típusait határozza meg, amelyeket szabványos elemzési műveletekben lehet elvégezni. Az igényeinek megfelelően adja meg a **VisualFeatureTypes** -értékek készletét. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Node.js rendszerkép-elemzési ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése


Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) objektumot a kulccsal és a végponttal, és használja egy [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) objektum létrehozásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Ezután Definiáljon egy függvényt, `computerVision` és deklaráljon egy aszinkron sorozatot az elsődleges függvény és a visszahívási függvénnyel. Adja hozzá a rövid útmutató kódját az elsődleges függvényhez, és hívja meg a `computerVision` szkript alján. A rövid útmutatóban szereplő további kód a függvényen belülre mutat `computerVision` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Hitelesítettem a-ügyfelet](?success=authenticate-client#analyze-an-image) [, hogy egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)

## <a name="analyze-an-image"></a>Rendszerkép elemzése

Az ebben a szakaszban található kód elemzi a távoli képeket a különböző vizualizációs funkciók kinyeréséhez. Ezeket a műveleteket az **analyzeImage** metódusának részeként is elvégezheti, vagy meghívhatja őket az egyes metódusok használatával. A részletekért tekintse meg a [dokumentációt](/javascript/api/@azure/cognitiveservices-computervision/) .

> [!NOTE]
> Elemezheti a helyi rendszerképet is. Tekintse meg a [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) metódusokat, például a **analyzeImageInStream**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) található mintakód-kódot.

### <a name="get-image-description"></a>Rendszerkép lekérése – Leírás

A következő kód beolvassa a rendszerképhez létrehozott feliratok listáját. További részletekért tekintse meg a [képek leírása](../../concept-describing-images.md) című témakört.

Először határozza meg az elemezni kívánt rendszerkép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Ezután adja hozzá a következő kódot a rendszerkép leírásának beszerzéséhez, és nyomtassa ki a-konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Rendszerkép beolvasása kategória

A következő kód lekéri a rendszerkép észlelt kategóriáját. További részletekért tekintse meg a [képek kategorizálása](../../concept-categorizing-images.md) című témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

A segítő függvény megadása `formatCategories` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Képcímkék beolvasása

A következő kód beolvassa az észlelt címkék készletét a képen. További részletekért tekintse meg a [tartalom címkéit](../../concept-tagging-images.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

A segítő függvény megadása `formatTags` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő kód észleli a rendszerképben szereplő általános objektumokat, és kiírja azokat a konzolra. További részletekért lásd az [objektum észlelése](../../concept-object-detection.md) című témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Adja meg a segítő függvényt, `formatRectObjects` hogy a felső, bal, alsó és jobb koordinátákat adja vissza a szélességgel és a magassággal együtt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a rendszerképben, és kinyomtatja őket a konzolra. További részletekért lásd a [márka észlelését](../../concept-brand-detection.md) ismertető témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő kód az észlelt arcokat adja vissza a képen a téglalap koordinátáival, majd a Face attribútumok elemet. További részletekért tekintse meg az [Arcfelismerés](../../concept-detecting-faces.md) című témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

A segítő függvény megadása `formatRectFaces` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, zamatos vagy véres tartalom észlelése

A következő kód kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További részletekért tekintse meg a [felnőtt, a zamatos és a véres tartalmat](../../concept-detecting-adult-content.md) .

Adja meg a használni kívánt rendszerkép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Ezután adja hozzá a következő kódot a felnőtt tartalom észleléséhez, és nyomtassa ki az eredményeket a-konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Rendszerképek színsémájának beolvasása

A következő kód az észlelt színattribútumokat nyomtatja ki a képen, például a domináns színeket és a kiejtés színét. További részletekért lásd: [Színsémák](../../concept-detecting-color-schemes.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Adja meg a segítő függvényt a `printColorScheme` színséma adatainak a konzolra való kinyomtatásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Tartományhoz tartozó tartalom beolvasása

A képelemzés speciális modellt használhat a lemezképek további elemzéséhez. További részletekért tekintse meg a [tartományra vonatkozó tartalmat](../../concept-detecting-domain-content.md) .

Először határozza meg az elemezni kívánt rendszerkép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

A következő kód az észlelt tereptárgyak adatait elemzi a képen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Adja meg a segítő függvényt `formatRectDomain` az észlelt tereptárgyak hely adatainak elemzéséhez.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>A rendszerkép típusának beolvasása

A következő kód a képtípussal kapcsolatos információkat jeleníti &mdash; meg, legyen szó ClipArt vagy vonalas rajzolásról.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

A segítő függvény megadása `describeType` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [Elemeztem egy olyan képet](?success=analyze-image#run-the-application) [, amelyet egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=analyze-image)



## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `node` fájlban található paranccsal.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Futtattam az alkalmazást a](?success=run-the-application#clean-up-resources) [probléma megoldásához](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [](?success=clean-up-resources#next-steps) [Egy hibába ütközött az](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources) erőforrások

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan telepítheti a rendszerkép-elemzési ügyféloldali kódtárat, és hogyan készíthet alapszintű képelemzési hívásokat. Következő lépésként tekintse meg az API-funkciók elemzését ismertető témakört.

> [!div class="nextstepaction"]
>[Az elemzési API meghívása](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Képek elemzése – áttekintés](../../overview-image-analysis.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)található.

