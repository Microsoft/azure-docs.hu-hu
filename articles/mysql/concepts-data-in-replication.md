---
title: Adatreplikálás – Azure Database for MySQL
description: További információ a külső kiszolgálóról a Azure Database for MySQL szolgáltatásba való szinkronizáláshoz szükséges adatreplikálás használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: ab2433bfa4df3d75f10bc9128dc736ff6d12be76
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210516"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Az adatreplikálás Azure Database for MySQLba

Felhőbe irányuló replikálás lehetővé teszi, hogy szinkronizálja az adatokat egy külső MySQL-kiszolgálóról a Azure Database for MySQL szolgáltatásba. A külső kiszolgáló lehet helyszíni, virtuális gépek vagy más felhőalapú szolgáltatók által üzemeltetett adatbázis-szolgáltatás. A felhőbe irányuló replikálás a MySQL-re épülő bináris log (BinLog) fájl-vagy gtid-alapú replikáción alapul. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört. 

## <a name="when-to-use-data-in-replication"></a>Mikor kell használni a felhőbe irányuló replikálás
A felhőbe irányuló replikálás használatának főbb forgatókönyvei:

- **Hibrid adatszinkronizálás:** A felhőbe irányuló replikálás segítségével megtarthatja a helyszíni kiszolgálók és a Azure Database for MySQL között szinkronizált adatokat. Ez a szinkronizálás a hibrid alkalmazások létrehozásához hasznos. Ez a módszer akkor fordul elő, ha egy meglévő helyi adatbázis-kiszolgálóval rendelkezik, de az adott régióban szeretné áthelyezni az információkat a végfelhasználók számára.
- **Több felhős szinkronizálás:** Összetett felhőalapú megoldások esetén a felhőbe irányuló replikálás segítségével szinkronizálhat adatokat Azure Database for MySQL és különböző felhőalapú szolgáltatók között, beleértve a felhőben üzemeltetett virtuális gépeket és adatbázis-szolgáltatásokat.
 
Áttelepítési forgatókönyvek esetén használja a [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Korlátozások és szempontok

### <a name="data-not-replicated"></a>Nem replikált adatértékek
A forráskiszolgáló [*MySQL rendszeradatbázisa*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) nem replikálódik. A rendszer nem replikálja a fiókok és engedélyek módosításait a forráskiszolgálón. Ha létrehoz egy fiókot a forráskiszolgálón, és ennek a fióknak el kell érnie a másodpéldány-kiszolgálót, akkor manuálisan hozza létre ugyanazt a fiókot a replika-kiszolgáló oldalán. A rendszeradatbázisban található táblák megismeréséhez tekintse meg a [MySQL-kézikönyvet](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Szűrés
Ha ki szeretné hagyni a (helyszíni, virtuális gépeken tárolt vagy más felhőalapú szolgáltatók által üzemeltetett adatbázis-szolgáltatás) tábláinak replikálását, `replicate_wild_ignore_table` akkor a paraméter támogatott. Ha szükséges, frissítse ezt a paramétert az Azure-ban üzemeltetett replika-kiszolgálón a [Azure Portal](howto-server-parameters.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával.

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) .

## <a name="supported-in-general-purpose-or-memory-optimized-tier-only"></a>Csak általános célú vagy csak a memória optimalizált szintjein támogatott
Az adatreplikálás csak általános célú és a memória optimalizált díjszabási szintjein támogatott.

### <a name="requirements"></a>Követelmények
- A forráskiszolgáló verziójának legalább a MySQL 5,6-es verziójának kell lennie. 
- A forrás-és a replika-kiszolgáló verziószámának azonosnak kell lennie. Például mindkettőnek a MySQL 5,6-es vagy újabb verziójúnak kell lennie a MySQL 5,7-es verziójának.
- Minden táblának rendelkeznie kell egy elsődleges kulccsal.
- A forráskiszolgálón a MySQL InnoDB motort kell használnia.
- A felhasználónak rendelkeznie kell engedéllyel a bináris naplózás konfigurálásához és új felhasználók létrehozásához a forráskiszolgálón.
- Ha a forráskiszolgálón engedélyezve van az SSL, ellenőrizze, hogy a tartományhoz megadott SSL HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány szerepel-e a `mysql.az_replication_change_master` vagy `mysql.az_replication_change_master_with_gtid` tárolt eljárásban. Tekintse át az alábbi [példákat](./howto-data-in-replication.md#4-link-source-and-replica-servers-to-start-data-in-replication) és a `master_ssl_ca` paramétert.
- Győződjön meg arról, hogy a forráskiszolgáló IP-címe hozzá lett adva az Azure Database for MySQL-replika kiszolgálói tűzfalszabályok számára. A tűzfalszabályokat az [Azure Portallal](./howto-manage-firewall-using-portal.md) vagy az [Azure CLI-vel](./howto-manage-firewall-using-cli.md) frissítheti.
- Győződjön meg arról, hogy a forráskiszolgáló üzemeltetése lehetővé teszi a bejövő és a kimenő forgalmat is a 3306-es porton.
- Győződjön meg arról, hogy a forráskiszolgáló **nyilvános IP-címmel** rendelkezik, a DNS nyilvánosan elérhető, vagy rendelkezik teljes tartománynévvel (FQDN).


## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan [állíthatja be az adatreplikációt](howto-data-in-replication.md)
- Tudnivalók [Az Azure-beli replikálásról olvasási replikákkal](concepts-read-replicas.md)
- Az [adatáttelepítés minimális állásidővel való áttelepítésének ismertetése a DMS használatával](howto-migrate-online.md)
