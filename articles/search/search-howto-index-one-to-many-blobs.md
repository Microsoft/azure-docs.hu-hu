---
title: Több dokumentumot tartalmazó Blobok indexelése
titleSuffix: Azure Cognitive Search
description: Azure-Blobok bejárása a szöveges tartalomhoz az Azure Cognitive Search blob indexelő használatával, ahol minden blob egy vagy több keresési indexbeli dokumentumot eredményezhet.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430980"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Blobok indexelése több keresési dokumentum létrehozásához

Alapértelmezés szerint a blob indexelő egyetlen keresési dokumentumként fogja kezelni a blob tartalmát. Ha a blob részletesebb megjelenítését szeretné egy keresési indexben, beállíthatja, hogy a **parsingMode** -értékek több keresési dokumentumot hozzanak létre egy blobból. A sok keresési dokumentumot eredményező **parsingMode** értékek `delimitedText` ( [CSV](search-howto-index-csv-blobs.md)) és `jsonArray` vagy `jsonLines` ( [JSON](search-howto-index-json-blobs.md)esetén).

Ha ezeket az elemzési módokat használja, a megjelenő új keresési dokumentumoknak egyedi dokumentum-kulcsokkal kell rendelkezniük, és probléma merül fel annak megállapítása során, hogy az adott érték honnan származik. A szülő blob legalább egy egyedi értéket tartalmaz a (z) formában `metadata_storage_path property` , de ha az érték több keresési dokumentumhoz is hozzájárul, a kulcs már nem egyedi az indexben.

A probléma megoldása érdekében a blob-indexelő létrehoz egy `AzureSearch_DocumentKey` , az egyetlen blob szülőből létrehozott alárendelt keresési dokumentumok egyedi azonosításával. Ez a cikk bemutatja, hogyan működik ez a funkció.

## <a name="one-to-many-document-key"></a>Egy-a-többhöz dokumentum kulcsa

Az Azure Cognitive Search indexben megjelenített minden dokumentumot egyedileg azonosít egy dokumentum kulcsa. 

Ha nincs megadva elemzési mód, és ha nincs [explicit mező-hozzárendelés](search-indexer-field-mappings.md) a keresési dokumentum kulcsához tartozó indexelő definícióban, a blob indexelő automatikusan leképezi a `metadata_storage_path property` dokumentum kulcsát. Ez a leképezés biztosítja, hogy az egyes Blobok külön keresési dokumentumként jelenjenek meg, és elmenti Önnek a mező leképezésének létrehozását (általában csak azonos névvel és típusokkal rendelkező mezőket automatikusan leképezi).

A fent felsorolt elemzési módok bármelyikének használatakor az egyik blob a "sok" keresési dokumentumra mutat, így a dokumentum kulcsa kizárólag a blob metaadatainak alapján használható. Ennek a korlátozásnak a leküzdéséhez az Azure Cognitive Search képes a blobokból kinyert egyes entitások "egy a többhöz" dokumentum-kulcsának generálására. Ez a tulajdonság neve AzureSearch_DocumentKey, és hozzá lesz adva a blobból kinyert egyes entitásokhoz. Ennek a tulajdonságnak az értéke garantáltan egyedi a Blobok közötti egyes entitások esetében, és az entitások külön keresési dokumentumokként jelennek meg.

Alapértelmezés szerint, ha nincs megadva explicit mező a Key index mezőhöz, a a leképezése a `AzureSearch_DocumentKey` `base64Encode` mező leképezése függvény használatával történik.

## <a name="example"></a>Példa

Tegyük fel, hogy az index definíciója a következő mezőkkel rendelkezik:

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

És a blob-tároló Blobokkal rendelkezik a következő szerkezettel:

_Blob1.jsbekapcsolva_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.jsbekapcsolva_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

Amikor létrehoz egy indexelő, és beállítja a **parsingMode** -t a `jsonLines` kulcs mezőhöz tartozó explicit mező-hozzárendelések megadása nélkül, a következő leképezés implicit módon lesz alkalmazva.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

Ez a beállítás az alábbi ábrához hasonló disambiguated eredményez (a Base64 kódolású azonosító lerövidíti a rövid időpontot).

| ID (Azonosító) | hőmérséklet | nyomás | időbélyeg |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2020 – 02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2020 – 02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Egyéni mezők leképezése az index kulcsa mezőhöz

Feltételezve, hogy ugyanazt az index-definíciót az előző példában is látható, tegyük fel, hogy a blob-tároló blobokat tartalmaz a következő szerkezettel:

_Blob1.jsbekapcsolva_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.jsbekapcsolva_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

Ha a parsingMode-vel hoz létre indexelő `delimitedText` , természetesnek tűnhet, hogy egy mező-leképezési függvényt állítson be a kulcs mezőhöz a következőképpen:

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

Ez a leképezés azonban _nem_ eredményez 4 olyan dokumentumot, amely megjelenik az indexben, mert a `recordid` mező nem egyedi a _Blobok között_. Ezért javasoljuk, hogy a `AzureSearch_DocumentKey` tulajdonságból az "egy a többhöz" elemzési üzemmódhoz tartozó Key index mezőre alkalmazott implicit mező-leképezést használja.

Ha explicit mező-hozzárendelést szeretne beállítani, győződjön meg arról, hogy a _sourceField_ különbözik az egyes entitások **minden blobban**.

> [!NOTE]
> A `AzureSearch_DocumentKey` kinyert entitások egyediségének biztosításához használt megközelítés változhat, ezért nem szabad az alkalmazás igényeinek megfelelően használni.

## <a name="next-steps"></a>Következő lépések

Ha még nem ismeri a blob-indexelés alapszintű szerkezetét és munkafolyamatát, először tekintse át az Azure [blob Storage indexelését az azure Cognitive Search](search-howto-index-json-blobs.md) . A különböző blob-tartalomtípusok elemzési módjaival kapcsolatos további információkért tekintse át a következő cikkeket.

> [!div class="nextstepaction"]
> [CSV-Blobok](search-howto-index-csv-blobs.md) 
>  indexelése [JSON-Blobok indexelése](search-howto-index-json-blobs.md)
