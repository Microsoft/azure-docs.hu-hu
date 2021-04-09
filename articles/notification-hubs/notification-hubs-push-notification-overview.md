---
title: Mi az az Azure Notification Hubs?
description: Ismerje meg, hogyan bővítheti lehetőségeit az Azure Notification Hubs használatával hozzáadott leküldéses értesítési képességekkel.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: ba5a329d12735fbddc86ff2e3725a1e7de6d9d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546465"
---
# <a name="what-is-azure-notification-hubs"></a>Mi az az Azure Notification Hubs?

Az Azure Notification Hubs egy könnyen használható és felskálázásos leküldéses motort biztosít, amely lehetővé teszi, hogy bármilyen háttérből (felhőből vagy helyszíniből) bármilyen platformra (iOS, Android, Windows stb.) küldjön értesítéseket. A Notification Hubs vállalati és végfelhasználói célokra is egyaránt nagyszerűen használható. Íme, néhány példaforgatókönyv:

- Legfrissebb híreket tartalmazó értesítések küldése felhasználók millióinak minimális késéssel.
- Helyalapú kuponok küldése az ez iránt érdeklődő felhasználói szegmenseknek.
- Eseményekkel kapcsolatos értesítések küldése felhasználóknak vagy csoportoknak média-/sport-/pénzügyi/játékalkalmazásokban.
- Promóciós tartalmak leküldése alkalmazásokba marketingcéllal az ügyfelek bevonása érdekében.
- Értesítse a felhasználókat a nagyvállalati eseményekről, például az új üzenetekről és a munkaelemekről.
- Kódok küldése többtényezős hitelesítéshez.

## <a name="what-are-push-notifications"></a>Mik azok a leküldéses értesítések?

A leküldéses értesítések az alkalmazások közötti kommunikációt jelentik, ahol a Mobile apps felhasználói értesítést kapnak bizonyos szükséges információkról, általában egy előugró ablakban vagy egy mobileszközön. A felhasználók általában az üzenet megtekintésére vagy elvetésére választanak; az előbbi megnyithatja az értesítést küldő mobil alkalmazást. Egyes értesítéseket a rendszer a háttérben az alkalmazás által feldolgozható és eldönthető színfalak mögött hajt végre.

A leküldéses értesítések fontos részei a végfelhasználói alkalmazásoknak az alkalmazással kapcsolatos marketingtevékenységek elősegítésében és az alkalmazáshasználat növelésében, illetve a vállalati alkalmazásoknak a naprakész üzleti információk kommunikálásában. Ez a legjobb alkalmazás-felhasználó kommunikáció, mivel ez energiahatékony a mobileszközök számára, rugalmasan használható az értesítések küldéséhez, és akkor érhető el, ha a megfelelő alkalmazások nem aktívak.

> [!NOTE]
> Az Azure Notification Hubs nem támogatja hivatalosan a Internet Protocol (VOIP) leküldéses értesítések küldését. [Ez a cikk azt ismerteti, hogyan használhatja a APNS VoIP-értesítéseket](voip-apns.md) az Azure Notification Hubs használatával.

Az alábbi témakörökben tekinthet meg további információt a leküldéses értesítésekkel kapcsolatban néhány népszerű platform esetén:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](/previous-versions/windows/apps/hh779725(v=win.10))

## <a name="how-do-push-notifications-work"></a>Hogyan működnek a leküldéses értesítések?

A leküldéses értesítéseket a rendszerek platformspecifikus, *Platform Notification System* (PNS) nevű infrastruktúrákon keresztül küldik el. Alapszintű leküldéses funkciókat biztosítanak egy adott leíróval rendelkező eszközre, és nem rendelkeznek közös felülettel. Ahhoz, hogy értesítést küldjön az összes ügyfélnek az alkalmazások Android-, iOS-és Windows-verzióiban, a fejlesztőnek külön kell működnie Apple Push Notification Service (APNS), a Firebase Cloud Messaging (FCM) és a Windows Notification Service (WNS) szolgáltatással.

A leküldés lényegében a következőképpen működik:

1. Egy alkalmazás szeretne értesítést kapni, így kapcsolatba lép azzal a PNS, amelyen az alkalmazás fut, és egyedi és ideiglenes leküldéses leírót kér. A leíró típusa a rendszertől függ (például a WNS URI-ket használ, míg a APNS tokeneket használ).
2. Az ügyfélalkalmazás ezt a leírót az alkalmazás-háttér vagy a szolgáltató tárolja.
3. Leküldéses értesítés küldéséhez az alkalmazás háttere a leíró használatával kapcsolatba lép a PNS egy adott ügyfélalkalmazás megcélzásához.
4. A PNS továbbküldi az értesítést a leíróban meghatározott eszközre.

![A leküldéses értesítés munkafolyamata](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>A leküldéses értesítések kihívásai

A PNS-ek hatékonyak. Számos teendőt hagynak ugyanakkor az alkalmazásfejlesztőre még a gyakori leküldéses értesítési forgatókönyvek, például a leküldéses értesítések szegmentált felhasználóknak való szórásos küldésének megvalósítása esetén is.

A leküldéses értesítések küldése olyan összetett infrastruktúrát igényel, amely nem kapcsolódik az alkalmazás fő üzleti logikához. Az infrastruktúra egyes kihívásai a következők:

- **Platformfüggőség**
  - A háttérrendszer összetett és nehezen kezelhető platformtól függő logikát igényel, hogy az értesítéseket különböző platformokon lévő eszközökre küldje el, mivel a PNSes nem egységesek.
- **Méretezés**
  - A PNS irányelvek alapján minden alkalmazás indításakor frissíteni kell az eszköz jogkivonatait. A háttérrendszer nagy mennyiségű forgalmat és adatbázis-hozzáférést biztosít a tokenek naprakészen tartásához. Ha az eszközök száma több száz, ezres vagy millióra nő, az infrastruktúra létrehozásának és fenntartásának díja nagy.
  - A legtöbb PNS nem támogatja a több eszközre küldött szórásos küldést. Egy millió eszközre való szórásos küldés egy millió, PNS-ekre küldött hívást eredményez. Ekkora forgalomra való méretezés minimális késéssel egyáltalán nem egyszerű feladat.
- **Útválasztás**
  - Bár a PNSes lehetővé teszik az üzenetek küldését az eszközökre, a legtöbb alkalmazás-értesítés a felhasználókra vagy a érdekcsoportokre irányul. A háttérrendszer-kezelőnek fenn kell tartania egy beállításjegyzéket az eszközök érdeklődési csoportok, felhasználók, tulajdonságok stb. szerinti hozzárendeléséhez. Ez a terhelés az alkalmazások piaci és karbantartási költségeihez járul hozzá.

## <a name="why-use-azure-notification-hubs"></a>Miért érdemes az Azure Notification Hubs szolgáltatást használni?

Notification Hubs kiküszöböli a leküldéses értesítések saját alkalmazás-háttérből való küldésével kapcsolatos összes bonyolultságot. A szolgáltatás többplatformos, kibővített leküldéses értesítési infrastruktúrája csökkenti a leküldéses értesítésekkel kapcsolatos kódolást, valamint egyszerűsíti a háttérrendszert. A Notification Hubs segítségével az eszközök csak a PNS-leíróknak egy központban való regisztrálásáért, a háttérrendszer pedig az üzenetek felhasználóknak vagy érdekcsoportoknak való elküldéséért felelős, ahogy az alábbi ábrán is látható:

![Értesítési központ ábrája](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs a használatra kész leküldéses motor a következő előnyökkel jár:

- **Platformfüggetlen**
  - Az összes jelentős leküldéses platform támogatása.
  - Közös felület, ahonnan leküldéses értesítéseket küldhet minden platformra platformspecifikus vagy platformfüggetlen formátumban platformspecifikus munka nélkül.
  - Eszközleírók kezelése egy helyen.
- **Háttérrendszertől független**
  - Felhőbeli vagy helyszíni.
  - .NET, Node.js, Java, Python stb.
- **Sokféle kézbesítési minta**
  - Közvetítés egy vagy több platformra: azonnal egyetlen API-hívással akár több millió eszközre is küldhet több platformon.
  - Leküldés eszközökre: Az értesítésekkel adott eszközöket célozhat meg.
  - Leküldés a felhasználónak: a címkék és a sablonok segítségével elérheti a felhasználók összes platformfüggetlen eszközét.
  - Leküldés a szegmensbe dinamikus címkékkel: a címkék funkció segítségével szegmentálhatja az eszközöket és továbbíthatja őket az igényeinek megfelelően, akár egy szegmensre, akár szegmensek kifejezésére küldi őket (például aktív, és nem új felhasználóként él Seattle-ben). Ahelyett, hogy a közzététel-előfizetésre korlátozódjon, az eszközök címkéit bárhol és bármikor frissítheti.
  - Honosított leküldés: a sablonok funkció a háttérbeli kód befolyásolása nélkül segít a honosítás megvalósításában.
  - Csendes leküldés: Engedélyezheti a leküldés és lekérés (push-to-pull) mintát, így az eszközökön csendes értesítések küldésével aktiválhatja bizonyos lekérések vagy műveletek végrehajtását.
  - Ütemezett leküldés: bármikor ütemezheti az értesítések küldését.
  - Közvetlen leküldés: Kihagyhatja az eszközök regisztrációját a Notification Hubs szolgáltatásban, és közvetlenül kötegelt értesítéseket küldhet le eszközleírók egy listájára.
  - Személyre szabott leküldéses: az eszköz leküldéses változói lehetővé teszik az eszközre jellemző személyre szabott leküldéses értesítések egyéni kulcs-érték párokkal
- **Részletes telemetria**
  - Az általános leküldés, az eszköz, a hiba és a művelet telemetria mind a Azure Portal, mind programozott módon elérhetők.
  - Az üzenetsor-telemetria nyomon követi a kezdeti kérések leküldéseit a Notification Hubs szolgáltatásnak a leküldés sikeres elküldése után.
  - Platform Notification System visszajelzése a PNSes származó összes visszajelzést közli a hibakeresésben.
- **Méretezhetőség**
  - Gyors üzenetek küldése több millió eszközre az újratervezés vagy az eszközök horizontális felskálázása nélkül.
- **Biztonság**
  - Közös hozzáférésű jogosultságkód (SAS) vagy összevont hitelesítés.

## <a name="next-steps"></a>Következő lépések

Az értesítési központ létrehozásának és használatának első lépéseinek végrehajtásához kövesse az [értesítések mobilalkalmazásokba való leküldésével](notification-hubs-android-push-notification-google-fcm-get-started.md) foglalkozó oktatóanyagot.

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: ./notification-hubs-push-notification-fixer.md
[Android]: ./notification-hubs-android-push-notification-google-gcm-get-started.md
[Windows Universal]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Windows Phone]: ./notification-hubs-windows-mobile-push-notifications-mpns.md
[Kindle]: ./notification-hubs-android-push-notification-google-fcm-get-started.md
[Xamarin.iOS]: ./xamarin-notification-hubs-ios-push-notification-apns-get-started.md
[Xamarin.Android]: ./xamarin-notification-hubs-push-notifications-android-gcm.md
[Microsoft.WindowsAzure.Messaging.NotificationHub]: /previous-versions/azure/reference/dn339221(v=azure.100)
[Microsoft.ServiceBus.Notifications]: /previous-versions/azure/
[App Service Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
