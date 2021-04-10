---
title: Monitoring Azure Cosmos DB adathivatkozás | Microsoft Docs
description: A naplók és a metrikák Azure Cosmos DBban való figyeléséhez szükséges fontos anyagminta.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/07/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 5f542b35110a6d967640ad91faead75f6cc0e0c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593285"
---
# <a name="monitoring-azure-cosmos-db-data-reference"></a>Azure Cosmos DB adathivatkozás figyelése

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ez a cikk referenciaként szolgál az Azure Cosmos DB teljesítményének és rendelkezésre állásának elemzéséhez gyűjtött naplózási és metrikaadatokra vonatkozóan. A Azure Cosmos DB figyelési adatainak gyűjtésével és elemzésével kapcsolatos részletekért tekintse meg a [figyelő Azure Cosmos DBT](monitor-cosmos-db.md) ismertető cikket.

## <a name="metrics"></a>Mérőszámok

A Azure Cosmos DBnak megfelelő metrikák a névtérben **Cosmos dB standard metrikákban** tárolódnak. Az összes Azure Monitor támogatási metrikájának listáját (beleértve a Azure Cosmos DBt is) lásd: [Azure monitor támogatott metrikák](../azure-monitor/essentials/metrics-supported.md). Ez a szakasz felsorolja a Azure Cosmos DB összegyűjtött, automatikusan összegyűjtött platform-metrikákat.  

### <a name="request-metrics"></a>Kérelmek metrikái

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa) |Leírás|Dimenziók| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---| ---|
| TotalRequests (összes kérelem) | Darabszám (darabszám) | Benyújtott kérelmek száma| DatabaseName, CollectionName, régió, StatusCode| Mind | TotalRequests, http-2xx, http-3xx, http 400, HTTP 401, belső kiszolgálóhiba, szolgáltatás nem érhető el, szabályozott kérelmek, átlagos kérelmek másodpercenként | Az állapotkód, a tároló percenkénti részletességgel történő figyelésére használatos. Az átlagos kérelmek másodpercenkénti lekéréséhez használja a Count összesítést percek alatt, és ossza meg a 60-as számmal. |
| MetadataRequests (metaadat-kérelmek) |Darabszám (darabszám) | A metaadat-kérelmek száma. Azure Cosmos DB minden fiókhoz rendszermetaadat-tárolót tart fenn, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen. | DatabaseName, CollectionName, régió, StatusCode| Mind| |Metaadat-kérelmek miatti szabályozások figyelésére használatos.|
| MongoRequests (Mongo kérelmek) | Darabszám (darabszám) | A Mongo kérelmek száma | DatabaseName, CollectionName, régió, CommandName, ErrorCode| Mind |Mongo-lekérdezési kérelmek gyakorisága, a Mongo frissítési kérelmi aránya, a Mongo törlési kérelmi aránya, a Mongo beszúrási kérelmi aránya, a Mongo száma kérelmek aránya| Mongo-kérési hibák figyelésére használatos. |

### <a name="request-unit-metrics"></a>Adategység metrikáinak kérése

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo-kérelem díja) | Darabszám (összesen) |Felhasznált Mongo-kérelmek egységei| DatabaseName, CollectionName, régió, CommandName, ErrorCode| Mind |Mongo-lekérdezési kérelem díja, Mongo frissítési kérelmének díja, Mongo törlési kérelmének díja, Mongo-kérelem díja, Mongo száma kérelmek díja| Egy percen belül figyeli a Mongo-erőforrást.|
| TotalRequestUnits (összes kérelem egysége)| Darabszám (összesen) | Felhasználható kérési egységek| DatabaseName, CollectionName, régió, StatusCode |Mind| TotalRequestUnits| A teljes RU-használat figyelésére használható percenkénti részletességgel. Ha másodpercenként átlagosan felhasznált RU-t szeretné lekérni, használja a teljes összesítést percenként, és ossza meg a 60-as számmal.|
| ProvisionedThroughput (kiépített átviteli sebesség)| Darabszám (maximum) |Kiépített átviteli sebesség a tároló részletessége szerint| DatabaseName, ContainerName| 5 M| | Tárolón kiépített átviteli sebesség figyelésére szolgál.|

### <a name="storage-metrics"></a>Tárolási metrikák

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (rendelkezésre álló tár) |Bájtok (összesen) | Régiónként 5 perces részletességgel jelentett teljes rendelkezésre álló tárterület| DatabaseName, CollectionName, régió| 5 M| Rendelkezésre álló tárhely| A rendelkezésre álló tárolókapacitás (csak a rögzített tárolási gyűjtemények esetében érvényes) figyeléséhez használatos minimális részletességnek 5 percnek kell lennie.| 
| DataUsage (adathasználat) |Bájtok (összesen) |Régiónként 5 perces részletességgel jelentett teljes adatfelhasználás| DatabaseName, CollectionName, régió| 5 M |Adatméret | A tároló és régió teljes adatfelhasználásának figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie.|
| IndexUsage (használat indexelése) | Bájtok (összesen) |Az összes indexelési használat összesen 5 perces részletességgel számolt be régiónként| DatabaseName, CollectionName, régió| 5 M| Index mérete| A tároló és régió teljes adatfelhasználásának figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie. |
| DocumentQuota (dokumentum kvótája) | Bájtok (összesen) | Régiónként 5 perces részletességgel jelentett teljes tárolási kvóta.| DatabaseName, CollectionName, régió| 5 M |Tárkapacitás| A tároló és régió teljes kvótájának figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie.|
| DocumentCount (dokumentumok száma) | Darabszám (összesen) |Régiónként 5 perces részletességgel jelentett dokumentumok száma összesen| DatabaseName, CollectionName, régió| 5 M |Dokumentumok száma|A dokumentumok számának a tárolóban és régióban való figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie.|

### <a name="latency-metrics"></a>Késési metrikák

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Használat |
|---|---|---|---| ---| ---|
| ReplicationLatency (replikációs késés)| Ezredmásodperc (minimum, maximum, átlag) | P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz| SourceRegion, TargetRegion| Mind | Egy földrajzilag replikált fiók két régiója közötti P99-replikációs késés figyelésére használatos. |
| Kiszolgálóoldali késés| Ezredmásodperc (átlagos) | A kiszolgáló által a kérelem feldolgozásához szükséges idő. | CollectionName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, régió | Mind | A kérés késésének figyelésére szolgál a Azure Cosmos DB kiszolgálón. |

### <a name="availability-metrics"></a>Rendelkezésre állási metrikák

|Metrika (metrika megjelenítendő neve) |Egység (aggregáció típusa)|Leírás| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---|
| ServiceAvailability (szolgáltatás rendelkezésre állása)| Százalék (minimum, maximum) | A fiók egy órás részletességgel kéri a rendelkezésre állást| 1H | Szolgáltatás rendelkezésre állása | Az átadott kérelmek százalékos arányát jelöli. A rendszer hibát okozó kérést küld, ha az állapotkód 410, 500 vagy 503, amely a fiók rendelkezésre állásának figyelésére használatos az óránkénti részletességgel. |

### <a name="cassandra-api-metrics"></a>Cassandra API metrikák

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Használat |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-kérelmek) | Darabszám (darabszám) | Cassandra API kérelmek száma| DatabaseName, CollectionName, ErrorCode, régió, OperationType, ResourceType| Mind| A Cassandra-kérések figyelésére szolgál percenkénti részletességgel. Az átlagos kérelmek másodpercenkénti lekéréséhez használja a Count összesítést percek alatt, és ossza meg a 60-as számmal.|
| CassandraRequestCharges (Cassandra-kérelmek díjai) | Darabszám (Sum, min, Max, AVG) | A Cassandra API által felhasznált kérelmek egységei | DatabaseName, CollectionName, régió, OperationType, ResourceType| Mind| Egy Cassandra API fiók által percenként használt RUs figyelésére használatos.|
| CassandraConnectionClosures (Cassandra-kapcsolatok bezárása) |Darabszám (darabszám) |Lezárt Cassandra-kapcsolatok száma| ClosureReason, régió| Mind | Az ügyfelek és a Azure Cosmos DB Cassandra API közötti kapcsolat figyelésére használatos.|

További információkért tekintse [meg az Azure monitor által támogatott platform-mérőszámok](../azure-monitor/essentials/metrics-supported.md)listáját.

## <a name="resource-logs"></a>Erőforrásnaplók

A következő táblázat a Azure Cosmos DB erőforrás-naplófájljainak tulajdonságait sorolja fel. Az erőforrás-naplókat Azure Monitor naplókba vagy az Azure Storage-ba gyűjti. Azure Monitor a rendszer a naplókat az erőforrás-szolgáltató * * neve alatt gyűjti a **AzureDiagnostics** táblában `MICROSOFT.DOCUMENTDB` .

| Azure Storage-mező vagy-tulajdonság | Azure Monitor naplók tulajdonsága | Leírás |
| --- | --- | --- |
| **idő** | **TimeGenerated** | A művelet bekövetkeztekor megadott dátum és idő (UTC). |
| **resourceId** | **Erőforrás** | Az Azure Cosmos DB fiók, amely számára engedélyezve vannak a naplók.|
| **Kategória** | **Kategória** | A Azure Cosmos DB, a **DataPlaneRequests**, a **MongoRequests**, a **QueryRuntimeStatistics**, a **PartitionKeyStatistics**, a **PartitionKeyRUConsumption**, a **ControlPlaneRequests** **, a CassandraRequests, a** **GremlinRequests** a rendelkezésre álló naplózási típusok. |
| **operationName** | **OperationName** | A művelet neve. A művelet neve lehet:,,,,,,,,,  `Create` `Update` ,, `Read` `ReadFeed` `Delete` `Replace` `Execute` `SqlQuery` `Query` `JSQuery` `Head` `HeadFeed` vagy `Upsert` .   |
| **Tulajdonságok** | n.a. | A mező tartalmát az alábbi sorok írják le. |
| **Tevékenységazonosító** | **activityId_g** | A naplózott művelet egyedi GUID azonosítója. |
| **userAgent** | **userAgent_s** | Az ügyfél felhasználói ügynökét megadó karakterlánc, amelyből a kérést elküldték. A felhasználói ügynök formátuma: `{user agent name}/{version}` .|
| **requestResourceType** | **requestResourceType_s** | Az elérni kívánt erőforrás típusa. Ez az érték lehet adatbázis, tároló, dokumentum, melléklet, felhasználó, engedély, tárolt eljárás, trigger, felhasználó által definiált függvény vagy ajánlat. |
| **statusCode** | **statusCode_s** | A művelet válaszának állapota. |
| **requestResourceId** | **ResourceId** | A kérelemre vonatkozó resourceId. A végrehajtott művelettől függően ez az érték a következőre mutat:, `databaseRid` `collectionRid` vagy `documentRid` .|
| **clientIpAddress** | **clientIpAddress_s** | Az ügyfél IP-címe. |
| **requestCharge** | **requestCharge_s** | A művelet által használt RU/s száma |
| **collectionRid** | **collectionId_s** | A gyűjtemény egyedi azonosítója.|
| **időtartama** | **duration_d** | A művelet időtartama ezredmásodpercben. |
| **requestLength** | **requestLength_s** | A kérelem hossza (bájt). |
| **responseLength** | **responseLength_s** | A válasz hossza (bájt).|
| **resourceTokenPermissionId** | **resourceTokenPermissionId_s** | Ez a tulajdonság jelzi a megadott erőforrás-jogkivonat engedélyének azonosítóját. Az engedélyekkel kapcsolatos további tudnivalókért tekintse meg az [adatai biztonságos elérését](./secure-access-to-data.md#permissions) ismertető cikket. |
| **resourceTokenPermissionMode** | **resourceTokenPermissionMode_s** | Ez a tulajdonság jelzi az erőforrás-jogkivonat létrehozásakor beállított engedélyezési módot. Az engedélyezési mód rendelkezhet olyan értékekkel, mint az "all" vagy az "READ". Az engedélyekkel kapcsolatos további tudnivalókért tekintse meg az [adatai biztonságos elérését](./secure-access-to-data.md#permissions) ismertető cikket. |
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ez az érték nem üres, ha [erőforrás-tokeneket](./secure-access-to-data.md#resource-tokens) használ a hitelesítéshez. Az érték a felhasználó erőforrás-AZONOSÍTÓJÁRA mutat. |
| **responseLength** | **responseLength_s** | A válasz hossza (bájt).|

Az összes Azure Monitor naplózási kategória és a társított sémák hivatkozásainak listáját lásd: [Azure monitor naplók kategóriái és sémái](../azure-monitor/essentials/resource-logs-schema.md). 

## <a name="azure-monitor-logs-tables"></a>Azure Monitor naplók táblái

Azure Cosmos DB Kusto-táblákat használ Azure Monitor naplókból. Ezeket a táblákat a log Analytics használatával kérdezheti le. A Kusto Bales-alkalmazások listáját a [Azure monitor naplók táblázatának dokumentációjában](/azure/azure-monitor/reference/tables/tables-resourcetype#azure-cosmos-db) találja.

## <a name="see-also"></a>Lásd még:

- A figyelési Azure Cosmos DB leírását a [figyelés Azure Cosmos db](monitor-cosmos-db.md) című részben tekintheti meg.
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/essentials/monitor-azure-resource.md) .