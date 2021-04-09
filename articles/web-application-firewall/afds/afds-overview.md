---
title: Mi az Azure webalkalmazási tűzfal az Azure-beli bejárati ajtón?
description: Ismerje meg, hogy az Azure-webalkalmazások tűzfala az Azure bejárati ajtó szolgáltatásában Hogyan védi a webalkalmazásait a kártékony támadások ellen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: victorh
ms.openlocfilehash: 0e3073c491b251a1dbb505d4656de9c6a1e0ac7f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048493"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure webalkalmazási tűzfal az Azure-beli bejárati ajtón

Az Azure webalkalmazási tűzfal (WAF) az Azure-beli bejárati ajtón központosított védelmet biztosít a webalkalmazások számára. A WAF megvédi webszolgáltatásait a gyakori biztonsági rések és sebezhetőségek ellen. A szolgáltatás elérhetővé válik a felhasználók számára, és segít a megfelelőségi követelmények teljesítésében.

A WAF globális és központosított megoldás. Üzembe helyezése az Azure-beli hálózati peremhálózati telephelyeken a világon. A WAF-kompatibilis webalkalmazások minden bejövő kérelmet megvizsgálnak a hálózat peremén. 

A WAF megakadályozza, hogy a rosszindulatú támadások a támadási források közelébe lépjenek, mielőtt belépnek a virtuális hálózatba. A globális védelmet a teljesítmény feláldozása nélkül érheti el. A WAF szabályzat egyszerűen az előfizetésben lévő bejárati profilokra mutató hivatkozásokat tartalmaz. Az új szabályok percek alatt üzembe helyezhetők, így gyorsan reagálhat a veszélyforrások változására.

![Azure-beli webalkalmazási tűzfal](../media/overview/wafoverview.png)

Az Azure bejárati ajtaja [két új SKU-](../../frontdoor/standard-premium/overview.md)t vezet be előzetes verzióban: a bejárati ajtó standard és a bejárat Premium SKU A WAF natív módon integrálva van a prémium szintű SKU-val a teljes funkcionalitással. A bejárati ajtó szabványos SKU-jának csak az [Egyéni szabályok](#custom-authored-rules) támogatottak.

## <a name="waf-policy-and-rules"></a>WAF szabályzat és szabályok

Beállíthat egy [WAF szabályzatot](waf-front-door-create-portal.md) , és hozzárendelheti a szabályzatot egy vagy több előtér-előtérben a védelemhez. A WAF szabályzatok a biztonsági szabályok két típusát alkotják:

- az ügyfél által létrehozott egyéni szabályok.

- felügyelt szabálykészlet, amely az Azure által felügyelt előre konfigurált szabályok gyűjteménye.

Ha mindkettő létezik, az egyéni szabályok feldolgozása a felügyelt szabálykészlet szabályainak feldolgozása előtt történik meg. A szabályok egyeztetési feltételből, prioritásból és műveletből állnak. Támogatott tevékenységtípusok: Engedélyezés, LETILTÁS, napló és átirányítás. A felügyelt és az egyéni szabályok kombinálásával teljes mértékben testre szabott szabályzatot hozhat létre, amely megfelel az adott alkalmazás védelmi követelményeinek.

A szabályzaton belüli szabályok feldolgozása prioritási sorrendben történik. A Priority egy egyedi egész szám, amely meghatározza a feldolgozandó szabályok sorrendjét. A kisebb egész szám magasabb prioritást jelöl, és ezeket a szabályokat a rendszer a magasabb egész értékkel rendelkező szabályok előtt értékeli ki. A szabály egyeztetése után a szabályban definiált megfelelő művelet lesz alkalmazva a kérelemre. Az ilyen egyezés feldolgozását követően az alacsonyabb prioritású szabályok nem kerülnek feldolgozásra.

Egy bejárati ajtó által szállított webalkalmazásnak egyszerre csak egy WAF szabályzata lehet társítva. Azonban a WAF szabályzatok nélkül is beállíthatja a bejárati ajtót. Ha van WAF-szabályzat, a rendszer replikálja az összes peremhálózati helyszínre, hogy biztosítsa az egységes biztonsági házirendeket az egész világon.

## <a name="waf-modes"></a>WAF módok

A WAF házirend a következő két módban való futtatásra konfigurálható:

- **Észlelési mód:** Ha az észlelési módban fut, a WAF nem hajt végre más műveleteket, mint a figyelők, és naplózza a kérést és a megegyező WAF-szabályt a WAF-naplókba. Bekapcsolhatja a naplózási diagnosztikát a bejárati ajtóhoz. A portál használata esetén lépjen a **diagnosztika** szakaszra.

- **Megelőzési mód:** Megelőzési módban a WAF a megadott műveletet hajtja végre, ha egy kérelem megfelel egy szabálynak. Ha talál egyezést, az alacsonyabb prioritású további szabályok kiértékelése nem történik meg. A rendszer a WAF-naplókban is naplózza a megfeleltetett kérelmeket.

## <a name="waf-actions"></a>WAF műveletek

A WAF-ügyfelek az egyik műveletből választhatnak, ha egy kérelem megfelel egy szabály feltételeinek:

- **Engedélyezés:**  A kérelem áthalad a WAF, és a rendszer továbbítja a háttérbe. Az alacsonyabb prioritású szabályok nem tudják blokkolni ezt a kérést.
- **Letiltás:** A kérés le van tiltva, és a WAF választ küld az ügyfélnek, és nem továbbítja a kérést a háttér felé.
- **Napló:**  A rendszer naplózza a kérelmet a WAF-naplókban, és a WAF folytatja az alacsonyabb prioritású szabályok kiértékelését.
- **Átirányítás:** A WAF átirányítja a kérést a megadott URI-ra. A megadott URI egy házirendi szintű beállítás. A konfigurálást követően a rendszer az **átirányítási** műveletnek megfelelő összes kérelmet elküldi az adott URI-nak.

## <a name="waf-rules"></a>WAF-szabályok

A WAF szabályzatok két különböző típusú biztonsági szabályt tartalmazhatnak – egyéni szabályokat, amelyeket az ügyfél és a felügyelt szabályrendszerek, valamint az Azure által felügyelt előre konfigurált szabályok határoznak meg.

### <a name="custom-authored-rules"></a>Egyéni létrehozott szabályok

Az egyéni szabályokat a következőképpen állíthatja be: WAF

- **IP-engedélyezési lista és blokkolási lista:** A webalkalmazásokhoz való hozzáférést az ügyfél IP-címei vagy az IP-címtartományok listája alapján szabályozhatja. Az IPv4-és IPv6-címek egyaránt támogatottak. A lista beállítható úgy, hogy blokkolja vagy engedélyezze ezeket a kéréseket, ahol a forrás IP-cím megegyezik a listában szereplő IP-címekkel.

- **Földrajzi alapú hozzáférés-vezérlés:** A webalkalmazásokhoz való hozzáférést az ügyfél IP-címéhez társított országkód alapján szabályozhatja.

- **Http-paraméterek-alapú hozzáférés-vezérlés:** A karakterlánc-egyezések alapjául a HTTP/HTTPS-kérések paramétereinek alapszabályai érvényesek.  Például a lekérdezési karakterláncok, a POST argumentumok, a kérelem URI-ja, a kérelem fejléce és a kérelem törzse.

- **Kérelem metódus-alapú hozzáférés-vezérlése:** A kérelem HTTP-kérelmi metódusán alapuló szabályok. Például: GET, PUT vagy HEAD.

- **Méret megkötése:** Az alapszabályokat a kérelem adott részeinek hosszára alapozhatja, például a lekérdezési karakterláncot, az URI-t vagy a kérelem törzsét.

- **Korlátozási szabályok:** A díjszabás-ellenőrzési szabály az ügyfél IP-címétől érkező rendellenes nagy forgalom korlátozása. Egy percen belül beállíthat egy küszöbértéket az ügyfél IP-címén engedélyezett webes kérelmek számára. Ez a szabály különbözik egy olyan IP-lista alapú engedélyezési/letiltási szabálytól, amely engedélyezi vagy letiltja az ügyfél IP-címéről érkező összes kérést. A díjszabási korlátok kombinálhatók további egyeztetési feltételekkel, például a HTTP (S) paraméterekkel, amelyek a részletes díjszabás-vezérléshez egyeznek.

### <a name="azure-managed-rule-sets"></a>Azure által felügyelt szabálykészlet

Az Azure által felügyelt szabálykészlet egyszerű módszert kínál a védelem közös biztonsági fenyegetésekkel való üzembe helyezésére. Mivel az ilyen szabályrendszerek az Azure felügyeli, a szabályok az új támadási aláírások elleni védelemhez szükséges módon frissülnek. Az Azure által felügyelt alapértelmezett szabálykészlet a következő veszélyforrás-kategóriákra vonatkozó szabályokat tartalmazza:

- Webhelyek közötti, szkriptalapú támadás
- Java-támadások
- Helyi fájl felvétele
- PHP-injektálási támadások
- Távoli parancs végrehajtása
- Távolifájl-beszúrás
- Munkamenet-rögzítés
- SQL-injektálás elleni védelem
- Protokoll-támadók

Az alapértelmezett szabálykészlet verziószáma akkor növekszik, amikor új támadási aláírásokat adnak hozzá a szabálykészlet számára.
Az alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van a WAF házirendek észlelési módjában. Az alapértelmezett szabálykészlet egyes szabályait letilthatja vagy engedélyezheti az alkalmazásra vonatkozó követelmények teljesítéséhez. Szabályként beállíthat meghatározott műveleteket is (Engedélyezés/LETILTÁS/átirányítás/napló).

Esetenként előfordulhat, hogy a WAF kiértékelése során bizonyos kérési attribútumokat ki kell hagyni. Gyakori példa Active Directory beszúrt tokenek használata a hitelesítéshez. Egy felügyelt szabály, szabálytípus vagy a teljes szabálykészlet kizárási listáját is konfigurálhatja.  

Az alapértelmezett művelet a LETILTÁS. Emellett az egyéni szabályok is konfigurálhatók ugyanabban a WAF-házirendben, ha meg szeretné kerülni az alapértelmezett szabálykészlet összes előre konfigurált szabályát.

Az egyéni szabályokat a rendszer mindig alkalmazza az alapértelmezett szabálykészlet szabályainak kiértékelése előtt. Ha egy kérelem megfelel egy egyéni szabálynak, a rendszer alkalmazza a vonatkozó szabály műveletét. A kérést a rendszer blokkolja vagy továbbítja a háttérnek. Más egyéni szabályok vagy az alapértelmezett szabálykészlet szabályai nem lesznek feldolgozva. Eltávolíthatja a WAF szabályzatok alapértelmezett szabályát is.

### <a name="bot-protection-rule-set-preview"></a>Robot Protection-szabály beállítása (előzetes verzió)

Engedélyezheti egy felügyelt robot védelmi szabály beállítását, hogy egyéni műveleteket hajtson végre az ismert bot-kategóriákból érkező kérések esetén. 

Három bot-kategória támogatott: rossz, jó és ismeretlen. A robot-aláírásokat a WAF platform felügyeli és dinamikusan frissíti.

A helytelen robotok közé tartoznak a rosszindulatú IP-címekről és robotoktól származó robotok, amelyek hamisítják identitását. A rosszindulatú IP-címek a Microsoft Threat Intelligence-hírcsatornából származnak, és óránként frissülnek. A [intelligens biztonsági gráf](https://www.microsoft.com/security/operations/intelligence) a Microsoft fenyegetésekkel kapcsolatos intelligenciát, és több szolgáltatás, például Azure Security Center használatát is használja.

A jó robotok közé tartoznak az ellenőrzött keresőmotorok. Az ismeretlen kategóriák közé tartoznak olyan további robot-csoportok is, amelyek botokként azonosítottak. Például: piac-elemző, hírcsatorna-beolvasási és adatgyűjtési ügynökök. 

Az ismeretlen robotok további ellenőrzés nélkül vannak kategorizálva a közzétett felhasználói ügynökökön keresztül. Egyéni műveleteket is beállíthat a különböző típusú robotok blokkolásához, engedélyezéséhez, naplózásához és átirányításához.

![Robot Protection-szabály beállítása](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> A robot Protection-szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és az előzetes verziójú szolgáltatói szerződéssel rendelkezik. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha a bot Protection engedélyezve van, a rendszer a bot-szabályoknak megfelelő bejövő kérelmeket a FrontdoorWebApplicationFirewallLog naplóban naplózza. A WAF-naplókat egy Storage-fiókból, az Event hub-ból vagy a log Analyticsből is elérheti.

## <a name="configuration"></a>Konfiguráció

Az összes WAF konfigurálhatja és telepítheti a Azure Portal, a REST API-k, a Azure Resource Manager sablonok és a Azure PowerShell használatával.

## <a name="monitoring"></a>Figyelés

A WAF figyelése a bejárati ajtón integrált Azure Monitor a riasztások nyomon követésére és a forgalmi trendek egyszerű figyelésére.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az Azure-beli [webalkalmazási tűzfalat Application Gateway](../ag/ag-overview.md)