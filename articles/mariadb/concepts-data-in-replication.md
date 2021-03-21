---
title: Adatreplikálás – Azure Database for MariaDB
description: További információ a külső kiszolgálóról a Azure Database for MariaDB szolgáltatásba való szinkronizáláshoz szükséges adatreplikálás használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662627"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Az adatreplikálás Azure Database for MariaDBba

A beérkező adatokra épülő replikáció lehetővé teszi, hogy szinkronizálja egy helyszínen, virtuális gépeken vagy más felhőszolgáltatók által üzemeltetett adatbázis-szolgáltatásokban futó MariaDB-kiszolgáló adatait az Azure Database for MariaDB-szolgáltatásba. A beérkező adatokra épülő replikáció a MariaDB natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A BinLog replikálásával kapcsolatos további tudnivalókért tekintse meg a [BinLog-replikáció áttekintése](https://mariadb.com/kb/en/library/replication-overview/)című témakört.

## <a name="when-to-use-data-in-replication"></a>Mikor kell használni a felhőbe irányuló replikálás
A felhőbe irányuló replikálás használatának főbb forgatókönyvei:

- **Hibrid adatszinkronizálás:** A felhőbe irányuló replikálás segítségével megtarthatja a helyszíni kiszolgálók és a Azure Database for MariaDB között szinkronizált adatokat. Ez a szinkronizálás a hibrid alkalmazások létrehozásához hasznos. Ez a módszer akkor fordul elő, ha egy meglévő helyi adatbázis-kiszolgálóval rendelkezik, de az adott régióban szeretné áthelyezni az információkat a végfelhasználók számára.
- **Több felhős szinkronizálás:** Összetett felhőalapú megoldások esetén a felhőbe irányuló replikálás segítségével szinkronizálhat adatokat Azure Database for MariaDB és különböző felhőalapú szolgáltatók között, beleértve a felhőben üzemeltetett virtuális gépeket és adatbázis-szolgáltatásokat.

## <a name="limitations-and-considerations"></a>Korlátozások és szempontok

### <a name="data-not-replicated"></a>Nem replikált adatértékek
A forráskiszolgáló [*MySQL rendszeradatbázisa*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) nem replikálódik. A rendszer nem replikálja a fiókok és engedélyek módosításait a forráskiszolgálón. Ha létrehoz egy fiókot a forráskiszolgálón, és ennek a fióknak el kell érnie a másodpéldány-kiszolgálót, akkor manuálisan hozza létre ugyanazt a fiókot a replika-kiszolgáló oldalán. A rendszeradatbázisban található táblák megismeréséhez tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Követelmények
- A forráskiszolgáló verziójának legalább MariaDB 10,2-es verziójúnak kell lennie.
- A forrás-és a replika-kiszolgáló verziószámának azonosnak kell lennie. Például mindkettőnek a 10,2-es verzió MariaDB kell lennie.
- Minden táblának rendelkeznie kell egy elsődleges kulccsal.
- A forráskiszolgálón a InnoDB motort kell használnia.
- A felhasználónak rendelkeznie kell engedéllyel a bináris naplózás konfigurálásához és új felhasználók létrehozásához a forráskiszolgálón.
- Ha a forráskiszolgálón engedélyezve van az SSL, ellenőrizze, hogy a tartományhoz megadott SSL HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány szerepel-e a `mariadb.az_replication_change_master` tárolt eljárásban. Tekintse át az alábbi [példákat](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) és a `master_ssl_ca` paramétert.
- Győződjön meg arról, hogy a forráskiszolgáló IP-címe hozzá lett adva az Azure Database for MariaDB-replika kiszolgálói tűzfalszabályok számára. A tűzfalszabályokat az [Azure Portallal](howto-manage-firewall-portal.md) vagy az [Azure CLI-vel](howto-manage-firewall-cli.md) frissítheti.
- Győződjön meg arról, hogy a forráskiszolgáló üzemeltetése lehetővé teszi a bejövő és a kimenő forgalmat is a 3306-es porton.
- Győződjön meg arról, hogy a forráskiszolgáló **nyilvános IP-címmel** rendelkezik, a DNS nyilvánosan elérhető, vagy rendelkezik teljes tartománynévvel (FQDN).

### <a name="other"></a>Egyéb
- Az adatreplikálás csak általános célú és a memória optimalizált díjszabási szintjein támogatott.

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [állíthatja be az adatreplikációt](howto-data-in-replication.md).
