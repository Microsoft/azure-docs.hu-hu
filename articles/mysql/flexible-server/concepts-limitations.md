---
title: Korlátozások – Azure Database for MySQL – rugalmas kiszolgáló
description: Ez a cikk Azure Database for MySQL rugalmas kiszolgáló korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 48aef337326d58b2a503dc48862571efde0d37ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034520"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL-rugalmas kiszolgáló korlátozásai (előzetes verzió)

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk a Azure Database for MySQL rugalmas kiszolgálói szolgáltatás korlátozásait ismerteti. A MySQL-adatbázismotor [általános korlátai](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) is érvényesek. Ha szeretne többet megtudni az erőforrásról (számítási, memória-, tárolási) szintjeiről, tekintse meg a [számítási és tárolási](concepts-compute-storage.md) cikkeket.

## <a name="server-parameters"></a>Kiszolgálóparaméterek

> [!NOTE]
> Ha a (z) és a (z) kiszolgáló paramétereinek minimális/maximális értékeit keresi `max_connections` `innodb_buffer_pool_size` , akkor ezek az adatok a kiszolgálói paraméterek fogalmakat [kiszolgáló paraméterei](./concepts-server-parameters.md) című cikkbe kerültek.

Azure Database for MySQL támogatja a kiszolgálói paraméterek értékének finomhangolását. Néhány paraméter minimális és maximális értéke (pl. `max_connections`, `join_buffer_size` , `query_cache_size` ) a számítási és a kiszolgáló számítási mérete határozza meg. A korlátokkal kapcsolatos további információkért tekintse meg a [kiszolgálói paramétereket](./concepts-server-parameters.md) .

A szolgáltatás nem támogatja a jelszavakat, például a "validate_password" és a "caching_sha2_password".

## <a name="storage-engines"></a>Storage-motorok

A MySQL számos tárolási motort támogat. Azure Database for MySQL rugalmas kiszolgálón a következő tárolási motorok támogatottak és nem támogatottak:

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

### <a name="supported"></a>Támogatott
- `LOAD DATA INFILE` támogatott, de a `[LOCAL]` paramétert meg kell adni, és egy UNC elérési útra kell irányítani (az Azure Storage csatlakoztatva van az SMB protokollon keresztül).

## <a name="functional-limitations"></a>Funkcionális korlátozások

### <a name="zone-redundant-ha"></a>Zóna redundáns HA
- Ezt a konfigurációt csak a kiszolgáló létrehozása során lehet beállítani.
- A feltört számítási szint nem támogatja.

### <a name="networking"></a>Hálózatkezelés
- A kapcsolódási módszer nem módosítható a kiszolgáló létrehozása után. Ha a kiszolgáló *magánhálózati hozzáféréssel (VNet-integrációval)* jön létre, a létrehozás után nem módosítható *nyilvános hozzáférésre (engedélyezett IP-címekre)* , és fordítva
- A TLS/SSL alapértelmezés szerint engedélyezve van, és nem tiltható le.
- A kiszolgáló által támogatott minimális TLS-verzió a TLS 1.2. További információért tekintse meg a [Kapcsolódás a TLS/SSL használatával](./how-to-connect-tls-ssl.md) című témakört.

### <a name="stopstart-operation"></a>Leállítás/indítás művelet
- Nem támogatott a zónában redundáns HA konfigurációk (elsődleges és készenléti).
- Olvasási replika-konfigurációk (forrás-és replikák) esetén nem támogatott.

### <a name="scale-operations"></a>Skálázási műveletek
- A kiszolgáló tárterületének csökkentése nem támogatott.

### <a name="read-replicas"></a>Olvasási replikák
- Nem támogatott a zónában redundáns HA konfigurációk (elsődleges és készenléti).

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítése
- A fő adatbázismotor-verziók közötti automatikus áttelepítés nem támogatott. Ha szeretné frissíteni a főverziót, hozzon létre egy [memóriaképet, és állítsa vissza](../concepts-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

### <a name="restoring-a-server"></a>Kiszolgáló visszaállítása
- Az időponthoz tartozó visszaállítással a rendszer az új kiszolgálókat ugyanazzal a számítási és tárolási konfigurációval hozza létre, mint a forráskiszolgálón. Az újonnan visszaállított kiszolgáló számítási felskálázása a kiszolgáló létrehozása után is lehetséges.
- A törölt kiszolgáló visszaállítása nem támogatott.

## <a name="features-available-in-single-server-but-not-yet-supported-in-flexible-server"></a>Az egykiszolgálós és a rugalmas kiszolgáló által még nem támogatott szolgáltatások 
A Azure Database for MySQL egyetlen kiszolgálón elérhető összes funkció még nem érhető el a rugalmas kiszolgálókon. Az egykiszolgálós és a rugalmas kiszolgálók közötti szolgáltatások összehasonlításának teljes listájáért tekintse [meg a megfelelő MySQL-kiszolgáló beállítását az Azure dokumentációjában.](../select-right-deployment-type.md#comparing-the-mysql-deployment-options-in-azure)

## <a name="next-steps"></a>Következő lépések

- [A megfelelő MySQL-kiszolgáló beállításának megismerése az Azure dokumentációjában](../select-right-deployment-type.md)
- [A rugalmas kiszolgálók számítási és tárolási lehetőségeinek](concepts-compute-storage.md) megismerése
- A [támogatott MySQL-verziók](concepts-supported-versions.md) ismertetése
- Gyors útmutató: [Azure Database for MySQL rugalmas kiszolgáló létrehozásához használja a Azure Portal](quickstart-create-server-portal.md)
