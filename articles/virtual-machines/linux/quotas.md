---
title: vCPU-kvóták
description: Ismerje meg az Azure-hoz készült vCPU-kvótákat.
author: cynthn
ms.service: virtual-machines
ms.subservice: quota
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 1b9c0d50754d582ca7ada5d0b46c6f998b59d3ae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549566"
---
# <a name="check-vcpu-quotas-using-the-azure-cli"></a>VCPU-kvóták keresése az Azure CLI használatával

A virtuális gépekhez és a virtuálisgép-méretezési csoportokhoz tartozó vCPU-kvóták minden egyes előfizetéshez két rétegben vannak rendezve. Az első réteg a teljes regionális vCPU, a második pedig a virtuális gépek különböző méretű, például a D sorozatú vCPU. Amikor új virtuális gépet telepít, a virtuális gép vCPU nem lépheti túl a virtuális gép vCPU-kvótáját vagy a teljes regionális vCPU-kvótát. Ha túllépi a kvótákat, a virtuális gép üzembe helyezése nem lesz engedélyezett. A régión belül a virtuális gépek teljes száma is rendelkezik kvótával. Az egyes kvóták részletei a [Azure Portal](https://portal.azure.com) **előfizetés** lapjának **használat + kvóták** szakaszában láthatók, vagy az Azure CLI használatával is lekérdezheti az értékeket.

> [!NOTE]
> A kvóta kiszámításának alapja a lefoglalt és a visszafoglalt magok teljes száma. Ha további magokra van szüksége, [igényeljen kvótát](../../azure-portal/supportability/resource-manager-core-quotas-request.md) , vagy törölje azokat a virtuális gépeket, amelyekre már nincs szükség. 


## <a name="check-usage"></a>Használat ellenőrzése

A kvóta használatát az [az VM List-használat](/cli/azure/vm)használatával tekintheti meg.

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Ezután a következőhöz hasonló eredményt kell kapnia:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Reserved VM Instances
A fenntartott VM-példányok, amelyek a virtuális gépek méretének rugalmassága nélkül egyetlen előfizetésre vannak korlátozva, új aspektust adhatnak hozzá a vCPU-kvótához. Ezek az értékek leírják, hogy a megadott méret hány példánya legyen üzembe helyezhető az előfizetésben. A kvótarendszer helyőrzőként működnek, így biztosítható, hogy a kvóta le legyen foglalva ahhoz, hogy az Azure-foglalások elérhetők legyenek az előfizetésben. Ha például egy adott előfizetés 10 Standard_D1 foglal le, Standard_D1 foglalások használati korlátja 10 lesz. Ez azt eredményezi, hogy az Azure gondoskodik arról, hogy a Standard_D1 példányok esetében legalább 10 vCPU legyen elérhető a teljes regionális vCPU-kvótában, és legalább 10 vCPU legyen elérhető a standard D család vCPU-kvótájában, amelyet Standard_D1 példányokhoz kell használni.

Ha egy előfizetési RI megvásárlásához kvóta-növelésre van szükség, akkor az előfizetésre vonatkozó [kvóta növelését kérheti](../../azure-portal/supportability/resource-manager-core-quotas-request.md) .

## <a name="next-steps"></a>Következő lépések

A számlázással és a kvótákkal kapcsolatos további információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json).
