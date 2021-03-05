---
title: Erőforráscsoportok kezelése – Azure CLI
description: Az Azure CLI-vel kezelheti az erőforráscsoportokat Azure Resource Manager használatával. Megjeleníti az erőforráscsoportok létrehozását, listázását és törlését.
author: mumian
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e28b66844eaa0b73c2654175dea2e31d3cd75f5d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172096"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure Resource Manager erőforráscsoportok kezelése az Azure CLI használatával

Ismerje meg, hogyan kezelheti Azure-erőforráscsoportait az Azure CLI és a [Azure Resource Manager](overview.md) használatával. Az Azure-erőforrások kezelésével kapcsolatban lásd: az Azure- [erőforrások kezelése az Azure CLI használatával](manage-resources-cli.md).

Az erőforráscsoportok kezelésével kapcsolatos további cikkek:

- [Azure-erőforráscsoportok kezelése a Azure Portal használatával](manage-resources-portal.md)
- [Azure-erőforráscsoportok kezelése Azure PowerShell használatával](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Mi az erőforráscsoport?

Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általában olyan erőforrásokat adjon hozzá, amelyek ugyanazt az életciklust használják ugyanahhoz az erőforráscsoporthoz, így egyszerűen üzembe helyezheti, frissítheti és törölheti őket csoportként.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Amikor megad egy helyet az erőforráscsoporthoz, meg kell adnia, hogy hol tárolja a metaadatokat.

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

A következő CLI-parancs létrehoz egy erőforráscsoportot.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Erőforráscsoportok listázása

A következő CLI-parancsfájl felsorolja az előfizetéséhez tartozó erőforráscsoportokat.

```azurecli-interactive
az group list
```

Egy erőforráscsoport beszerzése:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Erőforráscsoportok törlése

A következő CLI-szkript töröl egy erőforráscsoportot:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

További információ arról, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését: [Azure Resource Manager erőforráscsoport törlése](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások központi telepítése meglévő erőforráscsoporthoz

Lásd: [erőforrások központi telepítése meglévő erőforráscsoporthoz](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Erőforráscsoport és erőforrások üzembe helyezése

Létrehozhat egy erőforráscsoportot, és erőforrásokat telepíthet a csoportba egy Resource Manager-sablon használatával. További információ: [erőforráscsoport létrehozása és erőforrások telepítése](../templates/deploy-to-subscription.md#resource-groups).

## <a name="redeploy-when-deployment-fails"></a>Újratelepítése, ha a telepítés sikertelen

Ez a szolgáltatás a *hiba miatt visszagörgetés* néven is ismert. További információ: [újratelepítése, ha a telepítés sikertelen](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Áthelyezés másik erőforráscsoporthoz vagy előfizetésbe

A csoportban található erőforrásokat áthelyezheti egy másik erőforráscsoporthoz. További információ: [erőforrások áthelyezése](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Erőforráscsoportok zárolása

A zárolás megakadályozza a szervezet más felhasználói számára a kritikus erőforrások, például az Azure-előfizetés, az erőforráscsoport vagy az erőforrás véletlen törlését vagy módosítását.

Az alábbi parancsfájl zárol egy erőforráscsoportot, így az erőforráscsoport nem törölhető.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName
```

Az alábbi parancsfájl egy erőforráscsoport összes zárolását lekéri:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName
```

A következő szkript törli a zárolást:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](lock-resources.md).

## <a name="tag-resource-groups"></a>Erőforráscsoportok címkézése

Címkéket alkalmazhat az erőforráscsoportok és az erőforrások számára, hogy logikailag szervezze az eszközöket. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Erőforráscsoportok exportálása sablonokba

Az erőforráscsoport sikeres beállítása után érdemes megtekinteni az erőforráscsoport Resource Manager-sablonját. A sablon exportálása két előnyt kínál:

- Automatizálja a megoldás jövőbeli üzembe helyezéseit, mert a sablon tartalmazza az összes teljes infrastruktúrát.
- A sablon szintaxisának megismeréséhez tekintse meg a megoldást jelölő JavaScript Object Notation (JSON).

Egy erőforráscsoport összes erőforrásának exportálásához használja az [az Group export](/cli/azure/group#az_group_export) nevű csoportot, és adja meg az erőforráscsoport nevét.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName
```

A parancsfájl megjeleníti a sablont a konzolon. Másolja a JSON-t, és mentse fájlként.

Az erőforráscsoport összes erőforrásának exportálása helyett kiválaszthatja, hogy mely erőforrásokat szeretné exportálni.

Egy erőforrás exportálásához adja át az erőforrás-azonosítót.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $resourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az group export --resource-group $resourceGroupName --resource-ids $storageAccount
```

Több erőforrás exportálásához adja át a szóközzel tagolt erőforrás-azonosítókat. Az összes erőforrás exportálásához ne adja meg ezt az argumentumot, vagy adja meg a következőt: "*".

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccount1 $storageAccount2
```

A sablon exportálásakor megadhatja, hogy a rendszer milyen paramétereket használ a sablonban. Alapértelmezés szerint az erőforrásnevek paraméterei szerepelnek, de nem rendelkeznek alapértelmezett értékkel. A paraméter értékét át kell adni az üzembe helyezés során.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

Az erőforrásban a nevet a paraméter használja.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Ha a (z `--include-parameter-default-value` ) paramétert használja a sablon exportálásakor, a Template paraméter egy alapértelmezett értéket tartalmaz, amely az aktuális értékre van beállítva. Ezt az alapértelmezett értéket használhatja, vagy felülírhatja az alapértelmezett értéket egy másik érték megadásával.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Ha a (z `--skip-resource-name-params` ) paramétert használja a sablon exportálásakor, az erőforrásnevek paraméterei nem szerepelnek a sablonban. Ehelyett az erőforrás neve közvetlenül az erőforráson az aktuális értékre van beállítva. A név nem szabható testre a telepítés során.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

A sablon exportálása funkció nem támogatja Azure Data Factory erőforrások exportálását. A Data Factory-erőforrások exportálásával kapcsolatos további tudnivalókért lásd: az [adatfeldolgozó másolása vagy klónozása Azure Data Factory-ben](../../data-factory/copy-clone-data-factory.md).

A klasszikus üzemi modellel létrehozott erőforrások exportálásához [át kell telepítenie azokat a Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md)-alapú üzemi modellbe.

További információkért lásd: [egy-és többerőforrásos exportálás a Azure Portal sablonba](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Erőforráscsoportok hozzáférésének kezelése

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) segítségével kezelheti az Azure-beli erőforrásokhoz való hozzáférést. További információ: [Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure CLI használatával](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Következő lépések

- Azure Resource Manager megismeréséhez tekintse meg a [Azure Resource Manager áttekintése](overview.md)című témakört.
- A Resource Manager-sablon szintaxisának megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](../templates/template-syntax.md)című témakört.
- A sablonok fejlesztésének megismeréséhez tekintse meg az útmutató [lépésről lépésre szóló oktatóanyagokat](../index.yml).
- A Azure Resource Manager sablon sémáinak megtekintéséhez lásd: [sablon-hivatkozás](/azure/templates/).