---
title: Végpontok közötti titkosítás engedélyezése a gazdagép-Azure CLI által felügyelt lemezek titkosításával
description: Használjon titkosítást a gazdagépen az Azure Managed Disks szolgáltatásban a végpontok közötti titkosítás engedélyezéséhez.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3eecb584f468bc170f0325da8d734a1890691483
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601771"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>A végpontok közötti titkosítás engedélyezése az Azure CLI használatával a gazdagépen való titkosítással

Amikor engedélyezi a titkosítást a gazdagépen, a virtuálisgép-gazdagépen tárolt adatok titkosítva maradnak a tárolási szolgáltatásba titkosított adatforgalomban. A gazdagépen található titkosítással, valamint az egyéb felügyelt lemezes titkosítási típusokkal kapcsolatos elméleti információkat lásd: [titkosítás a gazdagép-végpontok közötti titkosítással a virtuális gép adataihoz](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

A virtuális gépek méreteit programozott módon is megtalálhatja. Ha szeretné megtudni, hogyan kérheti le őket programozott módon, tekintse meg a [támogatott VM-méretek keresése](#finding-supported-vm-sizes) szakaszt.

## <a name="prerequisites"></a>Előfeltételek

A virtuális gép/VMSS EncryptionAtHost tulajdonságának használata előtt engedélyeznie kell az előfizetés szolgáltatását. Az előfizetés funkciójának engedélyezéséhez kövesse az alábbi lépéseket:

1.  Futtassa a következő parancsot az előfizetés funkciójának regisztrálásához

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  Győződjön meg arról, hogy a regisztrációs állapot regisztrálva van (néhány percet vesz igénybe) az alábbi parancs használatával, mielőtt kipróbálja a funkciót.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault és DiskEncryptionSet létrehozása

Ha a szolgáltatás engedélyezve van, be kell állítania egy Azure Key Vault és egy DiskEncryptionSet, ha még nem tette meg.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Példák

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Hozzon létre egy titkosítást használó virtuális gépet az ügyfél által felügyelt kulcsokkal rendelkező gazdagépen. 

Hozzon létre egy felügyelt lemezekkel rendelkező virtuális gépet a korábban létrehozott DiskEncryptionSet erőforrás-URI-ja használatával, hogy titkosítsa az operációs rendszer és az adatlemezek gyorsítótárát az ügyfél által kezelt kulcsokkal. A rendszer a platform által felügyelt kulcsokkal titkosítja a temp lemezeket. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Platform által felügyelt kulcsokkal rendelkező virtuális gép létrehozása titkosítással a gazdagépen. 

Hozzon létre egy titkosítással rendelkező virtuális gépet, amelyen engedélyezve van az operációs rendszer/adatlemezek és az ideiglenes lemezek gyorsítótárának titkosítása a platform által felügyelt kulcsokkal. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Frissítsen egy virtuális gépet, hogy engedélyezze a titkosítást a gazdagépen. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>A virtuális gép gazdagépén lévő titkosítás állapotának megtekintése

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Hozzon létre egy virtuálisgép-méretezési csoport titkosítással a gazdagépen az ügyfél által felügyelt kulcsokkal. 

Hozzon létre egy virtuálisgép-méretezési készletet felügyelt lemezekkel a korábban létrehozott DiskEncryptionSet erőforrás-URI-ja segítségével, hogy titkosítsa az operációs rendszer és az adatlemezek gyorsítótárát az ügyfél által kezelt kulcsokkal. A rendszer a platform által felügyelt kulcsokkal titkosítja a temp lemezeket. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>A platform által felügyelt kulcsokkal rendelkező virtuálisgép-méretezési csoport létrehozása titkosítással a gazdagépen. 

Hozzon létre egy virtuálisgép-méretezési csoport titkosítással a gazdagépen, amelyen engedélyezve van a platform által felügyelt kulcsokkal rendelkező operációs rendszer/adatlemezek és ideiglenes lemezek gyorsítótárának titkosítása. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>A virtuális gépek méretezési csoportjának frissítése a titkosítás engedélyezéséhez a gazdagépen. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoportnál a gazdagépen lévő titkosítás állapotának megtekintése

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Támogatott virtuális gépek méretének keresése

A régi virtuálisgép-méretek nem támogatottak. A támogatott virtuálisgép-méretek listáját a következők valamelyikével érheti el:

Az [erőforrás-SKU API](/rest/api/compute/resourceskus/list) meghívása és annak ellenőrzése, hogy a képesség igaz értékre van-e `EncryptionAtHostSupported` állítva .

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Vagy hívja meg a [Get-AzComputeResourceSku PowerShell-](/powershell/module/az.compute/get-azcomputeresourcesku) parancsmagot.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta és konfigurálta ezeket az erőforrásokat, a segítségével biztonságossá teheti a felügyelt lemezeket. A következő hivatkozás olyan parancsfájlokat tartalmaz, amelyek mindegyike megfelelő forgatókönyvekkel rendelkezik, amelyek segítségével biztonságossá teheti a felügyelt lemezeket.

[Azure Resource Manager sablon mintái](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
