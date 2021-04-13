---
title: Az olvasási API meghívása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hívhatja meg az olvasási API-t, és részletesen konfigurálhatja a viselkedését.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: 8e0ef789653181d744100ef6e179bcf328f6d704
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308625"
---
# <a name="call-the-read-api"></a>Az olvasási API meghívása

Ebből az útmutatóból megtudhatja, hogyan hívhatja meg az olvasási API-t a szövegek képekből való kinyeréséhez. Megtudhatja, milyen különböző módokon konfigurálhatja az API viselkedését az igényeinek megfelelően.

Ez az útmutató feltételezi, hogy már <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" létrehozott egy Computer Vision erőforrást "  target="_blank"> Computer Vision erőforrás létrehozása </a> és az előfizetési kulcs és a végpont URL-címének beszerzése. Ha még nem tette meg, [kövesse az első](../quickstarts-sdk/client-library.md) lépéseket.

## <a name="submit-data-to-the-service"></a>Adatküldés a szolgáltatásba

Az olvasási API [olvasási hívása](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) egy képet vagy PDF-dokumentumot használ a bemenet és a szöveg aszinkron módon történő kibontásához.

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

A hívás visszatérési értéke egy nevű válasz fejléc mező `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó műveleti azonosítót tartalmazza.

|Válasz fejléce| Példaérték |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Számlázás** 
>
> A [Computer Vision díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) oldala tartalmazza a beolvasott árképzési szintet. Minden elemzett rendszerkép vagy lap egy tranzakció. Ha a műveletet a 100 oldalakat tartalmazó PDF-vagy TIFF-dokumentummal hívja meg, akkor az olvasási művelet 100 tranzakcióként fog megjelenni, és az 100-tranzakciókért díjat számítunk fel. Ha 50 hívást kezdeményezett a műveletre, és mindegyik hívás egy 100-oldalas dokumentumot küldött be, akkor az 50 X 100 = 5000 tranzakcióért kell fizetnie.

## <a name="determine-how-to-process-the-data"></a>Az adatfeldolgozás módjának meghatározása

### <a name="language-specification"></a>Nyelvi specifikáció

Az [olvasási](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) hívás opcionális paramétert tartalmaz a nyelvhez. Az olvasás támogatja az automatikus nyelvi azonosítást és a többnyelvű dokumentumokat, ezért csak akkor adjon meg egy nyelvi kódot, ha a dokumentumot adott nyelven szeretné feldolgozni.

### <a name="natural-reading-order-output-latin-languages-only"></a>Természetes olvasási sorrend kimenete (csak latin nyelveken)
Adja meg azt a sorrendet, amelyben a szövegsorok kimenete a `readingOrder` lekérdezési paraméterrel történik. Az `natural` az alábbi példában látható módon egy emberi felhasználóbarát olvasási sorrendi kimenetre használható. Ez a funkció csak latin nyelveken támogatott.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="OCR-olvasási sorrend – példa":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Válassza ki az oldal (oka) t vagy az oldal tartományait szöveg kinyeréséhez
Nagyméretű többoldalas dokumentumok esetén a `pages` lekérdezési paraméterrel adhatja meg az oldalszámokat vagy az oldalak tartományait, hogy csak az adott lapról származó szöveget lehessen kinyerni. Az alábbi példa egy 10 oldalas dokumentumot mutat be, amely mindkét esetben kinyert szöveggel rendelkezik – az összes oldalt (1-10) és a kijelölt lapokat (3-6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Kijelölt lapok kimenete":::

## <a name="get-results-from-the-service"></a>Eredmények beolvasása a szolgáltatásból

A második lépés az [olvasási eredmények lekérése](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750) művelet hívása. Ez a művelet az olvasási művelet által létrehozott műveleti azonosító bemenetét veszi át. 

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. 

|Érték | Értelmezés |
|:-----|:----|
| `notStarted`| A művelet nem indult el. |
| `running`| A művelet feldolgozása folyamatban van. |
| `failed`| A művelet sikertelen volt. |
| `succeeded`| A művelet sikeresen befejeződött. |

Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A másodpercenkénti kérések (RPS) mértékének meghaladása érdekében használjon 1 – 2 másodperces intervallumot.

> [!NOTE]
> Az ingyenes szintek percenként 20 hívásra korlátozzák a kérelmek sebességét. A fizetős csomag másodpercenként 10 másodpercenkénti kérést (RPS) tesz lehetővé. Vegye figyelembe az Azure-erőforrások identfier és régióját, és nyisson meg egy Azure-támogatási jegyet, vagy lépjen kapcsolatba a fiók csapatával, és igényeljen másodpercenkénti kérést (RPS).

Ha az **állapot** mező `succeeded` értéke, a JSON-válasz a rendszerképből vagy dokumentumból kinyert szöveges tartalmat tartalmazza. A JSON-válasz a felismert szavak eredeti sorát tárolja. Magában foglalja a kinyert szöveg sorait és a hozzájuk tartozó mezők koordinátáit. Minden szöveges sor tartalmazza az összes kinyert szót a koordinátáikkal és a megbízhatósági pontszámokkal.

> [!NOTE]
> A műveletnek átadott adatok `Read` átmenetileg titkosítva vannak, és a nyugalmi állapotban tárolódnak, majd törölve lesznek. Ez lehetővé teszi, hogy az alkalmazások a szolgáltatás válaszának részeként beolvassák a kinyert szöveget.

### <a name="sample-json-output"></a>Példa JSON-kimenetre

Tekintse meg a sikeres JSON-válasz következő példáját:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Szövegsorok kézírásos besorolása (csak latin nyelv esetén)
A válasz magában foglalja annak besorolását, hogy az egyes szövegfájlok kézírásos stílusúak-e, valamint a megbízhatósági pontszám mellett. Ez a funkció csak latin nyelveken támogatott. Az alábbi példa a képen látható szöveg kézírásos besorolását mutatja be.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Példa OCR kézírás-besorolásra":::

## <a name="next-steps"></a>Következő lépések

A REST API kipróbálásához nyissa meg az [OLVASÁSI API-referenciát](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
