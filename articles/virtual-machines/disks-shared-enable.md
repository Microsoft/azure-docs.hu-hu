---
title: Megosztott lemezek engedélyezése az Azure Managed Disks szolgáltatásban
description: Egy megosztott lemezzel rendelkező Azure felügyelt lemez konfigurálása, amely több virtuális gépen is megosztható
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e8bb97196fcceea0c86f58fe4a63146e681c427e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500733"
---
# <a name="enable-shared-disk"></a>Megosztott lemez engedélyezése

Ez a cikk ismerteti, hogyan engedélyezheti a megosztott lemezek funkciót az Azure Managed Disks szolgáltatásban. Az Azure Shared Disks az Azure Managed Disks új funkciója, amely lehetővé teszi, hogy egyszerre több virtuális géphez (VM) csatolja a felügyelt lemezeket. A felügyelt lemezek több virtuális géphez való csatolásával új vagy meglévő fürtözött alkalmazásokat telepíthet át az Azure-ba. 

Ha olyan felügyelt lemezeken keres elméleti információkat, amelyeken engedélyezve vannak a megosztott lemezek, tekintse meg az Azure-beli [megosztott lemezeket](disks-shared.md).

## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A megosztott lemezek több operációs rendszert támogatnak. Tekintse meg a támogatott operációs rendszerekkel kapcsolatos fogalmi cikk [Windows](./disks-shared.md#windows) -és [Linux](./disks-shared.md#linux) -részeit.

## <a name="disk-sizes"></a>Lemezek mérete

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Megosztott lemezek telepítése

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Prémium SSD üzembe helyezése megosztott lemezként

Ha olyan felügyelt lemezt kíván üzembe helyezni, amelyen engedélyezve van a megosztott lemez szolgáltatás, használja az új tulajdonságot, `maxShares` és adjon meg egy 1-nél nagyobb értéket. Így a lemez több virtuális gépen is megosztható.

> [!IMPORTANT]
> A értéke `maxShares` csak akkor állítható be vagy módosítható, ha egy lemez le van választva az összes virtuális gépről. Tekintse meg az engedélyezett értékek [lemezének méretét](#disk-sizes) `maxShares` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager-sablon](#tab/azure-resource-manager)

A következő sablon használata előtt cserélje le a, a, a `[parameters('dataDiskName')]` `[resourceGroup().location]` és a `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` értéket a saját értékeire.

[prémium SSD megosztott lemez sablonja](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Ultra-lemez üzembe helyezése megosztott lemezként

Ha olyan felügyelt lemezt kíván üzembe helyezni, amelyen engedélyezve van a megosztott lemez szolgáltatás, módosítsa a `maxShares` paramétert 1-nél nagyobb értékre. Így a lemez több virtuális gépen is megosztható.

> [!IMPORTANT]
> A értéke `maxShares` csak akkor állítható be vagy módosítható, ha egy lemez le van választva az összes virtuális gépről. Tekintse meg az engedélyezett értékek [lemezének méretét](#disk-sizes) `maxShares` .


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Regionális lemez – példa

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Példa a zóna lemezére

Ez a példa majdnem ugyanaz, mint az előző, kivéve, ha létrehoz egy lemezt az 1. rendelkezésre állási zónában.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Regionális lemez – példa

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Példa a zóna lemezére

Ez a példa majdnem ugyanaz, mint az előző, kivéve, ha létrehoz egy lemezt az 1. rendelkezésre állási zónában.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Resource Manager-sablon](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Regionális lemez – példa

A következő sablon használata előtt cserélje le a,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` és `[parameters('diskMBpsReadOnly')]` értéket a saját értékeire.

[Regionális megosztott Ultra Disks-sablon](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Példa a zóna lemezére

A következő sablon használata előtt cserélje le a,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` és `[parameters('diskMBpsReadOnly')]` értéket a saját értékeire.

[Többszintű megosztott ultravékony lemezek sablonja](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Az Azure-beli megosztott lemezek használata a virtuális gépekkel

Miután telepített egy megosztott lemezt a `maxShares>1` használatával, csatlakoztathatja a lemezt egy vagy több virtuális géphez.

> [!NOTE]
> Ha Ultra-lemezt telepít, győződjön meg arról, hogy az megfelel a szükséges követelményeknek. További információ: az [Azure Ultra Disks használata](disks-enable-ultra-ssd.md) .

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Támogatott SCSI PR-parancsok

Miután csatlakoztatta a megosztott lemezt a fürtben lévő virtuális gépekhez, létrehozhat kvórumot, és írási/olvasási lehetőséget is használhat a lemezre az SCSI PR használatával. Az Azure megosztott lemezek használatakor az alábbi PR-parancsok érhetők el:

A lemezzel való kommunikációhoz Kezdje a következőt: állandó – foglalás-művelet lista:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE, PR_PREEMPT_RESERVATION vagy PR_RELEASE_RESERVATION használatakor adja meg a következő állandó fenntartási típus egyikét:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION vagy PR_RELEASE – foglalás használatakor állandó foglalási kulcsot kell megadnia.


## <a name="next-steps"></a>Következő lépések

Ha Azure Resource Manager-sablonokat szeretne használni a lemez üzembe helyezéséhez, a következő sablonok érhetők el:
- [Prémium SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Regionális Ultra-lemezek](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Zónákra kiterjedően Ultra lemezek](https://aka.ms/SharedUltraDiskARMtemplateZonal)