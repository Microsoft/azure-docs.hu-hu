---
title: Korlátozások – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 9b18b24686908ac92f97ea0cae892369919ae4d6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721019"
---
# <a name="limitations-in-azure-database-for-mysql"></a>A Azure Database for MySQL korlátozásai
A következő szakaszok ismertetik a kapacitást, a tárolási motor támogatását, a jogosultságok támogatását, az adatmanipulációs nyilatkozatok támogatását és az adatbázis-szolgáltatás működési korlátait. Lásd még a MySQL-adatbázismotor esetében alkalmazandó [általános korlátozásokat](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) is.

## <a name="server-parameters"></a>Kiszolgálóparaméterek

> [!NOTE]
> Ha a (z) és a (z) kiszolgáló-paraméterek minimális/maximális értékeit keresi `max_connections` `innodb_buffer_pool_size` , akkor ezek az adatok a **[kiszolgálói paraméterek](./concepts-server-parameters.md)** cikkbe kerültek.

Azure Database for MySQL támogatja a kiszolgálói paraméterek értékének finomhangolását. Néhány paraméter minimális és maximális értéke (pl. `max_connections`, `join_buffer_size` , `query_cache_size` ) meghatározása a kiszolgáló díjszabási szintjével és virtuális mag történik. A korlátokkal kapcsolatos további információkért tekintse meg a [kiszolgálói paramétereket](./concepts-server-parameters.md) .

A kezdeti üzembe helyezéskor az Azure for MySQL-kiszolgáló az időzóna-információkhoz tartozó rendszertáblákat tartalmazza, de ezek a táblák nem lesznek feltöltve. Az időzóna-táblázatok úgy tölthetők fel, hogy meghívja a `mysql.az_load_timezone` tárolt eljárást egy eszközről, például a MySQL-parancssorból vagy a MySQL Workbenchből. A tárolt eljárás meghívásához és a globális vagy munkamenet szintű időzónák beállításához tekintse meg a [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) -cikkeket.

A szolgáltatás nem támogatja a jelszavakat, például a "validate_password" és a "caching_sha2_password".

## <a name="storage-engines"></a>Storage-motorok

A MySQL számos tárolási motort támogat. Azure Database for MySQL a következő tárolási motorok támogatottak és nem támogatottak:

### <a name="supported"></a>Támogatott
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [Archívum](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [ÖSSZEVONT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Jogosultságok az adatkezelési támogatás &

Számos kiszolgálói paraméter és beállítás akaratlanul csökkentheti a kiszolgáló teljesítményét, vagy megtagadhatja a MySQL-kiszolgáló DEA savas tulajdonságait. A szolgáltatás integritásának és az SLA-nak a termék szintjén történő fenntartása érdekében a szolgáltatás nem tesz elérhetővé több szerepkört. 

A MySQL szolgáltatás nem teszi lehetővé a közvetlen hozzáférést a mögöttes fájlrendszerhez. Egyes adatmanipulációs parancsok nem támogatottak. 

### <a name="unsupported"></a>Nem támogatott

A következők nem támogatottak:
- DBA-szerepkör: korlátozott. Azt is megteheti, hogy a rendszergazda felhasználóval (az új kiszolgáló létrehozásakor jön létre) lehetővé teszi a DDL-és DML-utasítások többségének végrehajtását. 
- SZUPER jogosultság: ehhez hasonlóan a [Super Privilege](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is korlátozott.
- Leszűkítés: a létrehozáshoz és a korlátozásához Super jogosultságok szükségesek. Ha biztonsági másolat használatával importálja az adatimportálást, távolítsa el `CREATE DEFINER` manuálisan a parancsokat, vagy használja a `--skip-definer` parancsot a mysqldump végrehajtásakor.
- Rendszeradatbázisok: a [MySQL rendszeradatbázis](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) csak olvasható, és a különböző Pásti funkciók támogatására szolgál. A rendszeradatbázis nem módosítható `mysql` .
- `SELECT ... INTO OUTFILE`: Nem támogatott a szolgáltatásban.
- `LOAD_FILE(file_name)`: Nem támogatott a szolgáltatásban.

### <a name="supported"></a>Támogatott
- `LOAD DATA INFILE` támogatott, de a `[LOCAL]` paramétert meg kell adni, és egy UNC elérési útra kell irányítani (az Azure Storage csatlakoztatva van az SMB protokollon keresztül).

## <a name="functional-limitations"></a>Funkcionális korlátozások

### <a name="scale-operations"></a>Skálázási műveletek
- A dinamikus skálázás az alapszintű díjszabási szintekre és a rendszerből nem támogatott.
- A kiszolgáló tárolási méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítése
- A fő adatbázismotor-verziók közötti automatikus áttelepítés jelenleg nem támogatott. Ha a következő főverzióra szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](./concepts-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanazzal a konfigurációval jön létre, mint a kiszolgáló.
- A törölt kiszolgáló visszaállítása nem támogatott.

### <a name="vnet-service-endpoints"></a>VNet-szolgáltatásvégpontok
- A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.

### <a name="storage-size"></a>Tárterület mérete
- Tekintse meg [a díjszabási](concepts-pricing-tiers.md) szinten a tárterületre vonatkozó korlátozásokat.

## <a name="current-known-issues"></a>Aktuális ismert problémák
- A MySQL-kiszolgáló példánya a kiszolgáló nem megfelelő verzióját jeleníti meg a kapcsolatok létrehozása után. A megfelelő kiszolgálópéldány-motor verziójának lekéréséhez használja a `select version();` parancsot.

## <a name="next-steps"></a>Következő lépések
- [Az egyes szolgáltatási szinteknél elérhető szolgáltatások](concepts-pricing-tiers.md)
- [Támogatott MySQL-adatbázis-verziók](concepts-supported-versions.md)
