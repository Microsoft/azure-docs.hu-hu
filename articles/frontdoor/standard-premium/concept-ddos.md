---
title: 'Azure-beli bejárati ajtó: DDoS Protection'
description: Ez az oldal információt nyújt arról, hogy az Azure bejárati ajtó standard/Premium hogyan nyújt védelmet a DDoS-támadások ellen
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100145"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>DDoS-védelem az Azure-beli előtérben standard/prémium (előzetes verzió)

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure bejárati ajtó számos funkciót és sajátosságot tartalmaz, amelyek segíthetnek az elosztott szolgáltatásmegtagadási (DDoS) támadások megelőzésében. Ezek a funkciók megakadályozhatják, hogy a támadók elérjék az alkalmazást, és hatással legyenek az alkalmazás rendelkezésre állására és teljesítményére.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="integration-with-azure-ddos-protection-basic"></a>Integráció a Azure DDoS Protection alapszintű

A bejárati ajtót a Azure DDoS Protection Basic védi. A szolgáltatás alapértelmezés szerint és külön díj nélkül integrálva van az előtérben lévő platformba. A bejárati ajtó globálisan üzembe helyezett hálózatának teljes skálája és kapacitása védelmet nyújt a közös hálózati rétegbeli támadásokkal szemben a folyamatos forgalom monitorozása és a valós idejű mérséklés révén. Az alapszintű DDoS Protection a leggyakoribb, leggyakrabban előforduló, 7. rétegbeli DNS-lekérdezések áradásait, valamint a 3. és 4. rétegbeli támadásokat is védi a nyilvános végpontok megcélzásával. Ez a szolgáltatás bizonyítottan nyomon követheti a Microsoft vállalati és fogyasztói szolgáltatásainak védelmét a nagy méretű támadásokkal szemben. További információ: [Azure DDoS Protection](../../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Protokollok blokkolása

A bejárati ajtó csak a HTTP és a HTTPS protokollok forgalmát fogadja el, és csak az érvényes kérelmeket fogja feldolgozni egy ismert `Host` fejléccel. Ez a viselkedés segít enyhíteni a DDoS-támadások bizonyos típusait, beleértve a különböző protokollok és portok, a DNS-erősítési támadások és a TCP-mérgezés elleni támadásokat.

## <a name="capacity-absorption"></a>Kapacitás-felszívódás

A bejárati ajtó egy nagy mértékben skálázható, globálisan elosztott szolgáltatás. Számos ügyfelünk van, beleértve a Microsoft saját nagy léptékű felhőalapú termékeit, amelyek másodpercenként több százezer kérést kapnak. A bejárati ajtó az Azure hálózatának peremén, a nagy mennyiségű támadás bevonásán és földrajzilag elkülönítve található. Ez megakadályozhatja, hogy a rosszindulatú adatforgalom továbbra is az Azure-hálózat peremén haladjon.

## <a name="caching"></a>Gyorsítótárazás

A [bejárati ajtó gyorsítótárazási képességei](concept-caching.md) a támadás által generált nagy forgalmú kötetek hátterének biztosítására használhatók. A gyorsítótárazott erőforrásokat a rendszer az első ajtó peremhálózati csomópontjairól adja vissza, így azok nem jutnak el a háttérbe. A dinamikus válaszokban még a rövid gyorsítótár lejárati ideje (másodperc vagy perc) nagy mértékben csökkentheti a háttérbeli szolgáltatások terhelését. További információ a gyorsítótárazási fogalmakról és a mintákról: [gyorsítótárazási szempontok](/azure/architecture/best-practices/caching) és [gyorsítótár-](/azure/architecture/patterns/cache-aside)feltöltési minta.

## <a name="web-application-firewall-waf"></a>Webalkalmazási tűzfal (WAF)

A [bejárati ajtó webalkalmazási tűzfala (WAF)](../../web-application-firewall/afds/afds-overview.md) számos különböző típusú támadás enyhítésére használható:

* A felügyelt szabálykészlet használatával számos gyakori támadás ellen védelmet nyújt.
* Egy meghatározott földrajzi régión vagy egy meghatározott régión kívüli forgalom letiltható vagy átirányítható statikus weblapra. További információ: [geo-szűrés](../../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* A kártékonyként azonosított IP-címek és tartományok letiltható.
* A díjszabás korlátozásával megakadályozható, hogy az IP-címek túl gyakran hívják meg a szolgáltatást.
* Létrehozhat [Egyéni WAF-szabályokat](../../web-application-firewall/afds/waf-front-door-custom-rules.md) az ismert aláírásokkal rendelkező http-vagy https-támadások automatikus blokkolásához és sebességének korlátozásához.

## <a name="for-further-protection"></a>További védelem

Ha további védelemre van szüksége, engedélyezheti [Azure DDoS Protection standard](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) használatát azon a VNet, amelyen a háttér üzembe helyezése történik. DDoS Protection a standard szintű ügyfelek több előnyt kapnak, többek között:

* Cost Protection
* SLA-garancia
* A DDoS Rapid Response csapat szakértőinek hozzáférése azonnali segítségért a támadás során.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre standard/prémium szintű bejárati ajtót](create-front-door-portal.md).
