---
title: Az Azure Cache for Redis adatvesztéssel járó hibáinak elhárítása
description: Megtudhatja, Hogyan oldhatók fel az adatvesztéssel kapcsolatos problémák az Azure cache használatával a Redis, például a kulcsok részleges elvesztése, a kulcs lejárata vagy a kulcsok teljes elvesztése.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 6db036752bab7b84b72a37b148eaec7aa5765ef3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92538595"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Az Azure Cache for Redis adatvesztéssel járó hibáinak elhárítása

Ez a cikk azt ismerteti, hogyan diagnosztizálhatja az Azure cache-ben esetlegesen előforduló tényleges vagy észlelt adatvesztést a Redis.

> [!NOTE]
> Az útmutatóban ismertetett hibaelhárítási lépések többek között a Redis-parancsok futtatására és a különböző teljesítmény-mérőszámok figyelésére vonatkozó utasításokat tartalmaznak. További információkért és útmutatásért tekintse meg a [További információ](#additional-information) szakasz cikkeit.
>

## <a name="partial-loss-of-keys"></a>Kulcsok részleges elvesztése

A Redis készült Azure cache nem törli a kulcsokat, miután a memóriában tárolták őket. Azonban eltávolítja a kulcsokat a lejárati vagy kizárási szabályzatokra, valamint az explicit kulcs-törlési parancsokra válaszul. Az Redis-példányhoz tartozó prémium vagy standard szintű Azure cache elsődleges csomópontján írt kulcsok nem érhetők el azonnal a replikán. Az adatok replikálása az elsődlegesről a replikára aszinkron és nem blokkoló módon történik.

Ha úgy találja, hogy a kulcsok eltűntek a gyorsítótárból, ellenőrizze az alábbi lehetséges okokat:

| Ok | Leírás |
|---|---|
| [Kulcs lejárata](#key-expiration) | A kulcsok el lesznek távolítva a rájuk vonatkozó időtúllépés miatt. |
| [Kulcs kizárása](#key-eviction) | A kulcsok el lesznek távolítva a memória nyomása alatt. |
| [Kulcs törlése](#key-deletion) | A kulcsok eltávolítása explicit törlési parancsokkal történik. |
| [Aszinkron replikáció](#async-replication) | Az adatreplikálási késések miatt a kulcsok nem érhetők el a replikán. |

### <a name="key-expiration"></a>Kulcs lejárata

A Redis-hez készült Azure cache automatikusan eltávolítja a kulcsot, ha a kulcshoz időtúllépés van rendelve, és az adott időszak lejárt. További információ a Redis-kulcs lejáratáról: a [lejárati](https://redis.io/commands/expire) parancs dokumentációja. Az időtúllépési értékek a [set](https://redis.io/commands/set), a [SETEX](https://redis.io/commands/setex), a [GETSET](https://redis.io/commands/getset)és az Other **\* Store** parancs használatával is megadhatók.

A kulcsok lejárati idejének lekéréséhez használja az [info](https://redis.io/commands/info) parancsot. A `Stats` szakasz a lejárt kulcsok teljes számát jeleníti meg. A `Keyspace` szakasz részletesen ismerteti az időtúllépéssel rendelkező kulcsok számát és az átlagos időtúllépési értéket.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

A gyorsítótárhoz tartozó diagnosztikai metrikákat is megtekintheti, hogy megtudja, van-e korreláció a kulcs hiánya és a lejárt kulcsok egy tüske között. Az ilyen típusú problémák hibakereséséhez tekintse **meg a** [Redis-webtárhely hibakeresésének](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) függelékét.

### <a name="key-eviction"></a>Kulcs kizárása

A Redis-hez készült Azure cache memóriát igényel az adattároláshoz. Kiüríti a kulcsokat a rendelkezésre álló memória felszabadításához, ha szükséges. Ha az [info](https://redis.io/commands/info) parancs **used_memory** vagy **used_memory_rss** értéke megközelíti a konfigurált **maxmemory** -beállítást, a Redis-hez készült Azure cache a [gyorsítótár-házirend](https://redis.io/topics/lru-cache)alapján megkezdi a kulcsok eltávolítását a memóriából.

A kizárt kulcsok számát a [info](https://redis.io/commands/info) parancs használatával figyelheti:

```
# Stats

evicted_keys:13224
```

A gyorsítótárhoz tartozó diagnosztikai metrikákat is megtekintheti, hogy megtudja, van-e korreláció a kulcs hiánya és a kizárt kulcsok egy tüske között. Az ilyen típusú problémák hibakereséséhez tekintse **meg a** [Redis-webtárhely hibakeresésének](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) függelékét.

### <a name="key-deletion"></a>Kulcs törlése

A Redis-ügyfelek kiadhatják a [del](https://redis.io/commands/del) vagy a [HDEL](https://redis.io/commands/hdel) parancsot, hogy explicit módon eltávolítsanak kulcsokat az Azure cache-ből a Redis. A törlési műveletek számát az [info](https://redis.io/commands/info) parancs használatával követheti nyomon. Ha a **del** vagy a **HDEL** parancs meghívása megtörtént, a szakasz a `Commandstats` szakaszban látható.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Aszinkron replikáció

A standard vagy a prémium szinten lévő Redis-példányok bármely Azure cache-je elsődleges csomóponttal és legalább egy replikával van konfigurálva. Az adatok az elsődlegesről a replikára másolódnak aszinkron módon, egy háttérben futó folyamat használatával. A [Redis.IO](https://redis.io/topics/replication) webhely leírja, hogyan működik az Redis adatreplikációja. Azokban az esetekben, amikor az ügyfelek gyakran írnak Redis, a részleges adatvesztés miatt előfordulhat, hogy a replikáció nem garantált azonnali. Ha például az elsődleges üzenet azt *követően* leáll, hogy az ügyfél egy kulcsot ír, de *mielőtt* a háttérben elküldi a kulcsot a replikának, a kulcs elvész, amikor a replika átveszi az új elsődlegesként.

## <a name="major-or-complete-loss-of-keys"></a>Kulcsok jelentős vagy teljes elvesztése

Ha a legtöbb vagy az összes kulcs eltűnt a gyorsítótárból, ellenőrizze az alábbi lehetséges okokat:

| Ok | Leírás |
|---|---|
| [Kulcs kiürítése](#key-flushing) | A kulcsok törlése manuálisan megtörtént. |
| [Helytelen adatbázis-kijelölés](#incorrect-database-selection) | A Redis-hez készült Azure cache nem alapértelmezett adatbázis használatára van beállítva. |
| [Redis-példány hibája](#redis-instance-failure) | A Redis-kiszolgáló nem érhető el. |

### <a name="key-flushing"></a>Kulcs kiürítése

Az ügyfelek meghívhatják a [FLUSHDB](https://redis.io/commands/flushdb) parancsot egy *önálló* adatbázis vagy [FLUSHALL](https://redis.io/commands/flushall) összes kulcsának eltávolítására egy Redis-gyorsítótárban lévő *összes adatbázis* kulcsának eltávolításához. Annak megállapításához, hogy a kulcsok kiürítése megtörtént-e, használja az [info](https://redis.io/commands/info) parancsot. A `Commandstats` szakasz azt mutatja, hogy a **Flush** parancs meghívása megtörtént-e:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Helytelen adatbázis-kijelölés

A Redis-hez készült Azure cache alapértelmezés szerint a **db0** adatbázist használja. Ha átvált egy másik adatbázisra (például **db1**), és megpróbálja beolvasni a kulcsokat, az Azure cache for Redis nem fogja tudni megkeresni azokat. Minden adatbázis logikailag különálló egység, és egy másik adatkészletet tárol. A [Select](https://redis.io/commands/select) parancs használatával más elérhető adatbázisokat is használhat, és mindegyikben megkeresheti a kulcsokat.

### <a name="redis-instance-failure"></a>Redis-példány hibája

A Redis egy memóriában tárolt adattároló. Az adat a Redis cache-t üzemeltető fizikai vagy virtuális gépeken történik. Az alapszintű csomag Redis-példányához tartozó Azure cache csak egyetlen virtuális gépen (VM) fut. Ha a virtuális gép nem érhető el, a gyorsítótárban tárolt összes adattal elvész. 

A standard és a prémium szintű gyorsítótárak sokkal nagyobb rugalmasságot biztosítanak az adatvesztés ellen, ha két virtuális gépet használ egy replikált konfigurációban. Ha az ilyen gyorsítótárban lévő elsődleges csomópont meghibásodik, a replika csomópont átveszi az adatszolgáltatások automatikus kiszolgálására. Ezek a virtuális gépek külön tartományokban találhatók a hibák és a frissítések számára, így a lehető legkevesebb eséllyel elérhetetlenné válnak. Ha azonban jelentős adatközpont-kimaradás történik, előfordulhat, hogy a virtuális gépek továbbra is leállnak egymással. Ezekben a ritka esetekben az adatai elvesznek.

Érdemes lehet a [Redis adatmegőrzést](https://redis.io/topics/persistence) és a [geo-replikálást](./cache-how-to-geo-replication.md) használni az adatvédelem ezen infrastrukturális hibákkal szembeni védelme érdekében.

## <a name="additional-information"></a>További információ

- [Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása](cache-troubleshoot-server.md)
- [A megfelelő szint kiválasztása](cache-overview.md#choosing-the-right-tier)
- [Az Azure Cache for Redis monitorozása](cache-how-to-monitor.md)
- [Hogyan Futtathatok Redis-parancsokat?](cache-development-faq.md#how-can-i-run-redis-commands)