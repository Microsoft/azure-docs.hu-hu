---
title: Az ügyféloldali függvénytárat hívó Azure kommunikációs szolgáltatások – áttekintés
titleSuffix: An Azure Communication Services concept document
description: Áttekintést nyújt a hívó ügyféloldali függvénytáráról.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/04/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a26f1a297837a01089783b1e920d1a6a17bc6cdc
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618321"
---
# <a name="calling-client-library-overview"></a>Hívási ügyfélkódtár áttekintése

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Az *ügyfelek* és szolgáltatások esetében két különálló család hívható meg az ügyféloldali kódtárak meghívásával *.* A jelenleg elérhető ügyféloldali kódtárak a végfelhasználói élményekhez készültek: webhelyek és natív alkalmazások.

A szolgáltatás ügyféloldali kódtárai még nem érhetők el, és hozzáférést biztosítanak a robotokkal és más szolgáltatásokkal való integrációhoz alkalmas nyers hang-és video-adatsíkokhoz.

## <a name="calling-client-library-capabilities"></a>Ügyféloldali kódtár képességeinek meghívása

Az alábbi lista azokat a funkciókat mutatja be, amelyek jelenleg elérhetők az Azure kommunikációs szolgáltatásokban az ügyféloldali kódtárak meghívásakor.

| Szolgáltatások csoportja | Képesség                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Alapvető képességek | Egy-az-egyhez hívás elhelyezése két felhasználó között                                                                           | ✔️   | ✔️            | ✔️
|                   | Csoportos hívás elhelyezése kettőnél több felhasználóval (legfeljebb 350 felhasználó)                                                       | ✔️   | ✔️            | ✔️
|                   | Egy-az-egyhez hívás előléptetése két felhasználóval egy csoportos hívásban több mint két felhasználóval                                 | ✔️   | ✔️            | ✔️
|                   | Csatlakozás egy csoportos híváshoz az elindítása után                                                                              | ✔️   | ✔️            | ✔️
|                   | Egy másik VoIP-résztvevő meghívása egy folyamatos csoportos híváshoz                                                       | ✔️   | ✔️            | ✔️
|  Közepes hívások vezérlése | Videó be-és kikapcsolása                                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Mikrofon némítása/feloldása                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Váltás a kamerák között                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Helyi tartás/letartás                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Aktív beszélő                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Válasszon hangszórót a hívásokhoz                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Válassza ki a mikrofont a hívásokhoz                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Résztvevő állapotának megjelenítése<br/>*Tétlen, korai adathordozó, csatlakozás, csatlakoztatott, betartva, a lobbyban, leválasztva*         | ✔️   | ✔️            | ✔️           
|                   | Hívás állapotának megjelenítése<br/>*Korai adathordozó, bejövő, csatlakozás, csengetés, csatlakoztatott, megtartás, leválasztás, leválasztva* | ✔️   | ✔️            | ✔️           
|                   | Annak megjelenítése, hogy a résztvevő el van-e némítva                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Annak az okának a megjelenítése, amiért a résztvevő meghagyott egy hívást                                                                       | ✔️   | ✔️            | ✔️     
| Képernyő megosztása    | A teljes képernyő megosztása az alkalmazáson belülről                                                                 | ✔️   | ❌            | ❌           
|                   | Egy adott alkalmazás megosztása (a futó alkalmazások listájából)                                                | ✔️   | ❌            | ❌           
|                   | Webböngészőt tartalmazó lap megosztása a megnyitott lapok listájából                                                                  | ✔️   | ❌            | ❌           
|                   | A résztvevő megtekintheti a távoli képernyő megosztását                                                                            | ✔️   | ✔️            | ✔️         
| Névsora            | Résztvevők listázása                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Résztvevő eltávolítása                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Egy-az-egyhez hívás elhelyezése PSTN-résztvevővel                                                                     | ✔️   | ✔️            | ✔️   
|                   | Csoportos hívás elhelyezése PSTN-résztvevőkkel                                                                           | ✔️   | ✔️            | ✔️
|                   | Egy-az-egyhez hívás előléptetése PSTN-résztvevővel egy csoportos hívásban                                                 | ✔️   | ✔️            | ✔️
|                   | Tárcsázás egy csoportos hívásból PSTN-résztvevőként                                                                    | ✔️   | ✔️            | ✔️   
| Általános kérdések           | A mikrofon, a hangszóró és a kamera tesztelése hangtesztelési szolgáltatással (elérhető a 8. hívással: echo123)                   | ✔️   | ✔️            | ✔️ 
| Eszközkezelés | Engedély kérése hang és/vagy videó használatára                                                                       | ✔️   | ✔️            | ✔️
|                   | Kamera listájának beolvasása                                                                                                     | ✔️   | ✔️            | ✔️ 
|                   | Kamera beállítása                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Kiválasztott kamera beolvasása                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Mikrofonok listájának beolvasása                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Mikrofon beállítása                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Kiválasztott mikrofon beolvasása                                                                                             | ✔️   | ✔️            | ✔️
|                   | Hangszórók listájának beolvasása                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Hangszóró beállítása                                                                                                         | ✔️   | ✔️            | ✔️
|                   | Kiválasztott hangszóró beolvasása                                                                                                | ✔️   | ✔️            | ✔️
| Videó megjelenítése   | Egyetlen videó renderelése számos helyen (helyi kamera vagy távoli stream)                                                  | ✔️   | ✔️            | ✔️
|                   | Skálázási mód beállítása/frissítése                                                                                           | ✔️   | ✔️            | ✔️ 
|                   | Távoli video stream megjelenítése                                                                                          | ✔️   | ✔️            | ✔️



## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>A JavaScript az operációs rendszer és a böngésző ügyféloldali függvénytárának támogatását hívja meg

A következő táblázat a jelenleg elérhető támogatott böngészők készletét tartalmazza. A böngésző legújabb három verzióját támogatjuk, hacsak másként nincs jelezve.

|                                  | Chrome | Safari  | Edge (króm) | 
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS * * *                         |  ✔️    | ✔️**   | ❌             |
| Windows * * *                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

* A Safari-verziók 13.1 + támogatottak, az 1:1-hívások nem támogatottak a Safarion. 

* * A Safari 14 +/macOS 11 + szükséges a kimenő videók támogatásához. 

A kimenő képernyő megosztása csak asztali platformokon (Windows, macOS és Linux rendszeren) támogatott, a böngésző verziójától függetlenül, és semmilyen mobil platformon (Android, iOS, iPad és Tablet) nem támogatott.

Egy iOS-alkalmazás a Safariban nem tudja enumerálni/kiválasztani a MIC és a hangszórók eszközeit (például Bluetooth); Ez az operációs rendszer korlátozása, és mindig csak egy eszköz van.


## <a name="calling-client---browser-security-model"></a>Ügyfél-böngésző biztonsági modell meghívása

### <a name="user-webrtc-over-https"></a>Felhasználói WebRTC HTTPS-kapcsolaton keresztül

WebRTC API-k `getUserMedia` , például a szükséges, hogy az API-kat meghívó alkalmazás HTTPS-en keresztül legyen kézbesítve.

A helyi fejlesztéshez használhatja a következőt: `http://localhost` .

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Az SDK-t hívó kommunikációs szolgáltatások beágyazása iframe-ben

A különböző böngészők elfogadják az új [engedélyek házirendjét (más néven szolgáltatási szabályzatot)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) . Ez a házirend hatással van a hívási forgatókönyvekre azáltal, hogy azt szabályozza, hogyan érhetik el az alkalmazások az eszköz kameráját és mikrofonját egy kereszthivatkozásos iframe-elem használatával.

Ha iframe-t szeretne használni az alkalmazás egy másik tartományból való tárolásához, az `allow` attribútumot a megfelelő értékkel kell hozzáadnia az IFRAME-hez.

Ez az IFRAME például a kamera és a mikrofon elérését is lehetővé teszi:

```html
<iframe allow="camera *; microphone *">
```

## <a name="calling-client-library-streaming-support"></a>Az ügyféloldali kódtár streaming-támogatásának meghívása
A kommunikációs szolgáltatások az ügyféloldali kódtárat a következő folyamatos átviteli konfigurációkat támogatják:

|           |Webes | Android/iOS|
|-----------|----|------------|
|**egyidejűleg elküldött kimenő adatfolyamok száma** |1 videó + 1 képernyő megosztása | 1 videó + 1 képernyő megosztása|
|**egyidejűleg megjeleníthető bejövő adatfolyamok száma** |1 videó + 1 képernyő megosztása| 6 videó + 1 képernyő megosztása |


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a hívással](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

További információért tekintse át a következő cikkeket:
- Ismerkedjen meg az általános [hívási folyamatokkal](../call-flows.md)
- Tudnivalók a [hívási típusokról](../voice-video-calling/about-call-types.md)
- [A PSTN-megoldás megtervezése](../telephony-sms/plan-solution.md)
