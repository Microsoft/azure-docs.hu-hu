---
title: Azure Private Link rendelkezésre állás
description: Ebből a cikkből megtudhatja, hogy mely Azure-szolgáltatások támogatják a Private Link.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 866eb9feb152c0094cd5281fe4820ccc4589386f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778318"
---
# <a name="azure-private-link-availability"></a>Azure Private Link rendelkezésre állás

Azure Private Link lehetővé teszi az Azure PaaS-szolgáltatások (például az Azure Storage és az SQL Database) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások privát végponton keresztüli hozzáférését [a](private-endpoint-overview.md) virtuális hálózatban. 

> [!IMPORTANT]
> Azure Private Link már általánosan elérhető. A privát végpont és Private Link szolgáltatás (a standard terheléselosztás mögötti szolgáltatás) általánosan elérhető. A különböző Azure PaaS-szolgáltatások Azure Private Link ütemezés szerint fognak üzembe ásni. Az ismert korlátozásokért lásd: [Privát végpont és](private-endpoint-overview.md#limitations) Private Link [Szolgáltatás.](private-link-service-overview.md#limitations) 

## <a name="service-availability"></a>Szolgáltatás rendelkezésre állása

Az alábbi táblázatok a Private Link és a régiókat sorolják fel, ahol elérhetők. 

### <a name="ai--machine-learning"></a>Mesterséges intelligencia és gépi tanulás

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Minden nyilvános régió    |  | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Elemzés

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Minden nyilvános régió <br/> Minden kormányzati régió |  Proxykapcsolati [szabályzat esetén támogatott](../azure-sql/database/connectivity-architecture.md#connection-policy) |FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | Minden nyilvános régió<br/>Minden kormányzati régió      |   | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot az Azure Event Hubhoz.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics-& Application Insights) | Minden nyilvános régió      |  | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Minden nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió    | A hitelesítő adatokat egy Azure-kulcstartóban kell tárolni| FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Compute

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Minden nyilvános régió      |  | Előnézet  </br> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure App Configuration](../azure-app-configuration/concept-private-endpoint.md) |
|Azure által felügyelt lemezek | Minden nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió    | [Válassza az ismert korlátozásokat](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot az Azure Managed Disks.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Tárolók

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Minden nyilvános régió<br/> Minden kormányzati régió    | A tároló-beállításjegyzék prémium szintű csomagja támogatja. [Szint kiválasztása](../container-registry/container-registry-skus.md)| FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service – Kubernetes API | Minden nyilvános régió      |  | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Kubernetes Service.](../aks/private-clusters.md)   |

### <a name="databases"></a>Adatbázisok

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | Minden nyilvános régió <br/> Minden kormányzati régió<br/>Az összes kínai régió      |  Proxykapcsolati [szabályzat esetén támogatott](../azure-sql/database/connectivity-architecture.md#connection-policy) | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure SQL](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Minden nyilvános régió<br/> Minden kormányzati régió</br> Az összes kínai régió | |FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL – Egyetlen kiszolgáló         | Minden nyilvános régió <br/> Minden kormányzati régió<br/>Az összes kínai régió     | Támogatott a általános célú memóriaoptimalált tarifacsomagok esetében | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Database for PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Minden nyilvános régió<br/> Minden kormányzati régió<br/>Az összes kínai régió      |  | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Database for MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Minden nyilvános régió<br/> Minden kormányzati régió<br/>Az összes kínai régió     |  | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integráció

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Minden nyilvános régió<br/> Minden kormányzati régió       |  | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Event Grid.](../event-grid/network-security.md) |
|Azure Service Bus | Minden nyilvános régió<br/>Minden kormányzati régió  | A prémium szintű csomaggal Azure Service Bus. [Szint kiválasztása](../service-bus-messaging/service-bus-premium-messaging.md) | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Eszközök internetes hálózata (IoT)

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Minden nyilvános régió    |  | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Az összes nyilvános régió, amelyet a Azure Digital Twins     |  | Előnézet <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Digital Twins.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Felügyelet és cégirányítás

| Támogatott szolgáltatások | Elérhető régiók | További szempontok | Állapot  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | Minden nyilvános régió<br/> Minden kormányzati régió |  | Előnézet </br> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Automation.](../automation/how-to/private-link-security.md)|
|Azure Backup | Minden nyilvános régió<br/> Minden kormányzati régió   |  | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Backup.](../backup/private-endpoints.md)   |

### <a name="security"></a>Biztonság

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Minden nyilvános régió<br/> Minden kormányzati régió      |  | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Tárolás
|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob Storage (beleértve a Data Lake Storage Gen2)       |  Minden nyilvános régió<br/> Minden kormányzati régió       |  A V2 fiók általános célú támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Blob Storage-hoz.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Minden nyilvános régió<br/> Minden kormányzati régió      | |   FE <br/> [Útmutató hálózati Azure Files létrehozásához.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Minden nyilvános régió      | |   FE <br/> [Útmutató hálózati Azure Files létrehozásához.](../storage/file-sync/file-sync-networking-endpoints.md)   |
| Azure Queue Storage       |  Minden nyilvános régió<br/> Minden kormányzati régió       |  A V2 fiók általános célú támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Queue Storage-hoz.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  Minden nyilvános régió<br/> Minden kormányzati régió       |  A V2 fiók általános célú támogatott | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Table Storage-hoz.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Az összes nyilvános régió, kivéve: Közép-Németország, Északkelet-Németország <br/> Minden kormányzati régió  | | FE <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Webes
|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | USA KELETI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA,<br/>USA 2. NYUGATI RÉGIÓja, Minden Kína régió      |  | Előnézet   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot az Azure SignalR számára.](../azure-signalr/howto-private-endpoints.md)   |
|Azure-webalkalmazások | Minden nyilvános régió<br/> Kína 2. északi régiója & 2. keleti régiója    | PremiumV2, PremiumV3 vagy Function Premium csomaggal támogatott  | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot az Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Minden nyilvános régió <br/> Minden kormányzati régió | A privát módú szolgáltatásokkal támogatott | FE   <br/> [Megtudhatja, hogyan hozhat létre privát végpontot az Azure Search szolgáltatáshoz.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Minden nyilvános régió      |  | Előnézet <br/> [Megtudhatja, hogyan hozhat létre privát végpontot a Azure Relay.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Private Link standard terheléselosztási szolgáltatással

|Támogatott szolgáltatások  |Elérhető régiók | További szempontok | Állapot  |
|:-------------------|:-----------------|:----------------|:--------|
|Private Link standard szintű szolgáltatások mögötti Azure Load Balancer | Minden nyilvános régió<br/> Minden kormányzati régió<br/>Minden kínai régió  | Támogatott a standard Load Balancer | FE <br/> [Ismerje meg, hogyan hozhat létre privát kapcsolati szolgáltatást.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Következő lépések

További információ a Azure Private Link szolgáltatásról:
- [Mi az az Azure privát kapcsolat?](private-link-overview.md)
- [Privát végpont létrehozása az Azure Portal segítségével](create-private-endpoint-portal.md)
