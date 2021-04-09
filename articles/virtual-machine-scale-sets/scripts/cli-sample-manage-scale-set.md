---
title: Azure CLI-minta a virtuális gépek méretezési csoportjának felügyeletéhez
description: Ez a minta bemutatja, hogyan adhat hozzá lemezeket virtuálisgép-méretezési csoportokhoz. Frissítheti a lemezeket, és hozzáadhatja a virtuális gépeket az Azure AD-hitelesítéshez.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672603"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása és kezelése

Ezeket a mintákat a virtuálisgép-méretezési csoportoknak az Azure CLI használatával történő prototípusára használhatja.

Ezek a példák a következő műveleteket mutatják be:

* Virtuálisgép-méretezési csoportot hoz létre.
* Új vagy meglévő lemezek hozzáadása és frissítése egy méretezési csoportba vagy a készlet egy példányára.
* Méretezési csoport hozzáadása Azure Active Directory (Azure AD) hitelesítéshez.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Példák a parancsokra

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Új adatlemez hozzáadása után formázza és csatlakoztassa a lemezt. Windows rendszerű virtuális gépek esetén lásd: [felügyelt adatlemez csatolása Windows rendszerű virtuális géphez a Azure Portal használatával](../../virtual-machines/windows/attach-managed-disk-portal.md). Linux rendszerű virtuális gépek esetén lásd: [lemez hozzáadása Linux rendszerű virtuális géphez](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

A kibontott lemez használatához bontsa ki a mögöttes partíciót. További információ: [lemezpartíció és fájlrendszer kibontása](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem).

Ez a példa egy adatlemezt méretezett. Ugyanezt az eljárást használhatja az operációsrendszer-lemezek frissítéséhez is. A Windows rendszerű virtuális gépekkel kapcsolatos további információkért lásd: [a virtuális gép operációsrendszer-meghajtójának kibontása](../../virtual-machines/windows/expand-os-disk.md). A Linux rendszerű virtuális gépekkel kapcsolatos további információkért lásd: [virtuális merevlemezek kibontása linuxos virtuális gépen az Azure CLI-vel](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A parancsok használata után a következő parancs futtatásával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Az ebben a cikkben használt Azure CLI-referenciák

* [az Disk delete](/cli/azure/disk#az_disk_delete)
* [az Disk List](/cli/azure/disk#az_disk_list)
* [az Disk Update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [az virtuálisgép-méretezési csoport felszabadítása](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [az vmss Disk leválasztása](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [az vmss Identity assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [az vmss List-instances](/cli/azure/vmss#az_vmss_list_instances)
* [az vmss restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)