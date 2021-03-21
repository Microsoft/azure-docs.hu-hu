---
title: Mi az az Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Az Azure Load Balancer funkciói, architektúrája és implementálása. Ismerje meg, hogyan működik a Load Balancer és hogyan használható a felhőben.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 51ceb72d53f78264edcadd2255e20c8fbdac2cae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181210"
---
# <a name="what-is-azure-load-balancer"></a>Mi az az Azure Load Balancer?

A *terheléselosztás* a betöltés (bejövő hálózati forgalom) egyenletes elosztását jelenti a háttérbeli erőforrások vagy kiszolgálók egy csoportján belül. 

Azure Load Balancer a nyílt rendszerek összekapcsolására szolgáló modell 4. rétegében működik. Ez az egyetlen kapcsolódási pont az ügyfelek számára. A Load Balancer elosztja a terheléselosztó előtér-példányaira érkező bejövő folyamatokat. Ezek a folyamatok a konfigurált terheléselosztási szabályok és az állapot-mintavételek szerint vannak megadva. A háttérbeli készlet példányai lehetnek Azure-Virtual Machines vagy-példányok egy virtuálisgép-méretezési csoportokban.

A **[nyilvános terheléselosztó](./components.md#frontend-ip-configurations)** a virtuális hálózatban lévő virtuális gépek (VM-EK) kimenő kapcsolatait is biztosítja. Ezek a kapcsolatok a magánhálózati IP-címek nyilvános IP-címekre való fordításával valósíthatók meg. A nyilvános terheléselosztó a virtuális gépek internetes forgalmának elosztására szolgál.

A **[belső (vagy magánhálózati)](./components.md#frontend-ip-configurations)** terheléselosztó akkor használatos, ha a magánhálózati IP-címekre csak a frontenden van szükség. A belső terheléselosztó a virtuális hálózaton belüli forgalom elosztására szolgál. A terheléselosztó felülete hibrid forgatókönyv esetén a helyszíni hálózatról is elérhető.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Ábra: többrétegű alkalmazások terheléselosztása nyilvános és belső Load Balancer használatával*

További információ az egyes terheléselosztó-összetevőkről: Azure Load Balancer- [összetevők](./components.md).

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. 
> * Ha DNS-alapú globális útválasztást szeretne végezni, és **nem** rendelkezik a TRANSPORT Layer Security (TLS) protokoll leállítására ("SSL-kiszervezés"), a HTTP/HTTPS-kérelemre vagy az alkalmazás-réteg feldolgozására vonatkozó követelményekkel, tekintse át a [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Ha terheléselosztást szeretne betölteni a kiszolgálók között az alkalmazási réteg egyik régiójában, tekintse át [Application Gateway](../application-gateway/overview.md).
> * Ha optimalizálni szeretné a webes forgalom globális útválasztását, és a gyors globális feladatátvételsel optimalizálja a legfelső szintű végfelhasználói teljesítményt és megbízhatóságot, tekintse meg a [bejárati ajtót](../frontdoor/front-door-overview.md).
> 
> A végpontok közötti forgatókönyvek igény szerint összekapcsolják ezeket a megoldásokat.
> Az Azure terheléselosztási lehetőségeinek összehasonlítását lásd: [Az Azure terheléselosztási lehetőségeinek áttekintése](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="why-use-azure-load-balancer"></a>Miért érdemes Azure Load Balancer?
A Azure Load Balancer segítségével méretezheti az alkalmazásokat, és létrehozhat olyan, magasan elérhető szolgáltatásokat. A Load Balancer mind a bejövő, mind a kimenő forgatókönyveket támogatja. A Load Balancer alacsony késést és nagy átviteli sebességet biztosít, és akár több millió folyamatra is méretezhető az összes TCP-és UDP-alkalmazás esetében.

Az Azure standard Load Balancer használatával végrehajtható főbb forgatókönyvek a következők:

- A **[belső](./quickstart-load-balancer-standard-internal-portal.md)** és a **[külső](./quickstart-load-balancer-standard-public-portal.md)** forgalom terheléselosztása az Azure Virtual Machines szolgáltatásba.

- Növelje a rendelkezésre állást az erőforrások zónán **[belüli](./tutorial-load-balancer-standard-public-zonal-portal.md)** és **[közötti](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** elosztásával.

- Az Azure-beli virtuális gépek **[kimenő kapcsolatának](./load-balancer-outbound-connections.md)** konfigurálása.

- Az **[állapot](./load-balancer-custom-probe-overview.md)** -mintavétel segítségével figyelheti a terheléselosztási erőforrásokat.

- A **[port továbbításával](./tutorial-load-balancer-port-forwarding-portal.md)** a virtuális gépek nyilvános IP-cím és port használatával férhetnek hozzá a virtuális hálózatokhoz.

- Engedélyezze az IPv6 **[terheléselosztásának](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** támogatását **[](../virtual-network/ipv6-overview.md)**.

- A standard Load Balancer többdimenziós metrikákat biztosít [Azure monitoron](../azure-monitor/overview.md)keresztül.  Ezek a metrikák szűrhetők, csoportosíthatók és kibonthatók egy adott dimenzióra vonatkozóan.  A szolgáltatás teljesítményének és állapotának aktuális és korábbi betekintést nyújtanak. A [Azure Load Balancer bepillantást nyerhet](./load-balancer-insights.md) előre konfigurált irányítópultot, amely hasznos vizualizációkkal rendelkezik ezekhez a metrikához.  A Resource Health is támogatott. További részletekért tekintse meg a **[standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md)** .

- Terheléselosztási szolgáltatásokat **[több porton, több IP-címen vagy mindkettőn](./load-balancer-multivip-overview.md)**.

- **[Belső](./move-across-regions-internal-load-balancer-portal.md)** és **[külső](./move-across-regions-external-load-balancer-portal.md)** terheléselosztó erőforrásainak áthelyezése az Azure-régiók között.

- A TCP-és UDP-forgalom terheléselosztása minden porton egyidejűleg **[Ha portokat](./load-balancer-ha-ports-overview.md)** használ.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Alapértelmezés szerint biztonságos

* A standard Load Balancer a zéró megbízhatóságú hálózati biztonsági modellre épül.

* A standard Load Balancer alapértelmezés szerint biztonságos, és a virtuális hálózat része. A virtuális hálózat egy privát és elszigetelt hálózat.  

* A standard szintű terheléselosztó és a szabványos nyilvános IP-címek a bejövő kapcsolatokhoz vannak zárva, kivéve, ha a hálózati biztonsági csoportok megnyitják őket. A NSG az engedélyezett forgalom explicit módon történő engedélyezésére szolgálnak.  Ha nem rendelkezik NSG a virtuális gép erőforrásának alhálózatán vagy hálózati ADAPTERén, a forgalom nem jogosult az erőforrás elérésére. A NSG és a forgatókönyvre való alkalmazásával kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md).

* Az alapszintű Load Balancer alapértelmezés szerint nyitva van az interneten. 

* A Load Balancer nem tárolja az ügyféladatokat.

## <a name="pricing-and-sla"></a>Díjszabás és SLA

A standard Load Balancer díjszabási információit lásd: [Load Balancer díjszabása](https://azure.microsoft.com/pricing/details/load-balancer/).
Az alapszintű Load Balancer díjmentesen vehető igénybe.
Lásd: [SLA a Load Balancerhez](https://aka.ms/lbsla). Az alapszintű Load Balancer nem rendelkezik SLA-val.

## <a name="whats-new"></a>Újdonságok

Fizessen elő az RSS-hírcsatornára, és tekintse meg a legújabb Azure Load Balancer szolgáltatás frissítéseit az [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) oldalon.

## <a name="next-steps"></a>Következő lépések

A Load Balancer használatának első lépéseiért tekintse meg [a nyilvános standard Load Balancer létrehozása](quickstart-load-balancer-standard-public-portal.md) című témakört.

A Azure Load Balancer korlátozásokkal és összetevőkkel kapcsolatos további információkért lásd: [Azure Load Balancer összetevők](./components.md) és [Azure Load Balancer fogalmak](./concepts.md)