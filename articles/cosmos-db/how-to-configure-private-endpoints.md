---
title: Azure Private-hivatkozás konfigurálása Azure Cosmos-fiókhoz
description: Megtudhatja, hogyan állíthatja be az Azure Private-hivatkozást egy Azure Cosmos-fiók eléréséhez egy virtuális hálózatban lévő magánhálózati IP-cím használatával.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2020
ms.author: thweiss
ms.openlocfilehash: b05fa32529372a89ff441b953f001dc2ab1b5606
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84431655"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Azure Private-hivatkozás konfigurálása Azure Cosmos-fiókhoz

Az Azure Private link használatával privát végponton keresztül csatlakozhat egy Azure Cosmos-fiókhoz. A magánhálózati végpont a virtuális hálózaton belüli alhálózat magánhálózati IP-címeinek halmaza. Ezután korlátozhatja a hozzáférést egy Azure Cosmos-fiókhoz magánhálózati IP-címeken keresztül. Ha a privát hivatkozás korlátozott NSG-szabályzatokkal van kombinálva, az segít csökkenteni az adatkiszűrése kockázatát. Ha többet szeretne megtudni a privát végpontokról, tekintse meg az [Azure privát hivatkozását](../private-link/private-link-overview.md) ismertető cikket.

A privát hivatkozás lehetővé teszi, hogy a felhasználók hozzáférjenek egy Azure Cosmos-fiókhoz a virtuális hálózatból vagy bármely társ virtuális hálózatból. A privát kapcsolatra leképezett erőforrások a VPN-en vagy az Azure-ExpressRoute keresztül is elérhetők a helyszínen keresztül. 

Az automatikus vagy manuális jóváhagyási módszer használatával kapcsolódhat egy privát kapcsolattal konfigurált Azure Cosmos-fiókhoz. További információ: a privát hivatkozás dokumentációjának [jóváhagyási munkafolyamat](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) szakasza. 

Ez a cikk leírja a privát végpontok létrehozásának lépéseit. Feltételezi, hogy az automatikus jóváhagyási módszert használja.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Hozzon létre egy privát végpontot a Azure Portal használatával

A következő lépésekkel hozhat létre egy meglévő Azure Cosmos-fiókhoz tartozó privát végpontot a Azure Portal használatával:

1. A **minden erőforrás** panelen válasszon egy Azure Cosmos-fiókot.

1. Válassza a **privát végponti kapcsolatok** elemet a beállítások listájából, majd válassza a **privát végpont**elemet:

   ![Privát végpont létrehozására szolgáló kijelölés a Azure Portalban](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. A **privát végpont létrehozása – alapvető beállítások** panelen adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Válasszon ki egy erőforráscsoportot.|
    | **Példány részletei** |  |
    | Name | Adja meg a privát végpont nevét. Ha ezt a nevet hozza, hozzon létre egy egyedit. |
    |Régió| Válassza ki azt a régiót, ahol a privát hivatkozást telepíteni szeretné. Hozza létre a magánhálózati végpontot ugyanazon a helyen, ahol a virtuális hálózat található.|
    |||
1. Válassza a **Tovább: erőforrás**elemet.
1. A **privát végpont létrehozása – erőforrás**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a **Kapcsolódás egy Azure-erőforráshoz a címtárban**lehetőséget. <br/><br/> Ezután kiválaszthatja az egyik erőforrást a privát hivatkozás beállításához. Vagy kapcsolódhat valaki másnak erőforrásához egy erőforrás-azonosító vagy egy, az Önnel megosztott alias használatával.|
    | Előfizetés| Válassza ki az előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. AzureCosmosDB/databaseAccounts**lehetőséget. |
    | Erőforrás |Válassza ki az Azure Cosmos-fiókját. |
    |Cél alerőforrása |Válassza ki a leképezni kívánt Azure Cosmos DB API-típust. Ez az alapértelmezett érték az SQL, a MongoDB és a Cassandra API-k esetében csak egyetlen választás. A Gremlin és a Table API-k esetében választhatja az **SQL** -t is, mivel ezek az API-k az SQL API-val együttműködnek. |
    |||

1. Válassza a **Tovább: konfigurálás**lehetőséget.
1. A **privát végpont létrehozása – konfiguráció**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Hálózat**| |
    | Virtuális hálózat| Válassza ki a virtuális hálózatot. |
    | Alhálózat | Válassza ki az alhálózatot. |
    |**saját DNS integráció**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. <br><br/> A magánhálózati végponthoz való kapcsolódáshoz DNS-rekordra van szükség. Javasoljuk, hogy a privát végpontot egy privát DNS-zónával integrálja. Saját DNS-kiszolgálókat is használhat, vagy létrehozhat DNS-rekordokat a virtuális gépeken található gazdagép-fájlok használatával. |
    |saját DNS zóna |Válassza a **privatelink.Documents.Azure.com**lehetőséget. <br><br/> A magánhálózati DNS-zóna automatikusan meg van határozva. A Azure Portal használatával nem módosítható.|
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** lapon az Azure ellenőrzi a konfigurációt.
1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

Ha jóváhagyta az Azure Cosmos-fiókhoz tartozó privát hivatkozást, akkor a Azure Portalban a **tűzfal és a virtuális hálózatok** ablaktábla **minden hálózat** lehetőség nem érhető el.

Az alábbi táblázat a különböző Azure Cosmos-fiók API-típusai, a támogatott alerőforrások és a hozzájuk tartozó saját zónák neve közötti leképezést mutatja. A Gremlin és a Table API fiókokat az SQL API-n keresztül is elérheti, így ezek az API-k két bejegyzést használhatnak.

|Azure Cosmos-fiók API-típusa  |Támogatott alerőforrások (vagy csoport azonosítója) |Saját zóna neve  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tábla    |    Tábla     |   privatelink.table.cosmos.azure.com    |
|Tábla     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>A magánhálózati IP-címek beolvasása

A magánhálózati végpont kiépítés után lekérdezheti az IP-címeket. A Azure Portal IP-címeinek megtekintése:

1. Válassza az **Összes erőforrás** elemet.
1. Keresse meg a korábban létrehozott privát végpontot. Ebben az esetben ez a **cdbPrivateEndpoint3**.
1. A DNS-beállítások és az IP-címek megtekintéséhez válassza az **Áttekintés** lapot.

![Magánhálózati IP-címek a Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Több IP-cím jön létre egy privát végponton:

* Az Azure Cosmos-fiók globális (régió-agnosztikus) végpontjának egyike
* Egy minden régióhoz, ahol az Azure Cosmos-fiók telepítve van

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Privát végpont létrehozása Azure PowerShell használatával

Futtassa a következő PowerShell-szkriptet egy "MyPrivateEndpoint" nevű privát végpont létrehozásához egy meglévő Azure Cosmos-fiókhoz. Cserélje le a változó értékeket a környezet részleteire.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Privát végpont integrálása privát DNS-zónával

A privát végpont létrehozása után a következő PowerShell-parancsfájl használatával integrálhatja azt egy privát DNS-zónával:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>A magánhálózati IP-címek beolvasása

A magánhálózati végpont kiosztása után a következő PowerShell-parancsfájl használatával kérdezheti le az IP-címeket és a teljes tartománynevek megfeleltetését:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Privát végpont létrehozása az Azure CLI használatával

Futtassa az alábbi Azure CLI-szkriptet egy "myPrivateEndpoint" nevű privát végpont létrehozásához egy meglévő Azure Cosmos-fiókhoz. Cserélje le a változó értékeket a környezet részleteire.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Privát végpont integrálása privát DNS-zónával

A privát végpont létrehozása után a következő Azure CLI-parancsfájl használatával integrálhatja azt egy privát DNS-zónával:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Privát végpont létrehozása Resource Manager-sablonnal

A magánhálózati kapcsolatot úgy is beállíthatja, hogy létrehoz egy magánhálózati végpontot egy virtuális hálózati alhálózatban. Ezt egy Azure Resource Manager sablon segítségével érheti el.

A következő kód használatával hozzon létre egy "PrivateEndpoint_template. JSON" nevű Resource Manager-sablont. Ez a sablon egy meglévő virtuális hálózat meglévő Azure Cosmos SQL API-fiókjához hoz létre privát végpontot.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**A sablonhoz tartozó Parameters fájl megadása**

Hozzon létre egy paramétereket tartalmazó fájlt a sablonhoz, és nevezze el "PrivateEndpoint_parameters. JSON" néven. Adja hozzá a következő kódot a parameters (paraméterek) fájlhoz:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**A sablon üzembe helyezése PowerShell-parancsfájl használatával**

Hozzon létre egy PowerShell-parancsfájlt az alábbi kód használatával. A parancsfájl futtatása előtt cserélje le az előfizetés-azonosítót, az erőforráscsoport nevét és az egyéb változó értékeket a környezet részleteire.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

A PowerShell-parancsfájlban a `GroupId` változó csak egy értéket tartalmazhat. Ez az érték a fiók API-típusa. Az engedélyezett értékek a következők:,,, `Sql` `MongoDB` `Cassandra` `Gremlin` és `Table` . Néhány Azure Cosmos-fióktípus több API-n keresztül érhető el. Például:

* A Gremlin API-fiókok a Gremlin és az SQL API-fiókokból is elérhetők.
* Table API fiók a tábla-és SQL API-fiókokból is elérhető.

Ezen fiókok esetében minden egyes API-típushoz létre kell hoznia egy privát végpontot. A megfelelő API-típus van megadva a `GroupId` tömbben.

A sablon sikeres üzembe helyezését követően az alábbi képen láthatóhoz hasonló kimenet jelenik meg. Az `provisioningState` érték az, `Succeeded` Ha a magánhálózati végpontok megfelelően vannak beállítva.

![Üzembe helyezési kimenet a Resource Manager-sablonhoz](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

A sablon üzembe helyezése után a magánhálózati IP-címek az alhálózaton belül vannak lefoglalva. Az Azure Cosmos-fiók tűzfalszabály úgy van konfigurálva, hogy csak a magánhálózati végpontról fogadjon kapcsolatokat.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>A privát végpont integrálása saját DNS zónával

A következő kód használatával hozzon létre egy "PrivateZone_template. JSON" nevű Resource Manager-sablont. Ez a sablon létrehoz egy magánhálózati DNS-zónát egy meglévő Azure Cosmos SQL API-fiókhoz egy meglévő virtuális hálózaton.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

A következő kód használatával hozzon létre egy "PrivateZoneRecords_template. JSON" nevű Resource Manager-sablont.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**A sablonhoz tartozó Parameters fájl megadása**

Hozza létre a következő két paraméter-fájlt a sablonhoz. Hozza létre a "PrivateZone_parameters. JSON" fájlt. a következő kóddal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Hozza létre a "PrivateZoneRecords_parameters. JSON" fájlt. a következő kóddal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**A sablon üzembe helyezése PowerShell-parancsfájl használatával**

Hozzon létre egy PowerShell-parancsfájlt az alábbi kód használatával. A parancsfájl futtatása előtt cserélje le az előfizetés-azonosítót, az erőforráscsoport nevét és az egyéb változó értékeket a környezet részleteire.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Egyéni DNS konfigurálása

Saját DNS-zónát kell használnia azon az alhálózaton belül, ahol létrehozta a magánhálózati végpontot. Konfigurálja úgy a végpontokat, hogy minden magánhálózati IP-cím le legyen képezve egy DNS-bejegyzésre. (Lásd a `fqdns` korábban bemutatott válasz tulajdonságát.)

A privát végpont létrehozásakor az Azure-ban egyesítheti azt egy privát DNS-zónával. Ha ehelyett egyéni DNS-zónát használ, úgy kell beállítania, hogy a magánhálózati végpont számára fenntartott magánhálózati IP-címekhez adja hozzá a DNS-rekordokat.

## <a name="private-link-combined-with-firewall-rules"></a>Privát hivatkozás a tűzfalszabályok együttes használatával

A következő helyzetek és eredmények akkor lehetségesek, ha a privát hivatkozásokat a tűzfalszabályok kombinálva használják:

* Ha nem konfigurálja a tűzfalszabályok egyikét sem, akkor alapértelmezés szerint minden forgalom elérheti az Azure Cosmos-fiókokat.

* Ha nyilvános forgalmat vagy szolgáltatási végpontot állít be, és privát végpontokat hoz létre, akkor a megfelelő tűzfalszabály engedélyezi a bejövő forgalom különböző típusait. Ha a magánhálózati végpont olyan alhálózaton van konfigurálva, ahol a szolgáltatási végpont is be van állítva:
  * a privát végpont által leképezett adatbázis-fiók felé irányuló forgalmat privát végponton keresztül irányítja a rendszer.
  * az alhálózatról érkező más adatbázis-fiókok felé irányuló forgalmat a szolgáltatás-végponton keresztül irányítja a rendszer.

* Ha nem állít be nyilvános forgalmat vagy szolgáltatási végpontot, és privát végpontokat hoz létre, akkor az Azure Cosmos-fiók csak a privát végpontokon keresztül érhető el. Ha nem konfigurálja a nyilvános forgalmat vagy a szolgáltatási végpontot, az összes jóváhagyott privát végpont elutasítása vagy törlése után a fiók nyitva van a teljes hálózatra, kivéve, ha a PublicNetworkAccess nincs letiltva (lásd az alábbi szakaszt).

## <a name="blocking-public-network-access-during-account-creation"></a>Nyilvános hálózati hozzáférés letiltása a fiók létrehozása során

Az előző szakaszban leírtak szerint, és hacsak nincsenek megadva meghatározott tűzfalszabályok, a privát végpontok hozzáadásával az Azure Cosmos-fiókja csak privát végpontokon keresztül érhető el. Ez azt jelenti, hogy az Azure Cosmos-fiók a létrehozása után, illetve a privát végpont hozzáadása előtt elérhető a nyilvános forgalomból. Annak biztosítása érdekében, hogy a nyilvános hálózati hozzáférés le legyen tiltva, még a privát végpontok létrehozása előtt is, beállíthatja a `publicNetworkAccess` jelölőt a `Disabled` fiók létrehozása során. Tekintse meg [ezt a Azure Resource Manager sablont](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) a jelző használatának módját bemutató példához.

## <a name="port-range-when-using-direct-mode"></a>Porttartomány közvetlen mód használata esetén

Ha közvetlen módú kapcsolaton keresztül használ privát hivatkozást egy Azure Cosmos-fiókkal, gondoskodnia kell arról, hogy a TCP-portok teljes tartománya (0-65535) nyitva legyen.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Privát végpont frissítése régió hozzáadásakor vagy eltávolításakor

A régiók Azure Cosmos-fiókhoz való hozzáadásával vagy eltávolításával ehhez a fiókhoz DNS-bejegyzéseket kell hozzáadnia vagy eltávolítania. A régiók hozzáadása vagy eltávolítása után frissítheti az alhálózat saját DNS-zónáját, hogy azok tükrözzék a hozzáadott vagy eltávolított DNS-bejegyzéseket, valamint a hozzájuk tartozó magánhálózati IP-címeket.

Tegyük fel például, hogy egy Azure Cosmos-fiókot helyez üzembe három régióban: "USA nyugati régiója", "Közép-USA" és "Nyugat-Európa". Amikor létrehoz egy privát végpontot a fiókjához, négy privát IP-cím van lefoglalva az alhálózatban. A három régió mindegyikének van egy IP-címe, és a globális/régió-agnosztikus végpontnak egyetlen IP-címe van.

Később hozzáadhat egy új régiót (például "East US") az Azure Cosmos-fiókhoz. Az új régió hozzáadása után hozzá kell adnia egy megfelelő DNS-rekordot a saját DNS-zónájához vagy az egyéni DNS-hez.

A régió eltávolításakor ugyanezeket a lépéseket használhatja. A régió eltávolítását követően el kell távolítania a megfelelő DNS-rekordot a saját DNS-zónájából vagy az egyéni DNS-ből.

## <a name="current-limitations"></a>Aktuális korlátozások

Az alábbi korlátozások érvényesek, ha privát hivatkozást használ egy Azure Cosmos-fiókkal:

* Ha az Azure Cosmos-fiókkal létesített privát hivatkozást közvetlen módú kapcsolaton keresztül használja, csak a TCP protokollt használhatja. A HTTP protokoll jelenleg nem támogatott.

* Ha Azure Cosmos DB API-ját használja a MongoDB-fiókokhoz, a rendszer csak a kiszolgáló 3,6-es verziójában lévő fiókok esetében támogatja a magánhálózati végpontot (azaz a végpontot a formátumban használó fiókok esetében `*.mongo.cosmos.azure.com` ). A magánhálózati hivatkozás nem támogatott a (z) 3,2-es verziójú kiszolgálókon lévő fiókok esetében (azaz a végpontot használó fiókok esetében `*.documents.azure.com` ). A privát hivatkozás használatához át kell telepítenie a régi fiókokat az új verzióra.

* Ha olyan MongoDB-fiókhoz használ Azure Cosmos DB API-t, amely privát hivatkozást tartalmaz, előfordulhat, hogy egyes eszközök vagy tárak nem működnek, mivel automatikusan kiszűrik a `appName` paramétert a kapcsolati karakterláncból. Ez a paraméter szükséges a fiókhoz való kapcsolódáshoz egy privát végponton keresztül. Bizonyos eszközök, például a Visual Studio Code, nem távolítják el ezt a paramétert a kapcsolatok sztringből, ezért kompatibilisek.

* A hálózati rendszergazdának legalább az engedélyt meg kell adni `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` Az Azure Cosmos-fiók hatókörében, hogy automatikusan jóváhagyott privát végpontokat hozzon létre.

* A közvetlen üzemmód jelenleg nem támogatott Kínában működő Azure-régiókban.

### <a name="limitations-to-private-dns-zone-integration"></a>A magánhálózati DNS-zónák integrálására vonatkozó korlátozások

A magánhálózati DNS-zónában lévő DNS-rekordokat a rendszer nem távolítja el automatikusan, amikor töröl egy privát végpontot, vagy eltávolít egy régiót az Azure Cosmos-fiókból. A DNS-rekordokat manuálisan kell eltávolítania a következő előtt:

* Ehhez a magánhálózati DNS-zónához társított új privát végpont hozzáadása.
* Új régió hozzáadása bármely olyan adatbázis-fiókhoz, amely az ehhez a magánhálózati DNS-zónához csatolt privát végpontokkal rendelkezik.

Ha nem törli a DNS-rekordokat, előfordulhat, hogy váratlan adatsík-problémák történnek. Ezek a problémák közé tartoznak az adatkimaradások a privát végpont eltávolítása vagy a régió eltávolítása után hozzáadott régiókhoz.

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB biztonsági funkcióival kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* Ha Azure Cosmos DB tűzfalat szeretne konfigurálni, tekintse meg a [tűzfal támogatása](firewall-support.md)című témakört.

* Ha meg szeretné tudni, hogyan konfigurálhatja az Azure Cosmos-fiókhoz tartozó virtuális hálózati szolgáltatás végpontját, tekintse meg a [virtuális hálózatok elérésének konfigurálása](how-to-configure-vnet-service-endpoint.md)című témakört.

* Ha többet szeretne megtudni a privát hivatkozásokról, tekintse meg az [Azure Private link](../private-link/private-link-overview.md) dokumentációját.
