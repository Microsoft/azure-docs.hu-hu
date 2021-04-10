---
title: Az Azure Active Directory-fiókon alapuló hitelesítés konfigurálása
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Megtudhatja, hogyan csatlakozhat a SQL Databasehoz, az SQL felügyelt példányához és az Azure szinapszis Analyticshez Azure Active Directory hitelesítéssel, az Azure AD konfigurálása után.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: 9e7b337d4358f9685d683c308d6df9110607207a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643416"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL-sel

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre és tölthet fel egy Azure Active Directory (Azure AD-példányt), majd hogyan használhatja az Azure AD-t a [Azure SQL Database](sql-database-paas-overview.md), az [Azure SQL felügyelt példányaival](../managed-instance/sql-managed-instance-paas-overview.md)és az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)szolgáltatással. Az áttekintést lásd: [Azure Active Directory hitelesítés](authentication-aad-overview.md).

## <a name="azure-ad-authentication-methods"></a>Azure AD-hitelesítési módszerek

Az Azure AD-hitelesítés a következő hitelesítési módszereket támogatja:

- Azure AD-beli kizárólag Felhőbeli identitások
- Az Azure AD hibrid identitások, amelyek támogatják a következőket:
  - Felhőalapú hitelesítés két lehetőséggel, zökkenőmentes egyszeri bejelentkezéssel (SSO)
    - Azure AD jelszó-kivonatos hitelesítés
    - Azure AD átmenő hitelesítés
  - Összevont hitelesítés

További információ az Azure AD-hitelesítési módszerekről, valamint az közül választhat: [válassza ki a megfelelő hitelesítési módszert a Azure Active Directory Hybrid Identity megoldáshoz](../../active-directory/hybrid/choose-ad-authn.md).

Az Azure AD hibrid identitásokkal, beállítással és szinkronizálással kapcsolatos további információkért lásd:

- Jelszó-kivonatolási hitelesítés – [jelszó-kivonatolási szinkronizálás megvalósítása Azure ad Connect szinkronizálással](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Átmenő hitelesítés – Azure Active Directory átmenő [hitelesítés](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Összevont hitelesítés – [Active Directory összevonási szolgáltatások (AD FS) üzembe helyezése az Azure-ban és a](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) [Azure ad Connect és az összevonás](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Azure AD-példány létrehozása és feltöltése

Hozzon létre egy Azure AD-példányt, és töltse fel a felhasználókat és a csoportokat. Az Azure AD lehet a kezdeti Azure AD által felügyelt tartomány. Az Azure AD az Azure AD-vel összevont helyszíni Active Directory Domain Services is lehet.

További információk a következő témakörökben találhatók: [Helyszíni identitások integrálása az Azure Active Directoryval](../../active-directory/hybrid/whatis-hybrid-identity.md), [Saját tartománynév hozzáadása az Azure AD-hez](../../active-directory/fundamentals/add-custom-domain.md), [A Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonást](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Az Azure AD-címtár felügyelete](../../active-directory/fundamentals/active-directory-whatis.md), [Az Azure AD kezelése Windows PowerShell használatával](/powershell/azure/) és [Hibrid identitás – szükséges portok és protokollok](../../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz

1. Rendelje hozzá az Azure-előfizetést a Azure Active Directoryhoz, hogy a címtárban az adatbázist futtató Azure-előfizetés számára megbízható könyvtárat biztosít. Részletekért lásd: az [Azure-előfizetés hozzárendelése vagy hozzáadása a Azure Active Directory bérlőhöz](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. A Azure Portal Directory kapcsolójának használatával váltson át a tartományhoz társított előfizetésre.

   > [!IMPORTANT]
   > Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. Ez az előfizetés és a címtár közötti bizalmi kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn, amelyek inkább az előfizetések gyermekerőforrásainak számítanak. Ha egy előfizetés lejár, akkor az előfizetéssel társított ilyen egyéb erőforrások hozzáférése is lejár. De a címtár az Azure-ban elérhető marad, és más előfizetéseket társíthat ezzel a címtárral, és folytathatja a címtár felhasználóinak kezelését. Az erőforrásokkal kapcsolatos további információkért lásd: [az erőforrás-hozzáférés ismertetése az Azure-ban](../../active-directory/external-identities/add-users-administrator.md). További információ erről a megbízható kapcsolatról: [Azure-előfizetés hozzárendelése vagy hozzáadása Azure Active Directoryhoz](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Azure AD-rendszergazda SQL Database-kiszolgálóval

Az Azure összes [kiszolgálója](logical-servers.md) (amely SQL Database vagy az Azure szinapszis) a teljes kiszolgáló rendszergazdájának egyetlen kiszolgálói rendszergazdai fiókkal kezdődik. Hozzon létre egy második rendszergazdai fiókot Azure AD-fiókként. Ez a rendszerbiztonsági tag egy tárolt adatbázis-felhasználóként jön létre a kiszolgáló Master adatbázisában. A rendszergazdai fiókok a **db_owner** szerepkör tagjai minden felhasználói adatbázisban, és az egyes felhasználói adatbázisokat **dbo** -felhasználóként adja meg. A rendszergazdai fiókokkal kapcsolatos további információkért lásd: [adatbázisok és bejelentkezések kezelése](logins-create-manage.md).

Ha Geo-replikációval Azure Active Directory használ, a Azure Active Directory rendszergazdát mind az elsődleges, mind a másodlagos kiszolgálókhoz be kell állítani. Ha a kiszolgáló nem rendelkezik Azure Active Directory-rendszergazdával, akkor Azure Active Directory bejelentkezések és a felhasználók a `Cannot connect` kiszolgáló hibáját kapják meg.

> [!NOTE]
> Azok a felhasználók, akik nem egy Azure ad-fiókon (például a kiszolgáló rendszergazdai fiókján) alapulnak, nem hozhatnak létre Azure AD-alapú felhasználókat, mert nem rendelkeznek engedéllyel a javasolt adatbázis-felhasználók ellenőrzéséhez az Azure AD-vel.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Az Azure AD-rendszergazda kiépítése (felügyelt SQL-példány)

> [!IMPORTANT]
> Csak akkor hajtsa végre ezeket a lépéseket, ha egy felügyelt Azure SQL-példányt épít ki. Ezt a műveletet csak a globális rendszergazda vagy egy emelt szintű szerepkörű rendszergazda hajthatja végre az Azure AD-ben.
>
> A **nyilvános előzetes** verzióban a címtár- **olvasók** szerepkört hozzárendelheti egy CSOPORThoz az Azure ad-ben. A csoport tulajdonosai ezután hozzáadhatják a felügyelt példány identitását a csoport tagjaként, ami lehetővé teszi az Azure AD-rendszergazdák kiépítését az SQL felügyelt példányához. A szolgáltatással kapcsolatos további információkért lásd: az [Azure SQL Azure Active Directory Directory-olvasók szerepköre](authentication-aad-directory-readers-role.md).

Az SQL felügyelt példányának engedélyekkel kell rendelkeznie az Azure AD olvasásához, hogy sikeresen olyan feladatokat lehessen végrehajtani, mint például a felhasználók hitelesítése a biztonsági csoporttagság vagy az új felhasználók létrehozása révén. Ahhoz, hogy működjön, meg kell adnia a felügyelt SQL-példány engedélyét az Azure AD olvasásához. Ezt a Azure Portal vagy a PowerShell használatával teheti meg.

### <a name="azure-portal"></a>Azure Portal

Az SQL felügyelt példány Azure AD olvasási engedélyének a Azure Portal használatával történő megadásához jelentkezzen be globális rendszergazdaként az Azure AD-ben, és kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)a jobb felső sarokban válassza ki a megfelelő aktív könyvtárak legördülő listájából a kívánt kapcsolatokat.

2. Válassza ki a megfelelő Active Directory alapértelmezett Azure AD-ként.

   Ez a lépés összekapcsolja az Active Directoryhoz társított előfizetést az SQL felügyelt példányához, és gondoskodik arról, hogy ugyanazt az előfizetést használja az Azure AD-példányhoz és az SQL felügyelt példányához is.

3. Navigáljon az Azure AD-integrációhoz használni kívánt SQL felügyelt példányhoz.

   ![Képernyőkép a Azure Portal a kiválasztott SQL felügyelt példányhoz tartozó Active Directory rendszergazdai oldal megnyitásáról.](./media/authentication-aad-configure/aad.png)

4. Válassza ki a Active Directory felügyeleti oldal tetején található szalagcímet, és adja meg az engedélyt az aktuális felhasználónak.

    ![Képernyőkép az SQL felügyelt példány engedélyeinek megadásához a Active Directory eléréséhez. Az engedélyek megadása gomb van kiválasztva.](./media/authentication-aad-configure/grant-permissions.png)

5. A művelet sikeres végrehajtása után a következő értesítés jelenik meg a jobb felső sarokban:

    ![Képernyőkép arról, hogy az Active Directory olvasási engedélyei sikeresen frissítve lettek a felügyelt példányra vonatkozóan.](./media/authentication-aad-configure/success.png)

6. Most kiválaszthatja az SQL felügyelt példányához tartozó Azure AD-rendszergazdát. Ehhez a Active Directory admin lapon válassza a **felügyeleti parancs beállítása** lehetőséget.

    ![A kiválasztott SQL felügyelt példányhoz tartozó Active Directory felügyeleti lapon Kiemelt rendszergazdai parancs beállítása.](./media/authentication-aad-configure/set-admin.png)

7. Az Azure AD-rendszergazda lapon keressen rá egy felhasználóra, válassza ki a felhasználót vagy csoportot, majd válassza a **kiválasztás** lehetőséget.

   A Active Directory felügyeleti oldal megjeleníti a Active Directory összes tagját és csoportját. A nem kiválasztható felhasználók vagy csoportok nem választhatók ki, mert nem támogatottak az Azure AD-rendszergazdák. Tekintse meg a támogatott rendszergazdák listáját az [Azure ad-funkciókban és korlátozásokban](authentication-aad-overview.md#azure-ad-features-and-limitations). Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) csak a Azure Portalra vonatkozik, és nincs propagálva SQL Database, SQL felügyelt példány vagy Azure szinapszis számára.

    ![Azure Active Directory adminisztrátor hozzáadása](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. A Active Directory-rendszergazda lap tetején válassza a **Mentés** lehetőséget.

    ![Képernyőkép a Active Directory felügyeleti oldalról a rendszergazda beállítása és a rendszergazda eltávolítása gomb mellett található mentés gombbal.](./media/authentication-aad-configure/save.png)

    A rendszergazda módosításának folyamata több percet is igénybe vehet. Ezután megjelenik az új rendszergazda a Active Directory admin mezőben.

Miután kiépített egy Azure AD-rendszergazdát az SQL felügyelt példányához, megkezdheti az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) létrehozását a [bejelentkezési szintaxis létrehozásával](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) . További információ: [SQL felügyelt példányának áttekintése](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration).

> [!TIP]
> Ha később el szeretné távolítani a rendszergazdát, a Active Directory-rendszergazda lap tetején válassza a **rendszergazda eltávolítása** lehetőséget, majd kattintson a **Mentés** gombra.

### <a name="powershell"></a>PowerShell

Ha az SQL felügyelt példányát Azure AD olvasási engedéllyel szeretné megadni a PowerShell használatával, futtassa a következő parancsfájlt:

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

### <a name="powershell-for-sql-managed-instance"></a>PowerShell a felügyelt SQL-példányhoz

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell-parancsmagok futtatásához Azure PowerShell kell telepítenie és futnia. Részletes információk: [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása).

> [!IMPORTANT]
> Az Azure SQL felügyelt példánya továbbra is támogatja a PowerShell Azure Resource Manager (RM) modult, de a jövőbeli fejlesztés az az. SQL modulhoz készült. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

Az Azure AD-rendszergazda kiépítéséhez hajtsa végre a következő Azure PowerShell parancsokat:

- Connect-AzAccount
- Select-AzSubscription

Az SQL felügyelt példányához az Azure AD-rendszergazda üzembe helyezéséhez és kezeléséhez használt parancsmagok az alábbi táblázatban láthatók:

| Parancsmag neve | Leírás |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Kiépít egy Azure AD-rendszergazdát a jelenlegi előfizetésben található SQL felügyelt példányhoz. (Az aktuális előfizetésből kell származnia)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Eltávolít egy Azure AD-rendszergazdát az SQL által felügyelt példányhoz az aktuális előfizetésben. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Egy Azure AD-rendszergazdával kapcsolatos információkat ad vissza az aktuális előfizetésben található SQL felügyelt példányra vonatkozóan.|

A következő parancs információt kér egy Azure AD-rendszergazdáról egy ManagedInstance01 nevű SQL felügyelt példányról, amely egy ResourceGroup01 nevű erőforráscsoporthoz van társítva.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

A következő parancs egy Adattervezők nevű Azure AD-rendszergazdai csoportot foglal le a ManagedInstance01 nevű SQL felügyelt példányhoz. Ez a kiszolgáló az erőforráscsoport ResourceGroup01 van társítva.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

A következő parancs eltávolítja az Azure AD-rendszergazdát az erőforráscsoport ResourceGroup01 társított ManagedInstanceName01 nevű SQL felügyelt példányhoz.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az SQL felügyelt példányához az alábbi CLI-parancsok meghívásával is kiépítheti az Azure AD-rendszergazdát:

| Parancs | Leírás |
| --- | --- |
|[az SQL mi ad-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Kiépíti az SQL felügyelt példányának Azure Active Directory rendszergazdáját (az aktuális előfizetésből kell származnia). |
|[az SQL mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Eltávolít egy Azure Active Directory rendszergazdát az SQL felügyelt példányához. |
|[az SQL mi ad-admin List](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | A felügyelt SQL-példányhoz jelenleg konfigurált Azure Active Directory-rendszergazda adatait adja vissza. |
|[az SQL mi ad-Admin Update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | A felügyelt SQL-példány Active Directory-rendszergazdájának frissítése. |

További információ a CLI-parancsokról: [az SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Az Azure AD-rendszergazda kiépítése (SQL Database)

> [!IMPORTANT]
> Csak akkor hajtsa végre ezeket a lépéseket, ha SQL Database vagy az Azure szinapszis számára kiépít egy [kiszolgálót](logical-servers.md) .

A következő két eljárás bemutatja, hogyan helyezhet üzembe egy Azure Active Directory-rendszergazdát a kiszolgálón a Azure Portal és a PowerShell használatával.

### <a name="azure-portal"></a>Azure Portal

1. A [Azure Portal](https://portal.azure.com/)jobb felső sarokban válassza ki a kapcsolódást a lehetséges aktív könyvtárak listájának legördülő listához. Válassza ki a megfelelő Active Directory alapértelmezett Azure AD-ként. Ez a lépés összekapcsolja az előfizetéshez kapcsolódó Active Directory a kiszolgálóval, és gondoskodik arról, hogy ugyanazt az előfizetést használja az Azure AD és a kiszolgáló számára is.

2. Keresse meg és válassza ki az **SQL Servert**.

    ![SQL-kiszolgálók keresése és kiválasztása](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Ezen az oldalon az **SQL-kiszolgálók** kiválasztása előtt kiválaszthatja a név melletti **csillagot** a kategória *kedvencéhez* , és hozzáadhat **SQL-kiszolgálókat** a bal oldali navigációs sávon.

3. A **SQL Server** lapon válassza a **Active Directory rendszergazda** lehetőséget.

4. A **Active Directory-rendszergazda** lapon válassza a **rendszergazda beállítása** lehetőséget.

    ![SQL Servers set Active Directory admin](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. A rendszergazda **hozzáadása** lapon keressen rá egy felhasználóra, válassza ki a felhasználót vagy csoportot, majd válassza a **kiválasztás** lehetőséget. (A Active Directory felügyeleti oldal megjeleníti a Active Directory összes tagját és csoportját. A nem kiválasztható felhasználók vagy csoportok nem választhatók ki, mert nem támogatottak az Azure AD-rendszergazdák. (Tekintse meg a támogatott rendszergazdák listáját az **Azure ad-szolgáltatások és-korlátozások** című szakaszban [Azure Active Directory hitelesítés használata a SQL Database vagy az Azure szinapszis használatával történő hitelesítéshez](authentication-aad-overview.md).) Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) csak a portálra vonatkozik, és nem terjed ki SQL Serverra.

    ![Azure Active Directory rendszergazda kiválasztása](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. A **Active Directory-rendszergazda** lap tetején válassza a **Mentés** lehetőséget.

    ![rendszergazda mentése](./media/authentication-aad-configure/save-admin.png)

A rendszergazda módosításának folyamata több percet is igénybe vehet. Ezután megjelenik az új rendszergazda a **Active Directory admin** mezőben.

   > [!NOTE]
   > Az Azure AD-rendszergazda beállításakor az új rendszergazda neve (felhasználó vagy csoport) nem lehet már jelen a virtuális főadatbázisban kiszolgálói hitelesítési felhasználóként. Ha van ilyen, az Azure AD-rendszergazda telepítése meghiúsul; a létrehozás visszavonása és annak jelzése, hogy a rendszergazda (név) már létezik. Mivel egy ilyen kiszolgálói hitelesítési felhasználó nem része az Azure AD-nek, az Azure AD-hitelesítéssel való csatlakozáshoz szükséges bármilyen erőfeszítés sikertelen lesz.

Ha később el szeretné távolítani a rendszergazdát, a **Active Directory-rendszergazda** lap tetején válassza a **rendszergazda eltávolítása** lehetőséget, majd kattintson a **Mentés** gombra.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>PowerShell a SQL Database és az Azure Szinapszishoz

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell-parancsmagok futtatásához Azure PowerShell kell telepítenie és futnia. Részletes információk: [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása). Az Azure AD-rendszergazda kiépítéséhez hajtsa végre a következő Azure PowerShell parancsokat:

- Connect-AzAccount
- Select-AzSubscription

Az Azure AD-rendszergazdák üzembe helyezéséhez és kezeléséhez használt parancsmagok a SQL Database és az Azure szinapszis számára:

| Parancsmag neve | Leírás |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Kiépít egy Azure Active Directory rendszergazdát a SQL Database vagy az Azure szinapszis-t üzemeltető kiszolgáló számára. (Az aktuális előfizetésből kell származnia) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Eltávolít egy Azure Active Directory rendszergazdát a SQL Database vagy az Azure szinapszis-t futtató kiszolgáló számára.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |A SQL Database vagy az Azure szinapszis-t futtató kiszolgáló számára jelenleg konfigurált Azure Active Directory-rendszergazda adatait adja vissza. |

Az egyes parancsokra vonatkozó további információk megtekintéséhez használja a Get-Help PowerShell-parancsot. Például: `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

A következő parancsfájl egy  `40b79501-b343-44ed-9ce7-da4c8cc7353f` **Group-23** nevű erőforráscsoport DBA_Group (objektumazonosító) nevű Azure ad-rendszergazdai csoportját is kiépíti a **demo_server** -kiszolgálóhoz:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

A **DisplayName** beviteli paraméter az Azure ad megjelenítendő nevét vagy az egyszerű felhasználónevet is elfogadja. Például: ``DisplayName="John Smith"`` és ``DisplayName="johns@contoso.com"``. Az Azure AD-csoportok esetében csak az Azure AD megjelenítendő neve támogatott.

> [!NOTE]
> Az Azure PowerShell parancs ```Set-AzSqlServerActiveDirectoryAdministrator``` nem akadályozza meg, hogy az Azure ad-rendszergazdák kiépítsék a nem támogatott felhasználók számára. Nem támogatott felhasználó is kiépíthető, de nem tud kapcsolódni egy adatbázishoz.

A következő példa a választható **ObjectId** használja:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Az Azure AD- **ObjectId** akkor van szükség, ha a **DisplayName attribútum** nem egyedi. A **ObjectId** és a **DisplayName** értékek beolvasásához használja klasszikus Azure portál Active Directory szakaszát, és tekintse meg egy felhasználó vagy csoport tulajdonságait.

Az alábbi példa a kiszolgáló aktuális Azure AD-rendszergazdájával kapcsolatos információkat adja vissza:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Az alábbi példa egy Azure AD-rendszergazdát távolít el:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure AD-rendszergazdák üzembe helyezéséhez hívja a következő CLI-parancsokat:

| Parancs | Leírás |
| --- | --- |
|[az SQL Server ad-admin Create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Kiépít egy Azure Active Directory rendszergazdát a SQL Database vagy az Azure szinapszis-t üzemeltető kiszolgáló számára. (Az aktuális előfizetésből kell származnia) |
|[az SQL Server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Eltávolít egy Azure Active Directory rendszergazdát a SQL Database vagy az Azure szinapszis-t futtató kiszolgáló számára. |
|[az SQL Server ad-admin List](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | A SQL Database vagy az Azure szinapszis-t futtató kiszolgáló számára jelenleg konfigurált Azure Active Directory-rendszergazda adatait adja vissza. |
|[az SQL Server ad-Admin Update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | A SQL Database vagy az Azure szinapszis-t üzemeltető kiszolgáló Active Directory-rendszergazdájának frissítése. |

További információ a CLI-parancsokról: [az SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> A REST API-k használatával kiépítheti Azure Active Directory-rendszergazdát is. További információ: [Service Management REST API referenciák és műveletek Azure SQL Database műveletekhez Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Ügyfélszámítógépek konfigurálása

A következő szoftvereket kell telepítenie minden olyan ügyfélszámítógépen, amelyről az alkalmazások vagy a felhasználók az Azure AD-identitások használatával csatlakoznak SQL Database vagy Azure Szinapszishoz:

- A .NET-keretrendszer 4,6-es vagy újabb verziója [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- SQL Server (*ADAL.DLL*) Azure Active Directory hitelesítési könyvtára. Az alábbi letöltési hivatkozások segítségével telepítheti a *ADAL.DLL* könyvtárat tartalmazó legújabb SSMS, ODBC és OLE DB illesztőprogramot.
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC-illesztő, 17 SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [OLE DB 18. illesztőprogram SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

Ezeket a követelményeket az alábbiak szerint teljesítheti:

- A [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) vagy [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) legújabb verziójának telepítése megfelel a .NET-keretrendszer 4,6-es követelményének.
  - A SSMS a *ADAL.DLL* x86-os verzióját telepíti.
  - A SSDT telepíti a *ADAL.DLL* amd64-es verzióját.
  - A [Visual Studio letöltések](https://www.visualstudio.com/downloads/download-visual-studio-vs) legújabb Visual Studio verziója megfelel a .NET-keretrendszer 4,6-es követelményének, de nem telepíti a *ADAL.DLL* szükséges amd64-es verzióját.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Az Azure AD-identitásokhoz hozzárendelt, tartalmazott felhasználók létrehozása

Mivel az SQL felügyelt példánya támogatja az Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) használatát, a tárolt adatbázis-felhasználók használata nem kötelező. Az Azure AD kiszolgálói rendszerbiztonsági tagok (bejelentkezések) lehetővé teszik bejelentkezések létrehozását Azure AD-felhasználókból, -csoportokból vagy -alkalmazásokból. Ez azt jelenti, hogy az SQL felügyelt példányát az Azure AD-kiszolgáló bejelentkezésével, nem pedig egy tárolt adatbázis-felhasználó használatával lehet hitelesíteni. További információ: [SQL felügyelt példányának áttekintése](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration). Az Azure AD Server-rendszerbiztonsági tag (Logins) létrehozásával kapcsolatos szintaxisért lásd: [create login (bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)).

Azonban a Azure Active Directory hitelesítés használata a SQL Database és az Azure szinapszis használatához az Azure AD-identitáson alapuló, tárolt adatbázis-felhasználókat kell használnia. Egy tárolt adatbázis-felhasználó nem rendelkezik bejelentkezéssel a főadatbázisban, és az adatbázishoz társított Azure AD-beli identitáshoz társítja az adatokat. Az Azure AD-identitás lehet egyéni felhasználói fiók vagy csoport is. További információ a tárolt adatbázis-felhasználókról: [tárolt adatbázis-felhasználók – az adatbázis hordozhatóvé tétele](/sql/relational-databases/security/contained-database-users-making-your-database-portable).

> [!NOTE]
> Az adatbázis felhasználói (a rendszergazdák kivételével) nem hozhatók létre a Azure Portal használatával. Az Azure-szerepkörök nem lesznek propagálva az adatbázisba SQL Database, az SQL felügyelt példányában vagy az Azure Szinapszisban. Az Azure-szerepkörök az Azure-erőforrások kezeléséhez használatosak, és nem vonatkoznak az adatbázis engedélyeire. A **SQL Server közreműködő** szerepkör például nem biztosít hozzáférést az adatbázishoz SQL Database, az SQL felügyelt példányában vagy az Azure szinapszisban való kapcsolódáshoz. A hozzáférési engedélyt közvetlenül az adatbázisban kell megadni a Transact-SQL-utasítások használatával.

> [!WARNING]
> `:` `&` A T-SQL és a utasításokban szereplő felhasználónevek `CREATE LOGIN` nem támogatják a speciális karaktereket, például a kettőspontot vagy a jeleket `CREATE USER` .

Ha olyan Azure AD-alapú tárolt adatbázis-felhasználót szeretne létrehozni (az adatbázist birtokló kiszolgáló rendszergazdájától eltérő), akkor az adatbázishoz egy Azure AD-identitással kell csatlakoznia, amely legalább a **felhasználói engedély megváltoztatására** jogosult. Ezután használja a következő Transact-SQL szintaxist:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* lehet egy Azure ad-felhasználó egyszerű felhasználóneve vagy egy Azure ad-csoport megjelenített neve.

**Példák:** Az Azure AD összevont vagy felügyelt tartományi felhasználót képviselő tárolt adatbázis-felhasználó létrehozása:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Egy Azure AD-vagy összevont tartományi csoportot jelképező, tárolt adatbázis-felhasználó létrehozásához adja meg a biztonsági csoport megjelenítendő nevét:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Egy olyan tárolt adatbázis-felhasználó létrehozása, amely egy Azure AD-jogkivonattal csatlakozó alkalmazást jelképez:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Ehhez a parancshoz az SQL Access Azure AD-t (a "külső szolgáltatót") a bejelentkezett felhasználó nevében kell megadnia. Bizonyos esetekben előfordulhat, hogy az Azure AD egy kivételt ad vissza az SQL-nek. Ezekben az esetekben a felhasználó az 33134-es SQL-hibát fogja látni, amelynek tartalmaznia kell az Azure AD-specifikus hibaüzenetet. A legtöbb esetben a hiba azt jelenti, hogy a rendszer megtagadja a hozzáférést, vagy a felhasználónak regisztrálnia kell az MFA-ban az erőforrás eléréséhez, vagy az első féltől származó alkalmazások közötti hozzáférést előhitelesítéssel kell kezelni. Az első két esetben a problémát általában a felhasználó Azure AD-bérlője által beállított feltételes hozzáférési szabályzatok okozzák: megakadályozza, hogy a felhasználó hozzáférjen a külső szolgáltatóhoz. A feltételes hozzáférési szabályzatok frissítésével engedélyezheti a (z) "00000002-0000-0000-C000-000000000000" alkalmazáshoz való hozzáférést (az Azure AD Graph API alkalmazás-AZONOSÍTÓját) a probléma megoldásához. Abban az esetben, ha a hiba azt mondja, hogy a rendszer az első féltől származó alkalmazások hozzáférését az előhitelesítésen keresztül kell kezelni, a probléma az, hogy a felhasználó egyszerű szolgáltatásként van bejelentkezve. A parancsnak sikeresnek kell lennie, ha egy felhasználó hajtja végre.

> [!TIP]
> Az Azure-előfizetéshez társított Azure Active Directorytól eltérő Azure Active Directory nem hozhat létre közvetlenül felhasználót. Azonban a társított Active Directoryban (más néven külső felhasználók) importált felhasználók a bérlő Active Directory Active Directory csoportjába is hozzáadhatók. Ha létrehoz egy tárolt adatbázis-felhasználót az AD-csoport számára, a külső Active Directory felhasználói hozzáférhetnek SQL Databasehoz.

A tárolt adatbázis-felhasználók Azure Active Directory identitások alapján történő létrehozásával kapcsolatos további információkért lásd: [felhasználó létrehozása (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> A kiszolgáló Azure Active Directory-rendszergazdájának eltávolítása megakadályozza, hogy bármely Azure AD-hitelesítési felhasználó csatlakozzon a kiszolgálóhoz. Ha szükséges, a használhatatlan Azure AD-felhasználókat a SQL Database rendszergazdája manuálisan is elvégezheti.

> [!NOTE]
> Ha **lejárt a kapcsolat időkorlátja**, előfordulhat, hogy a `TransparentNetworkIPResolution` kapcsolati karakterlánc paraméterét false értékre kell állítania. További információ: [a .NET-keretrendszer 4.6.1-TransparentNetworkIPResolution kapcsolati időtúllépési hibája](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution).

Amikor létrehoz egy adatbázis-felhasználót, a felhasználó megkapja a **kapcsolódási** engedélyt, és a **nyilvános** szerepkör tagjaként tud csatlakozni az adatbázishoz. Kezdetben csak a felhasználó számára elérhető engedélyek érvényesek a **nyilvános** szerepkörre, vagy bármely olyan Azure ad-csoportnak biztosított engedély, amely tagja a felhasználónak. Miután kiépített egy Azure AD-alapú tárolt adatbázis-felhasználót, a felhasználónak további engedélyeket is megadhat, ugyanúgy, ahogy bármely más típusú felhasználó számára engedélyezi. Általában engedélyeket ad az adatbázis szerepköreinek, és felhasználókat ad hozzá a szerepkörökhöz. További információ: adatbázismotor- [engedélyek alapjai](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). A speciális SQL Database szerepkörökkel kapcsolatos további információkért lásd: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseban](logins-create-manage.md).
A felügyelt tartományba külső felhasználóként importált összevont tartományi felhasználói fióknak a felügyelt tartományi identitást kell használnia.

> [!NOTE]
> Az Azure AD-felhasználók az E (EXTERNAL_USER) típusú adatbázis-metaadatokban vannak megjelölve, az X típusú csoportok esetében (EXTERNAL_GROUPS). További információ: [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Kapcsolódás az adatbázishoz a SSMS vagy a SSDT használatával  

Ha ellenőrizni szeretné, hogy az Azure AD-rendszergazda megfelelően van-e beállítva, kapcsolódjon a **Master** adatbázishoz az Azure ad rendszergazdai fiók használatával.
Egy Azure AD-alapú tárolt adatbázis-felhasználó (az adatbázist birtokló kiszolgáló rendszergazdája kivételével) kiépítéséhez kapcsolódjon az adatbázishoz egy olyan Azure AD-identitással, amely hozzáféréssel rendelkezik az adatbázishoz.

> [!IMPORTANT]
> Azure Active Directory hitelesítés támogatása [SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) és a Visual Studio 2015 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) érhető el. A SSMS augusztusi 2016-es kiadása a Active Directory univerzális hitelesítés támogatását is magában foglalja, amely lehetővé teszi, hogy a rendszergazdák telefonhívást, szöveges üzenetet, intelligens kártyákat PIN-kóddal vagy a mobil alkalmazások értesítésével megkövetelje Multi-Factor Authentication használatát.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Azure AD-identitás használata a SSMS vagy a SSDT használatával történő kapcsolódáshoz

A következő eljárások bemutatják, hogyan csatlakozhat az Azure AD-identitással rendelkező SQL Databasehoz SQL Server Management Studio vagy SQL Server adatbázis-eszközök használatával.

### <a name="active-directory-integrated-authentication"></a>Integrált hitelesítés Active Directory

Akkor használja ezt a módszert, ha a Windows rendszerbe bejelentkezett egy összevont tartományból származó Azure Active Directory hitelesítő adataival, vagy egy felügyelt tartományba, amely az áteresztő és a jelszó-kivonatoló hitelesítés zökkenőmentes egyszeri bejelentkezésére van konfigurálva. További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](../../active-directory/hybrid/how-to-connect-sso.md).

1. Indítsa el Management Studio vagy az adateszközöket, és a **Kapcsolódás a kiszolgálóhoz** (vagy a **Kapcsolódás az adatbázis-kezelőhöz**) párbeszédpanel **hitelesítés** mezőjében válassza a **Azure Active Directory integrált** elemet. Nincs szükség jelszóra, vagy megadható, mert a rendszer a meglévő hitelesítő adatait fogja megjeleníteni a kapcsolatban.

   ![AD integrált hitelesítés kiválasztása][11]

2. Válassza a **Beállítások** gombot, és a **kapcsolat tulajdonságai** lapon a **Kapcsolódás az adatbázishoz** mezőbe írja be annak a felhasználói adatbázisnak a nevét, amelyhez csatlakozni szeretne. További információ: [többtényezős Azure ad-hitelesítés](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) a SSMS 17. x és 18. x kapcsolati tulajdonságai közötti különbségekről.

   ![Adja meg az adatbázis nevét][13]

### <a name="active-directory-password-authentication"></a>Jelszavas hitelesítés Active Directory

Ezt a módszert akkor használja, ha Azure ad-beli egyszerű névvel csatlakozik az Azure AD által felügyelt tartomány használatával. Azt is megteheti, hogy összevont fiókokat használ a tartományhoz való hozzáférés nélkül, például ha távolról dolgozik.

Ezzel a módszerrel hitelesítheti SQL Database vagy az SQL felügyelt példányát az Azure AD felhőalapú identitás-felhasználókkal vagy az Azure AD hibrid identitásokat használó alkalmazásokkal. Ez a módszer támogatja azokat a felhasználókat, akik a Windows hitelesítő adatait szeretnék használni, de a helyi számítógép nincs csatlakoztatva a tartományhoz (például a távelérés használatával). Ebben az esetben a Windows-felhasználó megadhatja a tartományi fiókját és jelszavát, valamint hitelesítheti az adatbázist SQL Database, az SQL felügyelt példányában vagy az Azure Szinapszisban.

1. Indítsa el Management Studio vagy az adateszközöket, és a **Kapcsolódás a kiszolgálóhoz** (vagy a **Kapcsolódás az adatbázis-kezelőhöz**) párbeszédpanel **hitelesítés** mezőjében válassza a **Azure Active Directory-Password** lehetőséget.

2. **A Felhasználónév mezőbe írja** be a Azure Active Directory felhasználónevét a **Felhasználónév \@ domain.com** formátumban. A felhasználóneveknek Azure Active Directory vagy egy felügyelt vagy összevont tartományból származó fiókkal kell rendelkezniük, Azure Active Directory.

3. A **jelszó** mezőbe írja be a Azure Active Directory fiók vagy a felügyelt/összevont tartományi fiók felhasználói jelszavát.

    ![AD jelszó-hitelesítés kiválasztása][12]

4. Válassza a **Beállítások** gombot, és a **kapcsolat tulajdonságai** lapon a **Kapcsolódás az adatbázishoz** mezőbe írja be annak a felhasználói adatbázisnak a nevét, amelyhez csatlakozni szeretne. (Lásd az előző beállításban található ábrát.)

### <a name="active-directory-interactive-authentication"></a>Active Directory interaktív hitelesítés

Ezt a módszert használja az interaktív hitelesítéshez Multi-Factor Authentication (MFA) vagy anélkül, hogy a jelszót interaktívan kell igényelni. Ezzel a módszerrel hitelesíthető a SQL Database-ben, az SQL felügyelt példányán, valamint az Azure AD csak felhőalapú identitást használó felhasználók vagy az Azure AD hibrid identitásokat használó Azure szinapszis-adatbázison.

További információ: a [multi-Factor Azure ad-hitelesítés használata a SQL Database és az Azure szinapszis használatával (SSMS-támogatás az MFA-hoz)](authentication-mfa-ssms-overview.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Azure AD-identitás használata az ügyfélalkalmazások közötti kapcsolódáshoz

A következő eljárások bemutatják, hogyan csatlakozhat egy Azure AD-identitással rendelkező SQL Databasehoz egy ügyfélalkalmazás használatával.

### <a name="active-directory-integrated-authentication"></a>Integrált hitelesítés Active Directory

Az integrált Windows-hitelesítés használatához a tartomány Active Directory Azure Active Directoryhoz kell összevontnak lennie, vagy olyan felügyelt tartománynak kell lennie, amely zökkenőmentes egyszeri bejelentkezésre van konfigurálva a továbbított vagy a jelszó-kivonatoló hitelesítéshez. További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](../../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> Az integrált Windows-hitelesítéshez használt [MSAL.net (Microsoft. Identity. Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) nem támogatott az átmenő és a jelszó-kivonatoló hitelesítés zökkenőmentes egyszeri bejelentkezéséhez.

Az adatbázishoz csatlakozó ügyfélalkalmazás (vagy szolgáltatás) tartományhoz csatlakozó gépen kell futnia a felhasználó tartományi hitelesítő adatai alatt.

Ha integrált hitelesítéssel és egy Azure AD-identitással szeretne csatlakozni egy adatbázishoz, az adatbázis-kapcsolati karakterláncban a hitelesítési kulcsszót kell beállítani `Active Directory Integrated` . A következő C#-mintakód ADO .NET-et használ.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A kapcsolati karakterlánc kulcsszava `Integrated Security=True` nem támogatott Azure SQL Databasehoz való csatlakozáshoz. ODBC-kapcsolatok esetén el kell távolítania a szóközöket, és be kell állítania a hitelesítést a "ActiveDirectoryIntegrated" értékre.

### <a name="active-directory-password-authentication"></a>Jelszavas hitelesítés Active Directory

Ha az Azure AD csak felhőalapú identitású felhasználói fiókjaival vagy az Azure AD Hybrid identitys-t használó adatbázisokkal szeretne csatlakozni, akkor a hitelesítési kulcsszót a értékre kell állítani `Active Directory Password` . A kapcsolatok karakterláncának tartalmaznia kell a felhasználói azonosító/UID azonosítót, valamint a jelszó/PWD kulcsszavakat és értékeket. A következő C#-mintakód ADO .NET-et használ.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

További információ az Azure AD-hitelesítési módszerekről az [Azure ad-hitelesítés GitHub-bemutatójában](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)elérhető bemutató kód minták használatával.

## <a name="azure-ad-token"></a>Azure AD-jogkivonat

Ez a hitelesítési módszer lehetővé teszi, hogy a közepes szintű szolgáltatások [JSON webes jogkivonatokat (JWT)](../../active-directory/develop/id-tokens.md) szerezzenek be az adatbázishoz SQL Database, az SQL felügyelt példányban vagy az Azure szinapszisban, ha az Azure ad-ből tokent szereznek be. Ez a módszer lehetővé teszi a különböző alkalmazási forgatókönyvek, például a szolgáltatás-identitások, az egyszerű szolgáltatások és az alkalmazások tanúsítványalapú hitelesítés használatával történő használatát. Az Azure AD-jogkivonat-hitelesítés használatához négy alapvető lépést kell végrehajtania:

1. Regisztrálja alkalmazását Azure Active Directory és szerezze be a kód ügyfél-AZONOSÍTÓját.
2. Hozzon létre egy adatbázis-felhasználót, amely az alkalmazást jelképezi. (A 6. lépésben korábban fejeződött be.)
3. Hozzon létre egy tanúsítványt az ügyfélszámítógépen, amelyen az alkalmazás fut.
4. Adja hozzá a tanúsítványt az alkalmazás kulcsaként.

Példa a kapcsolatok karakterláncára:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

További információ: [SQL Server biztonsági blog](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth). A tanúsítványok hozzáadásával kapcsolatos információkért lásd: a [tanúsítványalapú hitelesítés első lépései a Azure Active Directory](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

A következő utasítások az Sqlcmd 13,1-es verziójának használatával csatlakoznak, amely a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=53591)érhető el.

> [!NOTE]
> `sqlcmd` a `-G` paranccsal nem működik a rendszeridentitások használata, és a felhasználónak egyszerű felhasználónevet kell bejelentkeznie.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Az Azure AD-hitelesítés hibáinak megoldása

Az Azure AD-hitelesítéssel kapcsolatos hibaelhárítással kapcsolatban az alábbi blogban talál útmutatást: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Következő lépések

- A bejelentkezések, a felhasználók, az adatbázis-szerepkörök és a SQL Databaseban lévő engedélyek áttekintését lásd: [bejelentkezések, felhasználók, adatbázis-szerepkörök és felhasználói fiókok](logins-create-manage.md).
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](/sql/relational-databases/security/authentication-access/principals-database-engine).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](/sql/relational-databases/security/authentication-access/database-level-roles).
- További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](firewall-configure.md).
- Az Azure ad vendég felhasználó Azure AD-rendszergazdaként való beállításával kapcsolatos információkért lásd: [Azure ad Guest Users-felhasználók létrehozása és beállítása Azure ad-rendszergazdaként](authentication-aad-guest-users.md).
- Az rendszerbiztonsági tag Azure SQL-sel való kezelésével kapcsolatos információkért lásd: [Azure ad-felhasználók létrehozása Azure ad-alkalmazásokkal](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
