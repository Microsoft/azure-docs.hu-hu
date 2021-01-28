---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, cog-serv-seo-aug-2020
ms.openlocfilehash: b44fae4d2a4c8d0a60d751068e765932fed7e8fb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948423"
---
A Pythonhoz készült Language Understanding (LUIS) ügyféloldali kódtárak a következőket használhatják:

* Alkalmazás létrehozása
* Egy cél, egy gépi megtanult entitás hozzáadása egy példához
* Alkalmazás betanítása és közzététele
* Előrejelzési futtatókörnyezet lekérdezése

[Dokumentáció](/python/api/azure-cognitiveservices-language-luis/index)  |  [Szerzői műveletek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/authoring) és [előrejelzési](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/runtime) függvénytár forráskódja | [Csomag (PyPI)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/sdk-3x/authoring_and_predict.py)

## <a name="prerequisites"></a>Előfeltételek

* A [Python 3. x](https://www.python.org/)jelenlegi verziója.
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* Ha már rendelkezik Azure-előfizetéssel, [hozzon létre egy Language Understanding szerzői erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) a Azure Portal a kulcs és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a [létrehozott](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) erőforrás kulcsára és végpontra az alkalmazás Language Understanding létrehozásához való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja. A szolgáltatás kipróbálásához használhatja az ingyenes díjszabási szintet ( `F0` ).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. A konzol ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és helyezze át a könyvtárba.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Hozzon létre egy nevű fájlt `authoring_and_predict.py` a Python-kódjához.

    ```console
    touch authoring_and_predict.py
    ```

### <a name="install-the-client-library-with-pip"></a>Az ügyféloldali kódtár telepítése a pip-sel

Az alkalmazás könyvtárában telepítse a Pythonhoz készült Language Understanding (LUIS) ügyféloldali kódtárat a következő paranccsal:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="authoring-object-model"></a>Szerzői objektummodell

A Language Understanding (LUIS) authoring Client egy [LUISAuthoringClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient) objektum, amely a szerzői kulcsot tartalmazó Azure-ba hitelesíti.

## <a name="code-examples-for-authoring"></a>Példák a szerzői műveletekre

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* Alkalmazások – [Létrehozás](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#add-application-create-object--custom-headers-none--raw-false----operation-config-), [Törlés](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [Közzététel](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Példa hosszúságú kimondott szöveg – [Hozzáadás köteg](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)szerint, [Törlés azonosító alapján](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Funkciók – [kifejezések listája](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* Modell – [leképezések](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) és entitások kezelése
* Minta – [minták](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-) kezelése
* Betanítás [az](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) alkalmazáshoz és a lekérdezés a [betanítási állapothoz](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Verziók](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations) – kezelés klónozással, exportálással és törléssel


## <a name="prediction-object-model"></a>Előrejelzési objektum modellje

A Language Understanding (LUIS) előrejelzési futtatókörnyezet ügyfele egy [LUISRuntimeClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient) objektum, amely az Azure-ba hitelesíti az erőforrás kulcsát.

## <a name="code-examples-for-prediction-runtime"></a>Példák az előrejelzési futtatókörnyezetre

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* Előrejelzés [átmeneti vagy üzemi tárolóhely](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-) alapján
* Előrejelzés [verziója](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-) szerint

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

Adja hozzá az ügyféloldali kódtárakat a Python-fájlhoz.

[!code-python[Add python libraries to code file](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=Dependencies)]


## <a name="add-boilerplate-code"></a>Szabványos kód hozzáadása

1. Adja hozzá a `quickstart` metódust és a hívását. Ez a metódus a fennmaradó kód legtöbbjét tartalmazza. Ezt a metódust a fájl végén kell meghívni.

    ```python
    def quickstart():

        # add calls here, remember to indent properly

    quickstart()
    ```

1. Ha másként nincs megadva, adja hozzá a további kódot a gyors üzembe helyezési metódushoz.

## <a name="create-variables-for-the-app"></a>Változók létrehozása az alkalmazáshoz

Hozzon létre két változót: az első módosítást, a második beállított értéket, ahogy a kód mintában szerepelnek. 

1. Hozzon létre változókat a szerzői kulcsok és az erőforrások neveinek tárolásához.

    [!code-python[Variables you need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouChange)]

1. Hozzon létre változókat a végpontok, az alkalmazás neve, a verzió és a leképezés nevének tárolásához.

    [!code-python[Variables you don't need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) objektumot a kulccsal, és használja a végpontján egy [LUISAuthoringClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient) objektum létrehozásához.

[!code-python[Authenticate the client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS-alkalmazás létrehozása

A LUIS-alkalmazás a természetes nyelvi feldolgozó (NLP) modellt tartalmazza, beleértve a szándékokat, az entitásokat és a példaként szolgáló hosszúságú kimondott szöveg.

Hozzon létre egy [AppsOperation](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations) -objektum [hozzáadási](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#add-application-create-object--custom-headers-none--raw-false----operation-config-) metódusát az alkalmazás létrehozásához. A név és a nyelvi kultúra kötelező tulajdonságai.

[!code-python[Create a LUIS app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Szándék létrehozása az alkalmazáshoz
A LUIS-alkalmazás modelljében lévő elsődleges objektum a szándék. A szándék összehangolja a felhasználói Kimondás _szándékait_ tartalmazó csoporttal. Előfordulhat, hogy egy felhasználó felteheti a kérdést, vagy egy olyan utasítást, amely egy bot (vagy más ügyfélalkalmazás) által megadott _kívánt_ választ keres. Ilyenek például a repülőjáratok foglalása, az időjárás megkérdezése egy adott célállomáson, és az ügyfélszolgálat elérhetőségi adatai.

Használja az [Model.add_intent](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) metódust az egyedi szándék nevével, majd adja át az alkalmazás azonosítóját, a verziószámát és az új leképezés nevét.

Az `intentName` érték nem módosítható az `OrderPizzaIntent` [alkalmazáshoz tartozó változók létrehozása](#create-variables-for-the-app) változóinak részeként.

[!code-python[Create intent for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Entitások létrehozása az alkalmazáshoz

Habár az entitások nem kötelezőek, a legtöbb alkalmazásban megtalálhatók. Az entitás kinyeri az adatokat a felhasználótól, és a felhasználó szándékának fullfil szükséges. Az [előre elkészített](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) és az egyéni entitások több típusa is van, amelyek mindegyike saját Adatátalakítási objektum-(DTO-) modellel rendelkezik.  Az alkalmazásba felvenni kívánt közös előre összeépített entitások közé tartozik a [Number](../luis-reference-prebuilt-number.md), a [datetimeV2](../luis-reference-prebuilt-datetimev2.md), a [geographyV2](../luis-reference-prebuilt-geographyv2.md)és a [sorszám](../luis-reference-prebuilt-ordinal.md).

Fontos tudni, hogy az entitások nincsenek megjelölve szándékkal megjelölve. Ezek általában számos szándékra vonatkoznak. Csak például a felhasználó hosszúságú kimondott szöveg van megjelölve egy adott, egyetlen szándékkal.

Az entitások létrehozási módszerei a [ModelOperations](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations) osztály részét képezik. Minden entitás típusa saját Adatátalakítási objektum (DTO) modellt tartalmaz.

Az entitás-létrehozási kód olyan gépi tanulási entitást hoz létre, amely alentitásokkal és az alentitásokra alkalmazott funkciókkal rendelkezik `Quantity` .

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Részleges képernyőkép a portálról, amely a létrehozott entitást, az alentitásokkal és a &quot;mennyiség&quot; alentitásokra alkalmazott funkciókkal rendelkező gépi tanulási entitást mutatja.":::

[!code-python[Create entities for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddEntities)]

A következő metódust a metódus fölé helyezve `quickstart` megkeresheti a mennyiség alentitásának azonosítóját, hogy hozzá lehessen rendelni a szolgáltatásokat az adott alentitáshoz.

[!code-python[Find subentity id](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Példaként való Kimondás hozzáadása a szándékhoz

Ha meg szeretné határozni a teljes szándékot, és kinyeri az entitásokat, az alkalmazásnak példákat kell hosszúságú kimondott szöveg. A példákban egy adott, egyetlen szándékot kell megcélozni, és az összes egyéni entitást meg kell jelölni. Az előre elkészített entitásokat nem kell megjelölni.

Adja hozzá például a hosszúságú kimondott szöveg egy [ExampleLabelObject](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject) -objektumok listájának létrehozásával, amely minden egyes példa kiírásának egy objektumát tartalmazza. Mindegyik példa minden entitást megjelöl az entitás neve és az entitás értéke név/érték párokkal rendelkező szótárával. Az entitás értékének pontosan úgy kell lennie, ahogy a példa szövegében megjelenik.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Részleges képernyőkép, amely a címkével ellátott példát mutatja a portálon. ":::

Hívja meg a [példákat. vegye fel](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations) az alkalmazás azonosítóját, a Version ID-t és a példát.

[!code-python[Add example utterance to intent](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Az alkalmazás betanítása

A modell létrehozása után a LUIS alkalmazást a modell ezen verziójára kell képezni. A betanított modell használható egy [tárolóban](../luis-container-howto.md), vagy [közzétehető](../luis-how-to-publish-app.md) az átmeneti vagy a termék tárolóhelyeken.

A [Train.train_version](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) metódusnak szüksége van az alkalmazás-azonosítóra és a verzió azonosítóra.

Nagyon kis modell, például ez a rövid útmutató mutatja, nagyon gyorsan betanítja. Üzemi szintű alkalmazások esetén az alkalmazásnak be kell vonnia a [get_status](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) metódus lekérdezési hívását, amely meghatározza, hogy mikor vagy ha sikeres a képzés. A válasz az egyes objektumokhoz külön állapotú [ModelTrainingInfo](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo) objektumok listája. Az összes objektumnak sikeresnek kell lennie ahhoz, hogy a képzés befejezze.

[!code-python[Train the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Alkalmazás közzététele az üzemi tárolóhelyen

Tegye közzé a LUIS alkalmazást az [app. publish](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) metódus használatával. Ez közzéteszi a jelenlegi betanított verziót a végponton megadott tárolóhelyre. Az ügyfélalkalmazás ezt a végpontot használja arra, hogy felhasználói hosszúságú kimondott szöveg küldjön a szándékok és az entitások kinyerésének előrejelzésére.

[!code-python[Publish app to production slot](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Az előrejelzési futtatókörnyezet ügyfelének hitelesítése

Használja a hitelesítő adatok objektumot a kulcsával, és használja azt a végponttal egy [LUISRuntimeClientConfiguration](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration) objektum létrehozásához.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-python[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Előrejelzés lekérése futtatókörnyezetből

Adja hozzá a következő kódot az előrejelzési futtatókörnyezethez való kérelem létrehozásához.

A felhasználó kimondása a [prediction_request](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest) objektum részét képezi.

A **[get_slot_prediction](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** metódusnak több paraméterre van szüksége, például az alkalmazás azonosítója, a tárolóhely neve és az előrejelzési kérelem objektum a kérelem teljesítéséhez. A többi lehetőség, például a részletes, az összes leképezés megjelenítése és a napló megadása nem kötelező. A kérelem egy [PredictionResponse](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse) objektumot ad vissza.

[!code-python[Get prediction from runtime](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `python` fájlban található paranccsal.

```console
python authoring_and_predict.py
```
