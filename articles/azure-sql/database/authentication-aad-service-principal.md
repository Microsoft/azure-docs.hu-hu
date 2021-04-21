---
title: Azure Active Directory szolgáltatásnév Azure SQL
description: Az Azure AD-alkalmazások (szolgáltatásnév) támogatják az Azure AD-felhasználók létrehozását Azure SQL Database, Azure SQL Managed Instance és Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 17e846c7435e2f1cc77c5915f7e0b308c3706f96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775420"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory szolgáltatásnév Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A Azure Active Directory (Azure AD) felhasználóinak Azure SQL Database (SQL DB) és [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Azure AD-alkalmazások (szolgáltatásnév) nevében történő létrehozása jelenleg nyilvános előzetes verzióban **érhető el.**

> [!NOTE]
> Ez a funkció már támogatott a SQL Managed Instance.

## <a name="service-principal-azure-ad-applications-support"></a>Szolgáltatásnév (Azure AD-alkalmazások) támogatása

Ez a cikk az Azure AD-val integrált és az Azure AD-regisztráció részét képezi alkalmazásokra vonatkozik. Ezeknek az alkalmazásoknak gyakran hitelesítési és engedélyezési hozzáférésre van szükségük Azure SQL feladatok végrehajtásához. Ez a nyilvános előzetes **verziójú** funkció mostantól lehetővé teszi, hogy a szolgáltatásnévvel Azure AD-felhasználókat hozzanak létre SQL Database és Azure Synapse. Egy korlátozás megakadályozta az Azure AD-objektumok létrehozását az eltávolított Azure AD-alkalmazások nevében.

Ha egy Azure AD-alkalmazást a Azure Portal vagy egy PowerShell-paranccsal regisztrál, két objektum jön létre az Azure AD-bérlőben:

- Egy alkalmazásobjektum
- Egy szolgáltatásnév-objektum

További információ az Azure AD-alkalmazásokról: Alkalmazás- és [szolgáltatásnév-objektumok](../../active-directory/develop/app-objects-and-service-principals.md) a Azure Active Directory és Azure-szolgáltatásnév létrehozása a [Azure PowerShell.](/powershell/azure/create-azure-service-principal-azureps)

SQL Database, Azure Synapse és SQL Managed Instance a következő Azure AD-objektumokat támogatják:

- Azure AD-felhasználók (felügyelt, összevont és vendég)
- Azure AD-csoportok (felügyelt és összevont)
- Azure AD-alkalmazások 

Az Azure AD-alkalmazás nevében használható T-SQL-parancs mostantól támogatott a SQL Database `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` és Azure Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Az Azure AD-felhasználók szolgáltatásnévvel való létrehozásának funkciói

Ennek a funkciónak a támogatása olyan Azure AD-alkalmazásautomatizálási folyamatokban hasznos, ahol az Azure AD-objektumok létrehozása és karbantartása SQL Database és Azure Synapse emberi beavatkozás nélkül. A szolgáltatásnév lehet az SQL logikai kiszolgáló Azure AD-rendszergazdája egy csoport vagy egy egyéni felhasználó részeként. Az alkalmazás automatizálhatja az Azure AD-objektumok létrehozását a SQL Database és Azure Synapse rendszergazdaként való végrehajtásakor, és nem igényel további SQL-jogosultságokat. Ez lehetővé teszi az adatbázis-felhasználók létrehozásának teljes automatizálását. Ez a szolgáltatás a rendszer által hozzárendelt felügyelt identitások és a felhasználó által hozzárendelt felügyelt identitások esetén is támogatott. További információ: Mik azok az [Azure-erőforrások felügyelt identitása?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Szolgáltatásnév engedélyezése Azure AD-felhasználók létrehozásához

Ha engedélyezni szeretné egy Azure AD-SQL Database és Azure Synapse Azure AD-alkalmazás nevében történő létrehozását, a következő beállításokra van szükség:

1. Rendelje hozzá a kiszolgáló identitását. A hozzárendelt kiszolgálóidentitás a felügyeltszolgáltatás-identitást (MSI) jelöli. A felügyelt identitások kiszolgálóidentitása Azure SQL nem támogatja a felhasználó által felügyelt identitást (UMI).
    - Új logikai Azure SQL hajtsa végre a következő PowerShell-parancsot:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    További információ: [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) parancs.

    - Meglévő logikai Azure SQL hajtsa végre a következő parancsot:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    További információ: [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) parancs.

    - Annak ellenőrzéséhez, hogy a kiszolgáló identitása hozzá van-e rendelve a kiszolgálóhoz, hajtsa végre a Get-AzSqlServer parancsot.

    > [!NOTE]
    > A kiszolgáló identitása cli-parancsokkal is hozzárendelhető. További információ: [az sql server create és](/cli/azure/sql/server#az_sql_server_create) az az sql server [update](/cli/azure/sql/server#az_sql_server_update).

2. Adjon engedélyt az Azure AD [**Directory-olvasóknak**](../../active-directory/roles/permissions-reference.md#directory-readers) a kiszolgálóhoz létrehozott vagy hozzárendelt kiszolgálóidentitáshoz.
    - Az engedély megadásához kövesse a következő cikkben SQL Managed Instance Azure [AD-rendszergazda (SQL Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance) című cikkben SQL Managed Instance.
    - Az engedélyt megadó Azure AD-felhasználónak az Azure **AD** globális rendszergazdai vagy kiemelt szerepkörök rendszergazdai szerepkörének kell **lennie.**

> [!IMPORTANT]
> Az 1. és a 2. lépést a fenti sorrendben kell végrehajtani. Először hozza létre vagy rendelje hozzá a kiszolgálóidentitást, majd adja meg a [**Címtárolvasók engedélyt.**](../../active-directory/roles/permissions-reference.md#directory-readers) Ha kihagyja ezen lépések valamelyikét, vagy mindkettő végrehajtási hibát okoz egy Azure AD-objektum létrehozásakor a Azure SQL egy Azure AD-alkalmazás nevében.
>
> Ha a szolgáltatásnév használatával adja meg vagy nem adja meg az Azure AD-rendszergazdai jogosultságot, az alkalmazásnak a [Directory.Read.All](/graph/permissions-reference#application-permissions-18) alkalmazás API-engedéllyel is rendelkeznie kell az Azure AD-ban. Az Azure [AD-rendszergazda](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)beállításához szükséges engedélyekkel kapcsolatos további információkért és az Azure AD-felhasználók Azure AD-alkalmazás nevében történő létrehozásához szükséges részletes utasításokért lásd: Oktatóanyag: Azure AD-felhasználók létrehozása [Azure AD-alkalmazásokkal.](authentication-aad-service-principal-tutorial.md)
>
> A **nyilvános előzetes verzióban** hozzárendelheti a **Címtárolvasó** szerepkört egy csoporthoz az Azure AD-ban. A csoport tulajdonosai ezután hozzáadhatják **a** felügyelt identitást ennek a csoportnak a  tagjaként, így nincs szükség globális rendszergazdára vagy kiemelt szerepkör rendszergazdára a Címtárolvasó szerepkör **megadásához.** További információ erről a szolgáltatásról: Címtárolvasók szerepkör a Azure Active Directory [for Azure SQL.](authentication-aad-directory-readers-role.md)

## <a name="troubleshooting-and-limitations-for-public-preview"></a>A nyilvános előzetes verzió hibaelhárítása és korlátozásai

- Ha Azure AD-objektumokat hoz létre Azure SQL Azure AD-alkalmazás nevében a  kiszolgálóidentitás engedélyezése és a Címtárolvasók engedélyének megadása nélkül, a művelet az alábbi lehetséges hibákkal meghiúsul. Az alábbi példahiba egy PowerShell-parancsvégrehajtásra mutat, amely létrehoz egy SQL Database-felhasználót a következő `myapp` [cikkben: Oktatóanyag: Azure AD-felhasználók létrehozása Azure AD-alkalmazásokkal.](authentication-aad-service-principal-tutorial.md)
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - A fenti hiba esetén kövesse az Identitás hozzárendelése a logikai kiszolgálóhoz [Azure SQL](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) és a Címtárolvasók hozzárendelése engedélyt az SQL logikai kiszolgáló [identitásához című lépéseit.](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)
    > [!NOTE]
    > A fent jelzett hibaüzenetek módosulnak, mielőtt a funkció ga ga ( ga) egyértelműen azonosítani tudja az Azure AD-alkalmazástámogatás hiányzó beállítási követelményeit.
- Az Azure AD-alkalmazás Azure AD-rendszergazdaként való SQL Managed Instance csak a CLI-paranccsal és az [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) vagy újabb verziójú PowerShell-paranccsal támogatott. További információ: [az sql mi ad-admin create és](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) parancs. 
    - Ha az Azure AD-Azure Portal-SQL Managed Instance szeretné beállítani, egy lehetséges megkerülő megoldás lehet egy Azure AD-csoport létrehozása. Ezután adja hozzá a szolgáltatásnév (Azure AD-alkalmazás) ehhez a csoporthoz, és állítsa be ezt a csoportot Azure AD-rendszergazdaként a SQL Managed Instance.
    - A szolgáltatásnév (Azure AD-alkalmazás) Azure AD-rendszergazdaként való beállítása SQL Database és Azure Synapse a Azure Portal, [a PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)és a [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) parancsokkal támogatott.
- Ha egy Másik Azure AD-bérlőből származó szolgáltatásnévvel használ egy Azure AD-alkalmazást, SQL Database egy másik bérlőben létrehozott SQL Managed Instance nem fog működni. Az alkalmazáshoz hozzárendelt szolgáltatásnévnek ugyanattól a bérlőtől kell lennie, mint az SQL logikai kiszolgálónak vagy a felügyelt példánynak.
- [Az Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) vagy újabb modulra akkor van szükség, ha a PowerShell használatával egyéni Azure AD-alkalmazást hoz létre Azure AD-rendszergazdaként a Azure SQL. Győződjön meg arról, hogy frissített a legújabb modulra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure AD-felhasználók létrehozása Azure AD-alkalmazásokkal](authentication-aad-service-principal-tutorial.md)
