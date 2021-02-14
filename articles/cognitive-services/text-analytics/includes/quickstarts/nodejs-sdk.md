---
title: 'Gyors útmutató: Text Analytics v3 ügyféloldali kódtár a Node.jshoz | Microsoft Docs'
description: Ismerkedjen meg a Node.js v3 Text Analytics ügyféloldali kódtáraval.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 2a5bc5b6f9e2cb9a3f6895847dd8f03bc2540e18
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515002"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

a [v3-referenciák dokumentációja](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview)  |  [v3 függvénytár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [v3 csomag (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3 minta](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

a [v3-referenciák dokumentációja](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest)  |  [v3 függvénytár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [v3 csomag (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3 minta](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

[v2 dokumentáció](/javascript/api/@azure/cognitiveservices-textanalytics)  |  [v2 függvénytár forráskódja](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics)  |  [v2 csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics)  |  [v2 minta](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Node.js](https://nodejs.org/)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" hozzon létre egy Text Analytics erőforrást, "  target="_blank"> és hozzon létre egy Text Analytics-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Text Analytics APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* Az elemzés funkció használatához szüksége lesz egy Text Analytics erőforrásra a standard (S) árképzési szinttel.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp 

cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` . 

```console
npm init
```
### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Telepítse a `@azure/ai-text-analytics` NPM-csomagokat:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.3
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), amely a jelen rövid útmutatóban szereplő példákat tartalmazza. 


# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Telepítse a `@azure/ai-text-analytics` NPM-csomagokat:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), amely a jelen rövid útmutatóban szereplő példákat tartalmazza. 

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Telepítse a `@azure/cognitiveservices-textanalytics` NPM-csomagokat:

```console
npm install --save @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), amely a jelen rövid útmutatóban szereplő példákat tartalmazza. 

---

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.
Hozzon létre egy nevű fájlt `index.js` , és adja hozzá a következőket:

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

```javascript
"use strict";

const os = require("os");
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```
---

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Objektummodell

Az Text Analytics-ügyfél egy olyan `TextAnalyticsClient` objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg elemzéséhez, egyetlen sztringként vagy kötegként.

A rendszer az API-nak elküldi a szöveget, `documents` amely a `dictionary` `id` `text` használt módszertől függően a, a és az attribútumok kombinációját tartalmazó objektumokat tartalmazza `language` . Az `text` attribútum tárolja a forrásban elemezni kívánt szöveget `language` , és a `id` értéke bármilyen lehet. 

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista. 

## <a name="code-examples"></a>Kódpéldák

* [Ügyfél-hitelesítés](#client-authentication)
* [Hangulatelemzés](#sentiment-analysis) 
* [Vélemény bányászata](#opinion-mining)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-ner)
* [Entitás összekapcsolása](#entity-linking)
* Személyazonosításra alkalmas adatok
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="client-authentication"></a>Ügyfél-hitelesítés

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Hozzon létre egy új `TextAnalyticsClient` objektumot a kulcs és a végpont paraméterként.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Hozzon létre egy új `TextAnalyticsClient` objektumot a kulcs és a végpont paraméterként.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre [](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) egy új TextAnalyticsClient `credentials` -objektumot `endpoint` paraméterként.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `analyzeSentiment()` metódusát, és szerezze be a visszaadott `SentimentBatchResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, a dokumentum szintjének véleményét megbízhatósági pontszámokkal. Az eredmény az egyes dokumentumokhoz tartozó mondatok szintjének, valamint az eltolások, a hossz és a megbízhatósági pontszámok értékeit tartalmazza.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Vélemény bányászata

Az elemzések és a vélemények kitermelésének elvégzéséhez hozzon létre egy tömböt az elemezni kívánt dokumentumot tartalmazó sztringből. Hívja meg az ügyfél `analyzeSentiment()` metódusát a beállítási jelző hozzáadásával `includeOpinionMining: true` és a visszaadott objektum beolvasásával `SentimentBatchResult` . Ismételje meg az eredmények listáját, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, a dokumentum szintjének véleményét megbízhatósági pontszámokkal. Az eredmény az egyes dokumentumokhoz nem csak a fentiekben említett mondatokat tartalmazza, hanem a szempontokat és a vélemények szintjét is.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

    const sentimentInput = [
        {
            text: "The food and service were unacceptable, but the concierge were nice",
            id: "0",
            language: "en"
        }
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
            console.log("\tMined opinions");
            for (const { aspect, opinions } of sentence.minedOpinions) {
                console.log(`\t\tAspect text: ${aspect.text}`);
                console.log(`\t\tAspect sentiment: ${aspect.sentiment}`);
                console.log(`\t\tAspect Positive: ${aspect.confidenceScores.positive.toFixed(2)} \tNegative: ${aspect.confidenceScores.negative.toFixed(2)}`);
                console.log("\t\tAspect opinions:");
                for (const { text, sentiment, confidenceScores } of opinions) {
                    console.log(`\t\tOpinion text: ${text}`);
                    console.log(`\t\tOpinion sentiment: ${sentiment}`);
                    console.log(`\t\tOpinion Positive: ${confidenceScores.positive.toFixed(2)} \tNegative: ${confidenceScores.negative.toFixed(2)}`);
                }
            }
        });
    });
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Sentences Sentiment(1):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Mined opinions
                Aspect text: food
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: service
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: concierge
                Aspect sentiment: positive
                Aspect Positive: 1.00   Negative: 0.00
                Aspect opinions:
                Opinion text: nice
                Opinion sentiment: positive
                Opinion Positive: 1.00  Negative: 0.00
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `analyzeSentiment()` metódusát, és szerezze be a visszaadott `SentimentBatchResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, a dokumentum szintjének véleményét megbízhatósági pontszámokkal. Az eredmény az egyes dokumentumokhoz tartozó mondatok szintjének, valamint az eltolások, a hossz és a megbízhatósági pontszámok értékeit tartalmazza.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy listát az elemezni kívánt dokumentumokat tartalmazó szótár-objektumok listájáról. Hívja meg az ügyfél [hangulati ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) metódusát, és szerezze be a visszaadott [SentimentBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Ismételje meg az eredmények listáját, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és hangulati pontszámát. Ha a pontszám közelebb van a 0 értékhez, a negatív érzést jelez, míg az 1. számú pontszám pozitív hangulatot jelez.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Nyelvfelismerés

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `detectLanguage()` metódusát, és szerezze be a visszaadott értéket `DetectLanguageResultCollection` . Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit a megfelelő elsődleges nyelvvel.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `detectLanguage()` metódusát, és szerezze be a visszaadott értéket `DetectLanguageResultCollection` . Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit a megfelelő elsődleges nyelvvel.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Primary Language French
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy listát a dokumentumokat tartalmazó szótár-objektumokról. Hívja meg az ügyfél [detectLanguage ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) metódusát, és szerezze be a visszaadott [LanguageBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és nyelvét.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

> [!NOTE]
> A verzióban `3.1` :
> * Az entitások összekapcsolása egy külön kérelem, mint a kapcsolatfelvétel.

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizeEntities()` metódusát, és kérje le az `RecognizeEntitiesResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entitáskapcsolás

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizeLinkedEntities()` metódusát, és kérje le az `RecognizeLinkedEntitiesResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, AZONOSÍTÓját, forrását, URL-címét és egyezéseit. A tömbben lévő összes objektum `matches` eltolást, hosszúságot és pontszámot fog tartalmazni az adott egyeztetéshez.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Személyazonosításra alkalmas adatok felismerése

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizePiiEntities()` metódusát, és kérje le az `RecognizePIIEntitiesResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, típusát és pontszámát.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

> [!NOTE]
> A verzióban `3.0` :
> * Az entitások összekapcsolása egy külön kérelem, mint a kapcsolatfelvétel.

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizeEntities()` metódusát, és kérje le az `RecognizeEntitiesResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entitáskapcsolás

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizeLinkedEntities()` metódusát, és kérje le az `RecognizeLinkedEntitiesResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, AZONOSÍTÓját, forrását, URL-címét és egyezéseit. A tömbben lévő összes objektum `matches` eltolást, hosszúságot és pontszámot fog tartalmazni az adott egyeztetéshez.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

> [!NOTE]
> Az 2,1-es verzióban az entitások összekapcsolása szerepel a megjelenő válaszban.

Hozza létre a dokumentumokat tartalmazó objektumok listáját. Hívja meg az ügyfél [entitások ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) metódusát, és kérje le a [EntitiesBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit. Minden észlelt entitás esetében nyomtassa ki a wikipedia nevét, típusát és altípusait (ha van), valamint az eredeti szöveg helyét.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `extractKeyPhrases()` metódusát, és szerezze be a visszaadott `ExtractKeyPhrasesResult` objektumot. Ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt legfontosabb kifejezéseket.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `extractKeyPhrases()` metódusát, és szerezze be a visszaadott `ExtractKeyPhrasesResult` objektumot. Ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt legfontosabb kifejezéseket.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Hozza létre a dokumentumokat tartalmazó objektumok listáját. Hívja meg az ügyfél [()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) metódusát, és kérje le a visszaadott     [KeyPhraseBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) objektumot. Ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt legfontosabb kifejezéseket.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Futtassa a kódot a `node index.js` konzoljának ablakában.

### <a name="output"></a>Kimenet

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Az API aszinkron használata az elemzési művelettel

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

> [!CAUTION]
> Az elemzési műveletek használatához Text Analytics-erőforrást kell használnia a standard (S) árképzési szinttel.  

Hozzon létre egy nevű új függvényt `analyze_example()` , amely meghívja a `beginAnalyze()` függvényt. Az eredmény egy hosszú ideig futó művelet, amely az eredmények lekérdezésére lesz lekérdezve.

```javascript
const documents = [
  "Microsoft was founded by Bill Gates and Paul Allen.",
];

async function analyze_example(client) {
  console.log("== Analyze Sample ==");

  const tasks = {
    entityRecognitionTasks: [{ modelVersion: "latest" }]
  };
  const poller = await client.beginAnalyze(documents, tasks);
  const resultPages = await poller.pollUntilDone();

  for await (const page of resultPages) {
    const entitiesResults = page.entitiesRecognitionResults![0];
    for (const doc of entitiesResults) {
      console.log(`- Document ${doc.id}`);
      if (!doc.error) {
        console.log("\tEntities:");
        for (const entity of doc.entities) {
          console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
        }
      } else {
        console.error("  Error:", doc.error);
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Kimenet

```console
== Analyze Sample ==
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

Az elemzési művelettel is felderítheti a személyes és a kulcsfontosságú kifejezések kinyerését. Tekintse meg a minták elemzése a [javascripthez](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/javascript) és a [írógéppel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/typescript/src) a githubon című témakört.

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Ez a funkció az 3,0-es verzióban nem érhető el.

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Ez a funkció az 2,1-es verzióban nem érhető el.

---

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `node` fájlban található paranccsal.

```console
node index.js
```
