---
title: Az Azure-szolgáltatáscímkék áttekintése
titlesuffix: Azure Virtual Network
description: Tudnivalók a szolgáltatáscímkékről. A szolgáltatáscímkék minimálisra csökkentik a biztonsági szabályok létrehozásának összetettségét.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/14/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: b5f6f06af3eecabe26f7b587a790912f99b006e4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496758"
---
# <a name="virtual-network-service-tags"></a>Virtuális hálózati szolgáltatáscímkék
<a name="network-service-tags"></a>

A szolgáltatáscímke egy adott Azure-szolgáltatásból származó IP-címelőtagok csoportját jelöli. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és a címek változásával automatikusan frissíti a szolgáltatáscímkét, így minimalizálja a hálózati biztonsági szabályok gyakori frissítésének összetettségét.

A szolgáltatáscímkékkel hálózati biztonsági csoportok [](./network-security-groups-overview.md#security-rules) vagy hálózati biztonsági csoportok hálózati hozzáférés-vezérlését határozhatja [Azure Firewall.](../firewall/service-tags.md) Biztonsági szabályok létrehozásakor használjon szolgáltatáscímkéket adott IP-címek helyett. A szolgáltatáscímke nevének (például **ApiManagement)**  a  szabály megfelelő forrás- vagy célmezőben való megadásával engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. 

> [!NOTE] 
> 2021 márciusában a felhasználó által megadott útvonalakban explicit IP-címtartományok helyett [szolgáltatáscímkéket is használhat.](./virtual-networks-udr-overview.md) Ez a funkció jelenleg nyilvános előzetes verzióban érhető el. 

A szolgáltatáscímkék használatával hálózatelszigetelést érhet el, és megvédheti Azure-erőforrásait az általános internettől, miközben nyilvános végpontokkal bíró Azure-szolgáltatásokat érhet el. Hozzon létre bejövő/kimenő hálózati biztonságicsoport-szabályokat az **internetre/internetről** kimenő forgalom [](#available-service-tags) megtagadása érdekében, és engedélyezze az **AzureCloudba** vagy az azure-szolgáltatások egyéb elérhető szolgáltatáscímkéibe irányuló és az onnan kimenő forgalmat.

![Az Azure-szolgáltatások hálózati elkülönítése szolgáltatáscímkék használatával](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Elérhető szolgáltatáscímkék
Az alábbi táblázat a hálózati biztonsági csoportok szabályaiban használható összes [szolgáltatáscímkét](./network-security-groups-overview.md#security-rules) tartalmazza.

Az oszlopok jelzik, hogy a címke:

- A bejövő vagy kimenő forgalmat lefedő szabályokhoz alkalmas.
- Támogatja [a regionális hatókört.](https://azure.microsoft.com/regions)
- Használható a [Azure Firewall](../firewall/service-tags.md) szabályokban.

Alapértelmezés szerint a szolgáltatáscímkék a teljes felhő tartományát tükrözik. Egyes szolgáltatáscímkék részletesebb szabályozást is lehetővé teszik a megfelelő IP-címtartományok egy adott régióra való korlátozásával. A Storage szolgáltatáscímke például az Azure Storage-et jelöli a teljes felhőre, de a **Storage.WestUS** a tartományt a WestUS régióból származó tároló IP-címtartományára szűkíti.  Az alábbi táblázat azt jelzi, hogy az egyes szolgáltatáscímkék támogatják-e az ilyen regionális hatókört.  

| Címke | Cél | Használhat bejövő vagy kimenő forgalmat? | Lehet regionális? | Használható a Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Műveletcsoport. | Bejövő | Nem | Nem |
| **ApiManagement** | Az Azure-beli API Management üzemelő példányok felügyeleti forgalma. <br/><br/>*Megjegyzés:* Ez a címke az Azure API Management szolgáltatásvégpontot jelöli régiónkénti vezérlősíkhoz. Ez lehetővé teszi az ügyfelek számára, hogy felügyeleti műveleteket hajtsanak végre az API Management szolgáltatásban konfigurált API-kon, műveleteken, szabályzaton és NamedValueson.  | Bejövő | Igen | Igen |
| **ApplicationInsightsAvailability** | Application Insights rendelkezésre állás. | Bejövő | Nem | Nem |
| **AppConfiguration (Alkalmazáskonfiguráció)** | App Configuration. | Kimenő | Nem | Nem |
| **AppService**    | Azure App Service. Ez a címke a webalkalmazások és a függvényalkalmazások kimenő biztonsági szabályaihoz ajánlott.  | Kimenő | Igen | Igen |
| **AppServiceManagement** | Felügyeleti forgalom az üzemelő példányok számára App Service Environment. | Mindkettő | Nem | Igen |
| **AzureActiveDirectory** | Azure Active Directory. | Kimenő | Nem | Igen |
| **AzureActiveDirectoryDomainServices** | Felügyeleti forgalom a dedikált központi telepítések Azure Active Directory Domain Services. | Mindkettő | Nem | Igen |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Kimenő | Nem | Nem |
| **AzureAPIForFHIR** | Azure API for FHIR (Fast Healthcare Interoperability Resources).<br/><br/> *Megjegyzés: Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül.*| Kimenő | Nem | Nem |
| **AzureRinInfrastructure** | Azure Arc engedélyezett kiszolgálókat, Azure Arc Kubernetes- és Vendégkonfiguráció-forgalmat.<br/><br/>*Megjegyzés:* Ez a címke az **AzureActiveDirectory,****az AzureTrafficManager** és az **AzureResourceManager** címkéktől függ. *Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül.*| Kimenő | Nem | Igen |
| **AzureBackup** |Azure Backup.<br/><br/>*Megjegyzés:* Ez a címke a **Storage** és az **AzureActiveDirectory címkéktől függ.** | Kimenő | Nem | Igen |
| **AzureBotService** | Azure Bot Service. | Kimenő | Nem | Nem |
| **AzureCloud** | Az [adatközpont összes nyilvános IP-címe.](https://www.microsoft.com/download/details.aspx?id=56519) | Kimenő | Igen | Igen |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Ez a címke vagy a címke által lefedett IP-címek segítségével az indexelők biztonságos hozzáférést adhatnak az adatforráshoz. További részletekért tekintse meg [az indexelő kapcsolati](../search/search-indexer-troubleshooting.md#connection-errors) dokumentációját. <br/><br/> *Megjegyzés:* A keresési szolgáltatás IP-címe nem szerepel a szolgáltatáscímke  IP-címtartományai listájában, és az adatforrások IP-tűzfalához is hozzá kell adni. | Bejövő | Nem | Nem |
| **AzureConnectors** | Ez a címke a felügyelt összekötőkhöz használt IP-címeket jelöli, amelyek bejövő webhook-visszahívást kezdeményeznek az Azure Logic Apps-szolgáltatásba, valamint a megfelelő szolgáltatásokhoz, például az Azure Storage-hoz vagy a Azure Event Hubs. | Bejövő/kimenő | Igen | Igen |
| **AzureContainerRegistry** | Azure Container Registry. | Kimenő | Igen | Igen |
| **AzureCosmosDB** | Azure Cosmos DB. | Kimenő | Igen | Igen |
| **AzureDatabricks** | Azure Databricks. | Mindkettő | Nem | Nem |
| **AzureDataExplorerManagement** | Azure Data Explorer Management. | Bejövő | Nem | Nem |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Kimenő | Nem | Igen |
| **AzureDevSpaces** | Azure Dev Spaces. | Kimenő | Nem | Nem |
| **AzureDevOps** | Azure Dev Ops.<br/><br/>*Megjegyzés: Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül*| Bejövő | Nem | Igen |
| **AzureDigitalTwins** | Azure Digital Twins.<br/><br/>*Megjegyzés:* Ez a címke vagy a címke által lefedett IP-címek felhasználhatók az eseményútvonalakhoz konfigurált végpontok elérésének korlátozására. *Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül* | Bejövő | Nem | Igen |
| **AzureEventGrid** | Azure Event Grid. | Mindkettő | Nem | Nem |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door. | Mindkettő | Nem | Nem |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Megjegyzés:* Ez a címke az **AzureActiveDirectory,** **az AzureFrontDoor.Frontend és** az **AzureFrontDoor.FirstParty címkéktől** függ. | Kimenő | Nem | Nem |
| **AzureIoTHub** | Azure IoT Hub. | Kimenő | Nem | Nem |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Megjegyzés:* Ez a címke az **AzureActiveDirectory címkétől függ.** | Kimenő | Igen | Igen |
| **AzureLoadBalancer** | Az Azure-infrastruktúra terheléselosztási szolgáltatása. A címke lefordítja annak a gazdagépnek a virtuális [IP-címét](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16), ahonnan az Azure állapot-mintavételei származnak. Ez csak a mintavételi forgalomra vonatkozik, a háttérerőforrásra nem. Ha nem használ ilyen Azure Load Balancer, felülbírálhatja ezt a szabályt. | Mindkettő | Nem | Nem |
| **AzureMachineLearning** | Azure Machine Learning. | Mindkettő | Nem | Igen |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon és egyéni metrikák (GiG-végpontok).<br/><br/>*Megjegyzés:* A Log Analyticshez a **Storage címkére** is szükség van. Linux-ügynökök használata esetén a **GuestAndHybridManagement** címke is szükséges. | Kimenő | Nem | Igen |
| **AzureOpenDatasets** | Azure Open Datasets.<br/><br/>*Megjegyzés:* Ez a címke az **AzureFrontDoor.Frontend** és a **Storage címkétől függ.** | Kimenő | Nem | Nem |
| **AzurePlatformDNS** | Az alapszintű infrastruktúra (alapértelmezett) DNS-szolgáltatás.<br/><br>Ezzel a címkével letilthatja az alapértelmezett DNS-t. Legyen óvatos, ha ezt a címkét használja. Javasoljuk, hogy olvassa el az [Azure platformmal kapcsolatos szempontokat.](./network-security-groups-overview.md#azure-platform-considerations) Javasoljuk továbbá, hogy a címke használata előtt végezzen tesztelést. | Kimenő | Nem | Nem |
| **AzurePlatformIMDS** | Az Azure Instance Metadata Service (IMDS), amely egy alapszintű infrastruktúra-szolgáltatás.<br/><br/>Ezzel a címkével letilthatja az alapértelmezett IMDS-t. Legyen óvatos, ha ezt a címkét használja. Javasoljuk, hogy olvassa el az [Azure platformmal kapcsolatos szempontokat.](./network-security-groups-overview.md#azure-platform-considerations) Javasoljuk továbbá, hogy a címke használata előtt végezzen tesztelést. | Kimenő | Nem | Nem |
| **AzurePlatformLKM** | Windows-licencelés vagy kulcskezelő szolgáltatás.<br/><br/>Ezzel a címkével letilthatja a licencelés alapértelmezett beállítását. Legyen óvatos a címke használatakor. Javasoljuk, hogy olvassa el az [Azure platformmal kapcsolatos szempontokat.](./network-security-groups-overview.md#azure-platform-considerations)  Javasoljuk továbbá, hogy a címke használata előtt végezzen tesztelést. | Kimenő | Nem | Nem |
| **AzureResourceManager** | Azure Resource Manager. | Kimenő | Nem | Nem |
| **AzureSignalR** | Azure SignalR. | Kimenő | Nem | Nem |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Megjegyzés:* Ez a címke az **AzureActiveDirectory,** **az AzureKeyVault,** az **EventHub,** a **GuestAndHybridManagement** és a **Storage címkétől** függ. | Kimenő | Nem | Nem |
| **AzureTrafficManager** | Azure Traffic Manager IP-címek mintavétele.<br/><br/>További információ a mintavételi IP Traffic Manager címekkel kapcsolatban: [gyakori Azure Traffic Manager.](../traffic-manager/traffic-manager-faqs.md) | Bejövő | Nem | Igen |  
| **BatchNodeManagement** | Felügyeleti forgalom a dedikált környezetek számára Azure Batch. | Mindkettő | Nem | Igen |
| **CognitiveServicesManagement** | A forgalom címtartományai a Azure Cognitive Services. | Mindkettő | Nem | Nem |
| **DataFactory**  | Azure Data Factory | Mindkettő | Nem | Nem |
| **DataFactoryManagement** | Felügyeleti forgalom Azure Data Factory. | Kimenő | Nem | Nem |
| **Dynamics365ForMarketingEmail** | A Dynamics 365 marketing e-mail szolgáltatásának címtartományai. | Kimenő | Igen | Nem |
| **EventHub** | Azure Event Hubs. | Kimenő | Igen | Igen |
| **GatewayManager** | Az Azure-beli virtuális és VPN Gateway üzemelő példányok Application Gateway. | Bejövő | Nem | Nem |
| **GuestAndHybridManagement** | Azure Automation és vendégkonfiguráció. | Kimenő | Nem | Igen |
| **HDInsight** | Azure HDInsight. | Bejövő | Igen | Nem |
| **Internet** | A virtuális hálózaton kívüli és a nyilvános internet által elérhető IP-címtér.<br/><br/>A címtartomány tartalmazza az [Azure tulajdonában lévő nyilvános IP-címtartományt.](https://www.microsoft.com/download/details.aspx?id=41653) | Mindkettő | Nem | Nem |
| **LogicApps** | Logic Apps. | Mindkettő | Nem | Nem |
| **LogicAppsManagement** | Felügyeleti forgalom Logic Apps. | Bejövő | Nem | Nem |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Kimenő | Nem | Nem |
| **MicrosoftContainerRegistry** | Tároló-beállításjegyzék a Microsoft-tároló rendszerképéhez. <br/><br/>*Megjegyzés:* Ez a címke az **AzureFrontDoor.FirstParty címkére van függőségi** viszonyban. | Kimenő | Igen | Igen |
| **PowerBI** | PowerBi. *Megjegyzés: Ez a címke jelenleg nem konfigurálható az Azure Portalon keresztül.* | Mindkettő | Nem | Nem|
| **PowerQueryOnline** | Power Query Online. | Mindkettő | Nem | Nem |
| **ServiceBus** | Azure Service Bus prémium szolgáltatási szintet használó forgalmat. | Kimenő | Igen | Igen |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Megjegyzés:* Ez a címke a Service Fabric vezérlősík szolgáltatásvégpontját jelöli régiónként. Ez lehetővé teszi az ügyfelek számára, hogy felügyeleti műveleteket hajtsanak végre Service Fabric fürtökön a virtuális hálózatukról (pl. végpont). https:// westus.servicefabric.azure.com) | Mindkettő | Nem | Nem |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL és Azure Synapse Analytics.<br/><br/>*Megjegyzés:* Ez a címke a szolgáltatást jelöli, de nem a szolgáltatás adott példányait. Például a címke az Azure SQL Database szolgáltatást jelöli, de nem egy adott SQL-adatbázist vagy -kiszolgálót. Ez a címke nem vonatkozik a felügyelt SQL-példányra. | Kimenő | Igen | Igen |
| **SqlManagement** | Felügyeleti forgalom SQL-dedikált üzemelő példányok esetén. | Mindkettő | Nem | Igen |
| **Storage** | Azure Storage. <br/><br/>*Megjegyzés:* Ez a címke a szolgáltatást jelöli, de nem a szolgáltatás adott példányait. Például a címke az Azure Storage szolgáltatást jelöli, de nem egy adott Azure Storage-fiókot. | Kimenő | Igen | Igen |
| **StorageSyncService** | Társzinkronizálási szolgáltatás. | Mindkettő | Nem | Nem |
| **WindowsVirtualDesktop** | Windows Virtual Desktop. | Mindkettő | Nem | Igen |
| **VirtualNetwork** | A virtuális hálózat címtere (a virtuális hálózathoz meghatározott összes IP-címtartomány), az összes csatlakoztatott helyszíni [címtér,](virtual-network-peering-overview.md) társviszonyban álló virtuális hálózat, [virtuális](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)hálózati átjáróhoz csatlakoztatott virtuális hálózat, a gazdagép virtuális [IP-címe](./network-security-groups-overview.md#azure-platform-considerations)és a felhasználó által megadott útvonalakon használt [címelőtagok.](virtual-networks-udr-overview.md) Ez a címke tartalmazhat alapértelmezett útvonalakat is. | Mindkettő | Nem | Nem |

>[!NOTE]
>A klasszikus üzembe helyezési modellben (Azure Resource Manager előtt az előző táblázatban felsorolt címkék egy része támogatott. Ezek a címkék másképpen vannak megírva:
>
>| Klasszikus helyesírás | Egyenértékű Resource Manager címke |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Az Azure-szolgáltatások szolgáltatáscímkéi a használt felhő címelőtagját jelölik. Az Azure nyilvános felhő sql-címkeértékének megfelelő mögöttes IP-tartományok például eltérnek az Azure China-felhő mögöttes tartományaitól. 

> [!NOTE]
> Ha megvalósít [](virtual-network-service-endpoints-overview.md) egy virtuális hálózati szolgáltatásvégpontot egy szolgáltatáshoz, például az [](virtual-networks-udr-overview.md#optional-default-routes) Azure Storage-hoz vagy a Azure SQL Database-hoz, az Azure hozzáad egy útvonalat a szolgáltatás virtuális hálózatának alhálózatához. Az útvonal címelőtagjai ugyanazok a címelőtagok vagy CIDR-tartományok, mint a megfelelő szolgáltatáscímke.

## <a name="service-tags-on-premises"></a>Helyszíni szolgáltatáscímkék  
Beszerezheti az aktuális szolgáltatáscímkét és tartományinformációkat, amelyek a helyszíni tűzfal konfigurációi részeként szerepeljenek. Ez az információ az egyes szolgáltatáscímkéknek megfelelő IP-címtartományok aktuális időponthoz igaz listája. Az információkat szoftveresen vagy JSON-fájl letöltésével szerezheti be, a következő szakaszokban leírtak szerint.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>A Service Tag Discovery API használata (nyilvános előzetes verzió)
Programozott módon lekérheti a szolgáltatáscímkék aktuális listáját az IP-címtartomány részleteivel együtt:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure CLI](/cli/azure/network#az-network-list-service-tags)

> [!NOTE]
> Az új Szolgáltatáscímke-adatok propagálása az API-eredményekben akár 4 hetet is igénybe vesz. A válasz metaadataiban a módosítási szám akkor lesz megnövelve, amikor ez történik. Az eredmények között átmeneti különbségek lehetnek, ha eltérő helyértékeket ad meg. Ha az eredményeket NSG-szabályok létrehozására használja, a helyparamatert úgy kell beállítania, hogy megfeleljen az NSG régiójának. 

> [!NOTE]
> Az API-adatok azokat a címkéket képviselik, amelyek NSG-szabályokkal használhatók. Ez a címkék egy része jelenleg a letölthető JSON-fájlban található. A nyilvános előzetes verzióban azonban nem garantáljuk, hogy az adatok az egyik frissítéstől a következőig változatlanok maradnak. 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Szolgáltatáscímkék felderítése letölthető JSON-fájlokkal 
Letöltheti az aktuális szolgáltatáscímkéket tartalmazó JSON-fájlokat az IP-címtartomány részleteivel együtt. Ezeket a listákat hetente frissítjük és közzétenjük. Az egyes felhők helyei:

- [Nyilvános Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

Az ezekben a fájlokban lévő IP-címtartományok CIDR-ként vannak megszabadva. 

> [!NOTE]
>Az információk egy részcsoportja XML-fájlokban lett közzétéve az [Azure Public,](https://www.microsoft.com/download/details.aspx?id=41653)az [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)és az Azure [Germany esetében.](https://www.microsoft.com/download/details.aspx?id=54770) Ezek az XML-letöltések 2020. június 30-ig elavultak lesznek, és a továbbiakban nem lesznek elérhetők. A Discovery API- vagy JSON-fájlletöltéseket az előző szakaszokban leírtak szerint érdemes átemelni.

### <a name="tips"></a>Tippek 
- A frissítéseket az egyik közzétételtől a következőig észlelheti, ha a JSON-fájlban nagyobb *changeNumber* értékeket észlel. Minden alszakasz (például **Storage.WestUS)** saját *changeNumber* számokkal rendelkezik, amely a módosítások előfordulásakor növekszik. A fájl *changeNumber* felső szintje akkor növekszik, ha az alszakaszok bármelyike módosul.
- A szolgáltatáscímke-információk (például a Storage összes címtartományának lekérte a WestUS-ban) elemzési példáiért tekintse meg a [Service Tag Discovery API PowerShell-dokumentációját.](/powershell/module/az.network/Get-AzNetworkServiceTag)
- Amikor új IP-címeket ad hozzá a szolgáltatáscímkékhez, azok legalább egy hétig nem lesznek használatban az Azure-ban. Ez időt ad arra, hogy frissítsen minden olyan rendszert, amely esetleg nyomon követi a szolgáltatáscímkékhez társított IP-címeket.

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, [hogyan hozhat létre hálózati biztonsági csoportot.](tutorial-filter-network-traffic.md)
