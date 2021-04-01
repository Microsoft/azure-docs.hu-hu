---
title: Azure CLI – nem csatolt felügyelt és nem felügyelt lemezek keresése és törlése
description: Az Azure által felügyelt és nem felügyelt (VHD/oldal Blobok) lemezek az Azure CLI használatával történő megkeresése és törlése.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0c3e8bb2ff6f3313e851a4253a95a5ad923a8f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016216"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Az Azure által felügyelt és nem felügyelt lemezek megkeresése és törlése az Azure CLI használatával
Amikor töröl egy virtuális gépet (VM) az Azure-ban, alapértelmezés szerint a virtuális géphez csatolt összes lemez nem törlődik. Ez a funkció segít megakadályozni az adatvesztést a virtuális gépek véletlen törlése miatt. A virtuális gép törlése után továbbra is fizetnie kell a nem csatlakoztatott lemezekre. Ez a cikk bemutatja, hogyan keresheti meg és törölheti a nem csatolt lemezeket, és csökkentheti a szükségtelen költségeket. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Felügyelt lemezek: nem csatolt lemezek keresése és törlése 

A következő parancsfájl a nem csatlakoztatott [felügyelt lemezeket](../managed-disks-overview.md) keresi a **többé** tulajdonság értékének vizsgálatával. Ha egy felügyelt lemez egy virtuális géphez van csatlakoztatva, a **többé** tulajdonság tartalmazza a virtuális gép erőforrás-azonosítóját. Ha egy felügyelt lemez nincs csatlakoztatva, a **többé** tulajdonság null értékű. A parancsfájl megvizsgálja az Azure-előfizetések összes felügyelt lemezét. Ha a parancsfájl egy olyan felügyelt lemezt keres, amelynek **többé** tulajdonsága NULL értékűre van állítva, akkor a parancsfájl megállapítja, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a szkriptet úgy, hogy a **deleteUnattachedDisks** változót 0-ra állítja. Ezzel a művelettel megkeresheti és megtekintheti az összes nem csatlakoztatott felügyelt lemezt.
>
>Miután átvizsgálta az összes nem csatlakoztatott lemezt, futtassa újra a szkriptet, és állítsa az **deleteUnattachedDisks** változót 1-re. Ez a művelet lehetővé teszi az összes nem csatlakoztatott felügyelt lemez törlését.
>

```azurecli
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0
unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nem felügyelt lemezek: nem csatolt lemezek keresése és törlése 

A nem felügyelt lemezek az [Azure Storage-fiókokban](../../storage/common/storage-account-overview.md) [blobként](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) tárolt VHD-fájlok. A következő parancsfájl a nem csatolt nem felügyelt lemezeket (blobokat) keresi a **LeaseStatus** tulajdonság értékének vizsgálatával. Ha egy nem felügyelt lemez egy virtuális géphez van csatlakoztatva, a **LeaseStatus** tulajdonság **zárolt** értékre van állítva. Ha egy nem felügyelt lemez nincs csatlakoztatva, a **LeaseStatus** tulajdonság **zárolása zárolva** értékre van állítva. A parancsfájl megvizsgálja az Azure-előfizetések összes Azure Storage-fiókjában lévő összes nem felügyelt lemezt. Ha a parancsfájl egy nem felügyelt lemezt keres, amelynek **LeaseStatus** tulajdonsága **zárolva** értékre van állítva, akkor a parancsfájl megállapítja, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a szkriptet úgy, hogy a **deleteUnattachedVHDs** változót 0-ra állítja. Ezzel a művelettel megkeresheti és megtekintheti a nem csatolt nem felügyelt virtuális merevlemezeket.
>
>Miután átvizsgálta az összes nem csatlakoztatott lemezt, futtassa újra a szkriptet, és állítsa az **deleteUnattachedVHDs** változót 1-re. Ezzel a művelettel törölheti az összes nem csatolt nem felügyelt virtuális merevlemezt.
>

```azurecli
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0
storageAccountIds=$(az storage account list --query [].[id] -o tsv)
for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Következő lépések

További információ: Storage- [fiók törlése](../../storage/common/storage-account-create.md#delete-a-storage-account).