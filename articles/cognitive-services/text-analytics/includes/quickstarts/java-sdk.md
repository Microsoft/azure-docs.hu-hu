---
title: 'Rövid útmutató: Text Analytics v3-as ügyféloldali kódtár létrehozása Java-| Microsoft Docs'
description: A Java-hoz Text Analytics v3-as ügyféloldali kódtár első lépések.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: d39f40d4059b43246f523b4d01ca92c0360bc574
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765066"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

[Referenciadokumentáció](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.5/sdk/textanalytics/azure-ai-textanalytics)  |  [Csomag](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.5)  |  [Minták](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.5/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

[Referenciadokumentáció](/java/api/overview/azure/ai-textanalytics-readme)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics)  |  [Csomag](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)  |  [Minták](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* [Java fejlesztői készlet](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) 8-as vagy újabb verzióval
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Text Analytics-erőforrást, Text Analytics erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> végpont </a> lekért létrehozásához.  Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás elemre.**
    * Szüksége lesz a létrehozott erőforrás kulcsának és végpontjának létrehozására, hogy az alkalmazást a Text Analytics API-hoz. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.
* Az Elemzés funkció használatához szüksége lesz egy Text Analytics (S) tarifacsomaggal.

## <a name="setting-up"></a>Beállítása

### <a name="add-the-client-library"></a>Az ügyféloldali kódtár hozzáadása

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy Maven-projektet a választott IDE-környezetben vagy fejlesztői környezetben. Ezután adja hozzá a következő függőséget a projektpom.xml *fájlhoz.* Az egyéb buildeszközök [implementáció-szintaxisát](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.5) megtalálja az interneten.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.5</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy Maven-projektet az előnyben részesített IDE-környezetben vagy fejlesztői környezetben. Ezután adja hozzá a következő függőséget a projektpom.xml *fájlhoz.* Más buildeszközök [implementációszintaxisát megtalálja az](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) interneten.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java)amely a rövid útmutatóban található példakódokat tartalmazza. 

---

Hozzon létre egy nevű `TextAnalyticsSamples.java` Java-fájlt. Nyissa meg a fájlt, és adja hozzá a következő `import` utasításokat:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

A java-fájlban adjon hozzá egy új osztályt, és adja hozzá az Azure-erőforrás kulcsát és végpontját az alább látható módon.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Adja hozzá a következő fő metódust a osztályhoz. A metódusokat később fogja definiálni.

# <a name="version-31-preview"></a>[3.1-es verzió (előzetes verzió)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
        AnalyzeOperationExample(client)
}
```

---


## <a name="object-model"></a>Objektummodell

A Text Analytics ügyfél egy olyan objektum, amely az Ön kulcsával hitelesíti magát az Azure-ban, és függvényeket biztosít a szövegek egyetlen sztringként vagy kötegként `TextAnalyticsClient` való elfogadásához. Szöveget küldhet szinkron vagy aszinkron módon az API-nak. A válaszobjektum minden elküldt dokumentum elemzési adatait tartalmazza. 

## <a name="code-examples"></a>Kódpéldák

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis) 
* [Véleménybányászat](#opinion-mining)
* [Nyelvfelismerés](#language-detection)
* [Nevestű entitások felismerése](#named-entity-recognition-ner)
* [Entitás-összekapcsolás](#entity-linking)
* [Kulcskifejezések kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy metódust az objektum példányosulatához a saját erőforrásának `TextAnalyticsClient` kulcsával Text Analytics végponttal. Ez a példa megegyezik az API 3.0-s és 3.1-es verzióival.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


A program metódusában hívja meg a hitelesítési módszert az `main()` ügyfél példányosítatáshoz.

## <a name="sentiment-analysis"></a>Hangulatelemzés

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

> [!NOTE]
> `3.1`Verzióban:
> * Hangulatelemzés véleménybányászati elemzést is tartalmaz, amely opcionális jelző. 
> * A véleménybányászat aspektus- és véleményszintű érzelmeket is tartalmaz. 

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `sentimentAnalysisExample()` annak függvényét hívja `analyzeSentiment()` meg. A `AnalyzeSentimentResult` visszaadott objektum a következőket fogja `documentSentiment` tartalmazni: és `sentenceSentiments` ha sikeres, vagy `errorMessage` ha nem. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Kimenet

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

### <a name="opinion-mining"></a>Véleménybányászat

A hangulatelemzés véleménybányászattal való végrehajtásához hozzon létre egy új függvényt néven, amely a korábban létrehozott ügyfelet használja, és a objektum beállításával hívja meg a `sentimentAnalysisWithOpinionMiningExample()` `analyzeSentiment()` `AnalyzeSentimentOptions` függvényét. A `AnalyzeSentimentResult` visszaadott objektum a következőket fogja `documentSentiment` tartalmazni: és `sentenceSentiments` ha sikeres, vagy `errorMessage` ha nem. 


```java
 static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
 {
     // The document that needs be analyzed.
     String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

     System.out.printf("Document = %s%n", document);

     AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
     final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
     SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
     System.out.printf(
             "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
             documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());


     documentSentiment.getSentences().forEach(sentenceSentiment -> {
         SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
         System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                 sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
         sentenceSentiment.getOpinions().forEach(opinion -> {
             TargetSentiment targetSentiment = opinion.getTarget();
             System.out.printf("\t\tTarget sentiment: %s, target text: %s%n", targetSentiment.getSentiment(),
                     targetSentiment.getText());
             for (AssessmentSentiment assessmentSentiment : opinion.getAssessments()) {
                 System.out.printf("\t\t\t'%s' assessment sentiment because of \"%s\". Is the assessment negated: %s.%n",
                         assessmentSentiment.getSentiment(), assessmentSentiment.getText(), assessmentSentiment.isNegated());
             }
         });
     });
 }
```

### <a name="output"></a>Kimenet

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: atmosphere
            'negative' assessment sentiment because of "bad". Is the assessment negated: false.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: Staff
            'negative' assessment sentiment because of "friendly". Is the assessment negated: true.
            'negative' assessment sentiment because of "helpful". Is the assessment negated: true.
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `sentimentAnalysisExample()` annak függvényét hívja `analyzeSentiment()` meg. A `AnalyzeSentimentResult` visszaadott objektum a következőket fogja `documentSentiment` tartalmazni: és `sentenceSentiments` ha sikeres, vagy `errorMessage` ha nem. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Kimenet

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

---

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `detectLanguageExample()` annak függvényét hívja `detectLanguage()` meg. A visszaadott objektum tartalmazni fog egy észlelt elsődleges nyelvet, egy listát a többi sikeres nyelvről, vagy `DetectLanguageResult` `errorMessage` egy ha nem. Ez a példa megegyezik az API 3.0-s és 3.1-es verzióival.

> [!Tip]
> Bizonyos esetekben nehéz lehet a bemenet alapján különböző nyelveket felfedni. A paraméterrel kétbetűs országkódot `countryHint` is megadhat. Alapértelmezés szerint az API az "US" értéket használja alapértelmezett countryHint értékként, hogy eltávolítsa ezt a viselkedést, ha ezt a paramétert üres sztringre `countryHint = ""` állítja. Másik alapértelmezett beállításhoz állítsa be a tulajdonságot, és adja `TextAnalyticsClientOptions.DefaultCountryHint` át az ügyfél inicializálása során.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Kimenet

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>Nevestű entitások felismerése (NER)

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

> [!NOTE]
> `3.1`Verzióban:
> * A NER különböző módszereket tartalmaz a személyes adatok észlelésére. 
> * Az entitás-összekapcsolás egy külön kérelem, mint a NER.

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `recognizeEntitiesExample()` annak függvényét hívja `recognizeEntities()` meg. A visszaadott objektum tartalmazni fogja a sikeres vagy nem `CategorizedEntityCollection` `CategorizedEntity` sikeres objektumok `errorMessage` listáját.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Kimenet

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>Entitás-összekapcsolás

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `recognizeLinkedEntitiesExample()` annak függvényét hívja `recognizeLinkedEntities()` meg. A visszaadott objektum tartalmazni fogja a sikeres vagy nem `LinkedEntityCollection` `LinkedEntity` sikeres objektumok `errorMessage` listáját. Mivel a csatolt entitások egyedileg vannak azonosítva, ugyanazon entitás előfordulásai objektumok listájaként vannak egy objektum `LinkedEntity` alatt `LinkedEntityMatch` csoportosítva.


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Kimenet

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>Személyazonosításra alkalmas adatok felismerése

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `recognizePiiEntitiesExample()` annak függvényét hívja `recognizePiiEntities()` meg. A visszaadott objektum tartalmazni fogja a sikeres vagy nem `PiiEntityCollection` `PiiEntity` sikeres objektumok `errorMessage` listáját. A kiíratott szöveget is tartalmazza, amely a szövegbeviteli szövegből áll, és az összes azonosítható entitást lecseréli a `*****` szövegre.

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Kimenet

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

> [!NOTE]
> `3.0`Verzióban:
> * A NER különböző módszereket tartalmaz a személyes adatok észlelésére. 
> * Az entitás-összekapcsolás egy külön kérelem, mint a NER.

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `recognizeEntitiesExample()` annak függvényét hívja `recognizeEntities()` meg. A visszaadott objektum tartalmazni fogja a sikeres vagy nem `CategorizedEntityCollection` `CategorizedEntity` sikeres objektumok `errorMessage` listáját.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Kimenet

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

### <a name="entity-linking"></a>Entitás-összekapcsolás

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `recognizeLinkedEntitiesExample()` annak függvényét hívja `recognizeLinkedEntities()` meg. A visszaadott objektum tartalmazni fogja a sikeres vagy nem `LinkedEntityCollection` `LinkedEntity` sikeres objektumok `errorMessage` listáját. Mivel a csatolt entitások egyedileg vannak azonosítva, ugyanazon entitás előfordulásai objektumok listájaként vannak egy objektum `LinkedEntity` alatt `LinkedEntityMatch` csoportosítva.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Kimenet

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `extractKeyPhrasesExample()` annak függvényét hívja `extractKeyPhrases()` meg. A `ExtractKeyPhraseResult` visszaadott objektum tartalmazni fogja a sikeres kulcskifejezések listáját, vagy ha `errorMessage` nem, akkor a következőt: . Ez a példa megegyezik az API 3.0-s és 3.1-es verziójával.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Kimenet

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Az API aszinkron használata az Elemzés művelettel

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

Hozzon létre egy új függvényt `analyzeBatchActionsExample()` néven, amely a függvényt `beginAnalyzeBatchActions()` hívja meg. Az eredmény egy hosszú ideig futó művelet lesz, amelynek eredményeit a rendszer lekérdezi.

```java
static void analyzeBatchActionsExample(TextAnalyticsClient client)
{
        List<TextDocumentInput> documents = Arrays.asList(
                        new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen.")
                        );

        
        SyncPoller<AnalyzeBatchActionsOperationDetail, PagedIterable<AnalyzeBatchActionsResult>> syncPoller =
                client.beginAnalyzeBatchActions(documents,
                        new TextAnalyticsActions().setDisplayName("Analyze Batch Actions Quickstart")
                                .setRecognizeEntitiesOptions(new RecognizeEntitiesOptions()),
                        new AnalyzeBatchActionsOptions().setIncludeStatistics(false),
                        Context.NONE);

        // Task operation statistics
        while (syncPoller.poll().getStatus() == LongRunningOperationStatus.IN_PROGRESS) {
            final AnalyzeBatchActionsOperationDetail operationResult = syncPoller.poll().getValue();
            System.out.printf("Action display name: %s, Successfully completed actions: %d, in-process actions: %d, failed actions: %d, total actions: %d%n",
                    operationResult.getDisplayName(), operationResult.getActionsSucceeded(),
                    operationResult.getActionsInProgress(), operationResult.getActionsFailed(),
                    operationResult.getActionsInTotal());
        }

        syncPoller.waitForCompletion();

        Iterable<PagedResponse<AnalyzeBatchActionsResult>> pagedResults = syncPoller.getFinalResult().iterableByPage();
        for (PagedResponse<AnalyzeBatchActionsResult> page : pagedResults) {
            System.out.printf("Response code: %d, Continuation Token: %s.%n", page.getStatusCode(), page.getContinuationToken());
            page.getElements().forEach(analyzeBatchActionsResult -> {
                System.out.println("Entities recognition action results:");
                IterableStream<RecognizeEntitiesActionResult> recognizeEntitiesActionResults =
                        analyzeBatchActionsResult.getRecognizeEntitiesActionResults();
                if (recognizeEntitiesActionResults != null) {
                    recognizeEntitiesActionResults.forEach(actionResult -> {
                        if (!actionResult.isError()) {
                            // Recognized entities for each of documents from a batch of documents
                            AtomicInteger counter = new AtomicInteger();
                            for (RecognizeEntitiesResult documentResult : actionResult.getResult()) {
                                System.out.printf("%n%s%n", documents.get(counter.getAndIncrement()));
                                if (documentResult.isError()) {
                                    // Erroneous document
                                    System.out.printf("Cannot recognize entities. Error: %s%n",
                                            documentResult.getError().getMessage());
                                } else {
                                    // Valid document
                                    documentResult.getEntities().forEach(entity -> System.out.printf(
                                            "Recognized entity: %s, entity category: %s, entity subcategory: %s, confidence score: %f.%n",
                                            entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
                                }
                            }
                        } else {
                            TextAnalyticsError actionError = actionResult.getError();
                            // Erroneous action
                            System.out.printf("Cannot execute Entities Recognition action. Error: %s%n", actionError.getMessage());
                        }
                    });
                }
            });
        }
    }
```

Miután hozzáadta ezt a példát az alkalmazáshoz, hívja meg a `main()` metódusban.

```java
analyzeBatchActionsExample(client);
```

### <a name="output"></a>Kimenet

```console
Action display name: Analyze Batch Actions Quickstart, Successfully completed actions: 0, in-process actions: 1, failed actions: 0, total actions: 1
Response code: 200, Continuation Token: null.
Entities recognition action results:

Text = Microsoft was founded by Bill Gates and Paul Allen., Id = 0, Language = null
Recognized entity: Microsoft, entity category: Organization, entity subcategory: null, confidence score: 0.970000.
Recognized entity: Bill Gates, entity category: Person, entity subcategory: null, confidence score: 1.000000.
Recognized entity: Paul Allen, entity category: Person, entity subcategory: null, confidence score: 0.990000.
```

Az Elemzés művelettel piI-adatokat is észlelhet, felismerheti a csatolt entitásokat és a kulcskifejezések kinyerése műveleteket. Tekintse meg [az Elemzés mintát a](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro) GitHubon.

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Ez a funkció a 3.0-s verzióban nem érhető el.

---
