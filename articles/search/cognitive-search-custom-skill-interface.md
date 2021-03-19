---
title: Az egyéni képességek kezelőfelületi definíciója
titleSuffix: Azure Cognitive Search
description: Egyéni adatkiemelési felület a webes API-k egyéni képességeihez az Azure Cognitive Search mesterséges intelligencia-gazdagító folyamatában.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e78f0d1e8d6d637dfebe1ff475ab8416ba49a263
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935415"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Egyéni képesség hozzáadása Azure Cognitive Search-bővítési folyamathoz

> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=172&end=221]

Az Azure Cognitive Search bővítési [folyamata](cognitive-search-concept-intro.md) a [beépített kognitív képességekből](cognitive-search-predefined-skills.md) , valamint a folyamathoz személyesen létrehozott és hozzáadott [Egyéni képességekkel](cognitive-search-custom-skill-web-api.md) is összeállítható. Ebből a cikkből megtudhatja, hogyan hozhat létre olyan egyéni képességet, amely egy olyan felületet tesz elérhetővé, amely lehetővé teszi, hogy egy AI-dúsítási folyamat része legyen. 

Az egyéni képességek létrehozása lehetővé teszi, hogy a tartalom egyedi átalakításokat helyezzen be. Az egyéni képességek egymástól függetlenül hajthatók végre, a szükséges alkoholtartalom-növelési lépés alkalmazásával. Meghatározhatja például a mezőkre vonatkozó egyéni entitásokat, egyéni besorolási modelleket hozhat létre az üzleti és pénzügyi szerződések és dokumentumok megkülönböztetéséhez, vagy hozzáadhat egy beszédfelismerési képességet a hangfájlok mélyebb eléréséhez a releváns tartalmakhoz. Lépésről lépésre példaként tekintse meg a következő [példát: egyéni képesség létrehozása AI](cognitive-search-create-custom-skill-example.md)-bővítéshez.

 Bármilyen egyéni képességre van szükség, van egy egyszerű és egyértelmű felület, amellyel egy egyéni képességet csatlakoztathat a többi bővítési folyamathoz. A [készségkészlet](cognitive-search-defining-skillset.md) való felvétel egyetlen követelménye az, hogy képes legyen a bemenetek fogadására és kimenetek kibocsátására olyan módokon, amelyek a készségkészlet belül a teljes használatra készek. Ennek a cikknek a célja a dúsítási folyamat által igényelt bemeneti és kimeneti formátum.

## <a name="web-api-custom-skill-interface"></a>Webes API egyéni ügyességi felülete

Egyéni WebAPI-képzettségi végpontok alapértelmezett időtúllépéssel, ha nem ad vissza választ egy 30 másodperces ablakban. Az indexelési folyamat szinkronban van, és az indexelés időtúllépési hibát eredményez, ha az adott ablakban nem érkezik válasz.  Az időtúllépési paraméter beállításával akár 230 másodperc is beállítható:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Győződjön meg arról, hogy az URI biztonságos (HTTPS).

Jelenleg az egyéni képességekkel való interakció egyetlen mechanizmusa egy webes API-felületen keresztül történik. A webes API-nak meg kell felelnie az ebben a részben ismertetett követelményeknek.

### <a name="1--web-api-input-format"></a>1. webes API bemeneti formátuma


> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=294&end=340]


A webes API-nak el kell fogadnia a feldolgozandó rekordok tömbjét. Minden rekordnak tartalmaznia kell egy "tulajdonság táska" karaktert, amely a webes API-hoz megadott bemenet. 

Tegyük fel, hogy létre szeretne hozni egy egyszerű gazdagító, amely azonosítja a szerződés szövegében említett első dátumot. Ebben a példában a skill egyetlen bemeneti *contractText* fogad el a szerződés szövegeként. A szakértelem egyetlen kimenettel is rendelkezik, amely a szerződés dátuma. Ahhoz, hogy a gazdagodás még érdekesebb legyen, ezt a *contractDate* egy többrészes összetett típus alakjában adja vissza.

A webes API-nak készen kell állnia a bemeneti rekordok kötegének fogadására. Az *Values* tömb minden tagja az adott rekord bemenetét jelöli. Minden rekordhoz a következő elemek szükségesek:

+ Egy *recordId* -tag, amely egy adott rekord egyedi azonosítója. Ha a dúsítás visszaadja az eredményeket, meg kell adnia ezt a *recordId* ahhoz, hogy a hívó megegyezzen a rekord eredményével a bemeneten.

+ Egy *adattag,* amely lényegében egy, az egyes rekordok beviteli mezőinek egy zacskója.

A fenti példa alapján a webes API-nak a következőhöz hasonló kérelmeket kell várnia:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
A valóságban a szolgáltatás több száz vagy több ezer rekorddal is meghívható, és nem csak az itt látható háromat.

### <a name="2-web-api-output-format"></a>2. webes API kimeneti formátuma

A kimenet formátuma egy *recordId* tartalmazó rekordok halmaza, és egy tulajdonság táska 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Ebben a példában csak egy kimenet szerepel, de egynél több tulajdonságot is ki lehet exportálni. 

### <a name="errors-and-warning"></a>Hibák és figyelmeztetés

Ahogy az előző példában is látható, hibaüzeneteket és figyelmeztető üzeneteket adhat vissza az egyes rekordokhoz.

## <a name="consuming-custom-skills-from-skillset"></a>A készségkészlet egyéni képességeinek felhasználása

Webes API-gazdagító létrehozásakor a kérés részeként a HTTP-fejléceket és a paramétereket is leírhatja. Az alábbi kódrészlet azt mutatja be, hogyan lehet lekérdezni a kérelmek paramétereit és a *választható* HTTP-fejléceket a készségkészlet definíciójának részeként. A HTTP-fejlécek nem kötelezőek, de lehetővé teszik további konfigurációs képességek hozzáadását a szakértelemhez, valamint a készségkészlet-definícióból való beállítását.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk az egyéni képességek készségkészlet való integrálásához szükséges interfész-követelményeket tárgyalja. Az alábbi hivatkozásokra kattintva további információkat tudhat meg az egyéni képességekről és a készségkészlet-összeállításról.

+ [Tekintse meg a videót az egyéni képességekről](https://youtu.be/fHLCE-NZeb4)
+ [Energiaellátási készségek: az egyéni képességek tárháza](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Példa: egyéni képesség létrehozása AI-bővítéshez](cognitive-search-create-custom-skill-example.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Készségkészlet létrehozása (REST)](/rest/api/searchservice/create-skillset)
+ [A dúsított mezők leképezése](cognitive-search-output-field-mapping.md)