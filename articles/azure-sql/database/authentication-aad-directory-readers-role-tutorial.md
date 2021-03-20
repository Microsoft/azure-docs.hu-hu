---
title: Címtár-olvasók szerepkör hozzárendelése egy Azure AD-csoporthoz és szerepkör-hozzárendelések kezelése
description: Ez a cikk bemutatja, hogyan engedélyezheti a címtár-olvasók szerepkört az Azure ad-csoportokkal az Azure AD szerepkör-hozzárendelések kezeléséhez az Azure SQL Database, az Azure SQL felügyelt példányával és az Azure szinapszis Analytics használatával
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: bc809cf02b827b7498890cb7d929c44bd360ab53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094709"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Oktatóanyag: címtár-olvasók szerepkör hozzárendelése Azure AD-csoporthoz és szerepkör-hozzárendelések kezelése

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> A jelen cikk egy csoportjának a **címtár-olvasói** szerepkörhöz való hozzárendelése **nyilvános előzetes** verzióban érhető el. 

Ebből a cikkből megtudhatja, hogyan hozhat létre csoportot a Azure Active Directoryban (Azure AD), és hogyan rendelheti hozzá a csoportot a [**címtár-olvasók**](../../active-directory/roles/permissions-reference.md#directory-readers) szerepkörhöz. A Directory-olvasók engedélyei lehetővé teszik, hogy a csoport tulajdonosa további tagokat vegyen fel a csoportba, például a [Azure SQL Database](sql-database-paas-overview.md) [felügyelt identitását](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) , az [Azure SQL felügyelt példányát](../managed-instance/sql-managed-instance-paas-overview.md)és az [Azure szinapszis Analytics szolgáltatást](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Ezzel a lépéssel megkerülheti, hogy [globális rendszergazda](../../active-directory/roles/permissions-reference.md#global-administrator) vagy [Kiemelt szerepkörű rendszergazda](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) rendelje hozzá a címtár-olvasók szerepkört közvetlenül a bérlő összes Azure SQL logikai kiszolgálójának identitásához.

Ez az oktatóanyag a [felhőalapú csoportok használata a szerepkör-hozzárendelések Azure Active Directory (előzetes verzió) szolgáltatásban való kezeléséhez című részben](../../active-directory/roles/groups-concept.md)ismertetett szolgáltatást használja. 

Az Azure SQL-hez készült Azure AD-csoport címtár-olvasók szerepkörének kiosztásával kapcsolatos további információkért lásd: az [Azure sql Azure Active Directory Directory-olvasói szerepköre](authentication-aad-directory-readers-role.md).

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure AD-példány. További információ: [Azure ad-hitelesítés konfigurálása és kezelése az Azure SQL-szel](authentication-aad-configure.md).
- Egy SQL Database, SQL felügyelt példány vagy Azure szinapszis.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Directory-olvasók szerepkör-hozzárendelés a Azure Portal használatával

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Új csoport létrehozása és tulajdonosok és szerepkör társítása

1. Ehhez a kezdeti beállításhoz [globális rendszergazdai](../../active-directory/roles/permissions-reference.md#global-administrator) vagy [Kiemelt szerepkörű rendszergazdai](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) jogosultsággal rendelkező felhasználó szükséges.
1. A rendszerjogosultságú felhasználó bejelentkezik a [Azure Portalba](https://portal.azure.com).
1. Lépjen a **Azure Active Directory** erőforráshoz. A **felügyelt** területen válassza a **csoportok** lehetőséget. Új csoport létrehozásához válassza az **új csoport** lehetőséget.
1. Válassza a **Biztonság** lehetőséget a csoport típusaként, és töltse ki a többi mezőt. Győződjön meg arról, hogy az **Azure ad-szerepkörök beállítása a csoporthoz (előzetes verzió)** az **Igen** értékre van állítva. Ezután rendelje hozzá az Azure AD **Directory-olvasók** szerepkört a csoporthoz.
1. Rendelje hozzá az Azure AD-felhasználókat tulajdonos (ok) ként a létrehozott csoporthoz. A csoport tulajdonosa lehet egy normál AD-felhasználó, amely nem rendelkezik hozzárendelt Azure AD rendszergazdai szerepkörrel. A tulajdonosnak olyan felhasználónak kell lennie, amely a SQL Database, az SQL felügyelt példányát vagy az Azure Szinapszisot kezeli.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="HRE – új csoport":::

1. Kattintson a **Létrehozás** elemre.

### <a name="checking-the-group-that-was-created"></a>A létrehozott csoport ellenőrzése

> [!NOTE]
> Győződjön meg arról, hogy a **csoport típusa** **Biztonság**. *Microsoft 365* csoportok nem támogatottak az Azure SQL-ben.

A létrehozott csoport vizsgálatához és kezeléséhez lépjen vissza a Azure Portal **csoportok** ablaktáblájába, és keresse meg a csoport nevét. A csoportok kiválasztása után további tulajdonosokat és tagokat adhat hozzá a **tulajdonosok** és a **tagok** **kezelése** menüben. A csoporthoz **hozzárendelt szerepkörök** is áttekinthetők.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="Képernyőfelvétel a csoportok ablaktábláról a tagok, a tulajdonosok és a hozzárendelt szerepkörök (előzetes verzió) beállítások menüjében kiemelve.":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Azure SQL felügyelt identitás hozzáadása a csoporthoz

> [!NOTE]
> Ehhez a példához SQL felügyelt példányt használunk, de a SQL Database és az Azure szinapszis hasonló lépéseit is alkalmazhatja ugyanezen eredmények eléréséhez.

A további lépések végrehajtásához a globális rendszergazda vagy a Kiemelt szerepkörű rendszergazda felhasználóra már nincs szükség.

1. Jelentkezzen be a Azure Portalba az SQL felügyelt példányát kezelő felhasználóként, és a korábban létrehozott csoport tulajdonosa.

1. Keresse meg az **SQL felügyelt példánya** erőforrásának nevét a Azure Portal.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="Képernyőkép a felügyelt SQL-példányok képernyőjéről az SQL-példány neve ssomitest és az alhálózat neve ManagedInstance kiemelve.":::

   Az SQL felügyelt példányának létrehozása során létre lett hozva egy Azure-identitás a példányhoz. A létrehozott identitás neve megegyezik a felügyelt SQL-példány nevének előtagjával. A következő lépéseket követve megkeresheti az SQL felügyelt példányának Azure AD-alkalmazásként létrehozott identitásának egyszerű szolgáltatását:

    - Lépjen a **Azure Active Directory** erőforráshoz. A **kezelés** beállítás alatt válassza a **vállalati alkalmazások** lehetőséget. Az **objektumazonosító** a példány identitása.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="Képernyőfelvétel a vállalati alkalmazások oldaláról Azure Active Directory erőforráshoz a kijelölt SQL felügyelt példány objektumazonosító alapján.":::

1. Lépjen a **Azure Active Directory** erőforráshoz. A **felügyelt** területen válassza a **csoportok** lehetőséget. Válassza ki a létrehozott csoportot. A csoport **felügyelt** beállítása alatt válassza a **tagok** lehetőséget. Válassza a **Tagok hozzáadása** lehetőséget, és adja hozzá az SQL felügyelt példány egyszerű szolgáltatásnevet a csoport tagjaként a fent található név megkeresésével.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="Képernyőkép a Azure Active Directory-erőforrás tagjainak oldaláról az SQL felügyelt példány új tagként való hozzáadására kijelölt beállításokkal.":::

> [!NOTE]
> Több percet is igénybe vehet, hogy az Azure-rendszeren keresztül propagálja az egyszerű szolgáltatás engedélyeit, és lehetővé tegye az Azure AD-Graph API elérését. Előfordulhat, hogy néhány percet várnia kell, mielőtt kiépít egy Azure AD-rendszergazdát az SQL felügyelt példányához.

### <a name="remarks"></a>Megjegyzések

A SQL Database és az Azure szinapszis esetében a kiszolgáló identitása az Azure SQL logikai kiszolgáló létrehozásakor vagy a kiszolgáló létrehozása után hozható létre. A kiszolgáló identitásának SQL Database vagy Azure Szinapszisban való létrehozásával és beállításával kapcsolatos további információkért lásd: [egyszerű szolgáltatásnév engedélyezése az Azure ad-felhasználók létrehozásához](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

SQL felügyelt példány esetén a **címtár-olvasók** szerepkört hozzá kell rendelni a felügyelt példány identitásához, mielőtt beállít [egy Azure ad-rendszergazdát a felügyelt példányhoz](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

A címtár- **olvasók** szerepkörnek a kiszolgáló identitáshoz való hozzárendeléséhez nincs szükség a SQL Database vagy az Azure szinapszis szolgáltatáshoz a logikai kiszolgálóhoz tartozó Azure ad-rendszergazda beállításakor. Ahhoz azonban, hogy egy Azure ad-objektumot az Azure AD-alkalmazás nevében SQL Database vagy Azure Szinapszisban lehessen létrehozni, szükség van a **címtár-olvasók** szerepkörre. Ha a szerepkör nincs hozzárendelve az SQL logikai kiszolgáló identitásához, az Azure AD-felhasználók létrehozása az Azure SQL-ben sikertelen lesz. További információ: [Azure Active Directory egyszerű szolgáltatásnév az Azure SQL](authentication-aad-service-principal.md)-ben.

## <a name="directory-readers-role-assignment-using-powershell"></a>Directory-olvasók szerepkör-hozzárendelés a PowerShell használatával

> [!IMPORTANT]
> A [globális rendszergazda](../../active-directory/roles/permissions-reference.md#global-administrator) vagy a [Kiemelt szerepkörű rendszergazdának](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) futtatnia kell ezeket a kezdeti lépéseket. A PowerShell mellett az Azure AD Microsoft Graph API-t is kínál, amely [szerepkörökhöz hozzárendelhető csoportot hoz létre az Azure ad-ben](../../active-directory/roles/groups-create-eligible.md#using-microsoft-graph-api).

1. Töltse le az Azure AD előzetes verzió PowerShell-modulját az alábbi parancsokkal. Lehet, hogy rendszergazdaként kell futtatnia a PowerShellt.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Kapcsolódjon az Azure AD-bérlőhöz.

    ```powershell
    Connect-AzureAD
    ```

1. Hozzon létre egy biztonsági csoportot a **címtár-olvasók** szerepkör hozzárendeléséhez.

    - `DirectoryReaderGroup`, `Directory Reader Group` , és `DirRead` a beállításoknak megfelelően módosítható.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Rendeljen **címtár-olvasók** szerepkört a csoporthoz.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Rendeljen tulajdonosokat a csoporthoz.

    - Cserélje le `<username>` a-t arra a felhasználóra, akinek a csoportját be szeretné állítani. A fenti lépések megismétlésével több tulajdonos is hozzáadható.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    A következő parancs használatával győződjön meg a csoport tulajdonosainak:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    A csoport tulajdonosait a [Azure Portal](https://portal.azure.com)is ellenőrizheti. Kövesse a [létrehozott csoport ellenőrzése](#checking-the-group-that-was-created)című témakör lépéseit.

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>Az egyszerű szolgáltatás társítása a csoport tagjaként

A további lépések végrehajtásához a globális rendszergazda vagy a Kiemelt szerepkörű rendszergazda felhasználóra már nincs szükség.

1. Az Azure SQL-erőforrást is kezelő csoport tulajdonosának használatával futtassa az alábbi parancsot az Azure AD-hez való kapcsolódáshoz.

    ```powershell
    Connect-AzureAD
    ```

1. Rendelje hozzá a szolgáltatásnevet a létrehozott csoport tagjaként.

    - Cserélje le az `<ServerName>` kifejezést az Azure SQL logikai kiszolgáló nevére, vagy a felügyelt példány nevét. További információ: szakasz, [Azure SQL Service-identitás hozzáadása a csoporthoz](#add-azure-sql-managed-identity-to-the-group)

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    A következő parancs visszaadja az egyszerű szolgáltatásnév objektum AZONOSÍTÓját, amely azt jelzi, hogy a csoport hozzá lett adva:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Következő lépések

- [Az Azure SQL Azure Active Directory Directory-olvasói szerepköre](authentication-aad-directory-readers-role.md)
- [Oktatóanyag: Azure AD-felhasználók létrehozása az Azure AD-alkalmazásokkal](authentication-aad-service-principal-tutorial.md)
- [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL-sel](authentication-aad-configure.md)