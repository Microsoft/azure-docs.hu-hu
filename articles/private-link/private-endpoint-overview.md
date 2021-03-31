---
title: Mi az az Azure-beli privát végpont?
description: További tudnivalók az Azure Private-végpontról
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 79d21549e7234e4ee342776466f8d3d8ced5f08c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508809"
---
# <a name="what-is-azure-private-endpoint"></a>Mi az az Azure privát végpont?

Az Azure privát végpont egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz. A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így lényegében bekapcsolja a szolgáltatást a virtuális hálózatba. A szolgáltatás olyan Azure-szolgáltatás lehet, mint például az Azure Storage, az Azure Cosmos DB, az SQL vagy a saját [privát kapcsolati szolgáltatása](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Magánhálózati végpont tulajdonságai 
 A privát végpontok a következő tulajdonságokat határozzák meg: 


|Tulajdonság  |Leírás |
|---------|---------|
|Név    |    Az erőforráscsoporthoz tartozó egyedi név.      |
|Alhálózat    |  A magánhálózati IP-címek virtuális hálózatról való üzembe helyezésére és lefoglalására szolgáló alhálózat. Az alhálózatra vonatkozó követelményekért tekintse meg a jelen cikk korlátozások című szakaszát.         |
|Magánhálózati kapcsolat erőforrása    |   A privát kapcsolati erőforrás az erőforrás-AZONOSÍTÓval vagy az aliassal való kapcsolódáshoz az elérhető típusok listájából. Az erőforrásnak elküldett összes forgalomhoz egyedi hálózati azonosító jön létre.       |
|Cél alerőforrás   |      A kapcsolódáshoz használandó alerőforrás. Minden egyes privát kapcsolati erőforrástípus különböző beállításokkal választható ki a megfelelő beállítás alapján.    |
|A kapcsolatok jóváhagyási módszere    |  Automatikus vagy manuális. Az Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedélyei alapján a privát végpontok automatikusan is jóváhagyva lesznek. Ha az Azure RBAC nélküli privát kapcsolati erőforráshoz próbál csatlakozni, a manuális módszerrel engedélyezheti, hogy az erőforrás tulajdonosa jóváhagyja a kapcsolatot.        |
|Kérelem üzenete     |  Megadhat egy üzenetet, hogy a kért kapcsolatok manuálisan legyenek jóváhagyva. Ez az üzenet egy adott kérelem azonosítására szolgál.        |
|A kapcsolat állapota   |   Írásvédett tulajdonság, amely megadja, hogy a magánhálózati végpont aktív-e. A forgalom csak a jóváhagyott állapotú privát végpontok használatával küldhető el. További elérhető állapotok: <br>-**Jóváhagyva**: a rendszer automatikusan vagy manuálisan jóváhagyta a kapcsolódást, és készen áll a használatra.</br><br>-**Függőben**: a kapcsolat manuálisan lett létrehozva, és a privát hivatkozás erőforrás-tulajdonosa jóváhagyásra vár.</br><br>-**Visszautasítva**: a magánhálózati kapcsolat erőforrásának tulajdonosa elutasította a kapcsolatot.</br><br>-Kapcsolat **megszakítva**: a magánhálózati kapcsolat erőforrásának tulajdonosa eltávolította a kapcsolatot. A privát végpont informatív lesz, és törölni kell a tisztításhoz. </br>|

Íme néhány fontos adat a privát végpontokról: 
- A privát végpont lehetővé teszi, hogy az azonos VNet, regionálisan egymással virtuális hálózatok, globálisan összekapcsolt virtuális hálózatok és helyszíni kapcsolaton keresztüli kapcsolatot létesítsen a [VPN](https://azure.microsoft.com/services/vpn-gateway/) -t vagy [expressz útvonalat](https://azure.microsoft.com/services/expressroute/) és szolgáltatásokat használó privát kapcsolaton keresztül.
 
- A hálózati kapcsolatokat csak a privát végponthoz csatlakozó ügyfelek indíthatják el, a szolgáltatók nem rendelkeznek útválasztási konfigurációval, hogy kapcsolatokat kezdeményezzenek a szolgáltatás felhasználói számára. A kapcsolatok csak egyetlen irányban hozhatók létre.

- Privát végpont létrehozásakor a rendszer az erőforrás életciklusához is létrehoz egy írásvédett hálózati adaptert. A csatoló dinamikusan magánhálózati IP-címeket kap az alhálózatból, amely leképezi a magánhálózati kapcsolat erőforrását. A magánhálózati IP-cím értéke változatlan marad a magánhálózati végpont teljes életciklusa során.
 
- A magánhálózati végpontot a virtuális hálózattal megegyező régióban kell telepíteni. 
 
- A magánhálózati kapcsolati erőforrás a virtuális hálózat és a magánhálózati végpontok egy másik régiójában is üzembe helyezhető.
 
- Több privát végpont is létrehozható ugyanazzal a privát kapcsolati erőforrással. Egy közös DNS-kiszolgáló konfigurációját használó egyetlen hálózat esetében az ajánlott eljárás egyetlen magánhálózati végpont használata egy adott privát kapcsolati erőforráshoz a duplikált bejegyzések vagy a DNS-feloldási ütközések elkerülése érdekében. 
 
- Több privát végpont is létrehozható ugyanazon a virtuális hálózaton belül ugyanazon vagy különböző alhálózatokon. Az előfizetésben létrehozható privát végpontok száma korlátozott. Részletekért lásd: az [Azure korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- A privát kapcsolati erőforrás előfizetését is regisztrálni kell a Microsoft. Network erőforrás-szolgáltatóban. Részletekért lásd: [Azure Resource Providers](../azure-resource-manager/management/resource-providers-and-types.md).

 
## <a name="private-link-resource"></a>Magánhálózati kapcsolat erőforrása 
A privát kapcsolati erőforrás egy adott privát végpont célját célozza meg. Az alábbi lista a rendelkezésre álló privát kapcsolati erőforrástípusok listáját tartalmazza: 
 
|Privát hivatkozás erőforrásának neve  |Erőforrás típusa   |Alerőforrások  |
|---------|---------|---------|
|**Privát kapcsolati szolgáltatás** (saját szolgáltatás)   |  Microsoft. Network/privateLinkServices       | üres |
|**Azure Automation** |  Microsoft. Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Azure SQL Database** | Microsoft. SQL/kiszolgálók    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft. SQL/kiszolgálók    |  SQL Server (sqlServer)        | 
|**Azure Storage**  | Microsoft. Storage/storageAccounts    |  BLOB (blob, blob_secondary)<BR> Tábla (tábla, table_secondary)<BR> Üzenetsor (Üzenetsor, queue_secondary)<BR> Fájl (fájl, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft. Storage/storageAccounts    |  BLOB (blob, blob_secondary)<BR> Data Lake fájlrendszer Gen2 (DFS, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft. AzureCosmosDB/databaseAccounts    | SQL, MongoDB, Cassandra, Gremlin, Table|
|**Azure Database for PostgreSQL – egyetlen kiszolgáló** | Microsoft. DBforPostgreSQL/kiszolgálók    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft. DBforMySQL/kiszolgálók    | Portra beállított mysqlserver |
|**Azure Database for MariaDB** | Microsoft. DBforMariaDB/kiszolgálók    | mariadbServer |
|**Azure IoT Hub** | Microsoft. Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Microsoft. kulcstartó/tárolók    | tár |
|**Azure Kubernetes Service – Kubernetes API** | Microsoft. Tárolószolgáltatás/managedClusters    | felügyelet |
|**Azure Search** | Microsoft. Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft. ContainerRegistry/nyilvántartók    | registry |
|**Azure App Configuration** | Microsoft. Appconfiguration/configurationStores    | configurationStores |
|**Azure Backup** | Microsoft. Recoveryservices szolgáltatónál/tárolók    | tár |
|**Azure Event Hub** | Microsoft. EventHub/névterek    | névtér |
|**Azure Service Bus** | Microsoft. ServiceBus/névterek | névtér |
|**Azure Relay** | Microsoft. Relay/névterek | névtér |
|**Azure Event Grid** | Microsoft. EventGrid/témakörök    | témakör |
|**Azure Event Grid** | Microsoft. EventGrid/tartományok    | domain |
|**Azure App Service** | Microsoft. Web/Sites    | helyek |
|**Azure Machine Learning** | Microsoft. MachineLearningServices/munkaterületek    | amlworkspace |
|**SignalR** | Microsoft. SignalRService/szignáló    | signalR |
|**Azure Monitor** | Microsoft. bepillantások/privateLinkScopes    | azuremonitor |
|**Cognitive Services** | (Microsoft. CognitiveServices/fiókok    | account |
|**Azure File Sync** | Microsoft. StorageSync/storageSyncServices    | AFS |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Privát végpontok hálózati biztonsága 
Ha privát végpontokat használ az Azure-szolgáltatásokhoz, a forgalom egy adott privát kapcsolati erőforráshoz van védve. A platform egy hozzáférés-vezérléssel ellenőrzi, hogy a hálózati kapcsolatok csak a megadott privát kapcsolati erőforrást érik-e el. Ha további erőforrásokat szeretne elérni ugyanazon az Azure-szolgáltatáson belül, további privát végpontokra van szükség. 
 
A munkaterhelések teljes mértékben lezárhatók a nyilvános végpontokhoz való hozzáféréstől egy támogatott Azure-szolgáltatáshoz való kapcsolódáshoz. Ez a vezérlő egy további hálózati biztonsági réteget biztosít az erőforrásokhoz egy beépített kiszűrése-védelem biztosításával, amely megakadályozza az ugyanazon az Azure-szolgáltatáson üzemeltetett egyéb erőforrásokhoz való hozzáférést. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Hozzáférés egy privát kapcsolati erőforráshoz jóváhagyási munkafolyamat használatával 
A következő kapcsolat-jóváhagyási módszerekkel csatlakozhat egy privát kapcsolati erőforráshoz:
- A **rendszer automatikusan** jóváhagyja, ha a saját vagy engedéllyel rendelkezik az adott privát kapcsolati erőforráshoz. A szükséges engedély a privát kapcsolat erőforrástípus alapján, a következő formátumban: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- **Manuális** kérés, ha nem rendelkezik a szükséges engedélyekkel, és hozzáférést szeretne kérni. A rendszer elindít egy jóváhagyási munkafolyamatot. A privát végpont és a következő privátvégpont-kapcsolat „Függőben” állapotban jön létre. A privát hivatkozás erőforrásának tulajdonosa felelős a kapcsolat jóváhagyásáért. A jóváhagyást követően a magánhálózati végpont engedélyezve van a forgalom normál módon történő elküldéséhez, ahogy az a következő jóváhagyási munkafolyamat-diagramon is látható.  

![munkafolyamat-jóváhagyás](media/private-endpoint-overview/private-link-paas-workflow.png)
 
A magánhálózati kapcsolat erőforrás-tulajdonosa a következő műveleteket hajthatja végre egy privát végponti kapcsolaton keresztül: 
- Tekintse át az összes privát végponti kapcsolat részleteit. 
- Privát végponti kapcsolatok jóváhagyása. A megfelelő privát végpont engedélyezve lesz a privát kapcsolati erőforrás felé irányuló forgalom elküldéséhez. 
- Privát végponti kapcsolatok elutasítása. A rendszer frissíti a megfelelő privát végpontot, hogy tükrözze az állapotot.
- Privát végponti kapcsolatok törlése bármilyen állapotban. A rendszer a megfelelő privát végpontot leválasztott állapottal frissíti, hogy tükrözze a műveletet, a privát végpont tulajdonosa csak ezen a ponton tudja törölni az erőforrást. 
 
> [!NOTE]
> Csak a jóváhagyott állapotú privát végpont küldhet forgalmat egy adott privát kapcsolati erőforrásba. 

### <a name="connecting-using-alias"></a>Csatlakozás alias használatával
Az alias egy egyedi moniker, amely akkor jön létre, amikor a szolgáltatás tulajdonosa létrehoz egy standard Load Balancer mögötti privát kapcsolati szolgáltatást. A szolgáltatás tulajdonosai megoszthatják ezt az aliast a felhasználókkal kapcsolat nélkül. A felhasználók az erőforrás URI-ja vagy az alias használatával igényelhetnek kapcsolatot a privát kapcsolati szolgáltatással. Ha az alias használatával szeretne csatlakozni, létre kell hoznia egy privát végpontot a manuális kapcsolat jóváhagyási módszerével. A manuális kapcsolódási jóváhagyási módszer használatához állítsa a manuális kérelem paraméterét True értékre a magánhálózati végpont létrehozása folyamat során. A részletekért tekintse meg a [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) és [az az Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az-network-private-endpoint-create) parancsot. 

## <a name="dns-configuration"></a>DNS-konfiguráció 
Ha a kapcsolati karakterlánc részeként egy teljes tartománynevet (FQDN) használó privát kapcsolati erőforráshoz csatlakozik, fontos, hogy a DNS-beállításokat helyesen konfigurálja a lefoglalt magánhálózati IP-címhez való feloldáshoz. Előfordulhat, hogy a meglévő Azure-szolgáltatások már rendelkeznek DNS-konfigurációval, ha nyilvános végponton keresztül csatlakoznak. Ezt felül kell bírálni a privát végpont használatával történő kapcsolódáshoz. 
 
A magánhálózati végponthoz társított hálózati adapter tartalmazza a DNS konfigurálásához szükséges összes információt, beleértve a teljes TARTOMÁNYNEVEt és az adott privát kapcsolati erőforrás számára lefoglalt magánhálózati IP-címeket is. 

A privát végpontok számára a DNS konfigurálásával kapcsolatos ajánlott eljárásokról és javaslatokról részletes információk a [privát végpont DNS-konfigurációs cikkében](private-endpoint-dns.md)olvashatók.



 
## <a name="limitations"></a>Korlátozások
 
A következő táblázat tartalmazza a privát végpontok használatakor felismert korlátozások listáját: 


|Korlátozás |Leírás |Kockázatcsökkentés  |
|---------|---------|---------|
|A hálózati biztonsági csoport (NSG) szabályai és a felhasználó által megadott útvonalak nem vonatkoznak a privát végpontra    |A NSG nem támogatott a privát végpontokon. Míg a privát végpontot tartalmazó alhálózatokhoz NSG társítható, a szabályok nem lesznek érvényesek a privát végpont által feldolgozott forgalomra. A privát végpontok alhálózaton való üzembe helyezéséhez [le kell tiltani a hálózati házirendek kényszerítését](disable-private-endpoint-network-policy.md) . A NSG továbbra is érvényben van az ugyanazon alhálózaton futó egyéb munkaterheléseken. Az összes ügyfél-alhálózat útvonala egy/32 előtagot használ, és az alapértelmezett útválasztási viselkedés megváltoztatásához hasonló UDR van szükség.  | A forgalmat a forrás-ügyfeleken a kimenő forgalomra vonatkozó NSG szabályok használatával szabályozhatja. Egyéni útvonalak üzembe helyezése a/32 előtaggal a privát végponti útvonalak felülbírálásához. A kimenő kapcsolatok NSG és figyelési információi továbbra is támogatottak, és használhatók        |


## <a name="next-steps"></a>Következő lépések
- [Privát végpont létrehozása SQL Database a portál használatával](create-private-endpoint-portal.md)
- [Privát végpont létrehozása SQL Databasehoz a PowerShell használatával](create-private-endpoint-powershell.md)
- [Privát végpont létrehozása SQL Databasehoz a parancssori felület használatával](create-private-endpoint-cli.md)
- [Privát végpont létrehozása a Storage-fiókhoz a portál használatával](./tutorial-private-endpoint-storage-portal.md)
- [Privát végpont létrehozása az Azure Cosmos-fiókhoz a portál használatával](../cosmos-db/how-to-configure-private-endpoints.md)
- [Saját privát kapcsolati szolgáltatás létrehozása Azure PowerShell használatával](create-private-link-service-powershell.md)
- [Saját privát hivatkozás létrehozása Azure Database for PostgreSQL-egyetlen kiszolgálóhoz a portál használatával](../postgresql/howto-configure-privatelink-portal.md)
- [Saját privát hivatkozás létrehozása Azure Database for PostgreSQL-egyetlen kiszolgálóhoz a parancssori felület használatával](../postgresql/howto-configure-privatelink-cli.md)
- [Saját privát hivatkozás létrehozása Azure Database for MySQLhoz a portál használatával](../mysql/howto-configure-privatelink-portal.md)
- [Saját privát hivatkozás létrehozása Azure Database for MySQLhoz a CLI használatával](../mysql/howto-configure-privatelink-cli.md)
- [Saját privát hivatkozás létrehozása Azure Database for MariaDBhoz a portál használatával](../mariadb/howto-configure-privatelink-portal.md)
- [Saját privát hivatkozás létrehozása Azure Database for MariaDBhoz a CLI használatával](../mariadb/howto-configure-privatelink-cli.md)
- [Saját privát hivatkozás létrehozása Azure Key Vaulthoz a portál és a parancssori felület használatával](../key-vault/general/private-link-service.md)
