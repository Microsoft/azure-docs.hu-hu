---
title: Azure bejárati ajtó – DDoS Protection
description: Ez az oldal információt nyújt arról, hogy az Azure bejárati ajtó hogyan segít a DDoS-támadások elleni védelemben
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350783"
---
# <a name="ddos-protection-on-front-door"></a>DDoS-védelem a bejárati ajtón

Az Azure bejárati ajtó számos funkciót és sajátosságot tartalmaz, amelyek segíthetnek az elosztott szolgáltatásmegtagadási (DDoS) támadások megelőzésében. Ezek a funkciók megakadályozhatják, hogy a támadók elérjék az alkalmazást, és hatással legyenek az alkalmazás rendelkezésre állására és teljesítményére.

## <a name="integration-with-azure-ddos-protection-basic"></a>Integráció a Azure DDoS Protection alapszintű

A bejárati ajtót a Azure DDoS Protection Basic védi. A szolgáltatás alapértelmezés szerint integrálva van az előtérben lévő platformba, és díjmentesen, felár nélkül. A bejárati ajtó globálisan üzembe helyezett hálózatának teljes skálája és kapacitása védelmet nyújt a közös hálózati rétegbeli támadásokkal szemben a folyamatos forgalom monitorozása és a valós idejű mérséklés révén. Az alapszintű DDoS Protection a leggyakoribb, leggyakrabban előforduló, 7. rétegbeli DNS-lekérdezések áradásait, valamint a 3. és 4. rétegbeli támadásokat is védi a nyilvános végpontok megcélzásával. Ez a szolgáltatás bizonyítottan nyomon követheti a Microsoft vállalati és fogyasztói szolgáltatásainak védelmét a nagy méretű támadásokkal szemben. További információ: [Azure DDoS Protection](../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Protokollok blokkolása

A bejárati ajtó csak a HTTP és a HTTPS protokollok forgalmát fogadja el, és csak az érvényes kérelmeket fogja feldolgozni egy ismert `Host` fejléccel. Ez a viselkedés segít enyhíteni a DDoS-támadások bizonyos típusait, beleértve a különböző protokollok és portok, a DNS-erősítési támadások és a TCP-mérgezés elleni támadásokat.

## <a name="capacity-absorption"></a>Kapacitás-felszívódás

A bejárati ajtó egy nagy mértékben skálázható, globálisan elosztott szolgáltatás. Számos ügyfelünk van, beleértve a Microsoft saját nagy léptékű felhőalapú termékeit, amelyek másodpercenként több százezer kérést kapnak. A bejárati ajtó az Azure hálózatának peremén található, és a nagy mennyiségű támadásokat elnyeli és földrajzilag elkülöníti. Ez megakadályozhatja, hogy a rosszindulatú adatforgalom továbbra is az Azure-hálózat peremén haladjon.

## <a name="caching"></a>Gyorsítótárazás

A [bejárati ajtó gyorsítótárazási képességei](./front-door-caching.md) a támadás által generált nagy forgalmú kötetek hátterének biztosítására használhatók. A gyorsítótárazott erőforrásokat a rendszer az első ajtó peremhálózati csomópontjairól adja vissza, így azok nem jutnak el a háttérbe. A dinamikus válaszokban még a rövid gyorsítótár lejárati ideje (másodperc vagy perc) nagy mértékben csökkentheti a háttérbeli szolgáltatások terhelését. További információ a gyorsítótárazási fogalmakról és a mintákról: [gyorsítótárazási szempontok](/azure/architecture/best-practices/caching) és [gyorsítótár-](/azure/architecture/patterns/cache-aside)feltöltési minta.

## <a name="web-application-firewall-waf"></a>Webalkalmazási tűzfal (WAF)

A [bejárati ajtó webalkalmazási tűzfala (WAF)](../web-application-firewall/afds/afds-overview.md) számos különböző típusú támadás enyhítésére használható:

* A felügyelt szabálykészlet számos gyakori támadás ellen védelmet nyújt.
* Egy meghatározott földrajzi régión vagy egy meghatározott régión kívüli forgalom letiltható vagy átirányítható statikus weblapra. További információ: [geo-szűrés](../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* A kártékonyként azonosított IP-címek és tartományok letiltható.
* A díjszabás korlátozásával megakadályozható, hogy az IP-címek túl gyakran hívják meg a szolgáltatást.
* Létrehozhat [Egyéni WAF-szabályokat](../web-application-firewall/afds/waf-front-door-custom-rules.md) az ismert aláírásokkal rendelkező http-vagy https-támadások automatikus blokkolásához és sebességének korlátozásához.

## <a name="for-further-protection"></a>További védelem

Ha további védelemre van szüksége, engedélyezheti [Azure DDoS Protection standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) használatát azon a VNet, amelyen a háttér üzembe helyezése történik. DDoS Protection a standard szintű ügyfelek további előnyöket kapnak, például a költséghatékonyságot, az SLA-garanciát és a DDoS gyors válasz csapatának szakértőkhöz való hozzáférését a támadás során felmerülő azonnali segítség érdekében.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan konfigurálhat egy [WAF-profilt a bejárati ajtón](front-door-waf.md). 
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.