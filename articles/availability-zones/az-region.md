---
title: A rendelkezésreállási zónákat támogató Azure-szolgáltatások
description: Az Azure-ban kiválóan elérhető és rugalmas alkalmazások hozhatók létre, Availability Zones biztosítanak fizikailag különálló helyet, amellyel erőforrásokat futtathat.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 5152b0ecf6eebe87031a2165d5d8ba11d99209cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601185"
---
# <a name="azure-services-that-support-availability-zones"></a>A rendelkezésreállási zónákat támogató Azure-szolgáltatások

Microsoft Azure a globális infrastruktúrát minden rétegben tervezték és építették, hogy a lehető legmagasabb szintű redundancia és rugalmasság legyen elérhető ügyfelei számára. Az Azure-infrastruktúra földrajzokból, régiókból és Availability Zonesokból áll, amelyek korlátozzák a meghibásodások robbanási sugarát, és így korlátozzák az ügyfelek alkalmazásaira és adataira gyakorolt lehetséges hatást. A Azure Availability Zones-konstrukció olyan szoftver-és hálózatkezelési megoldás kialakítására lett kifejlesztve, amely védelmet nyújt az adatközponti hibák ellen, és nagyobb magas rendelkezésre állást biztosít ügyfeleinknek.

A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden zóna egy vagy több, független energiaellátással, hűtéssel és hálózatkezeléssel rendelkező adatközpontból áll. A régiókban Availability Zones fizikai elkülönítése korlátozza az alkalmazások és az adatok hatását a zónák hibáiból, például a nagy léptékű áradások, a nagyobb viharok és a főviharok, valamint más olyan események esetében, amelyek megzavarják a hely elérését, a biztonságos áthaladást, a kibővített segédprogramok rendelkezésre állását és az erőforrások elérhetőségét. A Availability Zones és a hozzájuk társított adatközpontok úgy vannak kialakítva, hogy ha az egyik zónát feltörték, akkor a régió többi Availability Zones is támogatja a szolgáltatásokat, a kapacitást és a rendelkezésre állást.

Az összes Azure-beli felügyeleti szolgáltatás a régió szintű hibáktól való ellenálló képességnek számít. A hibák spektrumában egy vagy több rendelkezésre állási zónának a régión belüli meghibásodása kisebb meghibásodási sugarú, mint a teljes régió meghibásodása. Az Azure helyreállíthatja a régión belüli felügyeleti szolgáltatások zóna szintű meghibásodását. Az Azure a régión belül egyszerre végzi el a kritikus karbantartást, így megakadályozva, hogy az adott régión belül üzembe helyezett felhasználói erőforrásokkal kapcsolatos hibák ne legyenek Availability Zones.


![3 zónával rendelkező Azure-régió fogalmi nézete](./media/az-region/azure-region-availability-zones.png)


A Availability Zones támogató **Azure-szolgáltatások** három kategóriába sorolhatók: a többtényezős, a **zóna-redundáns** és a **nem regionális** szolgáltatásokra. Az ügyfelek számítási feladatait kategorizálhatja az alkalmazások teljesítményének és tartósságának kielégítése érdekében.

- **Zónákra** kiterjedő szolgáltatások – egy erőforrást egy meghatározott, saját maga által kiválasztott rendelkezésre állási zónába helyezhet üzembe, amely szigorúbb késési vagy teljesítménybeli követelményeket is elérhet.  A rugalmasságot úgy kell megtervezni, hogy az alkalmazásokat és az adatforrásokat egy vagy több, a régión belüli zónába replikálja.  Az erőforrások rögzíthető egy adott zónához. Például a virtuális gépek, a felügyelt lemezek vagy a standard IP-címek egy adott zónába rögzíthetők, ami nagyobb rugalmasságot tesz lehetővé azáltal, hogy az erőforrások egy vagy több példánya több zónában is elterjedt.

- **Zóna – redundáns szolgáltatások** – az Azure platform replikálja az erőforrást és az adatzónákat.  A Microsoft kezeli a magas rendelkezésre állást, mivel az Azure automatikusan replikálja és elosztja a régión belüli példányokat.  A ZRS például három zónában replikálja az adatforrást, így a zóna meghibásodása nem befolyásolja az adott adatforrást. 

- **Nem regionális szolgáltatások** – a szolgáltatások mindig az Azure földrajzi helyekről érhetők el, és rugalmasak a zónákra kiterjedő kimaradások, valamint az egész régióra kiterjedő kimaradások terén. 


Az Azure-ban elérhető átfogó Üzletmenet-folytonosság érdekében az Azure region Pairs Availability Zones kombinációját használva hozza létre az alkalmazás-architektúrát. Az alkalmazásokat és az adatait szinkron módon replikálhatja az Azure-régión belüli Availability Zones használatával, hogy magas rendelkezésre állást biztosítson, és aszinkron módon replikálja az Azure-régiókat a vész-helyreállítási védelem érdekében. További információért olvassa el a [Availability Zones használatával történő magas rendelkezésre állású megoldások kiépítése](/azure/architecture/high-availability/building-solutions-for-high-availability)című témakört. 

## <a name="azure-services-supporting-availability-zones"></a>Availability Zones támogató Azure-szolgáltatások

 - A régebbi generációs virtuális gépek nincsenek felsorolva. További információ: [a virtuális gépek méreteinek előző generációi](../virtual-machines/sizes-previous-gen.md).
 - Ahogy azt az Azure-beli [régiókban és Availability Zones](az-overview.md)is említettük, egyes szolgáltatások nem regionálisak. Ezek a szolgáltatások nem rendelkeznek egy adott Azure-régiótól való függőséggel, mivel ilyenek a zónákra kiterjedő kimaradások, valamint az egész régióra kiterjedő kimaradások.  A nem regionális szolgáltatások listáját a [régió által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/)között találja.


## <a name="azure-regions-with-availability-zones"></a>Azure-régiók Availability Zones


| Amerika           | Európa               | Afrika              | Ázsia és a Csendes-óceáni térség   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Dél-Brazília       | Közép-Franciaország       | Dél-Afrika északi régiója * | Kelet-Japán     |
| Közép-Kanada     | Középnyugat-Németország |                     | Délkelet-Ázsia |
| Az USA középső régiója         | Észak-Európa         |                     | Kelet-Ausztrália |
| USA keleti régiója            | Az Egyesült Királyság déli régiója             |                     |                |
| USA 2. keleti régiója          | Nyugat-Európa          |                     |                |
| USA déli középső régiója |                      |                     |                |
| USA-beli államigazgatás – Virginia     |                      |                     |                |
| USA 2. nyugati régiója        |                      |                     |                |


\* Ha többet szeretne megtudni a Availability Zones és az elérhető szolgáltatások támogatásáról ezekben a régiókban, lépjen kapcsolatba a Microsoft értékesítési vagy ügyfélszolgálati képviselőjével. Az Availability Zonest támogató közelgő régiókkal kapcsolatban lásd: [Azure földrajzi](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)területek.


## <a name="azure-services-supporting-availability-zones"></a>Availability Zones támogató Azure-szolgáltatások

- A régebbi generációs virtuális gépek nem szerepelnek az alábbi listában. További információ: [a virtuális gépek méreteinek előző generációi](../virtual-machines/sizes-previous-gen.md).

- Egyes szolgáltatások nem regionálisak, további információért lásd: [régiók és Availability Zones az Azure-ban](az-overview.md) . Ezek a szolgáltatások nem függnek egy adott Azure-régiótól, így rugalmasak lehetnek a zónákra kiterjedő kimaradások és az egész régióra kiterjedő kimaradások terén.  A nem regionális szolgáltatások listáját a [régió által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/)között találja.


### <a name="zone-resilient-services"></a>Rugalmas szolgáltatások 

: globe_with_meridians: nem regionális szolgáltatások – a szolgáltatások mindig az Azure földrajzi helyekről érhetők el, és rugalmasak a zónákra kiterjedő kimaradások, valamint az egész régióra kiterjedő kimaradások terén.

: large_blue_diamond: rugalmas az egész zóna számára 

**Alapszolgáltatások**

|     Termékek                                                    | Rugalmasság             |
|-----------------------------------------------------------------|:----------------------------:|
|     Storage-fiók                                           | : large_blue_diamond:  |
|     Application Gateway (v2)                                  | : large_blue_diamond:  |
|     Azure Backup                                                | : large_blue_diamond:  |
|     Azure Cosmos DB                                           | : large_blue_diamond:  |
|     2. generációs Azure Data Lake Storage                             | : large_blue_diamond:  |
|     Azure Express-útvonal                                       | : large_blue_diamond:  |
|     Azure nyilvános IP-cím                                           | : large_blue_diamond:  |
|     Azure SQL Database (általános célú szintű)                 | : large_blue_diamond:  |
|     Azure SQL Database (prémium szintű & üzletileg kritikus szinten)     | : large_blue_diamond:  |
|     Disk Storage                                                | : large_blue_diamond:  |
|     Event Hubs                                                  | : large_blue_diamond:  |
|     Key Vault                                                   | : large_blue_diamond:  |
|     Load Balancer                                               | : large_blue_diamond:  |
|     Service Bus                                                 | : large_blue_diamond:  |
|     Service Fabric                                            | : large_blue_diamond:  |
|     Tárolás: gyors/ritka Blob Storage rétegek                      | : large_blue_diamond:  |
|     Tárterület: Managed Disks                                    | : large_blue_diamond:  |
|     Virtual Machines méretezési csoportok                               | : large_blue_diamond:  |
|     Virtual Machines                                          | : large_blue_diamond:  |
|     Virtual Machines: Av2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Bs-Series                               | : large_blue_diamond:  |
|     Virtual Machines: DSv2-Series                             | : large_blue_diamond:  |
|     Virtual Machines: DSv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Dv2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Dv3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: ESv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Ev3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: F sorozat                                | : large_blue_diamond:  |
|     Virtual Machines: FS-Series                               | : large_blue_diamond:  |
|     Virtual Network                                           | : large_blue_diamond:  |
|     VPN Gateway                                                 | : large_blue_diamond:  |


**Mainstream szolgáltatások**

| Termékek                                        | Rugalmasság |
|-------------------------------------------------|:------------:|
| App Service-környezetek                        |      : large_blue_diamond:  |
| Azure Active Directory tartományi szolgáltatások          |      : large_blue_diamond:  |
| Azure Bastion                                   |      : large_blue_diamond:  |
| Azure Cache for Redis                           |      : large_blue_diamond:  |
| Azure Cognitive Services: Text Analytics        |      : large_blue_diamond:  |
| Azure Adatkezelő                             |      : large_blue_diamond:  |
| Azure Database for MySQL – rugalmas kiszolgáló      |      : large_blue_diamond:  |
| Azure Database for PostgreSQL – rugalmas kiszolgáló |      : large_blue_diamond:  |
| Azure DDoS Protection                           |      : large_blue_diamond:  |
| Azure Disk Encryption                           |      : large_blue_diamond:  |
| Azure Firewall                                  |      : large_blue_diamond:  |
| Azure Firewall Manager                          |      : large_blue_diamond:  |
| Azure Kubernetes Service (AKS)                  |      : large_blue_diamond:  |
| Azure Private Link                              |      : large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      : large_blue_diamond:  |
| Azure Site Recovery                             |      : large_blue_diamond:  |
| Azure SQL: virtuális gép                      |      : large_blue_diamond:  |
| Azure Search                                    |      : large_blue_diamond:  |
| Azure Web Application Firewall                  |      : large_blue_diamond:  |
| Container Registry                              |      : large_blue_diamond:  |
| Event Grid                                      |      : large_blue_diamond:  |
| Network Watcher                                 |      : large_blue_diamond:  |
| Network Watcher: Traffic Analytics              |      : large_blue_diamond:  |
| Power BI Embedded                               |      : large_blue_diamond:  |
| Prémium Blob Storage                            |      : large_blue_diamond:  |
| Storage: prémium szintű Azure-fájlok                    |      : large_blue_diamond:  |
| Virtual Machines: Azure dedikált gazdagép          |      : large_blue_diamond:  |
| Virtual Machines: Ddsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Ddv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dsv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dv4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Edsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Edv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Esv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Ev4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Fsv2-Series                   |      : large_blue_diamond:  |
| Virtual Machines: M sorozat                      |      : large_blue_diamond:  |
| Virtuális WAN                                     |      : large_blue_diamond:  |
| Virtuális WAN: ExpressRoute                       |      : large_blue_diamond:  |
| Virtuális WAN: pont – hely VPN Gateway          |      : large_blue_diamond:  |
| Virtuális WAN: helyek közötti VPN Gateway           |      : large_blue_diamond:  |


**Nem regionális**

|     Termékek                                  |     Rugalmasság    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     : globe_with_meridians:             |
|     Azure Active Directory                  |     : globe_with_meridians:             |
|     Azure Komplex veszélyforrások elleni védelem          |     : globe_with_meridians:             |
|     Azure Advisor                             |     : globe_with_meridians:             |
|     Azure Blueprints                          |     : globe_with_meridians:             |
|     Azure Bot Services                        |     : globe_with_meridians:             |
|     Azure Front Door                          |     : globe_with_meridians:             |
|     Azure Defender a IoT                  |     : globe_with_meridians:             |
|     Azure Front Door                           |     : globe_with_meridians:             |
|     Azure Information Protection            |     : globe_with_meridians:             |
|     Azure-világítótorony                        |     : globe_with_meridians:             |
|     Azure Managed Applications              |     : globe_with_meridians:             |
|     Azure Maps                                |     : globe_with_meridians:             |
|     Azure Policy                              |     : globe_with_meridians:             |
|     Azure-erőforrás gráf                    |     : globe_with_meridians:             |
|     Azure Sentinel                            |     : globe_with_meridians:             |
|     Azure Stack                               |     : globe_with_meridians:             |
|     Azure Stack Edge                        |     : globe_with_meridians:             |
|     Cloud Shell                               |     : globe_with_meridians:             |
|     Content Delivery Network                  |     : globe_with_meridians:             |
|     Költségkezelés                           |     : globe_with_meridians:             |
|     Microsoft Azure Ügyfélszéf    |     : globe_with_meridians:             |
|     Intune                                    |     : globe_with_meridians:             |
|     Microsoft Azure peering szolgáltatás         |     : globe_with_meridians:             |
|     Microsoft Azure Portal                  |     : globe_with_meridians:             |
|     Microsoft Cloud App Security              |     : globe_with_meridians:             |
|     Microsoft Graph                           |     : globe_with_meridians:             |
|     Security Center                         |     : globe_with_meridians:             |
|     Traffic Manager                         |     : globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>A Availability Zones-beli virtuális gépek díjszabása

A rendelkezésre állási zónában üzembe helyezett virtuális gépekhez nem kell további díjat fizetni. További információkért tekintse át a [sávszélesség díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Ismerkedés a Availability Zones

- [Virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)
- [Felügyelt lemez hozzáadása a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Zónában redundáns virtuálisgép-méretezési csoport létrehozása](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Virtuális gépek terheléselosztása zónák között egy olyan standard Load Balancer használatával, amely zóna nélküli előtérbeli felületet használ](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Virtuális gépek terheléselosztása zónán belül egy standard Load Balancer és egy zóna-előtérbeli felület használatával](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zónaredundáns tárolás](../storage/common/storage-redundancy.md)
- [Általános célú SQL Database](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Event Hubs georeplikációs vészhelyreállítás](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus georeplikációs vészhelyreállítás](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Zónaredundáns virtuális hálózati átjáró létrehozása](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zóna redundáns régiójának hozzáadása Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Azure cache Első lépések Redis Availability Zones](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Azure Active Directory Domain Services-példány létrehozása](../active-directory-domain-services/tutorial-create-instance.md)
- [Availability Zonest használó Azure Kubernetes-szolgáltatásbeli (ak-beli) fürt létrehozása](../aks/availability-zones.md)


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Régiók és rendelkezésre állási zónák az Azure-ban](az-overview.md)
