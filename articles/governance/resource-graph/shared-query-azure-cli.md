---
title: 'Rövid útmutató: Megosztott lekérdezés létrehozása az Azure CLI használatával'
description: Ebben a rövid útmutatóban a lépéseket követve engedélyezheti a Resource Graph bővítményt az Azure CLI-hez, és létrehozhat egy megosztott lekérdezést.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 2befc2b6895cd9d2c797fc8ed0e28a27eb6e73e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870552"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Rövid útmutató: Megosztott Resource Graph létrehozása az Azure CLI használatával

Az Azure [CLI-Azure Resource Graph](/cli/azure/) használatának első lépése annak ellenőrzése, hogy a bővítmény telepítve van-e. E rövid útmutató segítségével hozzáadhatja a bővítményt a telepített Azure CLI-hez. A bővítményt helyileg telepített Azure CLI-vel vagy az [Azure Cloud Shellen](https://shell.azure.com) keresztül is használhatja.

A folyamat végén hozzáadja a bővítményt a választott Azure CLI-telepítéshez, és létrehoz egy Resource Graph lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>A Resource Graph bővítmény hozzáadása

Ahhoz, hogy az Azure CLI működjön a Azure Resource Graph, hozzá kell adni a bővítményt. Ez a bővítmény mindenhol működik, ahol az Azure CLI használható, beleértve a [Basht Windows 10-en](/windows/wsl/install-win10), a [Cloud Shellt](https://shell.azure.com) (különállón és portálon belülin egyaránt), az [Azure CLI Docker-rendszerképet](https://hub.docker.com/_/microsoft-azure-cli), vagy akár helyileg telepítve is.

1. Ellenőrizze, hogy telepítve van-e a legújabb Azure CLI **(legalább 2.8.0).** Ha még nincs telepítve, kövesse [ezeket az utasításokat](/cli/azure/install-azure-cli-windows).

1. A választott Azure CLI-környezetben az [az extension add](/cli/azure/extension#az_extension_add) paranccsal importálja Resource Graph bővítményt:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Ellenőrizze, hogy a bővítmény telepítve van-e, és a várt verzió -e **(legalább 1.1.0),** [és hogy az az extension list:](/cli/azure/extension#az_extension_list)

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Megosztott Resource Graph létrehozása

Most, hogy az Azure CLI-bővítmény hozzá van adva a választott környezethez, itt az ideje egy Resource Graph lekérdezésnek. A megosztott lekérdezés egy Azure Resource Manager objektum, amely számára engedélyeket adhat vagy futtathat Azure Resource Graph Explorerben. A lekérdezés hely szerint csoportosítva összegzi az összes erőforrás _számát._

1. Hozzon létre egy erőforráscsoportot [az az group create](/cli/azure/group#az_group_create) használatával a megosztott Azure Resource Graph tárolására. Ennek az erőforráscsoportnak a neve, `resource-graph-queries` a helye pedig `westus2` .

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Hozza létre Azure Resource Graph lekérdezést a bővítmény `graph` és az az graph [shared-query create paranccsal:](/cli/azure/graph/shared-query#az_graph_shared_query_create)

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Listába sorolja az új erőforráscsoportban található megosztott lekérdezéseket. Az [az graph shared-query list parancs](/cli/azure/graph/shared-query#az_graph_shared_query_list) értéktömböt ad vissza.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Egyetlen megosztott lekérdezési eredmény lekérdezhető az [az graph shared-query show paranccsal.](/cli/azure/graph/shared-query#az_graph_shared_query_show)

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Futtassa a megosztott lekérdezést az Azure CLI-ban az az graph query parancs `{{shared-query-uri}}` [szintaxisával.](/cli/azure/graph#az_graph_query)
   Először másolja ki `id` a mezőt az előző parancs `show` eredményből. Cserélje `shared-query-uri` le a példában szereplő szöveget a mező `id` értékére, de hagyja meg a körüli `{{` és `}}` karaktereket.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > A `{{shared-query-uri}}` szintaxis egy előzetes **verziójú** funkció.

A megosztott lekérdezések keresésének Resource Graph is a Azure Portal. A portálon a keresősávban keressen rá a "lekérdezések Resource Graph keresésre". Válassza ki a megosztott lekérdezést. Az Áttekintés **lapon** a Lekérdezés **lapon** megjelenik a mentett lekérdezés. A **Szerkesztés** gomb megnyitja a [Resource Graph Explorerben.](./first-query-portal.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a Resource Graph lekérdezést, erőforráscsoportot és bővítményt az Azure CLI-környezetből, ezt a következő parancsokkal használhatja:

- [az graph shared-query delete](/cli/azure/graph/shared-query#az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [az extension remove](/cli/azure/extension#az_extension_remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzáadta a Resource Graph bővítményt az Azure CLI-környezethez, és létrehozott egy megosztott lekérdezést. Ha többet szeretne megtudni a Resource Graph nyelvről, folytassa a lekérdezési nyelv részleteit tartalmazó oldalon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)