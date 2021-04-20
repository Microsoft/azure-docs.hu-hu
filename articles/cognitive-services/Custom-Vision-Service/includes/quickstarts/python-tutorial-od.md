---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: d73fef916c2652f1fa4b98fd84173c1ec0abb263
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725149"
---
A Pythonhoz készült Custom Vision első lépések. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az objektumészlelési modell építéséhez szükséges példakódot. Létre fog hozni egy projektet, címkéket fog hozzáadni, betanítja a projektet, majd a projekt előrejelzési végponti URL-címével programozott módon teszteli azt. Ezt a példát sablonként használhatja saját képfelismerő alkalmazásának építéséhez.

> [!NOTE]
> Ha kódírás nélkül szeretne objektumészlelési modellt felépíteni és betaníteni, tekintse meg inkább a [böngészőalapú útmutatót.](../../get-started-build-detector.md) 

Használja a Custom Vision Pythonhoz készült ügyféloldali kódtárát a következőre:

* Új Custom Vision-projekt létrehozása
* Címkék hozzáadása a projekthez
* Képek feltöltése és címkézése
* A projekt betanítása
* Az aktuális iteráció közzététele
* Az előrejelzési végpont tesztelése

[Referenciadokumentáció](/python/api/overview/azure/cognitiveservices/customvision)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision)  |  [Csomag (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/)  |  [Minták](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A Python-telepítésnek tartalmaznia kell a [pip et.](https://pip.pypa.io/en/stable/) A parancssorban futtatva ellenőrizheti, hogy telepítve `pip --version` van-e a pip. A pip lekért használatához telepítse a Python legújabb verzióját.
* Az Azure-előfizetés létrehozása után hozzon létre egy Custom Vision-erőforrást, és hozzon létre egy Custom Vision-erőforrást a Azure Portal-ban egy betanítás és előrejelzési erőforrás létrehozásához, valamint a kulcsok és a végpont lekért <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> létrehozásához. Várja meg az üzembe helyezést, majd kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrások kulcsra és végpontra ahhoz, hogy az alkalmazást a Custom Vision. A kulcsokat és a végpontot a rövid útmutató későbbi, lenti kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Pythonhoz készült Custom Vision megírásához szüksége lesz a Custom Vision ügyféloldali kódtárra. A Python telepítése után futtassa a következő parancsot a PowerShellben vagy egy konzolablakban:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-fájlt, és importálja a következő kódtárakat.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py)amely az ebben a rövid útmutatóban található példakódokat tartalmazza.

Hozzon létre változókat az erőforrás Azure-végpontja és előfizetési kulcsai számára.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_creds)]


> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Custom Vision szakaszban létrehozott erőforrás-erőforrások telepítése sikeresen megtörtént, kattintson az **Erőforráshoz** ugrás gombra a **Következő lépések területen.**  A kulcsokat és a végpontot  az erőforrások kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.** Le kell szereznie a betanítás és az előrejelzési erőforrások kulcsait, valamint a betanító erőforrás API-végpontját.
>
> Az előrejelzési erőforrás-azonosító értékét az  erőforrás Áttekintés lapján találja, amely **előfizetés-azonosítóként van felsorolva.**
>
> Ne felejtse el eltávolítani a kulcsokat a kódból, amikor végzett, és soha ne tegye közzé őket nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információt a biztonsági Cognitive Services [talál.](../../../cognitive-services-security.md)

## <a name="object-model"></a>Objektummodell

|Név|Leírás|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ez az osztály kezeli a modellek létrehozását, betanítását és közzétételét. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ez az osztály kezeli a modellek objektumészlelési előrejelzésekhez való lekérdezését.|
|[ImagePrediction (Képprediction)](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Ez az osztály egyetlen objektum-előrejelzést határoz meg egyetlen képen. Tartalmazza az objektumazonosító és -név tulajdonságait, a határolókeret helyét és a megbízhatósági pontszámot.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következőket mutatják be a Pythonhoz készült Custom Vision kódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Új Custom Vision-projekt létrehozása](#create-a-new-custom-vision-project)
* [Címkék hozzáadása a projekthez](#add-tags-to-the-project)
* [Képek feltöltése és címkézése](#upload-and-tag-images)
* [A projekt betanítása](#train-the-project)
* [Az aktuális iteráció közzététele](#publish-the-current-iteration)
* [Az előrejelzési végpont tesztelése](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy betanító és előrejelzési ügyfél példányosíthatja a végpontot és a kulcsokat. Hozza létre az **ApiKeyServiceClientCredentials** objektumokat a kulcsokkal, és használja őket a végponttal egy [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) és [CustomVisionPredictionClient objektum létrehozásához.](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Új Custom Vision-projekt létrehozása

Adja hozzá a következő kódot a szkripthez egy új Custom Vision Service-projekt létrehozásához. 

A [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-&preserve-view=true) létrehozásakor további beállítások megadásához tekintse meg a create_project metódust (lásd [a Detektor](../../get-started-build-detector.md) létrehozása webportál útmutatóját).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Címkék hozzáadása a projekthez

Ha objektumcímkéket szeretne létrehozni a projektben, adja hozzá a következő kódot:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Először töltse le a projekthez a mintaképeket. Mentse a Képek [mintamappa tartalmát](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) a helyi eszközre.

> [!NOTE]
> A betanítás befejezéséhez szélesebb képkészletre van szüksége? A Microsoft Garage projekt, a Trove lehetővé teszi képek gyűjtését és megvásárlását betanítás céljából. Miután összegyűjtötte a képeket, letöltheti őket, majd a szokásos módon importálhatja Custom Vision saját projektjére. További [információért látogasson el a Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) oldalára.

Ha képeket címkéz az objektum-észlelési projektekben, az egyes címkézett objektumok régióját normalizált koordináták használatával kell megadnia. A következő kód a mintául szolgáló rendszerképeket társítja a címkézett régiójával. A régiók normalizált koordinátákban adják meg a határolókeretet, és a következő sorrendben adják meg a koordinátákat: bal oldali, felső, szélesség, magasság.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tagging)]

> [!NOTE]
> Ha nem tudja megjelölni a régiók koordinátáit kattintással és húzással, a webes felhasználói felületet a következő [Customvision.ai.](https://www.customvision.ai/) Ebben a példában a koordináták már meg vannak téve.

Ezután a társítások ezen térképével feltöltheti az egyes mintaképeket a régió koordinátáival (legfeljebb 64 képet tölthet fel egyetlen kötegbe). Adja hozzá a következő kódot.


[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Módosítania kell a képek elérési útját az alapján, hogy hová letöltötte Cognitive Services Python SDK-minták tárhelyét.

## <a name="train-the-project"></a>A projekt betanítása

Ez a kód hozza létre az előrejelzési modell első iterációját. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Betanítás kijelölt címkékkel
>
> Igény szerint az alkalmazott címkéknek csak egy részkészletét is betaníthatja. Ezt akkor lehet jó, ha még nem alkalmazott elég címkét, de van elég más címkéből. A **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** a nem kötelező paramétert *selected_tags* a használni kívánt címkék azonosító sztringjeinek listájára. A modell betanítja, hogy csak a listában található címkéket ismerje fel.

## <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Az iteráció nem érhető el az előrejelzési végponton, amíg közzé nem tette. A következő kód elérhetővé teszi a modell aktuális iterációját a lekérdezéshez. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Az előrejelzési végpont tesztelése

A képek előrejelzési végpontra való küldéséhez és az előrejelzés lekéréséhez adja hozzá a következő kódot a fájl végéhez:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_test)]


## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa *a CustomVisionQuickstart.py.*

```powershell
python CustomVisionQuickstart.py
```

Az alkalmazás kimenetének meg kell jelennie a konzolon. Ezután ellenőrizheti, hogy a tesztkép (amely **a /images/Test**<base_image_location>található) megfelelően van-e megcímkézve, és hogy az észlelési régió helyes-e. Vissza is léphet a [Custom Vision webhelyére](https://customvision.ai), és megtekintheti az újonnan létrehozott projekt aktuális állapotát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Következő lépések

Ezzel az objektumészlelési folyamat minden lépését megtette a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran többször is be kell majd képezni és tesztelni kell a modellt, hogy pontosabb legyen. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* A minta forráskódja a [GitHubon található](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py)
* [SDK-referenciadokumentáció](/python/api/overview/azure/cognitiveservices/customvision)
