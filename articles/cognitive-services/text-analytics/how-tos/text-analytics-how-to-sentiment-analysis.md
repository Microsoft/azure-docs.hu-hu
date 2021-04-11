---
title: A Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan derítheti fel az érzelmeket, és hogyan teheti a véleményeket szövegben az Azure Cognitive Services Text Analytics API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: 7cd2b0a6b943ceb32420ef119a7fc5eddefa2e19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276994"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>Útmutató: az érzelmek elemzése és a vélemények bányászata

A Text Analytics API Hangulatelemzés szolgáltatása két módszert biztosít a pozitív és negatív érzelmek észlelésére. Ha Hangulatelemzés kérelmet küld, az API az érzelmeket tartalmazó címkéket (például "negatív", "semleges" és "pozitív") és megbízhatósági pontszámokat ad vissza a mondat és a dokumentum szintjén. A Hangulatelemzés végponton keresztül is küldhet vélemény-adatbányászati kérelmeket, amely részletes információkat tartalmaz a szavakhoz (például a termékek vagy szolgáltatások attribútumaihoz) kapcsolódó véleményekről a szövegben.

Az API által használt AI-modelleket a szolgáltatás nyújtja, csak tartalmat kell elküldeni az elemzéshez.

## <a name="sentiment-analysis-versions-and-features"></a>Verziók és szolgáltatások Hangulatelemzés

| Szolgáltatás                                   | Hangulatelemzés v3 | Hangulatelemzés v 3.1 (előzetes verzió) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Egyszeri és batch-kérelmek módszerei    | X                     | X                                 |
| Pontszámok és címkézés Hangulatelemzés             | X                     | X                                 |
| Linux-alapú [Docker-tároló](text-analytics-how-to-install-containers.md) | X  |  |
| Vélemény bányászata                            |                       | X                                 |

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hangulatelemzés a 3. x verzióban az érzelmek feliratait szövegre alkalmazza, amelyek a mondatok és a dokumentumok szintjén lesznek visszaadva, és mindegyiknek megbízhatósági pontszáma van. 

A feliratok *pozitívak*, *negatívok* és *semlegesek*. A dokumentum szintjén a *vegyes* hangulat felirat is visszaadható. A dokumentum hangulatát a következők határozzák meg:

| Mondat hangulata                                                                            | Visszaadott dokumentum címkéje |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Legalább egy `positive` mondat szerepel a dokumentumban. A mondatok további részei `neutral` . | `positive`              |
| Legalább egy `negative` mondat szerepel a dokumentumban. A mondatok további részei `neutral` . | `negative`              |
| Legalább egy `negative` mondat és legalább egy `positive` mondat szerepel a dokumentumban.    | `mixed`                 |
| A dokumentumban szereplő összes mondat `neutral` .                                                  | `neutral`               |

A megbízhatósági pontszámok 1-től 0-ig terjedhetnek. Az 1-nél közelebbi pontszámok nagyobb megbízhatóságot jeleznek a címke besorolásában, míg az alacsonyabb pontszámok alacsonyabb megbízhatóságot jeleznek. Minden egyes dokumentumhoz vagy mondathoz a címkékhez kapcsolódó előre jelzett pontszámok (pozitív, negatív és semleges) legfeljebb 1 adható hozzá. További információ: [text Analytics átláthatósági Megjegyzés](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="opinion-mining"></a>Vélemény bányászata

A kivonás a Hangulatelemzés egyik funkciója, amely az 3,1-es verzió előzetes verziójától kezdődően érhető el. Ez a funkció a természetes nyelvi feldolgozás (NLP) aspektus-alapú Hangulatelemzés is ismert, és részletesebb információkat nyújt a termékek vagy szolgáltatások attribútumaival kapcsolatos véleményekről. Az API-felületek célja (főnév vagy művelet) és értékelés (melléknév).

Ha például egy ügyfél visszajelzést küld egy olyan szállodáról, mint például "a szoba nagyszerű volt, de a személyzet nem volt barátja.", a kisegítő adatbányászat megkeresi a célkitűzéseket (aspektusokat) a szövegben, valamint a hozzájuk kapcsolódó értékeléseket (véleményeket) és a véleményét. Hangulatelemzés lehet, hogy csak negatív véleményét jelentse.

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="A vélemény bányászati példájának ábrája" lightbox="../media/how-tos/opinion-mining.png":::

Ha szeretné kivonni a kiértékelést az eredmények között, a megjelölést is fel kell vennie az `opinionMining=true` érzelmek elemzésére vonatkozó kérelembe. A vélemény adatbányászati eredményei szerepelni fognak az érzelmi elemzésre adott válaszban. A vélemény bányászata Hangulatelemzés kiterjesztése, és a jelenlegi [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)szinten szerepel.


## <a name="sending-a-rest-api-request"></a>REST API kérelem küldése 

### <a name="preparation"></a>Előkészítés

Az érzelmek elemzése nagyobb minőségi eredményt eredményez, ha kisebb mennyiségű szöveget ad meg a munkához. Ez a ellentétes a kulcsszókereséssel, amely nagyobb mennyiségű szöveg esetén teljesít jobban. Annak érdekében, hogy mindkét művelet a legjobb eredményt hozza, érdemes lehet ennek megfelelően átszervezni a bemeneteket.

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg és nyelv. A Hangulatelemzés számos különböző nyelvet támogat, több előzetes verzióban. További információt a [támogatott nyelvek](../language-support.md) ismertetésében talál.

A dokumentum méretének 5 120 karakternél rövidebbnek kell lennie a dokumentumban. A gyűjteményekben engedélyezett dokumentumok maximális száma a fogalmak szakaszban, az [adatkorlátozásokról](../concepts/data-limits.md?tabs=version-3) szóló cikkben olvasható. A kollekció elküldése a kérelem törzsében történik.

## <a name="structure-the-request"></a>A kérelem szerkezete

Hozzon létre egy POST kérést. A [Poster](text-analytics-how-to-call-api.md) vagy az API- **tesztelési konzolt** a következő hivatkozásokra kattintva gyorsan strukturálhatja és küldheti el. 

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

[Hangulatelemzés v 3.1 – dokumentáció](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

[Hangulatelemzés v3 – dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Kérelmek végpontjai

Állítsa be a HTTPS-végpontot az érzelmek elemzéséhez az Azure-ban vagy egy példányban létrehozott [text Analytics tárolóban](text-analytics-how-to-install-containers.md)Text Analytics erőforrás használatával. Meg kell adnia a használni kívánt verzió helyes URL-címét. Például:

> [!NOTE]
> A Text Analytics erőforráshoz tartozó kulcsot és végpontot a Azure Portalban találja. Ezek az erőforrás **gyors üzembe helyezés** lapján, az **Erőforrás-kezelés** területen találhatók. 

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

**Hangulatelemzés**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment`

**Vélemény bányászata**

A vélemény adatbányászati eredményeinek beszerzéséhez meg kell adnia a `opinionMining=true` paramétert. Például:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment?opinionMining=true`

Ez a paraméter alapértelmezés szerint be van állítva `false` . 

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

**Hangulatelemzés**

A v 3.0-s verzióban az egyetlen elérhető végpont a Hangulatelemzés.
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

A Text Analytics API kulcsot tartalmazó kérelem fejlécének beállítása. A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>Példa a Hangulatelemzésra és a közvélemény-bányászatra vonatkozó kérelemre  

A következő egy példa hangulatelemzésre beküldhető tartalomra. A kérelem formátuma ugyanaz, mint a `v3.0` és a `v3.1-preview` .
    
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

A kimenetet visszaadása azonnali. Az eredményeket egy olyan alkalmazásba is továbbíthatja, amely fogadja a JSON-t, vagy mentse a kimenetet egy fájlba a helyi rendszeren. Ezután importálja a kimenetet egy olyan alkalmazásba, amelyet az adatrendezéshez, kereséshez és kezeléshez használhat. A többnyelvű és a Emoji-támogatás miatt a válasz szöveges eltolásokat is tartalmazhat. További információkért lásd: [eltolások feldolgozása](../concepts/text-offsets.md) .

#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>Hangulatelemzés és vélemény bányászati példája

> [!IMPORTANT]
> Az alábbi JSON-példa a Hangulatelemzés, az API v 3.1-es verziójában felkínált vélemény kitermelésének használatára szolgál. Ha nem kéri a vélemények kitermelését, az API-válasz a 3,0-es **verzióval** megegyező lesz.  

A Hangulatelemzés v 3.1 a Hangulatelemzés és a közvélemény számára is visszatérhet a válasz objektumokra.
  
Az érzelmek elemzése a teljes dokumentumra vonatkozó hangulati címkét és megbízhatósági pontszámot ad vissza, valamint minden mondatot. Az 1-nél közelebbi pontszámok nagyobb megbízhatóságot jeleznek a címke besorolásában, míg az alacsonyabb pontszámok alacsonyabb megbízhatóságot jeleznek. A dokumentumok több mondattal is rendelkezhetnek, és az egyes dokumentumokban vagy mondatokban lévő megbízhatósági pontszámok akár 1-re is felvehetők.

A kisegítő adatbányászat a szövegben, valamint a hozzájuk kapcsolódó felmérésben (melléknév) keres célokat. Az alábbi válaszban azt a mondatot, amelyben *az étterem nagy élelmiszereket tartalmaz, és a pincérnek barátságos* két célja van: az *élelmiszer* és a *pincér*. Minden cél `relations` tulajdonsága tartalmaz egy `ref` értéket a társított `documents` , `sentences` és objektumokra mutató URI-hivatkozással `assessments` .

Az API a véleményeket cél (főnév vagy művelet) és értékelés (melléknév) alapján adja vissza.

```json
{
  "documents": [
    {
      "id": "1",
      "sentiment": "positive",
      "confidenceScores": {
        "positive": 1,
        "neutral": 0,
        "negative": 0
      },
      "sentences": [
        {
          "sentiment": "positive",
          "confidenceScores": {
            "positive": 1,
            "neutral": 0,
            "negative": 0
          },
          "offset": 0,
          "length": 58,
          "text": "The restaurant had great food and our waiter was friendly.",
          "targets": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 25,
              "length": 4,
              "text": "food",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/0"
                }
              ]
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 38,
              "length": 6,
              "text": "waiter",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/1"
                }
              ]
            }
          ],
          "assessments": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 19,
              "length": 5,
              "text": "great",
              "isNegated": false
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
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

### <a name="sentiment-analysis-example-response"></a>Példa Hangulatelemzés válaszra

Az érzelmek elemzése a teljes dokumentumra vonatkozó hangulati címkét és megbízhatósági pontszámot ad vissza, valamint minden mondatot. Az 1-nél közelebbi pontszámok nagyobb megbízhatóságot jeleznek a címke besorolásában, míg az alacsonyabb pontszámok alacsonyabb megbízhatóságot jeleznek. A dokumentumok több mondattal is rendelkezhetnek, és az egyes dokumentumokban vagy mondatokban lévő megbízhatósági pontszámok akár 1-re is felvehetők.

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

## <a name="summary"></a>Összefoglalás

Ebben a cikkben a Text Analytics API segítségével megtanulta az érzelmek elemzéséhez szükséges fogalmakat és munkafolyamatokat. Összegezve:

+ A Hangulatelemzés és a vélemények kihasználása a kiválasztott nyelvekhez érhető el.
+ A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
+ A POST kérelem a `/sentiment` végponthoz egy személyre szabott [hozzáférési kulccsal és egy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , az előfizetéséhez érvényes végpontot használ.
+ `opinionMining=true`Az érző Analysis-kérelmekben a vélemény-adatbányászat eredményeinek beszerzéséhez.
+ A válasz kimenete, amely az egyes dokumentumok AZONOSÍTÓinak hangulati pontszámát tartalmazza, továbbítható bármely olyan alkalmazásnak, amely elfogadja a JSON-t. Például: Excel és Power BI.

## <a name="see-also"></a>Lásd még

* [A Text Analytics áttekintése](../overview.md)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/client-libraries-rest-api.md)
* [Újdonságok](../whats-new.md)
