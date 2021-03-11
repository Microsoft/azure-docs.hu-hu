---
title: Virtuális gép létrehozása felügyelt rendszerképből az Azure-ban
description: Hozzon létre egy Windows rendszerű virtuális gépet egy általánosított felügyelt rendszerképből Azure PowerShell vagy a portál használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: bc87b0ec9cf41b2ddc5746a9734463f544b0cc2a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550688"
---
# <a name="create-a-vm-from-a-managed-image"></a>Virtuális gép létrehozása felügyelt rendszerképből

A Azure Portal vagy a PowerShell használatával több virtuális gépet (VM) is létrehozhat egy Azure által felügyelt virtuálisgép-rendszerképből. A felügyelt virtuálisgép-rendszerkép tartalmazza a virtuális gép létrehozásához szükséges információkat, beleértve az operációs rendszert és az adatlemezeket. A lemezképet alkotó virtuális merevlemezeket (VHD-ket), beleértve az operációsrendszer-lemezeket és az adatlemezeket is, felügyelt lemezként tárolja a rendszer. 

Új virtuális gép létrehozása előtt [létre kell hoznia egy felügyelt virtuálisgép-rendszerképet](capture-image-resource.md) , amelyet a forrás képként kíván használni, és olvasási hozzáférést kell adnia a rendszerképhez minden olyan felhasználónak, akinek hozzáféréssel kell rendelkeznie a képhez. 

Egy felügyelt rendszerkép akár 20 egyidejű telepítést is támogat. Ha több mint 20 virtuális gépet kíván létrehozni párhuzamosan, ugyanabból a felügyelt rendszerképből, az egyes virtuális merevlemezek tárolási teljesítményének korlátai miatt kiépítési időtúllépéseket eredményezhet. Több mint 20 virtuális gép egyidejű létrehozásához használjon egy 1 replikával konfigurált [megosztott Képgyűjteményi](../shared-image-galleries.md) képet minden 20 egyidejű virtuálisgép-telepítéshez.

## <a name="use-the-portal"></a>A portál használata

1. A felügyelt rendszerkép kereséséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **képeket**.
3. Válassza ki a listából a használni kívánt rendszerképet. Megnyílik a rendszerkép **áttekintése** oldal.
4. Válassza a **virtuális gép létrehozása** lehetőséget a menüből.
5. Adja meg a virtuális gép adatait. Az itt megadott felhasználónevet és jelszót fogja használni a rendszer a virtuális gépre való bejelentkezéshez. Ha elkészült, kattintson **az OK gombra**. Létrehozhatja az új virtuális gépet egy meglévő erőforráscsoporthoz, vagy az **új létrehozása** lehetőségre kattintva létrehozhat egy új erőforráscsoportot a virtuális gép tárolásához.
6. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. 
7. A **Beállítások** területen szükség szerint végezze el a módosításokat, majd kattintson **az OK gombra**. 
8. Az összefoglalás lapon a rendszerkép neve **privát képként** jelenik meg. A virtuális gép telepítésének elindításához kattintson **az OK gombra** .


## <a name="use-powershell"></a>A PowerShell használata

A PowerShell használatával létrehozhat egy virtuális gépet egy rendszerképből a [New-AzVm](/powershell/module/az.compute/new-azvm) parancsmag egyszerűsített paraméterének használatával. A rendszerképnek ugyanabban az erőforráscsoporthoz kell lennie, ahol létre szeretné hozni a virtuális gépet.

 

A [New-AzVm](/powershell/module/az.compute/new-azvm) egyszerűsített paraméterének csak azt kell megadnia, hogy a virtuális gép rendszerképből való létrehozásához meg kell adnia egy nevet, egy erőforráscsoportot és egy rendszerkép nevét. New-AzVm a **-Name** paraméter értékét fogja használni az összes automatikusan létrehozott erőforrás neveként. Ebben a példában részletesebb nevet adunk az egyes erőforrásokhoz, de a parancsmag automatikusan létrehozza azokat. Előre is létrehozhat erőforrásokat, például a virtuális hálózatot, és átadhatja az erőforrás nevét a parancsmagnak. New-AzVm a meglévő erőforrásokat fogja használni, ha a nevük alapján megtalálják őket.

A következő példa egy *myVMFromImage* nevű virtuális gépet hoz létre a *myResourceGroup* -erőforráscsoporthoz a *myImage* nevű rendszerképből. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>Következő lépések
[Windows rendszerű virtuális gépek létrehozása és kezelése a Azure PowerShell modullal](tutorial-manage-vm.md)