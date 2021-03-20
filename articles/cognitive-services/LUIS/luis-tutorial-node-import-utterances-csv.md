---
title: Hosszúságú kimondott szöveg importálása Node.js-LUIS használatával
titleSuffix: Azure Cognitive Services
description: A LUIS authoring API használatával megtudhatja, hogyan hozhat létre programozott módon egy LUIS-alkalmazást a meglévő adatokból CSV formátumban.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, devx-track-js
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/05/2019
ms.openlocfilehash: 58eb92f4d0bc3de4671ca2ece14a178a876e4a6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541041"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>A LUIS-alkalmazások programozott módon történő létrehozása Node.js

A LUIS egy programozott API-t biztosít, amely mindent tesz a [Luis](luis-reference-regions.md) -webhelynek. Ez időt takaríthat meg, ha korábban már meglévő adatokkal rendelkezik, és gyorsabb megoldás, ha a LUIS-alkalmazást programozott módon hozza létre, mint az adatok kézzel történő megadásával.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Előfeltételek

* Jelentkezzen be a [Luis](luis-reference-regions.md) webhelyre, és keresse meg a [szerzői kulcsot](luis-how-to-azure-subscription.md#authoring-key) a Fiókbeállítások menüpontban. Ezt a kulcsot használja a szerzői API-k meghívásához.
* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).
* Ez a cikk CSV-vel kezdődik a feltételezett vállalati naplófájlok felhasználói kérésekhez. Töltse le [itt](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Telepítse a legújabb Node.jst a NPM. Töltse le innen [.](https://nodejs.org/en/download/)
* **[Ajánlott]** A Visual Studio Code az IntelliSense és a hibakeresés szolgáltatáshoz ingyenesen [letölthető innen.](https://code.visualstudio.com/)

A cikkben szereplő összes kód az [Azure-samples Language Understanding GitHub-tárházban](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv)érhető el.

## <a name="map-preexisting-data-to-intents-and-entities"></a>A meglévő adatleképezések és entitások leképezése
Ha olyan rendszerről van szó, amely nem a LUIS használatával lett létrehozva, akkor is, ha olyan szöveges adatokat tartalmaz, amelyek a felhasználók által elvégezhető különböző dolgokra mutatnak, előfordulhat, hogy a meglévő kategóriából származó, a LUIS-ba irányuló felhasználói adatbevitelből származó leképezést szeretne leképezni. Ha a felhasználók által említett fontos szavakat vagy kifejezéseket azonosítani tudja, ezek a szavak az entitásokra képezhetők le.

Nyissa meg a [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) fájlt. Egy feltételezett otthoni Automation szolgáltatáshoz tartozó felhasználói lekérdezések naplóját tartalmazza, beleértve a kategorizálásuk módját, a felhasználó által említett és néhány oszlop hasznos információkkal való kihúzását.

![Korábbi adatmennyiségű CSV-fájl](./media/luis-tutorial-node-import-utterances-csv/csv.png)

Láthatja, hogy a **RequestType** oszlop lehet leképezés, és a **kérelem** oszlop egy példaként szolgáló kifejezésre mutat. A többi mező lehet entitás, ha a teljes értékben szerepelnek. Mivel vannak olyan leképezések, entitások és példa hosszúságú kimondott szöveg, amelyek egy egyszerű, minta alkalmazásra vonatkoznak.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>A LUIS-alkalmazás létrehozásának lépései a nem LUIS-adatokból
Új LUIS-alkalmazás létrehozása a CSV-fájlból:

* Az adatok elemzése a CSV-fájlból:
    * Konvertáljon olyan formátumra, amelyet a LUIS használatával tölthet fel a szerzői API-val.
    * Az elemzett adatokból gyűjtsön információkat a szándékokról és az entitásokról.
* API-hívások készítése a következőhöz:
    * Hozza létre az alkalmazást.
    * Az elemzett adatokból összegyűjtött leképezések és entitások hozzáadása.
    * Miután létrehozta a LUIS alkalmazást, felveheti a példa hosszúságú kimondott szöveg az elemzett adatokból.

Ezt a programot a fájl utolsó részében tekintheti meg `index.js` . Másolja vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) ezt a kódot, és mentse a következőbe: `index.js` .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>A CSV értelmezése

A CSV-hosszúságú kimondott szöveg tartalmazó oszlop-bejegyzéseket a LUIS által értelmezhető JSON formátumba kell elemezni. Ennek a JSON-formátumnak tartalmaznia kell egy `intentName` mezőt, amely azonosítja a kiírás szándékát. Tartalmaznia kell egy mezőt is `entityLabels` , amely akkor lehet üres, ha nincsenek a teljes entitások.

Például a "fények bekapcsolása" bejegyzést a következő JSON-ra kell leképezni:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

Ebben a példában a a `intentName` CSV-fájlban a **kérelem** oszlop fejlécében szereplő felhasználói kérelemből származik, és a `entityName` többi oszlopból származik, amely a legfontosabb információkat tartalmazza. Ha például van egy **művelet** vagy **eszköz** bejegyzése, és ez a karakterlánc a tényleges kérelemben is szerepel, akkor entitásként is címkézhető. A következő kód ezt az elemzési folyamatot mutatja be. Másolhatja vagy [letöltheti](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) , és mentheti a alkalmazásba `_parse.js` .

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>A LUIS-alkalmazás létrehozása
Miután az adatelemzés JSON-ba került, adja hozzá egy LUIS-alkalmazáshoz. A következő kód létrehozza a LUIS alkalmazást. Másolja vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) , majd mentse a alkalmazásba `_create.js` .

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Leképezések hozzáadása
Ha már van alkalmazás, meg kell adnia. A következő kód létrehozza a LUIS alkalmazást. Másolja vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) , majd mentse a alkalmazásba `_intents.js` .

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Entitások hozzáadása
A következő kód hozzáadja az entitásokat a LUIS alkalmazáshoz. Másolja vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) , majd mentse a alkalmazásba `_entities.js` .

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]



## <a name="add-utterances"></a>Beszédmódok hozzáadása
Miután az entitások és a leképezések meg lettek adva a LUIS alkalmazásban, felveheti a hosszúságú kimondott szöveg. A következő kód a [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API-t használja, amely egyszerre akár 100 hosszúságú kimondott szöveg-t is hozzáadhat.  Másolja vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) , majd mentse a alkalmazásba `_upload.js` .

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>A kód futtatása


### <a name="install-nodejs-dependencies"></a>Node.js függőségek telepítése
Telepítse a Node.js függőségeket a NPM-ből a terminál/parancssorban.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Konfigurációs beállítások módosítása
Az alkalmazás használatához módosítania kell a index.js fájl értékeit a saját végponti kulcsra, és meg kell adnia az alkalmazás nevét. Megadhatja az alkalmazás kulturális környezetét is, vagy megváltoztathatja a verziószámot.

Nyissa meg a index.js fájlt, és módosítsa ezeket az értékeket a fájl elejére.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us";
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>A szkript futtatása
Futtassa a szkriptet egy terminálról/parancssorból Node.js.

```console
> node index.js
```

vagy

```console
> npm start
```

### <a name="application-progress"></a>Alkalmazás állapota
Az alkalmazás futása közben a parancssorban az előrehaladás látható. A parancssor kimenete tartalmazza a LUIS-válaszok formátumát.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>A LUIS-alkalmazás megnyitása
Miután a szkript befejeződik, bejelentkezhet a [luisba](luis-reference-regions.md) , és megtekintheti a **saját alkalmazások** alatt létrehozott Luis-alkalmazást. Ekkor meg kell jelennie a **TurnOn**, a **kanyart** és a **none** leképezések alatt hozzáadott hosszúságú kimondott szöveg.

![TurnOn szándék](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az alkalmazás tesztelése és betanítása a LUIS webhelyén](luis-interactive-test.md)

## <a name="additional-resources"></a>További források

Ez a példa a következő LUIS API-kat használja:
- [alkalmazás létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [leképezések hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [entitások hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e)
- [hosszúságú kimondott szöveg hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
