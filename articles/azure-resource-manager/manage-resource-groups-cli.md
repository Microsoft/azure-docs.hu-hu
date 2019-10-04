---
title: Azure Resource Manager-csoportok kezelése az Azure CLI-vel |} A Microsoft Docs
description: Az Azure Resource Manager-csoportok kezelése az Azure CLI használatával.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: c50a96b2598b89d5072a9441162d198163156c8d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296270"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure Resource Manager-erőforráscsoportok kezelése az Azure CLI-vel

Ismerje meg, hogyan használható az Azure CLI-t [Azure Resource Manager](resource-group-overview.md) az Azure-erőforráscsoportok kezelése. Azure-erőforrások kezeléséhez, lásd: [Azure-erőforrások kezelése az Azure CLI-vel](./manage-resources-cli.md).

Más cikkek erőforráscsoportok kezeléséről:

- [Az Azure-erőforráscsoportok kezelése az Azure portal használatával](./manage-resources-portal.md)
- [Azure-erőforráscsoportok kezelése az Azure PowerShell használatával](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Mi az erőforráscsoport

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általánosságban elmondható adjon hozzá erőforrásokat, azonos életciklussal ugyanabban az erőforráscsoportban, így könnyen telepítése, frissítése és csoportként törölheti őket.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ha megad egy helyet az erőforráscsoportnak, határozunk meg a metaadatok tárolására.

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

A következő CLI-példaszkript létrehoz egy erőforráscsoportot, és megjeleníti az erőforráscsoport.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Erőforráscsoportok listázásához

A következő CLI-példaszkript az előfizetéshez tartozó erőforráscsoportok listája.

```azurecli-interactive
az group list
```

Egy erőforráscsoport lekérése:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Erőforráscsoport törlése

A következő CLI-példaszkript töröl egy erőforráscsoportot:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Milyen az Azure Resource Manager orders erőforrások törlését kapcsolatos további információkért lásd: [törölni az Azure Resource Manager-erőforrást csoport](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások üzembe helyezése egy meglévő erőforráscsoportot

Lásd: [helyezheti üzembe az erőforrásokat egy meglévő erőforráscsoportot](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Egy erőforráscsoport és erőforrások üzembe helyezése

Hozzon létre egy erőforráscsoportot, és az erőforrások üzembe helyezése a csoporthoz egy Resource Manager-sablon használatával. További információkért lásd: [hozzon létre és helyezhet üzembe erőforrásokat](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Telepítse újra a központi telepítésének hibája esetén

Ez a funkció más néven van *visszaállítási hiba*. További információkért lásd: [ismételt üzembe helyezése, központi telepítésének hibája esetén](./resource-group-template-deploy-cli.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Helyezze át egy másik erőforráscsoportba vagy előfizetésbe

A csoport az erőforrásokat áthelyezheti egy másik erőforráscsoportot. További információkért lásd: [erőforrások áthelyezése](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Zárolási erőforráscsoportok

Zárolás megakadályozza a véletlen törlését vagy módosítását a kritikus fontosságú erőforrások, például az Azure-előfizetésre, erőforráscsoportra vagy erőforrásra a szervezet más felhasználói. 

A következő parancsfájl zárolja az erőforráscsoport, az erőforráscsoport nem törölhető.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

A következő parancsfájl egy erőforráscsoportot az összes zárolásainak beolvasása:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

A következő parancsfájl egy zár törli:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Címke erőforráscsoportok

A címkékkel erőforráscsoportok és erőforrásokhoz, hogy logikusan rendszerezhesse az eszközöket. További információ: [az Azure-erőforrások rendszerezése címkék használatával](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Erőforráscsoportok exportálása sablonokhoz

Az erőforráscsoport sikeres beállítása után érdemes a Resource Manager-sablon az erőforráscsoport megtekintéséhez. A sablon exportálása két előnyöket kínál:

- Automatizálhatja a később üzembe helyezések, a megoldás, mert a sablon tartalmazza a teljes infrastruktúra.
- Ismerje meg a sablon szintaxisáról alapján, a JavaScript Object Notation (JSON), amely a megoldás jelöli.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

A parancsfájl a sablon a konzolon jeleníti meg.  Másolja a JSON-, és mentse a fájlt.

További információkért lásd: [sablont az Azure Portalon egyetlen vagy több erőforrás exportálási](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Erőforráscsoportok való hozzáférés kezelése

A [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. További információkért lásd: [RBAC és az Azure CLI-hozzáférés kezelése](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>További lépések

- Azure Resource Manager kapcsolatban lásd: [Azure Resource Manager áttekintése](./resource-group-overview.md).
- A Resource Manager-sablon szintaxisáról kapcsolatban lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](./resource-group-authoring-templates.md).
- Ismerje meg, hogyan fejleszthet sablonokat, tekintse meg a [lépésről lépésre haladó oktatóanyagok](/azure/azure-resource-manager/).
- Az Azure Resource Manager-sablon sémák megtekintése: [sablonreferenciája](/azure/templates/).