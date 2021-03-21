---
title: Optikai karakterfelismerés (OCR) – Computer Vision
titleSuffix: Azure Cognitive Services
description: A képek és dokumentumok optikai karakterfelismeréssel (OCR) kapcsolatos fogalmak a kinyomtatott és kézzel írott szöveggel a Computer Vision API használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e0247560afa8229f4fa5c25ec7dfbbca4f7defb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486098"
---
# <a name="optical-character-recognition-ocr"></a>Optikai karakterfelismerés (OCR)

Az Azure Computer Vision API olyan optikai karakterfelismerési (OCR) képességeket tartalmaz, amelyek kinyerik a képekből nyomtatott vagy kézírásos szöveget. Kinyerheti a képeket a képekből, például a licenccel rendelkező, a sorozatszámokkal, a dokumentumokból, a számlákból, a pénzügyi jelentésekről, a cikkekről és egyéb cikkekből származó képekből is.

## <a name="read-api"></a>API olvasása 

A Computer Vision [READ API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) az Azure legújabb OCR-technológiája (Ismerje meg az[újdonságokat](./whats-new.md)), amelyek kinyerik a nyomtatott szöveget (több nyelven), a kézírásos szövegeket (csak angol nyelven), számjegyeket és pénznem szimbólumokat a képekből és a többoldalas PDF-dokumentumokból. A szolgáltatás úgy van optimalizálva, hogy szövegből származó képekből és többoldalas PDF-dokumentumokból kinyerje a kevert nyelveket. Támogatja mind a nyomtatott, mind a kézírásos szöveg észlelését ugyanabban a képen vagy dokumentumban.

![Hogyan alakítja át az OCR a képeket és a dokumentumokat strukturált kimenetre a kinyert szöveggel](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Bemeneti követelmények
Az **olvasási** hívás a képeket és a dokumentumokat veszi fel bemenetként. A követelmények a következők:

* Támogatott fájlformátumok: JPEG, PNG, BMP, PDF és TIFF
* PDF-és TIFF-fájlok esetén akár 2000-oldalas (csak az ingyenes szinthez tartozó első két oldal) lesz feldolgozva.
* A fájlméretnek kevesebbnek kell lennie, mint 50 MB (4 MB az ingyenes szinten), és legalább 50 x 50 képpont és legfeljebb 10000 x 10000 képpont méretűnek kell lennie. 
* A PDF-méreteknek legfeljebb 17 x 17 hüvelyknek kell lenniük, amely a jogi vagy az A3-as papírméretnek felel meg, és kisebb.

> [!NOTE]
> **Nyelvi bevitel** 
>
> Az [olvasási hívás](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) opcionális paramétert tartalmaz a nyelvhez. Az olvasás támogatja az automatikus nyelvi azonosítást és a többnyelvű dokumentumokat, ezért csak akkor adjon meg egy nyelvi kódot, ha a dokumentumot adott nyelven szeretné feldolgozni.

## <a name="ocr-demo-examples"></a>OCR-bemutató (példák)

![OCR-bemutatók](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>1. lépés: az olvasási művelet

Az olvasási API [olvasási hívása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) egy képet vagy PDF-dokumentumot használ a bemenet és a szöveg aszinkron módon történő kibontásához. A hívás visszatérési értéke egy nevű válasz fejléc mező `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó műveleti azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Számlázás** 
>
> A [Computer Vision díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) oldala tartalmazza a beolvasott árképzési szintet. Minden elemzett rendszerkép vagy lap egy tranzakció. Ha a műveletet a 100 oldalakat tartalmazó PDF-vagy TIFF-dokumentummal hívja meg, akkor az olvasási művelet 100 tranzakcióként fog megjelenni, és az 100-tranzakciókért díjat számítunk fel. Ha 50 hívást kezdeményezett a műveletre, és mindegyik hívás egy 100-oldalas dokumentumot küldött be, akkor az 50 X 100 = 5000 tranzakcióért kell fizetnie.

## <a name="step-2-the-get-read-results-operation"></a>2. lépés: az olvasási eredmények beolvasása művelet

A második lépés az [olvasási eredmények lekérése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) művelet hívása. Ez a művelet az olvasási művelet által létrehozott műveleti azonosító bemenetét veszi át. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A másodpercenkénti kérések (RPS) mértékének meghaladása érdekében használjon 1 – 2 másodperces intervallumot.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: a művelet nem indult el. |
| |  | fut: a művelet feldolgozása folyamatban van. |
| |  | sikertelen: a művelet sikertelen volt. |
| |  | sikeres: a művelet sikeresen befejeződött. |

> [!NOTE]
> Az ingyenes szintek percenként 20 hívásra korlátozzák a kérelmek sebességét. A fizetős csomag másodpercenként 10 másodpercenkénti kérést (RPS) tesz lehetővé. Az Azure támogatási csatornájának vagy a fiókja csapatának használatával nagyobb kérést igényelhet másodpercenként (RPS).

Ha az **állapot** mező értéke **sikeres** , a JSON-válasz a rendszerképből vagy dokumentumból kinyert szöveges tartalmat tartalmazza. A JSON-válasz a felismert szavak eredeti sorát tárolja. Magában foglalja a kinyert szöveg sorait és a hozzájuk tartozó mezők koordinátáit. Minden szöveges sor tartalmazza az összes kinyert szót a koordinátáikkal és a megbízhatósági pontszámokkal.

> [!NOTE]
> A műveletnek átadott adatok `Read` átmenetileg titkosítva vannak, és a nyugalmi állapotban tárolódnak, majd törölve lesznek. Ez lehetővé teszi, hogy az alkalmazások a szolgáltatás válaszának részeként beolvassák a kinyert szöveget.

## <a name="sample-json-output"></a>Példa JSON-kimenetre

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

## <a name="natural-reading-order-output-latin-only"></a>Természetes olvasási sorrend kimenete (csak latin betűs)
Az [olvasási 3,2 előzetes verzió API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)-val adja meg azt a sorrendet, amelyben a szövegsorok kimenete a `readingOrder` lekérdezési paraméterrel történik. Az `natural` az alábbi példában látható módon egy emberi felhasználóbarát olvasási sorrendi kimenetre használható. Ez a funkció csak latin nyelveken támogatott.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="OCR-olvasási sorrend – példa":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Szövegsorok kézírásos besorolása (csak latin betűs)
Az [olvasási 3,2 előzetes verzió API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) -válasza magában foglalja annak besorolását, hogy az egyes szövegfájlok kézírásos stílussal vagy sem, valamint a megbízhatósági pontszámmal együtt. Ez a funkció csak latin nyelveken támogatott. Az alábbi példa a képen látható szöveg kézírásos besorolását mutatja be.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Példa OCR kézírás-besorolásra":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Válassza ki az oldal (oka) t vagy az oldal tartományait szöveg kinyeréséhez
Ha a [Read 3,2 Preview API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)-t használja nagyméretű többoldalas dokumentumok esetén, a `pages` lekérdezési paraméterrel adhatja meg az oldalszámokat vagy az oldalak tartományait, hogy csak az adott lapról származó szöveget lehessen kinyerni. Az alábbi példa egy 10 oldalas dokumentumot mutat be, amely mindkét esetben kinyert szöveggel rendelkezik – az összes oldalt (1-10) és a kijelölt lapokat (3-6).

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Kijelölt lapok kimenete":::

## <a name="supported-languages"></a>Támogatott nyelvek
Az olvasási API-k összesen 73 nyelvet támogatnak a nyomtatási stílus szövegeként. Tekintse meg az [OCR által támogatott nyelvek](./language-support.md#optical-character-recognition-ocr)teljes listáját. A kézzel írott stílusú OCR kizárólag angol nyelven támogatott.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>A felhőalapú API használata vagy helyszíni üzembe helyezés
Az olvasási 3. x Felhőbeli API-k az előnyben részesített lehetőségek a legtöbb ügyfél számára, mivel a könnyű integráció és a gyors termelékenység a dobozból. Az Azure és a Computer Vision szolgáltatás kezeli a méretezést, a teljesítményt, az adatbiztonságot és a megfelelőségi igényeket, miközben az ügyfelek igényeinek kielégítésére koncentrál.

A helyszíni telepítéshez az [olvasási Docker-tároló (előzetes verzió)](./computer-vision-how-to-install-containers.md) lehetővé teszi az új OCR-funkciók üzembe helyezését a saját helyi környezetében. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez.

## <a name="ocr-api"></a>OCR API

Az [OCR API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) egy régebbi felismerési modellt használ, csak a képeket támogatja, és szinkron módon hajtja végre az észlelt szöveggel való azonnali visszatérést. Lásd az [OCR által támogatott nyelvek](./language-support.md#optical-character-recognition-ocr) , majd az API olvasása című témakört.

> [!NOTE]
> A vison 2,0 RecognizeText művelet folyamatban van a jelen cikkben ismertetett új olvasási API Javához. A meglévő ügyfeleknek [át kell térniük az olvasási műveletek használatára](upgrade-api-versions.md).

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a [Computer Vision REST API vagy az ügyféloldali kódtár](./quickstarts-sdk/client-library.md)gyors üzembe helyezésével.
- További információ az [olvasási 3,1 Rest APIról](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Ismerje meg az [olvasási 3,2 nyilvános REST API előzetes](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) verzióját, amely összesen 73 nyelvet támogat.
