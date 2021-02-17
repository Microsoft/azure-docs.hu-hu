---
title: Azure Notification Hubs – gyakori kérdések (GYIK) | Microsoft Docs
description: Az Azure Notification Hubs-megoldások tervezésével és megvalósításával kapcsolatos gyakori kérdések.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
keywords: leküldéses értesítés, leküldéses értesítések, iOS leküldéses értesítések, Android leküldéses értesítések, iOS leküldés, Android leküldése
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: e34fbdca51e7680a80c768e49bae891cb56dfa9d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546448"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Leküldéses értesítések az Azure Notification Hubs: gyakori kérdések

## <a name="general"></a>Általános

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Mi a Notification Hubs erőforrás-szerkezete?

Az Azure Notification Hubs két erőforrás-szinttel rendelkezik: hubok és névterek. A hub egyetlen leküldéses erőforrás, amely egy alkalmazás platformfüggetlen leküldéses adatait képes tárolni. A névtér egy adott régióban található hubok gyűjteménye. Az ajánlott leképezés egyetlen alkalmazással egyezik egy névtérrel. A névtéren belül az éles környezetben működő üzemi központ, amely a tesztelési alkalmazással együtt használható, és így tovább.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Mi a Notification Hubs díjszabási modellje?

A legfrissebb díjszabás a [Notification Hubs díjszabási] oldalán található. Notification Hubs számlázása a névtér szintjén történik. (Névtér definíciója: "mi az az erőforrás szerkezete Notification Hubs?") A Notification Hubs három szintet kínál:

* **Ingyenes**: Ez a szinten jó kiindulási pont a leküldéses képességek feltárására. Éles alkalmazások esetében nem ajánlott. A 500-es és az 1 000 000-es leküldéses szolgáltatás havonta, a szolgáltatói szerződés (SLA) garanciája nélkül is elérhető.
* **Alapszintű**: Ez a szint (vagy a standard szint) kisebb üzemi alkalmazások esetében ajánlott. Az 200 000-es és a 10 000 000-es leküldéses adategységek havonta, alapkonfigurációként jelennek meg.
* **Standard**: Ez a szint közepes és nagy üzemi alkalmazások esetében ajánlott. Az 10 000 000-es és a 10 000 000-es leküldéses adategységek havonta, alapkonfigurációként jelennek meg. Gazdag telemetria tartalmaz (a leküldéses állapottal kapcsolatos további információkat).

Standard szintű funkciók:

* **Rich telemetria**: a leküldéses kérelmek nyomon követéséhez és a hibakereséshez platform Notification System visszajelzések Notification Hubséhez használhatja az telemetria-t.
* **Több-bérlős**: platform Notification System hitelesítő adatokkal használható a névtér szintjén. Ez a beállítás lehetővé teszi, hogy könnyedén Ossza szét a bérlőket ugyanazon a névtéren belüli hubokba.
* **Ütemezett** leküldés: az értesítések bármikor ütemezhetők.
* **Tömeges műveletek**: engedélyezi a regisztrációk exportálási/importálási funkcióit a [regisztrációk exportálási/importálási] dokumentumában leírtak szerint.

### <a name="what-is-the-notification-hubs-sla"></a>Mi a Notification Hubs SLA?

Az alapszintű és a standard szintű Notification Hubs-csomagok esetében a megfelelően konfigurált alkalmazások leküldéses értesítéseket küldhetnek, vagy az idő legalább 99,9%-ában elvégezhetik a regisztrációs kezelési műveleteket. Ha többet szeretne megtudni az SLA-ról, látogasson el a [Notification HUBS SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) oldalára.

> [!NOTE]
> Mivel a leküldéses értesítések a külső platformtól származó értesítési rendszerektől függenek, például az Apple leküldéses értesítési szolgáltatásának (APNs) és a Google Firebase Cloud Messaging (FCM) szolgáltatástól, az üzenetek kézbesítésére nem biztosítunk SLA-t. Miután Notification Hubs elküldte a batchs-t a platform értesítési rendszereinek (garantált SLA), a platform értesítési rendszereinek feladata a leküldések kézbesítése (SLA-val nem garantált).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hogyan a hubot vagy a névteret egy másik szintre?

Nyissa meg a **[Azure Portal]**  >  **Notification Hubs névterek** vagy **Notification Hubs**. Válassza ki a frissíteni kívánt erőforrást, és nyissa meg a **díjszabási szintet**. Vegye figyelembe a következő követelményeket:

* A frissített díjszabási csomag a névtérben lévő *összes* olyan hubhoz vonatkozik, amelyen dolgozik.
* Ha az eszközök száma meghaladja a küszöbértéket, akkor a visszalépés előtt törölnie kell az eszközöket.

## <a name="design-and-development"></a>Tervezés és fejlesztés

### <a name="which-server-side-platforms-do-you-support"></a>Mely kiszolgálóoldali platformokat támogatja?

A kiszolgálói SDK-k .NET-, Java-, Node.js-, PHP-és Python-kiszolgálókon érhetők el. Notification Hubs API-k REST-felületeken alapulnak, így közvetlenül a REST API-kkal dolgozhat, ha különböző platformokat használ, vagy nem szeretne további függőséget használni. További információért látogasson el a [Notification HUBS REST API] -k oldalára.

### <a name="which-client-platforms-do-you-support"></a>Mely ügyfélszoftvereket támogatja?

A leküldéses értesítések [iOS](ios-sdk-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone-telefon](notification-hubs-windows-mobile-push-notifications-mpns.md), [Android China (Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin [iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) és [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)rendszerű eszközökön, illetve [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari)rendszeren támogatottak. További információkért tekintse meg a [Notification Hubs első lépések oktatóanyagok](ios-sdk-get-started.md) lapot.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Támogatja a szöveges üzenetet, az e-maileket vagy a webes értesítéseket?

Notification Hubs értesítéseket küld a Mobile Apps szolgáltatást futtató eszközökre. Nem biztosít e-mail-vagy SMS-képességeket. Notification Hubs emellett nem biztosít böngészőbeli leküldéses értesítés kézbesítési szolgáltatást a dobozból. Az ügyfelek a támogatott kiszolgálóoldali platformokra épülő Signaler használatával tudják megvalósítani ezt a szolgáltatást. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Hány eszközt támogatok, ha leküldéses értesítéseket küldek Notification Hubson keresztül?

A támogatott eszközök számával kapcsolatos részletekért tekintse meg a [Notification Hubs díjszabási] oldalát.

Ha több mint 10 000 000 regisztrált eszköz támogatására van szüksége, az eszközöket több névtér között kell particionálnia.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hány leküldéses Értesítés küldhető el?

A kiválasztott szinttől függően az Azure Notification Hubs a rendszeren keresztül áramló értesítések száma alapján automatikusan méretezi a méretezést.

> [!NOTE]
> A teljes használati díj az elküldött leküldéses értesítések száma alapján növelhető. Győződjön meg arról, hogy ismeri a [Notification Hubs díjszabási] oldalon ismertetett szintű korlátokat.

Ügyfeleink a Notification Hubs használatával naponta több millió leküldéses értesítést küldhetnek. A leküldéses értesítések elérésének méretezéséhez nem szükséges semmilyen speciális művelet, feltéve, hogy az Azure Notification Hubs-t használja.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Mennyi ideig tart az elküldött leküldéses értesítések elérése az eszközön?

Normál használat esetén, ahol a bejövő terhelés konzisztens, sőt, az Azure Notification Hubs képes legalább *1 000 000 leküldéses értesítés elküldésére egy percet*. Ez az arány a címkék számától, a bejövő küldési adatok természetétől és más külső tényezőktől függően változhat.

A becsült szállítási idő alatt a szolgáltatás kiszámítja a célokat a platformon, és az üzeneteket a leküldéses értesítési szolgáltatásba (PNS) irányítja a regisztrált címkék vagy címkézési kifejezések alapján. A PNS feladata, hogy értesítéseket küldjön az eszközre.

A PNS nem garantálja az értesítések kézbesítésére vonatkozó SLA-t. A legtöbb leküldéses értesítés azonban néhány percen belül (általában 10 percen belül) érkezik a megcélzott eszközökre, amikor a rendszer elküldi a Notification Hubs. Néhány értesítés több időt is igénybe vehet.

> [!NOTE]
> Az Azure Notification Hubs olyan szabályzattal rendelkezik, amely 30 percen belül elvonja a PNS nem továbbított leküldéses értesítéseket. Ez a késleltetés több okból is előfordulhat, de általában azért, mert a PNS az alkalmazás szabályozása.

### <a name="is-there-any-latency-guarantee"></a>Van késési garancia?

A leküldéses értesítések jellegéből adódóan (amelyeket külső, platform-specifikus PNS továbbítanak), nincs késési garancia. A leküldéses értesítések többsége általában néhány percen belül elérhető.

### <a name="where-does-azure-notification-hubs-store-data"></a>Hol tárolja az Azure Notification Hubs az adattárolást?

Az Azure Notification Hubs az ügyfél regisztrációs szolgáltatásait az ügyfél által kiválasztott régióban tárolja. A Notification Hubs metaadatokat biztosít a vész-helyreállítási lefedettségről (a Notification Hubs neve, a kapcsolatok karakterlánca és egyéb kritikus információk). Az összes régió esetében, kivéve Dél-és Délkelet-Ázsiában, a metaadatok biztonsági mentése egy másik régióban (általában az Azure párosított régióban) található. A Dél-és Délkelet-ázsiai régiókban a biztonsági mentések ugyanabban a régióban vannak tárolva, hogy megfeleljenek az ezekben a régiókban tárolt adatrezidensi követelményeknek.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Mit kell figyelembe venni a névtereket és az értesítési központokat tartalmazó megoldások tervezésekor?

#### <a name="mobile-appenvironment"></a>Mobile App/Environment

* A Mobile apps egy értesítési központjának használata környezet alapján.
* Több-bérlős forgatókönyv esetén minden bérlőnek külön hubhoz kell tartoznia.
* Soha ne ossza meg ugyanazt az értesítési központot üzemi és tesztelési környezetekben. Ez a gyakorlat problémákhoz vezethet az értesítések küldésekor. (Az Apple homokozó-és éles leküldéses végpontokat kínál, amelyek mindegyike külön hitelesítő adatokkal rendelkezik.)
* Alapértelmezés szerint a Visual Studióban a Azure Portal vagy az Azure-beli integrált összetevő segítségével küldhet teszt értesítéseket a regisztrált eszközökre. A küszöbérték 10 eszközre van beállítva, amely véletlenszerűen van kiválasztva a regisztrációs készletből.

> [!NOTE]
> Ha a hub eredetileg Apple homokozó-tanúsítvánnyal lett konfigurálva, majd újrakonfigurálták az Apple éles tanúsítvány használatára, az eredeti eszköz jogkivonatai érvénytelenek. A tokenek érvénytelenek, mert a leküldések sikertelenek lesznek. Válassza el az üzemi és tesztelési környezeteket, és használjon különböző hubokat különböző környezetekhez.

#### <a name="pns-credentials"></a>PNS hitelesítő adatai

Ha egy platform fejlesztői portálján (például az Apple-ben vagy a Google-on) van regisztrálva egy alkalmazás, akkor a rendszer az alkalmazás azonosítóját és biztonsági jogkivonatait is elküldi. Az alkalmazás-háttérrendszer ezeket a jogkivonatokat biztosítja a platform PNS, így a leküldéses értesítések küldhetők az eszközökre. A biztonsági jogkivonatok tanúsítványok (például Apple iOS vagy Windows Phone-telefon) vagy biztonsági kulcsok (például Google Android vagy Windows) formájában lehetnek. Ezeket az értesítési hubokon kell konfigurálni. A konfiguráció általában az értesítési központ szintjén történik, de a névtér szintjén is elvégezhető egy több-bérlős forgatókönyvben.

#### <a name="namespaces"></a>Névterek

A névtereket a központi telepítés csoportosításához használhatja. Felhasználhatók arra is, hogy a több-bérlős forgatókönyvben ugyanazt az alkalmazást bérlő összes értesítési központját képviseljék.

#### <a name="geo-distribution"></a>Földrajzi eloszlás

A földrajzi eloszlás nem mindig kritikus a leküldéses értesítési forgatókönyvekben. Különböző PNSes (például APNs vagy FCM), amelyek leküldéses értesítéseket küldenek az eszközökre még nem terjeszthetők ki.

Ha olyan alkalmazással rendelkezik, amelyet globálisan használ, különböző névterekben hozhat létre hubokat a világ különböző Azure-régióiban található Notification Hubs szolgáltatás használatával.

> [!NOTE]
> Ez a megoldás nem ajánlott, mert az növeli a felügyeleti költségeket, különösen a regisztrációk esetében. Ezt csak akkor kell elvégezni, ha van kifejezett igénye.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Kell-e regisztrálni az alkalmazás hátterében vagy közvetlenül az ügyféleszközök segítségével?

Az alkalmazás-háttérbeli regisztrációk akkor hasznosak, ha a regisztráció létrehozása előtt hitelesíteni kell az ügyfeleket. Emellett akkor is hasznosak lehetnek, ha olyan címkéket szeretne használni, amelyeket az alkalmazás hátterének az alkalmazás logikája alapján kell létrehoznia vagy módosítania. További információért látogasson el a [háttérbeli regisztrációs útmutató] és a [háttér-regisztrációs útmutató 2] oldalára.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Mi a leküldéses értesítések kézbesítésének biztonsági modellje?

Az Azure Notification Hubs egy [közös hozzáférésű aláírás](../storage/common/storage-sas-overview.md)-alapú biztonsági modellt használ. Használhatja a közös hozzáférésű aláírási jogkivonatokat a legfelső szintű névtér szintjén vagy a részletes értesítési központ szintjén. A közös hozzáférésű aláírási jogkivonatok különböző engedélyezési szabályok követésére állíthatók be, például üzenet-engedélyek küldésére vagy értesítési engedélyek figyelésére. További információ: [Notification Hubs biztonsági modell] dokumentum.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hogyan kezelhetem a bizalmas adattartalmat a leküldéses értesítésekben?

A platform PNS minden értesítést továbbít a megcélzott eszközöknek. Amikor értesítést küld az Azure Notification Hubsnek, a rendszer feldolgozza és továbbítja a megfelelő PNS.

Minden kapcsolat, a küldőtől az Azure Notification Hubs a PNS, használja a HTTPS protokollt.

> [!NOTE]
> Az Azure Notification Hubs nem naplózza az üzenetek hasznos adatait.

Bizalmas adattartalom küldéséhez biztonságos leküldéses mintát ajánlott használni. A küldő a bizalmas adattartalom nélkül kézbesít egy, az eszközhöz tartozó üzenet-azonosítóval rendelkező ping-értesítést. Amikor az eszközön az alkalmazás megkapja a hasznos adatokat, az alkalmazás közvetlenül a biztonságos API-t hívja meg az üzenet részleteinek beolvasásához. A minta megvalósításának útmutatóját a [Notification Hubs biztonságos leküldéses oktatóanyag] oldalán találja.

## <a name="operations"></a>Üzemeltetés

### <a name="what-support-is-provided-for-disaster-recovery"></a>Milyen támogatást biztosít a vész-helyreállítás?

A metaadatokon alapuló vész-helyreállítási lefedettséget biztosítunk a végén (a Notification Hubs neve, a kapcsolatok karakterlánca és egyéb kritikus információk). Vész-helyreállítási forgatókönyv esetén a regisztrációs adatmennyiség az Notification Hubs infrastruktúra *egyetlen szegmense* , amely elveszett. Be kell vezetnie egy megoldást, amellyel újra fel lehet tölteni ezeket az adatokat az új hubhoz a helyreállítás után:

1. Másodlagos értesítési központ létrehozása egy másik adatközpontban. Azt javasoljuk, hogy hozzon létre egyet az elejétől kezdve, és megvédje Önt olyan vész-helyreállítási eseménytől, amely hatással lehet a felügyeleti képességeire. A vész-helyreállítási esemény időpontjában is létrehozhat egyet.

2. Tartsa a másodlagos értesítési központot szinkronban az elsődleges értesítési központtal az alábbi lehetőségek egyikével:

   * Használjon olyan alkalmazás-hátteret, amely egyszerre hozza létre és frissíti a telepítéseket mindkét értesítési központban. A telepítések lehetővé teszik saját egyedi eszköz azonosítójának megadását, ami a replikálási forgatókönyvhöz alkalmasabb. További információ: Ez a [mintakód](https://github.com/Azure/azure-notificationhubs-dotnet/tree/main/Samples/RedundantHubSample).
   * Használjon olyan alkalmazás-hátteret, amely az elsődleges értesítési központban található regisztrációk rendszeres memóriaképét biztonsági másolatként kéri le. Ezt követően tömeges beszúrást végezhet a másodlagos értesítési központban.

A másodlagos értesítési központ lejárt telepítésekkel/regisztrációkkal végződhet. Ha a leküldés egy lejárt leíróra történik, Notification Hubs automatikusan törli a társított telepítési/regisztrációs rekordot a PNS-kiszolgálótól kapott válasz alapján. Ha törölni szeretné a lejárt rekordokat egy másodlagos értesítési központból, adjon hozzá egyéni logikát, amely az egyes küldések visszajelzéseit dolgozza fel. Ezután lejár a telepítés/regisztráció a másodlagos értesítési központban.

Ha nem rendelkezik háttérrel, az alkalmazás a céleszköz indításakor új regisztrációt hajt végre a másodlagos értesítési központban. Végül a másodlagos értesítési központ minden aktív eszközön regisztrálva lesz.

A nem megnyitott alkalmazásokkal rendelkező eszközök nem kapják meg az értesítéseket.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Az összes tárolt adatai titkosított formában vannak tárolva?

Az Azure Notification Hubs a regisztrációs címkék kivételével titkosítja az összes vásárlói adatmennyiséget. Ezért ne tárolja a személyes vagy bizalmas adatok címkével történő tárolását.

### <a name="is-there-audit-log-capability"></a>Naplózható a naplózási funkció?

Igen. Az összes Notification Hubs felügyeleti művelettel frissítheti az Azure-beli tevékenység naplóját, amely a [Azure Portal]elérhető. Az Azure-tevékenység naplója betekintést nyújt az előfizetésében lévő erőforrásokon végrehajtott műveletekre. A műveletnapló segítségével meghatározhatja a mi, ki és mikor az előfizetésben lévő erőforrásokhoz kapcsolódó írási műveletek (PUT, POST, DELETE) esetében. Megtudhatja a műveletek állapotát és az egyéb kapcsolódó tulajdonságokat is. Azonban. a műveletnapló nem tartalmaz olvasási (GET) műveletet.

## <a name="monitoring-and-troubleshooting"></a>Figyelés és hibaelhárítás

### <a name="what-troubleshooting-capabilities-are-available"></a>Milyen hibaelhárítási lehetőségek érhetők el?

Az Azure Notification Hubs számos funkciót kínál a hibaelhárításhoz, különösen az eldobott értesítések leggyakoribb forgatókönyve tekintetében. Részletekért tekintse meg a [Notification Hubs hibaelhárítási] tanulmányt.

### <a name="what-telemetry-features-are-available"></a>Milyen telemetria funkciók érhetők el?

Az Azure Notification Hubs lehetővé teszi a [Azure Portal]telemetria-beli megtekintését. A metrikák részletei a [Notification Hubs metrikák] lapon érhetők el.

Programozott módon is elérheti a metrikákat. További információért tekintse át a következő cikkeket:

- [Azure monitor mérőszámok beolvasása a .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)-tel. Ez a példa a felhasználónevet és a jelszót használja. Ha tanúsítványt szeretne használni, a FromServicePrincipal metódust a [jelen példában](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs)látható módon kell megadnia a tanúsítvány megadásához. 
- [Erőforrások metrikáinak és tevékenységi naplóinak beolvasása](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Útmutató az Azure Monitor REST API-hoz](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> A sikeres értesítések egyszerűen leküldéses értesítéseket küldenek a külső PNS (például iOS-re, macOS-re vagy FCM-re Android-eszközök esetén). A PNS feladata az értesítések továbbítása az eszközök számára. A PNS jellemzően nem tesznek elérhetővé kézbesítési metrikákat harmadik félnek.  

[Azure Portal]: https://portal.azure.com
[Díjszabás Notification Hubs]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Notification Hubs REST API-k]: /previous-versions/azure/reference/dn530746(v=azure.100)
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Háttérbeli regisztrációs útmutató]: /previous-versions/azure/azure-services/dn743807(v=azure.100)
[Háttérbeli regisztrációs útmutató 2]: /previous-versions/azure/azure-services/dn530747(v=azure.100)
[Notification Hubs biztonsági modell]: /previous-versions/azure/azure-services/dn495373(v=azure.100)
[Biztonságos leküldéses oktatóanyag Notification Hubs]: ./notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md
[Hibaelhárítás Notification Hubs]: ./notification-hubs-push-notification-fixer.md
[Notification Hubs metrikák]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Regisztráció Exportálás/Importálás]: ./export-modify-registrations-bulk.md
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
