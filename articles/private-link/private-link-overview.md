---
title: Mi az az Azure privát kapcsolat?
description: Az Azure Private link funkcióinak, architektúrájának és megvalósításának áttekintése. Ismerje meg, hogyan működik az Azure Private-végpontok és az Azure Private link Service, és hogyan használhatja őket.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/28/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: ee9b38343176eec82d8e227e86faa97814f5be13
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616536"
---
# <a name="what-is-azure-private-link"></a>Mi az az Azure privát kapcsolat? 
Az Azure Private link lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett felhasználói/partneri szolgáltatások elérését a virtuális hálózat [privát végpontján](private-endpoint-overview.md) keresztül.

A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át. A szolgáltatás nyilvános internetre való kimutatása már nem szükséges. Létrehozhatja saját [privát kapcsolati szolgáltatását](private-link-service-overview.md) a virtuális hálózatban, és továbbíthatja az ügyfeleknek. Az Azure Private link használatával történő beállítás és felhasználás konzisztens az Azure Pásti, az ügyfél és a megosztott partneri szolgáltatások között.

> [!IMPORTANT]
> Az Azure Private link már általánosan elérhető. A privát végpont és a magánhálózati kapcsolat szolgáltatás (a standard Load Balancer mögötti szolgáltatás) általánosan elérhető. A különböző Azure-beli Pásti különböző időpontokban fog bejelentkezni az Azure Private-hivatkozásba. Tekintse meg a [rendelkezésre állás](#availability) című szakaszt a jelen cikk az Azure-beli, privát kapcsolaton keresztüli, pontos állapotáról. Az ismert korlátozásokért lásd: [privát végpont](private-endpoint-overview.md#limitations) és [privát kapcsolat szolgáltatás](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Azure Private link Center Azure Portal" border="false":::

## <a name="key-benefits"></a>Főbb előnyök
Az Azure Private link a következő előnyöket biztosítja:  
- **Saját hozzáférésű szolgáltatások az Azure platformon**: virtuális hálózatának összekapcsolása az Azure-ban, nyilvános IP-cím nélkül a forráson vagy a célhelyen. A szolgáltatók saját virtuális hálózatban tehetik a szolgáltatásaikat, és a felhasználók a helyi virtuális hálózatban érhetik el a szolgáltatásokat. A privát kapcsolati platform a fogyasztó és a szolgáltatások közötti kapcsolatot fogja kezelni az Azure-beli gerinc hálózaton. 
 
- Helyszíni és egymással **összekapcsolt hálózatok**: az Azure-ban futó, privát végpontokat használó EXPRESSROUTE, VPN-alagutakra és a virtuális hálózatokra épülő, az Azure-ban futtatott hozzáférési szolgáltatások. A szolgáltatás eléréséhez nincs szükség a ExpressRoute Microsoft-partnerek konfigurálására vagy az Internet bejárására. A privát hivatkozás biztonságos módszert biztosít a számítási feladatok Azure-ba történő átirányításához.
 
- **Védelem az adatszivárgás ellen**: a rendszer a teljes szolgáltatás helyett egy privát végpontot rendel egy Pásti erőforrás egy példányához. A felhasználók csak az adott erőforráshoz tudnak csatlakozni. A szolgáltatás bármely más erőforrásához való hozzáférés le van tiltva. Ez a mechanizmus védelmet nyújt az adatszivárgási kockázatokkal szemben. 
 
- **Globális elérhetőség**: kapcsolódjon a más régiókban futó szolgáltatásokhoz. A fogyasztó virtuális hálózata az A régióba kerülhet, és a B régióban található privát hivatkozás mögött található szolgáltatásokhoz is csatlakozhat.  
 
- **Kiterjesztheti saját szolgáltatásait**: az Azure-beli felhasználók számára is lehetővé teszi, hogy a szolgáltatás magántulajdonban legyen. Ha a szolgáltatást egy szabványos Azure Load Balancer mögött helyezi el, akkor azt privát hivatkozásként is engedélyezheti. A fogyasztó ezután közvetlenül kapcsolódhat a szolgáltatáshoz a saját virtuális hálózatában található privát végpont használatával. A kapcsolatkérelmeket a jóváhagyási hívási folyamat használatával kezelheti. Az Azure Private link a különböző Azure Active Directory bérlők számára tartozó felhasználók és szolgáltatások számára is működik. 

## <a name="availability"></a>Rendelkezésre állás 
 A következő táblázat felsorolja a privát kapcsolati szolgáltatásokat, valamint azokat a régiókat, ahol elérhetők. 

|Támogatott szolgáltatások  |Elérhető régiók | Néhány fontos megjegyzés | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|A standard Azure Load Balancer mögötti privát kapcsolati szolgáltatások | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió  | standard Load Balancer támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre privát hivatkozási szolgáltatást.](create-private-link-service-portal.md) |
| Azure Blob Storage (beleértve a Data Lake Storage Gen2)       |  Összes nyilvános régió<br/> Minden kormányzati régió       |  A fiók típusa általános célú v2-ben támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a blob Storage-hoz.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Összes nyilvános régió<br/> Minden kormányzati régió      | |   FE <br/> [Megtudhatja, hogyan hozhat létre Azure Files hálózati végpontokat.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Összes nyilvános régió      | |   FE <br/> [Megtudhatja, hogyan hozhat létre Azure Files hálózati végpontokat.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  Összes nyilvános régió<br/> Minden kormányzati régió       |  A fiók típusa általános célú v2-ben támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a várólista-tároláshoz.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  Összes nyilvános régió<br/> Minden kormányzati régió       |  A fiók típusa általános célú v2-ben támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Table Storage szolgáltatáshoz.](tutorial-private-endpoint-storage-portal.md)  |
|  Azure SQL Database         | Összes nyilvános régió <br/> Minden kormányzati régió<br/>Minden kínai régió      |  Proxy- [kapcsolatok házirendjében](../azure-sql/database/connectivity-architecture.md#connection-policy) támogatott | FE <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure SQL-hez](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics| Összes nyilvános régió <br/> Minden kormányzati régió |  Proxy- [kapcsolatok házirendjében](../azure-sql/database/connectivity-architecture.md#connection-policy) támogatott |FE <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure szinapszis Analytics szolgáltatáshoz.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Cosmos DB|  Összes nyilvános régió<br/> Minden kormányzati régió</br> Minden kínai régió | |FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Cosmos DBhoz.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL – egyetlen kiszolgáló         | Összes nyilvános régió <br/> Minden kormányzati régió<br/>Minden kínai régió     | A általános célú és a memória optimalizált díjszabási szintjein támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Database for PostgreSQLhoz.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió      |  | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Database for MySQLhoz.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió     |  | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Database for MariaDBhoz.](../mariadb/concepts-data-access-security-private-link.md)      |
|  Azure Digital Twins         | Az Azure Digital Twins által támogatott összes nyilvános régió     |  | Előnézet <br/> [Megtudhatja, hogyan hozhat létre privát végpontot az Azure Digital Twins szolgáltatáshoz.](../digital-twins/how-to-enable-private-link-portal.md)      |
|  Azure Key Vault         | Összes nyilvános régió<br/> Minden kormányzati régió      |  | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Key Vaulthoz.](../key-vault/general/private-link-service.md)   |
|Azure Kubernetes Service – Kubernetes API | Összes nyilvános régió      |  | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure Kubernetes szolgáltatáshoz.](../aks/private-clusters.md)   |
|Azure Search | Összes nyilvános régió <br/> Minden kormányzati régió | A szolgáltatás privát módban támogatott | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Searchhoz.](../search/service-create-private-endpoint.md)    |
|Azure Container Registry | Összes nyilvános régió<br/> Minden kormányzati régió    | A Container Registry prémium szintű csomagja támogatja. [Rétegek kiválasztása](../container-registry/container-registry-skus.md)| FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Container Registryhoz.](../container-registry/container-registry-private-link.md)   |
|Azure App Configuration | Összes nyilvános régió      |  | Előnézet  </br> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure app Configuration szolgáltatáshoz](../azure-app-configuration/concept-private-endpoint.md) |
|Azure Backup | Összes nyilvános régió<br/> Minden kormányzati régió   |  | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Backuphoz.](../backup/private-endpoints.md)   |
|Azure Event Hub | Összes nyilvános régió<br/>Minden kormányzati régió      |   | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure Event hub számára.](../event-hubs/private-link-service.md)  |
|Azure Service Bus | Minden nyilvános régió<br/>Minden kormányzati régió  | A prémium szintű Azure Service Bus támogatott. [Rétegek kiválasztása](../service-bus-messaging/service-bus-premium-messaging.md) | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Service Bushoz.](../service-bus-messaging/private-link-service.md)    |
|Azure Relay | Összes nyilvános régió      |  | Előnézet <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Relayhoz.](../azure-relay/private-link-service.md)  |
|Azure Event Grid| Összes nyilvános régió<br/> Minden kormányzati régió       |  | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Event Gridhoz.](../event-grid/network-security.md) |
|Azure-webalkalmazások | Összes nyilvános régió<br/> Észak-Kína 2 & 2. keleti régió    | PremiumV2, PremiumV3 vagy Function Premium csomaggal támogatott  | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure Web Appshoz.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Machine Learning | Összes nyilvános régió    |  | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Machine Learninghoz.](../machine-learning/how-to-configure-private-link.md)   |
| Azure Automation  | Összes nyilvános régió<br/> Minden kormányzati régió |  | Előnézet </br> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Automationhoz.](../automation/how-to/private-link-security.md)| |
| Azure IoT Hub | Összes nyilvános régió    |  | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure IoT Hubhoz.](../iot-hub/virtual-network-support.md) |
| Azure SignalR | USA KELETI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA,<br/>USA 2. nyugati régiója, minden kínai régió      |  | Előnézet   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot az Azure-jelzőhöz.](../azure-signalr/howto-private-endpoints.md)   |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Összes nyilvános régió      |  | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Monitorhoz.](../azure-monitor/logs/private-link-security.md)   | 
| Azure Batch | Az összes nyilvános régió kivételével: Közép-Németország, Északkelet-Németország <br/> Minden kormányzati régió  | | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Batchhoz.](../batch/private-connectivity.md) |
|Azure Data Factory | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió    | A hitelesítő adatokat egy Azure Key vaultban kell tárolni| FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Data Factoryhoz.](../data-factory/data-factory-private-link.md)   |
|Azure Managed Disks | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió    | [Kattintson ide az ismert korlátozásokhoz](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure Managed Diskshoz.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |



A legfrissebb értesítésekért keresse fel az [Azure Private link frissítései lapot](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Az Azure Private-hivatkozás Azure Monitorokkal való integrációt tartalmaz. Ez a kombináció lehetővé teszi a következőket:

 - Naplók archiválása egy Storage-fiókba.
 - Események továbbítása az Event hub-ba.
 - Azure Monitor-naplózás.

Azure Monitor a következő információkat érheti el: 
- **Privát végpont**: 
    - A magánhálózati végpont által feldolgozott adatértékek (be/ki)
 
- **Magánhálózati kapcsolati szolgáltatás**:
    - A privát kapcsolati szolgáltatás által feldolgozott adatértékek (be/ki)
    - NAT-port elérhetősége  
 
## <a name="pricing"></a>Díjszabás   
A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Gyakori kérdések  
Gyakori kérdések: [Azure Private link – gyakori kérdések](private-link-faq.md).
 
## <a name="limits"></a>Korlátok  
A korlátokat lásd: [Azure Private link Limits](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>szolgáltatói szerződés
SLA esetén lásd: [SLA az Azure Private linkhez](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: privát végpont létrehozása Azure Portal használatával](create-private-endpoint-portal.md)
- [Rövid útmutató: privát link szolgáltatás létrehozása a Azure Portal használatával](create-private-link-service-portal.md)
