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
ms.openlocfilehash: 716acec51226e9e4ff47605c3ee2bd9a86be1933
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588560"
---
# <a name="what-is-azure-load-balancer"></a>Mi az az Azure Load Balancer?

A *terheléselosztás* a betöltés (bejövő hálózati forgalom) egyenletes elosztását jelenti a háttérbeli erőforrások vagy kiszolgálók egy csoportján belül. 

Azure Load Balancer a nyílt rendszerek közötti összekapcsolási (OSI) modell négy rétegében működik. Ez az egyetlen kapcsolódási pont az ügyfelek számára. A Load Balancer elosztja a terheléselosztó előtér-példányaira érkező bejövő folyamatokat. Ezek a folyamatok a konfigurált terheléselosztási szabályok és az állapot-mintavételek szerint vannak megadva. A háttérbeli készlet példányai lehetnek Azure-Virtual Machines vagy-példányok egy virtuálisgép-méretezési csoportokban.

A **[nyilvános terheléselosztó](./components.md#frontend-ip-configurations)** a virtuális hálózatban lévő virtuális gépek (VM-EK) kimenő kapcsolatait is biztosítja. Ezek a kapcsolatok a magánhálózati IP-címek nyilvános IP-címekre való fordításával valósíthatók meg. A nyilvános terheléselosztó a virtuális gépek internetes forgalmának elosztására szolgál.

A **[belső (vagy magánhálózati)](./components.md#frontend-ip-configurations)** terheléselosztó akkor használatos, ha a magánhálózati IP-címekre csak a frontenden van szükség. A belső terheléselosztó a virtuális hálózaton belüli forgalom elosztására szolgál. A terheléselosztó felülete hibrid forgatókönyv esetén a helyszíni hálózatról is elérhető.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Ábra: többrétegű alkalmazások terheléselosztása nyilvános és belső Load Balancer használatával*

További információ az egyes terheléselosztó-összetevőkről: Azure Load Balancer- [összetevők](./components.md).

## <a name="why-use-azure-load-balancer"></a>Miért érdemes Azure Load Balancer?
A standard Load Balancer segítségével méretezheti az alkalmazásokat, és létrehozhat olyan, magasan elérhető szolgáltatásokat. A Load Balancer mind a bejövő, mind a kimenő forgatókönyveket támogatja. A Load Balancer alacsony késést és nagy átviteli sebességet biztosít, és akár több millió folyamatra is méretezhető az összes TCP-és UDP-alkalmazás esetében.

A standard Load Balancer használatával végrehajtható főbb forgatókönyvek a következők:

- A **[belső](./quickstart-load-balancer-standard-internal-portal.md)** és a **[külső](./quickstart-load-balancer-standard-public-portal.md)** forgalom terheléselosztása az Azure Virtual Machines szolgáltatásba.

- Növelje a rendelkezésre állást az erőforrások zónán **[belüli](./tutorial-load-balancer-standard-public-zonal-portal.md)** és **[közötti](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** elosztásával.

- Az Azure-beli virtuális gépek **[kimenő kapcsolatának](./load-balancer-outbound-connections.md)** konfigurálása.

- Az **[állapot](./load-balancer-custom-probe-overview.md)** -mintavétel segítségével figyelheti a terheléselosztási erőforrásokat.

- A **[port továbbításával](./tutorial-load-balancer-port-forwarding-portal.md)** a virtuális gépek nyilvános IP-cím és port használatával férhetnek hozzá a virtuális hálózatokhoz.

- Engedélyezze az IPv6 **[terheléselosztásának](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** támogatását **[](../virtual-network/ipv6-overview.md)**.

- Standard Load Balancer többdimenziós metrikákat biztosít [Azure monitoron](../azure-monitor/overview.md)keresztül.  Ezek a metrikák szűrhetők, csoportosíthatók és kibonthatók egy adott dimenzióra vonatkozóan.  A szolgáltatás teljesítményének és állapotának aktuális és korábbi betekintést nyújtanak. A [Azure Load Balancer bepillantást nyerhet](https://docs.microsoft.com/azure/load-balancer/load-balancer-insights) előre konfigurált irányítópultot, amely hasznos vizualizációkkal rendelkezik ezekhez a metrikához.  A Resource Health is támogatott. További részletekért tekintse át **[standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md)** .

- Terheléselosztási szolgáltatásokat **[több porton, több IP-címen vagy mindkettőn](./load-balancer-multivip-overview.md)**.

- **[Belső](./move-across-regions-internal-load-balancer-portal.md)** és **[külső](./move-across-regions-external-load-balancer-portal.md)** terheléselosztó erőforrásainak áthelyezése az Azure-régiók között.

- A TCP-és UDP-forgalom terheléselosztása minden porton egyidejűleg **[Ha portokat](./load-balancer-ha-ports-overview.md)** használ.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Alapértelmezés szerint biztonságos

A standard Load Balancer a megbízható hálózati biztonsági modellre épül. A standard Load Balancer alapértelmezés szerint biztonságos, és a virtuális hálózat része. A virtuális hálózat egy privát és elszigetelt hálózat.  Ez azt jelenti, hogy a standard szintű terheléselosztó és a szabványos nyilvános IP-címek a bejövő forgalomba vannak zárva, kivéve, ha a hálózati biztonsági csoportok meg vannak nyitva. A NSG az engedélyezett forgalom explicit módon történő engedélyezésére szolgálnak.  Ha nem rendelkezik NSG a virtuális gép erőforrásának alhálózatán vagy hálózati ADAPTERén, a forgalom nem jogosult az erőforrás elérésére. Ha többet szeretne megtudni a NSG és a forgatókönyvre való alkalmazásáról, tekintse meg a [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md)című témakört.
Az alapszintű Load Balancer alapértelmezés szerint nyitva van az interneten. Emellett a Load Balancer nem tárolja az ügyféladatokat.

## <a name="pricing-and-sla"></a>Díjszabás és SLA

Standard Load Balancer díjszabási információkért lásd: [Load Balancer díjszabása](https://azure.microsoft.com/pricing/details/load-balancer/).
Az Alapszintű Load Balancer használata ingyenes.
Lásd: [Load Balancer SLA](https://aka.ms/lbsla)-ja. Az alapszintű Load Balancer nem biztosít SLA-t.

## <a name="whats-new"></a>Újdonságok

Fizessen elő az RSS-hírcsatornára, és tekintse meg a legújabb Azure Load Balancer szolgáltatás frissítéseit az [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) oldalon.

## <a name="next-steps"></a>Következő lépések

A Load Balancer használatának első lépéseiért tekintse meg [a nyilvános standard Load Balancer létrehozása](quickstart-load-balancer-standard-public-portal.md) című témakört.

Azure Load Balancer korlátozásokkal és összetevőkkel kapcsolatos további információkért lásd: [Azure Load Balancer összetevők](./components.md) és [Azure Load Balancer fogalmak](./concepts.md)
