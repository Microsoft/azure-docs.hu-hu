---
title: Azure Private-kapcsolat elérhetősége
description: Ebből a cikkből megtudhatja, hogy mely Azure-szolgáltatások támogatják a privát hivatkozást.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 26485c84749b7d4c91159476b3f683c2b0f3831b
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555427"
---
# <a name="azure-private-link-availability"></a>Azure Private-kapcsolat elérhetősége

Az Azure Private link lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett felhasználói/partneri szolgáltatások elérését a virtuális hálózat [privát végpontján](private-endpoint-overview.md) keresztül. 

> [!IMPORTANT]
> Az Azure Private link már általánosan elérhető. A privát végpont és a magánhálózati kapcsolat szolgáltatás (a standard Load Balancer mögötti szolgáltatás) általánosan elérhető. A különböző Azure-beli Pásti különböző időpontokban fog bejelentkezni az Azure Private-hivatkozásba. Az ismert korlátozásokért lásd: [privát végpont](private-endpoint-overview.md#limitations) és [privát kapcsolat szolgáltatás](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Szolgáltatás rendelkezésre állása

A következő táblázatok felsorolják a privát kapcsolati szolgáltatásokat és azokat a régiókat, ahol elérhetők. 

### <a name="ai--machine-learning"></a>Mesterséges intelligencia és gépi tanulás

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Összes nyilvános régió    |  | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Machine Learninghoz.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Elemzés

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Összes nyilvános régió <br/> Minden kormányzati régió |  Proxy- [kapcsolatok házirendjében](../azure-sql/database/connectivity-architecture.md#connection-policy) támogatott |FE <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure szinapszis Analytics szolgáltatáshoz.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | Összes nyilvános régió<br/>Minden kormányzati régió      |   | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure Event hub számára.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Összes nyilvános régió      |  | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Monitorhoz.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió    | A hitelesítő adatokat egy Azure Key vaultban kell tárolni| FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Data Factoryhoz.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Compute

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Összes nyilvános régió      |  | Előnézet  </br> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure app Configuration szolgáltatáshoz](../azure-app-configuration/concept-private-endpoint.md) |
|Azure által felügyelt lemezek | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió    | [Az ismert korlátozások kiválasztása](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure Managed Diskshoz.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Tárolók

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Összes nyilvános régió<br/> Minden kormányzati régió    | A Container Registry prémium szintű csomagja támogatja. [Rétegek kiválasztása](../container-registry/container-registry-skus.md)| FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Container Registryhoz.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service – Kubernetes API | Összes nyilvános régió      |  | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure Kubernetes szolgáltatáshoz.](../aks/private-clusters.md)   |

### <a name="databases"></a>Adatbázisok

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | Összes nyilvános régió <br/> Minden kormányzati régió<br/>Minden kínai régió      |  Proxy- [kapcsolatok házirendjében](../azure-sql/database/connectivity-architecture.md#connection-policy) támogatott | FE <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure SQL-hez](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Összes nyilvános régió<br/> Minden kormányzati régió</br> Minden kínai régió | |FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Cosmos DBhoz.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL – egyetlen kiszolgáló         | Összes nyilvános régió <br/> Minden kormányzati régió<br/>Minden kínai régió     | A általános célú és a memória optimalizált díjszabási szintjein támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Database for PostgreSQLhoz.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió      |  | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Database for MySQLhoz.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió     |  | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Database for MariaDBhoz.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integráció

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Összes nyilvános régió<br/> Minden kormányzati régió       |  | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Event Gridhoz.](../event-grid/network-security.md) |
|Azure Service Bus | Minden nyilvános régió<br/>Minden kormányzati régió  | A prémium szintű Azure Service Bus támogatott. [Rétegek kiválasztása](../service-bus-messaging/service-bus-premium-messaging.md) | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Service Bushoz.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Eszközök internetes hálózata (IoT)

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Összes nyilvános régió    |  | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure IoT Hubhoz.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Az Azure Digital Twins által támogatott összes nyilvános régió     |  | Előnézet <br/> [Megtudhatja, hogyan hozhat létre privát végpontot az Azure Digital Twins szolgáltatáshoz.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Felügyelet és cégirányítás

| Támogatott szolgáltatások | Elérhető régiók | További szempontok | Állapot  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | Összes nyilvános régió<br/> Minden kormányzati régió |  | Előnézet </br> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Automationhoz.](../automation/how-to/private-link-security.md)|
|Azure Backup | Összes nyilvános régió<br/> Minden kormányzati régió   |  | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Backuphoz.](../backup/private-endpoints.md)   |

### <a name="security"></a>Biztonság

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Összes nyilvános régió<br/> Minden kormányzati régió      |  | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Key Vaulthoz.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Tárolás
|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob Storage (beleértve a Data Lake Storage Gen2)       |  Összes nyilvános régió<br/> Minden kormányzati régió       |  A fiók típusa általános célú v2-ben támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a blob Storage-hoz.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Összes nyilvános régió<br/> Minden kormányzati régió      | |   FE <br/> [Megtudhatja, hogyan hozhat létre Azure Files hálózati végpontokat.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Összes nyilvános régió      | |   FE <br/> [Megtudhatja, hogyan hozhat létre Azure Files hálózati végpontokat.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  Összes nyilvános régió<br/> Minden kormányzati régió       |  A fiók típusa általános célú v2-ben támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a várólista-tároláshoz.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  Összes nyilvános régió<br/> Minden kormányzati régió       |  A fiók típusa általános célú v2-ben támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Table Storage szolgáltatáshoz.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Az összes nyilvános régió kivételével: Közép-Németország, Északkelet-Németország <br/> Minden kormányzati régió  | | FE <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Batchhoz.](../batch/private-connectivity.md) |

### <a name="web"></a>Webes
|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | USA KELETI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA,<br/>USA 2. nyugati régiója, minden kínai régió      |  | Előnézet   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot az Azure-jelzőhöz.](../azure-signalr/howto-private-endpoints.md)   |
|Azure-webalkalmazások | Összes nyilvános régió<br/> Észak-Kína 2 & 2. keleti régió    | PremiumV2, PremiumV3 vagy Function Premium csomaggal támogatott  | FE   <br/> [Ismerje meg, hogyan hozhat létre privát végpontot az Azure Web Appshoz.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Összes nyilvános régió <br/> Minden kormányzati régió | A szolgáltatás privát módban támogatott | FE   <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Searchhoz.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Összes nyilvános régió      |  | Előnézet <br/> [Megtudhatja, hogyan hozhat létre saját végpontot a Azure Relayhoz.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Magánhálózati kapcsolati szolgáltatás standard Load balancerrel

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|A standard Azure Load Balancer mögötti privát kapcsolati szolgáltatások | Összes nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió  | standard Load Balancer támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre privát hivatkozási szolgáltatást.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Következő lépések

További információ az Azure Private link Service-ről:
- [Mi az az Azure privát kapcsolat?](private-link-overview.md)
- [Privát végpont létrehozása az Azure Portal segítségével](create-private-endpoint-portal.md)
