---
title: Végpontok közötti TLS engedélyezése az Azure Application Gateway
description: Ez a cikk áttekintést nyújt a Application Gateway végpontok közötti TLS-támogatásról.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: c39401289ffc6f27c292168adaa15c5163a3967b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001286"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>A TLS-lezárás és a végpontok közötti TLS áttekintése Application Gateway

Transport Layer Security (TLS), korábbi nevén SSL (SSL), a szabványos biztonsági technológia a webkiszolgáló és a böngésző közötti titkosított kapcsolat létesítéséhez. Ez a hivatkozás biztosítja, hogy a webkiszolgáló és a böngészők között átadott összes adatforgalom magán-és titkosítatlan maradjon. Az Application Gateway a TLS-lezárást is támogatja az átjárón, valamint a végpontok közötti TLS-titkosítást.

## <a name="tls-termination"></a>TLS-lezárás

Application Gateway támogatja a TLS-lezárást az átjárón, amely után a forgalom általában titkosítatlan folyamatokat végez a háttér-kiszolgálókon. Az Application Gateway TLS-megszakítása számos előnnyel jár:

- **Jobb teljesítmény** – a legnagyobb teljesítmény, ha a TLS-visszafejtés a kezdeti kézfogás. A teljesítmény javítása érdekében a visszafejtést végző kiszolgáló gyorsítótárazza a TLS munkamenet-azonosítókat, és felügyeli a TLS-munkamenetek jegyeit. Ha ezt az Application Gateway végzi el, az azonos ügyféltől érkező összes kérelem használhatja a gyorsítótárazott értékeket. Ha a háttér-kiszolgálókon történik, akkor minden alkalommal, amikor az ügyfél kérései egy másik kiszolgálóra kerülnek, az ügyfélnek újra kell hitelesítenie magát. A TLS-jegyek használata segít enyhíteni ezt a problémát, de az összes ügyfél nem támogatja, és bonyolult lehet a konfigurálás és a felügyelet.
- **A háttér-kiszolgálók jobb kihasználtsága – az** SSL/TLS-feldolgozás rendkívül nagy CPU-igényű, és egyre intenzívebben növekszik a kulcsok mérete. Ha eltávolítja ezt a munkát a háttér-kiszolgálókról, az lehetővé teszi számukra, hogy a lehető leghatékonyabban lássák a tartalmat.
- **Intelligens útválasztás** – a forgalom visszafejtésével az Application Gateway hozzáfér a kérelem tartalmához, például a fejlécekhez, az URI-hoz és így tovább, és ezeket az adatokat a kérelmek továbbítására használhatja.
- **Tanúsítványkezelő** – a tanúsítványokat csak az Application gatewayben kell megvásárolni és telepíteni, nem minden háttér-kiszolgálóra. Ez időt és pénzt takarít meg.

A TLS-megszakítás konfigurálásához TLS/SSL-tanúsítványt kell hozzáadni a figyelőhöz ahhoz, hogy a Application Gateway a TLS/SSL protokoll specifikációja alapján szimmetrikus kulcsot lehessen származtatni. A szimmetrikus kulcs ezután az átjárónak továbbított forgalom titkosítására és visszafejtésére szolgál. A TLS/SSL-tanúsítványnak személyes információcsere (PFX) formátumúnak kell lennie. Ez a fájlformátum lehetővé teszi az Application Gateway által igényelt titkos kulcs exportálását a forgalom titkosításának és visszafejtésének elvégzéséhez.

> [!IMPORTANT] 
> A figyelő tanúsítványa megköveteli a teljes tanúsítványlánc feltöltését (a HITELESÍTÉSSZOLGÁLTATÓ, a köztes és a levél tanúsítvány főtanúsítványa) a megbízhatósági lánc létrehozásához. 


> [!NOTE] 
>
> Az Application Gateway nem képes új tanúsítvány létrehozására vagy tanúsítványkérelem küldésére a hitelesítésszolgáltatónak.

Ahhoz, hogy a TLS-kapcsolat működjön, gondoskodnia kell arról, hogy a TLS/SSL-tanúsítvány megfeleljen a következő feltételeknek:

- Az aktuális dátum és idő a tanúsítvány érvényességi ideje és érvényességi tartománya között van.
- A tanúsítvány „köznapi neve” (Common Name, CN) megegyezik a kérésben szereplő gazdafejlécével. Ha például az ügyfél böngészője a(z) `https://www.contoso.com/` címre küldi a kérést, a köznapi névnek is a(z) `www.contoso.com` címnek kell lennie.

### <a name="certificates-supported-for-tls-termination"></a>A TLS-lezáráshoz támogatott tanúsítványok

Az Application Gateway a következő típusú tanúsítványokat támogatja:

- CA (hitelesítésszolgáltató) tanúsítvány: A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány egy hitelesítésszolgáltató (CA) által kiadott digitális tanúsítvány.
- EV (bővített érvényesítés) tanúsítvány: az EV-tanúsítvány egy olyan tanúsítvány, amely megfelel az iparági szabványnak megfelelő tanúsítványokra vonatkozó irányelveknek. Ezzel bekapcsolja a böngésző-kereső sáv zöldjét, és közzéteszi a vállalat nevét is.
- Helyettesítő tanúsítvány: Ez a tanúsítvány a *. site.com alapján tetszőleges számú altartományt támogat, ahol az altartománya lecseréli a *-t. Ez azonban nem támogatja a site.com, így ha a felhasználók a "www" beírása nélkül férnek hozzá a webhelyhez, a helyettesítő karakteres tanúsítvány nem fedi le azt.
- Self-Signed tanúsítványok: az ügyféloldali böngészők nem bíznak meg ezekkel a tanúsítványokkal, és figyelmeztetik a felhasználót, hogy a virtuális szolgáltatás tanúsítványa nem része egy megbízhatósági láncnak. Az önaláírt tanúsítványok olyan tesztelési és környezeti környezetekben használhatók, ahol a rendszergazdák vezérelhetik az ügyfeleket, és biztonságosan kihagyhatják a böngésző biztonsági riasztásait. Az éles munkaterhelések soha nem használhatnak önaláírt tanúsítványokat.

További információ: [a TLS-lezárás konfigurálása az Application Gateway használatával](./create-ssl-portal.md).

### <a name="size-of-the-certificate"></a>A tanúsítvány mérete
A [Application Gateway korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits) szakaszban találja a maximálisan támogatott TLS/SSL-tanúsítvány méretét.

## <a name="end-to-end-tls-encryption"></a>Végpontok közötti TLS-titkosítás

Előfordulhat, hogy nem szeretne titkosítatlan kommunikációt használni a háttér-kiszolgálókkal. Előfordulhat, hogy biztonsági követelmények, megfelelőségi követelmények vagy az alkalmazás csak biztonságos kapcsolatokat fogad el. Az Azure Application Gateway végpontok közötti TLS-titkosítással támogatja ezeket a követelményeket.

A végpontok közötti TLS lehetővé teszi a bizalmas adatok titkosítását és biztonságos továbbítását a háttérbe, miközben Application Gateway 7. rétegbeli terheléselosztási funkcióit használja. Ezen funkciók közé tartozik a cookie-alapú munkamenet-affinitás, az URL-alapú útválasztás, a helyek alapján történő Útválasztás támogatása, az X-* fejlécek újraírása vagy behelyezésének lehetősége.

Ha végpontok közötti TLS kommunikációs móddal van konfigurálva, Application Gateway leállítja a TLS-munkameneteket az átjárón, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Application Gateway ezután kezdeményez egy új TLS-kapcsolódást a háttér-kiszolgálóval, majd újratitkosítja az adataikat a háttér-kiszolgáló nyilvánoskulcs-tanúsítványával, mielőtt továbbítja a kérést a háttérbe. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz. A végpontok közötti TLS engedélyezése a [háttérbeli http-beállítás](./configuration-overview.md#http-settings) protokoll-beállításának HTTPS-re való beállításával történik, amelyet a rendszer a háttérbeli készletre alkalmaz.

A Application Gateway és a WAF v1 SKU esetében a TLS-házirend a felületi és a háttérbeli forgalomra is érvényes. Az előtérben Application Gateway kiszolgálóként működik, és érvényesíti a szabályzatot. A háttérben Application Gateway ügyfélként viselkedik, és a TLS-kézfogás során a protokoll/titkosítási adatokat küldi el.

A Application Gateway és a WAF v2 SKU esetében a TLS-házirend csak a előtér-forgalomra vonatkozik, és minden titkosítás a háttér-kiszolgáló számára érhető el, amely az adott titkosítási és TLS-verzió kiválasztását szabályozza a kézfogás során.

Application Gateway csak azokkal a háttér-kiszolgálókkal kommunikál, amelyek engedélyezik a tanúsítványt a Application Gateway vagy a tanúsítványokat jól ismert HITELESÍTÉSSZOLGÁLTATÓI hatóságok írták alá, és a tanúsítvány CN-je megegyezik a HTTP-háttér beállításai között szereplő állomásnévvel. Ezek közé tartoznak a megbízható Azure-szolgáltatások, például a Azure App Service/Web Apps és az Azure API Management.

Ha a háttér-készlet tagjainak tanúsítványait nem a jól ismert HITELESÍTÉSSZOLGÁLTATÓI hatóságok írják alá, akkor a háttérrendszer összes példányát, a végpontok közötti TLS-t engedélyezve kell lennie egy tanúsítvánnyal, hogy engedélyezze a biztonságos kommunikációt. A tanúsítvány hozzáadásával biztosítható, hogy az Application Gateway csak az ismert háttérbeli példányokkal kommunikáljon. Ez tovább biztosítja a végpontok közötti kommunikációt.

> [!NOTE] 
>
> A háttérrendszer-kiszolgálók hitelesítéséhez a háttérbeli **http-beállításhoz** hozzáadott tanúsítvány megegyező lehet, mint az Application Gateway- **ben a TLS** -megszakításhoz hozzáadott tanúsítvány, vagy a fokozott biztonság érdekében.

![végpontok közötti TLS-forgatókönyv][1]

Ebben a példában a TLS 1.2-t használó kérelmeket a rendszer a Pool1 háttér-kiszolgálókra irányítja a végpontok közötti TLS protokoll használatával.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>Végpontok közötti TLS és a tanúsítványok listázásának engedélyezése

Application Gateway csak olyan ismert háttérbeli példányokkal kommunikál, amelyek lehetővé teszik, hogy a tanúsítványát az Application Gateway tartalmazza. A végpontok közötti TLS-telepítési folyamat néhány eltérést mutat a használt Application Gateway verziójának tekintetében. A következő szakasz ezeket egyenként ismerteti.

## <a name="end-to-end-tls-with-the-v1-sku"></a>Végpontok közötti TLS a v1 SKU-val

Ha engedélyezni szeretné a végpontok közötti TLS-t a háttér-kiszolgálókkal, és Application Gateway a kérelmek átirányításához, az állapot-mintavételnek sikeresnek kell lennie, és vissza kell térnie az egészséges válaszra.

A HTTPS-állapotú Szondák esetében a Application Gateway v1 SKU a HTTP-beállításokba való feltöltéshez a (háttér-kiszolgáló tanúsítványának nyilvános kulcsa és nem a főtanúsítvány) pontos egyezését használja.

Ezután csak az ismert és az engedélyezett háttérrel rendelkező kapcsolatok engedélyezettek. A fennmaradó hátterek állapota nem kifogástalan. Az önaláírt tanúsítványok csupán tesztelési célokat szolgálnak, és nem ajánlottak éles számítási feladatokra. Az ilyen tanúsítványoknak engedélyezni kell az Application Gateway használatát az előző lépésekben leírtak szerint.

> [!NOTE]
> A megbízható Azure-szolgáltatások, például a Azure App Service esetében nem szükséges a hitelesítés és a megbízható főtanúsítványok beállítása. Alapértelmezés szerint megbízhatónak tekintendők.

## <a name="end-to-end-tls-with-the-v2-sku"></a>Végpontok közötti TLS a v2 SKU-val

A hitelesítési tanúsítványok elavultak, és a megbízható főtanúsítványok a Application Gateway v2 SKU-ban lettek lecserélve. A hitelesítési tanúsítványokhoz hasonlóan működnek, néhány fő különbséggel:

- Azok a tanúsítványok, amelyeket jól ismert HITELESÍTÉSSZOLGÁLTATÓI hatóságok aláírtak, és amelyek a HTTP-háttér-beállításokban szereplő állomásnévnek felelnek meg, nincs szükség további lépésre a TLS működésének befejezéséhez. 

   Ha például a háttérbeli tanúsítványokat egy jól ismert HITELESÍTÉSSZOLGÁLTATÓ állítja ki, és a contoso.com rendelkezik, és a háttérrendszer http-beállításának gazdagép mezője is contoso.com értékre van állítva, akkor nincs szükség további lépésekre. A háttérbeli http-beállítás protokollját HTTPS-re állíthatja, és az állapot-mintavétel és az adatelérési út is TLS-kompatibilis lesz. Ha Azure App Service vagy más Azure-webszolgáltatást használ a háttérként, akkor ezek implicit módon megbízhatóak, és a végpontok közötti TLS-hez nincs szükség további lépésekre.
   
> [!NOTE] 
>
> Ahhoz, hogy egy TLS-/SSL-tanúsítvány megbízható legyen, a háttér-kiszolgáló tanúsítványát egy jól ismert CA-nak kell kiállítania. Ha a tanúsítványt nem megbízható HITELESÍTÉSSZOLGÁLTATÓ állította ki, az Application Gateway ekkor megtekinti, hogy a kiállító HITELESÍTÉSSZOLGÁLTATÓ tanúsítványát egy megbízható HITELESÍTÉSSZOLGÁLTATÓ adta-e ki, így egészen addig, amíg nem található megbízható HITELESÍTÉSSZOLGÁLTATÓ (ekkor a megbízható, biztonságos kapcsolatok létrejönnek), vagy nem található megbízható HITELESÍTÉSSZOLGÁLTATÓ (ekkor az Application Gateway a háttér nem megfelelő állapotú lesz). Ezért javasoljuk, hogy a háttér-kiszolgáló tanúsítványa tartalmazza a legfelső szintű és a közbenső hitelesítésszolgáltatót is.

- Ha a tanúsítvány önaláírt vagy ismeretlen közvetítők által van aláírva, a végpontok közötti TLS engedélyezése a v2 SKU-ban egy megbízható főtanúsítványt kell definiálni. Application Gateway csak olyan háttérrel kommunikál, amelynek a kiszolgálói tanúsítvány főtanúsítványa megegyezik a készlethez társított háttérbeli http-beállításban található megbízható legfelső szintű tanúsítványok listájával.

- A főtanúsítvány egyeztetése mellett a Application Gateway v2 is ellenőrzi, hogy a háttérbeli http-beállításban megadott gazdagép-beállítás megegyezik-e a háttér-kiszolgáló TLS/SSL-tanúsítványa által megjelenített köznapi névvel (CN). Amikor TLS-kapcsolatot próbál létesíteni a háttérrel, Application Gateway v2 beállítja a Kiszolgálónév jelzése (SNI) bővítményt a háttérbeli http-beállításban megadott gazdagépre.

- Ha a **kiválasztó állomásnév a háttérbeli célhelyről** elemre van kiválasztva a háttérbeli http-beállításban a gazdagép mező helyett, akkor a SNI fejléc mindig a háttérrendszer-készlet teljes tartománynevére van beállítva, a háttér-kiszolgáló TLS/SSL-tanúsítványa pedig csak a teljes tartománynevet adja meg Ebben a forgatókönyvben nem támogatottak a háttérbeli készlet tagjai az IP-címekkel.

- A főtanúsítvány a háttér-kiszolgálói tanúsítványok Base64 kódolású főtanúsítványa.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>SNI különbségek a v1 és v2 SKU-ban

Ahogy azt korábban említettük, Application Gateway megszakítja a TLS-forgalmat az ügyfélről a Application Gateway figyelőben (hívjuk meg a frontend-kapcsolatot), a visszafejti a forgalmat, alkalmazza a szükséges szabályokat a háttérben lévő kiszolgáló meghatározására, amelyhez a kérést továbbítani kell, és létre kell hoznia egy új TLS-munkamenetet a háttér-kiszolgálóval (nevezzük a háttér-kapcsolatot).

Az alábbi táblázatok a v1 és v2 SKU közötti különbségeket ismertetik a SNI és a háttérbeli kapcsolatok tekintetében.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Előtér-TLS-kapcsolatok (ügyfél – Application Gateway)

---
Eset | v1 | v2 |
| --- | --- | --- |
| Ha az ügyfél megadja a SNI fejlécét, és az összes többhelyes figyelő engedélyezve van a "SNI megkövetelése" jelzővel | Adja vissza a megfelelő tanúsítványt, és ha a hely nem létezik (a server_name alapján), a kapcsolat alaphelyzetbe áll. | Ha elérhető, a megfelelő tanúsítványt adja vissza, ha van, akkor a visszaadja az első konfigurált HTTPS-figyelő tanúsítványát (a sorrendben).|
| Ha az ügyfél nem ad meg SNI-fejlécet, és ha az összes többhelyes fejléc engedélyezve van a "SNI megkövetelése" értékkel | A kapcsolatok alaphelyzetbe állítása | Az első konfigurált HTTPS-figyelő tanúsítványát adja vissza (a sorrendben).
| Ha az ügyfél nem ad meg SNI-fejlécet, és ha van olyan alapszintű figyelő, amely tanúsítvánnyal van konfigurálva | Az alapszintű figyelőben az ügyfélhez konfigurált tanúsítványt adja vissza (alapértelmezett vagy tartalék tanúsítvány) | Az első konfigurált HTTPS-figyelő tanúsítványát adja vissza (a sorrendben). |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Háttér TLS-kapcsolatok (Application Gateway a háttér-kiszolgálóhoz)

#### <a name="for-probe-traffic"></a>Mintavételi forgalom esetén

---
Eset | v1 | v2 |
| --- | --- | --- |
| SNI (server_name) fejléc a TLS-kézfogás során FQDN-ként | Állítsa be FQDN-ként a háttér-készletből. Az [RFC 6066](https://tools.ietf.org/html/rfc6066)-es verzióban a SNI állomásnév nem engedélyezi a literális IPv4-és IPv6-címeket. <br> **Megjegyzés:** A háttér-készlet teljes tartománynevének a DNS-t kell feloldania a háttér-kiszolgáló IP-címére (nyilvános vagy privát) | A SNI fejléce (server_name) a HTTP-beállításokhoz csatolt egyéni mintavétel állomásneve (ha be van állítva), más néven a HTTP-beállításokban említett állomásnév, ellenkező esetben a háttér-készletben említett teljes tartománynév. A sorrend sorrendje egyéni mintavételi > HTTP-beállítások > háttér-készlet. <br> **Megjegyzés:** Ha a HTTP-beállításokban és az egyéni mintavételben konfigurált állomásnevek eltérnek, akkor a prioritás szerint a SNI az egyéni mintavétel állomásneveként lesz beállítva.
| Ha a háttér-készlet címe IP-cím (v1), vagy ha az egyéni mintavételi állomásnév IP-címként van konfigurálva (v2) | A SNI (server_name) nem állítható be. <br> **Megjegyzés:** Ebben az esetben a háttér-kiszolgálónak képesnek kell lennie egy alapértelmezett/tartalék tanúsítvány visszaadására, és ezt engedélyezni kell a hitelesítési tanúsítvány alatt a HTTP-beállítások között. Ha nincs beállítva alapértelmezett/tartalék tanúsítvány a háttér-kiszolgálón, és a SNI várható, a kiszolgáló alaphelyzetbe állíthatja a kapcsolódást, és mintavételi hibákhoz vezethet. | A korábban említett elsőbbségi sorrendben, ha az IP-címe állomásnév, akkor a SNI nem lesz beállítva [RFC 6066](https://tools.ietf.org/html/rfc6066)-ként. <br> **Megjegyzés:** A SNI szintén nem állítható be v2-mintavétel esetén, ha nincs beállítva egyéni mintavétel, és nincs beállítva állomásnév a HTTP-beállítások vagy a háttér-készlet számára. |

> [!NOTE] 
> Ha nincs konfigurálva egyéni mintavétel, akkor Application Gateway az alapértelmezett mintavételt a következő formátumban küldi el \<protocol\> :://127.0.0.1: \<port\> /. Alapértelmezett HTTPS-mintavétel esetén például a rendszer a következőként küldi el: https://127.0.0.1:443/ . Vegye figyelembe, hogy az itt említett 127.0.0.1-t csak a HTTP-állomásfejléc használja, és az RFC 6066-ként nem fogja használni a SNI-fejlécet. Az állapot-mintavételi hibákkal kapcsolatos további információkért tekintse meg a [háttér állapotával kapcsolatos hibaelhárítási útmutatót](application-gateway-backend-health-troubleshooting.md).

#### <a name="for-live-traffic"></a>Élő forgalom esetén

---
Eset | v1 | v2 |
| --- | --- | --- |
| SNI (server_name) fejléc a TLS-kézfogás során FQDN-ként | Állítsa be FQDN-ként a háttér-készletből. Az [RFC 6066](https://tools.ietf.org/html/rfc6066)-es verzióban a SNI állomásnév nem engedélyezi a literális IPv4-és IPv6-címeket. <br> **Megjegyzés:** A háttér-készlet teljes tartománynevének a DNS-t kell feloldania a háttér-kiszolgáló IP-címére (nyilvános vagy privát) | A SNI fejléce (server_name) a HTTP-beállítások állomásnévként van beállítva, ellenkező esetben, ha a *PickHostnameFromBackendAddress* beállítás van kiválasztva, vagy ha nincs megadva állomásnév, akkor a rendszer a háttérrendszer-készlet KONFIGURÁCIÓJÁNAK teljes tartománynevét adja meg.
| Ha a háttérbeli készlet címe IP-cím vagy állomásnév nincs beállítva a HTTP-beállításokban | A SNI nem állítható be [RFC 6066](https://tools.ietf.org/html/rfc6066) -ként, ha a háttérbeli készlet bejegyzése nem teljes tartománynév | A SNI az ügyféltől származó bemeneti FQDN állomásnévként lesz beállítva, és a háttér-tanúsítvány CN-nek egyeznie kell ezzel az állomásnévvel.

## <a name="next-steps"></a>Következő lépések

A végpontok közötti TLS-vel kapcsolatos ismeretek megismerése után nyissa meg a végpontok közötti TLS-t a [Application Gateway és a PowerShell használatával](application-gateway-end-to-end-ssl-powershell.md) , és hozzon létre egy Application Gatewayt a végpontok közötti TLS használatával.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png