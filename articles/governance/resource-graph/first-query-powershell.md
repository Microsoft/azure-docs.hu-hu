---
title: 'Rövid útmutató: Az első PowerShell-lekérdezés'
description: Ebben a rövid útmutatóban a lépéseket követve engedélyezheti a Resource Graph modult a Azure PowerShell az első lekérdezés futtatásához.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-api
ms.openlocfilehash: e5e276e3be80354eeaaeba2821eb9e3242b368ad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533060"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Rövid útmutató: Az első lekérdezés Resource Graph futtatása a Azure PowerShell

Az Azure Resource Graph használatához először meg kell győződnie arról, hogy az Azure PowerShellhez szükséges modul telepítve van. E rövid útmutató segítségével hozzáadhatja a modult a telepített Azure PowerShellhez.

Ezen folyamatban a modult hozzáadja az Azure PowerShell-telepítéshez, és futtatja az első Resource Graph-lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>A Resource Graph modul hozzáadása

Ahhoz, hogy az Azure PowerShell lekérdezhesse az Azure Resource Graphot, el kell végezni a modul hozzáadását. Ez a modul használható helyileg telepített PowerShell-parancsokkal, [a Azure Cloud Shell](https://shell.azure.com)vagy a [PowerShell Docker-rendszerképpel.](https://hub.docker.com/_/microsoft-powershell)

### <a name="base-requirements"></a>Alapszintű követelmények

Az Azure Resource Graph modulhoz az alábbi szoftverek szükségesek:

- Azure PowerShell 1.0.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1-es vagy újabb verzió. Ha nincs telepítve vagy frissítve, kövesse [ezeket az utasításokat](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>A modul telepítése

A PowerShell Resource Graph modulja az **Az.ResourceGraph.**

1. Egy **rendszergazdai** PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Ellenőrizze, hogy a modul importálva lett-e, és hogy a legújabb verziójú -e (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Miután az Azure PowerShell modul hozzá lett adva a választott környezethez, ideje futtatni egy egyszerű Resource Graph-lekérdezést. A lekérdezés visszaadja az első  öt Azure-erőforrást az egyes erőforrások nevével **és** erőforrástípusával.

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
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancsrend először korlátozza a lekérdezési eredményeket, majd megrendeli azokat.

1. Először frissítse a lekérdezést, hogy a **Name** tulajdonság szerint legyen rendezve (`order by`), majd korlátozza (`limit`) az első öt találatra:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Ha a végső lekérdezés többször is lefut, feltéve, hogy a környezetben semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** (Név) tulajdonság szerint vannak megrendelve, de továbbra is az első öt eredményre korlátozódnak.

> [!NOTE]
> Ha a lekérdezés nem ad vissza eredményeket egy olyan előfizetésből, amelyhez már hozzáféréssel rendelkezik, vegye figyelembe, hogy a parancsmag alapértelmezés szerint az előfizetéseket használja az `Search-AzGraph` alapértelmezett környezetben. Az alapértelmezett környezetben található előfizetés-előfizetői adatok listájának eléréséhez futtassa ezt a következőt: Ha az összes olyan előfizetésben szeretne keresni, amelyhez hozzáféréssel rendelkezik, a parancsmag `(Get-AzContext).Account.ExtendedProperties.Subscriptions` PSDefaultParameterValues beállításához futtassa a következőt: `Search-AzGraph``$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a Resource Graph modult az Azure PowerShell-környezetből, ezt a következő paranccsal teheti meg:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Ezzel nem törli a korábban letöltött modulfájlt. Csak eltávolítja azt a futó PowerShell-munkamenetből.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzáadta a Resource Graph modult a Azure PowerShell környezethez, és futtatta az első lekérdezését. Ha többet szeretne megtudni a Resource Graph nyelvről, folytassa a lekérdezési nyelv részleteit tartalmazó oldalon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)
