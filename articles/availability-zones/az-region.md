---
title: A rendelkezésreállási zónákat támogató Azure-szolgáltatások
description: Magas rendelkezésre állékonyságú és rugalmas Azure-beli alkalmazások létrehozásához Availability Zones az erőforrások futtatásához fizikailag elkülönített helyeket biztosít.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/21/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 4c592c2d67df1e792200cc36449a6268807bbb56
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816259"
---
# <a name="azure-services-that-support-availability-zones"></a>A rendelkezésreállási zónákat támogató Azure-szolgáltatások

Microsoft Azure infrastruktúra kialakítása és kialakítása minden rétegben úgy lett kialakítva és felépítve, hogy a legmagasabb szintű redundanciát és rugalmasságot nyújtsa az ügyfeleinek. Az Azure-infrastruktúra földrajzi területekből, régiókból és Availability Zones áll, amelyek korlátozzák a meghibásodások körének sugarát, és ezáltal korlátozzák az ügyfélalkalmazások és -adatok lehetséges hatását. Az Azure Availability Zones szerkezet úgy lett kifejlesztve, hogy egy szoftveres és hálózati megoldást biztosítson az adatközpontok meghibásodása elleni védelemhez, valamint az ügyfelek számára magasabb szintű magas rendelkezésre állás (HA) érdekében.

A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden zóna egy vagy több, független áramellátással, hűtéssel és hálózattal is felépítésű adatközpontból áll. A régión belüli Availability Zones fizikai elkülönítése korlátozza az alkalmazásokra és adatokra gyakorolt hatást a zónahibák, például a nagy léptékű elárasztás, a nagyobb viharok és szuperviharok, valamint az egyéb olyan események esetén, amelyek megzavarhatják a webhelyhez való hozzáférést, a biztonságos áthaladást, a kiterjesztett segédprogramok üzemideját és az erőforrások rendelkezésre állását. Availability Zones és az ezekhez kapcsolódó adatközpontok úgy vannak kialakítva, hogy ha az egyik zónát feltörik, a szolgáltatásokat, a kapacitást és a rendelkezésre állást a régióban Availability Zones többi szolgáltatás is támogatja.

Minden Azure felügyeleti szolgáltatás úgy van tervezve, hogy rugalmas legyen a régiószintű hibáktól. A meghibásodások spektrumában egy régión belül egy vagy több rendelkezésre állási zóna meghibásodása kisebb hibakörtű, mint egy teljes régió meghibásodása. Az Azure helyre tud állni egy régión belüli felügyeleti szolgáltatások zónaszintű meghibásodása esetén. Az Azure a régión belül egyes zónákban végez kritikus karbantartást, hogy megakadályozza a régión belül üzembe helyezett Availability Zones erőforrásokkal kapcsolatos hibákat.


![egy 3 zónával található Azure-régió fogalmi nézete](./media/az-region/azure-region-availability-zones.png)


A Availability Zones **azure-szolgáltatások** három kategóriába sorolhatók: **zóna,** zónaredundáns és nem **regionális szolgáltatások.** Az ügyfelek számítási feladatai kategorizálhatóak ezen architektúra-forgatókönyvek bármelyikének kihasználásához az alkalmazás teljesítményének és tartósságának érdekében.

- **Zónaszolgáltatások** – Az erőforrások üzembe helyezhetők egy adott, önki kiválasztott rendelkezésre állási zónában a szigorúbb késési vagy teljesítménybeli követelmények elérése érdekében.  A rugalmasság úgy lett tervezve, hogy az alkalmazásokat és adatokat egy vagy több zónába replikálja a régión belül.  Az erőforrások egy adott zónához rögzítve vannak. Például a virtuális gépek, felügyelt lemezek vagy standard IP-címek egy adott zónához rögzíthatók, ami nagyobb rugalmasságot biztosít azáltal, hogy az erőforrások egy vagy több példánya több zónában van elterjedve.

- **Zónaredundáns szolgáltatások** – Az erőforrások replikálása vagy elosztása automatikusan történik a zónák között. A ZRS például három zónában replikálja az adatokat, hogy egy zónahiba ne befolyásolja az adatok hatékonyát.  

- **Nem regionális szolgáltatások** – A szolgáltatások mindig elérhetők az Azure földrajzi régióiból, és ellenállnak a zóna szintű kimaradásnak és a régióra kiterjedő kimaradásnak. 


Ha átfogó üzletmenet-folytonosságot érhet el az Azure-ban, az alkalmazásarchitektúrát az Azure Availability Zones párok együttes használatával építse ki. Az azure-régiókon belüli Availability Zones használatával szinkron módon replikálhatja az alkalmazásokat és adatokat a magas rendelkezésre állás és aszinkron replikálás érdekében az Azure-régiók között a vészhelyreállítás védelme érdekében. További tudnivalókért olvassa el a magas rendelkezésre állású megoldások [a](/azure/architecture/high-availability/building-solutions-for-high-availability)Availability Zones. 

## <a name="azure-services-supporting-availability-zones"></a>A szolgáltatást támogató Azure-Availability Zones

 - A régebbi generációs virtuális gépek nem szerepelnek a listán. További információ: [Virtuálisgép-méretek előző generációi.](../virtual-machines/sizes-previous-gen.md)
 - Ahogy az [Azure-ban a Régiók és Availability Zones](az-overview.md)is említettük, egyes szolgáltatások nem regionálisak. Ezek a szolgáltatások nem függnek egy adott Azure-régiótól, így rugalmasak a zóna egészére kiterjedő kimaradásokkal és a régióra kiterjedő kimaradásokkal szemben.  A nem regionális szolgáltatások listája a régiónként elérhető [termékek között található.](https://azure.microsoft.com/global-infrastructure/services/)


## <a name="azure-regions-with-availability-zones"></a>Azure-régiók Availability Zones
 

| Amerika           | Európa               | Afrika              | Ázsia és a Csendes-óceáni térség   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Dél-Brazília       | Közép-Franciaország       | Dél-Afrika északi részén* | Kelet-Ausztrália |
| Közép-Kanada     | Nyugat-Németország – Középső régió |                     | Közép-India* |
| Az USA középső régiója         | Észak-Európa         |                     | Kelet-Japán     |
| USA keleti régiója            | Az Egyesült Királyság déli régiója             |                     | Dél-Korea középső középső országa* |
| USA 2. keleti régiója          | Nyugat-Európa          |                     | Délkelet-Ázsia |
| USA déli középső sarka |                      |                     |                |
| USA-beli államigazgatás – Virginia    |                      |                     |                |
| USA 2. nyugati régiója        |                      |                     |                |
| USA 3. nyugati régiója*       |                      |                     |                |

\* Ha többet szeretne megtudni az Availability Zones és az elérhető szolgáltatások támogatásával kapcsolatban ezekben a régiókban, lépjen kapcsolatba a Microsoft értékesítési vagy ügyfél-képviselőjével. Az azure-beli földrajzi helyeket Availability Zones [régiókat.](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)


## <a name="azure-services-supporting-availability-zones"></a>Az azure-szolgáltatásokat támogató Availability Zones

- A régebbi generációs virtuális gépek nem szerepelnek az alábbi listában. További információkért lásd a [virtuálisgép-méretek előző generációit.](../virtual-machines/sizes-previous-gen.md)

- Egyes szolgáltatások nem regionálisak. További információ: [Régiók és Availability Zones Azure-ban.](az-overview.md) Ezek a szolgáltatások nem függnek egy adott Azure-régiótól, így rugalmasak a zóna egészére kiterjedő kimaradásokkal és a régióra kiterjedő kimaradásokkal szemben.  A nem regionális szolgáltatások listája a régiónként elérhető [termékek között található.](https://azure.microsoft.com/global-infrastructure/services/)


### <a name="zone-resilient-services"></a>Zónaredőképes szolgáltatások 

:globe_with_meridians: Nem regionális szolgáltatások – A szolgáltatások mindig elérhetők az Azure földrajzi helyről, és ellenállnak a zóna egészére kiterjedő kimaradásokkal és a régióra kiterjedő szolgáltatáskimaradásokkal szemben.

:large_blue_diamond: Rugalmas a zóna egészét kieső kimaradásokkal szemben 

**Alapszolgáltatások**

|     Termékek                                                    | Rugalmasság             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Application Gateway (V2)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)                                  | :large_blue_diamond:  |
|     [Azure Backup](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)                             | :large_blue_diamond:  |
|     [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)                                       | :large_blue_diamond:  |
|     [Azure nyilvános IP-cím](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)                                           | :large_blue_diamond:  |
|     Azure SQL Database ([általános célú réteg](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))                 | :large_blue_diamond:  |
|     Azure SQL Database([Prémium & üzletileg kritikus szint](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))     | :large_blue_diamond:  |
|     [Disk Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                                | :large_blue_diamond:  |
|     [Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)                                                  | :large_blue_diamond:  |
|     [Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)                                                   | :large_blue_diamond:  |
|     [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)                                               | :large_blue_diamond:  |
|     [Szolgáltatásbusz](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-geo-dr#availability-zones)                                                 | :large_blue_diamond:  |
|     [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications)                                            | :large_blue_diamond:  |
|     [Tárfiók](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                           | :large_blue_diamond:  |
|     Tárolás:   [A Blob Storage/Blob Storage rétegei](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                      | :large_blue_diamond:  |
|     Storage:   [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)                                    | :large_blue_diamond:  |
|     [Virtual Machines méretezési készletek](https://docs.microsoft.com/azure/virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set)                               | :large_blue_diamond:  |
|     [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                          | :large_blue_diamond:  |
|     Virtual Machines: [Av2 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Bs-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [DSv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [DSv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                            | :large_blue_diamond:  |
|     Virtual Machines: [Dv2-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [Dv3-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [ESv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [Ev3-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [F-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [FS-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#make-your-images-highly-available) | :large_blue_diamond:  |
|     [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway)                                         | :large_blue_diamond:  |
|     [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                                             | :large_blue_diamond:  |


**Alapvető szolgáltatások**


|     Termékek                                                    | Rugalmasság             |
|-----------------------------------------------------------------|:----------------------------:|
|     [App Service-környezetek](https://docs.microsoft.com/azure/app-service/environment/zone-redundancy)                                    | :large_blue_diamond:  |
|     [Azure Active Directory tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/overview)                      | :large_blue_diamond:  |
|     [Azure API Management](https://docs.microsoft.com/azure/api-management/zone-redundancy)                      | :large_blue_diamond:  |
|     [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)                                               | :large_blue_diamond:  |
|     [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-high-availability)                              | :large_blue_diamond:  |
|     [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-performance-optimization#availability-zones)               | :large_blue_diamond:  |
|     Azure Cognitive Services: [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)                    | :large_blue_diamond:  |
|     [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     Azure Database for MySQL – [Rugalmas kiszolgáló](https://docs.microsoft.com/azure/mysql/flexible-server/concepts-high-availability)                  | :large_blue_diamond:  |
|     Azure Database for PostgreSQL – [Rugalmas kiszolgáló](https://docs.microsoft.com/azure/postgresql/flexible-server/overview)             | :large_blue_diamond:  |
|     [Azure DDoS Protection](https://docs.microsoft.com/azure/ddos-protection/ddos-faq)                                       | :large_blue_diamond:  |
|     [Azure Disk Encryption](https://docs.microsoft.com/azure/virtual-machines/disks-redundancy)                                       | :large_blue_diamond:  |
|     [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                                              | :large_blue_diamond:  |
|     [Azure Firewall Manager](https://docs.microsoft.com/azure/firewall-manager/quick-firewall-policy)                                      | :large_blue_diamond:  |
|     [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones)                              | :large_blue_diamond:  |
|     [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery)                                         | :large_blue_diamond:  |
|     Azure SQL: [Virtuális gép](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                                  | :large_blue_diamond:  |
|     [Azure Web Application Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                              | :large_blue_diamond:  |
|     [Container Registry](https://docs.microsoft.com/azure/container-registry/zone-redundancy)                                          | :large_blue_diamond:  |
|     [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)                                                  | :large_blue_diamond:  |
|     [Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | :large_blue_diamond:  |
|     Network Watcher: [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | :large_blue_diamond:  |
|     [Power BI Embedded](https://docs.microsoft.com/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | :large_blue_diamond:  |
|     [Prémium Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-performance-tiers#:~:text=Table%201%20%20%20%20Area%20%20,%20%20Currently%20supports%20only%20locally-redundan%20...%20)                                        | :large_blue_diamond:  |
|     Tárolás: [Azure Premium Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning)                                | :large_blue_diamond:  |
|     Virtual Machines: [Azure Dedicated Host](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                     | :large_blue_diamond:  |
|     Virtual Machines: [Ddsv4 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Ddv4 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dsv4 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dv4-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [Edsv4 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Edv4 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Esv4 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Ev4 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [Fsv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: M [sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                  | :large_blue_diamond:  |
|     [Virtuális WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     Virtual WAN: [ExpressRoute](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     Virtual WAN: [Pont–hely VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                      | :large_blue_diamond:  |
|     Virtual WAN: Több [hely](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) között VPN Gateway                       | :large_blue_diamond:  |


**Specializált szolgáltatások**

|     Termékek                                                    | Rugalmasság             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     Cognitive Services: anomáliadetektor                        | :large_blue_diamond:  |
|     Cognitive Services: Form Recognizer                         | :large_blue_diamond:  |
|     Tárolás: utralemez                                         | :large_blue_diamond:  |


**Nem regionális**

|     Termékek                                                    | Rugalmasság             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | :globe_with_meridians: |
|     Azure Active Directory                                    | :globe_with_meridians: |
|     Azure Komplex veszélyforrások elleni védelem                            | :globe_with_meridians: |
|     Azure Advisor                                               | :globe_with_meridians: |
|     Azure Blueprints                                            | :globe_with_meridians: |
|     Azure Bot Services                                          | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Defender for IoT                                    | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Information Protection                              | :globe_with_meridians: |
|     Azure Lighthouse                                          | :globe_with_meridians: |
|     Azure Managed Applications                                | :globe_with_meridians: |
|     Azure Maps                                                  | :globe_with_meridians: |
|     Azure Performance Diagnostics                               | :globe_with_meridians: |
|     Azure Policy                                                | :globe_with_meridians: |
|     Azure Resource Graph                                      | :globe_with_meridians: |
|     Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Azure Stack Edge                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     Content Delivery Network                                    | :globe_with_meridians: |
|     Költségkezelés                                             | :globe_with_meridians: |
|     Ügyfélszéf a Microsoft Azure                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Microsoft Azure Peering Service                           | :globe_with_meridians: |
|     Microsoft Azure Portal                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     Security Center                                           | :globe_with_meridians: |
|     Traffic Manager                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>A virtuális gépek díjszabása Availability Zones

Azure Availability Zones azure-előfizetéssel érhető el. További információ: [Sávszélesség díjszabása.](https://azure.microsoft.com/pricing/details/bandwidth/)


## <a name="get-started-with-availability-zones"></a>Első lépések a Availability Zones

- [Virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)
- [Felügyelt lemez hozzáadása a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Zónaredundáns virtuálisgép-méretezési csoport létrehozása](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Virtuális gépek terheléselosztása zónák között standard Load Balancer zónaredundáns előteressel standard Load Balancer használatával](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zónán belüli virtuális gépek terheléselosztása zóna standard Load Balancer előtere mellett](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zónaredundáns tárolás](../storage/common/storage-redundancy.md)
- [SQL Database általános célú szint](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Event Hubs georeplikációs vészhelyreállítás](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus georeplikációs vészhelyreállítás](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Zónaredundáns virtuális hálózati átjáró létrehozása](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zónaredundáns régió hozzáadása a Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Első lépések Azure Cache for Redis Availability Zones](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Azure Active Directory Domain Services-példány létrehozása](../active-directory-domain-services/tutorial-create-instance.md)
- [A Azure Kubernetes Service (AKS) fürt létrehozása Availability Zones](../aks/availability-zones.md)


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Régiók és rendelkezésre állási zónák az Azure-ban](az-overview.md)
