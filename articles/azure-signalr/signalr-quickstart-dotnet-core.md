---
title: Rövid útmutató a Azure SignalR Service
description: Rövid útmutató, amelyből megtudhatja, hogyan hozhat létre az Azure SignalR szolgáltatással csevegőszobát ASP.NET Core MVC alkalmazásokkal.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 9099a8620f6e4d87ec38c10226d94b1b3cd3462f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865980"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Rövid útmutató: Csevegőszoba létrehozása a SignalR Service

Az Azure SignalR szolgáltatás egy olyan Azure-szolgáltatás, amely segítségével a fejlesztők könnyen hozhatnak létre webalkalmazásokat valós idejű funkciókkal. Ez a szolgáltatás eredetileg a SignalR-re épül [ASP.NET Core 2.1-hez,](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1)de már támogatja a későbbi verziókat.

Ez a cikk segítséget nyújt az első lépések megtételében az Azure SignalR szolgáltatás használatakor. Ebben a rövid útmutatóban egy csevegési alkalmazást fog létrehozni egy ASP.NET Core MVC-webalkalmazással. Az alkalmazás kapcsolatot létesít az Azure SignalR szolgáltatási erőforrással a valós idejű tartalomfrissítések engedélyezéséhez. A webalkalmazást helyileg fogja használni, és több böngészős ügyféllel fog csatlakozni. Minden ügyfél képes lesz tartalomfrissítéseket küldeni a többi ügyfélnek. 

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. Az egyik lehetőség [Visual Studio Code,](https://code.visualstudio.com/)amely Windows, macOS és Linux platformokon érhető el.

Az oktatóanyag kódja letölthető az [AzureSignalR-minták GitHub-adattárjából](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Emellett az ebben a rövid útmutatóban használt Azure-erőforrásokat a Create a SignalR Service script (Új szkript [létrehozása) SignalR Service használhatja.](scripts/signalr-cli-create-service.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [.NET Core SDK.](https://dotnet.microsoft.com/download)
* Töltse le vagy klónozza az [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub-adattárat. 

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR-erőforrás létrehozása

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Ebben a szakaszban a .NET Core parancssori felület [(CLI)](/dotnet/core/tools/) használatával fog létrehozni egy ASP.NET Core MVC webalkalmazás-projektet. A virtuális gép használatának a .NET Core parancssori felülete előnye Visual Studio, hogy Windows, macOS és Linux platformokon is elérhető. 

1. Hozzon létre egy mappát a projekthez. Ez a rövid útmutató az *E:\Testing\chattest mappát* használja.

2. Az új mappában futtassa a következő parancsot a projekt létrehozásához:

    ```dotnetcli
    dotnet new mvc
    ```

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsnetcore).

## <a name="add-secret-manager-to-the-project"></a>A Secret Manager hozzáadása a projekthez

Ebben a szakaszban a [Secret Manager](/aspnet/core/security/app-secrets) eszközt fogja hozzáadni a projekthez. A Secret Manager eszköz a projektfán kívül tárolja a fejlesztési munkához szükséges bizalmas adatokat. Ezzel a megközelítéssel megelőzhető, hogy az alkalmazás titkos kódjait véletlenül megosszuk a forráskódban.

1. Nyissa meg a *.csproj* fájlt. Adjon hozzá egy `DotNetCliToolReference` elemet a *Microsoft.Extensions.SecretManager.Tools* belefoglalásához. Adjon hozzá egy `UserSecretsId` elemet a *chattest.csproj* fájl alábbi kódjában látható módon, és mentse a fájlt.

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

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudajuk meg velünk a következőt:](https://aka.ms/asrs/qsnetcore).

## <a name="add-azure-signalr-to-the-web-app"></a>Az Azure SignalR hozzáadása a webalkalmazáshoz

1. Adjon hozzá egy hivatkozást a `Microsoft.Azure.SignalR` NuGet-csomaghoz a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Futtassa a következő parancsot a projekt csomagjainak visszaállításához:

    ```dotnetcli
    dotnet restore
    ```

3. Adjon hozzá egy *Azure:SignalR:ConnectionString* nevű titkos kódot a Secret Managerhez. 

    Ez a titkos kód tartalmazza a SignalR szolgáltatási erőforrás elérésére szolgáló kapcsolati sztringet. *Az Azure:SignalR:ConnectionString* az alapértelmezett konfigurációs kulcs, amely a SignalR által a kapcsolat létesítésében keres. A következő parancsban cserélje le az értéket a saját erőforrásának SignalR Service sztringjével.

    Ezt a parancsot ugyanabban a könyvtárban kell futtatnia, mint a *.csproj fájlt.*

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    A Secret Manager csak a webalkalmazás tesztelésére lesz használva helyileg üzemeltetett környezetben. Egy későbbi oktatóanyagban üzembe fogja helyezni a csevegő webalkalmazást az Azure-ban. Miután üzembe helyezett egy webalkalmazást az Azure-ban, a kapcsolati sztring Secret Managerrel való tárolása helyett egy alkalmazásbeállítást fog használni.

    Ez a titkos kulcs a Konfigurációs API-val érhető el. Kettőspont (:) A konfigurációs API-val együtt a konfiguráció nevében működik az összes támogatott platformon. Lásd: [Konfiguráció környezet szerint.](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider)


4. Nyissa *meg a Startup.cs-t,* és frissítse a metódust Azure SignalR Service `ConfigureServices` és `AddSignalR()` metódusok `AddAzureSignalR()` hívásával:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    Azáltal, hogy nem ad át paramétert a számára, ez a kód az alapértelmezett konfigurációs kulcsot SignalR Service `AddAzureSignalR()` erőforrás kapcsolati sztringhez. Az alapértelmezett konfigurációs kulcs az *Azure:SignalR:ConnectionString.*

5. A *Startup.cs fájlban* frissítse a metódust úgy, hogy `Configure` lecseréli az alábbi kódra.

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

A SignalRben a hub egy alapvető összetevő, amely az ügyfélről hívható metódusok készletét teszi elérhetővé. Ebben a szakaszban meghatároz egy központosztályt két metódussal:

* `Broadcast`: Ez a metódus üzenetet küld az összes ügyfélnek.
* `Echo`: Ez a metódus visszaküld egy üzenetet a hívónak.

Mindkét módszer a Core `Clients` SignalR SDK által ASP.NET felületet használja. Ez az interfész hozzáférést biztosít az összes csatlakoztatott ügyfélhez, így lekkultálhatja a tartalmat az ügyfelekre.

1. A projektkönyvtárban adjon hozzá egy új, *Hub* nevű mappát. Adjon hozzá egy *chatHub.cs* nevű új központi kódfájlt az új mappához.

2. Adja hozzá a következő kódot a *ChatHub.cs fájlhoz* a hub osztály meghatározásához és a fájl mentéshez.

    Frissítse az osztály névterét, ha a *SignalR.Mvc* fájltól eltérő projektnevet használt.

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

### <a name="add-the-client-interface-for-the-web-app"></a>A webalkalmazás ügyféloldali felületének hozzáadása

A csevegőszoba-alkalmazás ügyféloldali felhasználói felülete HTML-t és JavaScriptet fog tartalmazni egy *index.htmnevű* fájlban a *wwwroot könyvtárban.*

Másolja a *css/site.css* fájlt a *mintatár wwwroot* [mappájába.](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) Cserélje le a projekt *css/site.css fájlját* arra, amit kimásott.

Itt található az lindex.htm *fő kódja:*

Hozzon létre egy új fájlt a *wwwroot* könyvtárban *index.html*, másolja, majd illessze be a következő HTML-kódot az újonnan létrehozott fájlba:

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

A kód a *index.htmhívja* meg, `HubConnectionBuilder.build()` hogy HTTP-kapcsolatot létesítsen az Azure SignalR-erőforrással.

Ha a kapcsolat sikeresen létrejött, át lesz adva a `bindConnectionMessage` számára, amely eseménykezelőket ad a bejövő tartalomhoz, amely le lesz küldve az ügyfélnek. 

A `HubConnection.start()` kommunikálni kezd a központtal. Ezután `onConnected()` hozzáadja a gomb eseménykezelőket. Ezek az eseménykezelők a kapcsolat segítségével engedélyezik, hogy ez az ügyfél tartalomfrissítéseket küldjön le az összes csatlakozott ügyfélnek.

## <a name="add-a-development-runtime-profile"></a>Fejlesztési futtatási profil hozzáadása

Ebben a szakaszban egy fejlesztési futásidejű környezetet fog hozzáadni a ASP.NET Core-hoz. További információ: [Több környezet használatának a ASP.NET Core-ban.](/aspnet/core/fundamentals/environments)

1. Hozzon létre egy *Properties (Tulajdonságok)* nevű mappát a projektben.

2. Adjon hozzá egy *új,launchSettings.jsnevű* fájlt a mappához a következő tartalommal, majd mentse a fájlt.

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

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsnetcore).

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi összeállítása és futtatása

1. Az alkalmazás parancssori paranccsal való a .NET Core parancssori felülete futtassa a következő parancsot a parancshéjban:

    ```dotnetcli
    dotnet build
    ```

1. A build sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```dotnetcli
    dotnet run
    ```

    Az alkalmazás helyileg, az 5000-es porton lesz üzemeltetve a fejlesztési futásidejű profilban konfigurált módon:

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

1. Nyisson meg két böngészőablakot. Minden böngészőben a következőt kell használnia: `http://localhost:5000` . A rendszer kéri, hogy adja meg a nevét. Adjon meg egy ügyfélnevet mindkét ügyfélnek, és a Küldés gombbal tesztelje az üzenet tartalmának leküldését mindkét ügyfél **között.**

    ![Példa Azure SignalR csoportos csevegésre](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsnetcore).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a következő oktatóanyagra folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha végzett a gyorsindítási mintaalkalmazással, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszafordítható, és az adott csoportban található összes erőforrást tartalmazza. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha a minta üzemeltetési erőforrásait egy meglévő erőforráscsoportban hozta létre, amely a megtartni kívánt erőforrásokat tartalmazza, az egyes erőforrásokat külön-külön törölheti a panelről az erőforráscsoport törlése helyett.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

A Szűrés **név alapján szövegmezőbe** írja be az erőforráscsoport nevét. Ebben a rövid útmutatóban a *SignalRTestResources* nevű erőforráscsoportot használtuk. Az eredménylistában az erőforráscsoportban válassza a három pont **(...**) lehetőséget, > **erőforráscsoport törlése lehetőséget.**

![Erőforráscsoport törlésére vonatkozó kijelölések](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését kéri. Adja meg az erőforráscsoport nevét a megerősítéshez, majd válassza a **Törlés lehetőséget.**

A rendszer néhány pillanaton belül törli az erőforráscsoportot és annak erőforrásait.

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudajuk meg velünk a következőt:](https://aka.ms/asrs/qsnetcore).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új Azure SignalR Service erőforrást. Ezután egy ASP.NET Core-webalkalmazással valós időben lekért tartalomfrissítéseket több csatlakoztatott ügyfélnek. Ha többet szeretne megtudni a Azure SignalR Service, folytassa a hitelesítést bemutató oktatóanyagban.

> [!div class="nextstepaction"]
> [Azure SignalR szolgáltatás – hitelesítés](./signalr-concept-authenticate-oauth.md)
