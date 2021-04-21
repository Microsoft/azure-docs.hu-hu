---
title: Erőforráscsoport és erőforrások törlése
description: Az erőforráscsoportok és erőforrások törlését ismerteti. Leírja, Azure Resource Manager erőforráscsoport törlésekor hogyan lehet megrendelni az erőforrások törlését. Ismerteti a válaszkódokat és azt, Resource Manager hogyan kezeli őket annak megállapításához, hogy a törlés sikeres volt-e.
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 3c062c2f775e145347129f24b201748ee517daf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768668"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager erőforráscsoport és az erőforrás törlése

Ez a cikk bemutatja, hogyan törölhet erőforráscsoportokat és erőforrásokat. Leírja, Azure Resource Manager erőforráscsoport törlésekor hogyan lehet megrendelni az erőforrások törlését.

## <a name="how-order-of-deletion-is-determined"></a>A törlés sorrendjének meghatározása

Erőforráscsoport törlésekor a Resource Manager erőforrás törlésének sorrendjét. A következő sorrendet használja:

1. Az összes gyermek (beágyazott) erőforrás törlődik.

2. A rendszer ezután törli az egyéb erőforrásokat kezelő erőforrásokat. Az erőforrás tulajdonsága beállítható úgy, hogy egy másik erőforrás `managedBy` kezelje azt. Ha ez a tulajdonság be van állítva, a másik erőforrást kezelő erőforrás törlődik a többi erőforrás előtt.

3. A fennmaradó erőforrások az előző két kategória után törlődnek.

A rendelés meghatározása után a Resource Manager egy DELETE műveletet ad ki minden erőforráshoz. A folytatás előtt megvárja, amíg a függőségek befejeződnek.

Szinkron műveletek esetén a várt sikeres válaszkódok a következőek:

* 200
* 204
* 404

Aszinkron műveletek esetén a várt sikeres válasz 202. Resource Manager az aszinkron törlési művelet állapotának megállapításához nyomon követi a location fejlécet vagy az azure-async művelet fejlécét.
  
### <a name="deletion-errors"></a>Törlési hibák

Ha egy törlési művelet hibát ad vissza, a Resource Manager újra a DELETE hívást. Újra kell történnie az 5xx, 429 és 408 állapotkódokkal. Alapértelmezés szerint az újrapróbálkozás időtartama 15 perc.

## <a name="after-deletion"></a>Törlés után

Resource Manager get hívást ad ki minden egyes törölni próbált erőforráshoz. A GET-hívás válasza várhatóan 404 lesz. Ha Resource Manager 404-es, a törlés sikeresnek tekinti a befejezését. Resource Manager eltávolítja az erőforrást a gyorsítótárból.

Ha azonban az erőforrás GET hívása 200-as vagy 201-es értéket ad vissza, a Resource Manager újra létrehozza az erőforrást.

Ha a GET művelet hibát ad vissza, Resource Manager újra a GET műveletet a következő hibakódért:

* Kevesebb mint 100
* 408
* 429
* Nagyobb, mint 500

Más hibakódok esetén Resource Manager erőforrás törlése meghiúsul.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszafordítható.

## <a name="delete-resource-group"></a>Erőforráscsoport törlése

Az erőforráscsoport törléséhez használja az alábbi módszerek egyikét.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [portálon](https://portal.azure.com)válassza ki a törölni kívánt erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

   ![Erőforráscsoport törlése](./media/delete-resource-group/delete-group.png)

1. A törlés megerősítéséhez írja be az erőforráscsoport nevét

---

## <a name="delete-resource"></a>Erőforrás törlése

Erőforrás törléséhez használja az alábbi módszerek egyikét.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [portálon](https://portal.azure.com)válassza ki a törölni kívánt erőforrást.

1. Válassza a **Törlés** elemet. Az alábbi képernyőképen egy virtuális gép felügyeleti lehetőségei láthatóak.

   ![Erőforrás törlése](./media/delete-resource-group/delete-resource.png)

1. A rendszer kérésére erősítse meg a törlést.

---

## <a name="required-access"></a>Szükséges hozzáférés

Erőforráscsoport törléséhez hozzáférésre van szüksége a **Microsoft.Resources/subscriptions/resourceGroups** erőforrás törlési műveletéhez. Az erőforráscsoport összes erőforrását is törölnie kell.

A műveletek listájáért lásd: [Azure erőforrás-szolgáltatói műveletek.](../../role-based-access-control/resource-provider-operations.md) A beépített szerepkörök listájáért lásd: [Az Azure beépített szerepkörei.](../../role-based-access-control/built-in-roles.md)

Ha rendelkezik a szükséges hozzáféréssel, de [a](lock-resources.md) törlési kérelem sikertelen, annak az lehet az oka, hogy zárolva van az erőforráscsoport.

## <a name="next-steps"></a>Következő lépések

* A fogalmak Resource Manager lásd: Azure Resource Manager [áttekintése.](overview.md)
* Törlési parancsokkal: [PowerShell,](/powershell/module/az.resources/Remove-AzResourceGroup) [Azure CLI](/cli/azure/group#az_group_delete)és [REST API.](/rest/api/resources/resourcegroups/delete)
