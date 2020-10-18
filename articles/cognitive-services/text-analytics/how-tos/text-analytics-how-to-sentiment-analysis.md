---
title: Text Analytics REST APIekkel kapcsolatos érzelmek elemzése
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan derítheti fel a szövegbeli érzelmeket az Azure Cognitive Services Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 10/16/2020
ms.author: aahi
ms.openlocfilehash: 2b13ac02a8a276b2ef46c8ffd5f13846e394d938
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166930"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Útmutató: érzelmek észlelése a Text Analytics API használatával

A Text Analytics API Hangulatelemzés funkciója kiértékeli a szöveget, és az egyes mondatokhoz tartozó érzelmi pontszámokat és címkéket adja vissza. Ez hasznos lehet a közösségi média, az ügyfelek és a vitafórumok pozitív és negatív észlelésére. Az API által használt AI-modelleket a szolgáltatás nyújtja, csak tartalmat kell elküldeni az elemzéshez.

A hangulati elemzési kérelem elküldése után az API az érzelmeket tartalmazó címkéket (például "negatív", "semleges" és "pozitív") és a megbízhatósági pontszámokat adja vissza a mondat és a dokumentum szintjén.

A Hangulatelemzés számos különböző nyelvet támogat, több előzetes verzióban. További információt a [támogatott nyelvek](../text-analytics-supported-languages.md) ismertetésében talál.

## <a name="sentiment-analysis-versions-and-features"></a>Verziók és szolgáltatások Hangulatelemzés

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Szolgáltatás                                   | Hangulatelemzés v3 | Hangulatelemzés v 3.1 (előzetes verzió) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Egyszeri és batch-kérelmek módszerei    | X                     | X                                 |
| Érzelmek pontszámai és címkézése             | X                     | X                                 |
| Linux-alapú [Docker-tároló](text-analytics-how-to-install-containers.md) | X  |  |
| Vélemény bányászata                            |                       | X                                 |

## <a name="sentiment-scoring-and-labeling"></a>Érzelmek pontozása és címkézése

A (z) v3 Hangulatelemzés a szövegre, a mondatok és a dokumentumok szintjén visszaadott szövegre vonatkozik, és mindegyikhez megbízhatósági pontszám szükséges. 

A feliratok *pozitívak*, *negatívok*és *semlegesek*. A dokumentum szintjén a *vegyes* hangulat felirat is visszaadható. A dokumentum hangulatát a következők határozzák meg:

| Mondat hangulata                                                                            | Visszaadott dokumentum címkéje |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Legalább egy `positive` mondat szerepel a dokumentumban. A mondatok további részei `neutral` . | `positive`              |
| Legalább egy `negative` mondat szerepel a dokumentumban. A mondatok további részei `neutral` . | `negative`              |
| Legalább egy `negative` mondat és legalább egy `positive` mondat szerepel a dokumentumban.    | `mixed`                 |
| A dokumentumban szereplő összes mondat `neutral` .                                                  | `neutral`               |

A megbízhatósági pontszámok 1-től 0-ig terjedhetnek. Az 1-nél közelebbi pontszámok nagyobb megbízhatóságot jeleznek a címke besorolásában, míg az alacsonyabb pontszámok alacsonyabb megbízhatóságot jeleznek. Minden egyes dokumentumhoz vagy mondathoz a címkékhez kapcsolódó előre jelzett pontszámok (pozitív, negatív és semleges) legfeljebb 1 adható hozzá.

## <a name="opinion-mining"></a>Vélemény bányászata

A kivonás a Hangulatelemzés egyik funkciója, amely a 3,1-es verziótól kezdődően érhető el. 1. Ez a funkció a természetes nyelvi feldolgozásban (például a termékek vagy szolgáltatások attribútumaiban) lévő véleményekkel kapcsolatos részletesebb információkat tartalmaz a szövegben található, aspektusokon alapuló Hangulatelemzésként is.

Ha például egy ügyfél visszajelzést küld egy olyan szállodáról, mint például "a szoba nagyszerű volt, de a személyzet nem volt barátja", a kisegítő adatbányászat megkeresi a szövegben szereplő szempontokat, valamint a hozzájuk kapcsolódó véleményeket és érzelmeket:

| Szempont | Véleményét    | Hangulat |
|--------|------------|-----------|
| szoba   | nagyszerű      | pozitív  |
| személyzet  | nemkívánatos | negatív  |

Ha szeretné kivonni a kiértékelést az eredmények között, a megjelölést is fel kell vennie az `opinionMining=true` érzelmek elemzésére vonatkozó kérelembe. A vélemény adatbányászati eredményei szerepelni fognak az érzelmi elemzésre adott válaszban.

## <a name="sending-a-rest-api-request"></a>REST API kérelem küldése 

### <a name="preparation"></a>Előkészítés

Az érzelmek elemzése nagyobb minőségi eredményt eredményez, ha kisebb mennyiségű szöveget ad meg a munkához. Ez a ellentétes a kulcsszókereséssel, amely nagyobb mennyiségű szöveg esetén teljesít jobban. Annak érdekében, hogy mindkét művelet a legjobb eredményt hozza, érdemes lehet ennek megfelelően átszervezni a bemeneteket.

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg és nyelv.

A dokumentum méretének 5 120 karakternél rövidebbnek kell lennie a dokumentumban. Egy gyűjteményhez legfeljebb 1 000 elem (azonosító) tartozhat. A kollekció elküldése a kérelem törzsében történik.

## <a name="structure-the-request"></a>A kérelem szerkezete

Hozzon létre egy POST kérést. A [Poster](text-analytics-how-to-call-api.md) vagy az API- **tesztelési konzolt** a következő hivatkozásokra kattintva gyorsan strukturálhatja és küldheti el. 

#### <a name="version-31-preview1"></a>[3,1-es verzió – előzetes verzió. 1](#tab/version-3-1)

[Hangulatelemzés v 3.1 – dokumentáció](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

[Hangulatelemzés v3 – dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Kérelmek végpontjai

Állítsa be a HTTPS-végpontot az érzelmek elemzéséhez az Azure-ban vagy egy példányban létrehozott [text Analytics tárolóban](text-analytics-how-to-install-containers.md)Text Analytics erőforrás használatával. Meg kell adnia a használni kívánt verzió helyes URL-címét. Példa:

> [!NOTE]
> Az Azure Portalon megtalálhatja Text Analytics erőforrásának kulcsát és végpontját. Ezek az erőforrás **gyors üzembe helyezés** lapján, az **Erőforrás-kezelés**területen találhatók. 

#### <a name="version-31-preview1"></a>[3,1-es verzió – előzetes verzió. 1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

A vélemény adatbányászati eredményeinek beszerzéséhez meg kell adnia a `opinionMining=true` paramétert. Példa:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

Ez a paraméter alapértelmezés szerint be van állítva `false` . 

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

A Text Analytics API kulcsot tartalmazó kérelem fejlécének beállítása. A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

### <a name="example-sentiment-analysis-request"></a>Példa Hangulatelemzés kérelemre 

A következő egy példa hangulatelemzésre beküldhető tartalomra. A kérelem formátuma megegyezik mindkét verzió esetében.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>A kérelem közzététele

Az elemzés a kérelem megkapásakor történik meg. További információ a másodpercenként elküldhető kérelmek méretéről és számáról: az [adatkorlátozások](../overview.md#data-limits) szakasz az áttekintésben.

A Text Analytics API állapot nélküli. A fiókjában nem tárolunk semmilyen adatmennyiséget, és az eredményeket azonnal visszaadja a válaszban.


### <a name="view-the-results"></a>Az eredmények megtekintése

Az érzelmek elemzése a teljes dokumentumra vonatkozó hangulati címkét és megbízhatósági pontszámot ad vissza, valamint minden mondatot. Az 1-nél közelebbi pontszámok nagyobb megbízhatóságot jeleznek a címke besorolásában, míg az alacsonyabb pontszámok alacsonyabb megbízhatóságot jeleznek. A dokumentumok több mondattal is rendelkezhetnek, és az egyes dokumentumokban vagy mondatokban lévő megbízhatósági pontszámok akár 1-re is felvehetők.

A kimenetet visszaadása azonnali. Az eredményeket egy olyan alkalmazásba is továbbíthatja, amely fogadja a JSON-t, vagy mentse a kimenetet egy fájlba a helyi rendszeren. Ezután importálja a kimenetet egy olyan alkalmazásba, amelyet az adatrendezéshez, kereséshez és kezeléshez használhat. A többnyelvű és a Emoji-támogatás miatt a válasz szöveges eltolásokat is tartalmazhat. További információkért lásd: [eltolások feldolgozása](../concepts/text-offsets.md) .

#### <a name="version-31-preview1"></a>[3,1-es verzió – előzetes verzió. 1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>Példa Hangulatelemzés v 3.1-re – válasz

A Hangulatelemzés v 3.1 az 3,0-es **verzióban** található Response objektumon kívül a vélemények kibővítését is biztosítja. Az alábbi válaszban az *étterem nagy élelmiszerekkel rendelkezett, és a várt* két aspektusa van: az *élelmiszer* és a *pincér*. Az egyes aspektusok `relations` tulajdonsága egy értéket tartalmaz, `ref` amelynek URI-hivatkozása a társított `documents` , `sentences` és `opinions` objektumokra vonatkozik.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>Hangulatelemzés v 3.0-s példa válasz

Hangulatelemzés v3 válaszai az egyes elemzett mondatokra és dokumentumokra vonatkozó véleményeket és pontszámokat tartalmaznak.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
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

## <a name="summary"></a>Összegzés

Ebben a cikkben a Text Analytics API segítségével megtanulta az érzelmek elemzéséhez szükséges fogalmakat és munkafolyamatokat. Összegezve:

+ A Hangulatelemzés a kiválasztott nyelvekhez érhető el.
+ A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
+ A POST kérelem a `/sentiment` végponthoz egy személyre szabott [hozzáférési kulccsal és egy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , az előfizetéséhez érvényes végpontot használ.
+ A válasz kimenete, amely az egyes dokumentumok AZONOSÍTÓinak hangulati pontszámát tartalmazza, továbbítható bármely olyan alkalmazásnak, amely elfogadja a JSON-t. Például: Excel és Power BI.

## <a name="see-also"></a>Lásd még

* [A Text Analytics áttekintése](../overview.md)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/text-analytics-sdk.md)
* [Újdonságok](../whats-new.md)
