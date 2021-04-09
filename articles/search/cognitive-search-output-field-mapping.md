---
title: Bemenet hozzárendelése a kimeneti mezőkhöz
titleSuffix: Azure Cognitive Search
description: Kinyerheti és gazdagíthatja a forrásadatok mezőit, és leképezheti a kimeneti mezőket egy Azure Cognitive Search indexben.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001303"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI-gazdagított mezők leképezése kereshető indexre

![Indexelő szakaszai](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "indexelő szakaszai")

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a bővíthető beviteli mezőket egy kereshető index kimeneti mezőihez. Miután [meghatározta a készségkészlet](cognitive-search-defining-skillset.md), le kell képeznie minden olyan képesség kimeneti mezőjét, amely közvetlenül járul hozzá az értékekhez a keresési index egy adott mezőjében.

A bővített dokumentumokból származó tartalomnak az indexbe való áthelyezéséhez kimeneti mezők hozzárendelése szükséges.  A dúsított dokumentum valójában az információk fája, és bár az index összetett típusai is támogatottak, időnként előfordulhat, hogy a dúsított fában lévő adatokat egy egyszerű típusra kívánja átalakítani (például karakterláncok tömbje). A kimeneti mezők leképezése lehetővé teszi az adatalakzatok átalakításának elvégzését az információk összeolvasztásával. A kimeneti mezők leképezése mindig a készségkészlet végrehajtása után következik be, bár lehetséges, hogy ez a szakasz akkor is futtatható, ha nincs készségkészlet definiálva.

Példák a kimeneti mezők leképezésére:

* A készségkészlet részeként kibontotta a dokumentum egyes oldalain említett szervezetek nevét. Most le szeretné képezni ezeket a szervezeti neveket a EDM. Collection (EDM. String) típusú index egyik mezőjébe.

* A készségkészlet részeként létrehozott egy új, "Document/translated_text" nevű csomópontot. Azt szeretné, hogy a csomóponton lévő információk az index egy adott mezőjére legyenek leképezve.

* Nem rendelkezik készségkészlet, de egy összetett típust indexel egy Cosmos DB adatbázisból. Ehhez az összetett típushoz szeretne egy csomópontot beolvasni, és az index egy mezőjébe szeretné leképezni.

> [!NOTE]
> Nemrég engedélyezte a leképezési függvények funkcióit a kimeneti mezők hozzárendelésein. További részletek a leképezési függvényekről: [mező-hozzárendelési függvények](./search-indexer-field-mappings.md#field-mapping-functions)

## <a name="use-outputfieldmappings"></a>OutputFieldMappings használata

A mezők leképezéséhez adja hozzá `outputFieldMappings` az indexelő definícióját az alábbi ábrán látható módon:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

A kérelem törzse a következőképpen van strukturálva:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Minden egyes kimeneti mező leképezéséhez állítsa be a dúsított dokumentum fájában (sourceFieldName) található adatokat, valamint a mező nevét az indexben hivatkozott módon (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Információk összeolvasztása összetett típusokból 

Egy sourceFieldName elérési útja egy vagy több elemet is jelenthet. A fenti példában ```/document/content/sentiment``` egyetlen numerikus érték szerepel, míg a ```/document/content/organizations/*/description``` több szervezet leírását jelöli. 

Azokban az esetekben, ahol több elem is van, azok egy tömbbe kerülnek, amely tartalmazza az egyes elemeket. 

A fenti ```/document/content/organizations/*/description``` példában a *leírások* mezőben szereplő információ a leírások egy lapos tömbje lesz, az indexelés előtt:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Ez fontos elv, ezért egy másik példát is biztosítunk. Képzelje el, hogy az összetett típusok tömbje a dúsítási fa része. Tegyük fel, hogy van egy customEntities nevű tag, amely az alább leírtak szerint összetett típusokat tartalmaz.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Tegyük fel, hogy az indexnek van egy "diseases" nevű mezője (EDM. String), ahol az entitások nevét tárolni szeretné. 

Ezt egyszerűen megteheti a " \* " szimbólum használatával, a következőképpen:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Ez a művelet egyszerűen "lelapul" a customEntities elemek mindegyikének egyetlen tömbje a következőhöz hasonlóan:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Következő lépések
Miután leképezte a dúsított mezőket a kereshető mezőkre, az [index definíciójának részeként](search-what-is-an-index.md)megadhatja az egyes kereshető mezőkhöz tartozó mezőtulajdonságokat.

További információ a mezők hozzárendeléséről: [mező-hozzárendelések az Azure Cognitive Search indexelő](search-indexer-field-mappings.md)szolgáltatásban.