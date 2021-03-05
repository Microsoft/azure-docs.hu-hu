---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 2a4040c056b9b3b3e82d394807a4381b888ca640
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102184216"
---
Ismerkedés a Custom Vision a Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az objektum-észlelési modell felépítési példájának kódját. Létrehoz egy projektet, címkéket ad hozzá, betanítja a projektet, és a projekt előrejelzési végpontjának URL-címét használja a programozott teszteléshez. Ez a példa sablonként használható a saját rendszerkép-felismerő alkalmazás létrehozásához.

> [!NOTE]
> Ha kód írása _nélkül_ szeretne létrehozni és betanítani egy objektum-észlelési modellt, tekintse meg a [böngészőalapú útmutatást](../../get-started-build-detector.md) .

A Pythonhoz készült Custom Vision ügyféloldali kódtára a következőre használható:

* Új Custom Vision-projekt létrehozása
* Címkék hozzáadása a projekthez
* Képek feltöltése és címkézése
* A projekt betanítása
* Az aktuális iteráció közzététele
* Az előrejelzési végpont tesztelése

[Dokumentáció](/python/api/overview/azure/cognitiveservices/customvision)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision)  |  [Csomag (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/)  |  [Példák](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A Python-telepítésnek tartalmaznia kell a [pip](https://pip.pypa.io/en/stable/)-et. Ha a parancssorban fut, ellenőrizze, hogy a pip telepítve van-e `pip --version` . Szerezze be a pip-et a Python legújabb verziójának telepítésével.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" hozzon létre egy Custom Vision erőforrást egy "  target="_blank"> Custom Vision erőforrás létrehozásához <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal egy képzési és előrejelzési erőforrás létrehozásához, valamint a kulcsok és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Az alkalmazás Custom Visionhoz való összekapcsolásához szüksége lesz a létrehozott erőforrások kulcsára és végpontra. A kulcsokat és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Ha Custom Vision for Pythont szeretne írni egy rendszerkép-elemzési alkalmazásba, szüksége lesz a Custom Vision ügyféloldali könyvtárára. A Python telepítése után futtassa a következő parancsot a PowerShellben vagy a konzol ablakban:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-fájlt, és importálja a következő könyvtárakat.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Hozzon létre változókat az erőforrás Azure-végpontja és előfizetési kulcsai számára.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_creds)]


> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Custom Vision erőforrások telepítése sikeresen megtörtént, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsokat és végpontokat az **Erőforrás-kezelés** területen találja az erőforrások **kulcs és végpont** oldalain. A betanítási és előrejelzési kulcsokat is meg kell kapnia, valamint a képzési erőforrások végpontját.
>
> A megjelenő erőforrás-azonosító értékét az erőforrás **Áttekintés** lapján, az **előfizetés-azonosító** mezőben találja.
>
> Ne felejtse el eltávolítani a kulcsokat a kódból, amikor elkészült, és soha ne tegye közzé őket nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../../cognitive-services/cognitive-services-security.md) cikkben talál.

## <a name="object-model"></a>Objektummodell

|Név|Leírás|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ez az osztály kezeli a modellek létrehozását, betanítását és közzétételét. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ez az osztály kezeli a modellek lekérdezését az objektum-észlelési előrejelzésekhez.|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Ez az osztály egyetlen objektumra vonatkozó előrejelzést definiál egyetlen rendszerképben. Az objektum AZONOSÍTÓját és nevét, az objektum határoló mező helyét és egy megbízhatósági pontszámot tartalmaz.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült Custom Vision ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Új Custom Vision-projekt létrehozása](#create-a-new-custom-vision-project)
* [Címkék hozzáadása a projekthez](#add-tags-to-the-project)
* [Képek feltöltése és címkézése](#upload-and-tag-images)
* [A projekt betanítása](#train-the-project)
* [Az aktuális iteráció közzététele](#publish-the-current-iteration)
* [Az előrejelzési végpont tesztelése](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy képzési és előrejelzési ügyfél létrehozása a végponttal és a kulcsokkal. Hozzon létre **ApiKeyServiceClientCredentials** objektumokat a kulcsokkal, és használja őket a végponttal egy [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) és egy [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient) objektum létrehozásához.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Új Custom Vision-projekt létrehozása

Adja hozzá a következő kódot a szkripthez egy új Custom Vision Service-projekt létrehozásához. 

A projekt létrehozásakor további beállítások megadásához tekintse meg a [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-&preserve-view=true) metódust (a [Kiderítő webportál összeállításának](../../get-started-build-detector.md) útmutatója).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Címkék hozzáadása a projekthez

Ha a projektben szeretné létrehozni az objektum címkéit, adja hozzá a következő kódot:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Először töltse le a projekthez tartozó mintaképeket. Mentse a [Sample images mappa](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) tartalmát a helyi eszközre.

> [!NOTE]
> Szüksége van egy szélesebb körű rendszerképekre a képzés befejezéséhez? A Microsoft garázs-projekt, amely lehetővé teszi, hogy beszerezze a betanítási célokra szánt lemezképek készleteit. A képek összegyűjtése után letöltheti őket, majd a szokásos módon importálhatja őket a Custom Vision-projektbe. További információért látogasson el az [adattárház oldalára](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) .

Ha képeket címkéz az objektum-észlelési projektekben, az egyes címkézett objektumok régióját normalizált koordináták használatával kell megadnia. A következő kód a mintául szolgáló rendszerképeket társítja a címkézett régiójával. A régiók normalizált koordinátákban adják meg a határolókeretet, és a következő sorrendben adják meg a koordinátákat: bal oldali, felső, szélesség, magasság.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tagging)]

> [!NOTE]
> Ha nem rendelkezik kattintással és húzással a régiók koordinátáinak megjelöléséhez, használhatja a webes felhasználói felületet a következő címen: [Customvision.ai](https://www.customvision.ai/). Ebben a példában a koordináták már meg vannak biztosítva.

Ezután a társítások ezen térképével feltöltheti az egyes mintaképeket a régió koordinátáival (legfeljebb 64 lemezképet tölthet fel egyetlen kötegben). Adja hozzá a következő kódot.


[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> A lemezképek elérési útját módosítania kell, attól függően, hogy a Cognitive Services Python SDK Samples-tárházat korábban letöltötte.

## <a name="train-the-project"></a>A projekt betanítása

Ez a kód az előrejelzési modell első iterációját hozza létre. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Betanítás kiválasztott címkékkel
>
> Igény szerint betaníthatja az alkalmazott címkék egy részhalmazát. Ezt akkor érdemes megtenni, ha még nem alkalmazta elég bizonyos címkéket, de másokkal is rendelkezik. A **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** hívásban állítsa a választható paramétert *selected_tags* a használni kívánt címkék azonosító sztringek listájára. A modell betanításával csak a listán szereplő címkéket ismeri fel a rendszer.

## <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Egy iteráció nem érhető el az előrejelzési végponton, amíg közzé nem teszi. A következő kód a modell aktuális iterációját teszi elérhetővé a lekérdezéshez. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Az előrejelzési végpont tesztelése

A képek előrejelzési végpontra való küldéséhez és az előrejelzés lekéréséhez adja hozzá a következő kódot a fájl végéhez:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_test)]


## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa a *CustomVisionQuickstart.py*.

```powershell
python CustomVisionQuickstart.py
```

Az alkalmazás kimenetének meg kell jelennie a konzolon. Ezután ellenőrizheti, hogy a **<base_image_location>/images/test** található) tesztelési rendszerkép megfelelően van-e megjelölve, és hogy az észlelési régió helyes-e. Vissza is léphet a [Custom Vision webhelyére](https://customvision.ai), és megtekintheti az újonnan létrehozott projekt aktuális állapotát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Következő lépések

Most végrehajtotta az objektum-észlelési folyamat minden lépését a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran több alkalommal kell betanítania és tesztelni a modellt, hogy pontosabb legyen. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* A minta forráskódja megtalálható a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py)
* [Az SDK dokumentációja](/python/api/overview/azure/cognitiveservices/customvision)
