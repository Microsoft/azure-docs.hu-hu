---
title: Azure Cosmos DB SQL Python API, SDK & erőforrások
description: Ismerje meg az SQL Python API-t és az SDK-t, beleértve a kiadási dátumokat, a kiesés dátumát, valamint a Python SDK egyes verziói Azure Cosmos DB módosításait.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 04/06/2021
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: ff551c5d677029f39d9a3db3a64f3d03e2c57eba
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366028"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SQL API-hoz készült Azure Cosmos DB Python SDK: Kibocsátási megjegyzések és erőforrások
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP-összekötő](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

| Oldal| Hivatkozás |
|---|---|
|**SDK letöltése**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-dokumentáció**|[A Python API referenciadokumentációja](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos?view=azure-python&preserve-view=true)|
|**SDK telepítési útmutatója**|[Python SDK telepítési útmutatója](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Első lépések**|[A Python SDK első lépése](create-sql-api-python.md)|
|**Az aktuálisan támogatott platform**|[Python 2.7](https://www.python.org/downloads/) és [Python 3.6+](https://www.python.org/downloads/)|

## <a name="release-history"></a>Kiadási előzmények

## <a name="420"></a>4.2.0

**Hibajavítások**
- Kijavítva a hiba, amely miatt a folytatási token nem query_iterable, amikor a query_iterable az eredményeket lapról lapra lekérte.
- Kijavítva a hiba, amely miatt a dokumentum olvasása és törlése nem adott meg erőforrás-jogkivonatokat. 

**Új funkciók**
- A változáscsatorna `partitionKey` lekérdezése közbeni átadás támogatása hozzáadva.

## <a name="410"></a>4.1.0

- Elalasztó figyelmeztetés hozzáadva a "lusta" indexelési módhoz. A háttér már nem teszi lehetővé tárolók létrehozását ezzel a móddal, és ehelyett konzisztensre fogja állítani őket.

**Új funkciók**
- Az elemzési tár TTL beállításának lehetősége új tároló létrehozásakor hozzáadva.

**Hibajavítások**
- Ki van javítva `dicts` a támogatása bemeneti get_client API-kban.
- Ki van javítva a Python 2/3 kompatibilitása a lekérdezési iterátorokban.
- Ki van javítva a típusmutató hibája.
- Kijavítva a hiba, amely miatt a beállításfejlécek nem voltak hozzáadva upsert_item függvényhez. 
- Kijavítva a hiba, amely akkor lépett fel, amikor egy elem nem sztringazonosítót használt. Mostantól a TypeErrort emeli ki az AttributeError helyett.


## <a name="400"></a>4.0.0

* Stabil kiadás.
* A HttpLoggingPolicy hozzá van adva a folyamathoz, hogy lehetővé tegye egy egyéni naplózó átadását a kérés- és válaszfejlécek számára.

### <a name="400b6"></a>4.0.0b6

* Javítva a hiba a synchronized_request API-kban.
* A MediaReadMode és a MediaRequestTimeout el lett távolítva a ConnectionPolicyből, mivel a médiakérések nem támogatottak.

### <a name="400b5"></a>4.0.0b5

* az azure.cosmos.errors modul elavult, és az azure.cosmos.exceptions váltotta fel
* A hozzáférési feltétel paraméterei ( , , ) elavultak a `access_condition` `if_match` különálló és paraméterek `if_none_match` `match_condition` `etag` érdekében.
* Az útválasztási térkép szolgáltatójának hibajavítása.
* Hozzá van adva a Distinct(eltérők) és a Offset (Eltolás) és a Limit (Korlát) lekérdezés támogatása.
* Az alapértelmezett dokumentumlekérdezés végrehajtási környezete mostantól a következőre van használva:

  * Változáscsatorna-lekérdezések
  * egypartíciós lekérdezések ( `partitionkey` , a lehetőségek között `partitionKeyRangeId` található)
  * Nem dokumentum jellegű lekérdezések

* Több partíció összesítésére vonatkozó hibák, amelyeknél a partíciók közötti lekérdezés engedélyezése igaz értékre van állítva, de nincs "value" kulcsszó
* Lekéri a lekérdezésterv végpontját más forgatókönyvekhez a lekérdezésterv lekéréséhez
* `__repr__`Cosmos-entitásobjektumok támogatása hozzáadva.
* Frissített dokumentáció.

### <a name="400b4"></a>4.0.0b4

* Az összes művelethez hozzá van adva egy kulcsszó-argumentum támogatása, amely másodpercben adja meg az abszolút időtúllépést, amelyen belül a műveletet `timeout` végre kell adni. Ha túllépi az időtúllépési értéket, a `azure.cosmos.errors.CosmosClientTimeoutError` érték lesz megemelve.

* Új hozzáadva az `ConnectionRetryPolicy` újrapróbálkozási viselkedés kezelésére HTTP-kapcsolati hibák esetén.

* Új konstruktor és műveletenkénti konfigurációs kulcsszavas argumentumok hozzáadva:

  * `retry_total` – Az újrapróbálkozási kísérletek maximális száma.
  * `retry_backoff_max` – Az újrapróbálkozási várakozások maximális ideje másodpercben.
  * `retry_fixed_interval` – Az újrapróbálkozási időköz ezredmásodpercben javítva.
  * `retry_read` – A szoftvercsatornák olvasási újrapróbálkozási kísérletének maximális száma.
  * `retry_connect` – A csatlakozási hibák újrapróbálkozási kísérletének maximális száma.
  * `retry_status` – A hibaállapotkódok újrapróbálkozási kísérletének maximális száma.
  * `retry_on_status_codes` – Az újrapróbálkozási állapotkódok listája.
  * `retry_backoff_factor` – Tényező az újrapróbálkozási kísérletek közötti várakozási idő kiszámításához.

### <a name="400b3"></a>4.0.0b3

* És `create_database_if_not_exists()` funkciók `create_container_if_not_exists` hozzáadva a CosmosClienthez és a Database-hez.

### <a name="400b2"></a>4.0.0b2

* A 4.0.0b2-es verzió a második iteráció, amely a Python nyelvhez ajánlott eljárásoknak megfelelő ügyféloldali kódtárat hoz létre.

**Kompatibilitástörő változások**

* Az ügyfélkapcsolat úgy lett adaptálva, hogy az a-ban meghatározott HTTP-folyamatot `azure.core.pipeline` felhasználja.

* Az interaktív objektumok mostantól proxykként vannak átnevezve. Ide tartoznak az alábbiak:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* A konstruktora `CosmosClient` frissítve lett:

  * A `auth` paraméter új nevet ad, és mostantól közvetlenül a hitelesítés típusát veszi `credential` fel. Ez azt jelenti, hogy az elsődleges kulcs értéke, az erőforrás-jogkivonatok szótára vagy az engedélyek listája is átvehet. A régi szótárformátum azonban továbbra is támogatott.

  * A paraméter csak kulcsszós paraméterként lett megadva, és bár továbbra is támogatott, a szabályzat minden egyes attribútuma mostantól explicit kulcsszavas argumentumként is `connection_policy` átveheti:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Egy új konstruktor lett hozzáadva a-hoz, amely lehetővé teszi a létrehozást egy kapcsolati sztringen keresztül, amely `CosmosClient` a Azure Portal.

* Egyes `read_all` műveleteket a rendszer átnevezett `list` műveletekre:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Minden olyan művelet, amely vagy paramétert használ, átkerült a csak `request_options` `feed_options` kulcsszóval megadott paraméterekbe. Emellett, bár ezek a beállítási szótárak továbbra is támogatottak, a szótárban található egyes beállítások mostantól explicit kulcsszó-argumentumokként is támogatottak.

* A hibahierarchia innen `azure.core.AzureError` öröklődik:

  * A `HTTPFailure` új nevet kapott: `CosmosHttpResponseError`
  * `JSONParseFailure` el lett távolítva, és a következő váltotta fel: `azure.core.DecodeError`
  * További hibák hozzáadva adott válaszkódok esetén:
    * `CosmosResourceNotFoundError` a 404-es állapothoz
    * `CosmosResourceExistsError` a 409-es állapothoz
    * `CosmosAccessConditionFailedError` a 412-es állapothoz

* `CosmosClient` mostantól futtatható egy környezetkezelőben az ügyfélkapcsolat bezárásának kezelésével.

* Az iterható válaszok (például a lekérdezési válaszok és a listaválaszok) mostantól `azure.core.paging.ItemPaged` típusúak. A `fetch_next_block` metódust egy másodlagos iterátor váltotta fel, amelyet a metódus `by_page` elért.

### <a name="400b1"></a>4.0.0b1

A 4.0.0b1-es verzió az első olyan felhasználóbarát ügyféloldali kódtár létrehozása, amely megfelel a Python nyelv ajánlott eljárásának. További információt erről és az egyéb Azure SDK-kódtárak előzetes kiadásiról itt található: https://aka.ms/azure-sdk-preview1-python .

**Használonváltozások: Új API-kialakítás**

* A műveletek hatóköre mostantól egy adott ügyfélre terjed ki:

  * `CosmosClient`: Ez az ügyfél kezeli a fiókszintű műveleteket. Ez magában foglalja a szolgáltatástulajdonságok felügyeletét és a fiókokban található adatbázisok listázását.
  * `Database`: Ez az ügyfél kezeli az adatbázisszintű műveleteket. Ez magában foglalja a tárolók, felhasználók és tárolt eljárások létrehozását és törlését. A cosmosClient példányból név alapján érhető el.
  * `Container`: Ez az ügyfél egy adott tároló műveleteit kezeli. Ide tartozik az elemek lekérdezése és beszúrása, valamint a tulajdonságok kezelése.
  * `User`: Ez az ügyfél egy adott felhasználó műveleteit kezeli. Ez magában foglalja az engedélyek hozzáadását és törlését, valamint a felhasználói tulajdonságok felügyeletét.

    Ezek az ügyfelek az ügyfélhierarchiában a metódussal érhetők `get_<child>_client` el. Az új API-val kapcsolatos részletes információkért tekintse meg a [referenciadokumentációt.](https://aka.ms/azsdk-python-cosmos-ref)

* Az ügyfelekhez nem azonosító, hanem név alapján lehet hozzáférni. A hivatkozások létrehozásához nem kell sztringeket össze concatened.

* Az egyes modulokból már nem kell típusokat és metódusokat importálni. A nyilvános API felület közvetlenül a csomagban érhető `azure.cosmos` el.

* Az egyes kéréstulajdonságok kulcsszavas argumentumokként is megszabadhatóak különálló példányok `RequestOptions` összeépítése helyett.

### <a name="302"></a>3.0.2

* A MultiPolygon Datatype támogatása hozzáadva
* Hibajavítás a munkamenet-olvasási újrapróbálkozási szabályzatban
* Hibajavítás a 64-es sztringek dekódolása közbeni helytelen kitöltési problémákhoz

### <a name="301"></a>3.0.1

* Hibajavítás a LocationCache-ban
* Hibajavítási végpont újrapróbálkozási logikája
* A dokumentáció kijavítva

### <a name="300"></a>3.0.0

* Többrégió írási támogatása hozzáadva
* Névváltozások
  * DocumentClient – CosmosClient
  * Gyűjtemény tárolóba
  * Dokumentum–elem
  * Csomagnév frissítve az "azure-cosmos" névre
  * Névtér frissítve az "azure.cosmos" névtérre

### <a name="233"></a>2.3.3

* Proxy támogatása hozzáadva
* Változáscsatorna olvasásának támogatása hozzáadva
* Gyűjteménykvóta-fejlécek támogatása hozzáadva
* Nagy méretű munkamenet-jogkivonatok hibajavítása
* A ReadMedia API hibajavítása
* Hibajavítás a partíciókulcs-tartomány gyorsítótárában

### <a name="232"></a>2.3.2

* Támogatás hozzáadva az alapértelmezett újrabeállításhoz a csatlakozási problémák esetén.

### <a name="231"></a>2.3.1

* Frissült a dokumentáció, hogy Azure Cosmos DB Azure DocumentDB helyett hivatkozni.

### <a name="230"></a>2.3.0

* Ehhez az SDK-verzióhoz a Azure Cosmos DB Emulator legújabb verziója szükséges, amely letölthető a következő ről: https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* hibajavítás az összesített dicthez
* hibajavítás perjel levágása az erőforrás-hivatkozásban
* Unicode-kódolási tesztek

### <a name="220"></a>2.2.0

* A Kérelemegység percenként (RU/m) funkció támogatása hozzáadva.
* Új konzisztenciaszint támogatása ConsistentPrefix néven.

### <a name="210"></a>2.1.0

* Az összesítési lekérdezések (COUNT, MIN, MAX, SUM és AVG) támogatása hozzáadva.
* Hozzá van adva egy lehetőség az SSL-ellenőrzés letiltására a DocumentDB Emulatoron való futtatáskor.
* A függő kérelmek modul korlátozásának eltávolítása, hogy pontosan 2.10.0 legyen.
* A particionált gyűjtemények minimális átviteli sebességének 10 100 RU/s-ről 2500 RU/s-re csökkent.
* Parancsprogram-naplózás engedélyezése a tárolt eljárás végrehajtása során.
* REST API kiadással a 2017. 01. 19. verzióra nőtt.

### <a name="201"></a>2.0.1

* Szerkesztői módosításokat eszköztem a dokumentációs megjegyzésekben.

### <a name="200"></a>2.0.0

* A Python 3.5 támogatása.
* A kapcsolatkészletezés támogatása a requests modullal.
* A munkamenet-konzisztencia támogatása.
* A particionált gyűjtemények TOP/ORDERBY-lekérdezései már támogatottak.

### <a name="190"></a>1.9.0

* Újrapróbálkozási szabályzat támogatása hozzáadva a kérelmek szabályozásához. (A szabályozásos kérelmek túl nagy kérelemarányú kivételt, 429-es hibakódot kapnak.) Alapértelmezés szerint a DocumentDB kilencszer próbálkozik újra minden kéréshez a 429-es hibakód esetén, a válaszfejlécben a retryAfter idő tiszteletben állítva.
  Most már beállítható egy rögzített újrapróbálkozási időköz a ConnectionPolicy objektum RetryOptions tulajdonságának részeként, ha figyelmen kívül szeretné hagyni a kiszolgáló által az újrapróbálkozások között visszaadott retryAfter időt.
  A DocumentDB mostantól (az újrapróbálkozások számától függetlenül) legfeljebb 30 másodpercet vár az egyes kérelmekre, és a 429-es hibakóddal adja vissza a választ.
  Ez az idő a ConnectionPolicy objektum RetryOptions tulajdonságában is felülbírálható.

* A DocumentDB mostantól visszaadja az x-ms-throttle-retry-count és az x-ms-throttle-retry-wait-time-ms válaszfejléceket minden kérésben, amely az újrapróbálkozások számát jelöli, valamint azt az összesített időt, ameddig a kérés várakozott az újrapróbálkozások között.

* Eltávolítottuk a RetryPolicy osztályt és a megfelelő tulajdonságot (retry_policy) a document_client osztályban, és ehelyett egy RetryOptions osztályt vezettünk be, amely a ConnectionPolicy osztály RetryOptions tulajdonságát teszi elérhetővé, amely felülbírál néhány alapértelmezett újrapróbálkozási beállítást.

### <a name="180"></a>1.8.0

* A georeplikált adatbázisfiókok támogatása hozzáadva.
* Tesztjavítások a globális gazdagép és a masterKey egyéni tesztosztályba való áthelyezésére.

### <a name="170"></a>1.7.0

* A time to live (TTL) funkció támogatása hozzáadva a dokumentumokhoz.

### <a name="161"></a>1.6.1

* A kiszolgálóoldali particionálással kapcsolatos hibajavítások, amelyek speciális karaktereket engedélyeznek a partíciókulcs elérési útján.

### <a name="160"></a>1.6.0

* A kiszolgálóoldali particionált gyűjtemények szolgáltatás támogatása hozzáadva.

### <a name="150"></a>1.5.0

* Ügyféloldali horizontális skálázás keretrendszer hozzáadva az SDK-hoz. HashPartionResolver és RangePartitionResolver osztályokat valósított meg.

### <a name="142"></a>1.4.2

* Az Upsert implementációja. Új UpsertXXX metódusok hozzáadva az Upsert funkció támogatásához.
* Implementálja ID-Based Útválasztást. Nincs nyilvános API-módosítás, minden belső.

### <a name="130"></a>1.3.0

* A kiadás kihagyva, hogy a verziószám igazodva egymáshoz igazodva egymáshoz igazodva ugorva ugorható le.

### <a name="120"></a>1.2.0

* Támogatja a térinformatikai indexet.
* Ellenőrzi az összes erőforrás azonosító tulajdonságát. Az erőforrások azonosítói nem tartalmazhatnak `?, /, #, \\` karaktereket, és nem végződhet szóközre.
* Új fejléc "indexátalakítási folyamat" hozzáadása a ResourceResponse erőforráshoz.

### <a name="110"></a>1.1.0

* Megvalósítja a V2 indexelési szabályzatot

### <a name="101"></a>1.0.1

* Támogatja a proxykapcsolatot

## <a name="release--retirement-dates"></a>Kiadási & dátumok

A Microsoft legalább **12** hónappal az SDK-k kieső leváltása előtt értesítést küld, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra. Új szolgáltatásokkal, funkciókkal és optimalizálásokkal csak az aktuális SDK bővül, ezért azt javasoljuk, hogy a mindig lehető leghamarabb frissítsen a legújabb SDK-verzióra.

> [!WARNING]
> 2022. augusztus 31-től a Azure Cosmos DB nem javítja ki a hibákat, és nem nyújt támogatást az Azure Cosmos DB Python SDK for SQL API 1.x és 2.x verzióihoz. Ha nem szeretne frissíteni, az SDK 1.x és 2.x verziójából küldött kéréseket továbbra is az Azure Cosmos DB kiszolgálja.

| Verzió | Kiadás dátuma | Kivonás dátuma |
| --- | --- | --- |
| [4.2.0](#420) |2020. október 09. |--- |
| [4.1.0](#410) |2020. aug. 10. |--- |
| [4.0.0](#400) |2020. május 20. |--- |
| [3.0.2](#302) |2018. nov. 15. |--- |
| [3.0.1](#301) |2018. október 4. |--- |
| [2.3.3](#233) |2018. szeptember 8. |2022. augusztus 31. |
| [2.3.2](#232) |2018. május 8. |2022. augusztus 31. |
| [2.3.1](#231) |2017. december 21. |2022. augusztus 31. |
| [2.3.0](#230) |2017. november 10. |2022. augusztus 31. |
| [2.2.1](#221) |2017. szeptember 29. |2022. augusztus 31. |
| [2.2.0](#220) |2017. május 10. |2022. augusztus 31. |
| [2.1.0](#210) |2017. május 1. |2022. augusztus 31. |
| [2.0.1](#201) |2016. október 30. |2022. augusztus 31. |
| [2.0.0](#200) |2016. szeptember 29. |2022. augusztus 31. |
| [1.9.0](#190) |2016. július 7. |2022. augusztus 31. |
| [1.8.0](#180) |2016. június 14. |2022. augusztus 31. |
| [1.7.0](#170) |2016. április 26. |2022. augusztus 31. |
| [1.6.1](#161) |2016. április 8. |2022. augusztus 31. |
| [1.6.0](#160) |2016. március 29. |2022. augusztus 31. |
| [1.5.0](#150) |2016. január 3. |2022. augusztus 31. |
| [1.4.2](#142) |2015. október 6. |2022. augusztus 31. |
| 1.4.1 |2015. október 6. |2022. augusztus 31. |
| [1.2.0](#120) |2015. augusztus 6. |2022. augusztus 31. |
| [1.1.0](#110) |2015. július 9. |2022. augusztus 31. |
| [1.0.1](#101) |2015. május 25. |2022. augusztus 31. |
| 1.0.0 |2015. április 07. |2022. augusztus 31. |
| 0.9.4 előzetes |2015. január 14. |2016. február 29. |
| 0.9.3 előzetes |2014. december 9. |2016. február 29. |
| 0.9.2 előzetes |2014. november 25. |2016. február 29. |
| 0.9.1 előzetes |2014. szeptember 23. |2016. február 29. |
| 0.9.0 –előzetes |2014. augusztus 21. |2016. február 29. |

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Következő lépések

A Cosmos DB-ről a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás oldalán tekinthet meg további információt.
