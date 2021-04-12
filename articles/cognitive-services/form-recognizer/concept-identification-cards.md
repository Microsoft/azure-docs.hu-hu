---
title: Azonosítók – űrlap felismerő
titleSuffix: Azure Cognitive Services
description: Ismerje meg az Identity Documents-ből származó adatok kinyerésével kapcsolatos fogalmakat az űrlap-felismerő előre elkészített azonosítói API-val.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed8516f9a898131338fb5b4d75e25cd774c5ab43
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285356"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Űrlap-felismerő előre elkészített azonosító kártya (azonosító) modellje

Az Azure-űrlap felismerője az előre elkészített azonosítók modelljével elemezheti és kinyerheti a kormányzati azonosítók (azonosítók) adatait. A nagy teljesítményű [optikai karakterfelismerési (OCR)](../computer-vision/overview-ocr.md) képességeket kombinálja az azonosító képességekkel a globális Passportok és az Egyesült államokbeli illesztőprogram-licencek (az összes 50-es állam és DC) legfontosabb adatainak kinyeréséhez. Az azonosítók API Kinyeri a legfontosabb információkat ezekből az azonosító dokumentumokból, például az utónévből, a vezetéknevből, a születési dátumból, a bizonylatszámből és egyebekből. Ez az API az űrlap felismerő v 2.1 előzetes verziójában érhető el felhőalapú szolgáltatásként és helyszíni tárolóként.

## <a name="what-does-the-id-service-do"></a>Mire szolgál az ID szolgáltatás? 

Az előre elkészített azonosítók szolgáltatás kibontja a globális Passportok és az Egyesült államokbeli illesztőprogram-licencek kulcsfontosságú értékeit, és egy szervezett, strukturált JSON-válaszban adja vissza őket. 

![Minta-illesztőprogram licence](./media/id-example-drivers-license.JPG)

![Minta Passport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Kinyert mezők

|Név| Típus | Leírás | Érték | 
|:-----|:----|:----|:----|
|  Ország | ország | ISO 3166 szabványnak megfelelő országkód | USA | 
|  DateOfBirth | dátum | DOB éééé-hh-nn formátumban | "1980-01-01" | 
|  DateOfExpiration | dátum | Lejárati dátum éééé-hh-nn formátumban | "2019-05-05" |  
|  DocumentNumber | sztring | A kapcsolódó Passport-szám, az illesztőprogram licencének száma stb. | "340020013" |  
|  FirstName | sztring | A megadott név és a középső kezdőbetű kinyerve, ha van ilyen | Jennifer | 
|  LastName | sztring | Kinyert vezetékneve | Brooks |   
|  Állampolgárság | ország | ISO 3166 szabványnak megfelelő országkód | USA |
|  szex | gender | A lehetséges kinyert értékek a következők: "M", "F" és "X" | F | 
|  MachineReadableZone | object | Kinyert Passport-OCR, beleértve két 44 karakterből álló sort | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | sztring | Bizonylattípus, például Passport, jogosítvány | Passport |  
|  Cím | sztring | Kinyert címek (csak az illesztőprogram licence) | "123 UTCA, AHOL A VÁROSA WA 99999-1234"|
|  Region | sztring | Kinyert régió, állam, tartomány stb. (csak az illesztőprogram licence) | Washington | 

### <a name="additional-features"></a>További funkciók

Az azonosítók API a következő információkat is megadja:

* Mező megbízhatósági szintje (minden mező a hozzá tartozó megbízhatósági értéket adja vissza)
* OCR nyers szöveg (OCR – kinyert szöveges kimenet a teljes bevételezéshez)
* Az USA-beli illesztőprogram-licencek egyes kinyert mezőinek határoló mezője
* A géppel olvasható zóna (OCR) korlátja a Passportokon

  > [!NOTE]
  > Az előre elkészített azonosítók nem azonosítják az azonosító hitelességét
  >
  > Az űrlap-felismerő előre elkészített azonosítói kinyerik az azonosító adatokból származó legfontosabb adatokból. Azonban nem ismeri fel az eredeti személyazonossági dokumentum érvényességét vagy hitelességét. 

## <a name="try-it-out"></a>Próbálja ki

Az űrlap-felismerő azonosítók szolgáltatás kipróbálásához nyissa meg az online minta felhasználói felület eszközét:

> [!div class="nextstepaction"]
> [Előre elkészített modellek kipróbálása](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Bemeneti követelmények

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Támogatott azonosító típusok  

* **Előre elkészített azonosítók v 2.1 – előzetes verzió. 3** Kinyeri a legfontosabb értékeket a globális Passportok és az Egyesült államokbeli illesztőprogramok licencei alapján. 

  > [!NOTE]
  > AZONOSÍTÓ típusa támogatás 
  >
  > A jelenleg támogatott azonosító típusok a következők: Worldwide Passport és US Driver 's licenses. Aktívan arra törekszünk, hogy a világ más személyazonossági dokumentumaihoz is kiterjesszék az AZONOSÍTÓnk támogatását.

## <a name="post-analyze-id-document"></a>Az elemzési azonosító dokumentum közzététele

Az elemzési [azonosító](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) művelet egy, az azonosítóhoz tartozó képet vagy PDF-fájlt vesz fel bemenetként, és Kinyeri a kamat értékeit. A hívás egy válasz fejléc nevű mezőt ad vissza `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó eredmény-azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>Elemzési azonosító dokumentum eredményének beolvasása

<!---
Need to update this with updated APIM links when available
-->

A második lépés a [**Get elemezze IdDocument eredményének**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) meghívása. Ez a művelet az elemzési azonosító művelettel létrehozott eredmény-azonosítót veszi figyelembe. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A kérések másodpercenkénti (RPS) arányának meghaladása érdekében használjon 3 – 5 másodperces intervallumot.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: az elemzési művelet nem indult el. |
| |  | fut: az elemzési művelet folyamatban van. |
| |  | sikertelen: az elemzési művelet meghiúsult. |
| |  | sikeres: az elemzési művelet sikeresen befejeződött. |

Ha az **állapot** mező értéke **sikeres** , a JSON-válasz tartalmazni fogja a bevételezési és a szöveges felismerési eredményeket. Az azonosítók eredményét névvel ellátott mezőértékeket tartalmazó szótárként rendezi a rendszer, ahol minden érték tartalmazza a kinyert szöveget, a normalizált értéket, a határoló mezőt, a megbízhatóságot és a megfelelő szavakat. A szöveg-felismerés eredményét vonalak és szavak hierarchiája rendezi, szöveg, határolókeret és bizalmas adatok formájában.

![Példa a Bevételezés eredményeire](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Példa JSON-kimenetre

Tekintse meg a sikeres JSON-válasz következő példáját: a `readResults` csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. A `documentResults` csomópont tartalmazza a modell által felderített azonosító értékeket. Ez a csomópont olyan hasznos kulcs/érték párokat is talál, mint az utónév, a vezetéknév, a bizonylatszám és sok más.

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

- Próbálja ki a saját azonosítóit és mintáit az [űrlap-felismerő minta felhasználói felületén](https://fott-preview.azurewebsites.net/).
- Egy [űrlap-felismerő](quickstarts/client-library.md) rövid útmutatójának elvégzésével megkezdheti az azonosító-feldolgozó alkalmazás írását az űrlap-felismerővel a választott fejlesztői nyelven.

## <a name="see-also"></a>Lásd még

* [**Mi a Form Recognizer?**](./overview.md)
* [**REST API dokumentációs dokumentumok**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
