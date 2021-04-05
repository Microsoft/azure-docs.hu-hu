---
title: Az Azure Virtual Network NAT-kapcsolat hibáinak megoldása
titleSuffix: Azure Virtual Network
description: Virtual Network NAT problémáinak elhárítása.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 70410e58acb30c7694e6fe4a6dcaff57bee98607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223431"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Az Azure Virtual Network NAT-kapcsolat hibáinak megoldása

Ez a cikk segítséget nyújt a rendszergazdáknak a kapcsolódási problémák diagnosztizálásában és megoldásában Virtual Network NAT használatakor.

## <a name="problems"></a>Problémák

* [SNAT-kimerülés](#snat-exhaustion)
* [Az ICMP-pingelés sikertelen](#icmp-ping-is-failing)
* [Csatlakozási hibák](#connectivity-failures)
* [IPv6-együttélés](#ipv6-coexistence)
* [A csatlakoztatás nem a NAT-átjáró IP-címétől származik](#connection-doesnt-originate-from-nat-gateway-ips)

A problémák megoldásához kövesse az alábbi szakasz lépéseit.

## <a name="resolution"></a>Feloldás

### <a name="snat-exhaustion"></a>SNAT-kimerülés

Egyetlen [NAT Gateway-erőforrás](nat-gateway-resource.md) 64 000 akár 1 000 000 egyidejű folyamatra is képes.  Minden IP-cím 64 000 SNAT-portot biztosít a rendelkezésre álló leltárhoz. A NAT-átjáró erőforrásain legfeljebb 16 IP-címet használhat.  A SNAT mechanizmus részletes ismertetését [itt](nat-gateway-resource.md#source-network-address-translation) találja.

A SNAT-kimerülés kiváltó oka gyakran a kimenő kapcsolat létesítésének, kezelésének vagy konfigurálható időzítőknek az alapértelmezett értékekről való változásának egy anti-mintázata.  Alaposan olvassa át ezt a szakaszt.

#### <a name="steps"></a>Lépések

1. Ellenőrizze, hogy az alapértelmezett üresjárati időkorlátot 4 percnél magasabb értékre módosította-e.
2. Vizsgálja meg, hogy az alkalmazás hogyan hozza létre a kimenő kapcsolatokat (például a kód felülvizsgálatát vagy a csomagok rögzítését). 
3. Állapítsa meg, hogy a tevékenység várható viselkedés-e, vagy hogy az alkalmazás nem működik-e.  Az eredmények alátámasztására a Azure Monitor [mérőszámait](nat-metrics.md) használhatja. A SNAT-kapcsolatok metrikájának "sikertelen" kategóriáját használja.
4. Értékelje ki, hogy a megfelelő mintákat követi-e.
5. Annak kiértékelése, hogy a SNAT-portok kimerülését csökkenteni kell-e a NAT-átjáró erőforrásához rendelt további IP-címekkel.

#### <a name="design-patterns"></a>Tervezési minták

Mindig használja ki a kapcsolatok újrafelhasználását és a kapcsolatok készletezését, amikor csak lehetséges.  Ezek a minták elkerülik az erőforrás-kimerülési problémákat, és kiszámítható viselkedést eredményeznek. A mintákhoz tartozó primitívek számos fejlesztői könyvtárban és keretrendszerben találhatók.

_**Megoldás:**_ A megfelelő minták és ajánlott eljárások használata

- A NAT-átjáró erőforrásainak alapértelmezett TCP üresjárati időkorlátja 4 perc.  Ha ez a beállítás magasabb értékre változik, a NAT továbbra is hosszabb folyamatokra tart, és [szükségtelen nyomást okozhat a SNAT-porton](nat-gateway-resource.md#timers).
- Az atomi kérelmek (egy-egy kérelem/kapcsolatonként) nem megfelelő kialakítási megoldás. Az ilyen jellegű Anti-pattern korlátozza a méretezést, csökkenti a teljesítményt, és csökkenti a megbízhatóságot. Ehelyett használja újra a HTTP/S-kapcsolatokat a kapcsolatok és a társított SNAT-portok számának csökkentése érdekében. Az alkalmazások méretezése a TLS használata esetén a kisebb kézfogások, a terhelési és a titkosítási műveletek költségeinek növekedésével és teljesítményével nő.
- A DNS számos különálló folyamatot képes bevezetni a köteten, ha az ügyfél nem gyorsítótárazza a DNS-feloldók eredményét. Használja a gyorsítótárazást.
- Az UDP-folyamatok (például a DNS-lekérdezések) lefoglalják a SNAT-portokat az Üresjárati időkorlát időtartamára. Minél hosszabb az Üresjárati időkorlát, annál nagyobb a terhelés a SNAT-portokon. Használjon rövid üresjárati időkorlátot (például 4 perc).
- A kapcsolatok kötetét a kapcsolódási készletek használatával formázhatja.
- Soha ne hagyjon le csendes TCP-forgalmat, és a TCP-időzítők használatával törölje a folyamatot. Ha nem engedi, hogy a TCP explicit módon lezárta a kapcsolatot, az állapot a közbenső rendszerek és végpontok számára is lefoglalva marad, és a SNAT portok nem érhetők el más kapcsolatok számára. Ez a minta aktiválhatja az alkalmazások hibáit és SNAT a kimerültséget. 
- Ne változtassa meg az operációs rendszer szintű TCP-hez kapcsolódó időzítő-értékeket a hatás szakértői ismerete nélkül. Amíg a TCP-verem helyreáll, az alkalmazás teljesítménye negatív hatással lehet, ha a kapcsolatok végpontjai eltérő elvárásokkal rendelkeznek. Az időzítők módosítására irányuló vágy általában egy mögöttes tervezési probléma jele. Tekintse át a következő javaslatokat.

A SNAT-kimerültség az alapul szolgáló alkalmazásban más anti-mintákkal is felerősíthető. Tekintse át ezeket a további mintákat és ajánlott eljárásokat a szolgáltatás méretezésének és megbízhatóságának javítása érdekében.

- Fedezze fel a [TCP Üresjárati időkorlát](nat-gateway-resource.md#timers) alacsonyabb értékekre való csökkentésének hatásait, beleértve az alapértelmezett üresjárati időkorlátot (4 perc), hogy a SNAT-készletet a korábbi
- A hosszú ideig futó műveletek esetében érdemes [aszinkron lekérdezési mintákat](/azure/architecture/patterns/async-request-reply) felvenni a kapcsolatok erőforrásainak más műveletekhez való felszabadítására.
- A hosszú élettartamú folyamatokban (például az újrafelhasznált TCP-kapcsolatok esetében) a TCP-Keepalives vagy az Keepalives kell használnia, hogy elkerülje a köztes rendszerek időtúllépését. Az Üresjárati időkorlát növelése egy utolsó megoldás, amely nem oldja fel a kiváltó okot. A hosszú időtúllépés miatt alacsony a meghibásodás, ha az időtúllépés lejár, és késlelteti és szükségtelen hibákat jelez.
- A kecses [újrapróbálkozási mintákat](/azure/architecture/patterns/retry) az átmeneti hibák vagy a meghibásodások helyreállítása során el kell kerülni az agresszív újrapróbálkozások/törések elkerülése érdekében.
Egy új TCP-kapcsolat létrehozása minden HTTP-művelethez (más néven "Atomic connections") egy anti-minta.  Az Atomic Connections szolgáltatás megakadályozza, hogy az alkalmazás a jól méretezhető és a hulladék erőforrásokat is kihasználja.  Mindig több műveletet kell ugyanabba a hálózatba csatlakoztatni.  Az alkalmazás a tranzakciós sebességet és az erőforrások költségét is kihasználja.  Ha az alkalmazás Transport Layer encryption (például TLS) protokollt használ, az új kapcsolatok feldolgozásának jelentős díja van.  Tekintse át az [Azure Cloud design-mintákat](/azure/architecture/patterns/) az ajánlott eljárások további mintáinak megtekintéséhez.

#### <a name="additional-possible-mitigations"></a>További lehetséges enyhítések

_**Megoldás:**_ A kimenő kapcsolatok méretezése az alábbiak szerint történik:

| Eset | Bizonyíték |Kockázatcsökkentés |
|---|---|---|
| A SNAT-portok és a SNAT-portok kimerülése a magas kihasználtságú időszakok során tapasztalható. | A (z) Azure Monitor SNAT-kapcsolatok [metrikájának](nat-metrics.md) "sikertelen" kategóriája az idő és a magas csatlakozási kötet esetében átmeneti vagy állandó hibákat mutat be.  | Állapítsa meg, hogy adhat-e további nyilvános IP-cím erőforrásokat vagy nyilvános IP-előtag-erőforrásokat. Ez a Hozzáadás legfeljebb 16 IP-címet tesz lehetővé a NAT-átjáró számára. Ez a Hozzáadás további leltárt nyújt a rendelkezésre álló SNAT-portok (64 000/IP-címek) számára, és lehetővé teszi a forgatókönyv további skálázását.|
| Már 16 IP-címet adott meg, és továbbra is SNAT a portok kimerülése. | További IP-cím hozzáadására tett kísérlet sikertelen. Az IP-címek teljes száma a nyilvános IP-címek erőforrásaiból vagy a nyilvános IP-előtag erőforrásaiból összesen meghaladja a 16 értéket. | Terjessze az alkalmazási környezetet több alhálózatra, és adjon meg egy NAT Gateway-erőforrást az egyes alhálózatokhoz.  A tervezési minta (ok) újraértékelése az előző [útmutatás](#design-patterns)alapján optimalizálható. |

>[!NOTE]
>Fontos megérteni, hogy miért történik a SNAT kimerültség. Győződjön meg arról, hogy a megfelelő mintákat használja a méretezhető és megbízható forgatókönyvekhez.  Ha további SNAT-portokat ad hozzá egy forgatókönyvhöz anélkül, hogy meg kellene ismernie a kereslet okát, az utolsó megoldásnak kell lennie. Ha nem érti, hogy a forgatókönyv miért alkalmazza a nyomást a SNAT, további SNAT-portok hozzáadásával a leltárhoz további IP-címek hozzáadásával a rendszer csak az alkalmazás skálázási hibáját fogja késleltetni.  Előfordulhat, hogy más eredménytelenség és anti-mintázatok maszkolására is van lehetőség.

### <a name="icmp-ping-is-failing"></a>Az ICMP-pingelés sikertelen

[Virtual Network NAT](nat-overview.md) támogatja az IPv4 UDP-és TCP-protokollokat. Az ICMP nem támogatott, és a várt sikertelen lesz.  

_**Megoldás:**_ Ehelyett használjon TCP-kapcsolati teszteket (például "TCP ping") és az UDP-specifikus alkalmazás-réteg teszteket a végpontok közötti kapcsolat ellenőrzéséhez.

A következő táblázat kiindulási pontot használhat, amellyel a tesztek elindítására szolgáló eszközök használhatók.

| Operációs rendszer | Általános TCP-kapcsolatok tesztelése | TCP-alkalmazás rétegének tesztelése | UDP |
|---|---|---|---|
| Linux | NC (általános kapcsolatok tesztelése) | Curl (TCP-alkalmazás rétegének tesztelése) | alkalmazás-specifikus |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [-meghívás – Webkérés](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | alkalmazás-specifikus |

### <a name="connectivity-failures"></a>Csatlakozási hibák

[Virtual Network NAT](nat-overview.md) kapcsolódási problémáit számos különböző probléma okozhatja:

* a konfigurációs hibák miatti állandó hibák.
* a NAT-átjáró átmeneti vagy tartós [SNAT-kimerülése](#snat-exhaustion) ,
* átmeneti hibák az Azure-infrastruktúrában, 
* átmeneti hibák az Azure és a nyilvános internetes cél közötti útvonalon, 
* átmeneti vagy állandó hibák a nyilvános internetes célhelyen.

A következőhöz hasonló eszközök használhatók az érvényesítéshez: Az [ICMP ping nem támogatott](#icmp-ping-is-failing).

| Operációs rendszer | Általános TCP-kapcsolatok tesztelése | TCP-alkalmazás rétegének tesztelése | UDP |
|---|---|---|---|
| Linux | NC (általános kapcsolatok tesztelése) | Curl (TCP-alkalmazás rétegének tesztelése) | alkalmazás-specifikus |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [-meghívás – Webkérés](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | alkalmazás-specifikus |

#### <a name="configuration"></a>Konfiguráció

A konfiguráció ellenőrzését:
1. Van-e a NAT-átjáró-erőforrásnak legalább egy nyilvános IP-erőforrása vagy egy nyilvános IP-előtag-erőforrása? Legalább egy IP-címet szükséges a NAT-átjáróhoz rendelni ahhoz, hogy képes legyen kimenő kapcsolatot biztosítani.
2. A virtuális hálózat alhálózata a NAT-átjáró használatára van konfigurálva?
3. A UDR (felhasználó által megadott útvonal) használja, és felülbírálja a célhelyet?  A NAT-átjáró erőforrásai az alapértelmezett útvonalon (0/0) lesznek a konfigurált alhálózatokon.

#### <a name="snat-exhaustion"></a>SNAT-kimerülés

Tekintse át a jelen cikk [SNAT-kimerültség](#snat-exhaustion) című szakaszát.

#### <a name="azure-infrastructure"></a>Azure-infrastruktúra

Az Azure figyeli és nagy gonddal kezeli az infrastruktúrát. Átmeneti hibák léphetnek fel, ezért nincs garancia arra, hogy az átvitelek veszteségmentesek.  Használjon olyan kialakítási mintákat, amelyek lehetővé teszik a a TCP-alkalmazások SYN-újraküldését. A kapcsolat időtúllépése elég nagy a TCP SYN újraküldésének engedélyezéséhez, hogy csökkentse az elveszett SYN-csomagok által okozott átmeneti hatásokat.

_**Megoldás**_

* SNAT- [kimerültség](#snat-exhaustion)keresése.
* A SYN-újraküldési viselkedést vezérlő TCP-verem konfigurációs paramétere RTO ([újraküldési időkorlát](https://tools.ietf.org/html/rfc793)). A RTO értéke állítható, de általában 1 másodperc vagy magasabb értékre van beállítva az exponenciális visszalépéshez.  Ha az alkalmazás kapcsolati időkorlátja túl rövid (például 1 másodperc), akkor előfordulhat, hogy a rendszer szórványos kapcsolati időtúllépéseket lát.  Növelje meg az alkalmazás-kapcsolatok időtúllépését.
* Ha továbbra is megfigyelheti az alapértelmezett alkalmazás-viselkedéssel kapcsolatos váratlan időtúllépéseket, további hibaelhárításhoz nyisson meg egy támogatási esetet.

Nem javasoljuk, hogy mesterségesen csökkentse a TCP-kapcsolat időtúllépését, vagy hangolja a RTO paramétert.

#### <a name="public-internet-transit"></a>Nyilvános internetes tranzit

Az átmeneti hibák esélyei a cél és a több köztes rendszerek hosszabb elérési útjával növekednek. Várható, hogy az átmeneti hibák növelhetik az Azure- [infrastruktúra](#azure-infrastructure)gyakoriságát. 

Kövesse az [Azure-infrastruktúra](#azure-infrastructure) előző szakaszának utasításait.

#### <a name="internet-endpoint"></a>Internetes végpont

Az előző fejezetek azon internetes végponttal együtt érvényesek, amellyel a kommunikáció létrejött. A kapcsolódás sikerességét befolyásoló egyéb tényezők:

* a forgalom kezelése a célhely oldalon, beleértve a következőket is
- Az API-arány korlátozása a cél oldal alapján
- Térfogatmérő DDoS-enyhítés vagy Transport Layer Traffic Shaping
* tűzfal vagy más összetevők a célhelyen 

Általában a csomagok rögzítése a forráson és a célhelyen (ha van ilyen) szükséges ahhoz, hogy eldöntse, mi zajlik.

_**Megoldás**_

* SNAT- [kimerültség](#snat-exhaustion)keresése. 
* Ellenőrizze, hogy az adott régióban vagy máshol található végponthoz való csatlakozást szeretné-e összehasonlítani.  
* Ha nagy mennyiségű vagy tranzakciós sebességű tesztelést hoz létre, vizsgálja meg, hogy a sebesség csökkentése csökkenti-e a hibák előfordulását.
* Ha a módosítási arány hatással van a hibák arányára, ellenőrizze, hogy elérte-e az API-díjszabási korlátokat vagy más korlátozásokat a célhelyen.
* Ha a vizsgálat nem meggyőző, a további hibaelhárításhoz nyisson meg egy támogatási esetet.

#### <a name="tcp-resets-received"></a>A fogadott TCP-visszaállítások

A NAT-átjáró TCP-alaphelyzetbe állítja a forrás virtuális gépet olyan forgalom esetén, amely nem ismerhető fel folyamatban.

Ennek egyik lehetséges oka, hogy a TCP-kapcsolatok üresjárati időtúllépés miatt megszakadt.  Az üresjárati időkorlátot 4 perctől akár 120 percre is beállíthatja.

A TCP-alaphelyzetek nem jönnek létre a NAT-átjáró erőforrásainak nyilvános oldalán. A cél oldalon a TCP-alaphelyzeteket a forrás virtuális gép hozza létre, nem pedig a NAT-átjáró erőforrását.

_**Megoldás**_

* Tekintse át a [tervezési mintákra](#design-patterns) vonatkozó javaslatokat.  
* Ha szükséges, nyisson meg egy támogatási esetet a további hibaelhárításhoz.

### <a name="ipv6-coexistence"></a>IPv6-együttélés

[Virtual Network NAT](nat-overview.md) támogatja az IPv4 UDP-és TCP-protokollokat, és az [IPv6-előtaggal rendelkező alhálózat központi telepítése nem támogatott](nat-overview.md#limitations).

_**Megoldás:**_ NAT-átjáró üzembe helyezése IPv6-előtag nélküli alhálózaton.

[Virtual Network NAT-UserVoice](https://aka.ms/natuservoice)keresztül további képességeket is jelezhet.

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>A csatlakoztatás nem a NAT-átjáró IP-címétől származik

Konfigurálnia kell a NAT-átjárót, az IP-címet (ka), és azt, hogy melyik alhálózatnak kell használnia NAT Gateway-erőforrást. Azonban a NAT-átjáró üzembe helyezése előtt létezett virtuálisgép-példányok kapcsolatai nem az IP-címet (es) használják.  Úgy tűnik, hogy az IP-cím (ek) et használják a NAT-átjáró erőforrásával.

_**Megoldás**_

[Virtual Network NAT](nat-overview.md) lecseréli az alhálózat kimenő kapcsolatát, amelyhez be van állítva. Amikor az alapértelmezett SNAT vagy a terheléselosztó kimenő SNAT a NAT-átjárók használatára vált, az új kapcsolatok azonnal elkezdik használni a NAT-átjáró erőforrásához társított IP-címet (ka) t.  Ha azonban egy virtuális gép még mindig rendelkezik létesített csatlakozással a NAT-átjáró erőforrásra váltás során, akkor a kapcsolódás továbbra is a csatlakozás létrejötte után a régi SNAT IP-címet fogja használni.  Győződjön meg arról, hogy valóban új kapcsolatot létesít, és nem használ olyan kapcsolatot, amely már létezett, mert az operációs rendszer vagy a böngésző egy kapcsolati készletben gyorsítótárazta a kapcsolatokat.  Ha például a PowerShellben a _curlot_ használja, ügyeljen arra, hogy a _-DisableKeepalive_ paraméterrel kényszerítse az új kapcsolatokat.  Ha böngészőt használ, a kapcsolatok is összevonhatók.

Nem szükséges újraindítani a virtuális gépet a NAT-átjáró erőforrásának alhálózatának konfigurálásához.  Ha azonban a virtuális gép újraindul, a rendszer kiüríti a kapcsolatok állapotát.  A kapcsolat állapotának kiürítése után az összes kapcsolat a NAT-átjáró erőforrásának IP-címe (i) használatával kezdődik.  Ez azonban a virtuális gép olyan mellékhatása, amely újraindul, és nem jelzi, hogy újraindítás szükséges.

Ha továbbra is problémákba ütközött, nyisson meg egy támogatási esetet a további hibaelhárításhoz.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [Virtual Network NAT](nat-overview.md) -ról
* Tudnivalók a [NAT-átjáró erőforrásáról](nat-gateway-resource.md)
* Tudnivalók a [NAT-átjáró erőforrásaira vonatkozó mérőszámokról és riasztásokról](nat-metrics.md).
* [Ossza meg velünk a következőt Virtual Network NAT UserVoice-ben való létrehozásához](https://aka.ms/natuservoice).