---
title: Felügyelt lemezek tárterületének konvertálása különböző típusú lemezekre az Azure CLI használatával
description: Az Azure Managed Disks konvertálása a különböző lemezek típusai között az Azure CLI használatával.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: ec6d5b10b4c2336ba7fd221b43a7dbf43ed5cee1
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516821"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure Managed Disks Storage konvertálása standard csomagról prémium vagy prémium szintre

Az Azure Managed Disks négyféle típusú lemezből áll: az Azure Ultra Disks, a Premium SSD, a standard SSD és a standard HDD. A teljesítmény igényei alapján válthat a prémium szintű SSD, a standard SSD és a standard HDD között. Még nem tud váltani egy ultra-lemezre, és egy újat kell telepítenie.

A nem felügyelt lemezek esetében ez a funkció nem támogatott. A nem [felügyelt lemezeket azonban könnyedén átalakíthatja egy felügyelt lemezre](convert-unmanaged-to-managed-disks.md) , hogy képes legyen váltani a lemezek típusai között.

Ez a cikk bemutatja, hogyan alakíthatja át a felügyelt lemezeket az egyik lemezről a másikra az Azure CLI használatával. Az eszköz telepítéséhez vagy frissítéséhez tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

## <a name="before-you-begin"></a>Előkészületek

* A lemez átalakításához újra kell indítani a virtuális gépet (VM), ezért a lemezes tárolás áttelepítését egy már létező karbantartási időszakban kell ütemezni.
* A nem felügyelt lemezek esetében először [alakítsa át a felügyelt lemezeket](convert-unmanaged-to-managed-disks.md) , hogy át lehessen váltani a tárolási lehetőségek között.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Egy virtuális gép összes felügyelt lemezének átváltása egyik fiókról a másikra

Ebből a példából megtudhatja, hogyan alakíthatja át a virtuális gépek lemezeit a Premium Storage szolgáltatásba. Az ebben a példában szereplő SKU változó módosításával azonban a virtuális gép lemezeit szabványos SSD-re vagy standard HDD-re konvertálhatja. Vegye figyelembe, hogy a prémium szintű felügyelt lemezek használatához a virtuális gépnek olyan virtuálisgép- [méretet](../sizes.md) kell használnia, amely támogatja a Premium Storage-ot. Ez a példa a Premium Storage-t támogató méretre is vált.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>Egyéni felügyelt lemezek váltása egyik lemezről a másikra

A fejlesztési és tesztelési feladatok esetében érdemes lehet a standard és a prémium szintű lemezek kombinációját használni a költségek csökkentése érdekében. Dönthet úgy is, hogy csak a jobb teljesítményt igénylő lemezeket frissíti. Ebből a példából megtudhatja, hogyan alakíthat át egyetlen VM-lemezt a standard és a Premium Storage között. Az ebben a példában szereplő SKU változó módosításával azonban a virtuális gép lemezeit szabványos SSD-re vagy standard HDD-re konvertálhatja. A prémium szintű felügyelt lemezek használatához a virtuális gépnek olyan virtuálisgép- [méretet](../sizes.md) kell használnia, amely támogatja a Premium Storage-ot. Ez a példa a Premium Storage-t támogató méretre is vált.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Felügyelt lemezek átváltása egyik lemezről a másikra

Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a virtuális gépet a **virtuális gépek** listájából.
3. Ha a virtuális gép nem áll le, válassza a **Leállítás** elemet a virtuális gép **Áttekintés** paneljének tetején, és várjon, amíg a virtuális gép leáll.
4. A virtuális gép ablaktábláján válassza a menü **lemezek** elemét.
5. Válassza ki az átalakítani kívánt lemezt.
6. Válassza a **konfiguráció** lehetőséget a menüből.
7. Módosítsa a **fiók típusát** az eredeti lemez típusától a kívánt lemez típusra.
8. Válassza a **Mentés** lehetőséget, majd a lemez ablaktábla bezárásához.

A lemez típusának frissítése azonnali. Az átalakítás után újraindíthatja a virtuális gépet.

## <a name="next-steps"></a>Következő lépések

[Pillanatképek](snapshot-copy-managed-disk.md)használatával készítse el a virtuális gép írásvédett példányát.