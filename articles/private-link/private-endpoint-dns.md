---
title: Az Azure privát végpont DNS-konfigurációja
description: Az Azure Private Endpoint DNS konfigurációjának megismerése
services: private-link
author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: allensu
ms.openlocfilehash: edb6748009ef9660c020c77e411ce55f60b45791
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124601"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Az Azure privát végpont DNS-konfigurációja

Fontos, hogy a DNS-beállításokat helyesen konfigurálja a magánhálózati végpont IP-címének a kapcsolódási karakterlánc teljes tartománynevére (FQDN) való feloldásához.

Előfordulhat, hogy a meglévő Microsoft Azure szolgáltatások már rendelkeznek DNS-konfigurációval egy nyilvános végponthoz. Ezt a konfigurációt felül kell bírálni a privát végpont használatával történő kapcsolódáshoz. 
 
A magánhálózati végponthoz társított hálózati adapter tartalmazza a DNS konfigurálásához szükséges adatokat. A hálózati adapter információi tartalmazzák a privát kapcsolati erőforrás teljes tartománynevét és magánhálózati IP-címeit. 
 
A következő beállításokkal konfigurálhatja a magánhálózati végpontok DNS-beállításait: 
- **A gazda fájl használata (csak teszteléshez ajánlott)**. A virtuális gépen lévő gazdagépen a DNS felülbírálására is használható.  
- **Használjon privát DNS-zónát**. Privát DNS- [zónák](../dns/private-dns-privatednszone.md) használatával felülbírálhatja egy privát végpont DNS-feloldását. A saját DNS-zónák összekapcsolhatók a virtuális hálózattal bizonyos tartományok feloldásához.
- **Használja a DNS-továbbítót (nem kötelező)**. A DNS-továbbító használatával felülbírálhatja egy privát kapcsolati erőforrás DNS-feloldását. Hozzon létre egy DNS-továbbítási szabályt úgy, hogy a virtuális hálózatban üzemeltetett [DNS-kiszolgálón](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) a saját DNS-zónát használja.

> [!IMPORTANT]
> Nem ajánlott felülbírálni egy olyan zónát, amely aktívan használatban van a nyilvános végpontok feloldásához. Az erőforrásokhoz való csatlakozások nem lesznek képesek megfelelően feloldani a nyilvános DNS-re való DNS-továbbítás nélkül. A problémák elkerülése érdekében hozzon létre egy másik tartománynevet, vagy kövesse a javasolt nevet az alábbi szolgáltatásokhoz. 

## <a name="azure-services-dns-zone-configuration"></a>Azure-szolgáltatások DNS-zóna konfigurációja
Az Azure egy kanonikus név DNS-rekordját (CNAME) hozza létre a nyilvános DNS-ben. A CNAME rekord átirányítja a felbontást a privát tartomány nevére. Felülbírálhatja a felbontást a privát végpontok magánhálózati IP-címével. 
 
Az alkalmazásoknak nem kell módosítaniuk a kapcsolódási URL-címet. Nyilvános DNS-szolgáltatás feloldásakor a DNS-kiszolgáló feloldja a magánhálózati végpontokat. A folyamat nem érinti a meglévő alkalmazásokat. 

> [!IMPORTANT]
> A magánhálózati DNS-zónát egy adott típushoz már használó magánhálózatok csak akkor csatlakozhatnak nyilvános erőforrásokhoz, ha nem rendelkeznek magánhálózati végponti kapcsolatokkal, ellenkező esetben a DNS-zónához tartozó DNS-konfiguráció szükséges a DNS-feloldási folyamat befejezéséhez. 

Az Azure-szolgáltatások esetében használja az ajánlott zónák nevét az alábbi táblázatban leírtak szerint:

| Privát kapcsolat erőforrástípus/alerőforrása |saját DNS zóna neve | Nyilvános DNS-zónák továbbítói |
|---|---|---|
| Azure Automation/(Microsoft. Automation/automationAccounts)/webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (Microsoft. SQL/Servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure szinapszis Analytics (Microsoft. SQL/Servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/tábla (tábla, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/üzenetsor (Üzenetsor, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/fájl (fájl, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake fájlrendszer Gen2 (Microsoft. Storage/storageAccounts)/Data Lake fájlrendszer Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/tábla | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL – egyetlen kiszolgáló (Microsoft. DBforPostgreSQL/Servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft. DBforMySQL/Servers)/portra beállított mysqlserver | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/Servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft. kulcstartó/tárolók)/tároló | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service – Kubernetes API (Microsoft. Tárolószolgáltatás/managedClusters)/felügyelet | privatelink. {Region}. azmk8s. IO | {Region}. azmk8s. IO |
| Azure Search (Microsoft. Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft. ContainerRegistry/nyilvántartók)/beállításjegyzék | privatelink.azurecr.io | azurecr.io |
| Azure-alkalmazás konfigurációja (Microsoft. AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft. Recoveryservices szolgáltatónál/Vaults)/tároló | privatelink. {Region}. backup. windowsazure. com | {Region}. backup. windowsazure. com |
| Azure Site Recovery (Microsoft. Recoveryservices szolgáltatónál/Vaults)/tároló | {Region}. privatelink. siterecovery. windowsazure. com | {Region}. hypervrecoverymanager. windowsazure. com |
| Azure Event Hubs (Microsoft. EventHub/névterek)/névtér | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft. ServiceBus/névterek)/névtér | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net<br/><sup>1</sup> . privatelink.servicebus.Windows.net | azure-devices.net<br/>servicebus.windows.net |
| Azure Relay (Microsoft. Relay/névterek)/névtér | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft. EventGrid/témák)/témakör | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft. EventGrid/Domains)/tartomány | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft. Web/Sites)/helyek | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/workspaces)/amlworkspace | privatelink.api.azureml.ms<br/>privatelink.notebooks.azure.net | api.azureml.ms<br/>notebooks.azure.net<br/>instances.azureml.ms<br/>aznbcontent.net |
| Jelző (Microsoft. SignalRService/szignáló)/jelző | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft. bepillantások/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (Microsoft. CognitiveServices/accounts)/fiók | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/gyárak)/dataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/gyárak)/portál |  privatelink.azure.com  |  azure.com  |
| Azure cache for Redis (Microsoft. cache/Redis)/redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup> A IoT Hub beépített Event hub-kompatibilis végpontjának használata. További információ: [IoT hub beépített végpontjának Private link support](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint)

### <a name="china"></a>Kína

| Privát kapcsolat erőforrástípus/alerőforrása |saját DNS zóna neve | Nyilvános DNS-zónák továbbítói |
|---|---|---|
| Azure SQL Database (Microsoft. SQL/Servers)/SQL Server | privatelink.database.chinacloudapi.cn | database.chinacloudapi.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.cn | documents.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.cn | mongo.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.cn | cassandra.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.cn | gremlin.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/tábla | privatelink.table.cosmos.azure.cn | table.cosmos.azure.cn |
| Azure Database for PostgreSQL – egyetlen kiszolgáló (Microsoft. DBforPostgreSQL/Servers)/postgresqlServer | privatelink.postgres.database.chinacloudapi.cn | postgres.database.chinacloudapi.cn |
| Azure Database for MySQL (Microsoft. DBforMySQL/Servers)/portra beállított mysqlserver | privatelink.mysql.database.chinacloudapi.cn  | mysql.database.chinacloudapi.cn  |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/Servers)/mariadbServer | privatelink.mariadb.database.chinacloudapi.cn | mariadb.database.chinacloudapi.cn |


## <a name="dns-configuration-scenarios"></a>DNS-konfigurációs forgatókönyvek

A szolgáltatások teljes tartományneve automatikusan feloldja a nyilvános IP-címet. A magánhálózati végpont magánhálózati IP-címére való feloldáshoz módosítsa a DNS-konfigurációt.

A DNS egy kritikus összetevő, amellyel az alkalmazás megfelelően működik a magánhálózati végpont IP-címének feloldásával.

A beállítások alapján a következő forgatókönyvek érhetők el az integrált DNS-feloldással:

- [Virtuális hálózati munkaterhelések egyéni DNS-kiszolgáló nélkül](#virtual-network-workloads-without-custom-dns-server)
- [DNS-továbbítót használó helyszíni számítási feladatok](#on-premises-workloads-using-a-dns-forwarder)
- [Virtuális hálózat és helyszíni számítási feladatok DNS-továbbító használatával](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> [Azure Firewall DNS-proxy](../firewall/dns-settings.md#dns-proxy) DNS-továbbítóként használható a helyszíni [munkaterhelésekhez](#on-premises-workloads-using-a-dns-forwarder) és a [virtuális hálózati munkaterhelésekhez DNS-továbbító használatával](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder).

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Virtuális hálózati munkaterhelések egyéni DNS-kiszolgáló nélkül

Ez a konfiguráció az egyéni DNS-kiszolgáló nélküli virtuális hálózati munkaterhelésekhez megfelelő. Ebben az esetben az ügyfél az Azure által biztosított DNS-szolgáltatás [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)kérdezi le a MAGÁNHÁLÓZATI végpont IP-címét. Azure DNS a magánhálózati DNS-zónák DNS-feloldására lesz felelős.

> [!NOTE]
> Ez a forgatókönyv a Azure SQL Database javasolt magánhálózati DNS-zónát használja. Más szolgáltatások esetében a modellt a következő hivatkozással módosíthatja: [Azure-szolgáltatások DNS-zóna konfigurációja](#azure-services-dns-zone-configuration).

A megfelelő konfigurálásához a következő erőforrásokra van szükség:

- Ügyfél virtuális hálózata

- Saját DNS zóna [Privatelink.database.Windows.net](../dns/private-dns-privatednszone.md) [egy rekord beírásával](../dns/dns-zones-records.md#record-types)

- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

Az alábbi képernyőfelvételen a virtuális hálózati munkaterhelések DNS-feloldási folyamata látható a magánhálózati DNS-zónával:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Egyetlen virtuális hálózat és az Azure által biztosított DNS":::

Ezt a modellt kiterjesztheti ugyanahhoz a privát végponthoz társított virtuális hálózatokra is. [Új virtuális hálózati kapcsolatok hozzáadása](../dns/private-dns-virtual-network-links.md) a magánhálózati DNS-zónához az összes társ virtuális hálózat számára.

> [!IMPORTANT]
> Ehhez a konfigurációhoz egyetlen privát DNS-zónára van szükség. A különböző virtuális hálózatok azonos nevű zónáinak létrehozásához kézi műveletekre van szükség a DNS-rekordok egyesítéséhez.

> [!IMPORTANT]
> Ha egy másik előfizetéshez tartozó sugaras modellben használ privát végpontot, akkor ugyanazt a saját DNS-zónát használja a központban.

Ebben a forgatókönyvben van egy [hub és küllős](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) hálózati topológia. A küllős hálózatok megosztanak egy privát végpontot. A küllős virtuális hálózatok ugyanahhoz a magánhálózati DNS-zónához vannak társítva. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub és küllő az Azure által biztosított DNS-sel":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>DNS-továbbítót használó helyszíni számítási feladatok

A privát végpontok teljes tartománynevének feloldásához a helyszíni munkaterhelések esetén használjon DNS-továbbítót az Azure szolgáltatás [nyilvános DNS-zónájának](#azure-services-dns-zone-configuration) az Azure-ban való feloldásához.

Az alábbi forgatókönyv olyan helyszíni hálózatra mutat, amely DNS-továbbítóval rendelkezik az Azure-ban. Ez a továbbító a DNS-lekérdezéseket kiszolgáló szintű továbbítón keresztül oldja fel az Azure által megadott DNS- [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). 

> [!NOTE]
> Ez a forgatókönyv a Azure SQL Database javasolt magánhálózati DNS-zónát használja. Más szolgáltatások esetében a modellt a következő hivatkozással módosíthatja: [Azure-szolgáltatások DNS-zóna konfigurációja](#azure-services-dns-zone-configuration).

A megfelelő konfigurálásához a következő erőforrásokra van szükség:

- Helyszíni hálózat
- A helyszíni környezethez [csatlakoztatott](/azure/architecture/reference-architectures/hybrid-networking/) virtuális hálózat
- DNS-továbbító üzembe helyezése az Azure-ban 
- Saját DNS zónák [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md) [](../dns/dns-zones-records.md#record-types)
- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

A következő ábra a DNS-feloldási sorozatot mutatja be egy helyszíni hálózatról. A konfiguráció az Azure-ban üzembe helyezett DNS-továbbítót használja. A megoldás egy [virtuális hálózathoz társított](../dns/private-dns-virtual-network-links.md)privát DNS-zónából áll:

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Helyszíni Azure DNS használatával":::

Ez a konfiguráció bővíthető olyan helyszíni hálózatra, amely már rendelkezik DNS-megoldással. A helyszíni DNS-megoldás úgy van konfigurálva, hogy a DNS-forgalmat a Azure DNS egy [feltételes továbbítón](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)keresztül továbbítsa. A feltételes továbbító az Azure-ban üzembe helyezett DNS-továbbítóra hivatkozik.

> [!NOTE]
> Ez a forgatókönyv a Azure SQL Database javasolt magánhálózati DNS-zónát használja. Más szolgáltatások esetében a modellt a következő hivatkozással módosíthatja: [Azure-szolgáltatások DNS-zóna konfigurációja](#azure-services-dns-zone-configuration)

A megfelelő konfigurálásához a következő erőforrásokra van szükség:

- Helyi hálózat egyéni DNS-megoldással 
- A helyszíni környezethez [csatlakoztatott](/azure/architecture/reference-architectures/hybrid-networking/) virtuális hálózat
- DNS-továbbító üzembe helyezése az Azure-ban
- Saját DNS zónák [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md) [](../dns/dns-zones-records.md#record-types)
- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

A következő ábra a helyszíni hálózat DNS-feloldását szemlélteti. A DNS-feloldás feltételesen továbbítva van az Azure-ba. A megoldás egy [virtuális hálózathoz társított](../dns/private-dns-virtual-network-links.md)privát DNS-zónából áll.

> [!IMPORTANT]
> A feltételes továbbítást az ajánlott [nyilvános DNS-zónák továbbítójának](#azure-services-dns-zone-configuration)kell végrehajtania. Például: `database.windows.net` a **privatelink**. database.Windows.net helyett.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Helyszíni továbbítás Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Virtuális hálózat és helyszíni számítási feladatok DNS-továbbító használatával

A virtuális és a helyszíni hálózatokból származó privát végpontokhoz hozzáférő munkaterhelések esetén a DNS-továbbító használatával oldja fel az Azure-ban üzembe helyezett Azure [-szolgáltatás nyilvános DNS-zónáját](#azure-services-dns-zone-configuration) .

Az alábbi forgatókönyv az Azure-beli virtuális hálózatokkal rendelkező helyszíni hálózatra mutat. Mindkét hálózat egy megosztott hub-hálózatban található privát végponthoz fér hozzá.

A DNS-továbbító feladata az összes DNS-lekérdezés feloldása egy kiszolgálói szintű továbbítón keresztül az Azure által biztosított DNS-szolgáltatás [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Ehhez a konfigurációhoz egyetlen privát DNS-zónára van szükség. A helyszíni és a [társ virtuális hálózatokból](../virtual-network/virtual-network-peering-overview.md) végrehajtott összes ügyfélkapcsolatnak ugyanazt a magánhálózati DNS-zónát kell használnia.

> [!NOTE]
> Ez a forgatókönyv a Azure SQL Database javasolt magánhálózati DNS-zónát használja. Más szolgáltatások esetében a modellt a következő hivatkozással módosíthatja: [Azure-szolgáltatások DNS-zóna konfigurációja](#azure-services-dns-zone-configuration).

A megfelelő konfigurálásához a következő erőforrásokra van szükség:

- Helyszíni hálózat
- A helyszíni környezethez [csatlakoztatott](/azure/architecture/reference-architectures/hybrid-networking/) virtuális hálózat
- [Egyenrangú virtuális hálózat](../virtual-network/virtual-network-peering-overview.md) 
- DNS-továbbító üzembe helyezése az Azure-ban
- Saját DNS zónák [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md) [](../dns/dns-zones-records.md#record-types)
- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

Az alábbi ábrán a hálózatok, a helyszíni és a virtuális hálózatok DNS-feloldása látható. A megoldás DNS-továbbítót használ. A megoldás egy [virtuális hálózathoz társított](../dns/private-dns-virtual-network-links.md)privát DNS-zónából áll:

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Hibrid forgatókönyv":::

## <a name="next-steps"></a>Következő lépések
- [További tudnivalók a privát végpontokról](private-endpoint-overview.md)
