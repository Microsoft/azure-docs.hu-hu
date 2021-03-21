---
title: Magas rendelkezésre állás és terheléselosztás – Azure AD Application Proxy
description: A Traffic Distribution működése az alkalmazásproxy üzembe helyezésével. Az összekötők teljesítményének optimalizálására és a háttér-kiszolgálók terheléselosztásának használatára vonatkozó tippeket tartalmaz.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fda858b0811eb6308b8e5588eaeae9bff5a1730
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259389"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Az alkalmazásproxy-összekötők és-alkalmazások magas rendelkezésre állása és terheléselosztása

Ez a cikk azt ismerteti, hogyan működik a forgalom eloszlása az alkalmazásproxy üzembe helyezésével. Megvitatjuk, hogy:

- A forgalom elosztása a felhasználók és az összekötők között, valamint tippek az összekötő teljesítményének optimalizálásához

- Az összekötők és a háttérrendszer-kiszolgálók közötti adatforgalom, valamint a több háttér-kiszolgáló közötti terheléselosztási javaslatok

## <a name="traffic-distribution-across-connectors"></a>Forgalom eloszlása összekötők között

Az összekötők a magas rendelkezésre állás elve alapján létesítenek kapcsolatot. Nincs garancia arra, hogy a forgalom mindig egyenletesen oszlik meg az összekötők között, és nincs munkamenet-affinitás. Azonban a használat változó és a kérelmek küldése véletlenszerűen történik az Application proxy Service-példányok számára. Ennek eredményeképpen a forgalom általában csaknem egyenletesen oszlik meg az összekötők között. A diagram és az alábbi lépések azt mutatják be, hogyan jöttek kapcsolatok a felhasználók és az összekötők között.

![A felhasználók és összekötők közötti kapcsolatokat bemutató ábra](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Az ügyféleszközök felhasználója megpróbál hozzáférni egy, az Application proxyn keresztül közzétett helyszíni alkalmazáshoz.
2. A kérelem egy Azure Load Balanceron keresztül dönti el, hogy melyik alkalmazásproxy-szolgáltatási példánynak kell elvégeznie a kérelmet. Régiónként a kérés elfogadásához több tízezer példány áll rendelkezésre. Ez a módszer segít a forgalom egyenletes elosztásában a szolgáltatási példányok között.
3. A rendszer elküldi a kérést a [Service Busnak](../../service-bus-messaging/index.yml).
4. Service Bus a jeleket egy elérhető összekötőhöz. Az összekötő ezután felveszi a kérést a Service Busból.
   - A 2. lépésben a kérelmek különböző alkalmazásproxy-szolgáltatásbeli példányokra mutatnak, így a kapcsolatok nagyobb valószínűséggel lesznek különböző összekötők. Ennek eredményeképpen az összekötők szinte egyenletesen használhatók a csoporton belül.
5. Az összekötő továbbítja a kérést az alkalmazás háttér-kiszolgálójához. Ezután az alkalmazás visszaküldi a választ az összekötőnek.
6. Az összekötő befejezi a választ úgy, hogy megnyit egy kimenő kapcsolódást a szolgáltatás azon példányához, ahonnan a kérés érkezett. Ezt követően a rendszer azonnal lezárta a kapcsolatokat. Alapértelmezés szerint minden összekötő 200 egyidejű kimenő kapcsolatra van korlátozva.
7. Ezután a rendszer visszaküldi a választ az ügyfélnek a szolgáltatási példányból.
8. Az azonos kapcsolatok további kérelmei megismétlik a fenti lépéseket.

Egy alkalmazásnak gyakran sok erőforrása van, és több kapcsolatot nyit meg a betöltéskor. Az egyes kapcsolatok a fenti lépéseket követve válnak elérhetővé a szolgáltatási példányok számára, válasszon ki egy új elérhető összekötőt, ha a kapcsolódás még nem volt korábban összekötővel párosítva.


## <a name="best-practices-for-high-availability-of-connectors"></a>Ajánlott eljárások az összekötők magas rendelkezésre állásához

- Mivel a forgalom a magas rendelkezésre állás érdekében az összekötők között oszlik el, elengedhetetlen, hogy mindig legyen legalább két összekötő egy összekötő-csoportban. Három összekötő előnyben részesített, hogy további puffert biztosítson az összekötők között. A szükséges összekötők megfelelő számának meghatározásához kövesse a kapacitás megtervezése dokumentációt.

- Az összekötőket különböző kimenő kapcsolatokon helyezheti el, hogy elkerülje az egyetlen meghibásodási pontot. Ha az összekötők ugyanazt a kimenő csatlakozást használják, a csatlakozás hálózati problémája hatással lehet az összes összekötőre.

- A termelési alkalmazásokhoz való csatlakozáskor ne kényszerítse az összekötők újraindítását. Ez negatív hatással lehet az összekötők közötti forgalom eloszlására. Az összekötők újraindítása azt eredményezi, hogy a további összekötők nem lesznek elérhetők, és a többi elérhető összekötőhöz csatlakoznak. Az eredmény az összekötők egyenetlen használata.

- Kerülje a beágyazott ellenőrzés összes formáját az összekötők és az Azure közötti kimenő TLS-kommunikációra. Az ilyen típusú beágyazott ellenőrzés a kommunikációs folyamat romlását okozza.

- Győződjön meg arról, hogy az összekötők számára az automatikus frissítések szolgáltatás fut. Ha az alkalmazásproxy Connector Updater szolgáltatás fut, az összekötők automatikusan frissülnek, és megkapják a legújabb frissítést. Ha nem látja a Connector Updater szolgáltatást a kiszolgálón, újra kell telepítenie az összekötőt a frissítések beszerzéséhez.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Az összekötők és a háttérbeli alkalmazás-kiszolgálók közötti adatforgalom

Egy másik kulcsfontosságú hely, ahol a magas rendelkezésre állás egy tényező az összekötők és a háttér-kiszolgálók közötti kapcsolat. Ha egy alkalmazást az Azure AD Application Proxyon keresztül tesznek közzé, a felhasználók és az alkalmazások közötti forgalom három ugráson keresztül áramlik:

1. A felhasználó az Azure AD Application Proxy szolgáltatás nyilvános végpontján csatlakozik az Azure-ban. A kapcsolat létrejön az ügyfél a kezdeményező ügyfél IP-címe (nyilvános) és az alkalmazásproxy-végpont IP-címe között.
2. Az alkalmazásproxy-összekötő lekéri az ügyfél HTTP-kérelmét az alkalmazásproxy szolgáltatásból.
3. Az alkalmazásproxy-összekötő csatlakozik a célalkalmazás alkalmazáshoz. Az összekötő a saját IP-címét használja a kapcsolat létrehozásához.

![Alkalmazás-proxyn keresztül csatlakozó felhasználó diagramja](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X – továbbítva – a fejléc mezőhöz kapcsolódó megfontolások
Bizonyos helyzetekben (például a naplózás, a terheléselosztás stb.) a külső ügyfél eredeti IP-címének megosztása a helyszíni környezettel követelmény. A követelmény megoldásához az Azure AD Application Proxy-összekötő hozzáadja az X által továbbított-for header mezőt a kezdeményező ügyfél IP-címével (nyilvános) a HTTP-kérelemhez. A megfelelő hálózati eszköz (terheléselosztó, tűzfal) vagy a webkiszolgáló vagy háttérbeli alkalmazás elolvashatja és használhatja az információkat.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Ajánlott eljárások több App-kiszolgáló közötti terheléselosztáshoz
Ha az alkalmazásproxy-alkalmazáshoz hozzárendelt összekötő csoport két vagy több összekötővel rendelkezik, és a háttér-webalkalmazást több kiszolgálón (kiszolgálófarm) futtatja, a megfelelő terheléselosztási stratégia szükséges. Egy jó stratégia biztosítja, hogy a kiszolgálók egyenletesen felveszik az ügyfeleket, és meggátolják a kiszolgálók feletti vagy alatti kihasználtságot a kiszolgálófarm számára.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>1. forgatókönyv: a háttérbeli alkalmazás nem igényli a munkamenetek megőrzését
A legegyszerűbb eset az, amikor a háttér-webalkalmazás nem igényel munkamenet-kitartást (munkamenet-megőrzés). A felhasználótól érkező kéréseket a kiszolgálófarm bármely háttérbeli alkalmazás-példánya kezelheti. A 4. rétegbeli terheléselosztó használható, és nem lehet affinitást beállítani. Bizonyos lehetőségek közé tartozik a Microsoft hálózati terheléselosztás és a Azure Load Balancer vagy egy másik gyártótól származó terheléselosztó. Azt is megteheti, hogy a ciklikus multiplexelés DNS-t konfigurálhatja.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>2. forgatókönyv: a háttérbeli alkalmazáshoz munkamenet-megőrzés szükséges
Ebben az esetben a háttér-webalkalmazásnak a hitelesített munkamenet során a munkamenetek állandósága (munkamenet-megőrzés) szükséges. A felhasználótól érkező kérelmeket a kiszolgálófarm ugyanazon kiszolgálóján futó háttérbeli alkalmazás-példánynak kell kezelnie.
Ez a forgatókönyv bonyolultabb lehet, mivel az ügyfél általában több kapcsolatot létesít az alkalmazásproxy szolgáltatással. A különböző kapcsolatokon keresztüli kérelmek különböző összekötőket és kiszolgálókat is megérkeznek a farmon. Mivel az egyes összekötők a saját IP-címét használják ehhez a kommunikációhoz, a terheléselosztó nem tudja biztosítani a munkamenet-kikötést az összekötők IP-címe alapján. A forrás IP-affinitása nem használható.
Íme néhány lehetőség a 2. forgatókönyvhöz:

- 1. lehetőség: a munkamenet megőrzésének alapja a terheléselosztó által beállított munkamenet-cookie-ban. Ez a beállítás azért ajánlott, mert lehetővé teszi, hogy a terhelés egyenletesen legyen elosztva a háttér-kiszolgálók között. Ehhez a képességhez egy 7. rétegbeli terheléselosztó szükséges, amely képes a HTTP-forgalom kezelésére és a TLS-kapcsolat megszüntetésére. Az Azure Application Gateway (munkamenet-affinitás) vagy egy másik gyártótól származó terheléselosztó is használható.

- 2. lehetőség: a munkamenet megőrzésének alapja az X által továbbított fejlécnél. Ehhez a lehetőséghez egy 7. rétegbeli terheléselosztó szükséges, amely képes a HTTP-forgalom kezelésére és a TLS-kapcsolat megszüntetésére.  

- 3. lehetőség: a háttérbeli alkalmazás konfigurálása, hogy ne kelljen a munkamenetek megőrzésére.

A szoftver gyártójának dokumentációjában tájékozódhat a háttérrendszer terheléselosztási követelményeiről.

## <a name="next-steps"></a>Következő lépések

- [Alkalmazásproxy engedélyezése](application-proxy-add-on-premises-application.md)
- [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
- [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)
- [Az alkalmazásproxyval kapcsolatos problémák hibaelhárítása](application-proxy-troubleshoot.md)
- [Ismerje meg, hogyan támogatja az Azure AD architektúra a magas rendelkezésre állást](../fundamentals/active-directory-architecture.md)