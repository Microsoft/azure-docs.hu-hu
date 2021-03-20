---
title: Regisztráció kezelése
description: Ez a témakör azt ismerteti, hogyan regisztrálhat eszközöket értesítési központokkal a leküldéses értesítések fogadásához.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7807d28da459656938acb399eb8c621e4c292372
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89001569"
---
# <a name="registration-management"></a>Regisztrációkezelés

Ez a témakör azt ismerteti, hogyan regisztrálhat eszközöket értesítési központokkal a leküldéses értesítések fogadásához. A témakör a regisztrációkat magas szinten mutatja be, majd az eszközök regisztrálásának két fő mintáját mutatja be: regisztrálja az eszközről közvetlenül az értesítési központba, és regisztrálja az alkalmazási háttérrendszer használatával.

## <a name="what-is-device-registration"></a>Mi az az eszköz regisztrálása?

Az eszköz regisztrációja az értesítési központtal **regisztráció** vagy **telepítés** használatával történik.

### <a name="registrations"></a>Regisztrációk

A regisztráció társítja a platform Notification Service (PNS) leíróját egy olyan eszközhöz, amely címkével és valószínűleg sablonnal rendelkezik. A PNS-leíró lehet regisztrációban szereplő URI, eszköz-jogkivonat vagy FCM regisztrációs azonosító. A címkék segítségével az értesítéseket az eszközök megfelelő készletére irányíthatja. További információ: [útválasztási és címkézési kifejezések](notification-hubs-tags-segment-push-message.md). A sablonok a regisztrációs átalakítás megvalósítására szolgálnak. További információért lásd a [Sablonok](notification-hubs-templates-cross-platform-push-messages.md) szakaszt.

> [!NOTE]
> Az Azure Notification Hubs legfeljebb 60 címkét támogat eszközönként.

### <a name="installations"></a>Telepítések

A telepítés olyan továbbfejlesztett regisztráció, amely a leküldéses kapcsolódó tulajdonságokat tartalmazza. Az eszközök regisztrálásának legújabb és legjobb megközelítése. Azonban az ügyféloldali .NET SDK (az[értesítési központ SDK for háttér-műveletek esetében](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) nem támogatott.  Ez azt jelenti, hogy ha magáról az eszközről regisztrálja magát, az [Notification Hubs REST API](/rest/api/notificationhubs/create-overwrite-installation) megközelítést kell használnia a telepítések támogatásához. Ha háttér-szolgáltatást használ, akkor [az Notification hub SDK-t használhatja a háttérbeli műveletekhez](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

A következő alapvető előnyökkel jár a telepítések használata:

- A telepítés létrehozása vagy frissítése teljesen idempotens. Így újra megpróbálkozhat az ismétlődő regisztrációkkal kapcsolatos probléma nélkül is.
- A telepítési modell olyan speciális címke formátumot támogat ( `$InstallationId:{INSTALLATION_ID}` ), amely lehetővé teszi, hogy az értesítéseket közvetlenül az adott eszközre küldje. Ha például az alkalmazás kódja az `joe93developer` adott eszközhöz tartozó telepítési azonosítót állítja be, akkor a fejlesztők megcélozhatja ezt az eszközt, amikor értesítést küld a `$InstallationId:{joe93developer}` címkének. Ez lehetővé teszi egy adott eszköz célzását anélkül, hogy további kódolásra lenne szükség.
- A telepítések lehetővé teszik a részleges regisztrációs frissítések elvégzését is. A telepítés részleges frissítését egy PATCH metódussal kell kérni a [JSON-patch szabvány](https://tools.ietf.org/html/rfc6902)használatával. Ez akkor hasznos, ha frissíteni szeretné a címkéket a regisztrációban. Nem kell lekérnie a teljes regisztrációt, majd újra el kell küldenie az összes korábbi címkét.

A telepítés a következő tulajdonságokat tartalmazza. A telepítési tulajdonságok teljes listáját lásd: [telepítés létrehozása vagy felülírása REST API](/rest/api/notificationhubs/create-overwrite-installation) vagy [telepítési tulajdonságokkal](/dotnet/api/microsoft.azure.notificationhubs.installation).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Alapértelmezés szerint a regisztrációk és a telepítések nem járnak le.

A regisztrációknak és a telepítéseknek érvényes PNS-leírót kell tartalmazniuk minden eszközhöz/csatornához. Mivel a PNS-kezelők csak az eszközön lévő ügyfélalkalmazások szerezhetők be, az egyik minta az, hogy közvetlenül az adott eszközön regisztrálja az alkalmazást. Másfelől a címkékhez kapcsolódó biztonsági megfontolások és üzleti logika esetében szükség lehet az eszközök regisztrációjának felügyeletére az alkalmazás hátterében.

> [!NOTE]
> A telepítési API nem támogatja a Baidu szolgáltatást (bár a regisztrációk API-t). 

### <a name="templates"></a>Sablonok

Ha [sablonokat](notification-hubs-templates-cross-platform-push-messages.md)szeretne használni, az eszköz telepítése az adott eszközhöz társított összes sablont JSON formátumban is tartalmazza (lásd a fenti mintát). A sablon neve segít megcélozni a különböző sablonokat ugyanahhoz az eszközhöz.

Minden sablon neve egy sablon törzsére és egy opcionális címkére van leképezve. Emellett minden platformhoz további sablon-tulajdonságok is tartozhatnak. A Windows áruházban (WNS használatával) és Windows Phone-telefon 8 (a MPNS használatával) további fejlécek is lehetnek a sablon részei. APNs esetén a lejárati tulajdonságot állandó vagy sablon típusú kifejezésre állíthatja be. A telepítési tulajdonságok teljes listájáért tekintse meg, hogyan [hozhat létre vagy írhat felül egy telepítést a REST](/rest/api/notificationhubs/create-overwrite-installation) témakörben.

### <a name="secondary-tiles-for-windows-store-apps"></a>Másodlagos csempék a Windows áruházbeli alkalmazásokhoz

A Windows áruházbeli ügyfélalkalmazások esetében az értesítések másodlagos csempévé való küldése ugyanaz, mint az elsődlegesnek. Ez a telepítésekben is támogatott. A másodlagos csempék eltérő regisztrációban szereplő URI rendelkeznek, amelyeket az ügyfélalkalmazás transzparens módon kezel.

A SecondaryTiles szótár ugyanazt a TileId használja, amely a SecondaryTiles objektum létrehozásához használható a Windows áruházbeli alkalmazásban. Ahogy az elsődleges regisztrációban szereplő URI is, a másodlagos csempék ChannelUris bármikor megváltozhatnak. Ahhoz, hogy az értesítési központban lévő telepítések naprakészek maradjanak, az eszköznek frissítenie kell azokat a másodlagos csempék aktuális ChannelUris.

## <a name="registration-management-from-the-device"></a>Regisztrációs felügyelet az eszközről

Ha az ügyfél alkalmazásaiból végzi az eszközök regisztrálását, a háttérrendszer csak az értesítések elküldéséhez felelős. Az ügyfélalkalmazások folyamatosan kezelik a PNS, és regisztrálják a címkéket. A következő kép szemlélteti ezt a mintát.

![Regisztráció az eszközről](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Az eszköz először lekéri a PNS leírót a PNS, majd közvetlenül regisztrálja az értesítési központot. A regisztráció sikerességét követően az alkalmazás-háttérrendszer értesítést küldhet a regisztrációról. További információ az értesítések küldéséről: [útválasztási és címkézési kifejezések](notification-hubs-tags-segment-push-message.md).

Ebben az esetben csak a figyelési jogosultságokat használja az értesítési központok eléréséhez az eszközről. További információ: [Biztonság](notification-hubs-push-notification-security.md).

Az eszközről való regisztráció a legegyszerűbb módszer, de néhány hátránya van:

- Az ügyfélalkalmazások csak akkor frissíthetik a címkéket, ha az alkalmazás aktív. Ha például egy felhasználó két olyan eszközzel rendelkezik, amelyek a sport csapatával kapcsolatos címkéket regisztrálnak, amikor az első eszköz regisztrálja egy további címkét (például Seahawks), a második eszköz nem kapja meg a Seahawks vonatkozó értesítéseket, amíg a második eszközön lévő alkalmazást nem hajtja végre második alkalommal. Általánosságban elmondható, hogy ha a címkéket több eszköz is érinti, a címkék a háttérből való kezelése kívánatos lehetőség.
- Mivel az alkalmazások feltölthetők, a regisztráció adott címkékre történő biztonságossá tételéhez extra gond szükséges, ahogy azt a cikk [biztonsági](notification-hubs-push-notification-security.md)része ismerteti.

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Példa kód egy értesítési központból a telepítéssel való regisztráláshoz

Jelenleg ez csak a [Notification Hubs REST API](/rest/api/notificationhubs/create-overwrite-installation)használatával támogatott.

A PATCH metódust a [JSON-patch standard](https://tools.ietf.org/html/rfc6902) használatával is használhatja a telepítés frissítéséhez.

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored an installation ID in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Példa kód egy értesítési központból regisztrációt használó eszközről való regisztrálásra

Ezek a módszerek létrehoznak vagy frissítenek egy regisztrációs eszközt arra az eszközre, amelyen meghívja őket. Ez azt jelenti, hogy a leíró vagy a címkék frissítéséhez felül kell írnia a teljes regisztrációt. Ne feledje, hogy a regisztrációk átmenetiek, ezért mindig megbízható tárolóval kell rendelkeznie az adott eszközhöz szükséges aktuális címkékkel.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
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

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Regisztráció a háttérrendszer-kezelőből

A háttérbeli regisztrációk kezeléséhez további kódokat kell írni. Az eszközön lévő alkalmazásnak meg kell adnia a frissített PNS-leírót a háttérnek minden alkalommal, amikor az alkalmazás elindul (címkékkel és sablonokkal együtt), és a háttérrendszer frissítenie kell ezt a leírót az értesítési központban. A következő kép szemlélteti ezt a kialakítást.

![Regisztrációkezelés](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

A háttérbeli regisztrációk kezelésének előnyei közé tartozik a címkék módosításának lehetősége, még akkor is, ha az eszközön található megfelelő alkalmazás inaktív, valamint az ügyfélalkalmazás hitelesítéséhez, mielőtt hozzáad egy címkét a regisztrációhoz.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Példa kód egy értesítési központból egy telepítéssel való regisztrációhoz

Az eszköz továbbra is megkapja a PNS leíróját és a kapcsolódó telepítési tulajdonságokat, mint korábban, és meghívja a háttérbeli egyéni API-t, amely elvégzi a regisztrációt, és engedélyezi a címkéket stb. A háttérrendszer-kezelők az [értesítési központ SDK-t használhatják a háttérbeli műveletekhez](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

A PATCH metódust a [JSON-patch standard](https://tools.ietf.org/html/rfc6902) használatával is használhatja a telepítés frissítéséhez.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Példa kód egy értesítési központból egy regisztrációs AZONOSÍTÓval rendelkező eszközről való regisztráláshoz

Az alkalmazás-háttérrendszer segítségével alapszintű szifilisz-műveleteket hajthat végre a regisztráció során. Például:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

A háttérnek a regisztrációs frissítések közötti párhuzamosságot kell kezelnie. A Service Bus optimista Egyidejűség-vezérlést biztosít a regisztrációk kezeléséhez. A HTTP-szinten ez a ETag használatával történik a regisztrálási felügyeleti műveletekben. Ezt a szolgáltatást a Microsoft SDK-k transzparens módon használják, amelyek kivételt képeznek, ha egy frissítést a Egyidejűség miatt elutasítanak. Az alkalmazás-háttérrendszer feladata a kivételek kezelése, és szükség esetén a frissítés újbóli kipróbálása.
