---
title: Az Azure Active Directory-fiókon alapuló hitelesítés konfigurálása
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Megtudhatja, hogyan csatlakozhat SQL Database, SQL Managed Instance és Azure Synapse Analytics-hitelesítéssel Azure Active Directory az Azure AD konfigurálása után.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: c75364f2565611b6738996c082610229db0cb2a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762226"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Azure AD-hitelesítés konfigurálása és kezelése Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ez a cikk bemutatja, hogyan hozhat létre és használhat Azure AD Azure Active Directory példányt, majd hogyan használhatja az Azure AD-t Azure SQL Database [,](../managed-instance/sql-managed-instance-paas-overview.md) [Azure SQL Managed Instance](sql-database-paas-overview.md)és [Azure Synapse Analytics.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Az áttekintést lásd: [Azure Active Directory használata.](authentication-aad-overview.md)

## <a name="azure-ad-authentication-methods"></a>Az Azure AD hitelesítési módszerei

Az Azure AD-hitelesítés a következő hitelesítési módszereket támogatja:

- Csak felhőalapú Azure AD-identitások
- Az Azure AD hibrid identitások, amelyek támogatják a következő funkciókat:
  - Felhőalapú hitelesítés két lehetőséggel és közvetlen egyszeri bejelentkezéssel (SSO)
    - Azure AD-jelszó kivonatának hitelesítése
    - Azure AD átmenő hitelesítés
  - Összevont hitelesítés

Az Azure AD hitelesítési módszereivel és a választott módszerrel kapcsolatos további információkért lásd: A hibrid identitásmegoldáshoz Azure Active Directory [hitelesítési módszer kiválasztása.](../../active-directory/hybrid/choose-ad-authn.md)

Az Azure AD hibrid identitásokkal, beállítással és szinkronizálással kapcsolatos további információkért lásd:

- Jelszó kivonatának hitelesítése – [Jelszó kivonatszinkronizálásának megvalósítása Azure AD Connect szinkronizálással](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Átmenő hitelesítés – [Azure Active Directory átmenő hitelesítés](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Összevont hitelesítés – Active Directory összevonási szolgáltatások (AD FS) üzembe [helyezése](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) az Azure-ban, valamint Azure AD Connect [összevonás](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Azure AD-példány létrehozása és feltöltése

Hozzon létre egy Azure AD-példányt, és töltse fel felhasználókkal és csoportokkal. Az Azure AD lehet a kezdeti felügyelt Azure AD-tartomány. Az Azure AD az Azure AD helyi Active Directory szolgáltatásokkal összevont tartományi szolgáltatások is lehet.

További információk a következő témakörökben találhatók: [Helyszíni identitások integrálása az Azure Active Directoryval](../../active-directory/hybrid/whatis-hybrid-identity.md), [Saját tartománynév hozzáadása az Azure AD-hez](../../active-directory/fundamentals/add-custom-domain.md), [A Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonást](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Az Azure AD-címtár felügyelete](../../active-directory/fundamentals/active-directory-whatis.md), [Az Azure AD kezelése Windows PowerShell használatával](/powershell/azure/) és [Hibrid identitás – szükséges portok és protokollok](../../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz

1. Társítsa Azure-előfizetését a Azure Active Directory úgy, hogy a címtárat megbízható címtárként használja az adatbázist üzemeltető Azure-előfizetés számára. Részletekért lásd: Azure-előfizetés társítása vagy hozzáadása a [Azure Active Directory bérlőhöz.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

2. A címtár-Azure Portal a tartományhoz társított előfizetésre való váltáshoz.

   > [!IMPORTANT]
   > Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. Ez az előfizetés és a címtár közötti bizalmi kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn, amelyek inkább az előfizetések gyermekerőforrásainak számítanak. Ha egy előfizetés lejár, akkor az előfizetéssel társított ilyen egyéb erőforrások hozzáférése is lejár. De a címtár az Azure-ban elérhető marad, és más előfizetéseket társíthat ezzel a címtárral, és folytathatja a címtár felhasználóinak kezelését. További információ az erőforrásokról: Az Azure-beli [erőforrásokhoz való hozzáférés ismertetése.](../../active-directory/external-identities/add-users-administrator.md) További információ erről a megbízható kapcsolatról: Azure-előfizetés társítása vagy hozzáadása [a Azure Active Directory.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Azure AD-rendszergazda egy kiszolgálóval a SQL Database

Az [](logical-servers.md) Azure-ban minden kiszolgáló (SQL Database vagy Azure Synapse) egyetlen kiszolgálói rendszergazdai fiókkal kezdődik, amely a teljes kiszolgáló rendszergazdája. Hozzon létre egy második rendszergazdai fiókot Azure AD-fiókként. Ez a rendszerbiztonsági tag tartalmazottadatbázis-felhasználóként jön létre a kiszolgáló master adatbázisában. A rendszergazdai fiókok az összes **db_owner** szerepkör tagjai, és minden felhasználói adatbázist **dbo-felhasználóként kell megadniuk.** A rendszergazdai fiókokkal kapcsolatos további információkért lásd: [Adatbázisok és bejelentkezések kezelése.](logins-create-manage.md)

Ha a Azure Active Directory georeplikációval használja, a rendszergazdát Azure Active Directory az elsődleges és a másodlagos kiszolgálóhoz is konfigurálni kell. Ha egy kiszolgáló nem rendelkezik rendszergazdai Azure Active Directory, akkor a Azure Active Directory és a felhasználók egy `Cannot connect` "to server" hibaüzenetet kapnak.

> [!NOTE]
> Azok a felhasználók, akik nem Azure AD-fiókon alapulnak (beleértve a kiszolgáló-rendszergazdai fiókot is), nem hozhatnak létre Azure AD-alapú felhasználókat, mert nem tudják érvényesíteni a javasolt adatbázis-felhasználókat az Azure AD-val.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Azure AD-rendszergazda (SQL Managed Instance)

> [!IMPORTANT]
> Csak akkor kövesse ezeket a lépéseket, ha üzembe Azure SQL Managed Instance. Ezt a műveletet csak globális rendszergazda vagy kiemelt szerepkör-rendszergazda hajthatja végre az Azure AD-ban.
>
> A **nyilvános előzetes verzióban** hozzárendelheti a **Címtárolvasó** szerepkört egy csoporthoz az Azure AD-ban. A csoport tulajdonosai ezután hozzáadhatják a felügyeltpéldány-identitást a csoport tagjaként, ami lehetővé teszi, hogy Azure AD-rendszergazdát létesítsen a SQL Managed Instance. További információ erről a szolgáltatásról: Címtárolvasók szerepkör a Azure Active Directory [for Azure SQL.](authentication-aad-directory-readers-role.md)

A SQL Managed Instance, hogy elolvassa az Azure AD-t az olyan feladatok sikeres végrehajtásához, mint a felhasználók biztonsági csoporttagságon keresztüli hitelesítése vagy új felhasználók létrehozása. Ahhoz, hogy ez működjön, engedélyt kell adnunk a SQL Managed Instance számára az Azure AD olvasásra. Ezt az alkalmazás vagy a PowerShell Azure Portal meg.

### <a name="azure-portal"></a>Azure Portal

Ha olvasási engedélyt SQL Managed Instance az Azure AD-Azure Portal, jelentkezzen be globális rendszergazdaként az Azure AD-be, és kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)jobb felső sarkában válassza ki a kapcsolatot a lehetséges Active Directories legördülő listából.

2. Válassza ki a Active Directory Azure AD-t.

   Ez a lépés a Active Directory társított előfizetést a SQL Managed Instance-hoz társítja, és győződjön meg arról, hogy ugyanazt az előfizetést használja az Azure AD-példányhoz és a SQL Managed Instance.

3. Lépjen az SQL Managed Instance Azure AD-integrációhoz használni kívánt alkalmazáshoz.

   ![Képernyőkép a Azure Portal a Active Directory felügyelt SQL-példányhoz megnyitott felügyeleti oldalról.](./media/authentication-aad-configure/aad.png)

4. Válassza a rendszergazdai oldal tetején Active Directory, és adjon engedélyt az aktuális felhasználónak.

    ![Képernyőkép a felügyelt SQL-példány engedélyeinek megadására szolgáló párbeszédpanelről az Active Directory. Az Engedélyek megadása gomb ki van választva.](./media/authentication-aad-configure/grant-permissions.png)

5. A művelet sikeres után a következő értesítés jelenik meg a jobb felső sarokban:

    ![Képernyőkép egy értesítésről, amely megerősíti, hogy az Active Directory olvasási engedélyei sikeresen frissültek a felügyelt példányon.](./media/authentication-aad-configure/success.png)

6. Most kiválaszthatja az Azure AD-rendszergazdát a SQL Managed Instance. Ezért a Rendszergazdai Active Directory lapon válassza a **Rendszergazdai parancs beállítása** lehetőséget.

    ![Képernyőkép a kiválasztott felügyelt SQL-példány rendszergazdai Active Directory lapján kiemelt Rendszergazda beállítása parancsról.](./media/authentication-aad-configure/set-admin.png)

7. Az Azure AD rendszergazdai oldalán keressen rá egy felhasználóra, válassza ki a felhasználót vagy csoportot rendszergazdaként, majd válassza a **Kijelölés lehetőséget.**

   A Active Directory lapon a rendszergazdai csoport összes tagja és Active Directory. A kiszürkülő felhasználók és csoportok nem választhatók ki, mert nem támogatottak Azure AD-rendszergazdaként. A támogatott rendszergazdák listáját az Azure AD funkciói és [korlátozásai című témakörben láthatja.](authentication-aad-overview.md#azure-ad-features-and-limitations) Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) csak az Azure Portal-re vonatkozik, és nem propagálható SQL Database, SQL Managed Instance vagy Azure Synapse.

    ![Rendszergazda Azure Active Directory hozzáadása](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. A rendszergazdai oldal Active Directory kattintson a **Mentés gombra.**

    ![Képernyőkép a Active Directory lapról, a Felső sor Mentés gombjával a Rendszergazda beállítása és a Rendszergazda eltávolítása gombok mellett.](./media/authentication-aad-configure/save.png)

    A rendszergazda módosításának folyamata több percig is eltarthat. Ezután az új rendszergazda megjelenik a Active Directory mezőben.

Miután kiépít egy Azure AD-rendszergazdát a SQL Managed Instance, megkezdheti az Azure AD-kiszolgálóneveket (bejelentkezéseket) a [CREATE LOGIN szintaxissal.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) További információkért lásd a SQL Managed Instance [áttekintését.](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)

> [!TIP]
> Ha később el szeretne távolítani egy rendszergazdát, a rendszergazdai oldal Active Directory válassza a Rendszergazda eltávolítása **lehetőséget,** majd kattintson a Mentés **gombra.**

### <a name="powershell"></a>PowerShell

Ha olvasási engedélyt SQL Managed Instance Azure AD-nek a PowerShell használatával, futtassa a következő szkriptet:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

$aadTenant = "<YourTenantId>" # Enter your tenant ID
$managedInstanceName = "MyManagedInstance"

# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>PowerShell a SQL Managed Instance

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell-parancsmagok futtatásához telepítenie Azure PowerShell és futtatnia. Részletes információk: [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása).

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modult továbbra is támogatja a Azure SQL Managed Instance, de minden jövőbeli fejlesztés az Az.Sql modulra lesz kihozva. Az AzureRM modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek. További információ a kompatibilitásukról: [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Az új Azure PowerShell Az modul).

Azure AD-rendszergazda üzembe végrehajtásához hajtsa végre a következő Azure PowerShell parancsokat:

- Connect-AzAccount
- Select-AzSubscription

Az Azure AD rendszergazdai jogosultságok építéséhez és kezeléséhez használt parancsmagok SQL Managed Instance az alábbi táblázatban találhatóak:

| Parancsmag neve | Description |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Ki hoz létre egy Azure AD-SQL Managed Instance az aktuális előfizetésben lévő fiókhoz. (Az aktuális előfizetésből kell, hogy legyen)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Eltávolít egy Azure AD-rendszergazdát az SQL Managed Instance előfizetéshez. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Az aktuális előfizetésben lévő virtuális SQL Managed Instance Azure AD-rendszergazdájával kapcsolatos adatokat ad vissza.|

A következő parancs információkat kap egy ManagedInstance01 nevű SQL Managed Instance Azure AD-rendszergazdáról, amely egy ResourceGroup01 nevű erőforráscsoporthoz van társítva.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

A következő parancs egy ADATBÁZISA nevű Azure AD-rendszergazdai csoportot hoz létre a ManagedInstance01 nevű SQL Managed Instance számára. Ez a kiszolgáló a ResourceGroup01 erőforráscsoporthoz van társítva.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

A következő parancs eltávolítja a ResourceGroup01 erőforráscsoporthoz társított ManagedInstanceName01 nevű SQL Managed Instance azure AD-rendszergazdáját.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure AD-rendszergazdát is kiépíthet a SQL Managed Instance a következő PARANCSSORi felületi parancsok használatával:

| Parancs | Leírás |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) | Ki kell Azure Active Directory rendszergazdát a SQL Managed Instance (az aktuális előfizetésből kell, hogy legyen). |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_delete) | Eltávolít egy Azure Active Directory rendszergazdát a SQL Managed Instance. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_list) | Egy, a Azure Active Directory konfigurált rendszergazda adatait adja SQL Managed Instance. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_update) | Frissíti a Active Directory rendszergazdáját SQL Managed Instance. |

A CLI-parancsokkal kapcsolatos további információkért lásd: [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Azure AD-rendszergazda (SQL Database)

> [!IMPORTANT]
> Csak akkor kövesse ezeket [a](logical-servers.md) lépéseket, ha kiszolgálót SQL Database vagy Azure Synapse.

Az alábbi két eljárás azt mutatja be, hogyan lehet Azure Active Directory rendszergazdai jogosultságot a kiszolgálóhoz a Azure Portal PowerShell használatával.

### <a name="azure-portal"></a>Azure Portal

1. A [Azure Portal](https://portal.azure.com/)jobb felső sarkában válassza ki a kapcsolatot a lehetséges Active Directories (Lehetséges Active Directories) listájának legördülő listájához. Válassza ki a megfelelő Active Directory alapértelmezett Azure AD-ként. Ez a lépés összeköti az előfizetéshez társított Active Directory kiszolgálóval, és győződjön meg arról, hogy ugyanazt az előfizetést használja az Azure AD-hez és a kiszolgálóhoz is.

2. Keresse meg és válassza az **SQL Server lehetőséget.**

    ![SQL-kiszolgálók keresése és kiválasztása](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Ezen az oldalon az **SQL-kiszolgálók** kiválasztása  előtt kiválaszthatja a  név melletti csillagot, hogy kedvencként jelöljön ki egy kategóriát, és **hozzáadhat SQL-kiszolgálókat** a bal oldali navigációs sávhoz.

3. A **SQL Server** válassza a Rendszergazda **Active Directory lehetőséget.**

4. A Rendszergazda **Active Directory lapon** válassza a Rendszergazda **beállítása lehetőséget.**

    ![Az SQL-kiszolgálók Active Directory beállítása](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. A Rendszergazda **hozzáadása lapon** keressen rá egy felhasználóra, válassza ki a felhasználót vagy csoportot rendszergazdaként, majd válassza a **Kijelölés lehetőséget.** (A Active Directory lapon az összes tag és csoport látható a Active Directory. A kiszürkülő felhasználók és csoportok nem választhatók ki, mert nem támogatottak Azure AD-rendszergazdaként. (A támogatott rendszergazdák listáját a Use Azure Active Directory Authentication for [authentication for authentication with SQL Database or Azure Synapse](authentication-aad-overview.md)(Az Azure **AD** szolgáltatásai és korlátozásai) című Azure Synapse.) Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) csak a portálra vonatkozik, és nem propagálva a SQL Server.

    ![Válassza a Azure Active Directory lehetőséget](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. A rendszergazdai oldal **Active Directory kattintson** a **Mentés gombra.**

    ![rendszergazda mentése](./media/authentication-aad-configure/save-admin.png)

A rendszergazda módosításának folyamata több percig is eltarthat. Ezután az új rendszergazda megjelenik a Active Directory **mezőben.**

   > [!NOTE]
   > Az Azure AD-rendszergazda beállításakor az új rendszergazda neve (felhasználó vagy csoport) nem lehet már jelen a virtuális főadatbázisban kiszolgálóhitelesítési felhasználóként. Ha van ilyen, az Azure AD rendszergazdai beállítása sikertelen lesz; a létrehozás visszagördülése, amely jelzi, hogy már létezik ilyen rendszergazda (név). Mivel az ilyen kiszolgálóhitelesítési felhasználó nem része az Azure AD-nek, a kiszolgálóhoz való Csatlakozás Azure AD-hitelesítéssel meghiúsul.

Ha később el szeretne távolítani egy rendszergazdát, a rendszergazdai oldal **Active Directory válassza** a Rendszergazda eltávolítása, majd a Mentés **lehetőséget.** 

### <a name="powershell-for-sql-database-and-azure-synapse"></a>PowerShell SQL Database és Azure Synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell-parancsmagok futtatásához telepítenie Azure PowerShell és futtatnia. Részletes információk: [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása). Azure AD-rendszergazda üzembe végrehajtásához hajtsa végre a következő Azure PowerShell parancsokat:

- Connect-AzAccount
- Select-AzSubscription

Azure AD-rendszergazdai jogosultságok építéséhez és kezeléséhez használt parancsmagok SQL Database és Azure Synapse:

| Parancsmag neve | Description |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Kioszt egy Azure Active Directory rendszergazdát a kiszolgáló üzemeltető SQL Database vagy Azure Synapse. (Az aktuális előfizetésből kell, hogy legyen) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Eltávolít egy Azure Active Directory rendszergazdát a kiszolgáló üzemeltetési SQL Database vagy Azure Synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Információkat ad vissza egy Azure Active Directory rendszergazda számára, aki jelenleg konfigurálva van a kiszolgáló üzemeltetési SQL Database vagy Azure Synapse. |

A get-help PowerShell-paranccsal további információkat láthat az egyes parancsokról. Például: `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

A következő szkript egy **DBA_Group** (objektumazonosító) nevű Azure AD-rendszergazdai csoportot hoz létre a demo_server-23 nevű erőforráscsoportban található `40b79501-b343-44ed-9ce7-da4c8cc7353f`  **demo_server-kiszolgáló számára:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

A **DisplayName** bemeneti paraméter az Azure AD megjelenítendő nevét vagy az egyszerű felhasználónevet fogadja el. Például: ``DisplayName="John Smith"`` és ``DisplayName="johns@contoso.com"``. Az Azure AD-csoportok esetében csak az Azure AD megjelenítendő neve támogatott.

> [!NOTE]
> A Azure PowerShell nem ```Set-AzSqlServerActiveDirectoryAdministrator``` akadályozza meg, hogy Azure AD-rendszergazdákat létesítsen a nem támogatott felhasználók számára. Kiépíthet nem támogatott felhasználót, de nem tud csatlakozni egy adatbázishoz.

Az alábbi példa a választható **ObjectID-t használja:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Az Azure AD **ObjectID** azonosítóra akkor van szükség, ha a **DisplayName nem** egyedi. Az **ObjectID és** **a DisplayName** értékek lekérését a klasszikus Azure portál Active Directory szakaszában, majd egy felhasználó vagy csoport tulajdonságainak megtekintésével.

Az alábbi példa a kiszolgáló aktuális Azure AD-rendszergazdájának adatait adja vissza:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Az alábbi példa eltávolít egy Azure AD-rendszergazdát:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure AD-rendszergazdát az alábbi PARANCSSORi felületi parancsok hívása segítségével létesíthet:

| Parancs | Leírás |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) | Kioszt egy Azure Active Directory rendszergazdát a kiszolgáló üzemeltetési SQL Database vagy Azure Synapse. (Az aktuális előfizetésből kell, hogy legyen) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) | Eltávolít egy Azure Active Directory rendszergazdát a kiszolgáló üzemeltető SQL Database vagy Azure Synapse. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) | Információkat ad vissza Azure Active Directory aktuálisan a kiszolgáló üzemeltető kiszolgálójához konfigurált SQL Database vagy Azure Synapse. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) | Frissíti Active Directory kiszolgáló rendszergazdáját a SQL Database vagy Azure Synapse. |

A CLI-parancsokkal kapcsolatos további információkért lásd: [az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> A REST API-Azure Active Directory is kiépíthet egy rendszergazdai jogosultságot. További információ: [Service Management REST API Reference and Operations for Azure SQL Database Operations for Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Az ügyfélszámítógépek konfigurálása

Minden olyan ügyfélszámítógépen, amelyről az alkalmazásai vagy felhasználói Azure AD SQL Database vagy Azure Synapse csatlakoznak, a következő szoftvereket kell telepítenie:

- .NET-keretrendszer 4.6-os vagy újabb [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) a-ból.
- Azure Active Directory authentication library for SQL Server (*ADAL.DLL*). Az alábbi letöltési hivatkozások segítségével telepítheti a legújabb SSMS-t, ODBC-t és OLE DB-illesztőprogramot, amely a *ADAL.DLL* tartalmazza.
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC-illesztő 17 SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [OLE DB 18-as illesztő SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

Ezeket a követelményeket a következővel teljesíti:

- A SQL Server Management Studio vagy [](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Data Tools legújabb [](/sql/ssdt/download-sql-server-data-tools-ssdt) verziójának telepítése megfelel .NET-keretrendszer 4.6-os verziójának.
  - Az SSMS telepíti a *ADAL.DLL.*
  - Az SSDT telepíti aADAL.DLLamd64 *verzióját.*
  - A Visual Studio Visual Studio legújabb [](https://www.visualstudio.com/downloads/download-visual-studio-vs) verziója megfelel az .NET-keretrendszer 4.6 követelményeinek, de nem telepíti a szükséges amd64 *ADAL.DLL.*

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Az Azure AD-identitásokhoz hozzárendelt, tartalmazott felhasználók létrehozása

Mivel SQL Managed Instance támogatja az Azure AD-kiszolgálóneveket (bejelentkezéseket), a tartalmazott adatbázis-felhasználók használata nem szükséges. Az Azure AD kiszolgálói rendszerbiztonsági tagok (bejelentkezések) lehetővé teszik bejelentkezések létrehozását Azure AD-felhasználókból, -csoportokból vagy -alkalmazásokból. Ez azt jelenti, hogy a fiókkal való SQL Managed Instance Azure AD-kiszolgálói bejelentkezéssel és nem tartalmazottadatbázis-felhasználóval. További információkért lásd a SQL Managed Instance [áttekintését.](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration) Az Azure AD-kiszolgálónév (bejelentkezések) létrehozásának szintaxisát lásd: [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

Azonban a Azure Active Directory és SQL Database Azure Synapse azure AD-identitáson alapuló tartalmazottadatbázis-felhasználókra van szükség. A tartalmazottadatbázis-felhasználó nem rendelkezik bejelentkezéssel a master adatbázisban, és az adatbázishoz társított identitáshoz van leképezve az Azure AD-ban. Az Azure AD-identitás lehet egyéni felhasználói fiók vagy csoport. További információ a tartalmazottadatbázis-felhasználókról: [Tartalmazottadatbázis-felhasználók – Az adatbázis hordozható.](/sql/relational-databases/security/contained-database-users-making-your-database-portable)

> [!NOTE]
> Az adatbázis-felhasználók (a rendszergazdák kivételével) nem lehet létrehozni a Azure Portal. Az Azure-szerepköröket a rendszer nem propagálja az adatbázisba a SQL Database, SQL Managed Instance vagy Azure Synapse. Az Azure-szerepkörök az Azure-erőforrások kezelésére szolgálnak, és nem vonatkoznak az adatbázis-engedélyekre. A SQL Server  szerepkör például nem biztosít hozzáférést a SQL Database, a SQL Managed Instance vagy Azure Synapse. A hozzáférési engedélyt közvetlenül az adatbázisban kell megadni Transact-SQL-utasítások használatával.

> [!WARNING]
> A T-SQL és utasításokban nem támogatottak olyan speciális karakterek, mint a kettőspont vagy az ampersand, ha felhasználónévként szerepelnek `:` `&` a T-SQL-fájlban. `CREATE LOGIN` `CREATE USER`

Azure AD-alapú tartalmazottadatbázis-felhasználó (az adatbázist felügyelő kiszolgálói rendszergazda helyett) létrehozásához csatlakozzon az adatbázishoz egy Azure AD-identitással, legalább **ALTER ANY USER** engedéllyel rendelkező felhasználóként. Ezután használja a következő Transact-SQL szintaxist:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* lehet egy Azure AD-felhasználó egyszerű felhasználóneve vagy egy Azure AD-csoport megjelenítendő neve.

**Példák:** Az Azure AD összevont vagy felügyelt tartományi felhasználónak megfelelő tartalmazottadatbázis-felhasználó létrehozása:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Egy Azure AD-t vagy összevont tartományi csoportot képviselő tartalmazottadatbázis-felhasználó létrehozásához adja meg egy biztonsági csoport megjelenítendő nevét:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Olyan tartalmazottadatbázis-felhasználó létrehozása, amely egy Azure AD-jogkivonattal összekapcsolt alkalmazást képvisel:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Ehhez a parancshoz az SQL-nek hozzá kell férni az Azure AD-hez (a "külső szolgáltatóhoz") a bejelentkezett felhasználó nevében. Előfordulhatnak olyan körülmények, amelyek miatt az Azure AD kivételt ad vissza az SQL-nek. Ezekben az esetekben a felhasználó a 33134-es SQL-hibát fogja látni, amelynek tartalmaznia kell az Azure AD-specifikus hibaüzenetet. A legtöbb esetben a hibaüzenet szerint a hozzáférés megtagadva van, vagy hogy a felhasználónak regisztrálnia kell az MFA-ban az erőforrás eléréséhez, vagy hogy a külső alkalmazások közötti hozzáférést előzetes hitelesítéssel kell kezelni. Az első két esetben a problémát általában a felhasználó Azure AD-bérlője által beállított feltételes hozzáférési szabályzatok okják: megakadályozzák, hogy a felhasználó hozzáférjen a külső szolgáltatóhoz. A probléma megoldásához frissítse a feltételes hozzáférési szabályzatokat, hogy engedélyezze a hozzáférést a "00000002-0000-0000-c000-0000000000000" alkalmazáshoz (az Azure AD Graph API alkalmazásazonosítója). Abban az esetben, ha a hiba szerint a külső alkalmazások közötti hozzáférést előauthorizálással kell kezelni, a probléma oka az, hogy a felhasználó szolgáltatásnévként van bejelentkezve. A parancsnak sikeresnek kell lennie, ha ehelyett egy felhasználó hajtotta végre.

> [!TIP]
> Az Azure-előfizetéséhez társított Azure Active Directory nem hozhat létre közvetlenül felhasználót Azure Active Directory fiókból. A társított Active Directory-ban importált más Active Directories (más néven külső felhasználók) tagjai azonban hozzáadhatók Active Directory-csoporthoz a bérlői Active Directory. Ha létrehoz egy tartalmazottadatbázis-felhasználót az adott AD-csoporthoz, a külső Active Directory felhasználók hozzáférhetnek a SQL Database.

További információ a tartalmazottadatbázis-felhasználók identitások Azure Active Directory létrehozásáról: [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> A kiszolgáló Azure Active Directory eltávolításával megakadályozható, hogy bármely Azure AD-hitelesítést használó felhasználó csatlakozzon a kiszolgálóhoz. Szükség esetén a nem használható Azure AD-felhasználókat egy rendszergazda manuálisan SQL Database el.

> [!NOTE]
> Ha a **Kapcsolati időkorlát lejárt,** előfordulhat, hogy a kapcsolati sztring paraméterét false (hamis) `TransparentNetworkIPResolution` értékre kell állítania. További információ: Kapcsolat időtúllépési problémája a [4.6.1-es .NET-keretrendszer – TransparentNetworkIPResolution megoldással.](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution)

Adatbázis-felhasználó létrehozásakor a felhasználó megkapja a **CONNECT** engedélyt, és a NYILVÁNOS szerepkör tagjaként csatlakozhat az **adatbázishoz.** Kezdetben csak a felhasználó számára elérhető engedélyek a **NYILVÁNOS** szerepkörnek megadott engedélyek, vagy bármely olyan Azure AD-csoport számára megadott engedélyek, amelyek tagjai. Miután kiépít egy Azure AD-alapú tartalmazottadatbázis-felhasználót, további engedélyeket adhat a felhasználónak, ugyanúgy, mint bármely más típusú felhasználónak. Általában engedélyeket kell adni az adatbázis-szerepköröknek, és felhasználókat kell hozzáadni a szerepkörökhöz. További információ: [Az adatbázismotor engedélyeinek alapjai.](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) A speciális szerepkörökkel kapcsolatos további SQL Database az [Adatbázisok](logins-create-manage.md)és bejelentkezések kezelése a Azure SQL Database.
A felügyelt tartományba külső felhasználóként importált összevont tartományi felhasználói fióknak a felügyelt tartomány identitását kell használnia.

> [!NOTE]
> Az Azure AD-felhasználókat az adatbázis metaadatai E (EXTERNAL_USER) típusúként jelölik meg, X (EXTERNAL_GROUPS) típusú csoportokhoz. További információ: [sys.database_principals.](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Csatlakozás az adatbázishoz SSMS vagy SSDT használatával  

Az Azure AD-rendszergazda megfelelő beállításának megerősítéséhez csatlakozzon a **master** adatbázishoz az Azure AD rendszergazdai fiókjával.
Egy Azure AD-alapú tartalmazottadatbázis-felhasználó (az adatbázist felügyelő kiszolgálói rendszergazda helyett) üzembe építéséhez csatlakozzon az adatbázishoz egy Azure AD-identitással, amely hozzáféréssel rendelkezik az adatbázishoz.

> [!IMPORTANT]
> Az Azure Active Directory hitelesítés támogatása a [2016-os SQL Server 2016-os](/sql/ssms/download-sql-server-management-studio-ssms) Management Studio és SQL Server Data Tools 2015-Visual Studio érhető el. [](/sql/ssdt/download-sql-server-data-tools-ssdt) Az SSMS 2016. augusztusi kiadása az Active Directory Univerzális hitelesítés támogatását is tartalmazza, amely lehetővé teszi a rendszergazdák számára, hogy telefonhívással, szöveges üzenettel, PIN-kódot tartalmazó intelligens kártyákkal vagy mobilalkalmazás-értesítésekkel megkövetelht többtényezős hitelesítést követelnek meg.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Csatlakozás Azure AD-identitással SSMS vagy SSDT használatával

Az alábbi eljárások azt mutatják be, hogyan csatlakozhat SQL Database azure AD-identitással a SQL Server Management Studio vagy SQL Server Database Tools használatával.

### <a name="active-directory-integrated-authentication"></a>Active Directory hitelesítés

Akkor használja ezt a módszert, ha összevont tartományból származó Azure Active Directory hitelesítő adataival van bejelentkezve a Windowsba, vagy egy felügyelt tartományból, amely zökkenőmentes egyszeri bejelentkezésre van konfigurálva az átmenő és jelszó-kivonatos hitelesítéshez. További információkért lásd a [Azure Active Directory egyszeri bejelentkezést.](../../active-directory/hybrid/how-to-connect-sso.md)

1. Indítsa Management Studio vagy Data Tools eszközt, majd a Connect **to Server** (vagy  Connect **to Database Engine)**(Kapcsolódás a kiszolgálóhoz (vagy az adatbázismotorhoz) párbeszédpanel Hitelesítés párbeszédpanelén válassza a Azure Active Directory – **Integrált lehetőséget.** Nincs szükség jelszóra, vagy meg lehet adni, mert a kapcsolathoz a meglévő hitelesítő adatai jelennek meg.

   ![Válassza az AD Integrated Authentication (AD integrált hitelesítés) lehetőséget][11]

2. Válassza a **Beállítások** gombot, majd a  Kapcsolat tulajdonságai lapon a Csatlakozás adatbázishoz mezőbe írja be annak a felhasználói adatbázisnak a nevét, amelyhez csatlakozni szeretne.  További információért tekintse meg [a Multi-factor Azure AD-hitelesítést](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) az SSMS 17.x és 18.x kapcsolati tulajdonságai közötti különbségekről.

   ![Az adatbázis nevének kiválasztása][13]

### <a name="active-directory-password-authentication"></a>Active Directory hitelesítésének engedélyezése

Ezt a módszert akkor használja, ha azure AD egyszerű névvel csatlakozik az Azure AD felügyelt tartományával. Összevont fiókokhoz is használható a tartományhoz való hozzáférés nélkül, például távolról történő munka esetén.

Ezzel a módszerrel hitelesítheti magát az adatbázisban a SQL Database vagy a SQL Managed Instance csak az Azure AD felhőalapú identitást használó felhasználóival vagy az Azure AD hibrid identitásokat használó felhasználókkal. Ez a módszer azokat a felhasználókat támogatja, akik Windows-hitelesítő adataikat szeretnék használni, de a helyi gépük nincs a tartományhoz csatlakozva (például távelérés használatával). Ebben az esetben a Windows-felhasználó meg tudja jelezni a tartományi fiókját és jelszavát, és hitelesíthet az adatbázisban a SQL Database, a SQL Managed Instance vagy Azure Synapse.

1. Indítsa Management Studio vagy Data Tools eszközt, majd a Connect **to Server** (vagy  Connect **to Database Engine)**(Kapcsolódás a kiszolgálóhoz (vagy az adatbázismotorhoz) párbeszédpanel Hitelesítés párbeszédpanelén válassza a Azure Active Directory - Password (Jelszó) **lehetőséget.**

2. A Felhasználónév **mezőbe írja** be Azure Active Directory felhasználónevét a következő **formátumban: \@ domain.com.** A felhasználóneveknek egy fióknak kell Azure Active Directory vagy egy felügyelt vagy összevont tartományból származó fióknak kell Azure Active Directory.

3. A **Jelszó mezőbe** írja be a felhasználói jelszavát a Azure Active Directory-fiókhoz vagy a felügyelt/összevont tartományi fiókhoz.

    ![Válassza az AD password authentication (AD-jelszó-hitelesítés) lehetőséget][12]

4. Válassza **a Beállítások** gombot, majd a  Kapcsolat tulajdonságai lapon a Csatlakozás adatbázishoz mezőbe írja be annak a felhasználói adatbázisnak a nevét, amelyhez csatlakozni szeretne.  (Lásd az előző beállításban látható ábrát.)

### <a name="active-directory-interactive-authentication"></a>Active Directory hitelesítés engedélyezése

Ezzel a módszerrel interaktív hitelesítést használhat többtényezős hitelesítéssel (MFA) és anélkül is, ha a jelszót interaktívan kéri le. Ezzel a módszerrel hitelesítheti magát az adatbázisban az SQL Database, a SQL Managed Instance és az Azure Synapse csak az Azure AD felhőalapú identitást használó felhasználói vagy azok számára, akik hibrid Azure AD-identitásokat használnak.

További információ: [Többtényezős Azure AD-hitelesítés használata SQL Database és Azure Synapse (SSMS-támogatás MFA-hoz).](authentication-mfa-ssms-overview.md)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Azure AD-identitás használata ügyfélalkalmazásból való csatlakozáshoz

Az alábbi eljárások azt mutatják be, hogyan csatlakozhat egy SQL Database azure AD-identitással egy ügyfélalkalmazásból.

### <a name="active-directory-integrated-authentication"></a>Active Directory hitelesítés

Integrált Windows-hitelesítés használata esetén a tartomány Active Directory-ját össze kell állítani az Azure Active Directory-val, vagy olyan felügyelt tartománynak kell lennie, amely zökkenőmentes egyszeri bejelentkezésre van konfigurálva az átmenő vagy jelszó-kivonatos hitelesítéshez. További információkért lásd a [Azure Active Directory egyszeri bejelentkezést.](../../active-directory/hybrid/how-to-connect-sso.md)

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) az integrált Windows-hitelesítéshez nem támogatott az átmenő és jelszó-kivonatos hitelesítéshez való zökkenőmentes egyszeri bejelentkezéshez.

Az adatbázishoz csatlakozó ügyfélalkalmazásnak (vagy szolgáltatásnak) egy tartományhoz csatlakozó gépen kell futnia a felhasználó tartományi hitelesítő adataival.

Ha integrált hitelesítéssel és Azure AD-identitással szeretne csatlakozni egy adatbázishoz, az adatbázis-kapcsolati sztringben a Authentication kulcsszót a következőre kell beállítani: `Active Directory Integrated` . A következő C#-kódminta az ADO .NET-et használja.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A kapcsolati sztring `Integrated Security=True` kulcsszó nem támogatott a Azure SQL Database. ODBC-kapcsolat létesítéskor el kell távolítania a szóközöket, és a Hitelesítést az "ActiveDirectoryIntegrated" beállításra kell állítania.

### <a name="active-directory-password-authentication"></a>Active Directory hitelesítésének engedélyezése

Ha egy adatbázishoz az Azure AD csak felhőalapú identitás felhasználói fiókjait vagy az Azure AD hibrid identitásokat használó felhasználókkal szeretne csatlakozni, a Hitelesítési kulcsszót a következőre kell beállítani: `Active Directory Password` . A kapcsolati sztringnek tartalmaznia kell a Felhasználói azonosító/UID és a Jelszó/PWD kulcsszavakat és értékeket. A következő C#-kódminta az ADO .NET-et használja.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

További információ az Azure AD hitelesítési módszereiről az [Azure AD-hitelesítés GitHub-bemutatójában](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)elérhető bemutatókódminták segítségével.

## <a name="azure-ad-token"></a>Azure AD-jogkivonat

Ez a hitelesítési módszer lehetővé teszi a középső rétegbeli szolgáltatások számára, hogy [JSON webtokeneket (JWT)](../../active-directory/develop/id-tokens.md) szerezzenek be az adatbázishoz való csatlakozáshoz az SQL Database-ban, a SQL Managed Instance-ban vagy a Azure Synapse-ban egy jogkivonat az Azure AD-ból való beszerzésével. Ez a módszer különböző alkalmazás-forgatókönyveket tesz lehetővé, például szolgáltatás-identitásokat, szolgáltatásnévket és tanúsítványalapú hitelesítést használó alkalmazásokat. Az Azure AD-jogkivonat-hitelesítés használatának négy alapvető lépését kell végrehajtania:

1. Regisztrálja alkalmazását a Azure Active Directory, és szerezze be a kód ügyfél-azonosítóját.
2. Hozzon létre egy adatbázis-felhasználót, aki az alkalmazást képviseli. (A 6. lépésben már befejeződött.)
3. Hozzon létre egy tanúsítványt az alkalmazást az ügyfélszámítógépen.
4. Adja hozzá a tanúsítványt kulcsként az alkalmazáshoz.

Példa kapcsolati sztringre:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

További információ: SQL Server [blog.](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth) További információ a tanúsítvány hozzáadásáról: [A](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)tanúsítványalapú hitelesítés első lépések a Azure Active Directory.

### <a name="sqlcmd"></a>sqlcmd

A következő utasítások az sqlcmd 13.1-es verziójával csatlakoznak, amely a [letöltőközpontból érhető el.](https://www.microsoft.com/download/details.aspx?id=53591)

> [!NOTE]
> `sqlcmd` A parancs `-G` nem működik a rendszer-identitásokkal, és egyszerű felhasználó bejelentkezést igényel.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Azure AD-hitelesítés hibaelhárítása

Az Azure AD-hitelesítéssel kapcsolatos hibaelhárítási útmutatót a következő blogban talál: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Következő lépések

- A bejelentkezések, felhasználók, adatbázis-szerepkörök és engedélyek áttekintését lásd a SQL Database, felhasználók, adatbázis-szerepkörök és felhasználói fiókok [témakörben.](logins-create-manage.md)
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](/sql/relational-databases/security/authentication-access/principals-database-engine).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](/sql/relational-databases/security/authentication-access/database-level-roles).
- További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](firewall-configure.md).
- Az Azure AD-vendégfelhasználók Azure AD-rendszergazdaként való beállítását lásd: Azure AD-vendégfelhasználók létrehozása és [Beállítása Azure AD-rendszergazdaként.](authentication-aad-guest-users.md)
- További információ a szolgáltatásnév szolgáltatásnévvel való Azure SQL: Azure AD-felhasználók létrehozása [Azure AD-alkalmazásokkal](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
