---
title: Azure Load Balancer és Availability Zones
titleSuffix: Azure Load Balancer
description: Ezzel a képzési útvonallal megismerheti az Azure standard Load Balancer és a Availability Zones.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101699113"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer és Availability Zones

Azure Load Balancer támogatja a rendelkezésre állási zónák forgatókönyveit. A standard Load Balancer használatával növelheti a rendelkezésre állást a forgatókönyvben az erőforrások és a zónák közötti elosztásával.  Tekintse át ezt a dokumentumot, hogy megértse ezeket a fogalmakat és az alapvető forgatókönyv kialakítási útmutatóját

A Load Balancer lehet **zónában redundáns,** többtényezős vagy **nem** zóna. A (fent említett) zóna kapcsolódó tulajdonságainak konfigurálásához a terheléselosztó esetében válassza ki a megfelelő típusú előtérbeli felületet.

## <a name="zone-redundant"></a>Felesleges zóna

A Availability Zonest tartalmazó régiókban a standard Load Balancer zónákhoz is redundáns lehet. Ezt a forgalmat egyetlen IP-cím szolgálja ki.

Egyetlen előtéri IP-cím fogja túlélni A zóna meghibásodását. Az előtérbeli IP-cím felhasználható az összes (nem érintett) háttérbeli készlet tagjainak elérésére a zónától függetlenül. Egy vagy több rendelkezésre állási zóna meghiúsulhat, és az adatelérési út addig marad, amíg a régió egy zónája Kifogástalan állapotba kerül.

A frontend IP-címét egyszerre több független infrastruktúra-telepítés is szolgálja, több rendelkezésre állási zónában. Az újrapróbálkozások és az újralétesítések a zóna meghibásodása által nem érintett más zónákban is sikeresek lesznek.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Ábra: a zóna redundáns Load Balancer*

## <a name="zonal"></a>Zónaszintű

Dönthet úgy is, hogy egy olyan előtéri felületet biztosít, *amely egy zónás.*  Ez a forgatókönyv azt jelenti, hogy a bejövő vagy kimenő folyamatokat egy adott régió egyetlen zónája szolgálja ki.  A frontend a zóna állapotával osztja meg a sorsot.  Az adatelérési útvonalat a rendszer nem érinti a nem az adott zónán kívüli zónák meghibásodása esetén. Az IP-cím rendelkezésre állási zónákban való megjelenítéséhez használhatja a zónákhoz tartozó előtérbeli felületet.  

Ezen kívül az egyes zónákon belüli elosztott terhelésű végpontok esetén közvetlenül a zóna-előtérbeli környezetek használata is támogatott. Ez a konfiguráció lehetővé teszi, hogy zónán belüli elosztott terhelésű végpontok számára elérhetővé tegye a zónák egyedi figyelését. Nyilvános végpontok esetén integrálhatja őket egy DNS terheléselosztási termékkel, például [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , és egyetlen DNS-nevet használhat.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Ábra: Zona Load Balancer*

Nyilvános Load Balancer-előtérbeli felület esetén a **zóna** paramétert a nyilvános IP-címhez adja hozzá. Ezt a nyilvános IP-címet a megfelelő szabály által használt előtéri IP-konfiguráció hivatkozik.

Belső terheléselosztó előtérbeli felületéhez adjon hozzá egy **Zones** paramétert a belső terheléselosztó ELŐTÉRBELI IP-konfigurációjához. A zónákhoz tartozó előtér-felület egy adott zónához tartozó IP-címet garantál egy adott alhálózaton.

## <a name="design-considerations"></a><a name="design"></a> Tervezési szempontok

Most, hogy megértette standard Load Balancer zónákhoz kapcsolódó tulajdonságait, a következő tervezési szempontok segíthetnek a magas rendelkezésre állás kialakításában.

### <a name="tolerance-to-zone-failure"></a>A zóna meghibásodásának tűréshatára

- A **zóna redundáns** Load Balancer bármely zónában, egyetlen IP-címmel tud kiszolgálni egy zónákból álló erőforrást.  Az IP-cím képes túlélni egy vagy több zóna meghibásodását, ha legalább egy zóna állapota Kifogástalan marad a régión belül.
- A **zónákra** épülő előtér a szolgáltatás egyetlen zónába való csökkentése, és a sors megosztva a megfelelő zónával. Ha az üzembe helyezési zóna leáll, az üzemelő példány nem fogja túlélni ezt a hibát.

Javasoljuk, hogy az éles számítási feladatokhoz használja a zóna redundáns Load Balancerét.

### <a name="control-vs-data-plane-implications"></a>A Control vs adatsíkok következményei

A zóna – a redundancia nem jelent hitless adatsíkon vagy vezérlési síkon. Zóna – a redundáns folyamatok bármilyen zónát használhatnak, és a folyamatok a régió összes kifogástalan zónáját felhasználják. A zóna meghibásodása esetén az egészséges zónákat használó forgalmi folyamatok nem érintettek.

A zóna meghibásodása esetén egy zónát használó adatforgalom befolyásolható, de az alkalmazások helyreállíthatók. A forgalom a régión belüli, kifogástalan állapotú zónákban folytatódik, amikor az Azure a zóna meghibásodása körül konvergált.

Tekintse át az [Azure Cloud design-mintákat](/azure/architecture/patterns/) az alkalmazás meghibásodási forgatókönyvek rugalmasságának javítása érdekében.

## <a name="next-steps"></a>Következő lépések
- További információ a [Availability Zones](../availability-zones/az-overview.md)
- További tudnivalók a [Standard Load Balancerről](./load-balancer-overview.md)
- Megtudhatja, hogyan oszthatja át [a virtuális gépeket egy zónán belül egy zóna használatával standard Load Balancer](./quickstart-load-balancer-standard-public-cli.md)
- Megtudhatja, hogyan oszthatja meg [a virtuális gépeket a zónák között egy redundáns zóna használatával standard Load Balancer](./quickstart-load-balancer-standard-public-cli.md)
- Ismerje meg az [Azure Felhőbeli kialakítási mintáit](/azure/architecture/patterns/) , amelyekkel javítható az alkalmazás meghibásodási forgatókönyvekhez való rugalmassága.
