---
title: 'Gyors útmutató: optikai karakterfelismerési ügyféloldali kódtár a Pythonhoz'
description: Ebben a rövid útmutatóban megismerheti a Pythonhoz készült optikai karakterfelismerési ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: d0b2a854391097cc7d95c4286ba581f3660d397e
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284781"
---
<a name="HOLTop"></a>

Az optikai karakterfelismerési ügyféloldali kódtár használatával a nyomtatott és a kézírásos szöveg olvasható az olvasási API-val.

[Dokumentáció](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision)  |  [Csomag (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/)  |  [Példák](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A Python-telepítésnek tartalmaznia kell a [pip](https://pip.pypa.io/en/stable/)-et. Ha a parancssorban fut, ellenőrizze, hogy a pip telepítve van-e `pip --version` . Szerezze be a pip-et a Python legújabb verziójának telepítésével.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.

    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az ügyféloldali kódtár a használatával telepíthető:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Telepítse a párna könyvtárat is.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Hozzon létre egy új Python &mdash; -*Quickstart-file.py*, például:. Ezután nyissa meg az előnyben részesített szerkesztőben vagy IDE-ben.

### <a name="find-the-subscription-key-and-endpoint"></a>Az előfizetés kulcsának és végpontjának megkeresése

Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Computer Vision erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. Az előfizetési kulcsot és a végpontot az erőforrás- **kezelés** lehetőség alatt találja az erőforrás **kulcs és végpont** lapján. 

Hozzon létre változókat a Computer Vision előfizetési kulcs és végpont számára. Illessze be az előfizetési kulcsot és a végpontot a következő kódra, ahol az szerepel. Az Computer Vision-végpont rendelkezik az űrlappal `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Ne feledje, hogy ha elkészült, távolítsa el az előfizetési kulcsot a kódból, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Például az [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Megállítottam az ügyfelet](?success=set-up-client#object-model) , hogy [egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az OCR Python SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Ez az osztály közvetlenül kezeli az összes képműveletet, például a képek elemzését, a szöveg észlelését és a miniatűr létrehozását.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Ez az osztály minden Computer Vision funkcióhoz szükséges. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja. Ez megvalósítja a **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Ez az enumerálás a képelemzés különböző típusait határozza meg, amelyeket szabványos elemzési műveletekben lehet elvégezni. Az igényeinek megfelelően adja meg a **VisualFeatureTypes** -értékek készletét. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Python-hez készült OCR ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Nyomtatott és kézzel írt szöveg olvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) objektumot a kulccsal, és használja a végpontján egy [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) objektum létrehozásához.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [Hitelesítettem a-ügyfelet](?success=authenticate-client#read-printed-and-handwritten-text) [, hogy egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

Az OCR szolgáltatás megjelenítheti a képen látható szöveget, és átalakíthatja a karakteres adatfolyamba. Ezt két részből kell elvégezni.

### <a name="call-the-read-api"></a>Az olvasási API meghívása

Először a következő kód használatával hívja meg az **olvasási** metódust az adott képhez. Ez egy műveleti azonosítót ad vissza, és elindít egy aszinkron folyamatot a rendszerkép tartalmának olvasásához.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> Helyi rendszerképből is beolvashat szöveget. Tekintse meg a [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) metódusokat, például a **read_in_stream**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) található mintakód-kódot.

### <a name="get-read-results"></a>Olvasási eredmények beolvasása

Ezután kérje le az **olvasási** hívás által visszaadott művelet azonosítóját, és használja a szolgáltatás lekérdezésére a művelet eredményeinek lekérdezéséhez. A következő kód ellenőrzi a műveletet egy másodperces időközönként, amíg az eredmények vissza nem állnak. Ezután kinyomtatja a kinyert szöveges adatát a konzolon.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [Beolvastam](?success=read-printed-handwritten-text#run-the-application) a [problémát](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `python` fájlban található paranccsal.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [Futtattam az alkalmazást a](?success=run-the-application#clean-up-resources) [probléma megoldásához](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [](?success=clean-up-resources#next-steps) [Egy hibába ütközött az](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources) erőforrások

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Python OCR-függvénytárát a feladatok elvégzésére. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
>[OCR API-referenciák (Python)](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [OCR – áttekintés](../../overview-ocr.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)található.
