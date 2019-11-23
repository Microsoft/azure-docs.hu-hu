---
title: Perform sentiment analysis with Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: This article will show you how to detect sentiment in text with the Azure Cognitive Services Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: 33c9c708adcc196bc7d9b2e8a066d18e4dd20608
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326618"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Example: Detect sentiment with Text Analytics

The [Azure Sentiment Analysis API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) evaluates text input and returns a sentiment score for each document. Scores range from 0 (negative) to 1 (positive).

Ez a funkció akkor hasznos, ha a közösségi média, a vevő értékelések és a vitafórumokon kell felismeri a pozitív és negatív hangulatokat. Content is provided by you. Models and training data are provided by the service.

Currently, the Sentiment Analysis API supports English, German, Spanish, and French. Más nyelvek előzetes verzióban érhetők el. További információk: [Támogatott nyelvek](../text-analytics-supported-languages.md).

> [!TIP]
> The Azure Text Analytics API also provides a Linux-based Docker container image for sentiment analysis, so you can [install and run the Text Analytics container](text-analytics-how-to-install-containers.md) close to your data.

## <a name="concepts"></a>Alapelvek

A Text Analytics gépi tanulási osztályozó algoritmussal generálja a 0 és 1 közötti hangulatpontszámot. Az 1-hez közeli értékek pozitív, míg a 0-hoz közeliek negatív hangulatot jelölnek. A modellt nagy mennyiségű hangulattársításos szöveggel előre betanították. Currently, it isn't possible to provide your own training data. The model uses a combination of techniques during text analysis. Techniques include text processing, part-of-speech analysis, word placement, and word associations. Az algoritmussal kapcsolatos további információk: [A Text Analytics bemutatása](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

A hangulatelemzés a teljes dokumentum történik, szemben azzal, amikor a szöveg egy konkrét eleméhez gyűjtjük ki a hangulatot. In practice, there's a tendency for scoring accuracy to improve when documents contain one or two sentences rather than a large block of text. Az objektivitás megfelelőségvizsgálati fázis során a modell meghatározza, hogy a dokumentum egészében objektív-e vagy hangulatot tartalmaz. A document that's mostly objective doesn't progress to the sentiment detection phase, which results in a 0.50 score, with no further processing. For documents that continue in the pipeline, the next phase generates a score above or below 0.50. The score depends on the degree of sentiment detected in the document.

## <a name="sentiment-analysis-v3-public-preview"></a>Sentiment Analysis v3 public preview

The next version of Sentiment Analysis is now available for public preview. It provides significant improvements in the accuracy and detail of the API's text categorization and scoring. Try it using the [API test console](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment).

> [!NOTE]
> * The Sentiment Analysis v3 request format and [data limits](../overview.md#data-limits) are the same as the previous version.
> * At this time, Sentiment Analysis v3:
>    * Currently supports the English (`en`), Japanese (`ja`), Chinese Simplified (`zh-Hans`),  Chinese Traditional (`zh-Hant`), French (`fr`), Italian (`it`), Spanish (`es`), Dutch (`nl`), Portuguese (`pt`), and German (`de`) languages.
>    * Is available in the following regions: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`, and `West US 2`.

|Szolgáltatás |Leírás  |
|---------|---------|
|Improved accuracy     | Az előző verziókhoz képest jelentősen javult a pozitív, semleges, negatív és vegyes hangulat szöveges dokumentumokban való észlelése.           |
|Document and sentence-level sentiment score     | A hangulatot egy dokumentumon és annak egyes mondatain belül is észleli. Ha a dokumentum több mondatból áll, akkor minden mondathoz külön hangulatpontszám lesz rendelve.         |
|Sentiment labeling and scoring     | The API now returns sentiment categories for text, in addition to a sentiment score. The categories are `positive`, `negative`, `neutral`, and `mixed`.       |
| Improved output | Sentiment analysis now returns information for both an entire text document and its individual sentences. |
| model-version parameter | An optional parameter for choosing which version of the Text Analytics model is used on your data. |

### <a name="sentiment-labeling"></a>Sentiment labeling

Sentiment Analysis v3 can return scores and labels at a sentence and document level. The scores and labels are `positive`, `negative`, and `neutral`. At the document level, the `mixed` sentiment label (not the score) also can be returned. The sentiment of the document is determined by aggregating the scores of the sentences.

| Sentence sentiment                                                        | Returned document label |
|---------------------------------------------------------------------------|----------------|
| At least one positive sentence and the rest of the sentences are neutral. | `positive`     |
| At least one negative sentence and the rest of the sentences are neutral.  | `negative`     |
| At least one negative sentence and at least one positive sentence.         | `mixed`        |
| All sentences are neutral.                                                 | `neutral`      |

### <a name="model-versioning"></a>Model versioning

> [!NOTE]
> Model versioning for sentiment analysis is available starting in version `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="sentiment-analysis-v3-example-request"></a>Sentiment Analysis v3 example request

The following JSON is an example of a request made to the new version of Sentiment Analysis. The request formatting is the same as the previous version:

```json
    {
        "documents": [
        {
            "language": "en",
            "id": "1",
            "text": "Hello world. This is some input text that I love."
        },
        {
            "language": "en",
            "id": "2",
            "text": "It's incredibly sunny outside! I'm so happy."
        }
        ],
    }
```

### <a name="sentiment-analysis-v3-example-response"></a>Sentiment Analysis v3 example response

While the request format is the same as the previous version, the response format has changed. The following JSON is an example response from the new version of the API:

```json
    {
        "documents": [
            {
                "id": "1",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.98570585250854492,
                    "neutral": 0.0001625834556762,
                    "negative": 0.0141316400840878
                },
                "sentences": [
                    {
                        "sentiment": "neutral",
                        "sentenceScores": {
                            "positive": 0.0785155147314072,
                            "neutral": 0.89702343940734863,
                            "negative": 0.0244610067456961
                        },
                        "offset": 0,
                        "length": 12
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.98570585250854492,
                            "neutral": 0.0001625834556762,
                            "negative": 0.0141316400840878
                        },
                        "offset": 13,
                        "length": 36
                    }
                ]
            },
            {
                "id": "2",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.89198976755142212,
                    "neutral": 0.103382371366024,
                    "negative": 0.0046278294175863
                },
                "sentences": [
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.78401315212249756,
                            "neutral": 0.2067587077617645,
                            "negative": 0.0092281140387058
                        },
                        "offset": 0,
                        "length": 30
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.99996638298034668,
                            "neutral": 0.0000060341349126,
                            "negative": 0.0000275444017461
                        },
                        "offset": 31,
                        "length": 13
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="example-c-code"></a>Example C# code

You can find an example C# application that calls this version of Sentiment Analysis on [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="preparation"></a>Előkészítés

Sentiment analysis produces a higher-quality result when you give it smaller chunks of text to work on. Ez a ellentétes a kulcsszókereséssel, amely nagyobb mennyiségű szöveg esetén teljesít jobban. A legjobb eredmény elérése érdekében célszerű a bemenetet ennek megfelelően átszervezni.

You must have JSON documents in this format: ID, text, and language.

Document size must be under 5,120 characters per document. You can have up to 1,000 items (IDs) per collection. A kollekció elküldése a kérelem törzsében történik. The following sample is an example of content you might submit for sentiment analysis:

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1\. lépés: A kérés felépítése

For more information on request definition, see [Call the Text Analytics API](text-analytics-how-to-call-api.md). A következő pontokat a kényelem kedvéért itt megismételjük:

+ Create a POST request. To review the API documentation for this request, see the [Sentiment Analysis API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Set the HTTP endpoint for sentiment analysis by using either a Text Analytics resource on Azure or an instantiated [Text Analytics container](text-analytics-how-to-install-containers.md). You must include `/text/analytics/v2.1/sentiment` in the URL. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`.

+ Set a request header to include the [access key](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) for Text Analytics operations.

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Use [Postman](text-analytics-how-to-call-api.md) or open the **API testing console** in the [documentation](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) to structure the request and post it to the service.

## <a name="step-2-post-the-request"></a>2\. lépés: A kérés elküldése

Az elemzés a kérelem megkapásakor történik meg. For information on the size and number of requests you can send per minute and second, see the [data limits](../overview.md#data-limits) section in the overview.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.


## <a name="step-3-view-the-results"></a>Step 3: View the results

The sentiment analyzer classifies text as predominantly positive or negative. It assigns a score in the range of 0 to 1. A 0,5 közeli értékek semlegesek vagy határozatlanok. A 0,5-ös pontszám semlegességet jelez. When a string can't be analyzed for sentiment or has no sentiment, the score is always 0.5 exactly. Ha például egy spanyol nyelvű sztringet ad meg angol nyelvi kóddal, a pontszám 0,5 lesz.

A kimenetet visszaadása azonnali. You can stream the results to an application that accepts JSON or save the output to a file on the local system. Then, import the output into an application that you can use to sort, search, and manipulate the data.

The following example shows the response for the document collection in this article:

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="summary"></a>Összefoglalás

In this article, you learned concepts and workflow for sentiment analysis by using Text Analytics in Azure Cognitive Services. Összegezve:

+ The [Sentiment Analysis API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) is available for selected languages.
+ JSON documents in the request body include an ID, text, and language code.
+ The POST request is to a `/sentiment` endpoint by using a personalized [access key and an endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) that's valid for your subscription.
+ Response output, which consists of a sentiment score for each document ID, can be streamed to any app that accepts JSON. Example apps include Excel and Power BI, to name a few.

## <a name="see-also"></a>Lásd még:

 [Text Analytics overview](../overview.md) [Frequently asked questions (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kulcsszavak kinyerése](text-analytics-how-to-keyword-extraction.md)
