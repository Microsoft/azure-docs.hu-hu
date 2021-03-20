---
title: Virtual Machine Scale Sets kezelése Azure PowerShell
description: Általános Azure PowerShell parancsmagok a Virtual Machine Scale Sets kezeléséhez, például a példányok elindításához és leállításához, illetve a méretezési csoport kapacitásának módosításához.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: eee4dd7fae872f6b3ddd01f60aba732edc170766
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570588"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Virtuálisgép-méretezési csoport kezelése Azure PowerShell

A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Ez a cikk a leggyakoribb Azure PowerShell-parancsmagokat ismerteti, amelyek lehetővé teszik ezen feladatok elvégzését.

Ha létre kell hoznia egy virtuálisgép-méretezési készletet, [Azure PowerShell használatával hozhat létre méretezési készletet](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Méretezési csoportra vonatkozó információk megtekintése
A méretezési csoportokra vonatkozó általános információk megtekintéséhez használja a [Get-AzVmss](/powershell/module/az.compute/get-azvmss). A következő példa a *myResourceGroup* erőforráscsoport *myScaleSet* nevű méretezési csoportjának adatait olvassa be. Adja meg a saját nevét a következőképpen:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
A méretezési csoportokban lévő virtuálisgép-példányok listájának megtekintéséhez használja a [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Az alábbi példa felsorolja a *myScaleSet* nevű méretezési csoport összes virtuálisgép-példányát, valamint a *myResourceGroup* erőforráscsoportot. Adja meg a saját értékeit a következő nevekhez:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Egy adott virtuálisgép-példányra vonatkozó további információk megtekintéséhez adja hozzá a `-InstanceId` [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) paramétert, és adja meg a megtekinteni kívánt példányt. Az alábbi példa a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban található virtuálisgép *-példányok* adatait tekinti meg. Adja meg a saját nevét a következőképpen:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Egy API-hívásban részletes *instanceView* információkat is megtudhat az összes példányról, ami segíthet elkerülni a nagyméretű TELEPÍTÉSEk API-szabályozását.

```powershell
Get-AzVmssVM -InstanceView -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
Az előző parancsok a méretezési csoportra és a virtuálisgép-példányokra vonatkozó információkat mutatták. A méretezési csoport példányainak számának növeléséhez vagy csökkentéséhez módosíthatja a kapacitást. A méretezési csoport automatikusan létrehozza vagy eltávolítja a szükséges virtuális gépek számát, majd konfigurálja a virtuális gépeket az alkalmazás forgalmának fogadására.

Először hozzon létre egy méretezési csoport objektumot a [Get-AzVmss](/powershell/module/az.compute/get-azvmss)paranccsal, majd adja meg a új értékét `sku.capacity` . A kapacitás módosításának alkalmazásához használja az [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Az alábbi példa frissíti a *myResourceGroup* -erőforráscsoport *myScaleSet* az *5* példány kapacitását. Adja meg a saját értékeit az alábbiak szerint:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. Ha csökkenti a méretezési csoport kapacitását, a rendszer először eltávolítja a legmagasabb példány-azonosítóval rendelkező virtuális gépeket.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Virtuális gépek leállítása és elindítása egy méretezési csoportban
A méretezési csoportokban lévő egy vagy több virtuális gép leállításához használja a [stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Az `-InstanceId` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. Több virtuális gép leállításához az egyes példányok AZONOSÍTÓit vesszővel kell elválasztani.

A következő példa leállítja a *0* . példányt a *myScaleSet* nevű méretezési csoportba és a *myResourceGroup* -erőforráscsoporthoz. Adja meg a saját értékeit az alábbiak szerint:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

A leállított virtuális gépek alapértelmezés szerint felszabadított állapotban vannak, és nem kell értük díjat fizetni. Ha azt szeretné, hogy a virtuális gép a leállítás után is üzembe helyezett állapotban maradjon, adja hozzá a `-StayProvisioned` paramétert az előző parancshoz. Az üzembe helyezett állapotú, leállított virtuális gépek esetében normál számítási díjak merülnek fel.


### <a name="start-vms-in-a-scale-set"></a>Virtuális gépek indítása méretezési csoportokban
Egy vagy több virtuális gép egy méretezési csoportba való elindításához használja a [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Az `-InstanceId` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. Több virtuális gép indításához az egyes példányok AZONOSÍTÓit vesszővel kell elválasztani.

A következő példa elindítja a *0* . példányt a *myScaleSet* nevű méretezési csoportba és a *myResourceGroup* -erőforráscsoporthoz. Adja meg a saját értékeit az alábbiak szerint:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Virtuális gépek újraindítása egy méretezési csoportban
Egy vagy több virtuális gép újraindításához a méretezési csoportokban használja az [restart-AzVmss](/powershell/module/az.compute/restart-azvmss). Az `-InstanceId` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. Több virtuális gép újraindításához vesszővel válassza el az egyes példányok AZONOSÍTÓit.

A következő példa újraindítja a *0* . példányt a *MyScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg a saját értékeit az alábbiak szerint:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Virtuális gépek eltávolítása egy méretezési csoportból
Egy vagy több virtuális gép egy méretezési csoportból való eltávolításához használja a [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss). A `-InstanceId` paraméter segítségével megadhat egy vagy több eltávolítandó virtuális gépet. Ha nem ad meg példány-azonosítót, a rendszer a méretezési csoport összes virtuális gépe törlődik. Több virtuális gép eltávolításához az egyes példányok AZONOSÍTÓit vesszővel kell elválasztani.

A következő példa eltávolítja a *0* . példányt a *myScaleSet* nevű méretezési csoportba és a *myResourceGroup* -erőforráscsoporthoz. Adja meg a saját értékeit az alábbiak szerint:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Következő lépések
A méretezési csoportokkal kapcsolatos egyéb gyakori feladatok közé tartozik az [alkalmazások központi telepítése](virtual-machine-scale-sets-deploy-app.md)és a virtuálisgép- [példányok frissítése](virtual-machine-scale-sets-upgrade-scale-set.md). Az [automatikus méretezési szabályok konfigurálásához](virtual-machine-scale-sets-autoscale-overview.md)Azure PowerShell is használhatja.
