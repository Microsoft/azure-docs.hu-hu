---
title: Régiók és Availability Zones az Azure-ban
description: Ismerje meg az Azure-beli régiókat és Availability Zoneseket, hogy megfeleljenek a technikai és szabályozási követelményeinek.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: a15a94694f3c0623830650a8b5bbb00dc4c4cb6b
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285513"
---
# <a name="regions-and-availability-zones-in-azure"></a>Régiók és Availability Zones az Azure-ban

Microsoft Azure szolgáltatások globálisan elérhetők a Felhőbeli műveletek optimális szinten történő elvégzéséhez. Az igényeinek legmegfelelőbb régiót a technikai és szabályozási megfontolások alapján választhatja ki: a szolgáltatás funkcióit, az adattárolást, a megfelelőségi követelményeket és a késést.

## <a name="terminology"></a>Terminológia

A régiók és Availability Zones az Azure-ban való jobb megismeréséhez segít megérteni a kulcsfontosságú feltételeket vagy fogalmakat.

| Kifejezés vagy fogalom | Description |
| --- | --- |
| régió | A késés által meghatározott kerületben üzembe helyezett adatközpontok halmaza, amely egy dedikált regionális, kis késleltetésű hálózaton keresztül csatlakozik. |
| földrajz | A világ egyik területe, amely legalább egy Azure-régiót tartalmaz. A földrajzi területek olyan diszkrét piacot határoznak meg, amely megőrizheti az adattárolási és megfelelőségi határokat. A régiócsoportok lehetővé teszik, hogy a speciális adattárolási és megfelelőségi igényekkel rendelkező ügyfelek földrajzilag közel tárolhassák adataikat és alkalmazásaikat. A földrajzi területek hibatűrők, hogy a teljes régió meghibásodása a dedikált nagy kapacitású hálózati infrastruktúrával való kapcsolaton keresztül álljanak. |
| Rendelkezésre állási zóna | A régión belüli egyedi fizikai helyszínek. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. |
| javasolt régió | Egy régió, amely a szolgáltatások széles skáláját biztosítja, és úgy lett kialakítva, hogy támogassa a Availability Zones most vagy a jövőben. Ezek a Azure Portal a **javasolt** módon vannak kijelölve. |
| alternatív (egyéb) régió | Olyan régió, amely kibővíti az Azure-t az adatrezidensi határon belül, ahol egy javasolt régió is létezik. A másodlagos régiók segítik a késés optimalizálását, és egy második régiót biztosítanak a vész-helyreállítási igényeknek. Ezeket nem a Availability Zones támogatására tervezték (bár az Azure rendszeresen értékeli ezeket a régiókat annak megállapítása érdekében, hogy az ajánlott régiók legyenek). Ezek a Azure Portal vannak kijelölve.  |
| alapszolgáltatás | Egy alapszintű Azure-szolgáltatás, amely minden régióban elérhető, amikor a régió általánosan elérhető. |
| mainstream szolgáltatás | Egy Azure-szolgáltatás, amely az összes 90 javasolt régióban elérhető, a régió általános rendelkezésre állása vagy igény szerinti rendelkezésre állása mellett, alternatív régiókban. |
| speciális szolgáltatás | Egy olyan Azure-szolgáltatás, amely igény szerinti rendelkezésre állást biztosít a régiók számára a testreszabott/speciális hardverek által támogatott régiókban. |
| regionális szolgáltatás | Egy regionálisan üzembe helyezett Azure-szolgáltatás, amely lehetővé teszi az ügyfél számára, hogy megadja azt a régiót, amelybe a szolgáltatás telepítve lesz. A teljes listát lásd: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| nem regionális szolgáltatás | Egy Azure-szolgáltatás, amelynek nincs függősége egy adott Azure-régióban. A nem regionális szolgáltatások üzembe helyezése két vagy több régióban történik, és ha regionális hiba fordul elő, a szolgáltatás egy másik régióban lévő példánya folytatja az ügyfelek kiszolgálását. A teljes listát lásd: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Régiók

A régió a késés által meghatározott kerületen belül üzembe helyezett adatközpontok készlete, amely egy dedikált regionális, kis késleltetésű hálózaton keresztül csatlakozik. Az Azure rugalmasságot biztosít olyan alkalmazások üzembe helyezéséhez, ahol szükség van rá, több régiót is beleértve a régiók közötti rugalmasság biztosításához. További információ: [a rugalmassági pillér áttekintése](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Rendelkezésre állási zónák

A rendelkezésre állási zóna egy magas rendelkezésre állású ajánlat, amely védelmet nyújt alkalmazásai és adatai számára az adatközpont hibáiból. A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található. Egy régión belüli Availability Zones fizikai elkülönítése megvédi az alkalmazásokat és az adatközpontok meghibásodását. Zóna – a redundáns szolgáltatások az alkalmazások és az adatok replikálását Availability Zones az egypontos meghibásodások elleni védelem érdekében. Az Azure Availability Zones az iparág legjobb 99,99%-os rendelkezésre állását kínálja a virtuális gép számára. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Az Azure-régiók rendelkezésre állási zónái egy tartalék tartomány és egy frissítési tartomány kombinációja. Ha például három vagy több virtuális gépet hoz létre három zónában egy Azure-régióban, a virtuális gépeket a rendszer gyakorlatilag három tartalék tartományba és három frissítési tartományba terjeszti. Az Azure platform felismeri ezt az eloszlást a frissítési tartományok között, így biztosítva, hogy a különböző zónákba tartozó virtuális gépek nem frissülnek egyszerre.

Magas rendelkezésre állást építhet ki az alkalmazás-architektúrába a számítási, tárolási, hálózatkezelési és adaterőforrások egy zónán belüli és más zónákban való replikálásával. A rendelkezésre állási zónákat támogató Azure-szolgáltatások két kategóriába sorolhatók:

- **Zónákra** épülő szolgáltatások – ha egy erőforrás egy adott zónához (például virtuális gépekhez, felügyelt lemezekhez, szabványos IP-címekhez) van rögzítve, vagy
- **Zóna – redundáns szolgáltatások** – ha az Azure platform automatikusan replikálja a zónákat (például a zóna redundáns tárolását, SQL Database).

Az Azure-ban elérhető átfogó Üzletmenet-folytonosság érdekében az Azure region Pairs Availability Zones kombinációját használva hozza létre az alkalmazás-architektúrát. Az alkalmazásokat és az adatait szinkron módon replikálhatja az Azure-régión belüli Availability Zones használatával, hogy magas rendelkezésre állást biztosítson, és aszinkron módon replikálja az Azure-régiókat a vész-helyreállítási védelem érdekében.
 
![egy régió egy zónájának elméleti nézete](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> A rendelkezésre állási zónák azonosítói (a fenti képen az 1, 2 és 3 számok) logikai módon vannak leképezve az egyes előfizetések tényleges fizikai zónájához, egymástól függetlenül. Ez azt jelenti, hogy egy adott előfizetéshez tartozó rendelkezésre állási 1. zóna eltérő fizikai zónára hivatkozhatnak, mint a rendelkezésre állás 1. zóna egy másik előfizetésben. Ezért javasoljuk, hogy a rendelkezésre állási zónák azonosítóit ne használja a virtuális gépek elhelyezésére szolgáló különböző előfizetések között.

## <a name="region-and-service-categories"></a>Régió-és szolgáltatási kategóriák

Az Azure-szolgáltatások régiókban való rendelkezésre állásának megközelítését legjobban a javasolt régiókban és alternatív régiókban elérhető szolgáltatások jellemzik.

- **Ajánlott régió** – egy régió, amely a szolgáltatások széles skáláját biztosítja, és úgy lett kialakítva, hogy támogassa a Availability Zones most vagy a jövőben. Ezek a Azure Portal a **javasolt** módon vannak kijelölve.
- **Alternatív (egyéb) régió** – egy régió, amely kibővíti az Azure lábnyomát egy adatrezidensi határon belül, ahol egy javasolt régió is létezik. A másodlagos régiók segítik a késés optimalizálását, és egy második régiót biztosítanak a vész-helyreállítási igényeknek. Ezeket nem a Availability Zones támogatására tervezték (bár az Azure rendszeresen értékeli ezeket a régiókat annak megállapítása érdekében, hogy az ajánlott régiók legyenek). Ezek a Azure Portal vannak kijelölve. 

### <a name="comparing-region-types"></a>A régiók típusait hasonlítja össze

Az Azure-szolgáltatások három kategóriába sorolhatók: az alapszintű, a mainstream és a specializált szolgáltatásokra. Az Azure általános szabályzata, amely a szolgáltatások bármely régióba való üzembe helyezését végzi, elsősorban a régió típusa, a szolgáltatási kategóriák és az ügyfél igényei szerint:

- Alapszintű **– minden** ajánlott és alternatív régióban elérhető, amikor a régió általánosan elérhető, vagy egy új, általánosan elérhetővé váló alapszolgáltatásból 90 napon belül.
- **Mainstream** (általános) – az összes javasolt régióban elérhető, a régió általános rendelkezésre állása 90 napon belül. az alternatív régiókban igény szerint vezérelt (számos más régióba már telepítve van).
- **Specializált** – célzott szolgáltatások, amelyek gyakran az iparág által irányított vagy speciális hardverrel támogatottak. Az igény szerinti rendelkezésre állás régiók között (sok már a javasolt régiók nagy részébe van telepítve).

Ha szeretné megtekinteni, hogy mely szolgáltatások lettek üzembe helyezve egy adott régióban, valamint az előzetes verzió vagy a szolgáltatások általános elérhetősége egy adott régióban, tekintse meg a [régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Ha egy szolgáltatási ajánlat nem érhető el egy adott régióban, a Microsoft értékesítési képviselőjével kapcsolatba léphet.

| Régió típusa | Nem regionális | Alapító | Mainstream | Specializált | Rendelkezésre állási zónák | Adattárolási hely |
| --- | --- | --- | --- | --- | --- | --- |
| Ajánlott | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Igény szerint vezérelt | :heavy_check_mark: | :heavy_check_mark: |
| Alternatív | :heavy_check_mark: | :heavy_check_mark: | Igény szerint vezérelt | Igény szerint vezérelt | N/A | :heavy_check_mark: |

### <a name="services-by-category"></a>Szolgáltatások kategóriánként

Ahogy azt korábban említettük, az Azure három kategóriába sorolja a szolgáltatásokat: az alapszintű, a mainstream és a Specialized. A szolgáltatási kategóriák az általános rendelkezésre álláshoz vannak rendelve. A szolgáltatások gyakran elindítják az életciklusát speciális szolgáltatásként, és az igény és a kihasználtság növelését a mainstream vagy az Foundation is támogatja. A következő táblázat az alapszintű szolgáltatások kategóriáit sorolja fel. Vegye figyelembe a következőket a tábláról:

- Egyes szolgáltatások nem regionálisak. További információ és a nem regionális szolgáltatások listája: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/).
- A szolgáltatások vagy virtuális gépek régebbi generációja nem szerepel a felsorolásban. További információkért lásd: a [virtuális gépek méreteinek korábbi generációinak](../virtual-machines/sizes-previous-gen.md)dokumentációja.
- A szolgáltatások nem kapnak kategóriát az általános Elérhetőségig (GA). További információ és az előzetes verziójú szolgáltatások listája: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Alapító                           | Mainstream                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Storage-fiókok                       | API Management                                    | 
> | Application Gateway                    | Alkalmazás konfigurációja                                 | 
> | Azure Backup                           | App Service                                       | 
> | Azure Cosmos DB                        | Automation                                        | 
> | 2. generációs Azure Data Lake Storage           | Azure Active Directory tartományi szolgáltatások            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure-beli nyilvános IP-cím                        | Azure Cache for Redis                             | 
> | Azure SQL Database                     | Azure Cognitive Services                          | 
> | Felügyelt Azure SQL-példány             | Azure Cognitive Services: Computer Vision         | 
> | Disk Storage                           | Azure Cognitive Services: Content Moderator       | 
> | Event Hubs                             | Azure Cognitive Services: Face                    | 
> | Key Vault                              | Azure Cognitive Services: Text Analytics          | 
> | Terheléselosztóval                          | Azure Adatkezelő                               | 
> | Service Bus                            | Azure Database for MySQL                          | 
> | Service Fabric                         | Azure Database for PostgreSQL                     | 
> | Tárolás: gyors/ritka Blob Storage rétegek   | Azure DDoS Protection                             | 
> | Tárterület: Managed Disks                 | Azure Firewall                                    | 
> | Virtual Machine Scale Sets             | Azure Firewall Manager                            | 
> | Virtual Machines                       | Azure Functions                                   | 
> | Virtual Machines: Azure dedikált gazdagép | Azure IoT Hub                                     | 
> | Virtual Machines: Av2-Series           | Azure Kubernetes Service (AKS)                    | 
> | Virtual Machines: Bs-Series            | Azure Monitor: Application Insights               | 
> | Virtual Machines: DSv2-Series          | Azure Monitor: Log Analytics                      | 
> | Virtual Machines: DSv3-Series          | Azure Private Link                                | 
> | Virtual Machines: Dv2-Series           | Azure Site Recovery                               | 
> | Virtual Machines: Dv3-Series           | Azure Synapse Analytics                           |     
> | Virtual Machines: ESv3-Series          | Batch                                             | 
> | Virtual Machines: Ev3-Series           | Cloud Services: M sorozat                          | 
> | Virtual Network                        | Container Instances                               | 
> | VPN Gateway                            | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Media Services                                    | 
> |                                        | Network Watcher                                   | 
> |                                        | Prémium Blob Storage                              | 
> |                                        | Premium Files Storage                             | 
> |                                        | Virtual Machines: Ddsv4-Series                    | 
> |                                        | Virtual Machines: Ddv4-Series                     | 
> |                                        | Virtual Machines: Dsv4-Series                     | 
> |                                        | Virtual Machines: Dv4-Series                      | 
> |                                        | Virtual Machines: Edsv4-Series                    | 
> |                                        | Virtual Machines: Edv4-Series                     | 
> |                                        | Virtual Machines: Esv4-Series                     | 
> |                                        | Virtual Machines: Ev4-Series                      | 
> |                                        | Virtual Machines: Fsv2-Series                     | 
> |                                        | Virtual Machines: M sorozat                        | 
> |                                        | Virtuális WAN                                       | 



### <a name="specialized-services"></a>Speciális szolgáltatások
Ahogy azt korábban említettük, az Azure három kategóriába sorolja a szolgáltatásokat: az alapszintű, a mainstream és a Specialized. A szolgáltatási kategóriák az általános rendelkezésre álláshoz vannak rendelve. A szolgáltatások gyakran elindítják az életciklusát speciális szolgáltatásként, és az igény és a kihasználtság növelését a mainstream vagy az Foundation is támogatja. A következő táblázat a speciális szolgáltatásokat sorolja fel. 

> [!div class="mx-tableFixed"]
> | Specializált                                          |
> |------------------------------------------------------|
> | Azure API for FHIR                                   |
> | Azure Analysis Services                              |
> | Azure Blockchain Service                             |
> | Azure Cognitive Services: anomália detektor           |
> | Azure Cognitive Services: Custom Vision              |
> | Azure Cognitive Services: űrlap-felismerő            |
> | Azure Cognitive Services: lebilincselő olvasó           |
> | Azure Cognitive Services: Language Understanding     |
> | Azure Cognitive Services: személyre szabás               |
> | Azure Cognitive Services: QnA Maker                  |
> | Azure Cognitive Services: beszédfelismerési szolgáltatások            |
> | Azure Data Share                                     |
> | Azure Databricks                                     |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Azure Dedicated HSM                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure Red Hat OpenShift                              |
> | Azure SignalR Service                                |
> | Azure Spring Cloud                                   |
> | Azure Stream Analytics                               |
> | Azure Time Series Insights                           |
> | Azure VMware Solution                                |
> | Azure VMware Solution by CloudSimple                 |
> | Spatial Anchors                                      |
> | Tárterület: Archive Storage                             |
> | Ultra Disk Storage                                   |
> | Video Indexer                                        |
> | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: DCsv2 sorozat                       |
> | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: HBv2-Series                        |
> | Virtual Machines: HCv1-Series                        |
> | Virtual Machines: H sorozat                           |
> | Virtual Machines: LSv2-Series                        |
> | Virtual Machines: Mv2-Series                         |
> | Virtual Machines: NCv3-Series                        |
> | Virtual Machines: NDv2-Series                        |
> | Virtual Machines: NVv3-Series                        |
> | Virtual Machines: NVv4-Series                        | 
> | Virtual Machines: SAP HANA on Azure Large Instances  |




## <a name="next-steps"></a>Következő lépések

- [Az Azure-Availability Zones támogató régiók](az-region.md)
- [Gyorssablonok](https://aka.ms/azqs)
