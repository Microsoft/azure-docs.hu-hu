---
title: Nyugták – űrlap-felismerő
titleSuffix: Azure Cognitive Services
description: Ismerje meg a beérkezési elemzéssel kapcsolatos fogalmakat az űrlap-felismerő API-használat és-korlátok alapján.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 00709ca5e842e51edbf5b26c53fe0a18e80bb896
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262348"
---
# <a name="receipt-concepts"></a>Nyugtákra vonatkozó fogalmak

Az Azure-űrlap felismerője az előre elkészített modellek egyikével elemezheti a nyugtákat. A beérkezési API Kinyeri a legfontosabb információkat az értékesítési visszaigazolásokból az angol nyelven, például a kereskedelmi név, a tranzakció dátuma, a tranzakciók összege, a sorok és egyebek. 

## <a name="understanding-receipts"></a>A visszaigazolások ismertetése 

Számos vállalat és személy továbbra is az értékesítési visszaigazolásokból származó adatok manuális kinyerésére támaszkodik, akár üzleti költségekről szóló jelentésekre, térítésekre, naplózásra, adózási célokra, költségvetésre, marketingre vagy más célra. Ezekben a forgatókönyvekben gyakran a fizikai visszaigazolás képei szükségesek az érvényesítéshez.  

Az adatok e nyugtából való automatikus kinyerése bonyolult lehet. A visszaigazolások lehetnek gyűrött és nehezen olvasható, nyomtatott vagy kézírásos részek, valamint a beérkezések smartphone-képei alacsony színvonalúak lehetnek. Emellett a beérkezési sablonok és a mezők nagy mértékben a piac, a régió és a kereskedő szerint változhatnak. Ezek a kihívások mind az kinyerési, mind a helyszíni észlelési folyamat során egyedi problémát jelentenek.  

Az optikai karakterfelismerés (OCR) és az előre összevont beérkezési modell használatával a beérkezési API lehetővé teszi ezen bevételezés-feldolgozási forgatókönyvek és az adatok kinyerését a visszaigazolásokból, például a kereskedelmi név, a tipp, a Total, a line Item és egyebek. Ezzel az API-val nincs szükség a modell betanítására, csak küldje el a nyugtát az elemzés visszaigazolási API-nak, és kinyeri az adatgyűjtést.

![minta nyugtája](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>Mit tesz a beérkezési API? 

Az előre létrehozott beérkezési API kibontja az értékesítési nyugták tartalmát &mdash; , amelyet általában egy étteremben, a kereskedőn vagy egy élelmiszerboltban szereztek be.

### <a name="fields-extracted"></a>Kinyert mezők

* Kereskedő neve 
* Kereskedelmi címe 
* Kereskedő telefonszáma 
* Tranzakció dátuma 
* Tranzakciós idő 
* Részösszeg 
* Adó 
* Összesen 
* Tipp 
* Sorok kinyerése (például cikkmennyiség, elem ára, elem neve)

### <a name="additional-features"></a>További funkciók

A beérkezési API a következő információkat is megadja:

* Bevételezés típusa (például a részletezett, a bankkártya stb.)
* Mező megbízhatósági szintje (minden mező a hozzá tartozó megbízhatósági értéket adja vissza)
* OCR nyers szöveg (OCR – kinyert szöveges kimenet a teljes bevételezéshez)
* Az egyes értékek, sorok és szavak határoló mezője

## <a name="input-requirements"></a>Bemeneti követelmények

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Támogatott területi beállítások 

* Az előre összeállított **beérkezési v 2.0** (GA) támogatja a Sales nyugtákat az en-us területi beállításban
* A **Pre-Builder v 2.1 – preview. 1** (nyilvános előzetes verzió) további támogatást nyújt a következő en-bevételezési területi beállításokhoz: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Nyelvi bevitel 
  >
  > Az előre összeállított nyugták v 2.1 – preview. 1 egy opcionális kérési paraméterrel megadhatja a további angol piacoktól érkező bevételezési területi beállításokat. Az ausztrál (EN-AU), Kanada (EN-CA), Nagy-Britannia (EN-GB) és India (en-IN) típusú értékesítési nyugták esetében megadhatja a területi beállításokat a jobb eredmények eléréséhez. Ha nincs megadva területi beállítás a v 2.1-ben – előzetes verzió. 1, a modell alapértelmezett értéke az EN-US modell lesz.


## <a name="the-analyze-receipt-operation"></a>A Bevételezés elemzése művelet

Az [elemzés visszaigazolása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeReceiptAsync) egy nyugtát tartalmazó képet vagy PDF-fájlt vesz fel bemenetként, és Kinyeri a kamat és a szöveg értékeit. A hívás egy válasz fejléc nevű mezőt ad vissza `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó eredmény-azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Művelet – hely | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>Az elemzés visszaigazolási eredményének lekérése művelet

A második lépés az [elemzés visszaigazolási eredményének beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeReceiptResult) művelet hívása. Ez a művelet az elemzés-visszaigazolási művelettel létrehozott eredmény-azonosítót veszi figyelembe. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A kérések másodpercenkénti (RPS) arányának meghaladása érdekében használjon 3 – 5 másodperces intervallumot.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: az elemzési művelet nem indult el. |
| |  | fut: az elemzési művelet folyamatban van. |
| |  | sikertelen: az elemzési művelet meghiúsult. |
| |  | sikeres: az elemzési művelet sikeresen befejeződött. |

Ha az **állapot** mező értéke **sikeres** , a JSON-válasz tartalmazni fogja a bevételezési és a szöveges felismerési eredményeket. A nyugta megértésének eredménye a megnevezett mezőértékek szótára lesz, ahol minden érték tartalmazza a kinyert szöveget, normalizált értéket, a határolókeret, a megbízhatóság és a hozzá tartozó szó elemeit. A szöveg-felismerés eredményét vonalak és szavak hierarchiája rendezi, szöveg, határolókeret és bizalmas adatok formájában.

![Példa a Bevételezés eredményeire](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Példa JSON-kimenetre

Tekintse meg a sikeres JSON-válasz következő példáját: a "readResults" csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. A "documentResults" csomópont a modell által felderített névjegykártya-specifikus értékeket tartalmazza. Itt talál hasznos kulcs/érték párokat, mint például az utónév, a vezetéknév, a vállalat neve és még sok más.

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

Mivel a beérkezési API egyszerű JSON-kimenettel rendelkezik, több módon is használhatja a kinyert mezők értékeit. Integrálja a költségeket a belső költségekkel rendelkező alkalmazásokkal a költségelszámolás előzetes kitöltéséhez. Ennél a forgatókönyvnél további információért olvassa el, hogy a Acumatica hogyan használja a beérkezési API-t, hogy [a költségek kevésbé fájdalmas folyamaton](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure)alapulnak.  

### <a name="auditing-and-accounting"></a>Naplózás és nyilvántartás 

A beérkezési API kimenete nagy számú, a költség-jelentési és a költségtérítési folyamat különböző pontjain elvégezhető kiadások elemzésére is használható. A visszaigazolásokat feldolgozhatja manuális audit vagy gyors jóváhagyások osztályozásához.  

A nyugta kimenete is hasznos lehet az üzleti vagy személyes használatra való általános könyveléshez. A beérkezési API használatával bármilyen nyers beérkezési képet vagy PDF-adatokat átalakíthat egy végrehajtható digitális kimenetbe.

### <a name="consumer-behavior"></a>Fogyasztói viselkedés 

A nyugták hasznos információkat tartalmaznak, amelyek segítségével elemezheti a fogyasztói viselkedést és a vásárlási trendeket.

A beérkezési API a [AIBuilder-visszaigazolás feldolgozási funkcióját](https://docs.microsoft.com/ai-builder/prebuilt-receipt-processing)is felhasználja.

## <a name="next-steps"></a>Következő lépések

- Töltse ki az [űrlap-felismerő ügyféloldali függvénytárának](quickstarts/client-library.md) rövid útmutatóját, hogy megkezdje a nyugták feldolgozását a választott nyelven az űrlap-felismerővel.
- Vagy kövesse a [beérkezési API Python](./quickstarts/python-receipts.md) rövid útmutatóját a visszaigazolások felismeréséhez a REST API használatával.

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](./overview.md)
* [REST API dokumentációs dokumentumok](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer/api)
