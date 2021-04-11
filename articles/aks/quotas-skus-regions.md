---
title: Erőforrások, SKU-régiók és régiók korlátai
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az alapértelmezett kvótákat, a korlátozott csomópontú virtuális gépek SKU-méreteit és az Azure Kubernetes szolgáltatás (ak) régiójának rendelkezésre állását.
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 3e1e74834153584525d2093d2a1bb8ba8e991e5a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011464"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóták, virtuális gépek méretére vonatkozó korlátozások és a régió elérhetősége az Azure Kubernetes szolgáltatásban (ak)

Az összes Azure-szolgáltatás alapértelmezett korlátokat és kvótákat állít be az erőforrásokhoz és szolgáltatásokhoz, beleértve bizonyos virtuálisgép-SKU-használati korlátozásokat is.

Ez a cikk az Azure Kubernetes-szolgáltatás (ak) erőforrásainak alapértelmezett erőforrás-korlátait és az AK-ban található AK-régiók elérhetőségét részletezi.

## <a name="service-quotas-and-limits"></a>Szolgáltatási kvóták és korlátok

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Üzembe helyezett infrastruktúra

Az üzembe helyezett infrastruktúrára minden egyéb hálózati, számítási és tárterületi korlátozás érvényes. A vonatkozó korlátokat az [Azure-előfizetések és-szolgáltatások korlátozásai](../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben tekintheti meg.

> [!IMPORTANT]
> AK-fürt frissítésekor a rendszer átmenetileg felhasznál további erőforrásokat. Ezek az erőforrások közé tartoznak például a virtuális hálózati alhálózat vagy a virtuális gép vCPU kvótájában elérhető IP-címek. 
>
> Windows Server-tárolók esetén a legújabb csomópont-frissítések alkalmazásához frissítési műveletet is végrehajthat. Ha nem rendelkezik a rendelkezésre álló IP-címtartomány vagy a vCPU kvóta az ideiglenes erőforrások kezeléséhez, a fürt frissítési folyamata sikertelen lesz. További információ a Windows Server-csomópont frissítési folyamatáról: [Node-készlet frissítése az AK-ban][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Korlátozott virtuálisgép-méretek

Az AK-fürtök mindegyik csomópontja rögzített mennyiségű számítási erőforrást (például vCPU és memóriát) tartalmaz. Ha egy AK-csomópont nem elegendő számítási erőforrást tartalmaz, előfordulhat, hogy a hüvelyek nem fognak megfelelően futni. A szükséges *Kube-* hüvelyek és az alkalmazások megbízható ütemezésének biztosításához **ne használja a következő virtuálisgép-SKU-t az AK-ban**:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

A virtuálisgép-típusokkal és a számítási erőforrásokkal kapcsolatos további információkért lásd: [virtuális gépek méretei az Azure-ban][vm-skus].

## <a name="region-availability"></a>Régiónkénti elérhetőség

A fürtök üzembe helyezésének és futtatásának legújabb listáját lásd: az [AK-régiók rendelkezésre állása][region-availability].

## <a name="next-steps"></a>Következő lépések

Bizonyos alapértelmezett korlátok és kvóták is megnövelhető. Ha az erőforrás támogatja a növekedést, kérje a növelést egy [Azure-támogatási kérelemben][azure-support] (a **probléma típusa** beállításnál válassza a **kvóta** lehetőséget).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
