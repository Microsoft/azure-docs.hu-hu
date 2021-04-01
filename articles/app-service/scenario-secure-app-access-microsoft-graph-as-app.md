---
title: Oktatóanyag – a webalkalmazás a Microsoft Graphként fér hozzá az alkalmazáshoz | Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan férhet hozzá a Microsoft Graph lévő adataihoz a felügyelt identitások használatával.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 06837ab0f4685787f8d2615e81d0405fdb8ec711
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99062560"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Oktatóanyag: hozzáférés Microsoft Graph egy biztonságos alkalmazásból az alkalmazással

Megtudhatja, hogyan érheti el Microsoft Graph egy Azure App Serviceon futó webalkalmazásból.

:::image type="content" alt-text="A Microsoft Graph elérését bemutató diagram." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Meg szeretné hívni Microsoft Graph a webalkalmazáshoz. A webalkalmazások adatokhoz való hozzáférésének biztonságos módja, ha [rendszerhez rendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md)használ. A Azure Active Directory felügyelt identitása lehetővé teszi, hogy a App Service szerepköralapú hozzáférés-vezérléssel (RBAC) keresztül hozzáférjen az erőforrásokhoz az alkalmazás hitelesítő adatainak megadása nélkül. Miután felügyelt identitást rendelt a webalkalmazáshoz, az Azure gondoskodik a tanúsítványok létrehozásáról és elosztásáról. Nem kell aggódnia a titkok vagy az alkalmazások hitelesítő adatainak kezelésével kapcsolatban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Rendszerhez rendelt felügyelt identitás létrehozása egy webalkalmazásban.
> * Microsoft Graph API-engedélyek hozzáadása felügyelt identitáshoz.
> * Meghívása Microsoft Graph egy webalkalmazásból felügyelt identitások használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Olyan Azure App Service futó webalkalmazás, amelyen engedélyezve van a [app Service hitelesítés/engedélyezési modul](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-app"></a>Felügyelt identitás engedélyezése az alkalmazásban

Ha a Visual studión keresztül hozza létre és teszi közzé a webalkalmazást, a felügyelt identitás engedélyezve lett az alkalmazásban. Az App Service-ben válassza az **identitás** elemet a bal oldali ablaktáblán, majd válassza a **rendszer hozzárendelve** lehetőséget. Ellenőrizze, hogy az **állapot** be **van-e** állítva. Ha nem, válassza a **Mentés** lehetőséget, majd válassza az **Igen** lehetőséget a rendszer által hozzárendelt felügyelt identitás engedélyezéséhez. Ha a felügyelt identitás engedélyezve van, az állapot be értékre van állítva **, és az** objektumazonosító elérhető.

Jegyezze fel az **objektumazonosító** értékét, amelyet a következő lépésben kell végrehajtania.

:::image type="content" alt-text="A rendszer által hozzárendelt identitást megjelenítő képernyőkép." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Hozzáférés biztosítása Microsoft Graph

A Microsoft Graphhoz való hozzáféréskor a felügyelt identitásnak megfelelő engedélyekkel kell rendelkeznie a végrehajtani kívánt művelethez. Jelenleg nincs lehetőség ilyen engedélyek hozzárendelésére a Azure Portalon keresztül. A következő parancsfájl hozzáadja a kért Microsoft Graph API-engedélyeket a felügyelt identitási szolgáltatás egyszerű objektumához.

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

A parancsfájl végrehajtása után a [Azure Portal](https://portal.azure.com) ellenőrizheti, hogy a kért API-engedélyek hozzá vannak-e rendelve a felügyelt identitáshoz.

Lépjen **Azure Active Directory**, majd válassza a **vállalati alkalmazások** lehetőséget. Ez az ablaktábla a bérlő összes egyszerű szolgáltatását megjeleníti. A **minden alkalmazás** területen válassza ki a felügyelt identitáshoz tartozó szolgáltatásnevet. 

Ha ezt az oktatóanyagot követi, két egyszerű szolgáltatásnév létezik (például SecureWebApp2020094113531). A **Kezdőlap URL-címmel** rendelkező egyszerű szolgáltatás a bérlőben található webalkalmazást jelöli. A **Kezdőlap URL-címét** nem tartalmazó egyszerű szolgáltatásnév a webalkalmazás rendszer által hozzárendelt felügyelt identitását jelöli. A felügyelt identitás **objektumazonosító** -értéke megegyezik a korábban létrehozott felügyelt identitás OBJEKTUMAZONOSÍTÓ-azonosítójával.

Válassza ki az egyszerű szolgáltatást a felügyelt identitáshoz.

:::image type="content" alt-text="A minden alkalmazás lehetőséget megjelenítő képernyőkép." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

Az **Áttekintés** területen válassza az **engedélyek** lehetőséget, majd tekintse meg a Microsoft Graph további engedélyeit.

:::image type="content" alt-text="Képernyőkép, amely az engedélyek panelt jeleníti meg." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Microsoft Graph (.NET) hívása

A [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy jogkivonat hitelesítő adatának beszerzésére szolgál a kód számára, amely engedélyezi a kérések Microsoft Graph. Hozza létre a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy példányát, amely a felügyelt identitás használatával beolvassa a jogkivonatokat, és csatolja őket a szolgáltatás ügyfeléhez. A következő kódrészlet beolvassa a hitelesített jogkivonat hitelesítő adatait, és a használatával létrehoz egy szolgáltatás-ügyfél objektumot, amely a csoportban lévő felhasználókat kéri le.

Ha ezt a kódot egy minta alkalmazás részeként szeretné látni, tekintse [meg a githubon található mintát](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity).

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>A Microsoft. Identity. Web. MicrosoftGraph ügyféloldali kódtár csomagjának telepítése

Telepítse a [Microsoft. Identity. Web. MicrosoftGraph NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) a projektbe a .net Core parancssori felület vagy a Visual Studióban található Package Manager konzol használatával.

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Nyisson meg egy parancssort, és váltson arra a könyvtárra, amely tartalmazza a projektfájlt.

Futtassa a telepítési parancsokat.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

# <a name="package-manager"></a>[Csomagkezelő](#tab/package-manager)

Nyissa meg a projektet/megoldást a Visual Studióban, és nyissa meg **a konzolt a**  >  **NuGet Package Manager** csomagkezelő  >  **Console** paranccsal.

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

Ha elkészült az Oktatóanyaggal, és már nincs szüksége a webalkalmazásra vagy a kapcsolódó erőforrásokra, [törölje a létrehozott erőforrásokat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Rendszerhez rendelt felügyelt identitás létrehozása egy webalkalmazásban.
> * Microsoft Graph API-engedélyek hozzáadása felügyelt identitáshoz.
> * Meghívása Microsoft Graph egy webalkalmazásból felügyelt identitások használatával.

Megtudhatja, hogyan csatlakozhat egy [.net Core-alkalmazáshoz](tutorial-dotnetcore-sqldb-app.md), [Python-alkalmazáshoz](tutorial-python-postgresql-app.md), [Java-alkalmazáshoz](tutorial-java-spring-cosmosdb.md)vagy [Node.js-alkalmazáshoz](tutorial-nodejs-mongodb-app.md) egy adatbázishoz.