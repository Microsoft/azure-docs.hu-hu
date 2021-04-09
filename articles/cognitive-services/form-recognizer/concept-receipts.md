---
title: Nyugták – űrlap-felismerő
titleSuffix: Azure Cognitive Services
description: Ismerje meg a beérkezési elemzéssel kapcsolatos fogalmakat az űrlap-felismerő API-használat és-korlátok alapján.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 81e8cd5cf4af8da76ae4eb09bed5a4ee0368da4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467357"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Űrlap-felismerő előre összeépített bevételezési modellje

Az Azure-űrlap felismerője az előre elkészített beérkezési modell használatával elemezheti és kinyerheti az értékesítési nyugták adatait. A hatékony optikai karakterfelismerési [(OCR)](../computer-vision/concept-recognizing-text.md) képességeket kombinálja a részletes tanulási modellekkel, hogy kinyerje a legfontosabb információkat az angol nyelven írt nyugták alapján.

## <a name="understanding-receipts"></a>A visszaigazolások ismertetése

Számos vállalat és személy továbbra is az értékesítési visszaigazolásokból származó, manuálisan kinyert adatokra támaszkodik. Az adatok e nyugtából való automatikus kinyerése bonyolult lehet. Előfordulhat, hogy a visszaigazolások gyűrött, nehezen olvasható, kézírásos részekkel rendelkeznek, és alacsony minőségi okostelefon-rendszerképeket tartalmaznak. Emellett a beérkezési sablonok és a mezők nagy mértékben a piac, a régió és a kereskedő szerint változhatnak. Ezek az adatok kinyerése és a mező-észlelési kihívások egy egyedi probléma feldolgozását teszik elérhetővé.  

A beérkezési API az optikai karakterfelismerés (OCR) és az előre elkészített modell használatával lehetővé teszi a nagy mennyiségű bevételezés-feldolgozási forgatókönyvek használatát. A beérkezési API-val nem kell modelleket betanítani. Küldje el a beérkezési képet az elemzés beérkezési API-nak, és az adatok kibontása.

![minta nyugtája](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>Mire szolgál a beérkezési szolgáltatás? 

Az előre elkészített bevételezési szolgáltatás kibontja az értékesítési nyugták tartalmát &mdash; , amelyet általában egy étterem, kiskereskedő vagy élelmiszerbolt tárol.

### <a name="fields-extracted"></a>Kinyert mezők

|Név| Típus | Leírás | Szöveg | Érték (szabványosított kimenet) |
|:-----|:----|:----|:----| :----|
| ReceiptType | sztring | Az értékesítési visszaigazolás típusa | Elkülönül |  |
| MerchantName | sztring | A nyugtát kiállító kereskedő neve | Contoso |  |
| MerchantPhoneNumber | Telefonszám | A felsorolt telefonszámok száma | 987-654-3210 | + 19876543210 |
| MerchantAddress | sztring | Kereskedők listázott címe | 123 Main St Redmond WA 98052 |  |
| TransactionDate | dátum | A visszaigazolás kiállításának dátuma | 2019. június 06. | 2019-06-26  |
| TransactionTime | time | A visszaigazolás kiállításának időpontja | 4:49 PM | 16:49:00  |
| Összesen | szám | Teljes tranzakció összes nyugtája | $14,34 | 14,34 |
| Részösszeg | szám | Beérkezési részösszegek – gyakran az adók alkalmazása előtt | $12,34 | 12.34 |
| Adó | szám | Beérkezési adó, gyakran forgalmi adó vagy ezzel egyenértékű | 2 USD | 2.00 |
| Tipp | szám | A vásárló által tartalmazott tipp | $1,00 | 1,00 |
| Elemek | objektumok tömbje | Kinyert elemek, a név, a mennyiség, az egység ára és a kinyert teljes összeg | |
| Name | sztring | Elemnév | 6. felszíni Pro | |
| Mennyiség | szám | Az egyes elemek mennyisége | 1 | |
| Ár | szám | Minden egyes elemszintű egység ára | $999,00 | 999,00 |
| Teljes díj | szám | Sor teljes díja | $999,00 | 999,00 |

### <a name="additional-features"></a>További funkciók

A beérkezési API a következő információkat is megadja:

* Mező megbízhatósági szintje (minden mező a hozzá tartozó megbízhatósági értéket adja vissza)
* OCR nyers szöveg (OCR – kinyert szöveges kimenet a teljes bevételezéshez)
* Az egyes értékek, sorok és szavak határoló mezője

## <a name="try-it-out"></a>Próbálja ki

Az űrlap-felismerő beérkezési szolgáltatás kipróbálásához nyissa meg az online minta felhasználói felület eszközét:

> [!div class="nextstepaction"]
> [Előre elkészített modellek kipróbálása](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Bemeneti követelmények

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Támogatott területi beállítások 

* Az előre összeállított **beérkezési v 2.0** (GA) támogatja a Sales nyugtákat az en-us területi beállításban
* **Előzetesen kiépített beérkezési v 2.1 – előzetes verzió. 3** (nyilvános előzetes) további támogatás a következő en beérkezési területi beállításokhoz: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Nyelvi bevitel 
  >
  > Az előre elkészített beérkezés v 2.1-es verziója – előzetes verzió. 3 egy opcionális kérési paraméterrel megadhatja a további angol piacoktól érkező bevételezési területi beállításokat. Az ausztrál (EN-AU), Kanada (EN-CA), Nagy-Britannia (EN-GB) és India (en-IN) típusú értékesítési nyugták esetében megadhatja a területi beállításokat a jobb eredmények eléréséhez. Ha nincs megadva területi beállítás a v 2.1 – preview. 3 esetében, a modell alapértelmezett értéke az EN-US modell lesz.


## <a name="the-analyze-receipt-operation"></a>A Bevételezés elemzése művelet

Az [elemzés visszaigazolása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync) egy nyugtát tartalmazó képet vagy PDF-fájlt vesz fel bemenetként, és Kinyeri a kamat és a szöveg értékeit. A hívás egy válasz fejléc nevű mezőt ad vissza `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó eredmény-azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>Az elemzés visszaigazolási eredményének lekérése művelet

A második lépés az [elemzés visszaigazolási eredményének beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult) művelet hívása. Ez a művelet az elemzés-visszaigazolási művelettel létrehozott eredmény-azonosítót veszi figyelembe. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A kérések másodpercenkénti (RPS) arányának meghaladása érdekében használjon 3 – 5 másodperces intervallumot.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: a művelet nem indult el. |
| |  | fut: az elemzési művelet folyamatban van. |
| |  | sikertelen: az elemzési művelet meghiúsult. |
| |  | sikeres: az elemzési művelet sikeresen befejeződött. |

Ha az **állapot** mező értéke **sikeres** , a JSON-válasz tartalmazni fogja a bevételezési és a szöveges felismerési eredményeket. A nyugta megértésének eredménye a megnevezett mezőértékek szótára. Minden érték tartalmazza a kinyert szöveget, a normalizált értéket, a határoló mezőt, a megbízhatóságot és a megfelelő szavakat. A szöveg-felismerés eredményét vonalak és szavak hierarchiája rendezi, szöveg, határolókeret és bizalmas adatok formájában.

![Példa a Bevételezés eredményeire](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Példa JSON-kimenetre


A beolvasási visszaigazolás eredményének beolvasása műveletre adott válasz a visszaigazolás strukturált ábrázolása lesz a kinyert információkkal együtt.  Tekintse meg a [minta beérkezési fájlját](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg) és a strukturált kimeneti [minta bevételezési kimenetét](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

Tekintse meg a sikeres JSON-válasz következő példáját:
* A `"readResults"` csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. 
* A `"documentResults"` csomópont tartalmazza a modell által felderített névjegykártya-specifikus értékeket. Itt talál hasznos kulcs/érték párokat, mint például az utónév, a vezetéknév, a vállalat neve és még sok más.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="customer-scenarios"></a>Felhasználói forgatókönyvek  

A beérkezési API-val kinyert adatok különféle feladatok elvégzésére használhatók. Az alábbiakban néhány példát láthat arra, hogy ügyfeleink hogyan teljesítik a beérkezési API-t.

### <a name="business-expense-reporting"></a>Üzleti költségek jelentése  

Gyakran az üzleti költségek bejelentése magában foglalja a beérkezési képekből származó adatok manuális bevitelét. A beérkezési API-val a kibontott mezők használatával részben automatizálhatja ezt a folyamatot, és gyorsan elemezheti a nyugtákat.  

A beérkezési API egy egyszerű JSON-kimenet, amely lehetővé teszi a kinyert mezőértékek több módon történő használatát. Integrálja a költségeket a belső költségekkel rendelkező alkalmazásokkal a költségelszámolás előzetes kitöltéséhez. Ennél a forgatókönyvnél további információért olvassa el, hogy a Acumatica hogyan használja a beérkezési API-t, hogy [a költségek kevésbé fájdalmas folyamaton](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure)alapulnak.  

### <a name="auditing-and-accounting"></a>Naplózás és nyilvántartás

A beérkezési API kimenete nagy számú, a költség-jelentési és a költségtérítési folyamat különböző pontjain elvégezhető kiadások elemzésére is használható. A visszaigazolásokat feldolgozhatja manuális audit vagy gyors jóváhagyások osztályozásához.  

A nyugta kimenete is hasznos lehet az üzleti vagy személyes használatra való általános könyveléshez. A beérkezési API használatával bármilyen nyers beérkezési képet vagy PDF-adatokat átalakíthat egy végrehajtható digitális kimenetbe.

### <a name="consumer-behavior"></a>Fogyasztói viselkedés 

A nyugták hasznos információkat tartalmaznak, amelyek segítségével elemezheti a fogyasztói viselkedést és a vásárlási trendeket.

A beérkezési API a [mesterséges intelligencia-készítő bevételezés feldolgozási funkcióját](/ai-builder/prebuilt-receipt-processing)is felhasználja.

## <a name="next-steps"></a>Következő lépések

 . Első lépésként írja le a nyugtát feldolgozó alkalmazást a választott fejlesztői nyelven.

> [!div class="nextstepaction"]
> [Űrlap-felismerő rövid útmutatójának befejezése](quickstarts/client-library.md)

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](overview.md)
* [Űrlap-felismerő API-referenciája](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)
>
