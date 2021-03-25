---
title: Gyakori hibák elhárítása – Azure Database for MySQL
description: Ismerje meg, hogy miként lehet elhárítani a gyakori áttelepítési hibákat a Azure Database for MySQL szolgáltatás új felhasználóinak.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: 546f29330b76548ea553cfb7e4e31ac35b19cb1c
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105037546"
---
# <a name="common-errors"></a>Gyakori hibák

A Azure Database for MySQL egy teljes körűen felügyelt szolgáltatás, amelyet a MySQL közösségi verziója működtet. A felügyelt szolgáltatási környezetekben a MySQL-élmény eltérhet a saját környezetében futó MySQL-től. Ebből a cikkből megtudhatja, hogy a felhasználók milyen gyakori hibákba ütköznek a Azure Database for MySQL szolgáltatásba való Migrálás vagy fejlesztés során.

## <a name="common-connection-errors"></a>Gyakori csatlakoztatási hibák

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>HIBA 1184 (08S01): megszakított kapcsolódás 22-adatbázishoz: "db-Name" felhasználó: "user" host: "hostIP" (init_connect parancs sikertelen)
A fenti hiba a sikeres bejelentkezés után következik be, de a munkamenet létrehozásakor minden parancs végrehajtása előtt. A fenti üzenet azt jelzi, hogy helytelen értéket adott meg init_connect Server paraméter értéke, ami miatt a munkamenet inicializálása sikertelen lesz.

Vannak olyan kiszolgálói paraméterek, mint a require_secure_transport, amelyek a munkamenet szintjén nem támogatottak, ezért a paraméterek értékének módosítását a init_connect használatával a 1184-as hiba okozhatja a MySQL-kiszolgálóhoz való csatlakozáskor az alábbi ábrán látható módon.

MySQL> adatbázisok megjelenítése; HIBA 2006 (HY000): a MySQL-kiszolgáló nem lépett fel a kapcsolatok között. Kísérlet az újracsatlakozásra... Kapcsolódási azonosító: 64897 aktuális adatbázis: * * * nincs * * * hiba 1184 (08S01): a megszakított kapcsolódás a 22-es adatbázishoz: "db-Name" felhasználó: "user" host: "hostIP" (init_connect parancs sikertelen)

**Megoldás** : állítsa alaphelyzetbe init_connect értéket a Azure Portal kiszolgálói paraméterek lapján, és csak a támogatott kiszolgálói paramétereket adja meg init_connect paraméter használatával. 


## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>A SUPER Privilege és a DBA szerepkör hiánya miatti hibák

A SUPER Privilege és a DBA szerepkör nem támogatott a szolgáltatásban. Ennek eredményeképpen előfordulhat, hogy az alábbi gyakori hibák merülhetnek fel:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>1419-es hiba: nem rendelkezik a SUPER jogosultsággal, és a bináris naplózás engedélyezve van ( *Előfordulhat* , hogy a kevésbé biztonságos log_bin_trust_function_creators változót szeretné használni)

A fenti hiba akkor fordulhat elő, amikor egy függvényt hoz létre, az alábbi triggert vagy egy sémát importál. A DDL-utasítások, például a CREATE FUNCTION vagy a CREATE TRIGGER a bináris naplóba íródnak, így a másodlagos replika is végrehajthatja azokat. A replika SQL-szála teljes körű jogosultságokkal rendelkezik, amelyek felhasználhatók a jogosultságok kiterjesztésére. Ha ezt a veszélyforrást a bináris naplózást engedélyező kiszolgálókon szeretné védeni, a MySQL-motornak a tárolt függvények létrehozásához a SUPER jogosultsággal kell rendelkeznie, a szokásos CREATE RUTIN jogosultsággal kiegészítve. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Megoldás**: a hiba elhárításához állítsa a portálon a log_bin_trust_function_creators 1 értéket a [kiszolgáló paraméterek](howto-server-parameters.md) paneljére, HAJTsa végre a DDL-utasításokat, vagy importálja a sémát a kívánt objektumok létrehozásához. Továbbra is megtarthatja log_bin_trust_function_creators 1-re, hogy a kiszolgáló elkerülje a hibát a jövőben. Javasoljuk, hogy log_bin_trust_function_creators, mivel a [MySQL közösségi dokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) kiemelt biztonsági kockázat van kiemelve a MySQL-hez készült Azure db for MySQL szolgáltatásban, mivel a bin-napló nem érhető el a fenyegetések ellen.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>1227-es hiba (42000) a következő 101 sorban: hozzáférés megtagadva; Ehhez a művelethez szükség van a SUPER jogosultság (ok) ra (legalább az egyikre). A művelet nem sikerült az 1. ExitCode

A fenti hiba akkor fordulhat elő, amikor egy memóriaképfájl importálása vagy a [definomabb](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html)eljárásokat tartalmazó eljárás létrehozása történik. 

**Megoldás**: a hiba megoldásához a rendszergazda felhasználó jogosultságokat adhat az eljárások létrehozásához vagy végrehajtásához az alábbi példáknak megfelelően:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Azt is megteheti, hogy lecseréli az importálási folyamatot futtató rendszergazda felhasználó nevét az alább látható módon.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>1227-es hiba (42000) a következő 295 sorban: hozzáférés megtagadva; Ehhez a művelethez szükség van a SUPER vagy a SET_USER_ID jogosultság (ok) re.

A fenti hiba akkor fordulhat elő, ha egy memóriaképfájl importálásával vagy parancsfájl futtatásával ellátott CREATE nézetet hoz létre a definomabb utasításokkal. A Azure Database for MySQL nem engedélyezi a SUPER jogosultságokat, vagy nem SET_USER_ID jogosultságot semmilyen felhasználó számára. 

**Megoldás**: 
* Ha lehetséges, használja a definomabb felhasználót a létrehozás nézet végrehajtásához. Valószínű, hogy sok olyan nézet létezik, amely különböző engedélyekkel rendelkezik, így ez nem valósítható meg.  OR
* Szerkessze a memóriaképet, vagy HOZZon létre egy nézet parancsfájlt, és távolítsa el a definomabb = utasítást a memóriakép fájlból, vagy 
* Szerkessze a memóriaképet, vagy HOZZon létre egy VIEW szkriptet, és cserélje le a definomabb értékeket a felhasználóval rendszergazdai engedélyekkel, akik az Importálás vagy a parancsfájl végrehajtása művelettel rendelkeznek.

> [!Tip] 
> A sed vagy a Perl használatával módosítsa a memóriaképfájl vagy az SQL-szkriptet a definomabb = utasítás helyére.

## <a name="common-connection-errors-for-server-admin-login"></a>A kiszolgáló-rendszergazdai bejelentkezés gyakori hálózati hibái

Azure Database for MySQL-kiszolgáló létrehozásakor a kiszolgáló rendszergazdai bejelentkezési adatait a kiszolgáló létrehozásakor kell megadnia. A kiszolgáló-rendszergazdai bejelentkezés lehetővé teszi új adatbázisok létrehozását, új felhasználók hozzáadását és engedélyek megadását. Ha a kiszolgáló-rendszergazdai bejelentkezés törölve lett, az engedélyei visszavonásra kerülnek, vagy megváltoznak a jelszavuk, előfordulhat, hogy a kapcsolatok során megtekintheti az alkalmazás kapcsolódási hibáit. A következő gyakori hibák némelyike

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>HIBA 1045 (28000): hozzáférés megtagadva a felhasználó felhasználóneve @ IP-címe (jelszó használata: igen)

A fenti hiba a következő esetekben fordul elő:

* A Felhasználónév nem létezik
* A felhasználói Felhasználónév törölve
* a jelszavának módosítása vagy alaphelyzetbe állítása.

**Megoldás**: 
* Annak ellenőrzése, hogy a "username" érvényes felhasználóként van-e a kiszolgálón, vagy véletlenül törölve van-e. A következő lekérdezés végrehajtásához jelentkezzen be a Azure Database for MySQL felhasználóba:
  ```sql
  select user from mysql.user;
  ```
* Ha a fenti lekérdezés végrehajtásához nem tud bejelentkezni a MySQL-be, javasoljuk, hogy [Azure Portal használatával állítsa alaphelyzetbe a rendszergazdai jelszót](howto-create-manage-server-portal.md). A jelszó alaphelyzetbe állítása lehetőség a Azure Portal segítségével újból létrehozhatja a felhasználót, alaphelyzetbe állíthatja a jelszót, és visszaállíthatja a rendszergazdai engedélyeket, ami lehetővé teszi a kiszolgáló-rendszergazda használatával történő bejelentkezést és további műveletek elvégzését.

## <a name="next-steps"></a>Következő lépések
Ha nem találta meg a keresett választ, vegye figyelembe a következőket:

- Tegye fel kérdéseit a [Microsoft Q&a kérdéses oldalra](/answers/topics/azure-database-mysql.html) vagy [stack Overflowra](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Küldjön e-mailt a MySQL-hez készült Azure Database for MySQL Team [ @Ask Azure db](mailto:AskAzureDBforMySQL@service.microsoft.com)-nek. Ez az e-mail-cím nem technikai támogatási alias.
- Vegye fel a kapcsolatot az Azure támogatási szolgálatával, és kérjen [jegyet a Azure Portalból](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
