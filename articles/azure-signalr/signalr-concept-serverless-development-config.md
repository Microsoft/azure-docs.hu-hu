---
title: Azure Functions-alkalmazás fejlesztése & konfigurálása – Azure-jelző
description: A kiszolgáló nélküli valós idejű alkalmazások fejlesztésének és konfigurálásának részletei a Azure Functions és az Azure Signaler szolgáltatás használatával
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3d69b72012819e3d9099e447b9048fe07aea86d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97858705"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással

Azure Functions alkalmazások a valós idejű funkciók hozzáadására használhatják az [Azure signaler szolgáltatás kötéseit](../azure-functions/functions-bindings-signalr-service.md) . Az ügyfélalkalmazások számos nyelven elérhető ügyféloldali SDK-kat használnak az Azure Signaler szolgáltatáshoz való csatlakozáshoz és valós idejű üzenetek fogadásához.

Ez a cikk a Signaler szolgáltatással integrált Azure Function-alkalmazások fejlesztésére és konfigurálására vonatkozó fogalmakat ismerteti.

## <a name="signalr-service-configuration"></a>A signaler szolgáltatás konfigurációja

Az Azure Signaler szolgáltatás különböző módokon konfigurálható. Azure Functions használata esetén a szolgáltatást *kiszolgáló* nélküli módban kell konfigurálni.

A Azure Portal keresse meg a szignáló szolgáltatás erőforrásának *Beállítások* lapját. Állítsa a *szolgáltatási módot* *kiszolgáló* nélküli értékre.

![Jelző szolgáltatás üzemmódja](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Az Azure Functions fejlesztése

Az Azure Functions és az Azure SignalR szolgáltatással létrehozott kiszolgáló nélküli, valós idejű alkalmazásokhoz két Azure-függvényre van szükség:

* A „negotiate” függvényre, amelyet az ügyfél egy érvényes SignalR-szolgáltatási hozzáférési jogkivonat és a szolgáltatásvégpont URL-címének lekéréséhez hív meg
* Egy vagy több olyan függvény, amely kezeli a Signaler szolgáltatás üzeneteit, és üzeneteket küld vagy kezel csoporttagság

### <a name="negotiate-function"></a>egyeztetési függvény

Az ügyfélalkalmazás érvényes hozzáférési jogkivonatot igényel az Azure Signaler szolgáltatáshoz való kapcsolódáshoz. A hozzáférési tokenek névtelenek vagy egy adott felhasználói AZONOSÍTÓra hitelesíthetők. A kiszolgáló nélküli szignáló szolgáltatás alkalmazásai egy "egyeztetés" nevű HTTP-végpontot igényelnek a jogkivonat és egyéb kapcsolódási információk beszerzéséhez, például a Signaler szolgáltatás végpontjának URL-címét.

A kapcsolati információ objektum létrehozásához használjon HTTP-triggert használó Azure-függvényt és a *SignalRConnectionInfo* bemeneti kötését. A függvénynek olyan HTTP-útvonalon kell lennie, amely a-ben ér véget `/negotiate` .

A [C#](#class-based-model) nyelvben nem szükséges a *SignalRConnectionInfo* bemeneti kötése, és sokkal könnyebben adhat hozzá egyéni jogcímeket. Lásd: [a tapasztalatok egyeztetése osztály alapú modellben](#negotiate-experience-in-class-based-model)

Az egyeztetési függvény létrehozásával kapcsolatos további információkért tekintse meg a [ *SignalRConnectionInfo* bemeneti kötési referenciáját](../azure-functions/functions-bindings-signalr-service-input.md).

A hitelesített tokenek létrehozásáról a [app Service hitelesítés használata](#using-app-service-authentication)című témakörben olvashat bővebben.

### <a name="handle-messages-sent-from-signalr-service"></a>A Signaler szolgáltatásból küldött üzenetek kezelése

A signaler- *trigger* kötésének használatával kezelheti a jelző szolgáltatásból küldött üzeneteket. Értesítést kaphat, ha az ügyfelek üzeneteket vagy ügyfeleket küldenek a csatlakozáshoz vagy a kapcsolat bontásához.

További információ: a [ *jelző trigger* kötési hivatkozása](../azure-functions/functions-bindings-signalr-service-trigger.md).

Emellett a függvény végpontját is be kell állítania, hogy a szolgáltatás elindítsa a függvényt, ha az ügyfélről érkezik üzenet. A felsőbb réteg konfigurálásával kapcsolatos további tudnivalókért tekintse [meg ezt a](concept-upstream.md)dokumentumot.

### <a name="sending-messages-and-managing-group-membership"></a>Üzenetek küldése és csoporttagság kezelése

A *signaler* kimeneti kötés használatával üzeneteket küldhet az Azure Signaler szolgáltatáshoz csatlakozó ügyfeleknek. Az üzeneteket az összes ügyfél számára továbbíthatja, vagy egy adott felhasználói AZONOSÍTÓval hitelesített vagy egy adott csoportba felvett ügyfelek egy részhalmazára is elküldheti.

A felhasználók hozzáadhatók egy vagy több csoporthoz. A *jelző* kimeneti kötés használatával felhasználókat adhat hozzá vagy távolíthat el a csoportokból.

További információ: a [ *signaler* output kötési referenciája](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Szignáló hubok

A jelző a "hubok" fogalmát mutatja. Az egyes ügyfélkapcsolatok és a Azure Functionsból küldött összes üzenet hatóköre egy adott hubhoz tartozik. A hubokat a kapcsolatok és az üzenetek logikai névterekre való elkülönítésére használhatja.

## <a name="class-based-model"></a>Osztály alapú modell

Az osztály alapú modell a C# nyelvre van kijelölve. A Class-alapú modellel konzisztens, a Signaler kiszolgálóoldali programozási felülete lehet. A következő funkciókkal rendelkezik.

* Kevesebb konfigurációs munka: az osztálynév a nevet használja `HubName` , a metódus neve pedig a `Event` `Category` metódus neve alapján automatikusan eldöntve.
* Automatikus paraméter kötése: `ParameterNames` nincs szükség sem attribútumra `[SignalRParameter]` . A paraméterek az Azure Function metódus argumentumait automatikusan kötik a sorrendben.
* Kényelmes kimeneti és egyeztetési élmény.

A következő kódok szemléltetik ezeket a funkciókat:

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

Minden olyan függvénynek, amely a Class-alapú modellt kívánja használni, a **ServerlessHub** örökölt osztály metódusának kell lennie. A példában szereplő osztálynév `SignalRTestHub` a hub neve.

### <a name="define-hub-method"></a>Hub-metódus megadása

Az összes hub-metódusnak rendelkeznie **kell** `InvocationContext` `[SignalRTrigger]` attribútummal, és a paraméter nélküli konstruktort kell használnia. Ezt követően a **metódus neve** paraméter **eseményként** lesz kezelve.

Alapértelmezés szerint `category=messages` a metódus neve csak a következő nevek egyike lehet:

* **OnConnected**: kezelés másként `category=connections, event=connected`
* **OnDisconnected**: kezelés másként `category=connections, event=disconnected`

### <a name="parameter-binding-experience"></a>Paraméter kötési élménye

Az osztály alapú modellben `[SignalRParameter]` szükségtelen, mert az összes argumentum alapértelmezettként van megjelölve, kivéve, ha az `[SignalRParameter]` alábbi esetek egyike:

* Az argumentumot egy kötési attribútum rendezi.
* Az argumentum típusa a következő, `ILogger` vagy `CancellationToken`
* Az argumentumot attribútum szerint rendezi a rendszer. `[SignalRIgnore]`

### <a name="negotiate-experience-in-class-based-model"></a>Az osztály-alapú modell egyeztetése

A Signal bemeneti kötés használata helyett a `[SignalR]` Class-alapú modell egyeztetése rugalmasabb lehet. Az alaposztály `ServerlessHub` metódusa

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

Ez a funkció a felhasználó által testre szabható `userId` vagy `claims` a függvény végrehajtása közben testreszabható.

## <a name="use-signalrfilterattribute"></a>A `SignalRFilterAttribute` használata

A felhasználó örökölheti és implementálhatja az absztrakt osztályt `SignalRFilterAttribute` . Ha kivételeket vált ki `FilterAsync` , a `403 Forbidden` rendszer visszaküldi az ügyfeleknek.

Az alábbi minta bemutatja, hogyan valósítható meg egy olyan ügyfél-szűrő, amely csak a `admin` meghívását engedélyezi `broadcast` .

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

Használja ki az attribútumot a függvény engedélyezéséhez.

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>Ügyfél-fejlesztés

A signaler ügyfélalkalmazások a Signaler Client SDK-t több nyelven is kihasználhatják, így egyszerűen csatlakozhatnak és fogadhatnak üzeneteket az Azure Signaler szolgáltatástól.

### <a name="configuring-a-client-connection"></a>Ügyfélkapcsolat konfigurálása

A Signaler szolgáltatáshoz való kapcsolódáshoz az ügyfélnek sikeres kapcsolódási egyeztetést kell végrehajtania, amely a következő lépésekből áll:

1. A fentiekben ismertetett *egyeztetési* http-végpontra vonatkozó kérelem elvégzése érvényes kapcsolódási adatok beszerzéséhez
1. Kapcsolódás a Signaler szolgáltatáshoz a szolgáltatási végpont URL-címével és az *egyeztetési* végponttól kapott hozzáférési jogkivonat használatával

A signaler ügyféloldali SDK-k már tartalmazzák az egyeztetési kézfogás végrehajtásához szükséges logikát. Adja át az egyeztetési végpont URL-címét, mínusz a `negotiate` szegmenst az SDK-hoz `HubConnectionBuilder` . Íme egy példa a JavaScriptben:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

A konvenció szerint az SDK automatikusan hozzáfűzi `/negotiate` az URL-címhez, és az egyeztetés megkezdéséhez használja azt.

> [!NOTE]
> Ha a JavaScript/írógéppel SDK-t használja egy böngészőben, engedélyeznie kell az [CORS-alapú erőforrás-megosztást](#enabling-cors) a függvényalkalmazás.

A Signaler Client SDK használatával kapcsolatos további információkért tekintse meg a nyelv dokumentációját:

* [.NET Standard](/aspnet/core/signalr/dotnet-client)
* [JavaScript](/aspnet/core/signalr/javascript-client)
* [Java](/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Üzenetek küldése az ügyféltől a szolgáltatásnak

Ha a Signaler-erőforráshoz beállított [felsőbb réteggel](concept-upstream.md) rendelkezik, akkor bármely szignáló ügyféllel küldhet üzeneteket az ügyféltől a Azure functions. Íme egy példa a JavaScriptben:

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Azure Functions konfiguráció

Az Azure Signaler szolgáltatással integrálható Azure functions alkalmazások ugyanúgy telepíthetők, mint bármely jellemző Azure Function-alkalmazás, például a [folyamatos üzembe helyezés](../azure-functions/functions-continuous-deployment.md), a [zip-telepítés](../azure-functions/deployment-zip-push.md)és [a csomagból való futtatás](../azure-functions/run-functions-from-deployment-package.md).

A Signaler szolgáltatás kötéseit használó alkalmazások esetében azonban néhány speciális szempontot figyelembe kell venni. Ha az ügyfél böngészőben fut, engedélyezni kell a CORS. Ha az alkalmazás hitelesítést igényel, akkor App Service hitelesítéssel integrálhatja az egyeztetési végpontot.

### <a name="enabling-cors"></a>CORS engedélyezése

A JavaScript/írógéppel ügyfél HTTP-kérelmeket tesz elérhetővé az egyeztetési függvénynek a kapcsolódási egyeztetés elindításához. Ha az ügyfélalkalmazás az Azure Function alkalmazástól eltérő tartományban található, az eltérő eredetű erőforrás-megosztást (CORS) engedélyezni kell a Function alkalmazásban, vagy a böngésző letiltja a kérelmeket.

#### <a name="localhost"></a>Localhost

Ha a Function alkalmazást a helyi számítógépen futtatja, akkor a `Host` CORS engedélyezéséhez hozzáadhat egy szakaszt a *local.settings.jshoz* . A `Host` szakaszban adja hozzá a két tulajdonságot:

* `CORS` -adja meg az ügyfélalkalmazás forrásaként szolgáló alap URL-címet
* `CORSCredentials` -Állítsa be, hogy `true` engedélyezze a "withCredentials" kéréseket

Példa:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>Felhőbeli Azure Functions CORS

Ha engedélyezni szeretné a CORS az Azure Function alkalmazásban, lépjen a CORS konfigurációs képernyőjére a Function alkalmazás *platform szolgáltatások* lapján a Azure Portal.

> [!NOTE]
> A CORS-konfiguráció még nem érhető el Azure Functions Linux-használati tervben. Az [Azure API Management](#cloud---azure-api-management) használatával engedélyezze a CORS.

A CORS hozzáférés-vezérlést engedélyező hitelesítő adatokat engedélyezni kell a Signaler-ügyfélnek az egyeztetési függvény meghívásához. Jelölje be a jelölőnégyzetet az engedélyezéshez.

Az *engedélyezett eredetek* szakaszban adjon hozzá egy bejegyzést a webalkalmazás forrás alap URL-címével.

![CORS konfigurálása](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Felhő – Azure API Management

Az Azure API Management olyan API-átjárót biztosít, amely funkciókkal bővíti a meglévő háttér-szolgáltatásokat. Használhatja CORS hozzáadásához a Function alkalmazáshoz. A szolgáltatás díjköteles díjszabással és havi ingyenes támogatással rendelkezik.

Az [Azure functions-alkalmazások importálásával](../api-management/import-function-app-as-api.md)kapcsolatos információkért tekintse meg az API Management dokumentációját. Az importálás után hozzáadhat egy bejövő szabályzatot, amely lehetővé teszi a CORS hozzáférés-vezérlés – engedélyezés – hitelesítő adatok támogatását.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Konfigurálja a Signaler-ügyfeleket a API Management URL-cím használatára.

### <a name="using-app-service-authentication"></a>App Service hitelesítés használata

A Azure Functions beépített hitelesítéssel támogatja a népszerű szolgáltatók, például a Facebook, a Twitter, a Microsoft-fiók, a Google és a Azure Active Directory támogatását. Ez a funkció integrálható a *SignalRConnectionInfo* -kötéssel, hogy kapcsolatot hozzon létre az Azure-beli jelző szolgáltatással, amelyek hitelesítése felhasználói azonosítóra történt. Az alkalmazás képes üzeneteket küldeni az adott felhasználói AZONOSÍTÓra irányuló *szignáló* kimeneti kötés használatával.

A Azure Portal a Function app *platform-funkciók* lapján nyissa meg a *hitelesítési/engedélyezési* beállítások ablakot. A hitelesítés konfigurálásához kövesse az [app Service-hitelesítés](../app-service/overview-authentication-authorization.md) dokumentációját.

A konfigurálást követően a hitelesített HTTP-kérelmek tartalmazzák `x-ms-client-principal-name` `x-ms-client-principal-id` a hitelesített identitás felhasználónevét és a felhasználói azonosítóját tartalmazó fejléceket is.

A *SignalRConnectionInfo* kötési konfigurációjában ezeket a fejléceket használhatja a hitelesített kapcsolatok létrehozásához. Íme egy példa C# egyeztetési függvény, amely a `x-ms-client-principal-id` fejlécet használja.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Ezután üzeneteket küldhet az adott felhasználónak a `UserId` jelző üzenet tulajdonságának beállításával.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Más nyelvekkel kapcsolatos információkért tekintse meg az [Azure signaler szolgáltatás kötéseit](../azure-functions/functions-bindings-signalr-service.md) Azure functions-referenciához.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan fejlesztheti és konfigurálhatja a kiszolgáló nélküli Signal Service-alkalmazásokat Azure Functions használatával. Hozzon létre egy alkalmazást saját maga a [signaler szolgáltatás áttekintés lapján](index.yml)található gyors indítás vagy oktatóanyag használatával.
