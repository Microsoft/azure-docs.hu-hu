---
title: Hibaelhárítás az Azure kommunikációs szolgáltatásokban
description: Ismerje meg, hogyan gyűjtheti össze a kommunikációs szolgáltatások megoldásához szükséges információkat.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: db6aafc8c9db7a67c9ee70d524d17a642d03dfd8
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259064"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Hibaelhárítás az Azure kommunikációs szolgáltatásokban

Ez a dokumentum segítséget nyújt a kommunikációs szolgáltatási megoldásban felmerülő problémák elhárításában. Ha SMS-sel kapcsolatos hibaelhárítást végez, [engedélyezheti a kézbesítési jelentéskészítést Event Grid](../quickstarts/telephony-sms/handle-sms-events.md) az SMS-kézbesítési adatok rögzítéséhez.

## <a name="getting-help"></a>Segítség kérése

Javasoljuk, hogy a fejlesztők a problémák megoldására, a funkciókra és a problémák jelentésére vonatkozó kérdéseket küldjenek be. Ennek támogatásához egy [dedikált támogatási és Súgó-beállítási oldal](../support.md) áll rendelkezésére, amely a támogatási lehetőségeket sorolja fel.

Bizonyos típusú problémák elhárítása érdekében a következő információk bármelyikét kérheti:

* **MS-CV-azonosító**: ez az azonosító a hívások és üzenetek hibakereséséhez használatos.
* **Call ID**: ez az azonosító a kommunikációs szolgáltatások hívásainak azonosítására szolgál.
* **SMS-üzenet azonosítója**: ez az azonosító az SMS-üzenetek azonosítására szolgál.
* **Hívásnaplók: ezek** a naplók részletes információkat tartalmaznak, amelyek a hívási és hálózati problémák megoldásához használhatók.


## <a name="access-your-ms-cv-id"></a>Hozzáférés az MS-CV-AZONOSÍTÓhoz

Az MS-CV azonosító az SDK-k `clientOptions` inicializálásakor az Object példányban a diagnosztika konfigurálásával érhető el. A diagnosztika konfigurálható bármely Azure SDK-hoz, beleértve a csevegést, az identitást és a VoIP-hívást is.

### <a name="client-options-example"></a>Ügyfél-beállítások – példa

A következő kódrészletek szemléltetik a diagnosztika konfigurációját. Ha az SDK-kat a diagnosztika engedélyezve van, a rendszer a diagnosztikai adatokat a beállított esemény-figyelőhöz fogja kiadni:

# <a name="c"></a>[C#](#tab/csharp)
```
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
```
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Hozzáférés a hívási AZONOSÍTÓhoz

A hang-és videohívások hibaelhárításakor a rendszer kérheti a megadását `call ID` . Ez az `id` objektum tulajdonságán keresztül érhető el `call` :

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.startCall` or `callAgent.join` methods
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId)
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.startCall(…)` or `callAgent.join(…)` methods
Log.d(call.getCallId())
```
---

## <a name="access-your-sms-message-id"></a>Az SMS-üzenet AZONOSÍTÓjának elérése

SMS-problémák esetén a Response objektumból gyűjtheti az üzenet AZONOSÍTÓját.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>Hívásnaplók engedélyezése és elérése

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az Azure kommunikációs szolgáltatások meghívója az SDK [@azure/logger](https://www.npmjs.com/package/@azure/logger) -t belsőleg a könyvtáron, a naplózás vezérlésére támaszkodik.
A `setLogLevel` csomag metódusának használatával `@azure/logger` konfigurálja a napló kimenetét:

```javascript
import { setLogLevel } from '@azure/logger';
setLogLevel('verbose');
const callClient = new CallClient();
```

A AzureLogger segítségével átirányíthatja az Azure SDK-k naplózási kimenetét a `AzureLogger.log` metódus felülbírálásával: Ez akkor lehet hasznos, ha át szeretné irányítani a naplókat a konzolon kívüli helyre.

```javascript
import { AzureLogger } from '@azure/logger';
// redirect log output
AzureLogger.log = (...args) => {
  console.log(...args); // to console, file, buffer, REST API..
};
```

# <a name="ios"></a>[iOS](#tab/ios)

Az iOS rendszerre való fejlesztéskor a naplók `.blog` fájlokba kerülnek. Vegye figyelembe, hogy a naplókat nem lehet közvetlenül megtekinteni, mert azok titkosítottak.

Ezek a Xcode megnyitásával érhetők el. Nyissa meg a Windows > eszközök és szimulátorok > eszközöket. Jelölje ki az eszközét. A telepített alkalmazások területen válassza ki az alkalmazást, és kattintson a "tároló letöltése" elemre.

Ezzel egy fájlt fog adni `xcappdata` . Kattintson a jobb gombbal a fájlra, és válassza a "csomag tartalmának megjelenítése" lehetőséget. Ekkor megjelenik az `.blog` Azure-támogatási kérelemhez csatolni kívánt fájlok.

# <a name="android"></a>[Android](#tab/android)

Az Android rendszerre való fejlesztéskor a rendszer fájlokba tárolja a naplókat `.blog` . Vegye figyelembe, hogy a naplókat nem lehet közvetlenül megtekinteni, mert azok titkosítottak.

Android Studioon navigáljon az eszköz Fájlkezelőhöz úgy, hogy kijelöli a megtekintés > eszköz Windows > eszköz Fájlkezelő lehetőséget a szimulátorból és az eszközről. A `.blog` fájl az alkalmazás könyvtárában fog megjelenni, és így kell kinéznie `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog` . Ezt a fájlt csatolhatja a támogatási kérelemhez.


---

## <a name="calling-sdk-error-codes"></a>SDK-hibakódok hívása

Az Azure kommunikációs szolgáltatások meghívása SDK a következő hibakódokat használja a hívási problémák elhárításához. Ezek a hibakódok a tulajdonságon keresztül jelennek meg a `call.callEndReason` hívás befejeződése után.

| Hibakód | Description | Elvégzendő művelet |
| -------- | ---------------| ---------------|
| 403 | Tiltott/hitelesítési hiba. | Győződjön meg arról, hogy a kommunikációs szolgáltatások jogkivonata érvényes, és nem járt le. |
| 404 | A hívás nem található. | Ellenőrizze, hogy létezik-e a hívott szám (vagy hívja meg az összekötőt). |
| 408 | A hívási vezérlő időkorlátja lejárt. | A hívási vezérlő időtúllépést várt a felhasználói végpontok protokoll üzeneteire való várakozás közben. Győződjön meg arról, hogy az ügyfelek csatlakoztatva vannak és elérhetők. |
| 410 | A helyi média verem vagy a média-infrastruktúra hibája. | Győződjön meg arról, hogy a legújabb SDK-t használja egy támogatott környezetben. |
| 430 | Nem lehet kézbesíteni az üzenetet az ügyfélalkalmazás számára. | Győződjön meg arról, hogy az ügyfélalkalmazás fut és elérhető. |
| 480 | A távoli ügyfél-végpont nincs regisztrálva. | Győződjön meg arról, hogy a távoli végpont elérhető. |
| 481 | Nem sikerült kezelni a bejövő hívást. | Támogatási kérelem nyújtása a Azure Portalon keresztül. |
| 487 | A hívás megszakítva, helyileg visszautasítva, a végpontok eltérő hibája miatt véget ért, vagy nem sikerült létrehozni az adathordozó-ajánlatot. | A várt viselkedés. |
| 490, 491, 496, 487, 498 | Helyi végponti hálózati problémák. | Győződjön meg arról, hogy a hálózatban van. |
| 500, 503, 504 | Kommunikációs szolgáltatások infrastrukturális hibája. | Támogatási kérelem nyújtása a Azure Portalon keresztül. |
| 603 | A távoli kommunikációs szolgáltatások résztvevője által visszautasított globális hívás | A várt viselkedés. |

## <a name="related-information"></a>Kapcsolódó információk
- [Naplók és diagnosztika](logging-and-diagnostics.md)
- [Metrikák](metrics.md)
