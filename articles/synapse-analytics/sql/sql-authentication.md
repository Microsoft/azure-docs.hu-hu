---
title: SQL-hitelesítés
description: Az SQL-hitelesítés ismertetése az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 505c0de5a508bd97b10091451116ec3670a20493
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677555"
---
# <a name="sql-authentication"></a>SQL-hitelesítés

Az Azure szinapszis Analytics két SQL-űrlapot tartalmaz – az erőforrás-felhasználás szabályozását lehetővé tevő tényezők. Ez a cikk azt ismerteti, hogy a két forma-tényező hogyan szabályozza a felhasználói hitelesítést.

A szinapszis SQL engedélyezéséhez két engedélyezési típust használhat:

- Azure Active Directory-engedélyezés
- SQL-engedélyezés

Azure Active Directory lehetővé teszi, hogy egyetlen helyet biztosítson a felhasználók felügyeletéhez. Az SQL-hitelesítés lehetővé teszi, hogy az örökölt alkalmazások jól ismert módon használják a szinapszis SQL-t.

## <a name="administrative-accounts"></a>Rendszergazdai fiókok

Kettő rendszergazdaként működő felügyeleti fiók létezik (**Kiszolgálói rendszergazdai** és **Active Directory-rendszergazdai**). Az SQL Serverhez tartozó rendszergazdai fiókok azonosításához nyissa meg a Azure Portal, és keresse meg a szinapszis SQL Tulajdonságok lapját.

![SQL Server-rendszergazdák](./media/sql-authentication/sql-admins.png)

- **Kiszolgálói rendszergazda**

  Az Azure szinapszis Analytics létrehozásakor a **kiszolgáló-rendszergazdai bejelentkezési** nevet kell megadnia. Az SQL-kiszolgáló ekkor létrehozza a fiókot a master adatbázis egyik bejelentkezési neveként. Ez a fiók SQL Server-hitelesítéssel csatlakozik (felhasználónévvel és jelszóval). Ilyen fiókból csak egy létezhet.

- **Azure Active Directory-rendszergazda**

  Egy Azure Active Directory-fiók (különálló vagy biztonságicsoport-fiók) is konfigurálható rendszergazdaként. Az Azure AD-rendszergazda konfigurálása nem kötelező, de az Azure AD **-rendszergazdát be kell** állítani, ha az Azure ad-fiókokat szeretné használni a szinapszis SQL-hez való kapcsolódáshoz.

A **kiszolgálói rendszergazda** és az **Azure ad-rendszergazdai** fiókok jellemzői a következők:

- Az egyetlen olyan fiók, amely képes automatikusan csatlakozni bármely SQL Database a kiszolgálón. (Felhasználói adatbázishoz történő csatlakozáshoz a többi fióknak vagy az adatbázis tulajdonosának kell lennie, vagy felhasználói fiókkal kell rendelkeznie az adatbázisban.)
- Ezek a fiókok `dbo`-felhasználóként lépnek be a felhasználói adatbázisokba, és minden engedéllyel rendelkeznek az adatbázison belül. (A felhasználói adatbázis tulajdonosa szintén `dbo`-felhasználóként jelentkezik be.)
- Ne adja meg az `master` adatbázist `dbo` felhasználóként, és korlátozott engedélyekkel rendelkezzen a főkiszolgálón.
- **Nem** tagjai a standard SQL Server `sysadmin` rögzített kiszolgálói szerepkörnek, amely nem érhető el a SQL Databaseban.  
- Adatbázisok, bejelentkezések, főkiszolgálók és kiszolgálói szintű IP-tűzfalszabályok létrehozására, módosítására és eldobására is képes.
- Tagokat adhat hozzá és távolíthat el a `dbmanager` és a `loginmanager` szerepkörökhöz.
- Megtekintheti a `sys.sql_logins` rendszertáblát.

## <a name="serverless-sql-pool"></a>[Kiszolgáló nélküli SQL-készlet](#tab/serverless)

A kiszolgáló nélküli SQL-készlethez hozzáféréssel rendelkező felhasználók kezeléséhez az alábbi utasításokat használhatja.

A kiszolgáló nélküli SQL-készletbe való bejelentkezéshez használja a következő szintaxist:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
Ha a bejelentkezési azonosító létezik, létrehozhat felhasználókat az egyes adatbázisokban a kiszolgáló nélküli SQL Pool végponton belül, és megadhatja a szükséges engedélyeket ezekhez a felhasználókhoz. A használat létrehozásához a következő szintaxist használhatja:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

A bejelentkezés és a felhasználó létrehozása után a szokásos SQL Server szintaxissal adhat meg jogosultságokat.

## <a name="sql-pool"></a>[SQL-készlet](#tab/provisioned)

### <a name="administrator-access-path"></a>Rendszergazdai hozzáférés elérési útja

Ha a kiszolgálószintű tűzfal megfelelően van konfigurálva, az **SQL Server-rendszergazda** és az **Azure Active Directory-rendszergazda** olyan ügyféleszközökkel csatlakozhat, mint az SQL Server Management Studio vagy az SQL Server Data Tools. Csak a legújabb verziójú eszközök teszik elérhetővé az összes rendelkezésre álló funkciót és képességet. 

Az alábbi ábrán a két rendszergazdai fiók jellemző konfigurációja látható:
 
![a két felügyeleti fiók konfigurálása](./media/sql-authentication/1sql-db-administrator-access.png)

Amikor nyitott portot használ a kiszolgálószintű tűzfalon, a rendszergazdák bármely SQL Database-adatbázishoz csatlakozhatnak.

### <a name="database-creators"></a>Adatbázis-létrehozók

Ezen rendszergazdai szerepkörök egyike a **dbmanager** szerepkör. Ezen szerepkör tagjai létrehozhatnak új adatbázisokat. A szerepkör használatához hozzon létre egy felhasználót az `master` adatbázisban, majd adja hozzá a felhasználót a **dbmanager** adatbázis-szerepkörhöz. 

Adatbázis létrehozásához a felhasználónak egy Azure Active Directory felhasználó alapján egy SQL Server-bejelentkezésen alapuló felhasználónak kell lennie az adatbázisban, vagy tartalmaznia kell az `master` adatbázis felhasználóját.

1. Rendszergazdai fiók használatával kapcsolódjon az `master` adatbázishoz.
2. Hozzon létre egy SQL Server hitelesítési bejelentkezési azonosítót a [create login](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true) utasítás használatával. Mintautasítás:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Használjon erős jelszót a bejelentkezési vagy tartalmazottadatbázis-felhasználó létrehozásakor. További információkért lásd az [erős jelszavak](/sql/relational-databases/security/strong-passwords?view=azure-sqldw-latest&preserve-view=true) létrehozását ismertető cikket.

   A teljesítmény javítása érdekében a bejelentkezéseket (a kiszolgálószintű elsődleges fiókokat) átmenetileg adatbázisszinten is gyorsítótárazza a rendszer. A hitelesítési gyorsítótár frissítésével kapcsolatban lásd a [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?view=azure-sqldw-latest&preserve-view=true) használatát ismertető cikket.

3. Hozzon létre egy adatbázis-felhasználót a [create User](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest&preserve-view=true) utasítás használatával. A felhasználó lehet egy Azure Active Directory hitelesítéssel rendelkező adatbázis-felhasználó (ha konfigurálta az Azure AD-hitelesítéshez szükséges környezetet), vagy egy SQL Server-hitelesítés vagy egy adatbázis-felhasználó, vagy egy SQL Server hitelesítési felhasználó egy SQL Server hitelesítési bejelentkezésen alapuló (az előző lépésben létrehozott) Minta utasítások:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Adja hozzá az új felhasználót a **DBManager** adatbázis-szerepkörhöz a `master` [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest&preserve-view=true) eljárás használatával (vegye figyelembe, hogy az [Alter role](/sql/t-sql/statements/alter-role-transact-sql?view=azure-sqldw-latest&preserve-view=true) utasítás nem támogatott az SQL kiépített verziójában). Mintautasítások:

   ```sql
   EXEC sp_addrolemember 'dbmanager', 'Mary'; 
   EXEC sp_addrolemember 'dbmanager', 'mike@contoso.com]'; 
   ```

   > [!NOTE]
   > A dbmanager egy, a master adatbázisban található adatbázis-szerepkör, így csak adatbázis-felhasználót adhat hozzá a dbmanager szerepkörhöz. Nem adhat kiszolgálószintű bejelentkezést az adatbázisszintű szerepkörökhöz.

5. Szükség esetén konfiguráljon egy tűzfalszabályt úgy, hogy az új felhasználó csatlakozhasson. (Előfordulhat, hogy az új felhasználóra már vonatkozik létező tűzfalszabály.)

Most a felhasználó tud csatlakozni az `master` adatbázishoz, és létrehozhat új adatbázisokat. Az adatbázist létrehozó fiók az adatbázis tulajdonosává válik.

### <a name="login-managers"></a>Bejelentkezéskezelők

A másik rendszergazdai szerepkör a bejelentkezéskezelői szerepkör. Ezen szerepkör tagjai létrehozhatnak új bejelentkezéseket a master adatbázisban. Ha szeretné, elvégezheti ugyanezen lépéseket (bejelentkezést és felhasználót hozhat létre, és a felhasználót a **loginmanager** szerepkörhöz adhatja) annak érdekében, hogy egy felhasználó új bejelentkezéseket hozhasson létre a master adatbázisban. Általában a bejelentkezések nem szükségesek, mivel a Microsoft azt javasolja, hogy a felhasználók a tárolt adatbázis-felhasználókat használják, és így az adatbázis szintjén hitelesítsék a felhasználókat a bejelentkezések alapján. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true) cikkben talál.

---

## <a name="non-administrator-users"></a>Nem rendszergazdai felhasználók

Általában a nem rendszergazdai fiókoknak nincs szükségük a Master adatbázis elérésére. Hozzon létre tartalmazottadatbázis-felhasználókat az adatbázis szintjén a [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) utasítással. 

A felhasználó Azure Active Directory-hitelesítésű tartalmazottadatbázis-felhasználó lehet (ha az Azure AD-hitelesítéshez konfigurálta a környezetét), vagy egy SQL Server-hitelesítésű tartalmazottadatbázis-felhasználó, illetve egy SQL Server-hitelesítésű felhasználó, az SQL Server-hitelesítési bejelentkezéstől függően (amelyet az előző lépésben hozott létre).  

Felhasználók létrehozásához csatlakozzon az adatbázishoz, és hajtson végre a következő példákhoz hasonló utasításokat:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

A kezdetekben csak a rendszergazdák vagy az adatbázis tulajdonosa hozhat létre felhasználókat. Ha további felhasználókat szeretne felhatalmazni új felhasználók létrehozására, adja a kiválasztott felhasználónak az `ALTER ANY USER` engedélyt egy, a következőhöz hasonló utasítással:

```sql
GRANT ALTER ANY USER TO Mary;
```

Ha további felhasználóknak kívánja teljes hozzáférést biztosítani az adatbázishoz, akkor a **db_owner** rögzített adatbázis-szerepkör tagjának kell lennie.

Azure SQL Database vagy szinapszis kiszolgáló nélküli verziójában használja az `ALTER ROLE` utasítást.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

A dedikált SQL-készletben használja az [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest&preserve-view=true).

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> Az adatbázis-felhasználók kiszolgálói bejelentkezésen alapuló létrehozásának egyik gyakori oka, hogy a felhasználóknak több adatbázishoz kell hozzáférnie. Mivel a tárolt adatbázis-felhasználók egyedi entitások, az egyes adatbázisok a saját felhasználóit és a hozzá tartozó jelszavukat is megőrzik. Ez túlterhelést okozhat, mivel a felhasználónak emlékeznie kell minden egyes adatbázishoz tartozó jelszóra, és nem tarthat fenn, ha több jelszót szeretne több adatbázishoz módosítani. Azonban SQL Server bejelentkezések és magas rendelkezésre állás (aktív geo-replikálás és feladatátvételi csoportok) használatakor a SQL Server bejelentkezéseket manuálisan kell beállítani az egyes kiszolgálókon. Ellenkező esetben az adatbázis felhasználója a feladatátvétel után már nem lesz leképezve a kiszolgálói bejelentkezéshez, és nem fog tudni hozzáférni az adatbázishoz a feladatátvétel után. 

A Geo-replikálási bejelentkezések konfigurálásával kapcsolatos további információkért lásd:  [Azure SQL Database biztonsági beállításainak konfigurálása és kezelése geo-visszaállításhoz vagy feladatátvételhez](../../azure-sql/database/active-geo-replication-security-configure.md).

### <a name="configuring-the-database-level-firewall"></a>Adatbázisszintű tűzfal konfigurálása

Ajánlott eljárásként a nem rendszergazdai felhasználóknak csak az általuk használt adatbázisokhoz kell hozzáféréssel rendelkezniük a tűzfalon keresztül. Ahelyett, hogy a kiszolgálószintű tűzfalon keresztül hitelesítené az IP-címüket, és hozzáférést adna nekik az összes adatbázishoz, az [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?view=azure-sqldw-latest&preserve-view=true) utasítással konfigurálja az adatbázisszintű tűzfalat. Az adatbázisszintű tűzfal nem konfigurálható a portálon keresztül.

### <a name="non-administrator-access-path"></a>Nem rendszergazdai hozzáférés elérési útja

Ha az adatbázisszintű tűzfal megfelelően van konfigurálva, az adatbázis felhasználói olyan ügyféleszközökkel csatlakozhatnak, mint az SQL Server Management Studio vagy az SQL Server Data Tools. Csak a legújabb verziójú eszközök teszik elérhetővé az összes rendelkezésre álló funkciót és képességet. Az alábbi ábrán egy tipikus nem rendszergazdai elérési út látható.

![Nem rendszergazdai hozzáférés elérési útja](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Csoportok és szerepkörök

A hatékony hozzáférés-kezelés egyéni bejelentkezési adatok helyett csoportokhoz és szerepkörökhöz rendelt engedélyeket használ.

- Azure Active Directory-hitelesítés használatakor az Azure Active Directory-felhasználókat helyezze egy Azure Active Directory-csoportba. Hozzon létre a csoportban egy adatbázis-felhasználót. Helyezzen egy vagy több adatbázis-felhasználót egy [adatbázis-szerepkörbe](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true), majd rendeljen [engedélyeket](/sql/relational-databases/security/permissions-database-engine?view=azure-sqldw-latest&preserve-view=true) az adatbázis-szerepkörhöz.

- SQL Server-hitelesítés használata esetén hozzon létre tartalmazottadatbázis-felhasználókat az adatbázisban. Helyezzen egy vagy több adatbázis-felhasználót egy [adatbázis-szerepkörbe](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true), majd rendeljen [engedélyeket](/sql/relational-databases/security/permissions-database-engine?view=azure-sqldw-latest&preserve-view=true) az adatbázis-szerepkörhöz.

Az adatbázis-szerepkörök lehetnek beépített szerepkörök, mint például a **db_owner**, a **db_ddladmin**, a **db_datawriter**, a **db_datareader**, a **db_denydatawriter** vagy a **db_denydatareader**. A **db_owner** általában teljes körű engedélyek biztosítására szolgál néhány felhasználó számára. A többi rögzített adatbázis-szerepkör hasznos az egyszerű adatbázisok fejlesztésének gyors elkezdéséhez, de a legtöbb éles környezetbeli adatbázishoz nem ajánlott. 

A **db_datareader** rögzített adatbázis-szerepkör csak olvasási hozzáférést biztosít az adatbázis minden táblájához, ami általában több a feltétlenül szükségesnél. 

Sokkal jobb, ha a [create role](/sql/t-sql/statements/create-role-transact-sql) utasítással saját felhasználó által definiált adatbázis-szerepköröket hoz létre, és minden szerepkört körültekintően biztosít az üzleti igényeknek leginkább megfelelő engedélyekhez. Ha a felhasználó egyszerre több szerepkörnek is tagja, akkor a rendszer összesíti az engedélyeket.

## <a name="permissions"></a>Engedélyek

Az SQL Database-ben több mint 100 engedély adható vagy tagadható meg külön-külön. Ezek közül számos engedély beágyazott. Egy sémában található `UPDATE` engedély például a séma minden táblájára vonatkozó `UPDATE` engedélyt tartalmazza. A legtöbb engedélyrendszerhez hasonlóan az engedély megtagadása felülírja a megadását. 

Az engedélyek beágyazott jellege és száma miatt lehetséges, hogy alapos tervezés szükséges az adatbázis megfelelő védelmét biztosító engedélyrendszer kialakításához. 

Kezdje az [Engedélyek (Adatbázismotor)](/sql/relational-databases/security/permissions-database-engine) szakaszban felsorolt engedélyek listájával, majd tekintse át az engedélyek [poszterméretű ábráját](/sql/relational-databases/security/media/database-engine-permissions.png).

### <a name="considerations-and-restrictions"></a>Megfontolandó szempontok és korlátozások

A SQL Database-beli bejelentkezések és felhasználók kezelésekor vegye figyelembe a következő szempontokat:

- Az utasítások végrehajtásakor csatlakoznia kell a **Master** adatbázishoz `CREATE/ALTER/DROP DATABASE` .
- A **kiszolgáló-rendszergazdai** bejelentkezéshez tartozó adatbázis-felhasználó nem módosítható és nem távolítható el.
- A **kiszolgáló-rendszergazdai** bejelentkezés alapértelmezett nyelve az amerikai angol (US-English).
- Csak a rendszergazdák (**kiszolgáló-rendszergazdai** bejelentkező vagy Azure AD-rendszergazda) és a **master** adatbázis **dbmanager** adatbázis-szerepkörének tagjai rendelkeznek a `CREATE DATABASE` és a `DROP DATABASE` utasítások futtatásához szükséges engedéllyel.
- A `CREATE/ALTER/DROP LOGIN` utasítások futtatásához csatlakoznia kell a master adatbázishoz. A bejelentkezési adatok használata azonban nem javasolt. Helyette használja a tartalmazott adatbázis felhasználóit.
- A felhasználói adatbázishoz történő csatlakozáshoz adja meg a kapcsolati sztringben szereplő adatbázis nevét.
- Csak a kiszolgálószintű fő bejelentkező és a **master adatbázis****loginmanager** adatbázis-szerepkörének tagjai rendelkeznek a `CREATE LOGIN`, `ALTER LOGIN` és `DROP LOGIN` utasítások futtatásához szükséges engedéllyel.
- A `CREATE/ALTER/DROP LOGIN` és `CREATE/ALTER/DROP DATABASE` utasítások egy ADO.NET-alkalmazásban való végrehajtásakor a paraméteres parancsok használata nem engedélyezett. További információkért lásd: [Parancsok és paraméterek](/dotnet/framework/data/adonet/commands-and-parameters).
- A `CREATE/ALTER/DROP DATABASE` és `CREATE/ALTER/DROP LOGIN` utasítások futtatásakor csak az egyes utasítások lehetnek a Transact-SQL kötegben szereplő egyetlen utasítások. Különben hiba történik. Például a következő Transact-SQL utasítás azt ellenőrzi, hogy az adatbázis létezik-e. Ha igen, akkor meghívja a `DROP DATABASE` utasítást az adatbázis eltávolításához. Mivel a `DROP DATABASE` utasítás nem a köteg egyetlen utasítása, a következő Transact-SQL utasítás futtatása hibát eredményez.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Ehelyett használja a következő Transact-SQL-utasítást:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- A `CREATE USER` utasítás `FOR/FROM LOGIN` beállítással történő futtatásakor ez lehet a Transact-SQL kötegben szereplő egyetlen utasítás.
- A `ALTER USER` utasítás `WITH LOGIN` beállítással történő futtatásakor ez lehet a Transact-SQL kötegben szereplő egyetlen utasítás.
- A `CREATE/ALTER/DROP` utasítás használatához a felhasználónak `ALTER ANY USER` engedéllyel kell rendelkeznie az adatbázisban.
- Ha az adatbázis-szerepkör tulajdonosa szeretne hozzáadni vagy eltávolítani egy felhasználót az adott szerepkörből, akkor a következő hiba léphet fel: **A „Név” felhasználó vagy szerepkör nem található ebben az adatbázisban.** Ez a hiba azért fordul elő, mert a felhasználó nem látható a tulajdonos számára. A probléma megoldása érdekében ruházza fel a szerepkör tulajdonosát a `VIEW DEFINITION` engedéllyel. 

## <a name="next-steps"></a>Következő lépések

További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](/sql/relational-databases/security/contained-database-users-making-your-database-portable) cikkben talál.
