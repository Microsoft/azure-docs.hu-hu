---
title: 'Rövid útmutató: az első PowerShell-lekérdezés'
description: Ebben a rövid útmutatóban a következő lépésekkel engedélyezheti a Azure PowerShell Resource Graph-modult, és futtathatja az első lekérdezést.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 131bed4fe60035682a317e186f11561bc005b298
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917673"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása a Azure PowerShell használatával

Az Azure Resource Graph használatához először meg kell győződnie arról, hogy az Azure PowerShellhez szükséges modul telepítve van. E rövid útmutató segítségével hozzáadhatja a modult a telepített Azure PowerShellhez.

Ezen folyamatban a modult hozzáadja az Azure PowerShell-telepítéshez, és futtatja az első Resource Graph-lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>A Resource Graph modul hozzáadása

Ahhoz, hogy az Azure PowerShell lekérdezhesse az Azure Resource Graphot, el kell végezni a modul hozzáadását. Ez a modul helyileg telepített PowerShell-lel, [Azure Cloud Shell](https://shell.azure.com)vagy a [PowerShell Docker-lemezképpel](https://hub.docker.com/_/microsoft-powershell)is használható.

### <a name="base-requirements"></a>Alapszintű követelmények

Az Azure Resource Graph modulhoz az alábbi szoftverek szükségesek:

- Azure PowerShell 1.0.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1-es vagy újabb verzió. Ha nincs telepítve vagy frissítve, kövesse [ezeket az utasításokat](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>A modul telepítése

A PowerShellhez készült Resource Graph-modul az **az. ResourceGraph**.

1. **Rendszergazdai** PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Ellenőrizze, hogy a modul importálása megtörtént-e, és a legújabb verzió-e (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Miután az Azure PowerShell modul hozzá lett adva a választott környezethez, ideje futtatni egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást adja vissza az egyes erőforrások **nevével** és **erőforrás-típusával** .

1. Futtassa az első Azure Resource Graph-lekérdezését a `Search-AzGraph` parancsmag használatával:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Frissítse a lekérdezést úgy, hogy a rendezési szempont (`order by`) a **Name** tulajdonság legyen:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancs először a lekérdezés eredményeit korlátozza, majd megrendeli azokat.

1. Először frissítse a lekérdezést, hogy a **Name** tulajdonság szerint legyen rendezve (`order by`), majd korlátozza (`limit`) az első öt találatra:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** tulajdonság szerint vannak rendezve, de továbbra is az első öt találatra korlátozódnak.

> [!NOTE]
> Ha a lekérdezés nem ad vissza olyan előfizetésből származó eredményeket, amelyhez már van hozzáférése, akkor vegye figyelembe, hogy `Search-AzGraph` a parancsmag alapértelmezett környezetében az előfizetések alapértelmezés szerint szerepelnek. Ha szeretné megtekinteni az alapértelmezett környezet részét képező előfizetési azonosítók listáját `(Get-AzContext).Account.ExtendedProperties.Subscriptions` , akkor az összes Ön által elérhető előfizetésben megadhatja a PSDefaultParameterValues a `Search-AzGraph` parancsmag futtatásával. `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a Resource Graph modult az Azure PowerShell-környezetből, ezt a következő paranccsal teheti meg:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Ez nem törli a korábban letöltött Module-fájlt. Csak eltávolítja azt a futó PowerShell-munkamenetből.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban felvette az erőforrás-gráf modult a Azure PowerShell környezetbe, és futtatta az első lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)