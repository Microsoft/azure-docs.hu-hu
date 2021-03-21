---
title: Összetevők Azure Load Balancer
description: Azure Load Balancer-összetevők áttekintése
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: 6bf090cde7262fdae9c98ef55227bf2925937dbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739831"
---
# <a name="azure-load-balancer-components"></a>Összetevők Azure Load Balancer

Azure Load Balancer tartalmaz néhány kulcsfontosságú összetevőt. Ezek az összetevők az előfizetésen keresztül konfigurálhatók az alábbi módokon:

* Azure Portal
* Azure CLI
* Azure PowerShell
* Resource Manager-sablonok

## <a name="frontend-ip-configuration"></a>Előtér-IP-konfiguráció <a name = "frontend-ip-configurations"></a>

A Azure Load Balancer IP-címe. Ez a kapcsolódási pont az ügyfelek számára. Ezek az IP-címek a következők lehetnek:

- **Nyilvános IP-cím**
- **Magánhálózati IP-cím**

Az IP-cím természete határozza meg, hogy milyen **típusú** terheléselosztó lett létrehozva. A magánhálózati IP-címek kiválasztása létrehoz egy belső terheléselosztó. A nyilvános IP-cím kiválasztása nyilvános Load balancert hoz létre.

|  | Nyilvános Load Balancer  | Belső terheléselosztó |
| ---------- | ---------- | ---------- |
| **Előtér-IP-konfiguráció**| Nyilvános IP-cím | Magánhálózati IP-cím|
| **Leírás** | A nyilvános terheléselosztó a nyilvános IP-címet és a bejövő forgalom portját a virtuális gép magánhálózati IP-címére és portjára képezi le. A Load Balancer a virtuális gépről érkező válasz felé irányuló forgalmat is leképezi. A terheléselosztási szabályok alkalmazásával adott típusú adatforgalom több virtuális gépre vagy szolgáltatásba is terjeszthető. A webkérések adatforgalmát például eloszthatja több webkiszolgáló között.| A belső terheléselosztó a virtuális hálózaton belüli erőforrásokra osztja el a forgalmat. Az Azure korlátozza a hozzáférést egy elosztott virtuális hálózat előtér-IP-címeihez. Az előtér-IP-címeket és a virtuális hálózatokat a rendszer soha nem teszi elérhetővé közvetlenül az internetes végpontok számára. A belső üzletági alkalmazások az Azure-ban futnak, és csak az Azure-ból vagy a helyszíni erőforrásokból érhetők el. |
| **Támogatott SKU-i** | Alapszintű, standard | Alapszintű, standard |

![Rétegű terheléselosztó – példa](./media/load-balancer-overview/load-balancer.png)

A Load Balancer több előtér-IP-címmel is rendelkezhet. További információ a [több előtérbeli felületről](load-balancer-multivip-overview.md).

## <a name="backend-pool"></a>A háttérkészlet

Egy virtuálisgép-méretezési csoportban lévő virtuális gépek vagy példányok csoportja, amely a beérkező kérést szolgálja. Ahhoz, hogy a költséghatékonyan megfeleljen a nagy mennyiségű bejövő forgalomnak, a számítástechnikai irányelvek általában azt ajánlják, hogy további példányokat vegyen fel a háttér-készletbe.

A Load Balancer azonnal újrakonfigurálja magát az automatikus újrakonfigurálással, ha feljebb vagy lejjebb méretezi a példányokat. Ha virtuális gépeket ad hozzá vagy távolít el a háttér-készletből, a terheléselosztó további műveletek nélkül újrakonfigurálja a terheléselosztó-t. A háttér-készlet hatóköre a virtuális hálózatban található bármely virtuális gép.

A háttérbeli készlet kialakításának megtervezése során tervezze meg a minimális számú egyéni háttér-készlet erőforrást a felügyeleti műveletek hosszának optimalizálása érdekében. Nincs különbség az adatsíkok teljesítményében vagy méretezésében.

## <a name="health-probes"></a>Állapotminták

Az állapot-mintavétel a háttér-készletben lévő példányok állapotának meghatározására szolgál. A terheléselosztó létrehozása során állítson be egy állapot-mintavételt a terheléselosztó számára a használathoz.  Ez az állapot-mintavétel megállapítja, hogy egy példány állapota Kifogástalan-e, és képes-e a forgalom fogadására.

Megadhatja az állapot-mintavételek nem megfelelő állapotát. Ha egy mintavétel nem válaszol, a terheléselosztó leállítja az új kapcsolatok küldését a nem kifogástalan állapotú példányokra. A mintavételi hiba nem érinti A meglévő kapcsolatokat. A kapcsolatok addig folytatódnak, amíg az alkalmazás:

- A folyamat vége
- Tétlen időtúllépés történik
- A virtuális gép leáll

A Load Balancer különböző állapot-mintavételi típusokat biztosít a végpontokhoz: TCP, HTTP és HTTPS. [További információ a Load Balancer Health](load-balancer-custom-probe-overview.md)-mintavételről.

Az alapszintű Load Balancer nem támogatja a HTTPS-próbákat. Az alapszintű Load Balancer minden TCP-kapcsolatot bezár (beleértve a létesített kapcsolatokat is).

## <a name="load-balancing-rules"></a>Terheléselosztási szabályok

Egy terheléselosztó-szabály segítségével határozható meg, hogy a rendszer hogyan ossza el a bejövő forgalmat a háttér-készleten belüli **összes** példányra. Egy terheléselosztási szabály egy adott előtérbeli IP-konfigurációt és portot képez le több háttérbeli IP-cím és port számára.

Használjon például egy terheléselosztási szabályt az 80-as porthoz, hogy átirányítsa a forgalmat a felületi IP-címről a háttérbeli példányok 80-es portjára.

:::image type="content" source="./media/load-balancer-components/lbrules.png" alt-text="Terheléselosztó szabályának hivatkozási diagramja" border="false":::

*Ábra: Load-Balancing szabályok*

## <a name="high-availability-ports"></a>Magas rendelkezésre állású portok

Egy **"protokoll – mind és port – 0"** konfigurált terheléselosztó-szabály. 

Ez a szabály lehetővé teszi egyetlen szabály számára, hogy betöltse a belső standard Load Balancer összes portjára érkező TCP-és UDP-folyamatokat. 

A terheléselosztási döntés egy folyamaton alapul. Ez a művelet a következő öt rekordos kapcsolaton alapul: 

1. 
    forrás IP-címe
  
2. forrásport
3. cél IP-címe
4. célport
5. protokoll

A HA-portok terheléselosztási szabályai segítenek a kritikus forgatókönyvek, például a magas rendelkezésre állás és a virtuális hálózatokon belüli hálózati virtuális berendezések (NVA-EK) méretezése terén. A funkció segíthet, ha nagy számú portot kell terheléselosztással elválasztani.

<p align="center">
  <img src="./media/load-balancer-components/harules.svg" alt="Figure depicts how Azure Load Balancer directs all frontend ports to three instances of all backend ports" width="512" title="HA portok szabályai">
</p>

*Ábra: HA portok szabályai*

További információ a [Ha portokról](load-balancer-ha-ports-overview.md).

## <a name="inbound-nat-rules"></a>Bejövő NAT-szabályok

A bejövő NAT-szabály továbbítja a bejövő forgalmat az IP-cím és a port kombinációja számára. A rendszer elküldje a forgalmat egy **adott** virtuális gépre vagy példányra a háttér-készletben. A portok továbbítása a terheléselosztással megegyező kivonatoló alapú eloszlással történik.

:::image type="content" source="./media/load-balancer-components/inboundnatrules.png" alt-text="Bejövő NAT-szabály hivatkozási diagramja" border="false":::

*Ábra: bejövő NAT-szabályok*

A bejövő NAT-szabályok Virtual Machine Scale Sets környezetében a bejövő NAT-készletek. További információ a [Load Balancer-összetevőkről és a virtuálisgép-méretezési csoportról](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer).

## <a name="outbound-rules"></a>Kimenő szabályok

Egy kimenő szabály konfigurálja a kimenő hálózati címfordítást (NAT) a háttér-készlet által azonosított összes virtuális géphez vagy példányhoz. Ez a szabály lehetővé teszi, hogy a háttérbeli példányok kommunikáljanak (kimenő) az interneten vagy más végpontokon.

További információ a [kimenő kapcsolatokról és a szabályokról](load-balancer-outbound-connections.md).

Az alapszintű Load Balancer nem támogatja a kimenő szabályokat.

:::image type="content" source="./media/load-balancer-components/outbound-rules.png" alt-text="Kimenő szabály hivatkozási diagramja" border="false":::

*Ábra: kimenő szabályok*

## <a name="limitations"></a>Korlátozások

- A Load Balancer [korlátainak](../azure-resource-manager/management/azure-subscription-service-limits.md) megismerése 
- A Load Balancer terheléselosztást és port továbbítást biztosít bizonyos TCP-vagy UDP-protokollokhoz. A terheléselosztási szabályok és a bejövő NAT-szabályok támogatják a TCP és az UDP használatát, de nem más IP-protokollok, beleértve az ICMP-t is.
- A háttérbeli virtuális gép kimenő folyamata egy belső Load Balancer felületére sikertelen lesz.
- Egy terheléselosztó-szabály nem terjedhet ki két virtuális hálózatra.  A frontendeknek és a háttérbeli példányoknak ugyanabban a virtuális hálózatban kell lenniük.  
- A továbbítási IP-töredékek nem támogatottak a terheléselosztási szabályokban. Az UDP-és TCP-csomagok IP-töredezettsége nem támogatott a terheléselosztási szabályokban. HA portok terheléselosztási szabályai használhatók a meglévő IP-töredékek továbbítására. További információ: [magas rendelkezésre állású portok – áttekintés](load-balancer-ha-ports-overview.md).
- Rendelkezésre állási csoportokban csak 1 nyilvános Load Balancer és 1 belső Load Balancer tartozhat

## <a name="next-steps"></a>Következő lépések

- A Load Balancer használatának megkezdéséhez tekintse meg [a nyilvános standard Load Balancer létrehozása](quickstart-load-balancer-standard-public-portal.md) című témakört.
- További információ a [Azure Load Balancerról](load-balancer-overview.md).
- Tudnivalók a [nyilvános IP-címekről](../virtual-network/virtual-network-public-ip-address.md)
- További információ a [magánhálózati IP-címekről](../virtual-network/private-ip-addresses.md)
- Tudnivalók a [standard Load Balancer és a Availability Zones](load-balancer-standard-availability-zones.md)használatáról.
- Ismerje meg a [standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md).
- További információ [a TCP alaphelyzetbe állításáról üresjáratban](load-balancer-tcp-reset.md).
- [A standard Load Balancer és a ha portok terheléselosztási szabályainak](load-balancer-ha-ports-overview.md)megismerése.
- További információ a [hálózati biztonsági csoportokról](../virtual-network/network-security-groups-overview.md).
- További információ a [Load Balancer korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer).
- Tudnivalók a [portok továbbításának](./tutorial-load-balancer-port-forwarding-portal.md)használatáról.
