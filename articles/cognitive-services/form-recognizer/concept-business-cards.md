---
title: Névjegykártyák – űrlap-felismerő
titleSuffix: Azure Cognitive Services
description: A névjegykártya-elemzéssel kapcsolatos fogalmak megismerése az űrlap-felismerő API használatának és korlátainak használatával.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5211c1263af599eb5fd09ad276545c725ce5c867
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466991"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Űrlap-felismerő előre elkészített névjegykártya-modellje 

Az Azure-űrlap felismerője az előre elkészített névjegykártya-modell használatával elemezheti és kinyerheti a kapcsolattartási adatokat az üzleti kártyáról. A hatékony optikai karakterfelismerési (OCR) képességeket kombinálja az üzleti kártyák megismerésére szolgáló modellel, amellyel kinyerheti a legfontosabb információkat az üzleti kártyáról az angol nyelven. Kigyűjti a személyes kapcsolattartási adatokat, a vállalat nevét, a munkakör címét és egyebeket. Az előre elkészített névjegykártya API nyilvánosan elérhető a Form felismerő v 2.1 előzetes verziójában. 

## <a name="what-does-the-business-card-service-do"></a>Mit tesz a Business Card szolgáltatás?

Az előre elkészített Business Card API Kinyeri a legfontosabb mezőket az üzleti kártyákból, és visszaadja őket egy szervezett JSON-válaszban.

![Contoso részletezett rendszerkép a FOTT + JSON kimenetből](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>Kinyert mezők:

|Név| Típus | Description | Szöveg | 
|:-----|:----|:----|:----|
| ContactNames | objektumok tömbje | A névjegykártyából kinyert kapcsolattartó neve | [{"FirstName": "John", "LastName": "DOE"}] |
| FirstName | sztring | A partner első (megadott) neve | John | 
| LastName | sztring | A partner utolsó (családi) neve |     DOE | 
| CompanyNames | sztringek tömbje | Vállalati név kinyerve a vállalati kártyáról | ["Contoso"] | 
| Részlegek | sztringek tömbje | Kapcsolattartó részleg vagy szervezet | ["R&D"] | 
| JobTitles | sztringek tömbje | Kapcsolattartó neve | ["Informatikus mérnök"] | 
| E-mail-címek | sztringek tömbje | Névjegykártyából kinyert e-mail cím | ["johndoe@contoso.com"] | 
| Webhelyek | sztringek tömbje | A vállalati kártyáról kinyert webhely | ["https://www.contoso.com"] | 
| Címek | sztringek tömbje | A névjegykártyából kinyert címek | ["123 Main Street, Redmond, WA 98052"] | 
| MobilePhones | telefonszámok tömbje | A névjegykártyából kinyert mobil telefonszám | ["+ 19876543210"] |
| Faxok | telefonszámok tömbje | A névjegykártyából kinyert fax telefonszáma | ["+ 19876543211"] |
| WorkPhones | telefonszámok tömbje | A névjegykártyából kinyert munkahelyi telefonszám | ["+ 19876543231"] |
| OtherPhones     | telefonszámok tömbje | A névjegykártyából kinyert egyéb telefonszám | ["+ 19876543233"] |


A Business Card API az összes felismert szöveget visszaküldheti a névjegykártyából. Ez az OCR-kimenet szerepel a JSON-válaszban.  

### <a name="input-requirements"></a>Bemeneti követelmények 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>A névjegykártya elemzése művelet

Az [elemzés](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync) a névjegykártyán egy, a bevitt adatokhoz tartozó képet vagy PDF-fájlt vesz fel, és Kinyeri a kamat értékeit. A hívás egy válasz fejléc nevű mezőt ad vissza `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó eredmény-azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>Az elemzés az üzleti kártya eredményének lekérdezése művelet

A második lépés a [Get elemezze névjegykártya-eredmény](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult) műveletének meghívása. Ez a művelet a névjegykártya elemzése művelet által létrehozott eredmény-azonosítót veszi figyelembe. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A kérések másodpercenkénti (RPS) arányának meghaladása érdekében használjon 3 – 5 másodperces intervallumot.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: az elemzési művelet nem indult el.<br /><br />fut: az elemzési művelet folyamatban van.<br /><br />sikertelen: az elemzési művelet meghiúsult.<br /><br />sikeres: az elemzési művelet sikeresen befejeződött.|

Ha az **állapot** mező értéke **sikeres** , a JSON-válasz tartalmazni fogja a névjegykártya megértését és az opcionális szöveg-felismerési eredményeket, ha szükséges. Az üzleti kártya megértésének eredménye a megnevezett mezőértékek szótára lesz, ahol minden érték a kinyert szöveget, normalizált értéket, határoló mezőt, megbízhatóságot és a hozzá tartozó elemeket tartalmazza. A szöveg-felismerés eredményét vonalak és szavak hierarchiája rendezi, szöveg, határolókeret és bizalmas adatok formájában.

![névjegykártya-kimenet mintája](./media/business-card-results.png)

### <a name="sample-json-output"></a>Példa JSON-kimenetre

A Get elemezze az üzleti kártya eredményére adott válasz a névjegykártya strukturált ábrázolása lesz a kinyert információkkal együtt.  A [minta névjegykártya-fájl](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) és a strukturált kimeneti [minta névjegykártya-kimenete](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)itt található.

Tekintse meg a sikeres JSON-válasz következő példáját:
* A `"readResults"` csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. 
* A `"documentResults"` csomópont tartalmazza a modell által felderített névjegykártya-specifikus értékeket. Itt találhat hasznos kapcsolattartási adatokat, például az utónév, a vezetéknév, a vállalat neve és egyebek.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

A gyors üzembe helyezési útmutatóval a Python és a REST API segítségével implementálhatja [a névjegykártya](./QuickStarts/client-library.md) -adatgyűjtést.

## <a name="customer-scenarios"></a>Felhasználói forgatókönyvek  

A Business Card API-val kinyert adatok különféle feladatok elvégzésére használhatók. A kapcsolattartási adatok kinyerése automatikusan időt takarít meg a felhasználók számára az ügyfelek felé irányuló szerepkörökben. Az alábbiakban néhány példát láthat arra, hogy ügyfeleink hogyan teljesítik a Business Card API-t:

* A kapcsolattartási adatok kinyerése az üzleti kártyákból és a telefonos névjegyek gyors létrehozása. 
* Integrálja a CRM-sel, hogy automatikusan hozzon létre névjegyeket a névjegykártya-lemezképek használatával. 
* Tartsa nyomon az értékesítési érdeklődőket.  
* A meglévő névjegykártya-rendszerképekből tömegesen kinyerheti a kapcsolattartási adatokat. 

A Business Card API a [mesterséges intelligencia-készítő névjegykártya-feldolgozási funkciót](/ai-builder/prebuilt-business-card)is felhasználja.

## <a name="next-steps"></a>Következő lépések

- A gyors üzembe helyezéshez [kövesse az üzleti](./quickstarts/client-library.md) kártyák felismerésének első lépéseit.

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](./overview.md)
* [REST API dokumentációs dokumentumok](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)
