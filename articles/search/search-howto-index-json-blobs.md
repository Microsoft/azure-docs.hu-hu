---
title: Keresés JSON-blobokon
titleSuffix: Azure Cognitive Search
description: Azure JSON-Blobok bejárása a szöveges tartalomhoz az Azure Cognitive Search blob indexelő használatával. Az indexelő automatizálja az adatfeldolgozást a kiválasztott adatforrásokhoz, például az Azure Blob Storage-hoz.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259050"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>JSON-Blobok indexelése blob indexelő használatával az Azure-ban Cognitive Search

Ebből a cikkből megtudhatja, hogyan [konfigurálhat egy blob-indexelő](search-howto-indexing-azure-blob-storage.md) a JSON-dokumentumokból álló Blobok számára. Az Azure Blob Storage-beli JSON-Blobok általában az alábbi űrlapokat feltételezik:

+ Egyetlen JSON-dokumentum
+ Jól formázott JSON-elemek tömbjét tartalmazó JSON-dokumentum
+ Több entitást tartalmazó JSON-dokumentum, amely egy sortöréssel elválasztva

A blob indexelő egy **`parsingMode`** paramétert biztosít a keresési dokumentum kimenetének optimalizálásához a szerkezet-elemzési üzemmódok alapján a következő lehetőségek közül:

| parsingMode | JSON-dokumentum | Description |
|--------------|-------------|--------------|
| **`json`** | Egy blob | alapértelmezett A JSON-blobokat egyetlen darab szövegként elemzi. Minden JSON-blob egyetlen keresési dokumentum lesz. |
| **`jsonArray`** | Több blobon | Egy JSON-tömböt elemez a blobban, ahol a tömb minden eleme külön keresési dokumentum lesz.  |
| **`jsonLines`** | Több blobon | Egy olyan blobot elemez, amely több JSON-entitást (tömböt is) tartalmaz, és az egyes elemeket sortöréssel választja el egymástól. Az indexelő egy új keresési dokumentumot indít el az egyes új sorok után. |

Mindkét **`jsonArray`** és **`jsonLines`** esetében érdemes áttekintenie [egy blob indexelését, hogy számos keresési dokumentumot hozzon létre](search-howto-index-one-to-many-blobs.md) annak megismeréséhez, hogy a blob indexelő hogyan kezelje a dokumentum kulcsát az ugyanabból a blobból létrehozott több keresési dokumentum esetében.

Az indexelő definíciójában opcionálisan beállíthatja a [mezők leképezését](search-indexer-field-mappings.md) , hogy kiválassza, hogy a forrás JSON-dokumentum mely tulajdonságait használja a rendszer a cél keresési indexének feltöltéséhez. Például az elemzési mód használata esetén **`jsonArray`** , ha a tömb alacsonyabb szintű tulajdonságként létezik, beállíthat egy **`document root`** tulajdonságot, amely azt jelzi, hogy a tömb hová kerül a blobon belül.

Az alábbi szakaszok részletesebben ismertetik az egyes módokat. Ha nem ismeri az indexelő-ügyfeleket és a fogalmakat, tekintse meg [a keresési indexelő létrehozása](search-howto-create-indexers.md)című témakört. Ismernie kell az [alapszintű blob indexelő konfigurációjának](search-howto-indexing-azure-blob-storage.md)részleteit is, ami itt nem ismétlődik.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Egyetlen JSON-dokumentum indexelése (egy blobon)

Alapértelmezés szerint a blob-indexek a JSON-blobokat egyetlen szövegrészként elemezik, egy keresési dokumentumot a tárolóban lévő összes blobhoz. Ha a JSON strukturált, a keresési dokumentum tükrözheti ezt a struktúrát, és az egyes elemek egyéni mezőkként jelennek meg. Tegyük fel például, hogy rendelkezik a következő JSON-dokumentummal az Azure Blob Storage-ban:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

A blob indexelő a JSON-dokumentumot egyetlen keresési dokumentumba elemzi, a "text", a "datePublished" és a "Tags" kifejezéssel egyező nevű indexet tölt be a forrásból az azonos névvel ellátott és beírt cél index mezőkkel. A "text", "datePublished" és "Tags" mezőkkel rendelkező indexek esetében a blob indexelő a kérelemben található mező-hozzárendelés nélkül következtetheti ki a megfelelő leképezést.

Bár az alapértelmezett viselkedés egy JSON-blobban található keresési dokumentum, a "JSON" elemzési mód beállítása módosítja a tartalom belső mezőkhöz való leképezését, és `content` a keresési index tényleges mezőin belüli mezőket támogatja. Az elemzési mód indexelő definíciója az **`json`** alábbihoz hasonló lehet:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Ahogy az összes indexelő esetében, ha a mezők nem felelnek meg egyértelműen, érdemes explicit módon megadnia az egyes [mezők leképezéseit](search-indexer-field-mappings.md) , hacsak nem a blob-tartalomhoz és a metaadatokhoz elérhető implicit mezők leképezéseit használja az [alapszintű blob indexelő konfigurálása](search-howto-indexing-azure-blob-storage.md)című cikkben leírtak szerint.

### <a name="json-example-single-hotel-json-files"></a>JSON-példa (egyetlen Hotel JSON-fájl)

A JSON-dokumentum a GitHubon [tárolt adatkészlete](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) hasznos a JSON-elemzések teszteléséhez, ahol minden blob egy strukturált JSON-fájlt képvisel. Feltöltheti az adatfájlokat a blob Storage-ba, és az **adatok importálása** varázslóval gyorsan kiértékelheti, hogy a tartalom hogyan legyen elemezve az egyes keresési dokumentumokban. 

Az adathalmaz öt blobból áll, amelyek mindegyike tartalmaz egy olyan szállodai dokumentumot, amelyben egy címterület és egy szoba gyűjtemény található. A blob indexelő észleli mindkét gyűjteményt, és tükrözi a bemeneti dokumentumok szerkezetét az index sémában.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON-tömbök elemzése

Azt is megteheti, hogy a JSON Array kapcsolót használja. Ez a beállítás akkor hasznos, ha a Blobok jól formázott JSON-objektumokat tartalmazó tömböt tartalmaznak, és azt szeretné, hogy minden elem külön keresési dokumentum legyen. **`jsonArrays`** A használatával a következő JSON-blob három különálló dokumentumot hoz létre, amelyek mindegyike `"id"` és `"text"` mezője.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

Az **`parameters`** Indexelő tulajdonsága az elemzési mód értékeit tartalmazza. JSON-tömb esetén az indexelő definíciójának az alábbi példához hasonlóan kell kinéznie.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>jsonArrays-példa (klinikai vizsgálatok mintaadatok)

A GitHubon a [klinikai vizsgálatok JSON-adatkészlete](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) hasznos a JSON-tömb elemzésének teszteléséhez. Feltöltheti az adatfájlokat a blob Storage-ba, és az **adatok importálása** varázslóval gyorsan kiértékelheti, hogy a tartalom hogyan legyen elemezve az egyes keresési dokumentumokban. 

Az adatkészlet nyolc blobból áll, amelyek mindegyike az entitások JSON-tömbjét tartalmazza, és összesen 100 entitást tartalmaz. Az entitások attól függően változnak, hogy mely mezők legyenek feltöltve, de a végeredmény egyetlen keresési dokumentum, amely az összes tömbben, minden tömbben, a teljes blobban szerepel.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Beágyazott JSON-tömbök elemzése

A beágyazott elemeket tartalmazó JSON-tömbök esetében megadható, **`documentRoot`** hogy többszintű struktúrát jelezzen. Ha például a Blobok így néznek ki:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Ezzel a konfigurációval indexelheti a tulajdonságban található tömböt `level2` :

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>JSON-entitások értelmezése a Sortöréssel elválasztva

Ha a blob egy sortöréssel elválasztott több JSON-entitást tartalmaz, és azt szeretné, hogy minden elem külön keresési dokumentum legyen, használja a következőt: **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

A JSON-sorok esetében az indexelő definíciójának az alábbi példához hasonlóan kell kinéznie.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>jsonLines példa (caselaw-mintaadatok)

A GitHubon a [CASELAW JSON-adatkészlete](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) hasznos a JSON új vonal-elemzés teszteléséhez. Más mintákhoz hasonlóan a blob Storage-ba is feltöltheti ezeket az adatokkal, és az **adatok importálása** varázslóval gyorsan kiértékelheti, hogy milyen hatással van az elemzési mód az egyes blobokra.

Az adathalmaz egy olyan blobot tartalmaz, amely 10 JSON-entitást tartalmaz egy új sorral elválasztva, ahol minden entitás egyetlen jogi esetet ismertet. A végeredmény egyetlen keresési dokumentum az egyes entitásokban.

## <a name="map-json-fields-to-search-fields"></a>JSON-mezők leképezése keresési mezőkre

A mező-hozzárendelések a forrás mező egy célhelyhez való társítására szolgálnak olyan helyzetekben, amikor a mezők nevei és típusai nem azonosak. A mező-hozzárendelések azonban a JSON-dokumentumok részeinek egyeztetésére is használhatók, és a keresési dokumentum legfelső szintű mezőibe "emelheti" azokat.

A következő példa szemlélteti ezt a forgatókönyvet. A mezők hozzárendelésével kapcsolatos további információkért lásd: [mezők leképezése](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Tegyük fel, hogy a keresési index a következő mezőkkel rendelkezik: típus, típusú `text` `Edm.String` és típusú `date` `Edm.DateTimeOffset` `tags` `Collection(Edm.String)` . Figyelje meg a "datePublished" közötti eltérést az index forrásában és `date` mezőjében. A JSON a kívánt alakzattá való leképezéséhez használja a következő mező-hozzárendeléseket:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

A forrás mezők a JSON- [mutató](https://tools.ietf.org/html/rfc6901) jelölésének használatával vannak megadva. A továbbítási perjelet a JSON-dokumentum gyökerére való hivatkozással kezdheti meg, majd kiválaszthatja a kívánt tulajdonságot (tetszőleges szintű beágyazás esetén) a perjelek közötti kétirányú útvonal használatával.

Az egyes tömb elemeit nulla alapú index használatával is megtekintheti. Ha például a fenti példában szereplő "címkék" tömb első elemét szeretné kiválasztani, használja a következőhöz hasonló mezőt:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Ha **`sourceFieldName`** olyan tulajdonságra hivatkozik, amely nem szerepel a JSON-blobban, a leképezés hiba nélkül kimarad. Ez a viselkedés lehetővé teszi, hogy az indexelés folytassa a más sémával rendelkező JSON-Blobok (gyakori használati eset) folytatását. Mivel nincs érvényesítési ellenőrzés, gondosan tekintse meg a leképezéseket, hogy ne veszítse el a dokumentumokat a megfelelő okból.
>

## <a name="next-steps"></a>Következő lépések

+ [BLOB-indexek konfigurálása](search-howto-indexing-azure-blob-storage.md)
+ [Mező-hozzárendelések definiálása](search-indexer-field-mappings.md)
+ [Az indexelők áttekintése](search-indexer-overview.md)
+ [CSV-Blobok indexelése blob-indexelő használatával](search-howto-index-csv-blobs.md)
+ [Oktatóanyag: részben strukturált adatok keresése az Azure Blob Storage-ból](search-semi-structured-data.md)