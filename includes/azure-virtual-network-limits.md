---
title: fájl belefoglalása
description: fájl belefoglalása
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 44245bc3cd9fd1afcfe9a74d60e2f51135a247ee
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734103"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Hálózati korlátok – Azure Resource Manager
A következő korlátozások csak a **Azure Resource Manager** régiónként felügyelt hálózati erőforrások esetében érvényesek. Megtudhatja, hogyan [tekintheti meg az aktuális erőforrás-használatot az előfizetési korlátok](../articles/networking/check-usage-against-limits.md)alapján.

> [!NOTE]
> A közelmúltban minden alapértelmezett korlátot megnövelt a maximális határértékek között. Ha nincs maximális korlát oszlop, az erőforrás nem rendelkezik állítható korlátokkal. Ha a múltban már a támogatásban is megnőtt a korlát, és a következő táblázatokban nem láthatók a frissített korlátok, [Nyisson meg egy online ügyfélszolgálati kérést díjmentesen](../articles/azure-resource-manager/templates/error-resource-quota.md) .

| Erőforrás | Korlát | 
| --- | --- |
| Virtuális hálózatok |1,000 |
| Alhálózatok száma virtuális hálózatonként |3,000 |
| Virtuális hálózatonkénti virtuális társhálózat létesítések |500 |
| [Virtuális hálózati átjárók (VPN-átjárók) virtuális hálózatonként](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Virtuális hálózati átjárók (ExpressRoute-átjárók) virtuális hálózatonként](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |
| Virtuális hálózatonkénti privát IP-címek |65 536 |
| Magánhálózati IP-címek/hálózati adapterek |256 |
| Magánhálózati IP-címek száma virtuális gépenként |256 |
| Nyilvános IP-címek/hálózati adapter |256 |
| Nyilvános IP-címek virtuális gépenként |256 |
| [Virtuális gép vagy szerepkör-példány hálózati adapterén egyidejű TCP-vagy UDP-forgalom](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) |500 000 |
| Hálózati adapterek |65 536 |
| Network Security Groups (Hálózati biztonsági csoportok) |5000 |
| NSG-ben szereplő NSG-szabályok |1,000 |
| A forráshoz vagy célhoz megadott IP-címek és tartományok egy biztonsági csoportban |4,000 |
| Alkalmazásbiztonsági csoportok |3,000 |
| Alkalmazás biztonsági csoportjai IP-konfiguráció alapján, NIC-ben |20 |
| IP-konfigurációk alkalmazás biztonsági csoportjában |4,000 |
| A hálózati biztonsági csoport összes biztonsági szabályában megadható alkalmazás-biztonsági csoportok |100 |
| Felhasználó által definiált útválasztási táblák |200 |
| Felhasználó által definiált útvonalak útválasztási táblázatban |400 |
| Pont – hely típusú legfelső szintű tanúsítványok Azure-VPN Gateway |20 |
| Virtuális hálózati csapok |100 |
| Hálózati adapter KOPPINTson a konfigurációk virtuális hálózatra KOPPINTva |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Nyilvános IP-címek korlátai
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Nyilvános IP-címek<sup>1</sup> | 10 alapszintű. | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Statikus nyilvános IP-címek<sup>1</sup> | 10 alapszintű. | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Standard nyilvános IP-címek<sup>1</sup> | 10 | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| [Nyilvános IP-címek erőforráscsoport szerint](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md#microsoftnetwork) | 800 | Vegye fel a kapcsolatot az ügyfélszolgálattal. | 
| Nyilvános IP-előtagok | korlátozott a standard nyilvános IP-címek száma egy előfizetésben | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Nyilvános IP-előtag hossza | /28 | Vegye fel a kapcsolatot az ügyfélszolgálattal. |

<sup>1</sup> A nyilvános IP-címekre vonatkozó alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverzió, az utólagos elszámolású, a CSP. A Nagyvállalati Szerződés-előfizetések alapértelmezett értéke például 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Terheléselosztó korlátai
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik. Megtudhatja, hogyan [tekintheti meg az aktuális erőforrás-használatot az előfizetési korlátok](../articles/networking/check-usage-against-limits.md)alapján.

**standard Load Balancer**

| Erőforrás                                | Korlát         |
|-----------------------------------------|-------------------------------|
| Terheléselosztók                          | 1,000                         |
| Szabályok (Load Balancer + bejövő NAT) erőforrás alapján                      | 1500                         |
| Hálózati adapterek szabályai (a hálózati adapter összes IP-címe között) | 300                           |
| Előtér-IP-konfigurációk              | 600                           |
| Háttérbeli készlet mérete                       | 1 000 IP-konfiguráció, egyetlen virtuális hálózat |
| Háttérbeli erőforrások száma Load Balancer <sup> 1<sup> | 250                   |
| Magas rendelkezésre állású portok                 | 1/belső előtér       |
| Kimenő szabályok száma Load Balancer        | 600                           |
| Terheléselosztó virtuális gépenként                   | 2 (1 nyilvános és 1 belső)   |

<sup>1</sup> a korlát akár 150 erőforrást is tartalmaz, az önálló virtuálisgép-erőforrások, a rendelkezésre állási csoport erőforrásai és a virtuálisgép-méretezési csoportok elhelyezésével együtt.

**Alapszintű Load Balancer**

| Erőforrás                                | Korlát        |
|-----------------------------------------|------------------------------|
| Terheléselosztók                          | 1,000                        |
| Szabályok/erőforrás                      | 250                          |
| Hálózati adapterek szabályai (a hálózati adapter összes IP-címe között) | 300                          |
| Előtér-IP-konfigurációk <sup> 2<sup>  | 200                          |
| Háttérbeli készlet mérete                       | 300 IP-konfiguráció, egyetlen rendelkezésre állási csoport |
| Rendelkezésre állási készletek száma Load Balancer     | 1                            |
| Terheléselosztó virtuális gépenként                   | 2 (1 nyilvános és 1 belső)  |

<sup>2</sup> a háttérbeli készletben lévő egyetlen különálló erőforrás korlátja (önálló virtuális gép, rendelkezésre állási csoport vagy virtuálisgép-méretezési csoportok) a 250 előtér-IP-konfigurációval rendelkezhet egyetlen alapszintű nyilvános Load Balancer és alapszintű belső Load Balancer.

<a name="virtual-networking-limits-classic"></a>A következő korlátozások csak a **klasszikus** üzemi modellen keresztül felügyelt hálózati erőforrások esetében érvényesek. Megtudhatja, hogyan [tekintheti meg az aktuális erőforrás-használatot az előfizetési korlátok](../articles/networking/check-usage-against-limits.md)alapján.

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |100 |100 |
| Helyi hálózati helyek |20 |50 |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |20 |
| Virtuális hálózatonkénti privát IP-címek |4 096 |4 096 |
| Virtuális gép vagy szerepkör-példány hálózati adapterén egyidejű TCP-vagy UDP-forgalom |500 000, legfeljebb 1 000 000 kettő vagy több hálózati adapter számára. |500 000, legfeljebb 1 000 000 kettő vagy több hálózati adapter számára. |
| Hálózati biztonsági csoportok (NSG) |200 |200 |
| NSG-ben szereplő NSG-szabályok |200 |1,000 |
| Felhasználó által definiált útválasztási táblák |200 |200 |
| Felhasználó által definiált útvonalak útválasztási táblázatban |400 |400 |
| Nyilvános IP-címek (dinamikus) |500 |500 |
| Fenntartott nyilvános IP-címek |500 |500 |
| Nyilvános IP-cím üzembe helyezése |5 |Kapcsolatfelvétel a támogatási szolgáltatással |
| Magánhálózati IP-cím (belső terheléselosztás) üzembe helyezése |1 |1 |
| Végpontok hozzáférés-vezérlési listái (ACL-ek) |50 |50 |
