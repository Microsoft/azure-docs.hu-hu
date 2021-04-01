---
title: fájl belefoglalása
description: foglalja bele a háttérbeli ASP .NET WebAPI-projekt létrehozására szolgáló kódot tartalmazó fájlt.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 6f490b6f25112ed8a10bbd865070bd07ea3ee84f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016946"
---
## <a name="create-the-webapi-project"></a>A WebAPI-projekt létrehozása

Az alábbi szakaszok az új ASP.NET WebAPI hátterrendszer létrehozását ismertetik. E folyamat három fő célja:

- **Ügyfelek hitelesítése**: Az ügyfélkérések hitelesítéséhez és a kérés adott felhasználóhoz való hozzárendeléséhez egy üzenetkezelőt fog hozzáadni a rendszerhez.
- **Regisztrálás értesítésekre a WebAPI háttérrendszer használatával**: Annak érdekében, hogy az ügyféleszközön értesítéseket lehessen fogadni, egy vezérlőt fog hozzáadni az új regisztrációk kezeléséhez. A rendszer [címke](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md) formájában automatikusan hozzáadja a hitelesített felhasználó nevét a regisztrációhoz.
- **Értesítések küldése az ügyfeleknek**: Egy vezérlőt is hozzá fog adni annak érdekében, hogy a felhasználók biztonságos leküldést indíthassanak az eszközökre és a címkéhez társított ügyfelek számára.

Az új ASP.NET WebAPI háttérrendszer létrehozása a következő műveletekkel történik:

> [!IMPORTANT]
> Ha a Visual Studio 2015-ös vagy régebbi verzióját használja, az oktatóanyag elkezdése előtt ellenőrizze, hogy telepítette-e a Visual Studio NuGet-csomagkezelőjének legfrissebb verzióját.
>
>Az ellenőrzéshez indítsa el a Visual Studiót. Az **Eszközök** menüben válassza a **Bővítmények és frissítések** pontot. Keresse meg az Ön által használt Visual Studio-verzióhoz tartozó **NuGet-csomagkezelőt**, és ellenőrizze, hogy a legfrissebb verzió van-e telepítve a gépén. Ha nem a legfrissebb verzió van telepítve a gépén, távolítsa el, és telepítse újra a NuGet-csomagkezelőt.

![Képernyőkép a bővítmények és frissítések párbeszédpanelről, amely a NuGet-csomag kezelése a Visual Studios-csomaghoz lehetőség ki van emelve.][B4]

> [!NOTE]
> Győződjön meg arról, hogy telepítette a Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) -t a webhely telepítéséhez.

1. Indítsa el a Visual Studiót vagy a Visual Studio Expresst.

2. Kattintson a **Server Explorer** (Kiszolgálókezelő) elemre, és jelentkezzen be az Azure-fiókjába. Be kell jelentkeznie ahhoz, hogy a fiókban létrehozhassa a webhelyerőforrásokat.

3. A Visual Studióban kattintson a jobb gombbal a Visual Studio-megoldásra, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Project** (Új projekt) lehetőségre.
4. Bontsa ki a **Visual C#** csomópontot, válassza a **Web** lehetőséget, majd kattintson az **ASP.NET Web Application** (ASP.NET-webalkalmazás) elemre.

5. A **Name** (Név) mezőbe írja be az **AppBackend** nevet, majd kattintson az **OK** gombra.

    ![A New Project (Új projekt) ablak][B1]

6. A **New ASP.NET Project** (Új ASP.NET-projekt) ablakban jelölje be a **Web API** (Webes API) jelölőnégyzetet, majd kattintson az **OK** gombra.

    ![A New ASP.NET Project (Új ASP.NET-projekt) ablak][B2]

7. A **Configure Microsoft Azure Web App** (Microsoft Azure-webalkalmazás konfigurálása) ablakban válasszon ki egy előfizetést, majd az **App Service plan** (App Service-csomag) listában végezze el az alábbi műveletek valamelyikét:

    * Válassza ki a korábban létrehozott App Service-csomagot.
    * Válassza a **Create a new app service plan** (Új App Service-csomag létrehozása) lehetőséget is, és hozzon létre egy új csomagot.

   Az oktatóanyag elvégzéséhez nincs szükség adatbázisra. Az App Service-csomag kiválasztása után kattintson az **OK** gombra a projekt létrehozásához.

    ![A Configure Microsoft Azure Web App (Microsoft Azure-webalkalmazás konfigurálása) ablak][B5]

    Ha nem látja ezt a lapot az App Service-csomag konfigurálásához, folytassa az Oktatóanyaggal. Az alkalmazást később is beállíthatja az alkalmazás közzétételekor. 

## <a name="authenticate-clients-to-the-webapi-backend"></a>Ügyfelek hitelesítése a WebAPI háttérrendszeren

Ebben a szakaszban egy új, **AuthenticationTestHandler** nevű üzenetkezelő-osztályt fog létrehozni az új háttérrendszer számára. A rendszer ezt az osztályt a [DelegatingHandler](/previous-versions/visualstudio/hh193679(v=vs.118)) kezelőből származtatja, és üzenetkezelőként adja hozzá, így az képes a háttérrendszerhez beérkező összes kérés feldolgozására.

1. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal az **AppBackend** projektre, válassza az **Add** (Hozzáadás) parancsot, majd a **Class** (Osztály) elemet.
2. Nevezze el az új osztályt **AuthenticationTestHandler.cs** néven, majd kattintson az **Add** (Hozzáadás) gombra az osztály létrehozásához. Az egyszerűség kedvéért a rendszer ezt az osztályt fogja használni a felhasználók *alapszintű hitelesítéséhez*. Az alkalmazás bármilyen hitelesítési séma használatára képes.
3. Az AuthenticationTestHandler.cs osztályban adja hozzá a következő `using`-utasításokat:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. Az AuthenticationTestHandler.cs osztályban cserélje le az `AuthenticationTestHandler` osztálydefiníciót az alábbi kódra:

    A kezelő akkor engedélyezi a kérést, ha teljesül a következő három feltétel:

   * A kérés tartalmaz *engedélyezési* fejlécet.
   * A kérés *alapszintű* hitelesítést használ.
   * A felhasználónév és a jelszó sztring azonos.

   Ellenkező esetben a kérést a rendszer elutasítja. Ez nem egy valós hitelesítési és engedélyezési megközelítés, csak egy egyszerű példa ehhez az oktatóanyaghoz.

   Ha az `AuthenticationTestHandler` hitelesíti és engedélyezi a kérésüzenetet, az alapszintű hitelesítést használó felhasználót a rendszer hozzákapcsolja az aktuális kéréshez a [HttpContext](/dotnet/api/system.web.httpcontext.current) felületén. A HttpContext felhasználói adatait a későbbiekben egy másik vezérlő (RegisterController) fogja használni egy [címke](/previous-versions/azure/azure-services/dn530749(v=azure.100)) az értesítésregisztrációs kéréshez való hozzáadásához.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (VerifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool VerifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Biztonsági megjegyzés: Az `AuthenticationTestHandler` osztály nem biztosít valós hitelesítést. A rendszer azt csak az alapszintű hitelesítés utánzására használja, és nem tekinthető biztonságosnak. Az éles alkalmazásokban és szolgáltatásokban implementálnia kell egy biztonságos hitelesítési mechanizmust.
5. Az üzenetkezelő regisztrálásához adja hozzá a következő kódot a `Register` metódus végéhez az **App_Start/WebApiConfig.cs** osztályban:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Mentse a módosításokat.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>Regisztráció értesítésekre a WebAPI háttérrendszer használatával

Ebben a szakaszban egy új vezérlőt fog hozzáadni a WebAPI háttérrendszerhez a felhasználók és az eszközök értesítés-regisztrációs kéréseinek kezeléséhez az értesítési központ ügyfélkönyvtárával. A vezérlő hozzáad egy felhasználói címkét a hitelesített és az `AuthenticationTestHandler` által a HttpContext elemhez kapcsolt felhasználóhoz. A címke `"username:<actual username>"` sztringformátumú lesz.

1. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal az **AppBackend** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.

2. A bal oldali ablaktáblán válassza az **Online** elemet, majd a **keresőmezőbe** írja be a **Microsoft.Azure.NotificationHubs** kifejezést.

3. Az eredmények listájából válassza ki a **Microsoft Azure Notification Hubs** elemet, majd az **Install** (Telepítés) lehetőséget. Fejezze be a telepítést, majd zárja be a NuGet-csomagkezelő ablakát.

    Ez a művelet hozzáad egy, az Azure Notification Hubs SDK-ra mutató hivatkozást a [Microsoft.Azure.Notification Hubs NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) használatával.

4. Hozzon létre egy új osztályfájlt, amely az értesítések küldésére használt értesítési központtal való kapcsolatot jelöli. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **Models** (Modellek) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) gombra. Nevezze el az új osztályt **Notifications.cs** néven, majd kattintson az **Add** (Hozzáadás) gombra az osztály létrehozásához.

    ![Az Add New Item (Új elem hozzáadása) ablak][B6]

5. Adja hozzá a következő `using`-utasítást a Notifications.cs fájl elejéhez:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. Cserélje le a `Notifications` osztálydefiníciót a következő kódra, a két helyőrzőt pedig az értesítési központ kapcsolati sztringjére (teljes hozzáféréssel) és a központ nevére (az [Azure Portalon](https://portal.azure.com) érhető el):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
    > [!IMPORTANT]
    > A folytatás előtt adja meg a hub **nevét** és **DefaultFullSharedAccessSignature** . 
    
7. A következő lépésben hozzon létre egy új vezérlőt **RegisterController** néven. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Controller** (Vezérlő) gombra.

8. Válassza a **Web API 2 Controller - Empty** (Web API 2 vezérlő – Üres) elemet, majd kattintson az **Add** (Hozzáadás) gombra.

    ![Az Add Scaffold (Szerkezet hozzáadása) ablak][B7]

9. A **Controller name** (Vezérlő neve) mezőben nevezze el az új osztályt **RegisterController** néven, majd kattintson az **Add** (Hozzáadás) gombra.

    ![Az Add Controller (Vezérlő hozzáadása) ablak][B8]

10. Adja hozzá a következő `using`-utasításokat a RegisterController.cs fájlhoz:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Adja hozzá a következő kódot a(z) `RegisterController` osztálydefiníciójához: Ebben a kódban felhasználói címkét fog hozzáadni a HttpContexthez csatolt felhasználóhoz. A felhasználó hitelesítését és a HttpContexthez való csatolását az Ön által hozzáadott üzenetszűrő (`AuthenticationTestHandler`) végezte. További választható ellenőrzéseket is hozzáadhat, amelyekkel ellenőrizheti, hogy a felhasználó jogosult-e a kért címkékre történő regisztráláshoz.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Mentse a módosításokat.

## <a name="send-notifications-from-the-webapi-backend"></a>Értesítések küldése a WebAPI háttérrendszerről

Ebben a szakaszban egy új vezérlőt ad hozzá, amely értesítés küldését teszi lehetővé az ügyféleszközök számára. A rendszer az értesítést az ASP.NET WebAPI-háttérrendszeren lévő Azure Notification Hubs .NET Libraryt használó felhasználónév-címke alapján hozza létre.

1. Hozzon létre egy másik új, **NotificationsController** nevű vezérlőt. Ennek során ugyanúgy járjon el, mint az előző szakaszban a **RegisterController** vezérlő esetében.

2. Adja hozzá a következő `using`-utasításokat a NotificationsController.cs fájlhoz:

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Adja hozzá a következő metódust a **NotificationsController** osztályhoz:

    Ez a kód egy értesítéstípust fog küldeni a platformértesítési szolgáltatás (PNS) `pns` paramétere alapján. A(z) `to_tag` értékét a rendszer az üzenet *felhasználónév*-címkéjének beállítására használja. A címkének egyeznie kell egy aktív értesítésiközpont-regisztráció felhasználónév-címkéjével. Az értesítési üzenetet a rendszer a POST-kérés törzséből nyeri ki, és a cél PNS-nek megfelelően formázza.

    A támogatott eszközök által az értesítések fogadására használt PNS-től függően az értesítések különböző formátumok használatával támogatottak. Windows rendszerű eszközökön például használhat [bejelentési értesítéseket a WNS formátummal](/uwp/schemas/tiles/toastschema/schema-root), amelyet egy másik PNS nem támogat közvetlenül. Ebben az esetben a háttérrendszernek a támogatni kívánt eszközök PNS-e által támogatott formátum szerint kell formáznia az értesítést. Ezt követően használja a megfelelő küldési API-t a [NotificationHubClient osztályon](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient).

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Nyomja le az **F5** billentyűt az alkalmazás futtatásához, valamint eddigi munkája pontosságának ellenőrzéséhez. Az alkalmazás megnyit egy webböngészőt, amelyen az ASP.NET kezdőlapja jelenik meg.

## <a name="publish-the-new-webapi-backend"></a>Az új WebAPI háttérrendszer közzététele

A következőkben üzembe fogja helyezni ezt az alkalmazást egy Azure-webhelyen, hogy minden eszközről elérhető legyen.

1. Kattintson a jobb gombbal az **AppBackend** projektre, és válassza a **Publish** (Közzététel) lehetőséget.

2. Közzétételi célként válassza a **Microsoft Azure App Service** lehetőséget, majd kattintson a \*\*Publish (Közzététel) gombra. Ekkor megnyílik a Create App Service (App Service létrehozása) ablak. Itt létrehozhatja az összes, az ASP.NET-webalkalmazás Azure-ban való futtatásához szükséges Azure-erőforrást.

    ![A Microsoft Azure App Service (Microsoft Azure App Service) csempe][B15]

3. A **Create App Service** (App Service létrehozása) ablakban válassza ki az Azure-fiókját. Válassza a **change Type**  >  **Web App**(típus módosítása) lehetőséget. Tartsa meg az alapértelmezett **webalkalmazásnevet**, majd válassza a **Subscription** (Előfizetés), **Resource Group** (Erőforráscsoport) és **App Service Plan** (App Service-csomag) elemeket.

4. Válassza a **Létrehozás** lehetőséget.

5. Jegyezze fel a **Summary** (Összegzés) szakaszban lévő **Site URL** (Webhely URL-címe) tulajdonságot. Ezt az URL-címet az oktatóanyag későbbi részében fogja használni *háttérvégpontként*.

6. Válassza a **Közzététel** lehetőséget.

Miután a varázsló befejeződött, közzéteszi az ASP.NET-webalkalmazást az Azure-on, majd megnyitja azt az alapértelmezett böngészőben.  Az alkalmazását az Azure App Servicesben tekintheti meg.

Az URL-cím a webalkalmazás korábban megadott nevét használja, a következő formátumban: http://<alkalmazás_neve>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG