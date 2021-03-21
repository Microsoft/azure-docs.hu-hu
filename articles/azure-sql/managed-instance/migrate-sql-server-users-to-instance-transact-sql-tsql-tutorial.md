---
title: SQL Server Windows-felhasználók és-csoportok migrálása SQL felügyelt példányra T-SQL használatával
description: Ismerje meg, hogyan telepítheti át a Windows-felhasználókat és-csoportokat egy SQL Server-példányban az Azure SQL felügyelt példányaira
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: f2dd34ab7c6ee5be26836e4abb86960605ee44ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84708665"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>Oktatóanyag: Windows-felhasználók és-csoportok áttelepíthetők egy SQL Server példányban az Azure SQL felügyelt példányára T-SQL DDL-szintaxis használatával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> A felhasználók és csoportok az SQL felügyelt példányra való átadásához használt szintaxis **nyilvános előzetes** verzióban érhető el.

Ez a cikk végigvezeti a helyszíni Windows-felhasználók és-csoportok áttelepítésének folyamatán a SQL Server az Azure SQL felügyelt példányain a T-SQL szintaxis használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Bejelentkezések létrehozása a SQL Serverhoz
> - Tesztelési adatbázis létrehozása az áttelepítéshez
> - Bejelentkezések, felhasználók és szerepkörök létrehozása
> - Az adatbázis biztonsági mentése és visszaállítása az SQL felügyelt példányaira (MI)
> - Felhasználók manuális migrálása az ALTER USER szintaxis használatával
> - A hitelesítés tesztelése az új leképezett felhasználókkal

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő előfeltételek érvényesek:

- A Windows-tartományt összevonták a Azure Active Directory (Azure AD) szolgáltatással.
- Hozzáférés Active Directory felhasználók/csoportok létrehozásához.
- Meglévő SQL Server a helyszíni környezetben.
- Meglévő SQL felügyelt példány. Tekintse meg a rövid útmutató [: SQL felügyelt példány létrehozása](instance-create-quickstart.md)című témakört.
  - `sysadmin`Az SQL felügyelt példányának az Azure ad-bejelentkezések létrehozásához kell használnia.
- [Hozzon létre egy Azure ad-rendszergazdát a felügyelt SQL-példányhoz](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).
- Csatlakozhat az SQL felügyelt példányához a hálózaton belül. További információkat a következő cikkekben talál:
  - [Alkalmazás csatlakoztatása felügyelt Azure SQL-példányhoz](connect-application-instance.md)
  - [Gyors útmutató: pont – hely kapcsolat konfigurálása egy Azure SQL felügyelt példányhoz a helyszínen](point-to-site-p2s-configure.md)
  - [Nyilvános végpont konfigurálása az Azure SQL felügyelt példányában](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>A T-SQL DDL szintaxisa

Alább látható a T-SQL DDL szintaxisa, amely támogatja a Windows-felhasználók és-csoportok áttelepítését egy SQL Server példányról az SQL felügyelt példányra az Azure AD-hitelesítéssel.

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>Argumentumok

_domainName_</br>
A felhasználó tartománynevét adja meg.

_userName_</br>
Megadja az adatbázison belül azonosított felhasználó nevét.

_= loginName \@ domainName.com_</br>
Felhasználó újraleképezése az Azure AD-bejelentkezésre

_groupName_</br>
Megadja az adatbázison belül azonosított csoport nevét.

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>1. rész: bejelentkezések létrehozása a SQL Server a Windows-felhasználók és-csoportok számára

> [!IMPORTANT]
> A következő szintaxis egy felhasználót és egy csoportos bejelentkezést hoz létre a SQL Serverban. Az alábbi szintaxis végrehajtása előtt meg kell győződnie arról, hogy a felhasználó és a csoport a Active Directory (AD) belül található. </br> </br>
> Felhasználók: testUser1, testGroupUser </br>
> Csoport: Migrálás – a testGroupUser az Active Directory-beli áttelepítési csoporthoz kell tartoznia

Az alábbi példa létrehoz egy bejelentkezést SQL Server a _testUser1_ nevű fiókhoz a _aadsqlmi_ tartomány alatt.

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases

use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;
go;

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser].
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;
go;


-- Check logins were created
select * from sys.server_principals;
go;
```

Hozzon létre egy adatbázist ehhez a teszthez.

```sql
-- Create a database called [migration]
create database migration
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>2. rész: Windows-felhasználók és-csoportok létrehozása, szerepkörök és engedélyek hozzáadása

A teszt felhasználó létrehozásához használja a következő szintaxist.

```sql
use migration;  
go

-- Create Windows user [aadsqlmi\testUser1] with login
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1];
go
```

A felhasználói engedélyek keresése:

```sql
-- Check the user in the Metadata
select * from sys.database_principals;
go

-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions;
go
```

Hozzon létre egy szerepkört, és rendelje hozzá a tesztelési felhasználót a következő szerepkörhöz:

```sql
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole;
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole;
go

alter role UserMigrationRole add member [aadsqlmi\testUser1];
go
```

A következő lekérdezés használatával jelenítheti meg az adott szerepkörhöz rendelt felhasználóneveket:

```sql
-- Display user name assigned to a specific role
SELECT DP1.name AS DatabaseRoleName,
   isnull (DP2.name, 'No members') AS DatabaseUserName
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name;
```

Csoport létrehozásához használja a következő szintaxist. Ezután vegye fel a csoportot a szerepkörbe `db_owner` .

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration];
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration';
go

--Check the db_owner role for 'aadsqlmi\migration' group
select is_rolemember('db_owner', 'aadsqlmi\migration')
go
-- Output  ( 1 means YES)
```

Hozzon létre egy tesztelési táblát, és adjon hozzá néhányat a következő szintaxis használatával:

```sql
-- Create a table and add data
create table test ( a int, b int);
go

insert into test values (1,10)
go

-- Check the table values
select * from test;
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>3. rész: az egyéni felhasználói adatbázis biztonsági mentése és visszaállítása az SQL felügyelt példányaira

Készítsen biztonsági másolatot az áttelepítési adatbázisról az [adatbázisok másolása biztonsági mentéssel és visszaállítással](/sql/relational-databases/databases/copy-databases-with-backup-and-restore), vagy használja a következő szintaxist:

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

Kövesse a rövid útmutatót [: adatbázis visszaállítása SQL felügyelt példányra](restore-sample-database-quickstart.md).

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>4. rész: felhasználók átirányítása a felügyelt SQL-példányra

Futtassa az ALTER USER parancsot az áttelepítési folyamat végrehajtásához az SQL felügyelt példányon.

1. Jelentkezzen be az SQL felügyelt példányára az SQL felügyelt példányához tartozó Azure AD-rendszergazdai fiók használatával. Ezután hozza létre az Azure AD-bejelentkezést az SQL felügyelt példányában a következő szintaxis használatával. További információ: [oktatóanyag: az SQL felügyelt példányának biztonsága Azure SQL Database az Azure ad-kiszolgáló résztvevői (bejelentkezések) használatával](aad-security-configure-tutorial.md).

    ```sql
    use master
    go

    -- Create login for AAD user [testUser1@aadsqlmi.net]
    create login [testUser1@aadsqlmi.net] from external provider
    go

    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net]
    create login [migration] from external provider
    go

    --Check the two new logins
    select * from sys.server_principals
    go
    ```

1. Győződjön meg arról, hogy a megfelelő adatbázishoz, táblához és résztvevőhöz tartozó áttelepítés megvalósul.

    ```sql
    -- Switch to the database migration that is already restored for MI
    use migration;
    go

    --Check if the restored table test exist and contain a row
    select * from test;
    go

    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals;
    go
    -- the old user aadsqlmi\testUser1 should be there
    -- the old group aadsqlmi\migration should be there
    ```

1. Használja az ALTER USER szintaxist a helyszíni felhasználó Azure AD-bejelentkezésre való leképezéséhez.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net];
    go

    -- Check the principal
    select * from sys.database_principals;
    go
    -- New user testUser1@aadsqlmi.net should be there instead
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    -- Check a specific role
    -- Display Db user name assigned to a specific role
    SELECT DP1.name AS DatabaseRoleName,
    isnull (DP2.name, 'No members') AS DatabaseUserName
    FROM sys.database_role_members AS DRM
    RIGHT OUTER JOIN sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
    LEFT OUTER JOIN sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
    WHERE DP1.type = 'R'
    ORDER BY DP1.name;
    ```

1. Használja az ALTER USER szintaxist a helyszíni csoport Azure AD-bejelentkezésre való leképezéséhez.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration];
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals;
    go

    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    --Check the db_owner role for 'aadsqlmi\migration' user
    select is_rolemember('db_owner', 'migration')
    go
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>5. rész: az Azure AD-felhasználó vagy-csoport hitelesítésének tesztelése

Tesztelje az SQL felügyelt példányának hitelesítését az ALTER USER szintaxissal korábban az Azure AD-bejelentkezéshez hozzárendelt felhasználó használatával.

1. Jelentkezzen be az összevont virtuális gépre az Azure SQL felügyelt példány-előfizetésének használatával `aadsqlmi\testUser1`
1. SQL Server Management Studio (SSMS) használatával jelentkezzen be az SQL felügyelt példányára **Active Directory integrált** hitelesítéssel, az adatbázishoz való csatlakozással `migration` .
    1. A testUser1@aadsqlmi.net hitelesítő adatokkal is bejelentkezhet a SSMS kapcsolóval **Active Directory – univerzális, MFA-támogatással**. Ebben az esetben azonban nem használhatja az egyszeri bejelentkezési mechanizmust, és jelszót kell beírnia. Nem kell összevont virtuális gépet használnia a felügyelt SQL-példányba való bejelentkezéshez.
1. A szerepkör-tag **kiválasztása** után a `test` táblázatból választhat

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

Tesztelje a hitelesítést egy SQL felügyelt példányon egy Windows-csoport tagja használatával `migration` . A felhasználót `aadsqlmi\testGroupUser` az áttelepítés előtt fel kell venni a csoportba `migration` .

1. Jelentkezzen be az összevont virtuális gépre az Azure SQL felügyelt példány-előfizetésének használatával `aadsqlmi\testGroupUser`
1. Az SSMS használata **Active Directory integrált** hitelesítéssel, kapcsolódás az Azure SQL felügyelt példány-kiszolgálójához és az adatbázishoz `migration`
    1. A testGroupUser@aadsqlmi.net hitelesítő adatokkal is bejelentkezhet a SSMS kapcsolóval **Active Directory – univerzális, MFA-támogatással**. Ebben az esetben azonban nem használhatja az egyszeri bejelentkezési mechanizmust, és jelszót kell beírnia. Nem kell összevont virtuális gépet használnia a felügyelt SQL-példányba való bejelentkezéshez.
1. A szerepkör részeként `db_owner` új táblát is létrehozhat.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> A Azure SQL Database ismert tervezési problémái miatt a csoport tagjaként végrehajtott Create an Table utasítás sikertelen lesz a következő hibával: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Az aktuális megkerülő megoldás egy meglévő sémával rendelkező tábla létrehozása a fenti esetben <dbo. New>

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: SQL Server migrálása a felügyelt Azure SQL-példányra a DMS használatával](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
