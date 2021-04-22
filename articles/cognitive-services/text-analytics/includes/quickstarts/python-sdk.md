---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.openlocfilehash: d36666ea35a4ce3fc894e35dd8ca3860900ecef6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107881057"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

[3.1-es referenciadokumentáció](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  [3.1-es kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics)  |  [3.1-es csomag (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v3.1-minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

[v3 referenciadokumentáció](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  [v3 kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics)  |  [v3 csomag (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v3-minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Text Analytics-erőforrást, Text Analytics erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás gombra.**
    * A létrehozott erőforrás kulcsának és végpontjának szüksége lesz az alkalmazás a Text Analytics API-hoz való csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.
* Az Elemzés funkció használatához szüksége lesz egy Text Analytics standard (S) tarifacsomaggal.

## <a name="setting-up"></a>Beállítása

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után a következővel telepítheti az ügyféloldali kódtárat:

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)amely a rövid útmutatóban található példakódokat tartalmazza. 

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)amely a rövid útmutatóban található példakódokat tartalmazza. 


---

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-fájlt, és hozzon létre változókat az erőforrás Azure-végpontjának és előfizetői kulcsának.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektummodell

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

A Text Analytics ügyfél egy objektum, amely hitelesíti `TextAnalyticsClient` magát az Azure-ban. Az ügyfél számos metódust biztosít a szövegelemzéshez. 

Amikor egy szöveg feldolgozása történik az API-nak a listájaként, amely lehet sztringlista, dict-hez hasonló ábrázolás, vagy a `documents` `TextDocumentInput/DetectLanguageInput` listája. Egy objektum , és kombinációját `dict-like` `id` `text` `language/country_hint` tartalmazza. A attribútum tárolja az elemzett szöveget a forrásban, és a `text` `country_hint` bármilyen érték `id` lehet. 

A válaszobjektum egy lista, amely az egyes dokumentumok elemzési adatait tartalmazza. 

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

A Text Analytics ügyfél egy objektum, amely az Ön kulcsával hitelesíti `TextAnalyticsClient` magát az Azure-ban. Az ügyfél több módszert is biztosít a szöveg kötegként való elemzésére. 

Amikor kötegelt feldolgozási szöveget küld az API-nak a listájaként, amelyek a használt metódustól függően , és attribútumok kombinációját tartalmazó `documents` `dictionary` `id` `text` `language` objektumok. A attribútum tárolja az elemzett szöveget a forrásban, és a `text` `language` bármilyen érték `id` lehet. 

A válaszobjektum egy lista, amely az egyes dokumentumok elemzett információit tartalmazza. 

---

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következő feladatok elvégzését mutatják be a Pythonhoz készült Text Analytics kódtárával:

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Véleménybányászat](#opinion-mining)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-ner) 
* [Személyazonosításra alkalmas adatok felismerése](#personally-identifiable-information-recognition) 
* [Entitás-összekapcsolás](#entity-linking)
* [Kulcskifejezések kinyerése](#key-phrase-extraction)


# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-ner) 
* [Entitás-összekapcsolás](#entity-linking)
* [Kulcskifejezések kinyerése](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy függvényt az objektum példányosul `TextAnalyticsClient` a fent létrehozott ÉS `key` `endpoint` objektummal. Ezután hozzon létre egy új ügyfelet. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy függvényt az objektum példányosul `TextAnalyticsClient` a fent létrehozott ÉS `key` `endpoint` objektummal. Ezután hozzon létre egy új ügyfelet. Vegye `api_version=TextAnalyticsApiVersion.V3_0` figyelembe, hogy a 3.0-s verzióhoz definiálva kell lennie.

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

Ha az ügyféloldali kódtár 5.1.0-s verzióját telepítette a használatával, megadhatja a Text Analytics API 3.0-s verzióját az ügyfél `pip install azure-ai-textanalytics --pre` `api_version` paraméterrel. Csak akkor használja a következő `authenticate_client()` metódust, ha az ügyfél 5.1.0-s vagy újabb.

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

--- 

## <a name="sentiment-analysis"></a>Hangulatelemzés

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel `sentiment_analysis_example()` argumentumként, majd hívja meg `analyze_sentiment()` a függvényt. A visszaadott válaszobjektum tartalmazza a teljes bemeneti dokumentum hangulatcímkéjét és pontszámát, valamint az egyes mondatok hangulatelemzését.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Kimenet

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>Véleménybányászat

A hangulatelemzés véleménybányászattal való létrehozására hozzon létre egy új függvényt néven, amely argumentumként az ügyfelet használja, majd a kapcsolójelölővel hívja meg `sentiment_analysis_with_opinion_mining_example()` `analyze_sentiment()` a `show_opinion_mining=True` függvényt. A visszaadott válaszobjektum nem csak a teljes bemeneti dokumentum hangulatcímkéjét és pontszámát fogja tartalmazni minden mondat hangulatelemzésével, hanem az aspektus- és véleményszintű hangulatelemzést is.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Kimenet

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' target 'food'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'negative' target 'service'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel `sentiment_analysis_example()` argumentumként, majd hívja meg `analyze_sentiment()` a függvényt. A visszaadott válaszobjektum tartalmazza a teljes bemeneti dokumentum hangulatcímkéjét és pontszámát, valamint az egyes mondatok hangulatelemzését.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Kimenet

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

---

## <a name="language-detection"></a>Nyelvfelismerés

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel `language_detection_example()` argumentumként, majd hívja meg a `detect_language()` függvényt. A visszaadott válaszobjektum az észlelt nyelvet fogja tartalmazni a (ha sikeres) és a (ha `primary_language` `error` nem) értékeket.

> [!Tip]
> Bizonyos esetekben nehéz lehet a bemenet alapján különböző nyelveket felfedni. A paraméterrel kétbetűs országkódot `country_hint` is megadhat. Alapértelmezés szerint az API az "US" értéket használja alapértelmezett countryHint értékként, hogy eltávolítsa ezt a viselkedést, ha ezt a paramétert üres sztringre `country_hint : ""` állítja. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Kimenet

```console
Language:  French
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel `language_detection_example()` argumentumként, majd hívja meg `detect_language()` a függvényt. A visszaadott válaszobjektum az észlelt nyelvet fogja tartalmazni , ha sikeres, és ha nem, akkor a `primary_language` `error` következőt: .

> [!Tip]
> Bizonyos esetekben nehéz lehet a bemenet alapján különböző nyelveket felfedni. A paraméterrel kétbetűs országkódot `country_hint` is megadhat. Alapértelmezés szerint az API az "US" értéket használja alapértelmezett countryHint értékként, ennek a viselkedésnek a eltávolításához alaphelyzetbe állíthatja ezt a paramétert, ha ezt az értéket üres sztringre `country_hint : ""` állítja. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Kimenet

```console
Language:  French
```


---

## <a name="named-entity-recognition-ner"></a>Megnevezett entitások felismerése (NER)

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

> [!NOTE]
> `3.1`Verzióban: 
> * Az entitás-összekapcsolás egy külön kérelem, mint a NER.

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel argumentumként, majd hívja meg a függvényt, és végigveszi `entity_recognition_example` `recognize_entities()` az eredményeket. A visszaadott válaszobjektum tartalmazza az észlelt entitások listáját a (ha sikeres) és a (ha `entity` `error` nem) entitásokat. Minden észlelt entitáshoz nyomtassa ki a Kategóriát, Sub-Category ha létezik.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Kimenet

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel argumentumként, majd hívja meg a függvényt, és végigveszi `entity_linking_example()` `recognize_linked_entities()` az eredményeket. A visszaadott válaszobjektum tartalmazza az észlelt entitások listáját a (ha sikeres) és a (ha `entities` `error` nem) entitásokat. Mivel a csatolt entitások egyedileg vannak azonosítva, ugyanazon entitás előfordulásai objektumok listájaként vannak egy objektum `entity` alatt `match` csoportosítva.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Kimenet

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>Személyazonosításra alkalmas adatok felismerése

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel argumentumként, majd hívja meg a függvényt, és végigveszi `pii_recognition_example` `recognize_pii_entities()` az eredményeket. A visszaadott válaszobjektum tartalmazza az észlelt entitások listáját a (ha sikeres) és a (ha `entity` `error` nem) entitásokat. Minden észlelt entitáshoz nyomtassa ki a Kategóriát, Sub-Category ha létezik.

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Kimenet

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

> [!NOTE]
> `3.0`Verzióban: 
> * Az entitás-összekapcsolás egy külön kérelem, mint a NER.

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel argumentumként, majd hívja meg a függvényt, és végigveszi `entity_recognition_example` `recognize_entities()` az eredményeket. A visszaadott válaszobjektum tartalmazza az észlelt entitások listáját a (ha sikeres) és a (ha `entity` `error` nem) entitásokat. Minden észlelt entitáshoz nyomtassa ki a Kategóriát, Sub-Category ha létezik.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Kimenet

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel argumentumként, majd hívja meg a függvényt, és végigveszi az `entity_linking_example()` `recognize_linked_entities()` eredményeket. A visszaadott válaszobjektum tartalmazza az észlelt entitások listáját a (ha sikeres) és a (ha `entities` `error` nem) entitásokat. Mivel a csatolt entitások egyedileg vannak azonosítva, a rendszer ugyanannak az entitásnak az előfordulásait objektumok listájaként csoportosítja egy `entity` objektum `match` alatt.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Kimenet

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

---

### <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel `key_phrase_extraction_example()` argumentumként, majd hívja meg a `extract_key_phrases()` függvényt. Az eredmény az észlelt kulcskifejezések listáját tartalmazza a (ha sikeres) és a (ha `key_phrases` `error` nem) kifejezéseket. Nyomtassa ki az észlelt kulcskifejezéseket.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Kimenet

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel `key_phrase_extraction_example()` argumentumként, majd hívja meg a `extract_key_phrases()` függvényt. Az eredmény az észlelt kulcskifejezések listáját tartalmazza a (ha sikeres) és a (ha `key_phrases` `error` nem) kifejezéseket. Nyomtassa ki az észlelt kulcskifejezéseket.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Kimenet

```console
    Key Phrases:
         cat
         veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-batch-analyze-operation"></a>Az API aszinkron használata a kötegelt elemzés művelettel

# <a name="version-31-preview"></a>[3.1-es előzetes verzió](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Hozzon létre egy nevű új függvényt, amely az ügyfelet veszi fel `analyze_batch_example()` argumentumként, majd hívja meg `begin_analyze_batch_actions()` a függvényt. Az eredmény egy hosszú ideig futó művelet lesz, amelynek eredményeit a rendszer lekérdezi.

```python
from azure.ai.textanalytics import (
    RecognizeEntitiesAction
)

def analyze_batch_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = client.begin_analyze_batch_actions(
            documents,
            display_name="Sample Text Analysis",
            actions=[RecognizeEntitiesAction()]
        )

        result = poller.result()
        action_results = [action_result for action_result in list(result) if not action_result.is_error]

        entities_recognition_task_result = action_results[0]
        print("Results of Entities Recognition action:")
        docs = [doc for doc in entities_recognition_task_result.document_results if not doc.is_error]

        for idx, doc in enumerate(docs):
            print("\nDocument text: {}".format(documents[idx]))
            for entity in doc.entities:
                print("Entity: {}".format(entity.text))
                print("...Category: {}".format(entity.category))
                print("...Confidence Score: {}".format(entity.confidence_score))
                print("...Offset: {}".format(entity.offset))
            print("------------------------------------------")

analyze_batch_example(client)
```

### <a name="output"></a>Kimenet

```console
Results of Entities Recognition task:
Document text: Microsoft was founded by Bill Gates and Paul Allen.
Entity: Microsoft
...Category: Organization
...Confidence Score: 0.83
...Offset: 0
Entity: Bill Gates
...Category: Person
...Confidence Score: 0.85
...Offset: 25
Entity: Paul Allen
...Category: Person
...Confidence Score: 0.9
...Offset: 40
------------------------------------------
```

A kötegelt elemzés művelettel piI-adatokat is észlelhet, és kulcskifejezéseket kinyerhet. Tekintse meg [a Batch Analyze mintát a](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_batch_actions.py) GitHubon.

# <a name="version-30"></a>[3.0-s verzió](#tab/version-3)

Ez a funkció a 3.0-s verzióban nem érhető el.

---
