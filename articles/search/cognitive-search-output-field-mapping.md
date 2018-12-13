---
title: Térkép cognitive search bővített bemeneti mezők átalakítása a táblakimeneti mezők – Azure Search
description: Bontsa ki és forrás datová Pole bővítését, és az Azure Search-index táblakimeneti mezők leképezése.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a662558deeeb09849a23a361aabea962eb633cd7
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314669"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>A kereshető indexet hozva képi elemekben gazdag mezők leképezése

Ebből a cikkből megismerheti, hogyan hidaljuk adatbeviteli mezők leképezése egy kereshető indexet a táblakimeneti mezők. Miután [egy indexmezők definiált](cognitive-search-defining-skillset.md), le kell képeznie a táblakimeneti mezők bármilyen szaktudás értékeket egy adott mezőt a keresési index közvetlenül kidolgozásában. Mezőleképezések is szükséges a tartalmak áthelyezését képi elemekben gazdag dokumentumok az indexbe.


## <a name="use-outputfieldmappings"></a>OutputFieldMappings használata
Mezők leképezése hozzá `outputFieldMappings` , az indexelő definíciója az alább látható módon:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

A kérelem törzsében a következőképpen épül:

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
            "targetFieldName": "descriptions"
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
Az egyes kimeneti mezőleképezés állítja be a képi elemekben gazdag (sourceFieldName) mező nevét, és a mező nevét az indexben (targetFieldName) hivatkozott.

Az elérési utat egy sourceFieldName a elem egy vagy több olyan elemet hozhat létre. A fentiek példában ```/document/content/sentiment``` jelöli egyetlen numerikus érték, miközben ```/document/content/organizations/*/description``` számos szervezet leírások jelöli. Azokban az esetekben, ahol több elemből, hogy vannak "simítva" egy tömb, amely tartalmazza a elemek. Több konkrétan az a ```/document/content/organizations/*/description``` például, az adatok a *leírások* mező néznek leírások egybesimított tömbje indexelés előtt:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>További lépések
A képi elemekben gazdag mező kereshető mezők leképezése után beállíthatja a kereshető mezők mindegyike a Mezőtulajdonságok [az index definícióját részeként](search-what-is-an-index.md).

Mezőleképezés kapcsolatos további információkért lásd: [az Azure Search indexelők Mezőleképezéseivel](search-indexer-field-mappings.md).