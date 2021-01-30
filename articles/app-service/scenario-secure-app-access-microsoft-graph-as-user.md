---
title: 'Oktatóanyag – a webalkalmazás Microsoft Graph felhasználóként éri el a következőt: | Azure'
description: Ebből az oktatóanyagból megtudhatja, hogyan férhet hozzá Microsoft Graphhoz a bejelentkezett felhasználóhoz.
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
ms.openlocfilehash: 3413c1a3f27b48c60ae730ad230c653928702faa
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063383"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>Oktatóanyag: hozzáférés Microsoft Graph a biztonságos alkalmazásból felhasználóként

Megtudhatja, hogyan érheti el Microsoft Graph egy Azure App Serviceon futó webalkalmazásból.

:::image type="content" alt-text="A Microsoft Graph elérését bemutató diagram." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Hozzá kívánja adni az Microsoft Graphhoz való hozzáférést a webalkalmazásból, és végrehajthat valamilyen műveletet a bejelentkezett felhasználóként. Ez a szakasz azt ismerteti, hogyan lehet delegált engedélyeket adni a webalkalmazásnak, és beolvasni a bejelentkezett felhasználó profiljának adatait Azure Active Directory (Azure AD) szolgáltatásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Delegált engedélyek megadása egy webalkalmazáshoz.
> * Microsoft Graph meghívása egy webalkalmazásból a bejelentkezett felhasználó számára.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Olyan Azure App Service futó webalkalmazás, amelyen engedélyezve van a [app Service hitelesítés/engedélyezési modul](scenario-secure-app-authentication-app-service.md).

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>Előtér-hozzáférés biztosítása a híváshoz Microsoft Graph

Most, hogy engedélyezte a hitelesítést és az engedélyezést a webalkalmazásban, a webalkalmazás regisztrálva van a Microsoft Identity platformon, és egy Azure AD-alkalmazás támogatja. Ebben a lépésben a webalkalmazás engedélyt ad a felhasználónak a Microsoft Graph eléréséhez. (Technikai szempontból a webalkalmazás Azure AD-alkalmazásának engedélyeket kell biztosítania a felhasználó számára a Microsoft Graph Azure AD-alkalmazás eléréséhez.)

A [Azure Portal](https://portal.azure.com) menüben válassza a **Azure Active Directory** lehetőséget, vagy keresse meg, majd válassza a **Azure Active Directory** lehetőséget bármelyik lapon.

Válassza ki **Alkalmazásregisztrációk**  >  **tulajdonában lévő alkalmazások**  >  **megtekintheti a címtárban található összes alkalmazást**. Válassza ki a webalkalmazás nevét, majd válassza az **API-engedélyek** lehetőséget.

Válassza **az engedély hozzáadása** lehetőséget, majd válassza a Microsoft API-k és a Microsoft Graph lehetőséget.

Válassza a **delegált engedélyek** lehetőséget, majd válassza a **felhasználó. olvasás** elemet a listából. Válassza az **engedélyek hozzáadása** lehetőséget.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>Az App Service konfigurálása használható hozzáférési jogkivonat visszaadására

A webalkalmazás most már rendelkezik a szükséges engedélyekkel a bejelentkezett felhasználóként való Microsoft Graph eléréséhez. Ebben a lépésben App Service hitelesítését és engedélyezését konfigurálja úgy, hogy használható hozzáférési jogkivonatot biztosítson a Microsoft Graph eléréséhez. Ebben a lépésben szüksége lesz az alsóbb rétegbeli szolgáltatás ügyfél-vagy alkalmazás-AZONOSÍTÓJÁRA (Microsoft Graph). Microsoft Graph alkalmazás *-azonosítója 00000003-0000-0000-C000-000000000000*.

> [!IMPORTANT]
> Ha nem konfigurálja a App Service egy használható hozzáférési token visszaadására, akkor hibaüzenet jelenik meg, ```CompactToken parsing failed with error code: 80049217``` Amikor a kódban Microsoft Graph API-kat hív meg.

Nyissa meg a [Azure erőforrás-kezelőt](https://resources.azure.com/) , és használja az erőforrás-fát, és keresse meg a webalkalmazást. Az erőforrás URL-címének hasonlónak kell lennie `https://resources.azure.com/subscriptions/subscription-id/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914` .

A Azure Erőforrás-kezelő ekkor megnyílik az erőforrás-fában kiválasztott webalkalmazással. Az oldal tetején válassza az **írás/írás** lehetőséget az Azure-erőforrások szerkesztésének engedélyezéséhez.

A bal oldali böngészőben bontsa ki a következőt: **config**  >  **authsettings elemre**.

A **authsettings elemre** nézetben válassza a **Szerkesztés** lehetőséget. Állítsa be a ```additionalLoginParams``` következő JSON-karakterláncot a másolt ügyfél-azonosító használatával.

```json
"additionalLoginParams": ["response_type=code id_token","resource=00000003-0000-0000-c000-000000000000"],
```

Mentse a beállításokat a **put** lehetőség kiválasztásával. A beállítás érvénybe léptetése több percet is igénybe vehet. A webalkalmazása úgy van konfigurálva, hogy a megfelelő hozzáférési jogkivonattal hozzáférhessen Microsoft Graphhoz. Ha nem, a Microsoft Graph hibát jelez, ami azt jelzi, hogy a Compact token formátuma helytelen.

## <a name="call-microsoft-graph-net"></a>Microsoft Graph (.NET) hívása

A webalkalmazás már rendelkezik a szükséges engedélyekkel, és hozzáadja Microsoft Graph ügyfél-AZONOSÍTÓját is a bejelentkezési paraméterekhez. A [Microsoft. Identity. Web kódtár](https://github.com/AzureAD/microsoft-identity-web/)használatával a webalkalmazás hozzáférési jogkivonatot kap a hitelesítéshez Microsoft Graph. A 1.2.0 és újabb verziókban a Microsoft. Identity. Web könyvtár integrálódik a-val, és a App Service hitelesítés/engedélyezési modul mellett is futtatható. A Microsoft. Identity. Web észleli, hogy a webalkalmazás a App Serviceban található, és lekéri a hozzáférési jogkivonatot a App Service Authentication/Authorization modulból. A hozzáférési jogkivonatot ezután a Microsoft Graph API-val hitelesített kérelmekkel együtt továbbítja a rendszer.

Ha ezt a kódot egy minta alkalmazás részeként szeretné látni, tekintse [meg a githubon található mintát](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf).

> [!NOTE]
> A Microsoft. Identity. Web függvénytár nem szükséges az alapszintű hitelesítéshez/engedélyezéshez, illetve a kérelmek Microsoft Graph-vel történő hitelesítéséhez a webalkalmazásban. Az [alsóbb rétegbeli API-k biztonságosan hívhatók](tutorial-auth-aad.md#call-api-securely-from-server-code) , és csak a app Service hitelesítés/engedélyezési modul engedélyezett.
> 
> Azonban a App Service hitelesítés/engedélyezés az alapszintű hitelesítési forgatókönyvekhez lett tervezve. Összetettebb forgatókönyvek esetén (például egyéni jogcímek kezelésére) a Microsoft. Identity. Web Library vagy a [Microsoft Authentication Library](../active-directory/develop/msal-overview.md)szükséges. Az elején még egy beállítás és konfiguráció is működik, de a Microsoft. Identity. Web függvénytár a App Service hitelesítési/engedélyezési modullal együtt futhat. Később, amikor a webalkalmazásnak összetettebb forgatókönyvek kezelésére van szüksége, letilthatja a App Service hitelesítési/engedélyezési modulját, a Microsoft. Identity. web pedig már az alkalmazás részét képezi.

### <a name="install-client-library-packages"></a>Ügyféloldali függvénytár-csomagok telepítése

Telepítse a [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) és a [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet-csomagokat a projektbe a Visual Studióban található .net Core parancssori felület vagy a Package Manager konzol használatával.

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Nyisson meg egy parancssort, és váltson arra a könyvtárra, amely tartalmazza a projektfájlt.

Futtassa a telepítési parancsokat.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[Csomagkezelő](#tab/package-manager)

Nyissa meg a projektet/megoldást a Visual Studióban, és nyissa meg **a konzolt a**  >  **NuGet Package Manager** csomagkezelő  >  **Console** paranccsal.

Futtassa a telepítési parancsokat.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

A *Startup.cs* fájlban a ```AddMicrosoftIdentityWebApp``` metódus hozzáadja a Microsoft. Identity. Web fájlt a webalkalmazáshoz. A ```AddMicrosoftGraph``` metódus Microsoft Graph támogatást tesz lehetővé.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

A *AzureAd* meghatározza a Microsoft. Identity. Web könyvtár konfigurációját. A [Azure Portal](https://portal.azure.com)válassza a **Azure Active Directory** lehetőséget a portál menüben, majd válassza a **Alkalmazásregisztrációk** lehetőséget. Válassza ki az App Service hitelesítési/engedélyezési moduljának engedélyezésekor létrehozott alkalmazás-regisztrációt. (Az alkalmazás regisztrációjának meg kell egyeznie a webalkalmazás nevével.) A bérlői azonosítót és az ügyfél-azonosítót az alkalmazás regisztrációjának áttekintése lapon találja. A tartománynév a bérlő Azure AD Áttekintés oldalán található.

A *Graph* meghatározza a Microsoft Graph végpontot és az alkalmazás által igényelt kezdeti hatóköröket.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

Az alábbi példa azt mutatja be, hogyan hívható meg a Microsoft Graph a bejelentkezett felhasználóként, és hogyan kérhet le néhány felhasználói adatot. A ```GraphServiceClient``` rendszer befecskendezi az objektumot a vezérlőbe, és a Microsoft. Identity. Web könyvtár használatával konfigurálta a hitelesítést.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az Oktatóanyaggal, és már nincs szüksége a webalkalmazásra vagy a kapcsolódó erőforrásokra, [törölje a létrehozott erőforrásokat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Delegált engedélyek megadása egy webalkalmazáshoz.
> * Microsoft Graph meghívása egy webalkalmazásból a bejelentkezett felhasználó számára.

> [!div class="nextstepaction"]
> [Az App Service Microsoft Graph alkalmazásként fér hozzá](scenario-secure-app-access-microsoft-graph-as-app.md)