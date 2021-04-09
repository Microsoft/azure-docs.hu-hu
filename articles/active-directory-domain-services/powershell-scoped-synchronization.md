---
title: Hatókörrel rendelkező szinkronizálás a Azure AD Domain Services PowerShell használatával | Microsoft Docs
description: Ismerje meg, hogyan konfigurálható az Azure ad PowerShell az Azure AD-ből származó hatókörön belüli szinkronizálás Azure Active Directory Domain Services felügyelt tartományra való konfigurálásához
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: f877a631fd3c89d74b9e3b47cf205bbcf173ebc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453410"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Hatókörön belüli szinkronizálás konfigurálása az Azure AD-ből Azure Active Directory Domain Services az Azure AD PowerShell használatával

A hitelesítési szolgáltatások biztosításához Azure Active Directory Domain Services (Azure AD DS) szinkronizálja a felhasználókat és a csoportokat az Azure AD-ből. Hibrid környezetben a helyszíni Active Directory Domain Services (AD DS) környezet felhasználóit és csoportjait először szinkronizálhatja az Azure AD-vel az Azure AD Connect használatával, majd szinkronizálhatja az Azure AD DSával.

Alapértelmezés szerint az Azure AD-címtárból származó összes felhasználó és csoport szinkronizálva van egy Azure AD DS felügyelt tartományba. Ha konkrét igényei vannak, ehelyett csak a megadott felhasználók szinkronizálását lehet választani.

Ez a cikk bemutatja, hogyan hozhat létre olyan felügyelt tartományt, amely hatókörön belüli szinkronizálást használ, majd az Azure AD PowerShell használatával megváltoztathatja vagy letilthatja a hatókörön belüli felhasználókat. [Ezeket a lépéseket a Azure Portal használatával][scoped-sync]is elvégezheti.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services felügyelt tartomány létrehozásához és konfigurálásához][tutorial-create-instance].
* Az Azure AD DS szinkronizálási hatókörének módosításához *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.

## <a name="scoped-synchronization-overview"></a>Hatókörön belüli szinkronizálás – áttekintés

Alapértelmezés szerint az Azure AD-címtárból származó összes felhasználó és csoport egy felügyelt tartományba van szinkronizálva. Ha csak néhány felhasználónak kell hozzáférnie a felügyelt tartományhoz, csak ezeket a felhasználói fiókokat lehet szinkronizálni. Ez a hatókörön belüli szinkronizálás csoport alapú. A csoport alapú hatókörű szinkronizálás konfigurálásakor a rendszer csak a megadott csoportokhoz tartozó felhasználói fiókokat szinkronizálja a felügyelt tartományba. Beágyazott csoportok nincsenek szinkronizálva, csak a kiválasztott csoportok.

A szinkronizálási hatókört a felügyelt tartomány létrehozása előtt vagy után is módosíthatja. A szinkronizálás hatókörét egy egyszerű szolgáltatásnév definiálja az alkalmazás-azonosító 2565bd9d-DA50-47d4-8B85-4c97f669dc36. A hatókör elvesztésének megelőzése érdekében ne törölje vagy módosítsa a szolgáltatásnevet. Ha véletlenül törölve van, a szinkronizálási hatókör nem állítható helyre. 

Tartsa szem előtt az alábbi figyelmeztetéseket, ha módosítja a szinkronizálási hatókört:

- Teljes szinkronizálás történik.
- A felügyelt tartományban már nem szükséges objektumok törlődnek. Az új objektumok a felügyelt tartományban jönnek létre.

További információ a szinkronizálási folyamatról: a [Azure ad Domain Services szinkronizálásának megismerése][concepts-sync].

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShell-parancsfájl hatókörön belüli szinkronizáláshoz

A hatókörrel rendelkező szinkronizálás PowerShell használatával történő konfigurálásához először mentse a következő parancsfájlt egy nevű fájlba `Select-GroupsToSync.ps1` .

Ez a parancsfájl az Azure AD DSt konfigurálja a kiválasztott csoportok Azure AD-vel való szinkronizálásához. A rendszer a megadott csoportok részét képező összes felhasználói fiókot szinkronizálja a felügyelt tartományba.

Ez a szkript a cikk további lépéseiben található.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>Hatókörön belüli szinkronizálás engedélyezése

A csoport alapú hatókörű szinkronizálás felügyelt tartományon való engedélyezéséhez hajtsa végre a következő lépéseket:

1. Először állítsa be az *"filteredSync" = "enabled* " beállítást az Azure AD DS erőforráson, majd frissítse a felügyelt tartományt.

    Ha a rendszer kéri, adja meg egy *globális rendszergazda* hitelesítő adatait, hogy bejelentkezzen az Azure ad [-][Connect-AzureAD] bérlőbe a AzureAD parancsmag használatával:

    ```powershell
    # Connect to your Azure AD tenant
    Connect-AzureAD

    # Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    # Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    # Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Most meg kell adnia azoknak a csoportoknak a listáját, amelyek felhasználóit szinkronizálni kell a felügyelt tartományba.

    Futtassa a `Select-GroupsToSync.ps1` szkriptet, és adja meg a szinkronizálandó csoportok listáját. A következő példában a szinkronizálandó csoportok a *GroupName1* és a *GroupName2*.

    > [!WARNING]
    > A hatókörön belüli szinkronizáláshoz a csoportok listájában szerepelnie kell a *HRE DC-rendszergazdák* csoportnak. Ha nem tartalmazza ezt a csoportot, a felügyelt tartomány használhatatlan.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

A szinkronizálás hatókörének módosítása azt eredményezi, hogy a felügyelt tartomány újraszinkronizálja az összes adatot. A felügyelt tartományban már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt is igénybe vehet.

## <a name="modify-scoped-synchronization"></a>Hatókörön belüli szinkronizálás módosítása

Azon csoportok listájának módosításához, amelyek felhasználóit szinkronizálni kell a felügyelt tartományba, futtassa a `Select-GroupsToSync.ps1` parancsfájlt, és adja meg a szinkronizálandó csoportok új listáját.

A következő példában a szinkronizálandó csoportok már nem tartalmazzák a *GroupName2*, és most már tartalmazza a *GroupName3*.

> [!WARNING]
> A hatókörön belüli szinkronizáláshoz a csoportok listájában szerepelnie kell a *HRE DC-rendszergazdák* csoportnak. Ha nem tartalmazza ezt a csoportot, a felügyelt tartomány használhatatlan.

Ha a rendszer kéri, adja meg egy *globális rendszergazda* hitelesítő adatait, hogy bejelentkezzen az Azure ad [-][Connect-AzureAD] bérlőbe a AzureAD parancsmag használatával:

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

A szinkronizálás hatókörének módosítása azt eredményezi, hogy a felügyelt tartomány újraszinkronizálja az összes adatot. A felügyelt tartományban már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt is igénybe vehet.

## <a name="disable-scoped-synchronization"></a>Hatókörön belüli szinkronizálás letiltása

Ha le szeretné tiltani a csoport alapú hatókörű szinkronizálást egy felügyelt tartományon, állítsa a *"filteredSync" = "Letiltva"* lehetőséget az Azure AD DS erőforráson, majd frissítse a felügyelt tartományt. Ha elkészült, az összes felhasználó és csoport szinkronizálásra van beállítva az Azure AD-ből.

Ha a rendszer kéri, adja meg egy *globális rendszergazda* hitelesítő adatait, hogy bejelentkezzen az Azure ad [-][Connect-AzureAD] bérlőbe a AzureAD parancsmag használatával:

```powershell
# Connect to your Azure AD tenant
Connect-AzureAD

# Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

# Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

# Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

A szinkronizálás hatókörének módosítása azt eredményezi, hogy a felügyelt tartomány újraszinkronizálja az összes adatot. A felügyelt tartományban már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

További információ a szinkronizálási folyamatról: a [Azure ad Domain Services szinkronizálásának megismerése](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
