---
title: Biztonságos webhook-kézbesítés az Azure AD-vel Azure Event Grid
description: Ismerteti, hogyan lehet eseményeket kézbesíteni a Azure Active Directory által védett HTTPS-végpontoknak Azure Event Grid használatával
ms.topic: how-to
ms.date: 03/20/2021
ms.openlocfilehash: 1298910db78ba468dd9744e84ee4629161e0a776
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076036"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Események közzététele az Azure Active Directory által védett végpontokon
Ez a cikk azt ismerteti, hogyan használható a Azure Active Directory (Azure AD) az esemény- **előfizetés** és a **webhook-végpont** közötti kapcsolat biztonságossá tételéhez. Az Azure AD-alkalmazások és-szolgáltatások áttekintését lásd: [Microsoft Identity platform (v 2.0) – áttekintés](../active-directory/develop/v2-overview.md).

Ez a cikk a bemutató Azure Portal használja, de a funkció a CLI, a PowerShell vagy az SDK-k használatával is engedélyezhető.


## <a name="create-an-azure-ad-application"></a>Azure AD-alkalmazás létrehozása
Regisztrálja webhookját az Azure AD-ben egy Azure AD-alkalmazás létrehozásával a védett végpont számára. Lásd [: forgatókönyv: védett webes API](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview). Beállíthatja, hogy a védett API-t egy Daemon-alkalmazás hívja meg.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Az Azure AD-alkalmazás használatának engedélyezése Event Grid
Ez a szakasz bemutatja, hogyan engedélyezheti Event Grid az Azure AD-alkalmazás használatát. 

> [!NOTE]
> A szkript végrehajtásához az [Azure ad alkalmazás-rendszergazda szerepkör](../active-directory/roles/permissions-reference.md#all-roles) tagjának kell lennie.

### <a name="connect-to-your-azure-tenant"></a>Kapcsolódás az Azure-bérlőhöz
Először kapcsolódjon az Azure-bérlőhöz a `Connect-AzureAD` parancs használatával. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>A Microsoft. EventGrid egyszerű szolgáltatásnév létrehozása
A következő parancsfájl futtatásával hozza létre a **Microsoft. EventGrid** szolgáltatáshoz tartozó egyszerű szolgáltatást, ha még nem létezik. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Szerepkör létrehozása az alkalmazáshoz   
Futtassa az alábbi szkriptet az Azure AD-alkalmazáshoz tartozó szerepkör létrehozásához. Ebben a példában a szerepkör neve: **AzureEventGridSecureWebhookSubscriber**. Módosítsa a PowerShell-parancsfájlt az `$myTenantId` Azure ad-bérlői azonosító használatára, valamint az `$myAzureADApplicationObjectId` Azure ad-alkalmazás Object ID azonosítójával

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.AllowedMemberTypes.Add("User");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása
A szerepkör-hozzárendelést létre kell hozni a webhook Azure AD alkalmazás a HRE alkalmazáshoz vagy a HRE-felhasználóhoz az esemény-előfizetés létrehozásához. Használja az alábbi parancsfájlok egyikét attól függően, hogy egy HRE-alkalmazás vagy egy HRE-felhasználó létrehozza-e az esemény-előfizetést.

#### <a name="option-a-create-a-role-assignment-for-event-subscription-aad-app"></a>A. lehetőség. hozzon létre egy szerepkör-hozzárendelést az esemény-előfizetés HRE alkalmazásához 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="option-b-create-a-role-assignment-for-event-subscription-aad-user"></a>B. lehetőség: szerepkör-hozzárendelés létrehozása az esemény-előfizetés HRE felhasználója számára 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
New-AzureADUserAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Event Grid egyszerű szolgáltatásnév hozzáadása a szerepkörhöz
Futtassa az New-AzureADServiceAppRoleAssignment parancsot Event Grid egyszerű szolgáltatásnév hozzárendeléséhez az előző lépésben létrehozott szerepkörhöz.

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Futtassa az alábbi parancsokat a később használt információk kiírásához.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Az esemény-előfizetés konfigurálása
Esemény-előfizetés létrehozásakor kövesse az alábbi lépéseket:

1. Válassza ki a végpont típusát **Webhookként**. 
1. A végpont **URI azonosítójának** megadása.

    ![Válassza ki a végpont típusát webhook](./media/secure-webhook-delivery/select-webhook.png)
1. Válassza az **esemény-előfizetések létrehozása** lap tetején található **További szolgáltatások** fület.
1. A **További szolgáltatások** lapon hajtsa végre a következő lépéseket:
    1. Válassza a **HRE-hitelesítés használata** lehetőséget, és konfigurálja a bérlői azonosítót és az alkalmazás azonosítóját:
    1. Másolja az Azure AD-bérlő AZONOSÍTÓját a parancsfájl kimenetéről, és adja meg a **HRE-bérlő azonosítója** mezőben.
    1. Másolja az Azure AD-alkalmazás AZONOSÍTÓját a parancsfájl kimenetéről, majd adja meg a **HRE alkalmazás-azonosító** mezőjében.

        ![Biztonságos webhook művelet](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* További információ a hitelesítési kulcsról: [Event Grid biztonság és hitelesítés](security-authentication.md).
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
