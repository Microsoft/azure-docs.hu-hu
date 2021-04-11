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
ms.openlocfilehash: c1f610a5fb3db41091ddc4d0d921bf1a0cd2cf34
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012228"
---
# <a name="call-the-read-api"></a>Az olvasási API meghívása

Ebből az útmutatóból megtudhatja, hogyan hívhatja meg az olvasási API-t a szövegek képekből való kinyeréséhez. Megtudhatja, milyen különböző módokon konfigurálhatja az API viselkedését az igényeinek megfelelően.

Ez az útmutató feltételezi, hogy már <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" létrehozott egy Computer Vision erőforrást "  target="_blank"> Computer Vision erőforrás létrehozása </a> és az előfizetési kulcs és a végpont URL-címének beszerzése. Ha még nem tette meg, [kövesse az első](../quickstarts-sdk/client-library.md) lépéseket.

## <a name="submit-data-to-the-service"></a>Adatküldés a szolgáltatásba

Az olvasási API [olvasási hívása](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) egy képet vagy PDF-dokumentumot használ a bemenet és a szöveg aszinkron módon történő kibontásához.

`https://{endpoint}/vision/v3.2-preview.3/read/analyze[?language][&pages][&readingOrder]`

A hívás visszatérési értéke egy nevű válasz fejléc mező `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó műveleti azonosítót tartalmazza.

|Válasz fejléce| Példaérték |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Számlázás** 
>
> A [Computer Vision díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) oldala tartalmazza a beolvasott árképzési szintet. Minden elemzett rendszerkép vagy lap egy tranzakció. Ha a műveletet a 100 oldalakat tartalmazó PDF-vagy TIFF-dokumentummal hívja meg, akkor az olvasási művelet 100 tranzakcióként fog megjelenni, és az 100-tranzakciókért díjat számítunk fel. Ha 50 hívást kezdeményezett a műveletre, és mindegyik hívás egy 100-oldalas dokumentumot küldött be, akkor az 50 X 100 = 5000 tranzakcióért kell fizetnie.

## <a name="determine-how-to-process-the-data"></a>Az adatfeldolgozás módjának meghatározása

### <a name="language-specification"></a>Nyelvi specifikáció

Az [olvasási](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) hívás opcionális paramétert tartalmaz a nyelvhez. Az olvasás támogatja az automatikus nyelvi azonosítást és a többnyelvű dokumentumokat, ezért csak akkor adjon meg egy nyelvi kódot, ha a dokumentumot adott nyelven szeretné feldolgozni.

### <a name="natural-reading-order-output-latin-languages-only"></a>Természetes olvasási sorrend kimenete (csak latin nyelveken)
Az [olvasási 3,2 előzetes verzió API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)segítségével megadhatja, hogy a szövegsorok milyen sorrendben legyenek kimenetben a `readingOrder` lekérdezési paraméterrel. Az `natural` az alábbi példában látható módon egy emberi felhasználóbarát olvasási sorrendi kimenetre használható. Ez a funkció csak latin nyelveken támogatott.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="OCR-olvasási sorrend – példa":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Válassza ki az oldal (oka) t vagy az oldal tartományait szöveg kinyeréséhez
Ha a [Read 3,2 Preview API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)-t használja nagyméretű többoldalas dokumentumok esetén, a `pages` lekérdezési paraméterrel adhatja meg az oldalszámokat vagy az oldalak tartományait, hogy csak az adott lapról származó szöveget lehessen kinyerni. Az alábbi példa egy 10 oldalas dokumentumot mutat be, amely mindkét esetben kinyert szöveggel rendelkezik – az összes oldalt (1-10) és a kijelölt lapokat (3-6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Kijelölt lapok kimenete":::

## <a name="get-results-from-the-service"></a>Eredmények beolvasása a szolgáltatásból

A második lépés az [olvasási eredmények lekérése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) művelet hívása. Ez a művelet az olvasási művelet által létrehozott műveleti azonosító bemenetét veszi át. 

`https://{endpoint}/vision/v3.2-preview.3/read/analyzeResults/{operationId}`

Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. 

|Érték | Értelmezés |
|:-----|:----|
| `notStarted`| A művelet nem indult el. |
| `running`| A művelet feldolgozása folyamatban van. |
| `failed`| A művelet sikertelen volt. |
| `succeeded`| A művelet sikeresen befejeződött. |

Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A másodpercenkénti kérések (RPS) mértékének meghaladása érdekében használjon 1 – 2 másodperces intervallumot.

> [!NOTE]
> Az ingyenes szintek percenként 20 hívásra korlátozzák a kérelmek sebességét. A fizetős csomag másodpercenként 10 másodpercenkénti kérést (RPS) tesz lehetővé. Az Azure támogatási csatornájának vagy a fiókja csapatának használatával nagyobb kérést igényelhet másodpercenként (RPS).

Ha az **állapot** mező `succeeded` értéke, a JSON-válasz a rendszerképből vagy dokumentumból kinyert szöveges tartalmat tartalmazza. A JSON-válasz a felismert szavak eredeti sorát tárolja. Magában foglalja a kinyert szöveg sorait és a hozzájuk tartozó mezők koordinátáit. Minden szöveges sor tartalmazza az összes kinyert szót a koordinátáikkal és a megbízhatósági pontszámokkal.

> [!NOTE]
> A műveletnek átadott adatok `Read` átmenetileg titkosítva vannak, és a nyugalmi állapotban tárolódnak, majd törölve lesznek. Ez lehetővé teszi, hogy az alkalmazások a szolgáltatás válaszának részeként beolvassák a kinyert szöveget.

### <a name="sample-json-output"></a>Példa JSON-kimenetre

Tekintse meg a sikeres JSON-válasz következő példáját:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
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
Az [olvasási 3,2 előzetes verzió API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) -válasza magában foglalja annak besorolását, hogy az egyes szövegfájlok kézírásos stílussal vagy sem, valamint a megbízhatósági pontszámmal együtt. Ez a funkció csak latin nyelveken támogatott. Az alábbi példa a képen látható szöveg kézírásos besorolását mutatja be.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Példa OCR kézírás-besorolásra":::

## <a name="next-steps"></a>Következő lépések

A REST API kipróbálásához nyissa meg az [OLVASÁSI API-referenciát](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005).