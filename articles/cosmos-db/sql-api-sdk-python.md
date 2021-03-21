---
title: Azure Cosmos DB SQL Python API, SDK &-erőforrások
description: Ismerkedjen meg az SQL Python API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat és a Azure Cosmos DB Python SDK egyes verzióiban végrehajtott módosításokat.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: 8487743efd4f18806ae03ed7529927736314988b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595686"
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
> * [Spark-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

| Oldal| Hivatkozás |
|---|---|
|**SDK letöltése**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-dokumentáció**|[Python API-referenciák dokumentációja](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos?view=azure-python&preserve-view=true)|
|**SDK telepítési utasítások**|[A Python SDK telepítési utasításai](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Első lépések**|[Ismerkedés a Python SDK-val](create-sql-api-python.md)|
|**Jelenleg támogatott platform**|[Python 2,7](https://www.python.org/downloads/) és [Python 3.5.3 +](https://www.python.org/downloads/)|

## <a name="release-history"></a>Kiadási előzmények

## <a name="420"></a>4.2.0

**Hibajavítások**
- Kijavítva a hiba, ha a folytatási tokent nem tiszteli a rendszer, ha query_iterable az eredmények oldal alapján történő beolvasására szolgál.
- Kijavítva a hiba, ahol az erőforrás-jogkivonatok nem állnak tiszteletben a dokumentumok olvasására és törlésére. 

**Új funkciók**
- További támogatás a `partitionKey` change-feed lekérdezése során végzett továbbításhoz.

## <a name="410"></a>4.1.0

- Elavult figyelmeztetés lett hozzáadva a "lusta" indexelési módhoz. A háttérrendszer már nem teszi lehetővé, hogy a tárolókat ezzel a móddal hozza létre, és a rendszer konzisztensen állítsa be őket.

**Új funkciók**
- Új tároló létrehozásakor hozzá lett adva az analitikai tároló ÉLETTARTAMának beállítása.

**Hibajavítások**
- Rögzített támogatás a `dicts` Get_client API-khoz bemenetként.
- Javított Python 2/3-kompatibilitás a lekérdezési iterációkban.
- Rögzített típusú hint-hiba.
- Kijavítva a hiba, ahol a beállítások fejlécei nem lettek hozzáadva a upsert_item függvényhez. 
- Rögzített hiba, ha nem sztring típusú azonosítót használ egy adott elemmel. Ezzel a AttributeError helyett a TypeError-t is felveti.


## <a name="400"></a>4.0.0

* Stabil kiadás.
* A HttpLoggingPolicy hozzáadása a folyamathoz, amely lehetővé teszi az átadást egy egyéni naplózó számára a kérelem és válasz fejlécek esetében.

### <a name="400b6"></a>4.0.0 B6

* Rögzített hiba a synchronized_request for Media API-k esetében.
* A MediaReadMode és a MediaRequestTimeout eltávolítása a ConnectionPolicy-ből nem támogatott.

### <a name="400b5"></a>4.0.0 B5

* Azure. Cosmos. errors modul elavult és helyébe az Azure. Cosmos. kivételek
* A hozzáférési feltétel paraméterei ( `access_condition` , `if_match` ,) a `if_none_match` különálló és a paraméterek mellett elavultak `match_condition` `etag` .
* Kijavítva a hiba az útválasztási leképezési szolgáltatóban.
* A lekérdezés különböző, eltolási és korlát-támogatással bővült.
* Az alapértelmezett dokumentum-lekérdezés végrehajtási környezete már használatban van

  * Hírcsatorna-lekérdezések módosítása
  * az egypartíciós lekérdezések (a `partitionkey` `partitionKeyRangeId` lehetőségek között szerepelnek)
  * Nem dokumentált lekérdezések

* Hibák a több partíción lévő összesítések esetében, a több partíciós lekérdezés engedélyezése igaz értékre van állítva, de nincs "érték" kulcsszó.
* Lekérdezési terv végpontjának beolvasása más forgatókönyvek lekérdezési tervének beolvasásához
* `__repr__`A Cosmos Entity-objektumok támogatása hozzáadva.
* Frissített dokumentáció.

### <a name="400b4"></a>4.0.0 B4

* Egy `timeout` kulcsszó argumentum hozzáadása az összes művelethez egy abszolút időkorlát megadásával, amelyen belül a műveletet végre kell hajtani. Ha túllépi az időtúllépési értéket, a `azure.cosmos.errors.CosmosClientTimeoutError` rendszer megemeli.

* Új Hozzáadás `ConnectionRetryPolicy` az újrapróbálkozási viselkedés kezeléséhez http-kapcsolódási hibák esetén.

* Új konstruktor és Operational Configuration kulcsszó argumentumok hozzáadva:

  * `retry_total` – Újrapróbálkozások maximális száma.
  * `retry_backoff_max` – Maximális újrapróbálkozási várakozási idő másodpercben.
  * `retry_fixed_interval` -Rögzített újrapróbálkozási időköz ezredmásodpercben.
  * `retry_read` – Az olvasási próbálkozások maximális száma.
  * `retry_connect` – A csatlakozási hiba újrapróbálkozási kísérletek maximális száma.
  * `retry_status` – Az újrapróbálkozási kísérletek maximális száma a hibák állapotának kódjain.
  * `retry_on_status_codes` – Az újrapróbálkozáshoz megadott állapotkódok listája.
  * `retry_backoff_factor` – Az újrapróbálkozási kísérletek közötti várakozási idő kiszámítására szolgáló tényező.

### <a name="400b3"></a>4.0.0 B3

* `create_database_if_not_exists()` `create_container_if_not_exists` A CosmosClient és az adatbázishoz hozzáadott és funkciókkal rendelkezik.

### <a name="400b2"></a>4.0.0 B2

* A 4.0.0 B2 verziója a második iteráció a Python nyelvi ajánlott eljárásainak megfelelő ügyféloldali kódtár létrehozásához.

**Kompatibilitástörő változások**

* Az ügyfélkapcsolatot a ben definiált HTTP-folyamat felhasználására alakítottuk ki `azure.core.pipeline` .

* Az interaktív objektumok mostantól proxyként lettek átnevezve. Ide tartoznak az alábbiak:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* A (z) konstruktora `CosmosClient` frissítve lett:

  * A `auth` paraméter át lett nevezve, `credential` és most közvetlenül a hitelesítési típust fogja végrehajtani. Ez azt jelenti, hogy az elsődleges kulcs értéke, az erőforrás-tokenek szótára vagy az engedélyek listája adható át. Azonban a régi szótár formátuma továbbra is támogatott.

  * A `connection_policy` paraméter csak a kulcsszót megadó paramétert adta meg, és a szolgáltatás továbbra is támogatott, a szabályzat egyes attribútumai mostantól explicit kulcsszó-argumentumként adhatók át:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Új konstruktor lett hozzáadva, amely `CosmosClient` lehetővé teszi a létrehozást a Azure Portalból beolvasott kapcsolódási karakterlánc használatával.

* Néhány `read_all` művelet átnevezve lett a következő `list` műveletekre:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Minden olyan művelet, amely elvégzi a `request_options` vagy a `feed_options` paramétereket, ezeket a rendszer áthelyezte a csak kulcsszavas paraméterekbe. Emellett, míg ezek a lehetőségek a szótárak továbbra is támogatottak, a szótárban lévő egyes beállítások mostantól explicit kulcsszó-argumentumként támogatottak.

* A hiba-hierarchia mostantól örökölt `azure.core.AzureError` :

  * A `HTTPFailure` új nevet kapott: `CosmosHttpResponseError`
  * `JSONParseFailure` el lett távolítva, és lecserélte `azure.core.DecodeError`
  * További hibák léptek fel az adott válasz kódokhoz:
    * `CosmosResourceNotFoundError` 404-es állapot esetén
    * `CosmosResourceExistsError` 409-es állapot esetén
    * `CosmosAccessConditionFailedError` 412-es állapot esetén

* `CosmosClient` Most már futtatható egy Context Managerben, hogy kezelni tudja a ügyfélkapcsolatot.

* Az ITER-válaszok (például a lekérdezési válaszok és a válaszok listája) mostantól típusúak `azure.core.paging.ItemPaged` . A metódust `fetch_next_block` egy másodlagos iteráció váltotta fel, amelyet a `by_page` metódus elért.

### <a name="400b1"></a>4.0.0 B1

A B1-es verzió a 4.0.0 első előzetes verziója, amely a Python nyelvi ajánlott eljárásainak megfelelő, felhasználóbarát ügyféloldali kódtár létrehozására irányul. További információt erről és az egyéb Azure SDK-kódtárak előzetes kiadásáról a következő webhelyen talál: https://aka.ms/azure-sdk-preview1-python .

**Változtatások megszakítása: új API-kialakítás**

* A műveletek mostantól egy adott ügyfél hatókörén vannak:

  * `CosmosClient`: Ez az ügyfél a fiók szintű műveleteket kezeli. Ide tartozik a szolgáltatás tulajdonságainak kezelése és a fiókban lévő adatbázisok listázása.
  * `Database`: Ez az ügyfél kezeli az adatbázis-szintű műveleteket. Ez magában foglalja a tárolók, a felhasználók és a tárolt eljárások létrehozását és törlését. A név alapján egy cosmosClient-példányból is elérhető.
  * `Container`: Ez az ügyfél egy adott tároló műveleteit kezeli. Ide tartozik az elemek lekérdezése és beszúrása, valamint a tulajdonságok kezelése.
  * `User`: Ez az ügyfél egy adott felhasználó műveleteit kezeli. Ez magában foglalja az engedélyek hozzáadását és törlését, valamint a felhasználói tulajdonságok kezelését.

    Ezek az ügyfelek a metódus használatával érhetők el az ügyfél-hierarchiában `get_<child>_client` . Az új API-val kapcsolatos részletes információkért tekintse meg a [dokumentációt](https://aka.ms/azsdk-python-cosmos-ref).

* Az ügyfelek neve nem azonosító alapján érhető el. Nem kell összefűzni a karakterláncokat a hivatkozások létrehozásához.

* Nem kell több típust és metódust importálni az egyes modulokból. A nyilvános API felületi terület közvetlenül a csomagban érhető el `azure.cosmos` .

* Az egyedi kérelmek tulajdonságai kulcsszó argumentumként adhatók meg, nem pedig külön `RequestOptions` példányok létrehozásakor.

### <a name="302"></a>3.0.2

* Többsokszögű adattípus támogatása
* Hibajavítás a munkamenetben – olvasási újrapróbálkozási házirend
* Hiba elhárítása helytelen kitöltési problémák esetén a Base 64 sztring dekódolásakor

### <a name="301"></a>3.0.1

* Hibajavítás a LocationCache
* Hibajavítási végpont újrapróbálkozási logikája
* Rögzített dokumentáció

### <a name="300"></a>3.0.0

* Több régiós írási támogatás hozzáadva
* Elnevezési változások
  * DocumentClient – CosmosClient
  * Gyűjtemény tárolóba
  * Dokumentum – tétel
  * A csomag neve frissítve az "Azure-Cosmos" értékre
  * A névtér frissítve a következőre: "Azure. Cosmos"

### <a name="233"></a>2.3.3

* Proxy-támogatás hozzáadva
* A módosítási hírcsatorna olvasásának támogatása
* A gyűjteményi kvóta fejlécének támogatása
* Hibajavítás nagyméretű munkamenet-tokenek esetén – probléma
* Hibajavítás a ReadMedia API-hoz
* Hibajavítás a partíciós kulcs tartományának gyorsítótárában

### <a name="232"></a>2.3.2

* Az alapértelmezett újrapróbálkozások támogatása a kapcsolódási problémák esetén.

### <a name="231"></a>2.3.1

* Az Azure DocumentDB helyett a Azure Cosmos DB referenciára szolgáló dokumentáció frissítve.

### <a name="230"></a>2.3.0

* Az SDK-verzióhoz a Azure Cosmos DB Emulator legújabb verziójára van szükség a letöltéshez https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* hibajavítás összesített dict
* hibajavítás az erőforrás-hivatkozás vágási perjeléhez
* Unicode kódolású tesztek tesztelése

### <a name="220"></a>2.2.0

* A kérési egység percenkénti (RU/m) funkciójának támogatása.
* Egy új, ConsistentPrefix nevű konzisztencia-szint támogatása.

### <a name="210"></a>2.1.0

* Összesítő lekérdezések támogatása (DARABSZÁM, MIN., MAX., SUM és AVG).
* Lehetőség az SSL-ellenőrzés letiltására a DocumentDB emulátoron való futtatáskor.
* A függő kérelmek moduljának letiltásával pontosan 2.10.0 kell lennie.
* A 10 100 RU/s és 2500 RU/s közötti particionált gyűjtemények minimális átviteli sebességének csökkentése.
* A parancsfájl-naplózás engedélyezésének támogatása a tárolt eljárás végrehajtása során.
* Ezzel a kiadással a REST API verziója az "2017-01-19" értékre ütközött.

### <a name="201"></a>2.0.1

* Szerkesztési módosításokat készített a dokumentációs megjegyzésekben.

### <a name="200"></a>2.0.0

* Python 3,5-támogatás hozzáadva.
* A kapcsolatok készletezésének támogatása a kérelmek modul használatával.
* A munkamenet konzisztenciájának támogatása hozzáadva.
* A rendszer hozzáadta a particionált gyűjtemények leggyakoribb/ORDERBY-lekérdezésének támogatását.

### <a name="190"></a>1.9.0

* Ismételt újrapróbálkozási szabályzat támogatása a szabályozott kérelmekhez. (A szabályozott kérelmek túl nagy kivételt kapnak, hibakód: 429.) Alapértelmezés szerint a DocumentDB a 429-es hibakód miatt kilenc alkalommal újrapróbálkozik az egyes kérelmeknél, a retryAfter idő tiszteletben tartásával.
  A ConnectionPolicy objektum RetryOptions tulajdonságának részeként megadható a rögzített újrapróbálkozási időköz, ha figyelmen kívül hagyja az újrapróbálkozások között a kiszolgáló által visszaadott retryAfter időt.
  A DocumentDB most legfeljebb 30 másodpercet vár minden szabályozott kérelem esetében (az újrapróbálkozások számától függetlenül), és a 429-es hibakódú választ adja vissza.
  Ez az idő felülbírálható a ConnectionPolicy objektum RetryOptions tulajdonságában is.

* A DocumentDB most visszaadja az x-MS-gázadagoló-újrapróbálkozás-Request-Count és az x-MS-fojtószelep-újrapróbálkozás-WAIT-Time-MS értéket az összes kérelemben, hogy az újrapróbálkozások számának és a kérésnek az újrapróbálkozások közötti kumulatív idejét is megadja.

* Eltávolította a RetryPolicy osztályt és a document_client osztályban elérhető megfelelő tulajdonságot (retry_policy), és ehelyett egy RetryOptions osztályt vezetett be, amely a ConnectionPolicy osztály RetryOptions tulajdonságát használja, amely az alapértelmezett újrapróbálkozások egyes beállításainak felülbírálására használható.

### <a name="180"></a>1.8.0

* Hozzáadta a Geo-replikált adatbázis-fiókok támogatását.
* A globális gazdagép és a masterKey egyéni tesztelési osztályokba való áthelyezéséhez tesztelje a javításokat.

### <a name="170"></a>1.7.0

* Az élettartam (TTL) szolgáltatás támogatása a dokumentumok számára.

### <a name="161"></a>1.6.1

* A kiszolgálóoldali particionálással kapcsolatos hibajavítások speciális karakterek a partíciós kulcs elérési útjában való engedélyezéséhez.

### <a name="160"></a>1.6.0

* Hozzáadta a kiszolgálóoldali particionált gyűjtemények funkció támogatását.

### <a name="150"></a>1.5.0

* Az SDK-hoz hozzáadott ügyféloldali horizontális Felskálázási keretrendszer. Implementált HashPartionResolver és RangePartitionResolver osztályok.

### <a name="142"></a>1.4.2

* Upsert implementálása. Új UpsertXXX-metódusok lettek hozzáadva a Upsert szolgáltatás támogatásához.
* ID-Based útválasztás implementálása. Nincs nyilvános API-változás, az összes változás belső.

### <a name="130"></a>1.3.0

* A kiadás kihagyva, hogy a verziószáma más SDK-k használatával legyen összehangolva

### <a name="120"></a>1.2.0

* A térinformatikai index támogatása.
* Érvényesíti az összes erőforrás azonosító tulajdonságát. Az erőforrások azonosítói nem tartalmazhatnak karaktereket, és nem állhatnak `?, /, #, \\` szóközzel.
* Hozzáadja az új "index átalakítási folyamat" fejlécet a ResourceResponse.

### <a name="110"></a>1.1.0

* A v2 indexelési házirend megvalósítása

### <a name="101"></a>1.0.1

* Proxy-kapcsolatok támogatása

## <a name="release--retirement-dates"></a>Kiadási & nyugdíjazási dátumok

A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra. Új szolgáltatásokkal, funkciókkal és optimalizálásokkal csak az aktuális SDK bővül, ezért azt javasoljuk, hogy a mindig lehető leghamarabb frissítsen a legújabb SDK-verzióra.

> [!WARNING]
> 2022. augusztus 31-ig a Azure Cosmos DB többé nem javít, vagy nem nyújt támogatást az SQL API-hoz készült Azure Cosmos DB Python SDK 1. x és 2. x verziójához. Ha nem szeretné, hogy a rendszer ne frissítse az SDK 1. x és 2. x verziójában küldött kérelmeket, továbbra is a Azure Cosmos DB szolgáltatás fogja kiszolgálni.

| Verzió | Kiadás dátuma | Kivonás dátuma |
| --- | --- | --- |
| [4.2.0](#420) |Október 9., 2020 |--- |
| [4.1.0](#410) |Augusztus 10, 2020 |--- |
| [4.0.0](#400) |2020. május 20. |--- |
| [3.0.2](#302) |November 15., 2018 |--- |
| [3.0.1](#301) |Oct 04, 2018 |--- |
| [2.3.3](#233) |Szeptember 08., 2018 |Augusztus 31., 2022 |
| [2.3.2](#232) |2018. május 8. |Augusztus 31., 2022 |
| [2.3.1](#231) |December 21., 2017 |Augusztus 31., 2022 |
| [2.3.0](#230) |November 10., 2017 |Augusztus 31., 2022 |
| [2.2.1](#221) |Szeptember 29., 2017 |Augusztus 31., 2022 |
| [2.2.0](#220) |2017. május 10. |Augusztus 31., 2022 |
| [2.1.0](#210) |Május 01., 2017 |Augusztus 31., 2022 |
| [2.0.1](#201) |Október 30-ig 2016 |Augusztus 31., 2022 |
| [2.0.0](#200) |Szeptember 29., 2016 |Augusztus 31., 2022 |
| [1.9.0](#190) |2016. július 7. |Augusztus 31., 2022 |
| [1.8.0](#180) |Június 14., 2016 |Augusztus 31., 2022 |
| [1.7.0](#170) |2016. április 26. |Augusztus 31., 2022 |
| [1.6.1](#161) |2016. április 08. |Augusztus 31., 2022 |
| [1.6.0](#160) |Március 29., 2016 |Augusztus 31., 2022 |
| [1.5.0](#150) |2016. január 03. |Augusztus 31., 2022 |
| [1.4.2](#142) |Október 06.2015 |Augusztus 31., 2022 |
| 1.4.1 |Október 06.2015 |Augusztus 31., 2022 |
| [1.2.0](#120) |Augusztus 06, 2015 |Augusztus 31., 2022 |
| [1.1.0](#110) |Július 9., 2015 |Augusztus 31., 2022 |
| [1.0.1](#101) |2015. május 25. |Augusztus 31., 2022 |
| 1.0.0 |2015. április 7. |Augusztus 31., 2022 |
| 0.9.4 – előbérlet |Január 14., 2015 |Február 29., 2016 |
| 0.9.3 – előbérlet |December 9., 2014 |Február 29., 2016 |
| 0.9.2 – előbérlet |November 25., 2014 |Február 29., 2016 |
| 0.9.1 – előbérlet |Szeptember 23., 2014 |Február 29., 2016 |
| 0.9.0 – előbérlet |2014. augusztus 21. |Február 29., 2016 |

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Következő lépések

A Cosmos DB-ről a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás oldalán tekinthet meg további információt.
