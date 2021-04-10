---
title: Azure Hybrid Benefit Linux rendszerű virtuálisgép-méretezési csoportokhoz
description: Ismerje meg, hogyan alkalmazhatók a Azure Hybrid Benefit a virtuálisgép-méretezési csoportokra, így pénzt takaríthat meg az Azure-on futó linuxos virtuális gépeken.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: a714434c39a0c40c2e908f2d0c424f02851921a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933678"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Azure Hybrid Benefit Linux rendszerű virtuálisgép-méretezési csoporthoz (nyilvános előzetes verzió)

A Linux rendszerű **virtuálisgép-méretezési csoportra vonatkozó Azure Hybrid Benefit jelenleg nyilvános előzetes** verzióban érhető el. A AHB juttatás segítségével csökkentheti a RHEL és a SLES [virtuálisgép-méretezési](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)csoportok futtatásának költségeit.

Ezzel a kedvezménysel csak a méretezési csoport infrastrukturális költségeiért kell fizetnie. Az előnyök az összes RHEL és SLES piactéren elvégezhető (TB) lemezkép esetében elérhetők.


>[!NOTE]
> Ez a cikk a Linux-VMSS Azure Hybrid Benefit ismerteti. Létezik egy külön [cikk, amely a Linux rendszerű [virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux)érhető el, amely már elérhető az Azure-ügyfelek számára, 2020 novembere óta.

## <a name="benefit-description"></a>Juttatás leírása
Az Azure Hybrid lehetővé teszi a meglévő felhőalapú hozzáférési licencek használatát a Red hat-ból vagy a SUSE-ből, a virtuálisgép-méretezési csoport példányai pedig rugalmasan alakíthatók ki a saját előfizetéses (BYOS) számlázásra. 

A TB Marketplace-rendszerképből üzembe helyezett virtuálisgép-méretezési csoport az infrastruktúra és a szoftver díját is felszámítja. A AHB használatával a TB-példányok újratelepítése nélkül alakíthatók át BYOS számlázási modellre.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Azure Hybrid Benefit a Cost vizualizációt Linux rendszerű virtuális gépeken.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>A Linux rendszerhez való Azure Hybrid Benefit jogosultság hatóköre
Azure Hybrid Benefit elérhető az Azure Marketplace-ről származó összes RHEL és SLES TB-lemezképhez. Az előny még nem érhető el RHEL vagy SLES BYOS-lemezképekhez vagy egyéni rendszerképekhez az Azure Marketplace-en.

Az Azure dedikált gazdagép példányai és az SQL Hybrid előnyök nem jogosultak Azure Hybrid Benefitre, ha már használja a Linux rendszerű virtuális gépek előnyeit.

## <a name="get-started"></a>Bevezetés

### <a name="red-hat-customers"></a>Red Hat-ügyfelek

A RHEL Azure Hybrid Benefit a következő feltételeknek megfelelő Red Hat-ügyfelek számára érhető el:

- Az Azure-ban való használatra jogosult aktív vagy nem használt RHEL-előfizetések
- Egy vagy több előfizetést engedélyeztek az Azure-ban a [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) program használatával

> [!IMPORTANT]
> Győződjön meg arról, hogy a megfelelő előfizetés engedélyezve van a [felhőalapú hozzáférési](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) programban.

A Red Hat előnyeinek használatának megkezdéséhez:

1. A [Red Hat Cloud Access felhasználói felületének](https://access.redhat.com/management/cloud)használatával engedélyezheti a jogosult RHEL-előfizetéseket az Azure-ban.

   A Red Hat Cloud Access engedélyezési folyamat során megadott Azure-előfizetések ezután használhatják a Azure Hybrid Benefit funkciót.
1. Alkalmazza Azure Hybrid Benefit a meglévő és az új RHEL TB virtuálisgép-méretezési csoportokra. Az előny engedélyezéséhez használhatja a Azure Portal vagy az Azure CLI-t is.
1. Kövesse az ajánlott [következő lépéseket](https://access.redhat.com/articles/5419341) a RHEL virtuális gépek frissítési forrásainak konfigurálásához és a RHEL-előfizetés megfelelőségi iránymutatásaihoz.


### <a name="suse-customers"></a>SUSE-ügyfelek

A SUSE előnyeinek használatának megkezdéséhez:

1. Regisztráljon a SUSE nyilvános felhő programjával.
1. Alkalmazza az előnyt az újonnan létrehozott vagy meglévő virtuálisgép-méretezési csoportra az Azure Portal vagy az Azure CLI használatával.
1. Regisztrálja az előnyben részesített virtuális gépeket egy külön frissítési forrással.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Az Azure Portal előnyének engedélyezése és letiltása 
A AHB a virtuálisgép-méretezési csoportokon történő engedélyezéséhez és letiltásához **jelenleg nem érhető el** a portál.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Előnyök engedélyezése és letiltása az Azure CLI használatával

A `az vmss update` parancs használatával frissítheti a meglévő virtuális gépeket. RHEL virtuális gépek esetén futtassa a parancsot a `--license-type` paraméterrel `RHEL_BYOS` . SLES virtuális gépek esetén futtassa a parancsot a `--license-type` paraméterrel `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>PARANCSSORI felület – példa az előny engedélyezésére
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>PARANCSSORI felület – példa az előny letiltására
Az előny letiltásához használja a következő `--license-type` értéket `None` :

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> A méretezési csoportok ["frissítési szabályzattal"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) rendelkeznek, amely meghatározza, hogy a virtuális gépek hogyan legyenek naprakészek a legújabb méretezési csoport modelljével. Ezért ha a VMSS "automatikus" frissítési szabályzattal rendelkezik, a rendszer automatikusan alkalmazza a AHB juttatást, amint a virtuálisgép-példányok frissülnek. Ha a VMSS "Rolling" frissítési szabályzata van, az ütemezett frissítések alapján AHB lesz alkalmazva.
A manuális frissítési szabályzat esetében minden meglévő virtuális gép "manuális frissítését" kell végrehajtania.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>CLI-példa: virtuálisgép-méretezési csoport példányainak frissítése a "manuális frissítés" házirend esetében 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>A Azure Hybrid Benefit alkalmazása virtuálisgép-méretezési csoport létrehozásakor 
Amellett, hogy a Azure Hybrid Benefit a meglévő utólagos elszámolású virtuálisgép-méretezési csoportra alkalmazza, a virtuálisgép-méretezési csoport létrehozásakor hívhatja meg. Az előnyök a következők:
- A TB és a BYOS virtuálisgép-méretezési csoport példányait ugyanazzal a képpel és folyamattal is kiépítheti.
- Lehetővé teszi a jövőbeli licencelési mód módosítását, ami nem érhető el BYOS-képpel.
- A virtuálisgép-méretezési csoport példányai alapértelmezés szerint a Red Hat frissítési infrastruktúrához (RHUI) lesznek csatlakoztatva, így biztosítva, hogy továbbra is naprakészek és biztonságosak legyenek. Az üzembe helyezést követően bármikor módosíthatja a frissített mechanizmust.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>CLI-példa virtuálisgép-méretezési csoport létrehozására a AHB benefittel
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Következő lépések
* [Megtudhatja, hogyan hozhat létre és frissíthet virtuális gépeket, és hogyan adhat hozzá licenceket (RHEL_BYOS, SLES_BYOS) az Azure CLI-vel való Azure Hybrid Benefithoz](/cli/azure/vmss)
