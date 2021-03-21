---
title: 'Gyors útmutató: megosztott lekérdezés létrehozása az Azure CLI-vel'
description: Ebben a rövid útmutatóban az Azure CLI-hez készült Resource Graph-bővítmény engedélyezéséhez és egy megosztott lekérdezés létrehozásához szükséges lépéseket követheti.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ec1b010771790339a13777624f04c7bd2db01f11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594383"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Gyors útmutató: erőforrás-gráf megosztott lekérdezés létrehozása az Azure CLI használatával

Az Azure Resource Graph és az [Azure CLI](/cli/azure/) használatának első lépéseként győződjön meg arról, hogy a bővítmény telepítve van. E rövid útmutató segítségével hozzáadhatja a bővítményt a telepített Azure CLI-hez. A bővítményt helyileg telepített Azure CLI-vel vagy az [Azure Cloud Shellen](https://shell.azure.com) keresztül is használhatja.

A folyamat végén hozzáadta a bővítményt az Azure CLI-telepítéshez, és létrehoz egy Resource Graph megosztott lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>A Resource Graph bővítmény hozzáadása

Annak engedélyezéséhez, hogy az Azure CLI működjön az Azure Resource Graph használatával, hozzá kell adni a bővítményt. Ez a bővítmény mindenhol működik, ahol az Azure CLI használható, beleértve a [Basht Windows 10-en](/windows/wsl/install-win10), a [Cloud Shellt](https://shell.azure.com) (különállón és portálon belülin egyaránt), az [Azure CLI Docker-rendszerképet](https://hub.docker.com/_/microsoft-azure-cli), vagy akár helyileg telepítve is.

1. Győződjön meg arról, hogy a legújabb Azure CLI telepítve van (legalább a **2.8.0**). Ha még nincs telepítve, kövesse [ezeket az utasításokat](/cli/azure/install-azure-cli-windows).

1. Az Azure CLI-környezetében válassza az [az Extension Add](/cli/azure/extension#az_extension_add) parancsot az erőforrás-gráf bővítmény importálásához a következő paranccsal:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Ellenőrizze, hogy telepítve van-e a bővítmény, és a várt verzió (legalább **1.1.0**) az [az Extension List](/cli/azure/extension#az_extension_list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Erőforrás-gráf megosztott lekérdezésének létrehozása

Ha az Azure CLI-bővítmény hozzá van adva a környezethez, itt az ideje, hogy egy erőforrás-gráf megosztott lekérdezés legyen. A megosztott lekérdezés egy Azure Resource Manager objektum, amely engedélyt adhat az Azure Resource Graph Explorerben, vagy futtathatja azt. A lekérdezés a _hely_ szerint csoportosított összes erőforrás számát összegzi.

1. Hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group#az_group_create) paranccsal az Azure Resource Graph megosztott lekérdezés tárolásához. Ez az erőforráscsoport neve `resource-graph-queries` , a helye pedig `westus2` .

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Hozza létre az Azure Resource Graph megosztott lekérdezését a `graph` kiterjesztéssel és az [az Graph Shared-Query Create](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_create) paranccsal:

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Az új erőforráscsoport megosztott lekérdezéseit sorolja fel. Az az [Graph Shared-Query List](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_list) parancs az értékek tömbjét adja vissza.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Csak egyetlen megosztott lekérdezési eredmény eléréséhez használja az az [Graph Shared-Query show](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_show) parancsot.

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Futtassa a megosztott lekérdezést az Azure CLI-ben az az `{{shared-query-uri}}` [gráf Query](/cli/azure/ext/resource-graph/graph#ext_resource_graph_az_graph_query) parancs szintaxisával.
   Először másolja a `id` mezőt az előző parancs eredményéről `show` . Cserélje le a `shared-query-uri` példában szereplő szöveget a mező értékére `id` , de hagyja meg a környező `{{` és a `}}` karaktereket.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > A `{{shared-query-uri}}` szintaxis egy **előzetes** verziójú funkció.

Az erőforrás-gráf megosztott lekérdezésének másik módja a Azure Portal. A portálon a keresősáv használatával keressen rá a "Resource Graph-lekérdezések" kifejezésre. Válassza ki a megosztott lekérdezést. Az **Áttekintés** lapon a **lekérdezés** lapon megjelenik a mentett lekérdezés. A **Szerkesztés** gomb megnyitható az [Erőforrás-diagram Explorerben](./first-query-portal.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani az erőforrás-gráf megosztott lekérdezését, az erőforráscsoportot és a bővítményt az Azure CLI-környezetből, ezt az alábbi parancsokkal végezheti el:

- [az Graph Shared-Query delete](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [Az Extension Remove](/cli/azure/extension#az_extension_remove)

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

Ebben a rövid útmutatóban hozzáadta a Resource Graph-bővítményt az Azure CLI-környezethez, és létrehozott egy megosztott lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)