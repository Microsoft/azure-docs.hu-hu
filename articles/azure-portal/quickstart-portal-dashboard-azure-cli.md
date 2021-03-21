---
title: Azure Portal irányítópult létrehozása az Azure CLI-vel
description: 'Gyors útmutató: útmutató az irányítópultok létrehozásához a Azure Portal az Azure CLI használatával. Az irányítópult a Felhőbeli erőforrások célzott és rendezett nézete.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 12/4/2020
ms.openlocfilehash: ddfee1932c6887c6ca7593ca7a28c03e68e09899
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104613211"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Rövid útmutató: Azure Portal irányítópult létrehozása az Azure CLI-vel

A Azure Portal egyik irányítópultja a Felhőbeli erőforrások célzott és rendezett nézete. Ebből a cikkből megtudhatja, hogyan hozhat létre irányítópultot az Azure CLI használatával.
Az irányítópulton egy virtuális gép (VM) teljesítménye, valamint néhány statikus információ és hivatkozás látható.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben számlázni kívánja az erőforrásokat.
Válasszon egy előfizetést az az [Account set](/cli/azure/account#az_account_set) parancs használatával:

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) az az [Group Create](/cli/azure/group#az_group_create) paranccsal, vagy használjon egy meglévő erőforráscsoportot:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az az [VM Create](/cli/azure/vm#az_vm_create) paranccsal:

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> A jelszónak összetettnak kell lennie.
> Ez egy új Felhasználónév és jelszó.
> Nem például az Azure-ba való bejelentkezéshez használt fiók.
> További információ: a [felhasználónévre vonatkozó követelmények](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) és a [jelszóra vonatkozó követelmények](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Az üzembe helyezés elindul, és általában néhány percet vesz igénybe.
Az üzembe helyezés befejezése után lépjen tovább a következő szakaszra.

## <a name="download-the-dashboard-template"></a>Az irányítópult-sablon letöltése

Mivel az Azure-irányítópultok erőforrások, JSON-ként is képviseltetik magukat.
További információ: [Az Azure-irányítópultok szerkezete](./azure-portal-dashboards-structure.md).

Töltse le a következő fájlt: [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

A letöltött sablon testreszabásához módosítsa a következő értékeket az értékekre:

* `<subscriptionID>`: Az Ön előfizetése
* `<rgName>`: Erőforráscsoport, például `myResourceGroup`
* `<vmName>`: Virtuális gép neve, például `SimpleWinVM`
* `<dashboardTitle>`: Irányítópult címe, például `Simple VM Dashboard`
* `<location>`: Az Azure-régiója, például `centralus`

További információ: a [Microsoft Portal irányítópultok sablonjának referenciája](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Az irányítópult-sablon üzembe helyezése

Mostantól üzembe helyezheti a sablont az Azure CLI-n belül.

1. Futtassa az az [Portal irányítópult Create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) parancsot a sablon üzembe helyezéséhez:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Az az [Portal irányítópult show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) parancs futtatásával győződjön meg arról, hogy az irányítópult sikeresen létrejött.

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Az aktuális előfizetés összes irányítópultjának megjelenítéséhez használja az [az Portal Dashboard List](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Az erőforráscsoport összes irányítópultját is megtekintheti:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Az irányítópultot az az [Portal Dashboard Update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) paranccsal frissítheti:

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A virtuális gép és a hozzá tartozó irányítópult eltávolításához törölje az azokat tartalmazó erőforráscsoportot.

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a cikk hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurecli
az group delete --name myResourceGroup
```

Csak az irányítópult eltávolításához használja az az [Portal irányítópult delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete) parancsot:

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Következő lépések

További információ az irányítópultok Azure CLI-támogatásáról: [az portál irányítópultja](/cli/azure/ext/portal/portal/dashboard).
