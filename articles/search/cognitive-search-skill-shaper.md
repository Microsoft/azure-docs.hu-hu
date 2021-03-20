---
title: Formázó kognitív képesség
titleSuffix: Azure Cognitive Search
description: Metaadatok és strukturált adatok kinyerése strukturálatlan adatokból, és az Azure Cognitive Search mesterséges intelligencia-dúsítási folyamata összetett típusként.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 81eb0e60befc544a6c3bee8f04e901b6a5e472bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85560822"
---
# <a name="shaper-cognitive-skill"></a>Formázó kognitív képesség

A **formáló** képesség több bemenetet egyesít egy [összetett típusba](search-howto-complex-data-types.md) , amelyet később a dúsítási folyamat során lehet hivatkozni. A **formáló** képességgel lényegében létrehozhat egy struktúrát, megadhatja a struktúra tagjainak nevét, és értékeket rendelhet hozzájuk az egyes tagokhoz. Példák a keresési forgatókönyvekben hasznos konszolidált mezőkre: az első és az utolsó név egyetlen struktúrába, városba és állapotba való egyesítése egyetlen struktúrába, vagy név és születésnap egyetlen struktúrában egyedi identitás létrehozásához.

Emellett a [3. forgatókönyvben](#nested-complex-types) bemutatott **formáló** képesség egy opcionális *sourceContext* tulajdonságot is feltesz a bemenetbe. A *forrás* -és *sourceContext* tulajdonságok kölcsönösen kizárják egymást. Ha a bemenet a szakértelem kontextusában van, egyszerűen használja a *forrást*. Ha a bemenet a szakértelem kontextusa szerint *eltérő* kontextusban van, használja a *sourceContext*. A *sourceContext* meg kell adnia egy beágyazott bemenetet a forrásként kezelendő adott elemmel. 

A kimeneti név mindig "output". Belsőleg a folyamat egy másik nevet (például a "analyzedText") is leképezheti az alábbi példákban látható módon, de a **shapeer** -képesség maga "output" értéket ad vissza a válaszban. Ez akkor lehet fontos, ha a dúsított dokumentumokat észleli, és az elnevezési eltérést észleli, vagy ha egyéni képességet hoz létre, és saját maga strukturálja a választ.

> [!NOTE]
> A **formáló** képesség nem kötődik Cognitive Services API-hoz, és nem kell fizetnie a használatért. Továbbra is [csatlakoztatnia kell egy Cognitive Services-erőforrást](cognitive-search-attach-cognitive-services.md), hogy felülírja az **ingyenes** erőforrás-beállítást, amely naponta csak kis mennyiségű napi dúsítást korlátozza.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. util. ShaperSkill

## <a name="scenario-1-complex-types"></a>1. forgatókönyv: összetett típusok

Vegyünk egy olyan forgatókönyvet, amelyben létre szeretne hozni egy *analyzedText* nevű struktúrát, amelynek két tagja van: *szöveg* és *hangulat*. Egy indexben egy többrészes kereshető mező *összetett típusú* , és gyakran jön létre, ha a forrásadatok olyan összetett szerkezettel rendelkeznek, amely leképezi azt.

Az összetett típusok létrehozásának másik megközelítése azonban a **shapeer** -képességen keresztül történik. Ennek a képességnek a készségkészlet való belefoglalásával a készségkészlet-feldolgozás során a memóriában lévő műveletek az adatalakzatokat beágyazott struktúrákkal is kihasználhatják, amelyek ezután az index összetett típusára képezhetők le. 

A következő példa a szaktudás megadása bemenetként adja meg a tagok nevét. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Minta index

A készségkészlet egy indexelő hívja meg, és az indexelő megköveteli az indexet. Az index összetett mezője az alábbi példához hasonlóan jelenhet meg. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Szaktudás bemenete

Egy bejövő JSON-dokumentum, amely felhasználható bemenetet biztosít ehhez az **alakzathoz** , a következő lehet:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Szaktudás kimenete

A **formáló** képesség egy új, *analyzedText* nevű elemet hoz létre, amely a *szöveg* és a *hangulat* együttes elemeit tartalmazza. Ez a kimenet megfelel az index sémájának. A rendszer egy Azure Cognitive Search indexbe importálja és indexeli.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>2. forgatókönyv: bemeneti konszolidáció

Egy másik példában Képzelje el, hogy a folyamat feldolgozásának különböző szakaszaiban kibontotta a könyv címét, és a könyv különböző oldalain található fejezetek címeit. Most már létrehozhat egyetlen, a különböző bemenetből álló struktúrát.

Az ehhez a forgatókönyvhöz tartozó **formáló** képesség definíciója a következő példához hasonló lehet:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Szaktudás kimenete
Ebben az esetben a **Shape** az összes fejezeti címet lelapul egyetlen tömb létrehozásához. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>3. forgatókönyv: beágyazott környezetek bemenetének összevonása

Képzelje el, hogy rendelkezik egy könyv címével, fejezeteivel és tartalmával, és futtatja az entitások felismerését és a kulcsfontosságú kifejezéseket a tartalomban, és most a különböző ismeretekből származó eredményeket kell összevonni egyetlen alakzatba a fejezet neve, az entitások és a legfontosabb kifejezések használatával.

Az ehhez a forgatókönyvhöz tartozó **formáló** képesség definíciója a következő példához hasonló lehet:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Szaktudás kimenete
Ebben az esetben az **alakzat** összetett típust hoz létre. Ez a struktúra a memóriában van. Ha egy [tudásbázisba](knowledge-store-concept-intro.md)szeretné menteni, hozzon létre egy leképezést a készségkészlet, amely meghatározza a tárolási tulajdonságokat.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Összetett típusok használata](search-howto-complex-data-types.md)
+ [Tudástár](knowledge-store-concept-intro.md)
+ [Hozzon létre egy Knowledge Store-t a REST-ben](knowledge-store-create-rest.md)