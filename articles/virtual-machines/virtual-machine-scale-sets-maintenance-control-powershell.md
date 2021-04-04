---
title: Az operációsrendszer-rendszerkép frissítésének karbantartási szabályozása Azure-beli virtuálisgép-méretezési csoportokon a PowerShell használatával
description: Megtudhatja, hogyan szabályozhatja, hogy az operációs rendszer rendszerképének automatikus frissítése hogyan történjen az Azure virtuálisgép-méretezési csoportokban a karbantartási és a PowerShell használatával.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91974838"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>Előzetes verzió: az operációsrendszer-rendszerkép frissítésének karbantartási szabályozása Azure-beli virtuálisgép-méretezési csoportokon a PowerShell használatával

A karbantartási vezérléssel eldöntheti, hogy mikor alkalmazza a vendég operációs rendszer rendszerképének automatikus frissítését a virtuálisgép-méretezési csoportokra. Ez a témakör a karbantartás-vezérlés Azure PowerShell beállításait ismerteti. További információ a karbantartási vezérlők használatáról: [Az Azure-beli virtuálisgép-méretezési csoportok karbantartási vezérlése](virtual-machine-scale-sets-maintenance-control.md).

> [!IMPORTANT]
> Az operációsrendszer-képek Azure-beli virtuálisgép-méretezési csoportokon történő frissítésének karbantartási ellenőrzése jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="enable-the-powershell-module"></a>A PowerShell-modul engedélyezése

Győződjön `PowerShellGet` meg róla, hogy naprakész.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Telepítse a `Az.Maintenance` PowerShell-modult.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Ha helyileg telepíti a rendszert, győződjön meg róla, hogy rendszergazdaként megnyitja a PowerShell-parancssort.

Azt is megteheti, hogy meg kell erősítenie, hogy nem *megbízható adattárból* kíván telepíteni. Írja be, `Y` vagy válassza az **Igen** lehetőséget az összes elemre a modul telepítéséhez.

## <a name="connect-to-an-azure-account"></a>Kapcsolódás Azure-fiókhoz

Kapcsolódjon a kívánt Azure-fiókhoz a [AzAccount](/powershell/module/az.accounts/connect-azaccount) és a [set-AzAccount](/powershell/module/az.accounts/set-azcontext)használatával.

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>Karbantartási konfiguráció létrehozása

Hozzon létre egy erőforráscsoportot tárolóként a konfigurációhoz. Ebben a példában egy *myMaintenanceRG* nevű erőforráscsoportot hoznak létre a *eastus2*-ben. Ha már rendelkezik egy használni kívánt erőforráscsoporthoz, akkor kihagyhatja ezt a részt. Csak cserélje le az erőforráscsoport nevét a többi példán a sajátra.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

A [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) használatával hozzon létre egy karbantartási konfigurációt. Ez a példa létrehoz egy *konfig* nevű karbantartási konfigurációt az operációs rendszer rendszerképéhez. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> A karbantartás **időtartamának** *5 óra* vagy hosszabbnak kell lennie. A karbantartási **ismétlődést** *napi* értékre kell beállítani.

A használata `-MaintenanceScope OSImage` biztosítja, hogy a karbantartási konfiguráció a vendég operációs rendszer frissítéseinek vezérlésére szolgáljon.

Ha ugyanazzal a névvel próbál létrehozni egy konfigurációt, de egy másik helyen, hibaüzenetet kap. A konfigurációs névnek egyedinek kell lennie az erőforráscsoport számára.

A [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)használatával lekérdezheti a rendelkezésre álló karbantartási konfigurációkat.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>A virtuálisgép-méretezési csoport hozzárendelése a karbantartási konfigurációhoz

Egy virtuálisgép-méretezési csoport bármilyen karbantartási konfigurációhoz társítható, a karbantartási konfiguráció régiójától és előfizetéstől függetlenül. Ha a karbantartási konfigurációt választotta, a méretezési csoport új rendszerkép-frissítései automatikusan a következő rendelkezésre álló karbantartási időszakra lesznek ütemezve.

A [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) használatával rendeljen hozzá egy virtuálisgép-méretezési készletet a karbantartási konfigurációhoz.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>Az operációs rendszer automatikus frissítésének engedélyezése

Engedélyezheti az operációs rendszer automatikus frissítését minden olyan virtuálisgép-méretezési csoport esetében, amely a karbantartási vezérlést fogja használni. A virtuálisgép-méretezési csoport automatikus operációsrendszer-frissítéseinek engedélyezéséhez tekintse meg az Azure-beli [virtuálisgép-méretezési csoport automatikus operációsrendszer-rendszerképének frissítését](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) ismertető dokumentumot. 


## <a name="next-steps"></a>Következő lépések

Ismerje meg az Azure-ban futó virtuális gépek karbantartását és frissítéseit.

> [!div class="nextstepaction"]
> [Karbantartás és frissítések](maintenance-and-updates.md)