---
title: Azure-beli egyéni erőforrás-szolgáltató létrehozása Azure PowerShell
description: Útmutató Azure-beli egyéni erőforrás-szolgáltató létrehozásához Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.topic: quickstart
ms.devlang: azurepowershell
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6912f7f6fdc88c5d611bfbfd78f15e5f7a949f70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91951842"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Rövid útmutató: Azure egyéni erőforrás-szolgáltató létrehozása Azure PowerShell

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre saját Azure-beli egyéni erőforrás-szolgáltatót az az [. CustomProviders](/powershell/module/az.customproviders) PowerShell-modul használatával.

> [!CAUTION]
> Az Azure Custom Providers szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés. Az előzetes verzió használata NEM javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Követelmények

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](/powershell/azure/install-az-ps). Ha a Cloud Shell használatát választja, további információt [a Azure Cloud Shell áttekintése](../../cloud-shell/overview.md) című témakörben talál.

> [!IMPORTANT]
> Míg az az **. CustomProviders** PowerShell-modul előzetes verzióban érhető el, a parancsmaggal külön kell telepítenie `Install-Module` . Miután a PowerShell-modul általánosan elérhetővé válik, az a PowerShell-modul kiadásainak részévé válik, és natív módon elérhető lesz a Azure Cloud Shellon belülről.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon ki egy adott előfizetést a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példa egy erőforráscsoportot hoz létre a megadott névvel és a megadott helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Egyéni erőforrás-szolgáltató létrehozása

Egyéni erőforrás-szolgáltató létrehozásához vagy frissítéséhez használja a [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) parancsmagot az alábbi példában látható módon.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Az egyéni erőforrás-szolgáltatói jegyzékfájl beszerzése

Az egyéni erőforrás-szolgáltatói jegyzékre vonatkozó információk lekéréséhez használja a [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) parancsmagot az alábbi példában látható módon.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Társítás létrehozása

Társítás létrehozásához vagy frissítéséhez használja a [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) parancsmagot az alábbi példában látható módon.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Társítás beszerzése

Egy társítással kapcsolatos információk lekéréséhez használja a [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) parancsmagot az alábbi példában látható módon.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a cikkben létrehozott erőforrások nem szükségesek, az alábbi példák futtatásával törölheti őket.

### <a name="delete-an-association"></a>Társítás törlése

Egy társítás eltávolításához használja a [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) parancsmagot. A következő példa egy társítást töröl.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Egyéni erőforrás-szolgáltató törlése

Egyéni erőforrás-szolgáltató eltávolításához használja a [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) parancsmagot. Az alábbi példa egy egyéni erőforrás-szolgáltatót töröl.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a cikk hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

További információ az [Azure egyéni erőforrás-szolgáltatókkal](overview.md)kapcsolatban.