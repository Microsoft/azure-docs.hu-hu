---
title: Szöveg kinyerése képekből
titleSuffix: Azure Cognitive Search
description: Szöveg és egyéb információk feldolgozása és kinyerése az Azure Cognitive Search-folyamatokból származó képekből.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e77bbd6e82d0d4a48b72e13e60b60608f2d7674
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419591"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Információk feldolgozása és kinyerése a képekből mesterséges intelligencia-gazdagító forgatókönyvekben

Az Azure Cognitive Search számos képességgel rendelkezik a képek és a képfájlok használatáról. A dokumentum repedése során a *imageAction* paraméter használatával kinyerheti a szövegeket a fényképekből vagy az alfanumerikus szöveget tartalmazó képekből, például a "Leállítás" szót egy leállítási jelre. Más forgatókönyvek közé tartozik például a képek szöveges ábrázolása, például a "pitypang", egy pitypang fényképe vagy a "sárga" szín. A rendszerképpel kapcsolatos metaadatokat is kinyerheti, például a méretét.

Ez a cikk részletesebben ismerteti a képfeldolgozást, és útmutatást nyújt a rendszerképek egy mesterséges intelligenciával kapcsolatos folyamatokban való használatához.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Normalizált rendszerképek lekérése

A dokumentum repedésének részeként új indexelő konfigurációs paraméterek találhatók a fájlokba ágyazott képfájlok vagy rendszerképek kezelésére. Ezek a paraméterek a lemezképek további alsóbb rétegbeli feldolgozáshoz való normalizálása céljából használatosak. A képek normalizálása egységesebb lesz. A nagyméretű képek mérete átméretezi a maximális magasságot és a szélességet, hogy a felhasználásra felhasználható legyen. A tájolási metaadatokat biztosító rendszerképeknél a kép elforgatása a függőleges betöltéshez van beállítva. A metaadatok módosításait az egyes lemezképekhez létrehozott összetett típusok rögzítik. 

A képnormalizálás nem kapcsolható ki. A képeken ismétlődő képességek normalizált képeket várnak. A képnormalizálás indexelésének engedélyezéséhez az szükséges, hogy egy készségkészlet csatolva legyen ehhez az indexelő.

| Konfigurációs paraméter | Leírás |
|--------------------|-------------|
| imageAction   | A "None" értékre van állítva, ha a rendszer nem hajt végre műveletet a beágyazott képek vagy képfájlok előfordulásakor. <br/>A "generateNormalizedImages" értékre állítva a normalizált képek tömbjét hozza létre a dokumentum repedésének részeként.<br/>Állítsa "generateNormalizedImagePerPage" értékre, ha normalizált képekből álló tömböt hoz létre, ahol az adatforrásban található PDF-fájlok esetében minden oldal egy kimeneti képhez jelenik meg.  A funkció ugyanaz, mint a nem PDF fájltípusok esetében a "generateNormalizedImages".<br/>Minden olyan lehetőség esetében, amely nem "None", a rendszer a képeket a *normalized_images* mezőben teszi elérhetővé. <br/>Az alapértelmezett érték a "None". Ez a konfiguráció csak a blob-adatforrásokra vonatkozik, ha a "dataToExtract" tulajdonság értéke "contentAndMetadata". <br/>Egy adott dokumentumból legfeljebb 1000 lemezkép lesz kinyerve. Ha a dokumentumban több mint 1000 kép található, akkor a rendszer kinyeri az első 1000, és egy figyelmeztetést fog generálni. |
|  normalizedImageMaxWidth | A generált normalizált képek maximális szélessége (képpontban). Az alapértelmezett érték 2000. A maximálisan engedélyezett érték 10000. | 
|  normalizedImageMaxHeight | A generált normalizált képek maximális magassága (képpontban). Az alapértelmezett érték 2000. A maximálisan engedélyezett érték 10000.|

> [!NOTE]
> Ha a *imageAction* tulajdonságot a "None" értékre állítja, a *parsingMode* tulajdonság nem állítható be a "default" értéktől eltérő értékre.  Az indexelő konfigurációjában csak az alábbi két tulajdonság egyikét állíthatja be nem alapértelmezett értékre.

Állítsa a **parsingMode** paramétert `json` (az egyes Blobok egyetlen dokumentumként való indexeléséhez), vagy `jsonArray` (ha a Blobok JSON-tömböket tartalmaznak, és egy tömb minden eleméhez külön dokumentumként kell kezelni őket).

A normalizált képek maximális szélességének és magasságának 2000 képpont alapértéke az [OCR-képesség](cognitive-search-skill-ocr.md) és a [képelemzési képesség](cognitive-search-skill-image-analysis.md)által támogatott maximális méretektől függ. Az [OCR-képesség](cognitive-search-skill-ocr.md) legfeljebb 4200-es szélességet és magasságot támogat a nem angol nyelvű és a 10000 angol nyelv esetén.  Ha csökkenti a maximális korlátot, a feldolgozás a készségkészlet-definíciótól és a dokumentumok nyelvétől függően meghiúsulhat a nagyobb rendszerképeken. 

A imageAction az alábbi módon adhatja meg az [Indexelő definíciójában](/rest/api/searchservice/create-indexer) :

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Ha a *imageAction* egy másik értékre van állítva, akkor az új *normalized_images* mező képek tömbjét fogja tartalmazni. Mindegyik rendszerkép egy összetett típus, amely a következő tagokkal rendelkezik:

| Rendszerkép tagja       | Leírás                             |
|--------------------|-----------------------------------------|
| adatok               | A normalizált képfájl BASE64 kódolású karakterlánca JPEG formátumban.   |
| szélesség              | A normalizált képek szélessége képpontban megadva |
| magasság             | A normalizált képek magassága képpontban megadva |
| originalWidth      | A rendszerkép eredeti szélessége a normalizálás előtt. |
| originalHeight      | A rendszerkép eredeti magassága a normalizálás előtt. |
| rotationFromOriginal |  A normalizált rendszerkép létrehozásához szükséges fokban lévő óramutató járása. 0 fok és 360 fok közötti érték. Ez a lépés beolvassa a metaadatokat a kamera vagy képolvasó által generált rendszerképből. Általában a 90 fok többszöröse. |
| contentOffset | Az a tartalom mezőn belüli eltolás, amelyből a képet kinyerték. Ez a mező csak beágyazott rendszerképekkel rendelkező fájlokra alkalmazható. |
| pageNumber | Ha a képet kibontották vagy PDF-fájlként állították ki, akkor ez a mező tartalmazza a PDF-fájl oldalszámát, amelyet a program kinyert vagy megjelenített, 1-től kezdődően.  Ha a rendszerkép nem PDF-fájlból származik, ez a mező 0 lesz.  |

 *Normalized_images* mintájának értéke:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Képekkel kapcsolatos ismeretek

Két beépített kognitív képességgel rendelkezik, amelyek bemenetként készítenek képeket: [OCR](cognitive-search-skill-ocr.md) és [Image Analysis](cognitive-search-skill-image-analysis.md). 

Jelenleg ezek a képességek csak a dokumentum csinos lépése által generált képekkel működnek. Így az egyetlen támogatott bemenet `"/document/normalized_images"` .

### <a name="image-analysis-skill"></a>Rendszerkép-elemzési képesség

A [képelemzési képesség](cognitive-search-skill-image-analysis.md) a vizualizációs funkciók gazdag készletét Kinyeri a kép tartalma alapján. Például létrehozhat egy képfeliratot egy képből, címkéket hozhat, vagy azonosíthatja a hírességeket és a tereptárgyait.

### <a name="ocr-skill"></a>OCR-képesség

Az [OCR-képesség](cognitive-search-skill-ocr.md) a képfájlok, például a jpg, a PNG és a bitképek szövegét kinyeri. Kinyerheti a szöveget és az elrendezési adatokat is. Az elrendezési adatok kereteket biztosítanak az egyes azonosított karakterláncokhoz.

## <a name="embedded-image-scenario"></a>Beágyazott rendszerkép forgatókönyve

Egy gyakori forgatókönyvben egyetlen karakterláncot kell létrehozni, amely az összes fájl tartalmát, a szöveg és a képforrás szövegét is tartalmazza a következő lépések végrehajtásával:  

1. [Normalized_images kinyerése](#get-normalized-images)
1. OCR-képesség futtatása `"/document/normalized_images"` bemenetként
1. Egyesítse a képek szöveges ábrázolását a fájlból kinyert nyers szöveggel. A [szöveg egyesítése](cognitive-search-skill-textmerger.md) képességgel összevonhatja a szöveges adattömböket egyetlen nagy sztringbe.

A következő példában a készségkészlet létrehoz egy *merged_text* mezőt, amely a dokumentum szöveges tartalmát tartalmazza. Emellett magában foglalja az egyes beágyazott képek OCRed szövegét is. 

#### <a name="request-body-syntax"></a>Kérelem törzsének szintaxisa
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```

Most, hogy már rendelkezik egy merged_text mezővel, leképezheti azt kereshető mezőként az indexelő definíciójában. A fájlok összes tartalma, beleértve a képek szövegét, kereshetővé válik.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Kinyert szöveg határoló mezőinek megjelenítése

Egy másik gyakori forgatókönyv a keresési eredmények elrendezési adatainak megjelenítése. Például érdemes lehet kiemelni, hogy hol található egy szöveg a képen a keresési eredmények részeként.

Mivel az OCR-lépés a normalizált lemezképeken történik, az elrendezés koordinátái a normalizált képterületen vannak. A normalizált rendszerkép megjelenítésekor a koordináták jelenléte általában nem probléma, de előfordulhat, hogy az eredeti rendszerképet szeretné megjeleníteni. Ebben az esetben alakítsa át az elrendezésben lévő összes koordináta-pontot az eredeti kép koordináta-rendszerébe. 

Segítőként, ha a normalizált koordinátákat át kell alakítani az eredeti koordináta-területre, a következő algoritmust használhatja:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```
## <a name="passing-images-to-custom-skills"></a>Képek átadása egyéni képességekhez

Olyan forgatókönyvek esetén, ahol egyéni képességre van szükség a képeken való munkához, képeket adhat át az egyéni képességek számára, és szöveget vagy képeket adhat vissza. A [Python minta](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing) rendszerképének feldolgozása a munkafolyamatot mutatja be. A következő készségkészlet a mintából származnak.

A következő készségkészlet a normalizált képet (amely a dokumentum repedése során kapott), és megjeleníti a képekből származó szeleteket.

#### <a name="sample-skillset"></a>Minta készségkészlet
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
          "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
          "name": "ImageSkill",
          "description": "Segment Images",
          "context": "/document/normalized_images/*",
          "uri": "https://your.custom.skill.url",
          "httpMethod": "POST",
          "timeout": "PT30S",
          "batchSize": 100,
          "degreeOfParallelism": 1,
          "inputs": [
            {
              "name": "image",
              "source": "/document/normalized_images/*"
            }
          ],
          "outputs": [
            {
              "name": "slices",
              "targetName": "slices"
            }
          ],
          "httpHeaders": {}
        }
  ]
}
```

#### <a name="custom-skill"></a>Egyéni szaktudás

Maga az egyéni szaktudás kívül esik a készségkészlet. Ebben az esetben ez a Python-kód, amely az első ciklusban a rekordok kötegét az egyéni szakértelem formátumában, majd átalakítja a Base64 kódolású karakterláncot egy képre.

```python
# deserialize the request, for each item in the batch
for value in values:
  data = value['data']
  base64String = data["image"]["data"]
  base64Bytes = base64String.encode('utf-8')
  inputBytes = base64.b64decode(base64Bytes)
  # Use numpy to convert the string to an image
  jpg_as_np = np.frombuffer(inputBytes, dtype=np.uint8)
  # you now have an image to work with
```
A rendszerkép visszaküldéséhez hasonlóan egy Base64 kódolású karakterláncot ad vissza egy JSON-objektumon belül egy `$type` tulajdonsággal `file` .

```python
def base64EncodeImage(image):
    is_success, im_buf_arr = cv2.imencode(".jpg", image)
    byte_im = im_buf_arr.tobytes()
    base64Bytes = base64.b64encode(byte_im)
    base64String = base64Bytes.decode('utf-8')
    return base64String

 base64String = base64EncodeImage(jpg_as_np)
 result = { 
  "$type": "file", 
  "data": base64String 
}
```

## <a name="see-also"></a>Lásd még
+ [Indexelő létrehozása (REST)](/rest/api/searchservice/create-indexer)
+ [Rendszerkép-elemzési képesség](cognitive-search-skill-image-analysis.md)
+ [OCR-képesség](cognitive-search-skill-ocr.md)
+ [Szöveg egyesítésének képessége](cognitive-search-skill-textmerger.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [A dúsított mezők leképezése](cognitive-search-output-field-mapping.md)
+ [Képek átadása egyéni szakembereknek](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing)
