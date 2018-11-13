---
title: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 67cd78d4900b8ce53cf0c50116c02a9c1b967687
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958763"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>Adatbázisfiókok kezelése az Azure Cosmos DB-ben

Ez a cikk ismerteti, hogyan kezelheti Cosmos DB-fiókját: többkiszolgálós környezet beállítását, régiók hozzáadását és eltávolítását, több írási régió konfigurálását és a feladatátvételi prioritások beállítását. 

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Ügyfelek konfigurálása többkiszolgálós környezethez

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK

```csharp
// Create a new Connection Policy
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to Preferred locations
// The name of the location will match what you see in the portal/etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the Connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java Sync SDK

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Set up the connection policy with your preferred regions
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Régiók hozzáadása és eltávolítása az adatbázisfiókból

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Lépjen Azure Cosmos DB-fiókjához, és nyissa meg az **Adatok globális replikálása** menüt.

2. Régiók hozzáadásához jelöljön ki egy vagy több régiót a térképen a kívánt régióhoz tartozó, **„+”** címkével ellátott üres hatszögre kattintva. Úgy is hozzáadhat egy régiót, hogy rákattint a **+ Régió hozzáadása** lehetőségre, majd kiválaszt egy régiót a legördülő menüből.

3. Régiók eltávolításához törölje a régiók kijelölését a térképen a pipával ellátott kék színű hatszögekre kattintva, vagy válassza a kuka (🗑) ikont a régió nevétől jobbra.

4. Kattintson a Mentés gombra a módosítások mentéséhez.

   ![Régiók hozzáadása/eltávolítása menü](./media/how-to-manage-database-account/add-region.png)

Egyrégiós írási módban nem távolíthatja el az írási régiót. Az aktuális írási régió törlése előtt feladatátvételt kell végeznie egy másik régióba.

Többrégiós írási módban szabadon adhat hozzá és távolíthat el régiókat, amíg marad legalább egy régió.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>Több írási régió konfigurálása

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Adatbázisfiók létrehozásakor győződjön meg arról, hogy a **Többrégiós írás** beállítás engedélyezve van.

![Képernyőkép a Cosmos DB-fiók létrehozásáról](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager-sablon

Az alábbi JSON-kód példa egy Resource Manager-sablonra. A segítségével üzembe helyezhet egy Azure Cosmos DB-fiókot, amely Korlátozott frissesség konzisztenciaszabályzatot használ, ahol a maximális frissességi időtartam 5 másodperc, az elavult kérelmek maximális engedélyezett száma pedig 100. A Resource Manager-sablon formátumával és a szintaxissal kapcsolatos információkért tekintse meg a [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) dokumentációját.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a name="enable-manual-failover-for-your-cosmos-account"></a>Manuális feladatátvétel engedélyezése a Cosmos-fiókhoz

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Lépjen Azure Cosmos DB-fiókjához, és nyissa meg az **Adatok globális replikálása** menüt.

2. Kattintson a menü tetején található **Manuális feladatátvétel** gombra.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. A **Manuális feladatátvétel** menüben válassza ki az új írási régiót, és a négyzetet bejelölve erősítse meg, hogy megértette, ezzel a beállítással módosul az írási régió.

4. A feladatátvételi művelet elindításához kattintson az OK gombra.

   ![Manuális feladatátvétel menü a portálon](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a name="enable-automatic-failover-for-your-cosmos-account"></a>Automatikus feladatátvétel engedélyezése a Cosmos-fiókhoz

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Az Azure Cosmos DB-fiókban nyissa meg az **Adatok globális replikálása** panelt. 

2. Kattintson a lap tetején található **Automatikus feladatátvétel** gombra.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az **Automatikus feladatátvétel** panelen ellenőrizze, hogy az **Automatikus feladatátvétel engedélyezése** **BE** értékre van-e állítva. 

4. Kattintson a menü alján található Mentés gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

A feladatátvételi prioritásokat is beállíthatja ebben a menüben.

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-cosmos-account"></a>A Cosmos-fiók feladatátvételi prioritásainak beállítása

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Az Azure Cosmos DB-fiókban nyissa meg az **Adatok globális replikálása** panelt. 

2. Kattintson a lap tetején található **Automatikus feladatátvétel** gombra.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az **Automatikus feladatátvétel** panelen ellenőrizze, hogy az **Automatikus feladatátvétel engedélyezése** **BE** értékre van-e állítva. 

4. A feladatátvételi prioritást az olvasási régiók húzásával módosíthatja. Ehhez vigye az egérmutatót a régió fölé, majd kattintson a három pontra az ekkor megjelenő sor bal oldalán. 

5. Kattintson a menü alján található Mentés gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

Ebben a menüben nem módosíthatja az írási régiót. Az írási régió manuális módosításához manuális feladatátvételt kell végrehajtania.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
az cosmosdb failover-priority-change --name <Cosmos DB Account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>További lépések

A konzisztenciaszintek és az adatütközések Cosmos DB-ben történő kezeléséről az alábbi dokumentumokból tudhat meg többet:

* [A konzisztencia kezelése](how-to-manage-consistency.md)
* [Régiók közötti ütközések kezelése](how-to-manage-conflicts.md)

