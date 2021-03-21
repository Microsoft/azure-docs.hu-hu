---
title: 'Rövid útmutató: Azure dedikált HSM létrehozása Azure PowerShell'
description: Azure Dedicated HSM létrehozása az Azure PowerShell használatával
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.date: 11/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eebfb257d0324cf2771bd3af979ddbebb8429fb7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94905621"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>Rövid útmutató: Azure dedikált HSM létrehozása Azure PowerShell

Ez a cikk bemutatja, hogyan hozhat létre Azure dedikált HSM-t az az [. dedikált HSM](/powershell/module/az.dedicatedhsm) PowerShell-modul használatával.

## <a name="requirements"></a>Követelmények

* Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Míg az az **. dedikált HSM** PowerShell-modul előzetes verzióban érhető el, a parancsmaggal külön kell telepítenie `Install-Module` . Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon ki egy adott előfizetést a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példa egy erőforráscsoportot hoz létre a megadott névvel és a megadott helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>Dedikált HSM létrehozása

Dedikált HSM létrehozásához használja a [New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm) parancsmagot. A következő példa egy dedikált HSM-t hoz létre a megadott előfizetésben.

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

## <a name="get-a-dedicated-hsm"></a>Dedikált HSM beszerzése

Egy meglévő dedikált HSM információinak lekéréséhez használja a [Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm) parancsmagot. A következő példa lekéri a megadott dedikált HSM-et.

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>Dedikált HSM frissítése

Dedikált HSM frissítéséhez használja az [Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm) parancsmagot. Az alábbi példa egy dedikált HSM-et frissít a megadott előfizetésben.

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

Ha a cikkben létrehozott erőforrások nem szükségesek, az alábbi példák futtatásával törölheti őket.

### <a name="remove-a-dedicated-hsm"></a>Dedikált HSM eltávolítása

A dedikált HSM eltávolításához használja a [Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm) parancsmagot. A következő példa törli a megadott dedikált HSM-et.

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a cikk hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>Következő lépések

További információ az [Azure DEDIKÁLT HSM](overview.md)-ről.
