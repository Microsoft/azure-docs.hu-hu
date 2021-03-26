---
title: Azure-szolgáltatás címkék – áttekintés
titlesuffix: Azure Virtual Network
description: További információ a szolgáltatási címkékről. A szolgáltatás-címkék segítségével csökkentheti a biztonsági szabályok létrehozásának bonyolultságát.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 2d14ca2423d34926a9e297823a6515c2c5dde06a
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607116"
---
# <a name="virtual-network-service-tags"></a>Virtuális hálózati szolgáltatás címkéi
<a name="network-service-tags"></a>

A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét.

A szolgáltatás-címkék használatával hálózati [biztonsági csoportokon](./network-security-groups-overview.md#security-rules) vagy [Azure Firewallon](../firewall/service-tags.md)is meghatározhat hálózati hozzáférés-vezérlést. A szolgáltatási címkéket adott IP-címek helyett használhatja biztonsági szabályok létrehozásakor. A szolgáltatási címke nevének, például a **ApiManagement** megadásával a szabály megfelelő *forrás* vagy *cél* mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. 

> [!NOTE] 
> Március 2021-én a [felhasználói útvonalakban](./virtual-networks-udr-overview.md)lévő explicit IP-címtartományok helyett a szolgáltatási címkéket is használhatja. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el. 

A szolgáltatás-címkék használatával hálózati elkülönítést érhet el, és biztosíthatja az Azure-erőforrásokat az általános internetről, miközben nyilvános végpontokkal rendelkező Azure-szolgáltatásokhoz fér hozzá. Bejövő/kimenő hálózati biztonsági csoportokra vonatkozó szabályok létrehozása az **internetre** irányuló adatforgalom megtagadásához, valamint az adott Azure-szolgáltatások **AzureCloud** vagy más [elérhető szolgáltatás-címkévé](#available-service-tags) tételének engedélyezése.

![Az Azure-szolgáltatások hálózati elkülönítése szolgáltatás-címkék használatával](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Elérhető szolgáltatás címkéi
A következő táblázat tartalmazza a [hálózati biztonsági csoport](./network-security-groups-overview.md#security-rules) szabályaiban használható összes szolgáltatás címkéjét.

Az oszlopok azt jelzik, hogy a címke:

- Megfelelő a bejövő vagy kimenő forgalmat lefedő szabályokhoz.
- A [regionális](https://azure.microsoft.com/regions) hatókört támogatja.
- [Azure Firewall](../firewall/service-tags.md) -szabályokban használható.

Alapértelmezés szerint a szolgáltatás címkéi a teljes felhő tartományait tükrözik. Egyes szolgáltatási címkék a megfelelő IP-tartományok adott régióra való korlátozásával is részletesebb szabályozást tesznek lehetővé. A szolgáltatás címkéje például az **Azure Storage-** t jelöli a teljes felhőhöz, de a **Storage. WestUS** leszűkíti a tartományt úgy, hogy csak a WestUS régióból származó tárolási IP-címekre legyenek érvényesek. Az alábbi táblázat azt jelzi, hogy az egyes szolgáltatási címkék támogatják-e az ilyen regionális hatókört.  

| Címke | Cél | Használhat bejövő vagy kimenő adatforgalmat? | Lehet regionális? | Használható a Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Műveleti csoport. | Bejövő | Nem | Nem |
| **ApiManagement** | Felügyeleti forgalom az Azure API Managementhoz – dedikált központi telepítések. <br/><br/>*Megjegyzés:* Ez a címke az Azure API Management szolgáltatási végpontját jelöli a vezérlési sík régiónként. Ez lehetővé teszi, hogy az ügyfelek felügyeleti műveleteket hajtsanak végre a API Management szolgáltatáson konfigurált API-kkal, műveletekkel, házirendekkel és NamedValues.  | Bejövő | Igen | Yes |
| **ApplicationInsightsAvailability** | Application Insights rendelkezésre állás. | Bejövő | Nem | Nem |
| **AppConfiguration** | Alkalmazás konfigurációja. | Kimenő | Nem | Nem |
| **AppService**    | Azure App Service. Ezt a címkét a Web Apps és a functions alkalmazások kimenő biztonsági szabályaihoz ajánlott használni.  | Kimenő | Igen | Yes |
| **AppServiceManagement** | Felügyeleti forgalom a App Service Environment számára dedikált központi telepítések esetén. | Mindkettő | Nem | Igen |
| **AzureActiveDirectory** | Azure Active Directory. | Kimenő | Nem | Igen |
| **AzureActiveDirectoryDomainServices** | Felügyeleti forgalom a Azure Active Directory Domain Services számára dedikált központi telepítések esetén. | Mindkettő | Nem | Igen |
| **AzureAdvancedThreatProtection** | Az Azure komplex veszélyforrások elleni védelem. | Kimenő | Nem | Nem |
| **AzureAPIForFHIR** | Azure API a FHIR-hez (gyors egészségügyi együttműködési erőforrások).<br/><br/> *Megjegyzés: Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül.*| Kimenő | Nem | Nem |
| **AzureArcInfrastructure** | Azure arc-kompatibilis kiszolgálók, Azure arc-kompatibilis Kubernetes és vendég-konfigurációs forgalom.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **AzureActiveDirectory**, a **AzureTrafficManager** és a **AzureResourceManager** címkével. *Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül*.| Kimenő | Nem | Igen |
| **AzureBackup** |Azure Backup.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **Storage** és a **AzureActiveDirectory** címkével. | Kimenő | Nem | Igen |
| **AzureBotService** | Azure Bot Service. | Kimenő | Nem | Nem |
| **AzureCloud** | Minden [adatközpont nyilvános IP-címe](https://www.microsoft.com/download/details.aspx?id=56519). | Kimenő | Igen | Yes |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Ez a címke vagy a címke által jelzett IP-címek segítségével biztosítható, hogy az indexelő biztonságos hozzáférést biztosítson az adatforrásokhoz. További részletekért tekintse meg az [Indexelő kapcsolati dokumentációját](../search/search-indexer-troubleshooting.md#connection-errors) . <br/><br/> *Megjegyzés*: a keresési szolgáltatás IP-címe nem szerepel az ehhez a szolgáltatási címkéhez tartozó IP-címtartományok listájában, és **hozzá kell adni** az adatforrások IP-tűzfalához is. | Bejövő | Nem | Nem |
| **AzureConnectors** | Ez a címke a felügyelt összekötők számára használt IP-címeket mutatja be, amelyek a bejövő webhookok visszahívását teszik elérhetővé a Azure Logic Apps szolgáltatásnak és a hozzájuk kapcsolódó szolgáltatásoknak (például az Azure Storage vagy az Azure Event Hubs). | Bejövő/kimenő | Igen | Yes |
| **AzureContainerRegistry** | Azure Container Registry. | Kimenő | Igen | Yes |
| **AzureCosmosDB** | Azure Cosmos DB. | Kimenő | Igen | Yes |
| **AzureDatabricks** | Azure Databricks. | Mindkettő | Nem | Nem |
| **AzureDataExplorerManagement** | Azure Adatkezelő-felügyelet. | Bejövő | Nem | Nem |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Kimenő | Nem | Igen |
| **AzureDevSpaces** | Azure fejlesztői tárhelyek. | Kimenő | Nem | Nem |
| **AzureDevOps** | Azure dev Ops.<br/><br/>*Megjegyzés: Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül*| Bejövő | Nem | Igen |
| **AzureDigitalTwins** | Azure digitális Twins.<br/><br/>*Megjegyzés:* Ez a címke vagy a címke által jelzett IP-címek segítségével korlátozható az esemény-útvonalakhoz konfigurált végpontokhoz való hozzáférés. *Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül* | Bejövő | Nem | Igen |
| **AzureEventGrid** | Azure Event Grid. | Mindkettő | Nem | Nem |
| **AzureFrontDoor. frontend** <br/> **AzureFrontDoor. backend** <br/> **AzureFrontDoor.FirstParty**  | Azure bejárati ajtó. | Mindkettő | Nem | Nem |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Megjegyzés:* Ez a címke a **AzureActiveDirectory**, a **AzureFrontDoor. frontend** és a **AzureFrontDoor. FirstParty** címkéktől függ. | Kimenő | Nem | Nem |
| **AzureIoTHub** | Azure IoT Hub. | Kimenő | Nem | Nem |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **AzureActiveDirectory** címkével. | Kimenő | Igen | Yes |
| **AzureLoadBalancer** | Az Azure-infrastruktúra Load Balancer. A címke a gazdagép (168.63.129.16) [virtuális IP-címére van](./network-security-groups-overview.md#azure-platform-considerations) lefordítva, ahol az Azure Health-Szondák származnak. Ez csak a mintavételi forgalmat, nem pedig a háttér-erőforráshoz való valós forgalmat foglalja magában. Ha nem Azure Load Balancer használ, felülbírálhatja ezt a szabályt. | Mindkettő | Nem | Nem |
| **AzureMachineLearning** | Azure Machine Learning. | Mindkettő | Nem | Igen |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon és egyéni metrikák (GiG-végpontok).<br/><br/>*Megjegyzés:* Log Analytics esetén a **Storage** címkét is meg kell adni. Ha Linux-ügynököket használ, a **GuestAndHybridManagement** címkét is meg kell adni. | Kimenő | Nem | Igen |
| **AzureOpenDatasets** | Azure Open-adatkészletek.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **AzureFrontDoor. frontend** és a **Storage** címkével. | Kimenő | Nem | Nem |
| **AzurePlatformDNS** | Az alapszintű infrastruktúra (alapértelmezett) DNS szolgáltatás.<br/><br>Ezt a címkét használhatja az alapértelmezett DNS letiltásához. Ha ezt a címkét használja, körültekintően járjon el. Javasoljuk, hogy olvassa el az [Azure platformmal kapcsolatos szempontokat](./network-security-groups-overview.md#azure-platform-considerations). Javasoljuk továbbá, hogy a címke használata előtt végezzen tesztelést. | Kimenő | Nem | Nem |
| **AzurePlatformIMDS** | Az Azure Instance Metadata Service (IMDS), amely egy alapszintű infrastruktúra-szolgáltatás.<br/><br/>Ezt a címkét használhatja az alapértelmezett IMDS letiltásához. Ha ezt a címkét használja, körültekintően járjon el. Javasoljuk, hogy olvassa el az [Azure platformmal kapcsolatos szempontokat](./network-security-groups-overview.md#azure-platform-considerations). Javasoljuk továbbá, hogy a címke használata előtt végezzen tesztelést. | Kimenő | Nem | Nem |
| **AzurePlatformLKM** | Windows licencelés vagy kulcskezelő szolgáltatás.<br/><br/>Ezt a címkét használhatja a licencelés alapértelmezett értékének letiltásához. Ha ezt a címkét használja, körültekintően járjon el. Javasoljuk, hogy olvassa el az [Azure platformmal kapcsolatos szempontokat](./network-security-groups-overview.md#azure-platform-considerations).  Javasoljuk továbbá, hogy a címke használata előtt végezzen tesztelést. | Kimenő | Nem | Nem |
| **AzureResourceManager** | Azure Resource Manager. | Kimenő | Nem | Nem |
| **AzureSignalR** | Azure-jelző. | Kimenő | Nem | Nem |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **AzureActiveDirectory**, a **AzureKeyVault**, a **EventHub**, a **GuestAndHybridManagement** és a **Storage** címkével. | Kimenő | Nem | Nem |
| **AzureTrafficManager** | Azure Traffic Manager mintavétel IP-címei.<br/><br/>Traffic Manager mintavétel IP-címeivel kapcsolatos további információkért lásd: [Azure Traffic Manager – gyakori kérdések](../traffic-manager/traffic-manager-faqs.md). | Bejövő | Nem | Igen |  
| **BatchNodeManagement** | Felügyeleti forgalom a Azure Batch számára dedikált központi telepítések esetén. | Mindkettő | Nem | Igen |
| **CognitiveServicesManagement** | Az Azure Cognitive Services adatforgalmára vonatkozó címtartományok. | Mindkettő | Nem | Nem |
| **DataFactory**  | Azure Data Factory | Mindkettő | Nem | Nem |
| **DataFactoryManagement** | Azure Data Factory felügyeleti forgalma. | Kimenő | Nem | Nem |
| **Dynamics365ForMarketingEmail** | A Dynamics 365 marketing e-mail-szolgáltatásának címtartományt. | Kimenő | Igen | Nem |
| **EventHub** | Azure Event Hubs. | Kimenő | Igen | Yes |
| **GatewayManager** | Az Azure VPN Gateway és Application Gateway számára dedikált központi telepítések kezelési forgalma. | Bejövő | Nem | Nem |
| **GuestAndHybridManagement** | Azure Automation és vendég konfiguráció. | Kimenő | Nem | Igen |
| **HDInsight** | Azure-HDInsight. | Bejövő | Igen | Nem |
| **Internet** | Az IP-címtartomány, amely a virtuális hálózaton kívül esik, és a nyilvános interneten érhető el.<br/><br/>A címtartomány magában foglalja az [Azure tulajdonában lévő nyilvános IP-címterület területét](https://www.microsoft.com/download/details.aspx?id=41653). | Mindkettő | Nem | Nem |
| **LogicApps** | Logic Apps. | Mindkettő | Nem | Nem |
| **LogicAppsManagement** | Logic Apps felügyeleti forgalma. | Bejövő | Nem | Nem |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Kimenő | Nem | Nem |
| **MicrosoftContainerRegistry** | Microsoft-tároló lemezképek tároló-beállításjegyzéke. <br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **AzureFrontDoor. FirstParty** címkével. | Kimenő | Igen | Yes |
| **PowerBI** | PowerBi. *Megjegyzés: Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül.* | Mindkettő | Nem | Nem|
| **PowerQueryOnline** | Power Query online. | Mindkettő | Nem | Nem |
| **ServiceBus** | Azure Service Bus a prémium szintű szolgáltatási szintet használó forgalom. | Kimenő | Igen | Yes |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Megjegyzés:* Ez a címke a Service Fabric szolgáltatási végpontot jelöli a vezérlési sík régiónként. Ez lehetővé teszi, hogy az ügyfelek felügyeleti műveleteket hajtsanak végre a Service Fabric-fürtökhöz a VNET (például végpontot). https://westus.servicefabric.azure.com) | Mindkettő | Nem | Nem |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL és az Azure szinapszis Analytics.<br/><br/>*Megjegyzés:* Ez a címke a szolgáltatást jelöli, de a szolgáltatás adott példányai nem. Például a címke az Azure SQL Database szolgáltatást jelöli, de nem egy adott SQL-adatbázist vagy -kiszolgálót. Ez a címke nem vonatkozik az SQL felügyelt példányára. | Kimenő | Igen | Yes |
| **SqlManagement** | Felügyeleti forgalom az SQL-dedikált üzemelő példányokhoz. | Mindkettő | Nem | Igen |
| **Storage** | Azure Storage. <br/><br/>*Megjegyzés:* Ez a címke a szolgáltatást jelöli, de a szolgáltatás adott példányai nem. Például a címke az Azure Storage szolgáltatást jelöli, de nem egy adott Azure Storage-fiókot. | Kimenő | Igen | Yes |
| **StorageSyncService** | Storage Sync szolgáltatás. | Mindkettő | Nem | Nem |
| **WindowsVirtualDesktop** | Windows rendszerű virtuális asztal. | Mindkettő | Nem | Igen |
| **VirtualNetwork** | A virtuális hálózati címtartomány (a virtuális hálózathoz meghatározott összes IP-címtartomány), az összes csatlakoztatott helyszíni [címterület, a virtuális hálózat](virtual-network-peering-overview.md) , a [virtuális hálózati átjáróhoz](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)csatlakoztatott virtuális hálózatok, a [gazdagép virtuális IP-címe](./network-security-groups-overview.md#azure-platform-considerations), valamint a [felhasználó által megadott útvonalakon](virtual-networks-udr-overview.md)használt előtagok címei. Ez a címke tartalmazhatja az alapértelmezett útvonalakat is. | Mindkettő | Nem | Nem |

>[!NOTE]
>A klasszikus üzembe helyezési modellben (Azure Resource Manager előtt) az előző táblázatban felsorolt címkék egy részhalmaza támogatott. Ezek a címkék máshogy vannak írva:
>
>| Klasszikus helyesírás | Egyenértékű Resource Manager-címke |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Az Azure-szolgáltatások szolgáltatási címkéi a használt Felhőbeli előtagokat jelölik. Az Azure-beli nyilvános felhőben az **SQL** -címke értékének megfelelő mögöttes IP-címtartományok például eltérnek az Azure China Cloud-beli mögöttes tartománytól.

> [!NOTE]
> Ha egy szolgáltatáshoz (például az Azure Storage-hoz vagy a Azure SQL Database) [virtuális hálózati szolgáltatási végpontot](virtual-network-service-endpoints-overview.md) valósít meg, az Azure egy [útvonalat](virtual-networks-udr-overview.md#optional-default-routes) helyez el a szolgáltatás virtuális hálózati alhálózatához. Az útvonalon található címek előtagjai ugyanazok a címek előtagjai vagy CIDR, mint a megfelelő szolgáltatási címke.

## <a name="service-tags-on-premises"></a>Szolgáltatási címkék a helyszínen  
A szolgáltatáshoz tartozó aktuális címke és tartomány információ beszerzésével a helyszíni tűzfal konfigurációjának részeként is felvehető. Ez az információ az egyes szolgáltatási címkéknek megfelelő IP-címtartományok aktuális időponthoz tartozó listája. Az információkat programozott módon vagy JSON-fájl letöltésével szerezheti be az alábbi szakaszokban leírtak szerint.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>A Service tag Discovery API használata (nyilvános előzetes verzió)
Programozott módon lekérheti a szolgáltatási címkék aktuális listáját az IP-címtartomány részleteivel együtt:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure CLI](/cli/azure/network#az-network-list-service-tags)

> [!NOTE]
> Habár a nyilvános előzetes verzióban érhető el, a felderítési API a JSON-letöltések által visszaadott információknál kevésbé naprakész adatokat adhat vissza. (Lásd a következő szakaszt.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Szolgáltatási címkék felderítése a letölthető JSON-fájlokkal 
A szolgáltatáshoz tartozó címkék aktuális listáját tartalmazó JSON-fájlok az IP-címtartomány részleteivel együtt tölthetők le. Ezek a listák hetente frissülnek és jelennek meg. Az egyes felhő helyei a következők:

- [Nyilvános Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

Az ezekben a fájlokban lévő IP-címtartományok CIDR-jelöléssel rendelkeznek. 

> [!NOTE]
>Az adatok egy részhalmaza közzé lett téve az [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), az [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)és az [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770)XML-fájljaiban. Az XML-letöltéseket a rendszer a 2020-es június 30-ig elavult, és a továbbiakban nem lesz elérhető. Az előző szakaszokban leírtaknak megfelelően telepítse át a programot a felderítési API vagy a JSON-fájl letöltésére.

### <a name="tips"></a>Tippek 
- Az egyik kiadvány frissítései a következőre észlelhetők a JSON-fájlban lévő, megnövekedett *changeNumber* értékek alapján. Az egyes alszakaszok (például a **Storage. WestUS**) saját *changeNumber* rendelkeznek, amely a változásoknak megfelelően növekszik. A fájl *changeNumber* legfelső szintje az alszakaszok bármelyikének megváltozásakor növekszik.
- A szolgáltatási címke adatainak elemzésére (például a WestUS-ben az összes címtartomány beszerzésére) vonatkozó példákat a [Service tag felderítési API PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag) dokumentációjában találja.
- Ha új IP-címeket adnak hozzá a szolgáltatáshoz, a rendszer legalább egy hétig nem fogja használni őket az Azure-ban. Ezzel a szolgáltatással bármikor frissítheti azokat a rendszereket, amelyeknek szüksége lehet a szolgáltatási címkékhez társított IP-címek nyomon követésére.

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [hozhat létre hálózati biztonsági csoportot](tutorial-filter-network-traffic.md).
