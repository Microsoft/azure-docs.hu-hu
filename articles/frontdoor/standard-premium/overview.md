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
ms.openlocfilehash: 32654f743301f9f2f6c010947d73d957c96dceac
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100889"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Mi az Azure bejárati ajtó standard/Premium (előzetes verzió)?

> [!IMPORTANT]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Tekintse meg az Azure-beli előtérben lévő [dokumentumokat](../front-door-overview.md).

Az Azure bejárati ajtó standard/Premium egy gyors, megbízható és biztonságos, modern felhőalapú CDN, amely a Microsoft globális peremhálózati hálózatát használja, és integrálható az intelligens veszélyforrások elleni védelemmel. A Microsoft, az Azure bejárati ajtó, az Azure webalkalmazási tűzfal (WAF) Azure CDN szabványának képességeit egyetlen biztonságos felhőalapú CDN-platformra ötvözi.

A standard/prémium szintű Azure-ban a globális fogyasztói és nagyvállalati alkalmazásokat biztonságos és magas teljesítményű, személyre szabott modern alkalmazásokkal alakíthatja át, amelyekben az alacsony késéssel rendelkező globális célközönségek is elérhetők.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Azure bejárati ajtó standard/prémium architektúrája" lightbox="../media/overview/front-door-overview-expanded.png":::

Az Azure bejárati ajtó standard/Premium a 7. rétegbeli (HTTP/HTTPS) rétegben működik, és a globális kapcsolatok javítása érdekében a Split TCP és a Microsoft globális hálózatának használatával támogatja a többhelyes protokollt Az útválasztási módszer alapján biztosíthatja, hogy az Azure bevezető ajtaja átirányítsa az ügyfelek kérelmeit a leggyorsabb és leginkább elérhető forrásra. Az alkalmazás-háttérrendszer egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás. A AzureFront Door standard/Premium számos forgalom-útválasztási módszert és a forrás állapot figyelési lehetőségeit kínálja a különböző alkalmazási igényeknek és automatikus feladatátvételi forgatókönyveknek megfelelően. A Traffic Managerhoz hasonlóan a bejárati ajtó is rugalmasan működik a hibáknál, beleértve a teljes Azure-régió meghibásodásait is.

Az Azure bejárati ajtaja is védi az alkalmazást a Web Application Firewall, a bot Protection és a beépített 3/Layer 4 DDoS Protection használatával. Emellett a privát kapcsolati szolgáltatással is biztonságossá teszi a privát háttér-végpontokat. Az Azure bejárati ajtaja globális méretekben kínálja a Microsoft legjobb biztonsági megoldásait.  

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

Az Azure bejárati ajtó standard/Premium egyetlen egységesített platformot biztosít a statikus tartalmakhoz és a dinamikus alkalmazások gyorsításához, fokozott biztonsági funkciókkal. A bejárati ajtó lehetővé teszi az alkalmazás globális útválasztásának meghatározását, kezelését és figyelését is.

Az Azure bejárati ajtó standard/Premium (előzetes verzió) főbb szolgáltatásai:

- Gyorsított alkalmazások teljesítménye a Split TCP-alapú csomópontos protokoll használatával.

- Az intelligens **[állapot](concept-health-probes.md)** mintavételének figyelése és terheléselosztása az **[eredetek](concept-origin.md)** között.

- Saját egyéni tartományt definiálhat rugalmas tartományi ellenőrzéssel.

- Alkalmazások biztonsága integrált [webalkalmazási tűzfallal (WAF)](../../web-application-firewall/afds/afds-overview.md).

- SSL-kiszervezés és integrált tanúsítványkezelő.

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
