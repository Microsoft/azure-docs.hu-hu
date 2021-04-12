---
title: Kiszolgálói paraméterek – Azure Database for MySQL – rugalmas kiszolgáló
description: Ez a témakör útmutatást nyújt a kiszolgálói paraméterek konfigurálásához Azure Database for MySQL rugalmas kiszolgálókon.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: d64dc4f3c034279aee7401503bbb60883c9ed4e7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492239"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Kiszolgálói paraméterek Azure Database for MySQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk a kiszolgálói paraméterek Azure Database for MySQL rugalmas kiszolgálón történő konfigurálásával kapcsolatos szempontokat és irányelveket ismerteti.

## <a name="what-are-server-variables"></a>Mi a kiszolgálói változók? 

A MySQL-motor számos különböző [kiszolgálói változót/paramétert](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) biztosít a motor működésének konfigurálásához és finomhangolásához. Bizonyos paraméterek dinamikusan állíthatók be futtatókörnyezet közben, míg mások "statikus" állapotban vannak, és a kiszolgáló újraindítását igénylik.

Azure Database for MySQL rugalmas kiszolgáló lehetővé teszi a MySQL-kiszolgáló különböző paramétereinek értékének módosítását a [Azure Portal](./how-to-configure-server-parameters-portal.md) és az [Azure CLI](./how-to-configure-server-parameters-cli.md) használatával a számítási feladatok igényeinek megfelelően.

## <a name="configurable-server-parameters"></a>Konfigurálható kiszolgálói paraméterek

A kiszolgálói paraméterek használatával kezelheti Azure Database for MySQL rugalmas kiszolgáló konfigurációját. A kiszolgálói paraméterek az alapértelmezett és ajánlott értékkel vannak konfigurálva a kiszolgáló létrehozásakor. A Azure Portal kiszolgálói paraméter a módosítható és nem módosítható kiszolgálói paramétereket is megjeleníti. A nem módosítható kiszolgálói paraméterek szürkén jelennek meg.

A támogatott kiszolgálói paraméterek listája folyamatosan bővül. A Azure Portal kiszolgálói paraméterek lapján megtekintheti a teljes listát, és konfigurálhatja a kiszolgáló paramétereinek értékeit.

Az alábbi részekben tájékozódhat a számos gyakran frissített kiszolgálói paraméter korlátairól. A korlátokat a kiszolgáló számítási szintjei és mérete (virtuális mag) határozzák meg.

> [!NOTE]
> Ha olyan kiszolgálói paramétert szeretne módosítani, amely nem módosítható, de szeretné, hogy a környezete módosítható legyen, nyisson meg egy [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) -tételt vagy szavazzon, ha a visszajelzés már létezik, ami segíthet a rangsorolásban.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Azure Database for MySQL rugalmas kiszolgálón a bináris naplók mindig engedélyezve vannak (azaz `log_bin` be van állítva). a log_bin_trust_function_creators alapértelmezés szerint be van kapcsolva a rugalmas kiszolgálókon. 

A bináris naplózási formátum mindig **sor** , és a kiszolgálóval létesített összes kapcsolat **mindig** sor alapú bináris naplózást használ. A sor-alapú bináris naplózással nem léteznek biztonsági problémák, és a bináris naplózás nem törhető le, így biztonságosan engedélyezheti [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) **a** használatát.

Ha a [ `log_bin_trust_function_creators` ] értéke KIkapcsolva értékre van állítva, az eseményindítók létrehozásához hasonló hibaüzenetek jelenhetnek meg, ha *nem rendelkezik a felügyelői jogosultsággal, és a bináris naplózás engedélyezve van (a kevésbé biztonságos `log_bin_trust_function_creators` változót érdemes használni)*. 

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) .

|**Tarifacsomag**|**Virtuális mag (ok)**|**Memória mérete (GiB)**|**Alapértelmezett érték (bájt)**|**Minimális érték (bájt)**|**Maximális érték (bájt)**|
|---|---|---|---|---|---|
|Feltört (B1s)|1|1|134217728|33554432|134217728|
|Feltört (B1ms)|1|2|536870912|134217728|536870912|
|Burstable|2|4|2147483648|134217728|2147483648|
|Általános célú|2|8|6442450944|134217728|6442450944|
|Általános célú|4|16|12884901888|134217728|12884901888|
|Általános célú|8|32|25769803776|134217728|25769803776|
|Általános célú|16|64|51539607552|134217728|51539607552|
|Általános célú|32|128|103079215104|134217728|103079215104|
|Általános célú|48|192|154618822656|134217728|154618822656|
|Általános célú|64|256|206158430208|134217728|206158430208|
|Memóriaoptimalizált|2|16|12884901888|134217728|12884901888|
|Memóriaoptimalizált|4|32|25769803776|134217728|25769803776|
|Memóriaoptimalizált|8|64|51539607552|134217728|51539607552|
|Memóriaoptimalizált|16|128|103079215104|134217728|103079215104|
|Memóriaoptimalizált|32|256|206158430208|134217728|206158430208|
|Memóriaoptimalizált|48|384|309237645312|134217728|309237645312|
|Memóriaoptimalizált|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

A MySQL a tábla létrehozása során megadott konfiguráció alapján különböző tablespaces-ban tárolja a InnoDB táblát. A [System tablespace](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) a InnoDB adatszótárának tárolóhelye. A [file-by-Table tablespace](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) egyetlen InnoDB-táblához tartalmaz adatmennyiséget és indexeket, és a fájlrendszerben tárolja a saját adatfájljában. Ezt a viselkedést a `innodb_file_per_table` Server paraméter vezérli. A `innodb_file_per_table` beállítás `OFF` hatására a InnoDB táblákat hozhat létre a System tablespaceben. Ellenkező esetben a InnoDB táblákat hoz létre a fájl-/táblázatos tablespaces-ben.

Azure Database for MySQL rugalmas kiszolgáló a legnagyobb, **4 TB**-os, egyetlen adatfájlban támogatott. Ha az adatbázis mérete meghaladja a 4 TB-ot, hozzon létre egy táblázatot [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tablespace-ban. Ha 4 TB-nál nagyobb méretű tábla van, használja a partíciós táblát.

### <a name="max_connections"></a>max_connections

Max_connection értékét a kiszolgáló memóriájának mérete határozza meg. 

|**Tarifacsomag**|**Virtuális mag (ok)**|**Memória mérete (GiB)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|---|
|Feltört (B1s)|1|1|85|10|171|
|Feltört (B1ms)|1|2|171|10|341|
|Burstable|2|4|341|10|683|
|Általános célú|2|8|683|10|1365|
|Általános célú|4|16|1365|10|2731|
|Általános célú|8|32|2731|10|5461|
|Általános célú|16|64|5461|10|10923|
|Általános célú|32|128|10923|10|21845|
|Általános célú|48|192|16384|10|32768|
|Általános célú|64|256|21845|10|43691|
|Memóriaoptimalizált|2|16|1365|10|2731|
|Memóriaoptimalizált|4|32|2731|10|5461|
|Memóriaoptimalizált|8|64|5461|10|10923|
|Memóriaoptimalizált|16|128|10923|10|21845|
|Memóriaoptimalizált|32|256|21845|10|43691|
|Memóriaoptimalizált|48|384|32768|10|65536|
|Memóriaoptimalizált|64|504|43008|10|86016|

Ha a kapcsolatok túllépik a korlátot, a következő hibaüzenet jelenhet meg:
> HIBA 1040 (08004): túl sok kapcsolat

> [!IMPORTANT]
> A legjobb megoldás érdekében javasoljuk, hogy használjon olyan kapcsolati Pooler, mint a ProxySQL, hogy hatékonyan kezelhesse a kapcsolatokat.

A MySQL-hez való új ügyfélkapcsolatok létrehozása időt vesz igénybe, a kapcsolatok pedig adatbázis-erőforrásokat foglalnak magukban, még akkor is, ha tétlenek. A legtöbb alkalmazás sok rövid életű kapcsolatot igényel, amely ezt a helyzetet összeképezi. Ennek eredményeképpen kevesebb erőforrás érhető el a tényleges munkaterheléshez, ami csökkenti a teljesítményt. Egy kapcsolati Pooler, amely csökkenti az üresjárati kapcsolatokat, és a meglévő kapcsolatokat újra felhasználva segít elkerülni ezt. A ProxySQL beállításával kapcsolatos további információkért látogasson el a [blogbejegyzésbe](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>A ProxySQL egy nyílt forráskódú közösségi eszköz. A szolgáltatást a Microsoft a lehető legjobb módon támogatja. Ahhoz, hogy az éles támogatáshoz mérvadó útmutatást kapjon, kiértékelheti és elérheti a [terméktámogatási ProxySQL](https://proxysql.com/services/support/).

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Ha a "sor mérete túl nagy (> 8126)" hasonló hibaüzenetet kap, érdemes kikapcsolni a paramétert **innodb_strict_mode**. A (z) **innodb_strict_mode** kiszolgáló paramétert nem lehet globálisan módosítani a kiszolgáló szintjén, mert ha a sor adatmérete nagyobb, mint 8k, a rendszer hibaüzenet nélkül csonkolja az adatmennyiséget, ami potenciális adatvesztéshez vezethet. Javasoljuk, hogy módosítsa a sémát úgy, hogy az illeszkedjen az oldal méretének korlátozásához. 

Ez a paraméter a használatával állítható be a munkamenet szintjén `init_connect` . Ha **innodb_strict_modet** szeretne beállítani a munkamenet szintjén, a [beállítás paraméter nem jelenik](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters)meg.

> [!NOTE]
> Ha rendelkezik olvasási replika-kiszolgálóval, akkor a forráskiszolgáló munkamenet-szintjének kikapcsolásához **innodb_strict_mode** beállítás megszakítja a replikálást. Javasoljuk, hogy a paramétert állítsa ki, ha olvasási replikával rendelkezik.

### <a name="time_zone"></a>time_zone

A kezdeti üzembe helyezést követően az Azure for MySQL rugalmas kiszolgáló a rendszertáblákat is tartalmazza az időzóna-információkhoz, de ezek a táblák nincsenek feltöltve. Az időzóna-táblázatok úgy tölthetők fel, hogy meghívja a `mysql.az_load_timezone` tárolt eljárást egy eszközről, például a MySQL-parancssorból vagy a MySQL Workbenchből. A tárolt eljárás meghívásához és a globális vagy munkamenet szintű időzónák beállításához tekintse meg a [Azure Portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) vagy az [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) -cikkeket.

## <a name="non-modifiable-server-parameters"></a>Nem módosítható kiszolgálói paraméterek

A Azure Portal Server paraméter panel a módosítható és nem módosítható kiszolgálói paramétereket is megjeleníti. A nem módosítható kiszolgálói paraméterek szürkén jelennek meg. Ha nem módosítható kiszolgálói paramétert szeretne konfigurálni a munkamenet szintjén, tekintse meg a [Azure Portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) vagy az [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) -cikket a paraméter a kapcsolati szinten való beállításához a használatával `init_connect` .

## <a name="next-steps"></a>Következő lépések

- [Kiszolgáló paramétereinek konfigurálása Azure Portal](./how-to-configure-server-parameters-portal.md)
- A [kiszolgálói paraméterek konfigurálása az Azure CLI-ben](./how-to-configure-server-parameters-cli.md)
