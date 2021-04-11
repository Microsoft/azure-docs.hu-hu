---
title: Mi az Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: A Virtual Network NAT funkcióinak, erőforrásainak, architektúrájának és megvalósításának áttekintése. Megtudhatja, hogyan működik Virtual Network NAT és hogyan használható a NAT Gateway-erőforrások a felhőben.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: 3a4e9649d921c6af34d99ab290adb96be3ad1282
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058527"
---
# <a name="what-is-virtual-network-nat"></a>Mi az Virtual Network NAT?

Virtual Network NAT (hálózati címfordítás) egyszerűbbé teszi a csak kimenő internetkapcsolatot a virtuális hálózatokhoz. Ha egy alhálózaton van konfigurálva, minden kimenő kapcsolat a megadott statikus nyilvános IP-címeket használja.  A kimenő kapcsolat a virtuális gépekhez közvetlenül csatlakoztatott terheléselosztó vagy nyilvános IP-címek nélkül is lehetséges. A NAT teljes körűen felügyelt és rugalmas.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP (PIP) and an IP prefix." width="256" title="Virtual Network NAT">
</p>



*Ábra: Virtual Network NAT*

## <a name="static-ip-addresses-for-outbound-only"></a>Statikus IP-címek csak kimenőként

A kimenő kapcsolatok a NAT-vel rendelkező minden egyes alhálózat esetében meghatározhatók.  Ugyanazon a virtuális hálózaton belül több alhálózat is rendelkezhet különböző NAT-hálózatokkal. Az alhálózat konfigurálásához meg kell adni a NAT-átjáró által használt erőforrást. Bármely virtuálisgép-példány UDP-és TCP-kimenő forgalma a NAT protokollt fogja használni. 

A NAT kompatibilis a szabványos SKU nyilvános IP-címek erőforrásaival vagy a nyilvános IP-előtag erőforrásaival vagy a kettő kombinációjával.  A nyilvános IP-előtagot közvetlenül is használhatja, vagy eloszthatja az előtag nyilvános IP-címeit több NAT Gateway-erőforrás között. A NAT a teljes adatforgalmat az előtag IP-címeinek tartományához irányítja.  Az üzemelő példányok IP-szűrése már egyszerű.

Az alhálózat összes kimenő forgalmát a NAT automatikusan dolgozza fel az ügyfél-konfiguráció nélkül.  A felhasználó által megadott útvonalak nem szükségesek. A NAT elsőbbséget élvez más kimenő forgatókönyvekkel szemben, és lecseréli egy alhálózat alapértelmezett internetes célját.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>Igény szerinti SNAT több IP-címmel a skálázáshoz

A NAT a "port hálózati címfordítást" (PNAT vagy PAT) használja, és a legtöbb munkaterheléshez ajánlott. A dinamikus vagy eltérő munkaterhelések könnyen elhelyezhetők az igény szerinti kimenő forgalom lefoglalásával. A rendszer elkerüli a részletes előzetes tervezést, előzetes kiosztást, és végül a kimenő erőforrások túlzott kiépítését. A SNAT-portok erőforrásai megosztva és minden alhálózaton elérhetők egy adott NAT Gateway-erőforrás használatával, és szükség esetén rendelkezésre állnak.

A NAT-hoz csatlakoztatott nyilvános IP-címek akár 64 000 egyidejű folyamatot biztosítanak az UDP és a TCP számára. Elkezdheti egyetlen IP-cím használatát, és akár 16 IP-címet is felhasználhat nyilvános IP-címekkel vagy nyilvános IP-előtagokkal vagy mindkettővel.  A NAT-átjáró erőforrás az erőforráshoz társított összes IP-címet az azonos NAT-átjáró erőforrással konfigurált összes alhálózatról kimenő kapcsolatokra fogja használni.

A NAT lehetővé teszi, hogy a folyamatok a virtuális hálózatról az internetre jöjjenek létre. Az internetről érkező adatforgalom csak aktív folyamatra adott válaszként engedélyezett.

A terheléselosztó kimenő SNAT eltérően a NAT nem rendelkezik korlátozásokkal, amelyek esetében a virtuálisgép-példányok magánhálózati IP-címe kimenő kapcsolatokat hajthat végre.  Az elsődleges és másodlagos IP-konfigurációk kimenő internetkapcsolatot hozhatnak létre a NAT használatával.

## <a name="coexistence-of-inbound-and-outbound"></a>A bejövő és a kimenő együttes létezése

A NAT a következő szabványos SKU-erőforrásokkal kompatibilis:

- Terheléselosztóval
- Nyilvános IP-cím
- Nyilvános IP-előtag

NAT-vel együtt használva ezek az erőforrások bejövő internetkapcsolatot biztosítanak az alhálózat (k) számára. A NAT biztosítja az alhálózat (k) összes kimenő internetkapcsolatát.

A NAT és a kompatibilis szabványos SKU-funkciók tisztában vannak a folyamat elindításának irányával. A bejövő és a kimenő forgatókönyvek is létezhetnek. Ezek a forgatókönyvek megkapják a hálózati címfordítás helyes fordítását, mivel ezek a funkciók tisztában vannak a folyamat irányával. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="512" title="Virtual Network NAT-folyamat iránya">
</p>

*Ábra: Virtual Network NAT flow iránya*

## <a name="fully-managed-highly-resilient"></a>Teljes körűen felügyelt, nagyon rugalmas

A NAT teljes mértékben ki van bővítve az elejétől. Nincs szükség Felskálázási vagy kibővített műveletre.  Az Azure kezeli a NAT működését.  A NAT-nak mindig több tartalék tartománya van, és a szolgáltatás kimaradása nélkül több hibát is képes fenntartani.

## <a name="tcp-reset-for-unrecognized-flows"></a>Ismeretlen folyamatok TCP-visszaállítása

A NAT privát oldala TCP-átállítási csomagokat küld a nem létező TCP-kapcsolatban lévő kommunikációra tett kísérletekhez. Az egyik példa olyan kapcsolat, amely elérte az üresjárati időkorlátot. A következő fogadott csomag visszaállítja a TCP-visszaállítást a magánhálózati IP-címekre, hogy jelezze és kényszerítse a kapcsolat bezárását.

A NAT nyilvános oldala nem hoz létre TCP-visszaállítási csomagokat vagy bármilyen más forgalmat.  Csak az ügyfél virtuális hálózata által előállított forgalom van kibocsátva.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurálható TCP Üresjárati időkorlát

A rendszer 4 perces alapértelmezett TCP üresjárati időkorlátot használ, és akár 120 percre is növelhető. A folyamat bármely tevékenysége visszaállíthatja az üresjárati időzítőt, beleértve a TCP-Keepalives is.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Területi vagy zónák elkülönítése rendelkezésre állási zónákkal

A NAT alapértelmezés szerint regionális. A [rendelkezésre állási zónák](../availability-zones/az-overview.md) létrehozásakor a NAT el lehet különíteni egy adott zónában (a zóna központi telepítése).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="512" title="NAT Virtual Network rendelkezésre állási zónákkal">
</p>

*Ábra: Virtual Network NAT és rendelkezésre állási zónák*

## <a name="multi-dimensional-metrics-for-observability"></a>Többdimenziós mérőszámok a megfigyeléshez

A NAT működését a Azure Monitorban elérhető többdimenziós metrikák használatával figyelheti. Ezek a metrikák a használat és a hibaelhárítás megtartására használhatók.  A NAT-átjáró erőforrásai a következő metrikákat teszik elérhetővé:
- Bájt
- Csomagok
- Eldobott csomagok
- SNAT-kapcsolatok összesen
- SNAT-kapcsolatok állapotának átállítása időszakonként.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Általánosan elérhető a NAT-adatelérési út legalább 99,9%-os rendelkezésre állású.

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos részletekért tekintse meg a [Virtual Network díjszabását](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="availability"></a>Rendelkezésre állás

Virtual Network NAT és a NAT-átjáró erőforrás az összes Azure-beli felhő [régiójának](https://azure.microsoft.com/global-infrastructure/regions/)összes régiójában elérhető.

## <a name="suggestions"></a>Javaslatok

Szeretnénk tudni, hogyan lehet javítani a szolgáltatást. Javasolja és szavazzon arra, hogy mi a következő lépés a [NAT-UserVoice](https://aka.ms/natuservoice).

## <a name="limitations"></a>Korlátozások

* A NAT kompatibilis a standard SKU nyilvános IP-címmel, a nyilvános IP-előtaggal és a terheléselosztó erőforrásaival. Az alapszintű erőforrások, például az alapszintű Load Balancer és az ezekből származtatott termékek nem kompatibilisek a NAT-val.  Az alapszintű erőforrásokat a NAT-mel nem konfigurált alhálózatra kell helyezni.
* Az IPv4-címek családja támogatott.  A NAT nem támogatja az IPv6-cím családját.  A NAT nem helyezhető üzembe IPv6-előtaggal rendelkező alhálózaton.
* A NAT nem tud több virtuális hálózatot kifogni.

## <a name="next-steps"></a>Következő lépések

* További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
* [Ossza meg velünk a következőt Virtual Network NAT UserVoice-ben való létrehozásához](https://aka.ms/natuservoice).
