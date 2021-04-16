---
title: 'Rövid útmutató: Azure Dedicated HSM létrehozása Azure PowerShell'
description: Azure Dedicated HSM létrehozása az Azure PowerShell használatával
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/13/2020
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: d5b6d0399ceb98caf9bdd7bbd725e6d92af0eaa3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537790"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>Rövid útmutató: Azure Dedicated HSM létrehozása Azure PowerShell

Ez a cikk bemutatja, hogyan hozhat létre Azure Dedicated HSM [az Az.DedicatedHsm](/powershell/module/az.dedicatedhsm) PowerShell-modullal.

## <a name="requirements"></a>Követelmények

* Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Bár az **Az.DedicatedHsm** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával. Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat ki kell számlázni. Válasszon ki egy adott előfizetést a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre [egy Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

Az alábbi példa egy erőforráscsoportot hoz létre a megadott névvel és a megadott helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>Dedikált HSM létrehozása

Dedikált HSM létrehozásához használja a [New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm) parancsmagot. Az alábbi példa egy dedikált HSM-et hoz létre a megadott előfizetésben.

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>Dedikált HSM lekért

Egy meglévő dedikált HSM-ről a [Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm) parancsmag használatával lehet információkat lekérni. Az alábbi példa lekérte a megadott dedikált HSM-et.

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>Dedikált HSM frissítése

Dedikált HSM frissítéséhez használja az [Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm) parancsmagot. Az alábbi példa egy dedikált HSM-et frissíti a megadott előfizetésben.

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a cikkben létrehozott erőforrásokra nincs szükség, az alábbi példák futtatásával törölheti őket.

### <a name="remove-a-dedicated-hsm"></a>Dedikált HSM eltávolítása

Dedikált HSM eltávolításához használja a [Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm) parancsmagot. Az alábbi példa törli a megadott dedikált HSM-et.

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

> [!CAUTION]
> Az alábbi példa törli a megadott erőforráscsoportot és a benne lévő összes erőforrást.
> Ha a cikk hatóköre kívül esik a megadott erőforráscsoportban, akkor azok is törlődnek.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Dedicated HSM.](overview.md)
