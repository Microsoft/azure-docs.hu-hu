---
title: Azure Traffic Manager – forgalom-útválasztási módszerek
description: Ez a cikk segítséget nyújt az Traffic Manager által használt különböző forgalom-útválasztási módszerek megismerésében.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: eeebded128f636ecba2e4e0dab1bc2f0632aaa6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730972"
---
# <a name="traffic-manager-routing-methods"></a>A Traffic Manager útválasztási módszerei

Az Azure Traffic Manager hat forgalmi útválasztási módszert támogat a hálózati forgalom különböző szolgáltatási végpontokra való továbbításának meghatározásához. Bármely profil esetében a Traffic Manager a hozzá társított forgalom-útválasztási módszert alkalmazza az összes kapott DNS-lekérdezéshez. A forgalom-útválasztási módszer határozza meg, hogy melyik végpontot adja vissza a rendszer a DNS-válaszban.

A következő forgalom-útválasztási módszerek érhetők el Traffic Managerban:

* **[Prioritás](#priority-traffic-routing-method):** válassza a **prioritásos** útválasztás lehetőséget, ha az összes forgalomhoz elsődleges szolgáltatási végpontot szeretne használni. Több biztonsági mentési végpont is megadható abban az esetben, ha az elsődleges vagy az egyik biztonsági mentési végpont nem érhető el.
* **[Súlyozott](#weighted):** válassza a **súlyozott** útválasztás lehetőséget, ha a terhelést a súlyozás alapján kívánja terjeszteni a végpontok között. Állítsa be a súlyozást úgy, hogy egyenletesen ossza szét az összes végponton.
* **[Teljesítmény](#performance):** válassza ki a **teljesítmény** -útválasztást, ha különböző földrajzi helyekhez tartozó végpontokkal rendelkezik, és azt szeretné, hogy a végfelhasználók a legalacsonyabb hálózati késéshez használják a "legközelebbi" végpontot.
* **[Földrajzi](#geographic):** válassza a **földrajzi** útválasztás lehetőséget a felhasználók adott végpontokra (Azure, External vagy nested) való közvetlen kiválasztására, amely alapján a DNS-lekérdezések földrajzilag származnak. Ez az útválasztási módszer lehetővé teszi, hogy megfeleljen az olyan forgatókönyveknek, mint például az adatszuverenitási mandátumok, a tartalmak honosítása & a felhasználói élmény és a különböző régiókból érkező forgalom mérése.
* **[](#multivalue)** Többértékű: válassza a többértékű lehetőséget **Traffic Manager olyan** profiloknál, amelyek csak IPv4/IPv6-címeket tartalmazhatnak végpontként. Ha a profilhoz lekérdezés érkezik, az összes kifogástalan állapotú végpontot adja vissza.
* **[Alhálózat](#subnet):** válassza az **alhálózati** forgalom – útválasztási módszer lehetőséget a végfelhasználói IP-címtartományok készletének leképezésére egy adott végpontra. A kérés fogadásakor a visszaadott végpont lesz az adott kérelem forrás IP-címéhez hozzárendelve. 


Minden Traffic Manager profil rendelkezik állapot-figyeléssel és a végpontok automatikus feladatátvételével. További információ: [Traffic Manager Endpoint monitoring](traffic-manager-monitoring.md). Egy Traffic Manager-profilon belül egyszerre csak egy forgalom-útválasztási módszert lehet konfigurálni. Bármikor kiválaszthat egy másik forgalom-útválasztási módszert a profiljához. A módosítások egy percen belül érvénybe lépnek, állásidő nélkül. A forgalom útválasztási módszereit beágyazott Traffic Manager profilok használatával kombinálhatja. A profilok beágyazásával olyan kifinomult forgalom-útválasztási konfigurációk alakíthatók ki, amelyek megfelelnek a nagyobb és összetett alkalmazások igényeinek. További információ: [beágyazott Traffic Manager profilok](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Prioritási forgalom – útválasztási módszer

A szervezet gyakran szeretne megbízhatóságot biztosítani szolgáltatásaik számára. Ehhez telepítsen egy vagy több biztonsági mentési szolgáltatást abban az esetben, ha az elsődleges leáll. A "prioritás" forgalom – az útválasztási módszer lehetővé teszi, hogy az Azure-ügyfelek könnyedén implementálják ezt a feladatátvételi mintát.

![Azure Traffic Manager "prioritás" forgalom – útválasztási módszer](media/traffic-manager-routing-methods/priority.png)

A Traffic Manager-profil szolgáltatóvégpontok prioritásos listáját tartalmazza. A Traffic Manager alapértelmezés szerint minden forgalmat az elsődleges (legmagasabb prioritású) végpontra irányít. Ha az elsődleges végpont nem elérhető, a Traffic Manager a második végpontra irányítja a forgalmat. Abban az esetben, ha az elsődleges és a másodlagos végpont nem érhető el, a forgalom harmadikra kerül, és így tovább. A végpontok elérhetősége a konfigurált állapot (engedélyezve vagy letiltva), és a végpont folyamatos figyelése alapján van meghatározva.

### <a name="configuring-endpoints"></a>Végpontok konfigurálása

A Azure Resource Manager használatával explicit módon konfigurálja a végpont prioritását az egyes végpontok "priority" tulajdonságával. Ez a tulajdonság 1 és 1000 közötti érték. Az alacsonyabb érték nagyobb prioritást jelent. A végpontok nem oszthatják meg a prioritási értékeket. A tulajdonság beállítása nem kötelező. Ha nincs megadva, a rendszer a végponti sorrend alapján alapértelmezett prioritást használ.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Súlyozott forgalom – útválasztási módszer
A "súlyozott" forgalom-útválasztási módszer lehetővé teszi, hogy egyenletesen ossza el a forgalmat, vagy használjon előre definiált súlyozást.

![Azure Traffic Manager "súlyozott" forgalom – útválasztási módszer](media/traffic-manager-routing-methods/weighted.png)

A súlyozott forgalom – útválasztási módszernél a Traffic Manager profil konfigurációjában minden egyes végponthoz hozzá kell rendelni a súlyozást. A súlyok 1 és 1000 közötti egész számok. Ezt a paramétert nem kötelező megadni. Ha nincs megadva, a Traffic managerek az alapértelmezett súlyozást használják: "1". Minél nagyobb a súly, annál magasabb a prioritás.

A Traffic Manager minden kapott DNS-lekérdezés esetében véletlenszerűen választ egy elérhető végpontot. Egy adott végpont kiválasztásának valószínűsége az összes elérhető végponthoz rendelt súlyokon múlik. Az összes végponton ugyanazt a súlyozást használva egy egyenletes forgalom eloszlását eredményezi. Ha az adott végpontok nagyobb vagy alacsonyabb súlyozását szeretné használni, a DNS-válaszokban a végpontok többször vagy ritkábban lesznek visszaadva.

A súlyozott módszer néhány hasznos forgatókönyvet is lehetővé tesz:

* Fokozatos alkalmazás frissítése: az új végpont felé irányuló forgalom százalékaránya, és az idő múlásával fokozatosan növelje a forgalmat 100%-ra.
* Alkalmazások áttelepítése az Azure-ba: hozzon létre egy profilt az Azure-ban és a külső végpontokkal. Állítsa be úgy a végpontok súlyozását, hogy az új végpontokat részesíti előnyben.
* Nagyobb kapacitás a felhőben: a helyszíni telepítést gyorsan kiterjesztheti a felhőbe azáltal, hogy egy Traffic Manager profil mögé helyezi. Ha további kapacitásra van szüksége a felhőben, több végpontot is hozzáadhat vagy engedélyezhet, és megadhatja, hogy a forgalom milyen hányada kerüljön az egyes végpontokra.

A súlyok a Azure Portal, a Azure PowerShell, a CLI vagy a REST API-k használatával konfigurálhatók.

A megjegyezhető pont az, hogy a DNS-válaszokat az ügyfelek gyorsítótárazzák. Azokat a rekurzív DNS-kiszolgálókat is gyorsítótárazza, amelyeket az ügyfelek a DNS-nevek feloldására használnak. Ez a gyorsítótárazás befolyásolhatja a súlyozott forgalom eloszlását. Ha az ügyfelek száma és a rekurzív DNS-kiszolgálók nagy méretűek, a forgalom eloszlása a várt módon működik. Ha azonban az ügyfelek vagy a rekurzív DNS-kiszolgálók száma kicsi, a gyorsítótárazás jelentősen elferdítheti a forgalom eloszlását.

Gyakori használati esetek a következők:

* Fejlesztési és tesztelési környezetek
* Alkalmazás – alkalmazás kommunikáció
* A közös rekurzív DNS-infrastruktúrát (például a vállalat által a proxyn keresztül csatlakozó alkalmazottakat) használó, keskeny felhasználói bázist célzó alkalmazások

Ezek a DNS-gyorsítótárazási hatások közösek az összes DNS-alapú forgalom-útválasztási rendszeren, nem csak az Azure Traffic Manager. Bizonyos esetekben a DNS-gyorsítótár explicit módon történő törlése megkerülő megoldással rendelkezhet. Ha ez nem működik, egy másik forgalom-útválasztási módszer megfelelőbb lehet.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Teljesítmény-forgalom – útválasztási módszer

A végpontok üzembe helyezése a világ két vagy több helyén is javíthatja az alkalmazások rugalmasságát. A "Performance" forgalom – útválasztási módszer segítségével átirányíthatja a forgalmat a legközelebbi helyre.

![Azure Traffic Manager "Performance" forgalom – útválasztási módszer](media/traffic-manager-routing-methods/performance.png)

A "legközelebbi" végpont nem feltétlenül a legközelebbi földrajzi távolság alapján mérve. Ehelyett a "Performance" forgalom-útválasztási módszer határozza meg a legközelebbi végpontot a hálózati késés mérésével. A Traffic Manager egy internetes késési táblázatot tart fenn az IP-címtartományok és az egyes Azure-adatközpontok közötti oda-és visszautazási idő nyomon követéséhez.

Traffic Manager megkeresi a bejövő DNS-kérelem forrás IP-címét az Internet késési táblájában. Traffic Manager ezután kiválaszt egy elérhető végpontot az Azure-adatközpontban, amely az adott IP-címtartomány legalacsonyabb késésével rendelkezik. Ezután a Traffic Manager visszaadja a végpontot a DNS-válaszban.

Ahogy azt a [Traffic Manager működése című témakör](traffic-manager-how-it-works.md)ismerteti, Traffic Manager nem kap DNS-lekérdezéseket közvetlenül az ügyfelektől. Ehelyett a DNS-lekérdezések a rekurzív DNS szolgáltatásból származnak, amelyet az ügyfelek a használatára konfiguráltak. Így a "legközelebb" végpont meghatározásához használt IP-cím nem az ügyfél IP-címe, hanem a rekurzív DNS szolgáltatás IP-címe. Ez az IP-cím az ügyfél számára megfelelő proxy.


Traffic Manager rendszeresen frissíti az Internet késési táblázatát a globális Internet és az új Azure-régiók változásainak figyelembevétele érdekében. Az alkalmazás teljesítménye azonban a terhelésnek az interneten keresztüli valós idejű változásai alapján változhat. Teljesítmény-forgalom – az Útválasztás nem figyeli a terhelést egy adott szolgáltatási végponton. Ha egy végpont elérhetetlenné válik, Traffic Manager nem fog szerepelni a DNS-lekérdezési válaszokban.


Megjegyzés:

* Ha a profil több végpontot is tartalmaz ugyanabban az Azure-régióban, akkor a Traffic Manager egyenletesen osztja el a forgalmat az adott régióban elérhető végpontok között. Ha egy régión belül más adatforgalom-eloszlást szeretne használni, használhat [beágyazott Traffic Manager profilokat](traffic-manager-nested-profiles.md)is.
* Ha a legközelebbi Azure-régióban az összes engedélyezett végpont csökken, Traffic Manager áthelyezi a forgalmat a legközelebbi Azure-régióban lévő végpontokra. Ha szeretne megadni egy előnyben részesített feladatátvételi sorozatot, használjon [beágyazott Traffic Manager profilokat](traffic-manager-nested-profiles.md).
* Ha külső végpontokkal vagy beágyazott végpontokkal rendelkező teljesítmény-forgalmi útválasztási módszert használ, meg kell adnia a végpontok helyét. Válassza ki az üzemelő példányhoz legközelebb eső Azure-régiót. Ezek a helyszínek az Internet késési táblázat által támogatott értékek.
* A végpontot kiválasztó algoritmus determinisztikus. Az azonos ügyfélről érkező ismétlődő DNS-lekérdezések ugyanahhoz a végponthoz vannak irányítva. Az ügyfelek általában eltérő rekurzív DNS-kiszolgálókat használnak utazás közben. Lehet, hogy az ügyfél egy másik végponthoz irányítható. Az útválasztást az Internet késési táblájának frissítései is befolyásolhatják. Ezért nem garantálható, hogy az ügyfelek mindig ugyanahhoz a végponthoz legyenek irányítva.
* Az Internet késési táblázatának megváltozásakor észreveheti, hogy egyes ügyfelek egy másik végpontra vannak irányítva. Ez az útválasztási változás pontosabban az aktuális késési információ alapján történik. Ezek a frissítések elengedhetetlenek a teljesítmény-forgalom pontosságának fenntartásához – az Útválasztás az interneten folyamatosan fejlődik.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Földrajzi forgalom – útválasztási módszer

Traffic Manager profilokat úgy konfigurálhatja, hogy a földrajzi útválasztási módszert használják, hogy a felhasználók a DNS-lekérdezéstől származó földrajzi hely alapján átirányítsák a megadott végpontokat (Azure, External vagy nested). Ez az útválasztási módszer lehetővé teszi, hogy megfeleljen az adatszuverenitási felhatalmazásoknak, a tartalmak honosításának & a felhasználói élménynek, valamint a különböző régiókból érkező forgalom mérésének.
Ha egy profil földrajzi útválasztásra van konfigurálva, a profilhoz társított összes végponthoz hozzá kell rendelni egy földrajzi régiót. A földrajzi régió a részletesség szintjének következő szintjein lehet 
- Világ – bármely régió
- Regionális csoportosítás – például Afrika, Közel-Kelet, Ausztrália/csendes-óceáni térség stb. 
- Ország/régió – például Írország, Peru, Hongkong (KKT) stb. 
- Állam/tartomány – például USA – Kalifornia, Ausztrália – Queensland, Canada-Alberta stb. (Megjegyzés: Ez a részletességi szint csak az Ausztráliában, Kanadában és az USA-beli Államokban/tartományokban támogatott.)

Ha egy régiót vagy régiót rendel hozzá egy végponthoz, az ezekből a régiókból érkező kéréseket csak az adott végponthoz irányítja a rendszer. A Traffic Manager a DNS-lekérdezés forrás IP-címét használja annak meghatározásához, hogy a felhasználó melyik régióból kérdezi le. Általában a helyi DNS-feloldó IP-címe, amely a felhasználó lekérdezését teszi elérhetővé.  

![Azure Traffic Manager földrajzi forgalom – útválasztási módszer](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager beolvassa a DNS-lekérdezés forrás IP-címét, és eldönti, hogy melyik földrajzi régióból származik. Ezután megvizsgálja, hogy van-e olyan végpont, amelyhez ez a földrajzi régió hozzá van rendelve. Ez a keresés a legalacsonyabb részletességi szint (állam/tartomány, ahol a támogatott, az ország/régió szintjén) kezdődik, és egészen a legmagasabb szintre, ami a **világ**. A bejárással kapott első egyezés a lekérdezési válaszban visszaadni kívánt végpontként van kiválasztva. Beágyazott típus végpontjának egyeztetése esetén a rendszer az adott gyermek profilon belüli végpontot adja vissza az útválasztási módszer alapján. A következő pontok alkalmazhatók erre a viselkedésre:

- Egy földrajzi régió csak a Traffic Manager-profil egyik végpontján képezhető le, ha az útválasztási típus földrajzi útválasztás. Ez a korlátozás biztosítja, hogy a felhasználók útválasztása determinisztikus, és az ügyfelek olyan forgatókönyveket is engedélyezhetik, amelyekben egyértelmű földrajzi határok szükségesek.
- Ha a felhasználó régiója két különböző végpontok földrajzi leképezése alatt van felsorolva, Traffic Manager kiválasztja a legalacsonyabb részletességgel rendelkező végpontot. Traffic Manager nem fogja figyelembe venni az adott régiótól érkező útválasztási kérelmeket a másik végpontra. Vegyünk például egy földrajzi útválasztási típus-profilt két végponttal – Endpoint1 és Endpoint2. A Endpoint1 úgy van konfigurálva, hogy fogadja az Írországban érkező forgalmat, és a Endpoint2 az Európából érkező forgalom fogadására van konfigurálva. Ha egy kérelem Írországból származik, a rendszer mindig a Endpoint1 irányítja át.
- Mivel egy régió csak egy végponthoz rendelhető, Traffic Manager visszaadja a választ, hogy a végpont állapota Kifogástalan-e.

    >[!IMPORTANT]
    >Erősen ajánlott, hogy a földrajzi útválasztási módszert használó ügyfelek társítsa azokat a beágyazott típusú végpontokkal, amelyekben legalább két végpontot tartalmazó alárendelt profilok találhatók.
- Ha a végpont egyezést talál, és a végpont **leállított** állapotban van, akkor Traffic Manager egy nem adatválaszú választ ad vissza. Ebben az esetben a földrajzi régió hierarchiájában nem végeznek további kereséseket. Ez a viselkedés a beágyazott végpontok típusai esetében is alkalmazható, ha a gyermek profil **leállított** vagy **letiltott** állapotban van.
- Ha egy végpont **letiltott** állapotot jelez, nem kerül bele a régió-egyeztetési folyamatba. Ez a viselkedés a beágyazott végpontok típusai esetében is alkalmazható, ha a végpont **letiltott** állapotban van.
- Ha egy olyan földrajzi régióból érkezik lekérdezés, amely nem rendelkezik leképezéssel a profilban, Traffic Manager egy nem adatválaszú választ ad vissza. Ezért erősen ajánlott a földrajzi útválasztást egy végponttal használni. Ideális esetben beágyazott típusúnak kell lennie legalább két végponton belül a gyermek profilban, és **a régióhoz** hozzá van rendelve. Ez a konfiguráció azt is biztosítja, hogy a rendszer minden olyan IP-címet kezelje, amely nem a régióhoz van rendelve.

Ahogy azt a [Traffic Manager működése című témakör](traffic-manager-how-it-works.md)ismerteti, Traffic Manager nem kap DNS-lekérdezéseket közvetlenül az ügyfelektől. A DNS-lekérdezések a rekurzív DNS szolgáltatásból származnak, amelyet az ügyfelek a használatára konfiguráltak. Ezért a régió meghatározásához használt IP-cím nem az ügyfél IP-címe, hanem a rekurzív DNS-szolgáltatás IP-címe. Ez az IP-cím az ügyfél számára megfelelő proxy.

### <a name="faqs"></a>Gyakori kérdések

* [Mik azok a használati esetek, amikor a földrajzi útválasztás hasznos?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Hogyan eldönteni, hogy kell-e használni a teljesítmény-útválasztási módszert vagy a földrajzi útválasztási módszert?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Milyen régiókat támogat a Traffic Manager földrajzi útválasztáshoz?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Hogyan határozza meg a Traffic Manager, hogy a felhasználók honnan kérdezik le?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Garantált, hogy Traffic Manager képes pontosan meghatározni a felhasználó pontos földrajzi helyét minden esetben?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [A végpontnak fizikailag ugyanabban a régióban kell lennie, mint a földrajzi útválasztáshoz konfiguráltnak?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Rendelhetek földrajzi régiókat olyan profilokban lévő végpontokhoz, amelyek nem a földrajzi útválasztásra vannak konfigurálva?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Miért kapok hibaüzenetet, amikor megpróbálom módosítani egy meglévő profil útválasztási módszerét a földrajzi helyekre?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Ezért erősen ajánlott, hogy az ügyfelek beágyazott profilokat hozzanak létre a földrajzi útválasztást engedélyező profilhoz tartozó végpontok helyett?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Van olyan korlátozás az API-verzióra, amely támogatja ezt az útválasztási típust?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Többértékű forgalom – útválasztási módszer
A többértékű **forgalom –** útválasztási módszer lehetővé teszi, hogy több kifogástalan állapotú végpontot KAPJON egyetlen DNS-lekérdezési válaszban. Ez a konfiguráció lehetővé teszi, hogy a hívó ügyféloldali újrapróbálkozásokat hajtson végre más végpontokkal abban az esetben, ha egy visszaadott végpont nem válaszol. Ez az elrendezés javíthatja a szolgáltatás rendelkezésre állását, és csökkentheti a kifogástalan állapotú végpontok DNS-beli lekérdezésével járó késést. A többértékű útválasztási módszer csak akkor működik, ha az összes "External" típusú végpontot IPv4-vagy IPv6-címként adja meg. Ha egy lekérdezés érkezik ehhez a profilhoz, a rendszer minden kifogástalan állapotú végpontot visszaadott, és egy konfigurálható maximális visszaküldési számra vonatkozik.

### <a name="faqs"></a>Gyakori kérdések

* [Mik azok a használati esetek, ahol a többértékű útválasztás hasznos?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Hány végpontot ad vissza a rendszer a többértékű útválasztás használata esetén?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Ugyanazokat a végpontokat kapják meg, amikor többértékű útválasztást használunk?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Alhálózati forgalom – útválasztási módszer
Az **alhálózati** forgalom – útválasztási módszer lehetővé teszi a végfelhasználói IP-címtartományok készletének leképezését egy profil adott végpontjai számára. Ha a Traffic Manager DNS-lekérdezést kap ehhez a profilhoz, akkor a rendszer megvizsgálja a kérelem forrás IP-címét. Ezután meghatározza, hogy melyik végponthoz van hozzárendelve, és a lekérdezési válaszban visszaadja a végpontot. A legtöbb esetben a forrás IP-címe a hívó által használt DNS-feloldó.

A végponthoz hozzárendelni kívánt IP-cím megadható CIDR tartományként (például 1.2.3.0/24), vagy címtartományként (például 1.2.3.4-5.6.7.8). A végpontokhoz társított IP-tartományoknak egyedinek kell lenniük a profilon belül. A címtartomány nem rendelkezhet átfedéssel egy másik végpont IP-címével ugyanabban a profilban.
Ha címtartomány nélküli végpontot határoz meg, akkor az tartalékként működik, és a többi alhálózatról is átirányítja a forgalmat. Ha nem tartalmaz tartalék végpontot, Traffic Manager a nem meghatározott tartományokra vonatkozó adatválaszokat küld. Javasoljuk, hogy adjon meg egy tartalék végpontot, amely biztosítja, hogy az összes lehetséges IP-tartomány meg legyen adva a végpontok között.

Az alhálózati útválasztással különböző felhasználói élményt biztosíthat az adott IP-területről csatlakozó felhasználók számára. Tegyük fel például, hogy a vállalati iroda összes kérését egy másik végpontra irányítja át. Ez az útválasztási módszer különösen akkor hasznos, ha az alkalmazás csak belső verzióját próbálja tesztelni. Egy másik használati helyzet az, amikor egy adott internetszolgáltatóval csatlakozó felhasználóknak más felületet szeretne biztosítani (például blokkolni szeretné egy adott internetszolgáltató ügyfeleit).

### <a name="faqs"></a>Gyakori kérdések

* [Mik azok a használati esetek, amikor az alhálózat-útválasztás hasznos?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Hogyan ismeri Traffic Manager a végfelhasználó IP-címét?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Hogyan adhatok meg IP-címeket alhálózati útválasztás használatakor?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Hogyan adhatok tartalék végpontot alhálózati útválasztás használatakor?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Mi történik, ha egy végpont le van tiltva egy alhálózat útválasztási típusának profiljában?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan fejleszthet magas rendelkezésre állású alkalmazásokat [Traffic Manager Endpoint monitoring](traffic-manager-monitoring.md) használatával