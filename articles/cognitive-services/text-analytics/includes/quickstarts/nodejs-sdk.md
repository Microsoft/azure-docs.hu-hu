---
title: 'Rövid útmutató: Text Analytics v3-as ügyféloldali kódtár Node.js | Microsoft Docs'
description: Első lépések a v3 Text Analytics ügyféloldali kódtárának Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 3640a03f8ac814fec2823a761e651ab386438c5c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327404"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

[v3 referenciadokumentáció](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview)  |  [v3 kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [v3-as csomag (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

[a v3 referenciadokumentációja](/javascript/api/overview/azure/ai-text-analytics-readme)  |  [v3 kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [v3-as csomag (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* Az alkalmazás aktuális [Node.js. ](https://nodejs.org/)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Text Analytics-erőforrást, Text Analytics erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás elemre.**
    * Szüksége lesz a létrehozott erőforrás kulcsának és végpontjának létrehozására, hogy az alkalmazást a Text Analytics API-hoz. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.
* Az Elemzés funkció használatához szüksége lesz egy Text Analytics standard (S) tarifacsomaggal.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp 

cd myapp
```

Futtassa `npm init` az parancsot egy node-alkalmazás fájllal való `package.json` létrehozásához. 

```console
npm init
```
### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Telepítse az `@azure/ai-text-analytics` NPM-csomagokat:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.5
```

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)amely a rövid útmutatóban található példakódokat tartalmazza. 


# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Telepítse az `@azure/ai-text-analytics` NPM-csomagokat:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)amely a rövid útmutatóban található példakódokat tartalmazza. 

---

Az alkalmazás `package.json` fájlja frissül a függőségekkel.
Hozzon létre egy nevű fájlt, `index.js` és adja hozzá a következőket:

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

---

Hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Objektummodell

A Text Analytics ügyfél egy objektum, amely az Ön kulcsával hitelesíti `TextAnalyticsClient` magát az Azure-ban. Az ügyfél több metódust is biztosít a szöveg elemzésére, egyetlen sztringként vagy kötegként.

A rendszer a , és attribútumok kombinációját tartalmazó objektumok listájaként küldi el a szöveget az API-nak a `documents` `dictionary` használt `id` `text` `language` metódustól függően. A attribútum tárolja az elemzett szöveget a forrásban, és a `text` `language` bármilyen érték `id` lehet. 

A válaszobjektum egy lista, amely az egyes dokumentumok elemzési adatait tartalmazza. 

## <a name="code-examples"></a>Kódpéldák

* [Ügyfél-hitelesítés](#client-authentication)
* [Hangulatelemzés](#sentiment-analysis) 
* [Véleménybányászat](#opinion-mining)
* [Nyelvfelismerés](#language-detection)
* [Nevestű entitások felismerése](#named-entity-recognition-ner)
* [Entitás-összekapcsolás](#entity-linking)
* Személyazonosításra alkalmas adatok
* [Kulcskifejezések kinyerése](#key-phrase-extraction)

## <a name="client-authentication"></a>Ügyfél-hitelesítés

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy `TextAnalyticsClient` új objektumot a kulccsal és a végponttal paraméterként.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy `TextAnalyticsClient` új objektumot a kulccsal és a végponttal paraméterként.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `analyzeSentiment()` metódusát, és szerezze be a visszaadott `SentimentBatchResult` objektumot. Iteráljon végig az eredmények listáján, és nyomtassa ki az egyes dokumentumok azonosítóját, dokumentumszintű hangulatát megbízhatósági pontszámokkal. Az eredmény minden dokumentumhoz mondatszintű érzelmeket, valamint eltolási, hossz- és megbízhatósági pontszámokat tartalmaz.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

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

### <a name="opinion-mining"></a>Véleménybányászat

A véleménybányászattal végzett hangulatelemzéshez hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél metódusát `analyzeSentiment()` a kapcsolójelölő hozzáadásával, `includeOpinionMining: true` és szerezze be a visszaadott `SentimentBatchResult` objektumot. Iteráljon végig az eredmények listáján, és nyomtassa ki az egyes dokumentumok azonosítóját, dokumentumszintű hangulatát megbízhatósági pontszámokkal. Az eredmények nem csak a fenti mondatszintű érzelmeket, hanem az aspektus- és véleményszintű érzelmeket is tartalmaznak.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

  const sentimentInput = [
    {
      text: "The food and service were unacceptable, but the concierge were nice",
      id: "0",
      language: "en"
    }
  ];
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

### <a name="output"></a>Kimenet

```console
- Document 0
        Document text: The food and service were unacceptable, but the concierge were nice
        Overall Sentiment: positive
        Sentiment confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
        Sentences
        - Sentence sentiment: positive
          Confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
          Mined opinions
                - Target text: food
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: service
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: concierge
                  Target sentiment: positive
                  Target confidence scores: { positive: 1, negative: 0 }
                  Target assessments
                        - Text: nice
                          Sentiment: positive
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `analyzeSentiment()` metódusát, és szerezze be a visszaadott `SentimentBatchResult` objektumot. Iteráljon végig az eredmények listáján, és nyomtassa ki az egyes dokumentumok azonosítóját, dokumentumszintű hangulatát megbízhatósági pontszámokkal. Az eredmény minden dokumentumhoz mondatszintű érzelmeket, valamint eltolási, hossz- és megbízhatósági pontszámokat tartalmaz.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

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

---

## <a name="language-detection"></a>Nyelvfelismerés

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `detectLanguage()` metódusát, és szerezze be a visszaadott `DetectLanguageResultCollection` metódust. Ezután iteráljon végig az eredményeken, és nyomtassa ki az egyes dokumentumok azonosítóját a megfelelő elsődleges nyelvvel.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

### <a name="output"></a>Kimenet

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `detectLanguage()` metódusát, és szerezze be a visszaadott `DetectLanguageResultCollection` metódust. Ezután iteráljon végig az eredményeken, és nyomtassa ki az egyes dokumentumok azonosítóját a megfelelő elsődleges nyelvvel.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

### <a name="output"></a>Kimenet

```console
ID: 0
        Primary Language French
```

---

## <a name="named-entity-recognition-ner"></a>nevesített entitások felismerése (NER)

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

> [!NOTE]
> `3.1`Verzióban:
> * Az entitás-összekapcsolás egy külön kérelem, mint a NER.

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `recognizeEntities()` metódusát, és szerezze be az `RecognizeEntitiesResult` objektumot. Iteráljon végig az eredmények listáján, és nyomtassa ki az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

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

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `recognizeLinkedEntities()` metódusát, és szerezze be az `RecognizeLinkedEntitiesResult` objektumot. Iteráljon végig az eredmények listáján, és nyomtassa ki az entitás nevét, azonosítóját, adatforrását, URL-címét és egyezéseit. A tömb minden `matches` objektuma tartalmazni fog eltolást, hosszt és pontszámot az adott egyezéshez.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

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

### <a name="personally-identifying-information-pii-recognition"></a>Személyes azonosításra alkalmas adatok (PII) felismerése

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `recognizePiiEntities()` metódusát, és szerezze be az `RecognizePIIEntitiesResult` objektumot. Iteráljon végig az eredmények listáján, és nyomtassa ki az entitás nevét, típusát és pontszámát.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

### <a name="output"></a>Kimenet

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

> [!NOTE]
> `3.0`Verzióban:
> * Az entitás-összekapcsolás egy külön kérelem, mint a NER.

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `recognizeEntities()` metódusát, és szerezze be az `RecognizeEntitiesResult` objektumot. Iteráljon végig az eredmények listáján, és nyomtassa ki az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

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

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `recognizeLinkedEntities()` metódusát, és szerezze be az `RecognizeLinkedEntitiesResult` objektumot. Iteráljon végig az eredmények listáján, és nyomtassa ki az entitás nevét, azonosítóját, adatforrását, URL-címét és egyezéseit. A tömb minden `matches` objektuma tartalmazni fog eltolást, hosszt és pontszámot az adott egyezéshez.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

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


---

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `extractKeyPhrases()` metódusát, és szerezze be a visszaadott `ExtractKeyPhrasesResult` objektumot. Iteráljon végig az eredményeken, és nyomtassa ki az egyes dokumentumok azonosítóját és az észlelt kulcskifejezéseket.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy sztringtömböt, amely tartalmazza az elemezni kívánt dokumentumot. Hívja meg az ügyfél `extractKeyPhrases()` metódusát, és szerezze be a visszaadott `ExtractKeyPhrasesResult` objektumot. Iteráljon végig az eredményeken, és nyomtassa ki az egyes dokumentumok azonosítóját és az észlelt kulcskifejezéseket.

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

Futtassa a kódot a `node index.js` következővel a konzolablakban: .

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Az API aszinkron használata az Elemzés művelettel

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

> [!CAUTION]
> Az Elemzés műveletekhez standard (S) tarifacsomaggal Text Analytics erőforrást kell használnia.  

Hozzon létre egy új függvényt `analyze_example()` néven, amely a függvényt `beginAnalyze()` hívja meg. Az eredmény egy hosszú ideig futó művelet lesz, amelynek eredményeit a rendszer lekérdezi.

```javascript
async function analyze_example(client) {
  const documents = [
    "Microsoft was founded by Bill Gates and Paul Allen.",
  ];

  const actions = {
    recognizeEntitiesActions: [{ modelVersion: "latest" }],
  };
  const poller = await client.beginAnalyzeBatchActions(documents, actions, "en");

  console.log(
    `The analyze batch actions operation was created on ${poller.getOperationState().createdOn}`
  );
  console.log(
    `The analyze batch actions operation results will expire on ${
      poller.getOperationState().expiresOn
    }`
  );
  const resultPages = await poller.pollUntilDone();
  for await (const page of resultPages) {
    const entitiesAction = page.recognizeEntitiesResults[0];
    if (!entitiesAction.error) {
      for (const doc of entitiesAction.results) {
        console.log(`- Document ${doc.id}`);
        if (!doc.error) {
          console.log("\tEntities:");
          for (const entity of doc.entities) {
            console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
          }
        } else {
          console.error("\tError:", doc.error);
        }
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Kimenet

```console
The analyze batch actions operation was created on Fri Mar 12 2021 09:53:49 GMT-0800 (Pacific Standard Time)
The analyze batch actions operation results will expire on Sat Mar 13 2021 09:53:49 GMT-0800 (Pacific Standard Time)
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

Az Elemzés művelettel piI-adatokat is észlelhet, felismerheti a csatolt entitásokat és a kulcskifejezések kinyerése műveleteket. Tekintse meg az Analyze samples for JavaScript and TypeScript (Minták elemzése [JavaScripthez](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript) és [TypeScripthez) a](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src) GitHubon.

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Ez a funkció a 3.0-s verzióban nem érhető el.

---

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `node` gyorsindítási fájlban található paranccsal.

```console
node index.js
```
