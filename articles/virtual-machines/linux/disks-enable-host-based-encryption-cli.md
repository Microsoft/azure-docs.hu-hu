---
title: Végpontok között titkosítás engedélyezése titkosítással a gazdagépen – Azure CLI – felügyelt lemezek
description: A gazdagép titkosításának használatával engedélyezheti a végpontok között titkosítást az Azure-beli felügyelt lemezeken.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 45cdb9217eebf6e3129718a96d9f7b72a3ab62b3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533604"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Az Azure CLI használata a gazdagépen titkosítást használó végpontok között titkosítás engedélyezéséhez

Ha engedélyezi a titkosítást a gazdagépen, a virtuálisgép-gazdagépen tárolt adatok titkosítva vannak, az adatok pedig titkosítva vannak a Storage szolgáltatás felé. Elméleti információk a gazdagépen való titkosításról, valamint egyéb felügyelt lemeztitkosítási típusokról: Titkosítás gazdagépen – Végpontok között titkosítás a virtuális [gép adataihoz.](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

A támogatott virtuálisgép-méretek teljes listája programozott módon lekért lista. A programozott lekérésükről a Támogatott virtuálisgép-méretek keresése című szakaszban [olvashat.](#finding-supported-vm-sizes)
A virtuális gép méretének frissítése ellenőrzéssel ellenőrzi, hogy az új virtuálisgép-méret támogatja-e az EncryptionAtHost funkciót.

## <a name="prerequisites"></a>Előfeltételek

A virtuális gép/VMSS EncryptionAtHost tulajdonságának használata előtt engedélyeznie kell a funkciót az előfizetésben. A funkció előfizetéshez való engedélyezéséhez kövesse az alábbi lépéseket:

1.  A funkció előfizetéshez való regisztrálásához hajtsa végre a következő parancsot

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  A szolgáltatás kipróbálása előtt ellenőrizze, hogy a regisztrációs állapot Regisztrálva (eltarthat néhány percig) az alábbi paranccsal.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Hozzon létre egy Azure Key Vault És DiskEncryptionSet adatokat

A funkció engedélyezése után be kell állítania egy Azure Key Vault és egy DiskEncryptionSet készletet, ha még nem tette meg.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Példák

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Virtuális gép létrehozása ügyfél által kezelt kulcsokkal engedélyezett titkosítással a gazdagépen. 

Hozzon létre egy virtuális gépet felügyelt lemezekkel a korábban az operációs rendszer és az adatlemezek gyorsítótárának ügyfél által kezelt kulcsokkal való titkosításához létrehozott DiskEncryptionSet erőforrás-URI-ját használva. Az ideiglenes lemezek platform által felügyelt kulcsokkal vannak titkosítva. 

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

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Hozzon létre egy virtuális gépet titkosítással a platform által felügyelt kulcsokkal engedélyezett gazdagépen. 

Virtuális gép létrehozása engedélyezett titkosítással a gazdagépen az operációsrendszer-/adatlemezek és az ideiglenes lemezek gyorsítótárának titkosításához platform által felügyelt kulcsokkal. 

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

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Virtuális gép frissítése a gazdagép titkosításának engedélyezéséhez. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Virtuális gép gazdagépen való titkosításának ellenőrzése

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Virtuálisgép-méretezési csoport létrehozása ügyfél által kezelt kulcsokkal engedélyezett titkosítással a gazdagépen. 

Hozzon létre egy virtuálisgép-méretezési csoport felügyelt lemezekkel a korábban az operációs rendszer és az adatlemezek gyorsítótárának ügyfél által kezelt kulcsokkal való titkosításához létrehozott DiskEncryptionSet erőforrás-URI-ját használva. Az ideiglenes lemezek platform által felügyelt kulcsokkal vannak titkosítva. 

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

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Hozzon létre egy virtuálisgép-méretezési csoport titkosítást a platform által felügyelt kulcsokkal engedélyezett gazdagépen. 

Virtuálisgép-méretezési csoport létrehozása engedélyezett titkosítással a gazdagépen az operációsrendszer-/adatlemezek és az ideiglenes lemezek gyorsítótárának titkosításához platform által felügyelt kulcsokkal. 

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

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Frissítsen egy virtuálisgép-méretezési készletet, hogy engedélyezze a titkosítást a gazdagépen. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési készlet gazdagépen való titkosításának ellenőrzése

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Támogatott virtuálisgép-méretek keresése

Az örökölt virtuálisgép-méretek nem támogatottak. A támogatott virtuálisgép-méretek listáját a következő két listában találja:

Az [erőforrás-Skus API hívása és](/rest/api/compute/resourceskus/list) annak ellenőrzése, hogy `EncryptionAtHostSupported` a képesség True (Igaz) **értékre van-e állítva.**

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

Vagy hívja meg a [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) PowerShell-parancsmagot.

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

Most, hogy létrehozta és konfigurálta ezeket az erőforrásokat, használhatja őket a felügyelt lemezek biztonságossá való használatára. Az alábbi hivatkozás példaszkprogramokat tartalmaz, amelyek mindegyikének van egy-egy forgatókönyve, és amelyek segítségével biztonságossá teheti a felügyelt lemezeket.

[Azure Resource Manager mintasablonok](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
