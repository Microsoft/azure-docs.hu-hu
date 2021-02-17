---
title: Az Azure cache figyelése a Redis
description: Ismerje meg, hogyan figyelheti a Redis-példányok Azure cache állapotát és teljesítményét
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 0ff11c9601fb55e27d8780185d77c177e9d9201b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584637"
---
# <a name="monitor-azure-cache-for-redis"></a>Az Azure cache figyelése a Redis

Az Azure cache for Redis [Azure monitort](../azure-monitor/index.yml) használ a gyorsítótár-példányok figyelésére. Megtekintheti a metrikákat, rögzítheti a metrikák diagramokat a Kezdőpulton, testreszabhatja a figyelési diagramok dátumát és időtartományát, metrikákat adhat hozzá és távolíthat el a diagramokból, valamint riasztásokat állíthat be bizonyos feltételek teljesülése esetén. Ezek az eszközök lehetővé teszik az Azure cache Redis-példányok állapotának figyelését, valamint a gyorsítótárazási alkalmazások kezelését.

Az Azure cache Redis-példányokra vonatkozó metrikáit körülbelül percenként, a Redis [info](https://redis.io/commands/info) parancs használatával gyűjti össze, és 30 napig automatikusan tárolja (lásd: [gyorsítótár-metrikák exportálása](#export-cache-metrics) más adatmegőrzési szabályzat konfigurálásához), hogy megjelenjenek a metrikák diagramjaiban, és a riasztási szabályok alapján értékeljék azokat. További információ az egyes gyorsítótár-metrikák különböző adatértékeiről: [elérhető metrikák és jelentési időközök](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

A gyorsítótár-metrikák megtekintéséhez [tallózzon](cache-configure.md#configure-azure-cache-for-redis-settings) a [Azure Portal](https://portal.azure.com)gyorsítótár-példányához.  Az Azure cache for Redis tartalmaz néhány beépített diagramot az **Áttekintés** panelen és a **Redis metrikák** paneljén. Minden diagram testreszabható a metrikák hozzáadásával vagy eltávolításával, valamint a jelentéskészítési időköz módosításával.

![Hat gráf látható. Az egyik közülük a gyorsítótárbeli találatok és a gyorsítótár az elmúlt órában marad.](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Előre konfigurált mérőszámok diagramjainak megtekintése

Az **Áttekintés** panelen a következő előre konfigurált figyelési diagramok szerepelnek.

* [Figyelési diagramok](#monitoring-charts)
* [Használati diagramok](#usage-charts)

### <a name="monitoring-charts"></a>Figyelési diagramok

Az **Áttekintés** panel **figyelés** szakasza a **találatok és a kimaradás**, a lekérések **, a** **kapcsolatok** és az **összes parancs** diagramot tartalmazza.

![Figyelési diagramok](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Használati diagramok

Az **Áttekintés** panel **használat** szakasza a **Redis-kiszolgáló terhelését**, a **memóriahasználat**, a **hálózati sávszélesség** és a **CPU-használat** diagramokat tartalmazza, valamint megjeleníti a gyorsítótár-példány **díjszabási szintjét** is.

![Használati diagramok](./media/cache-how-to-monitor/redis-cache-usage-part.png)

A **díjszabási** csomag megjeleníti a gyorsítótár díjszabási szintjét, és felhasználható a gyorsítótár más díjszabási [szinten való skálázására](cache-how-to-scale.md) .

## <a name="view-metrics-with-azure-monitor"></a>Metrikák megtekintése az Azure monitorral

Az Redis-metrikák megtekintéséhez és az egyéni diagramok létrehozásához Azure Monitor használatával kattintson a **metrikák** elemre az **erőforrás menüben**, és szabja testre a diagramot a kívánt metrikák, jelentési időköz, diagramtípus és egyéb elemek használatával.

![A contoso55 bal oldali navigációs paneljén a metrikák a figyelés és a Kiemelt lehetőség alatt állnak. A mérőszámokon a metrikák listája látható. A gyorsítótár-találatok és a gyorsítótár-kihagyók ki vannak választva.](./media/cache-how-to-monitor/redis-cache-monitor.png)

A metrikák Azure Monitor használatával történő használatáról a [Microsoft Azure metrikáinak áttekintése](../azure-monitor/data-platform.md)című témakörben talál további információt.

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Gyorsítótár-metrikák exportálása

Alapértelmezés szerint a Azure Monitor gyorsítótár-metrikái [30 napig tárolódnak](../azure-monitor/essentials/data-platform-metrics.md) , majd törlődnek. Ha 30 napnál hosszabb ideig szeretné megőrizni a gyorsítótár-metrikákat, [kijelölheti a Storage-fiókot](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) , és megadhatja a gyorsítótár metrikáinak **megőrzési (nap)** szabályzatát. 

Storage-fiók konfigurálása a gyorsítótár metrikái számára:

1. Az **Azure cache for Redis** oldalon, a **figyelés** fejléc alatt válassza a **diagnosztika** lehetőséget.
2. Válassza a **+ diagnosztikai beállítások hozzáadása** elemet.
3. Nevezze el a beállításokat.
4. Keresse **meg az archívumot egy Storage-fiókban**. A tárolásért és a tranzakciókért a normál adatforgalmi díjat kell fizetnie, amikor diagnosztikát küld egy Storage-fiókba.
4. Válassza a **Konfigurálás** lehetőséget, hogy kiválassza azt a Storage-fiókot, amelyben tárolni szeretné a gyorsítótár metrikáit.
5. A táblázat fejlécének **mérőszáma** alatt jelölje be a tárolni kívánt sorok melletti jelölőnégyzetet (például **AllMetrics**). **Adatmegőrzési (nap)** szabályzatot kell megadni. A maximális napok megőrzésének megadható értéke **365 nap**. Ha azonban örökre meg szeretné őrizni a metrikák adatait, állítsa a **megőrzés (nap)** **értéket 0-ra**.
6. Kattintson a **Mentés** gombra.


![Redis-diagnosztika](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>A gyorsítótár-metrikák Storage-ba való archiválásán kívül továbbíthatja [őket egy Event hub-ba, vagy elküldheti azokat Azure monitor naplókba](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values).
>

A metrikák eléréséhez tekintse meg azokat a Azure Portal a jelen cikkben ismertetett módon, és a [Azure monitor metrikák REST API](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)használatával is elérheti őket.

> [!NOTE]
> Ha módosítja a Storage-fiókokat, a korábban konfigurált Storage-fiókban lévő adatfájlok továbbra is letölthetők maradnak, de nem jelennek meg a Azure Portal.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Elérhető metrikák és jelentéskészítési időközök

A gyorsítótár metrikáit számos jelentési intervallum, például az **elmúlt óra**, a **mai**, a **múlt hét** és az **Egyéni** használatával kell jelenteni. Az egyes metrikák diagram **metrika** paneljén a diagram egyes mérőszámai átlagos, minimális és maximális értékei láthatók, és egyes mérőszámok a jelentési időköz összegét jelenítik meg. 

Minden metrika két verziót tartalmaz. Az egyik mérőszám a teljes gyorsítótárra, valamint a [fürtözést](cache-how-to-premium-clustering.md)használó gyorsítótárak teljesítményére vonatkozóan a metrika egy második, a név részét képező változata a `(Shard 0-9)` gyorsítótárban lévő egyetlen szegmens esetében a teljesítményt is méri. Ha például egy gyorsítótár négy szegmensből `Cache Hits` áll, a teljes gyorsítótárban lévő találatok száma, és `Cache Hits (Shard 3)` csak a gyorsítótárban lévő szegmensek találatai.

> [!NOTE]
> Akkor is előfordulhat, ha a gyorsítótár üresjáratban van, és nem kapcsolódik aktív ügyfélalkalmazás. a gyorsítótár-tevékenységek, például a csatlakoztatott ügyfelek, a memóriahasználat és a végrehajtott műveletek láthatók. Ez a tevékenység normális a Redis-példányhoz tartozó Azure cache működése során.
> 
> 

| Metric | Leírás |
| --- | --- |
| Gyorsítótár-találatok |A megadott jelentési intervallumban végrehajtott sikeres kulcsok száma. Ez a szám `keyspace_hits` a Redis [info](https://redis.io/commands/info) parancsból képezi le. |
| Gyorsítótár késése (előzetes verzió) | A gyorsítótár késése a gyorsítótár csomópontok közötti késése alapján számítható ki. Ezt a mérőszámot a rendszer a másodpercenként méri, és három dimenzióval rendelkezik:, `Avg` `Min` és, amelyek a `Max` gyorsítótár átlagos, minimális és maximális késését jelölik a megadott jelentési intervallumban. |
| Gyorsítótár-tévesztések |A sikertelen kulcsok keresésének száma a megadott jelentési intervallumban. Ez a szám `keyspace_misses` a REDIS info parancsból képezi le. A gyorsítótár-lemaradás nem feltétlenül jelenti azt, hogy probléma van a gyorsítótárral. Ha például a gyorsítótár-félretéve programozási mintát használja, az alkalmazás először a gyorsítótárban tekinti meg az elemeket. Ha az elem nem létezik (cache Miss), az elem beolvasása az adatbázisból történik, és a következő alkalommal kerül a gyorsítótárba. A gyorsítótár-leállások normál viselkedést jelentenek a gyorsítótár-félretéve programozási minta esetében. Ha a gyorsítótárbeli hiányzó adatok száma a vártnál magasabb, vizsgálja meg a gyorsítótárból kitöltött és beolvasott alkalmazási logikát. Ha a memória terhelése miatt kizárja az elemeket a gyorsítótárból, előfordulhat, hogy egyes gyorsítótárak hiányoznak, de a memória nyomásának jobb mérőszáma a következő: `Used Memory` `Evicted Keys` . |
| Gyorsítótár-olvasás |A gyorsítótárban másodpercenként beolvasott adatok mennyisége megabájt/másodpercben (MB/s) a megadott jelentési intervallumban. Ez az érték a gyorsítótárat működtető virtuális gépet támogató hálózati adapterek származik, és nem Redis-specifikus. **Ez az érték a gyorsítótár által használt hálózati sávszélességnek felel meg. Ha riasztásokat szeretne beállítani a kiszolgálóoldali hálózati sávszélesség korlátaihoz, akkor ezt a számlálót használva hozza létre `Cache Read` . Tekintse meg [ezt a táblázatot](cache-planning-faq.md#azure-cache-for-redis-performance) a különböző gyorsítótár-díjszabási szintek és méretek megfigyelt sávszélesség-korlátozásához.** |
| Gyorsítótár-írás |A gyorsítótárban másodpercenként (MB/s) a gyorsítótárba írt adatmennyiség a megadott jelentési intervallumban. Ez az érték a gyorsítótárat működtető virtuális gépet támogató hálózati adapterek származik, és nem Redis-specifikus. Ez az érték felel meg a gyorsítótárba az ügyféltől továbbított adatok hálózati sávszélességének. |
| Csatlakoztatott ügyfelek |A gyorsítótárban a megadott jelentési intervallumban létesített ügyfélkapcsolatok száma. Ez a szám `connected_clients` a REDIS info parancsból képezi le. A [kapcsolódási korlát](cache-configure.md#default-redis-server-configuration) elérésekor a gyorsítótár további kapcsolódási kísérletei sikertelenek lesznek. A belső folyamatok és kapcsolatok miatt még a csatlakoztatott ügyfelek néhány példánya is lehet, ha nincsenek aktív ügyfélalkalmazások. |
| CPU |A Redis-kiszolgálóhoz tartozó Azure cache CPU-kihasználtsága a megadott jelentési intervallumban százalékban kifejezve. Ez az érték leképezi az operációs rendszer `\Processor(_Total)\% Processor Time` teljesítményszámláló értékét. |
| Hibák | Meghatározott hibák és teljesítményproblémák, amelyekkel a gyorsítótár egy megadott jelentési intervallum során tapasztalható. Ennek a metrikának nyolc dimenziója van, amelyek különböző típusú hibákat mutatnak be, de a későbbiekben további hozzáadásra kerülhetnek. A következő típusú hibaüzenetek jelennek meg: <br/><ul><li>Feladatátvétel – ha a gyorsítótár **feladatátvételt** hajt végre (az alárendelt előlépteti az elsődlegest)</li><li>**Dataloss** – Ha adatvesztés történik a gyorsítótárban</li><li>**UnresponsiveClients** – ha az ügyfelek nem tudnak elég gyorsan beolvasni az adatait a kiszolgálóról</li><li>**AOF** – ha a AOF-megőrzéssel kapcsolatos probléma merül fel</li><li>**RDB** – ha a RDB-megőrzéssel kapcsolatos probléma merül fel</li><li>**Importálás** – ha probléma van a RDB importálásával kapcsolatban</li><li>**Exportálás** – ha a RDB exportálásával kapcsolatos probléma merül fel</li></ul> |
| Kizárt kulcsok |A gyorsítótárból kizárt elemek száma a megadott jelentési intervallumban a `maxmemory` korlát miatt. Ez a szám `evicted_keys` a REDIS info parancsból képezi le. |
| Lejárt kulcsok |Az elemek száma a gyorsítótárból a megadott jelentési intervallumban lejárt. Ez az érték `expired_keys` a REDIS info parancsból képezi le.|
| Lekérések |A lekérési műveletek száma a gyorsítótárból a megadott jelentési intervallumban. Ez az érték a következő értékek összege a Redis info all parancsban:,,,,, `cmdstat_get` `cmdstat_hget` `cmdstat_hgetall` `cmdstat_hmget` `cmdstat_mget` `cmdstat_getbit` és `cmdstat_getrange` , és egyenlő a gyorsítótárbeli találatok összegével és a jelentési intervallumban lemaradt értékekkel. |
| Műveletek száma másodpercenként | A gyorsítótár-kiszolgáló által másodpercenként feldolgozott parancsok teljes száma a megadott jelentési intervallumban.  Ez az érték a "instantaneous_ops_per_sec" értéket képezi le a Redis INFO parancsból. |
| Redis-kiszolgáló terhelése |Azon ciklusok százalékos aránya, amelyekben a Redis-kiszolgáló foglalt, és nem várakozik az üzenetek üresjáratára. Ha a számláló eléri a 100-et, az azt jelenti, hogy a Redis-kiszolgáló elérte a teljesítmény felső határát, és a processzor nem tud gyorsabban feldolgozni. Ha magas Redis-kiszolgáló betöltését látja, akkor időtúllépési kivételek jelennek meg az ügyfélen. Ebben az esetben érdemes megfontolnia, hogy az adatait több gyorsítótárba lehessen felskálázással vagy particionálni. |
| Halmazok |A megadott jelentési intervallumban a gyorsítótárban beállított műveletek száma. Ez az érték a következő értékek összege a Redis info all paranccsal:,,,,,,,,, `cmdstat_set` `cmdstat_hset` `cmdstat_hmset` `cmdstat_hsetnx` `cmdstat_lset` `cmdstat_mset` `cmdstat_msetnx` `cmdstat_setbit` `cmdstat_setex` `cmdstat_setrange` és `cmdstat_setnx` . |
| Kulcsok összesen  | A gyorsítótárban lévő kulcsok maximális száma a korábbi jelentési időszakban. Ez a szám `keyspace` a REDIS info parancsból képezi le. Az alapul szolgáló mérőszámok rendszerének korlátozása miatt a fürtöket engedélyező gyorsítótárak esetében az összes kulcs visszaadja azon szegmens kulcsainak maximális számát, amely a jelentési intervallumban a kulcsok maximális száma volt.  |
| Műveletek összesen |A gyorsítótár-kiszolgáló által a megadott jelentési intervallumban feldolgozott parancsok teljes száma. Ez az érték `total_commands_processed` a REDIS info parancsból képezi le. Ha a Redis-hez készült Azure cache-t kizárólag a pub/sub esetében használja, akkor a,, vagy rendszerhez nem lesz mérőszám, `Cache Hits` `Cache Misses` `Gets` `Sets` de `Total Operations` a pub/sub műveletekhez tartozó gyorsítótár-használatot tükröző mérőszámok szerepelnek. |
| Felhasznált memória |A gyorsítótárban lévő kulcs/érték párokhoz felhasznált gyorsítótár-memória mennyisége a megadott jelentési intervallumban (MB). Ez az érték `used_memory` a REDIS info parancsból képezi le. Ez az érték nem tartalmazza a metaadatokat vagy a töredezettséget. |
| Felhasznált memória százalékos aránya | A megadott jelentési időközben használt teljes memória%-a.  Ez az érték a `used_memory` REDIS info parancs értékére hivatkozik a százalék kiszámításához. |
| Felhasznált memória RSS-címe |A megadott jelentési intervallumban MEGABÁJTban használt gyorsítótár-memória mennyisége, beleértve a töredezettséget és a metaadatokat is. Ez az érték `used_memory_rss` a REDIS info parancsból képezi le. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Riasztások

A mérőszámok és tevékenységnaplók alapján beállíthatja, hogy milyen riasztásokat szeretne kapni. Az Azure Monitor segítségével konfigurálhat riasztásokat, amelyek aktiválása esetén a követező műveletek végrehajtására kerülhet sor:

* E-mail-értesítés küldése
* Webhook meghívása
* Egy Azure Logic App-alkalmazás meghívása

A gyorsítótár riasztási szabályainak konfigurálásához kattintson a **riasztási szabályok** elemre az **erőforrás menüben**.

![Figyelés](./media/cache-how-to-monitor/redis-cache-monitoring.png)

A riasztások konfigurálásával és használatával kapcsolatos további információkért lásd: [a riasztások áttekintése](../azure-monitor/alerts/alerts-classic-portal.md).

## <a name="activity-logs"></a>Tevékenységnaplók
A Tevékenységnaplók betekintést nyújtanak a Redis-példányok Azure-gyorsítótárában végrehajtott műveletekre. Korábban "naplók" vagy "operatív naplók" néven ismert. A Tevékenységnaplók használatával meghatározhatja a "mi, ki és mikor" minden olyan írási műveletet (PUT, POST, DELETE), amelyet az Azure cache-ben a Redis-példányok számára készített. 

> [!NOTE]
> A tevékenységi naplók nem tartalmaznak olvasási (GET) műveleteket.
>

Ha meg szeretné tekinteni a gyorsítótárhoz tartozó tevékenységek naplóit, kattintson az **erőforrás menü** **tevékenység naplók** elemére.

További információ a tevékenységek naplóiról: [Az Azure-tevékenység naplójának áttekintése](../azure-monitor/essentials/platform-logs-overview.md).