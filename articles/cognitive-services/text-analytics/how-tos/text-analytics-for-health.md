---
title: Az Text Analytics használata az állapothoz
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan kinyerheti és címkézheti az orvosi adatokat strukturálatlan klinikai szövegből Text Analytics for Health használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: d7f71afd1cf47ab1a94d984090e8fb52ee69c6b4
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99088938"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Útmutató: a Text Analytics for Health használata (előzetes verzió)

> [!IMPORTANT] 
> A Text Analytics for Health egy előzetes verzió, amely az "adott állapotban" és "minden HIBÁval" érhető el. Ennek megfelelően a **text Analytics for Health (előzetes verzió) nem valósítható meg, és nem helyezhető üzembe semmilyen éles használatban.** Az egészségügyi Text Analytics nem az orvosi eszközként, klinikai támogatásként, diagnosztikai eszközként vagy más olyan technológiával való használatra készültek, amelyet a diagnosztika, a gyógyítás, a mérséklés, a kezelés vagy más feltételek megelőzéséhez, illetve más feltételekhez való felhasználáshoz kíván használni, és a Microsoft semmilyen jogot nem biztosít erre a képességre ilyen célra. Ezt a képességet nem úgy tervezték, hogy az orvosi szakorvosok vagy egészségügyi szakvélemények, diagnózis, kezelés vagy az egészségügyi szakemberek klinikai ítélete helyett ne legyenek implementálva, és ne legyenek használhatók. Az ügyfél kizárólag a Text Analytics for Health szolgáltatásért felelős. A Microsoft nem garantálja, hogy az állapotra vagy a képességgel kapcsolatban biztosított bármely anyagra vonatkozó Text Analytics bármely egészségügyi célra elegendő, vagy bármely személy egészségügyi vagy orvosi követelményeinek kielégítése érdekében. 


A Text Analytics for Health a Text Analytics API szolgáltatás egyik funkciója, amely kinyeri és felcímkézi az olyan strukturálatlan szövegekből származó releváns egészségügyi információkat, mint az orvos megjegyzései, a mentesítési összefoglalók, a klinikai dokumentumok és az elektronikus egészségügyi rekordok.  A szolgáltatás két módon használható: 

* [A web-alapú API (aszinkron)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Docker-tároló (szinkron)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Funkciók

Az állapot Text Analytics az elnevezett entitások felismerése, a relációk kinyerése, az entitások kivonása és az angol nyelvű szöveggel összekapcsoló entitások a strukturálatlan klinikai és bioorvosi szövegek elemzését végzik.

### <a name="named-entity-recognition"></a>[Megnevezett entitások felismerése](#tab/ner)

A nevesített entitások felismerése észleli a strukturálatlan szövegben említett szavakat és kifejezéseket, amelyek egy vagy több szemantikai típushoz társíthatók, például a diagnosztika, a gyógyszer neve, a tünet/aláírás vagy az életkor.

> [!div class="mx-imgBorder"]
> ![Állapot](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Kapcsolatok kibontása](#tab/relation-extraction)

A kapcsolat kibontása azonosítja a szövegben említett fogalmak közötti értelmes kapcsolatokat. A "feltétel időpontja" típusú kapcsolatok például a feltétel nevének egy időponthoz társításával találhatók meg. 

> [!div class="mx-imgBorder"]
> ![Állapot újraindítása](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entitáskapcsolás](#tab/entity-linking)

Az entitások a disambiguates különböző entitásokat kapcsolnak össze úgy, hogy a szövegben megnevezett entitásokat társítanak a fogalmak előre definiált adatbázisában található fogalmakhoz. Például az egységes orvosi nyelvi rendszer (UMLS).

> [!div class="mx-imgBorder"]
> ![Állapot EL](../media/ta-for-health/health-entity-linking.png)

A Text Analytics for Health támogatja az egyesített orvosi nyelvi rendszer ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) Metathesaurus tudásbázisban található állapot-és orvosbiológiai szótárakhoz összekapcsolását.

### <a name="negation-detection"></a>[Tagadás észlelése](#tab/negation-detection) 

Az orvosi tartalom értelmét nagy hatással van a módosítók, például a tagadás, ami kritikus következményekkel járhat, ha nem diagnosztizálják. A Text Analytics for Health támogatja a szövegben említett különböző entitások tagadásának észlelését. 

> [!div class="mx-imgBorder"]
> ![Állapot negatív](../media/ta-for-health/health-negation.png)

---

A támogatott entitások teljes listájáért tekintse meg az Text Analytics for Health által visszaadott [entitási kategóriákat](../named-entity-types.md?tabs=health) . A megbízhatósági pontszámokkal kapcsolatos információkért tekintse meg a [text Analytics átláthatósági megjegyzését](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages-and-regions"></a>Támogatott nyelvek és régiók

A Text Analytics for Health csak az angol nyelvű dokumentumokat támogatja. 

A Health Hosted web API Text Analytics jelenleg csak ezekben a régiókban érhető el: USA 2. nyugati régiója, USA 2. keleti régiója, USA középső régiója, Észak-Európa és Nyugat-Európa.

## <a name="request-access-to-the-public-preview"></a>Hozzáférés kérése a nyilvános előzetes verzióhoz

Töltse ki és küldje el a [Cognitive Services kérelem űrlapját](https://aka.ms/csgate) , hogy hozzáférést kérjen a Text Analytics for Health nyilvános előzetes verziójához. Az állapotfigyelő Text Analyticsért nem számítunk fel díjat. 

Az űrlap adatokat kér Önnek, a vállalatnak és a felhasználói forgatókönyvnek, amelyhez a tárolót fogja használni. Az űrlap elküldése után az Azure Cognitive Services csapata áttekinti és e-mailben értesíti Önt a döntésről.

> [!IMPORTANT]
> * Az űrlapon az Azure-előfizetési AZONOSÍTÓhoz társított e-mail-címet kell használnia.
> * Az Ön által használt Azure-erőforrást a jóváhagyott Azure-előfizetés azonosítójával kell létrehozni. 
> * A Microsofttól származó alkalmazás állapotával kapcsolatos frissítésekért keresse meg az e-mail-címét (a beérkezett fájlok és a levélszemét mappáját is).

## <a name="using-the-docker-container"></a>A Docker-tároló használata 

Ha a saját környezetben szeretné futtatni a Text Analytics az állapotfigyelő tárolóban, kövesse az alábbi [utasításokat a tároló letöltéséhez és telepítéséhez](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Az ügyfélkódtár használata

A Text Analytics ügyféloldali kódtár legújabb előzetes verziója lehetővé teszi, hogy egy ügyfél-objektum használatával meghívja az Text Analyticst az állapothoz. Tekintse át a dokumentációt, és nézze meg a példákat a GitHubon:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>REST API kérelem küldése 

### <a name="preparation"></a>Előkészítés

A Text Analytics for Health magasabb színvonalú eredményt hoz létre, ha kisebb mennyiségű szöveges munkát ad meg. Ez más Text Analytics funkciók, például a legfontosabb mondatok kinyerése, ami jobb a nagyobb blokkokon. Ezeknek a műveleteknek a legjobb eredményeinek eléréséhez érdemes lehet ennek megfelelően átstrukturálni a bemeneteket.

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg és nyelv. 

A dokumentum méretének 5 120 karakternél rövidebbnek kell lennie a dokumentumban. A gyűjteményekben engedélyezett dokumentumok maximális száma a fogalmak szakaszban, az [adatkorlátozásokról](../concepts/data-limits.md?tabs=version-3) szóló cikkben olvasható. A kollekció elküldése a kérelem törzsében történik.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Az API-kérelem strukturálása az üzemeltetett aszinkron webes API-hoz

A tároló és a tárolt webes API esetében is létre kell hoznia egy POST-kérelmet. A [Poster](text-analytics-how-to-call-api.md), a curl-parancs vagy az **API-tesztelési konzol** a [text Analytics a Health Hosted API-referenciájának](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) használatával gyorsan létrehozhatja és elküldheti a post-kérést a kívánt régióban található üzemeltetett webes API-hoz. 

Alább látható egy példa arra, hogy milyen JSON-fájl van csatolva a Text Analytics for Health API-kérelem POST törzséhez:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Üzemeltetett aszinkron webes API-válasz 

Mivel ez a POST-kérelem a feladatok aszinkron művelethez való elküldésére szolgál, nincs szöveg a válasz objektumban.  A válasz fejlécében azonban szüksége lesz a művelet – hely kulcs értékére a GET kérelem végrehajtásához a feladatok és a kimenet állapotának vizsgálatához.  Alább látható egy példa a művelet – hely kulcs értékére a POST kérelem válasz fejlécében:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

A feladatok állapotának megtekintéséhez tegyen egy GET kérelmet az URL-címre a POST Response művelet – hely kulcs fejlécének értékében.  A következő állapotok használhatók a feladatok állapotának megjelenítéséhez:,,,,, `NotStarted` `running` `succeeded` `failed` `rejected` `cancelling` és `cancelled` .  

A `NotStarted` `running` Get kéréssel megegyező URL-cím törlésével megszakíthatja a vagy a vagy az állapotú feladatot.  A TÖRLÉSi hívással kapcsolatos további információk a [Health Hosted API-referenciájának Text Analytics](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob)érhetők el.

Az alábbi példa egy GET kérelem válaszát szemlélteti.  Vegye figyelembe, hogy a kimenet lekérésre kerül, amíg a `expirationDateTime` (24 óra a feladatok létrejöttének idejétől számítva) el lett távolítva a kimenet törlését követően.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>A tároló API-kérelmének szerkezete

A [Poster](text-analytics-how-to-call-api.md) vagy az alábbi példában szereplő curl-kérelem használatával beküldhet egy lekérdezést a telepített tárolóba, a `serverURL` változót pedig a megfelelő értékre cserélve.  Figyelje meg, hogy az API verziója a tároló URL-címében eltér az üzemeltetett API-tól.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

A következő JSON egy példa egy JSON-fájlra, amely a Text Analytics for Health API-kérelem POST törzséhez van csatolva:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Tároló válaszának törzse

A következő JSON-példa az állapot API-Response törzsének Text Analytics a tároló szinkron hívásával:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Tagadási észlelés kimenete

A tagadás észlelésének használatakor bizonyos esetekben egyetlen tagadási időszak egyszerre több kifejezést is felhasználhat. Az elismert entitások elutasítása a JSON-kimenetben a jelző logikai értékével jelenik `isNegated` meg, például:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>A kapcsolatok kinyerésének kimenete

A kapcsolat extrakciós kimenete a kapcsolat *forrására* és *CÉLJÁra* vonatkozó URI-hivatkozásokat tartalmaz. A társított szerepkörrel rendelkező entitások `ENTITY` hozzá vannak rendelve a `target` mezőhöz. A társított szerepkörrel rendelkező entitások `ATTRIBUTE` hozzá vannak rendelve a `source` mezőhöz. A rövidítések kapcsolata kétirányú `source` és `target` mezőket tartalmaz, és a következőre lesz `bidirectional` beállítva: `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Lásd még

* [A Text Analytics áttekintése](../overview.md)
* [Elnevezett entitások kategóriái](../named-entity-types.md)
* [Újdonságok](../whats-new.md)
