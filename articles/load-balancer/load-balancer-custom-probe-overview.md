---
title: Állapot-mintavételek a szolgáltatás skálázásához és magas rendelkezésre állásának biztosításához
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan használhatók az állapot-mintavételek a Azure Load Balancer mögötti példányok figyeléséhez
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a008d7b26738b9552a7a43ab026391bd9afe0aa8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780943"
---
# <a name="load-balancer-health-probes"></a>A Load Balancer állapotmintái

Ha a Azure Load Balancer terheléselosztási szabályokat használ, meg kell adnia az állapot-mintavételt, hogy az Load Balancer a háttérbeli végpont állapotának észlelését.  Az állapot-mintavétel és a mintavételi válaszok konfigurációja határozza meg, hogy mely háttérbeli készlet-példányok kapják meg az új folyamatokat. Az állapot-mintavételek segítségével észlelhető egy alkalmazás meghibásodása egy háttér-végponton. Egyéni választ is létrehozhat az állapot-mintavételre, és a Flow Control állapot-ellenőrzésével kezelheti a terhelést vagy a tervezett állásidőt. Ha az állapot-mintavétel sikertelen, Load Balancer leállítja az új folyamatok küldését a megfelelő sérült példányra. A kimenő kapcsolatra nincs hatással, csak a bejövő kapcsolatok érintettek.

Az állapotadatok több protokollt is támogatnak. Egy adott állapot mintavételi protokolljának rendelkezésre állása Load Balancer SKU-tól függ.  Emellett a szolgáltatás működése Load Balancer SKU-ban is változik a táblázatban látható módon:

| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| **[Mintavételi típusok](#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Mintavételezési viselkedés](#probedown)** | Az összes TCP-folyamat folytatódik. | Az összes TCP-folyamat lejár. | 


>[!IMPORTANT]
>Tekintse át a dokumentumot teljes egészében, beleértve az alábbi fontos [tervezési útmutatót](#design) egy megbízható szolgáltatás létrehozásához.

>[!IMPORTANT]
>Load Balancer állapot-mintavételek az IP-168.63.129.16 származnak, és nem szabad blokkolni a mintavételek számára a példányok jelölését.  A részletekért tekintse át a mintavételi [forrás IP-címét](#probesource) .

>[!IMPORTANT]
>A beállított időtúllépési küszöbértéktől függetlenül a HTTP (S) Load Balancer Health mintavételek automatikusan lekérik a példányokat, ha a kiszolgáló olyan állapotkódot ad vissza, amely nem HTTP 200 OK, vagy ha a kapcsolatok a TCP-visszaállítással megszakadnak.

## <a name="probe-configuration"></a><a name="probes"></a>Mintavételi konfiguráció

Az állapot-mintavételi konfiguráció a következő elemekből áll:

- Az egyéni mintavételek közötti intervallum időtartama
- Azoknak a mintavételi válaszoknak a száma, amelyeket meg kell figyelni a mintavétel más állapotba való átállása előtt
- A mintavétel protokollja
- A mintavétel portja
- Http-alapú http-(S-) mintavételek használatakor használandó HTTP-elérési út

>[!NOTE]
>Azure PowerShell, Azure CLI, sablonok vagy API használata esetén a mintavételi definíció nem kötelező, vagy nincs bejelölve. A mintavétel-ellenőrzési tesztek csak az Azure Portal használata esetén hajthatók végre.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Az alkalmazási jel ismertetése, a jel észlelése és a platform reakciója

A mintavételi válaszok száma mindkettőre vonatkozik

- azon sikeres mintavételek száma, amelyek lehetővé teszik, hogy a példány megjelölve legyen megjelölve, és
- a példányok leállításának megjelölését kiváltó időkorlátok száma.

A megadott időkorlát-és intervallum-értékek határozzák meg, hogy egy példány fel vagy le lesz-e jelölve.  Az Intervallum időtartamának szorzata a mintavételi válaszok száma határozza meg azt az időtartamot, ameddig a mintavételi válaszokat észlelni kell.  A szolgáltatás pedig a szükséges mintavételek elvégzése után fog reagálni.

A viselkedést egy példával is illusztráljuk. Ha a mintavételi válaszok számát a 2 értékre, az intervallum pedig 5 másodpercre állította be, akkor ez azt jelenti, hogy a mintavételi időtúllépési hibákat 10 másodpercen belül meg kell figyelni.  Mivel a mintavétel elküldésének időpontja nincs szinkronizálva az alkalmazás állapotának megváltozásakor, a következő két forgatókönyv szerint lehet megkötni az időt:

1. Ha az alkalmazás az első mintavétel előtt elindít egy időtúllépési mintavételi választ, az események észlelése 10 másodpercet vesz igénybe (2 x 5 másodperc), valamint az alkalmazás időtartamát, amely az első mintavétel megérkezése után időtúllépést jelez.  Feltételezzük, hogy az észlelés valamivel több mint 10 másodpercet vesz igénybe.
2. Ha az alkalmazás elindít egy időtúllépési mintavételi választ az első mintavétel megérkezése után, az események észlelése nem kezdődik el, amíg a következő mintavétel megérkezik (és időtúllépés esetén), plusz egy 10 másodpercet (2 x 5 másodperces intervallum).  Feltételezheti, hogy ez az észlelés 15 másodpercen belül eltarthat.

Ebben a példában az észlelést követően a platform némi időt vesz igénybe, hogy reagáljon erre a változásra.  Ez a következőtől függ: 

1. Amikor az alkalmazás megkezdi az állapot módosítását, és
2. Ha ez a változás észlelhető, és megfelel a szükséges feltételeknek (a mintavételek száma a megadott időközönként történik), és
3. Ha az észlelést a platformon keresztül közölték 

feltételezheti, hogy az időtúllépési mintavételre adott válasz legalább 10 másodpercnél kevesebb, és legfeljebb 15 másodpercre van szüksége, hogy reagáljon az alkalmazásban lévő jel változására.  Ez a példa arra szolgál, hogy bemutassa, mi történik, de nem lehet előre jelezni a fenti durva útmutatón túli pontos időtartamot.

>[!NOTE]
>Az állapot mintavétele a háttér-készlet összes futó példányát fogja felismerni. Ha egy példány leáll, a rendszer nem ellenőrzi, amíg újra nem indítják.

## <a name="probe-types"></a><a name="types"></a>Mintavételi típusok

Az állapot-mintavétel által használt protokoll a következők egyikére konfigurálható:

- [TCP-figyelők](#tcpprobe)
- [HTTP-végpontok](#httpprobe)
- [HTTPS-végpontok](#httpsprobe)

Az elérhető protokollok a használt Load Balancer SKU-tól függnek:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| **Standard termékváltozat** |    &#9989; |   &#9989; |   &#9989; |
| **Alapszintű termékváltozat** |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a> TCP-mintavétel

A TCP-mintavételek egy kétirányú, nyitott TCP-kézfogás és a megadott port használatával kezdeményezik a csatlakozást.  A TCP-mintavételek egy négyirányú záró TCP-kézfogással szüntetik meg a kapcsolatokat.

A mintavételi időköz minimális értéke 5 másodperc, a nem megfelelő állapotú válaszok minimális száma pedig 2.  Az összes intervallum teljes időtartama nem haladhatja meg a 120 másodpercet.

A TCP-mintavétel meghiúsul, ha:
* A példányon a TCP-figyelő nem válaszol az időtúllépési időszak alatt.  A mintavétel az időtúllépési kérelmek száma alapján van megjelölve, és a rendszer a mintavétel megjelölése előtt úgy konfigurálta, hogy megválaszolatlan állapotba lépjen.
* A mintavétel TCP-visszaállítást kap a példányból.

Az alábbi ábrán látható, hogyan fejezheti ki az ilyen típusú mintavételi konfigurációkat egy Resource Manager-sablonban:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a>HTTP/HTTPS-mintavétel

>[!NOTE]
>A HTTPS-mintavétel csak [standard Load Balancer](./load-balancer-overview.md)esetében érhető el.

A HTTP-és HTTPS-mintavételek a TCP-mintavételre épülnek, és a megadott elérési úttal rendelkező HTTP GET-t adnak ki. Mindkét mintavételi módszer támogatja a HTTP GET relatív elérési útját. A HTTPS-mintavételek ugyanazok, mint a HTTP-mintavételek Transport Layer Security (TLS, korábbi nevén SSL) burkoló hozzáadásával. Az állapot-mintavétel akkor van megjelölve, ha a példány a 200 HTTP-állapottal válaszol az időtúllépési időszakon belül.  Az állapot mintavétele alapértelmezés szerint 15 másodpercenként megkísérli a beállított állapot mintavételi portjának ellenőrzését. A mintavételi időköz minimális értéke 5 másodperc. Az összes intervallum teljes időtartama nem haladhatja meg a 120 másodpercet.

A HTTP-/HTTPS-mintavételek is hasznosak lehetnek a saját logikájának megvalósításához, hogy eltávolítsa a példányokat a terheléselosztó forgása alól, ha a mintavételi port egyben a szolgáltatás figyelője is. Dönthet például úgy, hogy eltávolít egy példányt, ha az 90%-os CPU felett van, és nem 200 HTTP-állapotot ad vissza. 

> [!NOTE] 
> A HTTPS-mintavétel olyan tanúsítványok használatát igényli, amelyeken alapul a teljes láncban a SHA256 minimális aláírási kivonata.

Ha Cloud Services használ, és w3wp.exe használó webes szerepkörökkel rendelkezik, akkor a webhely automatikus figyelése is elérhető. A webhely kódjában fellépő hibák nem 200 állapotot adnak vissza a terheléselosztó-próbára.

A HTTP/HTTPS-mintavétel sikertelen, ha:
* A mintavételi végpont a 200-tól eltérő HTTP-választ ad vissza (például 403, 404 vagy 500). Ekkor a rendszer azonnal megjelöli az állapot-mintavételt. 
* A mintavételi végpontok nem válaszolnak a minimális mintavételi intervallumban és a 30 másodperces időkorlát alatt. Több mintavételi kérelem is megválaszolható, mielőtt a mintavétel nem fut állapotba kerül, és az összes időtúllépési intervallum összegét el nem éri.
* A mintavétel végpontja a TCP alaphelyzetbe állításával zárja be a kapcsolatokat.

Az alábbi ábrán látható, hogyan fejezheti ki az ilyen típusú mintavételi konfigurációkat egy Resource Manager-sablonban:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Vendég ügynök mintavétele (csak klasszikus)

A Cloud Service szerepkörei (feldolgozói szerepkörök és webes szerepkörök) alapértelmezés szerint egy vendég ügynököt használnak a mintavételi figyeléshez.  A vendég ügynök mintavétele a legutóbb konfigurált konfiguráció.  Az állapot-mintavételt mindig explicit módon, TCP-vagy HTTP-mintavételsel kell használni. A vendég ügynök mintavétele nem annyira hatékony, mint a legtöbb alkalmazási forgatókönyvhöz explicit módon meghatározott Szondák.

A vendég ügynök mintavétele ellenőrzi a vendég ügynököt a virtuális gépen belül. Ezután csak akkor figyeli és válaszol a HTTP 200 OK válaszával, ha a példány üzemkész állapotban van. (Más állapotok foglalt, újrahasznosíthatók vagy leállnak.)

További információ: [a Service definition-fájl (csdef) konfigurálása az állapot](/previous-versions/azure/reference/ee758710(v=azure.100)) -mintavételhez vagy az [első lépések a Cloud Services nyilvános terheléselosztó létrehozásával](/previous-versions/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Ha a vendég ügynök nem tud válaszolni a http 200 OK értékre, a terheléselosztó nem válaszoló jelöli meg a példányt. Ezután leállítja a folyamatok küldését erre a példányra. A terheléselosztó továbbra is a példányt vizsgálja. 

Ha a vendég ügynök HTTP 200-re válaszol, a terheléselosztó újra elküldi az új folyamatokat erre a példányra.

Webes szerepkör használata esetén a webhely kódja általában w3wp.exeban fut, amelyet az Azure-háló vagy a vendég ügynök nem figyel. A w3wp.exe hibái (például HTTP 500-válaszok) nem jelentik a vendég ügynöknek. Ennek következtében a terheléselosztó nem veszi át a példányt a rotációs állapotból.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Mintavételezési viselkedés

A TCP, a HTTP és a HTTPS állapotú tesztek kifogástalannak minősülnek, és a háttér-végpontot a következőképpen kell megjelölni:

* Az állapot mintavétele a virtuális gép indítása után sikeres volt.
* A háttérbeli végpont megfelelő állapotának megjelöléséhez szükséges mintavételek száma teljesült.

Minden olyan háttér-végpont, amely kifogástalan állapotot ért el, jogosult az új folyamatok fogadására.  

> [!NOTE]
> Ha az állapot-mintavétel ingadozik, a terheléselosztó a háttér-végpont Kifogástalan állapotba való visszaállítását követően is megvárja a problémát. Ez a további várakozási idő védi a felhasználót és az infrastruktúrát, és szándékos szabályzat.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Mintavételezési viselkedés

### <a name="tcp-connections"></a>TCP-kapcsolatok

Az új TCP-kapcsolatok sikeresen megmaradnak az egészséges háttérbeli végponton.

Ha a háttérbeli végpont állapota nem sikerül, a rendszer a háttérbeli végponthoz tartozó TCP-kapcsolatokat folytatja.

Ha a háttér-készlet összes példányának összes mintavétele sikertelen, a rendszer nem küld új folyamatokat a háttér-készletbe. Standard Load Balancer lehetővé teszi a létesített TCP-folyamatok folytatását.  Az alapszintű Load Balancer leállítja az összes meglévő TCP-folyamatot a háttér-készletbe.
 
Load Balancer egy átmenő szolgáltatás (nem szakítja meg a TCP-kapcsolatokat), és a folyamat mindig az ügyfél és a virtuális gép vendég operációs rendszere és alkalmazása között van. Az összes mintavétel nélküli készlet esetén a frontend nem válaszol a TCP-kapcsolatok nyílt kísérleteinek (SYN) állapotára, mivel nincs kifogástalan háttér-végpont a folyamat fogadásához és a SYN-ACK-vel való reagáláshoz.

### <a name="udp-datagrams"></a>UDP-datagramok

Az UDP-datagramok kifogástalan háttérbeli végpontokra lesznek továbbítva.

Az UDP nem kapcsolódik az UDP-hez, és nincs nyomon követett folyamat. Ha a háttérbeli végpont állapota nem sikerül, a meglévő UDP-folyamatok egy másik Kifogástalan állapotba kerülnek a háttér-készletben.

Ha a háttér-készlet összes példányának összes mintavétele sikertelen, a meglévő UDP-folyamatok megszűnnek az alapszintű és a standard Load Balancer esetében.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Mintavételi forrás IP-címe

A Load Balancer egy elosztott szondázás szolgáltatást használ a belső állapot modelljéhez. A szondázás szolgáltatás minden olyan gazdagépen megtalálható, ahol a virtuális gépek, és igény szerint programozhatók, hogy az ügyfél konfigurációján alapuló rendszerállapot-mintavételek készüljenek. Az állapot mintavételi forgalma közvetlenül az állapot-mintavételt és az ügyfél virtuális gépet létrehozó szondázás szolgáltatás között van. Az összes Load Balancer állapot-mintavétel a forrásként megadott IP-168.63.129.16 származik.  Az IP-címtartomány egy olyan VNet belül használható, amely nem RFC1918 terület.  Globálisan fenntartott Microsoft tulajdonú IP-cím használata csökkenti az IP-címek ütközését a VNet belül használt IP-címtartomány miatt.  Ez az IP-cím minden régióban azonos, és nem változik, és nem biztonsági kockázat, mert csak a belső Azure platform összetevő képes a csomagok forrására ebből az IP-címről. 

A AzureLoadBalancer szolgáltatás címkéje azonosítja ezt a forrás IP-címet a [hálózati biztonsági csoportokban](../virtual-network/network-security-groups-overview.md) , és alapértelmezés szerint engedélyezi az állapot-mintavételi forgalmat.

Load Balancer Health [-mintavételen kívül a következő műveletek ezt az IP-címet használják](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Lehetővé teszi a virtuálisgép-ügynök számára a kommunikációt a platformmal, hogy jelezze azt "Ready" állapotban
- Lehetővé teszi a kommunikációt a DNS virtuális kiszolgálóval a szűrt névfeloldás biztosításához olyan ügyfelek számára, akik nem határoznak meg egyéni DNS-kiszolgálókat.  Ez a szűrés biztosítja, hogy az ügyfelek csak az üzembe helyezésük gazdagépeit oldják fel.
- Lehetővé teszi, hogy a virtuális gép dinamikus IP-címet szerezzen be a DHCP szolgáltatásból az Azure-ban.

## <a name="design-guidance"></a><a name="design"></a> Tervezési útmutató

A rendszer az állapot-mintavételt használja a szolgáltatás rugalmas és méretezhetővé tételéhez. A helytelen konfiguráció vagy a rossz kialakítási minta befolyásolhatja a szolgáltatás rendelkezésre állását és méretezhetőségét. Tekintse át ezt a teljes dokumentumot, és gondolja át, milyen hatással van a forgatókönyvre, ha a mintavételi válasz megjelölve vagy megjelölve van, és hogyan befolyásolja az alkalmazási forgatókönyv rendelkezésre állását.

Amikor megtervezi az alkalmazáshoz tartozó állapotfigyelő modellt, a háttér-végponton található portot kell mintavételre felhasználnia, amely tükrözi az adott példány állapotát __és__ az Ön által biztosított alkalmazást.  Az alkalmazás portjának és a mintavételi portnak nem kell megegyeznie.  Bizonyos helyzetekben érdemes lehet a mintavételi port más, mint az alkalmazás által a szolgáltatás által biztosított port.  

Esetenként hasznos lehet az alkalmazás számára, hogy az állapot-mintavételi választ ne csak az alkalmazás állapotának észlelésére, hanem közvetlenül a Load Balancer is jelezze, ha a példánynak fogadnia kell vagy nem kap új folyamatokat.  A mintavételi választ úgy is kezelheti, hogy az alkalmazás ellennyomás hozzon létre, és szabályozza az új folyamatok egy példányra való továbbítását az állapot-ellenőrzés elvégzésével, illetve az alkalmazás karbantartásának előkészítésével és a forgatókönyv kiürítésének elindításával.  Standard Load Balancer használatakor a mintavétel nélküli jelek mindig lehetővé [teszik a TCP](#probedown) -forgalom folytatását az Üresjárati időkorlát vagy a kapcsolat lezárása után. 

Az UDP-terheléselosztáshoz egyéni állapot-mintavételi jelet kell kiállítani a háttérbeli végpontból, és a megfelelő figyelőhöz tartozó TCP-, HTTP-vagy HTTPS-alapú állapot-mintavételt kell használnia, hogy tükrözze az UDP-alkalmazás állapotát.

Ha a [hektáros portok terheléselosztási szabályait](load-balancer-ha-ports-overview.md) [standard Load Balancerekkel](./load-balancer-overview.md)használja, az összes port terheléselosztásra kerül, és egyetlen állapot-mintavételi válasznak kell tükröznie a teljes példány állapotát.

Ne fordítsa le vagy ne vezessen olyan állapotú mintavételt az VNet egy másik példányára, amely megkapja az állapot-mintavételt, mivel ez a konfiguráció lépcsőzetes hibákat eredményezhet a forgatókönyvben.  Vegye figyelembe a következő helyzetet: a külső gyártótól származó készülékek készletét egy Load Balancer erőforrás háttér-készletében helyezi üzembe, amely biztosítja a berendezések méretezését és redundanciát, és az állapot-mintavétel úgy van konfigurálva, hogy mintavételt végezzen a külső gyártótól származó, vagy a berendezés mögött más virtuális gépekre fordított porton.  Ha ugyanazt a portot használja, amellyel lefordítja vagy proxyul kéri a többi virtuális gépre irányuló kéréseket a készülék mögött, akkor a készülék mögötti egyetlen virtuális gépről érkező összes mintavételi válasz megjelöli a berendezést. Ez a konfiguráció a teljes alkalmazási forgatókönyv lépcsőzetes meghibásodását eredményezheti a berendezés mögötti egyetlen háttérbeli végpont eredményeként.  Az trigger lehet egy időszakos mintavételi hiba, amely Load Balancer az eredeti célhely (a berendezés példánya) megjelölését, és a teljes alkalmazás-forgatókönyv letiltását eredményezi. A készülék állapotának mintavétele helyette. A mintavétel kiválasztásával megállapítható, hogy az állapot jelzése a hálózati virtuális berendezések (NVA-) forgatókönyvek esetében fontos szempont-e

Ha nem engedélyezi a mintavétel [forrás IP-címét](#probesource) a tűzfal házirendjeiben, az állapot-mintavétel sikertelen lesz, mert nem tudja elérni a példányát.  A Load Balancer az állapot-mintavételi hiba miatt megjelöli a példányt.  Ez a helytelen konfiguráció a terheléselosztási alkalmazás meghibásodását okozhatja.

Ahhoz, hogy a Load Balancer állapotának mintavétele megtörténjen a példányon, engedélyeznie **kell** ezt az IP-címet bármely Azure-beli [hálózati biztonsági csoportban](../virtual-network/network-security-groups-overview.md) és helyi tűzfal-házirendben.  Alapértelmezés szerint minden hálózati biztonsági csoport tartalmazza az AzureLoadBalancer az állapot-mintavételi forgalom engedélyezésére szolgáló [szolgáltatási címkét](../virtual-network/network-security-groups-overview.md#service-tags) .

Ha szeretné tesztelni az állapot-mintavételi hibát, vagy egy különálló példányt szeretne megjelölni, akkor a [hálózati biztonsági csoportokkal](../virtual-network/network-security-groups-overview.md) explicit módon blokkolhatja az állapot-mintavételt (a célport vagy a [forrás IP-címét](#probesource)), és szimulálhatja a mintavétel hibáját.

Ne konfigurálja a VNet a 168.63.129.16-t tartalmazó Microsoft tulajdonú IP-címtartományt.  Az ilyen konfigurációk ütköznek az állapot-mintavétel IP-címével, és a forgatókönyv meghibásodását okozhatja.

Ha több csatoló is van a virtuális gépen, akkor biztosítania kell, hogy válaszoljon a mintavételre azon a felületen, amelyen a kapott.  Előfordulhat, hogy a hálózati címfordítást a virtuális gépen, illesztőfelület alapján kell lefordítani.

Ne engedélyezze a [TCP-időbélyeget](https://tools.ietf.org/html/rfc1323).  A TCP-időbélyegek engedélyezése a virtuális gép vendég operációs rendszerének TCP-verem által eldobott TCP-csomagok meghibásodása esetén az állapot-ellenőrzéseket okozhatja, ami Load Balancer megjelöli a megfelelő végpontot.  A TCP-időbélyegek alapértelmezés szerint engedélyezve vannak a biztonsági megerősített virtuális gépek rendszerképein, és le kell tiltani őket.

## <a name="monitoring"></a>Figyelés

Mind a nyilvános, mind a belső [standard Load Balancer](./load-balancer-overview.md) a végpontok és a háttérbeli végpontok állapotának tesztelési állapota többdimenziós metrikák Azure monitoron keresztül. Ezeket a metrikákat más Azure-szolgáltatások vagy-partneri alkalmazások is felhasználhatják. 

Az alapszintű nyilvános Load Balancer a háttér-készletek alapján összesíti az állapot mintavételi állapotát Azure Monitor naplókon keresztül.  A belső alapszintű terheléselosztó számára nem érhetők el Azure Monitor naplók.  [Azure monitor naplók](load-balancer-monitor-log.md) használatával megtekintheti a nyilvános terheléselosztó mintavételi állapotának állapotát és a mintavételek darabszámát. A naplózás a Power BI vagy az Azure Operational Insights használatával biztosít statisztikai adatokat a terheléselosztó állapotáról.

## <a name="limitations"></a>Korlátozások

- A HTTPS-mintavételek nem támogatják az ügyféltanúsítvány-alapú kölcsönös hitelesítést.
- Ha a TCP-időbélyegek engedélyezve vannak, az állapot-mintavételek sikertelenek lesznek.

## <a name="next-steps"></a>Következő lépések

- További tudnivalók a [Standard Load Balancerről](./load-balancer-overview.md)
- [Ismerkedés a nyilvános Load Balancer létrehozásával a Resource Managerben a PowerShell használatával](quickstart-load-balancer-standard-public-powershell.md)
- [REST API az állapot-mintavételhez](/rest/api/load-balancer/loadbalancerprobes/)
- Új állapot-mintavételi képességek kérése [Load Balancer uservoice](https://aka.ms/lbuservoice)
