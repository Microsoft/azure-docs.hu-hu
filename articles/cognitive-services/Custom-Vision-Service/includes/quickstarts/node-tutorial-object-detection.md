---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: de8ca0a9410479b4d166a47e5c56742955b7853f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725166"
---
Ez az útmutató útmutatást és mintakódot tartalmaz, amelyek segítenek az Custom Vision ügyféloldali kódtárának Node.js egy objektumészlelési modell felépítésében. Létre fog hozni egy projektet, címkéket fog hozzáadni, betanítja a projektet, majd a projekt előrejelzési végpontjának URL-címével programozott módon teszteli azt. Ezt a példát sablonként használhatja saját képfelismerő alkalmazás építéséhez.

> [!NOTE]
> Ha kódírás nélkül szeretne objektumészlelési modellt felépíteni és betaníteni, tekintse meg inkább a [böngészőalapú útmutatót.](../../get-started-build-detector.md) 

Használja a Custom Vision .NET-hez használható ügyféloldali kódtárát a következőre:

* Új Custom Vision-projekt létrehozása
* Címkék hozzáadása a projekthez
* Képek feltöltése és címkézése
* A projekt betanítása
* Az aktuális iteráció közzététele
* Az előrejelzési végpont tesztelése

[Referenciadokumentáció (betanítás)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(előrejelzés) |](/javascript/api/@azure/cognitiveservices-customvision-prediction/) Kódtár [forráskódja (betanítás)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(előrejelzés)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Csomag (npm) [(betanítás)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(előrejelzés)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)  |  [minták](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* ANode.js[](https://nodejs.org/)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Custom Vision-erőforrást, és hozzon létre egy Custom Vision-erőforrást az Azure Portal-ban egy betanítás és előrejelzési erőforrás létrehozásához, valamint a kulcsok és a végpont lekért <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> létrehozásához. Várja meg az üzembe helyezést, és kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrások kulcsra és végpontra ahhoz, hogy az alkalmazást a Custom Vision. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg. 

```console
mkdir myapp && cd myapp
```

Az parancs `npm init` futtatásával hozzon létre egy node-alkalmazást egy `package.json` fájllal. 

```console
npm init
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Ha képelemző alkalmazást Custom Vision a Node.js, szüksége lesz a Custom Vision NPM-csomagokra. A telepítésükhez futtassa a következő parancsot a PowerShellben:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Az alkalmazás `package.json` fájlja frissül a függőségekkel.

Hozzon létre egy nevű fájlt, `index.js` és importálja a következő kódtárakat:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)amely a rövid útmutatóban található példakódokat tartalmazza.

Hozzon létre változókat az erőforrás Azure-végpontja és kulcsai számára. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Custom Vision szakaszban létrehozott erőforrás-erőforrások üzembe helyezése sikeresen megtörtént, kattintson az **Erőforrás** ugrás gombra a **Következő lépések területen.**  A kulcsokat és a végpontot  az erőforrások kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.** A betanítás és az előrejelzési erőforrások kulcsait, valamint a betanító erőforrás API-végpontját is be kell szereznie.
>
> Ne felejtse el eltávolítani a kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információért tekintse meg a Cognitive Services [cikkét.](../../../cognitive-services-security.md)

Adjon hozzá mezőket a projekt nevéhez és egy időtúllépési paramétert az aszinkron hívásokhoz.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Objektummodell

|Név|Leírás|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Ez az osztály kezeli a modellek létrehozását, betanítását és közzétételét. |
|[PredictionAPIClient (ElőrejelzésiAPIClient)](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Ez az osztály kezeli a modellek objektumészlelési előrejelzésekre való lekérdezését.|
|[Jóslás](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Ez az interfész egyetlen előrejelzést határoz meg egyetlen képhez. Tartalmazza az objektumazonosító és -név tulajdonságait, valamint egy megbízhatósági pontszámot.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan kell elvégezni a következő feladatokat Custom Vision JavaScript ügyféloldali kódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Új Custom Vision-projekt létrehozása](#create-a-new-custom-vision-project)
* [Címkék hozzáadása a projekthez](#add-tags-to-the-project)
* [Képek feltöltése és címkézése](#upload-and-tag-images)
* [A projekt betanítása](#train-the-project)
* [Az aktuális iteráció közzététele](#publish-the-current-iteration)
* [Az előrejelzési végpont tesztelése](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Példányosíthatja az ügyfélobjektumokat a végponttal és a kulccsal. Hozzon létre **egy ApiKeyCredentials** objektumot a kulccsal, és használja a végponttal egy [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) és [PredictionAPIClient objektum](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient) létrehozásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Segítő függvény hozzáadása

Adja hozzá a következő függvényt több aszinkron híváshoz. Ezt később fogja használni.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Új Custom Vision-projekt létrehozása

Indítson el egy új függvényt, amely az összes Custom Vision tartalmazza. Adja hozzá a következő kódot a alkalmazáshoz egy új Custom Vision szolgáltatásprojekt létrehozásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Először töltse le a projekthez a mintaképeket. Mentse a Képek [mintamappa tartalmát](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) a helyi eszközre.

> [!NOTE]
> A betanítás befejezéséhez szélesebb képkészletre van szüksége? A Microsoft Garage-projekt, a Trove lehetővé teszi képek gyűjtését és megvásárlását betanítás céljából. Miután összegyűjtötte a képeket, letöltheti őket, majd a szokásos módon importálhatja Custom Vision saját projektjére. További [információért látogasson el a Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) oldalára.

A minta képek projekthez adásához, helyezze el a következő kódot a címke létrehozása után. Ez a kód a képeket a hozzájuk tartozó címkékkel együtt tölti fel. Ha képeket címkéz az objektum-észlelési projektekben, az egyes címkézett objektumok régióját normalizált koordináták használatával kell megadnia. Ebben az oktatóanyagban a régiók szoftveres kódba vannak kódolva. A régiók normalizált koordinátákban adják meg a határolókeretet, és a következő sorrendben adják meg a koordinátákat: bal oldali, felső, szélesség, magasság. Egyetlen kötegben legfeljebb 64 képet tölthet fel.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> Módosítania kell az images ( ) elérési útját az alapján, hogy hová letöltötte Cognitive Services `sampleDataRoot` Python SDK-minták tára.

> [!NOTE]
> Ha nem tudja megjelölni a régiók koordinátáit egy kattintással és húzással, a következő webhelyen használhatja a [webes felhasználói Customvision.ai.](https://www.customvision.ai/) Ebben a példában a koordináták már meg vannak téve.


## <a name="train-the-project"></a>A projekt betanítása

Ez a kód létrehozza az előrejelzési modell első iterációját. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Ez a kód közzéteszi a betanított iterációt az előrejelzési végponton. A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Az iteráció nem érhető el az előrejelzési végponton, amíg közzé nem tette.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Az előrejelzési végpont tesztelése

Ha képet szeretne küldeni az előrejelzési végpontra és lekérni az előrejelzést, adja hozzá a következő kódot a függvényhez. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Ezután zárja be a Custom Vision függvényt, és hívja meg.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `node` paranccsal a gyorsindítási fájlban.

```shell
node index.js
```

Az alkalmazás kimenetének meg kell jelennie a konzolon. Ezután ellenőrizheti, hogy a tesztkép (a **<sampleDataRoot> /Test/** területen található) megfelelően van-e megcímkézve, és hogy az észlelési régió helyes-e. Vissza is léphet a [Custom Vision webhelyére](https://customvision.ai), és megtekintheti az újonnan létrehozott projekt aktuális állapotát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Következő lépések

Ezzel az objektumészlelési folyamat minden lépését megtette a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran többször is be kell majd képezni és tesztelni kell a modellt, hogy pontosabb legyen. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* A minta forráskódja a [GitHubon található](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)
* [SDK-referenciadokumentáció (képzés)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [SDK-referenciadokumentáció (előrejelzés)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
