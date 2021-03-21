---
title: Azure-lemezek leképezése Linux rendszerű virtuális gép vendég lemezekre
description: A Linux rendszerű virtuális gépekhez tartozó vendég lemezeket leképező Azure-lemezek meghatározása.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.collection: linux
ms.openlocfilehash: bc6c6273ab3d1a4403763e4ed0a8c491995fb2df
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556723"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Azure-lemezek leképezése Linux rendszerű virtuális gép vendég lemezekre

Előfordulhat, hogy meg kell határoznia a virtuális gép vendég lemezeit támogató Azure-lemezeket. Bizonyos esetekben a lemez vagy a kötet méretét összehasonlíthatja a csatlakoztatott Azure-lemezek méretével. Olyan esetekben, amikor több, azonos méretű Azure-lemez van a virtuális géphez csatlakoztatva, az adatlemezek logikai egységének számát (LUN) kell használnia. 

## <a name="what-is-a-lun"></a>Mi az a LUN?

A logikai egységek száma (LUN) egy adott tárolóeszköz azonosítására szolgáló szám. Minden tárolóeszközhöz egyedi numerikus azonosító van rendelve, amely nullával kezdődik. Az eszköz teljes elérési útját a busz száma, a célként megadott azonosító száma és a logikai egység száma (LUN) jelképezi. 

Például: ***Bus Number 0, cél azonosítója 0, LUN 3***

A gyakorlatban csak a LUN-t kell használnia.

## <a name="finding-the-lun"></a>A LUN megkeresése

Alább két módszert ismertetünk, amelyekkel megtalálhatja a lemez LUN-jét Linux rendszerben.

### <a name="lsscsi"></a>lsscsi

1. Kapcsolódás a virtuális géphez
1. `sudo lsscsi`

A felsorolt első oszlop tartalmazza a LUN-t, a formátum: [gazdagép: csatorna: Target:**LUN**].

### <a name="listing-block-devices"></a>Blokkos eszközök listázása

1. Kapcsolódás a virtuális géphez
1. `sudo ls -l /sys/block/*/device`

A lista utolsó oszlopa tartalmazza a LUN-t, a formátum: [gazdagép: csatorna: cél:**LUN**]

## <a name="finding-the-lun-for-the-azure-disks"></a>Az Azure-lemezek logikai egységének megkeresése

Az Azure-lemezek logikai egységét az Azure Portal, az Azure CLI használatával keresheti meg.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Azure-beli lemez logikai egységének megkeresése a Azure Portalban

1. A Azure Portal válassza a "Virtual Machines" lehetőséget a Virtual Machines listájának megjelenítéséhez
1. Válassza ki a virtuális gépet
1. Válassza a "lemezek" lehetőséget.
1. Válasszon ki egy adatlemezt a csatlakoztatott lemezek listájából.
1. A lemez a lemez részletei ablaktáblán jelenik meg. Az itt megjelenő LUN a lsscsi használatával felkeresett logikai egységekkel, vagy a blokk eszközeinek listázásával összefügg.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Azure-beli lemez logikai egységének megkeresése az Azure CLI-vel

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
