---
title: Azure PowerShell-példaszkript – Virtuális gép létrehozása felügyelt lemez csatlakoztatásával operációsrendszer-lemezként
description: Azure PowerShell-példaszkript – Virtuális gép létrehozása felügyelt lemez csatlakoztatásával operációsrendszer-lemezként
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 9c0d30f384ffa2747cd34dcec754938910312bf0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040167"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Virtuális gép létrehozása meglévő felügyelt operációsrendszer-lemezből a PowerShell használatával

Ez a szkript úgy hoz létre egy virtuális gépet, hogy operációsrendszer-lemezként csatlakoztat egy már létező felügyelt lemezt. Ezt a szkriptet az alábbi műveletekhez lehet alkalmazni:
* Virtuális gép létrehozása egy olyan meglévő felügyelt operációsrendszer-lemezről, amely egy másik előfizetésben található felügyelt lemezről lett átmásolva
* Virtuális gép létrehozása egy olyan meglévő felügyelt lemezről, amely egy speciális VHD-fájlból lett létrehozva 
* Virtuális gép létrehozása egy olyan meglévő felügyelt operációsrendszer-lemezről, amely egy pillanatképből lett létrehozva 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja a felügyelt lemez tulajdonságainak lekéréséhez, egy felügyelt lemez új virtuális géphez csatlakoztatásához, illetve egy virtuális gép létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/Get-AzDisk) | A lemez neve és erőforráscsoportja alapján lemezobjektumot szerez be. A lemezt a kapott lemezobjektum „Id” tulajdonságának használatával csatlakoztatja egy új virtuális géphez |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) | A felügyelt lemezt az „Id” tulajdonságának használatával operációsrendszer-lemezként csatlakoztatja egy új virtuális géphez |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | Hálózati adaptert hoz létre. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Virtuális gépet hoz létre. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

A Piactéri rendszerképekhez a [set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) használatával állíthatja be a csomag információit.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
