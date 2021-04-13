---
title: Felügyelt virtuális hálózatok & privát végpontok
description: Ismerje meg a felügyelt virtuális hálózatokat és a felügyelt privát végpontokat a Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: d777588f0abdd1f771deb259c597f6407e61d874
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364610"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory felügyelt Virtual Network (előzetes verzió)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a felügyelt Virtual Network és a felügyelt privát végpontokat ismerteti a Azure Data Factory.


## <a name="managed-virtual-network"></a>Felügyelt virtuális hálózat

Amikor létrehoz egy Azure Integration Runtime (IR) Azure Data Factory Managed Virtual Network (VNET) hálózatban, az integrációs környezet a felügyelt Virtual Network-vel lesz kiépítve, és privát végpontokat fog használni a támogatott adattárakhoz való biztonságos csatlakozáshoz. 

A felügyelt Azure IR belüli Virtual Network biztosítja az adatintegrációs folyamat elkülönített és biztonságos folyamatát. 

A felügyelt szolgáltatás használatának Virtual Network:

- Felügyelt Virtual Network a terhelést a felügyelt Virtual Network a Azure Data Factory. Nem kell alhálózatot létrehoznia a Azure Integration Runtime, amely végül számos magánhálózati IP-t használhatna a Virtual Network és ehhez a hálózati infrastruktúra előzetes megtervezésére lenne szükség. 
- Az adatintegráció biztonságos ához nincs szükség az Azure-hálózattal kapcsolatos mélyebb ismeretekre. Ehelyett a biztonságos ETL használata sokkal egyszerűbb az adatmérnökök számára. 
- A felügyelt Virtual Network privát végpontokkal együtt védelmet nyújt az adatok kiszivárgása ellen. 

> [!IMPORTANT]
>A felügyelt virtuális hálózat jelenleg csak ugyanabban a régióban támogatott, mint Azure Data Factory régióban.
 

![Az ADF felügyelt Virtual Network architektúrája](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Felügyelt privát végpontok

A felügyelt privát végpontok olyan privát végpontok, amelyek a Azure Data Factory felügyelt Virtual Network azure-erőforrásokra mutató privát kapcsolat létrehozásához hoztak létre. Ezeket a privát végpontokat az Azure Data Factory kezeli az Ön nevében. 

![Új felügyelt privát végpont](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory támogatja a privát hivatkozásokat. A privát kapcsolat lehetővé teszi az Azure-szolgáltatások (például az Azure Storage, Azure Cosmos DB, Azure Synapse Analytics) Azure Synapse Analytics.

Privát kapcsolat használatakor az adattárai és a felügyelt Virtual Network közötti forgalom teljes egészében a Microsoft gerinchálózatán keresztül történik. Private Link adatszivárgási kockázatok ellen nyújt védelmet. Az erőforrásokra mutató privát kapcsolatot egy privát végpont létrehozásával lehet létrehozni.

A privát végpont privát IP-címet használ a felügyelt Virtual Network, hogy hatékonyan beveszi a szolgáltatást. A privát végpontok egy adott Azure-erőforrásra vannak leképezve, nem a teljes szolgáltatásra. Az ügyfelek korlátozhatják a szervezet által jóváhagyott adott erőforráshoz való csatlakozást. További információ a privát [kapcsolatokról és a privát végpontokról.](../private-link/index.yml)

> [!NOTE]
> Javasoljuk, hogy felügyelt privát végpontokat hozzon létre az összes Azure-adatforráshoz való csatlakozáshoz. 
 
> [!WARNING]
> Ha egy PaaS-adattár (Blob, ADLS Gen2, Azure Synapse Analytics) már létrehozott egy privát végpontot, és még ha minden hálózatról engedélyezi is a hozzáférést, az ADF csak felügyelt privát végponttal férhet hozzá. Ilyen esetekben mindenképpen privát végpontot hozzon létre. 

A privát végpontkapcsolat "Függőben" állapotban jön létre, amikor felügyelt privát végpontot hoz létre a Azure Data Factory. Jóváhagyási munkafolyamat van elindítva. A privát kapcsolat erőforrás-tulajdonosa felelős a kapcsolat jóváhagyásáért vagy elutasításáért.

![Privát végpont kezelése](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Ha a tulajdonos jóváhagyja a kapcsolatot, létrejön a privát kapcsolat. Ellenkező esetben a privát kapcsolat nem lesz létrejött. A felügyelt privát végpont mindkét esetben frissülni fog a kapcsolat állapotával.

![Felügyelt privát végpont jóváhagyása](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Csak jóváhagyott állapotban lévő felügyelt privát végpont küldhet forgalmat egy adott privát kapcsolati erőforrásnak.

## <a name="interactive-authoring"></a>Interaktív tartalom
Az interaktív szerzői képességek olyan funkciókhoz használhatók, mint a kapcsolat tesztelése, a mappalista és a táblalista tallózása, a séma lekértése és az adatok előnézete. Az interaktív létrehozást az ADF által felügyelt virtuális hálózaton Azure Integration Runtime létrehozásakor vagy szerkesztésekor engedélyezheti. A háttérszolgáltatás előre lefoglalja a számítást az interaktív szerzői funkciókhoz. Ellenkező esetben a rendszer minden alkalommal lefoglalja a számítást, amikor bármilyen interaktív műveletet hajt végre, ami több időt fog végezni. Az interaktív szerzői műveletekhez szükséges idő (TTL) 60 perc, ami azt jelenti, hogy az utolsó interaktív szerzői művelettől 60 perc után automatikusan le lesz tiltva.

![Interaktív tartalom](./media/managed-vnet/interactive-authoring.png)

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Felügyelt virtuális hálózat létrehozása Azure PowerShell
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}"

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák
### <a name="supported-data-sources"></a>Támogatott adatforrások
Az alábbi adatforrások privát kapcsolaton keresztüli csatlakozása támogatott az ADF által felügyelt Virtual Network.
- Azure Blob-tároló
- Azure Table Storage
- Azure Files
- 2. generációs Azure Data Lake
- Azure SQL Database (nem tartalmazza a Azure SQL Managed Instance)
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Azure Key Vault
- Azure Private Link Szolgáltatás
- Azure Search
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory felügyelt Virtual Network a következő Azure-régiókban érhetők el:
- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- USA déli középső régiója
- USA középső régiója
- Észak-Európa
- Nyugat-Európa
- Az Egyesült Királyság déli régiója
- Délkelet-Ázsia
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Kelet-Kelet
- Kelet-Japán
- Nyugat-Japán
- Dél-Korea középső régiója
- Dél-Brazília
- Közép-Franciaország
- Észak-Svájc
- Az Egyesült Királyság nyugati régiója
- Kelet-Kanada
- Közép-Kanada

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Kimenő kommunikáció nyilvános végponton keresztül az ADF által felügyelt Virtual Network
- A kimenő kommunikációhoz csak a 443-as port van megnyitva.
- Az Azure Storage és az Azure Data Lake Gen2 nyilvános végponton keresztüli, az ADF felügyelt virtuális hálózatából történő csatlakoztatása nem támogatott.

### <a name="linked-service-creation-of-azure-key-vault"></a>Az összekapcsolt szolgáltatás létrehozása Azure Key Vault 
- Amikor társított szolgáltatást hoz létre az Azure Key Vaulthoz, nincs az Azure integrációs modulra irányuló hivatkozás. Így nem hozhat létre privát végpontot a csatolt szolgáltatás létrehozása Azure Key Vault. Ha azonban olyan adattárakhoz hoz létre csatolt szolgáltatást, amelyek egy csatolt szolgáltatásra hivatkoznak, és erre a linked Service-szolgáltatásra hivatkozik Azure Integration Runtime felügyelt Virtual Network engedélyezve van, akkor a létrehozás során létrehozhat egy privát végpontot a Azure Key Vault-hez csatolt szolgáltatáshoz. Azure Key Vault 
- **Tesztelje a** kapcsolati műveletet a Azure Key Vault szolgáltatáshoz csak az URL-formátumot érvényesíti, hálózati műveletet azonban nem.
- A **Privát végpont használata oszlop** mindig üresként jelenik meg, még akkor is, ha privát végpontot hoz létre a Azure Key Vault.
![Privát végpont az AKV-hez](./media/managed-vnet/akv-pe.png)

## <a name="next-steps"></a>Következő lépések

- Oktatóanyag: [Másolási folyamat létrehozása felügyelt Virtual Network privát végpontok használatával](tutorial-copy-data-portal-private.md) 
- Oktatóanyag: [Adatfolyam-leképezési folyamat létrehozása felügyelt Virtual Network privát végpontok használatával](tutorial-data-flow-private.md)