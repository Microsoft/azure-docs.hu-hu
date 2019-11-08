---
title: Bejelentkezések és felhasználók
description: Ismerje meg a SQL Database és SQL Data Warehouse biztonsági felügyeletet, pontosabban hogyan kezelheti az adatbázis-hozzáférést és a bejelentkezési biztonságot a kiszolgálói szintű rendszerbiztonsági fiók használatával.
keywords: sql database biztonság,adatbázis biztonságának felügyelete,bejelentkezési biztonság,adatbázis biztonsága,adatbázis-hozzáférés
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/26/2019
ms.openlocfilehash: a33c653199ae21b551acadfb4503eae029ddc5e4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822831"
---
# <a name="controlling-and-granting-database-access-to-sql-database-and-sql-data-warehouse"></a>Adatbázis-hozzáférés szabályozása és biztosítása SQL Database és SQL Data Warehouse számára

A tűzfalszabályok konfigurálása után kapcsolódhat az Azure [SQL Databasehoz](sql-database-technical-overview.md) , és [SQL Data Warehousehatja](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) a rendszergazdai fiókok egyikét, az adatbázis tulajdonosát vagy adatbázis-felhasználóként.  

> [!NOTE]  
> Ez a témakör az Azure SQL Serverre vonatkozik, valamint az Azure SQL Serveren létrehozott adatbázisok SQL Database és SQL Data Warehouse. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database. 
> [!TIP]
> Oktatóanyagért lásd: [a Azure SQL Database biztonságossá tétele](sql-database-security-tutorial.md). Ez az oktatóanyag nem vonatkozik **Azure SQL Database felügyelt példányra**.

## <a name="unrestricted-administrative-accounts"></a>Nem korlátozott rendszergazdai fiókok

Kettő rendszergazdaként működő felügyeleti fiók létezik (**Kiszolgálói rendszergazdai** és **Active Directory-rendszergazdai**). Az SQL Serverhez tartozó rendszergazdai fiókok azonosításához nyissa meg a Azure Portal, és keresse meg az SQL Server vagy SQL Database Tulajdonságok lapját.

![SQL Server-rendszergazdák](media/sql-database-manage-logins/sql-admins.png)

- **Kiszolgáló rendszergazdája**

  Azure SQL Server-kiszolgáló létrehozásakor ki kell jelölnie egy **kiszolgáló-rendszergazdai felhasználónevet**. Az SQL-kiszolgáló ekkor létrehozza a fiókot a master adatbázis egyik bejelentkezési neveként. Ez a fiók SQL Server-hitelesítéssel csatlakozik (felhasználónévvel és jelszóval). Ilyen fiókból csak egy létezhet.

  > [!NOTE]
  > A kiszolgáló-rendszergazda jelszavának alaphelyzetbe állításához lépjen a [Azure Portalra](https://portal.azure.com), kattintson az **SQL-kiszolgálók**elemre, válassza ki a kiszolgálót a listából, majd kattintson a **jelszó alaphelyzetbe állítása**elemre.

- **Azure Active Directory-rendszergazda**

  Egy Azure Active Directory-fiók (különálló vagy biztonságicsoport-fiók) is konfigurálható rendszergazdaként. Az Azure AD-rendszergazda konfigurálása nem kötelező, de az Azure AD **-rendszergazdát úgy kell** konfigurálni, hogy az Azure ad-fiókokkal kapcsolódjon SQL Databasehoz. Az Azure Active Directory hozzáférésének konfigurálásáról további információért lásd [az SQL Database-hez vagy az SQL Data Warehouse-hoz az Azure Active Directory-hitelesítéssel történő csatlakozást](sql-database-aad-authentication.md), illetve [az Azure AD MFA és az SQL Database, valamint az SQL Data Warehouse együttes támogatását](sql-database-ssms-mfa-authentication.md) ismertető cikket.

A **kiszolgálói rendszergazda** és az **Azure ad-rendszergazdai** fiókok jellemzői a következők:

- Az egyetlen olyan fiók, amely képes automatikusan csatlakozni bármely SQL Database a kiszolgálón. (Felhasználói adatbázishoz történő csatlakozáshoz a többi fióknak vagy az adatbázis tulajdonosának kell lennie, vagy felhasználói fiókkal kell rendelkeznie az adatbázisban.)
- Ezek a fiókok `dbo`-felhasználóként lépnek be a felhasználói adatbázisokba, és minden engedéllyel rendelkeznek az adatbázison belül. (A felhasználói adatbázis tulajdonosa szintén `dbo`-felhasználóként jelentkezik be.) 
- Ne adja meg a `master`-adatbázist `dbo` felhasználóként, és korlátozott engedélyekkel rendelkezzen a főkiszolgálón. 
- **Nem** tagjai a standard SQL Server `sysadmin` rögzített kiszolgálói szerepkörnek, amely nem érhető el az SQL Database-ben.  
- Adatbázisok, bejelentkezések, főkiszolgálók és kiszolgálói szintű IP-tűzfalszabályok létrehozására, módosítására és eldobására is képes.
- Tagokat adhat hozzá és távolíthat el a `dbmanager` és `loginmanager` szerepkörökhöz.
- Megtekintheti a `sys.sql_logins` rendszertáblát.

### <a name="configuring-the-firewall"></a>A tűzfal konfigurálása

Ha a kiszolgálószintű tűzfal egy önálló IP-címhez vagy -tartományhoz van konfigurálva, az **SQL Server-rendszergazda** és az **Azure Active Directory-rendszergazda** a master adatbázishoz és az összes felhasználói adatbázishoz csatlakozhat. A kezdeti kiszolgálószintű tűzfal az [Azure Portalon](sql-database-single-database-get-started.md) konfigurálható a [PowerShell](sql-database-powershell-samples.md) vagy a [REST API](https://msdn.microsoft.com/library/azure/dn505712.aspx) segítségével. A kapcsolatok létrejötte után a további kiszolgálói szintű IP-tűzfalszabályok a [Transact-SQL](sql-database-configure-firewall-settings.md)használatával is konfigurálhatók.

### <a name="administrator-access-path"></a>Rendszergazdai hozzáférés elérési útja

Ha a kiszolgálószintű tűzfal megfelelően van konfigurálva, az **SQL Server-rendszergazda** és az **Azure Active Directory-rendszergazda** olyan ügyféleszközökkel csatlakozhat, mint az SQL Server Management Studio vagy az SQL Server Data Tools. Csak a legújabb verziójú eszközök teszik elérhetővé az összes rendelkezésre álló funkciót és képességet. Az alábbi ábrán a két rendszergazdai fiók tipikus konfigurációja látható.

![a két felügyeleti fiók konfigurálása](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Amikor nyitott portot használ a kiszolgálószintű tűzfalon, a rendszergazdák bármely SQL Database-adatbázishoz csatlakozhatnak.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Csatlakozás egy adatbázishoz az SQL Server Management Studióval

A kiszolgálók, az adatbázisok, a kiszolgálói szintű IP-tűzfalszabályok és a SQL Server Management Studio az adatbázisok lekérdezéséhez való létrehozásával kapcsolatban lásd: Ismerkedés a Azure SQL Database- [kiszolgálók,-adatbázisok és-tűzfalszabályok használatával a Azure Portal és az SQL segítségével Kiszolgáló Management Studio](sql-database-single-database-get-started.md).

> [!IMPORTANT]
> Javasoljuk, hogy mindig a Management Studio legfrissebb verzióját használja, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="additional-server-level-administrative-roles"></a>További kiszolgálószintű rendszergazdai szerepkörök

>[!IMPORTANT]
>Ez a szakasz nem vonatkozik **Azure SQL Database felügyelt példányra** , mivel ezek a szerepkörök **Azure SQL Databasera**vonatkoznak.

A korábban már tárgyalt kiszolgálószintű rendszergazdai szerepkörökön kívül az SQL Database két korlátozott rendszergazdai szerepkört tesz elérhetővé a master adatbázisban, amelyekhez felhasználói fiókok adhatók, és amelyek hozzáférést biztosítanak adatbázisok létrehozásához vagy bejelentkezések kezeléséhez.

### <a name="database-creators"></a>Adatbázis-létrehozók

Ezen rendszergazdai szerepkörök egyike a **dbmanager** szerepkör. Ezen szerepkör tagjai létrehozhatnak új adatbázisokat. A szerepkör használatához hozzon létre egy felhasználót az `master` adatbázisban, majd adja hozzá a felhasználót a **dbmanager** adatbázis-szerepkörhöz. Egy adatbázis létrehozásához a felhasználónak a `master`-adatbázisban SQL Server bejelentkezési azonosítóval kell rendelkeznie, vagy Azure Active Directory felhasználón alapuló adatbázis-felhasználónak kell lennie.

1. Rendszergazdai fiókkal kapcsolódjon a `master`-adatbázishoz.
2. Hozzon létre egy SQL Server hitelesítési bejelentkezési azonosítót a [create login](https://msdn.microsoft.com/library/ms189751.aspx) utasítás használatával. Mintautasítás:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Használjon erős jelszót a bejelentkezési vagy tartalmazottadatbázis-felhasználó létrehozásakor. További információkért lásd az [erős jelszavak](https://msdn.microsoft.com/library/ms161962.aspx) létrehozását ismertető cikket.

   A teljesítmény javítása érdekében a bejelentkezéseket (a kiszolgálószintű elsődleges fiókokat) átmenetileg adatbázisszinten is gyorsítótárazza a rendszer. A hitelesítési gyorsítótár frissítésével kapcsolatban lásd a [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx) használatát ismertető cikket.

3. A `master` adatbázisban hozzon létre egy felhasználót a [create User](https://msdn.microsoft.com/library/ms173463.aspx) utasítás használatával. A felhasználó lehet egy Azure Active Directory hitelesítéssel rendelkező adatbázis-felhasználó (ha konfigurálta a környezetet az Azure AD-hitelesítéshez), vagy egy SQL Server-hitelesítés vagy egy adatbázis-felhasználó, vagy egy SQL Server hitelesítési felhasználó egy SQL Server alapján hitelesítési bejelentkezés (az előző lépésben létrehozott) Minta utasítások:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Adja hozzá az új felhasználót a **DBManager** adatbázis-szerepkörhöz `master` az [Alter role](https://msdn.microsoft.com/library/ms189775.aspx) utasítás használatával. Mintautasítások:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > A dbmanager egy, a master adatbázisban található adatbázis-szerepkör, így csak adatbázis-felhasználót adhat hozzá a dbmanager szerepkörhöz. Nem adhat kiszolgálószintű bejelentkezést az adatbázisszintű szerepkörökhöz.

5. Szükség esetén konfiguráljon egy tűzfalszabályt úgy, hogy az új felhasználó csatlakozhasson. (Előfordulhat, hogy az új felhasználóra már vonatkozik létező tűzfalszabály.)

Most a felhasználó csatlakozhat a `master` adatbázishoz, és létrehozhat új adatbázisokat. Az adatbázist létrehozó fiók az adatbázis tulajdonosává válik.

### <a name="login-managers"></a>Bejelentkezéskezelők

A másik rendszergazdai szerepkör a bejelentkezéskezelői szerepkör. Ezen szerepkör tagjai létrehozhatnak új bejelentkezéseket a master adatbázisban. Ha szeretné, elvégezheti ugyanezen lépéseket (bejelentkezést és felhasználót hozhat létre, és a felhasználót a **loginmanager** szerepkörhöz adhatja) annak érdekében, hogy egy felhasználó új bejelentkezéseket hozhasson létre a master adatbázisban. Bejelentkezésre általában nincs szükség, mivel a Microsoft tartalmazottadatbázis-felhasználók használatát javasolja, amelyek az adatbázis szintjén hitelesíthetők a bejelentkezéseken alapuló felhasználók használata helyett. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://msdn.microsoft.com/library/ff929188.aspx) cikkben talál.

## <a name="non-administrator-users"></a>Nem rendszergazdai felhasználók

Általában a nem rendszergazdai fiókoknak nincs szükségük a master adatbázis elérésére. Hozzon létre tartalmazottadatbázis-felhasználókat az adatbázis szintjén a [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) utasítással. A felhasználó lehet egy Azure Active Directory hitelesítéssel rendelkező adatbázis-felhasználó (ha konfigurálta a környezetet az Azure AD-hitelesítéshez), vagy egy SQL Server-hitelesítés vagy egy adatbázis-felhasználó, vagy egy SQL Server hitelesítési felhasználó egy SQL Server alapján hitelesítési bejelentkezés (az előző lépésben létrehozott) További információ: [tárolt adatbázis-felhasználók – az adatbázis hordozhatóvé tétele](https://msdn.microsoft.com/library/ff929188.aspx). 

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

Azure SQL Database használja a `ALTER ROLE` utasítást.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Azure SQL Data Warehouse használja az [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).
```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```


> [!NOTE]
> Az adatbázis-felhasználók SQL Database-kiszolgáló bejelentkezési alapján való létrehozásának egyik gyakori oka, hogy olyan felhasználók számára van szükség, akiknek több adatbázishoz kell hozzáférnie. Mivel a tárolt adatbázis-felhasználók egyedi entitások, az egyes adatbázisok a saját felhasználóit és a hozzá tartozó jelszavukat is megőrzik. Ez túlterhelést okozhat, mivel a felhasználónak emlékeznie kell minden egyes adatbázishoz tartozó jelszóra, és nem tarthat fenn, ha több jelszót szeretne több adatbázishoz módosítani. Azonban SQL Server bejelentkezések és magas rendelkezésre állás (aktív geo-replikálás és feladatátvételi csoportok) használatakor a SQL Server bejelentkezéseket manuálisan kell beállítani az egyes kiszolgálókon. Ellenkező esetben az adatbázis felhasználója a feladatátvétel után már nem lesz leképezve a kiszolgálói bejelentkezéshez, és nem fog tudni hozzáférni az adatbázishoz a feladatátvétel után. A Geo-replikálási bejelentkezések konfigurálásával kapcsolatos további információkért lásd: [Azure SQL Database biztonsági beállításainak konfigurálása és kezelése a Geo-visszaállításhoz vagy a feladatátvételhez](sql-database-geo-replication-security-config.md).

### <a name="configuring-the-database-level-firewall"></a>Adatbázisszintű tűzfal konfigurálása

Ajánlott eljárásként a nem rendszergazdai felhasználóknak csak az általuk használt adatbázisokhoz kell hozzáféréssel rendelkezniük a tűzfalon keresztül. Ahelyett, hogy a kiszolgálószintű tűzfalon keresztül hitelesítené az IP-címüket, és hozzáférést adna nekik az összes adatbázishoz, az [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) utasítással konfigurálja az adatbázisszintű tűzfalat. Az adatbázisszintű tűzfal nem konfigurálható a portálon keresztül.

### <a name="non-administrator-access-path"></a>Nem rendszergazdai hozzáférés elérési útja

Ha az adatbázisszintű tűzfal megfelelően van konfigurálva, az adatbázis felhasználói olyan ügyféleszközökkel csatlakozhatnak, mint az SQL Server Management Studio vagy az SQL Server Data Tools. Csak a legújabb verziójú eszközök teszik elérhetővé az összes rendelkezésre álló funkciót és képességet. Az alábbi ábrán egy tipikus nem rendszergazdai elérési út látható.

![Nem rendszergazdai hozzáférés elérési útja](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Csoportok és szerepkörök

A hatékony hozzáférés-kezelés egyéni bejelentkezési adatok helyett csoportokhoz és szerepkörökhöz rendelt engedélyeket használ. 

- Azure Active Directory-hitelesítés használatakor az Azure Active Directory-felhasználókat helyezze egy Azure Active Directory-csoportba. Hozzon létre a csoportban egy adatbázis-felhasználót. Helyezzen egy vagy több adatbázis-felhasználót egy [adatbázis-szerepkörbe](https://msdn.microsoft.com/library/ms189121), majd rendeljen [engedélyeket](https://msdn.microsoft.com/library/ms191291.aspx) az adatbázis-szerepkörhöz.

- SQL Server-hitelesítés használata esetén hozzon létre tartalmazottadatbázis-felhasználókat az adatbázisban. Helyezzen egy vagy több adatbázis-felhasználót egy [adatbázis-szerepkörbe](https://msdn.microsoft.com/library/ms189121), majd rendeljen [engedélyeket](https://msdn.microsoft.com/library/ms191291.aspx) az adatbázis-szerepkörhöz.

Az adatbázis-szerepkörök lehetnek beépített szerepkörök, mint például a **db_owner**, a **db_ddladmin**, a **db_datawriter**, a **db_datareader**, a **db_denydatawriter** vagy a **db_denydatareader**. A **db_owner** általában teljes körű engedélyek biztosítására szolgál néhány felhasználó számára. A többi rögzített adatbázis-szerepkör hasznos az egyszerű adatbázisok fejlesztésének gyors elkezdéséhez, de a legtöbb éles környezetbeli adatbázishoz nem ajánlott. A **db_datareader** rögzített adatbázis-szerepkör csak olvasási hozzáférést biztosít az adatbázis minden táblájához, ami általában több a feltétlenül szükségesnél. Sokkal jobb megoldás a [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) utasítás használata saját felhasználói adatbázis-szerepkörök létrehozásához, majd az üzleti igényekhez minimálisan szükséges engedélyek beállítása az egyes szerepkörökhöz. Ha a felhasználó egyszerre több szerepkörnek is tagja, akkor a rendszer összesíti az engedélyeket.

## <a name="permissions"></a>Engedélyek

Az SQL Database-ben több mint 100 engedély adható vagy tagadható meg külön-külön. Ezek közül számos engedély beágyazott. Egy sémában található `UPDATE` engedély például a séma minden táblájára vonatkozó `UPDATE` engedélyt tartalmazza. A legtöbb engedélyrendszerhez hasonlóan az engedély megtagadása felülírja a megadását. Az engedélyek beágyazott jellege és száma miatt lehetséges, hogy alapos tervezés szükséges az adatbázis megfelelő védelmét biztosító engedélyrendszer kialakításához. Kezdje az [Engedélyek (Adatbázismotor)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) szakaszban felsorolt engedélyek listájával, majd tekintse át az engedélyek [poszterméretű ábráját](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png).


### <a name="considerations-and-restrictions"></a>Megfontolandó szempontok és korlátozások

Az SQL Database bejelentkezéseinek és felhasználóinak kezelésekor vegye figyelembe a következőket:

- A **utasítások futtatásához csatlakoznia kell a**master adatbázishoz`CREATE/ALTER/DROP DATABASE`.   
- A **kiszolgáló-rendszergazdai** bejelentkezéshez tartozó felhasználó nem módosítható és nem vethető el. 
- A **kiszolgáló-rendszergazdai** bejelentkezés alapértelmezett nyelve az amerikai angol (US-English).
- Csak a rendszergazdák (**kiszolgáló-rendszergazdai** bejelentkező vagy Azure AD-rendszergazda) és a **master** adatbázis **dbmanager** adatbázis-szerepkörének tagjai rendelkeznek a `CREATE DATABASE` és a `DROP DATABASE` utasítások futtatásához szükséges engedéllyel.
- A `CREATE/ALTER/DROP LOGIN` utasítások futtatásához csatlakoznia kell a master adatbázishoz. A bejelentkezési adatok használata azonban nem javasolt. Helyette használja a tartalmazott adatbázis felhasználóit.
- A felhasználói adatbázishoz történő csatlakozáshoz adja meg a kapcsolati sztringben szereplő adatbázis nevét.
- Csak a kiszolgálószintű fő bejelentkező és a **master adatbázis** **loginmanager** adatbázis-szerepkörének tagjai rendelkeznek a `CREATE LOGIN`, `ALTER LOGIN` és `DROP LOGIN` utasítások futtatásához szükséges engedéllyel.
- A `CREATE/ALTER/DROP LOGIN` és `CREATE/ALTER/DROP DATABASE` utasítások ADO.NET alkalmazáson belüli futtatásakor a paraméteres parancsok futtatása nem engedélyezett. További információkért lásd: [Parancsok és paraméterek](https://msdn.microsoft.com/library/ms254953.aspx).
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
- Ha az adatbázis-szerepkör tulajdonosa szeretne hozzáadni vagy eltávolítani egy felhasználót az adott szerepkörből, akkor a következő hiba léphet fel: **A „Név” felhasználó vagy szerepkör nem található ebben az adatbázisban.** Ez a hiba akkor következik be, ha a felhasználó a tulajdonos számára nem látható. A probléma megoldása érdekében ruházza fel a szerepkör tulajdonosát a `VIEW DEFINITION` engedéllyel. 


## <a name="next-steps"></a>További lépések

- A tűzfalszabályokkal kapcsolatos további információk: [Azure SQL Database-tűzfal](sql-database-firewall-configure.md).
- Az SQL Database összes biztonsági szolgáltatásáról [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít áttekintést.
- Oktatóanyagért lásd: [a Azure SQL Database biztonságossá tétele](sql-database-security-tutorial.md).
- Információk a nézetekről és a tárolt eljárásokról: [Nézetek és tárolt eljárások létrehozása](https://msdn.microsoft.com/library/ms365311.aspx)
- Információk adatbázis-objektumhoz való hozzáférés biztosításáról: [Adatbázis-objektumhoz való hozzáférés biztosítása](https://msdn.microsoft.com/library/ms365327.aspx)
