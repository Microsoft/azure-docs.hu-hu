---
title: PUT-hívások műveletek létrehozásához és frissítéséhez
description: A számítási erőforrások létrehozási vagy frissítési műveleteire irányuló hívások
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: d6ee4179dce905d637e933743ade7452a2484077
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91978560"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>A számítási erőforrások létrehozásához vagy frissítéséhez szükséges hívások

`Microsoft.Compute` az erőforrások nem támogatják a *http Put* szemantika hagyományos definícióját. Ehelyett ezek az erőforrások javítási szemantikat használnak mind a PUT, mind a PATCH művelethez.

A **létrehozási** műveletek az alapértelmezett értékeket alkalmazzák, ha vannak ilyenek. Az erőforrás- **frissítések** Put vagy patch használatával végzett frissítése azonban nem alkalmazza az alapértelmezett tulajdonságokat. Az **Update** Operations alkalmazás szigorú javítási szemantikai műveleteket alkalmaz.

A `caching` virtuális gép lemez tulajdonsága például alapértelmezett értéke, `ReadWrite` Ha az erőforrás egy operációsrendszer-lemez.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

Ha azonban **frissítési** műveletekre van szükség egy tulajdonság kimaradása esetén, vagy *Null* értéket ad át, akkor változatlan marad, és nincsenek alapértelmezett értékek.

Ez akkor fontos, amikor frissítési műveleteket küld egy erőforrásnak a társítás eltávolítására irányuló szándékkal. Ha ez az erőforrás egy `Microsoft.Compute` erőforrás, az eltávolítandó tulajdonságot explicit módon ki kell nevezni, és hozzá kell rendelni egy értéket. Ennek eléréséhez a felhasználók egy üres sztringet (például **""**) adhatnak át. Ez arra utasítja a platformot, hogy távolítsa el a társítást.

> [!IMPORTANT]
> A "javítás" tömb elem nem támogatott. Ehelyett az ügyfélnek egy PUT vagy PATCH kérést kell tennie a frissített tömb teljes tartalmával. Ha például egy adatlemezt szeretne leválasztani egy virtuális gépről, hajtson végre GET kérést a jelenlegi virtuálisgép-modell beszerzéséhez, távolítsa el a leválasztani kívánt lemezt, `properties.storageProfile.dataDisks` és végezzen el egy Put kérelmet a frissített VM-entitással.

## <a name="examples"></a>Példák

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Megfelelő adattartalom a közelségi elhelyezési csoportok társításának eltávolításához

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Helytelen hasznos adatok a közelségi elhelyezési csoportok társításának eltávolításához

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Következő lépések
További információ a [Virtual Machines](/rest/api/compute/virtualmachines/createorupdate) -és [Virtual Machine Scale sets](/rest/api/compute/virtualmachinescalesets/createorupdate) -hívások létrehozásáról és frissítéséről