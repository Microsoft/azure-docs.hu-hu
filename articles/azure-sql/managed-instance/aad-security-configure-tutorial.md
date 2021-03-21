---
title: SQL felügyelt példányok biztonsága az Azure AD-kiszolgáló rendszerbiztonsági azonosítójával (bejelentkezések)
description: Ismerje meg az Azure SQL felügyelt példányának védelmét szolgáló technikákat és szolgáltatásokat, valamint az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) használatát
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 9161bf4f99ddfed479451d2091458ab309aa2c17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788621"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Oktatóanyag: az Azure SQL felügyelt példányának biztonsága Azure AD Server-rendszerbiztonsági tag használatával (bejelentkezések)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példánya szinte minden olyan biztonsági funkciót biztosít, amely a legújabb SQL Server (Enterprise Edition) adatbázismotor:

- Hozzáférés korlátozása elkülönített környezetben
- Identitást igénylő hitelesítési mechanizmusok használata: Azure Active Directory (Azure AD) és SQL-hitelesítés
- Hitelesítés használata szerepköralapú tagságokkal és engedélyekkel
- Biztonsági funkciók engedélyezése

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Azure AD-kiszolgáló rendszerbiztonsági tag (login) létrehozása felügyelt példányhoz
> - Engedélyek megadása az Azure AD-kiszolgáló résztvevői számára (bejelentkezések) felügyelt példányokban
> - Azure AD-felhasználók létrehozása Azure AD Server-rendszerbiztonsági tagokból (bejelentkezések)
> - Engedélyek kiosztása az Azure AD-felhasználók számára és az adatbázis biztonságának kezelése
> - Megszemélyesítés használata az Azure AD-felhasználók számára
> - Adatbázisok közötti lekérdezések használata az Azure AD-felhasználókkal
> - Ismerje meg a biztonsági funkciókat, például a fenyegetések elleni védelmet, a naplózást, az adatmaszkolást és a titkosítást

További információ: az [Azure SQL felügyelt példányának áttekintése](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Felügyelt példány
  - Kövesse ezt a cikket: gyors útmutató [: felügyelt példány létrehozása](instance-create-quickstart.md)
- Képes hozzáférni a felügyelt példányhoz, és [kiépített egy Azure ad-rendszergazdát a felügyelt példányhoz](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). További információ:
  - [Az alkalmazás összekötése felügyelt példányokkal](connect-application-instance.md)
  - [SQL felügyelt példány kapcsolati architektúrája](connectivity-architecture-overview.md)
  - [Azure Active Directory hitelesítés konfigurálása és kezelése SQL-sel](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Hozzáférés korlátozása 

A felügyelt példányok magánhálózati IP-címen keresztül érhetők el. Az elszigetelt SQL Server környezetekhez hasonlóan az alkalmazásoknak és a felhasználóknak a kapcsolat létrehozása előtt hozzá kell férniük az SQL felügyelt példány hálózatához (VNet). További információ: [az alkalmazás összekötése az SQL felügyelt példányával](connect-application-instance.md).

Egy szolgáltatás-végpontot felügyelt példányon is konfigurálhat, amely lehetővé teszi, hogy a nyilvános kapcsolatok ugyanolyan módon legyenek, mint a Azure SQL Database.
További információ: [nyilvános végpont konfigurálása az Azure SQL felügyelt példányában](public-endpoint-configure.md).

> [!NOTE]
> Még ha engedélyezve vannak a szolgáltatási végpontok, [Azure SQL Database tűzfalszabályok](../database/firewall-configure.md) nem érvényesek. Az Azure SQL felügyelt példányának saját [beépített tűzfala](management-endpoint-verify-built-in-firewall.md) van a kapcsolat kezeléséhez.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Azure AD-kiszolgáló rendszerbiztonsági tag (login) létrehozása a SSMS használatával

Az első Azure AD-kiszolgálói rendszerbiztonsági tag (login) a standard SQL-rendszergazdai fiók (nem Azure AD) vagy az üzembe helyezési `sysadmin` folyamat során létrehozott felügyelt példányhoz tartozó Azure ad-rendszergazda által hozható létre. További információ: Azure Active Directory- [rendszergazda létesítése SQL felügyelt példányhoz](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Az SQL felügyelt példányhoz való kapcsolódásra vonatkozó példákat a következő cikkekben talál:

- [Gyors útmutató: az Azure virtuális gép konfigurálása az SQL felügyelt példányhoz való kapcsolódásra](connect-vm-instance-configure.md)
- [Gyors útmutató: pont – hely kapcsolat konfigurálása a helyszíni SQL felügyelt példányhoz](point-to-site-p2s-configure.md)

1. Jelentkezzen be a felügyelt példányba egy olyan szabványos SQL bejelentkezési fiókkal (nem Azure AD), amely az `sysadmin` SQL felügyelt példányhoz tartozó vagy Azure ad-rendszergazda, [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms)használatával.

2. A **Object Explorerban** kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés** elemet.

3. A lekérdezési ablakban a következő szintaxis használatával hozzon létre egy helyi Azure AD-fiókhoz tartozó bejelentkezési azonosítót:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Ez a példa egy bejelentkezési azonosítót hoz létre a fiókhoz nativeuser@aadsqlmi.onmicrosoft.com .

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre a bejelentkezés létrehozásához.

5. A következő T-SQL-parancs végrehajtásával keresse meg az újonnan hozzáadott bejelentkezést:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Képernyőkép az "s" M s Object Explorer Results (eredmények) lapjáról, amely az újonnan hozzáadott bejelentkezés nevét, principal_idát, SID-azonosítóját, típusát és type_desc mutatja.](./media/aad-security-configure-tutorial/native-login.png)

További információ: [create login (bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)).

## <a name="grant-permissions-to-create-logins"></a>Engedélyek megadása bejelentkezések létrehozásához

Más Azure AD-kiszolgálói rendszerbiztonsági tag (login) létrehozásához SQL Server szerepköröket vagy engedélyeket kell megadnia a rendszerbiztonsági tag számára (SQL vagy Azure AD).

### <a name="sql-authentication"></a>SQL-hitelesítés

- Ha a bejelentkezés egy SQL-rendszerbiztonsági tag, csak a szerepkör részét képező bejelentkezések `sysadmin` használhatják a Create parancsot az Azure ad-fiókhoz tartozó bejelentkezések létrehozásához.

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

- Ha engedélyezni szeretné az újonnan létrehozott Azure AD-kiszolgáló rendszerbiztonsági tag (login) számára más bejelentkezési adatok létrehozását más Azure AD-felhasználók,-csoportok vagy-alkalmazások számára, adja meg a bejelentkezési `sysadmin` vagy `securityadmin` kiszolgálói szerepkört.
- Az Azure AD-kiszolgáló rendszerbiztonsági tagjának (bejelentkezési azonosítójának) minden más Azure AD-kiszolgáló-rendszerbiztonsági tag (login) létrehozásához meg kell adni legalább a **bejelentkezési** engedélyeket.
- Alapértelmezés szerint az újonnan létrehozott Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) számára biztosított szabványos engedély a következő: **SQL-kapcsolat** és **bármely adatbázis megtekintése**.
- A `sysadmin` kiszolgálói szerepkör a felügyelt példányokon belül számos Azure ad-kiszolgálói rendszerbiztonsági tag (bejelentkezés) számára biztosítható.

A bejelentkezés hozzáadása a `sysadmin` kiszolgálói szerepkörhöz:

1. Jelentkezzen be újra a felügyelt példányba, vagy használja a meglévő Azure AD-rendszergazdai vagy SQL-rendszerbiztonsági tag-t `sysadmin` .

1. A **Object Explorerban** kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés** elemet.

1. A `sysadmin` következő T-SQL szintaxissal adja meg a kiszolgálói szerepkört az Azure ad-kiszolgáló (login) számára:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    Az alábbi példa megadja a `sysadmin` kiszolgálói szerepkört a bejelentkezéshez nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>További Azure AD Server-rendszerbiztonsági tag (bejelentkezések) létrehozása a SSMS használatával

Ha az Azure AD-kiszolgáló rendszerbiztonsági tag (login) létrejött, és `sysadmin` jogosultságokkal rendelkezik, a bejelentkezés létrehozhat további bejelentkezéseket a **from External Provider** záradék használatával a **create login** paranccsal.

1. Kapcsolódjon a felügyelt példányhoz az Azure AD-kiszolgáló rendszerbiztonsági azonosítójával (login) SQL Server Management Studio használatával. Adja meg az SQL felügyelt példány állomásneve nevét. A SSMS-ben való hitelesítéshez három lehetőség közül választhat az Azure AD-fiókkal való bejelentkezéskor:

   - Active Directory – MFA-támogatással rendelkező univerzális
   - Active Directory – jelszó
   - Active Directory – integrált </br>

     ![Képernyőkép a Kapcsolódás a kiszolgálóhoz párbeszédpanelről S S M S, Active Directory-Universal, a hitelesítés legördülő menüben kiválasztott MFA-támogatással.](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     További információ: [univerzális hitelesítés (SSMS-támogatás multi-Factor Authentication)](../database/authentication-mfa-ssms-overview.md).

1. Válassza **a Active Directory-Universal lehetőséget MFA-támogatással**. Ez egy Multi-Factor Authentication bejelentkezési ablakot hoz létre. Jelentkezzen be az Azure AD-jelszavával.

    ![A Multi-Factor Authentication bejelentkezési ablak képernyőképe a jelszó megadása mezőben lévő kurzorral.](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. A SSMS **Object Explorer** kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés** elemet.
1. A lekérdezési ablakban a következő szintaxis használatával hozzon létre egy másik Azure AD-fiókhoz tartozó bejelentkezési azonosítót:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Ez a példa egy olyan bejelentkezési azonosítót hoz létre az Azure AD-felhasználó számára bob@aadsqlmi.net , amelynek tartományi aadsqlmi.net az Azure ad aadsqlmi.onmicrosoft.com-tartománnyal összevonták.

    Hajtsa végre a következő T-SQL-parancsot. Az összevont Azure AD-fiókok a helyi Windows-bejelentkezések és-felhasználók számára az SQL által felügyelt példányok helyére kerülnek.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Hozzon létre egy adatbázist a felügyelt példányban az [adatbázis létrehozása](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) szintaxis használatával. Ezt az adatbázist fogjuk használni a felhasználói bejelentkezések teszteléséhez a következő szakaszban.
    1. A **Object Explorerban** kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés** elemet.
    1. A lekérdezési ablakban használja a következő szintaxist egy **MyMITestDB** nevű adatbázis létrehozásához.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. SQL felügyelt példányok bejelentkezésének létrehozása egy Azure AD-beli csoport számára. A csoportnak léteznie kell az Azure AD-ben, mielőtt felveszi a bejelentkezést az SQL felügyelt példányba. Lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Hozzon létre egy csoportot _mygroup_ , és vegyen fel tagokat ebbe a csoportba.

1. Nyisson meg egy új lekérdezési ablakot a SQL Server Management Studioban.

    Ez a példa feltételezi, hogy létezik egy _mygroup_ nevű csoport az Azure ad-ben. Futtassa a következő parancsot:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Tesztként jelentkezzen be a felügyelt példányba az újonnan létrehozott bejelentkezéssel vagy csoporttal. Nyisson meg egy új kapcsolódást a felügyelt példányhoz, és használja az új bejelentkezést hitelesítéskor.
1. A **Object Explorerban** kattintson a jobb gombbal a kiszolgálóra, majd válassza az új **lekérdezés** lehetőséget az új kapcsolatban.
1. A következő parancs végrehajtásával keresse meg a kiszolgáló engedélyeit az újonnan létrehozott Azure AD-kiszolgáló rendszerbiztonsági tag (login) számára:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Az Azure AD vendég felhasználói csak akkor támogatottak az SQL felügyelt példányok bejelentkezéséhez, ha az Azure AD-csoport részeként van hozzáadva. Az Azure AD vendég felhasználója egy olyan fiók, amelyet a felügyelt példányhoz tartozó Azure AD-példányhoz kell meghívni egy másik Azure AD-példányból. Például: joe@contoso.com (Azure ad-fiók) vagy steve@outlook.com (Microsoft-fiók) hozzáadhatók egy csoporthoz az Azure ad aadsqlmi-példányban. A felhasználók egy csoportba való felvétele után a bejelentkezési szintaxis **létrehozásával** létrehozhat egy bejelentkezést a csoport SQL felügyelt példány **Master** adatbázisában. Azok a vendég felhasználók, akik tagjai ennek a csoportnak, csatlakozhatnak a felügyelt példányhoz az aktuális bejelentkezésük (például joe@contoso.com vagy steve@outlook.com ) használatával.

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Azure AD-felhasználó létrehozása az Azure AD-kiszolgáló rendszerbiztonsági azonosítójával (bejelentkezés)

Az egyes adatbázisokhoz való engedélyezés ugyanúgy működik, mint az SQL felügyelt példánya, mint a SQL Server adatbázisaiban. A felhasználók létrehozhatók egy adatbázis meglévő bejelentkezési adataiból, és megadhatók az adott adatbázisra vonatkozó engedélyekkel, vagy hozzáadhatók az adatbázis-szerepkörökhöz.

Most, hogy létrehoztunk egy **MyMITestDB** nevű adatbázist, és egy olyan bejelentkezési azonosítót, amely csak az alapértelmezett engedélyekkel rendelkezik, a következő lépés egy felhasználó létrehozása a bejelentkezésből. Jelenleg a bejelentkezés kapcsolódhat a felügyelt példányhoz, és megtekintheti az összes adatbázist, de nem tud kommunikálni az adatbázisokkal. Ha az alapértelmezett engedélyekkel rendelkező Azure AD-fiókkal jelentkezik be, és megpróbálja kibontani az újonnan létrehozott adatbázist, a következő hibaüzenet jelenik meg:

![Képernyőkép: a (z) "az adatbázis MyMITestDB nem érhető el" hibaüzenetet tartalmazó S s M s Object Explorer. (ObjectExplorer)".](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

További információ az adatbázis-engedélyek megadásáról: [első lépések az adatbázismotor engedélyeivel](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Azure AD-felhasználó létrehozása és minta tábla létrehozása

1. Jelentkezzen be a felügyelt példányba egy olyan `sysadmin` fiókkal, amely SQL Server Management Studiot használ.
1. A **Object Explorerban** kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés** elemet.
1. A lekérdezési ablakban a következő szintaxissal hozhat létre Azure AD-felhasználót egy Azure AD-kiszolgáló rendszerbiztonsági tag (login) használatával:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    A következő példa létrehoz egy felhasználót bob@aadsqlmi.net a bejelentkezésből bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Azt is támogatja, hogy Azure AD-felhasználót hozzon létre egy csoportba tartozó Azure AD-kiszolgálói rendszerbiztonsági tag (login) használatával.

    Az alábbi példa egy bejelentkezési azonosítót hoz létre az Azure ad-példányban található _mygroup_ .

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    A *mygroup* -hez tartozó összes felhasználó hozzáférhet a **MyMITestDB** -adatbázishoz.

    > [!IMPORTANT]
    > Amikor egy **felhasználót** egy Azure ad-kiszolgáló rendszerbiztonsági tagja (login) alapján hoz létre, a **bejelentkezéshez** ugyanazt a login_name kell megadnia a user_name.

    További információt a [felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)című témakörben talál.

1. Egy új lekérdezési ablakban hozzon létre egy teszt táblát a következő T-SQL parancs használatával:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Hozzon létre egy SSMS a létrehozott felhasználóval. Megfigyelheti, hogy a korábban létrehozott **TestTable** tábla nem látható `sysadmin` . A felhasználónak meg kell adnia az adatoknak az adatbázisból való beolvasásához szükséges engedélyeket.

1. A következő parancs végrehajtásával ellenőrizhető, hogy az aktuális engedély van-e a felhasználó számára:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Felhasználók hozzáadása adatbázis szintű szerepkörökhöz

Ahhoz, hogy a felhasználó megjelenjen az adatbázisban lévő adat, [adatbázis-szintű szerepköröket](/sql/relational-databases/security/authentication-access/database-level-roles) biztosíthatunk a felhasználónak.

1. Jelentkezzen be a felügyelt példányba egy olyan `sysadmin` fiókkal, amely SQL Server Management Studiot használ.

1. A **Object Explorerban** kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés** elemet.

1. Adja meg az Azure AD-felhasználó számára az `db_datareader` adatbázis-szerepkört a következő T-SQL szintaxis használatával:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    A következő példa a felhasználó bob@aadsqlmi.net és a csoport _mygroup_ , amely a `db_datareader` **MyMITestDB** -adatbázisra vonatkozó engedélyekkel rendelkezik:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. A következő parancs végrehajtásával győződjön meg arról, hogy az adatbázisban létrehozott Azure AD-felhasználó létezik:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Hozzon létre egy új kapcsolódást a felügyelt példányhoz azzal a felhasználóval, aki hozzá lett adva a `db_datareader` szerepkörhöz.
1. A tábla megjelenítéséhez bontsa ki **Object Explorer** az adatbázist.

    ![Képernyőkép a Object Explorerról a MyMITestDB található táblákhoz tartozó mappák struktúráját megjelenítő s s M S s M-ben. A dbo. A TestTable mappa ki van emelve.](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Nyisson meg egy új lekérdezési ablakot, és hajtsa végre a következő SELECT utasítást:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Meg tudja-e tekinteni a tábla adatait? Ekkor a visszaadott oszlopok láthatók.

    ![Képernyőkép az S s M S Object Explorer Results (eredmények) lapjáról, amely megjeleníti a tábla oszlopainak fejléceit, a AccountNum, a várost, a nevet és az állapotot.](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Azure AD-kiszolgáló szintű rendszerbiztonsági tag (bejelentkezések) megszemélyesítése

Az SQL felügyelt példánya támogatja az Azure AD-kiszolgáló szintű rendszerbiztonsági tag (login) megszemélyesítését.

### <a name="test-impersonation"></a>Megszemélyesítés tesztelése

1. Jelentkezzen be a felügyelt példányba egy olyan `sysadmin` fiókkal, amely SQL Server Management Studiot használ.

1. A **Object Explorerban** kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés** elemet.

1. A lekérdezési ablakban a következő parancs használatával hozzon létre egy új tárolt eljárást:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. A következő parancs használatával láthatja, hogy a tárolt eljárás végrehajtásakor megszemélyesített felhasználó **bob \@ aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Megszemélyesítés tesztelése a végrehajtás mint bejelentkezési utasítás használatával:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Csak a szerepkör részét képező SQL Server-szintű rendszerbiztonsági tag (login) `sysadmin` hajthatja végre a következő műveleteket az Azure ad résztvevői számára:
>
> - VÉGREHAJTÁS FELHASZNÁLÓKÉNT
> - VÉGREHAJTÁS BEJELENTKEZÉSKÉNT

## <a name="use-cross-database-queries"></a>Adatbázisok közötti lekérdezések használata

Az Azure ad-fiókok és az Azure AD-kiszolgáló résztvevői (bejelentkezések) esetében az adatbázisok közötti lekérdezések támogatottak. Egy Azure AD-csoporttal rendelkező adatbázis-lekérdezés teszteléséhez egy másik adatbázist és táblát kell létrehoznia. Ha már létezik, kihagyhatja egy másik adatbázis és egy tábla létrehozását.

1. Jelentkezzen be a felügyelt példányba egy olyan `sysadmin` fiókkal, amely SQL Server Management Studiot használ.
1. A **Object Explorerban** kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés** elemet.
1. A lekérdezési ablakban a következő parancs használatával hozzon létre egy **MyMITestDB2** nevű adatbázist és egy **TestTable2** nevű táblát:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. Az új lekérdezési ablakban hajtsa végre a következő parancsot a felhasználói _mygroup_ létrehozásához az új adatbázis- **MyMITestDB2**, és adja meg az adott adatbázishoz tartozó Select engedélyeket a _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Jelentkezzen be a felügyelt példányra SQL Server Management Studio használatával az Azure AD-csoport _mygroup_ tagjaként. Nyisson meg egy új lekérdezési ablakot, és hajtsa végre a több adatbázisra vonatkozó SELECT utasítást:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Ekkor meg kell jelennie a tábla eredményeinek a **TestTable2**.

## <a name="additional-supported-scenarios"></a>További támogatott forgatókönyvek

- Az SQL-ügynök kezelése és a feladatok végrehajtása az Azure AD Server-rendszerbiztonsági tag (login) esetében támogatott.
- Az adatbázis biztonsági mentési és visszaállítási műveleteit az Azure AD-kiszolgáló résztvevői (bejelentkezések) hajtják végre.
- Az Azure AD-kiszolgáló rendszertagjaihoz (bejelentkezésekhez) és a hitelesítési eseményekhez kapcsolódó összes utasítás [naplózása](auditing-configure.md) .
- Dedikált rendszergazdai kapcsolatok az Azure AD-kiszolgálói rendszerbiztonsági tagok (bejelentkezések) számára, amelyek a `sysadmin` kiszolgáló szerepkör tagjai.
- Az Azure AD-kiszolgáló résztvevői (bejelentkezések) a [Sqlcmd segédprogram](/sql/tools/sqlcmd-utility) és a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) eszköz használatával támogatottak.
- A bejelentkezési eseményindítók az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) felől érkező bejelentkezési események esetén támogatottak.
- Az Service Broker-és adatbázis-levelezés beállítható az Azure AD-kiszolgáló résztvevői (bejelentkezések) használatával.

## <a name="next-steps"></a>Következő lépések

### <a name="enable-security-features"></a>Biztonsági funkciók engedélyezése

Tekintse meg az [SQL felügyelt példányának biztonsági funkciói](sql-managed-instance-paas-overview.md#security-features) című cikket, amely átfogó listát nyújt az adatbázis biztonságossá tételének lehetőségeiről. A következő biztonsági funkciókat tárgyaljuk:

- [SQL felügyelt példányok naplózása](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Fenyegetések észlelése](threat-detection-configure.md)
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security)
- [Transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>SQL felügyelt példány képességei

A felügyelt SQL-példányok képességeinek teljes áttekintését lásd:

> [!div class="nextstepaction"]
> [SQL felügyelt példány képességei](sql-managed-instance-paas-overview.md)