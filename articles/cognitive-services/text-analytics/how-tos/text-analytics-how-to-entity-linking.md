---
title: Az entitások felismerésének használata a Text Analytics API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan azonosíthatja és egyértelműsítse a szövegben talált entitás identitását a Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 903147b6c9f102b83f65d67cf41d4df0c62c7b54
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280564"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Elnevezett entitások felismerésének használata a Text Analyticsban

A Text Analytics API lehetővé teszi a strukturálatlan szöveg átrendezését és a disambiguated entitások listájának beolvasása, amely a weben található további információkra mutató hivatkozásokat tartalmaz. Az API támogatja a nevesített entitások felismerését (egyrészt) több entitás kategóriája és az entitások összekapcsolása esetén.

## <a name="entity-linking"></a>Entitáskapcsolás

Az entitások összekapcsolása lehetővé teszi egy szövegben talált entitás identitásának azonosítását és egyértelműsítse (például annak meghatározását, hogy a "Mars" szó előfordulása a bolygóra vagy a háború római istenere vonatkozik). Ennek a folyamatnak a megfelelő nyelven kell megadnia egy tudásbázist, hogy az felismert entitások szövegben legyenek összekapcsolva. Az Entity Linking a [wikipedia](https://www.wikipedia.org/) -t használja a Tudásbázisban.

## <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

Az elnevezett entitások felismerése lehetővé teszi a különböző entitások azonosítását a szövegben, és azokat előre definiált osztályokra vagy típusokra kategorizálhatja, például: személy, hely, esemény, termék és szervezet.  

## <a name="personally-identifiable-information-pii"></a>Személyazonosításra alkalmas adatok

A személyre szabott funkció az egyoldalas szolgáltatás része, és képes azonosítani és kivonni a bizalmas entitásokat egy olyan szövegben, amely egy személyhez van társítva, például: telefonszám, e-mail-cím, levelezési cím, Passport-szám.

## <a name="named-entity-recognition-features-and-versions"></a>Elnevezett entitás-felismerési funkciók és verziók

| Szolgáltatás                                                         | V 3.0 | V 3.1 – előzetes verzió. 4 |
|-----------------------------------------------------------------|--------|----------|
| Egyszeri és batch-kérelmek módszerei                          | X      | X        |
| Bővített entitások felismerése több kategória között           | X      | X        |
| Különálló végpontok az entitások összekapcsolását és a meghívást kérő kérelmek küldéséhez. | X      | X        |
| Személyes ( `PII` ) és Health ( `PHI` ) információs entitások felismerése        |        | X        |
| Kivonás `PII`        |        | X        |

További információért lásd a [nyelvi támogatást](../language-support.md) ismertető témakört.

A nevesített entitások felismerése v3 több típusra kiterjedő kibővített észlelést biztosít. A "v 3.0" jelenleg az [általános entitások kategóriájában](../named-entity-types.md)ismeri fel az entitásokat.

Elnevezett Entity Recognition v 3.1 – preview. 4 tartalmazza a v 3.0 észlelési képességeit, és: 
* A személyes adatok () észlelésének lehetősége `PII` a `v3.1-preview.4/entities/recognition/pii` végpont használatával. 
* Egy opcionális `domain=phi` paraméter a bizalmas állapottal kapcsolatos információk észleléséhez ( `PHI` ).
* [Aszinkron művelet](text-analytics-how-to-call-api.md) a `/analyze` végpont használatával.

További információ: az [entitások kategóriái](../named-entity-types.md) című cikk és a [végpontok igénylése](#request-endpoints) című rész. A megbízhatósági pontszámokkal kapcsolatos további információkért tekintse meg a [text Analytics átláthatósági megjegyzését](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="sending-a-rest-api-request"></a>REST API kérelem küldése

### <a name="preparation"></a>Előkészítés

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg, nyelv.

Minden dokumentumnak 5 120 karakterből kell állnia, és gyűjteményben legfeljebb 1 000 elem (azonosító) lehet. A kollekció elküldése a kérelem törzsében történik.

### <a name="structure-the-request"></a>A kérelem szerkezete

Hozzon létre egy POST kérést. A következő hivatkozásokon a [Poster](text-analytics-how-to-call-api.md) vagy az **API-tesztelési konzol** használatával gyorsan feldolgozhatja és elküldheti az egyiket. 

> [!NOTE]
> Az Azure Portalon megtalálhatja Text Analytics erőforrásának kulcsát és végpontját. Ezek az erőforrás **gyors üzembe helyezés** lapján, az **Erőforrás-kezelés** területen találhatók. 


### <a name="request-endpoints"></a>Kérelmek végpontjai

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-preview)

A nevesített entitások felismerése `v3.1-preview.4` külön végpontokat használ a meghívásos, a személyes és az entitások összekapcsolására vonatkozó kérelmekhez. Az alábbi URL-formátumot használja a kérelem alapján.

**Entitás összekapcsolása**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/linking`

[Elnevezett entitás-felismerés verziója 3,1 – előzetes verzió referenciája a következőhöz: `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-4/operations/EntitiesLinking)

**Megnevezett entitások felismerése**
* Általános entitások – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/general`

[Elnevezett entitás-felismerés verziója 3,1 – előzetes verzió referenciája a következőhöz: `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-4/operations/EntitiesRecognitionGeneral)

**Személyazonosításra alkalmas adatok**
* Személyes ( `PII` ) információk – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/pii`

A nem kötelező `domain=phi` paraméterrel is felderítheti az állapotadatok ( `PHI` ) szövegben való észlelését. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/pii?domain=phi`

A-től kezdődően `v3.1-preview.4` a JSON-válasz tartalmaz egy `redactedText` tulajdonságot, amely tartalmazza azt a módosított bemeneti szöveget, amelyben a `*` rendszer az entitások egyes karakterei esetében az észlelt személyes adatokat felváltotta.

[Elnevezett entitás-felismerés verziója 3,1 – előzetes verzió referenciája a következőhöz: `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-4/operations/EntitiesRecognitionPii)

Az API megpróbálja felderíteni a [felsorolt entitások kategóriáit](../named-entity-types.md?tabs=personal) egy adott dokumentum nyelvén. Ha meg szeretné határozni, hogy mely entitásokat fogja észlelni és visszaadni, a megfelelő entitás-kategóriákkal válassza a nem kötelező személyre vonatkozó kategóriák paramétert. Ez a paraméter lehetővé teszi azon entitások észlelését is, amelyek alapértelmezés szerint nincsenek engedélyezve a dokumentum nyelvén. Például egy francia jogosítvány száma, amely angol nyelvű szövegben fordulhat elő.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/pii?piiCategories=[FRDriversLicenseNumber]`

**Aszinkron művelet**

A `v3.1-preview.4` verziótól kezdődően a végpont használatával aszinkron módon küldheti el a kéréseket és az entitások összekapcsolását `/analyze` .

* Aszinkron művelet – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze`

Lásd: [a Text Analytics API meghívása](text-analytics-how-to-call-api.md) az aszinkron kérelmek küldésével kapcsolatos információkért.

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Az elnevezett entitások felismerése v3 külön végpontokat használ az Egypéldányos és az entitás-összekapcsolási kérelmekhez Az alábbi URL-formátumot használja a kérelme alapján:

**Entitás összekapcsolása**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Elnevezett entitás-felismerés 3,0-es verziójának referenciája `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Megnevezett entitások felismerése**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Elnevezett entitás-felismerés 3,0-es verziójának referenciája `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

A Text Analytics API kulcsot tartalmazó kérelem fejlécének beállítása. A kérelem törzsében adja meg az előkészített JSON-dokumentumokat.

## <a name="example-requests"></a>Példák a kérelmekre

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-preview)

### <a name="example-synchronous-ner-request"></a>Példa szinkron kérésre 

A következő JSON egy példa az API-nak elküldhető tartalomra. A kérelem formátuma megegyezik az API mindkét verziójával.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

### <a name="example-asynchronous-ner-request"></a>Példa aszinkron hívásra

Ha az `/analyze` [aszinkron művelethez](text-analytics-how-to-call-api.md)a végpontot használja, az API-nak küldött feladatokat tartalmazó választ fog kapni.

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}
```

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

### <a name="example-synchronous-ner-request"></a>Példa szinkron kérésre 

Az 3,0-es verzió csak szinkron műveletet tartalmaz. A következő JSON egy példa az API-nak elküldhető tartalomra. A kérelem formátuma megegyezik az API mindkét verziójával.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

---

## <a name="post-the-request"></a>A kérelem közzététele

Az elemzés a kérelem megkapásakor történik meg. Tekintse meg az [adatkorlátozásokról](../overview.md#data-limits) szóló cikket, amely a percenként elküldhető kérelmek méretével és számával kapcsolatos információkat tartalmazza.

A Text Analytics API állapot nélküli. A fiókjában nem tárolunk semmilyen adatmennyiséget, és az eredményeket azonnal visszaadja a válaszban.

## <a name="view-results"></a>Eredmények megtekintése

Az összes POST kérelem egy JSON formátumú választ ad vissza az azonosítók és az észlelt entitás tulajdonságaival.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba. A többnyelvű és a Emoji-támogatás miatt a válasz szöveges eltolásokat is tartalmazhat. További információ: [a szöveg eltolásának feldolgozása](../concepts/text-offsets.md).

### <a name="example-responses"></a>Válaszok – példa

A 3-as verzió külön végpontokat biztosít az általános célú, a személyes adatokkal és az entitások összekapcsolásával kapcsolatban. Az 3,1-es verzió – a pareview aszinkron elemzési módot tartalmaz. A műveletekre adott válaszok alább láthatók. 

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-preview)

### <a name="synchronous-example-results"></a>Szinkron példa eredményei

Példa az általános célú válaszra:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

Példa a személyes adatokra válaszra:

```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Példa a választ hivatkozó entitásra:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

### <a name="example-asynchronous-result"></a>Példa aszinkron eredményre

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Példa az általános célú válaszra:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

---


## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta az entitások összekapcsolásával kapcsolatos fogalmakat és munkafolyamatokat a Cognitive Services Text Analytics használatával. Összegezve:

* A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
* A POST kéréseket egy vagy több végpontra küldi a rendszer, egy személyre szabott [hozzáférési kulccsal és egy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , az előfizetéséhez érvényes végpontot használva.
* Bármely alkalmazásban használhatók olyan válaszok, amelyek összekapcsolt entitásokból (beleértve a megbízhatósági pontszámokat, az eltolásokat és a webes hivatkozásokat) tartalmazzák

## <a name="next-steps"></a>Következő lépések

* [A Text Analytics áttekintése](../overview.md)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/client-libraries-rest-api.md)
* [Újdonságok](../whats-new.md)
