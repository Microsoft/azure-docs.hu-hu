---
title: 'Oktatóanyag: a Blazer Server csevegő alkalmazásának létrehozása – Azure-jelző'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és módosíthat egy blézer-kiszolgálói alkalmazást az Azure Signaler szolgáltatással
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: a1423e8aaf4b50db94cda0b935a7b1658249893e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726345"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Oktatóanyag: a blézer-kiszolgáló csevegési alkalmazásának létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre és módosíthat egy blézer-kiszolgálói alkalmazást. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Hozzon létre egy egyszerű csevegő szobát a Blaze Server alkalmazással.
> * A borotva összetevőinek módosítása.
> * Az események kezeléséhez és az adatkötéshez használjon összetevőket.
> * Gyors üzembe helyezés Azure App Service a Visual Studióban.
> * Helyi szignáló migrálása az Azure Signaler szolgáltatásba.

## <a name="prerequisites"></a>Előfeltételek
* A [.net Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) telepítése (verzió: >= 3.0.100)
* A [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (verzió: >= 16,3) telepítése
> A Visual Studio 2019 előzetes verziójának verziója is működik, amely az újabb .net Core-verzióra irányuló, legújabb Blazer Server-alkalmazás sablonjának kiadása.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Helyi csevegési helyiség készítése a Blaze Server alkalmazásban

A Visual Studio 2019 verzió 16.2.0 az Azure Signaler szolgáltatás beépített webalkalmazás-közzétételi folyamata, és a webalkalmazás és a Signaler szolgáltatás közötti függőségek kezelése sokkal kényelmesebb lenne. A helyi jelzővel dolgozhat a fejlesztői helyi környezetben, és az Azure Signaler szolgáltatáson dolgozik a Azure App Servicehoz, a kód módosítása nélkül is.

1. Csevegési Blazer-alkalmazás létrehozása
   
   A Visual Studióban válassza az új projekt létrehozása – > Blazer App-> (az alkalmazás neve és a mappa kiválasztása) – > Blazer Server alkalmazást. Győződjön meg arról, hogy már telepítette a .NET Core SDK 3.0-s verzióját, hogy a Visual Studio helyesen felismerje a célként megadott keretrendszert.

   [Az ![ új projekt létrehozása területen a Blazer-alkalmazások sablonjai vannak kiválasztva. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Vagy futtassa a cmd parancsot
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. A `BlazorChatSampleHub.cs` csevegéshez implementálni kívánt fájl hozzáadása `Hub` .
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Adjon hozzá egy végpontot a hubhoz a alkalmazásban `Startup.Configure()` .
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Telepítse `Microsoft.AspNetCore.SignalR.Client` a csomagot a signaler-ügyfél használatára.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. `ChatRoom.razor` `Pages` A signaler-ügyfél megvalósításához hozzon létre egy mappát a mappában. Kövesse az alábbi lépéseket, vagy egyszerűen másolja a [chat. Razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor).

   1. Oldal hivatkozásának és hivatkozásának hozzáadása
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Adja hozzá a kódot az új Signaler-ügyfélhez az üzenetek küldéséhez és fogadásához.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Adja hozzá a renderelési részt, mielőtt `@code` a felhasználói felület kommunikálni tudjon a signaler-ügyféllel.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Update `NavMenu.razor` (frissítés) – a csevegési helyiséghez hasonló REST-alapú belépési menü beszúrása `NavMenuCssClass` .

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Frissítés `site.css` a csevegési területek buborék nézeteinek optimalizálásához. Fűzze hozzá az alábbi kódot a végén.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Az alkalmazás futtatásához kattintson az <kbd>F5</kbd> gombra. Az alábbihoz hasonló módon cseveghet.

   [A ![ Bob és Alice közötti animált csevegés látható. Alice azt mondja, Hello, Bob, Szia. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Közzététel az Azure-ban

   Eddig a Blazer-alkalmazás a helyi jelzőn dolgozik, és a Azure App Service való üzembe helyezéskor azt javasoljuk, hogy az [Azure signaler szolgáltatást](/aspnet/core/signalr/scale#azure-signalr-service) használja, amely lehetővé teszi, hogy a Blaze-kiszolgáló alkalmazások nagy számú egyidejű szignáló kapcsolatra legyenek felskálázása. Emellett a Signaler szolgáltatás globális elérhetősége és a nagy teljesítményű adatközpontok jelentős mértékben segítik a földrajzi elhelyezkedés miatti késés csökkentését.

> [!IMPORTANT]
> A Blazer Server alkalmazásban a felhasználói felületi állapotok a kiszolgáló oldalon maradnak, ami azt jelenti, hogy ebben az esetben szükség van a kiszolgáló Sticky-ra. Ha van egyetlen app Server-kiszolgáló, a rendszer a kiszolgáló Sticky-ot is biztosítja a tervezéshez. Ha azonban több App-kiszolgáló is van, előfordulhat, hogy az ügyfél-egyeztetés és a kapcsolódás különböző kiszolgálókra mutat, és felhasználói felületi hibákhoz vezet a Blazer alkalmazásban. Ezért a következőhöz hasonlóan kell engedélyeznie a kiszolgálót `appsettings.json` :
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Kattintson a jobb gombbal a projektre, és navigáljon a elemre `Publish` .

   * Cél: Azure
   * Konkrét cél: a **Azure app Service** összes típusa támogatott.
   * App Service: hozzon létre egy újat, vagy válasszon ki egy meglévő app Service-t.

   [![Az animáció az Azure kiválasztását célozza célként, majd az Azure app Service adott célként. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Azure Signaler szolgáltatás-függőség hozzáadása

   A közzétett profil létrehozása után a **szolgáltatás függőségei** alatt egy javasolt üzenet jelenik meg. Kattintson a **Konfigurálás** gombra új létrehozásához, vagy válassza a meglévő Azure signaler szolgáltatást a panelen.

   [A ![ Közzététel lehetőségre kattintva Kiemelt a konfigurálandó hivatkozás. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   A szolgáltatás függősége az alábbi műveleteket hajtja végre, hogy az alkalmazás automatikusan váltson az Azure Signaler szolgáltatásra az Azure-ban.

   * Frissítsen [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration) Az Azure signaler szolgáltatás használatára.
   * Adja hozzá az Azure Signaler szolgáltatás NuGet csomagjának hivatkozását.
   * Módosítsa a profil tulajdonságait a függőségi beállítások mentéséhez.
   * A titkok tárolásának beállítása a választott lehetőségektől függ.
   * Adja hozzá `appsettings` a konfigurációt, hogy az alkalmazás célja legyen a kiválasztott Azure signaler szolgáltatás.

   [A ![ változások Összefoglalásakor a jelölőnégyzetek az összes függőség kiválasztására szolgálnak. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Az alkalmazás közzététele

   Most már készen áll a közzétételre. És a közzététel befejeződése után automatikusan a böngészőbe kerül az oldal. 
   > [!NOTE]
   > Előfordulhat, hogy a Azure App Service központi telepítésének késleltetése miatt nem működik azonnal, és a késleltetés érdekében próbálja meg frissíteni a lapot.
   > Emellett a böngésző hibakereső üzemmódját is használhatja az <kbd>F12</kbd> használatával annak ellenőrzéséhez, hogy a forgalom már át lett irányítva az Azure Signaler szolgáltatásba.

   [A ![ Blazer signaler csevegő mintában egy szövegmező szerepel a névben, valamint egy csevegés! gomb a csevegés indításához. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>További témakör: az Azure Signaler szolgáltatás engedélyezése a helyi fejlesztésben

1. Hivatkozás hozzáadása az Azure Signaler SDK-hoz

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Vegyen fel egy hívást az Azure Signaler szolgáltatásba a alkalmazásban `Startup.ConfigureServices()` .

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Az Azure Signaler szolgáltatás konfigurálása `ConnectionString` a vagy a-ben a `appsetting.json` [Secret Manager](/aspnet/core/security/app-secrets?tabs=visual-studio#secret-manager) eszközzel

> [!NOTE]
> A 2. lépés a [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration) signaler SDK-val való használatával helyettesíthető.
> 
> 1. Konfiguráció hozzáadása az Azure Signaler szolgáltatás bekapcsolásához `appsetting.json`
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Üzemeltetési indítási szerelvény társítása az Azure Signaler SDK használatához. Szerkessze `launchSettings.json` és adjon hozzá egy, az alábbihoz hasonló konfigurációt a belül `environmentVariables` .
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag elvégzése során létrehozott erőforrásokat, törölje az erőforráscsoportot az Azure Portalon.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Hozzon létre egy egyszerű csevegő szobát a Blaze Server alkalmazással.
> * A borotva összetevőinek módosítása.
> * Az események kezeléséhez és az adatkötéshez használjon összetevőket.
> * Gyors üzembe helyezés Azure App Service a Visual Studióban.
> * Helyi szignáló migrálása az Azure Signaler szolgáltatásba.

További információ a magas rendelkezésre állásról.
> [!div class="nextstepaction"]
> [Rugalmasság és vészhelyreállítás](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>További források

* [ASP.NET Core Blazer](/aspnet/core/blazor)
