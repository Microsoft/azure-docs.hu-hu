---
title: Kiszolgálói paraméterek – Azure Database for MySQL
description: Ez a témakör az Azure Database for MySQL kiszolgálói paramétereinek konfigurálásához nyújt útmutatást.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/26/2021
ms.openlocfilehash: 1b0bcf528a16e2f75bf21235980424b5375f8824
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539484"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Kiszolgálói paraméterek a Azure Database for MySQL

Ez a cikk az Azure Database for MySQL kiszolgálói paramétereinek konfigurálásával kapcsolatos szempontokat és irányelveket ismerteti.

## <a name="what-are-server-parameters"></a>Mik a kiszolgálói paraméterek? 

A MySQL-motor számos különböző kiszolgálói változót/paramétert biztosít a motor működésének konfigurálásához és finomhangolásához. Bizonyos paraméterek dinamikusan állíthatók be futtatókörnyezet közben, míg mások "statikus" állapotban vannak, és a kiszolgáló újraindítását igénylik.

Azure Database for MySQL lehetővé teszi a MySQL-kiszolgáló különböző paramétereinek értékének módosítását a [Azure Portal](./howto-server-parameters.md), az [Azure CLI](./howto-configure-server-parameters-using-cli.md)és a [PowerShell](./howto-configure-server-parameters-using-powershell.md) használatával, hogy megfeleljen a számítási feladatok igényeinek.

## <a name="configurable-server-parameters"></a>Konfigurálható kiszolgálói paraméterek

A támogatott kiszolgálói paraméterek listája folyamatosan bővül. A Azure Portal kiszolgálói paraméterek lapján megtekintheti a teljes listát, és konfigurálhatja a kiszolgáló paramétereinek értékeit.

Az alábbi részekben tájékozódhat a számos gyakran frissített kiszolgálói paraméter korlátairól. A korlátokat a kiszolgáló díjszabási szintje és virtuális mag határozzák meg.

### <a name="thread-pools"></a>Szálak készletei

A MySQL hagyományosan egy szálat rendel minden ügyfélkapcsolathoz. Mivel az egyidejű felhasználók száma növekszik, a teljesítmény megfelelő csökkenést eredményez. Számos aktív szál jelentős hatással lehet a teljesítményre, mert a környezet nagyobb mértékű váltással, a szálak tartalmával és a CPU-gyorsítótárak helytelen területi beállításával jár.

A kiszolgálóoldali szolgáltatást és a kapcsolatok készletezését elkülönítő szál-készletek, a teljesítmény maximalizálása a munkaszál dinamikus készletének bevezetésével, amely a kiszolgálón futó aktív szálak számának korlátozására és a szálak forgalmának minimalizálására használható. Ezzel biztosítható, hogy a kapcsolatok kitörése ne okozza a kiszolgáló számára az erőforrások kifogyása vagy a memória-meghibásodás miatti összeomlás. A szál-készletek a rövid lekérdezésekhez és a nagy CPU-igényű számítási feladatokhoz, például OLTP számítási feladatokhoz használhatók.

A Thread Pools szolgáltatással kapcsolatos további információkért tekintse meg a [Thread Pools in Azure Database for MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173) című témakört.

> [!NOTE]
> A szál-készlet funkció nem támogatott a MySQL 5,6 verzióban. 

### <a name="configuring-the-thread-pool"></a>A szál készletének konfigurálása
A szál készletének engedélyezéséhez frissítse a `thread_handling` kiszolgálói paramétert a "készlet-of-Threads" értékre. Alapértelmezés szerint ez a paraméter a (z) értékre van állítva `one-thread-per-connection` , ami azt jelenti, hogy a MySQL új szálat hoz létre minden új kapcsolathoz. Vegye figyelembe, hogy ez egy statikus paraméter, és a kiszolgáló újraindítását igényli.

A készletben lévő szálak maximális és minimális számát a következő kiszolgálói paraméterek beállításával állíthatja be: 
- `thread_pool_max_threads`: Ez az érték biztosítja, hogy a készletben ne legyen több szál ennél a számnál.
- `thread_pool_min_threads`: Ez az érték határozza meg, hogy hány szálat kell fenntartani a rendszer a kapcsolatok bezárása után is.

A rövid lekérdezések teljesítményével kapcsolatos problémák javítása érdekében a Azure Database for MySQL lehetővé teszi a Batch-végrehajtást, ha a lekérdezés végrehajtása után azonnal vissza nem tért a szál-készletbe, a szálak egy rövid ideig aktívak maradnak, amíg a következő lekérdezés a kapcsolódáson keresztül meg nem történik. A szál ezután gyorsan és egyszer hajtja végre a lekérdezést, a következőre vár, amíg a folyamat teljes ideje meghaladja a küszöbértéket. A Batch-végrehajtás viselkedését a következő kiszolgálói paraméterek alapján határozzák meg:  

-  `thread_pool_batch_wait_timeout`: Ez az érték határozza meg, hogy egy szál Mikor várakozik egy másik lekérdezés feldolgozására.
- `thread_pool_batch_max_time`: Ez az érték határozza meg azt a maximális időt, ameddig egy szál megismétli a lekérdezés-végrehajtás ciklusát, és a következő lekérdezésre vár.

> [!IMPORTANT]
> Az éles üzemben való bekapcsolás előtt tesztelje a szál készletét. 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Azure Database for MySQL a bináris naplók mindig engedélyezve vannak (azaz `log_bin` be van állítva). Ha triggereket szeretne használni, akkor ehhez hasonló hibaüzenetet kap, *Ha nem rendelkezik a felügyelői jogosultsággal, és engedélyezve van a bináris naplózás (a kevésbé biztonságos `log_bin_trust_function_creators` változót érdemes használni)*. 

A bináris naplózási formátum mindig **sor** , és a kiszolgálóval létesített összes kapcsolat **mindig** sor alapú bináris naplózást használ. A sor-alapú bináris naplózással nem léteznek biztonsági problémák, és a bináris naplózás nem törhető le, így a biztonságos beállítás értéke [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) **true (igaz**) lehet.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) .

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Legfeljebb 4 TB tárterületet támogató kiszolgálók

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|872415232|134217728|872415232|
|Alapszintű|2|2684354560|134217728|2684354560|
|Általános célú|2|3758096384|134217728|3758096384|
|Általános célú|4|8053063680|134217728|8053063680|
|Általános célú|8|16106127360|134217728|16106127360|
|Általános célú|16|32749125632|134217728|32749125632|
|Általános célú|32|66035122176|134217728|66035122176|
|Általános célú|64|132070244352|134217728|132070244352|
|Memóriaoptimalizált|2|7516192768|134217728|7516192768|
|Memóriaoptimalizált|4|16106127360|134217728|16106127360|
|Memóriaoptimalizált|8|32212254720|134217728|32212254720|
|Memóriaoptimalizált|16|65498251264|134217728|65498251264|
|Memóriaoptimalizált|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>A kiszolgálók legfeljebb 16 TB tárterületet támogatnak

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|872415232|134217728|872415232|
|Alapszintű|2|2684354560|134217728|2684354560|
|Általános célú|2|7516192768|134217728|7516192768|
|Általános célú|4|16106127360|134217728|16106127360|
|Általános célú|8|32212254720|134217728|32212254720|
|Általános célú|16|65498251264|134217728|65498251264|
|Általános célú|32|132070244352|134217728|132070244352|
|Általános célú|64|264140488704|134217728|264140488704|
|Memóriaoptimalizált|2|15032385536|134217728|15032385536|
|Memóriaoptimalizált|4|32212254720|134217728|32212254720|
|Memóriaoptimalizált|8|64424509440|134217728|64424509440|
|Memóriaoptimalizált|16|130996502528|134217728|130996502528|
|Memóriaoptimalizált|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` csak a általános célú és a memória optimalizált díjszabási szintjein lehet frissíteni.

A MySQL a tábla létrehozása során megadott konfiguráció alapján különböző tablespaces-ban tárolja a InnoDB táblát. A [System tablespace](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) a InnoDB adatszótárának tárolóhelye. A [file-by-Table tablespace](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) egyetlen InnoDB-táblához tartalmaz adatmennyiséget és indexeket, és a fájlrendszerben tárolja a saját adatfájljában. Ezt a viselkedést a `innodb_file_per_table` Server paraméter vezérli. A `innodb_file_per_table` beállítás `OFF` hatására a InnoDB táblákat hozhat létre a System tablespaceben. Ellenkező esetben a InnoDB táblákat hoz létre a fájl-/táblázatos tablespaces-ben.

A Azure Database for MySQL a legnagyobb, **4 TB**-os, egyetlen adatfájlban támogatott. Ha az adatbázis mérete meghaladja a 4 TB-ot, hozzon létre egy táblázatot [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tablespace-ban. Ha 4 TB-nál nagyobb méretű tábla van, használja a partíciós táblát.

### <a name="join_buffer_size"></a>join_buffer_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) .

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános célú|2|262144|128|268435455|
|Általános célú|4|262144|128|536870912|
|Általános célú|8|262144|128|1073741824|
|Általános célú|16|262144|128|2147483648|
|Általános célú|32|262144|128|4294967295|
|Általános célú|64|262144|128|4294967295|
|Memóriaoptimalizált|2|262144|128|536870912|
|Memóriaoptimalizált|4|262144|128|1073741824|
|Memóriaoptimalizált|8|262144|128|2147483648|
|Memóriaoptimalizált|16|262144|128|4294967295|
|Memóriaoptimalizált|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Alapszintű|1|50|10|50|
|Alapszintű|2|100|10|100|
|Általános célú|2|300|10|600|
|Általános célú|4|625|10|1250|
|Általános célú|8|1250|10|2500|
|Általános célú|16|2500|10|5000|
|Általános célú|32|5000|10|10000|
|Általános célú|64|10000|10|20000|
|Memóriaoptimalizált|2|625|10|1250|
|Memóriaoptimalizált|4|1250|10|2500|
|Memóriaoptimalizált|8|2500|10|5000|
|Memóriaoptimalizált|16|5000|10|10000|
|Memóriaoptimalizált|32|10000|10|20000|

Ha a kapcsolatok túllépik a korlátot, a következő hibaüzenet jelenhet meg:
> HIBA 1040 (08004): túl sok kapcsolat

> [!IMPORTANT]
> A legjobb megoldás érdekében javasoljuk, hogy használjon olyan kapcsolati Pooler, mint a ProxySQL, hogy hatékonyan kezelhesse a kapcsolatokat.

A MySQL-hez való új ügyfélkapcsolatok létrehozása időt vesz igénybe, a kapcsolatok pedig adatbázis-erőforrásokat foglalnak magukban, még akkor is, ha tétlenek. A legtöbb alkalmazás sok rövid életű kapcsolatot igényel, amely ezt a helyzetet összeképezi. Ennek eredményeképpen kevesebb erőforrás érhető el a tényleges munkaterheléshez, ami csökkenti a teljesítményt. Egy kapcsolati Pooler, amely csökkenti az üresjárati kapcsolatokat, és a meglévő kapcsolatokat újra felhasználva segít elkerülni ezt. A ProxySQL beállításával kapcsolatos további információkért látogasson el a [blogbejegyzésbe](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>A ProxySQL egy nyílt forráskódú közösségi eszköz. A szolgáltatást a Microsoft a lehető legjobb módon támogatja. Ahhoz, hogy az éles támogatáshoz mérvadó útmutatást kapjon, kiértékelheti és elérheti a [terméktámogatási ProxySQL](https://proxysql.com/services/support/).

### <a name="max_heap_table_size"></a>max_heap_table_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) .

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános célú|2|16777216|16384|268435455|
|Általános célú|4|16777216|16384|536870912|
|Általános célú|8|16777216|16384|1073741824|
|Általános célú|16|16777216|16384|2147483648|
|Általános célú|32|16777216|16384|4294967295|
|Általános célú|64|16777216|16384|4294967295|
|Memóriaoptimalizált|2|16777216|16384|536870912|
|Memóriaoptimalizált|4|16777216|16384|1073741824|
|Memóriaoptimalizált|8|16777216|16384|2147483648|
|Memóriaoptimalizált|16|16777216|16384|4294967295|
|Memóriaoptimalizált|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

A lekérdezés gyorsítótára alapértelmezés szerint ki van kapcsolva. A lekérdezési gyorsítótár engedélyezéséhez konfigurálja a `query_cache_type` paramétert. 

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) .

> [!NOTE]
> A lekérdezési gyorsítótár elavult a MySQL-5.7.20 telepítése, és el lett távolítva a MySQL 8,0-ben.

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|* * Maximális érték * *|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános célú|2|0|0|16777216|
|Általános célú|4|0|0|33554432|
|Általános célú|8|0|0|67108864|
|Általános célú|16|0|0|134217728|
|Általános célú|32|0|0|134217728|
|Általános célú|64|0|0|134217728|
|Memóriaoptimalizált|2|0|0|33554432|
|Memóriaoptimalizált|4|0|0|67108864|
|Memóriaoptimalizált|8|0|0|134217728|
|Memóriaoptimalizált|16|0|0|134217728|
|Memóriaoptimalizált|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

Alapértelmezés szerint a lower_case_table_name 1 értékre van állítva, és a MySQL 5,6-es és a MySQL 5,7-es verziójában frissítheti ezt a paramétert

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names) .

> [!NOTE]
> A MySQL 8,0-ben a lower_case_table_name alapértelmezett értéke 1, és nem módosítható.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Ha a "sor mérete túl nagy (> 8126)" hasonló hibaüzenetet kap, érdemes kikapcsolni a paramétert **innodb_strict_mode**. A (z) **innodb_strict_mode** kiszolgáló paramétert nem lehet globálisan módosítani a kiszolgáló szintjén, mert ha a sor adatmérete nagyobb, mint 8k, az adatokat a rendszer a lehetséges adatvesztéshez vezető hiba nélkül csonkolja. Azt javasoljuk, hogy módosítsa a sémát úgy, hogy az illeszkedjen az oldal méretének korlátozásához. 

Ez a paraméter a használatával állítható be a munkamenet szintjén `init_connect` . Ha **innodb_strict_modet** szeretne beállítani a munkamenet szintjén, a [beállítás paraméter nem jelenik](./howto-server-parameters.md#setting-parameters-not-listed)meg.

> [!NOTE]
> Ha rendelkezik olvasási replika-kiszolgálóval, akkor a forráskiszolgáló munkamenet-szintjének kikapcsolásához **innodb_strict_mode** beállítás megszakítja a replikálást. Javasoljuk, hogy a paramétert állítsa ki, ha olvasási replikával rendelkezik.

### <a name="sort_buffer_size"></a>sort_buffer_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) .

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános célú|2|524288|32768|4194304|
|Általános célú|4|524288|32768|8388608|
|Általános célú|8|524288|32768|16777216|
|Általános célú|16|524288|32768|33554432|
|Általános célú|32|524288|32768|33554432|
|Általános célú|64|524288|32768|33554432|
|Memóriaoptimalizált|2|524288|32768|8388608|
|Memóriaoptimalizált|4|524288|32768|16777216|
|Memóriaoptimalizált|8|524288|32768|33554432|
|Memóriaoptimalizált|16|524288|32768|33554432|
|Memóriaoptimalizált|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) .

|**Tarifacsomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N.A.|N.A.|
|Általános célú|2|16777216|1024|67108864|
|Általános célú|4|16777216|1024|134217728|
|Általános célú|8|16777216|1024|268435456|
|Általános célú|16|16777216|1024|536870912|
|Általános célú|32|16777216|1024|1073741824|
|Általános célú|64|16777216|1024|1073741824|
|Memóriaoptimalizált|2|16777216|1024|134217728|
|Memóriaoptimalizált|4|16777216|1024|268435456|
|Memóriaoptimalizált|8|16777216|1024|536870912|
|Memóriaoptimalizált|16|16777216|1024|1073741824|
|Memóriaoptimalizált|32|16777216|1024|1073741824|

### <a name="innodb-buffer-pool-warmup"></a>InnoDB puffer-készlet bemelegedési
Azure Database for MySQL kiszolgáló újraindítása után a rendszer betölti a lemezen lévő adatlapokat, mivel a rendszer a táblákat kérdezi le. Ez nagyobb késést és lassabb teljesítményt eredményez a lekérdezések első végrehajtásakor. Ez nem fogadható el a késésre érzékeny munkaterhelések esetében. A InnoDB-puffer használatával a bemelegedési idő lerövidíti a bemelegedési időszakot azáltal, hogy az újraindítás előtt újra betölti a pufferben lévő lemezeket, és nem várta a DML-t, vagy KIVÁLASZTJA a műveleteket a megfelelő sorok eléréséhez.

Csökkentheti a bemelegedési időszakot a Azure Database for MySQL kiszolgáló újraindítása után, amely teljesítménybeli előnyt jelent a [InnoDB-puffer kiszolgálói paramétereinek](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)konfigurálásával. A InnoDB az egyes pufferek legutóbb használt lapjainak százalékos arányát menti a kiszolgáló leállításakor, és visszaállítja ezeket a lapokat a kiszolgáló indításakor.

Azt is fontos megjegyezni, hogy a jobb teljesítmény a kiszolgáló hosszú indítási ideje alapján történik. Ha ez a paraméter engedélyezve van, a kiszolgáló indítási és újraindítási ideje a kiszolgálón kiépített IOPS függően növekedni fog. Javasoljuk, hogy tesztelje és figyelje az újraindítási időt annak biztosítására, hogy az indítási/újraindítási teljesítmény elfogadható legyen, mivel a kiszolgáló ebben az időszakban nem érhető el. Ezt a paramétert nem ajánlott használni, ha a IOPS kiépített értéke kisebb, mint 1000 IOPS (vagy más szóval, ha a kiépített tároló kisebb, mint a 335GB.

A puffer állapotának mentése a kiszolgáló leállítására beállított kiszolgáló paraméterében a következőre: `innodb_buffer_pool_dump_at_shutdown` `ON` . Hasonlóképpen állítsa be a kiszolgálói paramétert a `innodb_buffer_pool_load_at_startup` `ON` puffer-készlet állapotának visszaállításához a kiszolgáló indításakor. A kiszolgáló paraméter értékének csökkentésével és finomhangolásával szabályozhatja az indítási és újraindítási hatást, a `innodb_buffer_pool_dump_pct` paraméter pedig a következőre van beállítva: `25` .

> [!Note]
> A InnoDB puffer-készlet bemelegedési paramétereit csak az általános célú tároló kiszolgálók támogatják, legfeljebb 16 TB tárhellyel. További információ a [Azure Database for MySQL tárolási lehetőségeiről](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage).

### <a name="time_zone"></a>time_zone

A kezdeti üzembe helyezéskor az Azure for MySQL-kiszolgáló az időzóna-információkhoz tartozó rendszertáblákat tartalmazza, de ezek a táblák nem lesznek feltöltve. Az időzóna-táblázatok úgy tölthetők fel, hogy meghívja a `mysql.az_load_timezone` tárolt eljárást egy eszközről, például a MySQL-parancssorból vagy a MySQL Workbenchből. A tárolt eljárás meghívásához és a globális vagy munkamenet szintű időzónák beállításához tekintse meg a [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) -cikkeket.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálói paraméterek

Az alábbi kiszolgálói paraméterek nem konfigurálhatók a szolgáltatásban:

|**Paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|alapszintű innodb_file_per_table|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

Az itt felsorolt egyéb változók az alapértelmezett MySQL beépített értékekre vannak beállítva. Az alapértelmezett értékekhez tekintse meg a MySQL docs [8,0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5,7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)és [5,6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) verzióit. 

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [konfigurálhat sever-paramétereket a Azure Portal használatával](./howto-server-parameters.md)
- Megtudhatja, hogyan [konfigurálhat sever-paramétereket az Azure CLI használatával](./howto-configure-server-parameters-using-cli.md)
- Megtudhatja, hogyan [konfigurálhat sever-paramétereket a PowerShell használatával](./howto-configure-server-parameters-using-powershell.md)
