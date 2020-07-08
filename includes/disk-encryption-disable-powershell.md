---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e3cdfccc36549d2aa127636b15b698a4e19ccf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610564"
---
A titkosítást a Azure PowerShell, az Azure CLI vagy egy Resource Manager-sablonnal is letilthatja. A windowsos virtuális gépek adatlemezeinek titkosítása nem a várt módon működik, ha az operációs rendszer és az adatlemezek egyaránt titkosítottak. A titkosítás letiltása az összes lemezen.

- **Lemez titkosításának letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Titkosítás letiltása az Azure CLI-vel:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **A titkosítás letiltása Resource Manager-sablonnal:** 

    1. Kattintson a **telepítés az Azure** -ba lehetőségre a [lemez titkosításának letiltása Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) virtuálisgép-sablon futtatásához.
    2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gépet, a kötet típusát, a jogi feltételeket és a szerződést.
    3.  Kattintson a **vásárlás** gombra a lemez titkosításának letiltásához egy futó WINDOWSOS virtuális gépen. 
