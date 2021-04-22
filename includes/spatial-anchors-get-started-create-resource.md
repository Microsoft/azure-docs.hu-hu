---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 2c85e26d5a9115b00621c4099e3ed36afb224e3f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879586"
---
## <a name="create-a-spatial-anchors-resource"></a>Erőforrás Spatial Anchors létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Nyissa meg az <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

A bal oldali panelen válassza az **Erőforrás létrehozása lehetőséget.**

A keresőmezővel keressen rá a **Spatial Anchors.**

![Képernyőkép a keresési eredményekről a Spatial Anchors.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Válassza **Spatial Anchors** lehetőséget, majd a **Létrehozás lehetőséget.**

Az **Spatial Anchors panelen** tegye a következőket:

* Adjon meg egy egyedi erőforrásnevet normál alfanumerikus karakterek használatával.
* Válassza ki azt az előfizetést, amelyhez az erőforrást csatolni szeretné.
* Hozzon létre egy erőforráscsoportot az **Új létrehozása lehetőség kiválasztásával.** Adja neki a **myResourceGroup nevet,** majd kattintson az **OK gombra.**

  [!INCLUDE [resource group intro text](resource-group.md)]

* Válassza ki azt a helyet (régiót), ahol az erőforrást el kell helyezze.
* Az **erőforrás létrehozásának** megkezdéséhez válassza az Új lehetőséget.

![Az erőforrás Spatial Anchors panel képernyőképe.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Az erőforrás létrehozása után a Azure Portal jelzi, hogy az üzembe helyezés befejeződött.

![Képernyőkép az erőforrás üzembe helyezésének befejezéséről.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Válassza az **Erőforrás megnyitása** lehetőséget. Most már megtekintheti az erőforrás-tulajdonságokat.

Másolja az erőforrás **Account ID (Fiókazonosító)** értékét egy szövegszerkesztőbe későbbi használatra.

![Képernyőkép az erőforrás-tulajdonságok panelről.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Másolja az erőforrás Account **Domain (Fióktartomány) értékét** is egy szövegszerkesztőbe későbbi használatra.

![Képernyőkép az erőforrás fióktartományának értékével.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

A **Beállítások alatt** válassza a Kulcs **lehetőséget.** Másolja az **Elsődleges kulcs** értékét **(Fiókkulcs)** egy szövegszerkesztőbe későbbi használatra.

![A fiók Kulcsok panelének képernyőképe.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először készítse elő a környezetet az Azure CLI-hez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. Bejelentkezés után az [az account set](/cli/azure/account#az_account_set) paranccsal válassza ki azt az előfizetést, amelyben be kell állítania a spatial anchors-fiókot:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Futtassa [az az group create](/cli/azure/group#az_group_create) parancsot egy erőforráscsoport létrehozásához, vagy használjon egy meglévő erőforráscsoportot:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Az erőforráscsoport aktuális spatial [anchors-fiókjait az az spatial-anchors-account list paranccsal tudja](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_list) megtekinteni:

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Az előfizetés spatial anchors-fiókjait is megtekintheti:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Futtassa [az az spatial-anchors-account create parancsot](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_create) a spatial anchors-fiók létrehozásához:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Tekintse meg az erőforrás-tulajdonságokat [az az spatial-anchors-account show paranccsal:](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_show)

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Másolja az erőforrás **Account ID (Fiókazonosító)** és az **resource Account Domain** (Fióktartomány) értékét egy szövegszerkesztőbe későbbi használatra.

1. Futtassa [az az spatial-anchors-account key show parancsot](/cli/azure/spatial-anchors-account/key#az_spatial_anchors_account_key_show) az elsődleges és másodlagos kulcsok lekért útjára:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Másolja a kulcsértékeket egy szövegszerkesztőbe későbbi használatra.

   Ha újra létre kell hoznia kulcsokat, használja [az az spatial-anchors-account key renew](/cli/azure/spatial-anchors-account/key#az_spatial_anchors_account_key_renew) parancsot:

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

A fiókokat az [az spatial-anchors-account delete](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_delete) paranccsal törölheti:

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Először készítse elő a környezetet az Azure PowerShellhez:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Bár az **Az.MixedReality** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával. Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. Miután bejelentkezett, a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával válassza ki azt az előfizetést, amelyben a spatial anchors-fiókot be kell állítania:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Futtassa a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmagot egy erőforráscsoport létrehozásához, vagy használjon egy meglévő erőforráscsoportot:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Az erőforráscsoport aktuális spatial anchors-fiókjait a [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) parancsmaggal tudja megtekinteni:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   Az előfizetés spatial anchors-fiókjait is megtekintheti:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Futtassa [a New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) parancsmagot a spatial anchors-fiók létrehozásához:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. Tekintse meg az erőforrás-tulajdonságokat a [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) parancsmaggal:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Másolja az **accountId** és az **accountDomain** tulajdonság értékét egy szövegszerkesztőbe későbbi használatra.

1. Futtassa [a Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) parancsmagot az elsődleges és a másodlagos kulcsok lefuttatására:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Másolja a kulcsértékeket egy szövegszerkesztőbe későbbi használatra.

   Ha újra létre kell hoznia kulcsokat, használja a [New-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey) parancsmagot:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

A fiókokat a [Remove-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount) parancsmaggal törölheti:

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
