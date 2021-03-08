---
title: 'Gyors útmutató: a Text Analytics meghívása a cURL használatával REST API'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan lehet gyorsan megkezdeni az Azure Cognitive Services Text Analytics API használatát.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/01/2020
ms.author: aahi
ms.openlocfilehash: 17c24e19068c3d084da8a3c888729c2f78185c17
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444118"
---
# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

[a v 3.1 dokumentációja](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/)

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

[a v3-referenciák dokumentációja](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0) 

---

## <a name="prerequisites"></a>Előfeltételek

* A [curl](https://curl.haxx.se/)jelenlegi verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" hozzon létre egy Text Analytics erőforrást, "  target="_blank"> és hozzon létre egy Text Analytics-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Text Analytics APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

> [!NOTE]
> * A következő BASH-példák a `\` sor folytatási karakterét használják. Ha a konzol vagy a terminál eltérő vonalbeli folytatási karaktert használ, használja ezt a karaktert.
> * A [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code)megtalálhatja a nyelvspecifikus példákat is.
> * Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott Text Analytics erőforrás kulcsát és végpontját. Ezek az erőforrás **kulcs és végpont** lapján, az **Erőforrás-kezelés** területen találhatók. Ezután cserélje le az alábbi kódban szereplő karakterláncokat a kulcsra és a végpontra.
A Text Analytics API meghívásához a következő információkra lesz szüksége:


|parameter  |Description  |
|---------|---------|
|`-X POST <endpoint>`     | Megadja az API eléréséhez szükséges végpontot.        |
|`-H Content-Type: application/json`     | A JSON-adatok küldésének tartalomtípusa.          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | Megadja az API eléréséhez szükséges kulcsot.        |
|`-d <documents>`     | A küldeni kívánt dokumentumokat tartalmazó JSON.         |

A következő cURL-parancsokat egy BASH-rendszerhéjból hajtja végre a rendszer. Szerkessze ezeket a parancsokat saját erőforrás-névvel, erőforrás-kulccsal és JSON-értékekkel.

## <a name="sentiment-analysis"></a>Hangulatelemzés

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

> [!NOTE]
> Az alábbi példa egy `opinionMining=true` , a (z) paraméterrel Hangulatelemzés, a (z) paraméterrel a (z), a (z) és a (z) szövegben található, a szempontokkal (például a termékek vagy szolgáltatások attribútumaival) kapcsolatos vélemények részletes információit

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/sentiment?opinionMining=true \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "The customer service here is really good."}]}'
```

### <a name="json-response"></a>JSON-válasz

```json
{
   "documents":[
      {
         "id":"1",
         "sentiment":"positive",
         "confidenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
         },
         "sentences":[
            {
               "sentiment":"positive",
               "confidenceScores":{
                  "positive":1.0,
                  "neutral":0.0,
                  "negative":0.0
               },
               "offset":0,
               "length":41,
               "text":"The customer service here is really good.",
               "aspects":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":4,
                     "length":16,
                     "text":"customer service",
                     "relations":[
                        {
                           "relationType":"opinion",
                           "ref":"#/documents/0/sentences/0/opinions/0"
                        }
                     ]
                  }
               ],
               "opinions":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":36,
                     "length":4,
                     "text":"good",
                     "isNegated":false
                  }
               ]
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
``` 

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/sentiment/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "I had the best day of my life. I wish you were there with me."}]}'
```

### <a name="json-response"></a>JSON-válasz

```json
{
  "documents":[
    {
      "id":"1",
      "sentiment":"positive",
      "documentScores":{
        "positive":1.0,
        "neutral":0.0,
        "negative":0.0
      },
      "sentences":[
        {
          "sentiment":"positive",
          "sentenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
          },
          "offset":0,
          "length":30
        }
      ]
    }
  ],
  "errors":[
  ],
  "modelVersion":"2019-10-01"
}
```

---


## <a name="language-detection"></a>Nyelvfelismerés

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

### <a name="json-response"></a>JSON-válasz

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":0.99
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-09-01"
}
```

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

### <a name="json-response"></a>JSON-válasz

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":0.99
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-09-01"
}
```

---


## <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```


### <a name="json-response"></a>JSON-válasz

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "text":"trip",
               "category":"Event",
               "offset":18,
               "length":4,
               "confidenceScore":0.61
            },
            {
               "text":"Seattle",
               "category":"Location",
               "subcategory":"GPE",
               "offset":26,
               "length":7,
               "confidenceScore":0.82
            },
            {
               "text":"last week",
               "category":"DateTime",
               "subcategory":"DateRange",
               "offset":34,
               "length":9,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}

```

### <a name="detecting-personally-identifying-information"></a>Személyazonosításra alkalmas adatok észlelése

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

```bash
curl -X POST https://your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/entities/recognition/pii \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Call our office at 312-555-1234, or send an email to support@contoso.com"}]}'
```

### <a name="json-response"></a>JSON-válasz

```json
{
   "documents":[
      {
         "redactedText":"Insurance policy for *** on file 123-12-1234 is here by approved.",
         "id":"1",
         "entities":[
            {
               "text":"SSN",
               "category":"Organization",
               "offset":21,
               "length":3,
               "confidenceScore":0.45
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```


### <a name="json-response"></a>JSON-válasz

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "text":"trip",
               "category":"Event",
               "offset":18,
               "length":4,
               "confidenceScore":0.61
            },
            {
               "text":"Seattle",
               "category":"Location",
               "subcategory":"GPE",
               "offset":26,
               "length":7,
               "confidenceScore":0.82
            },
            {
               "text":"last week",
               "category":"DateTime",
               "subcategory":"DateRange",
               "offset":34,
               "length":9,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
```

---

## <a name="entity-linking"></a>Entitás összekapcsolása

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```

### <a name="json-response"></a>JSON-válasz

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"0d47c987-0042-5576-15e8-97af601614fa",
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"df2c4376-9923-6a54-893f-2ee5a5badbc7",
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"52535f87-235e-b513-54fe-c03e4233ac6e",
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```


### <a name="json-response"></a>JSON-válasz

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```


---


## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]


#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Hello world. This is some input text that I love."}]}'
```

```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle",
            "week"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

### <a name="json-response"></a>JSON-válasz

```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle",
            "week"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

---
