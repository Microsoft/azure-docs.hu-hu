---
title: Azure kommunikációs szolgáltatások – ismert problémák
description: További információ az Azure kommunikációs szolgáltatásokról
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: aa5530dd279e8b45382fe6841b6f193a652c0ba3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566795"
---
# <a name="known-issues-azure-communication-services-client-libraries"></a>Ismert problémák: Azure kommunikációs szolgáltatások ügyféloldali kódtárai
Ez a cikk az Azure kommunikációs szolgáltatások ügyféloldali kódtárakkal kapcsolatos korlátozásokkal és ismert problémákról nyújt információkat.

> [!IMPORTANT]
> Több tényező is befolyásolhatja a hívási élmény minőségét. A **[hálózati követelmények](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** dokumentációjában tájékozódhat a kommunikációs szolgáltatások hálózati konfigurációjáról és az ajánlott eljárások teszteléséről.


## <a name="javascript-client-library"></a>JavaScript-ügyfélkódtár

Ez a szakasz a JavaScript hang-és videohívások ügyféloldali kódtárainak az Azure kommunikációs szolgáltatásokban való kezelésével kapcsolatos ismert problémákról nyújt információkat.

### <a name="after-refreshing-the-page-user-is-not-removed-from-the-call-immediately"></a>Az oldal frissítése után a felhasználót a rendszer nem távolítja el azonnal a hívásból 
Ha a felhasználó egy hívásban van, és úgy dönt, hogy frissíti a lapot, akkor előfordulhat, hogy a kommunikációs szolgáltatások ügyféloldali könyvtára nem fogja tudni tájékoztatni a kommunikációs szolgáltatások adathordozó-szolgáltatását, hogy a rendszer leválasztja a kapcsolatot. A kommunikációs szolgáltatások adathordozó-szolgáltatása nem távolítja el a felhasználót azonnal a hívásból, de megvárja, amíg a felhasználó újracsatlakozik a hálózati kapcsolattal kapcsolatos problémákkal. A rendszer eltávolítja a felhasználót a hívásból, miután a Media Service időtúllépést okoz.

Javasoljuk, hogy a fejlesztők olyan tapasztalatokat hozzanak létre, amelyeknek nincs szükségük arra, hogy a végfelhasználók az alkalmazás lapjait a hívásokban is frissíteni lehessen. Ha a felhasználó frissíti az oldalt, a legjobb módszer az alkalmazás kezeléséhez, ha újra ugyanazt a kommunikációs szolgáltatásokat használó felhasználói azonosítót használja, miután a frissítés után visszaadja az alkalmazásnak.

A hívás más résztvevői szempontjából ez a felhasználó az előre megadott idő (1-2 perc) meghívása után is megmarad. Ha a felhasználó ugyanazzal a kommunikációs szolgáltatásbeli felhasználói AZONOSÍTÓval csatlakozik újra, akkor a gyűjteményben szereplő meglévő objektumként fog szerepelni `remoteParticipants` .
Ha korábban a felhasználó elküldte a videót, a `videoStreams` gyűjtemény mindaddig megőrzi a korábbi stream-információkat, amíg a szolgáltatás meg nem adja az időkorlátot, és nem távolítja el, ebben a forgatókönyvben az alkalmazás dönthet úgy, hogy megfigyeli a gyűjteményhez hozzáadott új adatfolyamokat `id` 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Több előnézetet nem lehet a weben több eszközről megjeleníteni
Ez egy ismert korlátozás. További információért tekintse meg az [ügyféloldali függvénytár meghívása című](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) témakört.

### <a name="enumeration-of-the-microphone-and-speaker-devices-is-not-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>A mikrofon-és hangsugárzó eszközök enumerálása nem lehetséges a Safariban, ha az alkalmazás iOS vagy iPadOS rendszeren fut 
Az alkalmazások nem tudják enumerálni/kiválasztani a MIC/Speaker eszközöket (például Bluetooth) a Safari iOS/iPad szolgáltatásban. Ez az operációs rendszer ismert korlátozása.

Ha macOS rendszeren használja a Safari alkalmazást, az alkalmazás nem fogja tudni felsorolni/kiválasztani a beszélőket a kommunikációs szolgáltatások Eszközkezelő. Ebben az esetben az eszközöket az operációs rendszer használatával kell kiválasztani. Ha macOS rendszeren használja a Chrome-t, az alkalmazás a kommunikációs szolgáltatások Eszközkezelő segítségével enumerálhatja vagy kiválaszthatja az eszközöket.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Az SMS-üzenetek vagy-hívások fogadásakor a rendszer elveszi a hangkapcsolatot egy folyamatban lévő VoIP-hívás során
A mobil böngészők nem tartanak fenn kapcsolatot a háttérben állapotban. Ennek eredményeként csökkenhet a hívás, ha a VoIP-hívást szöveges üzenet vagy bejövő PSTN-hívás megszakította, amely az alkalmazást a háttérben küldi el.

<br/>Ügyféloldali kódtár: hívás (JavaScript)
<br/>Böngészők: Safari, Chrome
<br/>Operációs rendszer: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>A video-eszközök többszöri váltásakor előfordulhat, hogy a video streaming átmenetileg leáll

Előfordulhat, hogy a video-eszközök közötti váltás miatt a videó adatfolyama szüneteltethető, miközben a rendszer a kiválasztott eszközről szerzi be a streamet.

#### <a name="possible-causes"></a>Lehetséges okok
A médiaadatfolyam-továbbítás és a mobileszközök közötti váltás számítási igényű. A gyakori váltás a teljesítmény romlását okozhatja. A fejlesztőknek javasoljuk, hogy egy másik eszköz elindítása előtt állítsanak le egy adatfolyamot.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>A Bluetooth Headset mikrofonja nem észlelhető, ezért nem hallható a Safari iOS rendszeren való hívásakor
A Safari nem támogatja a Bluetooth-fejhallgatókat iOS rendszeren. A Bluetooth-eszköz nem jelenik meg a rendelkezésre álló mikrofon beállításaiban, és a többi résztvevő nem fogja tudni meghallgatni, ha a Bluetooth-t használja a Safari használatával.

#### <a name="possible-causes"></a>Lehetséges okok
Ez egy ismert macOS/iOS/iPadOS operációs rendszer korlátozása. 

A **MacOS** és az **iOS/iPadOS** esetében nem lehetséges a beszélő eszközök enumerálása/kiválasztása a kommunikációs Eszközkezelő szolgáltatásokon keresztül, mivel a (z) Safari nem támogatja a hangszórók enumerálását/kijelölését. Ebben az esetben az eszköz kiválasztását az operációs rendszeren keresztül kell frissíteni.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>Az eszköz elforgatása gyenge minőségű videót hozhat létre
Az eszközök elforgatásakor a felhasználók csökkenthetik a videók minőségét.

<br/>Érintett eszközök: Google pixel 5, Google pixel 3a, Apple iPad 8 és Apple iPad X
<br/>Ügyféloldali kódtár: hívás (JavaScript)
<br/>Böngészők: Safari, Chrome
<br/>Operációs rendszer: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>A kamera-váltás a képernyő befagyasztását teszi lehetővé 
Ha egy kommunikációs szolgáltatás felhasználója a JavaScriptet hívó ügyféloldali kódtár használatával csatlakozik egy híváshoz, majd a kamera kapcsoló gombja megkeresi, a felhasználói felület teljesen nem válaszol, amíg az alkalmazás frissítve nem lesz, vagy a böngészőt a felhasználó a háttérre küldi.

<br/>Érintett eszközök: Google pixel 4a
<br/>Ügyféloldali kódtár: hívás (JavaScript)
<br/>Böngészők: Chrome
<br/>Operációs rendszer: iOS, Android


#### <a name="possible-causes"></a>Lehetséges okok
A vizsgálat alatt.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Ha a rendszer leállította a videó jelét, miközben a hívás "Csatlakozás" állapotban van, a rendszer nem küldi el a videót a hívás elindítása után. 
Ha a felhasználó úgy dönt, hogy a hívás állapota alatt gyorsan be-és kikapcsolja a videót, `Connecting` akkor ez a híváshoz kapcsolódó stream-problémákhoz vezethet. Arra bátorítjuk a fejlesztőket, hogy az alkalmazásaikat olyan módon készítsék el, amely nem igényli a videó be-és kikapcsolását, amíg a hívás `Connecting` állapotban van. A probléma a következő esetekben csökkenhet a videó teljesítményében:

 - Ha a felhasználó hanggal kezdődik, majd elindítja és leállítja a videót, miközben a hívás állapota folyamatban van `Connecting` .
 - Ha a felhasználó hanggal kezdődik, majd elindítja és leállítja a videót, miközben a hívás állapota folyamatban van `Lobby` .


#### <a name="possible-causes"></a>Lehetséges okok
A vizsgálat alatt.

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Időnként hosszú időt vesz igénybe a távoli résztvevő videók megjelenítése
Folyamatos csoportos hívás esetén _a felhasználó a_ videót küldi, majd a _B felhasználó_ csatlakozik a híváshoz. Előfordulhat, hogy a B felhasználó nem látja az A felhasználó videóját, vagy ha a felhasználó hosszú késleltetés után megkezdi A megjelenítést. Ezt olyan hálózati környezet okozhatja, amely további konfigurálást igényel. A hálózati beállításokkal kapcsolatos útmutatásért tekintse meg a [hálózati követelmények](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) dokumentációját.
