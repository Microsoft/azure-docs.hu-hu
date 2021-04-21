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
ms.openlocfilehash: 37571a82ca73342b8edfc4702686ccd9091887c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771503"
---
A titkosítást letilthatja az Azure PowerShell, az Azure CLI vagy egy Resource Manager használatával. 

>[!IMPORTANT]
>A linuxos virtuális gépeken Azure Disk Encryption titkosítás letiltása csak adatkötetek esetén támogatott. Az adat- vagy operációsrendszer-kötetek nem támogatják, ha az operációs rendszer kötete titkosított.  

- **Lemeztitkosítás letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Titkosítás letiltása az Azure CLI-val:** A titkosítás letiltásához használja [az az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Tiltsa le a titkosítást Resource Manager sablonnal:** A [titkosítás letiltásához használja a Titkosítás letiltása futó Linux rendszerű](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) virtuális gépen sablont.
     1. Kattintson az **Üzembe helyezés az Azure-ban** lehetőségre.
     2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gépet, a jogi feltételeket és a szerződést.
