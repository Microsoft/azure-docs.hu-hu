---
title: Azure Cosmos DB-erőforrásmodell
description: Ez a cikk Azure Cosmos DB erőforrás-modellt ismerteti, amely magában foglalja az Azure Cosmos-fiókot,-adatbázist,-tárolót és az elemeket. Ezen elemek hierarchiáját is lefedi egy Azure Cosmos-fiókban.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 4ed881b74f240946d98d9868344c898d3e9a9dad
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627331"
---
# <a name="azure-cosmos-db-resource-model"></a>Azure Cosmos DB-erőforrásmodell
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB egy teljes körűen felügyelt platform-szolgáltatás (Péter). A Azure Cosmos DB használatának megkezdéséhez először létre kell hoznia egy Azure Cosmos-fiókot az Azure-előfizetésben és-adatbázisokban, tárolókban és elemekben. Ez a cikk az erőforrás-modell hierarchiájának Azure Cosmos DB erőforrás-modelljét és különböző entitásait ismerteti.

Az Azure Cosmos-fiók a globális terjesztés és a magas rendelkezésre állás alapvető egysége. Az Azure Cosmos-fiókja egyedi DNS-nevet tartalmaz, és a Azure Portal vagy az Azure CLI használatával, vagy más nyelvfüggő SDK-k használatával kezelheti a fiókokat. További információ: [Az Azure Cosmos-fiók kezelése](how-to-manage-database-account.md). Az adatok és az átviteli sebesség több Azure-régióban való globális elosztása érdekében bármikor hozzáadhat és eltávolíthat Azure-régiókat a fiókjához. Beállíthatja, hogy a fiókja egyetlen vagy több írási régióval rendelkezzen. További információ: Azure- [régiók hozzáadása és eltávolítása a fiókhoz](how-to-manage-database-account.md). Beállíthatja egy fiók [alapértelmezett konzisztencia](consistency-levels.md) -szintjét.

## <a name="elements-in-an-azure-cosmos-account"></a>Elemek egy Azure Cosmos-fiókban

Az Azure Cosmos-tároló a méretezhetőség alapvető egysége. Gyakorlatilag korlátlan számú kiosztott átviteli sebesség (RU/s) és tárterület található a tárolón. Azure Cosmos DB transzparens módon particionálja a tárolót az Ön által megadott logikai partíciós kulcs használatával, hogy rugalmasan méretezheti a kiosztott átviteli sebességet és tárterületet.

Jelenleg legfeljebb 50 Azure Cosmos-fiókot hozhat létre az Azure-előfizetések alatt (ez egy, a támogatási kérésen keresztül növelhető enyhe korlát). Egyetlen Azure Cosmos-fiók gyakorlatilag korlátlan mennyiségű adattal és kiosztott átviteli sebességgel rendelkezhet. Az adatok és a kiépített átviteli sebesség kezeléséhez létrehozhat egy vagy több Azure Cosmos-adatbázist a fiókjában és az adatbázison belül, létrehozhat egy vagy több tárolót is. Az alábbi képen egy Azure Cosmos-fiók elemeinek hierarchiája látható:

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Azure Cosmos-fiók hierarchiája" border="false":::

Miután létrehozta az Azure-előfizetéshez tartozó fiókot, a fiókjában lévő adatokat adatbázisok, tárolók és elemek létrehozásával kezelheti. 

Az alábbi képen egy Azure Cosmos DB fiók különböző entitásait ábrázoló hierarchia látható:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos-fiókok entitásai" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos-adatbázisok

A fiókjában létrehozhat egy vagy több Azure Cosmos-adatbázist is. Az adatbázisok egy névtérhez hasonlítanak. Az adatbázis az Azure Cosmos-tárolók egy készletének felügyeleti egysége. A következő táblázat bemutatja, hogyan van leképezve egy adatbázis a különböző API-specifikus entitásokra:

| Azure Cosmos-entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-adatbázis | Adatbázis | Kulcstartomány | Adatbázis | Adatbázis | NA |

> [!NOTE]
> Az első táblázat létrehozásakor a Table API fiókok esetében automatikusan létrejön egy alapértelmezett adatbázis az Azure Cosmos-fiókban.

### <a name="operations-on-an-azure-cosmos-database"></a>Műveletek egy Azure Cosmos-adatbázisban

Az Azure Cosmos API-kkal az alábbi táblázatban leírtak szerint dolgozhat:

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- |
|Az összes adatbázis enumerálása| Igen | Yes | Igen (az adatbázis egy üres területre van leképezve) | Yes | NA | NA |
|Adatbázis beolvasása| Igen | Yes | Igen (az adatbázis egy üres területre van leképezve) | Yes | NA | NA |
|Új adatbázis létrehozása| Igen | Yes | Igen (az adatbázis egy üres területre van leképezve) | Yes | NA | NA |
|Adatbázis frissítése| Igen | Yes | Igen (az adatbázis egy üres területre van leképezve) | Yes | NA | NA |

## <a name="azure-cosmos-containers"></a>Azure Cosmos-tárolók

Az Azure Cosmos-tároló a skálázhatósági egység, amely a kiépített átviteli sebességre és a tárterületre is alkalmas. A tárolók horizontálisan particionálva vannak, majd több régióban replikálódnak. A tárolóhoz hozzáadott elemek automatikusan logikai partícióba vannak csoportosítva, amelyek a partíciós kulcs alapján vannak elosztva a fizikai partíciók között. A tároló átviteli sebessége egyenletesen oszlik el a fizikai partíciók között. A particionálással és a partíciós kulcsokkal kapcsolatos további tudnivalókért tekintse meg az [Adatparticionálást](partitioning-overview.md)ismertető témakört. 

Tároló létrehozásakor az átviteli sebességet az alábbi módokon konfigurálhatja:

* **Dedikált kiépített átviteli sebesség**: a tárolón kiépített átviteli sebesség kizárólag az adott tároló számára van fenntartva, és a SLA-kat támogatja. További információért lásd: [átviteli sebesség tárolón való kiépítése](how-to-provision-container-throughput.md).

* **Megosztott kiépített átviteli sebesség**: ezek a tárolók megosztják a kiosztott átviteli sebességet ugyanabban az adatbázisban lévő többi tárolóval (kivéve a dedikált kiosztott átviteli sebességgel konfigurált tárolókat). Ez azt jelenti, hogy az adatbázison kiosztott átviteli sebesség a "megosztott átviteli sebesség" tárolók között van megosztva. További információért lásd: [az átviteli sebesség kiépítése egy adatbázisban](how-to-provision-database-throughput.md).

> [!NOTE]
> A megosztott és a dedikált átviteli sebességet csak az adatbázis és a tároló létrehozásakor lehet konfigurálni. A dedikált teljesítményű módról a megosztott teljesítményű módra (és fordítva) való váltáshoz a tároló létrehozása után egy új tárolót kell létrehoznia, és migrálnia kell az adatokat az új tárolóba. Az adatáttelepítés a Azure Cosmos DB módosítási funkció használatával végezhető el.

Az Azure Cosmos-tároló rugalmasan méretezhető, függetlenül attól, hogy dedikált vagy megosztott kiosztású átviteli módok használatával hoz létre tárolókat.

A tárolók az elemek séma nélküli tárolói. A tároló elemei tetszőleges sémákkal rendelkezhetnek. Például egy olyan elem, amely egy személyt jelöl, és egy személygépkocsit jelképező elem is elhelyezhető *ugyanabban a tárolóban*. Alapértelmezés szerint a rendszer a tárolóhoz hozzáadott összes elemet automatikusan indexeli a explicit index vagy a séma kezelése nélkül. Az indexelési viselkedést úgy szabhatja testre, hogy az [indexelési házirendet](index-overview.md) egy tárolón konfigurálja. 

Beállíthatja, [hogy az élettartam (TTL)](time-to-live.md) a tároló kijelölt elemein legyen, vagy a teljes tároló számára, hogy szabályosan kiürítse ezeket az elemeket a rendszerből. Azure Cosmos DB automatikusan törli az elemeket, amikor lejárnak. Emellett garantálja, hogy a tárolón végrehajtott lekérdezés nem ad vissza egy rögzített kötésen belüli lejárt elemeket. További információ: a [TTL konfigurálása a tárolón](how-to-time-to-live.md).

A [módosítási hírcsatorna](change-feed.md) használatával előfizethet a tároló minden logikai partíciója által felügyelt műveleti naplóra. A módosítási hírcsatorna a tárolón végrehajtott összes frissítés naplóját tartalmazza, valamint az elemek előtti és utáni képeket is. További információ: [reaktív alkalmazások kiépítése a Change feed használatával](serverless-computing-database.md). A módosítási hírcsatorna megőrzési időtartamát a tárolóban lévő hírcsatorna módosítása házirend használatával is konfigurálhatja.

A tárolóhoz [tárolt eljárásokat, eseményindítókat, felhasználó által definiált függvényeket (UDF)](stored-procedures-triggers-udfs.md)és [egyesítési eljárásokat](how-to-manage-conflicts.md) is regisztrálhat.

Megadhat [egyedi kulcs megkötést](unique-keys.md) az Azure Cosmos-tárolóban. Egyedi kulcsokra vonatkozó szabályzat létrehozásával gondoskodhat a logikai partíciós kulcs egy vagy több értékének egyediségéről. Ha egyedi kulcs-házirend használatával hoz létre tárolót, akkor nem hozható létre olyan új vagy frissített elem, amely az egyedi kulcs megkötése által megadott értékeket duplikáló értékekkel rendelkezik. További információt az [egyedi kulcsokra vonatkozó megkötések](unique-keys.md)című témakörben talál.

A tárolók API-specifikus entitásokra vannak kialakítva, az alábbi táblázatban látható módon:

| Azure Cosmos-entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-tároló | Tároló | Tábla | Gyűjtemény | Graph | Tábla |

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

### <a name="properties-of-an-azure-cosmos-container"></a>Egy Azure Cosmos-tároló tulajdonságai

Az Azure Cosmos-tárolók rendszer által definiált tulajdonságokkal rendelkeznek. Attól függően, hogy melyik API-t használja, előfordulhat, hogy bizonyos tulajdonságok nem lesznek közvetlenül kitéve. A következő táblázat ismerteti a rendszerszintű tulajdonságok listáját:

| Rendszerszintű tulajdonság | Rendszer által generált vagy felhasználó által konfigurálható | Cél | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Rendszer által generált | Tároló egyedi azonosítója | Igen | Nem | Nem | Nem | Nem |
|\_ETAG | Rendszer által generált | Optimista Egyidejűség-vezérléshez használt entitás címkéje | Igen | Nem | Nem | Nem | Nem |
|\_TS | Rendszer által generált | A tároló utolsó frissített időbélyegzője | Igen | Nem | Nem | Nem | Nem |
|\_önálló | Rendszer által generált | A tároló címezhető URI-ja | Igen | Nem | Nem | Nem | Nem |
|id | Felhasználó által konfigurálható | A tároló felhasználó által definiált egyedi neve | Igen | Igen | Igen | Igen | Yes |
|indexingPolicy | Felhasználó által konfigurálható | Lehetővé teszi az index elérési útjának, az index típusának és az index mód módosítását | Igen | Nem | Nem | Nem | Igen |
|TimeToLive | Felhasználó által konfigurálható | Lehetővé teszi az elemek automatikus törlését a tárolóból egy beállított időszak után. Részletekért lásd: [time to Live](time-to-live.md). | Igen | Nem | Nem | Nem | Igen |
|changeFeedPolicy | Felhasználó által konfigurálható | Egy tároló elemein végrehajtott módosítások olvasására szolgál. Részletekért lásd: a [hírcsatorna módosítása](change-feed.md). | Igen | Nem | Nem | Nem | Igen |
|uniqueKeyPolicy | Felhasználó által konfigurálható | Egy logikai partícióban lévő egy vagy több érték egyediségének biztosítására szolgál. További információ: [egyedi kulcsokra vonatkozó megkötések](unique-keys.md). | Igen | Nem | Nem | Nem | Igen |

### <a name="operations-on-an-azure-cosmos-container"></a>Műveletek egy Azure Cosmos-tárolón

Az Azure Cosmos-tárolók az alábbi műveleteket támogatják az Azure Cosmos API-k bármelyikének használata esetén:

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Tárolók enumerálása egy adatbázisban | Igen | Igen | Igen | Yes | NA | NA |
| Tároló beolvasása | Igen | Igen | Igen | Yes | NA | NA |
| Új tároló létrehozása | Igen | Igen | Igen | Yes | NA | NA |
| Tároló frissítése | Igen | Igen | Igen | Yes | NA | NA |
| Tároló törlése | Igen | Igen | Igen | Yes | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos-elemek

Attól függően, hogy melyik API-t használja, egy Azure Cosmos-elem a gyűjteményben szereplő dokumentumot, egy tábla egy sorát, vagy egy gráf egyik csomópontját vagy szegélyét jelöli. Az alábbi táblázat az API-specifikus entitások Azure Cosmos-tételhez való hozzárendelését mutatja be:

| Cosmos-entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-tétel | Elem | Sor | Dokumentum | Csomópont vagy peremhálózati | Elem |

### <a name="properties-of-an-item"></a>Egy tétel tulajdonságai

Minden Azure Cosmos-eleme a következő, rendszerszintű tulajdonságokkal rendelkezik. Attól függően, hogy melyik API-t használja, előfordulhat, hogy néhányat nem lehet közvetlenül feltenni.

| Rendszerszintű tulajdonság | Rendszer által generált vagy felhasználó által konfigurálható| Cél | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Rendszer által generált | Az objektum egyedi azonosítója | Igen | Nem | Nem | Nem | Nem |
|\_ETAG | Rendszer által generált | Optimista Egyidejűség-vezérléshez használt entitás címkéje | Igen | Nem | Nem | Nem | Nem |
|\_TS | Rendszer által generált | Az elemek utolsó frissítésének időbélyege | Igen | Nem | Nem | Nem | Nem |
|\_önálló | Rendszer által generált | Az elemek címezhető URI-ja | Igen | Nem | Nem | Nem | Nem |
|id | Bármelyik | A felhasználó által definiált egyedi név logikai partícióban. | Igen | Igen | Igen | Igen | Yes |
|Tetszőleges felhasználó által definiált tulajdonságok | Felhasználó által megadott | A felhasználó által definiált tulajdonságok az API-natív ábrázolásban (beleértve a JSON-t, a BSON és a CQL) | Igen | Igen | Igen | Igen | Yes |

> [!NOTE]
> A tulajdonság egyedisége `id` csak minden logikai partíción belül érvényesül. Több dokumentum is rendelkezhet ugyanazzal a `id` tulajdonsággal különböző partíciós kulcs értékekkel.

### <a name="operations-on-items"></a>Elemek műveletei

Az Azure Cosmos-elemek a következő műveleteket támogatják. Az Azure Cosmos API-k bármelyikét használhatja a műveletek végrehajtásához.

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Beszúrás, csere, törlés, Upsert, olvasás | Nem | Igen | Igen | Igen | Igen | Igen |

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kezelheti Azure Cosmos-fiókját és egyéb fogalmait:

* [Útmutató az Azure Cosmos-fiók kezeléséhez](how-to-manage-database-account.md)
* [Globális terjesztés](distribute-data-globally.md)
* [Konzisztenciaszintek](consistency-levels.md)
* [VNET szolgáltatási végpont az Azure Cosmos-fiókhoz](how-to-configure-vnet-service-endpoint.md)
* [IP-tűzfal az Azure Cosmos-fiókhoz](how-to-configure-firewall.md)
* [Útmutató Azure-régiók Azure Cosmos-fiókhoz való hozzáadásához és eltávolításához](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA-kat](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
