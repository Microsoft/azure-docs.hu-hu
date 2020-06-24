---
title: Kiszolgálói paraméterek – Azure Database for MariaDB
description: Ez a témakör az Azure Database for MariaDB kiszolgálói paramétereinek konfigurálásához nyújt útmutatást.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 5ea5a94c73adda1328b97c3e985c4b6ac614d1b9
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255024"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Kiszolgálói paraméterek a Azure Database for MariaDB

Ez a cikk az Azure Database for MariaDB kiszolgálói paramétereinek konfigurálásával kapcsolatos szempontokat és irányelveket ismerteti.

## <a name="what-are-server-parameters"></a>Mik a kiszolgálói paraméterek? 

A MariaDB motor számos különböző kiszolgálói változót/paramétert biztosít, amelyek segítségével konfigurálhatja és beállíthatja a motor viselkedését. Bizonyos paraméterek dinamikusan állíthatók be futtatókörnyezet közben, míg mások "statikus" állapotban vannak, és a kiszolgáló újraindítását igénylik.

Azure Database for MariaDB lehetővé teszi a különböző MariaDB-kiszolgálói paraméterek értékének módosítását a [Azure Portal](./howto-server-parameters.md), az [Azure CLI](./howto-configure-server-parameters-cli.md)és a [PowerShell](./howto-configure-server-parameters-using-powershell.md) használatával a számítási feladatok igényeinek megfelelően.

## <a name="configurable-server-parameters"></a>Konfigurálható kiszolgálói paraméterek

A támogatott kiszolgálói paraméterek listája folyamatosan bővül. A Azure Portal kiszolgálói paraméterek lapján megtekintheti a teljes listát, és konfigurálhatja a kiszolgáló paramétereinek értékeit.

Az alábbi részekben tájékozódhat a számos gyakran frissített kiszolgálói paraméter korlátairól. A korlátokat a kiszolgáló díjszabási szintje és virtuális mag határozzák meg.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size) .

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Legfeljebb 4 TB tárterületet támogató kiszolgálók

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
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

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
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
> `innodb_file_per_table`csak a általános célú és a memória optimalizált díjszabási szintjein lehet frissíteni.

A MariaDB a tábla létrehozása során megadott konfiguráció alapján különböző tablespaces-ben tárolja a InnoDB táblát. A [System tablespace](https://mariadb.com/kb/en/innodb-system-tablespaces/) a InnoDB adatszótárának tárolóhelye. A [file-by-Table tablespace](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) egyetlen InnoDB-táblához tartalmaz adatmennyiséget és indexeket, és a fájlrendszerben tárolja a saját adatfájljában. Ezt a viselkedést a `innodb_file_per_table` Server paraméter vezérli. A `innodb_file_per_table` beállítás `OFF` hatására a InnoDB táblákat hozhat létre a System tablespaceben. Ellenkező esetben a InnoDB táblákat hoz létre a fájl-/táblázatos tablespaces-ben.

A Azure Database for MariaDB a legnagyobb **1 TB**-ot támogatja egyetlen adatfájlban. Ha az adatbázis mérete meghaladja az 1 TB-ot, hozzon létre egy táblázatot [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) tablespace-ban. Ha 1 TB-nál nagyobb méretű tábla van, akkor a partíciós táblát kell használnia.

### <a name="join_buffer_size"></a>join_buffer_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) .

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
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

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
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

Ha új ügyfélkapcsolatokat hoz létre a MariaDB-hoz, akkor az idő és a létrehozás után ezek a kapcsolatok adatbázis-erőforrásokat foglalnak el, még ha tétlen is. A legtöbb alkalmazás sok rövid életű kapcsolatot igényel, amely ezt a helyzetet összeképezi. Ennek eredményeképpen kevesebb erőforrás érhető el a tényleges munkaterheléshez, ami csökkenti a teljesítményt. Egy kapcsolati Pooler, amely csökkenti az üresjárati kapcsolatokat, és a meglévő kapcsolatokat újra felhasználva segít elkerülni ezt. A ProxySQL beállításával kapcsolatos további információkért látogasson el a [blogbejegyzésbe](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>A ProxySQL egy nyílt forráskódú közösségi eszköz. A szolgáltatást a Microsoft a lehető legjobb módon támogatja. Ahhoz, hogy az éles támogatáshoz mérvadó útmutatást kapjon, kiértékelheti és elérheti a [terméktámogatási ProxySQL](https://proxysql.com/services/support/).

### <a name="max_heap_table_size"></a>max_heap_table_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) .

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
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

A lekérdezési gyorsítótár alapértelmezés szerint engedélyezve van a MariaDB `have_query_cache` paraméterrel. 

A paraméterrel kapcsolatos további információkért tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) .

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|* * Maximális érték * *|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
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

### <a name="sort_buffer_size"></a>sort_buffer_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) .

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
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

A paraméterrel kapcsolatos további információkért tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) .

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|
|Alapszintű|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Alapszintű|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
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

### <a name="time_zone"></a>time_zone

Az időzóna-táblázatok úgy tölthetők fel, hogy meghívja a `mysql.az_load_timezone` tárolt eljárást egy eszközről, például a MySQL-parancssorból vagy a MySQL Workbenchből. A tárolt eljárás meghívásához és a globális vagy munkamenet szintű időzónák beállításához tekintse meg a [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) vagy az [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) -cikkeket.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálói paraméterek

Az alábbi kiszolgálói paraméterek nem konfigurálhatók a szolgáltatásban:

|**Paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|alapszintű innodb_file_per_table|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

Az itt felsorolt egyéb kiszolgálói paraméterek a [MariaDB](https://mariadb.com/kb/en/server-system-variables/)alapértelmezett értékeit adják meg a MariaDB.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [konfigurálhat sever-paramétereket a Azure Portal használatával](./howto-server-parameters.md)
- Megtudhatja, hogyan [konfigurálhat sever-paramétereket az Azure CLI használatával](./howto-configure-server-parameters-cli.md)
- Megtudhatja, hogyan [konfigurálhat sever-paramétereket a PowerShell használatával](./howto-configure-server-parameters-using-powershell.md)