---
title: Adatlemez leválasztása Windows rendszerű virtuális gépről – Azure
description: Adatlemez leválasztása az Azure-beli virtuális gépről a Resource Manager-alapú üzemi modell használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 24c95d486ce77028a2c49917d8f98de23a3a8315
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552133"
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

1. A bal oldali menüben válassza a **Virtual Machines** lehetőséget.
1. Válassza ki azt a virtuális gépet, amelyen a leválasztani kívánt adatlemez található.
1. A **Beállítások** alatt válassza a **Lemezek** lehetőséget.
1. A **lemezek** ablaktáblán a leválasztani kívánt adatlemez jobb széléhez válassza az **X** gombot a leválasztáshoz.
1. A módosítások mentéséhez kattintson a lap tetején található **Mentés** gombra.

A lemez a tárolóban marad, de már nincs csatlakoztatva a virtuális géphez. A lemez nincs törölve.

## <a name="next-steps"></a>Következő lépések

Ha újra fel szeretné használni az adatlemezt, egyszerűen [csatolhatja azt egy másik virtuális géphez](attach-managed-disk-portal.md).

Ha törölni szeretné a lemezt, hogy a továbbiakban ne történjen tárolási költség, tekintse meg a nem [csatolt Azure felügyelt és nem felügyelt lemezek keresése és törlése – Azure Portal](../disks-find-unattached-portal.md).
