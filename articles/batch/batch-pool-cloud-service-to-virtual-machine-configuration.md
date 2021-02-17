---
title: Batch-készlet konfigurációjának migrálása Cloud Servicesról Virtual Machinesra
description: Ismerje meg, hogyan frissítheti a készlet konfigurációját a legújabb és ajánlott konfigurációra
ms.topic: how-to
ms.date: 2/16/2021
ms.openlocfilehash: 9cbcf3864526bd8f8132f3b0f729e2d728e07bb8
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546040"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Batch-készlet konfigurációjának migrálása Cloud Servicesról Virtual Machinesra

A Batch-készleteket [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) vagy [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)használatával lehet létrehozni. a "virtualMachineConfiguration" az ajánlott konfiguráció, mivel az támogatja az összes batch-funkciót. a "cloudServiceConfiguration" készletek nem támogatják az összes funkciót, és nem terveznek új szolgáltatásokat.

Ha a "cloudServiceConfiguration" készleteket használja, erősen ajánlott, hogy a "virtualMachineConfiguration" készleteket használja. Ez lehetővé teszi az összes batch-képesség előnyeit, például a virtuálisgép- [sorozatok](batch-pool-vm-sizes.md), a linuxos virtuális gépek, a [tárolók](batch-docker-container-workloads.md), [Azure Resource Manager a virtuális hálózatok](batch-virtual-network.md)és a [csomópontos lemezek titkosításának](disk-encryption.md)kibővítését.

Ez a cikk azt ismerteti, hogyan lehet áttérni a következőre: "virtualMachineConfiguration".

## <a name="new-pools-are-required"></a>Új készletek szükségesek

A meglévő aktív készletek nem frissíthetők a "cloudServiceConfiguration" típusról a "virtualMachineConfiguration" értékre, ezért új készleteket kell létrehozni. Az "virtualMachineConfiguration" használatával létrehozott készleteket minden batch API, parancssori eszköz, Azure Portal és a Batch Explorer felhasználói felület támogatja.

**A [.net](tutorial-parallel-dotnet.md) -és [Python](tutorial-parallel-python.md) -oktatóanyagok példákat biztosítanak a készlet létrehozására a "virtualMachineConfiguration" használatával.**

## <a name="pool-configuration-differences"></a>Készlet konfigurációs eltérései

A készlet konfigurációjának frissítésekor a következőket kell figyelembe venni:

- a "cloudServiceConfiguration" pool-csomópontok mindig a Windows operációs rendszer, a "virtualMachineConfiguration" készletek lehetnek Linux vagy Windows operációs rendszer.
- A "cloudServiceConfiguration" készletekhez képest a "virtualMachineConfiguration" készletek szélesebb körű funkciókkal rendelkeznek, mint például a tárolók támogatása, az adatlemezek és a lemezek titkosítása.
- a "virtualMachineConfiguration" pool-csomópontok felügyelt operációsrendszer-lemezeket használnak. Az egyes csomópontokhoz használt [felügyelt lemez típusa](../virtual-machines/disks-types.md) a készlethez kiválasztott virtuális gép méretétől függ. Ha a virtuális gép mérete meg van adva a készlethez, például "Standard_D2s_v3", akkor a rendszer prémium szintű SSD-t használ. Ha meg van adva egy "nem s" virtuálisgép-méret, például "Standard_D2_v3", akkor a rendszer szabványos HDD-t használ.

   > [!IMPORTANT]
   > Ahogy a Virtual Machines és a Virtual Machine Scale Sets esetében is, az egyes csomópontokhoz használt operációs rendszer felügyelt lemeze költségekkel jár, ami a virtuális gépekhez képest további költségeket eredményez. A "cloudServiceConfiguration" csomópontok esetében nincs operációsrendszer-lemez, mert az operációsrendszer-lemez a helyi SSD-meghajtón jön létre.

- A készlet és a csomópont indítási és törlési ideje eltérhet a "cloudServiceConfiguration" készletek és a "virtualMachineConfiguration" készletek között.

## <a name="azure-data-factory-custom-activity-pools"></a>Egyéni tevékenység-készletek Azure Data Factory

Azure Batch készleteket Data Factory egyéni tevékenységek futtatására lehet használni. Az egyéni tevékenységek futtatásához használt összes "cloudServiceConfiguration" készletet törölni kell, és létre kell hozni az új "virtualMachineConfiguration" készleteket.

- A folyamatokat a törlés/újbóli létrehozás előtt szüneteltetni kell, hogy a végrehajtás ne legyen megszakítva.
- A társított szolgáltatás konfigurációjának módosításainak elkerüléséhez a készlet azonosítóját is használhatja.
- Folyamatok folytatása új készletek létrehozásakor.

További információ a Azure Batch használatáról Data Factory egyéni tevékenységek futtatásához:

- [Társított szolgáltatás Azure Batch](../data-factory/compute-linked-services.md#azure-batch-linked-service)
- [Egyéni tevékenységek egy Data Factory-folyamatban](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Következő lépések

- További információ a [készlet-konfigurációkról](nodes-and-pools.md#configurations).
- További információ a [Pool ajánlott eljárásairól](best-practices.md#pools).
- REST API a [készlet hozzáadására](/rest/api/batchservice/pool/add) és [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)vonatkozó referenciát.