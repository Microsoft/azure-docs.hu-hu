---
title: Keresés Azure Cosmos DB-adat fölött
titleSuffix: Azure Cognitive Search
description: Azure Cosmos DB adatok importálása az Azure Cognitive Search kereshető indexbe. Az indexelő automatizálja az adatfeldolgozást a kiválasztott adatforrásokhoz, például a Azure Cosmos DBhoz.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 563edae0292062e1ed7f216c69aeeb84ef0fa7a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119475"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Cosmos DB-adatok indexelése indexelővel az Azure Cognitive Searchben 

> [!IMPORTANT] 
> Az SQL API általánosan elérhető.
> A MongoDB API, a Gremlin API és a Cassandra API támogatás jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Az előzetes verzióhoz való hozzáférést az [űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)kitöltésével kérheti le. 
> Ezek a szolgáltatások a [REST API előzetes verzióban](search-api-preview.md) érhetők el. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

> [!WARNING]
> Az Azure Cognitive Search csak olyan Cosmos DB-gyűjteményeket támogat, amelyeknek [konzisztens](../cosmos-db/index-policy.md#indexing-mode) az [indexelési szabályzata](../cosmos-db/index-policy.md) . A lusta indexelési házirenddel rendelkező gyűjtemények indexelése nem ajánlott, és a hiányzó adatvesztést okozhat. A letiltott indexeléssel rendelkező gyűjtemények nem támogatottak.

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Cosmos db [Indexelő](search-indexer-overview.md) a tartalom kinyeréséhez és az Azure-Cognitive Search kereshetővé tételéhez. Ez a munkafolyamat létrehoz egy Azure Cognitive Search indexet, és betölti azt a Azure Cosmos DBból kinyert meglévő szöveggel. 

Mivel a terminológia zavaró lehet, érdemes megjegyezni, hogy [Azure Cosmos db indexelés](../cosmos-db/index-overview.md) és az [Azure Cognitive Search indexelés](search-what-is-an-index.md) különböző műveletek, amelyek egyediek az egyes szolgáltatásokhoz. Az Azure Cognitive Search indexelésének megkezdése előtt a Azure Cosmos DB-adatbázisnak már léteznie kell, és tartalmaznia kell az adatait.

Az Azure Cognitive Search Cosmos DB indexelő képes a különböző protokollokon keresztül elért [Azure Cosmos db elemek](../cosmos-db/account-databases-containers-items.md#azure-cosmos-items) bejárására. 

+ A általánosan elérhető [SQL API](../cosmos-db/sql-query-getting-started.md)-k esetében a [portál](#cosmos-indexer-portal), a [REST API](/rest/api/searchservice/indexer-operations)vagy a [.net SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) segítségével hozhatja létre az adatforrást és az indexelő.

+ A [MONGODB API (előzetes verzió)](../cosmos-db/mongodb-introduction.md)esetében használhatja a [portált](#cosmos-indexer-portal) vagy a [REST API 2020-06-30-es verzióját – előzetes verzióként](search-api-preview.md) az adatforrás és az indexelő létrehozásához.

+ A [Cassandra API (előzetes verzió)](../cosmos-db/cassandra-introduction.md) és a [Gremlin API (előzetes verzió)](../cosmos-db/graph-introduction.md)esetében csak a [REST API 2020-06-30-es verziójának előzetes verzióját](search-api-preview.md) használhatja az adatforrás és az indexelő létrehozásához.


> [!Note]
> Ha azt szeretné, hogy az Azure Cognitive Search támogatott legyen, szavazzon a [Table API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) felhasználói hangján.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>A portál használata

> [!Note]
> A portál jelenleg támogatja az SQL API-t és a MongoDB API-t (előzetes verzió).

Azure Cosmos DB elemek indexelésének legegyszerűbb módja a [Azure Portal](https://portal.azure.com/)varázsló használata. Mintavételezési adatok és metaadatok beolvasása a tárolóban az [**adatok importálása**](search-import-data-portal.md) varázsló az Azure Cognitive Search létrehozhat egy alapértelmezett indexet, leképezheti a forrás mezőket a célként megadott index mezőire, és betöltheti az indexet egyetlen művelettel. A forrásadatok méretétől és összetettségének függvényében percek alatt működőképes teljes szöveges keresési indexszel rendelkezhet.

Azt javasoljuk, hogy ugyanazt a régiót vagy helyet használja mind az Azure-Cognitive Search, mind a Azure Cosmos DB az alacsonyabb késés és a sávszélesség-költségek elkerülése érdekében.

### <a name="1---prepare-source-data"></a>1 – forrásadatok előkészítése

Rendelkeznie kell egy Cosmos DB fiókkal, egy Azure Cosmos DB-adatbázissal, amely az SQL API-ra, a MongoDB API-ra (előzetes verzió) vagy a Gremlin API-ra (előzetes verzió) és az adatbázis tartalmára van leképezve.

Győződjön meg arról, hogy a Cosmos DB adatbázisa tartalmaz adatait. Az [adatok importálása varázsló](search-import-data-portal.md) beolvassa a metaadatokat, és elvégzi az adatok mintavételezését az index sémájának következtetéséhez, de az adatokat a Cosmos DBból is betölti. Ha az adatok hiányoznak, a varázsló ezzel a hibával leáll: "hiba történt az index sémájának észlelése az adatforrásból: nem sikerült létrehozni a prototípus-indexet, mert a (z)" emptycollection "adatforrás nem adott vissza értéket".

### <a name="2---start-import-data-wizard"></a>2 – az adatimportálás megkezdése varázsló

A [varázsló elindításához](search-import-data-portal.md) a parancssáv az Azure Cognitive Search szolgáltatás lapján, vagy ha Cosmos db SQL API-hoz csatlakozik, az Cosmos db fiók bal oldali navigációs paneljének **Beállítások** szakaszában kattintson az **Azure-Cognitive Search hozzáadása** lehetőségre.

   ![Adatimportálási parancs a portálon](./media/search-import-data-portal/import-data-cmd2.png "Az adatimportálás varázsló elindítása")

### <a name="3---set-the-data-source"></a>3 – az adatforrás beállítása

**Az adatforrás lapon a** forrásnak **Cosmos DBnak** kell lennie, a következő jellemzőkkel:

+ A **név** az adatforrás-objektum neve. A létrehozás után kiválaszthatja más számítási feladatokhoz is.

+ **Cosmos db fióknak** a következő formátumok egyikében kell lennie:
    1. Az elsődleges vagy másodlagos kapcsolatok karakterlánca Cosmos DB a következő formátumban: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;` .
        + A 3,2-es és a 3,6-es verziójú **MongoDB gyűjtemények** a következő formátumot használják a Azure Portal Cosmos db fiókjához: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb`
        + A **Gremlin gráfok és a Cassandra-táblázatok** esetében regisztráljon a [GateD indexelő előzetes](https://aka.ms/azure-cognitive-search/indexer-preview) verziójára, és kérjen hozzáférést az előzetes verzióhoz, és tájékozódjon a hitelesítő adatok formázásáról.
    1.  Felügyelt identitás-kapcsolatok karakterlánca, amelynek a következő formátuma nem tartalmazza a fiók kulcsát: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)` . A kapcsolódási karakterlánc formátumának használatához kövesse a következő utasításokat: [Indexelő-kapcsolatok beállítása egy Cosmos db adatbázishoz felügyelt identitás használatával](search-howto-managed-identities-cosmos-db.md).

+ Az **adatbázis** egy meglévő adatbázis a fiókból. 

+ A **gyűjtemény** a dokumentumok tárolója. A dokumentumoknak léteznie kell ahhoz, hogy az importálás sikeres legyen. 

+ A **lekérdezés** üres is lehet, ha az összes dokumentumot szeretné használni, ellenkező esetben olyan lekérdezést is beírhat, amely kiválasztja a dokumentum részhalmazát. A **lekérdezés** csak az SQL API-hoz érhető el.

   ![Cosmos DB adatforrás-definíció](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB adatforrás-definíció")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 – a "tartalom gazdagítása" lap kihagyása a varázslóban

A kognitív képességek (vagy a dúsítás) hozzáadása nem szükséges importálási követelmény. Ha nem rendelkezik konkrét [mesterséges intelligencia-bővítéssel](cognitive-search-concept-intro.md) az indexelési folyamathoz, hagyja ki ezt a lépést.

A lépés kihagyásához kattintson a lap alján található kék gombra a "Next" és a "Skip" (kihagyás) elemre.

### <a name="5---set-index-attributes"></a>5 – index attribútumainak beállítása

Az **index** lapon meg kell jelennie egy adattípusú mezők listájának, valamint egy sor jelölőnégyzet az index attribútumainak beállításához. A varázsló metaadatok alapján és a forrásadatok mintavételezésével hozhatja meg a mezők listáját. 

Az attribútumok tömeges kiválasztásához kattintson az attribútum oszlop tetején található jelölőnégyzetre. Válassza a **beolvasható** és **kereshető** lehetőséget minden olyan mező esetében, amelyet vissza kell adni egy ügyfélalkalmazás számára, és a teljes szöveges keresés feldolgozására is érvényes. Megfigyelheti, hogy az egész számok nem teljes szöveges vagy zavaros kereshetők (a számok szó szerint vannak kiértékelve, és gyakran hasznosak a szűrőkben).

További információkért tekintse át az [index attribútumainak](/rest/api/searchservice/create-index#bkmk_indexAttrib) és a [nyelvi elemzők](/rest/api/searchservice/language-support) leírását. 

Szánjon egy kis időt a kiválasztott elemek áttekintésére. A varázsló futtatása után a rendszer létrehozza a fizikai adatstruktúrákat, és nem tudja szerkeszteni ezeket a mezőket az összes objektum eldobása és újbóli létrehozása nélkül.

   ![Cosmos DB index definíciója](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB index definíciója")

### <a name="6---create-indexer"></a>6 – indexelő létrehozása

Teljes mértékben meg van adva, a varázsló három különböző objektumot hoz létre a keresési szolgáltatásban. Az adatforrás-objektumok és az index objektumok elnevezett erőforrásokként lesznek mentve az Azure Cognitive Search szolgáltatásban. Az utolsó lépés egy indexelő objektumot hoz létre. Az indexelő elnevezése lehetővé teszi, hogy önálló erőforrásként is használható legyen, amelyet az index és az adatforrás objektumtól függetlenül ütemezhet és kezelhet, amely ugyanabban a varázsló-sorozatban jön létre.

Ha nem ismeri az indexelő funkciót, az *Indexelő* egy olyan erőforrás az Azure Cognitive Searchban, amely egy külső adatforrást mutat be a kereshető tartalomhoz. Az **adatimportálás** varázsló kimenete egy indexelő, amely feltérképezi a Cosmos DB adatforrást, Kinyeri a kereshető tartalmat, és importálja azt egy Azure-beli Cognitive Search indexbe.

Az alábbi képernyőfelvételen az alapértelmezett indexelő konfiguráció látható. Ha egyszer szeretné futtatni az indexelő, váltson **egyszerre** . Kattintson a **Submit (elküldés** ) gombra a varázsló futtatásához és az összes objektum létrehozásához. Az indexelés azonnal megkezdődik.

   ![Cosmos DB indexelő definíciója](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB indexelő definíciója")

Az adatimportálást a portál oldalain lehet figyelni. Az állapotjelző értesítések az indexelési állapotot és a feltöltött dokumentumok számát jelzik. 

Az indexelés befejezésekor a [Search Explorer](search-explorer.md) használatával kérdezheti le az indexet.

> [!NOTE]
> Ha nem látja a várt adattípust, előfordulhat, hogy további attribútumokat kell beállítania további mezőknél. Törölje az imént létrehozott indexet és indexelő, és ismételje meg a varázslót, és módosítsa az index attribútumainak kiválasztását az 5. lépésben. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API-k használata

A REST API használatával indexelheti Azure Cosmos db az összes indexelő munkafolyamatot követő háromrészes munkafolyamatot az Azure Cognitive Searchban: hozzon létre egy adatforrást, hozzon létre egy indexet, hozzon létre egy indexelő. A Cosmos DB adatokból való kivonása akkor történik meg, amikor elküldi a Create Indexer-kérést. A kérés befejezését követően lekérdezhető index jelenik meg. 

> [!NOTE]
> Cosmos DB Gremlin API-ból vagy Cosmos DB-Cassandra APIból származó adatok indexeléséhez először az [űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)kitöltésével kell hozzáférést kérnie a kezdeményezett előnézetekhez. A kérelem feldolgozása után az adatforrások létrehozásához a [REST API 2020-06-30-es verziójának előzetes verzióját](search-api-preview.md) kell használnia.

A cikk korábbi részeiben már említettük, hogy [Azure Cosmos db indexelés](../cosmos-db/index-overview.md) és az [Azure Cognitive Search indexelési](search-what-is-an-index.md) indexelés különböző művelet. Cosmos DB indexeléshez alapértelmezés szerint a rendszer az összes dokumentumot automatikusan indexeli, kivéve a Cassandra API. Ha kikapcsolja az automatikus indexelést, a dokumentumok csak a saját vagy a dokumentumok AZONOSÍTÓjának használatával érhetők el. Az Azure Cognitive Search indexeléséhez Cosmos DB automatikus indexelést kell bekapcsolni az Azure Cognitive Search által indexelt gyűjteményben. Amikor regisztrál a Cosmos DB Cassandra API indexelő előzetes verziójára, útmutatást kap a Cosmos DB indexelés beállításával kapcsolatban.

> [!WARNING]
> Azure Cosmos DB a DocumentDB következő generációja. Korábban a **2017-11-11** -es API-verzióval használhatja a `documentdb` szintaxist. Ez azt jelentette, hogy az adatforrás típusát a következőként adja meg: `cosmosdb` vagy `documentdb` . Az API **2019-05-06** -es verziójától kezdve az Azure Cognitive Search API-k és a portál csak a `cosmosdb` jelen cikkben leírtaknak megfelelően támogatja a szintaxist. Ez azt jelenti, hogy az adatforrás típusának a `cosmosdb` Cosmos db-végponthoz való kapcsolódáshoz is csatlakoznia kell.

### <a name="1---assemble-inputs-for-the-request"></a>1 – bemenetek összegyűjtése a kérelemhez

Minden kérelem esetében meg kell adnia a szolgáltatás nevét és a rendszergazdai kulcsot az Azure Cognitive Search (a POST fejlécben), valamint a blob Storage-hoz tartozó Storage-fiók nevét és kulcsát. A [Poster](search-get-started-rest.md) vagy a [Visual Studio Code](search-get-started-vs-code.md) használatával http-kéréseket küldhet az Azure Cognitive Searchnak.

Másolja a következő négy értéket a Jegyzettömbbe, hogy beillessze őket egy kérelembe:

+ Azure Cognitive Search szolgáltatás neve
+ Azure Cognitive Search rendszergazdai kulcs
+ Cosmos DB a kapcsolatok karakterlánca

Ezeket az értékeket a portálon találja:

1. Az Azure Cognitive Search portál oldalain másolja a keresési szolgáltatás URL-címét az Áttekintés lapról.

2. A bal oldali navigációs ablaktáblán kattintson a **kulcsok** elemre, majd másolja az elsődleges vagy a másodlagos kulcsot (ezek egyenértékűek).

3. Váltson a Cosmos Storage-fiók portál oldalaira. A bal oldali navigációs ablaktábla **Beállítások** területén kattintson a **kulcsok** elemre. Ez az oldal egy URI-t, két kapcsolatok karakterláncot és két kulcsot tartalmaz. Másolja az egyik kapcsolódási karakterláncot a Jegyzettömbbe.

### <a name="2---create-a-data-source"></a>2 – adatforrás létrehozása

Az **adatforrások** az index, a hitelesítő adatok és az adatok változásainak azonosítására szolgáló szabályzatokat határozzák meg (például módosított vagy törölt dokumentumok a gyűjteményen belül). Az adatforrás független erőforrásként van definiálva, így több indexelő is használható.

Adatforrás létrehozásához hozzon létre egy POST-kérést:

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }
```

A kérelem törzse tartalmazza az adatforrás definícióját, amelynek tartalmaznia kell a következő mezőket:

| Mező   | Leírás |
|---------|-------------|
| **név** | Kötelező. Válasszon egy tetszőleges nevet az adatforrás-objektum megjelenítéséhez. |
|**típusa**| Kötelező. Kell lennie `cosmosdb` . |
|**hitelesítő adatok** | Kötelező. A Cosmos DB a kapcsolatok karakterlánc-formátumát vagy a felügyelt identitás-kapcsolatok karakterlánc-formátumát kell követnie.<br/><br/>**SQL-gyűjtemények** esetén a kapcsolatok karakterláncai az alábbi formátumok bármelyikét követhetik: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<li>Felügyelt identitás-kapcsolatok karakterlánca, amelynek a következő formátuma nem tartalmazza a fiók kulcsát: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;` . A kapcsolódási karakterlánc formátumának használatához kövesse a következő utasításokat: [Indexelő-kapcsolatok beállítása egy Cosmos db adatbázishoz felügyelt identitás használatával](search-howto-managed-identities-cosmos-db.md).<br/><br/>A 3,2-es és a 3,6-es verziójú **MongoDB gyűjtemények** a következő formátumok valamelyikét használják a kapcsolatok karakterláncához: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<li>Felügyelt identitás-kapcsolatok karakterlánca, amelynek a következő formátuma nem tartalmazza a fiók kulcsát: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;ApiKind=MongoDb;` . A kapcsolódási karakterlánc formátumának használatához kövesse a következő utasításokat: [Indexelő-kapcsolatok beállítása egy Cosmos db adatbázishoz felügyelt identitás használatával](search-howto-managed-identities-cosmos-db.md).<br/><br/>A **Gremlin gráfok és a Cassandra-táblázatok** esetében regisztráljon a [GateD indexelő előzetes](https://aka.ms/azure-cognitive-search/indexer-preview) verziójára, és kérjen hozzáférést az előzetes verzióhoz, és tájékozódjon a hitelesítő adatok formázásáról.<br/><br/>Kerülje a portok számát a végpont URL-címében. Ha a portszámot is tartalmazza, az Azure Cognitive Search nem tudja indexelni a Azure Cosmos DB-adatbázist.|
| **tároló** | A következő elemeket tartalmazza: <br/>**név**: kötelező. Az indexelni kívánt adatbázis-gyűjtemény AZONOSÍTÓjának meghatározása.<br/>**lekérdezés**: nem kötelező. Megadhat egy lekérdezést, amely egy tetszőleges JSON-dokumentumot lelapul egy olyan egyszerű sémába, amelyet az Azure Cognitive Search tud indexelni.<br/>A MongoDB API, a Gremlin API és a Cassandra API esetében a lekérdezések nem támogatottak. |
| **dataChangeDetectionPolicy** | Ajánlott. Lásd: [módosított dokumentumok indexelése](#DataChangeDetectionPolicy) szakasz.|
|**dataDeletionDetectionPolicy** | Választható. Lásd: [törölt dokumentumok indexelése](#DataDeletionDetectionPolicy) szakasz.|

### <a name="using-queries-to-shape-indexed-data"></a>Indexelt adatformátumok használata lekérdezések használatával
Megadhat egy SQL-lekérdezést a beágyazott tulajdonságok vagy tömbök, a Project JSON-tulajdonságok és az indexelni kívánt adatszűréshez. 

> [!WARNING]
> Az egyéni lekérdezések nem támogatottak a **MONGODB API**, a **Gremlin api** és a **Cassandra API** esetén: a `container.query` paraméternek null értékűnek vagy elhagyott értéknek kell lennie. Ha egyéni lekérdezést kell használnia, kérjük, tudassa velünk a [felhasználói hangon](https://feedback.azure.com/forums/263029-azure-search).

Példa dokumentumra:

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

Lekérdezés szűrése:

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

Lekérdezés összeolvasztása:

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Leképezési lekérdezés:

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Tömb-összeolvasztási lekérdezés:

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

### <a name="3---create-a-target-search-index"></a>3 – cél keresési index létrehozása 

Ha még nem rendelkezik ilyennel, [hozzon létre egy cél Azure Cognitive Search indexet](/rest/api/searchservice/create-index) . Az alábbi példa egy azonosítót és egy leírás mezőt tartalmazó indexet hoz létre:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }
```

Győződjön meg arról, hogy a célként megadott index sémája kompatibilis a forrás JSON-dokumentumok sémájával vagy az egyéni lekérdezési leképezés kimenetével.

> [!NOTE]
> A particionált gyűjtemények esetében az alapértelmezett dokumentum kulcsa Azure Cosmos DB `_rid` tulajdonsága, amelyet az Azure Cognitive Search automatikusan átnevez, mert a `rid` mezőnevek nem kezdődhetnek aláhúzás karakterrel. Emellett Azure Cosmos DB `_rid` értékek olyan karaktereket tartalmaznak, amelyek érvénytelenek az Azure Cognitive Search kulcsaiban. Emiatt az `_rid` értékek Base64 kódolású.
> 
> A MongoDB-gyűjtemények esetében az Azure Cognitive Search automatikusan átnevezi a tulajdonságot a következőre: `_id` `id` .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>A JSON-adattípusok és az Azure Cognitive Search adattípusok közötti leképezés
| JSON-adattípus | Kompatibilis cél index típusú mezők |
| --- | --- |
| Logikai |EDM. Boolean, EDM. String |
| Egész számokhoz hasonló számok |EDM. Int32, EDM. Int64, EDM. String |
| A lebegő pontokhoz hasonló számok |EDM. Double, EDM. String |
| Sztring |Edm.String |
| Egyszerű típusok tömbje, például ["a", "b", "c"] |Collection(Edm.String) |
| A dátumokhoz hasonló karakterláncok |EDM. DateTimeOffset, EDM. String |
| GeoJSON objektumok, például {"type": "pont", "koordináták": [Long, Lat]} |Edm.GeographyPoint |
| Egyéb JSON-objektumok |N/A |

### <a name="4---configure-and-run-the-indexer"></a>4 – az indexelő konfigurálása és futtatása

Miután létrehozta az indexet és az adatforrást, készen áll az indexelő létrehozására:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

Ez az indexelő két óránként fut (az ütemezett időköz értéke "PT2H"). Az indexelő 30 percenkénti futtatásához állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemterv nem kötelező – ha nincs megadva, az indexelő csak egyszer fut a létrehozáskor. Az indexelő igény szerinti futtatása azonban bármikor elvégezhető.   

Az indexelő API létrehozásával kapcsolatos további információkért tekintse meg az [Indexelő létrehozása](/rest/api/searchservice/create-indexer)című leírást.

Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [Az Azure Cognitive Search indexelő szolgáltatásának beosztása](search-howto-schedule-indexers.md).

## <a name="use-net"></a>A .NET használata

Az általánosan elérhető .NET SDK teljes paritással rendelkezik az általánosan elérhető REST API. Javasoljuk, hogy tekintse át az előző REST API szakaszt a fogalmak, a munkafolyamatok és a követelmények megismeréséhez. A következő .NET API-referenciák dokumentációjában a JSON-indexelő implementálása felügyelt kódban végezhető el.

+ [azure.search.documents. Indexes. models. searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents. Indexes. models. searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents. Indexes. models. searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents. Indexes. models. SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Módosított dokumentumok indexelése

Az adatváltozás-észlelési szabályzat célja, hogy hatékonyan azonosítsa a módosított adatelemeket. Jelenleg az egyetlen támogatott szabályzat a [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy) `_ts` Azure Cosmos db által biztosított (timestamp) tulajdonság, amely a következőképpen van megadva:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

A szabályzat használata kifejezetten ajánlott a megfelelő indexelő teljesítmény biztosításához. 

Ha egyéni lekérdezést használ, győződjön meg arról, hogy a `_ts` lekérdezés a tulajdonságot kitervezte.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Növekményes előrehaladás és egyéni lekérdezések

Az indexelés során fellépő növekményes előrehaladás biztosítja, hogy ha az indexelő végrehajtását átmeneti hibák vagy végrehajtási időkorlát miatt megszakítja, az indexelő elvégezheti, hogy a legközelebb Mikor fusson, és ne kelljen újraindexelni a teljes gyűjteményt. Ez különösen fontos a nagyméretű gyűjtemények indexelése során. 

Ha egyéni lekérdezés használatakor szeretné engedélyezni a növekményes előrehaladást, győződjön meg arról, hogy a lekérdezés az oszlop alapján rendeli az eredményeket `_ts` . Ez lehetővé teszi, hogy az Azure Cognitive Search a meghibásodások jelenléte terén növekményes előrehaladást biztosítson.   

Bizonyos esetekben, még akkor is, ha a lekérdezés tartalmaz egy `ORDER BY [collection alias]._ts` záradékot, előfordulhat, hogy az Azure Cognitive Search nem következtet arra, hogy a lekérdezést a által megrendelte `_ts` . Megadhatja az Azure-Cognitive Search, hogy az eredmények a `assumeOrderByHighWaterMarkColumn` Configuration (konfiguráció) tulajdonság használatával legyenek rendezve. A célzás megadásához az alábbi módon hozza létre vagy frissítse az indexelő: 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Törölt dokumentumok indexelése

Ha a sorok törlődnek a gyűjteményből, általában törölni kívánja ezeket a sorokat a keresési indexből is. Az adattörlési észlelési szabályzat célja, hogy hatékonyan azonosítsa a törölt adatelemeket. Jelenleg az egyetlen támogatott házirend a `Soft Delete` házirend (a törlés egy bizonyos rendezési jelzővel van megjelölve), amely a következőképpen van megadva:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

Ha egyéni lekérdezést használ, győződjön meg arról, hogy a által hivatkozott tulajdonság `softDeleteColumnName` a lekérdezés szerint van-e kiválasztva.

Az alábbi példa egy olyan adatforrást hoz létre, amely egy törlési szabályzattal rendelkezik:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

## <a name="next-steps"></a><a name="NextSteps"></a>Következő lépések

Gratulálunk! Megtanulta, hogyan integrálhatja Azure Cosmos DB az Azure Cognitive Search indexelő használatával.

* Ha többet szeretne megtudni a Azure Cosmos DBről, tekintse meg a [Azure Cosmos db szolgáltatás lapot](https://azure.microsoft.com/services/cosmos-db/).
* Ha többet szeretne megtudni az Azure Cognitive Searchről, tekintse meg a [keresési szolgáltatás oldalát](https://azure.microsoft.com/services/search/).