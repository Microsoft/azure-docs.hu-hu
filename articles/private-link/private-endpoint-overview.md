---
title: Mi az az Azure-beli privát végpont?
description: Tudnivalók az Azure privát végpontról
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: f18d9cb2a1bf76986a5c77477085f2f12ab728ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771802"
---
# <a name="what-is-azure-private-endpoint"></a>Mi az az Azure privát végpont?

Az Azure privát végpont egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz. A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így lényegében bekapcsolja a szolgáltatást a virtuális hálózatba. A szolgáltatás lehet egy Azure-szolgáltatás, például az Azure Storage, Azure Cosmos DB, SQL stb. vagy egy saját Private Link [szolgáltatás.](private-link-service-overview.md)
  
## <a name="private-endpoint-properties"></a>Privát végpont tulajdonságai 
 A privát végpont a következő tulajdonságokat határozza meg: 


|Tulajdonság  |Leírás |
|---------|---------|
|Név    |    Egy egyedi név az erőforráscsoporton belül.      |
|Alhálózat    |  Az alhálózat, amely magánhálózati IP-címeket helyez üzembe és foglal le egy virtuális hálózatból. Az alhálózatra vonatkozó követelményekért tekintse meg a cikk Korlátozások szakaszát.         |
|Private Link erőforrás    |   A privát kapcsolat erőforrása, amely erőforrás-azonosító vagy alias használatával csatlakozik az elérhető típusok listájából. A rendszer létrehoz egy egyedi hálózati azonosítót az erőforrásnak küldött összes forgalomhoz.       |
|Cél-alforrás   |      A csatlakozáshoz a segédforrás. Minden egyes privát kapcsolati erőforrástípushoz különböző beállítások választhatók ki.    |
|Kapcsolat-jóváhagyási módszer    |  Automatikus vagy manuális. Az Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedélyei alapján a privát végpont automatikusan jóváhagyható. Ha az Azure RBAC nélkül próbál csatlakozni egy privát kapcsolati erőforráshoz, a manuális módszerrel engedélyezheti az erőforrás tulajdonosának a csatlakozás jóváhagyását.        |
|Kérésüzenet     |  Megadhat egy üzenetet a kért kapcsolatok manuális jóváhagyásához. Ez az üzenet egy adott kérés azonosítására használható.        |
|A kapcsolat állapota   |   Csak olvasható tulajdonság, amely meghatározza, hogy a privát végpont aktív-e. Csak jóváhagyott állapotban lévő privát végpontokkal lehet forgalmat küldeni. További elérhető államok: <br>-**Jóváhagyott:** A kapcsolat automatikusan vagy manuálisan lett jóváhagyva, és használatra kész.</br><br>-**Függőben:** A kapcsolat manuálisan lett létrehozva, és a privát kapcsolat erőforrásának tulajdonosa jóváhagyásra vár.</br><br>-**Elutasítva:** A kapcsolatot a privát kapcsolat erőforrásának tulajdonosa elutasította.</br><br>-**Leválasztva:** A kapcsolatot a privát kapcsolat erőforrás-tulajdonosa eltávolította. A privát végpont informatívsá válik, és törölni kell a tisztításhoz. </br>|

Néhány fontos részlet a privát végpontokkal kapcsolatban: 
- A privát végpont lehetővé teszi az ugyanattól a VNettől, regionálisan társviszonyban lévő virtuális hálózatoktól, globálisan társviszonyban lévő virtuális hálózatoktól és a helyszíni hálózatoktól származó ügyfelek közötti kapcsolatot [VPN](https://azure.microsoft.com/services/vpn-gateway/) vagy [Express Route,](https://azure.microsoft.com/services/expressroute/) valamint az Private Link.
 
- A hálózati kapcsolatokat csak a privát végponthoz csatlakozó ügyfelek kezdeményezhetik, a szolgáltatóknak nincs útválasztási konfigurációjuk ahhoz, hogy kapcsolatot kezdeményezzen a szolgáltatás fogyasztóival. A kapcsolatok csak egyetlen irányban létesítése lehet.

- Privát végpont létrehozásakor egy csak olvasható hálózati adapter is létrejön az erőforrás életciklusához. Az interfész dinamikusan magánhálózati IP-címeket kap a privát kapcsolati erőforrásra leképező alhálózatból. A magánhálózati IP-cím értéke a privát végpont teljes életciklusa során változatlan marad.
 
- A privát végpontot ugyanabban a régióban és előfizetésben kell üzembe helyezni, mint a virtuális hálózatot. 
 
- A privát kapcsolati erőforrás a virtuális hálózattól és a privát végponttól eltérő régióban is üzembe helyezhető.
 
- Ugyanazokkal a privát kapcsolati erőforrásokkal több privát végpont is létre lehet hozva. Közös DNS-kiszolgálókonfigurációt használó egyetlen hálózat esetén az ajánlott eljárás egyetlen privát végpont használata egy adott privát kapcsolati erőforráshoz, hogy elkerülje az ismétlődő bejegyzéseket vagy ütközéseket a DNS-feloldásban. 
 
- Ugyanazon a virtuális hálózaton belül több privát végpont is létre lehet hozva ugyanazon vagy különböző alhálózaton. Az előfizetésben létrehozható privát végpontok száma korlátozásokkal rendelkezik. Részletekért lásd: [Az Azure korlátai.](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)

- A privát kapcsolati erőforrásból származó előfizetést is regisztrálni kell a Micosoft.Network erőforrás-szolgáltatónál. Részletekért lásd: [Azure erőforrás-szolgáltatók.](../azure-resource-manager/management/resource-providers-and-types.md)

 
## <a name="private-link-resource"></a>Privát kapcsolat erőforrása 
A privát kapcsolati erőforrás egy adott privát végpont célhelye. Az alábbiakban az elérhető privát kapcsolati erőforrástípusok listája található: 
 
|Privát kapcsolati erőforrás neve  |Erőforrás típusa   |Alrések  |
|---------|---------|---------|
|**Private Link szolgáltatás** (saját szolgáltatás)   |  Microsoft.Network/privateLinkServices       | üres |
|**Azure Automation** |  Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Azure SQL Database** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Tábla (tábla, table_secondary)<BR> Üzenetsor (üzenetsor, queue_secondary)<BR> Fájl (fájl, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql, MongoDB, Cassandra, Gremlin, Tábla|
|**Azure Database for PostgreSQL -Single server (Egyetlen kiszolgáló)** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Azure IoT Hub** | Microsoft.Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Microsoft.KeyVault/vaults    | tár |
|**Azure Kubernetes Service – Kubernetes API** | Microsoft.ContainerService/managedClusters    | felügyelet |
|**Azure Search** | Microsoft.Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft.ContainerRegistry/registry    | registry |
|**Azure App Configuration** | Microsoft.Appconfiguration/configurationStores    | configurationStores |
|**Azure Backup** | Microsoft.RecoveryServices/vaults    | tár |
|**Azure Event Hub** | Microsoft.EventHub/névterek    | névtér |
|**Azure Service Bus** | Microsoft.ServiceBus/namespaces | névtér |
|**Azure Relay** | Microsoft.Relay/névterek | névtér |
|**Azure Event Grid** | Microsoft.EventGrid/topics    | témakör |
|**Azure Event Grid** | Microsoft.EventGrid/domains    | domain |
|**Azure App Service** | Microsoft.Web/sites    | Helyek |
|**Azure App Service-tárolóhelyek** | Microsoft.Web/sites    | sites-`<slot name>` |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | amlworkspace |
|**SignalR** | Microsoft.SignalRService/SignalR    | signalR |
|**Azure Monitor** | Microsoft.Insights/privateLinkScopes    | azuremonitor |
|**Cognitive Services** | (Microsoft.CognitiveServices/accounts    | account |
|**Azure File Sync** | Microsoft.StorageSync/storageSyncServices    | Afs |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Privát végpontok hálózati biztonsága 
Ha privát végpontokat használ az Azure-szolgáltatásokhoz, a forgalom egy adott privát kapcsolati erőforrásra van biztosítva. A platform egy hozzáférés-vezérléssel ellenőrzi, hogy a hálózati kapcsolatok csak a megadott privát kapcsolati erőforrást érik-e el. Az ugyanazon Azure-szolgáltatáson belüli további erőforrások eléréséhez további privát végpontokra van szükség. 
 
Teljes mértékben zárolhatja a számítási feladatokat a nyilvános végpontok elérésében egy támogatott Azure-szolgáltatáshoz való csatlakozáshoz. Ez a vezérlő egy további hálózati biztonsági réteget biztosít az erőforrások számára egy beépített kiszivárgás elleni védelem biztosításával, amely megakadályozza az ugyanazon az Azure-szolgáltatáson üzemeltetett egyéb erőforrásokhoz való hozzáférést. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Hozzáférés egy privát kapcsolati erőforráshoz jóváhagyási munkafolyamattal 
A következő kapcsolat-jóváhagyási módszerekkel csatlakozhat egy privát kapcsolati erőforráshoz:
- **Automatikusan** jóváhagyva, ha ön az adott privát kapcsolat erőforrásának a saját tulajdonában van, vagy rendelkezik az engedélyével. A szükséges engedély a privát kapcsolat erőforrástípusán alapul a következő formátumban: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- **Manuális** kérés, ha nem rendelkezik a szükséges engedéllyel, és hozzáférést szeretne kérni. A jóváhagyási munkafolyamat el lesz indítva. A privát végpont és a következő privátvégpont-kapcsolat „Függőben” állapotban jön létre. A privát hivatkozás erőforrásának tulajdonosa felelős a kapcsolat jóváhagyásáért. A jóváhagyás után a privát végpont normál módon képes forgalmat küldeni, ahogyan az alábbi jóváhagyási munkafolyamat-diagramon látható.  

![munkafolyamat-jóváhagyás](media/private-endpoint-overview/private-link-paas-workflow.png)
 
A privát kapcsolat erőforrás tulajdonosa a következő műveleteket hajthatja végre egy privát végponti kapcsolaton: 
- Tekintse át a privát végpontok kapcsolatainak minden részletét. 
- Privát végponti kapcsolat jóváhagyása. A megfelelő privát végpont számára engedélyezve lesz, hogy forgalmat küldjön a privát kapcsolati erőforrásnak. 
- Privát végpontkapcsolat elutasítása. A rendszer frissíti a megfelelő privát végpontot, hogy tükrözze az állapotot.
- Privát végponti kapcsolat törlése bármilyen állapotban. A megfelelő privát végpont a műveletnek megfelelően leválasztott állapottal frissül. A privát végpont tulajdonosa csak ezen a ponton törölheti az erőforrást. 
 
> [!NOTE]
> Csak jóváhagyott állapotban lévő privát végpont küldhet forgalmat egy adott privát kapcsolati erőforrásnak. 

### <a name="connecting-using-alias"></a>Csatlakozás alias használatával
Az alias egy egyedi moniker, amely akkor jön létre, amikor a szolgáltatás tulajdonosa létrehoz egy privát kapcsolati szolgáltatást egy standard terheléselosztási eszköz mögött. A szolgáltatás tulajdonosa offline is megoszthatja ezt az aliast a felhasználóival. A felhasználók az erőforrás URI-ját vagy az aliast használva kérhetnek kapcsolatot a privát kapcsolati szolgáltatáshoz. Ha alias használatával szeretne csatlakozni, létre kell hoznia egy privát végpontot manuális kapcsolat-jóváhagyási módszerrel. A manuális csatlakozási jóváhagyási módszer használata esetén állítsa a manuális kérésparamétert true (igaz)értékre a privát végpontok létrehozási folyamatában. A részletekért keresse fel [a New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) és [az az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) webhelyet. 

## <a name="dns-configuration"></a>DNS-konfiguráció 
Amikor a kapcsolati sztring részeként teljes tartománynévvel (FQDN) csatlakozik egy privát kapcsolati erőforráshoz, fontos, hogy a DNS-beállításokat megfelelően konfigurálja a lefoglalt magánhálózati IP-címre való feloldáshoz. Előfordulhat, hogy a meglévő Azure-szolgáltatások dns-konfigurációt használnak a nyilvános végponton keresztüli csatlakozáshoz. Ezt felül kell bírálni a privát végponttal való csatlakozáshoz. 
 
A privát végponthoz társított hálózati adapter tartalmazza a DNS konfigurálásához szükséges összes információt, beleértve az adott privát kapcsolati erőforráshoz lefoglalt teljes tartományneveket és magánhálózati IP-címeket. 

A privát végpontok DNS-ének konfigurálási ajánlott eljárásokkal és javaslatokkal kapcsolatos részletes információkért tekintse meg a privát [végpontOK DNS-konfigurációját ismertető cikket.](private-endpoint-dns.md)



 
## <a name="limitations"></a>Korlátozások
 
Az alábbi táblázat a privát végpontok használata esetén ismert korlátozások listáját tartalmazza: 


|Korlátozás |Leírás |Kockázatcsökkentés  |
|---------|---------|---------|
|A hálózati biztonsági csoport (NSG) szabályai és a felhasználó által megadott útvonalak nem vonatkoznak a privát végpontra    |Az NSG privát végpontok esetén nem támogatott. Bár a privát végpontot tartalmazó alhálózatok NSG-ket társíthatnak hozzá, a szabályok nem lesznek hatékonyak a privát végpont által feldolgozott forgalomra. A privát [végpontok alhálózaton](disable-private-endpoint-network-policy.md) való üzembe helyezéséhez le kell tiltani a hálózati szabályzatok betartatása szükséges. Az NSG továbbra is érvényben van az ugyanazon az alhálózaton üzemeltetett egyéb számítási feladatokon. Az útvonalak bármely ügyfél-alhálózaton /32 előtagot fognak használni, az alapértelmezett útválasztási viselkedés módosításához hasonló UDR szükséges  | A forgalom szabályozása NSG-szabályokkal a forrás ügyfelek kimenő forgalmához. Egyéni útvonalak üzembe helyezése /32 előtaggal a privát végpontútvonalak felülbírálása érdekében. Az NSG-forgalom naplói és a kimenő kapcsolatok monitorozási információi továbbra is támogatottak, és használhatók        |


## <a name="next-steps"></a>Következő lépések
- [Privát végpont létrehozása SQL Database portál használatával](create-private-endpoint-portal.md)
- [Privát végpont létrehozása SQL Database PowerShell használatával](create-private-endpoint-powershell.md)
- [Privát végpont létrehozása a SQL Database cli használatával](create-private-endpoint-cli.md)
- [Privát végpont létrehozása Storage-fiókhoz a portál használatával](./tutorial-private-endpoint-storage-portal.md)
- [Privát végpont létrehozása Azure Cosmos-fiókhoz a portál használatával](../cosmos-db/how-to-configure-private-endpoints.md)
- [Saját Private Link létrehozása a Azure PowerShell](create-private-link-service-powershell.md)
- [Saját kiszolgáló Private Link a Azure Database for PostgreSQL – Egyetlen kiszolgáló a portál használatával](../postgresql/howto-configure-privatelink-portal.md)
- [Saját kiszolgáló létrehozása Private Link a Azure Database for PostgreSQL – Egyetlen kiszolgáló a CLI használatával](../postgresql/howto-configure-privatelink-cli.md)
- [Saját fiók Private Link a Azure Database for MySQL portál használatával](../mysql/howto-configure-privatelink-portal.md)
- [Saját fiók Private Link a Azure Database for MySQL CLI használatával](../mysql/howto-configure-privatelink-cli.md)
- [Saját Private Link létrehozása Azure Database for MariaDB portál használatával](../mariadb/howto-configure-privatelink-portal.md)
- [Saját fiók Private Link a Azure Database for MariaDB CLI használatával](../mariadb/howto-configure-privatelink-cli.md)
- [Saját fiók Private Link a Azure Key Vault portál és a cli használatával](../key-vault/general/private-link-service.md)
