---
title: Felügyelt identitás hozzárendelése alkalmazás-szerepkörhöz a PowerShell használatával – Azure AD
description: Részletes útmutató felügyelt identitáshoz való hozzáférés másik alkalmazás szerepkörhöz való hozzárendeléshez a PowerShell használatával.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5150c26d67b77008c11764cc6e51ca7085ffcb31
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784910"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Felügyelt identitás hozzáférésének hozzárendelése egy alkalmazás-szerepkörhöz a PowerShell használatával

Az Azure-erőforrások felügyelt identitása identitást biztosít az Azure-szolgáltatásoknak a Azure Active Directory. Anélkül működnek, hogy hitelesítő adatokra lenne szükség a kódban. Az Azure-szolgáltatások ezzel az identitással hitelesítik magukat az Azure AD-hitelesítést támogató szolgáltatásokban. Az alkalmazás-szerepkörök szerepköralapú hozzáférés-vezérlést biztosítanak, és lehetővé teszik a szolgáltatások számára az engedélyezési szabályok implementációját.

Ebből a cikkből megtudhatja, hogyan rendelhet hozzá felügyelt identitást egy másik alkalmazás által elérhetővé tért alkalmazás-szerepkörhöz az Azure AD PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, tekintse meg az [áttekintés szakaszt.](overview.md) Mindenképpen tekintse át a rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt **[identitások közötti különbséget.](overview.md#managed-identity-types)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A példaszk szkriptek futtatásához két lehetőség áll rendelkezésre:
    - Használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amelyet a kódblokkok jobb felső sarkában található **Try It (Próbálja** ki) gombbal nyithat meg.
    - Futtatassa helyileg a szkripteket az [Azure AD PowerShell legújabb verziójának telepítésével.](/powershell/azure/active-directory/install-adv2)

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Felügyelt identitás hozzáférésének hozzárendelése egy másik alkalmazás alkalmazás-szerepköréhez

1. Felügyelt identitás engedélyezése egy Azure-erőforráson, például egy [Azure-beli virtuális gépen.](qs-configure-powershell-windows-vm.md)

1. Keresse meg a felügyelt identitás szolgáltatásnévének objektumazonosítóját.

   **A rendszer által hozzárendelt** felügyelt identitások esetén az objektumazonosítót az Azure Portal az erőforrás Identitás lapján **találja.** Az objektumazonosítót az alábbi PowerShell-szkript használatával is megkeresheti. Szüksége lesz az 1. lépésben létrehozott erőforrás erőforrás-azonosítójára, amely az erőforrás Tulajdonságok lapján, Azure Portal lapon érhető **el.**

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **Felhasználó által hozzárendelt** felügyelt identitás esetén a felügyelt identitás objektumazonosítóját az erőforrás Áttekintés lapján Azure Portal a felügyelt identitás **objektumazonosítóját.** A következő PowerShell-szkript használatával is megkeresheti az objektumazonosítót. Szüksége lesz a felhasználó által hozzárendelt felügyelt identitás erőforrás-azonosítójára.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Hozzon létre egy új alkalmazásregisztrációt, amely azt a szolgáltatást képviseli, amelybe a felügyelt identitás kérést küld. Ha az alkalmazásszerepkrét a felügyelt identitás számára elérhetővé tő API vagy szolgáltatás már rendelkezik szolgáltatásnévvel az Azure AD-bérlőben, hagyja ki ezt a lépést. Ha például hozzáférést szeretne a felügyelt identitás számára a Microsoft Graph API-hoz, kihagyhatja ezt a lépést.

1. Keresse meg a szolgáltatásalkalmazás szolgáltatásnévének objektumazonosítóját. Ezt a következő Azure Portal. Nyissa meg Azure Active Directory a Vállalati  alkalmazások lapot, keresse meg az alkalmazást, és keresse meg az **objektumazonosítót.** A szolgáltatásnév objektumazonosítóját a megjelenített neve alapján is megkeresheti a következő PowerShell-szkript használatával:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Az alkalmazások megjelenítendő nevei nem egyediek, ezért ellenőrizze, hogy a megfelelő alkalmazás szolgáltatásnevét szerezze-e be.

1. Adjon hozzá [egy alkalmazásszerepet](../develop/howto-add-app-roles-in-azure-ad-apps.md) a 3. lépésben létrehozott alkalmazáshoz. A szerepkört létrehozhatja a Azure Portal vagy a Microsoft Graph. Hozzáadhat például egy alkalmazás-szerepkört az alábbihoz hasonlóval:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Rendelje hozzá az alkalmazás-szerepkört a felügyelt identitáshoz. Az alkalmazás-szerepkör hozzárendeléséhez a következő információkra lesz szüksége:
    * `managedIdentityObjectId`: a felügyelt identitás szolgáltatásnévének objektumazonosítója, amelyet a 2. lépésben talált.
    * `serverServicePrincipalObjectId`: a kiszolgálóalkalmazás szolgáltatásnévének objektumazonosítója, amelyet a 4. lépésben talált.
    * `appRoleId`: a kiszolgálóalkalmazás által felfedett alkalmazás-szerepkör azonosítója, amelyet az 5. lépésben generált – a példában az alkalmazás szerepkör-azonosítója `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   A szerepkör-hozzárendelés hozzáadásához hajtsa végre a következő PowerShell-szkriptet:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Teljes szkript

Ez a példaszk szkript bemutatja, hogyan rendelheti hozzá egy Azure-webalkalmazás felügyelt identitását egy alkalmazás-szerepkörhöz.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrások felügyelt identitásának áttekintése](overview.md)
- A felügyelt identitás Azure-beli virtuális gépen való engedélyezéséről lásd: Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálása [a PowerShell használatával.](qs-configure-powershell-windows-vm.md)
