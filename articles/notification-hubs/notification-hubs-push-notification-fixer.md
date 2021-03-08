---
title: Eldobott értesítések diagnosztizálása az Azure-ban Notification Hubs
description: Ismerje meg, hogyan diagnosztizálhatja az eldobott értesítésekkel kapcsolatos gyakori problémákat az Azure Notification Hubsban.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 787cf922fcee0ee613fc0874a490830da9adf38a
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455025"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Eldobott értesítések diagnosztizálása az Azure-ban Notification Hubs

Az Azure Notification Hubs kapcsolatos gyakori kérdés, hogy miként lehet elhárítani az alkalmazások értesítéseinek nem jelennek meg az eszközökön. Az ügyfelek szeretnék tudni, hogy hol és miért dobták el az értesítéseket, és hogyan lehet elhárítani a problémát. Ez a cikk azt mutatja be, hogy az értesítések miért kerülhetnek eldobásra vagy nem fogadhatók az eszközökön. Azt is ismerteti, hogyan határozhatja meg a kiváltó okot.

Fontos elsőként megismerni, hogyan Notification Hubs leküldi az értesítéseket egy eszközre.

![Notification Hubs architektúra][0]

Egy tipikus küldési értesítési folyamat során az *alkalmazás* visszaküldi az üzenetet a Notification Hubs. Notification Hubs az összes regisztrációt feldolgozza. A célkitűzések meghatározásához figyelembe veszi a konfigurált címkéket és a címkézési kifejezéseket. A célok olyan regisztrációk, amelyeknek a leküldéses értesítést kell kapniuk. Ezek a regisztrációk a támogatott platformok bármelyikét kiterjedhetik: Android, Baidu (androidos eszközök Kínában), Fire OS (Amazon) iOS, Windows és Windows Phone-telefon.

A kitűzött célokkal Notification Hubs leküldi az értesítéseket a *leküldéses értesítési szolgáltatásnak* az eszköz platformján. Ilyenek például az Apple push Notification szolgáltatás (APNs) az iOS és a macOS rendszerekhez, valamint az Firebase Cloud Messaging (FCM) Android-eszközökhöz. Notification Hubs leküldi az értesítéseket több köteg regisztrációja között. A rendszer a megfelelő leküldéses értesítési szolgáltatással hitelesíti a Azure Portal megadott hitelesítő adatai alapján az **értesítési központ konfigurálása** területen. A leküldéses értesítési szolgáltatás ezután továbbítja az értesítéseket a megfelelő *ügyféleszközök* számára.

Az értesítések kézbesítésének utolsó szakasza a platform leküldéses értesítési szolgáltatása és az eszköz között található. Az értesítés kézbesítése sikertelen lehet a leküldéses értesítési folyamat négy fázisában (ügyfél, alkalmazás-háttér, Notification Hubs és a platform leküldéses értesítési szolgáltatása). További információ a Notification Hubs architektúrával kapcsolatban: [Notification Hubs Overview (áttekintés]).

A kezdeti tesztelési/előkészítési fázisban az értesítések kézbesítésének elmulasztása történhet. Az eldobott értesítések ebben a fázisban konfigurációs problémát jelezhetnek. Ha az értesítések kézbesítésének sikertelensége az éles környezetben történik, előfordulhat, hogy egy vagy több értesítés is el lesz dobva. Ebben az esetben egy mélyebb alkalmazás-vagy üzenetküldési minta probléma van megadva.

A következő szakasz olyan forgatókönyveket mutat be, amelyekben a rendszer a gyakoritól a ritkaig terjedő értesítéseket is elhagyhatja.

## <a name="notification-hubs-misconfiguration"></a>Helytelen konfiguráció Notification Hubs

Ahhoz, hogy az értesítéseket a megfelelő leküldéses értesítési szolgáltatáshoz küldje, Notification Hubs az alkalmazás kontextusában hitelesítenie kell magát. Létre kell hoznia egy fejlesztői fiókot a cél platform értesítési szolgáltatásával (Microsoft, Apple, Google stb.). Ezt követően regisztrálnia kell az alkalmazást az operációs rendszerrel, ahol a cél PNS való együttműködéshez használt jogkivonatot vagy kulcsot kap.

Hozzá kell adnia a platform hitelesítő adatait a Azure Portalhoz. Ha egyetlen értesítés sem éri el az eszközt, az első lépés annak biztosítása, hogy a helyes hitelesítő adatok a Notification Hubsban legyenek konfigurálva. A hitelesítő adatoknak meg kell egyezniük a platform-specifikus fejlesztői fiókkal létrehozott alkalmazással.

A folyamat elvégzéséhez szükséges részletes utasításokért tekintse meg az [Azure Notification Hubs első lépéseivel]foglalkozó témakört.

Íme néhány gyakori helytelen konfiguráció a következő kereséshez:

### <a name="notification-hub-name-location"></a>Értesítési központ nevének helye

Győződjön meg arról, hogy az értesítési központ neve (az elírások nélkül) ugyanaz, mint a következő helyen:

* Az ügyfél regisztrációja
* Honnan küld értesítést a háttérből
* A leküldéses értesítési szolgáltatás hitelesítő adatainak konfigurálása

Győződjön meg arról, hogy a megfelelő közös hozzáférésű aláírás-konfigurációs karakterláncokat használja az ügyfélen és az alkalmazás hátterét. Általában a **DefaultListenSharedAccessSignature** -t kell használnia az ügyfélen és a **DefaultFullSharedAccessSignature** az alkalmazás háttér-végpontján. Ez engedélyt ad az értesítések küldésére Notification Hubs.

### <a name="apn-configuration"></a>APN-konfiguráció

Két különböző hubokat kell fenntartania: az egyiket a termeléshez, egy másikat pedig a teszteléshez. A homokozóban használt tanúsítványt egy külön hubhoz kell feltöltenie, mint az éles környezetben használt tanúsítvány/hub. Ne próbálkozzon különböző típusú tanúsítványok feltöltésével ugyanahhoz a hubhoz. Ez az értesítési hibákat okoz.

Ha a különböző típusú tanúsítványokat véletlenül ugyanarra a központba tölti fel, akkor törölnie kell a hubot, és új hubhoz kell kezdenie. Ha valamilyen okból nem tudja törölni a hubot, legalább törölnie kell az összes meglévő regisztrációt a központból.

### <a name="fcm-configuration"></a>FCM-konfiguráció

1. Győződjön meg arról, hogy a Firebase-ből beszerzett *kiszolgáló kulcsa* megegyezik a Azure Portalban regisztrált kiszolgáló kulcsával.

   ![Firebase-kiszolgáló kulcsa][3]

2. Győződjön meg arról, hogy konfigurálta a **projekt azonosítóját** az ügyfélen. A **projekt azonosítójának** értékét a Firebase irányítópulton szerezheti be.

   ![Firebase projekt azonosítója][1]

## <a name="application-issues"></a>Alkalmazásokkal kapcsolatos problémák

### <a name="tags-and-tag-expressions"></a>Címkék és címkézési kifejezések

Ha címkéket vagy címkéző kifejezéseket használ a célközönség szegmentálásához, akkor előfordulhat, hogy az értesítés küldésekor nem található cél. Ez a hiba a küldési hívásban megadott címkék vagy címkézési kifejezések alapján történik.

Tekintse át a regisztrációkat, és győződjön meg arról, hogy a címkék egyeznek az értesítések elküldésekor. Ezután ellenőrizze, hogy csak az adott regisztrációval rendelkező ügyfelek értesítési nyugtája van-e.

Tegyük fel például, hogy az összes regisztrációja Notification Hubs használja a "Politics" címkét. Ha ezt követően értesítést küld a "sport" címkével, az értesítés nem lesz elküldve egyetlen eszközre sem. Az összetett eset olyan címkézési kifejezéseket tartalmazhat, amelyekben az "A címkével" *vagy* a "b" címkével regisztrálva van, de a "b && címke" címkét célozta meg. A cikk későbbi részében a saját diagnosztikai tippek című szakasz bemutatja, hogyan tekintheti át a regisztrációkat és azok címkéit.

### <a name="template-issues"></a>Sablonokkal kapcsolatos problémák

Ha sablonokat használ, ügyeljen arra, hogy kövesse a [sablonok]című témakör útmutatásait.

### <a name="invalid-registrations"></a>Érvénytelen regisztrációk

Ha az értesítési központ helyesen lett konfigurálva, és a címkék vagy a címkézési kifejezések helyesen lettek alkalmazva, akkor a rendszer érvényes célokat talál. Az értesítéseket el kell juttatni ezekre a célokba. Notification Hubs ezután több feldolgozási köteget indít el párhuzamosan. Az egyes kötegek üzeneteket küldenek egy regisztrációs csoportba.

> [!NOTE]
> Mivel Notification Hubs a kötegeket párhuzamosan dolgozza fel, az értesítések kézbesítési sorrendje nem garantált.

Notification Hubs egy "on-Once" üzenet-kézbesítési modellre van optimalizálva. A rendszer a lemásolást kísérelte meg, így egyetlen értesítés sem érkezik többször az eszközre. A rendszer ellenőrzi, hogy a rendszer csak egy üzenetet küld-e a leküldéses értesítési szolgáltatásnak a küldés előtt.

A rendszer minden köteget elküld a leküldéses értesítési szolgáltatásnak, amely viszont elfogadja és érvényesíti a regisztrációkat. A folyamat során lehetséges, hogy a leküldéses értesítési szolgáltatás hibát észlel egy köteg egy vagy több regisztrációjában. A leküldéses értesítési szolgáltatás ezután hibát jelez Notification Hubs, és a folyamat leáll. A leküldéses értesítési szolgáltatás teljes mértékben eldobja a köteget. Ez különösen igaz a APNs, amely TCP stream protokollt használ.

Ebben az esetben a rendszer eltávolítja a hibás regisztrációt az adatbázisból. Ezután újrapróbálkozunk az értesítés kézbesítésével a kötegben lévő többi eszközön.

Ha többet szeretne megtudni a regisztráció sikertelen kézbesítési kísérletéről, használja a Notification Hubs REST API-kat [telemetria: értesítési üzenet](/rest/api/notificationhubs/get-notification-message-telemetry) küldése a telemetria és a [PNS-visszajelzésről](/previous-versions/azure/reference/mt705560(v=azure.100)). A mintakód esetében tekintse meg a [Rest-példa küldése](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)című részt.

## <a name="push-notification-service-issues"></a>Leküldéses értesítési szolgáltatással kapcsolatos problémák

Miután a leküldéses értesítési szolgáltatás megkapja az értesítést, továbbítja az értesítést az eszköznek. Ezen a ponton a Notification Hubs nem szabályozhatja az értesítés kézbesítését az eszközre.

Mivel a platform Notification Services robusztus, az értesítések néhány másodpercen belül elérik az eszközöket. Ha a leküldéses értesítési szolgáltatás szabályozza a szabályozást, a Notification Hubs exponenciális visszalépési stratégiát alkalmaz. Ha a leküldéses értesítési szolgáltatás 30 percig nem érhető el, az üzenetek végleges lejáratát és eldobását is lehetővé teszi.

Ha egy leküldéses értesítési szolgáltatás megpróbál kézbesíteni egy értesítést, de az eszköz offline állapotban van, az értesítést a leküldéses értesítési szolgáltatás tárolja. A rendszer csak korlátozott ideig tárolja. Az értesítés kézbesítése az eszközre történik, ha az eszköz elérhetővé válik.

Minden alkalmazás csak egy közelmúltbeli értesítést tárol. Ha a rendszer több értesítést küld, amikor egy eszköz offline állapotban van, minden új értesítés miatt a rendszer elveti az utolsót. Csak a legújabb értesítéseket kell a APNs-ben és az *coalescing* -ben az FCM-ben *összeomlani* . (Az FCM egy összeomló kulcsot használ.) Ha az eszköz hosszú ideje offline állapotban marad, a rendszer elveti az eszközön tárolt értesítéseket. További információ: a [APNs áttekintése] és [az FCM-üzenetek].

A Notification Hubs használatával egy coalescing-kulcsot egy HTTP-fejlécen keresztül adhat át az általános SendNotification API-val. A .NET SDK esetében például a következőt használja: `SendNotificationAsync` . A SendNotification API emellett a HTTP-fejléceket is végrehajtja, amelyeket a rendszer a megfelelő leküldéses értesítési szolgáltatásnak ad át.

## <a name="self-diagnosis-tips"></a>Öndiagnosztikai tippek

Itt láthatók az eldobott értesítések kiváltó okának diagnosztizálására szolgáló elérési utak Notification Hubs.

### <a name="verify-credentials"></a>Hitelesítő adatok ellenőrzése

#### <a name="push-notification-service-developer-portal"></a>Leküldéses értesítési szolgáltatás – fejlesztői portál

Ellenőrizze a hitelesítő adatokat a megfelelő leküldéses értesítési szolgáltatás fejlesztői portálján (APNs, FCM, Windows Notification Service stb.). További információ: [oktatóanyag: értesítések küldése univerzális Windows-platform alkalmazásoknak az Azure Notification Hubs használatával](./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

#### <a name="azure-portal"></a>Azure Portal

A leküldéses értesítési szolgáltatás fejlesztői portálján beszerzett hitelesítő adatok áttekintéséhez és egyeztetéséhez lépjen a Azure Portal **hozzáférési szabályzatok** lapjára.

![Azure Portal hozzáférési szabályzatok][4]

### <a name="verify-registrations"></a>Regisztrációk ellenőrzése

#### <a name="visual-studio"></a>Visual Studio

A Visual Studióban a Server Explorerben csatlakozhat az Azure-hoz, így több Azure-szolgáltatást is megtekinthet és kezelhet, beleértve a Notification Hubst is. Ez a parancsikon elsősorban fejlesztési és tesztelési környezetben hasznos.

![A Visual Studio Server Explorer][9]

![Server Explorer (Kiszolgálókezelő)](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

A hub összes regisztrációját megtekintheti és kezelheti. A regisztrációk a platform, a natív vagy a sablon regisztrálása, a leküldéses értesítési szolgáltatás azonosítója, a regisztrációs azonosító és a lejárati dátum szerint kategorizálva is megadhatók. Ezen az oldalon szerkesztheti a regisztrációt is. Ez különösen hasznos a címkék szerkesztéséhez.

Kattintson a jobb gombbal az értesítési központra a **Server Explorerben**, és válassza a **Diagnosztizálás** lehetőséget. 

![Visual Studio Server Explorer: Diagnosztizálás menü](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

A következő oldal jelenik meg:

![Visual Studio: diagnosztika lap](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Váltson az **eszköz regisztrációi** lapra:

![Visual Studio: eszközök regisztrációja](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

A teszt **küldése** lapot használhatja a teszt értesítési üzenet elküldéséhez:

![Visual Studio: küldési teszt](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> A Visual Studióval csak a fejlesztés/tesztelés során és korlátozott számú regisztrációval szerkesztheti a regisztrációkat. Ha tömegesen kell szerkesztenie a regisztrációkat, érdemes lehet a regisztrációk [tömeges exportálása és módosítása](/previous-versions/azure/azure-services/dn790624(v=azure.100))című témakörben ismertetett exportálási és importálási funkciókat használni.

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Számos ügyfél [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) használatával tekintheti meg és kezelheti az értesítési hubokat. Service Bus Explorer egy nyílt forráskódú projekt. 

### <a name="verify-message-notifications"></a>Üzenetek értesítéseinek ellenőrzése

#### <a name="azure-portal"></a>Azure Portal

Ha teszt-értesítést szeretne küldeni az ügyfeleknek anélkül, hogy a szolgáltatást vissza kellene állítani, és nem fut, a **támogatás + hibaelhárítás** területen válassza a **küldési teszt** lehetőséget.

![Küldési funkció tesztelése az Azure-ban][7]

#### <a name="visual-studio"></a>Visual Studio

A Visual studióból is küldhet tesztelési értesítéseket.

![Küldési funkció tesztelése a Visual Studióban][10]

A Notification Hubs és a Visual Studio Server Explorer használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az értesítési központok eszköz-regisztrációjának megtekintése](/previous-versions/windows/apps/dn792122(v=win.10))
* [Deep Dive: Visual Studio 2013 Update 2 RC és Azure SDK 2,3]
* [A Visual Studio 2013 Update 3 és az Azure SDK 2,4 kiadásának bejelentése]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Sikertelen értesítések hibakeresése és az értesítés eredményének áttekintése

#### <a name="enabletestsend-property"></a>EnableTestSend tulajdonság

Ha Notification Hubson keresztül küld értesítést, az értesítés kezdetben várólistára kerül. Notification Hubs meghatározza a helyes célokat, majd elküldi az értesítést a leküldéses értesítési szolgáltatásnak. Ha a REST API vagy az ügyféloldali SDK-kat használja, a küldési hívás visszaküldése csak azt jelenti, hogy az üzenet a Notification Hubs. Nem nyújt betekintést arról, hogy mi történt, amikor Notification Hubs Végül elküldte az értesítést a leküldéses értesítési szolgáltatásnak.

Ha az értesítés nem érkezik meg az ügyfél-eszközre, akkor hiba történt, amikor Notification Hubs megpróbálta kézbesíteni a leküldéses értesítési szolgáltatásnak. Előfordulhat például, hogy a hasznos adatok mérete túllépi a leküldéses értesítési szolgáltatás által engedélyezett maximális értéket, vagy a Notification Hubs konfigurált hitelesítő adatok lehetnek érvénytelenek.

A leküldéses értesítési szolgáltatás hibáinak megismeréséhez használhatja a [EnableTestSend] tulajdonságot. Ez a tulajdonság automatikusan engedélyezve lesz, amikor tesztüzenet küld a portálról vagy a Visual Studio-ügyfélről. Ennek a tulajdonságnak a használatával részletes hibakeresési információkat és API-kat is megtekintheti. Jelenleg a .NET SDK-ban is használható. A rendszer az összes ügyfél SDK-nak végül hozzáadja.

Ha a REST-hívással szeretné használni a `EnableTestSend` tulajdonságot, a küldési hívás végéhez fűzze hozzá a *test* nevű lekérdezési karakterlánc paramétert. Például:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK – példa

Íme egy példa arra, hogyan lehet a .NET SDK-t natív előugró (Toast) értesítések küldésére használni:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

A végrehajtás végén `result.State` egyszerűen állapotú `Enqueued` . Az eredmények nem biztosítanak betekintést a leküldéses értesítésbe történt információkba.

A következő lépésben használhatja a `EnableTestSend` logikai tulajdonságot. Használja a `EnableTestSend` tulajdonságot az inicializálás során `NotificationHubClient` , hogy részletes állapotot kapjon az értesítés elküldésekor előforduló leküldéses értesítési szolgáltatás hibáiról. A küldési hívás további időt vesz igénybe, mert először Notification Hubs kell küldenie az értesítést a leküldéses értesítési szolgáltatásnak.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Példakimenet

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Ez az üzenet azt jelzi, hogy a Notification Hubsban konfigurált hitelesítő adatok érvénytelenek, vagy hogy probléma van a központban található regisztrációval. Törölje a regisztrációt, és hagyja, hogy az ügyfél újból létrehozza a regisztrációt az üzenet elküldése előtt.

> [!NOTE]
> A tulajdonság használata `EnableTestSend` nagy mértékben szabályozott. Ezt a lehetőséget csak fejlesztési és tesztelési környezetben, valamint korlátozott számú regisztrációval használhatja. A hibakeresési értesítések csak 10 eszközre lesznek elküldve. A hibakeresési műveletek másodpercenként 10 percenként is megadhatók.

### <a name="review-telemetry"></a>Telemetria áttekintése

#### <a name="azure-portal"></a>Azure Portal

A portálon gyors áttekintést kaphat az értesítési központban található összes tevékenységről.

1. Az **Áttekintés** lapon a regisztrációk, értesítések és hibák összesített nézetét láthatja a platformon.

   ![Notification Hubs áttekintése irányítópult][5]

2. A **figyelés** lapon számos más platform-specifikus mérőszámot is hozzáadhat mélyebb kereséshez. Megtekintheti azokat a hibákat, amelyeket a rendszer akkor ad vissza, amikor Notification Hubs megpróbálja elküldeni az értesítést a leküldéses értesítési szolgáltatásnak.

   ![Azure Portal tevékenység naplója][6]

3. Először tekintse át a **Bejövő üzenetek**, a **regisztrációs műveletek** és a **sikeres értesítések** áttekintését. Ezután nyissa meg a platform lapon a leküldéses értesítési szolgáltatásra jellemző hibák áttekintését.

4. Ha az értesítési központ hitelesítési beállításai helytelenek, megjelenik az üzenet **PNS hitelesítési hiba** . A leküldéses értesítési szolgáltatás hitelesítő adatait érdemes megnézni.

#### <a name="programmatic-access"></a>Szoftveres hozzáférés

További információ a programozott hozzáférésről: [programozott hozzáférés](/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Számos telemetria kapcsolatos funkció, például a regisztrációk exportálása és importálása, valamint a telemetria API-kon keresztüli hozzáférése csak a standard szintű szolgáltatási szinten érhető el. Ha az ingyenes vagy alapszintű szolgáltatási csomagból próbálja meg használni ezeket a funkciókat, a rendszer kivételt jelző üzenetet küld, ha az SDK-t használja. Ha közvetlenül a REST API-kkal használja a szolgáltatásokat, HTTP 403 (tiltott) hibaüzenetet kap.
>
> A telemetria-hez kapcsolódó funkciók használatához először győződjön meg arról, hogy a standard szintű szolgáltatási szintet használó Azure Portal.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs áttekintése]: notification-hubs-push-notification-overview.md
[Ismerkedés az Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Sablonok]: /previous-versions/azure/azure-services/dn530748(v=azure.100)
[A APNs áttekintése]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Az FCM-üzenetek]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: /previous-versions/azure/azure-services/dn790624(v=azure.100)
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: /previous-versions/windows/apps/dn792122(v=win.10)
[Deep Dive: Visual Studio 2013 Update 2 RC és Azure SDK 2,3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[A Visual Studio 2013 Update 3 és az Azure SDK 2,4 kiadásának bejelentése]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: /dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend
[Programmatic telemetry access]: /previous-versions/azure/azure-services/dn458823(v=azure.100)
