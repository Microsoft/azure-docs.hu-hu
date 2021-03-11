---
title: Azure-lemezek leképezése Windows VM-alapú vendég lemezekre
description: A Windows rendszerű virtuális gépek vendég lemezeit leképező Azure-lemezek meghatározása.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 7a4fad066af37217eb42060d5fc5a7ef716770c6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560990"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Azure-lemezek leképezése Windows VM-alapú vendég lemezekre

Előfordulhat, hogy meg kell határoznia a virtuális gép vendég lemezeit támogató Azure-lemezeket. Bizonyos esetekben a lemez vagy a kötet méretét összehasonlíthatja a csatlakoztatott Azure-lemezek méretével. Olyan esetekben, amikor több, azonos méretű Azure-lemez van a virtuális géphez csatlakoztatva, az adatlemezek logikai egységének számát (LUN) kell használnia. 

## <a name="what-is-a-lun"></a>Mi az a LUN?

A logikai egységek száma (LUN) egy adott tárolóeszköz azonosítására szolgáló szám. Minden tárolóeszközhöz egyedi numerikus azonosító van rendelve, amely nullával kezdődik. Az eszköz teljes elérési útját a busz száma, a célként megadott azonosító száma és a logikai egység száma (LUN) jelképezi. 

Például: ***Bus Number 0, cél azonosítója 0, LUN 3***

A gyakorlatban csak a LUN-t kell használnia.

## <a name="finding-the-lun"></a>A LUN megkeresése

A logikai egység megkeresésére két módszer áll rendelkezésre, amelyek közül választhat, ha [tárolóhelyeket](/windows-server/storage/storage-spaces/overview) használ, vagy nem.

### <a name="disk-management"></a>Lemezkezelés

Ha nem használ Storage-készleteket, a [Lemezkezelés](/windows-server/storage/disk-management/overview-of-disk-management) eszközzel megkeresheti a LUN-t.

1. Kapcsolódjon a virtuális géphez, és nyissa meg a Lemezkezelés a-t. Kattintson a jobb gombbal a Start gombra, és válassza a "lemez-kezelés" a elemet. Beírhatja `diskmgmt.msc` a Keresés indítása mezőbe is
1. Az alsó ablaktáblán kattintson a jobb gombbal bármelyik lemezre, és válassza a tulajdonságok lehetőséget.
1. A LUN az "általános" lap "location" (hely) tulajdonságában jelenik meg.

### <a name="storage-pools"></a>Storage-készletek

1. Kapcsolódjon a virtuális géphez, és nyissa meg a Kiszolgálókezelő eszközt
1. Válassza a "fájl-és tárolási szolgáltatások", a "kötetek", a "tárolási készletek" lehetőséget.
1. A Kiszolgálókezelő jobb alsó sarkában a "fizikai lemezek" szakasz jelenik meg. A Storage-készletet alkotó lemezeket itt találja, valamint az egyes lemezekhez tartozó LUN-ot.

## <a name="finding-the-lun-for-the-azure-disks"></a>Az Azure-lemezek logikai egységének megkeresése

Az Azure-lemezek logikai egységét az Azure Portal, az Azure CLI vagy a Azure PowerShell használatával keresheti meg

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Azure-beli lemez logikai egységének megkeresése a Azure Portalban

1. A Azure Portal válassza a "Virtual Machines" lehetőséget a Virtual Machines listájának megjelenítéséhez
1. Válassza ki a virtuális gépet
1. Válassza a "lemezek" lehetőséget.
1. Válasszon ki egy adatlemezt a csatlakoztatott lemezek listájából.
1. A lemez a lemez részletei ablaktáblán jelenik meg. Az itt megjelenő LUN a Eszközkezelő vagy a Kiszolgálókezelő használatával a vendégen felkeresett logikai egységekkel összefügg.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Azure-beli lemez logikai egységének megkeresése az Azure CLI vagy a Azure PowerShell használatával

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---