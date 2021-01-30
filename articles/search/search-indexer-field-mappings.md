---
title: Mezőleképezések az indexelőkben
titleSuffix: Azure Cognitive Search
description: Az indexelő mező-hozzárendelések konfigurálása a mezőnevek és az adatábrázolások közötti különbségek kiszámításához.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: efee1e1cda7767620931ef81825708d94a1925c3
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063179"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mező-hozzárendelések és átalakítások az Azure Cognitive Search indexelő használatával

![Indexelő szakaszai](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "indexelő szakaszai")

Az Azure Cognitive Search indexelő használatakor előfordulhat, hogy a bemeneti adatok nem egyeznek meg a célként megadott index sémájával. Ezekben az esetekben a **mező-hozzárendelések** segítségével átalakíthatja az adatait az indexelési folyamat során.

Bizonyos helyzetekben hasznosak lehetnek a mezők leképezése:

* Az adatforrás egy nevű mezővel rendelkezik `_id` , de az Azure Cognitive Search nem engedélyezi a mezőneveket, amely aláhúzással kezdődik. A mezők leképezése lehetővé teszi egy mező tényleges átnevezését.
* Az indexben több mezőt is fel szeretne tölteni ugyanabból az adatforrásból származó adatokból. Előfordulhat például, hogy különböző elemzőket szeretne alkalmazni ezekre a mezőkre.
* Egynél több adatforrásból származó adatokkal szeretné feltölteni az index mezőt, és az egyes adatforrások különböző mezőneveket használnak.
* Az adatai Base64 kódolása vagy dekódolása szükséges. A mező-hozzárendelések több **leképezési funkciót** is támogatnak, beleértve a Base64 kódoláshoz és a dekódoláshoz használható függvényeket.

> [!NOTE]
> Az indexelő mezőkhöz tartozó leképezések egyszerű módot biztosítanak az adatmezők index mezőibe történő leképezésére, és némi képességgel rendelkeznek a könnyű adatátalakításra. Az összetettebb adatfeldolgozáshoz szükség lehet az előzetes feldolgozásra, hogy egy olyan űrlapra alakítsa át, amely az indexelést eredményezi. Lehetséges, hogy az egyik lehetőség a [Azure Data Factory](../data-factory/index.yml).

## <a name="set-up-field-mappings"></a>Mezők leképezésének beállítása

A mezők leképezése három részből áll:

1. A `sourceFieldName` , amely az adatforrás egy mezőjét jelöli. Ezt a tulajdonságot kötelező megadni.
2. Egy opcionális `targetFieldName` , amely a keresési index egyik mezőjét jelöli. Ha nincs megadva, a rendszer ugyanazt a nevet használja, mint az adatforrásban.
3. Opcionális `mappingFunction` , amely az adatok átalakítását számos előre definiált függvény használatával végezheti el. Ez a bemeneti és a kimeneti mezők hozzárendelésein is alkalmazható. A függvények teljes listája [alább látható](#mappingFunctions).

A mező-hozzárendelések az `fieldMappings` Indexelő definíciójának tömbje számára lesznek hozzáadva.

> [!NOTE]
> Ha nincsenek hozzáadva mező-hozzárendelések, az indexelő feltételezi, hogy az adatforrást tartalmazó mezőket az azonos nevű index mezőkhöz kell rendelni. Mező-hozzárendelés hozzáadásával eltávolítja ezeket az alapértelmezett mező-hozzárendeléseket a forrás és a cél mezőhöz. Egyes indexelő, például [a blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md), alapértelmezett mező-hozzárendeléseket adhatnak hozzá az index kulcs mezőjéhez.

## <a name="map-fields-using-rest"></a>Mezők leképezése a REST használatával

Az [Indexelő](/rest/api/searchservice/create-Indexer) API-kérelem használatával új indexelő létrehozásakor mező-hozzárendeléseket adhat hozzá. Az [Indexelő](/rest/api/searchservice/update-indexer) API-kérelem használatával kezelheti egy meglévő indexelő mező-hozzárendeléseit.

Például a következő módon képezhető le egy forrás mezőt egy célként megadott mezőre egy másik névvel:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

A forrás mező több mező-hozzárendelésre is hivatkozhat. Az alábbi példa azt szemlélteti, hogyan lehet "elágazást" lemásolni egy mezőből, és ugyanabból a forrásból két különböző index-mezőre másolni:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Az Azure Cognitive Search a kis-és nagybetűk megkülönböztetésének összehasonlításával oldja fel a mezők és függvények nevét a mezők leképezésében. Ez kényelmes (nem kell beolvasnia az összes burkolatot), de az azt jelenti, hogy az adatforrás vagy az index nem rendelkezhet olyan mezőkkel, amelyek csak esettől eltérőek lehetnek.  
>

## <a name="map-fields-using-net"></a>Mezők leképezése a .NET használatával

A [FieldMapping](/dotnet/api/azure.search.documents.indexes.models.fieldmapping) osztály segítségével definiálhatja a mezők leképezéseit a .net SDK-ban, amely tartalmazza a tulajdonságokat, valamint a `SourceFieldName` `TargetFieldName` választható `MappingFunction` hivatkozást.

Az indexelő létrehozásakor vagy később a tulajdonság közvetlen beállításával megadhatja a mezők leképezését `Indexer.FieldMappings` .

A következő C#-példa egy indexelő létrehozásakor beállítja a mező-hozzárendeléseket.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Mező-hozzárendelési függvények

Egy mező-hozzárendelési függvény átalakítja egy mező tartalmát, mielőtt az indexben tárolja őket. A következő leképezési függvények jelenleg támogatottak:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode függvény

*URL-cím – biztonságos* Base64 kódolást végez a bemeneti karakterláncban. Feltételezi, hogy a bemenet UTF-8 kódolású.

#### <a name="example---document-key-lookup"></a>Példa – dokumentum kulcsának keresése

Csak az URL-alapú biztonságos karakterek szerepelhetnek az Azure Cognitive Search-dokumentum kulcsában (mivel az ügyfeleknek a [keresési API](/rest/api/searchservice/lookup-document) használatával tudniuk kell kezelni a dokumentumot). Ha a kulcs forrás mezőjében URL-nem biztonságos karakterek szerepelnek, akkor a függvény használatával `base64Encode` átalakíthatja az indexelési időt. Azonban a dokumentum kulcsa (az átalakítás előtt és után is) nem lehet hosszabb 1 024 karakternél.

Ha a beolvasott kulcsot keresési időpontban kéri le, a függvény használatával beolvashatja `base64Decode` az eredeti kulcs értékét, és használhatja azt a forrásbizonylat lekéréséhez.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

#### <a name="example---preserve-original-values"></a>Példa – eredeti értékek megőrzése

A [blob Storage indexelő](search-howto-indexing-azure-blob-storage.md) automatikusan felvesz egy mező-hozzárendelést a `metadata_storage_path` -ból, a blob URI-ját az index kulcs mezőjébe, ha nincs megadva a mező leképezése. Ez az érték Base64 kódolású, így biztonságos Azure Cognitive Search-dokumentum kulcsaként használni. Az alábbi példa azt mutatja be, hogyan lehet egyszerre leképezni egy *URL-cím – biztonságos Base64-* kódolású verziót `metadata_storage_path` egy `index_key` mezőre, és megőrizni az eredeti értéket egy `metadata_storage_path` mezőben:

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

Ha nem tartalmaz Parameters tulajdonságot a leképezési függvényhez, az alapértelmezés szerint az értéket adja meg `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Az Azure Cognitive Search két különböző Base64-kódolást támogat. Ugyanezeket a paramétereket kell használnia a kódoláshoz és a dekódoláshoz ugyanezen a mezőben. További információ: [Base64 kódolási beállítások](#base64details) a használandó paraméterek eldöntéséhez.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode függvény

A bemeneti karakterlánc Base64-dekódolását végzi. A bemenet a következő *URL-cím – biztonságos* Base64 kódolású karakterlánc lehet.

#### <a name="example---decode-blob-metadata-or-urls"></a>Példa – blob metaadatainak vagy URL-címek dekódolása

A forrásadatok olyan Base64 kódolású karakterláncokat tartalmazhatnak, mint például a blob metaadat-karakterláncok vagy a webes URL-címek, amelyeket egyszerű szövegként szeretne keresni. A függvény használatával a `base64Decode` kódolt adatokat visszaállíthatja normál karakterláncokra a keresési index feltöltésekor.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Ha nem tartalmaz Parameters tulajdonságot, a rendszer alapértelmezés szerint az értéket adja meg `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Az Azure Cognitive Search két különböző Base64-kódolást támogat. Ugyanezeket a paramétereket kell használnia a kódoláshoz és a dekódoláshoz ugyanezen a mezőben. További részletekért lásd a [Base64-kódolási beállításokat](#base64details) a használandó paraméterek eldöntéséhez.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64 kódolási beállítások

Az Azure Cognitive Search támogatja az URL-alapú biztonságos Base64-kódolást és a normál Base64-kódolást. Az indexelés során Base64 kódolású karakterláncot később kell dekódolni ugyanazzal a kódolási lehetőségekkel, különben az eredmény nem egyezik az eredetivel.

Ha a `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` kódoláshoz és a dekódoláshoz szükséges paraméterek a (z) értékre vannak beállítva `true` , akkor a következőképpen `base64Encode` viselkedik: [HttpServerUtility. UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) , és `base64Decode` úgy viselkedik, mint a [HttpServerUtility. UrlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode).

> [!WARNING]
> Ha `base64Encode` a kulcs értékének megadására szolgál, akkor igaz értékre kell `useHttpServerUtilityUrlTokenEncode` állítani. A kulcsok értékeihez csak az URL-alapú biztonságos Base64-kódolást lehet használni. Lásd: az [Azure Cognitive Search&#41;elnevezési szabályai &#40;](/rest/api/searchservice/naming-rules) az összes korlátozást a kulcsok értékeiben.

A .NET-kódtárak az Azure-ban Cognitive Search feltételezik a teljes .NET-keretrendszert, amely beépített kódolást biztosít. A `useHttpServerUtilityUrlTokenEncode` és a `useHttpServerUtilityUrlTokenDecode` beállítások ezt a beépített funkciót használják. Ha .NET Core-t vagy más keretrendszert használ, javasoljuk, hogy ezeket a beállításokat úgy állítsa be, hogy `false` közvetlenül a keretrendszer kódolási és dekódolási funkcióit hívja meg.

A következő táblázat a karakterlánc különböző Base64 kódolásait hasonlítja össze `00>00?00` . Ha meg szeretné határozni a Base64-függvények szükséges további feldolgozását (ha van ilyen), alkalmazza a függvénytár-kódolás függvényt a karakterláncon, `00>00?00` és hasonlítsa össze a kimenetet a várt kimenettel `MDA-MDA_MDA` .

| Encoding | Base64 kódolású kimenet | További feldolgozás a függvénytár-kódolás után | További feldolgozás a könyvtár dekódolása előtt |
| --- | --- | --- | --- |
| Base64 kitöltéssel | `MDA+MDA/MDA=` | URL-alapú biztonságos karakterek használata és kitöltés eltávolítása | Szabványos Base64-karakterek használata és kitöltés hozzáadása |
| Base64 kitöltés nélkül | `MDA+MDA/MDA` | URL-alapú biztonságos karakterek használata | Szabványos Base64-karakterek használata |
| URL – biztonságos Base64 kitöltéssel | `MDA-MDA_MDA=` | Kitöltés eltávolítása | Kitöltés hozzáadása |
| URL – biztonságos Base64 kitöltés nélkül | `MDA-MDA_MDA` | Nincs | Nincs |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition függvény

A megadott elválasztó karakterrel feldarabol egy sztring mezőt, és az eredményül kapott felosztásban kiválasztja a tokent a megadott pozícióban.

Ez a függvény a következő paramétereket használja:

* `delimiter`: a bemeneti karakterlánc felosztásakor elválasztóként használandó karakterlánc.
* `position`: a bemeneti sztring felosztása után kiválasztható token egészének nulla alapú pozíciója.

Ha például a bemenet `Jane Doe` , a `delimiter` `" "` (szóköz) és az `position` értéke 0, az eredmény a következő: `Jane` . Ha a `position` értéke 1, az eredmény `Doe` . Ha a pozíció olyan tokenre hivatkozik, amely nem létezik, hibaüzenetet ad vissza.

#### <a name="example---extract-a-name"></a>Példa – név kinyerése

Az adatforrás tartalmaz egy `PersonName` mezőt, és két különálló `FirstName` és mezőként szeretné indexelni `LastName` . Ezt a függvényt használhatja a bemenet elválasztóként való felosztásához.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection függvény

A sztringek JSON-tömbként formázott karakterláncának átalakítása egy karakterlánc-tömbbe, amely `Collection(Edm.String)` az index mezőinek feltöltésére használható.

Ha például a bemeneti karakterlánc `["red", "white", "blue"]` , akkor a típusú cél mező a következő `Collection(Edm.String)` három értékkel lesz feltöltve: `red` `white` , és `blue` . A nem értelmezhető JSON-karakterlánc-tömbökkel rendelkező bemeneti értékek esetén hibaüzenetet ad vissza.

#### <a name="example---populate-collection-from-relational-data"></a>Példa – a gyűjtemények feltöltése a kapcsolódó adatokból

Azure SQL Database nem rendelkezik olyan beépített adattípussal, amely természetesen az `Collection(Edm.String)` Azure Cognitive Search mezőihez van társítva. A karakterlánc-gyűjtési mezők feltöltéséhez JSON-karakterlánc-tömbként előre feldolgozhatja a forrásadatokat, majd használhatja a `jsonArrayToStringCollection` leképezési függvényt.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode függvény

Ez a függvény egy karakterlánc kódolására használható, hogy az "URL-biztonság" legyen. Ha olyan karakterláncot használ, amely nem engedélyezett karaktereket tartalmaz egy URL-címben, akkor ez a függvény a "nem biztonságos" karaktereket átalakítja a karakter-entitások megfelelőbe. Ez a függvény az UTF-8 kódolási formátumot használja.

#### <a name="example---document-key-lookup"></a>Példa – dokumentum kulcsának keresése

`urlEncode` a függvény a függvény alternatívájaként is használható `base64Encode` , ha csak a nem biztonságos URL-címeket szeretné átalakítani, miközben más karaktereket is tart.

Tegyük fel, hogy a bemeneti karakterlánc `<hello>` – a típusú cél mező a következő `(Edm.String)` értékkel lesz feltöltve: `%3chello%3e`

Ha a beolvasott kulcsot keresési időpontban kéri le, a függvény használatával beolvashatja `urlDecode` az eredeti kulcs értékét, és használhatja azt a forrásbizonylat lekéréséhez.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode függvény

 Ez a függvény UTF-8 kódolási formátumban konvertál egy URL-kódolású karakterláncot egy dekódolt karakterlánccá.

 ### <a name="example---decode-blob-metadata"></a>Példa – blob metaadatainak dekódolása

 Egyes Azure Storage-ügyfelek automatikusan URL-címet kódolnak a blob metaadatainak kódolásához, ha nem ASCII-karaktereket tartalmaznak. Ha azonban szeretné, hogy az ilyen metaadatok kereshetők legyenek (egyszerű szövegként), a függvény használatával a `urlDecode` kódolt adatokat visszaállíthatja normál karakterláncokra a keresési index feltöltésekor.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode függvény
 
 Ez a függvény bármilyen hosszúságú karakterláncot konvertál egy rögzített hosszúságú karakterlánccá.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Példa – túl hosszú dokumentum-kulcsok leképezése
 
Ha a dokumentum kulcsa 1024 karakternél hosszabb, akkor a rendszer ezt a függvényt alkalmazza a dokumentum kulcsának hosszának csökkentése érdekében.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```