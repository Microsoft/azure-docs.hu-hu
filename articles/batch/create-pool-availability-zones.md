---
title: Készlet létrehozása rendelkezésre állási zónák között
description: Megtudhatja, hogyan hozhat létre többszakaszos házirenddel rendelkező batch-készletet a hibák elleni védelem érdekében.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 56e718bedf504b8e69598c2d99ab8b889a470b89
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725288"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Azure Batch-készlet létrehozása Availability Zones között

A [Availability Zones](https://azure.microsoft.com/global-infrastructure/availability-zones/) támogató Azure-régiók legalább három különálló zónával rendelkeznek, amelyek mindegyike saját független áramforrással, hálózattal és hűtési rendszerrel rendelkezik. Ha Azure Batch-készletet hoz létre a virtuálisgép-konfiguráció használatával, dönthet úgy, hogy kiépíti a Batch-készletet Availability Zones között. Ha ezzel a zóna-házirenddel hozza létre a készletet, a kötegelt számítási csomópontok az Azure-adatközpontok meghibásodása elleni védelemben segítenek.

Létrehozhatja például a készletet egy olyan Azure-régióban, amely többhelyes szabályzattal rendelkezik, amely három Availability Zones támogat. Ha az egyik rendelkezésre állási zónában található Azure-adatközpont infrastruktúrával kapcsolatos hibával rendelkezik, a Batch-készlet továbbra is kifogástalan állapotú csomópontokkal fog rendelkezni a másik két Availability Zonesban, így a készlet elérhető marad a feladatütemezés számára.

## <a name="regional-support-and-other-requirements"></a>Regionális támogatás és egyéb követelmények

A Batch az Azure-ra vonatkozó paritást tart fenn Availability Zones támogatásával. A zóna lehetőség használatához a készletet egy [támogatott Azure-régióban](../availability-zones/az-region.md)kell létrehozni.

Ahhoz, hogy a Batch-készlet le legyen foglalva a rendelkezésre állási zónák között, a készletet létrehozó Azure-régiónak támogatnia kell a kért VM SKU-t egynél több zónában. Ezt úgy ellenőrizheti, ha meghívja az [erőforrás-SKU-lista API](/rest/api/compute/resourceskus/list) -t, és ellenőrzi a [resourceSku](/rest/api/compute/resourceskus/list#resourcesku) **locationInfo** mezőjét. Győződjön meg arról, hogy a kért VM SKU-ban több zóna is támogatott.

A [felhasználói előfizetés módú batch-fiókok](accounts.md#batch-accounts)esetében győződjön meg arról, hogy az előfizetés, amelyben a készletet létrehozza, nem rendelkezik zónára vonatkozó korlátozással a kért VM-SKU-ra vonatkozóan. Ennek megerősítéséhez hívja meg a [Resource SKU List API](/rest/api/compute/resourceskus/list) -t, és ellenőrizze a [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions). Ha a zóna korlátozása létezik, egy [támogatási jegyet](/troubleshoot/azure/general/region-access-request-process) is elküldhet a zóna korlátozásának eltávolításához.

Azt is vegye figyelembe, hogy nem hozhat létre olyan készletet, amely a csomópontok közötti kommunikációt lehetővé teszi, és a [InfiniBand támogató VM SKU](../virtual-machines/workloads/hpc/enable-infiniband.md)-t használ.

## <a name="create-a-batch-pool-across-availability-zones"></a>Batch-készlet létrehozása Availability Zones között

Az alábbi példák bemutatják, hogyan hozhat létre batch-készleteket Availability Zones között.

> [!NOTE]
> Ha a készletet egy zóna szerinti házirenddel hozza létre, a Batch szolgáltatás megkísérli lefoglalni a készletet a kiválasztott régió összes Availability Zones. nem adhat meg egy adott foglalást a zónák között.

### <a name="batch-management-client-net-sdk"></a>Batch Management-ügyfél .NET SDK

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL-CÍM

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

A kérés törzse

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Ismerje meg [, hogyan hozhat létre készletet egy Azure-beli virtuális hálózat alhálózatában](batch-virtual-network.md).
- Tudnivalók a [Azure batch-készletek nyilvános IP-címek nélküli létrehozásáról](./batch-pool-no-public-ip-address.md).