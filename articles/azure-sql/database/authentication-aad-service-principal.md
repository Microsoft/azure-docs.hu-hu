---
title: Azure Active Directory egyszerű szolgáltatásnév az Azure SQL-sel
description: Az Azure AD-alkalmazások (egyszerű szolgáltatásnév) támogatják az Azure AD-felhasználók létrehozását az Azure SQL Database, az Azure SQL felügyelt példányában és az Azure szinapszis Analytics szolgáltatásban
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 15cc935457f76fb1d2fe4e8d699db831ebacc357
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181754"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory egyszerű szolgáltatásnév az Azure SQL-sel

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A Azure Active Directory (Azure AD) felhasználói létrehozásának támogatása Azure SQL Database (SQL DB) és az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) az Azure ad-alkalmazások (egyszerű szolgáltatásnév) nevében jelenleg **nyilvános előzetes** verzióban érhető el.

> [!NOTE]
> Ezt a funkciót már támogatja az SQL felügyelt példánya.

## <a name="service-principal-azure-ad-applications-support"></a>Egyszerű szolgáltatásnév (Azure AD-alkalmazások) támogatása

Ez a cikk az Azure AD-vel integrált és az Azure AD-regisztráció részét képező alkalmazásokra vonatkozik. Ezeknek az alkalmazásoknak gyakran kell hitelesítési és engedélyezési hozzáféréssel rendelkezniük az Azure SQL-hez a különböző feladatok elvégzéséhez. A **nyilvános előzetes** verzióban elérhető funkció lehetővé teszi, hogy az egyszerű szolgáltatások Azure ad-felhasználókat hozzanak létre SQL Database és az Azure szinapszisban. Korlátozást észlelt az Azure AD-objektum létrehozása az eltávolított Azure AD-alkalmazások nevében.

Ha egy Azure AD-alkalmazást a Azure Portal vagy egy PowerShell-parancs használatával regisztrál, két objektum jön létre az Azure AD-bérlőben:

- Egy alkalmazásobjektum
- Egy szolgáltatásnév-objektum

Az Azure AD-alkalmazásokkal kapcsolatos további információkért tekintse meg a [Azure Active Directory alkalmazás-és szolgáltatásnév objektumait](../../active-directory/develop/app-objects-and-service-principals.md) , és [hozzon létre egy Azure-beli szolgáltatásnevet a Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

A SQL Database, az Azure szinapszis és az SQL felügyelt példánya a következő Azure AD-objektumokat támogatja:

- Azure AD-felhasználók (felügyelt, összevont és vendég)
- Azure AD-csoportok (felügyelt és összevont)
- Azure AD-alkalmazások 

Az Azure AD-alkalmazás nevében a T-SQL-parancs `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` már támogatott a SQL Database és az Azure szinapszis számára.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Az Azure AD-beli felhasználói létrehozás funkciói az egyszerű szolgáltatásokkal

Ennek a funkciónak a támogatása az Azure AD Application Automation folyamataiban hasznos, ahol az Azure AD-objektumokat a SQL Database és az Azure szinapszis nem emberi interakció nélkül hozza létre és tartja karban. Az egyszerű szolgáltatás lehet az SQL logikai kiszolgáló Azure AD-rendszergazdája egy csoport vagy egy adott felhasználó részeként. Az alkalmazás automatizálhatja az Azure AD-objektumok létrehozását a SQL Database-ben és az Azure Szinapszisban, ha rendszergazdaként futtatja, és nem igényel további SQL-jogosultságokat. Ez lehetővé teszi az adatbázis-felhasználók létrehozásának teljes automatizálását. Ez a funkció a rendszerhez rendelt felügyelt identitás és a felhasználó által hozzárendelt felügyelt identitás esetében is támogatott. További információ: [Mi az Azure-erőforrások felügyelt identitása?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Egyszerű szolgáltatásnév engedélyezése Azure AD-felhasználók létrehozásához

Az Azure AD-objektumok létrehozásához SQL Database és Azure Szinapszisban egy Azure AD-alkalmazás nevében a következő beállítások szükségesek:

1. Rendelje hozzá a kiszolgáló identitását. A hozzárendelt kiszolgáló identitása a felügyelt rendszer identitását (MSI) jelöli. Az Azure SQL-kiszolgáló identitása jelenleg nem támogatja a felhasználó által felügyelt identitást (UMI).
    - Új Azure SQL logikai kiszolgáló esetén hajtsa végre a következő PowerShell-parancsot:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    További információ: [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) parancs.

    - Meglévő Azure SQL logikai kiszolgálók esetén hajtsa végre a következő parancsot:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    További információ: [set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) parancs.

    - A Get-AzSqlServer parancs végrehajtásával ellenőrizze, hogy a kiszolgáló identitása hozzá van-e rendelve a kiszolgálóhoz.

    > [!NOTE]
    > A kiszolgáló identitása a CLI-parancsokkal is hozzárendelhető. További információ: [az SQL Server Create](/cli/azure/sql/server#az-sql-server-create) és [az SQL Server Update](/cli/azure/sql/server#az-sql-server-update).

2. Adja meg az Azure AD [**Directory-olvasók**](../../active-directory/roles/permissions-reference.md#directory-readers) engedélyeit a kiszolgáló számára létrehozott vagy a kiszolgálóhoz rendelt identitásnak.
    - Az engedély megadásához kövesse az SQL felügyelt példányának az alábbi cikkben elérhető leírását: az [Azure ad-rendszergazda kiépítése (SQL felügyelt példány)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Az engedélyt megadó Azure AD-felhasználónak az Azure AD **globális rendszergazdai** vagy **Kiemelt szerepkörű rendszergazdai** szerepkör részét kell képeznie.

> [!IMPORTANT]
> Az 1. és a 2. lépést a fenti sorrendben kell végrehajtani. Először hozza létre vagy rendelje hozzá a kiszolgáló identitását, majd adja meg a [**Directory-olvasók**](../../active-directory/roles/permissions-reference.md#directory-readers) engedélyét. Ha kihagyja a fenti lépések egyikét, vagy mindkettő végrehajtási hibát okoz az Azure AD-objektum Azure SQL-beli létrehozásakor egy Azure AD-alkalmazás nevében.
>
> Ha a szolgáltatásnevet az Azure AD-rendszergazda beállítására vagy törlésére használja, az alkalmazásnak tartalmaznia kell a [könyvtárat is. Read. All](/graph/permissions-reference#application-permissions-18) Application API engedély az Azure ad-ben. Az [Azure ad-rendszergazda beállításához szükséges engedélyekkel](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)és az Azure ad-felhasználók Azure ad-alkalmazás nevében történő létrehozásával kapcsolatos részletes utasításokért lásd [: oktatóanyag: Azure ad-felhasználók létrehozása az Azure ad-alkalmazásokkal](authentication-aad-service-principal-tutorial.md).
>
> A **nyilvános előzetes** verzióban a címtár- **olvasók** szerepkört hozzárendelheti egy CSOPORThoz az Azure ad-ben. A csoport tulajdonosai ezután hozzáadhatják a felügyelt identitást a csoport tagjaként, ami megkerülheti a **globális rendszergazda** vagy a **Kiemelt szerepkörök rendszergazdájának** , hogy megadja a **címtár-olvasók** szerepkört. A szolgáltatással kapcsolatos további információkért lásd: az [Azure SQL Azure Active Directory Directory-olvasók szerepköre](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>A nyilvános előzetes verzió hibaelhárítása és korlátozásai

- Ha Azure AD-objektumokat hoz létre az Azure SQL-ben egy Azure AD-alkalmazás nevében anélkül, hogy engedélyezte a kiszolgáló identitását és a **címtár-olvasók** engedélyét, a művelet sikertelen lesz a következő lehetséges hibákkal. Az alábbi példában szereplő hiba a PowerShell-parancsok végrehajtásához használható SQL Database felhasználó létrehozásához `myapp` a cikk [oktatóanyag: Azure ad-felhasználók létrehozása Azure ad-alkalmazásokkal](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - A fenti hiba esetén kövesse az [identitás az Azure SQL logikai kiszolgálóhoz való hozzárendelésének](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) lépéseit, és az [SQL logikai kiszolgáló identitásához rendeljen címtár-olvasók engedélyt](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > A fent jelzett hibaüzenetek megváltoznak, mielőtt a Feature GA egyértelműen azonosítani tudja az Azure AD-alkalmazás támogatásának hiányzó telepítési követelményét.
- Az Azure AD-alkalmazás Azure AD-rendszergazdaként való beállítása az SQL felügyelt példányhoz csak a CLI-parancs és a PowerShell-parancs használatával támogatott az [az. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) vagy újabb. További információt az az [SQL mi ad-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) és [set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) parancsokban talál. 
    - Ha az SQL felügyelt példányának Azure Portal szeretné beállítani az Azure AD-rendszergazdát, lehetséges megkerülő megoldásként hozzon létre egy Azure AD-csoportot. Ezután adja hozzá az egyszerű szolgáltatásnevet (Azure AD-alkalmazást) a csoporthoz, és állítsa be a csoportot Azure AD-rendszergazdaként a felügyelt SQL-példányhoz.
    - Az egyszerű szolgáltatásnév (Azure AD alkalmazás) Azure AD-rendszergazdaként való beállítása SQL Database és az Azure szinapszis használata a Azure Portal, a [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)és a [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) -parancsok használatával támogatott.
- Egy másik Azure AD-bérlőhöz tartozó egyszerű szolgáltatással rendelkező Azure AD-alkalmazás nem fog sikerülni, ha egy másik bérlőben létrehozott SQL Database vagy SQL felügyelt példányhoz fér hozzá. Az alkalmazáshoz rendelt egyszerű szolgáltatásnak ugyanahhoz a bérlőhöz kell tartoznia, mint az SQL logikai kiszolgálónak vagy a felügyelt példánynak.
- Az [az. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) modul vagy újabb verzióra van szükség, amikor a PowerShell használatával állítson be egy egyéni Azure ad-alkalmazást Azure ad-rendszergazdaként az Azure SQL-hez. Győződjön meg arról, hogy a legújabb modulra van frissítve.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure AD-felhasználók létrehozása az Azure AD-alkalmazásokkal](authentication-aad-service-principal-tutorial.md)