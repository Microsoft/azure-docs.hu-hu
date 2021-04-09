---
title: Azure webalkalmazási tűzfal az Azure-beli bejárati szolgáltatásban – gyakori kérdések
description: Ez a cikk válaszokat ad az Azure-beli webalkalmazási tűzfallal kapcsolatos gyakori kérdésekre.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5c2763112b1aa2d58f5dc57cea72a3d0bdea961e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545669"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Gyakori kérdések az Azure-webalkalmazási tűzfalról az Azure bejárati szolgáltatásában

Ez a cikk az Azure webalkalmazási tűzfallal (WAF) kapcsolatos gyakori kérdésekre ad választ az Azure bejárati szolgáltatásának funkcióit és funkcióit illetően. 

## <a name="what-is-azure-waf"></a>Mi az Azure WAF?

Az Azure WAF egy webalkalmazási tűzfal, amely segít megvédeni webalkalmazásait az olyan gyakori fenyegetésektől, mint az SQL-injektálás, a helyek közötti parancsfájlkezelés és más webszolgáltatások használata. A webalkalmazásokhoz való hozzáférés szabályozásához egyéni és felügyelt szabályok kombinációjából álló WAF-szabályzatot is meghatározhat.

Az Azure-WAF szabályzatok alkalmazhatók Application Gateway vagy Azure-beli előtérben futó webalkalmazásokra is.

## <a name="what-is-waf-on-azure-front-door"></a>Mi a WAF az Azure bejárati ajtaján? 

Az Azure bejárati ajtó egy rugalmasan méretezhető, globálisan elosztott alkalmazás-és Content Delivery Network. Az Azure WAF a bejárati ajtóval integráltan leállítja az Azure-hálózat peremén a szolgáltatásmegtagadási és a megcélzó alkalmazások támadásait, és a virtuális hálózat megkezdése előtt a támadási forrásokhoz közeledik, és a teljesítmény feláldozása nélkül nyújt védelmet.

## <a name="does-azure-waf-support-https"></a>Támogatja az Azure WAF a HTTPS-t?

A bejárati ajtó TLS-kiszervezést biztosít. A WAF natív módon integrálva van a bejárati ajtóval, és a visszafejtés után megvizsgálhatja a kérést.

## <a name="does-azure-waf-support-ipv6"></a>Támogatja az Azure WAF az IPv6-ot?

Igen. Az IPv4-és IPv6-alapú IP-korlátozást is konfigurálhatja.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Hogyan lehet naprakész a felügyelt szabálykészlet?

Mi a legjobb megoldás a fenyegetés tájképének megváltoztatására. Új szabály frissítésekor a rendszer hozzáadja az alapértelmezett szabálykészlet új verziószámmal.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Mi a terjesztési idő, ha módosítást végezek a WAF-szabályzatban?

A WAF-szabályzatok globális üzembe helyezése általában körülbelül 5 percet vesz igénybe, és gyakran hamarabb elvégezhető.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Eltérőek lehetnek a különböző régiók WAF házirendjei?

A bejárati ajtóval integrált WAF globális erőforrás. Ugyanaz a konfiguráció az összes bejárati hely esetében érvényes.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Hogyan a háttérbeli hozzáférés korlátozása csak a bejárati ajtóról?

A háttérbeli IP-Access Control listát úgy is konfigurálhatja, hogy csak a kimenő IP-címtartományok számára engedélyezze a közvetlen hozzáférést az internetről. A szolgáltatás címkéi a virtuális hálózaton való használatra támogatottak. Emellett ellenőrizheti, hogy az X által továbbított-Host HTTP-fejléc mező érvényes-e a webalkalmazáshoz.

## <a name="which-azure-waf-options-should-i-choose"></a>Mely Azure WAF-beállításokat válasszam?

A WAF-szabályzatok az Azure-ban való alkalmazása két lehetőséget kínál. A WAF az Azure bejárati ajtó egy globálisan elosztott, peremhálózati biztonsági megoldás. A Application Gateway WAF egy regionális, dedikált megoldás. Javasoljuk, hogy válasszon egy megoldást a teljes teljesítmény-és biztonsági követelmények alapján. További információ: terheléselosztás [Az Azure Application Delivery Suite](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md)szolgáltatással.

## <a name="whats-the-recommended-approach-to-enabling-waf-on-front-door"></a>Mi a javasolt módszer a WAF bekapcsolásához?

Ha engedélyezi a WAF egy meglévő alkalmazáson, az gyakran előfordul, hogy hamis pozitív észlelésekkel rendelkezik, ahol a WAF-szabályok fenyegetésként észlelik a legitim forgalmat. A felhasználókra gyakorolt hatás kockázatának csökkentése érdekében a következő folyamatot javasoljuk:

* A WAF [ **észlelési** módban](./waf-front-door-create-portal.md#change-mode) való engedélyezésével biztosíthatja, hogy a WAF ne blokkolja a kérelmeket, amíg a folyamaton keresztül dolgozik.
  > [!IMPORTANT]
  > Ez a folyamat leírja, hogyan engedélyezheti a WAF egy új vagy meglévő megoldásban, ha a prioritása az alkalmazás felhasználóinak való zavarok csökkentése. Ha támadás vagy közelgő fenyegetés van, érdemes lehet Ehelyett azonnal telepíteni a **WAF, és** a hangolási folyamattal figyelheti és beállíthatja a WAF az idő múlásával. Ez valószínűleg azt eredményezi, hogy a megbízható adatforgalom blokkolva lesz, ezért csak akkor ajánljuk ezt, ha fenyegetést tapasztal.
* Kövesse a [WAF hangolásához szükséges útmutatást](./waf-front-door-tuning.md). Ehhez a folyamathoz engedélyeznie kell a diagnosztikai naplózást, rendszeresen ellenőriznie kell a naplókat, és hozzá kell adnia a szabályok kizárását és egyéb enyhítéseket.
* Ismételje meg ezt az egész folyamatot, rendszeresen ellenőrizze a naplókat, amíg nem meggyőződött arról, hogy a rendszer nem blokkolja a megbízható forgalmat. A teljes folyamat több hetet is igénybe vehet. Ideális esetben az egyes hangolási módosítások után kevesebb hamis pozitív észlelést kell látnia.
* Végezetül engedélyezze a WAF **megelőzési módban**.
* Még ha a WAF éles környezetben is futtatja, akkor a naplók figyelésével azonosítsa a többi hamis pozitív észlelést. A naplók rendszeres felülvizsgálata a blokkolt valódi támadási kísérletek azonosítását is lehetővé teszi.

## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Minden integrált platformon ugyanazokat az WAF funkciókat támogatja?

Jelenleg a ModSec CRS 2.2.9, a CRS 3,0 és a CRS 3,1 szabályok csak a WAF Application Gateway-on támogatottak. A díjszabás korlátozása, a Geo-szűrés és az Azure által felügyelt alapértelmezett szabálykészlet-szabályok csak az Azure-beli WAF támogatottak.

## <a name="is-ddos-protection-integrated-with-front-door"></a>A DDoS Protection integrálva van a bejárati ajtóval? 

Az Azure-beli hálózati éleken globálisan elosztott Azure bejárati ajtó képes felvenni és földrajzilag elkülöníteni a nagy mennyiségű támadásokat. Létrehozhat egyéni WAF szabályzatot, amellyel automatikusan blokkolhatja és korlátozhatja az ismert aláírásokkal rendelkező http (s) támadásokat. További részletekként engedélyezheti DDoS Protection szabványt azon a VNet, amelyen a háttérrendszer üzembe lett helyezve. Azure DDoS Protection standard ügyfeleink további kedvezményeket kapnak, például a költséghatékonyságot, az SLA-garanciát és a DDoS gyors reagálású csapat szakembereinek hozzáférését a támadás során felmerülő azonnali segítségért. További információ: [DDoS Protection on bejárati ajtó](../../frontdoor/front-door-ddos.md).

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Miért van szükség további kérelmekre a díjszabási korlátozási szabályhoz konfigurált küszöbérték felett a háttérrendszer-kiszolgáló felé?

A díjszabási szabály korlátozza a rendellenesen nagy forgalmat bármely ügyfél IP-címről. Az ügyfél IP-címe által engedélyezett webes kérelmek számát egy-egy vagy öt perces időtartam alatt konfigurálhatja. A részletes díjszabás szabályozása érdekében a mérték korlátozása további egyeztetési feltételekkel kombinálható, például a HTTP (S) paraméterekkel egyező. 

Az azonos ügyféltől érkező kérések gyakran ugyanahhoz az előtérben lévő kiszolgálóhoz érkeznek. Ebben az esetben a küszöbérték feletti további kérelmeket azonnal le kell tiltani. 

Azonban előfordulhat, hogy az azonos ügyféltől érkező kérések egy másik, a számlálóra vonatkozó korlátot nem frissítő bejárati kiszolgálótól érkeznek. Előfordulhat például, hogy az ügyfél új kapcsolatokat nyit meg az egyes kérésekhez, és a küszöbérték alacsony. Ebben az esetben az új bejárati kiszolgálóra irányuló első kérésnek meg kell felelnie a határérték-ellenőrzési értéknek. A díjszabási küszöbérték általában magas értékre van állítva, hogy az ügyfél IP-címéről érkező szolgáltatásmegtagadási támadások elleni védekezést végezzen. Nagyon alacsony küszöbérték esetén előfordulhat, hogy a küszöbérték feletti további kérelmeket is láthat.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az [Azure webalkalmazási tűzfalat](../overview.md).
- További információ az [Azure bejáratáról](../../frontdoor/front-door-overview.md).