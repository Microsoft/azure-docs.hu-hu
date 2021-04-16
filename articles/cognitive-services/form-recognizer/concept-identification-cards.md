---
title: IDs - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Megismeri az identitásdokumentumok adatkinyerésével kapcsolatos fogalmakat Form Recognizer előre felépített azonosítók API-val.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: 00e51d2c9515191b6d127355f49eeed3000a46ed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514713"
---
# <a name="form-recognizer-prebuilt-identification-id-document-model"></a>Form Recognizer (ID) dokumentummodell létrehozása

Az Azure Form Recognizer az előre összeállított azonosítómodell használatával elemezheti és kinyerheti a kormányzati azonosítókból származó információkat. A hatékony optikai [karakterfelismerési (OCR)](../computer-vision/overview-ocr.md) képességeket és az azonosítófelismerési képességeket kombinálva kulcsfontosságú információkat képes kinyerni a Worldwide Passports és az Egyesült Államok driver's Licenses (mind az 50 állam és D.C.) licenceiből. Az Azonosítók API kinyeri a kulcsfontosságú információkat az identitásdokumentumból, például a vezetéknevet, a vezetéknevet, a születési dátumot, a dokumentum számát stb. Ez az API a Form Recognizer 2.1-es verzió előzetes kiadásában érhető el felhőszolgáltatásként és egy saját tárolóként.

## <a name="what-does-the-id-service-do"></a>Mire való az azonosítószolgáltatás?

Az előre összeállított IDs szolgáltatás kinyeri a fő értékeket a globális passports és az EGYESÜLT Államok Driver's Licenses szolgáltatásból, és egy szervezett, strukturált JSON-válaszban adja vissza őket.

### <a name="drivers-license-example"></a>**Példa illesztői licencre**

![Minta illesztőlicenc](./media/id-example-drivers-license.JPG)

### <a name="passport-example"></a>**Passport-példa**

![Minta Passport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Kinyert mezők

|Név| Típus | Leírás | Érték |
|:-----|:----|:----|:----|
|  Ország | ország | Az ISO 3166 szabványnak megfelelő országkód | "USA" |
|  DateOf (Dátum) | dátum | DOB YYYYY-MM-DD formátumban | "1980-01-01" |
|  DateOfExpiration (Lejárat dátuma) | dátum | Lejárati dátum YYYY-MM-DD formátumban | "2019-05-05" |
|  DocumentNumber (Dokumentumszám) | sztring | Kapcsolódó passport-szám, illesztőlicenc-szám stb. | "340020013" |
|  FirstName | sztring | Kinyert given name and middle initial if applicable (Kinyert megadott név és a középső kezdeti név, ha van) | "LOVE" |
|  LastName | sztring | Kinyert vezetéknév | "MERT" |
|  Állampolgárság | ország | Az ISO 3166 szabványnak megfelelő országkód | "USA" |
|  szex | gender | Lehetséges kinyert értékek: "M", "F" és "X" | "F" |
|  MachineReadableZone | object | Kinyert Passport MRZ, két sornyi, 44 karakterből áll | "P<USA<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType (Dokumentumtípus) | sztring | Dokumentumtípus, például Passport, Driver's License | "passport" |
|  Cím | sztring | Kibontott cím (csak illesztőprogram-licenc esetén) | "123 STREET ADDRESS YOUR CITY WA 99999-1234"|
|  Region | sztring | Kibontott régió, állam, tartomány stb. (csak sofőrlicenc) | "Washington" |

### <a name="additional-features"></a>További funkciók

Az IDs API a következő információkat is visszaadja:

* Mező megbízhatósági szintje (minden mező egy társított megbízhatósági értéket ad vissza)
* OCR nyers szöveg (OCR által kinyert szöveges kimenet a teljes nyugtához)
* Az Usa-beli illesztőprogramok licenceiben kinyert mezők határolókerete
* Határolókeret a géppel olvasható zónához (MRZ) a Passportson

  > [!NOTE]
  > Az előre felépített azonosítók nem észlelik az azonosító hitelességét
  >
  > Form Recognizer előre felépített azonosítók kulcsadatokatnyernek ki az azonosítóadatokból. Nem észleli azonban az eredeti személyazonossági dokumentum érvényességét vagy hitelességét.

## <a name="try-it-out"></a>Próbálja ki

A felhasználói Form Recognizer az online felhasználói felületi eszközben próbálhat ki:

> [!div class="nextstepaction"]
> [Előre összeállított modellek kipróbálása](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Bemeneti követelmények

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Támogatott azonosítótípusok

* **Előre felépített, 2.1-preview.3-as verziójú ID-k** Kinyeri a fő értékeket a globális passportok és az Egyesült Államok illesztőprogram-licenceiből.

  > [!NOTE]
  > Azonosítótípus támogatása
  >
  > A jelenleg támogatott azonosítótípusok közé tartozik a globális passport és az Egyesült Államok illesztőprogram-licencei. Aktívan szeretnénk bővíteni az azonosítók támogatását a világ más identitásdokumentumaira.

## <a name="post-analyze-id-document"></a>POST analyze id dokumentum

Az [Azonosító elemzése](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) művelet bemenetként egy azonosító képét vagy PDF-fájlját veszi fel, és kinyeri a fontos értékeket. A hívás egy nevű válaszfejléc-mezőt ad `Operation-Location` vissza. Az érték egy URL-cím, amely tartalmazza a következő lépésben használni kívánt `Operation-Location` eredményazonosítót.

|Válaszfejléc| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>GET Analyze Id dokumentum eredménye

<!---
Need to update this with updated APIM links when available
-->

A második lépés az Analyze [**idDocument Result**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) művelet hívása. Ez a művelet bemenete az Elemzésazonosító művelet által létrehozott eredményazonosító. Egy JSON-választ ad vissza, amely egy **állapotmezőt** tartalmaz az alábbi lehetséges értékekkel. Ezt a műveletet iteratívan kell meghívni, amíg a sikeres értékkel nem **tér** vissza. A másodpercenkénti kérelmek (RPS) sebességének túllépése érdekében használjon 3–5 másodperces időközt.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: Az elemzési művelet még nem indult el. |
| |  | running: Az elemzési művelet folyamatban van. |
| |  | failed: Az elemzési művelet sikertelen volt. |
| |  | succeeded: Az elemzési művelet sikeres volt. |

Ha az **állapot mezőben** a **sikeres érték szerepel,** a JSON-válasz tartalmazza a nyugták megértését és a szövegfelismerés eredményeit. Az adatokat a rendszer elnevezett mezőértékek szótáraként rendezi, ahol minden érték tartalmazza a kinyert szöveget, a normalizált értéket, a határolókeretet, a megbízhatóságot és a megfelelő szóelemeket. A szövegfelismerés eredménye sorok és szavak hierarchiájaként van rendezve, szöveggel, határolókeretekkel és megbízhatósági információkkal.

![mintás nyugtaeredmények](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>JSON-kimenetminta

Tekintse meg a sikeres JSON-válasz alábbi példáját: A csomópont tartalmazza az `readResults` összes felismert szöveget. A szöveg lap, sor, majd egyéni szavak szerint van rendezve. A `documentResults` csomópont tartalmazza a modell által felderített azonosítóértékeket. Ezen a csomóponton olyan hasznos kulcs/érték párokat is talál, mint például az első név, a vezetéknév, a dokumentum száma stb.

```json
{
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],

     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Következő lépések

* Próbálja ki a saját felhasználói felületének Form Recognizer [mintáit és mintáit.](https://fott-preview.azurewebsites.net/)
* Rövid útmutató [Form Recognizer az](quickstarts/client-library.md) azonosítófeldolgozó alkalmazások írásának első Form Recognizer az Ön által választott fejlesztői nyelven.

## <a name="see-also"></a>Lásd még

* [**Mi a Form Recognizer?**](./overview.md)
* [**REST API referencia-dokumentumok**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
