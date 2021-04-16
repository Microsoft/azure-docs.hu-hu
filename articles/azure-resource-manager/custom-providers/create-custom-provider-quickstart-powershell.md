---
title: Egyéni Azure-erőforrás-szolgáltató létrehozása Azure PowerShell
description: Leírja, hogyan hozhat létre egyéni Azure-erőforrás-szolgáltatót Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.date: 09/22/2020
ms.topic: quickstart
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9c19eb41210b6fba1935a0d158c8240375f4f8f5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533911"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Rövid útmutató: Egyéni Azure-erőforrás-szolgáltató létrehozása Azure PowerShell

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre saját egyéni Azure-erőforrás-szolgáltatót az [Az.CustomProviders](/powershell/module/az.customproviders) PowerShell-modullal.

> [!CAUTION]
> Az Azure Custom Providers jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés. Az előzetes verzió használata NEM javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Követelmények

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az Az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal. További információ az Az PowerShell-modul telepítéséről: [Install Azure PowerShell](/powershell/azure/install-az-ps). Ha úgy dönt, hogy a Cloud Shell használja, további információt [a](../../cloud-shell/overview.md) Azure Cloud Shell áttekintésében talál.

> [!IMPORTANT]
> Bár az **Az.CustomProviders** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával. Amint ez a PowerShell-modul általánosan elérhetővé válik, a későbbi Az PowerShell-modul kiadásának része lesz, és natív módon elérhető a Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat ki kell számlázni. Válasszon ki egy adott előfizetést a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre [egy Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

Az alábbi példa egy erőforráscsoportot hoz létre a megadott névvel és a megadott helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Egyéni erőforrás-szolgáltató létrehozása

Egyéni erőforrás-szolgáltató létrehozásához vagy frissítéséhez használja a [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) parancsmagot az alábbi példában látható módon.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Az egyéni erőforrás-szolgáltató jegyzékfájljának lekért

Az egyéni erőforrás-szolgáltató jegyzékfájljával kapcsolatos információk lekéréshez használja a [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) parancsmagot az alábbi példában látható módon.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Társítás létrehozása

Társítás létrehozásához vagy frissítéséhez használja a [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) parancsmagot az alábbi példában látható módon.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Társítás lekérte

Egy társítással kapcsolatos információk lekérése a [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) parancsmag használatával, az alábbi példában látható módon.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a cikkben létrehozott erőforrásokra nincs szükség, az alábbi példák futtatásával törölheti őket.

### <a name="delete-an-association"></a>Társítás törlése

Társítás eltávolításához használja a [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) parancsmagot. Az alábbi példa egy társítást töröl.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Egyéni erőforrás-szolgáltató törlése

Egyéni erőforrás-szolgáltató eltávolításához használja a [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) parancsmagot. Az alábbi példa töröl egy egyéni erőforrás-szolgáltatót.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

> [!CAUTION]
> Az alábbi példa törli a megadott erőforráscsoportot és a benne lévő összes erőforrást.
> Ha a cikk hatókörében kívüli erőforrások a megadott erőforráscsoportban vannak, akkor azok is törlődnek.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

További információ az [Azure egyéni erőforrás-szolgáltatóiról.](overview.md)
