---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 82318a94a6a095016fe1177ee486f035d101589c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776789"
---
A titkosítást letilthatja az Azure PowerShell, az Azure CLI vagy egy Resource Manager használatával. A windowsos virtuális gépek adatlemezeinek titkosítása nem a várt módon működik, ha az operációs rendszer és az adatlemezek egyaránt titkosítottak. Ehelyett tiltsa le a titkosítást az összes lemezen.

- **Tiltsa le a lemeztitkosítást Azure PowerShell:** A titkosítás letiltásához használja a [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Titkosítás letiltása az Azure CLI-val:** A titkosítás letiltásához használja az [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Tiltsa le a titkosítást Resource Manager sablonnal:** 

    1. Kattintson **a Deploy to Azure (Üzembe** helyezés az Azure-ban) elemre a [Disable disk encryption on running Windows VM (Lemeztitkosítás letiltása Windows rendszerű virtuális gépen) sablonból.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gépet, a kötet típusát, a jogi feltételeket és a szerződést.
    3.  Kattintson **a Vásárlás gombra** a lemeztitkosítás letiltásához egy futó Windows rendszerű virtuális gépen. 
