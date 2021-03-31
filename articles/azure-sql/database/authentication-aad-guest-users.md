---
title: Azure AD Guest-felhasználók létrehozása
description: Azure AD Guest-felhasználók létrehozása és Azure ad-rendszergazdaként való beállítása Azure AD-csoportok használata nélkül Azure SQL Database, Azure SQL felügyelt példány és Azure szinapszis Analytics használatával
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675058"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Azure AD-vendégfelhasználók létrehozása és beállítása Azure AD-rendszergazdaként

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Ez a cikk **nyilvános előzetes** verzióban érhető el.

A Azure Active Directory (Azure AD) vendég felhasználói az aktuális Azure AD-be importált felhasználók, más Azure Active Directory-címtárból vagy azokon kívül vannak. A vendég felhasználók például tartalmazhatnak más Azure Active könyvtárakból származó felhasználókat, illetve olyan fiókokat, mint például a *\@ Outlook.com*, a *\@ hotmail.com*, a *\@ Live.com* vagy a *\@ gmail.com*. Ez a cikk bemutatja, hogyan hozhat létre egy Azure AD vendég felhasználót, és hogyan állíthatja be a felhasználót Azure AD-rendszergazdaként az Azure SQL logikai kiszolgálóhoz, anélkül, hogy a vendég felhasználó tagja legyen az Azure AD-ban található csoportnak.

## <a name="feature-description"></a>A szolgáltatás leírása

Ez a funkció feloldja a jelenlegi korlátozást, amely lehetővé teszi a vendég felhasználók számára, hogy az Azure AD-ben létrehozott csoport tagjai legyenek Azure SQL Database, SQL felügyelt példányhoz vagy Azure szinapszis Analyticshez csatlakozzanak. A csoportot manuálisan kell hozzárendelni egy felhasználóhoz az adott adatbázisban a [create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) utasítás használatával. Miután létrehozta az adatbázis-felhasználót a vendég felhasználót tartalmazó Azure AD-csoport számára, a vendég felhasználó bejelentkezhet az adatbázisba Azure Active Directory az MFA-hitelesítés használatával. A **nyilvános előzetes** verzió részeként a vendég felhasználók létrehozhatók és közvetlenül csatlakozhatnak SQL Database, SQL felügyelt példányhoz vagy az Azure szinapszishoz anélkül, hogy előbb hozzá kellene adni őket egy Azure ad-csoporthoz, majd létre kell hozniuk egy adatbázis-felhasználót az adott Azure ad-csoport számára.

A szolgáltatás részeként lehetősége van arra is, hogy az Azure AD vendég felhasználóját közvetlenül az Azure SQL logikai kiszolgáló AD-rendszergazdájaként állítsa be. A meglévő funkciók, amelyekben a vendég felhasználó egy Azure AD-csoport tagja lehet, és ez a csoport beállítható úgy, hogy az Azure SQL logikai kiszolgáló Azure AD-rendszergazdája ne legyen hatással. Ez a változás nem érinti az olyan vendég felhasználókat az adatbázisban, amelyek egy Azure AD-csoport részét képezik.

További információ az Azure AD-csoportokat használó vendég felhasználók meglévő támogatásáról: [multi-factor Azure Active Directory hitelesítés használata](authentication-mfa-ssms-overview.md).

## <a name="prerequisite"></a>Előfeltétel

- Az [az. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) modul vagy újabb verzióra van szükség, amikor a PowerShell használatával állít be egy vendég felhasználót Azure ad-rendszergazdaként az Azure SQL logikai kiszolgálóhoz.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Adatbázis-felhasználó létrehozása az Azure AD vendég felhasználója számára 

Az alábbi lépéseket követve létrehozhat egy adatbázis-felhasználót egy Azure AD vendég felhasználó használatával.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Vendég felhasználó létrehozása a SQL Database és az Azure Szinapszisban

1. Győződjön meg arról, hogy a vendég felhasználó (például `user1@gmail.com` ) már hozzá van adva az Azure ad-hez, és egy Azure ad-rendszergazda lett beállítva az adatbázis-kiszolgálóhoz. Azure Active Directory hitelesítéshez Azure AD-rendszergazdai jogosultsággal kell rendelkeznie.

1. Kapcsolódjon az SQL Database-hez az Azure AD-rendszergazdaként vagy egy, a felhasználók létrehozásához megfelelő SQL-engedéllyel rendelkező Azure AD-felhasználóként, és futtassa az alábbi parancsot azon az adatbázison, amelyen a vendég felhasználót hozzá kell adni:

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Ekkor létre kell hoznia egy adatbázis-felhasználót a vendég felhasználó számára `user1@gmail.com` .

1. Az alábbi parancs futtatásával ellenőrizheti, hogy sikeresen létrejött-e az adatbázis-felhasználó:

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. A SQL Server Management Studio (SSMS) használatával válassza le és jelentkezzen be az adatbázisba, `user1@gmail.com` a **Azure Active Directory-Universal hitelesítési módszerével, MFA-val**. [](/sql/ssms/download-sql-server-management-studio-ssms) További információ: [a multi-factor Azure Active Directory hitelesítés használata](authentication-mfa-ssms-overview.md).

### <a name="create-guest-user-in-sql-managed-instance"></a>Vendég felhasználó létrehozása a felügyelt SQL-példányban

> [!NOTE]
> Az SQL felügyelt példánya támogatja az Azure AD-felhasználók és az Azure AD-beli adatbázis-felhasználók bejelentkezését. Az alábbi lépések bemutatják, hogyan hozhat létre egy Azure AD vendég felhasználóhoz tartozó bejelentkezési azonosítót és felhasználót az SQL felügyelt példányában. Azt is megteheti, hogy létrehoz egy [tárolt adatbázis-felhasználót](/sql/relational-databases/security/contained-database-users-making-your-database-portable) az SQL felügyelt példányában a [vendég felhasználó létrehozása a SQL Database és az Azure szinapszisban](#create-guest-user-in-sql-database-and-azure-synapse) szakaszban található metódus használatával.

1. Győződjön meg arról, hogy a vendég felhasználó (például `user1@gmail.com` ) már hozzá van adva az Azure ad-hez, és egy Azure ad-rendszergazda be van állítva az SQL felügyelt példány-kiszolgálójához. Azure Active Directory hitelesítéshez Azure AD-rendszergazdai jogosultsággal kell rendelkeznie.

1. Kapcsolódjon az SQL felügyelt példány-kiszolgálóhoz az Azure AD-rendszergazdaként vagy egy, a felhasználók létrehozásához megfelelő SQL-engedéllyel rendelkező Azure AD-felhasználóként, és futtassa a következő parancsot az `master` adatbázison a vendég felhasználó bejelentkezési azonosítójának létrehozásához:

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Ekkor létre kell hoznia egy bejelentkezési azonosítót a vendég felhasználó számára `user1@gmail.com` az `master` adatbázisban.

1. Az alábbi parancs futtatásával ellenőrizheti, hogy sikeresen létrejött-e a bejelentkezés:

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. Futtassa az alábbi parancsot azon az adatbázison, amelyen a vendég felhasználót hozzá kell adni: 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. Ekkor létre kell hoznia egy adatbázis-felhasználót a vendég felhasználó számára `user1@gmail.com` .

1. A SQL Server Management Studio (SSMS) használatával válassza le és jelentkezzen be az adatbázisba, `user1@gmail.com` a **Azure Active Directory-Universal hitelesítési módszerével, MFA-val**. [](/sql/ssms/download-sql-server-management-studio-ssms) További információ: [a multi-factor Azure Active Directory hitelesítés használata](authentication-mfa-ssms-overview.md).

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Vendég felhasználó beállítása Azure AD-rendszergazdaként

A következő lépésekkel állíthatja be az Azure AD vendég felhasználóját az SQL logikai kiszolgáló Azure AD-rendszergazdájaként.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>Az Azure AD-rendszergazda beállítása a SQL Database és az Azure szinapszis számára

1. Győződjön meg arról, hogy a vendég felhasználó (például `user1@gmail.com` ) már hozzá van adva az Azure ad-hez.

1. A következő PowerShell-parancs futtatásával adja hozzá a vendég felhasználót az Azure SQL logikai kiszolgáló Azure AD-rendszergazdájához:

    - Cserélje le az kifejezést az Azure- `<ResourceGroupName>` erőforráscsoport nevére, amely az Azure SQL logikai kiszolgálót tartalmazza.
    - Cserélje le az `<ServerName>` kifejezést az Azure SQL logikai kiszolgáló nevére. Ha a kiszolgáló neve `myserver.database.windows.net` , cserélje le a (z) értékre `<Server Name>` `myserver` .
    - Cserélje le a `<DisplayNameOfGuestUser>` nevet a vendég felhasználónevére.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    Az Azure CLI-parancs az [SQL Server ad-admin](/cli/azure/sql/server/ad-admin) használatával is beállíthatja a vendég felhasználót Azure ad-rendszergazdaként az Azure SQL logikai kiszolgálóhoz.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>Az Azure AD-rendszergazda beállítása az SQL felügyelt példányaihoz

1. Győződjön meg arról, hogy a vendég felhasználó (például `user1@gmail.com` ) már hozzá van adva az Azure ad-hez.

1. Lépjen a [Azure Portalra](https://portal.azure.com), és lépjen a **Azure Active Directory** -erőforráshoz. A **kezelés** területen lépjen a **felhasználók** ablaktáblára. Válassza ki a vendég felhasználóját, és jegyezze fel `Object ID` . 

1. A következő PowerShell-parancs futtatásával adja hozzá a vendég felhasználót a felügyelt SQL-példányhoz tartozó Azure AD-rendszergazdaként:

    - Cserélje le az `<ResourceGroupName>` t az Azure-erőforráscsoport nevére, amely tartalmazza az SQL felügyelt példányát.
    - Cserélje le `<ManagedInstanceName>` az t az SQL felügyelt példányának nevére.
    - Cserélje le a `<DisplayNameOfGuestUser>` nevet a vendég felhasználónevére.
    - Cserélje le `<AADObjectIDOfGuestUser>` a t a `Object ID` korábban összegyűjtöttre.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    Az SQL [Mi ad-admin](/cli/azure/sql/mi/ad-admin) Azure CLI-parancsával is beállíthatja a vendég felhasználót Azure ad-rendszergazdaként az SQL felügyelt példányához.

## <a name="limitations"></a>Korlátozások

A Azure Portal korlátozással rendelkezik, amely megakadályozza az Azure AD vendég felhasználóként való kiválasztását az SQL felügyelt példányához tartozó Azure AD-rendszergazdaként. Az Azure ad-n kívüli vendég fiókok, például a *\@ Outlook.com*, a *\@ hotmail.com*, a *\@ Live.com* vagy a *\@ gmail.com* esetében az ad admin választó megjeleníti ezeket a fiókokat, de szürkén jelenik meg, és nem választható ki. Az Azure AD-rendszergazda beállításához használja a fent felsorolt [PowerShell-vagy CLI-parancsokat](#setting-a-guest-user-as-an-azure-ad-admin) . Azt is megteheti, hogy a vendég felhasználót tartalmazó Azure AD-csoport az SQL felügyelt példányának Azure AD-rendszergazdájaként állítható be.

Ez a funkció a szolgáltatás általános elérhetősége előtt lesz engedélyezve az SQL felügyelt példánya számára.

## <a name="next-steps"></a>Következő lépések

- [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL-sel](authentication-aad-configure.md)
- [Multi-Factor Azure Active Directory hitelesítés használata](authentication-mfa-ssms-overview.md)
- [FELHASZNÁLÓ létrehozása (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)