---
title: Azure Cosmos DB erőforrások törlésének vagy módosításának megakadályozása
description: Az Azure-erőforrások zárolásával megakadályozhatja Azure Cosmos DB erőforrások törlését vagy módosítását.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 1c8c766208132aec115e1fbeb15af3a057c3de3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94636690"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Azure Cosmos DB erőforrások törlésének vagy módosításának megakadályozása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Rendszergazdaként előfordulhat, hogy egy Azure Cosmos-fiókot,-adatbázist vagy-tárolót kell zárolnia, hogy megakadályozza a szervezet más felhasználói számára a kritikus erőforrások véletlen törlését vagy módosítását. A zárolási szintet CanNotDelete (nem törölhető) vagy ReadOnly (csak olvasható) értékre állíthatja be.

- A **CanNotDelete** azt jelzi, hogy a jogosult felhasználók továbbra is olvashatják és módosíthatják az erőforrásokat, de nem tudják törölni az erőforrást.
- A **readonly** érték azt jelenti, hogy a jogosult felhasználók olvasni tudnak egy erőforrást, de nem tudják törölni vagy frissíteni az erőforrást. A zárolás alkalmazása hasonló ahhoz, hogy korlátozza az összes jogosult felhasználót az olvasó szerepkör által megadott engedélyekkel.

## <a name="how-locks-are-applied"></a>A zárolások alkalmazása

Ha egy fölérendelt hatókörben zárolja a zárolást, akkor a hatókörben lévő összes erőforrás örökli ugyanazt a zárolást. A később hozzáadott erőforrások még a szülőtől öröklik a zárolást. Az öröklés legszigorúbb zárolása elsőbbséget élvez.

Az Azure szerepköralapú hozzáférés-vezérlésével ellentétben a felügyeleti zárolások használatával korlátozásokat alkalmazhat az összes felhasználó és szerepkör között. A Azure Cosmos DB Azure-beli RBAC kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés a Azure Cosmos DBban](role-based-access-control.md).

A Resource Manager zárolásai csak a felügyeleti síkon történő műveletekre érvényesek, ezek pedig a https://management.azure.com címre küldött műveletek. A zárolások nem korlátozzák, hogy az erőforrások hogyan végzik saját funkcióikat. Az erőforrás változásai korlátozva vannak, de az erőforrás működése nincs korlátozva. Például egy Azure Cosmos-tároló írásvédett zárolása megakadályozza a tároló törlését vagy módosítását. Nem akadályozza meg a tárolóban lévő adatok létrehozását, frissítését és törlését. Az adattranzakciók engedélyezve vannak, mert ezek a műveletek nem lesznek elküldve a https://management.azure.com webhelyre.

## <a name="manage-locks"></a>Zárolások kezelése

> [!WARNING]
> Az erőforrás-zárolások nem működnek olyan módosítások esetén, amelyeket a felhasználók a fiók kulcsainak használatával Azure Cosmos DB érnek el, kivéve, ha az Azure Cosmos-fiókot először zárolják a disableKeyBasedMetadataWriteAccess tulajdonság engedélyezésével. A tulajdonság engedélyezése előtt ügyelni kell arra, hogy az ne szakítsa meg a meglévő alkalmazásokat, amelyek olyan SDK-, Azure Portal-vagy harmadik féltől származó eszközöket használnak, amelyek a fiókok kulcsain keresztül csatlakoznak, és olyan erőforrásokat módosítanak, mint például az átviteli sebesség, az indexelési házirendek frissítése stb. Ha többet szeretne megtudni, és egy ellenőrzőlistán keresztül szeretné meggyőződni, hogy az alkalmazások továbbra is működőképesek maradnak, tekintse meg [a Azure Cosmos db SDK-k változásait](role-based-access-control.md#prevent-sdk-changes) .

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure CLI

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Sablon

Azure Cosmos DB-erőforrásra vonatkozó zárolás alkalmazásakor használja a következő formátumokat:

- neve `{resourceName}/Microsoft.Authorization/{lockName}`
- típusa `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> Egy meglévő Azure Cosmos-fiók módosításakor ügyeljen rá, hogy a fiók és a gyermek erőforrásainak egyéb tulajdonságait is adja meg, ha redploying ezzel a tulajdonsággal. Ne telepítse a sablont úgy, hogy az az legyen, vagy alaphelyzetbe állítja az összes fiók tulajdonságait.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések

- [Azure Resource Manager zárolások áttekintése](../azure-resource-manager/management/lock-resources.md)
