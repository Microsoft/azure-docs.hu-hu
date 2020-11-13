---
title: Virtuális gép címkézése a PowerShell használatával
description: A virtuális gép a PowerShell használatával történő címkézésének ismertetése
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 1efb512923caed97126bdb4ee6267c6a9b57f251
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594969"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Virtuális gép címkézése az Azure-ban a PowerShell használatával

Ez a cikk bemutatja, hogyan címkézheti a virtuális gépeket az Azure-ban a PowerShell használatával. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül egy erőforráson vagy erőforráscsoporton helyezhetők el. Az Azure jelenleg legfeljebb 50 címkét támogat erőforrás és erőforráscsoport szerint. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor lehet elhelyezni egy erőforráson. Ha a virtuális gépet az Azure CLI-vel szeretné felcímkézni, tekintse meg a [virtuális gép címkézése az Azure-ban az Azure parancssori felület használatával](tag-cli.md)című témakört.


A `Get-AzVM` parancsmag használatával megtekintheti a virtuális géphez tartozó címkék aktuális listáját.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Ha a virtuális gép már tartalmaz címkéket, az összes címke megjelenik a lista formátuma területen.

Címkék hozzáadásához használja az `Set-AzResource` parancsot. A címkék a PowerShell használatával történő frissítésekor a címkék teljes egészében frissülnek. Ha olyan erőforráshoz ad hozzá egy címkét, amely már rendelkezik címkékkel, akkor meg kell adnia az erőforráson elhelyezni kívánt címkéket is. Az alábbi példa bemutatja, hogyan adhat hozzá további címkéket egy erőforráshoz a PowerShell-parancsmagok használatával.

Rendelje hozzá a virtuális gép összes aktuális címkéjét a `$tags` változóhoz a `Get-AzResource` és `Tags` tulajdonság használatával.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Az aktuális címkék megjelenítéséhez írja be a változót.

```azurepowershell-interactive
$tags
```

A kimenet a következőhöz hasonló lehet:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

Az alábbi példában egy nevű címkét adunk hozzá `Location` az értékkel `myLocation` . A paranccsal `+=` fűzze hozzá az új kulcs/érték párokat a `$tags` listához.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

A paranccsal `Set-AzResource` állíthatja be az *$Tags* változóban meghatározott összes címkét a virtuális gépen.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

A használatával `Get-AzResource` jelenítheti meg az erőforrás összes címkéjét.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

A kimenetnek az alábbihoz hasonlóan kell kinéznie, amely most már tartalmazza az új címkét:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```


**Következő lépések**

- Az Azure-erőforrások címkézésével kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md) és [címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).
- Ha szeretné megtekinteni, hogyan segíthetnek az Azure-erőforrások használatának kezelésében, tekintse meg [Az Azure-számla megismerése](../cost-management-billing/understand/review-individual-bill.md) és [a Microsoft Azure erőforrás-felhasználás betekintését](../cost-management-billing/manage/usage-rate-card-overview.md)ismertető témakört.
