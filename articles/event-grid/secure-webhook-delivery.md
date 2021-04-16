---
title: Biztonságos webhook-teljesítés az Azure AD-val Azure Event Grid
description: Ismerteti, hogyan kézbesítheti az eseményeket a Azure Active Directory által védett HTTPS-Azure Event Grid
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 6a0f9059e17d96d497b425abc9749e69c5ab4d41
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575547"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Események közzététele az Azure Active Directory által védett végpontokon
Ez a cikk azt ismerteti, hogyan használható Azure Active Directory (Azure AD) az esemény-előfizetés és a **webhook-végpont közötti kapcsolat biztonságossá való útjára.**  Az Azure AD-alkalmazások és -szolgáltatásnévk áttekintését lásd: [A Microsoft identitásplatformjának (v2.0) áttekintése.](../active-directory/develop/v2-overview.md)

Ez a cikk a Azure Portal a bemutatóhoz, de a funkció a parancssori felület, a PowerShell vagy az SDK-k használatával is engedélyezhető.

> [!IMPORTANT]
> 2021. március 30-án az esemény-előfizetések létrehozása vagy frissítése részeként további hozzáférés-ellenőrzést vezettünk be a biztonsági rések kezelése érdekében. Az előfizető ügyfél szolgáltatásnévnek tulajdonosnak kell lennie, vagy hozzá kell rendelnie egy szerepkört a célalkalmazás szolgáltatásnévhez. Konfigurálja újra az AAD-alkalmazást az alábbi új utasítások szerint.


## <a name="create-an-azure-ad-application"></a>Azure AD-alkalmazás létrehozása
Regisztrálja a webhookot az Azure AD-ban úgy, hogy létrehoz egy Azure AD-alkalmazást a védett végponthoz. Lásd: [Forgatókönyv: Védett webes API.](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview) Konfigurálja a védett API-t úgy, hogy egy démonalkalmazás hívja meg.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Az Event Grid engedélyezése az Azure AD-alkalmazás használatára
Ez a szakasz bemutatja, hogyan engedélyezheti a Event Grid az Azure AD-alkalmazás használatát. 

> [!NOTE]
> A szkript végrehajtásához [az Azure AD](../active-directory/roles/permissions-reference.md#all-roles) alkalmazás-rendszergazdai szerepkörének kell lennie.

### <a name="connect-to-your-azure-tenant"></a>Csatlakozás az Azure-bérlőhöz
Először csatlakozzon az Azure-bérlőhöz az `Connect-AzureAD` paranccsal. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Microsoft.EventGrid-szolgáltatásnév létrehozása
Futtassa a következő szkriptet a **Microsoft.EventGrid szolgáltatásnévének** létrehozásához, ha az még nem létezik. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else {
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Szerepkör létrehozása az alkalmazáshoz   
Az alábbi szkript futtatásával hozzon létre egy szerepkört az Azure AD-alkalmazáshoz. Ebben a példában a szerepkör neve: **AzureEventGridSecureWebhookSubscriber**. Módosítsa a PowerShell-szkripteket az Azure AD-bérlőazonosító és az `$myTenantId` `$myAzureADApplicationObjectId` Azure AD-alkalmazás objektumazonosítójának használatára

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
} else {      
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

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>Szerepkör-hozzárendelés létrehozása az esemény-előfizetést létrehozására használt ügyfélhez
A szerepkör-hozzárendelést az AAD-alkalmazás webhook-Azure AD alkalmazás kell létrehoznia, vagy az esemény-előfizetést létrehozó AAD-felhasználónak. Használja az alábbi szkriptek valamelyikét attól függően, hogy egy AAD-alkalmazás vagy AAD-felhasználó létrehozza-e az esemény-előfizetést.

> [!IMPORTANT]
> A biztonsági rések kezelése érdekében 2021. március 30-án az esemény-előfizetések létrehozása vagy frissítése részeként további hozzáférés-ellenőrzést vezettünk be. Az előfizető ügyfél szolgáltatásának tulajdonosnak kell lennie, vagy hozzá kell rendelnie egy szerepkört a célalkalmazás szolgáltatásnévhez. Konfigurálja újra az AAD-alkalmazást az alábbi új utasítások szerint.

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>Szerepkör-hozzárendelés létrehozása esemény-előfizetési AAD-alkalmazáshoz 

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
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>Szerepkör-hozzárendelés létrehozása esemény-előfizetésI AAD-felhasználóhoz 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>Szerepkör-hozzárendelés létrehozása Event Grid szolgáltatásnévhez
A New-AzureADServiceAppRoleAssignment parancs futtatásával rendeljen Event Grid szolgáltatásnévhez az előző lépésben létrehozott szerepkörhöz.

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Futtassa az alábbi parancsokat a később használt információk ki- és kimenetbe való be- és kimenete érdekében.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Az esemény-előfizetés konfigurálása
Esemény-előfizetés létrehozásakor kövesse az alábbi lépéseket:

1. A végpont típusaként válassza a **Web hook lehetőséget.** 
1. Adja meg a végpont **URI-ját.**

    ![Végponttípus kiválasztása webhook](./media/secure-webhook-delivery/select-webhook.png)
1. Válassza az **Esemény-előfizetések** létrehozása lap tetején található **További** szolgáltatások lapot.
1. A További **szolgáltatások lapon** tegye a következőket:
    1. Válassza **az AAD-hitelesítés használata lehetőséget,** és konfigurálja a bérlőazonosítót és az alkalmazásazonosítót:
    1. Másolja ki az Azure AD-bérlőazonosítót a szkript kimenetből, és adja meg az **AAD-bérlő azonosítója mezőben.**
    1. Másolja ki az Azure AD-alkalmazásazonosítót a szkript kimenetből, és adja meg az **AAD-alkalmazásazonosító mezőben.** Másik lehetőségként használhatja az AAD-alkalmazásazonosító URI-ját is. Az alkalmazásazonosító URI-jával kapcsolatos további információkért tekintse meg [ezt a cikket.](../app-service/configure-authentication-provider-aad.md)

        ![Biztonságos webhook művelet](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyelésével kapcsolatban: [Monitorozás Event Grid üzenetek kézbesítése.](monitor-event-delivery.md)
* A hitelesítési kulccsal kapcsolatos további információkért lásd: Event Grid [biztonság és hitelesítés.](security-authentication.md)
* További információ a Azure Event Grid létrehozásáról: Event Grid [előfizetési séma.](subscription-creation-schema.md)
