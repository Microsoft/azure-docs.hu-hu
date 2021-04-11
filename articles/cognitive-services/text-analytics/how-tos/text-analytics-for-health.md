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
ms.date: 03/11/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 80a943d235783852f57832363b5af8048f010575
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599432"
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

A kapcsolat kibontása azonosítja a szövegben említett fogalmak közötti értelmes kapcsolatokat. Például a "feltétel időpontjának" viszonyt úgy találja, hogy egy feltétel nevét társítja egy időponthoz, vagy egy rövidítést és a teljes leírást.  

> [!div class="mx-imgBorder"]
> ![Állapot újraindítása](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entitáskapcsolás](#tab/entity-linking)

A disambiguates különböző entitásokat összekapcsoló entitások a szövegben említett, elnevezett entitások társításával vannak társítva egy előre definiált adatbázisban, például az egységes orvosi nyelvi rendszeren (UMLS) található fogalmakhoz. Az orvosi fogalmakat a rendszer a normalizálás további formájaként is hozzárendeli az előnyben részesített elnevezéshez.

> [!div class="mx-imgBorder"]
> ![Állapot EL](../media/ta-for-health/health-entity-linking.png)

A Text Analytics for Health támogatja az egyesített orvosi nyelvi rendszer ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) Metathesaurus tudásbázisban található állapot-és orvosbiológiai szótárakhoz összekapcsolását.

### <a name="assertion-detection"></a>[Kijelentések észlelése](#tab/assertion-detection) 

Az orvosi tartalmak értelmét nagy hatással van a módosítók, például a negatív vagy feltételes állítások, amelyek kritikus fontosságú következményekkel járhatnak. Az állapot-Text Analytics három kategóriába sorolhatók a szövegben található entitások kiállításának észleléséhez: 

* meghatározni
* feltételes
* összefüggés

> [!div class="mx-imgBorder"]
> ![Állapot negatív](../media/ta-for-health/assertions.png)

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

> [!NOTE]
> Az aszinkron `/analyze` és a `/health` végpontok csak a következő régiókban érhetők el: USA 2. nyugati régiója, USA 2. keleti régiója, USA középső régiója, Észak-Európa és Nyugat-Európa.  Ha a végpontok számára sikeres kérelmeket szeretne tenni, győződjön meg arról, hogy az erőforrás a következő régiókban lett létrehozva.

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

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/health/jobs/<jobID>`

A feladatok állapotának megtekintéséhez tegyen egy GET kérelmet az URL-címre a POST Response művelet – hely kulcs fejlécének értékében.  A következő állapotok használhatók a feladatok állapotának megjelenítéséhez:,,,,, `NotStarted` `running` `succeeded` `failed` `rejected` `cancelling` és `cancelled` .  

A `NotStarted` `running` Get kéréssel megegyező URL-cím törlésével megszakíthatja a vagy a vagy az állapotú feladatot.  A TÖRLÉSi hívással kapcsolatos további információk a [Health Hosted API-referenciájának Text Analytics](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob)érhetők el.

Az alábbi példa egy GET kérelem válaszát szemlélteti.  A kimenet az `expirationDateTime` (a feladatok létrehozásának idejétől számított 24 órán belül) lekérésre elérhető, amely után a kimenet törölve lett.

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-03-11T05:43:37Z",
    "createdDateTime": "2021-03-11T05:42:32Z",
    "expirationDateTime": "2021-03-12T05:42:32Z",
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
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.94
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-03-01"
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
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>Érvényesítési kimenet

A Text Analytics for Health visszaadja az állítási módosítókat, amelyek olyan orvosi fogalmakhoz rendelt tájékoztató attribútumok, amelyek részletesebben megismerik a szövegben található fogalmak kontextusát. Ezek a módosító három kategóriára oszlanak, amelyek mindegyike egy másik aspektusra koncentrál, és a kölcsönösen kizáró értékeket tartalmazza. Kategóriánként csak egy érték van hozzárendelve az egyes entitásokhoz. Az egyes kategóriák leggyakoribb értéke az alapértelmezett érték. A szolgáltatás kimeneti válasza csak az alapértelmezett értéktől eltérő állítási módosítókat tartalmaz.

**Bizonyosság**  – információt nyújt a fogalom jelenlétéről (a jelen és a hiányzó helyről), valamint arról, hogy a szöveg hogyan vonatkozik a jelenlétére (a végleges és a lehetséges).
*   **Pozitív** [alapértelmezett]: a fogalom létezik vagy történt.
* **Negatív**: a fogalom jelenleg nem létezik, vagy soha nem történt meg.
* **Positive_Possible**: a koncepció valószínűleg létezik, de némi bizonytalanság van.
* **Negative_Possible**: a koncepció létezése nem valószínű, de némi bizonytalanság van.
* **Neutral_Possible**: lehetséges, hogy a fogalom nem létezik, vagy nem áll fenn az egyik oldalon.

**Feltételesség** – információt nyújt arról, hogy egy adott fogalom létezése bizonyos feltételektől függ-e. 
*   **Nincs** [alapértelmezett]: a koncepció tény és nem hipotetikus, és nem függ bizonyos feltételektől.
*   **Feltételezett**: a fogalom a jövőben is kialakulhat vagy bekövetkezik.
*   **Feltételes**: a fogalom létezik, vagy csak bizonyos feltételek esetén fordul elő.

**Társítás** – azt ismerteti, hogy a fogalom társítva van-e a szöveg vagy valaki más tárgyához.
*   **Tárgy** [alapértelmezett]: a fogalom a szöveg, általában a beteg tárgyához van társítva.
*   **Someone_Else**: a fogalom olyan személyhez van társítva, aki nem a szöveg tárgya.


Az állítások észlelése a kimondott entitásokat negatív értékként jelöli meg a biztonsági kategóriánál, például:

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty&quot;: &quot;negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id&quot;: &quot;C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id&quot;: &quot;0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>A kapcsolatok kinyerésének kimenete

A Text Analytics állapota felismeri a különböző fogalmak közötti kapcsolatokat, beleértve az attribútumok és az entitások közötti kapcsolatokat (például a törzs szerkezetének irányát, a gyógyszer adagolását) és az entitások közötti kapcsolatot (például a rövidítések észlelése).

**RÖVIDÍTÉSE**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

> [!NOTE]
> * A feltételre hivatkozó kapcsolatok a diagnosztikai entitás vagy a SYMPTOM_OR_SIGN entitás típusára vonatkozhatnak.
> * A GYÓGYSZERes KEZELÉSre hivatkozó kapcsolatok a MEDICATION_NAME vagy a MEDICATION_CLASS entitás típusára vonatkozhatnak.
> * Az időpontra hivatkozó kapcsolatok az időentitás vagy a dátum típusú entitás típusára vonatkozhatnak.

A kapcsolatok kinyerési kimenete URI-referenciákat és a kapcsolattípus entitásai által hozzárendelt szerepköröket tartalmaz. Például:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>Lásd még

* [A Text Analytics áttekintése](../overview.md)
* [Elnevezett entitások kategóriái](../named-entity-types.md)
* [Újdonságok](../whats-new.md)
