---
title: Korlátozások – Azure Database for MariaDB
description: Ez a cikk a Azure Database for MariaDB korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/2/2020
ms.openlocfilehash: d4546732f067988c9d7dd2d11c718a15fbe32d23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664299"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>A Azure Database for MariaDB korlátozásai
A következő szakaszok ismertetik a kapacitást, a tárolási motor támogatását, a jogosultságok támogatását, az adatmanipulációs nyilatkozatok támogatását és az adatbázis-szolgáltatás működési korlátait.

## <a name="server-parameters"></a>Kiszolgálóparaméterek

> [!NOTE]
> Ha a (z) és a (z) kiszolgáló-paraméterek minimális/maximális értékeit keresi `max_connections` `innodb_buffer_pool_size` , akkor ezek az adatok a **[kiszolgálói paraméterek](./concepts-server-parameters.md)** cikkbe kerültek.

Azure Database for MariaDB támogatja a kiszolgálói paraméterek értékének finomhangolását. Néhány paraméter minimális és maximális értéke (pl. `max_connections`, `join_buffer_size` , `query_cache_size` ) meghatározása a kiszolgáló díjszabási szintjével és virtuális mag történik. A korlátokkal kapcsolatos további információkért tekintse meg a [kiszolgálói paramétereket](./concepts-server-parameters.md) .

A kezdeti üzembe helyezés után az Azure for MariaDB-kiszolgáló az időzóna-információkhoz tartozó rendszertáblákat tartalmazza, de ezek a táblák nem lesznek feltöltve. Az időzóna-táblázatok úgy tölthetők fel, hogy meghívja a `mysql.az_load_timezone` tárolt eljárást egy eszközről, például a MySQL-parancssorból vagy a MySQL Workbenchből. A tárolt eljárás meghívásához és a globális vagy munkamenet szintű időzónák beállításához tekintse meg a [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) vagy az [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) -cikkeket.

A szolgáltatás nem támogatja a jelszavakat, például a "validate_password" és a "caching_sha2_password".

## <a name="storage-engine-support"></a>A Storage Engine támogatása

### <a name="supported"></a>Támogatott
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMÓRIA](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [Archívum](https://mariadb.com/kb/en/library/archive/)

## <a name="privileges--data-manipulation-support"></a>Jogosultságok az adatkezelési támogatás &

Számos kiszolgálói paraméter és beállítás akaratlanul csökkentheti a kiszolgáló teljesítményét, vagy megtagadhatja a MariaDB-kiszolgáló által kihagyott sav-tulajdonságokat. A szolgáltatás integritásának és az SLA-nak a termék szintjén történő fenntartása érdekében a szolgáltatás nem tesz elérhetővé több szerepkört. 

A MariaDB szolgáltatás nem engedélyezi közvetlen hozzáférését a mögöttes fájlrendszerhez. Egyes adatmanipulációs parancsok nem támogatottak. 

## <a name="privilege-support"></a>Jogosultságok támogatása

### <a name="unsupported"></a>Nem támogatott

A következők nem támogatottak:
- DBA-szerepkör: korlátozott. Azt is megteheti, hogy a rendszergazda felhasználóval (az új kiszolgáló létrehozásakor jön létre) lehetővé teszi a DDL-és DML-utasítások többségének végrehajtását. 
- SZUPER jogosultság: ehhez hasonlóan a [Super Privilege](https://mariadb.com/kb/en/library/grant/#global-privileges) is korlátozott.
- Leszűkítés: a létrehozáshoz és a korlátozásához Super jogosultságok szükségesek. Ha biztonsági másolat használatával importálja az adatimportálást, távolítsa el `CREATE DEFINER` manuálisan a parancsokat, vagy használja a `--skip-definer` parancsot a mysqldump végrehajtásakor.
- Rendszeradatbázisok: a [MySQL rendszeradatbázis](https://mariadb.com/kb/en/the-mysql-database-tables/) csak olvasható, és a különböző Pásti funkciók támogatására szolgál. A rendszeradatbázis nem módosítható `mysql` .
- `SELECT ... INTO OUTFILE`: Nem támogatott a szolgáltatásban.

### <a name="supported"></a>Támogatott
- `LOAD DATA INFILE` támogatott, de a `[LOCAL]` paramétert meg kell adni, és egy UNC elérési útra kell irányítani (az Azure Storage csatlakoztatva van az SMB protokollon keresztül).

## <a name="functional-limitations"></a>Funkcionális korlátozások

### <a name="scale-operations"></a>Skálázási műveletek
- A dinamikus skálázás az alapszintű díjszabási szintekre és a rendszerből nem támogatott.
- A kiszolgáló tárolási méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítése
- A fő adatbázismotor-verziók közötti automatikus áttelepítés jelenleg nem támogatott.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanazzal a konfigurációval jön létre, mint a kiszolgáló.
- A törölt kiszolgáló visszaállítása nem támogatott.

### <a name="subscription-management"></a>Előfizetés-kezelés
- Az előre létrehozott kiszolgálók az előfizetés és az erőforráscsoport között történő dinamikus áthelyezése jelenleg nem támogatott.

### <a name="vnet-service-endpoints"></a>VNet-szolgáltatásvégpontok
- A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.

### <a name="storage-size"></a>Tárterület mérete
- Tekintse meg [a díjszabási](concepts-pricing-tiers.md) szinten a tárterületre vonatkozó korlátozásokat.

## <a name="current-known-issues"></a>Aktuális ismert problémák
- A MariaDB-kiszolgálópéldány a kiszolgáló nem megfelelő verzióját jeleníti meg a kapcsolatok létrehozása után. A megfelelő kiszolgálópéldány-motor verziójának lekéréséhez használja a `select version();` parancsot.

## <a name="next-steps"></a>Következő lépések
- [Az egyes szolgáltatási szinteknél elérhető szolgáltatások](concepts-pricing-tiers.md)
- [Támogatott MariaDB-adatbázis-verziók](concepts-supported-versions.md)
