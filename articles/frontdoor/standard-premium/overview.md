---
title: Azure bejárati ajtó standard/prémium | Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure bejárati standard/Premium kiadásáról.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: bcfff1a2c8490a05f4b96a8e2ff68186348f596f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742364"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Mi az Azure bejárati ajtó standard/Premium (előzetes verzió)?

> [!IMPORTANT]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Tekintse meg az Azure-beli előtérben lévő [dokumentumokat](../front-door-overview.md).

Az Azure bejárati ajtó standard/Premium egy gyors, megbízható és biztonságos, modern felhőalapú CDN, amely a Microsoft globális peremhálózati hálózatát használja, és integrálható az intelligens veszélyforrások elleni védelemmel. Az Azure bejárati ajtó, az Azure Content Delivery Network (CDN) standard és az Azure webalkalmazási tűzfal (WAF) funkcióit egyetlen biztonságos felhőalapú CDN-platformra ötvözi.

A standard/prémium szintű Azure-szolgáltatásokkal a globális fogyasztói és vállalati alkalmazásokat biztonságos és magas teljesítményű, személyre szabott modern alkalmazásokkal alakíthatja át, amelyekben a felhasználók a hálózat peremén található globális közönséget érhetik el. Azt is lehetővé teszi, hogy az alkalmazás betöltés nélkül, a globális HTTP-terheléselosztás azonnali feladatátvételsel való használata mellett is felskálázást biztosítson.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Azure bejárati ajtó standard/prémium architektúrája" lightbox="../media/overview/front-door-overview-expanded.png":::

Az Azure bejárati ajtó standard/Premium a 7. rétegbeli (HTTP/HTTPS) rétegben működik, és a globális kapcsolatok javítása érdekében a Split TCP és a Microsoft globális hálózatának használatával támogatja a többhelyes protokollt A beállított szabályok alapján, a testreszabott útválasztási módszer alapján biztosíthatja, hogy az Azure bevezető ajtaja a leggyorsabb és legteljesebb forrásra irányítsa át az ügyfelek kérelmeit. Az alkalmazás forrása bármely, az Azure-on belül vagy kívül üzemeltetett, internetre irányuló szolgáltatás. Az Azure bejárati ajtó standard/Premium számos forgalom-útválasztási módszert és a forrás állapot figyelési lehetőségeit kínálja a különböző alkalmazási igényeknek és automatikus feladatátvételi forgatókönyveknek megfelelően. A Traffic Managerhoz hasonlóan a bejárati ajtó is rugalmasan működik a hibáknál, beleértve a teljes Azure-régió meghibásodásait is.

Az Azure bejárati ajtaja is védi az alkalmazást a szélein, az integrált webalkalmazási tűzfallal, a bot Protection szolgáltatással és a beépített 3/4. rétegbeli elosztott szolgáltatásmegtagadási (DDoS) védelemmel. Emellett a privát kapcsolati szolgáltatással is biztonságossá teszi a privát háttér-végpontokat. Az Azure bejárati ajtaja globális méretekben kínálja a Microsoft legjobb biztonsági megoldásait.  

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre.
>
> * Ha DNS-alapú globális útválasztást szeretne végezni, és **nem** rendelkezik a TRANSPORT Layer Security (TLS) protokoll leállítására ("SSL-kiszervezés"), a HTTP/HTTPS-kérelemre vagy az alkalmazás-réteg feldolgozására vonatkozó követelményekkel, tekintse át a [Traffic Manager](../../traffic-manager/traffic-manager-overview.md).
> * Ha terheléselosztást szeretne betölteni a kiszolgálók között az alkalmazási réteg egyik régiójában, tekintse át [Application Gateway](../../application-gateway/overview.md)
> * A hálózati réteg terheléselosztásának elvégzéséhez tekintse át [Load Balancer](../../load-balancer/load-balancer-overview.md).
>
> A végpontok közötti forgatókönyvek igény szerint összekapcsolják ezeket a megoldásokat.
> Az Azure terheléselosztási lehetőségeinek összehasonlítását lásd: [Az Azure terheléselosztási lehetőségeinek áttekintése](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium verziója jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Miért érdemes az Azure bejárati ajtót standard/Premium (előzetes verzió) használatára használni?

Az Azure Door standard/Premium egyetlen egységesített platformot biztosít, amely dinamikus és statikus gyorsítást tesz lehetővé beépített kulcsrakész biztonsági integrációval, valamint egy egyszerű és kiszámítható díjszabási modellel. A bejárati ajtó lehetővé teszi az alkalmazás globális útválasztásának meghatározását, kezelését és figyelését is.

Az Azure bejárati ajtó standard/Premium (előzetes verzió) főbb szolgáltatásai:

- Gyorsított alkalmazások teljesítménye a **[Split TCP-alapú](../front-door-routing-architecture#splittcp.md)** csomópontos protokoll használatával.

- Az intelligens **[állapot](concept-health-probes.md)** mintavételének figyelése és terheléselosztása az **[eredetek](concept-origin.md)** között.

- Saját **[Egyéni tartományt](how-to-add-custom-domain.md)** definiálhat rugalmas tartományi ellenőrzéssel.

- Alkalmazások biztonsága integrált **[webalkalmazási tűzfallal (WAF)](../../web-application-firewall/afds/afds-overview.md)**.

- SSL-kiszervezés és integrált **[tanúsítványkezelő](how-to-configure-https-custom-domain.md)**.

- Saját eredetének védelme **[privát hivatkozással](concept-private-link.md)**.  

- Testreszabható forgalom útválasztása és optimalizálása **[szabálykészlet](concept-rule-set.md)** használatával.

- **[Beépített jelentések](how-to-reports.md)** teljes körű irányítópulttal a bejárati ajtó és a biztonsági minták számára.

- **[Valós idejű figyelés](how-to-monitor-metrics.md)** és riasztások, amelyek integrálva vannak az Azure monitoring szolgáltatással.

- **[Naplózás](how-to-logs.md)** minden bejárati ajtós kérelemhez és sikertelen állapot-mintavételhez.

- A végpontok közötti IPv6-kapcsolat és a HTTP/2 protokoll natív támogatása.

## <a name="pricing"></a>Díjszabás

Az Azure bejárati ajtó standard/Premium két SKU, standard és Premium csomaggal rendelkezik. Lásd a [rétegek összehasonlítását](tier-comparison.md). Díjszabási információkért tekintse meg [A Front Door díjszabása](https://azure.microsoft.com/pricing/details/frontdoor/) című cikket. 

## <a name="whats-new"></a>Újdonságok

Fizessen elő az RSS-hírcsatornára, és tekintse meg az Azure-beli első ajtó funkciójának frissítéseit az [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) oldalon.

## <a name="next-steps"></a>Következő lépések

* Útmutató a [Front Door létrehozásához](create-front-door-portal.md).
