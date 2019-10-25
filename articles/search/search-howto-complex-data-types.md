---
title: Összetett adattípusok modellezése
titleSuffix: Azure Cognitive Search
description: A beágyazott vagy hierarchikus adatstruktúrák modellezése Azure Cognitive Search indexekben történhet a ComplexType és a Collections adattípusok használatával.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af68f232c893259747e6ed106eced70fd8b89351
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792232"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Összetett adattípusok modellezése az Azure-ban Cognitive Search

Az Azure Cognitive Search indexek feltöltéséhez használt külső adatkészletek számos alakzatban származhatnak. Esetenként hierarchikus vagy beágyazott alstruktúrákat is tartalmaznak. A példák több címet is tartalmazhatnak egyetlen ügyfél számára, több színt és méretet egyetlen SKU számára, egyetlen könyv több szerzője és így tovább. A modellezési feltételekben ezeket a struktúrákat *összetett*, *összetett*, *összetett*vagy *aggregált* adattípusoknak is nevezzük. Az Azure Cognitive Search az ehhez a koncepcióhoz használt kifejezés **összetett típusú**. Az Azure kognitív Cognitive Search az összetett típusok modellezése **összetett mezők**használatával történik. A komplex mező olyan mező, amely bármilyen adattípusú gyermekeket (almezőket) tartalmaz, beleértve a más összetett típusokat is. Ez hasonló módon működik, mint a strukturált adattípusok programozási nyelven.

Az összetett mezők a dokumentumban szereplő egyetlen objektumot vagy objektumok tömbjét jelölik, az adattípustól függően. `Edm.ComplexType` típusú mezők egyetlen objektumot jelölnek, míg a `Collection(Edm.ComplexType)` típusú mezők az objektumok tömbjét jelölik.

Az Azure Cognitive Search natív módon támogatja az összetett típusokat és gyűjteményeket. Ezek a típusok lehetővé teszik szinte bármilyen JSON-struktúra modellezését egy Azure Cognitive Search indexben. Az Azure Cognitive Search API-k előző verzióiban csak a lapos sorok importálhatók. A legújabb verzióban az index mostantól alaposabban megközelítheti a forrásadatokat. Más szóval, ha a forrásadatok összetett típusokkal rendelkeznek, az indexnek lehetnek összetett típusai is.

Első lépésként javasoljuk a [Hotel adatkészletét](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), amelyet betölthet a Azure Portal **adatimportálás** varázslójával. A varázsló összetett típusokat észlel a forrásban, és az észlelt struktúrák alapján javasol egy index sémát.

> [!Note]
> Az összetett típusok támogatása általánosan elérhető `api-version=2019-05-06`ban. 
>
> Ha a keresési megoldás az összevont adatkészletek korábbi megkerülő megoldásaira épül egy gyűjteményben, módosítsa az indexet úgy, hogy az a legújabb API-verzióban támogatott összetett típusokat tartalmazzon. Az API-verziók frissítésével kapcsolatos további információkért lásd: [frissítés a legújabb REST API verzióra](search-api-migration.md) , vagy [frissítsen a legújabb .net SDK-verzióra](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Példa összetett szerkezetre

A következő JSON-dokumentum egyszerű mezőkből és összetett mezőkből áll. Az összetett mezők, például a `Address` és a `Rooms`almezővel rendelkeznek. `Address` az egyes almezőkhöz egyetlen érték tartozik, mivel ez a dokumentum egyetlen objektuma. Ezzel szemben `Rooms` több, a gyűjteményben lévő objektumhoz tartozó értéket tartalmaz.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Összetett mezők létrehozása

Ahogy az index definíciója esetében is, a portál, a [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)vagy a [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) használatával összetett típusokat tartalmazó sémát hozhat létre. 

Az alábbi példa egy olyan JSON-index sémát mutat be, amely egyszerű mezőket, gyűjteményeket és összetett típusokat tartalmaz. Figyelje meg, hogy egy összetett típuson belül minden almező rendelkezik egy típussal, és rendelkezhet attribútumokkal, mint a legfelső szintű mezők. A séma megfelel a fenti példában szereplő adatként. `Address` egy olyan összetett mező, amely nem gyűjtemény (a szállodában egy-egy címe van). `Rooms` egy összetett gyűjtemény mező (a Hotel számos szobát tartalmaz).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Cognitive Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Összetett mezők frissítése

Az általános mezőkre vonatkozó összes [újraindexelő szabály](search-howto-reindex.md) továbbra is összetett mezőkre vonatkozik. Az itt található főbb szabályok újbóli létrehozásakor a mező hozzáadásához nincs szükség index-újraépítésre, de a legtöbb módosítás nem szükséges.

### <a name="structural-updates-to-the-definition"></a>A definíció szerkezeti frissítései

Új almezőket bármikor hozzáadhat egy összetett mezőhöz anélkül, hogy szükség lenne az index újraépítésére. Ha például az "Irányítószám" lehetőséget adja hozzá a `Address`hoz vagy a "kényelmi" elemhez, hogy `Rooms` engedélyezve legyen, ugyanúgy, mint a legfelső szintű mező hozzáadása egy indexhez. A meglévő dokumentumok NULL értékkel rendelkeznek az új mezőkhöz, amíg az adatok frissítésével explicit módon fel nem tölti ezeket a mezőket.

Figyelje meg, hogy egy összetett típuson belül minden almező rendelkezik egy típussal, és rendelkezhet attribútumokkal, mint a legfelső szintű mezők

### <a name="data-updates"></a>Adatfrissítések

Az indexben lévő meglévő dokumentumok frissítése az `upload` művelettel ugyanúgy működik, mint az összetett és az egyszerű mezők esetében – az összes mezőt lecseréli a rendszer. Azonban `merge` (vagy egy meglévő dokumentumra alkalmazott `mergeOrUpload`) nem ugyanúgy működik az összes mezőben. Pontosabban, `merge` nem támogatja az elemek összevonását egy gyűjteményen belül. Ez a korlátozás egyszerű típusok és összetett gyűjtemények gyűjteményei esetében létezik. Egy gyűjtemény frissítéséhez le kell kérnie a teljes gyűjtemény értékét, végre kell hajtania a módosításokat, majd tartalmaznia kell az új gyűjteményt az index API-kérelemben.

## <a name="searching-complex-fields"></a>Összetett mezők keresése

A szabad formátumú keresési kifejezések a várt módon működnek összetett típusokkal. Ha bármilyen kereshető mező vagy Almező megfelel a dokumentumnak, akkor maga a dokumentum egyezés.

A lekérdezések részletesebben, ha több kifejezéssel és operátorral rendelkezik, és egyes feltételeknél meg van adva mezőnév, ahogy az a [Lucene szintaxissal](query-lucene-syntax.md)is lehetséges. A lekérdezés például két, "Portland" és "OR" kifejezéssel próbálkozik a címtartomány két almezője között:

    search=Address/City:Portland AND Address/State:OR

Az ehhez hasonló lekérdezések nem *korrelálnak* a teljes szöveges kereséshez, a szűrőktől eltérően. A szűrőkben egy összetett gyűjtemény almezőin keresztüli lekérdezések korrelálnak [`any` vagy `all`](search-query-odata-collection-operators.md)tartománybeli változóinak használatával. A fenti Lucene-lekérdezés a "Portland, Maine" és "Portland, Oregon", valamint az Oregon többi városát tartalmazó dokumentumokat adja vissza. Ez azért történik, mert az egyes záradékok a teljes dokumentumban lévő mező összes értékére érvényesek, így nincs "aktuális aldokumentum" fogalma. Erről további információt a [OData-gyűjtési szűrők ismertetése az Azure Cognitive Searchban](search-query-understand-collection-filters.md)című témakörben talál.

## <a name="selecting-complex-fields"></a>Összetett mezők kijelölése

A `$select` paraméterrel kiválaszthatja, hogy mely mezőket adja vissza a rendszer a keresési eredmények között. Ha ezt a paramétert szeretné használni egy összetett mező adott almezőinek kiválasztásához, a szülő mezőt és az almezőt perjel (`/`) karakterrel elválasztva adja meg.

    $select=HotelName, Address/City, Rooms/BaseRate

Ha a keresési eredmények között szeretné, a mezőket beolvasható értékként kell megjelölni az indexben. Egy `$select` utasításban csak lekérhető mezők használhatók.

## <a name="filter-facet-and-sort-complex-fields"></a>Összetett mezők szűrése, aspektusa és rendezése

A szűréshez és a mezőkben végzett keresésekhez használt [OData-elérésiút-szintaxis](query-odata-filter-orderby-syntax.md) is használható a keresési kérelemben található mezők aspektusának, rendezésének és kiválasztásához. Összetett típusok esetén a szabályok érvényesek, amelyek meghatározzák, hogy mely almezőket lehet csoportosított vagy sokrétűként megjelölni. További információt ezekről a szabályokról a [create index API-referenciát](https://docs.microsoft.com/rest/api/searchservice/create-index#request)ismertető témakörben talál.

### <a name="faceting-sub-fields"></a>Metszeti almezők

Bármely almező kijelölhető, ha `Edm.GeographyPoint` vagy `Collection(Edm.GeographyPoint)`típusú.

A dimenzió eredményeiben visszaadott dokumentumok számát a rendszer kiszámítja a szülő dokumentum (a szálloda) számára, nem pedig az aldokumentumok egy összetett gyűjteményben (szobákban). Tegyük fel például, hogy egy szálloda 20 "Suite" típusú szobát tartalmaz. Mivel ez a dimenzióérték-paraméter `facet=Rooms/Type`, a dimenziók száma egy a szálloda számára, nem 20 a helyiségek számára.

### <a name="sorting-complex-fields"></a>Összetett mezők rendezése

A rendezési műveletek dokumentumok (szállodák) és nem aldokumentumok (szobák) esetében érvényesek. Ha összetett típusú gyűjteményt (például szobákat) tartalmaz, fontos tisztában lennie azzal, hogy a szobák egyáltalán nem rendezhetők. Valójában nem rendezheti a gyűjteményeket.

A rendezési műveletek akkor működnek, ha a mezők egyetlen értékkel rendelkeznek a dokumentumokban, függetlenül attól, hogy a mező egy egyszerű mező, vagy egy összetett típusú almező. A `Address/City` például rendezhető, mert egy helyen csak egy-egy címe van, így a `$orderby=Address/City` város szerint rendezi a hotelt.

### <a name="filtering-on-complex-fields"></a>Szűrés összetett mezőkön

A szűrő kifejezésben a komplex mezők almezőire is hivatkozhat. Egyszerűen használja ugyanazt a [OData elérésiút-szintaxist](query-odata-filter-orderby-syntax.md) , amelyet a rendszer a mezők rendezéséhez, rendezéséhez és kiválasztásához használ. A következő szűrő például az összes kanadai szállodát visszaküldi:

    $filter=Address/Country eq 'Canada'

Egy összetett gyűjtemény mező szűréséhez használhat **lambda kifejezést** a [`any` és `all` operátorral](search-query-odata-collection-operators.md). Ebben az esetben a lambda kifejezés **tartomány változója** egy olyan objektum, amely almezőket tartalmaz. Ezeket az alárendelt mezőket a standard OData Path szintaxissal tekintheti meg. A következő szűrő például az összes olyan szállodát visszaküldi, amely legalább egy deluxe szobával és az összes nem dohányzó szobával rendelkezik:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

A legfelső szintű egyszerű mezőkhöz hasonlóan a komplex mezők egyszerű almezői csak akkor szerepelhetnek a szűrőben, ha az index definíciójában a **szűrhető** attribútum értéke `true`. További információ: [create index API Reference](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

## <a name="next-steps"></a>Következő lépések

Próbálja ki a [Hotels adatkészletet](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) az **adatimportálás** varázslóban. Az adatok eléréséhez a readme szolgáltatásban megadott Cosmos DB kapcsolati információkra lesz szüksége.

Ezekkel az adatokkal a varázsló első lépéseként egy új Azure Cosmos DB adatforrást kell létrehoznia. Ha a varázslóban a cél index lapra kerül, egy összetett típusú index jelenik meg. Hozza létre és töltse be az indexet, majd hajtsa végre a lekérdezéseket az új struktúra megismeréséhez.

> [!div class="nextstepaction"]
> [Gyors útmutató: portál varázsló importáláshoz, indexeléshez és lekérdezésekhez](search-get-started-portal.md)