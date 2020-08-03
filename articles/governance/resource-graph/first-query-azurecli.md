---
title: 'Rövid útmutató: az első Azure CLI-lekérdezés'
description: Ebben a rövid útmutatóban az Azure CLI-hez készült Resource Graph-bővítmény engedélyezéséhez és az első lekérdezés futtatásához szükséges lépéseket követheti.
ms.date: 05/20/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 82e36027e5016554770fc9359f413c1fff6fe227
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500674"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása az Azure CLI használatával

Az Azure Resource Graph használatához először is meg kell győződnie arról, hogy az [Azure CLI-hez](/cli/azure/) szükséges bővítmény telepítve van. E rövid útmutató segítségével hozzáadhatja a bővítményt a telepített Azure CLI-hez. A bővítményt helyileg telepített Azure CLI-vel vagy az [Azure Cloud Shellen](https://shell.azure.com) keresztül is használhatja.

A folyamat végére a bővítmény hozzá lesz adva a kiválasztott Azure CLI-telepítéshez, és máris futtathatja az első Resource Graph-lekérdezését.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>A Resource Graph bővítmény hozzáadása

Ahhoz, hogy az Azure CLI lekérdezhesse az Azure Resource Graph-ot, el kell végezni a bővítmény hozzáadását. Ez a bővítmény mindenhol működik, ahol az Azure CLI használható, beleértve a [Basht Windows 10-en](/windows/wsl/install-win10), a [Cloud Shellt](https://shell.azure.com) (különállón és portálon belülin egyaránt), az [Azure CLI Docker-rendszerképet](https://hub.docker.com/r/microsoft/azure-cli/), vagy akár helyileg telepítve is.

1. Győződjön meg arról, hogy a legújabb Azure CLI telepítve van (legalább **2.0.76**). Ha még nincs telepítve, kövesse [ezeket az utasításokat](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. A választott Azure CLI környezetben importálja a bővítményt a következő paranccsal:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Ellenőrizze, hogy a bővítmény telepítve van-e, és a várt verzió-e (legalább **1.0.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Miután az Azure CLI-bővítmény hozzá lett adva a választott környezethez, ideje futtatni egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást fogja visszaadni az egyes erőforrások **nevével** és **erőforrástípusával**.

1. Futtassa az első Azure Resource Graph-lekérdezését a `graph` bővítmény és a `query` parancs használatával:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Frissítse a lekérdezést úgy, hogy a rendezési szempont (`order by`) a **Name** tulajdonság legyen:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancs először a lekérdezés eredményeit korlátozza, majd megrendeli azokat.

1. Először frissítse a lekérdezést, hogy a **Name** tulajdonság szerint legyen rendezve (`order by`), majd korlátozza (`limit`) az első öt találatra:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** tulajdonság szerint vannak rendezve, de továbbra is az első öt találatra korlátozódnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a Resource Graph bővítményt az Azure CLI környezetből, ezt a következő paranccsal teheti meg:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban hozzáadta a Resource Graph-bővítményt az Azure CLI-környezethez, és futtatta az első lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)
