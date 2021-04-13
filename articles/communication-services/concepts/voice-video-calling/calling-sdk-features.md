---
title: Azure Communication Services SDK hívásának áttekintése
titleSuffix: An Azure Communication Services concept document
description: Áttekintést nyújt a hívó SDK-ról.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 28072184d47beb32dc03e0d6ba52328bfceb5b73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364872"
---
# <a name="calling-sdk-overview"></a>Az SDK hívásának áttekintése

A hívó SDK lehetővé teszi, hogy a végfelhasználói eszközök hang- és videokommunikációt használjanak. Ez az oldal részletes leírást nyújt a hívó funkciókról, beleértve a platform- és böngészőtámogatási információkat. Az első lépésekhez tekintse [](../../quickstarts/voice-video-calling/getting-started-with-calling.md) meg a Rövid útmutatók hívása vagy a [Főkép hívása mintát.](../../samples/calling-hero-sample.md) 

Miután megkezdte a fejlesztést, [](../known-issues.md) tekintse meg az ismert problémák oldalát, és keresse meg a hibákat, amelyeken dolgozunk.

A hívó SDK fő funkciói:

- **Címzés –** Azure Communication Services általános [identitásokat biztosít,](../identity-model.md) amelyek a kommunikációs végpontok címzésére használhatók. Az ügyfelek ezekkel az identitásokkal hitelesítik magukat a szolgáltatásban, és kommunikálnak egymással. Ezek az identitások a hívási API-kban használatosak, így az ügyfelek számára láthatóvá teszi, hogy ki csatlakozik egy híváshoz (a névsorba).
- **Titkosítás** – A hívó SDK titkosítja a forgalmat, és megakadályozza az illetéktelen módosításokat a hálózaton. 
- **Eszközkezelés és** média – A hívó SDK olyan lehetőségeket biztosít, amelyek hang- és videoeszközökre kötik a tartalmakat, kódolják a tartalmakat a kommunikációs adatsíkon keresztüli hatékony átvitel érdekében, és renderelik a tartalmat a megadott kimeneti eszközökre és nézetekbe. A képernyő- és alkalmazásmegosztáshoz API-k is rendelkezésre állnak.
- **PSTN –** A hívó SDK képes hanghívásokat fogadni és kezdeményezni a hagyományos, nyilvánosan átkapcsolt telefonos rendszerrel, a telefonos ügyfélszolgálati rendszerben beszerzett [Azure Portal](../../quickstarts/telephony-sms/get-phone-number.md) programozott módon.
- **Teams-értekezletek** – A hívó SDK csatlakozhat a Teams-értekezletekhez, és interakcióba léphet a Teams hang- és videóadatsíkjával. [](../../quickstarts/voice-video-calling/get-started-teams-interop.md) 
- **Értesítések –** A hívó SDK API-kat biztosít, amelyek lehetővé teszik, hogy az ügyfelek értesítést kapnak a bejövő hívásokról. Olyan helyzetekben, amikor az alkalmazás nem az előtérben [](../notifications.md) fut, minták érhetők el felugró értesítések ("bejelentések") küldhetőek, amelyek tájékoztatják a végfelhasználókat a bejövő hívásokról. 

## <a name="detailed-capabilities"></a>Részletes képességek 

Az alábbi lista azokat a funkciókat sorolja fel, amelyek jelenleg elérhetők a Azure Communication Services AZDK-k hívása alatt.

| Funkciók csoportja | Képesség                                                                                                          | Js  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Alapvető képességek | Egy-az-egyhez hívás két felhasználó között                                                                           | ✔️   | ✔️            | ✔️
|                   | Csoportos hívás több mint két felhasználóval (legfeljebb 350 felhasználó)                                                       | ✔️   | ✔️            | ✔️
|                   | Egy-az-egyhez hívás meg hirdetése két felhasználóval egy több mint két felhasználót használó csoporthívásba                                 | ✔️   | ✔️            | ✔️
|                   | Csatlakozás csoportos híváshoz az elindulás után                                                                              | ✔️   | ✔️            | ✔️
|                   | Egy másik VoIP-résztvevő meghívása egy folyamatban lévő csoportos híváshoz                                                       | ✔️   | ✔️            | ✔️
|  Középső hívásvezérlés | Videó be- és kikapcsolása                                                                                              | ✔️   | ✔️            | ✔️
|                   | Némítás/némítás nélküli mikrofon                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Váltás kamerák között                                                                                              | ✔️   | ✔️            | ✔️
|                   | Helyi hold/un-hold                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Aktív beszélő                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Beszélő kiválasztása a hívásokhoz                                                                                            | ✔️   | ✔️            | ✔️
|                   | Mikrofon kiválasztása a hívásokhoz                                                                                         | ✔️   | ✔️            | ✔️
|                   | Résztvevő állapotának megjelenítése<br/>*Idle, Early media, Connecting, Connected, On hold, In Lobby, Disconnected*         | ✔️   | ✔️            | ✔️
|                   | Hívás állapotának megjelenítése<br/>*Korai média, Bejövő, Csatlakozás, Csengés, Csatlakoztatva, Tartsa, Kapcsolat bontása, Leválasztva* | ✔️   | ✔️            | ✔️
|                   | Annak megjelenítése, hogy egy résztvevő el van-e némítva                                                                                      | ✔️   | ✔️            | ✔️
|                   | Annak az oka, hogy egy résztvevő miért távozott a hívástól                                                                       | ✔️   | ✔️            | ✔️
| Képernyőmegosztás    | A teljes képernyő megosztása az alkalmazáson belülről                                                                 | ✔️   | ❌            | ❌
|                   | Egy adott alkalmazás megosztása (a futó alkalmazások listájából)                                                | ✔️   | ❌            | ❌
|                   | Böngészőlap megosztása a megnyitott lapok listájából                                                                  | ✔️   | ❌            | ❌
|                   | A résztvevők megtekinthetik a távoli képernyő megosztását                                                                            | ✔️   | ✔️            | ✔️
| Beosztásról            | Résztvevők listába sorolva                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Résztvevő eltávolítása                                                                                                | ✔️   | ✔️            | ✔️
| Pstn              | Egy-az-egyhez hívás egy PSTN-résztvevővel                                                                     | ✔️   | ✔️            | ✔️
|                   | Csoportos hívás a PSTN-résztvevőkkel                                                                           | ✔️   | ✔️            | ✔️
|                   | Egy-az-egyhez hívás megléptet egy PSTN-résztvevővel csoportos hívásba                                                 | ✔️   | ✔️            | ✔️
|                   | Csoportos hívásból való kitárcsázás PSTN-résztvevőként                                                                    | ✔️   | ✔️            | ✔️
| Általános kérdések           | Tesztelje a mikrofont, a beszélőt és a kamerát egy hangtesztelési szolgáltatással (amely a 8:echo123 hívásával érhető el)                   | ✔️   | ✔️            | ✔️
| Eszközkezelés | Kérjen engedélyt hang- és/vagy videóhasználatra                                                                       | ✔️   | ✔️            | ✔️
|                   | Kameralista lekérte                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Kamera beállítása                                                                                                          | ✔️   | ✔️            | ✔️
|                   | A kiválasztott kamera be- és lekért                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Mikrofonlista lekérte                                                                                                 | ✔️   | ❌           |❌  
|                   | Mikrofon beállítása                                                                                                      | ✔️   | ❌           | ❌  
|                   | A kiválasztott mikrofon be- és lekért                                                                                             | ✔️   | ❌           | ❌  
|                   | Beszélők listájának lekért listája                                                                                                   | ✔️   | ❌           | ❌  
|                   | Beszélő beállítása                                                                                                         | ✔️   | ❌           | ❌  
|                   | Kiválasztott beszélő be- és lekérte                                                                                                | ✔️   | ❌           | ❌  
| Videóre renderelés   | Egyetlen videó renderelése számos helyen (helyi kamera vagy távoli stream)                                                  | ✔️   | ✔️            | ✔️
|                   | Méretezési mód beállítása/frissítése                                                                                           | ✔️   | ✔️            | ✔️
|                   | Távoli videóstream renderelése                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>SDK-streamelési támogatás hívása
A Communication Services hívó SDK a következő streamkonfigurációkat támogatja:

| Korlát          |Webes | Android/iOS|
|-----------|----|------------|
|**Az egyidejűleg elküldhető kimenő streamek számára** |1 videó vagy 1 képernyőmegosztás | 1 videó + 1 képernyőmegosztás|
|**Az egyidejűleg renderelhet bejövő streamek számára** |1 videó vagy 1 képernyőmegosztás| 6 videó + 1 képernyőmegosztás |

## <a name="calling-sdk-timeouts"></a>SDK-időtúllépések hívása

A következő időtúllépések vonatkoznak a Communication Services az SDK-k hívásakor:

| Művelet           | Időtúllépés hossza másodpercben |
| -------------- | ---------- |
| Újracsatlakozás/eltávolítási résztvevő | 120 |
| Új módúság hozzáadása vagy eltávolítása egy hívásból (Videó vagy képernyőmegosztás kezdése/leállítása) | 40 |
| Hívásátviteli művelet időtúllépése | 60 |
| 1:1 A hívás-létesítés időtúllépése | 85 |
| Csoporthívás-létesítés időkorlátja | 85 |
| PSTN-hívás létesítésének időkorlátja | 115 |
| Csoporthívás időtúllépése 1:1 előléptetve | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>JavaScript-hívási SDK-támogatás operációs rendszer és böngésző szerint

Az alábbi táblázat a jelenleg elérhető támogatott böngészőket mutatja be. A böngésző legújabb három verzióját támogatjuk, hacsak nincs másként jelezve.

| Platform                         | Chrome | Safari*  | Edge (Chromium) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS                         |  ✔️    | ✔️**   | ❌             |
| Windows!                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

*A Safari 13.1-es és újabb verziói támogatottak, az 1:1-es hívások a Safariban nem támogatottak.

**Safari 14+/macOS 11+ szükséges a kimenő videótámogatáshoz.

A kimenő képernyőmegosztás csak asztali platformokon (Windows, macOS és Linux) támogatott, a böngésző verziójától függetlenül, és semmilyen mobilplatformon (Android, iOS, iPad és táblagép) nem támogatott.

A Safariban egy iOS-alkalmazás nem képes a mikrofon- és beszélőeszközök (például Bluetooth) számbavételére/kiválasztására; Ez az operációs rendszer korlátozása, és mindig csak egy eszköz van.


## <a name="calling-client---browser-security-model"></a>Ügyfél hívása – böngésző biztonsági modellje

### <a name="user-webrtc-over-https"></a>Felhasználói WebRTC HTTPS-protokollon keresztül

Az olyan WebRTC API-k, mint például megkövetelik, hogy az ezeket az API-kat megindító alkalmazás `getUserMedia` HTTPS-protokollon keresztül szolgálja ki őket.

Helyi fejlesztéshez használhatja a `http://localhost` et.

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>A hívó SDK Communication Services beágyazása iframe-be

A különböző böngészők új engedély-szabályzatot [(más](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) néven szolgáltatás-szabályzatot) hoznak létre. Ez a szabályzat úgy befolyásolja a hívási forgatókönyveket, hogy szabályozza, hogyan férhetnek hozzá az alkalmazások egy eszköz kamerához és mikrofonhoz egy iframe-elemen keresztül.

Ha egy iframe-et szeretne használni az alkalmazás egy másik tartományból származó részének a gazdagépére, hozzá kell adni a megfelelő értékű attribútumot az `allow` iframe-hez.

Ez az iframe például kamera- és mikrofon-hozzáférést is lehetővé tesz:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A hívásának első lépések](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

További információért tekintse át a következő cikkeket:
- Ismerkedjen meg az általános [hívási folyamatokkal](../call-flows.md)
- További információ a [hívástípusokról](../voice-video-calling/about-call-types.md)
- [A PSTN-megoldás megterve](../telephony-sms/plan-solution.md)
