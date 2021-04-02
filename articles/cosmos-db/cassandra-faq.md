---
title: Gyakori kérdések a Cassandra APIról Azure Cosmos DB
description: Választ kaphat a Azure Cosmos DB Cassandra APIával kapcsolatos gyakori kérdésekre.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: 1368a3174af08f557b6d08f298fba015601d568c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96030832"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Gyakran ismételt kérdések a Cassandra APIról Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ez a cikk az Apache Cassandra és a Azure Cosmos DB Cassandra API közötti funkcionalitásbeli különbségeket ismerteti. Emellett választ ad a Azure Cosmos DB Cassandra APIával kapcsolatos gyakori kérdésekre is.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Az Apache Cassandra és a Cassandra API közötti fő különbségek

- Az Apache Cassandra egy 100 MB-os korlátot javasol a partíciós kulcs méretétől függően. A Azure Cosmos DB Cassandra API legfeljebb 20 GB-nyi partíciót tesz lehetővé.
- Az Apache Cassandra lehetővé teszi a tartós véglegesítés letiltását. Kihagyhatja az írást a véglegesítő naplóba, és közvetlenül a memtables ugorhat. Ez adatvesztéshez vezethet, ha a csomópont leáll, mielőtt memtables a lemezre SSTables. Azure Cosmos DB mindig tartós véglegesíti az adatvesztés megelőzése érdekében.
- Az Apache Cassandra megtekintheti a csökkent teljesítményt, ha a munkaterhelés sok kihelyezést és törlést is magában foglal. Ennek az az oka, hogy az olvasási munkaterhelésnek át kell ugrania a legfrissebb adatok beolvasásához. A Cassandra API nem fog megjelenni a csökkent olvasási teljesítmény, ha a munkaterhelésnek sok kihelyezése vagy törlése van.
- A nagy terhelésű számítási feladatokra vonatkozó forgatókönyvek során a tömörítésnek futnia kell a SSTables a lemezen való egyesítéséhez. (Egyesítésre van szükség, mert az Apache Cassandra írása csak hozzáfűzést igényel. A rendszer több frissítést tárol különálló SSTable-bejegyzésekként, amelyeket időnként egyesíteni kell). Ez a helyzet csökkentheti a tömörítés során felmerülő olvasási teljesítményt is. Ez a teljesítményre gyakorolt hatás nem történik meg a Cassandra APIban, mert az API nem implementálja a tömörítést.
- Az Apache Cassandra-vel az 1 replikációs tényező beállítása lehetséges. Azonban alacsony rendelkezésre állást eredményez, ha az egyetlen csomópont, amelyen az adat leáll. Ez nem jelent problémát a Azure Cosmos DB Cassandra API miatt, mert mindig van egy 4-es replikációs tényező (kvórum: 3).
- A csomópontok Apache Cassandra-ben való hozzáadásával vagy eltávolításával manuális beavatkozásra van szükség, valamint az új csomópont magas CPU-használatával, míg a meglévő csomópontok áthelyezik a jogkivonat-tartományokat az új csomópontra. Ez a helyzet ugyanaz, amikor egy meglévő csomópontot szerel le. A Cassandra API azonban a szolgáltatásban vagy alkalmazásban megfigyelt problémák nélkül méretezhető.
- A fürt minden csomópontján nem szükséges **num_tokens** beállítani az Apache Cassandra-ban. Azure Cosmos DB teljes mértékben felügyeli a csomópontokat és a jogkivonat-tartományokat.
- A Cassandra API teljes mértékben felügyelve van. Nincs szüksége az Apache Cassandra-ban használt **nodetool** -parancsokra, például a javításra és a leszerelésre.

## <a name="other-frequently-asked-questions"></a>Egyéb gyakori kérdések

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Milyen protokoll-verziót támogat a Cassandra API?

A Azure Cosmos DB Cassandra API támogatja a CQL 3. x verzióját. A CQL kompatibilitása a nyilvános [Apache Cassandra GitHub-adattáron](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)alapul. Ha visszajelzést szeretne küldeni más protokollok támogatásáról, tudassa velünk a [felhasználói visszajelzések](https://feedback.azure.com/forums/263030-azure-cosmos-db) segítségével, vagy küldjön e-mailt a címre [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Miért van szükség az átviteli sebességre a táblázathoz?

Azure Cosmos DB beállítja a tároló alapértelmezett átviteli sebességét azon alapul, hogy hol hozza létre a táblázatot a következő helyről: Azure Portal vagy CQL.

A Azure Cosmos DB a teljesítményre és a késésre vonatkozó garanciákat biztosít a műveletek felső határain belül. Ezek a garanciák akkor lehetnek lehetségesek, ha a motor érvényesítheti a bérlő műveleteinek irányítását. Az átviteli sebesség beállításával biztosíthatja a garantált átviteli sebességet és a késést, mivel a platform fenntartja ezt a kapacitást, és garantálja a művelet sikerességét.
[Rugalmasan módosíthatja az átviteli sebességet](manage-scale-cassandra.md) , és kihasználhatja az alkalmazás szezonális előnyeit, és megtakaríthatja a költségeket.

Az átviteli sebesség fogalmát Azure Cosmos DB cikkben [szereplő kérelmek egységei](request-units.md) ismertetik. A tábla átviteli sebessége egyenlően oszlik meg a mögöttes fizikai partíciók között.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Mi a CQL-on keresztül létrehozott tábla átviteli sebessége?

A Azure Cosmos DB a másodpercenkénti kérelmek egységét (RU/s) használja az átviteli sebesség biztosításához. A CQL-en keresztül létrehozott táblák alapértelmezett értéke 400 RU. Az RU-t a Azure Portal lehet módosítani.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Mi történik, ha a rendszer felhasználja az átviteli sebességet?

A Azure Cosmos DB a teljesítményre és a késésre vonatkozó garanciákat biztosít a műveletek felső határain belül. Ezek a garanciák akkor lehetnek lehetségesek, ha a motor érvényesítheti a bérlő műveleteinek irányítását. Az átviteli sebesség beállításával biztosíthatja a garantált átviteli sebességet és a késést, mivel a platform fenntartja ezt a kapacitást, és garantálja a művelet sikerességét.

Ha túllépi ezt a kapacitást, a következő hibaüzenet jelenik meg, amely jelzi, hogy a kapacitást használta:

**0x1001 túlterhelt: a kérés nem dolgozható fel, mert a "kérések aránya nagy"** 

Fontos megtekinteni, hogy milyen műveletek (és a kötetek) okozzák ezt a problémát. Megtudhatja, milyen felhasználható kapacitásra lesz szüksége a kiépített kapacitáshoz a Azure Portal metrikákkal. Ezután gondoskodnia kell arról, hogy a kapacitás majdnem egyenlően legyen felhasználva az összes mögöttes partíción. Ha úgy látja, hogy az egyik partíció az átviteli sebesség nagy részét felhasználja, a számítási feladatok eldöntése megtörténik.

A metrikák elérhetők, amelyek bemutatják, hogyan használják az átviteli sebességet óra, nap és hét nap alatt, a partíciók között vagy összesítve. További információ: [monitorozás és hibakeresés a metrikákkal a Azure Cosmos db](use-metrics.md).

A diagnosztikai naplókat a [Azure Cosmos db diagnosztikai naplózási](./monitor-cosmos-db.md) cikkben ismertetjük.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Az elsődleges kulcs a Azure Cosmos DB partíciós kulcsának fogalmát képezi?

Igen, a partíciós kulcs az entitás megfelelő helyen történő elhelyezésére szolgál. Azure Cosmos DB a fizikai partíción tárolt megfelelő logikai partíció megtalálására szolgál. A particionálási koncepció jól magyarázható a [partícióban és a skálázás Azure Cosmos db](partitioning-overview.md) cikkben. A lényeges elvihetőség az, hogy a logikai partíciók nem haladnak át a 20 GB-os korláton.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>Mi történik, ha értesítést kapok, hogy egy partíció megtelt?

Azure Cosmos DB a szolgáltatói szerződésen (SLA) alapuló rendszer. Korlátlan méretű, a késés, az átviteli sebesség, a rendelkezésre állás és a konzisztencia garanciáit biztosítja. Ez a korlátlan tárterület a horizontálisan kibővített adatmennyiségen alapul, és a particionálást a legfontosabb koncepcióként használja. A particionálási koncepció jól magyarázható a [partícióban és a skálázás Azure Cosmos db](partitioning-overview.md) cikkben.

Be kell tartania a 20 GB-os korlátot az entitások számának és a logikai partíciók száma alapján. Annak érdekében, hogy az alkalmazás jól méretezhető legyen, azt javasoljuk, hogy *ne* hozzon létre egy gyors partíciót úgy, hogy az összes információt egy partícióban tárolja, és lekérdezi azt. Ez a hiba csak akkor jön el, ha az adatai elferdítve vannak: ez egy partíciós kulcs (több mint 20 GB). Az adateloszlás a Storage Portal használatával található. A hiba kijavításának módja a tábla újbóli létrehozása és a részletes elsődleges (partíciós kulcs) kiválasztása, amely lehetővé teszi az adatelosztást.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Használhatom a Cassandra API a Key Value Store-ban millió vagy több milliárd partíciós kulccsal?

A Azure Cosmos DB a tárterület horizontális felskálázásával korlátlan mennyiségű adattárolást képes tárolni. Ez a tárterület független az átviteli sebességtől. Igen, mindig a Cassandra API csak a kulcsok és értékek tárolására és lekérésére használhatja a megfelelő elsődleges/partíciós kulcs megadásával. Ezek az egyéni kulcsok a saját logikai partícióját kapják meg, és problémák nélkül, egy fizikai partíción ülnek.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Létrehozhatok több táblázatot is a Cassandra API?

Igen, több táblát is létre lehet hozni a Cassandra API. Ezeket a táblákat az átviteli sebesség és a tárolás egységként kezeli a rendszer.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Több táblázatot is Létrehozhatok egymás után?

Azure Cosmos DB erőforrás-szabályozású rendszer az adatkezelési és vezérlési sík tevékenységekhez. A tárolók, például gyűjtemények és táblák olyan futásidejű entitások, amelyek egy adott átviteli kapacitáshoz vannak kiépítve. A tárolók gyors egymásutánban való létrehozása nem várt tevékenység, és lehet, hogy szabályozva van. Ha olyan tesztekkel rendelkezik, amelyek azonnal eldobják vagy létrehozzák a táblákat, próbálja meg kihúzni a helyet.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Legfeljebb hány tábla hozható létre?

A táblák számának nincs fizikai korlátja. Ha nagy számú táblával rendelkezik (ahol a teljes állandó méret több mint 10 TB adat), amelyeket létre kell hoznia, nem a szokásos tíz vagy száz, küldjön e-mailt a címre [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Legfeljebb mekkora lemezterületet hozhatok létre?

Nincs fizikai korlátja a kulcstárolók számának, mert metaadat-tárolók. Ha nagy számú alapterülettel rendelkezik, küldjön e-mailt a címre [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>A normál táblázatból való Kiindulás után nagy mennyiségű adatok is behelyezhetők?

Igen. Az egységesen elosztott partíciókat feltételezve a rendszer automatikusan felügyeli a tárolókapacitást, és a további adattovábbítás során is növekszik. Így magabiztosan importálhatja a szükséges mennyiségű adatmennyiséget a csomópontok kezelése és kiépítése nélkül. Ha azonban sok azonnali adatmennyiségre van szüksége, érdemes lehet közvetlenül [kiépíteni a várt átviteli sebességet](set-throughput.md) ahelyett, hogy az alacsonyabb értéket kellene megkezdenie, és nem szeretné azonnal növelni.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>Használhatom a YAML az API viselkedésének konfigurálására?

A Azure Cosmos DB Cassandra API protokoll szintű kompatibilitást biztosít a műveletek végrehajtásához. Elrejti a felügyelet, a figyelés és a konfiguráció összetettségét. Fejlesztőként vagy felhasználóként nem kell aggódnia a rendelkezésre állás, a sírkövek, a kulcs gyorsítótára, a sor gyorsítótára, a Bloom Filter és számos más beállítás alapján. A Cassandra API a konfiguráció és a felügyelet nélkül szükséges olvasási és írási teljesítmény biztosítására koncentrál.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>A Cassandra API támogatja a csomópontok hozzáadását, a fürt állapotát és a csomópont állapotára vonatkozó parancsokat?

A Cassandra API leegyszerűsíti a kapacitás megtervezését, és válaszol az átviteli sebesség és a tárterület rugalmassági követelményeire. A Azure Cosmos DBával kiépítheti a szükséges átviteli sebességet. Ezt követően tetszőleges számú alkalommal felskálázást végezhet a nap folyamán, és nem kell aggódnia a csomópontok hozzáadásával, törlésével vagy kezelésével kapcsolatban. Nem szükséges eszközöket használni a csomópont-és a fürt kezeléséhez.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>Mi történik a különböző konfigurációs beállításokkal, például az egyszerű vagy a hálózati tárterülettel?

A Azure Cosmos DB globális elosztást biztosít a rendelkezésre álláshoz és a kis késleltetésű okokhoz. Nem kell replikákat vagy más dolgokat beállítania. Az írások mindig tartósan a kvórumot minden olyan régióban, ahol írsz, miközben teljesítménybeli garanciákat biztosítanak.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Mi történik a táblázat metaadatainak különböző beállításaival?

A Azure Cosmos DB teljesítménybeli garanciákat biztosít az olvasási, írási és átviteli sebességhez. Így nem kell aggódnia a konfigurációs beállítások megérintése és a véletlen kezelés érdekében. Ezek a beállítások közé tartoznak a Bloom Filter, a gyorsítótárazás, a javítási lehetőség, a gc_grace és a tömörítési memtable_flush_period.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>A Cassandra Tables esetében a Time-Live támogatott?

Igen, az élettartam támogatott.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Hogyan figyelhető meg az infrastruktúra az átviteli sebesség mellett?

A Azure Cosmos DB egy olyan platform-szolgáltatás, amely segít a hatékonyság növelésében, és nem kell aggódnia az infrastruktúra kezelésével és figyelésével. Például nem szükséges a csomópontok állapotának, a replika állapotának, a GC-nek és az operációs rendszer paramétereinek a figyelése a különböző eszközökkel. Mindössze annyit kell tennie, hogy a portál metrikájában elérhető átviteli sebességet szeretné megtekinteni, hogy a rendszer leszabályozza-e a szabályozást, majd növelje vagy csökkentse az átviteli sebességet. A következőket teheti:

- [SLA](./monitor-cosmos-db.md) -figyelés
- [Metrikák](use-metrics.md) használata
- [Diagnosztikai naplók](./monitor-cosmos-db.md) használata

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Mely ügyféloldali SDK-k működhetnek a Cassandra API?

Az Apache Cassandra SDK az CQLv3-t használó ügyféloldali illesztőprogramjait használták az ügyfélalkalmazások számára. Ha más illesztőprogramokat használ, vagy ha problémák merülnek fel, küldjön e-mailt a következőnek: [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="are-composite-partition-keys-supported"></a>Támogatottak-e az összetett partíciós kulcsok?

Igen, használhat reguláris szintaxist összetett partíciós kulcsok létrehozásához.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Használhatom a sstableloader az betöltéshez?

Nem, a sstableloader nem támogatott.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Egy helyszíni Apache Cassandra-fürtöt is párosítható a Cassandra API?

A Azure Cosmos DB jelenleg a működési terhelés nélküli felhőalapú környezethez optimalizált élményt nyújt. Ha párosításra van szüksége, küldjön e-mailt a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) forgatókönyv leírásával. Olyan ajánlaton dolgozunk, amely segít a helyszíni vagy a Felhőbeli Cassandra-fürtnek a Azure Cosmos DB Cassandra APIhoz való párosításában.

### <a name="does-the-cassandra-api-provide-full-backups"></a>Biztosít a Cassandra API teljes biztonsági mentést?

Azure Cosmos DB két teljes biztonsági mentést biztosít az összes API-ban négy órás időközönként. Így nem kell beállítania a biztonsági mentési ütemtervet. 

Ha módosítani kívánja az adatmegőrzést és a gyakoriságot, küldjön e-mailt [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) egy támogatási esetre, vagy emeljen fel. A biztonsági mentési képességgel kapcsolatos tudnivalókat az [automatikus online biztonsági mentés és visszaállítás Azure Cosmos db](online-backup-and-restore.md) cikkben találja.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hogyan kezeli a Cassandra API fiók a feladatátvételt, ha egy régió leáll?

A Cassandra API a Azure Cosmos DB globálisan elosztott platformján kölcsönöz. Annak biztosítása érdekében, hogy az alkalmazás képes legyen az adatközpont leállásának elvégzésére, legalább egy régiót engedélyezzen a fiókhoz a Azure Portal. További információ: [a magas rendelkezésre állás és a Azure Cosmos db](high-availability.md).

A fiókhoz tetszőleges számú régiót adhat hozzá, és szabályozhatja, hogy a feladatátvételi prioritás biztosításával miként lehet átadni a feladatokat. Az adatbázis használatához meg kell adnia egy alkalmazást is. Ha így tesz, az ügyfelek nem fognak tapasztalni állásidőt.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Az Cassandra API indexeli az entitás összes attribútumát alapértelmezés szerint?

Nem. A Cassandra API támogatja a [másodlagos indexeket](cassandra-secondary-index.md), amelyek hasonló módon viselkednek az Apache Cassandra-ben. Az API alapértelmezés szerint nem indexel minden attribútumot.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Helyileg is használhatom az új Cassandra API SDK-t az emulátorral?

Igen, ez támogatott. Ennek engedélyezéséről a [helyi fejlesztési és tesztelési célú Azure Cosmos db Emulator használata](local-emulator.md#cassandra-api) című cikkben olvashat bővebben.


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Hogyan telepíthetek át az Apache Cassandra-fürtökből származó adatok Azure Cosmos DB?

Az áttelepítési lehetőségekről az adatáttelepítés [Cassandra API fiókba Azure Cosmos db](cassandra-import-data.md) oktatóanyagban olvashat.


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Hol adhatok visszajelzést a Cassandra API szolgáltatásairól?

Visszajelzés küldése a [felhasználói](https://feedback.azure.com/forums/263030-azure-cosmos-db)visszajelzések használatával.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a [Azure Cosmos DB Cassandra API-fiók rugalmas skálázásával](manage-scale-cassandra.md).
