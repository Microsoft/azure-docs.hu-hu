---
title: Régiók és Availability Zones az Azure-ban
description: Ismerje meg az Azure-beli régiókat és Availability Zoneseket, hogy megfeleljenek a technikai és szabályozási követelményeinek.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 01/26/2021
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 0b67f113fb8ab3835419a75697e60a732d4ee390
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896053"
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
| mainstream szolgáltatás | Egy Azure-szolgáltatás, amely a régió/szolgáltatás általános rendelkezésre állása vagy igény szerinti rendelkezésre állása mellett, a különböző régiókban található 12 hónapon belül minden javasolt régióban elérhető. |
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

- Alapszintű **– minden** ajánlott és alternatív régióban elérhető, ha a régió általánosan elérhető, vagy az új alapszolgáltatás általánosan elérhetővé válását követő 12 hónapon belül.
- **Mainstream** – az összes ajánlott régióban elérhető a régió/szolgáltatás általános elérhetőségét követő 12 hónapon belül. az alternatív régiókban igény szerint vezérelt (számos más régióba már telepítve van).
- **Specializált** – célzott szolgáltatások, amelyek gyakran az iparág által irányított vagy speciális hardverrel támogatottak. Az igény szerinti rendelkezésre állás régiók között (sok már a javasolt régiók nagy részébe van telepítve).

Ha szeretné megtekinteni, hogy mely szolgáltatások lettek üzembe helyezve egy adott régióban, valamint az előzetes verzió vagy a szolgáltatások általános elérhetősége egy adott régióban, tekintse meg a [régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Ha egy szolgáltatási ajánlat nem érhető el egy adott régióban, a Microsoft értékesítési képviselőjével kapcsolatba léphet.

| Régió típusa | Nem regionális | Alapító | Mainstream | Specializált | Rendelkezésre állási zónák | Adattárolási hely |
| --- | --- | --- | --- | --- | --- | --- |
| Ajánlott | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Igény szerint vezérelt | :heavy_check_mark: | :heavy_check_mark: |
| Alternatív | :heavy_check_mark: | :heavy_check_mark: | Igény szerint vezérelt | Igény szerint vezérelt | N/A | :heavy_check_mark: |

### <a name="services-by-category"></a>Szolgáltatások kategóriánként

Ahogy azt korábban említettük, az Azure három kategóriába sorolja a szolgáltatásokat: az alapszintű, a mainstream és a Specialized. A szolgáltatási kategóriák az általános rendelkezésre álláshoz vannak rendelve. A szolgáltatások gyakran elindítják az életciklusát speciális szolgáltatásként, és az igény és a kihasználtság növelését a mainstream vagy az Foundation is támogatja. A következő táblázat az alapszintű, a mainstream vagy a specializált szolgáltatások kategóriáit sorolja fel. Vegye figyelembe a következőket a tábláról:

- Egyes szolgáltatások nem regionálisak. További információ és a nem regionális szolgáltatások listája: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/).
- A régebbi generációs virtuális gépek nincsenek felsorolva. További információ: a [virtuális gépek méreteinek korábbi generációinak](../virtual-machines/sizes-previous-gen.md) dokumentációja
- . A szolgáltatások nem kapnak kategóriát az általános Elérhetőségig (GA). További információ és az előzetes verziójú szolgáltatások listája: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Alapító                          | Mainstream                                        | Specializált                                          |
> |---------------------------------------|---------------------------------------------------|------------------------------------------------------|
> | Storage-fiókok                      | API Management                                    | Azure API for FHIR                                   |
> | Application Gateway                   | Alkalmazás konfigurációja                                 | Azure Analysis Services                              |
> | Azure Backup                          | App Service                                       | Azure Cognitive Services: anomália detektor           |
> | Azure Cosmos DB                       | Automation                                        | Azure Cognitive Services: Custom Vision              |
> | 2. generációs Azure Data Lake Storage          | Azure Active Directory tartományi szolgáltatások            | Azure Cognitive Services: űrlap-felismerő            |
> | Azure ExpressRoute                    | Azure Bastion                                     | Azure Cognitive Services: személyre szabás               |
> | Azure-beli nyilvános IP-cím                       | Azure Cache for Redis                             | Azure Cognitive Services: QnA Maker                  |
> | Azure SQL Database                    | Azure Cognitive Search                            | Azure Database for MariaDB                           |
> | Azure SQL: felügyelt példány          | Azure Cognitive Services                          | Azure Database Migration Service                     |
> | Cloud Services                        | Azure Cognitive Services: Computer Vision         | Azure Dedicated HSM                                  |
> | Cloud Services: Av2-Series            | Azure Cognitive Services: Content Moderator       | Azure Digital Twins                                  |
> | Cloud Services: Dv2-Series            | Azure Cognitive Services: Face                    | Azure Health bot                                     |
> | Cloud Services: Dv3-Series            | Azure Cognitive Services: lebilincselő olvasó        | Azure HPC Cache                                      |
> | Cloud Services: Ev3-Series            | Azure Cognitive Services: Language Understanding  | Azure Lab Services                                   |
> | Cloud Services: példányok szintjének IP-címei    | Azure Cognitive Services: beszédfelismerési szolgáltatások         | Azure NetApp Files                                   |
> | Cloud Services: Fenntartott IP           | Azure Cognitive Services: Text Analytics          | Azure SignalR szolgáltatás                                |
> | Disk Storage                          | Azure Cognitive Services: fordító              | Azure Spring Cloud szolgáltatás                           |
> | Event Hubs                            | Azure Data Explorer                               | Azure Time Series Insights                           |
> | Key Vault                             | Azure Data Share                                  | Azure VMware Solution                                |
> | Terheléselosztóval                         | Azure Database for MySQL                          | Azure VMware Solution by CloudSimple                 |
> | Service Bus                           | Azure Database for PostgreSQL                     | Cloud Services: H sorozat                             |
> | Service Fabric                        | Azure Databricks                                  | Data Catalog                                         |
> | Tárolás: gyors/ritka Blob Storage rétegek  | Azure DDoS Protection                             | Data Lake Analytics                                  |
> | Tárterület: Managed Disks                | Azure DevTest Labs                                | Azure Machine Learning Studio (klasszikus)              |
> | Virtual Machine Scale Sets            | Azure Firewall                                    | Spatial Anchors                                      |
> | Virtual Machines                      | Azure Firewall Manager                            | Tárterület: Archive Storage                             |
> | Virtual Machines: Av2-Series          | Azure Functions                                   | StorSimple                                           |
> | Virtual Machines: Bs-Series           | Azure IoT Hub                                     | Ultra Disk Storage                                   |
> | Virtual Machines: DSv2-Series         | Azure Kubernetes Service (AKS)                    | Video Indexer                                        |
> | Virtual Machines: DSv3-Series         | Azure Machine Learning                            | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: Dv2-Series          | Azure Monitor: Application Insights               | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: Dv3-Series          | Azure Monitor: Log Analytics                      | Virtual Machines: DCsv2 sorozat                       |
> | Virtual Machines: ESv3-Series         | Azure Private Link                                | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: Ev3-Series          | Azure Red Hat OpenShift                           | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: példányok szintjének IP-címei  | Azure Site Recovery                               | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: Fenntartott IP         | Azure Stream Analytics                            | Virtual Machines: HBv2-Series                        |
> | Virtual Network                       | Azure Synapse Analytics                           | Virtual Machines: HCv1-Series                        |
> | VPN Gateway                           | Batch                                             | Virtual Machines: H sorozat                           |
> |                                       | Cloud Services: M sorozat                          | Virtual Machines: LSv2-Series                        |
> |                                       | Container Instances                               | Virtual Machines: Mv2-Series                         |
> |                                       | Container Registry                                | Virtual Machines: NCv3-Series                        |
> |                                       | Data Factory                                      | Virtual Machines: NDv2-Series                        |
> |                                       | Event Grid                                        | Virtual Machines: NVv3-Series                        |
> |                                       | HDInsight                                         | Virtual Machines: NVv4-Series                        |> 
> |                                       | Logic Apps                                        | Virtual Machines: SAP HANA on Azure Large Instances  |
> |                                       | Media Services                                    |                                                      |
> |                                       | Network Watcher                                   |                                                      |
> |                                       | Notification Hubs                                 |                                                      |
> |                                       | Prémium Blob Storage                              |                                                      |
> |                                       | Premium Files Storage                             |                                                      |
> |                                       | Virtual Machines: Ddsv4-Series                    |                                                      |
> |                                       | Virtual Machines: Ddv4-Series                     |                                                      |
> |                                       | Virtual Machines: Dsv4-Series                     |                                                      |
> |                                       | Virtual Machines: Dv4-Series                      |                                                      |
> |                                       | Virtual Machines: Edsv4-Series                    |                                                      |
> |                                       | Virtual Machines: Edv4-Series                     |                                                      |
> |                                       | Virtual Machines: Esv4-Series                     |                                                      |
> |                                       | Virtual Machines: Ev4-Series                      |                                                      |
> |                                       | Virtual Machines: Fsv2-Series                     |                                                      |
> |                                       | Virtual Machines: M sorozat                        |                                                      |
> |                                       | Virtuális WAN                                       |                                                      |


## <a name="next-steps"></a>Következő lépések

- [Az Azure-Availability Zones támogató régiók](az-region.md)
- [Gyorssablonok](https://aka.ms/azqs)
