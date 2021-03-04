---
title: Azure Traffic Manager – gyakori kérdések
description: Ez a cikk a Traffic Managerekkel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
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
ms.openlocfilehash: 163436ad82ea6f5067ad41b7fdd7e315db6dc29a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095017"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager gyakori kérdések (GYIK)

## <a name="traffic-manager-basics"></a>Traffic Manager alapjai

### <a name="what-ip-address-does-traffic-manager-use"></a>Milyen IP-címet Traffic Manager használni?

Ahogy azt a [Traffic Manager működése című témakör](../traffic-manager/traffic-manager-how-it-works.md)ismerteti, Traffic Manager a DNS-szinten működik. DNS-válaszokat küld arra, hogy a megfelelő szolgáltatási végpontra irányítsa az ügyfeleket. Az ügyfelek ezután közvetlenül csatlakoznak a szolgáltatás-végponthoz, nem a Traffic Manageron keresztül.

Ezért Traffic Manager nem biztosít végpontot vagy IP-címet ahhoz, hogy az ügyfelek csatlakozni tudjanak. Ha statikus IP-címet szeretne használni a szolgáltatáshoz, azt a szolgáltatásban kell konfigurálni, nem Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Milyen típusú forgalmat lehet irányítani a Traffic Manager használatával?
Ahogy azt a [Traffic Manager működése című témakör](../traffic-manager/traffic-manager-how-it-works.md)ismerteti, a Traffic Manager végpont bármely, az Azure-on belül vagy kívül üzemeltetett internetkapcsolattal rendelkező szolgáltatás lehet. Ezért Traffic Manager a nyilvános internetről érkező forgalmat olyan végpontokra irányíthatja, amelyek szintén internetkapcsolattal rendelkeznek. Ha olyan végpontokkal rendelkezik, amelyek egy magánhálózaton belül találhatók (például [Azure Load Balancer](../load-balancer/components.md#frontend-ip-configurations)belső verziója), vagy ha a felhasználók DNS-kérelmeket tesznek elérhetővé az ilyen belső hálózatokból, akkor nem használhatja a forgalmat a Traffic Manager.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager támogatja a "Sticky" munkameneteket?

Ahogy azt a [Traffic Manager működése című témakör](../traffic-manager/traffic-manager-how-it-works.md)ismerteti, Traffic Manager a DNS-szinten működik. DNS-válaszokat használ arra, hogy az ügyfeleket a megfelelő szolgáltatási végpontra irányítsa. Az ügyfelek közvetlenül csatlakoznak a szolgáltatás-végponthoz, nem a Traffic Manageron keresztül. Ezért a Traffic Manager nem látja a HTTP-forgalmat az ügyfél és a kiszolgáló között.

Továbbá a Traffic Manager által fogadott DNS-lekérdezés forrás IP-címe a rekurzív DNS szolgáltatáshoz tartozik, nem az ügyfélhez. Ezért Traffic Manager az egyes ügyfeleket nem lehet nyomon követni, és nem tudja megvalósítani a "Sticky" munkameneteket. Ez a korlátozás általános az összes DNS-alapú forgalomirányítási rendszer esetében, és nem kifejezetten a Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Miért látok HTTP-hibát a Traffic Manager használatakor?

Ahogy azt a [Traffic Manager működése című témakör](../traffic-manager/traffic-manager-how-it-works.md)ismerteti, Traffic Manager a DNS-szinten működik. DNS-válaszokat használ arra, hogy az ügyfeleket a megfelelő szolgáltatási végpontra irányítsa. Az ügyfelek ezután közvetlenül csatlakoznak a szolgáltatás-végponthoz, nem a Traffic Manageron keresztül. A Traffic Manager nem látja a HTTP-forgalmat az ügyfél és a kiszolgáló között. Ezért az Ön által megjelenített HTTP-hibáknak az alkalmazásból kell származnia. Ahhoz, hogy az ügyfél csatlakozni tudjanak az alkalmazáshoz, az összes DNS-feloldási lépés befejeződött. Ebbe beletartozik minden olyan interakció, amely Traffic Manager az alkalmazás forgalmának folyamatán.

A további vizsgálatnak ezért az alkalmazásra kell összpontosítania.

Az ügyfél böngészőjéből eljuttatott HTTP-állomásfejléc a leggyakoribb problémák forrása. Győződjön meg arról, hogy az alkalmazás úgy van konfigurálva, hogy fogadja el a megfelelő állomásfejléc-fejlécet a használt tartománynévhez. A Azure App Servicet használó végpontok esetében lásd: [Egyéni tartománynév konfigurálása webalkalmazáshoz a Azure app Service a Traffic Manager használatával](../app-service/configure-domain-traffic-manager.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Milyen hatással van a Traffic Manager használatának teljesítményére?

Ahogy azt a [Traffic Manager működése című témakör](../traffic-manager/traffic-manager-how-it-works.md)ismerteti, Traffic Manager a DNS-szinten működik. Mivel az ügyfelek közvetlenül a szolgáltatási végpontokhoz csatlakoznak, a kapcsolat létrejötte után a Traffic Manager használatakor nincs hatással a teljesítményre.

Mivel a Traffic Manager DNS-szinten integrálódik az alkalmazásokkal, a DNS-feloldási láncba való beillesztéshez további DNS-névlekérdezésre van szükség. A Traffic Manager DNS-feloldási időpontra gyakorolt hatása minimális. A Traffic Manager a névkiszolgálók globális hálózatát használja, és a teljes képernyős [hálózatkezelést](https://en.wikipedia.org/wiki/Anycast) használja annak biztosítására, hogy a DNS-lekérdezések mindig a legközelebbi elérhető névkiszolgálók felé legyenek irányítva. A DNS-válaszok gyorsítótárazása emellett azt is jelenti, hogy a Traffic Manager használatával felmerülő további DNS-késés csak a munkamenetek töredékére vonatkozik.

A teljesítmény metódus a legközelebbi elérhető végpontra irányítja a forgalmat. A nettó eredmény az, hogy az ehhez a metódushoz társított általános teljesítményre gyakorolt hatásnak minimálisnak kell lennie. A DNS-késés növelését a végponthoz képest alacsonyabb hálózati késéssel kell ellensúlyozni.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Milyen alkalmazás-protokollokat használhatok a Traffic Manager?

Ahogy azt a [Traffic Manager működése című témakör](../traffic-manager/traffic-manager-how-it-works.md)ismerteti, Traffic Manager a DNS-szinten működik. A DNS-címkeresés után az ügyfelek közvetlenül, nem pedig Traffic Manageron keresztül csatlakoznak az alkalmazás-végponthoz. Ezért a kapcsolat bármely alkalmazási protokollt használhat. Ha a TCP-t a figyelési protokollként választja, akkor a Traffic Manager végponti állapotának figyelése bármely alkalmazási protokoll használata nélkül végezhető el. Ha úgy dönt, hogy az állapotot egy Application Protocol használatával ellenőrizte, a végpontnak képesnek kell lennie arra, hogy válaszoljon a HTTP vagy a HTTPS GET kérelmekre.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Használhatom a Traffic Managert "Naked" tartománynévvel?

Igen. Ha meg szeretné tudni, hogyan hozhat létre alias-rekordot a tartománynév csúcsához egy Azure Traffic Manager-profilra való hivatkozáshoz, tekintse meg [az alias-rekord konfigurálása a APEX-tartománynevek támogatásához a Traffic Manager](../dns/tutorial-alias-tm.md)használatával című témakört.

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>A DNS-lekérdezések kezelésekor az ügyfél alhálózati címének Traffic Manager kell lennie? 

Igen, az általa kapott DNS-lekérdezés forrás IP-címén kívül (amely általában a DNS-feloldó IP-címe), amikor a földrajzi, a teljesítmény-és az alhálózat-útválasztási módszerekhez keres keresést, a Traffic Manager az ügyfél alhálózati címét is figyelembe veszi, ha a feloldó lekérdezést küld a kérelemnek a végfelhasználó nevében.  
Pontosabban, [RFC 7871 – ügyfél-alhálózat a DNS-lekérdezésekben](https://tools.ietf.org/html/rfc7871) , amelyek a [DNS (EDNS0) bővítményi mechanizmust](https://tools.ietf.org/html/rfc2671) biztosítanak az ügyfél alhálózati címére az azt támogató feloldók alapján.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Mi a DNS-élettartam, és hogyan befolyásolja a felhasználókat?

Ha egy DNS-lekérdezés Traffic Manager, akkor a válasz a time-to-Live (TTL) nevű választ adja vissza. Ez az érték, amelynek az egysége másodpercben van, azt jelzi, hogy a DNS-feloldók a válaszok gyorsítótárazásának időtartamára vonatkoznak. Habár a DNS-feloldók nem garantálják ennek az eredménynek a gyorsítótárazását, a gyorsítótárazás lehetővé teszi, hogy az Traffic Manager DNS-kiszolgálók helyett a gyorsítótárból érkező további lekérdezésekre válaszoljanak. Ez a következőképpen befolyásolja a válaszokat:

- a magasabb élettartam csökkenti a Traffic Manager DNS-kiszolgálókon lévő lekérdezések számát, ami csökkentheti az ügyfél költségeit, mivel a kiszolgált lekérdezések száma számlázandó használat.
- a magasabb TTL-érték csökkentheti a DNS-címkeresés elvégzéséhez szükséges időt.
- a magasabb élettartam azt is jelenti, hogy az adatok nem tükrözik azokat a legfrissebb állapot-információkat, amelyeket a Traffic Manager a szondázás-ügynökökön keresztül szereztek be.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hogyan állítható be a TTL a Traffic Manager-válaszok esetében?

Beállíthatja, hogy a DNS ÉLETTARTAMa legfeljebb 0 másodperc legyen, és 2 147 483 647 másodpercnél nagyobb legyen (az [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )szabványnak megfelelő maximális tartomány). A 0 ÉLETTARTAMa azt jelenti, hogy az alárendelt DNS-feloldók nem gyorsítótárazzák a lekérdezési válaszokat, és az összes lekérdezésnek el kell érnie a Traffic Manager DNS-kiszolgálókat a feloldáshoz.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hogyan tudom megismerni a profilhoz tartozó lekérdezések mennyiségét? 

A Traffic Manager által megadott mérőszámok egyike a profil által válaszolt lekérdezések száma. Ezt az információt a profil szintjének összesítése során, vagy további bontással is megtekintheti, hogy megjelenjenek-e a megadott végpontokat visszaadó lekérdezések mennyisége. Emellett riasztásokat is beállíthat, amelyek értesítik, ha a lekérdezési válasz mennyisége átlépi a beállított feltételeket. További részletekért [Traffic Manager mérőszámokat és riasztásokat](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager földrajzi forgalom útválasztási módszere

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Mik azok a használati esetek, amikor a földrajzi útválasztás hasznos?

A földrajzi útválasztási típus bármely olyan forgatókönyvben használható, amelyben az Azure-ügyfeleknek földrajzi régiók alapján kell Megkülönböztetniük a felhasználókat. Ha például a földrajzi forgalom útválasztási módszerét használja, bizonyos régiókban a felhasználók különböző felhasználói élményt biztosíthatnak, mint a többi régióban. Egy másik példa a helyi adatszuverenitási mandátumok betartására, amelyek megkövetelik, hogy egy adott régióból származó felhasználókat csak az adott régióban lévő végpontok kézbesítsék.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hogyan eldönteni, hogy kell-e használni a teljesítmény-útválasztási módszert vagy a földrajzi útválasztási módszert?

A két népszerű útválasztási módszer közül a legfontosabb különbség az, hogy a teljesítmény-útválasztási módszernél elsődleges célja, hogy forgalmat továbbítson a hívónak, amely a legkisebb késést biztosítja a hívó számára, míg a földrajzi útválasztásban az elsődleges cél egy geo-kerítés kikényszerítve a hívók számára, hogy szándékosan átirányítsa őket egy adott végponthoz. Az átfedés történik, mivel a földrajzi közelség és az alacsonyabb késés között összefüggés van, bár ez nem mindig igaz. Lehet, hogy a végpont egy másik földrajzban van, amely jobb késést biztosít a hívó számára, és ebben az esetben a teljesítmény-útválasztás elküldi a felhasználót az adott végpontnak, de a földrajzi útválasztás mindig a földrajzi régiójuk számára leképezett végpontra küldi el azokat. Annak érdekében, hogy világossá tegyük, vegye figyelembe a következő példát – a földrajzi útválasztással nem gyakori leképezéseket végezhet, például az összes adatforgalmat elküldheti Ázsiából az USA-beli végpontokra, valamint az összes USA-beli forgalmat az ázsiai végpontokra. Ebben az esetben a földrajzi útválasztás szándékosan pontosan azt végzi el, hogy mit konfigurált, és a teljesítmény optimalizálása nem veszi figyelembe. 
>[!NOTE]
>Előfordulhatnak olyan helyzetek, amikor a teljesítményre és a földrajzi útválasztási képességekre is szükség lehet, mert ezek a forgatókönyvek a beágyazott profilok nagyszerű választást jelenthetnek. Beállíthat például egy olyan szülő profilt földrajzi útválasztással, amelyben a Észak-Amerika összes forgalmát egy beágyazott profilba küldi, amely az Egyesült államokbeli végpontokkal rendelkezik, és a teljesítmény-útválasztás használatával továbbítja ezeket a forgalmat az adott készletben lévő legjobb végpontra. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Milyen régiókat támogat a Traffic Manager földrajzi útválasztáshoz?

Az Traffic Manager által használt ország-/régió-hierarchia [itt](traffic-manager-geographic-regions.md)található. Habár ez a lap naprakészen tartja a módosításokat, az [Azure Traffic Manager REST API](/rest/api/trafficmanager/)használatával programozott módon is lekérheti ugyanezeket az adatokat. 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hogyan határozza meg a Traffic Manager, hogy a felhasználók honnan kérdezik le?

Traffic Manager a lekérdezés forrás IP-címét (ez valószínűleg egy helyi DNS-feloldó, amely a felhasználó nevében hajtja végre a lekérdezést), és belső IP-címet használ a régióhoz a hely meghatározásához. Ez a Térkép folyamatosan frissül az Internet változásainak figyelembevétele érdekében. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Garantált, hogy Traffic Manager képes pontosan meghatározni a felhasználó pontos földrajzi helyét minden esetben?

Nem, Traffic Manager nem tudja garantálni, hogy a DNS-lekérdezés forrás IP-címéből kikövetkeztetett földrajzi régió a következő okok miatt mindig megfelel a felhasználó helyének:

- Először is, ahogy azt az előző gyakori kérdések részben leírtak szerint a DNS-feloldó azt látja, hogy a keresés a felhasználó nevében történik. Habár a DNS-feloldó földrajzi helye jó proxy a felhasználó földrajzi helyéhez, a DNS-feloldó szolgáltatás lábnyomának és az ügyfél által használt DNS-feloldó szolgáltatásnak a terhelése is változhat. Egy malajziai ügyfél például megadhatja az eszköz beállításait egy olyan DNS-feloldó szolgáltatás használatával, amelynek a szingapúri DNS-kiszolgálója az adott felhasználóhoz vagy eszközhöz tartozó lekérdezési megoldások kezelésére is alkalmas lehet. Ebben az esetben Traffic Manager csak a Szingapúr helyének megfelelő feloldó IP-címét láthatja. Emellett tekintse meg az ügyfél-alhálózati címek támogatásával kapcsolatos korábbi gyakori kérdéseket ezen a lapon.

- Másodszor, Traffic Manager egy belső térképet használ az IP-cím földrajzi régióba fordításához. A Térkép ellenőrzése és frissítése folyamatosan történik, hogy megnövelje a pontosságot, és figyelembe veszi az Internet változó jellegét, továbbra is fennáll a lehetősége, hogy az adatok nem pontosan az összes IP-cím földrajzi helyének megfelelően legyenek ábrázolva.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>A végpontnak fizikailag ugyanabban a régióban kell lennie, mint a földrajzi útválasztáshoz konfiguráltnak?

Nem, a végpont helye nem korlátozza, hogy mely régiók rendelhetők hozzájuk. Az US-Central Azure-régióban található végpontok például az összes indiai felhasználót átirányítják.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Rendelhetek földrajzi régiókat olyan profilhoz tartozó végpontokhoz, amely nem a földrajzi útválasztásra van konfigurálva?

Igen, ha a profil útválasztási metódusa nem földrajzi, akkor az [Azure Traffic Manager REST API](/rest/api/trafficmanager/) használatával földrajzi régiókat rendelhet hozzá a profilban található végpontokhoz. A nem földrajzi útválasztás típusú profilok esetében ez a konfiguráció figyelmen kívül lesz hagyva. Ha egy későbbi időpontban módosítja az adott profilt földrajzi útválasztási típusra, Traffic Manager használhatja ezeket a leképezéseket.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Miért kapok hibaüzenetet, amikor megpróbálom módosítani egy meglévő profil útválasztási módszerét a földrajzi helyekre?

Egy olyan profilhoz tartozó végpontnak, amelynek földrajzi útválasztási útvonala van, legalább egy régiót hozzá kell rendelni. Meglévő profil földrajzi útválasztási típusra való átalakításához először a földrajzi régiókat kell hozzárendelni az összes végponthoz az [Azure Traffic Manager REST API](/rest/api/trafficmanager/) használatával, mielőtt módosítaná az útválasztási típust a földrajzi értékre. Ha a portált használja, először törölje a végpontokat, módosítsa a profil útválasztási módszerét földrajzi értékre, majd adja hozzá a végpontokat a földrajzi régiójuk leképezésével együtt.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Ezért erősen ajánlott, hogy az ügyfelek beágyazott profilokat hozzanak létre a földrajzi útválasztást engedélyező profilhoz tartozó végpontok helyett?

Egy régió csak egy végponthoz rendelhető egy adott profilon belül, ha a földrajzi útválasztási módszert használja. Ha ez a végpont nem olyan beágyazott típus, amelyhez hozzá van csatolva egy alárendelt profil, akkor a Traffic Manager továbbra is küldi a forgalmat, mivel a forgalom nem küld semmilyen forgalmat. Traffic Manager nem végez feladatátvételt egy másik végpontra, még akkor is, ha a hozzárendelt régió a végponthoz rendelt régió szülője (például ha egy, a spanyol régióval rendelkező végpont nem kifogástalan állapotú, nem végez feladatátvételt egy másik végpontra, amely az európai régióhoz van rendelve). Ezzel biztosítható, hogy Traffic Manager tiszteletben tartsák az ügyfél által a profiljában beállított földrajzi határokat. Annak érdekében, hogy a végpontok nem megfelelő állapotba kerüljön, a rendszer azt ajánlja, hogy a földrajzi régiók a különböző végpontok helyett több végponttal rendelkező beágyazott profilokhoz legyenek hozzárendelve. Így ha a beágyazott alárendelt profil egyik végpontja meghibásodik, a forgalom egy másik végpontra is átadható, ugyanazon a beágyazott alárendelt profilon belül.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Van olyan korlátozás az API-verzióra, amely támogatja ezt az útválasztási típust?

Igen, csak a 2017-03-01-es és újabb verziójú API-k támogatják a földrajzi útválasztási típust. A régebbi API-verziók nem használhatók a földrajzi útválasztási típusú profilok létrehozásához, illetve a földrajzi régiók végpontokhoz való hozzárendeléséhez. Ha egy régebbi API-verzióval kéri le a profilokat egy Azure-előfizetésből, a földrajzi útválasztási típusok bármelyik profilját nem adja vissza a rendszer. Emellett a régebbi API-verziók használatakor a rendszer minden olyan profilt visszaadott, amely egy földrajzi régió-hozzárendeléssel rendelkező végpontokkal rendelkezik, nem rendelkezik a földrajzi régiójának hozzárendelésével.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager alhálózat forgalmának útválasztási módszere

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Mik azok a használati esetek, amikor az alhálózat-útválasztás hasznos?

Az alhálózat-útválasztás lehetővé teszi, hogy megkülönböztetni a DNS-kérések IP-címének forrás IP-címe által azonosított felhasználók meghatározott csoportjainak nyújtotta élményt. Például ha a felhasználók a vállalati HQ webhelyéhez csatlakoznak, különböző tartalmakat láthat. Egy másik internetszolgáltató bizonyos internetszolgáltatók felhasználóit korlátozza, hogy csak az IPv4-kapcsolatokat támogató végpontokhoz férhessenek hozzá, ha ezek az internetszolgáltatók az IPv6 használatakor alárendelt teljesítményű teljesítményt biztosítanak.
Az alhálózati útválasztási módszer használatának egy másik oka a beágyazott profilok más profiljaival együtt. Ha például a földrajzi útválasztási módszert szeretné használni a felhasználók geo-kerítéséhez, de egy adott INTERNETSZOLGÁLTATÓ esetében, amelyet más útválasztási módszerként szeretne elvégezni, akkor rendelkezhet egy profilt alhálózati útválasztási módszerrel, és felülbírálhatja, hogy az INTERNETSZOLGÁLTATÓ egy adott gyermek profil használatára van szüksége, és a szabványos földrajzi profillal rendelkezik a mindenki másnak.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Hogyan ismeri Traffic Manager a végfelhasználó IP-címét?

A végfelhasználói eszközök jellemzően DNS-feloldót használnak a DNS-címkeresés végrehajtásához a nevükben. Az ilyen feloldók kimenő IP-címe az a forrás IP-címet Traffic Manager látja. Emellett az alhálózat-útválasztási módszer azt is megvizsgálja, hogy van-e olyan EDNS0 kiterjesztett ügyfél-alhálózati (ECS) információ, amelyet továbbítottak a kérelemmel. Ha az ECS-adatok megtalálhatók, akkor az Útválasztás meghatározására szolgáló címnek kell lennie. Az ECS-adatok hiányában a lekérdezés forrás IP-címe útválasztási célokra szolgál.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hogyan adhatok meg IP-címeket alhálózati útválasztás használatakor?

A végpontokhoz társítandó IP-címek kétféleképpen adhatók meg. Először is használhatja a quad pontozott decimális oktettjét a kezdő és záró címekkel a tartomány meghatározásához (például 1.2.3.4-5.6.7.8 vagy 3.4.5.6-3.4.5.6). Másodszor, a CIDR jelölés használatával megadhatja a tartományt (például 1.2.3.0/24). Több tartományt is megadhat, és használhat mindkét jelölési típust egy tartomány készletében. Néhány korlátozás érvényes.

-    A címtartományok átfedése nem lehetséges, mivel az egyes IP-címeket csak egyetlen végpontra kell leképezni.
-    A kezdő címnek nem lehet hosszabb a záró címtől
-    A CIDR-jelölés esetén az IP-címnek, amely előtt a "/" értéknek kell lennie, az adott tartomány kezdő címének kell lennie (például 1.2.3.0/24 érvényes, de a 1.2.3.4.4/24 érvénytelen).

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hogyan adhatok tartalék végpontot alhálózati útválasztás használatakor?

Az alhálózati útválasztással rendelkező profilokban, ha olyan végponttal rendelkezik, amely nem rendelkezik hozzárendelt alhálózatokkal, akkor a többi végpontnak nem megfelelő kéréseket ide irányítja a rendszer. Erősen ajánlott, hogy egy ilyen tartalék végpontot a profilban, mivel a Traffic Manager egy NXDOMAIN választ ad vissza, ha egy kérelem érkezik, és nincs leképezve egyetlen végpontra sem, vagy ha egy végpontra van leképezve, de a végpont állapota nem megfelelő.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Mi történik, ha egy végpont le van tiltva egy alhálózat útválasztási típusának profiljában?

Ha olyan végponttal rendelkezik, amelyben az alhálózat útválasztása le van tiltva, akkor a Traffic Manager úgy viselkedik, mintha a végpont és az alhálózati hozzárendelések nem léteznek. Ha egy olyan lekérdezés érkezik, amely megfelel az IP-címének hozzárendelésének, és a végpont le van tiltva, Traffic Manager egy tartalék végpontot ad vissza (egyet a leképezés nélkül), vagy ha egy ilyen végpont nem található, egy NXDOMAIN választ ad vissza.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Többértékű forgalom-útválasztási módszer Traffic Manager

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Mik azok a használati esetek, ahol a többértékű útválasztás hasznos?

A többértékű útválasztás egyetlen lekérdezési válaszban több kifogástalan állapotú végpontot ad vissza. Ennek legfőbb előnye, hogy ha egy végpont állapota nem megfelelő, az ügyfélnek több lehetősége van az újrapróbálkozásra anélkül, hogy újabb DNS-hívást kellene végrehajtania (ami egy felsőbb rétegbeli gyorsítótárból származó érték visszaadása lehet). Ez olyan rendelkezésre állási érzékeny alkalmazások esetében alkalmazható, amelyek csökkenteni szeretnék az állásidőt.
A többértékű útválasztási módszer egy másik használata, ha egy végpont "kettős címes", IPv4-és IPv6-címekre is vonatkozik, és azt szeretné, hogy a hívó mindkét lehetőséget kiválassza a végponthoz való csatlakozás kezdeményezése esetén.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hány végpontot ad vissza a rendszer a többértékű útválasztás használata esetén?

Megadhatja a visszaadni kívánt végpontok maximális számát, és a többértékű szám nem haladhatja meg a lekérdezés fogadásakor több kifogástalan állapotú végpontot is. A konfiguráció maximális lehetséges értéke 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Ugyanazokat a végpontokat kapják meg, amikor többértékű útválasztást használunk?

Nem garantáljuk, hogy az egyes lekérdezésekben ugyanazok a végpontok lesznek visszaadva. Ezt az is befolyásolja, hogy egyes végpontok nem megfelelő állapotba kerülhetnek, amikor a válaszban nem fognak szerepelni.

## <a name="real-user-measurements"></a>Valós felhasználói mérések

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Milyen előnyökkel jár a valós felhasználómérés használata?

Ha teljesítmény-útválasztási módszert használ, Traffic Manager kiválasztja a legjobb Azure-régiót, amely a forrás IP-cím és a EDNS-ügyfél alhálózatának vizsgálatával csatlakozik, és ellenőrzi a szolgáltatás által fenntartott hálózati késési intelligenciával. Valós felhasználómérés fokozza ezt a végfelhasználói bázisra azáltal, hogy a tapasztalataik hozzájárulnak ehhez a késési táblázathoz, továbbá annak biztosításához, hogy ez a táblázat megfelelően felölelje a végfelhasználói hálózatokat, ahonnan a végfelhasználók az Azure-hoz csatlakoznak. Ez nagyobb pontosságot eredményez a végfelhasználói útválasztásban.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Használhatok valós felhasználómérést nem Azure-régiókkal?

Valós felhasználómérés mértékeket és jelentéseket csak az Azure-régiók elérésére vonatkozó késéssel. Ha nem Azure-régiókban üzemeltetett végpontokkal rendelkező teljesítmény-alapú útválasztást használ, továbbra is használhatja ezt a funkciót azáltal, hogy az ehhez a végponthoz hozzárendelni kívánt reprezentatív Azure-régióhoz képest nagyobb késési információkkal szolgál.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Melyik útválasztási módszer előnyös a valós felhasználómérés?

A valós felhasználómérés használatával szerzett további információk csak a teljesítmény-útválasztási módszert használó profilok esetében alkalmazhatók. A valós felhasználómérés hivatkozás az összes profilból elérhető, amikor megtekinti a Azure Portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Külön kell engedélyeznie valós felhasználómérés az egyes profilokat?

Nem, csak egyszer kell engedélyeznie az előfizetést, és az összes megmért és jelentett késési információt minden profil számára elérhetővé kell tenni.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Az előfizetéshez tartozó valós felhasználómérés kikapcsolása Hogyan

Leállíthatja a valós felhasználóméréshoz kapcsolódó díjak összegyűjtését, ha leállítja a késési mérések összegyűjtését és küldését az ügyfélalkalmazás számára. Ha például a weblapokra ágyazott mérési JavaScriptet használja, leállíthatja ezt a szolgáltatást a JavaScript eltávolításával vagy az oldal megjelenítésének meghívásának kikapcsolásával.

A kulcsot a kulcs törlésével is kikapcsolhatja valós felhasználómérés. A kulcs törlése után a rendszer elvetette az adott kulccsal Traffic Manager eljuttatott mértékeket.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Használhatok a weblapokon kívül más ügyfélalkalmazások valós felhasználómérés is?

Igen, valós felhasználómérés úgy lett kialakítva, hogy a különböző típusú végfelhasználói ügyfelekkel gyűjtött adatokat betöltse. Ez a gyakori kérdések a támogatott ügyfélalkalmazások új típusaként lesznek frissítve.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hány mérést végeznek minden alkalommal, amikor a valós felhasználómérés engedélyezve weboldala jelenik meg?

Ha valós felhasználómérés használja a megadott mérési JavaScriptet, az egyes lapok renderelése hat mérést eredményez. Ezeket a rendszer visszaküldi a Traffic Manager szolgáltatásnak. Ezt a funkciót a Traffic Manager szolgáltatásnak jelentett mérések száma alapján számítjuk fel. Ha például a felhasználó a weboldaláról navigál a mérések végrehajtása előtt, de még a jelentés előtt, a rendszer ezeket a mértékeket nem veszi figyelembe a számlázási célokból.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Van-e késés a weblapon valós felhasználómérés szkript futtatása előtt?

Nem, a parancsfájl meghívása előtt nem kerül sor a programozott késésre.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Használhatom valós felhasználómérés csak a mérni kívánt Azure-régiókkal?

Nem, minden alkalommal, amikor meghívja a valós felhasználómérés parancsfájl a szolgáltatás által meghatározott hat Azure-régiót méri fel. Ez a beállítás a különböző meghívások és a nagy számú ilyen hívás között változik, a mérési lefedettség a különböző Azure-régiók között zajlik.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Korlátozható a megadott számú mérések száma?

A mérés JavaScript be van ágyazva a weboldalán belül, és a használatának megkezdéséhez és leállításához teljes mértékben meg kell határoznia. Ha a Traffic Manager szolgáltatás a mérendő Azure-régiók listájára vonatkozó kérelmet kap, akkor a rendszer a régiókat adja vissza.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Megtekinthetem az ügyfélalkalmazás által a valós felhasználómérés részeként végrehajtott méréseket?

Mivel a mérési logikát az ügyfélalkalmazás futtatja, teljes mértékben ellenőrizni kell, hogy mi történik, beleértve a késés mértékének megadását. Traffic Manager nem jelent összesítő nézetet az előfizetéshez társított kulcs alatt kapott mérésekről.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Módosíthatom Traffic Manager által biztosított mérési parancsfájlt?

A weblap beágyazásának szabályozása közben határozottan visszatartjuk a mérési parancsfájl módosításait, így biztosítva, hogy az informatikai intézkedések és a késések megfelelően jelentést készítsenek.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Mások is láthatják, hogy milyen kulcsot használok a valós felhasználómérés?

Ha a mérési parancsfájlt egy weblapra ágyazza be, akkor mások is láthatják a parancsfájlt és a valós felhasználómérés (RUM) kulcsát. Fontos azonban tudni, hogy ez a kulcs különbözik az előfizetés-azonosítótól, és a Traffic Manager által generált adatok kizárólag erre a célra használhatók. A RUM-kulcs ismerete nem fogja veszélyeztetni az Azure-fiók biztonságát.

### <a name="can-others-abuse-my-rum-key"></a>Mások is kihasználhatják a RUM-kulcsot?

Míg mások számára is lehetséges, hogy a kulcsot nem megfelelő információk küldésére használják az Azure-ba, néhány rossz mérés nem változtatja meg az útválasztást, mivel az a kapott összes többi méréssel együtt veszi figyelembe. Ha módosítania kell a kulcsokat, újra létrehozhatja azt a kulcsot, amelyen a régi kulcs el lesz vetve.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Be kell-e állítani a mérési JavaScriptet az összes weblapon?

Valós felhasználómérés nagyobb értéket biztosít a mérések számának növekedésével. Azt mondta, hogy ez az Ön döntése, hogy el kell-e helyeznie az összes weboldalán, vagy csak néhányat. Javasoljuk, hogy a leggyakrabban felkeresett oldalon helyezze el a felhasználót, ahol a felhasználóknak öt másodperc vagy annál nagyobb időt kell várniuk az adott oldalon.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>A végfelhasználók számára azonosíthatók a Traffic Manager, ha valós felhasználómérés használok?

Ha a megadott mérési JavaScript van használatban, Traffic Manager látható lesz a végfelhasználó ügyfél IP-címével és az általa használt helyi DNS-feloldó forrás IP-címével. Traffic Manager az ügyfél IP-címét csak a csonkítás után használja, hogy nem tudja azonosítani a méréseket küldő adott végfelhasználót.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A weblap mérési valós felhasználómérés az útválasztáshoz Traffic Manager kell használnia?

Nem, nincs szükség a Traffic Manager használatára. Traffic Manager útválasztási oldala külön működik a valós felhasználói mérési résztől, és bár nagyszerű ötlet, hogy mindkettőt ugyanazon a webtulajdonságon belül kell megadni, nem kell.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Szükség van-e az Azure-régiókban üzemeltetett szolgáltatások üzemeltetésére az valós felhasználómérés-vel való használatra?

Nem, a valós felhasználómérés működéséhez nem szükséges kiszolgálóoldali összetevőt üzemeltetni az Azure-ban. A mérési JavaScript és a különböző Azure-régiókban futó szolgáltatás által letöltött egyetlen képpontos képet az Azure üzemelteti és kezeli. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Növekedni fog az Azure sávszélesség-használatom a valós felhasználómérés használatakor?

Az előző válaszban említettek szerint a valós felhasználómérés kiszolgálóoldali összetevői az Azure tulajdonában és felügyelete alatt állnak. Ez azt jelenti, hogy az Azure-sávszélesség használata nem fog növekedni, mert valós felhasználómérés használ. Ez nem tartalmazza az Azure-díjakon kívüli sávszélesség-használatot. A sávszélességet az Azure-régiók késésének méréséhez csak egyetlen képpontos rendszerkép letöltésével lehet csökkenteni. 

## <a name="traffic-view"></a>Forgalomnézet

### <a name="what-does-traffic-view-do"></a>Mit tesz forgalomáttekintő?

A forgalomáttekintő Traffic Manager szolgáltatása, amely segít jobban megismerni a felhasználókat és a felhasználói élményt. A Traffic Manager által fogadott lekérdezések és a szolgáltatás által fenntartott hálózati késési intelligencia táblázatok használatával biztosítja a következőket:

- Azok a régiók, amelyekről a felhasználók csatlakoznak a végpontokhoz az Azure-ban.
- Az ezekből a régiókból csatlakozó felhasználók mennyisége.
- Azok az Azure-régiók, amelyekhez a rendszer átirányítja őket.
- Az Azure-régiók késési élményét.

Ezek az információk a földrajzi térképes átfedések és a portálon található táblázatos nézetek használatával érhetők el, továbbá a letöltéshez nyers adatként is elérhetők.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Milyen előnyökkel jár a forgalomáttekintő használata?

Forgalomáttekintő a Traffic Manager-profilok által fogadott forgalom általános nézetét adja meg. Különösen azt is megtudhatja, hogy a felhasználói bázis milyen mértékben kapcsolódik a szolgáltatáshoz, és egyformán fontos, hogy az átlagos késési élmény milyen. Ezután ezen információk alapján megtalálhatja azokat a területeket, amelyekben például az Azure-lábnyomot egy olyan régióra kell kibővíteni, amely alacsonyabb késéssel képes kiszolgálni ezeket a felhasználókat. Egy másik betekintést nyerhet a forgalomáttekintő használatával, hogy megtekintse a különböző régiókba irányuló adatforgalom mintáit, amelyek pedig segíthetnek a régiókban való kitalálás növelésének vagy csökkentésének meghozatalában.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Miben különbözik forgalomáttekintő az Azure monitoron keresztül elérhető Traffic Manager metrikákkal?

A Azure Monitor a profil és a végpontok által fogadott forgalom összesített szintjének megismerésére használható. Emellett lehetővé teszi a végpontok állapotának nyomon követését az állapot-ellenőrzés eredményeinek kiírásával. Ha ezen kívül szeretné megismerni, hogy a végfelhasználók milyen élményt nyújtanak az Azure-hoz, regionális szinten, forgalomáttekintő használható.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>A forgalomáttekintő használja az EDNS-ügyfél alhálózati adatait?

Az Azure Traffic Manager által kiszolgált DNS-lekérdezések megvizsgálják az ECS-adatokat az Útválasztás pontosságának növeléséhez. Ha azonban olyan adatkészletet hoz létre, amely azt jelzi, hogy a felhasználók honnan csatlakoznak, forgalomáttekintő a DNS-feloldó IP-címét használja.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hány napi adatmennyiséget forgalomáttekintő használni?

Forgalomáttekintő hozza létre a kimenetét az Ön által megtekintett naptól számított hét nap adatainak feldolgozásával. Ez egy mozgó ablak, és a rendszer minden alkalommal felkeresi a legfrissebb információkat.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hogyan kezeli a forgalomáttekintő a külső végpontokat?

Ha az Azure-régión kívüli külső végpontokat használ egy Traffic Manager profilban, kiválaszthatja, hogy egy olyan Azure-régióhoz legyen hozzárendelve, amely a késési jellemzőinek proxyja (ez valójában szükséges, ha teljesítmény-útválasztási módszert használ). Ha ez az Azure-régió leképezése, az forgalomáttekintő kimenet létrehozásakor az Azure-régió késési metrikái lesznek felhasználva. Ha nincs megadva Azure-régió, a késési információk üresek lesznek a külső végpontok adataiban.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Engedélyeznie kell a forgalomáttekintő az előfizetésem minden profiljához?

Az előzetes verzió ideje alatt a forgalomáttekintő előfizetési szinten volt engedélyezve. Az általánosan elérhetővé tett előrelépések részeként mostantól engedélyezheti a forgalomáttekintőt a profil szintjén, ami lehetővé teszi a funkció részletesebb engedélyezését. Alapértelmezés szerint a forgalomáttekintő le lesznek tiltva egy profil esetében.

>[!NOTE]
>Ha az előzetes verzió ideje alatt engedélyezte forgalomáttekintő az előfizetési szinten, most újra engedélyeznie kell azt az előfizetéshez tartozó összes profilhoz.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hogyan kapcsolható ki a forgalomáttekintő?

A portál vagy a REST API használatával kikapcsolhatja a forgalomáttekintőt bármelyik profilhoz. 

### <a name="how-does-traffic-view-billing-work"></a>Hogyan működik forgalomáttekintő számlázás?

A forgalomáttekintő díjszabás a kimenet létrehozásához használt adatpontok számától függ. Jelenleg az egyetlen támogatott adattípus a profil által fogadott lekérdezések. Emellett csak a forgalomáttekintő engedélyezésekor elvégzett feldolgozásért kell fizetnie. Ez azt jelenti, hogy ha egy adott időszakban engedélyezi a forgalomáttekintő egy adott időszakra vonatkozóan, és más időpontokban ki van kapcsolva, csak az adatpontok feldolgozása történik, miközben a funkció engedélyezve van a számlán.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-végpontok

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Használhatok Traffic Manager több előfizetésből származó végpontokkal?

A több előfizetésből származó végpontok használata nem lehetséges az Azure Web Apps. Az Azure Web Apps megköveteli, hogy a Web Apps használt egyéni tartománynevek csak egyetlen előfizetésben legyenek használatban. Nem lehet több előfizetésből Web Appst használni ugyanazzal a tartománynévvel.

Más végpontok esetében az Traffic Manager több előfizetésből származó végpontokkal is használható. A Resource Managerben bármely előfizetésből származó végpontok Traffic Managerhoz adhatók hozzá, feltéve, hogy a Traffic Manager profilt konfiguráló személy olvasási hozzáféréssel rendelkezik a végponthoz. Ezek az engedélyek az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)használatával is megadhatók. Más előfizetésből származó végpontokat [Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) vagy az [Azure CLI](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create)használatával lehet felvenni.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Használhatom Traffic Manager a Cloud Service átmeneti tárolóhelyeit?

Igen. A Cloud Service "átmeneti" tárolóhelyeit külső végpontként Traffic Manager lehet konfigurálni. Az állapot-ellenőrzéseket továbbra is az Azure-végpontok díjszabása alapján számoljuk el.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Támogatja a Traffic Manager az IPv6-végpontokat?

A Traffic Manager jelenleg nem biztosít IPv6-címezhető névkiszolgálók használatát. Traffic Manager azonban továbbra is használhatja az IPv6-végpontokhoz csatlakozó IPv6-ügyfelek. Az ügyfél nem hajtja végre a DNS-kérelmeket közvetlenül a Traffic Manager. Ehelyett az ügyfél rekurzív DNS szolgáltatást használ. A csak IPv6-alapú ügyfelek az IPv6 protokollon keresztül küldenek kéréseket a rekurzív DNS szolgáltatásnak. Ezután a rekurzív szolgáltatásnak képesnek kell lennie arra, hogy az IPv4 használatával kapcsolatba lépjen a Traffic Manager névkiszolgálói kiszolgálókkal.

Traffic Manager a végpont DNS-nevével vagy IP-címével válaszol. IPv6-végpont támogatásához két lehetőség közül választhat. A végpontot hozzáadhatja egy társított AAAA-rekorddal rendelkező DNS-névként, és Traffic Manager az állapotot, majd a lekérdezési válaszban CNAME-bejegyzéstípusként adja vissza. Azt is megteheti, hogy közvetlenül az IPv6-cím használatával adja hozzá a végpontot, és a Traffic Manager egy AAAA típusú rekordot ad vissza a lekérdezési válaszban.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Használhatok több webalkalmazással rendelkező Traffic Manager ugyanabban a régióban?

A Traffic Manager általában a különböző régiókban üzembe helyezett alkalmazásokra irányuló forgalom irányítására szolgál. Ugyanakkor azt is felhasználhatja, hogy egy adott alkalmazás több központi telepítéssel is rendelkezik ugyanabban a régióban. Az Traffic Manager Azure-végpontok nem engedélyezik, hogy ugyanahhoz az Azure-régióhoz több webalkalmazás-végpontot lehessen hozzáadni ugyanahhoz a Traffic Manager-profilhoz.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Hogyan az Traffic Manager profil Azure-végpontját egy másik erőforráscsoporthoz vagy előfizetésbe helyezi át?

A Traffic Manager profiljához társított Azure-végpontokat az erőforrás-azonosítóik alapján követjük nyomon. Ha egy olyan Azure-erőforrást, amelyet végpontként használ (például a nyilvános IP-címet, a klasszikus felhőalapú szolgáltatást, a WebApp-t vagy egy beágyazott módon használt Traffic Manager profilt), a rendszer áthelyezi egy másik erőforráscsoporthoz vagy előfizetésbe, és az erőforrás-azonosítót módosítja. Ebben a forgatókönyvben jelenleg a Traffic Manager profilt először törölnie kell, majd újra hozzá kell adnia a végpontokat a profilhoz.

## <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager-végpontmonitorozás

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Rugalmasan Traffic Manager az Azure-régiók hibáira?

Traffic Manager a magasan elérhető alkalmazások Azure-ban történő kézbesítésének kulcsfontosságú összetevője.
A magas rendelkezésre állás biztosítása érdekében Traffic Managernek rendkívül magas szintű rendelkezésre állással kell rendelkeznie, és rugalmasnak kell lennie a regionális meghibásodáshoz.

Az Traffic Manager-összetevők úgy vannak kialakítva, hogy az Azure-régiók teljes meghibásodása esetén rugalmasak legyenek. Ez a rugalmasság az összes Traffic Manager összetevőre vonatkozik: a DNS-névkiszolgálók, az API, a tárolási réteg és a végpont-figyelési szolgáltatásra.

Egy teljes Azure-régió meghibásodása esetén a Traffic Manager várhatóan továbbra is a szokásos módon fog működni. A több Azure-régióban üzembe helyezett alkalmazások kihasználhatják az Traffic Manager, hogy az alkalmazás egy elérhető példányára irányítsák a forgalmat.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Hogyan befolyásolja az erőforráscsoport helyének megválasztása a Traffic Manager?

Traffic Manager egyetlen globális szolgáltatás. Nem regionális. Az erőforráscsoport helyének kiválasztása nem tesz különbséget az adott erőforráscsoporthoz telepített Traffic Manager profilok esetében.

Azure Resource Manager megköveteli az összes erőforráscsoport számára a hely megadását, amely meghatározza az adott erőforráscsoporthoz üzembe helyezett erőforrások alapértelmezett helyét. Traffic Manager-profil létrehozásakor létrejön egy erőforráscsoport. Az összes Traffic Manager-profil **globális** értéket használ a helyükként, felülbírálva az erőforráscsoport alapértelmezését.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hogyan határozza meg az egyes végpontok aktuális állapotát?

Az egyes végpontok aktuális figyelési állapota a teljes profil mellett a Azure Portal jelenik meg. Ezek az információk a Traffic monitor [REST API](/rest/api/trafficmanager/), a PowerShell- [parancsmagok](/powershell/module/az.trafficmanager)és a [platformfüggetlen Azure CLI](/cli/azure/install-classic-cli)használatával is elérhetők.

A Azure Monitor is használhatja a végpontok állapotának nyomon követéséhez, valamint a vizualizációk megjelenítéséhez. A Azure Monitor használatáról az [Azure monitoring dokumentációjában](../azure-monitor/data-platform.md)olvashat bővebben.

### <a name="can-i-monitor-https-endpoints"></a>Figyelhető a HTTPS-végpontok?

Igen. Traffic Manager a HTTPS protokollon keresztül támogatja a szondázás használatát. Konfigurálja a **https** protokollt a figyelési konfigurációban lévő protokollként.

A Traffic Manager nem tud tanúsítvány-ellenőrzést biztosítani, beleértve a következőket:

* A kiszolgálóoldali tanúsítványok nincsenek érvényesítve
* A SNI-kiszolgálóoldali tanúsítványok nincsenek érvényesítve
* Az ügyféltanúsítványok nem támogatottak

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Egy végpont hozzáadásakor használhatok IP-címet vagy DNS-nevet?

Traffic Manager támogatja a végpontok hozzáadását háromféle módon, a DNS-név, IPv4-cím és IPv6-cím használatával. Ha a végpontot IPv4-vagy IPv6-címként adja hozzá, a lekérdezés válasza a vagy AAAA típusú rekord lesz. Ha a végpontot DNS-névként adták hozzá, a lekérdezés válasza a CNAME típusú rekord lesz. A végpontok IPv4-vagy IPv6-címként való hozzáadása csak akkor engedélyezett, ha a végpont **külső** típusú.
Az összes útválasztási módszert és figyelési beállítást a három végponti címzési típus támogatja.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Milyen típusú IP-címeket használhatok a végpontok hozzáadásakor?

Traffic Manager lehetővé teszi, hogy IPv4-vagy IPv6-címeket használjon a végpontok megadásához. Az alábbiakban felsorolunk néhány korlátozást:

- A fenntartott magánhálózati IP-címeknek megfelelő címek nem engedélyezettek. Ezek a címek közé tartoznak az RFC 1918, az RFC 6890, az RFC 5737, az RFC 3068, az RFC 2544 és az RFC 5771
- A címnek nem tartalmazhat portszámot (a profil konfigurációs beállításaiban használandó portokat adhatja meg)
- Ugyanabban a profilban nem szerepelhet két végpont ugyanazzal a célként megadott IP-címmel

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Használhatok különböző végpont-címzési típusokat egyetlen profilon belül?

Nem, Traffic Manager nem teszi lehetővé a végpontok címzési típusának összekeverését egy profilon belül, kivéve a többértékű útválasztási típusú profilok esetét, ahol az IPv4-és IPv6-címzési típusokat össze lehet keverni.

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Mi történik, ha egy bejövő lekérdezés bejegyzéstípusa eltér a végpontok címzési típusához társított bejegyzéstípustól?

Amikor lekérdezést fogad egy profilhoz, Traffic Manager először a megadott útválasztási módszernek és a végpontok állapotának megfelelően megkeresi a végpontot, amelyet vissza kell adni. Ezután a bejövő lekérdezésben kért bejegyzéstípust és a végponthoz társított bejegyzéstípust tekinti át, mielőtt visszaadja a választ az alábbi táblázat alapján.

Olyan profilok esetében, amelyek nem a többértékű útválasztási módszerrel rendelkeznek:

|Bejövő lekérdezési kérelem|     Végpont típusa|     Válasz megadva|
|--|--|--|
|BÁRMELY |    A/AAAA/CNAME |    Cél végpont| 
|A |    A/CNAME |    Cél végpont|
|A |    AAAA |    NODATA |
|AAAA |    AAAA/CNAME |    Cél végpont|
|AAAA |    A |    NODATA |
|CNAME |    CNAME |    Cél végpont|
|CNAME     |A/AAAA |    NODATA |
|

Olyan profilok esetében, amelyeknél az útválasztási módszer értéke többértékű:

|Bejövő lekérdezési kérelem|     Végpont típusa |    Válasz megadva|
|--|--|--|
|BÁRMELY |    A és AAAA kombinációja |    Cél végpontok|
|A |    A és AAAA kombinációja |    Csak az A típusú cél végpontok|
|AAAA    |A és AAAA kombinációja|     Csak AAAA típusú cél végpontok|
|CNAME |    A és AAAA kombinációja |    NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Használhatok egy beágyazott profil IPv4/IPv6-beli végpontját használó profilt?

Igen, azzal a kivétellel, hogy egy többértékű profil nem lehet szülő profil egy beágyazott profilban.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Leállítottam egy webalkalmazás-végpontot a Traffic Manager-profilban, de nem kapok forgalmat még az újraindítást követően sem. Hogyan javíthatom ezt?

Ha egy Azure-webalkalmazás-végpont leáll Traffic Manager leáll az állapotának ellenőrzése, és csak azt követően indítja újra az állapot-ellenőrzéseket, hogy a végpont újraindult. A késleltetés megelőzése érdekében tiltsa le, majd engedélyezze újra a végpontot a Traffic Manager profilban a végpont újraindítása után.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Használhatom Traffic Manager még akkor is, ha az alkalmazás nem támogatja a HTTP-t vagy a HTTPS-t?

Igen. Megadhatja a TCP protokollt a figyelési protokollként, és Traffic Manager kezdeményezheti a TCP-kapcsolatokat, és megvárhatja a végponttól kapott választ. Ha a végpont választ küld a kapcsolódási kérelemre a kapcsolat létrehozásához szükséges válasz alapján, akkor az időtúllépési időszakon belül a végpont állapota Kifogástalan lesz.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Milyen válaszokra van szükség a végponttól a TCP-figyelés használatakor?

A TCP-figyelés használatakor Traffic Manager egy háromutas TCP-kézfogást indít el egy SYN-kérelem küldésével a megadott porton. Ezután vár egy SYN-ACK választ a végponttól az időtúllépési beállításokban megadott időtartamra.

- Ha a figyelési beállításokban megadott időtúllépési időszakon belül egy SYN-ACK-válasz érkezik, akkor a végpont kifogástalannak minősül. Egy FIN vagy FIN-ACK a Traffic Manager várt válasza, amikor rendszeresen leállítja a szoftvercsatornát.
- Ha a megadott időkorlát után egy SYN-ACK-válasz érkezik, a Traffic Manager egy első lépésre válaszol a kapcsolat visszaállításához.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Milyen gyorsan Traffic Manager áthelyezni a felhasználókat egy nem kifogástalan állapotú végpontról?

Traffic Manager több olyan beállítást biztosít, amelyek segítségével szabályozhatja a Traffic Manager-profil feladatátvételi viselkedését a következőképpen:

- azt is megadhatja, hogy a Traffic Manager a végpontok gyakrabban legyenek felhasználva, ha a szondázás intervallumát 10 másodpercen belül beállítja. Ez biztosítja, hogy a rendszer a lehető leghamarabb észlelje a nem megfelelő állapotú végpontokat. 
- megadhatja, hogy mennyi ideig kell várni az állapot-ellenőrzési kérelem időtúllépése előtt (a minimális időtúllépési érték 5 másodperc).
- megadhatja, hogy hány hiba következhet be, mielőtt a végpont állapota sérült. Ez az érték lehet nullánál kisebb, ebben az esetben a végpont nem megfelelő állapotú, amint az első állapot-ellenőrzés sikertelen lesz. Ha azonban a minimálisan megengedettnél a 0 értéket használja a meghibásodások számához, akkor az elforgatáskor a végpontokat a vizsgálat során esetlegesen előforduló átmeneti problémák okozzák.
- megadhatja az élettartam (TTL) értéket a DNS-válasz számára, hogy az értéke legyen alacsony, mint 0. Ez azt jelenti, hogy a DNS-feloldók nem tudják gyorsítótárazni a választ, és minden új lekérdezés olyan választ kap, amely tartalmazza a legnaprakészebb állapotú, a Traffic Manager által használt információkat.

Ezeknek a beállításoknak a használatával a Traffic Manager 10 másodpercen belül képes biztosítani a feladatátvételt, miután egy végpont nem Kifogástalan állapotba kerül, és a rendszer egy DNS-lekérdezést hajt végre a megfelelő profilon.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hogyan adhatok meg különböző figyelési beállításokat a különböző végpontokhoz egy profilban?

Traffic Manager figyelési beállítások egy profil szintjén érhetők el. Ha csak egy végpontra vonatkozó figyelési beállítást kell használnia, azt úgy teheti meg, hogy a végpont olyan [beágyazott profillal](traffic-manager-nested-profiles.md) rendelkezik, amelynek figyelési beállításai eltérnek a szülő profiltól.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hogyan oszthatok ki HTTP-fejléceket a Traffic Manager állapot-ellenőrzésekhez a saját végpontokhoz?

Traffic Manager lehetővé teszi egyéni fejlécek megadását a HTTP (S) állapot-ellenőrzésekben, amelyeket a végpontokon kezdeményez. Ha egyéni fejlécet szeretne megadni, azt megteheti a profil szintjén (az összes végpontra vonatkozik), vagy megadhatja a végpont szintjén. Ha egy fejléc mindkét szinten van definiálva, akkor a végpont szintjén megadott érték felülbírálja a profil szintjét.
Ez egy gyakori használati eset a gazdagépek fejlécének megadásához, hogy Traffic Manager kérelmek megfelelően továbbíthatók legyenek egy több-bérlős környezetben üzemeltetett végponthoz. Ennek egy másik felhasználási esete a végpontok HTTP (S) kérelmi naplóiból érkező Traffic Manager kérelmek azonosítása

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Milyen állomásfejléc-ellenőrzést használ a rendszer a végpontok állapotának ellenőrzéséhez?

Ha nincs megadva egyéni állomásfejléc-beállítás, az Traffic Manager által használt állomásfejléc a profilban konfigurált végponti cél DNS-neve, ha elérhető.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Mik azok az IP-címek, amelyekről az állapot-ellenőrzések származnak?

Ide [kattintva](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) megtudhatja, hogyan kérheti le azoknak az IP-címeknek a listáját, amelyekről Traffic Manager állapot-ellenőrzés származhat. A legújabb lista lekéréséhez használhatja a REST API, az Azure CLI vagy a Azure PowerShell lehetőséget. Tekintse át a felsorolt IP-címek listáját, és győződjön meg arról, hogy az ezen IP-címekről érkező bejövő kapcsolatok engedélyezettek a végpontokon az állapotának ellenőrzéséhez.

Példa Azure PowerShell használatával:

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus
$result = $serviceTags.Values | Where-Object { $_.Name -eq "AzureTrafficManager" }
$result.Properties.AddressPrefixes
```

> [!NOTE]
> A nyilvános IP-címek értesítés nélkül változhatnak. Győződjön meg arról, hogy a legfrissebb információkat a Service tag Discovery API vagy a letölthető JSON-fájl használatával kéri le.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hány állapot-ellenőrzést várhatok a végpontom Traffic Manager?

A végpontot elérő Traffic Manager állapot-ellenőrzések száma a következőktől függ:

- a figyelési intervallumhoz beállított érték (a kisebb időköz több kérést jelent a végponton az adott időszakban).
- azoknak a helyeknek a száma, amelyekről az állapot-ellenőrzések származnak (az előző GYIK-ban felsorolt IP-címek, amelyekből várhatóan ezek az ellenőrzések láthatók).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hogyan kaphatok értesítést, ha az egyik végpontom leáll?

A Traffic Manager által megadott mérőszámok egyike egy profilban található végpontok állapotának állapota. Ezt a profilban lévő összes végpont összesítésének tekintheti meg (például a végpontok 75%-a kifogástalan), vagy a végpontok szintjén. Traffic Manager mérőszámok elérhetők a Azure Monitoron keresztül, és a [riasztási képességeivel](../azure-monitor/alerts/alerts-metric.md) értesítést kaphat, ha módosul a végpont állapotának változása. További részletek: [Traffic Manager mérőszámok és riasztások](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Beágyazott profilok Traffic Manager

### <a name="how-do-i-configure-nested-profiles"></a>Hogyan konfigurálja a beágyazott profilokat?

A beágyazott Traffic Manager profilok a Azure Resource Manager és a klasszikus Azure REST API-k, Azure PowerShell parancsmagok és platformfüggetlen Azure CLI-parancsok használatával konfigurálhatók. Ezeket az új Azure Portal is támogatják.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hány réteg beágyazását támogatja a Traffic Manger?

A profilokat akár 10 szint mélységig is beágyazhatja. A "Loops" használata nem engedélyezett.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Összekeverhető más típusú végpontok beágyazott alárendelt profilokkal is, ugyanabban a Traffic Manager-profilban?

Igen. Nincs korlátozás arra vonatkozóan, hogyan egyesítheti a különböző típusú végpontokat egy profilon belül.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hogyan vonatkozik a számlázási modell a beágyazott profilokra?

A beágyazott profilok használata nem befolyásolja a negatív díjszabást.

Traffic Manager számlázás két összetevőből áll: a végpontok állapotának ellenőrzése és a több millió DNS-lekérdezés

* Végpont állapotának ellenőrzése: a szülő profilban végpontként konfigurált alárendelt profilért nem számítunk fel díjat. A gyermek profilban található végpontok figyelése a szokásos módon történik.
* DNS-lekérdezések: minden lekérdezés csak egyszer számít. Egy alárendelt profilból egy végpontot visszaadó szülő profilra vonatkozó lekérdezés csak a szülő profilban számít.

A részletekért tekintse meg a [Traffic Manager díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Hatással van a beágyazott profilok teljesítményére?

Nem. Beágyazott profilok használata esetén nincs hatással a teljesítményre.

A Traffic Manager névkiszolgálók belsőleg áthaladják a profil hierarchiáját az egyes DNS-lekérdezések feldolgozásakor. Egy szülő profil DNS-lekérdezése DNS-választ kaphat egy alárendelt profil végpontjának használatával. Egyetlen CNAME rekord is használatos, függetlenül attól, hogy egyetlen profilt vagy beágyazott profilt használ. A hierarchiában lévő összes profilhoz nem szükséges CNAME rekordot létrehozni.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hogyan számítja Traffic Manager egy beágyazott végpont állapotát a fölérendelt profilban?

A szülő profil nem hajtja végre az állapot-ellenőrzéseket közvetlenül a gyermeken. Ehelyett a gyermek profil végpontjának állapotát használjuk a gyermek profil általános állapotának kiszámításához. A beágyazott végpont állapotának meghatározásához ezeket az információkat a beágyazott profil hierarchiája propagálja. A szülő profil ezt az összesített állapotot használja annak megállapítására, hogy a forgalom átirányítható-e a gyermekre.

A következő táblázat ismerteti a beágyazott végpontok Traffic Manager állapotának ellenőrzésének viselkedését.

| Gyermek Profil figyelő állapota | Szülő Endpoint monitor állapota | Jegyzetek |
| --- | --- | --- |
| Letiltva. A gyermek profil le lett tiltva. |Leállítva |A fölérendelt végpont állapota leáll, nem letiltva. A letiltott állapot annak jelzésére van fenntartva, hogy letiltotta a végpontot a szülő profilban. |
| Leromlott. Legalább egy alárendelt profil-végpont csökkentett teljesítményű állapotban van. |Online: a gyermek profilban található online végpontok száma legalább a MinChildEndpoints értéke.<BR>CheckingEndpoint: az online plusz CheckingEndpoint végpontok száma a gyermek profilban legalább a MinChildEndpoints értéke.<BR>Csökkentett teljesítményű: máskülönben. |A forgalmat az állapot CheckingEndpoint-végpontja irányítja át. Ha a MinChildEndpoints túl magasra van állítva, a végpont mindig csökken. |
| Online. Legalább egy alárendelt profil végpontja online állapotú. Egy végpont nem csökkentett teljesítményű állapotban van. |Lásd fentebb. | |
| CheckingEndpoints. Legalább egy alárendelt profil végpontja a következő: "CheckingEndpoint". Nincsenek végpontok "online" vagy "csökkentett teljesítményű" |Lásd fentebb. | |
| Inaktív. Az összes alárendelt profil végpontja le van tiltva vagy le van állítva, vagy ez a profil nem rendelkezik végpontokkal. |Leállítva | |

## <a name="next-steps"></a>Következő lépések:

- További információ a Traffic Manager [végpont monitorozásáról és az automatikus feladatátvételről](../traffic-manager/traffic-manager-monitoring.md).
- További információ a Traffic Manager [forgalom-útválasztási módszerekről](../traffic-manager/traffic-manager-routing-methods.md).