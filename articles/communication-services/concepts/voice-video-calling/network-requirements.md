---
title: A szervezet hálózatának előkészítése az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg az Azure kommunikációs szolgáltatásokkal kapcsolatos hang-és videohívás hálózati követelményeit
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 22bf8af89771e57621a0d8851ca31bb750b7cda2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108349"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Magas színvonalú média biztosítása az Azure kommunikációs szolgáltatásokban

Ez a dokumentum áttekintést nyújt azokról a tényezőkről és ajánlott eljárásokról, amelyeket figyelembe kell venni az Azure kommunikációs szolgáltatásokkal való magas színvonalú multimédiás kommunikáció kialakítása során.

## <a name="factors-that-affect-media-quality-and-reliability"></a>A média minőségét és megbízhatóságát befolyásoló tényezők

Számos különböző tényező járul hozzá az Azure kommunikációs szolgáltatások valós idejű adathordozó-(hang-, videó-és alkalmazás-megosztási) minőségéhez. Ezek közé tartozik a hálózati minőség, a sávszélesség, a tűzfal, a gazdagép és az eszközök konfigurációja.


### <a name="network-quality"></a>Hálózati minőség

A valós idejű adathordozók minőségét a mögöttes hálózati kapcsolat minősége jelentősen befolyásolja, de különösen a mennyisége:
* **Késés**. Itt az idő szükséges ahhoz, hogy egy IP-csomagot kapjon a (z) a hálózatról a B pontról. Ezt a hálózati propagálási késleltetést a két pont közötti fizikai távolság határozza meg, valamint a forgalom által átmenő eszközökön felmerülő további terhelés. A késés mérése egyirányú vagy oda-vissza (RTT) történik.
* A **csomagok elvesztése**. Egy adott időintervallumban elveszett csomagok százalékos aránya. A csomagok elvesztése közvetlenül befolyásolja a hangminőséget – a kisméretű, egyedi elveszett csomagoktól, amelyek szinte semmilyen hatással nincsenek, a teljes hang kivágását okozó visszaesési veszteségekre.
* **Inter-Packet megérkezési vibrálás vagy egyszerűen vibrálás**. Ez az egymást követő csomagok közötti késleltetés átlagos változása. Az Azure kommunikációs szolgáltatások a pufferelés során alkalmazkodnak a vibrálás bizonyos szintjeihez. Csak akkor jelenik meg, ha a vibrálás meghaladja azt a pufferelést, amelyet a résztvevő észlelt.

### <a name="network-bandwidth"></a>Hálózati sávszélesség

Győződjön meg arról, hogy a hálózat úgy van konfigurálva, hogy támogassa az egyidejű Azure kommunikációs szolgáltatások adathordozó-munkamenetei és más üzleti alkalmazások számára szükséges sávszélességet. A sávszélességbeli szűk keresztmetszetek végpontok közötti hálózati elérési útjának tesztelése kritikus fontosságú a multimédiás kommunikációs szolgáltatások megoldásának sikeres üzembe helyezéséhez.

A JavaScript SDK-k sávszélesség-követelményeit alább találja:

|Sávszélesség|Forgatókönyvek|
|:--|:--|
|40 kbps|Társ-társ hang hívása|
|500 kbps|Társ-társ hanghívás és a képernyő megosztása|
|500 kbps|Társközi minőségi videohívás 360p a 30fps címen|
|1,2 Mbps|Társközi HD-minőségű videó hívása HD 720p felbontásával a 30fps-on|
|500 kbps|Csoportos videohívás 360p a következő helyen: 30fps|
|1,2 Mbps|HD-csoportos videohívás a HD 720p felbontásával a 30fps| 

A natív Android-és iOS SDK-k sávszélesség-követelményeit alább találja:

|Sávszélesség|Forgatókönyvek|
|:--|:--|
|30 kbps|Társ-társ hang hívása |
|130 kbps|Társ-társ hanghívás és a képernyő megosztása|
|500 kbps|Társközi minőségi videohívás 360p a 30fps címen|
|1,2 Mbps|Társközi HD-minőségű videó hívása HD 720p felbontásával a 30fps-on|
|1,5 Mbps|Társközi HD minőségű videó hívása HD 1080p felbontással a 30fps-on |
|500kbps/1Mbps|Csoportos videohívás|
|1Mbps/2Mbps|HD csoportos videohívás (540p-videók 1080p képernyőn)|

### <a name="firewalls-configuration"></a>Tűzfal (ok) konfigurációja

Az Azure kommunikációs szolgáltatások kapcsolataihoz internetkapcsolat szükséges az adott portokhoz és IP-címekhez, hogy kiváló minőségű multimédiás élményeket lehessen elérni. Ezekhez a portokhoz és IP-címekhez való hozzáférés nélkül az Azure kommunikációs szolgáltatások továbbra is működhetnek. Az optimális élmény azonban az ajánlott portok és IP-címtartományok megnyitásakor van megadva.

| Kategória | IP-címtartományok vagy teljes tartománynév | Portok | 
| :-- | :-- | :-- |
| Média forgalma | [Az Azure nyilvános felhő IP-címeinek tartománya](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 – 3481, TCP-portok 443 |
| Jelzés, telemetria, regisztráció| *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Hálózati optimalizálás

A következő feladatok nem kötelezőek, és nem szükségesek az Azure kommunikációs szolgáltatások működéséhez. Ezzel az útmutatóval optimalizálhatja a hálózat és az Azure kommunikációs szolgáltatások teljesítményét, vagy ha tudja, hogy bizonyos hálózati korlátozások vannak érvényben.
Érdemes lehet további optimalizálást használni, ha:
* Az Azure kommunikációs szolgáltatások lassan futnak (lehet, hogy nincs elegendő sávszélessége)
* A hívások eldobásának megtartása (tűzfal vagy proxy-blokkolók miatt)
* A hívások statikus és kivágási, illetve hang-és hangjelzések (például a következő lehet: Jitter vagy csomagok elvesztése)

| Hálózati optimalizálási feladat | Részletek |
| :-- | :-- |
| A hálózat megtervezése | Ebben a dokumentációban megtalálhatja a hálózati hívások minimális követelményeit. Tekintse meg a [hálózat megtervezésére szolgáló csapatok példáját](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example) |
| Külső név feloldása | Ügyeljen arra, hogy az Azure Communications Services SDK-kat futtató összes számítógép fel tudja oldani a külső DNS-lekérdezéseket az Azure kommunikációs karbantartói által biztosított szolgáltatások felderítésére, valamint arról, hogy a tűzfalak nem akadályozzák a hozzáférést. Győződjön meg arról, hogy az SDK-k fel tudják oldani a címeket *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io  |
| Munkamenetek megőrzésének fenntartása | Győződjön meg arról, hogy a tűzfal nem módosítja a csatlakoztatott hálózati címfordítási (NAT) címeket vagy portokat az UDP-hez.
NAT-készlet méretének ellenőrzése | Ellenőrizze, hogy a hálózati címfordítási (NAT) készlet mérete szükséges-e a felhasználói kapcsolathoz. Ha több felhasználó és eszköz fér hozzá az Azure kommunikációs szolgáltatásokhoz [hálózati címfordítás (NAT) vagy címfordítás (Pat)](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365)használatával, győződjön meg arról, hogy az egyes nyilvánosan irányítható IP-címek mögött található eszközök nem lépik túl a támogatott számot. Győződjön meg arról, hogy megfelelő nyilvános IP-címek vannak hozzárendelve a NAT-készletekhez a portok kimerülésének megakadályozása érdekében. A portok kimerülése hozzájárul a belső felhasználók és eszközök számára, amelyek nem tudnak csatlakozni az Azure kommunikációs szolgáltatásaihoz |
| Behatolás-észlelési és-megelőzési útmutató | Ha a környezete olyan [behatolás-észlelési](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools) vagy-megelőzési rendszerrel (azonosítókkal/IP-ekkel) rendelkezik, amelyek a kimenő kapcsolatok további biztonsági rétegéhez vannak telepítve, engedélyezze az összes Azure kommunikációs szolgáltatás URL-címét |
| A felosztott alagút VPN konfigurálása | Javasoljuk, hogy adjon meg egy alternatív útvonalat a csapatok adatforgalmához, amely megkerüli a virtuális magánhálózat (VPN) megkerülését, amelyet gyakran a [felosztott alagút VPN-](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing)nek nevezünk. A felosztott bújtatás azt jelenti, hogy az Azure Communications Services forgalma nem halad át a VPN-en, hanem közvetlenül az Azure-ba kerül. A VPN megkerülése pozitív hatással lesz a média minőségére, és csökkenti a VPN-eszközök és a szervezet hálózata terhelését. A felosztott alagút VPN megvalósításához működjön együtt a VPN-szolgáltatóval. Egyéb okok, amiért javasoljuk a VPN megkerülését: <ul><li> A VPN-eket általában nem a valós idejű adathordozók támogatására tervezték vagy állították be.</li><li> A VPN-ek szintén nem támogatják az UDP-t (amely az Azure kommunikációs szolgáltatásokhoz szükséges)</li><li>A virtuális magánhálózatok további titkosítási réteget is bevezetnek, amely a már titkosított média-forgalomra épül.</li><li>Előfordulhat, hogy az Azure kommunikációs szolgáltatásokhoz való kapcsolódás nem bizonyulhat hatékonynak a VPN-eszközön keresztüli adatforgalom miatt.</li></ul>|
| QoS implementálása | [Használja a szolgáltatásminőség (QoS) szolgáltatást](https://docs.microsoft.com/microsoftteams/qos-in-teams) a csomagok rangsorolásának konfigurálásához. Ez javítja a hívások minőségét, és segítséget nyújt a hívások minőségének figyeléséhez és hibakereséséhez. A QoS-t egy felügyelt hálózat összes szegmensén kell megvalósítani. A QoS a nem várt hálózati események esetén is kockázatcsökkentő megoldást nyújt, még akkor is, ha a hálózat megfelelően kiosztotta a sávszélességet. A QoS-vel a hangforgalom rangsorolva van, így a váratlan események nem befolyásolják negatívan a minőséget. | 
| WiFi optimalizálása | A VPN-hez hasonlóan a WiFi hálózatok nem feltétlenül lettek kialakítva vagy konfigurálva a valós idejű adathordozók támogatásához. Az Azure kommunikációs szolgáltatásokat támogató Wi-Fi-hálózat megtervezése és optimalizálása fontos szempont a magas színvonalú üzembe helyezéshez. Vegye figyelembe a következő tényezőket: <ul><li>Implementálja a QoS-t vagy a Wi-Fi-t (WMM) annak biztosítására, hogy a média forgalmát megfelelően rangsorolja a WiFi-hálózatokon.</li><li>Megtervezheti és optimalizálhatja a WiFi-sávokat és a hozzáférési pont elhelyezését. A 2,4 GHz-es tartomány megfelelő élményt biztosít a hozzáférési pont elhelyezésének függvényében, de az adott tartományban működő más fogyasztói eszközök gyakran érintik a hozzáférési pontokat. Az 5 GHz-es tartomány nagyobb mértékben alkalmazkodik a valós idejű adathordozóhoz a sűrű tartománya miatt, de több hozzáférési pontot igényel, hogy elegendő lefedettséget kapjon. A végpontoknak is támogatniuk kell ezt a tartományt, és úgy kell konfigurálni, hogy ennek megfelelően használják ezeket a sávokat.</li><li>Ha kétsávos Wi-Fi-hálózatot használ, érdemes lehet a sávok irányítását implementálni. A Band Steering egy olyan technika, amelyet a WiFi-szállítók a kétsávos ügyfelek számára az 5 GHz-es tartomány használatára használnak.</li><li>Ha az azonos csatornához tartozó hozzáférési pontok túl közel vannak egymáshoz, akkor a jelek átfedésben lehetnek, és nem szándékosan versenyeznek, ami csökkentett teljesítményű felhasználói élményt eredményezhet. Győződjön meg arról, hogy az egymás melletti hozzáférési pontok olyan csatornákon vannak, amelyek nem fedik át egymást.</li></ul> Minden vezeték nélküli gyártó saját, a vezeték nélküli megoldás üzembe helyezésére vonatkozó javaslatokkal rendelkezik. Konkrét útmutatásért forduljon a WiFi-gyártóhoz.|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>Operációs rendszer és böngészők (JavaScript SDK-k esetén)

Az Azure kommunikációs szolgáltatások hang-és video-SDK-k bizonyos operációs rendszereket és böngészőket támogatnak.
Ismerje meg azokat az operációs rendszereket és böngészőket, amelyeket a hívó SDK-k támogatnak a [hívó fogalmi dokumentációjában](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

## <a name="next-steps"></a>Következő lépések

A következő dokumentumok érdekesek lehetnek:

- További információ a [könyvtárak meghívásáról](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)
- További információ az [ügyfél-kiszolgáló architektúráról](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture)
- További információ a [Call flow-topológiák](https://docs.microsoft.com/azure/communication-services/concepts/call-flows) használatáról
