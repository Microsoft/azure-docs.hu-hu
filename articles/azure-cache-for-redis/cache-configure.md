---
title: Az Azure cache konfigurálása a Redis-hez
description: Ismerje meg az Azure cache alapértelmezett Redis-konfigurációját az Redis-hez, és megtudhatja, hogyan konfigurálhatja az Azure cache-t Redis-példányokhoz
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 62e0c9bbf8b1c7cef9b1cc239810cb554b5ffa45
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433540"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Az Azure cache konfigurálása a Redis-hez
Ez a témakör az Azure cache Redis-példányok számára elérhető konfigurációkat ismerteti. Ez a témakör az Azure cache alapértelmezett Redis-kiszolgáló-konfigurációját is ismerteti Redis-példányok esetén.

> [!NOTE]
> A prémium szintű gyorsítótár szolgáltatásainak konfigurálásával és használatával kapcsolatos további információkért lásd: [az adatmegőrzés konfigurálása](cache-how-to-premium-persistence.md), [a fürtözés konfigurálása](cache-how-to-premium-clustering.md)és [a Virtual Network támogatásának konfigurálása](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-azure-cache-for-redis-settings"></a>Az Azure cache konfigurálása a Redis beállításaihoz
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Az Azure cache for Redis beállításait az **erőforrás menü**használatával tekintheti meg és konfigurálhatja az **Azure cache for Redis** panelben.

![Azure cache a Redis beállításaihoz](./media/cache-configure/redis-cache-settings.png)

A következő beállításokat tekintheti meg és konfigurálhatja az **erőforrás menüben**.

* [Áttekintés](#overview)
* [Tevékenységnapló](#activity-log)
* [Hozzáférés-vezérlés (IAM)](#access-control-iam)
* [Címkék](#tags)
* [Problémák diagnosztizálása és megoldása](#diagnose-and-solve-problems)
* [Beállítások](#settings)
    * [Hozzáférési kulcsok](#access-keys)
    * [Speciális beállítások](#advanced-settings)
    * [Azure cache a Redis Advisorhoz](#azure-cache-for-redis-advisor)
    * [Méretezés](#scale)
    * [Redis-fürt mérete](#cluster-size)
    * [Redis-adatmegőrzés](#redis-data-persistence)
    * [Frissítések ütemezése](#schedule-updates)
    * [Georeplikáció](#geo-replication)
    * [Virtuális hálózat](#virtual-network)
    * [Tűzfal](#firewall)
    * [Tulajdonságok](#properties)
    * [Zárak](#locks)
    * [Automation-parancsfájl](#automation-script)
* Felügyelet
    * [Adatok importálása](#importexport)
    * [Adatok exportálása](#importexport)
    * [Újraindítás](#reboot)
* [Monitorozás](#monitoring)
    * [Redis metrikák](#redis-metrics)
    * [Riasztási szabályok](#alert-rules)
    * [Diagnosztika](#diagnostics)
* Támogatási & hibaelhárítási beállítások
    * [Erőforrás állapota](#resource-health)
    * [Új támogatási kérelem](#new-support-request)


## <a name="overview"></a>Áttekintés

Az **Áttekintés** a gyorsítótárra vonatkozó alapszintű információkat tartalmaz, például a nevet, a portokat, az árképzési szintet és a kiválasztott gyorsítótár-metrikákat.

### <a name="activity-log"></a>Tevékenységnapló

A gyorsítótárban végrehajtott műveletek megtekintéséhez kattintson a **műveletnapló** elemre. A nézet kibontásához használhatja a szűrést is, hogy más erőforrásokat is tartalmazzon. További információ a naplók használatáról: [műveletek naplózása a Resource Managerrel](../azure-resource-manager/resource-group-audit.md). Az Azure cache Redis-események figyelésével kapcsolatos további információkért lásd: [műveletek és riasztások](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Hozzáférés-vezérlés (IAM)

A **hozzáférés-vezérlés (iam)** szakasz támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) a Azure Portal. Ez a konfiguráció segít a szervezeteknek egyszerűen és pontosan teljesíteni a hozzáférés-kezelési követelményeiket. További információ: [szerepköralapú hozzáférés-vezérlés a Azure Portalban](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Címkék

A **címkék** szakasz segít az erőforrások rendszerezésében. További információkért lásd: [Az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/resource-group-using-tags.md) című cikket.


### <a name="diagnose-and-solve-problems"></a>Problémák diagnosztizálása és megoldása

Kattintson a diagnosztizálás elemre, **és oldja** meg a gyakori problémákkal és stratégiákkal kapcsolatos problémákat.



## <a name="settings"></a>Beállítások
A **Settings (beállítások** ) szakasz a gyorsítótár következő beállításainak elérését és konfigurálását teszi lehetővé.

* [Hozzáférési kulcsok](#access-keys)
* [Speciális beállítások](#advanced-settings)
* [Azure cache a Redis Advisorhoz](#azure-cache-for-redis-advisor)
* [Méretezés](#scale)
* [Redis-fürt mérete](#cluster-size)
* [Redis-adatmegőrzés](#redis-data-persistence)
* [Frissítések ütemezése](#schedule-updates)
* [Georeplikáció](#geo-replication)
* [Virtuális hálózat](#virtual-network)
* [Tűzfal](#firewall)
* [Tulajdonságok](#properties)
* [Zárak](#locks)
* [Automation-parancsfájl](#automation-script)



### <a name="access-keys"></a>Elérési kulcs
Kattintson a **hozzáférési kulcsok** elemre a gyorsítótár elérési kulcsainak megtekintéséhez vagy újbóli létrehozásához. Ezeket a kulcsokat a gyorsítótárhoz csatlakozó ügyfelek használják.

![Azure cache a Redis hozzáférési kulcsaihoz](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Speciális beállítások
A **Speciális beállítások** panelen a következő beállítások konfigurálhatók.

* [Hozzáférési portok](#access-ports)
* [Memória-házirendek](#memory-policies)
* [Lemezterület-értesítések (speciális beállítások)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Hozzáférési portok
A nem SSL hozzáférés alapértelmezés szerint le van tiltva az új gyorsítótárakhoz. A nem SSL port engedélyezéséhez kattintson a **nem** lehetőségre a **hozzáférés engedélyezése csak SSL protokollon keresztül** a **Speciális beállítások** panelen, majd kattintson a **Mentés**gombra.

> [!NOTE]
> Az Azure cache Redis-hez való SSL-hozzáférése jelenleg a TLS 1,0, 1,1 és 1,2 protokollt támogatja, de a 1,0-es és az 1,1-os verziók hamarosan megszűnnek.  További részletekért olvassa el a [TLS 1,0 és a 1,1 eltávolítása lapot](cache-remove-tls-10-11.md) .

![Azure cache a Redis hozzáférési portjaihoz](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Memória-házirendek
A **Speciális beállítások** panelen a **Maxmemory**szabályzat, a **Maxmemory**és a **maxfragmentationmemory számára fenntartott** beállítások konfigurálhatók a gyorsítótár számára.

![Azure cache a Redis Maxmemory házirendjéhez](./media/cache-configure/redis-cache-maxmemory-policy.png)

A **Maxmemory házirend** konfigurálja a gyorsítótár kiürítési házirendjét, és lehetővé teszi a következő Kizárási szabályzatok kiválasztását:

* `volatile-lru` – ez az alapértelmezett kizárási házirend.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

`maxmemory` házirendekkel kapcsolatos további információkért lásd: [Kizárási házirendek](https://redis.io/topics/lru-cache#eviction-policies).

A **maxmemory fenntartott** beállítás azt a memóriát állítja be MB-ban, amely nem gyorsítótárbeli műveletekhez van fenntartva, például feladatátvétel közbeni replikáláshoz. Ennek az értéknek a beállítása lehetővé teszi, hogy konzisztens Redis-kiszolgáló élményt biztosítson a terheléstől függően. Ezt az értéket magasabb értékre kell állítani a nagy írási feladatok esetében. Ha a memória le van foglalva az ilyen műveletekhez, nem érhető el a gyorsítótárazott adatok tárolása.

A **maxfragmentationmemory fenntartott** beállítás azt a memóriát adja meg MB-ban, amely a memória töredezettségének kielégítésére van fenntartva. Ennek az értéknek a beállítása lehetővé teszi, hogy konzisztens Redis-kiszolgáló élményt biztosítson, ha a gyorsítótár megtelt, vagy a teljes és a töredezettségi arány magas. Ha a memória le van foglalva az ilyen műveletekhez, nem érhető el a gyorsítótárazott adatok tárolása.

Az új memória foglalási értékének (**maxmemory** vagy **maxfragmentationmemory**) kiválasztásakor figyelembe kell venni, hogy ez a változás milyen hatással lehet egy olyan gyorsítótárra, amely már fut nagy mennyiségű adattal. Ha például egy 53 GB-os gyorsítótárban 49 GB adat található, akkor a foglalás értékét 8 GB-ra kell módosítania, ez a módosítás a rendszer számára a maximális rendelkezésre álló memóriát is leállítja a 45 GB-ra. Ha a jelenlegi `used_memory` vagy az `used_memory_rss` érték meghaladja az új 45 GB-os korlátot, akkor a rendszernek ki kell zárnia az adatait, amíg a `used_memory` és a `used_memory_rss` nem éri el az 45 GB-ot. A kizárás növelheti a kiszolgáló terhelését és a memória töredezettségét. A gyorsítótár-metrikák, például a `used_memory` és az `used_memory_rss`további információit a [rendelkezésre álló metrikák és jelentéskészítési időközök](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)című részben tekintheti meg.

> [!IMPORTANT]
> A **maxmemory fenntartott** és a **maxfragmentationmemory számára fenntartott** beállítások csak a standard és a prémium szintű gyorsítótárak esetében érhetők el.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Lemezterület-értesítések (speciális beállítások)
A Redis a **Speciális beállítások** panelen konfigurálhatók. A lemezterület-értesítések lehetővé teszik, hogy az ügyfelek értesítést kapjanak bizonyos események bekövetkezésekor.

![Azure cache a Redis speciális beállításaihoz](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> A lemezterület-értesítések és az **értesítési terület – az események** beállítás csak a standard és a prémium szintű gyorsítótárak esetében érhető el.
> 
> 

További információ: [Redis](https://redis.io/topics/notifications). A mintakód esetében tekintse meg a [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) fájlját.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure cache a Redis Advisorhoz
Az **Azure cache for Redis Advisor** panel a gyorsítótárra vonatkozó javaslatokat jeleníti meg. A normál működés során nem jelenik meg javaslat. 

![Ajánlatok](./media/cache-configure/redis-cache-no-recommendations.png)

Ha a gyorsítótár műveletei (például a nagy memóriahasználat, a hálózati sávszélesség vagy a kiszolgáló terhelése) során bármilyen feltétel fordul elő, a rendszer riasztást jelenít meg az **Azure cache Redis** paneljén.

![Ajánlatok](./media/cache-configure/redis-cache-recommendations-alert.png)

További információt a **javaslatok** panelen találhat.

![Ajánlatok](./media/cache-configure/redis-cache-recommendations.png)

Ezeket a mérőszámokat a **Redis-hez készült Azure cache** [figyelési diagramok](cache-how-to-monitor.md#monitoring-charts) és [használati diagramok](cache-how-to-monitor.md#usage-charts) szakaszában követheti nyomon.

Az egyes díjszabási szintek eltérő korlátokkal rendelkeznek az ügyfélkapcsolatok, a memória és a sávszélesség tekintetében. Ha a gyorsítótár megközelíti a maximális kapacitást a metrikák számára egy tartós időtartamon belül, a rendszer javaslatot hoz létre. A **javaslatok** eszköz által áttekintett metrikákkal és korlátokkal kapcsolatos további információkért tekintse meg az alábbi táblázatot:

| Azure cache a Redis metrikához | További információ |
| --- | --- |
| Hálózatisávszélesség-felhasználás |[Gyorsítótár teljesítményének rendelkezésre álló sávszélessége](cache-faq.md#cache-performance) |
| Csatlakoztatott ügyfelek |[Alapértelmezett Redis-kiszolgáló konfigurációja – MaxClients](#maxclients) |
| Kiszolgáló terhelése |[Használati diagramok – Redis-kiszolgáló terhelése](cache-how-to-monitor.md#usage-charts) |
| Memóriahasználat |[Gyorsítótár teljesítményének mérete](cache-faq.md#cache-performance) |

A gyorsítótár frissítéséhez kattintson a **Frissítés most** lehetőségre a díjszabási csomag módosításához és a gyorsítótár [skálázásához](#scale) . Az árképzési szintek kiválasztásával kapcsolatos további információkért tekintse meg [Az Azure cache Redis-ajánlat és-méret használatát](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) ismertető témakört.


### <a name="scale"></a>Méretezés
Kattintson a **Scale (méretezés** ) elemre a gyorsítótár díjszabási szintjeinek megtekintéséhez vagy módosításához. A skálázással kapcsolatos további információkért lásd: az [Azure cache méretezése a Redis](cache-how-to-scale.md).

![Azure cache a Redis díjszabási szintjéhez](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis-fürt mérete
Az **(előzetes verzió) Redis** elemre kattintva módosíthatja a fürt méretét egy futó prémium gyorsítótárban, ha a fürtözés engedélyezve van.

> [!NOTE]
> Vegye figyelembe, hogy habár az Redis prémium szintű Azure cache-t általánosan elérhetővé tettük, a Redis-fürt mérete funkció jelenleg előzetes verzióban érhető el.
> 
> 

![Redis-fürt mérete](./media/cache-configure/redis-cache-redis-cluster-size.png)

A fürt méretének módosításához használja a csúszkát, vagy írjon be egy 1 és 10 közötti számot a szegmensek **száma** szövegmezőbe, majd kattintson **az OK** gombra a mentéshez.

> [!IMPORTANT]
> A Redis fürtszolgáltatás csak a prémium szintű gyorsítótárak esetében érhető el. További információ: [fürtözés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-clustering.md)-hez.
> 
> 


### <a name="redis-data-persistence"></a>Redis-adatmegőrzés
A prémium szintű gyorsítótárban az adatmegőrzés engedélyezéséhez, letiltásához vagy konfigurálásához kattintson az **Redis adatmegőrzés** lehetőségre. A Redis-hez készült Azure cache a [RDB-megőrzés](cache-how-to-premium-persistence.md#configure-rdb-persistence) vagy a [AOF-megőrzés](cache-how-to-premium-persistence.md#configure-aof-persistence)használatával biztosít Redis-megőrzést.

További információ: az [adatmegőrzés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-persistence.md)-hez.


> [!IMPORTANT]
> A Redis adatmegőrzés csak a prémium szintű gyorsítótárak esetében érhető el.
> 
> 

### <a name="schedule-updates"></a>Frissítések ütemezése
A **frissítések ütemezett frissítése** panelen megadhatja a gyorsítótár Redis-kiszolgálói frissítéseinek karbantartási időszakát. 

> [!IMPORTANT]
> A karbantartási időszak csak a Redis-kiszolgáló frissítéseire vonatkozik, és nem a gyorsítótárat üzemeltető virtuális gépek operációs rendszerének összes Azure-frissítésére vagy frissítésére.
> 
> 

![Frissítések ütemezése](./media/cache-configure/redis-schedule-updates.png)

A karbantartási időszak megadásához tekintse meg a kívánt napokat, és minden nap esetében határozza meg a karbantartási időszak kezdő óráját, majd kattintson **az OK**gombra. A karbantartási időszak időpontja UTC.

> [!IMPORTANT]
> Az **ütemezett frissítések** funkció csak a prémium szintű gyorsítótárak esetében érhető el. További információkért és útmutatásért lásd: [Azure cache for Redis Administration – frissítések frissítése](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Georeplikáció

A **geo-replikáció** panel egy mechanizmust biztosít két prémium szintű Azure cache Redis-példányokhoz való összekapcsolásához. Az egyik gyorsítótár elsődleges csatolt gyorsítótárként van kijelölve, a másik pedig másodlagos csatolt gyorsítótárként. A másodlagos csatolt gyorsítótár írásvédett lesz, és az elsődleges gyorsítótárba írt információk replikálódnak a másodlagos csatolt gyorsítótárba. Ez a funkció a gyorsítótár Azure-régiók közötti replikálására használható.

> [!IMPORTANT]
> A **geo-replikáció** csak a prémium szintű gyorsítótárak esetében érhető el. További információ és útmutatás: [geo-replikáció konfigurálása az Azure cache-hez a Redis](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network (Virtuális hálózat)
A **Virtual Network** szakasz a gyorsítótár virtuális hálózati beállításainak konfigurálását teszi lehetővé. A prémium szintű gyorsítótár VNET-támogatással történő létrehozásával és a beállítások frissítésével kapcsolatos információkért lásd: [Virtual Network támogatásának konfigurálása prémium szintű Azure cache-hez a Redis-hez](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> A virtuális hálózati beállítások csak a VNET-támogatással konfigurált prémium gyorsítótárak esetében érhetők el a gyorsítótár létrehozásakor. 
> 
> 

### <a name="firewall"></a>Tűzfal

A tűzfalszabályok konfigurálása a Redis összes Azure cache-hez elérhető.

A gyorsítótárra vonatkozó tűzfalszabályok megtekintéséhez és konfigurálásához kattintson a **tűzfal** elemre.

![Tűzfal](./media/cache-configure/redis-firewall-rules.png)

Megadhatja a tűzfalszabályok kezdő és záró IP-címtartományt. A tűzfalszabályok konfigurálásakor csak a megadott IP-címtartományok kapcsolatai kapcsolódhatnak a gyorsítótárhoz. Tűzfalszabály mentésekor rövid idő elteltével a szabály érvényben marad. Ez a késés általában egy percnél kisebb.

> [!IMPORTANT]
> Az Azure cache Redis-figyelő rendszerekhez való kapcsolatai mindig engedélyezve vannak, még akkor is, ha a tűzfalszabályok konfigurálva vannak.
> 
> 

### <a name="properties"></a>Tulajdonságok
Kattintson a **Tulajdonságok** elemre a gyorsítótárra vonatkozó információk megtekintéséhez, beleértve a gyorsítótár-végpontot és a portokat.

![Azure cache a Redis tulajdonságaihoz](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Zárolások
A **zárolások** szakasz lehetővé teszi egy előfizetés, erőforráscsoport vagy erőforrás zárolását, hogy megakadályozza a szervezet más felhasználói számára a kritikus erőforrások véletlen törlését vagy módosítását. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Automation-szkript

Kattintson az **Automation script (automatizálási parancsfájl** ) lehetőségre a üzembe helyezett erőforrások sablonjának létrehozásához és exportálásához a későbbi üzembe helyezésekhez. További információ a sablonok használatáról: [erőforrások központi telepítése Azure Resource Manager-sablonokkal](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Felügyeleti beállítások
A **felügyelet** szakaszban található beállítások lehetővé teszik a következő rendszergazdai feladatok végrehajtását a gyorsítótárban. 

![Felügyelet](./media/cache-configure/redis-cache-administration.png)

* [Adatok importálása](#importexport)
* [Adatok exportálása](#importexport)
* [Újraindítás](#reboot)


### <a name="importexport"></a>Import / Export (Importálás és exportálás)
Az import/export egy Azure cache a Redis adatkezelési művelethez, amely lehetővé teszi az adatok importálását és exportálását a gyorsítótárba egy Azure cache for Redis Database (RDB) pillanatkép importálásával és exportálásával egy Azure Storage-fiókban található oldal blobba. Az Importálás/exportálás lehetővé teszi, hogy áttelepítse a különböző Azure cache-t a Redis-példányok között, vagy a használat előtt feltöltse a gyorsítótárat az adatokkal.

Az importálással bármilyen felhőben vagy környezetben futó Redis-kiszolgálóról Redis kompatibilis RDB-fájlokat lehet használni, beleértve a Linuxon, a Windowson vagy bármely más felhőalapú szolgáltatón, például a Amazon Web Serviceson vagy más felhőben futó Redis. Az adatok importálása egyszerű módszer a gyorsítótár előre feltöltött adatokkal való létrehozására. Az importálási folyamat során az Azure cache for Redis betölti a RDB-fájlokat az Azure Storage-ból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

Az Exportálás lehetővé teszi az Azure cache-ben tárolt adatexportálást a Redis, hogy Redis a kompatibilis RDB-fájlokat. Ezzel a szolgáltatással áthelyezheti az adatok egyik Azure-gyorsítótárból a Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során létrejön egy ideiglenes fájl az Azure cache-t futtató virtuális gépen a Redis Server-példányhoz, és a fájl fel lesz töltve a kijelölt Storage-fiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződött be, a rendszer törli az ideiglenes fájlt.

> [!IMPORTANT]
> Az import/export csak a prémium szintű csomagok gyorsítótárai esetében érhető el. További információ és útmutatás: [adatok importálása és exportálása az Azure cache-ben a Redis-hez](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Újraindítás
Az **Újraindítás** panel lehetővé teszi a gyorsítótár csomópontjainak újraindítását. Ez az újraindítási funkció lehetővé teszi az alkalmazás rugalmasságának tesztelését, ha hiba történt a gyorsítótár-csomópontok meghibásodása esetén.

![Újraindítás](./media/cache-configure/redis-cache-reboot.png)

Ha olyan prémium szintű gyorsítótárral rendelkezik, amelyen engedélyezve van a fürtözés, kiválaszthatja, hogy a gyorsítótár mely szegmensei induljon újra.

![Újraindítás](./media/cache-configure/redis-cache-reboot-cluster.png)

A gyorsítótár egy vagy több csomópontjának újraindításához válassza ki a kívánt csomópontokat, majd kattintson az **Újraindítás**gombra. Ha olyan prémium szintű gyorsítótárral rendelkezik, amelyen engedélyezve van a fürtözés, válassza ki az újraindítani kívánt szegmens (eke) t, majd kattintson az **Újraindítás**gombra. Néhány perc elteltével a kiválasztott csomópont (ok) újraindul, és néhány perc múlva újra online állapotba kerül.

> [!IMPORTANT]
> Az újraindítás mostantól minden díjszabási szinten elérhető. További információkért és útmutatásért lásd: [Azure cache for Redis Administration – újraindítás](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Monitoring

A **figyelés** szakasz lehetővé teszi a diagnosztika és a figyelés konfigurálását az Azure cache-hez a Redis számára. További információ a Redis-figyelési és-diagnosztikai Azure cache-ről: az [Azure cache figyelése a Redis](cache-how-to-monitor.md).

![Diagnosztika](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis metrikák](#redis-metrics)
* [Riasztási szabályok](#alert-rules)
* [Diagnosztika](#diagnostics)

### <a name="redis-metrics"></a>Redis metrikák
Kattintson a **Redis metrikák** elemre a gyorsítótár [metrikáinak megtekintéséhez](cache-how-to-monitor.md#view-cache-metrics) .

### <a name="alert-rules"></a>Riasztási szabályok

Kattintson a **riasztási szabályok** elemre a riasztások Azure cache-alapú Redis-metrikák alapján történő konfigurálásához. További információ: [riasztások](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnosztika

Alapértelmezés szerint a Azure Monitor gyorsítótár-metrikái [30 napig tárolódnak](../azure-monitor/platform/data-platform-metrics.md) , majd törlődnek. Ha 30 napnál hosszabb ideig szeretné megőrizni a gyorsítótár-metrikákat, kattintson a **diagnosztika** lehetőségre a gyorsítótár-diagnosztika tárolásához használt [Storage-fiók konfigurálásához](cache-how-to-monitor.md#export-cache-metrics) .

>[!NOTE]
>A gyorsítótár-metrikák Storage-ba való archiválásán kívül továbbíthatja [őket egy Event hub-ba, vagy elküldheti azokat Azure monitor naplókba](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Támogatási & hibaelhárítási beállítások
A **támogatási és hibaelhárítási** szakaszban található beállítások lehetővé teszik a gyorsítótárral kapcsolatos problémák megoldását.

![Támogatás + hibaelhárítás](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Erőforrás állapota](#resource-health)
* [Új támogatási kérelem](#new-support-request)

### <a name="resource-health"></a>Resource Health
A **Resource Health** figyeli az erőforrást, és jelzi, hogy a várt módon fut-e. Az Azure Resource Health szolgáltatással kapcsolatos további információkért lásd: az [Azure Resource Health áttekintése](../resource-health/resource-health-overview.md).

> [!NOTE]
> A Resource Health jelenleg nem tud jelentést készíteni az Azure cache állapotáról a virtuális hálózatban üzemeltetett Redis-példányok esetében. További információ: az [összes gyorsítótár-funkció működése a GYORSÍTÓTÁR VNET való üzemeltetése során?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Új támogatási kérelem
Kattintson az **új támogatási kérelem** elemre a gyorsítótárhoz tartozó támogatási kérelem megnyitásához.





## <a name="default-redis-server-configuration"></a>Alapértelmezett Redis-kiszolgáló konfigurációja
A Redis-példányok új Azure gyorsítótára a következő alapértelmezett Redis-konfigurációs értékekkel van konfigurálva:

> [!NOTE]
> Az ebben a szakaszban található beállítások nem módosíthatók a `StackExchange.Redis.IServer.ConfigSet` metódus használatával. Ha ezt a metódust a szakasz egyik parancsával hívja meg, akkor a következő példához hasonló kivétel fordul elő:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> A konfigurálható értékek (például a **maximális memória-házirend**) a Azure Portal vagy parancssori felügyeleti eszközök, például az Azure CLI vagy a PowerShell használatával konfigurálhatók.
> 
> 

| Beállítás | Alapértelmezett érték | Leírás |
| --- | --- | --- |
| `databases` |16 |Az adatbázisok alapértelmezett száma 16, de az árképzési csomag alapján eltérő számot is beállíthat. <sup>1</sup> az alapértelmezett adatbázis az db 0, a `connection.GetDatabase(dbid)`, ahol a `dbid` `0` és `databases - 1`közötti szám lehet. |
| `maxclients` |A<sup>2</sup> . árképzési szinttől függ |Ez az érték a csatlakoztatott ügyfelek által egyszerre engedélyezett maximális szám. Ha elérte a korlátot, a Redis lezárja az összes új kapcsolatot, és "az ügyfelek maximális száma elérte a hibát" hibaüzenetet adja vissza. |
| `maxmemory-policy` |`volatile-lru` |A Maxmemory házirend az a beállítás, amellyel a Redis kiválaszthatja, hogy a rendszer hogyan válassza ki, hogy mit kell eltávolítani az `maxmemory` (a gyorsítótár létrehozásakor kiválasztott gyorsítótár-ajánlat mérete). Az Azure cache for Redis az alapértelmezett beállítás a `volatile-lru`, amely eltávolítja a kulcsokat egy lejárati készlettel egy LRU algoritmus használatával. Ez a beállítás konfigurálható a Azure Portalban. További információ: memória- [házirendek](#memory-policies). |
| `maxmemory-samples` |3 |A memória mentéséhez a LRU és a minimális TTL-algoritmusokat a pontos algoritmusok helyett közelítő algoritmusok használják. Alapértelmezés szerint a Redis három kulcsot ellenőriz, és kiválasztja azt, amelyet a közelmúltban kevésbé használt. |
| `lua-time-limit` |5000 |Lua-szkriptek maximális végrehajtási ideje ezredmásodpercben. Ha elérte a maximális végrehajtási időt, a Redis azt naplózza, hogy a parancsfájl még a maximálisan megengedett idő után is végrehajtás alatt áll, és a rendszer megkezdi a válaszadást a hibákkal rendelkező lekérdezésekre. |
| `lua-event-limit` |500 |A parancsfájl-események várólistájának maximális mérete. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Az ügyfél kimeneti pufferének korlátai felhasználhatók az olyan ügyfelek leválasztásának kényszerítésére, amelyek nem elég gyorsan olvasni az adatokat a kiszolgálóról (ennek gyakori oka, hogy a pub/sub-ügyfél nem tud az üzeneteket olyan gyorsan használni, ahogy a közzétevő elő tudja állítani őket). Tovább információ: [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>az egyes Azure-gyorsítótárok esetében a `databases` korlátja eltér a Redis díjszabási szintjénél, és beállítható a gyorsítótár létrehozásakor. Ha nincs megadva `databases` beállítás a gyorsítótár létrehozásakor, az alapértelmezett érték 16.

* Alapszintű és standard gyorsítótárak
  * C0 (250 MB) gyorsítótár – legfeljebb 16 adatbázis
  * C1 (1 GB) gyorsítótár – legfeljebb 16 adatbázis
  * C2 (2,5 GB) gyorsítótár – legfeljebb 16 adatbázis
  * C3 (6 GB) gyorsítótár – legfeljebb 16 adatbázis
  * C4 (13 GB) gyorsítótár – akár 32 adatbázis
  * C5 (26 GB) gyorsítótár – akár 48 adatbázis
  * C6 (53 GB) gyorsítótár – legfeljebb 64 adatbázis
* Prémium gyorsítótárak
  * P1 (6 GB-60 GB) – legfeljebb 16 adatbázis
  * P2 (13 GB-130 GB) – legfeljebb 32 adatbázis
  * P3 (26 GB-260 GB) – legfeljebb 48 adatbázis
  * P4 (53 GB-530 GB) – legfeljebb 64 adatbázis
  * Minden prémium szintű gyorsítótár a Redis-fürtön engedélyezve – a Redis-fürt csak az adatbázis 0 használatát támogatja, így a Redis-fürtön engedélyezve lévő prémium szintű gyorsítótárak esetében a `databases` korlát gyakorlatilag 1, a [Select](https://redis.io/commands/select) parancs használata nem engedélyezett. További információkért lásd: [az ügyfélalkalmazás használatának módosítása a fürtözéshez?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Az adatbázisokkal kapcsolatos további információkért lásd: [Mik a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> A `databases` beállítás csak a gyorsítótár létrehozásakor és csak a PowerShell, a CLI vagy más felügyeleti ügyfelek használatával konfigurálható. Ha például a PowerShell használatával konfigurálja a `databases` a gyorsítótár létrehozása során, tekintse meg a [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases)című témakört.
> 
> 

<a name="maxclients"></a>
<sup>2</sup>`maxclients` az egyes Azure-gyorsítótárak esetében eltér a Redis díjszabási szintjénél.

* Alapszintű és standard gyorsítótárak
  * C0 (250 MB) gyorsítótár – legfeljebb 256 kapcsolat
  * C1 (1 GB) gyorsítótár – akár 1 000 kapcsolat
  * C2 (2,5 GB) gyorsítótár – akár 2 000 kapcsolat
  * C3 (6 GB) gyorsítótár – akár 5 000 kapcsolat
  * C4 (13 GB) gyorsítótár – akár 10 000 kapcsolat
  * C5 (26 GB) gyorsítótár – akár 15 000 kapcsolat
  * C6 (53 GB) gyorsítótár – akár 20 000 kapcsolat
* Prémium gyorsítótárak
  * P1 (6 GB-60 GB) – legfeljebb 7 500 kapcsolat
  * P2 (13 GB-130 GB) – legfeljebb 15 000 kapcsolat
  * P3 (26 GB-260 GB) – legfeljebb 30 000 kapcsolat
  * P4 (53 GB-530 GB) – legfeljebb 40 000 kapcsolat

> [!NOTE]
> Míg a gyorsítótárak minden mérete *egy adott* számú kapcsolaton keresztül engedélyezett, a Redis-kapcsolatokhoz kapcsolódó minden egyes kapcsolathoz hozzá kell rendelni a terhelést. Ilyen például a CPU és a memóriahasználat a TLS/SSL titkosítás miatt. Egy adott gyorsítótár méretének maximális korlátja egy enyhén betöltött gyorsítótár. Ha a terhelés a kapcsolatok terhelése *és* az ügyfél műveleteinek terhelése meghaladja a rendszer kapacitását, akkor a gyorsítótár a kapacitással kapcsolatos problémákat is tapasztalhatja, még akkor is, ha nem lépte túl a gyorsítótár jelenlegi méretére vonatkozó korlátot.
> 
> 



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>A Redis Azure cache-ben nem támogatott Redis-parancsok
> [!IMPORTANT]
> Mivel az Azure cache konfigurálását és kezelését a Redis-példányok esetében a Microsoft felügyeli, a következő parancsok le vannak tiltva. Ha megpróbálja meghívni őket, a `"(error) ERR unknown command"`hoz hasonló hibaüzenet jelenik meg.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * HIBAKERESÉS
> * ÁTTELEPÍTÉSE
> * MENTÉS
> * SHUTDOWN
> * SLAVEOF
> * A FÜRTön belüli írási parancsok le vannak tiltva, de a csak olvasási jogosultsággal rendelkező fürt parancsai engedélyezettek.
> 
> 

További információ a Redis parancsokról: [https://redis.io/commands](https://redis.io/commands).

## <a name="redis-console"></a>Redis-konzol
A Redis-példányok számára a **Redis-konzol**segítségével biztonságosan adhat meg parancsokat az Azure cache-hez, amely az összes gyorsítótári szinten elérhető Azure Portal.

> [!IMPORTANT]
> - A Redis-konzol nem működik a [VNET](cache-how-to-premium-vnet.md). Ha a gyorsítótár egy VNET része, akkor csak a VNET lévő ügyfelek férhetnek hozzá a gyorsítótárhoz. Mivel a Redis-konzol a helyi böngészőben fut, amely a VNET kívül esik, nem tud csatlakozni a gyorsítótárhoz.
> - Nem minden Redis-parancs támogatott az Azure cache-ben a Redis számára. Az Azure cache for Redis szolgáltatásban letiltott Redis-parancsok listáját az [Azure cache Redis szakaszban nem támogatott korábbi Redis-parancsok között](#redis-commands-not-supported-in-azure-cache-for-redis) találja. További információ a Redis parancsokról: [https://redis.io/commands](https://redis.io/commands).
> 
> 

A Redis-konzol eléréséhez kattintson a **konzol** elemre az **Azure cache for Redis** panelben.

![Redis-konzol](./media/cache-configure/redis-console-menu.png)

Ha parancsokat szeretne kiadni a gyorsítótár-példányon, írja be a kívánt parancsot a konzolba.

![Redis-konzol](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>A Redis-konzol használata prémium szintű fürtözött gyorsítótárral

Ha a Redis-konzolt prémium szintű fürtözött gyorsítótárral használja, a parancsok a gyorsítótár egyetlen szegmensére is kiadhatók. Ha egy adott szegmenshez szeretne kiadni egy parancsot, először kapcsolódjon a kívánt szegmenshez úgy, hogy rákattint a szegmens választóra.

![Redis-konzol](./media/cache-configure/redis-console-premium-cluster.png)

Ha olyan kulcsot próbál meg elérni, amely a csatlakoztatott szegmenstől eltérő szegmensben található, akkor a következő üzenethez hasonló hibaüzenet jelenik meg:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

Az előző példában a szegmens 1 a kiválasztott szegmens, de `myKey` a 0. szegmensben található, amelyet a hibaüzenet `(shard 0)` része jelez. Ebben a példában a `myKey`hoz való hozzáféréshez válassza a szegmens 0 elemet a szegmens választó használatával, majd adja ki a kívánt parancsot.


## <a name="move-your-cache-to-a-new-subscription"></a>A gyorsítótár áthelyezése egy új előfizetésre
A gyorsítótárat áthelyezheti egy új előfizetésre az **Áthelyezés**gombra kattintva.

![Az Azure cache áthelyezése a Redis](./media/cache-configure/redis-cache-move.png)

További információ az erőforrások egyik erőforráscsoporthoz egy másikra való áthelyezéséről, illetve az egyik előfizetésről a másikra: [erőforrások áthelyezése új erőforrás-csoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Következő lépések
* További információ a Redis-parancsok használatáról: [Hogyan futtathatók a Redis parancsok?](cache-faq.md#how-can-i-run-redis-commands)

