---
title: HRE engedélyezése az Azure SSIS Integration Runtime
description: Ez a cikk azt ismerteti, hogyan engedélyezhető Azure Active Directory hitelesítés a felügyelt identitással a Azure Data Factory számára Azure-SSIS Integration Runtime létrehozásához.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: cd3f590e1869b28f0ac08ce98da32a98160e4e86
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392731"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure Active Directory-hitelesítés engedélyezése az Azure-SSIS integrációs modulhoz

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk bemutatja, hogyan engedélyezhető Azure Active Directory (Azure AD) hitelesítés a Azure Data Factory (ADF) felügyelt identitásával, és hogyan használható a hagyományos hitelesítési módszerek (például az SQL-hitelesítés) helyett a következőkre:

- Hozzon létre egy Azure-SSIS Integration Runtime (IR), amely bekapcsolja az SSIS Catalog adatbázist (SSISDB) a SQL Database vagy az SQL felügyelt példányában az Ön nevében.

- Csatlakozás különböző Azure-erőforrásokhoz, amikor Azure-SSIS IR SSIS-csomagokat futtat.

Az ADF felügyelt identitásával kapcsolatos további információkért lásd: [Data Factory felügyelt identitása](./data-factory-service-identity.md).

> [!NOTE]
>
> - Ebben az esetben az automatikus lapadagolóba felügyelt identitással való Azure AD-hitelesítés csak a SSIS IR létrehozási és további indítási műveleteiben használatos, amelyek pedig kiépítik és csatlakoznak a SSISDB. A SSIS-csomagok végrehajtásához a SSIS IR a SSISDB-létesítés során létrehozott, teljes körűen felügyelt fiókokkal csatlakozik a SSISDB-hez.
> - Ha már létrehozta a SSIS IR-t az SQL-hitelesítés használatával, nem állíthatja be újra az Azure AD-hitelesítés használatát a PowerShellen keresztül, de ezt Azure Portal/ADF-alkalmazáson keresztül teheti meg. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Az Azure AD engedélyezése Azure SQL Database

SQL Database támogatja az adatbázisok Azure AD-felhasználóval történő létrehozását. Először létre kell hoznia egy Azure AD-csoportot, amelynek a felügyelt identitása tagja az ADF-nek. Ezután be kell állítania egy Azure AD-felhasználót a SQL Database Active Directory-rendszergazdájaként, majd csatlakoznia kell hozzá a SQL Server Management Studio (SSMS) alkalmazásban a felhasználó használatával. Végül létre kell hoznia egy, az Azure AD-csoportot jelképező felhasználót, így az ADF felügyelt identitását Azure-SSIS IR használhatja a SSISDB létrehozásához az Ön nevében.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Azure AD-csoport létrehozása az ADF felügyelt identitásával tagként

Használhat meglévő Azure AD-csoportot, vagy létrehozhat egy újat az Azure AD PowerShell használatával.

1.  Telepítse az [Azure ad PowerShell](/powershell/azure/active-directory/install-adv2) -modult.

2.  Jelentkezzen be `Connect-AzureAD` a használatával, majd a következő parancsmag futtatásával hozzon létre egy csoportot, és mentse egy változóba:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Az eredmény az alábbi példához hasonlít, amely a változó értékét is megjeleníti:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Adja hozzá az ADF felügyelt identitását a csoporthoz. Az [Data Factory felügyelt identitásának](./data-factory-service-identity.md) beszerzéséhez kövesse az elsődleges felügyelt identitás-objektum azonosítóját (például: 765AD4AB-XXXX-XXXX-XXXX-51ed985819dc, de ne használjon felügyelt identitás-alkalmazás azonosítóját erre a célra).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Azt is megteheti, hogy később is megtekintheti a csoporttagság.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-sql-database"></a>Azure AD-hitelesítés konfigurálása SQL Databasehoz

Az [Azure ad-hitelesítést az alábbi lépésekkel konfigurálhatja és kezelheti az SQL-](../azure-sql/database/authentication-aad-configure.md) sel:

1.  A Azure Portal területen válassza a **minden szolgáltatás**  ->  **SQL-kiszolgálók** lehetőséget a bal oldali navigációs sávon.

2.  Válassza ki a kiszolgálót SQL Database az Azure AD-hitelesítéssel való konfiguráláshoz.

3.  A panel **Beállítások** szakaszában válassza a **Active Directory rendszergazda** elemet.

4.  A parancssorban válassza a **rendszergazda beállítása** lehetőséget.

5.  Válasszon ki egy Azure AD-felhasználói fiókot, amelyet a kiszolgáló rendszergazdájának kell elvégeznie, majd válassza a **Kiválasztás lehetőséget.**

6.  A parancssorban válassza a **Mentés lehetőséget.**

### <a name="create-a-contained-user-in-sql-database-representing-the-azure-ad-group"></a>Egy foglalt felhasználó létrehozása az Azure AD-csoportot képviselő SQL Databaseban

Ehhez a következő lépéshez [Microsoft SQL Server Management Studiora](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) van szükség.

1. Indítsa el a SSMS.

2. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen írja be a kiszolgáló nevét a **kiszolgáló neve** mezőbe.

3. A **hitelesítés** mezőben válassza a **Active Directory-Universal az MFA-támogatással** lehetőséget (a másik két Active Directory hitelesítési típust is használhatja, lásd: [Azure ad-hitelesítés konfigurálása és kezelése az SQL](../azure-sql/database/authentication-aad-configure.md)használatával).

4. A **Felhasználónév** mezőben adja meg a kiszolgáló-rendszergazdaként beállított Azure ad-fiók nevét, például: testuser@xxxonline.com .

5. Válassza a **kapcsolat** lehetőséget, és fejezze be a bejelentkezési folyamatot.

6. A **Object Explorer** bontsa ki a **Databases**  ->  **System Databases (adatbázisok rendszeradatbázisok** ) mappát.

7. Kattintson a jobb gombbal a **Master** adatbázis elemre, és válassza az **Új lekérdezés** lehetőséget.

8. A lekérdezési ablakban adja meg a következő T-SQL-parancsot, és válassza a **végrehajtás** lehetőséget az eszköztáron.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   A parancsnak sikeresen el kell végeznie, és egy befoglalt felhasználót kell létrehoznia a csoport ábrázolásához.

9. Törölje a lekérdezési ablakot, írja be a következő T-SQL-parancsot, majd válassza az eszköztáron a **végrehajtás** elemet.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   A parancsnak sikeresen el kell végeznie, és meg kell adnia a tárolt felhasználó számára, hogy létre tudja hozni az adatbázist (SSISDB).

10. Ha a SSISDB SQL-hitelesítéssel lett létrehozva, és az Azure AD-hitelesítés használatára szeretné használni a Azure-SSIS IR az eléréséhez, először győződjön meg arról, **hogy a főadatbázishoz** való hozzáférés engedélyezésének lépései sikeresen befejeződtek. Ezután kattintson a jobb gombbal a **SSISDB** -adatbázisra, és válassza az **Új lekérdezés** elemet.

11. A lekérdezési ablakban adja meg a következő T-SQL-parancsot, és válassza a **végrehajtás** lehetőséget az eszköztáron.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    A parancsnak sikeresen el kell végeznie, és egy befoglalt felhasználót kell létrehoznia a csoport ábrázolásához.

12. Törölje a lekérdezési ablakot, írja be a következő T-SQL-parancsot, majd válassza az eszköztáron a **végrehajtás** elemet.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    A parancsnak sikeresen el kell végeznie, és meg kell adnia a befoglalt felhasználó számára a SSISDB elérésének lehetőségét.

## <a name="enable-azure-ad-on-sql-managed-instance"></a>Az Azure AD engedélyezése a felügyelt SQL-példányon

Az SQL felügyelt példánya lehetővé teszi, hogy közvetlenül az ADF felügyelt identitásával hozzon létre egy adatbázist. Nem kell csatlakoztatnia az ADF felügyelt identitását egy Azure AD-csoporthoz, vagy létre kell hoznia egy, az adott csoportot jelképező felhasználót az SQL felügyelt példányában.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Azure AD-hitelesítés konfigurálása az Azure SQL felügyelt példányaihoz

Hajtsa végre az [Azure Active Directory-rendszergazda létesítése az SQL felügyelt példányhoz](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)című témakör lépéseit.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-sql-managed-instance"></a>Az ADF felügyelt identitásának hozzáadása felhasználóként az SQL felügyelt példányában

Ehhez a következő lépéshez [Microsoft SQL Server Management Studiora](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) van szükség.

1.  Indítsa el a SSMS.

2.  Kapcsolódjon az SQL felügyelt példányához egy olyan SQL Server fiók használatával, amely **rendszergazda**. Ez egy ideiglenes korlátozás, amely akkor lesz eltávolítva, ha az Azure AD-kiszolgáló rendszerbiztonsági tagja (bejelentkezések) az Azure SQL felügyelt példányaihoz a GA lesz. A következő hibaüzenet jelenik meg, ha egy Azure AD-beli rendszergazdai fiókot próbál használni a bejelentkezés létrehozásához: msg 15247, 16. szint, 1. sor, az 1. sorban a felhasználónak nincs engedélye a művelet elvégzésére.

3.  A **Object Explorer** bontsa ki a **Databases**  ->  **System Databases (adatbázisok rendszeradatbázisok** ) mappát.

4.  Kattintson a jobb gombbal a **Master** adatbázis elemre, és válassza az **Új lekérdezés** lehetőséget.

5.  A lekérdezési ablakban hajtsa végre az alábbi T-SQL-szkriptet, hogy hozzáadja az ADF felügyelt identitását felhasználóként

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    A parancsnak sikeresen végre kell hajtania az ADF felügyelt identitásának megadását, amely lehetővé teszi egy adatbázis (SSISDB) létrehozását.

6.  Ha a SSISDB SQL-hitelesítéssel lett létrehozva, és az Azure AD-hitelesítés használatára szeretné használni a Azure-SSIS IR az eléréséhez, először győződjön meg arról, **hogy a főadatbázishoz** való hozzáférés engedélyezésének lépései sikeresen befejeződtek. Ezután kattintson a jobb gombbal a **SSISDB** -adatbázisra, és válassza az **Új lekérdezés** elemet.

7.  A lekérdezési ablakban adja meg a következő T-SQL-parancsot, és válassza a **végrehajtás** lehetőséget az eszköztáron.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    A parancsnak sikeresen el kell végeznie az ADF felügyelt identitásának megadását a SSISDB elérésére.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure-SSIS IR kiépítése Azure Portal/ADF-alkalmazásban

Ha Azure Portal/ADF-alkalmazásban kiépíti a Azure-SSIS IR, az **SQL-beállítások** lapon válassza a **HRE-hitelesítés használata az ADF-hez tartozó felügyelt identitással** lehetőséget. Az alábbi képernyőfelvételen a SQL Database üzemeltetési SSISDB rendelkező IR-beállítások láthatók. A SSISDB-t futtató SQL felügyelt példányok esetében a **katalógus adatbázis-szolgáltatási szintje** és az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás nem alkalmazható, míg más beállítások ugyanazok.

A Azure-SSIS IR létrehozásával kapcsolatos további információkért lásd: [Azure-SSIS integrációs modul létrehozása Azure Data Factory-ben](./create-azure-ssis-integration-runtime.md).

![Az Azure-SSIS Integration Runtime beállításai](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Azure-SSIS IR kiépítése a PowerShell-lel

A Azure-SSIS IR PowerShell-lel való kiépítéséhez tegye a következőket:

1.  [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modul telepítése.

2.  A szkriptben ne állítsa be a `CatalogAdminCredential` paramétert. Például:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>SSIS-csomagok futtatása felügyelt Identitásos hitelesítéssel

Ha Azure-SSIS IRon futtat SSIS-csomagokat, a felügyelt identitás-hitelesítés használatával különböző Azure-erőforrásokhoz kapcsolódhat. Jelenleg már támogatott a felügyelt identitások hitelesítése a következő ügyfélkapcsolat-kezelők esetében.

- [OLE DB Csatlakozáskezelő](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET-Csatlakozáskezelő](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage-kapcsolatkezelő](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
