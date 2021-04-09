---
title: Gyakran ismételt kérdések (GYIK) az Azure Network Watcherról | Microsoft Docs
description: Ez a cikk az Azure Network Watcher szolgáltatással kapcsolatos gyakori kérdésekre ad választ.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: e7585880b98f62f819ff344c82846c2cfb1fd620
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019822"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Gyakran ismételt kérdések (GYIK) az Azure Network Watcher
Az [azure Network Watcher](./network-watcher-monitoring-overview.md) szolgáltatás olyan eszközöket biztosít, amelyekkel figyelheti, diagnosztizálhatja és megtekintheti a metrikákat, valamint engedélyezheti vagy letilthatja az Azure-beli virtuális hálózatok erőforrásaihoz tartozó naplókat. Ez a cikk a szolgáltatással kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános kérdések

### <a name="what-is-network-watcher"></a>Mi az a Network Watcher?
Network Watcher úgy van kialakítva, hogy figyelje és javítsa a IaaS (infrastruktúra-szolgáltatás) összetevőinek hálózati állapotát, beleértve a Virtual Machines, a virtuális hálózatokat, az alkalmazás-átjárókat, a terheléselosztó és az Azure-beli virtuális hálózatok egyéb erőforrásait. Nem megoldás a Péter (platform-szolgáltatás) infrastruktúrájának figyelésére, vagy a web/mobil Analitika beszerzésére.

### <a name="what-tools-does-network-watcher-provide"></a>Milyen eszközöket biztosít a Network Watcher?
Network Watcher a képességek három fő készletét biztosítja
* Figyelés
  * A [topológia nézetben](./view-network-topology.md) a virtuális hálózat erőforrásai és a köztük lévő kapcsolatok láthatók.
  * A [kapcsolat figyelője](./connection-monitor.md) lehetővé teszi a virtuális gép és egy másik hálózati erőforrás közötti kapcsolat és késés figyelését.
  * A [Network Performance monitor](../azure-monitor/insights/network-performance-monitor.md) lehetővé teszi a kapcsolatok és a késések figyelését a hibrid hálózati architektúrák, a Expressroute áramkörök és a szolgáltatási/alkalmazási végpontok között.  
* Diagnosztika
  * Az [IP-forgalom ellenőrzése](./network-watcher-ip-flow-verify-overview.md) lehetővé teszi, hogy a virtuális gép szintjén észlelje a forgalom szűrésével kapcsolatos problémákat.
  * A [következő ugrás](./network-watcher-next-hop-overview.md) segítséget nyújt a forgalmi útvonalak ellenőrzésében és az útválasztási problémák észlelésében.
  * A [kapcsolati hibakeresés](./network-watcher-connectivity-portal.md) lehetővé teszi a virtuális gép és egy másik hálózati erőforrás közötti egyszeri kapcsolódást és késést.
  * A [csomagok rögzítése](./network-watcher-packet-capture-overview.md) lehetővé teszi a virtuális hálózatban lévő virtuális gépek forgalmának rögzítését.
  * A [VPN-hibaelhárítás](./network-watcher-troubleshoot-overview.md) több diagnosztikai ellenőrzést futtat a VPN-átjárón és a kapcsolatokon a hibák elhárítása érdekében.
* Naplózás
  * A [NSG flow-naplók](./network-watcher-nsg-flow-logging-overview.md) lehetővé teszik a [hálózati biztonsági csoportok (NSG)](../virtual-network/network-security-groups-overview.md) összes forgalmának naplózását
  * [Traffic Analytics](./traffic-analytics.md) dolgozza fel a NSG flow-napló adatait, így megjelenítheti, lekérdezheti, elemezheti és értelmezheti a hálózati forgalmat.


További részletekért tekintse meg az [Network Watcher áttekintése lapot](./network-watcher-monitoring-overview.md).


### <a name="how-does-network-watcher-pricing-work"></a>Hogyan működik a Network Watcher díjszabása?
Látogasson el a Network Watcher-összetevők [díjszabási oldalára](https://azure.microsoft.com/pricing/details/network-watcher/) , valamint azok díjszabására.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Mely régiók Network Watcher támogatott/elérhető?
Az [Azure szolgáltatás rendelkezésre állási lapján](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) megtekintheti a legújabb regionális elérhetőséget.

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Milyen engedélyekre van szükség a Network Watcher használatához?
Tekintse meg a [Network Watcher használatához szükséges Azure RBAC-engedélyek](./required-rbac-permissions.md)listáját. Erőforrások üzembe helyezéséhez közreműködői engedélyekkel kell rendelkeznie a NetworkWatcherRG (lásd alább).

### <a name="how-do-i-enable-network-watcher"></a>Hogyan engedélyezhetem a Network Watchert?
A Network Watcher szolgáltatás minden előfizetéshez [automatikusan engedélyezve](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) van.

### <a name="what-is-the-network-watcher-deployment-model"></a>Mi a Network Watcher üzembe helyezési modell?
A Network Watcher szülő erőforrás minden régióban egyedi példánnyal van üzembe helyezve. Elnevezési formátum: NetworkWatcher_RegionName. Példa: a NetworkWatcher_centralus a "Central US" régió Network Watcher erőforrása.

### <a name="what-is-the-networkwatcherrg"></a>Mi a NetworkWatcherRG?
Network Watcher erőforrások a rejtett **NetworkWatcherRG** -erőforráscsoport alatt találhatók, amely automatikusan létrejön. Például a NSG flow-napló erőforrás a Network Watcher gyermek erőforrása, és engedélyezve van a NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Miért kell telepíteni a Network Watcher bővítményt? 
A Network Watcher-bővítményt minden olyan szolgáltatáshoz meg kell adni, amely egy virtuális gépről érkező forgalmat kell előállítani vagy feltartóztatni. 

### <a name="which-features-require-the-network-watcher-extension"></a>Mely szolgáltatásokhoz szükséges a Network Watcher bővítmény?
A csomagok rögzítése, a kapcsolati hibák és a kapcsolati figyelő funkcióinak jelen kell lennie a Network Watcher bővítménynek.

### <a name="what-are-resource-limits-on-network-watcher"></a>Mik a Network Watcher erőforrás-korlátai?
Tekintse meg az összes korlátot a [szolgáltatás korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits) lapon.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Miért csak egyetlen példányban engedélyezett Network Watcher régiónként? 
Network Watcher csak egyszer kell engedélyezni ahhoz, hogy az előfizetése működjön, ez a szolgáltatás nem megengedett.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Hogyan kezelhetem a Network Watcher erőforrást? 
A Network Watcher erőforrás a Network Watcher háttér-szolgáltatást jelöli, és teljes mértékben az Azure felügyeli. Az ügyfeleknek nem kell kezelnie. Az olyan műveletek, mint az áthelyezés, nem támogatottak az erőforráson. [Az erőforrás azonban törölhető is](./network-watcher-create.md#delete-a-network-watcher-in-the-portal). 

## <a name="service-availability-and-redundancy"></a>Szolgáltatás rendelkezésre állása és redundancia 

### <a name="is-the-network-watcher-service-zone-resilient"></a>Rugalmas a Network Watcher szolgáltatási zóna? 
Igen. A Network Watcher szolgáltatás alapértelmezés szerint rugalmas zónában van. 

### <a name="how-do-i-configure-the-network-watcher-service-to-be-zone-resilient"></a>Hogyan konfigurálja a Network Watcher szolgáltatást zónákra rugalmasan? 
A zóna rugalmasságának engedélyezéséhez nincs szükség ügyfél-konfigurációra. A Network Watcher erőforrások rugalmassága alapértelmezés szerint elérhető, és maga a szolgáltatás kezeli. 

## <a name="nsg-flow-logs"></a>NSG

### <a name="what-does-nsg-flow-logs-do"></a>Mit jelentenek a NSG flow-naplók?
Az Azure hálózati erőforrásait [hálózati biztonsági csoportok (NSG-EK)](../virtual-network/network-security-groups-overview.md)segítségével lehet egyesíteni és felügyelni. A NSG flow-naplók lehetővé teszik az 5 rekordos adatfolyamok naplózását a NSG keresztüli összes forgalomról. A nyers flow-naplók egy Azure Storage-fiókba íródnak, ahonnan szükség szerint további feldolgozásra, elemzésre, lekérdezésre vagy exportálásra kerülhet sor.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Hogyan használja a NSG a tűzfal mögötti Storage-fiókkal?

Ha tűzfal mögötti Storage-fiókot szeretne használni, meg kell adnia egy kivételt a megbízható Microsoft-szolgáltatások számára a Storage-fiókhoz való hozzáféréshez:

* Navigáljon a Storage-fiókhoz úgy, hogy beírja a Storage-fiók nevét a portálon vagy a [Storage-fiókok lapon](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) található globális Keresés mezőbe.
* A **BEÁLLÍTÁSOK** szakaszban válassza a **Tűzfalak és virtuális hálózatok** elemet
* A "hozzáférés engedélyezése innen" területen válassza a **kiválasztott hálózatok** elemet. Ezután a **kivételek** alatt jelölje be a **"megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiókhoz"** jelölőnégyzetet. 
* Ha ez a beállítás már ki lett választva, nincs szükség módosításra.  
* Keresse meg a cél NSG a [NSG flow naplói – áttekintés oldalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) , és engedélyezze a NSG-folyamat naplóit a fenti Storage-fiók kiválasztásával.

Pár perc elteltével ellenőrizheti a tárnaplókat – egy frissített időbélyeget vagy egy újonnan létrehozott JSON-fájlt kell látnia.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Hogyan használja a NSG a szolgáltatási végpont mögötti Storage-fiókkal?

A NSG-folyamatok további konfigurációk nélkül kompatibilisek a szolgáltatási végpontokkal. Tekintse meg a szolgáltatás-végpontok virtuális hálózatban való [engedélyezésével foglalkozó oktatóanyagot](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) .


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Mi a különbség a flow-naplók között 1 & 2 verzió között?
A flow-naplók 2. verziója bevezeti a *folyamat állapotának* fogalmát & tárolja a bájtok és a továbbított csomagok adatait. [További információk](./network-watcher-nsg-flow-logging-overview.md#log-format).

## <a name="next-steps"></a>Következő lépések
 - Néhány oktatóanyagért tekintse át a [dokumentáció áttekintését ismertető oldalt](./index.yml) , amellyel elsajátíthatja a Network Watcher.