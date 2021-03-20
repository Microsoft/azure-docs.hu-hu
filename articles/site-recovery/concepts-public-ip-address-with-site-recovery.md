---
title: Nyilvános IP-címek kiosztása a feladatátvétel után Azure Site Recovery
description: Ismerteti, hogyan lehet nyilvános IP-címeket beállítani a Azure Site Recovery és az Azure Traffic Manager a vész-helyreállítási és-áttelepítéshez
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 01c2f61dcf024e8c9dbbd5b2ee11a479b3c16305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86130286"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Nyilvános IP-címek beállítása a feladatátvétel után

A nyilvános IP-címek lehetővé teszik az internetes erőforrások bejövő kommunikációját az Azure-erőforrásokkal. A nyilvános IP-címek emellett lehetővé teszik az Azure-erőforrások kimenő kommunikációját az internettel és a nyilvános Azure-szolgáltatásokkal, az erőforráshoz rendelt IP-címet használva.
- Az internetről az erőforrás felé irányuló bejövő kommunikáció, például Azure Virtual Machines (VM), Azure Application Gateway, Azure Load Balancer, Azure VPN Gateway és mások. Továbbra is kommunikálhat bizonyos erőforrásokkal, például a virtuális gépekkel, ha a virtuális gép nem rendelkezik nyilvános IP-címmel, feltéve, hogy a virtuális gép egy terheléselosztó háttér-készlet része, és a terheléselosztó egy nyilvános IP-címet kap.
- Az internet felé irányuló kimenő kapcsolat kiszámítható IP-cím használatával. Egy virtuális gép például a hozzá tartozó nyilvános IP-cím nélkül tud kommunikálni az internet felé, de a címe az Azure által fordított hálózati cím, amely alapértelmezés szerint nem előre jelezhető nyilvános cím. Egy nyilvános IP-cím erőforráshoz való hozzárendelésével megtudhatja, melyik IP-címet használja a kimenő kapcsolat. A megadott hozzárendelési módszertől függően előfordulhat, hogy a címe megváltozhat. További információt a [nyilvános IP-cím létrehozása](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)című témakörben talál. Ha többet szeretne megtudni az Azure-erőforrások kimenő kapcsolatairól, tekintse meg a [Kimenő kapcsolatok ismertetése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.

Azure Resource Manager a nyilvános IP-cím olyan erőforrás, amely saját tulajdonságokkal rendelkezik. A nyilvános IP-cím-erőforrást például a következő erőforrásokhoz rendelheti hozzá:

* Virtuális gépek hálózati adapterei
* Internetkapcsolattal rendelkező terheléselosztók
* VPN-átjárók
* Alkalmazásátjárók

Ez a cikk azt ismerteti, hogyan használhatók a nyilvános IP-címek Site Recovery használatával.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Nyilvános IP-cím hozzárendelése helyreállítási terv használatával

Az üzemi alkalmazás nyilvános IP-címe **nem tartható fenn feladatátvételkor**. A feladatátvételi folyamat részeként indított munkaterhelésekhez hozzá kell rendelni egy Azure nyilvános IP-erőforrást a céltartományban. Ezt a lépést manuálisan vagy a helyreállítási tervekkel automatizáltan is elvégezheti. A helyreállítási terv helyreállítási csoportokba gyűjti a gépeket. Segít egy rendszeres helyreállítási folyamat definiálásában. A helyreállítási terv segítségével megadhatja a sorrendet, és automatizálhatja az egyes lépésekhez szükséges műveleteket, Azure Automation runbookok használatával végezheti el a feladatátvételt az Azure-ba vagy a parancsfájlokba.

A telepítés a következő:
- Hozzon létre egy [helyreállítási tervet](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) , és szükség szerint csoportosítsa a számítási feladatokat a tervbe.
- Szabja testre a tervet egy olyan lépés hozzáadásával, amely egy nyilvános IP-címet csatol [Azure Automation runbookok](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) parancsfájlokkal a feladatátvételt használó virtuális géphez.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Nyilvános végponti váltás DNS-szintű útválasztással

Az Azure Traffic Manager lehetővé teszi a DNS-szintű útválasztást a végpontok között, és segít a DR-forgatókönyvek [RTOs levezetésében](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) . 

További információ a feladatátvételi forgatókönyvekről Traffic Manager:
1. Helyszíniről [Azure-ba történő feladatátvétel](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) Traffic Manager 
2. [Azure-ról Azure-ba történő feladatátvétel](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) Traffic Manager 

A telepítés a következő:
- Hozzon létre egy [Traffic Manager profilt](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- A **prioritási** útválasztási módszer kihasználása esetén hozzon létre két végpontot – **elsődleges** forrásként és **feladatátvételt** az Azure-hoz. Az **elsődleges** hozzárendelés 1. prioritású, a **feladatátvétel** pedig 2. prioritású.
- Az **elsődleges** végpont lehet az [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) vagy a [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) , attól függően, hogy a forrás-környezet az Azure-on belül vagy kívül van-e.
- A **feladatátvételi** végpont **Azure** -végpontként jön létre. **Statikus nyilvános IP-címet** használjon, mivel ez a katasztrófa-esemény Traffic Manager külső végpontja lesz.

## <a name="next-steps"></a>Következő lépések
- További információ a [Traffic Managerról Azure site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- További információ a Traffic Manager [útválasztási módszerekről](../traffic-manager/traffic-manager-routing-methods.md).
- További információ az alkalmazások feladatátvételének automatizálására szolgáló [helyreállítási tervekről](site-recovery-create-recovery-plans.md) .
