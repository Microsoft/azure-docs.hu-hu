---
title: 'Rövid útmutató: Text Analytics ügyféloldali kódtár létrehozása C#-| Microsoft Docs'
description: 'A C-hez Text Analytics ügyféloldali kódtár első lépések #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 1fd102f0f94f1ce53bebfba94d4f4c1a1f9e3812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765094"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

[3.1-es referenciadokumentáció](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview)  |  [3.1-es kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)  |  [3.1-es csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.5)  |  [V3.1-minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

[a v3 referenciadokumentációja](/dotnet/api/azure.ai.textanalytics)  |  [v3 kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics)  |  [v3 Csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [v3-minták](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* A [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Text Analytics-erőforrást, Text Analytics erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> végpont </a> lekért létrehozásához.  Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás elemre.**
    * Szüksége lesz a létrehozott erőforrás kulcsának és végpontjának létrehozására, hogy az alkalmazást a Text Analytics API-hoz. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.
* Az Elemzés funkció használatához szüksége lesz egy Text Analytics (S) tarifacsomaggal.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-net-core-application"></a>Új .NET Core-alkalmazás létrehozása

A Visual Studio IDE használatával hozzon létre egy új .NET Core-konzolalkalmazást. Ezzel létrehoz egy "Hello World" projektet egyetlen C#-forrásfájllal: *program.cs*.

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Az ügyféloldali kódtár telepítéséhez kattintson  a jobb gombbal a megoldásra a Megoldáskezelő válassza a **Manage NuGet Packages (NuGet-csomagok kezelése) gombra.** A megnyíló csomagkezelőben válassza a **Tallózás lehetőséget,** és keressen a kifejezésre. `Azure.AI.TextAnalytics` Jelölje be **az include prerelase** (előzetes verzió) jelölőnégyzetet, válassza ki a `5.1.0-beta.5` verziót, majd kattintson a Install **(Telepítés) gombra.** Használhatja a Csomagkezelő [konzolját is.](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Az ügyféloldali kódtár telepítéséhez kattintson  a jobb gombbal a megoldásra a Megoldáskezelő, és válassza a **Manage NuGet Packages (NuGet-csomagok kezelése) gombra.** A megnyíló csomagkezelőben válassza a **Tallózás lehetőséget,** és keressen a kifejezésre. `Azure.AI.TextAnalytics` Válassza a `5.0.0` verziót, majd a **Telepítés lehetőséget.** Használhatja a Csomagkezelő [konzolját is.](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)


> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs)amely a rövid útmutatóban található példakódokat tartalmazza. 

---

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Nyissa meg *a program.cs fájlt,* és adja hozzá a következő `using` irányelveket:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Az alkalmazás osztályában hozzon létre változókat az erőforrás kulcsához `Program` és végpontjához.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Cserélje le az alkalmazás `Main` metódusát. A metódusokat később fogja definiálni.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Nyissa meg *a program.cs fájlt,* és adja hozzá a következő `using` irányelveket:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Az alkalmazás osztályában hozzon létre változókat az erőforrás kulcsához `Program` és végpontjához.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Cserélje le az alkalmazás `Main` metódusát. A metódusokat később fogja definiálni.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

---

## <a name="object-model"></a>Objektummodell

A Text Analytics-ügyfél egy olyan objektum, amely a kulccsal hitelesíti magát az Azure-ban, és függvényeket biztosít a szövegek egyetlen sztringként vagy kötegként `TextAnalyticsClient` való elfogadásához. Szöveget küldhet szinkron vagy aszinkron módon az API-nak. A válaszobjektum minden küldött dokumentum elemzési adatait tartalmazza. 

Ha a szolgáltatás verzióját használja, egy választható példány használatával különböző alapértelmezett beállításokkal inicializálhatja az ügyfelet (például alapértelmezett nyelv vagy `3.x` `TextAnalyticsClientOptions` ország/régió tipp). Hitelesítést egy további jogkivonattal Azure Active Directory is. 

## <a name="code-examples"></a>Kódpéldák

* [Hangulatelemzés](#sentiment-analysis)
* [Véleménybányászat](#opinion-mining)
* [Nyelvfelismerés](#language-detection)
* [Megnevezett entitások felismerése](#named-entity-recognition-ner)
* [Entitás-összekapcsolás](#entity-linking)
* [Kulcskifejezések kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Győződjön meg arról, hogy a korábbi fő metódusa létrehoz egy új ügyfélobjektumot a végponttal és a hitelesítő adatokkal.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Győződjön meg arról, hogy a korábbi fő metódusa létrehoz egy új ügyfélobjektumot a végponttal és a hitelesítő adatokkal.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet és `SentimentAnalysisExample()` annak függvényét hívja `AnalyzeSentiment()` meg. A visszaadott objektum tartalmazza a teljes bemeneti dokumentum hangulatcímkéjét és pontszámát, valamint az egyes mondatok hangulatelemzését, ha `Response<DocumentSentiment>` az sikeres volt. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Véleménybányászat

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, és a csomagba beírt kapcsolóval hívja meg a `SentimentAnalysisWithOpinionMiningExample()` `AnalyzeSentimentBatch()` `IncludeOpinionMining` `AnalyzeSentimentOptions` függvényét. A `AnalyzeSentimentResultCollection` visszaadott objektum tartalmazza a gyűjteményét, `AnalyzeSentimentResult` amelyben a a `Response<DocumentSentiment>` értékeket jelöli. A és a közötti különbség az, hogy az utóbbi minden mondatban tartalmazni fog egy elemzett célt és a kapcsolódó `SentimentAnalysis()` `SentimentAnalysisWithOpinionMiningExample()` `SentenceOpinion` értékeléseket. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (SentenceOpinion sentenceOpinion in sentence.Opinions)
            {
                Console.WriteLine($"\tTarget: {sentenceOpinion.Target.Text}, Value: {sentenceOpinion.Target.Sentiment}");
                Console.WriteLine($"\tTarget positive score: {sentenceOpinion.Target.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tTarget negative score: {sentenceOpinion.Target.ConfidenceScores.Negative:0.00}");
                foreach (AssessmentSentiment assessment in sentenceOpinion.Assessments)
                {
                    Console.WriteLine($"\t\tRelated Assessment: {assessment.Text}, Value: {assessment.Sentiment}");
                    Console.WriteLine($"\t\tRelated Assessment positive score: {assessment.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Assessment negative score: {assessment.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Target: food, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: service, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: concierge, Value: Positive
        Target positive score: 1.00
        Target negative score: 0.00
                Related Assessment: nice, Value: Positive
                Related Assessment positive score: 1.00
                Related Assessment negative score: 0.00

Press any key to exit.
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet hívja `SentimentAnalysisExample()` meg `AnalyzeSentiment()` a függvényével. A visszaadott objektum tartalmazza a teljes bemeneti dokumentum hangulatcímkéjét és pontszámát, valamint egy hangulatelemzést minden mondathoz, ha `Response<DocumentSentiment>` az sikeres. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

---

## <a name="language-detection"></a>Nyelvfelismerés

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)


Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet hívja `LanguageDetectionExample()` meg  `DetectLanguage()` a függvényével. A visszaadott objektum tartalmazza az észlelt nyelvet a nevével és `Response<DetectedLanguage>` az ISO-6391 kóddal együtt. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

> [!Tip]
> Bizonyos esetekben a bemenet alapján nehéz lehet különböző nyelveket felfedni. A paraméterrel kétbetűs `countryHint` ország-/régiókódot is megadhat. Alapértelmezés szerint az API az "US" értéket használja alapértelmezett countryHint értékként, ennek a viselkedésnek a eltávolításához alaphelyzetbe állíthatja ezt a paramétert, ha ezt az értéket üres sztringre `countryHint = ""` állítja. Egy másik alapértelmezett beállításhoz állítsa be a tulajdonságot, és adja át az `TextAnalyticsClientOptions.DefaultCountryHint` ügyfél inicializálása során.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Kimenet

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)


Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet hívja `LanguageDetectionExample()` meg  `DetectLanguage()` a függvényével. A visszaadott objektum tartalmazza az észlelt nyelvet a nevével és `Response<DetectedLanguage>` az ISO-6391 kóddal együtt. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

> [!Tip]
> Bizonyos esetekben nehéz lehet a bemenet alapján különböző nyelveket felfedni. A paraméterrel kétbetűs `countryHint` ország-/régiókódot is megadhat. Alapértelmezés szerint az API az "US" értéket használja alapértelmezett countryHint értékként, hogy eltávolítsa ezt a viselkedést, ha ezt a paramétert üres sztringre `countryHint = ""` állítja. Másik alapértelmezett beállításhoz állítsa be a tulajdonságot, és adja `TextAnalyticsClientOptions.DefaultCountryHint` át az ügyfél inicializálása során.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Kimenet

```console
Language:
        French, ISO-6391: fr
```


---

## <a name="named-entity-recognition-ner"></a>nevesített entitások felismerése (NER)

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)


Hozzon létre egy új, nevű függvényt, amely a korábban létrehozott ügyfelet hívja meg, majd a függvényét `EntityRecognitionExample()` `RecognizeEntities()` iterálja az eredmények között. A `Response<CategorizedEntityCollection>` visszaadott objektum az észlelt entitások gyűjteményét fogja `CategorizedEntity` tartalmazni. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Entitás-összekapcsolás

Hozzon létre egy új, nevű függvényt, amely a korábban létrehozott ügyfelet hívja meg, majd a függvényét `EntityLinkingExample()` `RecognizeLinkedEntities()` iterálja az eredmények között. A `Response<LinkedEntityCollection>` visszaadott objektum az észlelt entitások gyűjteményét fogja `LinkedEntity` tartalmazni. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza. Mivel a csatolt entitások egyedileg vannak azonosítva, ugyanazon entitás előfordulásai objektumok listájaként vannak egy objektum `LinkedEntity` alatt `LinkedEntityMatch` csoportosítva.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Kimenet

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Személyazonosításra alkalmas adatok felismerése

Hozzon létre egy új, nevű függvényt, amely a korábban létrehozott ügyfelet hívja meg, majd a függvényét `RecognizePIIExample()` `RecognizePiiEntities()` iterálja az eredmények között. A visszaadott `PiiEntityCollection` az észlelt SZEMÉLYI-entitások listáját jelöli. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Kimenet

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)


> [!NOTE]
> Új `3.0` verzió:
> * Az entitás-összekapcsolás mostantól elkülönül az entitások felismeréstől.


Hozzon létre egy új függvényt néven, amely a korábban létrehozott ügyfelet hívja meg, majd végigkerál az `EntityRecognitionExample()` `RecognizeEntities()` eredményeken. A `Response<IReadOnlyCollection<CategorizedEntity>>` visszaadott objektum az észlelt entitások listáját fogja tartalmazni. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Entitás-összekapcsolás

Hozzon létre egy új függvényt néven, amely a korábban létrehozott ügyfelet hívja meg, majd végigkerál az `EntityLinkingExample()` `RecognizeLinkedEntities()` eredményeken. A `Response<IReadOnlyCollection<LinkedEntity>>` visszaadott az észlelt entitások listáját jelöli. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza. Mivel a csatolt entitások egyedileg vannak azonosítva, ugyanazon entitás előfordulásai objektumok listájaként vannak egy objektum `LinkedEntity` alatt `LinkedEntityMatch` csoportosítva.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Kimenet

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

--- 


### <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet hívja `KeyPhraseExtractionExample()` meg `ExtractKeyPhrases()` a függvényével. A `<Response<KeyPhraseCollection>` visszaadott objektum az észlelt kulcskifejezések listáját fogja tartalmazni. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Kimenet

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet hívja `KeyPhraseExtractionExample()` meg `ExtractKeyPhrases()` a függvényével. A `<Response<IReadOnlyCollection<string>>` visszaadott objektum az észlelt kulcskifejezések listáját fogja tartalmazni. Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Kimenet

```console
Key phrases:
    cat
    veterinarian
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Az API aszinkron használata az elemzés művelettel

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Hozzon létre egy nevű új függvényt, amely a korábban létrehozott ügyfelet hívja `AnalyzeOperationExample()` meg `StartAnalyzeBatchActionsAsync()` a függvényével. A `AnalyzeBatchActionsOperation` visszaadott objektum tartalmazza az `Operation` interfészobjektumot. Mivel ez egy hosszú ideig futó művelet, a értéken a `await` `operation.WaitForCompletionAsync()` frissítve lesz. A befejezése `WaitForCompletionAsync()` után a gyűjteményt frissíteni kell a következőben: `operation.Value` . Ha hiba történt, a hibaüzenetet ad `RequestFailedException` vissza.


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };


    TextAnalyticsActions actions = new TextAnalyticsActions()
    {
        RecognizeEntitiesOptions = new List<RecognizeEntitiesOptions>() { new RecognizeEntitiesOptions() },
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeBatchActionsOperation operation = await client.StartAnalyzeBatchActionsAsync(batchDocuments, actions);

    await operation.WaitForCompletionAsync();

    Console.WriteLine($"Status: {operation.Status}");
    Console.WriteLine($"Created On: {operation.CreatedOn}");
    Console.WriteLine($"Expires On: {operation.ExpiresOn}");
    Console.WriteLine($"Last modified: {operation.LastModified}");
    if (!string.IsNullOrEmpty(operation.DisplayName))
        Console.WriteLine($"Display name: {operation.DisplayName}");
    Console.WriteLine($"Total actions: {operation.TotalActions}");
    Console.WriteLine($"  Succeeded actions: {operation.ActionsSucceeded}");
    Console.WriteLine($"  Failed actions: {operation.ActionsFailed}");
    Console.WriteLine($"  In progress actions: {operation.ActionsInProgress}");

    await foreach (AnalyzeBatchActionsResult documentsInPage in operation.Value)
    {
        RecognizeEntitiesResultCollection entitiesResult = documentsInPage.RecognizeEntitiesActionsResults.FirstOrDefault().Result;

        Console.WriteLine("Recognized Entities");

        foreach (RecognizeEntitiesResult result in entitiesResult)
        {
            Console.WriteLine($"  Recognized the following {result.Entities.Count} entities:");

            foreach (CategorizedEntity entity in result.Entities)
            {
                Console.WriteLine($"  Entity: {entity.Text}");
                Console.WriteLine($"  Category: {entity.Category}");
                Console.WriteLine($"  Offset: {entity.Offset}");
                Console.WriteLine($"  Length: {entity.Length}");
                Console.WriteLine($"  ConfidenceScore: {entity.ConfidenceScore}");
                Console.WriteLine($"  SubCategory: {entity.SubCategory}");
            }
            Console.WriteLine("");
        }
    }
}
```

Miután hozzáadta ezt a példát az alkalmazáshoz, hívja meg a `main()` metódust a `await` használatával.

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>Kimenet

```console
Status: succeeded
Created On: 3/10/2021 2:25:01 AM +00:00
Expires On: 3/11/2021 2:25:01 AM +00:00
Last modified: 3/10/2021 2:25:05 AM +00:00
Display name: Analyze Operation Quick Start Example
Total actions: 1
  Succeeded actions: 1
  Failed actions: 0
  In progress actions: 0
Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

Az Elemzés művelettel piI-adatokat és kulcskifejezéseket is észlelhet. Lásd az [Elemzés mintát a](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples) GitHubon.

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Ez a funkció a 3.0-s verzióban nem érhető el.

---
