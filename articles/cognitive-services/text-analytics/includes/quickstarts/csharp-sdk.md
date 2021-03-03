---
title: 'Rövid útmutató: a C#-hez készült ügyféloldali kódtár Text Analytics | Microsoft Docs'
description: 'Ismerkedés a C Text Analytics ügyféloldali függvénytárával #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 12838eb2cd8437b2c3b3c225651b51991625fd78
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750731"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

a [v 3.1 dokumentációja](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview)  |  [v 3.1 függvénytár-forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)  |  [v 3.1 csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.3)  |  [v 3.1 minta](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

a [v3-referenciák dokumentációja](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet)  |  [v3 függvénytár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics)  |  [v3 csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [v3 minta](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" hozzon létre egy Text Analytics erőforrást, "  target="_blank"> és hozzon létre egy Text Analytics-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez.  Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Text Analytics APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* Az elemzés funkció használatához szüksége lesz egy Text Analytics erőforrásra a standard (S) árképzési szinttel.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-net-core-application"></a>Új .NET Core-alkalmazás létrehozása

Hozzon létre egy új .NET Core Console-alkalmazást a Visual Studio IDE használatával. Ezzel létrehoz egy "Hello World" projektet egyetlen C# forrásfájl használatával: *program.cs*.

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Az ügyféloldali kódtár telepítéséhez kattintson a jobb gombbal a megoldásra a **megoldáskezelő** , majd válassza a **NuGet-csomagok kezelése** lehetőséget. A megnyíló csomagkezelő területen válassza a **Tallózás** lehetőséget, és keresse meg a következőt: `Azure.AI.TextAnalytics` . Jelölje be a **prerelase belefoglalása** jelölőnégyzetet, majd válassza a verzió `5.1.0-beta.3` , majd a **telepítés** lehetőséget. Használhatja a [Package Manager konzolt](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)is.

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Az ügyféloldali kódtár telepítéséhez kattintson a jobb gombbal a megoldásra a **megoldáskezelő** , majd válassza a **NuGet-csomagok kezelése** lehetőséget. A megnyíló csomagkezelő területen válassza a **Tallózás** lehetőséget, és keresse meg a következőt: `Azure.AI.TextAnalytics` . Válassza `5.0.0` a verzió, majd a **telepítés** lehetőséget. Használhatja a [Package Manager konzolt](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)is.


> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), amely a jelen rövid útmutatóban szereplő példákat tartalmazza. 

---

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Nyissa meg a *program.cs* fájlt, és adja hozzá a következő `using` irányelveket:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Az alkalmazás `Program` osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Cserélje le az alkalmazás `Main` metódusát. Az itt megnevezett metódusokat később kell meghatároznia.

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

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Nyissa meg a *program.cs* fájlt, és adja hozzá a következő `using` irányelveket:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Az alkalmazás `Program` osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Cserélje le az alkalmazás `Main` metódusát. Az itt megnevezett metódusokat később kell meghatároznia.

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

A Text Analytics ügyfél egy olyan `TextAnalyticsClient` objektum, amely az Azure-ban hitelesíti a kulcsot, és függvényeket biztosít a szöveg egyetlen karakterláncként vagy kötegként való fogadásához. Az API-ra szinkron módon vagy aszinkron módon küldhet szöveget. A válasz objektum fogja tartalmazni az összes elküldött dokumentum elemzési információit. 

Ha a szolgáltatás verzióját használja `3.x` , egy opcionális példánnyal is elvégezheti `TextAnalyticsClientOptions` az ügyfél inicializálását különböző alapértelmezett beállításokkal (például az alapértelmezett nyelv vagy az ország/régió tipp). Azure Active Directory jogkivonat használatával is végezhet hitelesítést. 

## <a name="code-examples"></a>Kódpéldák

* [Hangulatelemzés](#sentiment-analysis)
* [Vélemény bányászata](#opinion-mining)
* [Nyelvfelismerés](#language-detection)
* [Megnevezett entitások felismerése](#named-entity-recognition-ner)
* [Entitás összekapcsolása](#entity-linking)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Győződjön meg arról, hogy a korábban létrehozott fő metódus létrehoz egy új ügyfél-objektumot a végponttal és a hitelesítő adatokkal.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Győződjön meg arról, hogy a korábban létrehozott fő metódus létrehoz egy új ügyfél-objektumot a végponttal és a hitelesítő adatokkal.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Hozzon létre egy nevű új függvényt `SentimentAnalysisExample()` , amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `AnalyzeSentiment()` függvényt. A visszaadott `Response<DocumentSentiment>` objektum a teljes bemeneti dokumentum hangulati címkéjét és pontszámát, valamint az egyes mondatok sikerességi elemzését fogja tartalmazni. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

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

### <a name="opinion-mining"></a>Vélemény bányászata

Hozzon létre egy nevű új függvényt `SentimentAnalysisWithOpinionMiningExample()` , amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `AnalyzeSentimentBatch()` függvényt a `AdditionalSentimentAnalyses.OpinionMining` kapcsolóval. A visszaadott `AnalyzeSentimentResultCollection` objektum tartalmazza azt a gyűjteményt, `AnalyzeSentimentResult` amelyben a jelképezi `Response<DocumentSentiment>` . A és a közötti különbség, `SentimentAnalysis()` `SentimentAnalysisWithOpinionMiningExample()` hogy az utóbbi `MinedOpinion` minden mondatban tartalmazni fog, amely egy elemzett aspektust és a kapcsolódó vélemény (oka) t mutatja. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

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

            foreach (MinedOpinion minedOpinion in sentence.MinedOpinions)
            {
                Console.WriteLine($"\tAspect: {minedOpinion.Aspect.Text}, Value: {minedOpinion.Aspect.Sentiment}");
                Console.WriteLine($"\tAspect positive score: {minedOpinion.Aspect.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tAspect negative score: {minedOpinion.Aspect.ConfidenceScores.Negative:0.00}");
                foreach (OpinionSentiment opinion in minedOpinion.Opinions)
                {
                    Console.WriteLine($"\t\tRelated Opinion: {opinion.Text}, Value: {opinion.Sentiment}");
                    Console.WriteLine($"\t\tRelated Opinion positive score: {opinion.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Opinion negative score: {opinion.ConfidenceScores.Negative:0.00}");
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

        Aspect: food, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: service, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: concierge, Value: Positive
        Aspect positive score: 1.00
        Aspect negative score: 0.00
                Related Opinion: nice, Value: Positive
                Related Opinion positive score: 1.00
                Related Opinion negative score: 0.00


Press any key to exit.
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt `SentimentAnalysisExample()` , amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `AnalyzeSentiment()` függvényt. A visszaadott `Response<DocumentSentiment>` objektum a teljes bemeneti dokumentum hangulati címkéjét és pontszámát, valamint az egyes mondatok sikerességi elemzését fogja tartalmazni. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

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

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)


Hozzon létre egy nevű új függvényt `LanguageDetectionExample()` , amely a korábban létrehozott ügyfelet veszi át, és hívja meg a  `DetectLanguage()` függvényt. A visszaadott `Response<DetectedLanguage>` objektum az észlelt nyelvet, valamint a nevét és az ISO-6391 kódot fogja tartalmazni. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméter segítségével megadhatja a kétbetűs ország/régió kódját. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztringre állítja `countryHint = ""` . Másik alapértelmezett érték beállításához állítsa be a `TextAnalyticsClientOptions.DefaultCountryHint` tulajdonságot, és adja át azt az ügyfél inicializálása során.

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

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)


Hozzon létre egy nevű új függvényt `LanguageDetectionExample()` , amely a korábban létrehozott ügyfelet veszi át, és hívja meg a  `DetectLanguage()` függvényt. A visszaadott `Response<DetectedLanguage>` objektum az észlelt nyelvet, valamint a nevét és az ISO-6391 kódot fogja tartalmazni. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméter segítségével megadhatja a kétbetűs ország/régió kódját. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztringre állítja `countryHint = ""` . Másik alapértelmezett érték beállításához állítsa be a `TextAnalyticsClientOptions.DefaultCountryHint` tulajdonságot, és adja át azt az ügyfél inicializálása során.

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

## <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)


Hozzon létre egy nevű új függvényt `EntityRecognitionExample()` , amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizeEntities()` függvényt, és ismételje meg az eredményeket. A visszaadott `Response<CategorizedEntityCollection>` objektum az észlelt entitások gyűjteményét fogja tartalmazni `CategorizedEntity` . Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

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

### <a name="entity-linking"></a>Entitás összekapcsolása

Hozzon létre egy nevű új függvényt `EntityLinkingExample()` , amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizeLinkedEntities()` függvényt, és ismételje meg az eredményeket. A visszaadott `Response<LinkedEntityCollection>` objektum az észlelt entitások gyűjteményét fogja tartalmazni `LinkedEntity` . Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` . Mivel a csatolt entitások egyedi módon vannak azonosítva, az azonos entitások előfordulásainak az `LinkedEntity` objektumok listáján vannak csoportosítva `LinkedEntityMatch` .

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

Hozzon létre egy nevű új függvényt `RecognizePIIExample()` , amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizePiiEntities()` függvényt, és ismételje meg az eredményeket. A visszaadott `PiiEntityCollection` személy az észlelt személyes adatok listáját jelöli. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

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

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)


> [!NOTE]
> Új verzió `3.0` :
> * Az entitások összekapcsolása mostantól el van különítve az entitások felismerésével.


Hozzon létre egy nevű új függvényt `EntityRecognitionExample()` , amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizeEntities()` függvényt, és ismételje meg az eredményeket. A visszaadott `Response<IReadOnlyCollection<CategorizedEntity>>` objektum az észlelt entitások listáját fogja tartalmazni. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

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

### <a name="entity-linking"></a>Entitás összekapcsolása

Hozzon létre egy nevű új függvényt `EntityLinkingExample()` , amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizeLinkedEntities()` függvényt, és ismételje meg az eredményeket. A visszaadott `Response<IReadOnlyCollection<LinkedEntity>>` érték az észlelt entitások listáját jelöli. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` . Mivel a csatolt entitások egyedi módon vannak azonosítva, az azonos entitások előfordulásainak az `LinkedEntity` objektumok listáján vannak csoportosítva `LinkedEntityMatch` .

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

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Hozzon létre egy nevű új függvényt `KeyPhraseExtractionExample()` , amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `ExtractKeyPhrases()` függvényt. A visszaadott `<Response<KeyPhraseCollection>` objektum az észlelt legfontosabb kifejezések listáját fogja tartalmazni. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

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

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt `KeyPhraseExtractionExample()` , amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `ExtractKeyPhrases()` függvényt. A visszaadott `<Response<IReadOnlyCollection<string>>` objektum az észlelt legfontosabb kifejezések listáját fogja tartalmazni. Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .

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

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Az API aszinkron használata az elemzési művelettel

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Hozzon létre egy nevű új függvényt `AnalyzeOperationExample()` , amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `StartAnalyzeOperationBatch()` függvényt. A visszaadott `AnalyzeOperation` objektum tartalmazni fogja a `Operation` illesztőfelület-objektumát a következőhöz: `AnalyzeOperationResult` . Mivel ez egy hosszan futó művelet, a `await` (z `operation.WaitForCompletionAsync()` ) értékre a frissítéshez. A `WaitForCompletionAsync()` befejezését követően a gyűjteményt frissíteni kell a alkalmazásban `operation.Value` . Ha hiba történt, a rendszer a következőt fogja eldobni: `RequestFailedException` .


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };

    AnalyzeOperationOptions operationOptions = new AnalyzeOperationOptions()
    {
        EntitiesTaskParameters = new EntitiesTaskParameters(),
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeOperation operation = client.StartAnalyzeOperationBatch(batchDocuments, operationOptions, "en");

    await operation.WaitForCompletionAsync();

    AnalyzeOperationResult resultCollection = operation.Value;

    RecognizeEntitiesResultCollection entitiesResult = resultCollection.Tasks.EntityRecognitionTasks[0].Results;

    Console.WriteLine("Analyze Operation Request Details");
    Console.WriteLine($"    Status: {resultCollection.Status}");
    Console.WriteLine($"    DisplayName: {resultCollection.DisplayName}");
    Console.WriteLine("");

    Console.WriteLine("Recognized Entities");

    foreach (RecognizeEntitiesResult result in entitiesResult)
    {
        Console.WriteLine($"    Recognized the following {result.Entities.Count} entities:");

        foreach (CategorizedEntity entity in result.Entities)
        {
            Console.WriteLine($"    Entity: {entity.Text}");
            Console.WriteLine($"    Category: {entity.Category}");
            Console.WriteLine($"    Offset: {entity.Offset}");
            Console.WriteLine($"    ConfidenceScore: {entity.ConfidenceScore}");
            Console.WriteLine($"    SubCategory: {entity.SubCategory}");
        }
        Console.WriteLine("");
    }
}
```

Miután hozzáadta ezt a példát az alkalmazásához, hívja meg a `main()` metódust a használatával `await` .

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>Kimenet

```console
Analyze Operation Request Details
    Status: succeeded
    DisplayName: Analyze Operation Quick Start Example

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

Az elemzési művelettel is felderítheti a személyes és a kulcsfontosságú kifejezések kinyerését. Tekintse meg a [minta elemzése](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeOperation.md) a githubon című témakört.

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Ez a funkció az 3,0-es verzióban nem érhető el.

---
