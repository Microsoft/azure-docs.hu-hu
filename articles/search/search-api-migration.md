---
title: REST API verziók frissítése
titleSuffix: Azure Cognitive Search
description: Tekintse át az API-verziók közötti különbségeket, és Ismerje meg, hogy mely műveletek szükségesek a meglévő kód legújabb Azure Cognitive Search Service REST API verzióra való átváltásához.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d7734fde529c24e8113ea3b019d343b7223f0122
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91929642"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Frissítsen a legújabb REST APIra az Azure-ban Cognitive Search

Ha a [**Search REST API**](/rest/api/searchservice/)egy korábbi verzióját használja, ez a cikk segítséget nyújt az alkalmazás frissítéséhez a legújabb általánosan elérhető API-verzióra, **2020-06-30**-re.

Az 2020-06-30-es verzió egy fontos új funkciót ([Knowledge Store](knowledge-store-concept-intro.md)) tartalmaz, és számos kisebb viselkedési változást vezet be. Ennek a verziónak többnyire visszamenőlegesen kompatibilisnek kell lennie, ezért a kód módosításainak minimálisnak kell lennie, ha az előző verzióról frissít (2019-05-06).

> [!NOTE]
> A keresési szolgáltatás számos REST API verziót támogat, beleértve a korábbikat is. Továbbra is használhatja ezeket az API-verziókat, de javasoljuk, hogy a kód áttelepítését a legújabb verzióra, hogy hozzáférhessen az új funkciókhoz. Idővel a REST API elavult verziói elavultak lesznek, és már [nem támogatottak](search-api-versions.md#unsupported-versions).

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>A frissítés lépései

Amikor új verzióra frissít, valószínűleg nem kell módosítania a kód módosításait, kivéve a verziószámot. A következő esetekben lehet szükség a kód módosítására:

* A kód meghiúsul, ha a rendszer ismeretlen tulajdonságokat ad vissza egy API-válaszban. Alapértelmezés szerint az alkalmazás figyelmen kívül hagyja az általa nem értelmezhető tulajdonságokat.

* A kód megőrzi az API-kérelmeket, és megkísérli az új API-verzióra való újraküldést. Ez például akkor fordulhat elő, ha az alkalmazás a keresési API által visszaadott folytatási tokeneket is megőrzi (további információért keresse `@search.nextPageParameters` a [Search API-referenciát](/rest/api/searchservice/Search-Documents)).

* A kód olyan API-verzióra hivatkozik, amely a 2019-05-06-es időpontra vonatkozik, és az adott kiadásban feltörhető változások közül egy vagy több is érvényes. A [2019-05-06-es verzióra való frissítés](#upgrade-to-2019-05-06) további részleteket tartalmaz. 

Ha ezek bármelyike Önre vonatkozik, előfordulhat, hogy a kódot ennek megfelelően kell módosítania. Ellenkező esetben nem szükséges módosítani a módosításokat, de érdemes lehet megkezdeni az új verzióban hozzáadott szolgáltatások használatát.

## <a name="upgrade-to-2020-06-30"></a>Frissítés 2020-06-30-re

Az 2020-06-30-es verzió a REST API új általánosan elérhető kiadása. Az egyik megszakítási változás és számos viselkedési különbség van. 

A funkciók mostantól általánosan elérhetők ebben az API-verzióban:

* A szakértelmével által létrehozott dúsított tartalom állandó [tárolása, amely](knowledge-store-concept-intro.md)az alsóbb rétegbeli elemzéshez és más alkalmazásokon keresztül történő feldolgozáshoz lett létrehozva. Ezzel a képességgel az indexelő által vezérelt mesterséges intelligencia-bővítési folyamat egy keresési indexen kívül feltöltheti a tudásbázist is. Ha a szolgáltatás előzetes verzióját használta, az egyenértékű az általánosan elérhető verzióval. Az egyetlen kód módosítása szükséges az API-verzió módosítására.

### <a name="breaking-change"></a>Változás megszakítása

A korábbi API-verziókra írt meglévő kódok az API-Version = 2020-06-30 és újabb verziókban lesznek megszakítva, ha a kód a következő funkciókat tartalmazza:

* Bármely EDM. Date literál (az év hónapja, mint például a `2020-12-12` ) a szűrési kifejezésekben a EDM. DateTimeOffset formátumot kell követnie: `2020-12-12T00:00:00Z` . Ez a módosítás az időzóna-eltérések miatti hibás vagy váratlan lekérdezési eredmények kezelésére volt szükséges.

### <a name="behavior-changes"></a>Viselkedési változások

* A [BM25 ranking algoritmus](index-ranking-similarity.md) újabb technológiával helyettesíti az előző rangsorolási algoritmust. Az új szolgáltatások ezt az algoritmust automatikusan használják majd. Meglévő szolgáltatások esetén a paramétereket az új algoritmus használatára kell beállítani.

* A null értékek rendezett eredményei módosultak ebben a verzióban, és először Null érték jelenik meg, ha a rendezés `asc` és az utolsó, ha a rendezés `desc` . Ha kódot írt a null értékek rendezésének kezeléséhez, vegye figyelembe a változást.

## <a name="upgrade-to-2019-05-06"></a>Frissítés 2019-05-06-re

Az 2019-05-06-es verzió a REST API korábbi általánosan elérhető kiadása. Az API-verzióban általánosan elérhetővé vált szolgáltatások a következők:

* Az [automatikus kiegészítés](index-add-suggesters.md) egy typeahead funkció, amely egy részben megadott kifejezési bemenetet hajt végre.
* Az [összetett típusok](search-howto-complex-data-types.md) natív támogatást biztosítanak a strukturált objektumok számára a keresési indexben.
* A [JsonLines-elemzési módok](search-howto-index-json-blobs.md), az Azure Blob-indexelés részeként egy keresési dokumentumot hoz létre, amely egy sortöréssel elválasztott JSON-entitáson alapul.
* A [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítés olyan indexelést biztosít, amely a Cognitive Services mesterséges intelligencia-bővítési motorját használja.

### <a name="breaking-changes"></a>Kompatibilitástörő változások

A korábbi API-verziókra írt meglévő kódok az API-Version = 2019-05-06-es és újabb verziókban lesznek megszakítva, ha a kód a következő funkciókat tartalmazza:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>A Azure Cosmos db-DataSource indexelő most "type": "cosmosdb"

Ha [Cosmos db indexelő](search-howto-index-cosmosdb.md )használ, a következőre kell váltania: `"type": "documentdb"` `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Az indexelő végrehajtási eredményeinek hibái már nem rendelkeznek állapottal

Az indexelő végrehajtásához használt hiba szerkezete korábban egy `status` elemmel rendelkezett. Ez az elem el lett távolítva, mert nem volt hasznos információ.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Az indexelő adatforrás API többé nem adja vissza a kapcsolatok karakterláncait

A 2019-05-06-es és 2019-05-06-os API-verzióktól kezdve az adatforrás API többé nem adja vissza a kapcsolódási karakterláncokat a REST-műveletek válaszában. A korábbi API-verziókban a POST paranccsal létrehozott adatforrások esetében az Azure Cognitive Search a **201** -as értéket adta vissza, amelyet a OData-válasz követ, amely egyszerű szövegben szerepelt a kapcsolatok karakterláncában.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Az elnevezett entitások felismerésének kognitív készsége már megszűnt

Ha a kódban az [entitás-felismerési szakértelem nevet](cognitive-search-skill-named-entity-recognition.md) adta, a hívás sikertelen lesz. A helyettesítő funkció az [entitások felismerése](cognitive-search-skill-entity-recognition.md). A szaktudás hivatkozását más változtatások nélkül is le kell cserélni. Az API-aláírás mindkét verzió esetében azonos. 

### <a name="upgrading-complex-types"></a>Összetett típusok frissítése

Az API 2019-05-06-es verziója formális támogatást adott a komplex típusokhoz. Ha a kód a 2017-11-11 – Preview vagy 2016-09-01 – előzetes verzióban az összetett típus egyenértékűségére vonatkozó korábbi ajánlásokat implementálta, akkor az 2019-05-06-es verziótól kezdődően néhány új és megváltozott korlátozást kell figyelembe vennie:

+ Az almezők mélységének és az indexekben lévő összetett gyűjtemények számának korlátozása csökkent. Ha olyan indexeket hozott létre, amelyek túllépik ezeket a korlátozásokat az előzetes verziójú API-verziók használatával, akkor az API 2019-05-06-es verziójának használatával végzett frissítés vagy újbóli létrehozás sikertelen lesz. Ha ez Önre vonatkozik, újra kell terveznie a sémát, hogy az megfeleljen az új korlátoknak, majd újjáépítse az indexet.

+ Az API-Version 2019-05-06-es verzió új korlátja a dokumentumok összetett gyűjtemények elemeinek száma. Ha olyan dokumentumokkal hozta létre az indexeket, amelyek túllépik ezeket a korlátozásokat az előzetes verziójú API-verziók használatával, az API-Version 2019-05-06-es verzióval végzett újraindexelési kísérlet sikertelen lesz. Ha ez Önre vonatkozik, csökkentenie kell az összetett gyűjtemények elemeinek számát, mielőtt újraindexeli az adatokat.

További információ: [Azure Cognitive Search szolgáltatási korlátai](search-limits-quotas-capacity.md).

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Régi komplex típusú struktúra frissítése

Ha a kód összetett típusokat használ a korábbi előzetes verziójú API-verziók egyikével, lehetséges, hogy az alábbihoz hasonló index-definíció formátumot használ:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Az index mezőinek definiálásához egy újabb faszerkezetű formátum lett bevezetve az API 2017-11-11-es verziójában. Az új formátumban minden összetett mezőhöz tartozik egy mező gyűjtemény, amelyben az almezők definiálva vannak. Az API 2019-05-06-es verziójában ez az új formátum kizárólag a régi formátummal rendelkező indexek létrehozására vagy frissítésére tett kísérlet sikertelen lesz. Ha a régi formátummal létrehozott indexekkel rendelkezik, akkor az API 2017-11-11-es verziójának előzetes verzióját kell használnia az új formátumra való frissítéshez, mielőtt azok a 2019-05-06-es API-verzióval felügyelhetők legyenek.

A "Flat" indexeket az új formátumra frissítheti a következő lépésekkel az API 2017-11-11-es verziójának használatával:

1. Az index lekéréséhez hajtson végre egy GET-kérelmet. Ha már az új formátumban van, elkészült.

2. Fordítsa le az indexet a "Flat" formátumból az új formátumra. Ehhez kódot kell írnia, mivel az írás időpontjában nincs elérhető mintakód.

3. Helyezzen el egy PUT-kérést az index új formátumra való frissítéséhez. Ügyeljen arra, hogy ne módosítsa az index egyéb részleteit, például a mezők kereshetőség/szűrését, mert ezt a frissítési index API nem engedélyezi.

> [!NOTE]
> Nem lehetséges a régi "Flat" formátummal létrehozott indexek kezelése a Azure Portal. Frissítse az indexeket a "Flat" ábrázolásból a "Tree" kifejezésre a lehető leghamarabb.

## <a name="next-steps"></a>Következő lépések

Tekintse át a Search REST API dokumentációját. Ha problémákba ütközik, kérjen segítséget [stack overflow](https://stackoverflow.com/) vagy [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API referenciája](/rest/api/searchservice/)
