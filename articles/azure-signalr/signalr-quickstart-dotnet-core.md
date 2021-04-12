---
title: Útmutató az Azure Signaler szolgáltatás használatának megismeréséhez
description: Rövid útmutató, amelyből megtudhatja, hogyan hozhat létre az Azure SignalR szolgáltatással csevegőszobát ASP.NET Core MVC alkalmazásokkal.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 3cfcf57f455a5c3b17b794acf2ded66ed2285eff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94873505"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Rövid útmutató: csevegési hely létrehozása a Signaler szolgáltatás használatával

Az Azure SignalR szolgáltatás egy olyan Azure-szolgáltatás, amely segítségével a fejlesztők könnyen hozhatnak létre webalkalmazásokat valós idejű funkciókkal. A szolgáltatás eredetileg [ASP.NET Core 2,1-es jelzőn](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1)alapult, de mostantól támogatja a későbbi verziókat.

Ez a cikk segítséget nyújt az első lépések megtételében az Azure SignalR szolgáltatás használatakor. Ebben a rövid útmutatóban egy ASP.NET Core MVC-webalkalmazás használatával hoz létre csevegési alkalmazást. Az alkalmazás kapcsolatot létesít az Azure SignalR szolgáltatási erőforrással a valós idejű tartalomfrissítések engedélyezéséhez. A webalkalmazást helyileg fogja üzemeltetni, és több böngésző-ügyféllel is csatlakozhat. Minden ügyfél képes lesz tartalomfrissítéseket küldeni a többi ügyfélnek. 

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. Az egyik lehetőség a [Visual Studio Code](https://code.visualstudio.com/), amely a Windows, MacOS és Linux platformokon érhető el.

Az oktatóanyag kódja letölthető az [AzureSignalR-minták GitHub-adattárjából](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Emellett az ebben a rövid útmutatóban használt Azure-erőforrásokat is létrehozhatja a [Signal Service-parancsfájl létrehozása](scripts/signalr-cli-create-service.md)című lépéssel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [.net Core SDK](https://www.microsoft.com/net/download/windows).
* Töltse le vagy klónozott [AzureSignalR – minta GitHub-](https://github.com/aspnet/AzureSignalR-samples) tárházat. 

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR-erőforrás létrehozása

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Ebben a szakaszban a [.net Core parancssori felület (CLI)](/dotnet/core/tools/) használatával hoz létre egy ASP.net Core MVC webalkalmazás-projektet. A a .NET Core parancssori felülete a Visual Studióban való használatának előnye, hogy a Windows, macOS és Linux platformokon is elérhető. 

1. Hozzon létre egy mappát a projekt számára. Ez a rövid útmutató a *E:\Testing\chattest* mappát használja.

2. Az új mappában futtassa a következő parancsot a projekt létrehozásához:

    ```dotnetcli
    dotnet new mvc
    ```

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsnetcore).

## <a name="add-secret-manager-to-the-project"></a>A Secret Manager hozzáadása a projekthez

Ebben a szakaszban a [Secret Manager eszközt](/aspnet/core/security/app-secrets) adja hozzá a projekthez. A Secret Manager eszköz bizalmas adatokat tárol a projekt fáján kívüli fejlesztési munkához. Ez a módszer segít megelőzni az alkalmazás titkos kódjának véletlen megosztását a forráskódban.

1. Nyissa meg a *.csproj* fájlt. Adjon hozzá egy `DotNetCliToolReference` elemet a *Microsoft.Extensions.SecretManager.Tools* belefoglalásához. Vegyen fel egy `UserSecretsId` elemet is, ahogy az a következő kódban látható a *csevegő. csproj*, és mentse a fájlt.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsnetcore).

## <a name="add-azure-signalr-to-the-web-app"></a>Az Azure SignalR hozzáadása a webalkalmazáshoz

1. Adja hozzá a NuGet- `Microsoft.Azure.SignalR` csomagra mutató hivatkozást a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Futtassa a következő parancsot a projekt csomagjainak visszaállításához:

    ```dotnetcli
    dotnet restore
    ```

3. Adjon hozzá egy *Azure:SignalR:ConnectionString* nevű titkos kódot a Secret Managerhez. 

    Ez a titkos kód tartalmazza a SignalR szolgáltatási erőforrás elérésére szolgáló kapcsolati sztringet. *Azure: szignáló: a ConnectionString* az az alapértelmezett konfigurációs kulcs, amelyet a jelző a kapcsolat létesítéséhez keres. Cserélje le a következő parancs értékét a Signaling Service-erőforráshoz tartozó kapcsolatok karakterláncára.

    Ezt a parancsot a *. csproj* fájllal megegyező könyvtárba kell futtatnia.

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    A Secret Manager csak a webalkalmazás helyi tesztelésére szolgál. Egy későbbi oktatóanyagban üzembe helyezheti a csevegési webalkalmazást az Azure-ban. Miután telepítette a webalkalmazást az Azure-ba, a kapcsolati sztring a Secret Managerrel való tárolása helyett egy alkalmazás-beállítást fog használni.

    Ez a titok a konfigurációs API-val érhető el. Egy kettőspont (:) a konfiguráció neve a konfigurációs API-val minden támogatott platformon használható. Lásd: [konfiguráció környezet alapján](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider).


4. Nyissa meg a *Startup. cs* programot, és frissítse a `ConfigureServices` metódust az Azure signaler szolgáltatás használatára a `AddSignalR()` és metódusok meghívásával `AddAzureSignalR()` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    Ha nem küldi el a paramétert a rendszernek `AddAzureSignalR()` , ez a kód a jelző szolgáltatás erőforrás-kapcsolódási karakterláncának alapértelmezett konfigurációs kulcsát használja. Az alapértelmezett konfigurációs kulcs az *Azure: signaler: ConnectionString*.

5. A *Startup. cs*-ban frissítse a `Configure` metódust a következő kóddal való lecserélésével.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>Központosztály hozzáadása

A jelzőben a hub egy olyan alapvető összetevő, amely az ügyféltől hívható metódusok készletét teszi lehetővé. Ebben a szakaszban meghatároz egy központosztályt két metódussal:

* `Broadcast`: Ez a metódus üzenetet küld az összes ügyfélnek.
* `Echo`: Ez a metódus visszaküld egy üzenetet a hívónak.

Mindkét módszer a `Clients` ASP.net Core a signaler SDK által biztosított felületet használja. Ez az interfész hozzáférést biztosít az összes csatlakoztatott ügyfélhez, így tartalmat küldhet az ügyfeleknek.

1. A projektkönyvtárban adjon hozzá egy új, *Hub* nevű mappát. Vegyen fel egy új, *ChatHub. cs* nevű hub-kódrészletet az új mappába.

2. Adja hozzá a következő kódot a *ChatHub. cs* fájlhoz, hogy meghatározza a hub osztályt, és mentse a fájlt.

    Frissítse az osztály névterét, ha olyan projekt nevét használta, amely eltér a *signaler. MVC*-től.

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Az ügyfél felületének hozzáadása a webalkalmazáshoz

A csevegő alkalmazás ügyfél-felhasználói felülete HTML és JavaScript formátumú lesz egy *index.html* nevű fájlban a *wwwroot* könyvtárban.

Másolja a *CSS/site. css* fájlt a [Samples adattár](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) *wwwroot* mappájából. Cserélje le a projekt *CSS-vagy site. css-* fájlját a vágólapra.

*index.html* fő kódja:

Hozzon létre egy új fájlt a *wwwroot* könyvtárban *index.html*, másolja, majd ILLESSZE be az alábbi HTML-fájlt az újonnan létrehozott fájlba:

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

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
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

A *index.html* -ben található kód az `HubConnectionBuilder.build()` Azure signaler-erőforráshoz való http-kapcsolódást kezdeményez.

Ha a kapcsolat sikeresen létrejött, át lesz adva a `bindConnectionMessage` számára, amely eseménykezelőket ad a bejövő tartalomhoz, amely le lesz küldve az ügyfélnek. 

A `HubConnection.start()` kommunikálni kezd a központtal. Ezután `onConnected()` hozzáadja a Button eseménykezelőket. Ezek az eseménykezelők a kapcsolat segítségével engedélyezik, hogy ez az ügyfél tartalomfrissítéseket küldjön le az összes csatlakozott ügyfélnek.

## <a name="add-a-development-runtime-profile"></a>Fejlesztési futtatási profil hozzáadása

Ebben a szakaszban a ASP.NET Core fejlesztési futtatókörnyezeti környezetét fogja hozzáadni. További információ: [több környezet használata ASP.net Coreban](/aspnet/core/fundamentals/environments).

1. Hozzon létre egy *Tulajdonságok* nevű mappát a projektben.

2. Adjon hozzá egy *launchSettings.js* nevű új fájlt a mappához a következő tartalommal, és mentse a fájlt.

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsnetcore).

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

    ```dotnetcli
    dotnet build
    ```

1. A létrehozás sikeres befejeződése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```dotnetcli
    dotnet run
    ```

    Az alkalmazás az 5000-as porton, a fejlesztői futtatókörnyezeti profilban konfigurált módon lesz helyileg üzemeltetve:

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. Nyisson meg két böngészőablakot. A böngészőben nyissa meg a következőt: `http://localhost:5000` . A rendszer kéri, hogy adja meg a nevét. Adja meg az ügyfél nevét mindkét ügyfél számára, és tesztelje az üzenetek tartalmát mindkét ügyfél között a **Küldés** gomb használatával.

    ![Példa Azure-beli szignáló csoport csevegésére](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsnetcore).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbbra is a következő oktatóanyagot használja, megtarthatja az ebben a rövid útmutatóban létrehozott erőforrásokat, és újból felhasználhatja őket.

Ha elkészült a gyors üzembe helyezési minta alkalmazással, törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat a díjak elkerülése érdekében. 

> [!IMPORTANT]
> Egy erőforráscsoport törlése nem vonható vissza, és tartalmazza az adott csoport összes erőforrását. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha a minta tárolására szolgáló erőforrásokat olyan meglévő erőforráscsoporthoz hozta létre, amely a megőrizni kívánt erőforrásokat tartalmazza, akkor az erőforráscsoport törlése helyett az egyes erőforrásokat egyenként törölheti a paneljéről.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

A **szűrés név alapján** szövegmezőbe írja be az erőforráscsoport nevét. Ebben a rövid útmutatóban a *SignalRTestResources* nevű erőforráscsoportot használtuk. Az erőforráscsoport az eredmény listán válassza a három pontot (**..**.) > az **erőforráscsoport törlése** elemet.

![Erőforráscsoport törlésének kiválasztása](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését kéri. Adja meg a megerősíteni kívánt erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és annak erőforrásait.

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsnetcore).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új Azure Signal Service-erőforrást. Ezután egy ASP.NET Core webalkalmazással használta a tartalom frissítéseinek valós idejű leküldését több csatlakoztatott ügyfélre. Ha többet szeretne megtudni az Azure Signaler szolgáltatás használatáról, folytassa az oktatóanyagot, amely bemutatja a hitelesítést.

> [!div class="nextstepaction"]
> [Azure SignalR szolgáltatás – hitelesítés](./signalr-concept-authenticate-oauth.md)
