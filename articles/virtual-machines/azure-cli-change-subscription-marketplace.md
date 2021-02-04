---
title: Azure CLI-minta a piactéren futó Azure-beli virtuális gépek másik előfizetésre való áthelyezéséhez
description: Azure CLI-minta Azure Marketplace-beli virtuális gépek egy másik előfizetésre való áthelyezéséhez.
author: cynthn
ms.author: cynthn
manager: ''
ms.date: 01/29/2021
ms.topic: sample
ms.service: virtual-machines
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: bba81d8fbd24cf81a558b8c953c7a2e5e9290e9f
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539772"
---
# <a name="move-a-marketplace-azure-virtual-machine-to-another-subscription"></a>Piactér Azure-beli virtuális gép áthelyezése másik előfizetésre

Ha egy Piactéri virtuális gépet másik előfizetésre szeretne áthelyezni, át kell helyeznie az operációsrendszer-lemezt az előfizetésbe, majd újra létre kell hoznia a virtuális gépet.

Az adatlemez új előfizetésbe való áthelyezéséhez nincs szükség erre az eljárásra. Ehelyett hozzon létre egy új virtuális gépet az új előfizetésben a piactéren, majd helyezze át és csatolja az adatlemezt.

Ez a szkript három műveletet mutat be:

- Egy operációsrendszer-lemez pillanatképének létrehozása.
- Helyezze át a pillanatképet egy másik előfizetésbe.
- A pillanatkép alapján hozzon létre egy virtuális gépet.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-script"></a>Példaszkript

Ha egy Piactéri virtuális gépet egy másik előfizetésre szeretne áthelyezni, létre kell hoznia egy új virtuális gépet ugyanahhoz a Piactéri ajánlathoz az áthelyezett operációsrendszer-lemezről.

> [!NOTE]
> Ha a virtuális gépi csomag már nem érhető el a piactéren, nem használhatja ezt az eljárást.

```azurecli
#!/bin/bash
# Set variable values before proceeding. 

# Variables
sourceResourceGroup= Resource group for the current virtual machine
sourceSubscription= Subscription for the current virtual machine
vmName= Name of the current virtual machine

destinationResourceGroup= Resource group for the new virtual machine, create if necessary
destinationSubscription= Subscription for the new virtual machine

# Set your current subscription for the source virtual machine
az account set --subscription $sourceSubscription

# Load variables about your virtual machine
# osType = windows or linux
osType=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --subscription $sourceSubscription \
    --query 'storageProfile.osDisk.osType' --output tsv)

# offer = Your offer in Marketplace
offer=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'storageProfile.imageReference.offer' --output tsv)

# plan = Your plan in Marketplace
plan=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'plan' --output tsv)

# publisher = Your publisher in Marketplace
publisher=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'storageProfile.imageReference.publisher' --output tsv)

# Get information to create new virtual machine
planName=$(az vm get-instance-view --resource-group $sourceResourceGroup --subscription $sourceSubscription --query 'plan.name' --name $vmName)
planProduct=$(az vm get-instance-view --resource-group $sourceResourceGroup --subscription $sourceSubscription --query 'plan.product' --name $vmName)
planPublisher=$(az vm get-instance-view --resource-group $sourceResourceGroup --subscription $sourceSubscription --query 'plan.publisher' --name $vmName)

# Get the name of the OS disk
osDiskName=$(az vm show --resource-group $sourceResourceGroup --name $vmName \
    --query 'storageProfile.osDisk.name' --output tsv)

# Verify the terms for your market virtual machine
az vm image terms show --offer $offer --plan '$plan' --publisher $publisher \
    --subscription $sourceSubscription

# Deallocate the virtual machine
az vm deallocate --resource-group $sourceResourceGroup --name $vmName

# Create a snapshot of the OS disk
az snapshot create --resource-group $sourceResourceGroup --name MigrationSnapshot \
    --source "/subscriptions/$sourceSubscription/resourceGroups/$sourceResourceGroup/providers/Microsoft.Compute/disks/$osDiskName"

# Move the snapshot to your destination resource group
az resource move --destination-group $destinationResourceGroup \
    --destination-subscription-id $destinationSubscription \
    --ids "/subscriptions/$sourceSubscription/resourceGroups/$sourceResourceGroup/providers/Microsoft.Compute/snapshots/MigrationSnapshot"

# Set your subscription to the destination value
az account set --subscription $destinationSubscription

# Accept the terms from the Marketplace
az vm image terms accept --offer $offer --plan '$plan' --publisher $publisher \
    --subscription $destinationSubscription

# Create disk from the snapshot 
az disk create --resource-group $destinationResourceGroup --name DestinationDisk \
    --source "/subscriptions/$destinationSubscription/resourceGroups/$destinationResourceGroup/providers/Microsoft.Compute/snapshots/MigrationSnapshot" \
    --os-type $osType

# Create virtual machine from disk
az vm create --resource-group $destinationResourceGroup --name $vmName \
    --plan-name $planName --plan-product $planProduct  --plan-publisher $planPublisher \
    --attach-os-disk "/subscriptions/$destinationSubscription/resourceGroups/$destinationResourceGroup/providers/Microsoft.Compute/disks/DestinationDisk" \
    --os-type $osType
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A minta futtatása után az alábbi parancsokkal távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást:

```azurecli
az group delete --name $sourceResourceGroup --subscription $sourceSubscription
az group delete --name $destinationResourceGroup --subscription $destinationSubscription
```

## <a name="azure-cli-references-used-in-this-article"></a>Az ebben a cikkben használt Azure CLI-referenciák

- [az Account set](/cli/azure/account#az_account_set)
- [az disk create](/cli/azure/disk#az_disk_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az erőforrás áthelyezése](/cli/azure/resource#az_resource_move)
- [az snapshot create](/cli/azure/snapshot#az_snapshot_create)
- [az vm create](/cli/azure/vm#az_vm_create)
- [az VM felszabadítása](/cli/azure/vm#az_vm_deallocate)
- [az vm delete](/cli/azure/vm#az_vm_delete)
- [az VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view)
- [az VM-rendszerkép feltételeinek elfogadása](/cli/azure/vm/image/terms#az_vm_image_terms_accept)
- [az VM rendszerkép terms show](/cli/azure/vm/image/terms#az_vm_image_terms_show)
- [az vm show](/cli/azure/vm#az_vm_show)
