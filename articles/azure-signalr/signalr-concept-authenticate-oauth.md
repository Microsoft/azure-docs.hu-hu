---
title: Útmutató az Azure Signaler szolgáltatás ügyfeleinek hitelesítéséhez
description: Ismerje meg, hogyan implementálhatja saját hitelesítését, és hogyan integrálhatja azt az Azure Signaler szolgáltatással a E2E példa alapján.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cd5b709843ba2d98170aaa58eb5c3cc1ec8883af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734681"
---
# <a name="azure-signalr-service-authentication"></a>Azure SignalR szolgáltatás – hitelesítés

Ez az oktatóanyag a rövid útmutatóban bemutatott csevegőszoba-alkalmazásra épít. Ha még nem végezte el a [Csevegőszoba létrehozása SignalR szolgáltatással](signalr-quickstart-dotnet-core.md) gyakorlatot, először végezze el azt.

Ebben az oktatóanyagban megtanulhatja, hogyan implementálhatja a saját hitelesítését, és hogyan integrálhatja azt a Microsoft Azure SignalR Service szolgáltatással.

A rövid útmutató csevegőszoba-alkalmazásában eredetileg használt hitelesítés túl egyszerű a valós forgatókönyvekhez. Az alkalmazás lehetővé teszi, hogy az ügyfelek megadják a személyazonosságukat, a kiszolgáló pedig egyszerűen elfogadja azt. Ez a módszer nem túl hasznos a valós alkalmazások esetében, ahol a rosszindulatú felhasználók megszemélyesíthetnek más felhasználókat, hogy hozzáférjenek a bizalmas adatokhoz.

A [GitHub](https://github.com/) hitelesítési API-kat biztosít egy népszerű, [OAuth](https://oauth.net/) nevű iparági szabványoknak megfelelő protokoll alapján. Ezek az API-k lehetővé teszik a GitHub-fiókok hitelesítését a külső alkalmazások számára. Az oktatóanyag során ezekkel az API-kkal fogja megvalósítani a hitelesítést egy GitHub-fiókon keresztül, mielőtt engedélyezné az ügyfelek számára, hogy bejelentkezzenek a csevegőszoba-alkalmazásba. A GitHub-fiók hitelesítése után a fiókadatok cookie-ként lesznek hozzáadva, amelyet a webes ügyfél a hitelesítéshez fog használni.

A GitHub által biztosított OAuth-hitelesítési API-kkal kapcsolatos további információkat [a hitelesítés alapjait](https://developer.github.com/v3/guides/basics-of-authentication/) ismertető cikkben találja.

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

Az oktatóanyag kódja letölthető az [AzureSignalR-minták GitHub-adattárjából](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat).

![Azure-ban üzemeltetett teljes OAuth-hitelesítés](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új OAuth-alkalmazás regisztrálása a GitHub-fiókkal
> * Hitelesítésvezérlő hozzáadása a GitHub-hitelesítés támogatásához
> * ASP.NET Core-webalkalmazás üzembe helyezése az Azure-ban

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbi előfeltételekkel kell rendelkeznie:

- Egy [GitHubon](https://github.com/) létrehozott fiók
- [Git](https://git-scm.com/)
- [.NET Core SDK](https://www.microsoft.com/net/download/windows)
- A bash-környezethez konfigurált [Azure Cloud Shell](../cloud-shell/quickstart.md) .
- Töltse le vagy klónozott [AzureSignalR – minta GitHub-](https://github.com/aspnet/AzureSignalR-samples) tárházat.

## <a name="create-an-oauth-app"></a>OAuth-alkalmazás létrehozása

1. Nyisson meg egy webböngészőt, navigáljon a `https://github.com` helyre, és jelentkezzen be a fiókjába.

2. A fiókjához navigáljon a **Beállítások**  >  **fejlesztői beállítások** területen, majd kattintson az **új alkalmazás regisztrálása** vagy az **új OAuth alkalmazás** a *OAuth-alkalmazások* alatt lehetőségre.

3. Adja meg a következő beállításokat az új OAuth-alkalmazás számára, majd kattintson a **Register application (Alkalmazás regisztrálása)** lehetőségre:

    | Beállítás neve | Ajánlott érték | Leírás |
    | ------------ | --------------- | ----------- |
    | Alkalmazásnév | *Azure SignalR Chat* | A GitHub-felhasználónak képesnek kell lennie a hitelesítéssel rendelkező alkalmazás felismerésére és megbízhatóságára.   |
    | Kezdőlap URL-címe | `http://localhost:5000/home` | |
    | Alkalmazás leírása | *Egy chat room-minta, amely az Azure Signaler szolgáltatást használja GitHub-hitelesítéssel* | Az alkalmazás hasznos leírása, amely információkat nyújt az alkalmazás felhasználóinak az alkalmazott hitelesítés környezetéről. |
    | Az engedélyezési visszahívás URL-címe | `http://localhost:5000/signin-github` | Ez az OAuth-alkalmazás legfontosabb beállítása. Ez az a visszahívási URL-cím, amelyet a GitHub a sikeres hitelesítés után visszaad a felhasználónak. Ebben az oktatóanyagban az *AspNet.Security.OAuth.GitHub* csomag alapértelmezett visszahívási URL-címét kell használnia: */signin-github*.  |

4. Az új OAuth-alkalmazás regisztrálását követően adja hozzá az *Ügyfél-azonosítót* és a *Titkos ügyfélkulcsot* a Secret Managerhez az alábbi parancsokkal. Cserélje le a *Your_GitHub_Client_Id* és a *Your_GitHub_Client_Secret* elemeket az OAuth-alkalmazás értékeire.

    ```dotnetcli
    dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
    dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret
    ```

## <a name="implement-the-oauth-flow"></a>Az OAuth-folyamat megvalósítása

### <a name="update-the-startup-class-to-support-github-authentication"></a>A Startup osztály frissítése a GitHub-hitelesítés támogatásához

1. Adjon hozzá egy, a legújabb *Microsoft.AspNetCore.Authentication.Cookies* és *AspNet.Security.OAuth.GitHub* csomagokra mutató hivatkozást, és állítsa vissza az összes csomagot.

    ```dotnetcli
    dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
    dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
    dotnet restore
    ```

1. Nyissa meg a *Startup.cs* fájlt, és adjon hozzá `using` utasításokat az alábbi névterekhez:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. A `Startup` osztály tetején adjon hozzá konstansokat a GitHub OAuth alkalmazáskulcsokat tartalmazó Secret Manager kulcsokhoz.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. Adja hozzá a következő kódot a `ConfigureServices` metódushoz a GitHub OAuth-alkalmazással történő hitelesítés támogatásához:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. Adja hozzá a `GetUserCompanyInfoAsync` segédmetódust a `Startup` osztályhoz.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Frissítse a Startup osztály `Configure` metódusát az alábbi kódsorral, majd mentse a fájlt.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Hitelesítésvezérlő hozzáadása

Ebben a szakaszban meg fog valósítani egy `Login` API-t, amely a GitHub OAuth-alkalmazással hitelesíti az ügyfeleket. A hitelesítés után az API hozzáad egy cookie-t a webes ügyfél válaszához, mielőtt visszairányítaná az ügyfelet a csevegőalkalmazásra. A rendszer ezután ezzel a cookie-val fogja azonosítani az ügyfelet.

1. Adjon hozzá egy új vezérlő kódfájlt a *chattest\Controllers* könyvtárhoz. A fájlnak adja az *AuthController.cs* nevet.

2. Adja hozzá a következő kódot a hitelesítésvezérlőhöz. Ha nem a *chattest* projektkönyvtárat használta, ne felejtse el frissíteni a névteret:

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Mentse a módosításokat.

### <a name="update-the-hub-class"></a>A Hub osztály frissítése

Alapértelmezés szerint amikor a webes ügyfél csatlakozni próbál a SignalR Service szolgáltatáshoz, a csatlakozás egy belsőleg megadott hozzáférési token alapján lesz engedélyezve. Ez a hozzáférési token nincs hozzárendelve hitelesített identitáshoz. Ez a hozzáférés valójában névtelen hozzáférés.

Ebben a szakaszban be fogja kapcsolni a valódi hitelesítést azáltal, hogy hozzáadja a Hub osztályhoz az `Authorize` attribútumot, és frissíti a központ metódusait, hogy azok beolvassák a felhasználónevet a hitelesített felhasználó jogcíméből.

1. Nyissa meg a *Hub\Chat.cs* fájlt, és adjon hozzá hivatkozásokat a következő névterekhez:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Frissítse a központkódot a lent látható módon. Ez a kód hozzáadja az `Authorize` attribútumot a `Chat` osztályhoz, és a felhasználó hitelesített identitását használja a központ metódusaiban. Ezenkívül az `OnConnectedAsync` metódust is hozzáadja, amely naplózni fog egy rendszerüzenet a csevegőszobába minden alkalommal, amikor új ügyfél csatlakozik.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Mentse a módosításokat.

### <a name="update-the-web-client-code"></a>A webes ügyfélkód frissítése

1. Nyissa meg a *wwwroot\index.html* fájlt, és cserélje le a felhasználónevet kérő kódot a hitelesítésvezérlő által visszaadott cookie használatára szolgáló kódra.

    Távolítsa el az alábbi kódot az *index.html* fájlból:

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Illessze be az alábbi kódot a fenti kód helyére a cookie használatához:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Adja hozzá az alábbi definíciót az `appendMessage` függvényhez, közvetlenül a cookie használatához hozzáadott kódsor alá:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. Frissítse a `bindConnectionMessage` és az `onConnected` függvényeket az alábbi kóddal az `appendMessage` használatához.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. Az *index.html* fájl végén frissítse a `connection.start()` hibakezelőjét a lent látható módon ahhoz, hogy a rendszer felkérje a felhasználót a bejelentkezésre.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Mentse a módosításokat.

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás létrehozása és futtatása helyben

1. Mentse az összes fájl módosításait.

2. Buildelje az alkalmazást a .NET Core CLI használatával, majd futtassa a következő parancsot a parancsrendszerhéjban:

    ```dotnetcli
    dotnet build
    ```

3. Ha a létrehozás sikeresen befejeződött, hajtsa végre a következő parancsot a webalkalmazás helyben való futtatásához:

    ```dotnetcli
    dotnet run
    ```

    Alapértelmezés szerint a rendszer helyben, az 5000-es porton üzemelteti az alkalmazást:

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Production
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
                    Application started. Press Ctrl+C to shut down.
    ```

4. Nyisson meg egy böngészőablakot, és navigáljon a `http://localhost:5000` helyre. A GitHubbal való bejelentkezéshez kattintson a fenti **itt** hivatkozásra.

    ![Azure-ban üzemeltetett teljes OAuth-hitelesítés](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    A rendszer fel fogja kérni, hogy engedélyezze a csevegőalkalmazás hozzáférését a GitHub-fiókjához. Kattintson az **Engedélyezés** gombra.

    ![Az OAuth-alkalmazás engedélyezése](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    A rendszer visszairányítja a csevegőalkalmazásba, és belépteti a GitHub-fiókja nevével. A fióknév megállapításához a webalkalmazás az újonnan hozzáadott hitelesítéssel hitelesítette Önt.

    ![Azonosított fiók](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    Most, hogy a csevegőalkalmazás hitelesítést végez a GitHubbal és cookie-ként tárolja a hitelesítési adatokat, üzembe helyezheti azt az Azure-ban, hogy más felhasználók is hitelesítést végezhessenek a fiókjaikkal, illetve kommunikálhassanak más munkaállomásokról.

## <a name="deploy-the-app-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban

A környezet előkészítése az Azure CLI-hez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Ebben a szakaszban az Azure parancssori felületének (CLI) használatával hozzon létre egy új webalkalmazást [Azure app Service](../app-service/index.yml) a ASP.NET-alkalmazás Azure-ban való üzemeltetéséhez. A webalkalmazás a Git helyi üzemelő példányának használatára lesz konfigurálva. A webalkalmazás emellett a SignalR kapcsolati sztringgel, GitHub OAuth titkos alkalmazáskulcsokkal és egy üzembe helyező felhasználóval is konfigurálva lesz.

Az alábbi erőforrások létrehozásakor ügyeljen arra, hogy ugyanazt az erőforráscsoportot használja, mint amelyben a SignalR Service-erőforrás is található. Ha ezt a megközelítést alkalmazza, sokkal könnyebb dolga lesz később, amikor el akarja távolítani az erőforrásokat. A példák feltételezik, hogy a korábbi oktatóanyagokban javasolt *SignalRTestResources* csoportnevet használta.

### <a name="create-the-web-app-and-plan"></a>A webalkalmazás és a csomag létrehozása

Másolja az alábbi parancsok szövegét, és frissítse a paramétereket. Illessze be a frissített szkriptet az Azure Cloud Shellbe, majd nyomja le az **Enter** billentyűt az új App Service-csomag és webalkalmazás létrehozásához.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Paraméter | Leírás |
| -------------------- | --------------- |
| ResourceGroupName | Ezt az erőforráscsoport-nevet a korábbi oktatóanyagokban javasoltuk. Az oktatóanyag-erőforrásokat érdemes egy csoportban tárolni. Használja ugyanazt az erőforráscsoportot, mint amelyet a korábbi oktatóanyagokban használt. |
| WebAppPlan | Adjon meg egy új egyedi nevet App Service-csomaghoz. |
| WebAppName | Ez lesz az új webalkalmazás neve, illetve az URL-cím egy része. Egyedi nevet használjon. Például: signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>Alkalmazásbeállítások hozzáadása a webalkalmazáshoz

Ebben a szakaszban alkalmazásbeállításokat fog hozzáadni a következő összetevőkhöz:

* A SignalR Service-erőforrás kapcsolati sztringje
* A GitHub OAuth-alkalmazás ügyfél-azonosítója
* A GitHub OAuth-alkalmazás titkos ügyfélkulcsa

Másolja az alábbi parancsok szövegét, és frissítse a paramétereket. Illessze be a frissített szkriptet az Azure Cloud Shellbe, majd nyomja le az **Enter** billentyűt az alkalmazásbeállítások hozzáadásához:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Paraméter | Leírás |
| -------------------- | --------------- |
| GitHubClientId | Rendelje hozzá ezt a változót a GitHub OAuth-alkalmazás titkos ügyfél-azonosítójához. |
| GitHubClientSecret | Rendelje hozzá ezt a változót a GitHub OAuth-alkalmazás titkos jelszavához. |
| ResourceGroupName | Frissítse ezt a változót ugyanarra az erőforráscsoport-névre, mint amelyet az előző szakaszban használt. |
| SignalRServiceResource | Frissítse ezt a változót a rövid útmutatóban létrehozott SignalR Service-erőforrás nevére. Például: signalrtestsvc48778624. |
| WebAppName | Frissítse ezt a változót az előző szakaszban létrehozott új webalkalmazás nevére. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>A webalkalmazás konfigurálása a Git helyi üzemelő példányához

Illessze be az alábbi szkriptet az Azure Cloud Shellbe. Ez a szkript létrehoz egy új üzembe helyező felhasználónevet és jelszót, amelyet a kód webalkalmazásba való, Gittel történő üzembe helyezésekor fog használni. A szkript továbbá konfigurálja a webalkalmazást a helyi Git-adattárral történő üzembe helyezéshez, és visszaadja a Git telepítés URL-címét.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Paraméter | Leírás |
| -------------------- | --------------- |
| DeploymentUserName | Válasszon egy nevet az új üzembe helyező felhasználó számára. |
| DeploymentUserPassword | Válasszon egy jelszót az új üzembe helyező felhasználó számára. |
| ResourceGroupName | Használja ugyanazt az erőforráscsoport-nevet, mint amelyet az előző szakaszban használt. |
| WebAppName | Ez lesz a korábban létrehozott új webalkalmazás neve. |

Jegyezze fel a Git üzemelő példány parancs által visszaadott URL-címét. Ezt az URL-címet később fogja használni.

### <a name="deploy-your-code-to-the-azure-web-app"></a>A kód üzembe helyezése az Azure-webalkalmazásban

A kód üzembe helyezéséhez hajtsa végre az alábbi parancsokat egy Git-rendszerhéjban.

1. Lépjen a projekt gyökérkönyvtárába. Ha nem Git-adattárral inicializálta a projektet, hajtsa végre a következő parancsot:

    ```bash
    git init
    ```

2. Adjon hozzá egy távoli végpontot a Git üzemelő példány korábban feljegyzett URL-címéhez:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Készítse elő az inicializált adattárban található összes fájlt, és adjon hozzá egy véglegesítést.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Helyezze üzembe a kódot az Azure-webalkalmazásban.

    ```bash
    git push Azure main
    ```

    A rendszer fel fogja kérni, hogy végezzen hitelesítést a kód Azure-ban való üzembe helyezéséhez. Adja meg a fent létrehozott üzembe helyező felhasználó felhasználónevét és jelszavát.

### <a name="update-the-github-oauth-app"></a>A GitHub OAuth-alkalmazás frissítése

A legutolsó dolog, amit el kell végeznie, az a GitHub OAuth-alkalmazás **Homepage URL** (Kezdőlap URL-címe) és **Authorization callback URL** (Engedélyezés-visszahívási URL-cím) tulajdonságának frissítését, hogy azok az új üzemeltetett alkalmazásra mutassanak.

1. Nyisson meg [https://github.com](https://github.com) egy böngészőben, és navigáljon a fiókja **Beállítások**  >  **fejlesztői beállítások**  >  **OAuth-alkalmazásokhoz**.

2. Kattintson a hitelesítő alkalmazásra, és a frissítse a **Homepage URL** (Kezdőlap URL-címe) és **Authorization callback URL** (Engedélyezés-visszahívási URL-cím) tulajdonságát a lent látható módon:

    | Beállítás | Példa |
    | ------- | ------- |
    | Kezdőlap URL-címe | `https://signalrtestwebapp22665120.azurewebsites.net/home` |
    | Az engedélyezési visszahívás URL-címe | `https://signalrtestwebapp22665120.azurewebsites.net/signin-github` |

3. Nyissa meg a webalkalmazás URL-címét, és tesztelje az alkalmazást.

    ![Azure-ban üzemeltetett teljes OAuth-hitelesítés](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és a következő oktatóanyagban újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.

Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *SignalRTestResources* nevű erőforráscsoportot használtak. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** elemre.

![Törlés](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be az erőforráscsoport nevét, és kattintson a **Törlés** gombra.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban OAuth-hitelesítést adott hozzá az alkalmazáshoz, hogy jobb hitelesítési megoldást biztosítson az Azure SignalR Service szolgáltatással. Ha többet szeretne megtudni az Azure SignalR Server használatáról, lépjen tovább a SignalR Service Azure CLI-mintáira.

> [!div class="nextstepaction"]
> [Azure SignalR CLI-minták](./signalr-reference-cli.md)