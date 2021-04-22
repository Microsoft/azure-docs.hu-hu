---
title: Irányítópult létrehozása Azure Portal Azure CLI használatával
description: 'Rövid útmutató: Útmutató irányítópult létrehozásához a Azure Portal Azure CLI használatával. Az irányítópult a felhőbeli erőforrások célzott és rendezett nézete.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.date: 12/4/2020
ms.openlocfilehash: 581c8cc4c2da275467bc39c5c2008b29a5bc0e0e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875754"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Rövid útmutató: Irányítópult Azure Portal létrehozása az Azure CLI használatával

Az irányítópult a Azure Portal a felhőerőforrások célzott és rendezett nézete. Ez a cikk az Azure CLI irányítópultok létrehozására való használatával foglalkozik.
Az irányítópult megjeleníti a virtuális gép (VM) teljesítményét, valamint néhány statikus információt és hivatkozást.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben számlázni tudja az erőforrásokat.
Válasszon ki egy előfizetést az [az account set paranccsal:](/cli/azure/account#az_account_set)

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Hozzon létre [egy Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) [az az group create paranccsal,](/cli/azure/group#az_group_create) vagy használjon egy meglévő erőforráscsoportot:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create paranccsal:](/cli/azure/vm#az_vm_create)

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> A jelszónak bonyolultnak kell lennie.
> Ez egy új felhasználónév és jelszó.
> Ez például nem az a fiók, amely az Azure-ba való bejelentkezéshez használható.
> További információ: [Felhasználónév-követelmények](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) és [jelszókövetelmények.](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

Az üzembe helyezés elindul, és általában néhány percet vesz igénybe.
Az üzembe helyezés befejezése után lépjen tovább a következő szakaszra.

## <a name="download-the-dashboard-template"></a>Az irányítópult-sablon letöltése

Mivel az Azure-irányítópultok erőforrások, JSON-ként is ábrázolhatóak.
További információ: [Az Azure-irányítópultok szerkezete.](./azure-portal-dashboards-structure.md)

Töltse le a következő fájlt: [portal-dashboard-template-testvm.jsa következőn:](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Szabja testre a letöltött sablont a következő értékek értékekre való módosításával:

* `<subscriptionID>`: Az Ön előfizetése
* `<rgName>`: Erőforráscsoport, például `myResourceGroup`
* `<vmName>`: Virtuális gép neve, például `SimpleWinVM`
* `<dashboardTitle>`: Irányítópult címe, például `Simple VM Dashboard`
* `<location>`: Az Ön Azure-régiója, például: `centralus`

További információ: Referencia a [Microsoft Portal irányítópult-sablonjához.](/azure/templates/microsoft.portal/dashboards)

## <a name="deploy-the-dashboard-template"></a>Az irányítópult-sablon üzembe helyezése

Most már üzembe helyezheti a sablont az Azure CLI-ről.

1. Futtassa [az az portal dashboard create parancsot](/cli/azure/portal/dashboard#az_portal_dashboard_create) a sablon üzembe helyezéséhez:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Az az portal dashboard show parancs futtatásával ellenőrizze, hogy az irányítópult sikeresen [létrejött-e:](/cli/azure/portal/dashboard#az_portal_dashboard_show)

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Az aktuális előfizetéshez elérhető összes irányítópultot az az portal dashboard list használatával [láthatja:](/cli/azure/portal/dashboard#az_portal_dashboard_list)

```azurecli
az portal dashboard list
```

Egy erőforráscsoport összes irányítópultját is láthatja:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Az irányítópultot az [az portal dashboard update](/cli/azure/portal/dashboard#az_portal_dashboard_update) paranccsal frissítheti:

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A virtuális gép és a társított irányítópult eltávolításához törölje az azokat tartalmazó erőforráscsoportot.

> [!CAUTION]
> Az alábbi példa törli a megadott erőforráscsoportot és az összes benne lévő erőforrást.
> Ha a cikk hatókörében nem található erőforrások a megadott erőforráscsoportban vannak, akkor azok is törlődnek.

```azurecli
az group delete --name myResourceGroup
```

Ha csak az irányítópultot szeretné eltávolítani, használja [az az portal dashboard delete parancsot:](/cli/azure/portal/dashboard#az_portal_dashboard_delete)

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Következő lépések

Az irányítópultok Azure CLI-támogatásával kapcsolatos további információkért lásd: [az portal dashboard](/cli/azure/portal/dashboard).
