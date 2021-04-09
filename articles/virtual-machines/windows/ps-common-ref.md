---
title: Az Azure Virtual Machines általános PowerShell-parancsai
description: Gyakori PowerShell-parancsok az Azure-beli virtuális gépek létrehozásának és kezelésének megkezdéséhez.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: ecc3f5934e4dd37cc27a1715ea39f86619d581ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730206"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Gyakran használt PowerShell-parancsok az Azure-beli virtuális gépek létrehozásához és kezeléséhez

Ez a cikk az Azure-előfizetésben található virtuális gépek létrehozásához és kezeléséhez használható Azure PowerShell-parancsokat ismerteti.  Az adott parancssori kapcsolókkal és lehetőségekkel kapcsolatos részletesebb segítségért használhatja a **Get-Help** *parancsot*.

 

Ezek a változók akkor lehetnek hasznosak, ha a cikkben szereplő parancsok közül többet futtat:

- $location – a virtuális gép helye. A [Get-AzLocation](/powershell/module/az.resources/get-azlocation) használatával megkeresheti az Ön számára megfelelő [földrajzi régiót](https://azure.microsoft.com/regions/) .
- $myResourceGroup – a virtuális gépet tartalmazó erőforráscsoport neve.
- $myVM – a virtuális gép neve.

## <a name="create-a-vm---simplified"></a>Virtuális gép létrehozása – egyszerűsített

| Feladat | Parancs |
| ---- | ------- |
| Egyszerű virtuális gép létrehozása | [New-AzVM](/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> New-AzVM *egyszerűsített* paraméterekkel rendelkezik, ahol minden szükséges egyetlen név. A-name értéket fogja használni az új virtuális gép létrehozásához szükséges összes erőforrás neveként. Több is megadható, de ez minden szükséges.|
| Virtuális gép létrehozása egyéni rendszerképből | New-AzVm-ResourceGroupName $myResourceGroup-Name $myVM ImageName "myImage" – hely $location  <BR></BR><BR></BR>Már létre kell hoznia egy saját [felügyelt képet](capture-image-resource.md). A rendszerkép használatával több, azonos virtuális gép is létrehozható. |



## <a name="create-a-vm-configuration"></a>Virtuális gép konfigurációjának létrehozása

| Feladat | Parancs |
| ---- | ------- |
| Virtuális gép konfigurációjának létrehozása |$vm = [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) -VMName $MyVM-VMSize "Standard_D1_v1"<BR></BR><BR></BR>A virtuálisgép-konfiguráció a virtuális gép beállításainak definiálására vagy frissítésére szolgál. A konfiguráció inicializálva van a virtuális gép nevével és [méretével](../sizes.md). |
| Konfigurációs beállítások hozzáadása |$vm = [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) -VM $VM-Windows-számítógépnév $MyVM-hitelesítőadat $cred-ProvisionVMAgent-EnableAutoUpdate<BR></BR><BR></BR>Az operációs rendszer beállításai, beleértve a [hitelesítő adatokat](/powershell/module/microsoft.powershell.security/get-credential) , hozzá lettek adva a New-AzVMConfig használatával korábban létrehozott konfigurációs objektumhoz. |
| Hálózati adapter hozzáadása |$vm = [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) -VM $VM-ID $nic. ID<BR></BR><BR></BR>A virtuális gépnek [hálózati adapterrel](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json) kell rendelkeznie a virtuális hálózatban való kommunikációhoz. A [Get-AzNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) használatával lekérheti a meglévő hálózati adapterek objektumait is. |
| Platform rendszerképének meghatározása |$vm = [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) -VM $VM-közzétevő neve "publisher_name"-ajánlat "publisher_offer"-sku "product_sku"-version "Latest"<BR></BR><BR></BR>A rendszer a korábban a New-AzVMConfig használatával létrehozott konfigurációs objektumhoz adja hozzá a [rendszerképekkel kapcsolatos adatokat](cli-ps-findimage.md) . A parancs által visszaadott objektum csak akkor használatos, ha az operációsrendszer-lemezt platform-lemezkép használatára állítja be. |
| Virtuális gép létrehozása |[New-AzVM](/powershell/module/az.compute/new-azvm) -ResourceGroupName $MyResourceGroup-Location $Location-VM $VM<BR></BR><BR></BR>Az összes erőforrás egy [erőforráscsoporthoz](../../azure-resource-manager/management/manage-resource-groups-powershell.md)lett létrehozva. A parancs futtatása előtt futtassa a New-AzVMConfig, a set-AzVMOperatingSystem, a set-AzVMSourceImage, a Add-AzVMNetworkInterface és a set-AzVMOSDisk parancsot. |
| Virtuális gép frissítése |[Update-AzVM](/powershell/module/az.compute/update-azvm) -ResourceGroupName $MYRESOURCEGROUP – VM $VM<BR></BR><BR></BR>Szerezze be a virtuális gép aktuális konfigurációját a Get-AzVM használatával, módosítsa a virtuálisgép-objektum konfigurációs beállításait, majd futtassa ezt a parancsot. |

## <a name="get-information-about-vms"></a>Virtuális gépek adatainak beolvasása

| Feladat | Parancs |
| ---- | ------- |
| Egy előfizetésben lévő virtuális gépek listázása |[Get-AzVM](/powershell/module/az.compute/get-azvm) |
| Erőforráscsoporthoz tartozó virtuális gépek listázása |Get-AzVM – ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Az előfizetéshez tartozó erőforráscsoportok listájának lekéréséhez használja a [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup). |
| Virtuális gép adatainak lekérése |Get-AzVM-ResourceGroupName $myResourceGroup-Name $myVM |

## <a name="manage-vms"></a>Virtuális gépek kezelése
| Feladat | Parancs |
| --- | --- |
| Virtuális gép elindítása |[Start-AzVM](/powershell/module/az.compute/start-azvm) -ResourceGroupName $MyResourceGroup – név $myVM |
| Virtuális gép leállítása |[Stop-AzVM](/powershell/module/az.compute/stop-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Futó virtuális gép újraindítása |[Restart-AzVM](/powershell/module/az.compute/restart-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Virtuális gép törlése |[Remove-AzVM](/powershell/module/az.compute/remove-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |


## <a name="next-steps"></a>Következő lépések
* A virtuális gépek létrehozásának alapvető lépései a [Windows virtuális gép létrehozása a Resource Manager és a PowerShell használatával](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json)című témakörben találhatók.
