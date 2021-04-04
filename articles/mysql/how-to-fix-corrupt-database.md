---
title: Adatbázis-sérülés feloldása – Azure Database for MySQL
description: Ebből a cikkből megtudhatja, hogyan javíthatja az adatbázis-sérülési problémákat a Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766897"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Az adatbázis-sérüléssel kapcsolatos hibák elhárítása az Azure Database for MySQL-ben
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Az adatbázis-sérülés az alkalmazás leállását okozhatja. Fontos, hogy az adatvesztés elkerülése érdekében időben hárítsa el a korrupciós problémákat. Az adatbázis sérülése esetén a következő hibaüzenet jelenik meg a kiszolgáló naplófájljaiban: `InnoDB: Database page corruption on disk or a failed.`

Ebből a cikkből megtudhatja, hogyan oldható meg az adatbázis vagy a tábla sérülési problémái. Azure Database for MySQL a InnoDB motort használja. A szolgáltatás automatikus sérülés-ellenőrzési és javítási műveleteket végez. A InnoDB az összes beolvasott oldalon ellenőrzőösszegek futtatásával ellenőrzi a sérült lapokat. Ha egy ellenőrzőösszeg-különbözetet talál, akkor a automatikusan leállítja a MySQL-kiszolgálót.

Az alábbi beállításokkal gyorsan csökkentheti az adatbázis-sérüléssel kapcsolatos problémákat.

## <a name="restart-your-mysql-server"></a>A MySQL-kiszolgáló újraindítása

Az adatbázis vagy a tábla általában sérült, amikor az alkalmazás hozzáfér a táblához vagy az adatbázishoz. A InnoDB olyan összeomlás-helyreállítási mechanizmust tartalmaz, amely a kiszolgáló újraindításakor a legtöbb problémát képes megoldani. A kiszolgáló újraindítása segítheti a kiszolgáló helyreállítását egy olyan összeomlásból, amely miatt az adatbázis helytelen állapotban volt.

## <a name="use-the-dump-and-restore-method"></a>A dump és a Restore metódus használata

Azt javasoljuk, hogy a sérüléssel kapcsolatos problémákat a *memóriakép és visszaállítás* módszer használatával oldja fel. Ez a módszer a következőket foglalja magában:
1. A sérült tábla elérése.
1. A mysqldump segédprogrammal hozzon létre egy logikai biztonsági másolatot a táblából. A biztonsági mentés megőrzi a tábla szerkezetét és az abban belüli adatmennyiséget.
1. A tábla újbóli betöltése az adatbázisba.

### <a name="back-up-your-database-or-tables"></a>Az adatbázis vagy a táblák biztonsági mentése

> [!Important]
> - Győződjön meg arról, hogy konfigurált egy tűzfalszabályet a kiszolgáló ügyfélszámítógépről való eléréséhez. További információ: [Tűzfalszabályok konfigurálása egyetlen kiszolgálón](howto-manage-firewall-using-portal.md) és [Tűzfalszabály konfigurálása rugalmas kiszolgálón](flexible-server/how-to-connect-tls-ssl.md).
> - `--ssl-cert`Ha engedélyezve van az SSL, használja az SSL-beállítást a mysqldump.

Hozzon létre egy biztonságimásolat-fájlt a parancssorból a mysqldump használatával. Használja a következő parancsot:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Paraméterek leírása:
- `[ssl-cert=/path/to/pem]`: Az SSL-tanúsítvány elérési útja. Töltse le az SSL-tanúsítványt az ügyfélszámítógépen, és állítsa be az elérési utat a parancsban. Ne használja ezt a paramétert, ha az SSL le van tiltva.
- `[host]`: Az Azure Database for MySQL-kiszolgáló.
- `[uname]`: A kiszolgáló rendszergazdájának felhasználóneve.
- `[pass]`: A rendszergazdai felhasználó jelszava.
- `[dbname]`: Az adatbázis neve.
- `[backupfile.sql]`: Az adatbázis biztonsági másolatának fájlneve.

> [!Important]
> - Egyetlen kiszolgáló esetén használja a `admin-user@servername` `myserveradmin` következő parancsokban szereplő formátumot.
> - Rugalmas kiszolgáló esetén a `admin-user` következő parancsokban cserélje le a formátumot `myserveradmin` .

Ha egy adott tábla sérült, válassza ki a megfelelő táblákat az adatbázisban a biztonsági mentéshez:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Egy vagy több adatbázis biztonsági mentéséhez használja a `--database` kapcsolót, és sorolja fel az adatbázisok nevét szóközzel elválasztva:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Adatbázis vagy táblák visszaállítása

Az alábbi lépések bemutatják, hogyan állíthatja vissza az adatbázist vagy a táblákat. A biztonságimásolat-fájl létrehozása után visszaállíthatja a táblákat vagy adatbázisokat a MySQL segédprogram használatával. Futtassa ezt a parancsot:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Íme egy példa, amely a `testdb` mysqldump-mel létrehozott biztonságimásolat-fájlból áll vissza: 

> [!Important]
> - Egyetlen kiszolgáló esetén a `admin-user@servername` következő parancsban cserélje le a formátumot `myserveradmin` .
> - Rugalmas kiszolgáló esetén a ```admin-user``` következő parancsban cserélje le a formátumot `myserveradmin` . 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Következő lépések
Ha a fenti lépések nem oldják meg a problémát, bármikor visszaállíthatja a teljes kiszolgálót:
- [Kiszolgáló visszaállítása Azure Database for MySQL – egyetlen kiszolgáló](howto-restore-server-portal.md)
- [Kiszolgáló visszaállítása Azure Database for MySQL – rugalmas kiszolgáló](flexible-server/how-to-restore-server-portal.md)



