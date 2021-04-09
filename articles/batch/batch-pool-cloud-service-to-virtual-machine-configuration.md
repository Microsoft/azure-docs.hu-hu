---
title: Batch-készlet konfigurációjának migrálása Cloud Servicesról Virtual Machinesra
description: Ismerje meg, hogyan frissítheti a készlet konfigurációját a legújabb és ajánlott konfigurációra
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200559"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Batch-készlet konfigurációjának migrálása Cloud Servicesról virtuális gépre

A Batch-készleteket jelenleg a [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) vagy a [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration)használatával lehet létrehozni. Azt javasoljuk, hogy csak a virtuális gép konfigurációját használja, mivel ez a konfiguráció támogatja az összes batch-funkciót.

Cloud Services konfigurációs készletek nem támogatják a jelenlegi batch-szolgáltatásokat, és nem támogatják az újonnan hozzáadott funkciókat. Az új "CloudServiceConfiguration" készleteket nem lehet létrehozni, vagy új csomópontokat hozzáadni a meglévő készletekhez a [2024. február 29. után](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

Ha a Batch-megoldások jelenleg a "cloudServiceConfiguration" készleteket használják, javasoljuk, hogy a lehető leghamarabb váltson "virtualMachineConfiguration" értékre. Ez lehetővé teszi az összes batch-képesség előnyeit, például a virtuálisgép- [sorozatok](batch-pool-vm-sizes.md), a linuxos virtuális gépek, a [tárolók](batch-docker-container-workloads.md), [Azure Resource Manager a virtuális hálózatok](batch-virtual-network.md)és a [csomópontos lemezek titkosításának](disk-encryption.md)kibővítését.

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Készlet létrehozása a virtuális gép konfigurációjának használatával

Nem válthat olyan meglévő aktív készletet, amely "cloudServiceConfiguration"-t használ a "virtualMachineConfiguration" használatához. Ehelyett új készleteket kell létrehoznia. Miután létrehozta az új "virtualMachineConfiguration" készleteket, és replikálta az összes feladatot és feladatot, törölheti a régi "cloudServiceConfiguration'pools, amelyet már nem használ.

A Batch API-k, a parancssori eszközök, a Azure Portal és a Batch Explorer felhasználói felülete lehetővé teszi, hogy a készleteket a "virtualMachineConfiguration" használatával hozza létre.

A "virtualMachineConfiguration"-et használó készletek létrehozási folyamatának [bemutatóját a .net-oktatóanyag](tutorial-parallel-dotnet.md) vagy a [Python-oktatóanyag](tutorial-parallel-python.md)részben tekintheti meg.

## <a name="pool-configuration-differences"></a>Készlet konfigurációs eltérései

A két konfiguráció közötti legfontosabb különbségek közé tartoznak a következők:

- a "cloudServiceConfiguration" pool-csomópontok csak a Windows operációs rendszert használják. a "virtualMachineConfiguration" készletek Linux vagy Windows operációs rendszert is használhatnak.
- A "cloudServiceConfiguration" készletekhez képest a "virtualMachineConfiguration" készletek szélesebb körű funkciókkal rendelkeznek, mint például a tárolók támogatása, az adatlemezek és a lemezek titkosítása.
- A készlet és a csomópont indítási és törlési ideje eltérhet a "cloudServiceConfiguration" készletek és a "virtualMachineConfiguration" készletek között.
- a "virtualMachineConfiguration" pool-csomópontok felügyelt operációsrendszer-lemezeket használnak. Az egyes csomópontokhoz használt [felügyelt lemez típusa](../virtual-machines/disks-types.md) a készlethez kiválasztott virtuális gép méretétől függ. Ha a virtuális gép mérete meg van adva a készlethez, például "Standard_D2s_v3", akkor a rendszer prémium szintű SSD-t használ. Ha meg van adva egy "nem s" virtuálisgép-méret, például "Standard_D2_v3", akkor a rendszer szabványos HDD-t használ.

   > [!IMPORTANT]
   > Ahogy a Virtual Machines és a Virtual Machine Scale Sets esetében is, az egyes csomópontokhoz használt operációs rendszer felügyelt lemeze költségekkel jár, ami a virtuális gépekhez képest további költségeket eredményez. A "cloudServiceConfiguration" csomópontok esetében nincs szükség operációsrendszer-lemezre, mert az operációsrendszer-lemez a helyi SSD-csomóponton jön létre.

## <a name="azure-data-factory-custom-activity-pools"></a>Egyéni tevékenység-készletek Azure Data Factory

Azure Batch készleteket Data Factory egyéni tevékenységek futtatására lehet használni. Az egyéni tevékenységek futtatásához használt összes "cloudServiceConfiguration" készletet törölni kell, és létre kell hozni az új "virtualMachineConfiguration" készleteket.

Ha új készleteket hoz létre Data Factory egyéni tevékenységek futtatásához, kövesse az alábbi eljárásokat:

- Az új készletek létrehozása és a régiek törlése előtt szüneteltetheti az összes folyamatot, hogy a rendszer ne szakítsa meg a végrehajtást.
- A társított szolgáltatás konfigurációjának módosításainak elkerüléséhez a készlet azonosítóját is használhatja.
- Folyamatok folytatása új készletek létrehozásakor.

További információ a Azure Batch használatáról Data Factory egyéni tevékenységek futtatásáról: [Azure batch társított szolgáltatás](../data-factory/compute-linked-services.md#azure-batch-linked-service) és  [egyéni tevékenységek Data Factory folyamatokban](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Következő lépések

- További információ a [készlet-konfigurációkról](nodes-and-pools.md#configurations).
- További információ a [Pool ajánlott eljárásairól](best-practices.md#pools).
- Tekintse meg a [készlet hozzáadása](/rest/api/batchservice/pool/add) és a [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)REST API referenciát.