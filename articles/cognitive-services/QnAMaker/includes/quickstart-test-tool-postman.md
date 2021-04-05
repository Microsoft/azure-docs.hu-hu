---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: fa497b69b067d5556f11effdb52505895ecc3bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94386494"
---
Ez a Poster-alapú rövid útmutató végigvezeti Önt a Tudásbázisból kapott válasz beszerzésén.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Poster**](https://www.getpostman.com/).
* Rendelkeznie kell
    * Egy [QnA Maker szolgáltatás](../How-To/set-up-qnamaker-service-azure.md)
    * A betanított és közzétett Tudásbázis a rövid útmutatóból kiépített [kérdésekkel és válaszokkal](../Quickstarts/add-question-metadata-portal.md) a metaadatokkal és a Chit-csevegéssel van konfigurálva.

> [!NOTE]
> Ha készen áll arra, hogy a tudásalapú kérdésre válaszoljon, be kell [tanítania](../Quickstarts/create-publish-knowledge-base.md#save-and-train) és [közzé](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) kell tennie a tudásbázist. A Tudásbázis közzétételekor a **közzétételi** oldal MEGJELENÍTI a HTTP-kérelmek beállításait a válasz létrehozásához. A **Poster** lapon a válasz létrehozásához szükséges beállítások láthatók.

## <a name="set-up-postman-for-requests"></a>Poster beállítása a kérelmekhez

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Ez a rövid útmutató ugyanazokat a beállításokat használja, mint a Poster **post** -kérelem, majd úgy konfigurálja, hogy a rendszer a szolgáltatásnak a lekérdezéshez szükséges JSON-t küldje a szolgáltatásnak.

Ezzel az eljárással konfigurálhatja a Poster-t, majd beolvashatja az összes további szakaszt a POST Body JSON konfigurálásához.

1. A Tudásbázis **Beállítások** lapján kattintson a **poster (beküldés** ) fülre, és tekintse meg a Tudásbázisból a válasz létrehozásához használt konfigurációt. Másolja a következő adatokat a Poster-ban való használatra.

    |Name|Beállítás|Cél és érték|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Ez az URL-cím HTTP-metódusa és útvonala.|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|Ez az URL-cím gazdagépe. Fűzze össze a gazdagépet, és tegye az értékeket a teljes generateAnswer URL-cím beszerzéséhez.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|A fejléc értéke, hogy engedélyezze az Azure-ba irányuló kérését. |
    |`Content-type`|`application/json`|A tartalom fejlécének értéke.|
    ||`{"question":"<Your question>"}`|A POST kérelem törzse JSON-objektumként. Ez az érték a következő szakaszban módosul, attól függően, hogy mit kell tennie a lekérdezésnek.|

1. Nyissa meg a Poster-t, és hozzon létre egy új alapszintű **post** -kérelmet a közzétett Tudásbázis-beállításokkal. A következő szakaszban megváltoztathatja a POST Body JSON-t, hogy megváltoztassa a lekérdezést a Tudásbázisban.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

Ez a rövid útmutató ugyanazokat a beállításokat használja, mint a Poster **post** -kérelem, majd úgy konfigurálja, hogy a rendszer a szolgáltatásnak a lekérdezéshez szükséges JSON-t küldje a szolgáltatásnak.

Ezzel az eljárással konfigurálhatja a Poster-t, majd beolvashatja az összes további szakaszt a POST Body JSON konfigurálásához.

1. A Tudásbázis **Beállítások** lapján kattintson a **poster (beküldés** ) fülre, és tekintse meg a Tudásbázisból a válasz létrehozásához használt konfigurációt. Másolja a következő adatokat a Poster-ban való használatra.

    |Name|Beállítás|Cél és érték|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Ez az URL-cím HTTP-metódusa és útvonala.|
    |`Host`|`https://YOUR-RESOURCE_NAME.cognitiveservices.azure.com/qnamaker`|Ez az URL-cím gazdagépe. Fűzze össze a gazdagépet, és tegye az értékeket a teljes generateAnswer URL-cím beszerzéséhez.|
    |`Ocp-Apim-Subscription-Key`|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Az a fejléc értéke, amely engedélyezi a kérést. |
    |`Content-type`|`application/json`|A tartalom fejlécének értéke.|
    ||`{"question":"<Your question>"}`|A POST kérelem törzse JSON-objektumként. Ez az érték a következő szakaszban módosul, attól függően, hogy mit kell tennie a lekérdezésnek.|

1. Nyissa meg a Poster-t, és hozzon létre egy új alapszintű **post** -kérelmet a közzétett Tudásbázis-beállításokkal. A következő szakaszban megváltoztathatja a POST Body JSON-t, hogy megváltoztassa a lekérdezést a Tudásbázisban.
---

## <a name="use-metadata-to-filter-answer"></a>A válasz szűrése metaadatok használatával

Egy korábbi rövid útmutatóban a metaadatok két QnA-párokhoz lettek hozzáadva két különböző kérdés megkülönböztetése érdekében. Adja hozzá a metaadatokat a lekérdezéshez, hogy csak a megfelelő QnA-párokra korlátozza a szűrőt.

1. A Poster-ben módosítsa a lekérdezés JSON-t úgy, hogy hozzáadja a `strictFilters` tulajdonságot a név/érték párral `service:qna_maker` . A JSON-törzsnek a következőket kell tennie:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    A kérdés csak egyetlen szó, `size` amely a két kérdés és válasz párok bármelyikét visszaállíthatja. A `strictFilters` tömb arra utasítja a választ, hogy csak a `qna_maker` válaszokat csökkentse.

1. A válasz csak azt a választ tartalmazza, amely megfelel a szűrési feltételeknek.

    A következő válasz formázva lett az olvashatóság érdekében:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Ha van olyan kérdés-válasz pár, amely nem felel meg a keresési kifejezésnek, de nem felel meg a szűrőnek, akkor a rendszer nem adja vissza. Ehelyett a rendszer az általános választ `No good match found in KB.` adja vissza.

## <a name="use-debug-query-property"></a>Hibakeresési lekérdezési tulajdonság használata

> [!NOTE]
>A függőségek hibakeresési tulajdonságát nem ajánlott használni. Ez a tulajdonság hozzá lett adva, hogy segítse a termék csapatát a hibaelhárításban.

A hibakeresési információk segítenek megérteni a visszaadott válasz meghatározásának módját. Habár hasznos, nem szükséges. Ha hibakeresési információval szeretne válaszolni, adja hozzá a következő `debug` tulajdonságot:

1. A Poster-ben módosítsa a törzs JSON-t úgy, hogy hozzáadja a `debug` tulajdonságot. A JSON a következőket kell tennie:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. A válasz tartalmazza a válaszra vonatkozó információkat. A következő JSON-kimenetben néhány hibakeresési részlet lecserélve a három pontra.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Tesztelési Tudásbázis használata

Ha a teszt Tudásbázisból szeretne választ kapni, használja a `isTest` Body (törzs) tulajdonságot.

A Poster-ben módosítsa a törzs JSON-t úgy, hogy hozzáadja a `isTest` tulajdonságot. A JSON a következőket kell tennie:

```json
{
    'question':'size',
    'isTest': true
}
```

A JSON-válasz ugyanazt a sémát használja, mint a közzétett Tudásbázis-lekérdezés.

> [!NOTE]
> Ha a teszt és a közzétett tudásbázisok pontosan ugyanazok, akkor továbbra is lehet némi eltérés, mert a tesztelési index az erőforrás összes tudásbázisa között meg van osztva.

## <a name="query-for-a-chit-chat-answer"></a>A Chit-Chat-válasz lekérdezése

1. A Poster-ben csak a törzs JSON-fájlját módosítsa egy beszélgetési végű utasításra a felhasználótól. A JSON a következőket kell tennie:

    ```json
    {
        'question':'thank you'
    }
    ```

1. A válasz tartalmazza a pontszámot és a választ.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Mivel a `Thank you` utasításhoz tartozó kérdés pontosan megegyezik egy csevegési kérdéssel, a QnA Maker 100-as pontszámmal teljesen biztos a válaszban. QnA Maker az összes kapcsolódó kérdést, valamint a Chit-Chat metaadat-címkét tartalmazó metaadat-tulajdonságot is visszaadja.

## <a name="use-threshold-and-default-answer"></a>Küszöbérték és alapértelmezett válasz használata

A válaszhoz minimális küszöbértéket is igényelhet. Ha a küszöbérték nem teljesül, a rendszer az alapértelmezett választ adja vissza.

1. A Poster-ben csak a törzs JSON-fájlját módosítsa egy beszélgetési végű utasításra a felhasználótól. A JSON a következőket kell tennie:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    A Tudásbázis nem találja a választ, mert a kérdés pontszáma 71%, ehelyett a Tudásbázis létrehozásakor megadott alapértelmezett választ kell visszaadnia.

    A visszaadott JSON-válasz, beleértve a pontszámot és a választ:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker egy pontszámot adott vissza `0` , ami nem jelent megbízhatóságot. Emellett az alapértelmezett választ is visszaadja.

1. Módosítsa a küszöbértéket 60%-ra, és kérje újra a lekérdezést:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    A visszaadott JSON megtalálta a választ.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```