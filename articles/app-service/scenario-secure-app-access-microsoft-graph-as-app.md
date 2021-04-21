---
title: Oktatóanyag – A webalkalmazás Microsoft Graph alkalmazásként fér hozzá az alkalmazáshoz| Azure
description: Ez az oktatóanyag bemutatja, hogyan férhet hozzá az adatokhoz Microsoft Graph felügyelt identitások használatával.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurepowershell
ms.openlocfilehash: 5bb52799836b1975de9d936e04fb53987effb300
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832625"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Oktatóanyag: Microsoft Graph alkalmazásból származó adatok elérése alkalmazásként

Útmutató a Microsoft Graph futó webalkalmazásból való Azure App Service.

:::image type="content" alt-text="A hozzáférés-Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Meg szeretné hívni Microsoft Graph webalkalmazáshoz. A webalkalmazás adatokhoz való hozzáférésének biztonságos módja a rendszer által hozzárendelt [felügyelt identitás használata.](../active-directory/managed-identities-azure-resources/overview.md) A felügyelt identitások Azure Active Directory lehetővé App Service hozzáférését az erőforrásokhoz szerepköralapú hozzáférés-vezérléssel (RBAC) anélkül, hogy alkalmazás-hitelesítő adatokra volna szükség. Miután hozzárendelt egy felügyelt identitást a webalkalmazáshoz, az Azure gondoskodik a tanúsítványok létrehozásáról és terjesztéséről. Nem kell aggódnia a titkos kulcsok vagy az alkalmazás hitelesítő adatainak kezelésével.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Rendszer által hozzárendelt felügyelt identitás létrehozása egy webalkalmazásban.
> * Api Microsoft Graph engedélyek hozzáadása felügyelt identitáshoz.
> * Hívás Microsoft Graph webalkalmazásból felügyelt identitások használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy webalkalmazás, amely a Azure App Service fut, App Service [hitelesítési/engedélyezési modul engedélyezve van.](scenario-secure-app-authentication-app-service.md)

## <a name="enable-managed-identity-on-app"></a>Felügyelt identitás engedélyezése az alkalmazásban

Ha a webalkalmazást a Visual Studio hozza létre és teszi közzé, a felügyelt identitás engedélyezve lett az alkalmazásban. Az App Service-beli bal oldali panelen válassza az **Identitás,** majd a **Rendszer által hozzárendelt lehetőséget.** Ellenőrizze, hogy **az Állapot** be van-e **állítva.** Ha nem, válassza a **Mentés,** majd az **Igen** lehetőséget a rendszer által hozzárendelt felügyelt identitás engedélyezéséhez. Ha a felügyelt identitás engedélyezve van, az állapot **Be** lesz kapcsolva, és az objektumazonosító elérhető.

Jegyezze fel az **Objektumazonosító** értékét, amelyre a következő lépésben lesz szüksége.

:::image type="content" alt-text="Képernyőkép a rendszer által hozzárendelt identitásról." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Hozzáférés megadása a Microsoft Graph

A felügyelt Microsoft Graph hozzáférése során a felügyelt identitásnak megfelelő engedélyekkel kell rendelkeznie a végrehajtani kívánó művelethez. Jelenleg nincs lehetőség ilyen engedélyek hozzárendelésére a Azure Portal. A következő szkript hozzáadja a kért API Microsoft Graph api-engedélyeket a felügyelt identitás szolgáltatásnév-objektumához.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
# First result should be AppId 00000003-0000-0000-c000-000000000000
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph" | Select-Object -first 1

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spId/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

A szkript végrehajtása után ellenőrizheti a [](https://portal.azure.com) Azure Portal, hogy a kért API-engedélyek hozzá vannak-e rendelve a felügyelt identitáshoz.

Válassza a **Azure Active Directory,** majd a Vállalati **alkalmazások lehetőséget.** Ezen a panelen megjelenik a bérlőben az összes szolgáltatásnév. A **Minden alkalmazás mezőben** válassza ki a felügyelt identitás szolgáltatását. 

Ha ezt az oktatóanyagot követi, két szolgáltatásnév ugyanazokkal a megjelenített névvel rendelkezik (például SecureWebApp2020094113531). A Kezdőlap **URL-címével** rendelkezik szolgáltatásnév a bérlőben található webalkalmazást jelöli. A Kezdőlap **URL-címe** nélküli szolgáltatásnév a webalkalmazás rendszer által hozzárendelt felügyelt identitását jelöli. A **felügyelt identitás** Objektumazonosító értéke megegyezik a korábban létrehozott felügyelt identitás objektumazonosítójával.

Válassza ki a felügyelt identitás szolgáltatásnévét.

:::image type="content" alt-text="A Minden alkalmazás lehetőséget bemutató képernyőkép." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

Az **Áttekintés oldalon** válassza az **Engedélyek** lehetőséget, és láthatja, hogy milyen engedélyeket adott hozzá a Microsoft Graph.

:::image type="content" alt-text="Képernyőkép az Engedélyek panelről." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Hívás Microsoft Graph (.NET)

A [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy jogkivonat hitelesítő adatainak lekérését kéri le a kódhoz, hogy engedélyezze a Microsoft Graph. Hozza létre a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy példányát, amely a felügyelt identitással lekéri a jogkivonatokat, és csatolja őket a szolgáltatás-ügyfélhez. Az alábbi példakód lekérte a hitelesített jogkivonat hitelesítő adatait, és annak használatával létrehoz egy szolgáltatás-ügyfélobjektumot, amely lekérte a csoport felhasználóit.

Ha ezt a kódot egy mintaalkalmazás részeként látja, tekintse meg a [githubon](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity)található mintát.

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>A Microsoft.Identity.Web.MicrosoftGraph ügyféloldali kódtárcsomag telepítése

Telepítse a [Microsoft.Identity.Web.MicrosoftGraph NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) a projektben a .NET Core parancssori felület vagy a Csomagkezelő konzol használatával a Visual Studio.

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Nyisson meg egy parancssort, és váltson arra a könyvtárra, amely a projektfájlt tartalmazza.

Futtassa a telepítési parancsokat.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

# <a name="package-manager"></a>[Csomagkezelő](#tab/package-manager)

Nyissa meg a projektet/megoldást a Visual Studio, majd nyissa meg a konzolt a **Tools**  >  **NuGet Csomagkezelő** Csomagkezelő Console  >  **paranccsal.**

Futtassa a telepítési parancsokat.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph
```

---

### <a name="example"></a>Példa

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel az oktatóanyaggal, és már nincs szüksége a webalkalmazásra vagy a társított erőforrásokra, akkor a létrehozott [erőforrásokat kell megtisztítani.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Rendszer által hozzárendelt felügyelt identitás létrehozása egy webalkalmazásban.
> * Api Microsoft Graph engedélyek hozzáadása felügyelt identitáshoz.
> * Hívás Microsoft Graph webalkalmazásból felügyelt identitások használatával.

Megtudhatja, hogyan csatlakoztathat [egy .NET](tutorial-dotnetcore-sqldb-app.md)Core-alkalmazást, [Python-alkalmazást,](tutorial-python-postgresql-app.md) [Java-alkalmazást](tutorial-java-spring-cosmosdb.md)vagyNode.js [ egy](tutorial-nodejs-mongodb-app.md) adatbázishoz.
