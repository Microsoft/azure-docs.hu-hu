---
title: Azure Traffic Manager – gyakori kérdések
description: Ez a cikk a gyakori kérdésekre ad választ a Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: 708d63695cbba53578b13d1674b9aa99018bcae4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791120"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager gyakori kérdések (GYIK)

## <a name="traffic-manager-basics"></a>Traffic Manager alapszintű

### <a name="what-ip-address-does-traffic-manager-use"></a>Milyen IP-címet Traffic Manager használni?

A How Traffic Manager Works (Az Traffic Manager [működése)](../traffic-manager/traffic-manager-how-it-works.md)című Traffic Manager DNS-szinten működik. DNS-válaszokat küld az ügyfeleknek a megfelelő szolgáltatásvégpontra. Az ügyfelek ezután közvetlenül a szolgáltatásvégponthoz csatlakoznak, nem pedig a Traffic Manager.

Ezért a Traffic Manager nem biztosít végpontot vagy IP-címet az ügyfelek számára, amelyekhez csatlakozhatnak. Ha statikus IP-címet szeretne a szolgáltatáshoz, azt a szolgáltatásban kell konfigurálni, nem a Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Milyen típusú forgalom irányítható a Traffic Manager?
A How Traffic Manager Works (A Traffic Manager [működése)](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager végpont bármilyen, az Azure-ban vagy azon kívül üzemeltetett internetes szolgáltatás lehet. Ezért a Traffic Manager a nyilvános internetről származó forgalmat olyan végpontokra irányíthatja, amelyek szintén internetre vannak irányítva. Ha privát hálózaton belüli végpontokkal (például az [Azure Load Balancer](../load-balancer/components.md#frontend-ip-configurations)belső verziójával) vagy a felhasználók DNS-kéréseket továbbítnak az ilyen belső hálózatokról, akkor nem használhatja a Traffic Manager-t a forgalom útválasztásához.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Támogatja Traffic Manager a "ragadós" munkameneteket?

A How Traffic Manager Works (Az Traffic Manager [működése)](../traffic-manager/traffic-manager-how-it-works.md)című Traffic Manager DNS-szinten működik. DNS-válaszokat használ a megfelelő szolgáltatásvégponthoz irányítja az ügyfeleket. Az ügyfelek közvetlenül a szolgáltatásvégponthoz csatlakoznak, nem a Traffic Manager. Ezért a Traffic Manager nem látja a HTTP-forgalmat az ügyfél és a kiszolgáló között.

Emellett a kiszolgáló által fogadott DNS-lekérdezés forrás IP-Traffic Manager a rekurzív DNS-szolgáltatáshoz tartozik, nem az ügyfélhez. Ezért a Traffic Manager nem tudja nyomon követni az egyes ügyfeleket, és nem valósíthat meg "ragadós" munkameneteket. Ez a korlátozás minden DNS-alapú forgalomkezelő rendszerre vonatkozik, és nem csak a Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Miért jelenik meg HTTP-hiba a Traffic Manager?

Ahogy azt a [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md)című Traffic Manager DNS-szinten is működik. DNS-válaszokat használ arra, hogy az ügyfeleket a megfelelő szolgáltatásvégponthoz irányítsa. Az ügyfelek ezután közvetlenül a szolgáltatásvégponthoz csatlakoznak, nem pedig a Traffic Manager. Traffic Manager nem látja a HTTP-forgalmat az ügyfél és a kiszolgáló között. Ezért minden észlelt HTTP-hibának az alkalmazásból kell érkezike. Ahhoz, hogy az ügyfél csatlakozzon az alkalmazáshoz, minden DNS-feloldási lépés befejeződött. Ez magában foglal minden olyan interakciót, Traffic Manager az alkalmazás adatforgalmát.

Ezért a további vizsgálatnak az alkalmazásra kell összpontosítania.

Az ügyfél böngészőjéből küldött HTTP-állomásfejléc a leggyakoribb problémaforrás. Győződjön meg arról, hogy az alkalmazás úgy van konfigurálva, hogy elfogadja a használt tartománynévhez megfelelő állomásfejlécet. Az alkalmazást használó Azure App Service lásd: Egyéni tartománynév konfigurálása egy webalkalmazáshoz a Azure App Service [használatával Traffic Manager.](../app-service/configure-domain-traffic-manager.md)

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Milyen hatással van a Traffic Manager?

Ahogy azt a [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md)című Traffic Manager DNS-szinten is működik. Mivel az ügyfelek közvetlenül csatlakoznak a szolgáltatásvégpontjaihoz, a kapcsolat létrejötte után nincs hatással a Traffic Manager teljesítményére.

Mivel Traffic Manager dns-szinten integrálhatók az alkalmazásokkal, további DNS-kereséseket is be kell szúrni a DNS-feloldási láncba. A dns Traffic Manager feloldási időre gyakorolt hatása minimális. Traffic Manager névkiszolgálók globális hálózatát használja, és [anycast](https://en.wikipedia.org/wiki/Anycast) hálózattal biztosítja, hogy a DNS-lekérdezések mindig a legközelebbi elérhető névkiszolgálóhoz legyen irányítva. Emellett a DNS-válaszok gyorsítótárazása azt jelenti, hogy a dns-válaszokkal kapcsolatos további DNS-Traffic Manager csak a munkamenetek töredékére vonatkozik.

A Performance metódus a legközelebbi elérhető végpontrairányja a forgalmat. A nettó eredmény az, hogy a módszerhez kapcsolódó teljes teljesítményre gyakorolt hatásnak minimálisnak kell lennie. A DNS-késés bármilyen növekedését el kell ellensúlyozni a végpont kisebb hálózati késésével.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Milyen alkalmazás-protokollokat használhatok a Traffic Manager?

Ahogy azt a [Traffic Manager működése](../traffic-manager/traffic-manager-how-it-works.md)című Traffic Manager DNS-szinten is működik. A DNS-keresés befejezése után az ügyfelek közvetlenül az alkalmazásvégponthoz csatlakoznak, nem pedig a Traffic Manager. Ezért a kapcsolat bármilyen alkalmazás-protokollt használhat. Ha a TCP protokollt választja monitorozási protokollként, Traffic Manager végpont állapotfigyelése alkalmazás-protokollok használata nélkül is használhatja. Ha úgy dönt, hogy az állapotát egy alkalmazás-protokoll használatával ellenőrzi, a végpontnak képesnek kell lennie HTTP- vagy HTTPS GET-kérésekre válaszolni.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Használhatok Traffic Manager"csupasz" tartománynévvel?

Igen. Ha meg szeretne ismerkedni azzal, hogyan hozhat létre aliasrekordot a Azure Traffic Manager-profilra hivatkozó tartománynév-rekordhoz, tekintse meg az aliasrekordok a csúcstartománynevek [Traffic Manager.](../dns/tutorial-alias-tm.md)

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Figyelembe Traffic Manager az ügyfél alhálózati címét a DNS-lekérdezések kezelésekor? 

Igen, a kapott DNS-lekérdezés forrás IP-címe mellett (amely általában a DNS-feloldó IP-címe), a földrajzi, teljesítmény- és alhálózati útválasztási módszerek keresésének végrehajtásakor a Traffic Manager az ügyfél alhálózati címét is figyelembe veszi, ha a feloldó a kérést a végfelhasználó nevében továbbítja a lekérdezésbe.  
Pontosabban [RFC 7871 –](https://tools.ietf.org/html/rfc7871) A DNS-lekérdezések ügyfél-alhálózata, amely egy DNS-bővítmény [mechanizmust (EDNS0)](https://tools.ietf.org/html/rfc2671) biztosít, amely átadhatja az ügyfél alhálózati címét az azt támogató feloldóktól.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Mi az a DNS-TTL, és milyen hatással van ez a felhasználókra?

Amikor egy DNS-lekérdezés a Traffic Manager, beállítja a válaszban az úgynevezett time-to-live (TTL) értéket. Ez az érték, amelynek egysége másodpercben van, azt jelzi a lefelé irányuló DNS-feloldóknak, hogy mennyi ideig gyorsítótárazható ez a válasz. Bár a DNS-feloldók nem garantáltan gyorsítótárazják ezt az eredményt, a gyorsítótárazás lehetővé teszi számukra, hogy a DNS-kiszolgálók helyett válaszoljanak a gyorsítótáron Traffic Manager lekérdezésekre. Ez a következőképpen befolyásolja a válaszokat:

- A magasabb TTL csökkenti az Traffic Manager DNS-kiszolgálókra irányuló lekérdezések számát, ami csökkentheti az ügyfelek költségeit, mivel a kiszolgált lekérdezések száma számlázható használat.
- egy magasabb TTL csökkentheti a DNS-kereséshez szükséges időt.
- A magasabb TTL azt is jelenti, hogy az adatok nem tükrözik a Traffic Manager ügynökökkel kapott legfrissebb állapotinformációt.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Milyen magas vagy alacsony lehet az TTL beállítása a Traffic Manager válaszokhoz?

Profilszinten a DNS-TTL-t beállíthatja 0 másodpercre, és akár 2 147 483 647 másodpercre is (az [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )szabványnak megfelelő maximális tartomány). A 0-s TTL azt jelenti, hogy az lefelé irányuló DNS-feloldók nem gyorsítótáraznak lekérdezési válaszokat, és az összes lekérdezésnek el kell érnie a Traffic Manager DNS-kiszolgálókat a feloldás érdekében.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hogyan lehet megérteni a profilomhoz érkező lekérdezések mennyiségét? 

A profil által megadott Traffic Manager a profil által megválaszolt lekérdezések száma. Ezt az információt a profilszintű összesítésben is lekérdezheti, vagy feloszthatja tovább, hogy lássa, milyen mennyiségű lekérdezést adott végpontokat adott vissza. Emellett riasztásokat állíthat be, amelyek értesítik, ha a lekérdezés válaszkötete átlépi a beállított feltételeket. További részletekért a [metrikákat Traffic Manager riasztásokat.](traffic-manager-metrics-alerts.md)

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager forgalom-útválasztási módszer

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Milyen esetekben hasznos a földrajzi útválasztás?

A földrajzi útválasztási típus bármely olyan helyzetben használható, ahol az Azure-ügyfélnek földrajzi régiók alapján kell megkülönböztetnie a felhasználóit. A Földrajzi forgalom-útválasztási módszer használatával például más felhasználói élményt adhat adott régiók felhasználói felületének, mint a többi régió felhasználóinak. Egy másik példa a helyi adatszuverenitási szabályozások betartására, amelyek megkövetelik, hogy egy adott régió felhasználóit csak az adott régióban található végpontok szolgálják ki.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hogyan, hogy teljesítmény-útválasztási vagy földrajzi útválasztási módszert használjak?

A két népszerű útválasztási módszer közötti fő különbség az, hogy a teljesítmény-útválasztási módszerben az elsődleges cél az, hogy a forgalmat a hívó számára a legkisebb késést biztosító végpontra küldje, míg a földrajzi útválasztásban az elsődleges cél egy geokerítés kényszerítése a hívók számára, hogy szándékosan irányíthatja őket egy adott végponthoz. Az átfedés azért történik, mert korreláció van a földrajzi közeliség és az alacsonyabb késés között, bár ez nem mindig igaz. Előfordulhat, hogy egy másik földrajzi helyen található végpont jobb késést biztosít a hívónak, és ebben az esetben a Teljesítmény-útválasztás erre a végpontra fogja küldeni a felhasználót, de a földrajzi útválasztás mindig a földrajzi régiójukhoz hozzárendelt végpontra fogja küldeni őket. Annak érdekében, hogy egyértelművé tegye, fontolja meg a következő példát: a földrajzi útválasztással szokatlan leképezéseket is létrehozhat, például az ázsiai adatforgalmat az USA-beli végpontokra, az usa-beli forgalmat pedig az ázsiai végpontokra. Ebben az esetben a földrajzi útválasztás pontosan azt fogja tenni, amihez konfigurálta, és a teljesítményoptimalizálás nem fontos szempont. 
>[!NOTE]
>Előfordulhatnak olyan forgatókönyvek, amelyekben teljesítménybeli és földrajzi útválasztási képességekre is szükség lehet, ezekhez a beágyazott profilokhoz remek választás lehet. Létrehozhat például egy földrajzi útválasztású szülőprofilt, amelyben az Észak-Amerika-ból származó összes forgalmat egy beágyazott profilba küldheti, amely az Egyesült Államokbeli végpontokkal rendelkezik, és teljesítmény-útválasztással a készleten belüli legjobb végpontra küldheti ezeket a forgalmat. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Milyen régiókat támogat a földrajzi Traffic Manager támogatása?

A régiók által használt ország-/régióhierarchia Traffic Manager itt [található.](traffic-manager-geographic-regions.md) Bár ez az oldal mindig naprakészen tart minden módosítást, programozott módon is lekérheti ugyanezeket az információkat a [Azure Traffic Manager REST API.](/rest/api/trafficmanager/) 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hogyan határozza meg a Traffic Manager, hogy egy felhasználó honnan lekérdezést tart?

Traffic Manager a lekérdezés forrás IP-címét (ez valószínűleg egy helyi DNS-feloldó, aki a felhasználó nevében teszi a lekérdezést), és egy belső IP-cím–régió leképezés használatával határozza meg a helyet. A térkép folyamatosan frissül, hogy figyelembe veszi az internet változásait. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Garantált, hogy a Traffic Manager minden esetben pontosan meg tudja határozni a felhasználó földrajzi helyét?

Nem, Traffic Manager, hogy a DNS-lekérdezés forrás IP-címéről kivethető földrajzi régió mindig megfeleljen a felhasználó helyének a következő okok miatt:

- Az előző GYIK-ban leírtak szerint a forrás IP-címe az, hogy egy DNS-feloldó a felhasználó nevében keres. Bár a DNS-feloldó földrajzi helye megfelelő proxy a felhasználó földrajzi helyének, a DNS-feloldó szolgáltatás méretétől és az ügyfél által választott DNS-feloldó szolgáltatástól függően eltérő lehet. Például egy Malájban található ügyfél az eszköz beállításaiban megadhat egy DNS-feloldó szolgáltatást, amelynek a szingapúri DNS-kiszolgálója az adott felhasználó/eszköz lekérdezésfeloldásait kezeli. Ebben az esetben a Traffic Manager csak a szingapúri helynek megfelelő feloldó IP-címét láthatja. Ezen az oldalon az ügyfél alhálózati címének támogatásával kapcsolatos korábbi gyakori kérdéseket is tekintse meg.

- Másodszor, Traffic Manager egy belső térképet használ az IP-cím és a földrajzi régiók fordításához. Bár a térkép ellenőrzése és frissítése folyamatosan történik a pontosság növelése és az internet fejlődő természetének figyelembe véve, fennáll a lehetőség, hogy az információk nem az összes IP-cím földrajzi helyének pontos ábrázolásai.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>A végpontnak fizikailag ugyanabban a régióban kell lennie, mint a földrajzi útválasztáshoz konfigurált végpontnak?

Nem, a végpont helye nem korlátozza, hogy mely régiókat lehet leképezni rá. Az Azure-régióban található US-Central az összes indiai felhasználót irányíthatja hozzá.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Hozzárendelhet földrajzi régiókat olyan profil végpontjaihoz, amelyek nem földrajzi útválasztásra vannak konfigurálva?

Igen, ha egy profil útválasztási módszere nem földrajzi, a Azure Traffic Manager REST API segítségével földrajzi régiókat rendelhet a profil végpontjaihoz. [](/rest/api/trafficmanager/) Nem földrajzi útválasztási típusú profilok esetén a rendszer figyelmen kívül hagyja ezt a konfigurációt. Ha egy ilyen profilt később földrajzi útválasztási típusra módosít, Traffic Manager ezeket a leképezéseket.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Miért kapok hibaüzenetet, amikor egy meglévő profil útválasztási módszerét a Földrajzira szeretném módosítani?

A földrajzi útválasztású profilban található összes végponthoz legalább egy régiót le kell leképezni. Meglévő profil földrajzi útválasztási típusra konvertálásához először földrajzi régiókat kell társítani az összes [végponthoz](/rest/api/trafficmanager/) a Azure Traffic Manager REST API, mielőtt az útválasztási típust földrajzira módosítja. Ha a portált használja, először törölje a végpontokat, módosítsa a profil útválasztási módszerét a földrajzira, majd adja hozzá a végpontokat a földrajzi régióleképezéssel együtt.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Miért ajánlott az ügyfelek beágyazott profilokat létrehozni végpontok helyett olyan profil alatt, amelyeken engedélyezve van a földrajzi útválasztás?

Egy régió csak egy végponthoz rendelhető hozzá egy profilon belül, ha a földrajzi útválasztási módszert használja. Ha ez a végpont nem beágyazott típus, és gyermekprofil van hozzá csatolva, akkor ha a végpont nem megfelelő, az Traffic Manager továbbra is erre a végpontra küldi a forgalmat, mivel a forgalom küldésének mellőzését nem jobb megoldás. Traffic Manager nem ad át feladatátvételt egy másik végpontnak, még akkor sem, ha a hozzárendelt régió annak a régiónak a szülője, amely a nem megfelelőnek számító végponthoz van rendelve (például ha egy, a Spanyolország régióval rendelkezik végpont nem megfelelő, nem történik feladatátvétel egy másik végpontra, amelyhez az Európa régió van hozzárendelve). Ez biztosítja, hogy a Traffic Manager tiszteletben tartsa az ügyfél által a profiljában beállított földrajzi határokat. Annak érdekében, hogy a végpontok nem megfelelő kezelése esetén egy másik végpontra történő feladat-visszaadás előnyhöz jutjon, ajánlott a földrajzi régiókat különálló végpontok helyett több végponttal rendelkező beágyazott profilokhoz rendelni. Így ha a beágyazott gyermekprofil egyik végpontja meghibásodik, a forgalom egy másik végpontra is átveheti a feladatátvételt ugyanazon beágyazott gyermekprofilon belül.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Vonatkoznak olyan korlátozások az API-verzióra, amely támogatja ezt az útválasztási típust?

Igen, csak a 2017-03-01-es és újabb API-verziók támogatják a földrajzi útválasztási típust. A régebbi API-verziók nem használhatók földrajzi útválasztási típusú profilok kiosztásához vagy földrajzi régiók végponthoz való hozzárendeléséhez. Ha egy régebbi API-verziót használ a profilok Azure-előfizetésből való lekéréséhez, a rendszer nem ad vissza földrajzi útválasztási típusú profilt. Emellett a régebbi API-verziók használata esetén a visszaadott, földrajzi régiókhoz hozzárendelt végpontokkal rendelkezik profil nem jelenik meg a földrajzi régió-hozzárendeléssel.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager alhálózat forgalmának útválasztási módszere

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Milyen esetekben hasznos az alhálózatok útválasztása?

Az alhálózati útválasztás lehetővé teszi, hogy megkülönböztesse a dns-kérések IP-címének forrás IP-címe által azonosított felhasználók adott készletei által tapasztalt felhasználói élményt. Például különböző tartalmakat mutathat be, ha a felhasználók egy webhelyhez csatlakoznak a vállalati HQ-ból. Egy másik lehetőség a felhasználók korlátozása bizonyos INTERNET-címekről csak olyan végpontok elérésére, amelyek csak IPv4-kapcsolatokat támogatnak, ha az IPv6 használata esetén ezek az INTERNET-címek részparanitásos teljesítményt elérni.
Az alhálózati útválasztási módszer használatának másik oka a beágyazott profilkészletben található más profilok használata. Ha például földrajzi útválasztási módszert szeretne használni a felhasználók geokerítésére, de egy adott internetszolgáltatónál más útválasztási módszert szeretne használni, létrehozhat egy alhálózati útválasztási módszert használó profilt szülőprofilként, és felülbírálhatja az internetszolgáltatót, hogy egy adott gyermekprofilt használjon, és mindenki más számára standard földrajzi profillal is felhasználhatja.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Honnan Traffic Manager a végfelhasználó IP-címét?

A végfelhasználói eszközök általában DNS-feloldó használatával ják el a DNS-kereséseket a nevükben. Az ilyen feloldók kimenő IP-címe az, Traffic Manager forrás IP-címként lát. Emellett az alhálózat útválasztási metódusa azt is meg tudja tudni, hogy van-e olyan EDNS0 bővített ügyfél-alhálózati (ECS) információ, amely a kéréssel együtt lett átküldve. Ha vannak ECS-információk, akkor ez a cím határozza meg az útválasztást. ECS-információk hiányában a lekérdezés forrás IP-címe útválasztási célokra szolgál.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hogyan adhatók meg IP-címek alhálózati útválasztás használata esetén?

A végponthoz társítható IP-címek két módon is megszabadhatóak. Először is a quad pontozott decimális oktett-et kezdő és záró címekkel használva megadhatja a tartományt (például 1.2.3.4-5.6.7.8 vagy 3.4.5.6-3.4.5.6). Másodszor, a CIDR-beállítással megadhatja a tartományt (például 1.2.3.0/24). Több tartományt is megadhat, és használhat mindkét típust egy tartománykészletben. Vonatkozik néhány korlátozás.

-    A címtartományok nem lehetnek átfedésben, mivel minden IP-címet egyetlen végpontra kell leképezni
-    A kezdőcím nem lehet nagyobb a záró címnél
-    CIDR-notation esetén a /előtti IP-címnek az adott tartomány kezdőcímének kell lennie (például 1.2.3.0/24 érvényes, de az 1.2.3.4.4/24 NEM érvényes)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hogyan adható meg tartalék végpont alhálózati útválasztás használata esetén?

Ha alhálózat-útválasztású profillal rendelkezik, és rendelkezik olyan végponttal, amely nincs hozzárendelve alhálózatokkal, a rendszer a többi végponttal nem egyező kérést ide irányítja. Erősen ajánlott, hogy egy ilyen tartalék végpont legyen a profilban, mivel a Traffic Manager NXDOMAIN választ ad vissza, ha egy kérés érkezik, és nincs leképezve egyik végpontra sem, vagy ha egy végpontra van leképezve, de a végpont nem megfelelő.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Mi történik, ha egy végpont le van tiltva egy alhálózati útválasztási típusú profilban?

Ha egy alhálózati útválasztású profilban egy végpont le van tiltva, a Traffic Manager úgy fog viselkedni, mintha ez a végpont és az alhálózati leképezések nem léteznének. Ha olyan lekérdezés érkezik, amely megfelelt volna az IP-címleképezésnek, és a végpont le van tiltva, a Traffic Manager egy tartalék végpontot ad vissza (egy leképezés nélkül), vagy ha nincs ilyen végpont, NXDOMAIN választ ad vissza.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager MultiValue forgalom-útválasztási módszer

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Milyen esetekben hasznos a MultiValue útválasztás?

A MultiValue útválasztás több kifogástalan állapotú végpontot ad vissza egyetlen lekérdezési válaszban. Ennek fő előnye, hogy ha egy végpont nem megfelelő, az ügyfélnek több lehetőség áll rendelkezésre az újrapróbálkozásra anélkül, hogy újabb DNS-hívást kellene megismételni (amely ugyanezt az értéket ad vissza egy felfelé irányuló gyorsítótárból). Ez a rendelkezésre állásra érzékeny alkalmazásokra vonatkozik, amelyek minimalizálni szeretnék az állásidőt.
A MultiValue útválasztási módszer egy másik használata, ha a végpont IPv4- és IPv6-címeken is "kettős" használatú, és mindkét lehetőséget meg szeretné adni a hívónak, amely közül választhat, amikor kapcsolatot kezdeményez a végponttal.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hány végpontot ad vissza a függvény a MultiValue útválasztás használata esetén?

Megadhatja a visszaadott végpontok maximális számát, és a MultiValue legfeljebb annyi kifogástalan állapotú végpontot ad vissza lekérdezés esetén. A konfiguráció lehetséges maximális értéke 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Ugyanazt a végpontkészletet kapom, ha MultiValue útválasztást használok?

Nem tudjuk garantálni, hogy minden lekérdezés ugyanazt a végpontkészletet adja vissza. Ezt az is befolyásolja, hogy egyes végpontok nem megfelelőek lehetnek, és a válasz nem tartalmazza őket

## <a name="real-user-measurements"></a>Valós felhasználói mérések

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Milyen előnyökkel jár a valós felhasználómérés?

Ha teljesítmény-útválasztási módszert használ, a Traffic Manager kiválasztja a legjobb Azure-régiót, amelyhez a végfelhasználó csatlakozhat. Ennek érdekében megvizsgálja a forrás IP-címet és az EDNS-ügyfél alhálózatát (ha be van stb.), és ellenőrzi a szolgáltatás által karbantartott hálózati késési intelligenciával. valós felhasználómérés javítja ezt a végfelhasználói bázis számára, mivel a felhasználói élmény hozzájárul ehhez a késési táblázathoz, és biztosítja, hogy ez a táblázat megfelelő mértékben kiterjedjon a végfelhasználói hálózatokra, ahonnan a végfelhasználók csatlakoznak az Azure-hoz. Ez nagyobb pontosságot biztosít a végfelhasználó útválasztásában.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Használhatok nem Azure valós felhasználómérés régióban is?

valós felhasználómérés az Azure-régiók eléréséhez szükséges késést méri és jelenti. Ha nem Azure-régiókban üzemeltetett végpontokkal használ teljesítményalapú útválasztást, akkor is kihasználhatja ezt a funkciót, ha nagyobb késési információkkal rendelkezik a végponthoz társított reprezentatív Azure-régióról.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Melyik útválasztási módszernek van valós felhasználómérés?

A profilokkal szerzett további valós felhasználómérés csak a teljesítmény-útválasztási módszert futtató profilokra vonatkoznak. A valós felhasználómérés hivatkozás minden profilból elérhető, amikor megtekinti a Azure Portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Az egyes profilokat külön kell valós felhasználómérés engedélyezni?

Nem, ezt előfizetésenként csak egyszer kell engedélyeznie, és minden mért és jelentett késési információ elérhető az összes profil számára.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hogyan ki az valós felhasználómérés előfizetéshez?

Ha már nem gyűjt és küld vissza késési méréseket az ügyfélalkalmazásból, akkor nem kell további díjakat fizetnie a valós felhasználómérés kapcsolódó díjakért. Ha például a mérések javascriptet ágyaznak be weblapokbe, a funkció használatát megszüntetheti a JavaScript eltávolításával vagy a meghívásának kikapcsolásával az oldal megjelenítésekor.

A kulcs törlésével valós felhasználómérés is kikapcsolhatja a kulcsot. A kulcs törlése után a rendszer az adott Traffic Manager küldött összes mérést elveti.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Használhatok webes valós felhasználómérés más ügyfélalkalmazásokkal is?

Igen, valós felhasználómérés úgy lett kialakítva, hogy különböző típusú végfelhasználói ügyfeleken keresztül gyűjtse be az összegyűjtött adatokat. Ez a GYIK frissülni fog, amint az új típusú ügyfélalkalmazások támogatottak lesznek.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hány mérést végez a rendszer minden alkalommal, amikor valós felhasználómérés engedélyezve van a weblapom?

Ha valós felhasználómérés a megadott JavaScript-méréssel együtt, minden oldalremegjelenés hat mérést ad vissza. Ezeket a jelentések ezután visszajelentik a Traffic Manager szolgáltatásnak. A szolgáltatásért a szolgáltatásnak jelentett mérések száma alapján kell Traffic Manager díjat. Ha például a felhasználó elnavigál a weboldalról a mérések alatt, de még a jelentés előtt, a rendszer ezeket a méréseket nem számítja számlázási célra.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Van késés, mielőtt valós felhasználómérés szkript lefut a weblapomon?

Nem, nincs programozott késleltetés a szkript meghívása előtt.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Használhatok olyan valós felhasználómérés csak a mérni kívánt Azure-régiókkal?

Nem, a valós felhasználómérés szkript minden meghíváskor hat Azure-régiót méri a szolgáltatás által meghatározottak szerint. Ez a készlet megváltozik a különböző meghívások között, és nagy számú ilyen hívás esetén a mérési lefedettség több Azure-régióra is terjed.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Egy adott számra korlátozom a mérések számát?

A mérés JavaScripte a weblapba van beágyazva, és teljes körűen szabályozható, hogy mikor kezdje el és mikor ne használja. Amíg a Traffic Manager kap egy kérést a mérhető Azure-régiók listájára, régiók egy halmaza lesz visszaadva.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Láthatom az ügyfélalkalmazás által végzett méréseket a valós felhasználómérés?

Mivel a mérési logikát az ügyfélalkalmazásból futtatja, teljes mértékben ön tudja szabályozni, mi történik, beleértve a késési méréseket is. Traffic Manager nem ad összesített nézetet az előfizetéséhez csatolt kulcs alatt kapott mérésekről.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Módosítanom kell a mérési szkriptet, amelyet a Traffic Manager?

Bár Ön szabályozhatja, hogy mi legyen beágyazva a weblapra, erősen ajánlott a mérési szkript módosításainak a szabályozása a késések helyes mérésének és jelentésének biztosítása érdekében.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Mások is láthatják a kulcsokat, amit a valós felhasználómérés?

Amikor beágyazza a mérési szkriptet egy weblapra, mások is láthatják majd a szkriptet és a valós felhasználómérés kulcsot. Fontos azonban tudni, hogy ez a kulcs különbözik az előfizetés-azonosítótól, és azt az Traffic Manager, hogy csak erre a célra legyen használva. A KULCS ismerete nem fogja veszélyeztetni az Azure-fiók biztonságát.

### <a name="can-others-abuse-my-rum-key"></a>Visszaélnek mások a SAJÁT KEY-kulcsommal?

Bár mások is használhatnak az Ön kulcsát arra, hogy helytelen információkat küldjenek az Azure-ba, néhány helytelen mérés nem módosítja az útválasztást, mivel a többi méréssel együtt ezt is figyelembe veszi. Ha módosítania kell a kulcsokat, újra létrehozhatja a kulcsot, amikor a régi kulcs el lesz vetve.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Minden weblapomon el kell végeznem a mérés JavaScript-kódot?

valós felhasználómérés a mérések számának növekedésével több értéket nyújt. Miután említette, ön dönti el, hogy az összes weblapon vagy csak néhány oldalon kell-e azokat eltennünk. Javasoljuk, hogy kezdje azzal, hogy a leglátogatottabb oldalon teszi azt meg, ahol a felhasználónak öt másodperc vagy több másodpercig ezen az oldalon kell maradnia.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Azonosítják a végfelhasználókkal kapcsolatos információkat a Traffic Manager, ha valós felhasználómérés?

A megadott mérés JavaScript használata esetén a Traffic Manager látni fogja a végfelhasználó ügyfél IP-címét és az ő által használt helyi DNS-feloldó forrás IP-címét. Traffic Manager a csonkolt ügyfél IP-címét használja, hogy ne tudja azonosítani a méréseket elküldő végfelhasználót.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A mérő weblapnak valós felhasználómérés kell-e Traffic Manager az útválasztáshoz?

Nem, nem kell a Traffic Manager. A Traffic Manager útvonaltervezési oldala a Valós felhasználómérés résztől elkülönítve működik, és bár jó ötlet, ha mindkettő ugyanabban a webes tulajdonságban van, nem szükséges.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Szükségem van bármilyen szolgáltatásra az Azure-régiókban ahhoz, hogy a valós felhasználómérés?

Nem, az Azure-ban nem kell kiszolgálóoldali összetevőt valós felhasználómérés működnie. A Mérés JavaScripttel letöltött egyetlen képpontos képet és a különböző Azure-régiókban futó szolgáltatást az Azure üzemelteti és kezeli. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Növekedni fog az Azure-sávszélesség kihasználtsága, amikor valós felhasználómérés?

Ahogy az előző válaszban is említettük, a szolgáltatások kiszolgálóoldali összetevőit valós felhasználómérés Azure birtokolja és kezeli. Ez azt jelenti, hogy az Azure sávszélesség-használata nem fog növekedni, mert a valós felhasználómérés. Ez nem vonatkozik az Azure díján kívüli sávszélesség-használatra. Minimálisra csökkentjük a felhasznált sávszélességet, ha csak egy képpontos képet töltünk le, hogy megmérték a késést egy Azure-régióba. 

## <a name="traffic-view"></a>Forgalomnézet

### <a name="what-does-traffic-view-do"></a>Mi a forgalomáttekintő?

forgalomáttekintő a Traffic Manager funkciója, amely segít jobban megérteni a felhasználókat és a felhasználói élményt. A szolgáltatás által fogadott lekérdezéseket Traffic Manager és a szolgáltatás által karbantartott hálózati késésiintelligencia-táblákat használja a következők érdekében:

- Azok a régiók, ahonnan a felhasználók csatlakoznak az Azure-beli végpontjaihoz.
- Az ezekről a régiókról csatlakozó felhasználók mennyisége.
- Azok az Azure-régiók, amelyekbe a rendszer átküldi őket.
- Késési élményük ezekben az Azure-régiókban.

Ezek az információk a portálon a földrajzi térkép átfedésével és táblázatos nézetekkel is használhatók, amellett, hogy nyers adatokként is elérhetők a letöltéshez.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Milyen előnyöket élveznek a forgalomáttekintő?

forgalomáttekintő áttekintést nyújt a profilja által kapott Traffic Manager forgalomról. Elsősorban arra használható, hogy megértse, honnan csatlakozik a felhasználói bázis, és ugyanilyen fontos az átlagos késési élmény. Ezt az információt felhasználhatja olyan területek kereséséhez, amelyekre önnek kell összpontosítania, például ha kiterjeszti az Azure-beli memóriaigényét egy olyan régióra, amely kisebb késéssel képes kiszolgálni ezeket a felhasználókat. Egy másik, a forgalomáttekintő elemzésből származó elemzés a különböző régiókba való forgalom mintáira mutat, amelyek segítségével döntéseket hozhat az ilyen régiókban való növekedésről vagy csökkenésről.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Miben különbözik forgalomáttekintő az Azure Monitor Traffic Manager elérhető metrikáktól?

Azure Monitor a profilja és végpontjai által fogadott forgalom összesítési szintjén is megértheti. Azt is lehetővé teszi, hogy nyomon kövesse a végpontok állapotát az állapot-ellenőrzés eredményeinek felfedése révén. Ha ezeken túl kell mennie, és meg kell értenie, hogy a végfelhasználó hogyan csatlakozik az Azure-hoz regionális szinten, forgalomáttekintő a segítségével.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Használ forgalomáttekintő EDNS-ügyfél alhálózatának adatait?

A lekérdezések által kiszolgált DNS Azure Traffic Manager figyelembe veszi az ECS-információkat az útválasztás pontosságának növelése érdekében. Ha azonban olyan adatkészletet hoz létre, amely megmutatja, hogy a felhasználók honnan csatlakoznak, a forgalomáttekintő csak a DNS-feloldó IP-címét használja.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hány nap adatot használ forgalomáttekintő?

forgalomáttekintő a kimenetét az azt megelőző hét nap adatainak feldolgozásával hozza létre, amikor Ön megtekinti őket. Ez egy mozgó ablak, és a rendszer a legújabb adatokat használja fel minden alkalommal, amikor felkeresi.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hogyan kezeli forgalomáttekintő végpontokat?

Ha azure-régiókon kívül üzemeltetett külső végpontokat használ egy Traffic Manager-profilban, kiválaszthatja, hogy olyan Azure-régióra legyen leképezve, amely a késési jellemzőinek proxyja (ez valójában teljesítmény-útválasztási módszer használata esetén szükséges). Ha rendelkezik ezzel az Azure-régióleképezéssel, akkor a rendszer az Azure-régió késési metrikákat használja a forgalomáttekintő létrehozásakor. Ha nem ad meg Azure-régiót, a késési információk üresek lesznek a külső végpontok adataiban.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Engedélyeznem kell a forgalomáttekintő az előfizetésem egyes profiljaihoz?

Az előzetes verzió időtartama alatt forgalomáttekintő előfizetési szinten volt engedélyezve. Az általános rendelkezésre állás előtt elérhetővé tett fejlesztések részeként mostantól profilszinten engedélyezheti a forgalomáttekintő, így részletesebben engedélyezheti ezt a funkciót. Alapértelmezés szerint a forgalomáttekintő le lesz tiltva a profilhoz.

>[!NOTE]
>Ha az forgalomáttekintő előfizetési szinten engedélyezte az előfizetést az előzetes verzió ideje alatt, most újra engedélyeznie kell azt az előfizetés minden egyes profilja számára.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hogyan kapcsolhatja ki a forgalomáttekintő?

A portál vagy forgalomáttekintő bármelyik profilhoz kikapcsolhatja a REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Hogyan működik forgalomáttekintő számlázás?

forgalomáttekintő díjszabás a kimenet létrehozásához használt adatpontok számán alapul. Jelenleg az egyetlen támogatott adattípus a profil által kapott lekérdezések. Emellett csak akkor kell fizetni a feldolgozásért, amikor az forgalomáttekintő van. Ez azt jelenti, hogy ha engedélyezi a forgalomáttekintő egy hónap egy adott időszakában, és más időpontban kikapcsolja, akkor csak azok az adatpontok számítanak bele a számlába, amelyek akkor voltak feldolgozva, amikor a funkció engedélyezve volt.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-végpontok

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Használhatom a Traffic Manager több előfizetésből származó végpontokkal?

Az Azure-beli virtuális Web Apps. Az Azure Web Apps megköveteli, hogy a Web Apps használt egyéni tartományneveket csak egyetlen előfizetésen belül használja a rendszer. Az azonos tartománynévvel rendelkező Web Apps előfizetésből származó előfizetések nem használhatók.

Más végponttípusok esetében több előfizetésből származó Traffic Manager is használhatók. A Resource Manager előfizetésből származó végpontok hozzáadhatóak a Traffic Manager-hoz, ha a Traffic Manager-profilt konfiguráló személy olvasási hozzáféréssel rendelkezik a végponthoz. Ezek az engedélyek az [Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) adhatók meg.](../role-based-access-control/role-assignments-portal.md) Más előfizetésből származó végpontok az azure [Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) az Azure CLI használatával [is hozzáadhatóak.](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create)

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Használhatom a Traffic Manager a Cloud Service előkészítési tárolóhelyekkel?

Igen. A Cloud Service "előkészítési" tárolóhelyei a Traffic Manager végpontokként konfigurálhatóak. Az állapot-ellenőrzésekért továbbra is az Azure-végpontok díjszabása alapján kell fizetni.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Támogatja Traffic Manager IPv6-végpontokat?

Traffic Manager jelenleg nem biztosít IPv6-címezhető névkiszolgálókat. A Traffic Manager IPv6-végponthoz csatlakozó IPv6-ügyfelek azonban továbbra is használhatók. Az ügyfél nem továbbít dns-kéréseket közvetlenül a Traffic Manager. Ehelyett az ügyfél rekurzív DNS-szolgáltatást használ. A csak IPv6-alapú ügyfelek IPv6-on keresztül küldik a kéréseket a rekurzív DNS-szolgáltatásnak. Ezután a rekurzív szolgáltatásnak képesnek kell lennie az IPv4-Traffic Manager kapcsolatfelvételre a hálózati névkiszolgálókkal.

Traffic Manager válaszol a végpont DNS-nevével vagy IP-címével. Az IPv6-végpontok támogatásához két lehetőség áll rendelkezésre. A végpontot hozzáadhatja DNS-névként, amely rendelkezik társított AAAA-rekordtal, és a Traffic Manager ellenőrzi a végpont állapotát, és CNAME rekordtípusként adja vissza a lekérdezési válaszban. Ezt a végpontot közvetlenül is hozzáadhatja az IPv6-cím használatával, és Traffic Manager AAAA típusú rekordot fog visszaadni a lekérdezési válaszban.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Használhatok több Traffic Manager webalkalmazással ugyanabban a régióban?

A Traffic Manager általában a különböző régiókban üzembe helyezett alkalmazásokhoz irányítják a forgalmat. Ugyanakkor akkor is használható, ha egy alkalmazás több üzemelő példánysal rendelkezik ugyanabban a régióban. Az Traffic Manager Azure-végpontok nem teszik lehetővé, hogy egynél több webalkalmazás-végpontot hozzáadnak ugyanabba az Azure-régióba ugyanabba a Traffic Manager profilba.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Hogyan áthelyezni Traffic Manager-profilom Azure-végpontjait egy másik erőforráscsoportba vagy előfizetésbe?

A profilhoz társított Azure-Traffic Manager erőforrás-Traffic Manager erőforrás-számaik alapján követi a rendszer. Ha egy végpontként (például nyilvános IP-cím, klasszikus felhőszolgáltatás, WebApp vagy más beágyazott módon használt Traffic Manager-profil) végpontként használt Azure-erőforrást áthelyez egy másik erőforráscsoportba vagy előfizetésbe, az erőforrás-azonosítója megváltozik. Ebben a forgatókönyvben jelenleg úgy kell frissítenie a Traffic Manager, hogy először törli, majd hozzáadja a végpontokat a profilhoz.

## <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager-végpontmonitorozás

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Rugalmas Traffic Manager az Azure-régiók meghibásodásai esetén?

Traffic Manager a magas rendelkezésre álló alkalmazások Azure-beli teljesítésének kulcsfontosságú összetevője.
A magas rendelkezésre állás érdekében a Traffic Manager rendkívül magas rendelkezésre állásúnak kell lennie, és rugalmasnak kell lennie a regionális meghibásodásokkal szemben.

Az összetevők Traffic Manager bármely Azure-régió teljes meghibásodása esetén rugalmasak. Ez a rugalmasság minden Traffic Manager összetevőre vonatkozik: a DNS-névkiszolgálókra, az API-re, a tárolási rétegre és a végpontfigyelési szolgáltatásra.

Abban a valószínűtlen esetben, ha egy teljes Azure-régió kiesése Traffic Manager, a rendszer várhatóan továbbra is a megszokott módon fog működni. A több Azure-régióban üzembe helyezett alkalmazások Traffic Manager, hogy a forgalmat az alkalmazásuk egy elérhető példányához irányítják.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Hogyan befolyásolja a választott erőforráscsoport-hely Traffic Manager?

Traffic Manager egyetlen globális szolgáltatás. Nem regionális. Az erőforráscsoport helyének megválasztása nem Traffic Manager az adott erőforráscsoportban üzembe helyezett profilokat.

Azure Resource Manager erőforráscsoportnak meg kell adnia egy helyet, amely meghatározza az adott erőforráscsoportban üzembe helyezett erőforrások alapértelmezett helyét. Amikor létrehoz egy Traffic Manager profilt, az egy erőforráscsoportban jön létre. Minden Traffic Manager profil **globális** helyet használ, felülírva az erőforráscsoport alapértelmezett helyét.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hogyan az egyes végpontok aktuális állapotát?

Az egyes végpontok aktuális figyelési állapota a teljes profil mellett a következő Azure Portal. Ezek az információk a Traffic [Monitor](/rest/api/trafficmanager/)REST API, [a PowerShell-parancsmagok](/powershell/module/az.trafficmanager)és a platformfüggetlen Azure CLI használatával is [elérhetők.](/cli/azure/install-classic-cli)

A végpontok állapotát Azure Monitor a végpontok állapotának nyomon követéséhez és vizuális ábrázolásukhoz is használhatja. A szolgáltatás használatával kapcsolatos további Azure Monitor az [Azure Monitoring dokumentációjában.](../azure-monitor/data-platform.md)

### <a name="can-i-monitor-https-endpoints"></a>Figyelhetim a HTTPS-végpontokat?

Igen. Traffic Manager támogatja a HTTPS-protokollon keresztüli hitelesítést. Konfigurálja **a HTTPS** protokollt a figyelési konfigurációban.

A Traffic Manager nem tud tanúsítványérvényesítést biztosítani, beleértve a következőket:

* A kiszolgálóoldali tanúsítványok nincsenek ellenőrizve
* A kiszolgálóoldali SNI-tanúsítványok nincsenek érvényesítve
* Az ügyféltanúsítványok nem támogatottak

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Használhatok IP-címet vagy DNS-nevet végpont hozzáadásakor?

Traffic Manager a végpontok három különböző módon való hivatkozását támogatja – DNS-névként, IPv4-címként és IPv6-címként. Ha a végpontot IPv4- vagy IPv6-címként adja hozzá, a lekérdezés válasza A vagy AAAA típusú lesz. Ha a végpont DNS-névként lett hozzáadva, akkor a lekérdezési válasz CNAME típusú rekord lesz. A végpontok IPv4- vagy IPv6-címként való hozzáadása csak akkor engedélyezett, ha a végpont típusa **Külső.**
A három végpontcímzési típus minden útválasztási módszert és figyelési beállítást támogat.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Milyen típusú IP-címeket használhatok végpontok hozzáadásakor?

Traffic Manager IPv4- vagy IPv6-címek használatával határozhat meg végpontokat. Az alábbiakban felsorolunk néhány korlátozást:

- A fenntartott magánhálózati IP-címterek címeinek megfelelő címek nem engedélyezettek. Ilyen címek például az RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 és RFC 5771
- A cím nem tartalmazhat portszámokat (megadhatja a profilkonfigurációs beállításokban használni kívánt portokat)
- Egy profil két végpontja sem lehet ugyanazokkal a cél IP-címmel

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Használhatok különböző végpontcímzési típusokat egyetlen profilon belül?

Nem, Traffic Manager nem engedélyezi a végpontcímzési típusok egy profilon belüli keverését, kivéve a MultiValue útválasztási típusú profilok esetében, ahol az IPv4- és IPv6-címzési típusokat vegyesen használhatja

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Mi történik, ha egy bejövő lekérdezés rekordtípusa eltér a végpontok címzési típusával társított rekordtípustól?

Amikor lekérdezés érkezik egy profilra, a Traffic Manager először megkeresi a végpontot, amely a megadott útválasztási módszernek és a végpontok állapotának megfelelően lesz visszaadva. Ezután a bejövő lekérdezésben kért rekordtípust és a végponthoz társított rekordtípust, mielőtt választ ad vissza az alábbi táblázat alapján.

A MultiValue-tól különböző útválasztási módú profilok esetén:

|Bejövő lekérdezési kérelem|     Végpont típusa|     Megadott válasz|
|--|--|--|
|ANY (BÁRMELY) |    A / AAAA / CNAME |    Célvégpont| 
|A |    A / CNAME |    Célvégpont|
|A |    AAAA |    NODATA |
|AAAA |    AAAA / CNAME |    Célvégpont|
|AAAA |    A |    NODATA |
|CNAME |    CNAME |    Célvégpont|
|CNAME     |A/ AAAA |    NODATA |
|

MultiValue értékű útválasztási módú profilok esetén:

|Bejövő lekérdezési kérelem|     Végpont típusa |    Megadott válasz|
|--|--|--|
|ANY (BÁRMELY) |    Az A és az AAAA vegyesen |    Célvégpont|
|A |    Az A és az AAAA vegyesen |    Csak A típusú célvégpontokat|
|AAAA    |Az A és az AAAA vegyesen|     Csak AAAA típusú célvégpontokat|
|CNAME |    Az A és az AAAA vegyesen |    NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Használhatok IPv4-/IPv6-címekkel címzett végpontokkal egy beágyazott profilban egy profilt?

Igen, azzal a kivétellel, hogy a MultiValue típusú profil nem lehet szülőprofil egy beágyazott profilkészletben.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Leállítottam egy webalkalmazás-végpontot a Traffic Manager profilban, de az újraindítás után sem kapok forgalmat. Hogyan javítható ez?

Amikor egy Azure-webalkalmazás végpontja leáll Traffic Manager leállítja annak állapotát, és csak akkor indítja újra az állapotellenőrzéseket, ha azt észleli, hogy a végpont újraindult. A késés elkerülése érdekében a végpont újraindítása után tiltsa le, majd Traffic Manager a végpontot a profilban.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Használhatom a Traffic Manager akkor is, ha az alkalmazásom nem támogatja a HTTP-t vagy a HTTPS-t?

Igen. Monitorozási protokollként megadhatja a TCP protokollt, Traffic Manager kezdeményezhet TCP-kapcsolatot, és megvárhatja a végpont válaszát. Ha a végpont válaszként válaszol a kapcsolat létrehozására adott válaszra az időkorláton belül, akkor a végpont kifogástalan állapotúként lesz megjelölve.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Milyen konkrét válaszokra van szükség a végponttól a TCP-figyelés használata esetén?

TCP-figyelés használata esetén a Traffic Manager egy háromútos TCP-kézfogást kezd azáltal, hogy SYN-kérést küld a végpontnak a megadott porton. Ezután egy ideig vár a végponttól származó SYN-ACK-válaszra (az időtúllépési beállításokban megadva).

- Ha a SYN-ACK válasz a figyelési beállításokban megadott időtúllépési időtartamon belül érkezik, akkor a végpont kifogástalannak minősül. A FIN vagy FIN-ACK a várt válasz a szoftvercsatornától Traffic Manager amikor rendszeresen megszakít egy szoftvercsatornát.
- Ha SYN-ACK-válasz érkezik a megadott időtúllépés után, a Traffic Manager RST-val válaszol a kapcsolat alaphelyzetbe állításával.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Milyen gyorsan Traffic Manager áthelyezni a felhasználókat egy nem megfelelő végpontról?

Traffic Manager több olyan beállítást is biztosít, amelyek segítségével szabályozhatja a Traffic Manager profil feladatátvételi viselkedését:

- A mintavételi időköz 10 másodpercre Traffic Manager meg, hogy a rendszer gyakrabban mintavétele a végpontokat. Ez biztosítja, hogy a rendszer a lehető leghamarabb észlelni tudja a nem megfelelő végpontokat. 
- Megadhatja, hogy mennyi ideig várjon egy állapotellenőrzési kérelem időkorrekta előtt (az időkorrekta minimális értéke 5 mp).
- Megadhatja, hogy hány hiba következhet be, mielőtt a végpont nem megfelelőként lenne megjelölve. Ez az érték akár 0-s is lehet, amely esetben a végpont nem megfelelőként lesz megjelölve, amint az első állapot-ellenőrzés meghiúsul. Ha azonban a minimális 0 értéket használja a megengedett számú hiba esetén, az a végpontok rotációból való kiesését okozhatja az esetleges átmeneti problémák miatt, amelyek az vizsgálat során léphetnek fel.
- Megadhatja, hogy a DNS-válaszhoz szükséges idő (TTL) akár 0 is lehet. Ez azt jelenti, hogy a DNS-feloldók nem tudják gyorsítótárazza a választ, és minden új lekérdezés olyan választ kap, amely tartalmazza a lekérdezés által Traffic Manager legfrissebb információkat.

Ezekkel a beállításokkal a Traffic Manager 10 másodperc alatt biztosíthatja a feladatátvételt, miután egy végpont nem megfelelő, és a rendszer DNS-lekérdezést hoz létre a megfelelő profilon.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hogyan adhatók meg különböző figyelési beállítások a profil különböző végpontjaihoz?

Traffic Manager figyelési beállítások profilszintenként vannak megszűkve. Ha más figyelési beállítást kell használnia csak egy végponthoz, akkor ezt a végpontot beágyazott profilként kell használnia, amelynek figyelési beállításai eltérnek [a](traffic-manager-nested-profiles.md) szülőprofilétól.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hogyan rendelhetek HTTP-fejléceket a Traffic Manager állapot-ellenőrzéséhez a végpontjaimhoz?

Traffic Manager lehetővé teszi egyéni fejlécek megadását a HTTP(S) állapotellenőrzései során, amelyek a végpontokra kezdeményeznek. Ha egyéni fejlécet szeretne megadni, azt a profil szintjén (minden végpontra érvényes), vagy a végpont szintjén adhatja meg. Ha a fejléc mindkét szinten definiálva van, akkor a végpont szintjén megadott fejléc felülírja az 1. profilszintet.
Ennek egyik gyakori esete az állomásfejlécek megadása, hogy Traffic Manager kérések megfelelően, egy több-bérlős környezetben üzemeltetett végpontra irányítva. Egy másik ilyen eset a végpontOK HTTP Traffic Manager(S) kérelemnaplóiból származó kérések azonosítása

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Milyen állomásfejlécet használ a végponti állapot ellenőrzése?

Ha nem ad meg egyéni állomásfejléc-beállítást, a Traffic Manager által használt állomásfejléc a profilban konfigurált végpontcél DNS-neve, ha van ilyen.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Milyen IP-címekről származnak az állapotellenőrzések?

Kattintson [ide,](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) hogy megtudja, hogyan lehet lekérni azon IP-címek listáját, amelyekről Traffic Manager állapot-ellenőrzések származnak. A legújabb REST API az Azure CLI-Azure PowerShell is lekérheti. Tekintse át a felsorolt IP-címeket, és győződjön meg arról, hogy az ezekről az IP-címekről érkező bejövő kapcsolatok számára engedélyezett a végpontok számára az állapotuk ellenőrzése.

Példa a következő Azure PowerShell:

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus
$result = $serviceTags.Values | Where-Object { $_.Name -eq "AzureTrafficManager" }
$result.Properties.AddressPrefixes
```

> [!NOTE]
> A nyilvános IP-címek értesítés nélkül változhatnak. Győződjön meg arról, hogy a Service Tag Discovery API-val vagy letölthető JSON-fájllal lekéri a legújabb információkat.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hány állapotellenőrzésre számíthatok a végpontomon a Traffic Manager?

A végpontot Traffic Manager állapotellenőrzések száma a következőktől függ:

- a figyelési időközre beállított érték (a kisebb időköz azt jelenti, hogy egy adott időszakban több kérés lesz a végpontra állítva).
- a helyek száma, ahonnan az állapotellenőrzések származnak (azok az IP-címek, ahonnan az ellenőrzések várhatók, szerepel az előző GYIK-ben).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hogyan kaphatok értesítést, ha az egyik végpontom leáll?

A végpontok által megadott Traffic Manager a profil végpontjainak állapota. Ezt a profilon belüli összes végpont összesítéseként láthatja (például a végpontok 75%-a kifogástalan), vagy végpontonként. Traffic Manager metrikák a Azure Monitor-ban megjelenik, és a [](../azure-monitor/alerts/alerts-metric.md) riasztási képességeinek használatával értesítéseket kaphat, ha a végpont állapotában változás történik. További részletekért lásd: Traffic Manager [metrikák és riasztások megtekintése.](traffic-manager-metrics-alerts.md)  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager profilok létrehozása

### <a name="how-do-i-configure-nested-profiles"></a>Hogyan konfigurálja a beágyazott profilokat?

A beágyazott Traffic Manager profilok a Azure Resource Manager és a klasszikus Azure REST API-k, az Azure PowerShell-parancsmagok és a platformfüggetlen Azure CLI-parancsok használatával konfigurálhatóak. Ezek az új Azure Portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hány beágyazó réteget támogat a Traffic Manger?

A profilokat legfeljebb 10 szint mélyére ágyazhatja be. A "hurkok" nem engedélyezettek.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Keverni lehet más végponttípusokat beágyazott gyermekprofilokkal, ugyanabban a Traffic Manager profilban?

Igen. A különböző típusú végpontok egy profilon belüli egyesítésének nincs korlátozása.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hogyan vonatkozik a számlázási modell a beágyazott profilokra?

A beágyazott profilok használata nincs negatív hatással a díjszabásra.

Traffic Manager számlázás két összetevőből áll: végpont állapot-ellenőrzéseiből és több millió DNS-lekérdezésből

* Végpont állapotának ellenőrzése: A gyermekprofilok díjmentesen használhatók, ha végpontként vannak konfigurálva a szülőprofilban. A gyermekprofilban a végpontok figyelése a szokásos módon van kiszámlázva.
* DNS-lekérdezések: A rendszer minden lekérdezést csak egyszer számol. A gyermekprofilból végpontot visszaő szülőprofilra adott lekérdezések csak a szülőprofilba számítanak bele.

További részletekért tekintse meg a [Traffic Manager oldalon.](https://azure.microsoft.com/pricing/details/traffic-manager/)

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Van hatással a beágyazott profilok teljesítményére?

Nem. Beágyazott profilok használata nem befolyásolja a teljesítményt.

A Traffic Manager dns-lekérdezések feldolgozásakor a névkiszolgálók belsőleg haladnak át a profilhierarchián. A szülőprofilra való DNS-lekérdezés egy végponttal kapott DNS-választ kaphat egy gyermekprofiltól. A rendszer egyetlen CNAME rekordot használ, függetlenül attól, hogy egyetlen profilt vagy beágyazott profilt használ. A hierarchiában lévő minden profilhoz nem szükséges CNAME rekordot létrehozni.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hogyan számítja Traffic Manager egy szülőprofil beágyazott végpontjának állapotát?

A szülőprofil nem végez közvetlenül állapotellenőrzéseket a gyermeken. Ehelyett a gyermekprofil végpontjainak állapota alapján számítja ki a gyermekprofil általános állapotát. Ezek az információk a beágyazott profilhierarchiában propagálva határozzák meg a beágyazott végpont állapotát. A szülőprofil ezt az összesített állapot alapján határozza meg, hogy a forgalom irányítható-e a gyermekhez.

Az alábbi táblázat a beágyazott végpontok Traffic Manager állapotának ellenőrzését ismerteti.

| Gyermekprofil-figyelő állapota | Szülővégpont-figyelő állapota | Jegyzetek |
| --- | --- | --- |
| Letiltva. A gyermekprofil le van tiltva. |Leállítva |A szülővégpont állapota Leállítva, nem Letiltva. A Letiltva állapot fenntartva, ami azt jelzi, hogy letiltotta a végpontot a szülőprofilban. |
| Leromlott. Legalább egy gyermekprofil-végpont Csökkentett teljesítményű állapotban van. |Online: a gyermekprofilban legalább a Min ChildEndpoints értéke online végpontok száma.<BR>CheckingEndpoint: a gyermekprofilban az Online és a CheckingEndpoint végpontok száma legalább a Min ChildEndpoints értéke.<BR>Csökkentett teljesítményű: egyébként. |A forgalom a CheckingEndpoint állapot végpontjára van irányítva. Ha a MinChildEndpoints túl magasra van állítva, a végpont mindig csökkentett teljesítményű lesz. |
| Online. Legalább egy gyermekprofil-végpont Online állapotban van. Nincs csökkentett teljesítményű végpont. |Lásd fent. | |
| Ellenőrzőpontok. Legalább egy gyermekprofil-végpont a "CheckingEndpoint". Nincs "Online" vagy "Csökkentett teljesítményű" végpont |Lásd fentebb. | |
| Inaktív. Minden gyermekprofil-végpont Le van tiltva vagy Leállítva, vagy ez a profil nem rendelkezik végpontokkal. |Leállítva | |

## <a name="next-steps"></a>Következő lépések:

- További információ a Traffic Manager [monitorozásról és az automatikus feladatátvételről.](../traffic-manager/traffic-manager-monitoring.md)
- További információ a Traffic Manager [útválasztási módszereiről.](../traffic-manager/traffic-manager-routing-methods.md)