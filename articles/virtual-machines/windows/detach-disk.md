---
title: Adatlemez leválasztása Windows rendszerű virtuális gépről – Azure
description: Adatlemez leválasztása az Azure-beli virtuális gépről a Resource Manager-alapú üzemi modell használatával.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: 361ed04a6448bec18fac94ad90a33fe01a49e595
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974158"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Adatlemez leválasztása Windows rendszerű virtuális gépről

Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ezzel eltávolítja a lemezt a virtuális gépről, de nem távolítja el a tárolóból.

> [!WARNING]
> Ha leválaszt egy lemezt, nem törlődik automatikusan. Ha prémium szintű tárterületre iratkozott fel, akkor továbbra is a lemez tárolási díjait számítjuk fel. További információkért tekintse meg a [díjszabást és a számlázást Premium Storage használatakor](../disks-types.md#billing).

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.

 

## <a name="detach-a-data-disk-using-powershell"></a>Adatlemez leválasztása a PowerShell használatával

Az adatlemezeket a PowerShell *használatával is* elvégezheti, de ügyeljen arra, hogy a virtuális gép leválasztása előtt ne használja aktívan a lemezt.

Ebben a példában eltávolítjuk a **myDisk** nevű lemezt a **myResourceGroup** erőforráscsoport virtuálisgép- **myVM** . Először távolítsa el a lemezt a [Remove-AzVMDataDisk](/powershell/module/az.compute/remove-azvmdatadisk) parancsmag használatával. Ezután frissítse a virtuális gép állapotát az [Update-AzVM](/powershell/module/az.compute/update-azvm) parancsmag használatával az adatlemez eltávolítási folyamatának befejezéséhez.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

A lemez a tárolóban marad, de már nincs csatlakoztatva a virtuális géphez.

## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal

Elvégezheti az adatlemezek *eltávolítását* , de a virtuális gépről való leválasztása előtt győződjön meg arról, hogy a lemez nem használja aktívan a lemezt.

1. A bal oldali menüben válassza a **Virtual Machines**lehetőséget.
1. Válassza ki azt a virtuális gépet, amelyen a leválasztani kívánt adatlemez található.
1. A **Beállítások** alatt válassza a **Lemezek** lehetőséget.
1. A **lemezek** ablaktábla tetején válassza a **Szerkesztés**lehetőséget.
1. A **lemezek** ablaktáblán a leválasztani kívánt adatlemez jobb szélén kattintson a **Leválasztás**lehetőségre.
1. A módosítások mentéséhez kattintson a lap tetején található **Mentés** gombra.

A lemez a tárolóban marad, de már nincs csatlakoztatva a virtuális géphez.

## <a name="next-steps"></a>Következő lépések

Ha újra fel szeretné használni az adatlemezt, egyszerűen [csatolhatja azt egy másik virtuális géphez](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .