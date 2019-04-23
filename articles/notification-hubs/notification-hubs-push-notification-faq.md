---
title: 'Az Azure Notification hubs használatával: Gyakori kérdések (GYIK) |} A Microsoft Docs'
description: A Notification Hubs-megoldások tervezése és végrehajtási – gyakori kérdések
services: notification-hubs
documentationcenter: mobile
author: jwargo
manager: patniko
editor: spelluru
keywords: leküldéses értesítés, leküldéses értesítések, leküldéses értesítések iOS, android leküldéses értesítések, ios leküldéses, android leküldéses
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 03/11/2019
ms.author: jowargo
ms.openlocfilehash: 642e586371a0ce6abe362f8b10b54bfd8baff678
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998923"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Az Azure Notification Hubs leküldéses értesítések: Gyakori kérdések

## <a name="general"></a>Általános kérdések

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Mi az a Notification Hubs erőforrás szerkezete?

Az Azure Notification Hubs két erőforrás olyan szintje van: hubs és a névterek. A központ egy egyetlen leküldéses erőforrás, amelyet a platformfüggetlen leküldés adatokat egy alkalmazás tartalmazhat. A névtér egy adott régióban hubs gyűjteménye.

Ajánlott leképezés megegyezik egy alkalmazást egy névtérben. Egy adott névtéren belül rendelkezhet egy éles elosztó, amely az éles alkalmazásokban működik, egy tesztelési elosztó, amely működik a tesztelési alkalmazást, és így tovább.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Mi az a díjszabási modell, a Notification hubs szolgáltatásra?

A legfrissebb díjszabási részletek találhatók a [Notification Hubs díjszabása] lapot. A Notification Hubs a névterek szintjén történik. (A névtér definícióját, lásd a "Mi az Notification Hubs erőforrás szerkezete?") A Notification Hubs három szintet kínál:

* **Ingyenes**: Ez a szint leküldési képességek mélyebben jó kiindulási pont. Nem ajánlott éles alkalmazások számára. 500 eszközt kap, és nem szolgáltatás szolgáltatásiszint-szerződés (SLA) garantált minden névtér / hó, leküldi 1 millió.
* **Alapszintű**: Ez a szint (vagy a Standard szintű) kisebb az éles alkalmazások számára ajánlott. 200 000 eszköz kap, és 10 millió leküldést minden névtér kiindulópontként havonta.
* **Standard szintű**: Ez a szint Közepes nagy méretű éles alkalmazások ajánlott. 10 millió eszközöket kap, és 10 millió leküldést minden névtér kiindulópontként havonta. Sokoldalú telemetria (a megadott leküldéses állapotával kapcsolatos további adatokat) tartalmaz.

A standard szint funkciói:

* **Sokoldalú telemetria**: Notification Hubs üzenetszintű telemetriai adatokhoz használatával bármely leküldéses kérelmek és a Platform Notification System-visszajelzések követése a hibakereséshez.
* **Több-bérlős módhoz**: Platform Notification System-hitelesítő adatokkal a névtér szinten működik. Ez a beállítás lehetővé teszi a bérlők könnyen oszthatók ugyanazon névterében.
* **Ütemezett leküldés**: Értesítések küldésének bármikor ütemezheti.
* **Tömeges műveletek**: Regisztrációk Exportálás/importálás funkció lehetővé teszi a leírtak szerint a [Regisztrációk Exportálás/importálás] dokumentumot.

### <a name="what-is-the-notification-hubs-sla"></a>Mi az a Notification Hubs SLA-t?

Az alapszintű és standard szintű Notification Hubs vagyis a megfelelően konfigurált alkalmazások leküldéses értesítéseket küldeni, vagy legalább 99,9 %-ában regisztrációs műveletek végrehajtása. További információ az SLA-t, lépjen a [Notification Hubs SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) lapot.

> [!NOTE]
> Leküldéses értesítések függenek külső Platform Notification System (például az Apple APNS vagy a Google FCM), mert nincs SLA garanciát az üzenetek kézbesítését. A Notification Hubs köteg küld a Platform Notification System (garantált SLA), miután feladata a Platform Notification System rendszerek, hogy a leküldések (nem garantált SLA).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hogyan frissítse vagy Visszaléptetés a központ vagy a névtér másik tarifacsomagra?

Nyissa meg a  **[Azure Portal]** > **Notification Hubs-névterek** vagy **Notification Hubs**. Válassza ki az erőforrást szeretné frissíteni, és váltson **Tarifacsomag**. Vegye figyelembe az alábbi követelményeknek:

* A frissített tarifacsomag érvényes *összes* hubs használata a névtérben.
* Ha az eszközök száma meghaladja a korlátot, a réteg még alacsonyabb szolgáltatásszintre való, eszközök törlése előtt, alacsonyabbra szeretne.

## <a name="design-and-development"></a>Tervezés és fejlesztés

### <a name="which-server-side-platforms-do-you-support"></a>Mely kiszolgálóoldali platformokat támogatja?

Server SDK-k érhetők el .NET, Java, Node.js, PHP és Python. Notification Hubs API-k REST-felületeihez alapulnak, így használhatja közvetlenül a REST API-k Ha használ a különböző platformokon, vagy nem szeretné, további függőség. További információkért látogasson el a [A Notification Hubs – REST API-k] lapot.

### <a name="which-client-platforms-do-you-support"></a>Milyen ügyfélplatformokat támogatja?

Leküldéses értesítések támogatását [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) és az Android, [Chrome-alkalmazások](notification-hubs-chrome-push-notifications-get-started.md), és [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). További információkért látogasson el a [A Notification Hubs bevezetés kapcsolatos oktatóanyagok] lapot.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Támogatják a szöveges üzenet, e-mailben vagy webes értesítések?

A Notification Hubs elsősorban célja, hogy az értesítések küldése a mobilalkalmazások. Nem biztosítják e-mail vagy szöveges üzenetek kezelésére. Azonban ezeket a képességeket biztosító külső rendszerek integrálhatók az Notification Hubs használatával leküldéses értesítések küldése [Mobile Apps].

A Notification Hubs is biztosít egy böngészőben leküldéses értesítési kézbesítési szolgáltatás beépített. Ügyfelek valósítható meg ez a funkció kiszolgálóoldali által támogatott platformok felett SignalR használatával. Ha szeretne értesítések küldése a Chrome-tesztkörnyezetben a böngészőben megjelenő alkalmazásokba, tekintse meg a [Chrome-alkalmazások oktatóanyag].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Hogyan történik a Mobile Apps, mind az Azure Notification hubs használatával kapcsolatban, és mikor célszerű használni őket?

Ha rendelkezik egy meglévő mobil-háttéralkalmazást, és csak a leküldéses értesítések küldéséhez funkció hozzáadni kívánt, használhatja az Azure Notification hubs használatával. Ha azt szeretné, a mobil-háttéralkalmazás beállításához az alapoktól, fontolja meg az Azure App Service Mobile Apps funkcióját. Egy mobilalkalmazást egy értesítési központ automatikusan látja el, hogy a mobil-háttéralkalmazást, egyszerűen küldhet leküldéses értesítéseket. A Mobile Apps díjszabása tartalmaz egy értesítési központ az alapdíjat. Csak amikor ügyfél túllépi a csomagban foglalt leküldések kell fizetnie. A költségek további részletekért látogasson el a [App Service díjszabása] lapot.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Hány eszközt is támogatásnak Ha küldhető leküldéses értesítések a Notification hubs szolgáltatással?

Tekintse meg a [Notification Hubs díjszabása] oldalán a támogatott eszközök számát.

Ha támogatásra van szüksége a több mint 10 millió regisztrált eszközök esetében [lépjen kapcsolatba velünk](https://azure.microsoft.com/overview/contact-us/) közvetlenül, és segítségre van szüksége a megoldás méretezése.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hány leküldéses értesítéseket is küldhető?

A kiválasztott tarifacsomag függően az Azure Notification Hubs automatikusan felskálázással áthaladnak a rendszeren értesítések száma alapján.

> [!NOTE]
> A teljes használat költség növelheti szolgál ki a leküldéses értesítések száma alapján. Győződjön meg arról, hogy biztosan a vázolt szint korlátját tisztában van a [Notification Hubs díjszabása] lapot.

Ügyfeleink a Notification Hubs használatával millió leküldéses értesítés küldése naponta. Nem rendelkezik különösebb méretezését is elérjék a leküldéses értesítés küldését, mindaddig, amíg az Azure Notification Hubs használata.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Mennyi időt vesz igénybe az elküldött leküldéses értesítések az eszköz eléréséhez?

Egy normál használható a forgatókönyvben, ahol a bejövő terhelés konzisztens, és akkor is, az Azure Notification Hubs képes feldolgozni legalább *1 millió leküldéses értesítést küld egy percig*. Ez a díjszabás a címkék száma, a bejövő küldésekhez és az egyéb külső tényezők jellegétől függően változhat.

A várható szállítási idő alatt a szolgáltatás a cél platformonként számítja ki, és útvonalak üzeneteket, a Push Notification szolgáltatás (PNS) címke kifejezések vagy regisztrált címkék alapján. A feladata az értesítések küldéséhez az eszköz a PNS.

A PNS nem garantálja a olyan SLA értesítések kézbesítéséhez. Azonban a legtöbb leküldéses értesítések vannak céleszközökre kézbesítendő (általában 10 percen belül) néhány perc alatt az időponttól küldhetők el a Notification hubs használatával. Néhány értesítések több időt is igénybe vehet.

> [!NOTE]
> Az Azure Notification Hubs közvetlen bármely leküldéses értesítések, amelyek nem lett elküldve a számított 30 percen belül a pns-sel történő házirend tartozik. Ez a késleltetés különböző okok miatt fordulhat elő, de a legtöbb gyakran, mert a pns-sel az alkalmazás szabályozza.

### <a name="is-there-any-latency-guarantee"></a>Van bármilyen késési garancia?

Leküldéses értesítések (azok csoportokban egy külső, az egyes platformokra vonatkozó PNS) jellege miatt nem késés nem tudunk garanciát. Leküldéses értesítések a legtöbb általában néhány percen belül lépnek.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Hogyan készüljek névterek és a notification hubs egy megoldás tervezésekor érdemes figyelembe venni?

#### <a name="mobile-appenvironment"></a>Mobile app-környezet

* Az egyes mobil alkalmazások környezetenként egy értesítési központ használja.
* Egy több-bérlős forgatókönyvben minden bérlő külön hub kell rendelkeznie.
* Ne ossza meg az azonos értesítési központ éles környezetben, és tesztelési környezetek. Ez az eljárás problémákat okozhat, értesítések küldésekor. (Az Apple kínál védőfal és a termelési leküldéses végpont, minden egyes külön hitelesítő adatokkal.)
* Alapértelmezés szerint küldhet teszt értesítések a regisztrált eszközök az Azure Portalon vagy az Azure integrált összetevőt a Visual Studióban. 10 olyan eszközt, amely a regisztrációs készletből véletlenszerűen kiválasztott küszöbérték beállítása.

> [!NOTE]
> Ha a hub eredetileg konfigurálták az Apple védőfal tanúsítványt, és ezután újra lett konfigurálni, hogy az Apple-éles tanúsítványt, az eredeti tokenek érvénytelenek. Érvénytelen jogkivonatok miatt sikertelen leküldések. A termelési és tesztelési környezetek elkülönítésére, és különböző hubs használata a különböző környezetekhez.

#### <a name="pns-credentials"></a>PNS hitelesítő adatok

Mobilalkalmazás-platform fejlesztői portál (például az Apple vagy a Google) regisztrálva van, amikor egy alkalmazás azonosítóját, valamint a biztonsági jogkivonatokat elküldi a rendszer. A háttéralkalmazás ezek a jogkivonatok, hogy az eszközök leküldéses értesítések küldhetők a platform PNS biztosít. Biztonsági jogkivonatok tanúsítványok (például Apple IOS-es vagy Windows Phone) vagy a biztonsági kulcsok (például Google Android vagy Windows) formájában is lehet. A notification hubs kell ezeket konfigurálni. Konfigurációs általában a notification hub szintjén történik, de azt is megteheti egy több-bérlős forgatókönyvben a névterek szintjén.

#### <a name="namespaces"></a>Névterek

Üzembe helyezés csoportosítási névterek is használható. Akkor is használható, amelyek a notification hubs központok egy több-bérlős forgatókönyvben ugyanazt az alkalmazást az összes bérlői számára.

#### <a name="geo-distribution"></a>A földrajzi elosztás

A földrajzi elosztás nincs mindig kritikus fontosságú a leküldéses értesítési forgatókönyvek. Különböző PNSes (például a APNS vagy a FCM), amely a leküldéses értesítések kézbesíthetők eszközökre nem egyenletesen oszlik el.

Ha egy alkalmazás, amelynek globálisan, létrehozhat hubs különböző névterekben az Notification Hubs szolgáltatás segítségével különböző Azure-régiókban világszerte.

> [!NOTE]
> Ezzel az elrendezéssel fokozott nem ajánlott, mivel megnöveli a felügyeleti költségek, különösen a regisztrációhoz. Csak akkor, ha egy explicit módon kell kell elvégezni.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Teendő regisztrációk az alkalmazási háttérrendszerből, vagy közvetlenül az ügyfél eszközök?

Az alkalmazás háttérrendszerből kezdeményezett regisztrációk akkor hasznos, ha az ügyfelek hitelesítése a regisztráció létrehozása előtt kell. Azok is hasznos, ha létrejön vagy módosul a háttéralkalmazás alkalmazás logika alapján kell címkéket. További információkért látogasson el a [háttér-regisztrációs útmutató] és [háttér-regisztrációs útmutató 2] oldalakat.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Mi az a leküldéses értesítési biztonsági modellt?

Használja az Azure Notification Hubs egy [közös hozzáférésű jogosultságkód](../storage/common/storage-dotnet-shared-access-signature-part-1.md)-alapú biztonsági modellt. A közös hozzáférésű jogosultságkódok jogkivonataival is használhat, a legfelső szintű névterek szintjén vagy a részletes notification hub szintjén. Közös hozzáférésű jogosultságkód jogkivonatok különböző engedélyezési szabályokat, például kövesse, engedélyek üzenet küldése vagy az értesítés engedélyek figyelésére állítható. További információkért lásd: a [Notification Hubs biztonsági modell] dokumentumot.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hogyan kezelje a bizalmas hasznos a leküldéses értesítések?

Minden értesítés, amely szerint a platform PNS Céleszközök érkeznek. Azure Notification hubs felé küldött értesítést, feldolgozása és a megfelelő PNS átadott.

Minden kapcsolat, a küldőt, hogy a pns-sel, az Azure Notification Hubs a HTTPS használatára.

> [!NOTE]
> Az Azure Notification Hubs semmilyen módon nem naplózza a üzenetek hasznos.

Bizalmas hasznos adatot küld, azt javasoljuk egy biztonságos leküldéses minta használatával. A küldő egy pingelés értesítési üzenet azonosító nélkül a bizalmas hasznos adatokat továbbítja. A névjegyadatokat az eszköz megkapja a hasznos, ha az alkalmazás meghívja a biztonságos API közvetlenül az üzenet részleteit. Útmutató az ebben a minta megvalósítása, nyissa meg a [Notification Hubs leküldéses biztonságos oktatóanyag] lapot.

## <a name="operations"></a>Műveletek

### <a name="what-support-is-provided-for-disaster-recovery"></a>Milyen támogatja-e a vész-helyreállítási?

Metaadatok vész-helyreállítási lefedettség található a rendszerünkben (a Notification Hubs nevét, a kapcsolati karakterlánc és más kritikus fontosságú információkhoz) biztosítunk. Vész-helyreállítási helyzetekre akkor aktiválódik, ha van-e a regisztrációs adatok a *csak szegmens* a Notification Hubs infrastruktúra, amely elveszett. Szüksége lesz a tudja kezelni az adatok be az új eseményközpont utáni helyreállítás egy megoldás bevezetésére:

1. Hozzon létre egy másodlagos értesítési központ egy másik adatközpontba. Azt javasoljuk, hogy hozzon létre egyet a védelme biztosítható, hogy a vész-helyreállítási eseményeket, amelyek hatással lehetnek a kezelési lehetőségek az elejétől. Is létrehozhat egyet a vész-helyreállítási esemény időpontjában.

2. Adja meg a másodlagos értesítési központot, és a regisztrációk az elsődleges értesítési központból. Nem ajánlott, mindkét hubs a regisztrációk kezelése és, ha szinkronizálva maradnának módon származnak, a regisztráció közben. Ez az eljárás jól lejár a PNS-oldalon regisztrációk rejlő megfelelő miatt nem működik. A Notification Hubs megtisztítja őket, lejárt vagy érvénytelen a regisztrációk PNS visszajelzést kap.  

Háttéralkalmazások két javaslatok van:

* Egy háttéralkalmazás, amely fenntartja a végén a regisztrációk egy adott készletét használja. A másodlagos értesítési központot, majd műveleteket hajthat végre egy tömeges beszúrás.
* Egy háttéralkalmazás, amely egy normál memóriakép regisztrációk olvas be az elsődleges értesítési központ biztonsági mentéséhez használja. A másodlagos értesítési központot, majd műveleteket hajthat végre egy tömeges beszúrás.

> [!NOTE]
> Regisztrációk Exportálás/importálás funkció érhető el, a Standard szintű leírt a [Regisztrációk Exportálás/importálás] dokumentumot.

Ha a háttérkiszolgáló az alkalmazás indításakor a Céleszközök nincs, akkor hajtsa végre egy új regisztrációs a másodlagos értesítési központ. Végül a másodlagos értesítési központban regisztrált aktív eszközök fog rendelkezni.

Egy adott időszakban, amikor bontatlan alkalmazásokat futtató eszközökről nem kap értesítéseket lesz.

### <a name="is-there-audit-log-capability"></a>Naplózási naplózási képesség van?

Igen. Az összes Notification Hubs felügyeleti műveletek, amelyhez az Azure-tevékenységnapló érhető el a frissítés a [Azure Portal]. Az Azure-tevékenységnapló biztosít az előfizetésekben erőforrásokon végrehajtott műveletekkel kapcsolatos információkat. A tevékenységnapló használatával megadhatja, hogy mit, ki és mikor minden írási műveletek (PUT, POST, DELETE) arról, hogy az erőforrást az előfizetésében. A műveletek és az egyéb releváns tulajdonságok állapotát is ismernie is. Azonban. a tevékenységnapló nem tartalmazza az olvasási (GET) műveletet.

## <a name="monitoring-and-troubleshooting"></a>Megfigyelés és hibaelhárítás

### <a name="what-troubleshooting-capabilities-are-available"></a>Milyen hibaelhárítási funkciók érhetők el?

Az Azure Notification Hubs számos funkciót biztosít, különösen számára a leggyakoribb eset az elvetett értesítések hibaelhárítása. További információkért lásd: a [A Notification Hubs hibáinak elhárítása] tanulmányt.

### <a name="what-telemetry-features-are-available"></a>Milyen telemetriai funkciók érhetők el?

Telemetriai adatok megtekintése az Azure Notification hubs szolgáltatás segítségével a [Azure Portal]. A metrikák adatait a érhetők el a [Notification Hubs-metrikák] lap.

Elérheti a mérőszámokat szoftveresen is. További információkért tekintse át a következő cikkeket:

- [.NET-tel az Azure Monitor-metrikák beolvasása](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). Ebben a példában a felhasználónév és jelszó. Egy tanúsítványt szeretne használni, adja meg a tanúsítvány, ahogyan a FromServicePrincipal metódust túlterhelni [ebben a példában](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Mérőszámok és Tevékenységnaplók erőforrás beolvasása](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Az Azure Monitoring REST API-forgatókönyv](../azure-monitor/platform/rest-api-walkthrough.md)


> [!NOTE]
> Sikeres értesítések, leküldéses értesítések lesz küldve a külső pns-sel (például az Apple APNS) vagy a Google FCM egyszerűen jelenti. A feladata az, hogy az értesítések céleszközökre a PNS. Általában a PNS nem fedi fel kézbesítési metrikák át harmadik félnek.  

[Azure Portal]: https://portal.azure.com
[Notification Hubs díjszabása]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[A Notification Hubs – REST API-k]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[A Notification Hubs bevezetés kapcsolatos oktatóanyagok]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome-alkalmazások oktatóanyag]: https://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Háttér-regisztrációs útmutató]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Háttér-regisztrációs útmutató 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Notification Hubs biztonsági modell]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Notification Hubs leküldéses biztonságos oktatóanyag]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[A Notification Hubs hibáinak elhárítása]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs-metrikák]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Regisztrációk Exportálás/importálás]: https://docs.microsoft.com/en-us/azure/notification-hubs/export-modify-registrations-bulk
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[App Service díjszabása]: https://azure.microsoft.com/pricing/details/app-service/
