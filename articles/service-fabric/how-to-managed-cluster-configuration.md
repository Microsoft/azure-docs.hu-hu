---
title: A Service Fabric felügyelt fürt konfigurálása (előzetes verzió)
description: Megtudhatja, hogyan konfigurálhatja a Service Fabric felügyelt fürtöt automatikus operációsrendszer-frissítésekhez, NSG-szabályokhoz és egyebekhez.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732632"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Service Fabric felügyelt fürt (előzetes verzió) konfigurációs beállításai

A fürt létrehozásakor a [Service Fabric felügyelt fürt SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) -jának kiválasztásán kívül számos más módszert is konfigurálhat. Az aktuális előzetes verzióban a következőket teheti:

* A fürt [hálózati beállításainak](how-to-managed-cluster-networking.md) konfigurálása
* Virtuálisgép- [méretezési csoport kiterjesztésének](how-to-managed-cluster-vmss-extension.md) hozzáadása csomópont-típushoz
* [Felügyelt identitás](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) konfigurálása a csomópont-típusoknál
* Az [operációs rendszer automatikus frissítéseinek](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) engedélyezése a csomópontok számára
* Az [operációs rendszer és az adatlemez titkosításának](how-to-enable-managed-cluster-disk-encryption.md) engedélyezése a csomópontokon

## <a name="enable-automatic-os-image-upgrades"></a>Az operációs rendszer rendszerképének automatikus frissítésének engedélyezése

Dönthet úgy is, hogy engedélyezi az operációs rendszer rendszerképének automatikus frissítését a felügyelt fürtcsomópontok futtatására szolgáló virtuális gépeken. Bár a virtuálisgép-méretezési csoport erőforrásai az Ön nevében Service Fabric felügyelt fürtökkel vannak kezelve, lehetővé teszi az operációs rendszer rendszerképének automatikus frissítését a fürtcsomópontok számára. A [klasszikus Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) -fürtökhöz hasonlóan a felügyelt fürtcsomópontok nem frissülnek alapértelmezés szerint, hogy megakadályozza a fürt nem szándékos megszakadását.

Az operációs rendszer automatikus frissítéseinek engedélyezése:

* Használja a `2021-01-01-preview` *Microsoft. ServiceFabric/managedclusters* és a *Microsoft. ServiceFabric/managedclusters/nodetypes* erőforrások (vagy újabb) verzióját.
* A fürt tulajdonságának beállítása `enableAutoOSUpgrade` *true (igaz* ) értékre
* A fürt nodeTypes erőforrás-tulajdonságának beállítása a `vmImageVersion` *legújabb* értékre

Például:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Ha engedélyezve van, Service Fabric megkezdi a felügyelt fürt operációsrendszer-rendszerkép-verzióinak lekérdezését és nyomon követését. Ha új operációsrendszer-verzió érhető el, a fürtcsomópont-típusok (a virtuálisgép-méretezési csoportok) frissítése egyszerre történik. Service Fabric futtatókörnyezet frissítése csak a fürt csomópont operációsrendszer-rendszerképének frissítését követően végezhető el.

Ha egy frissítés meghiúsul, Service Fabric 24 óra elteltével újra próbálkozik, legfeljebb három újrapróbálkozás után. A klasszikus (nem felügyelt) Service Fabric frissítésekhez hasonlóan a nem megfelelő állapotú alkalmazások vagy csomópontok is letilthatják az operációs rendszer rendszerképének frissítését.

További információ a képek frissítéséről: [az operációs rendszer rendszerképének automatikus frissítése az Azure virtuálisgép-méretezési csoportokkal](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Következő lépések

[Service Fabric felügyelt fürtök áttekintése](overview-managed-cluster.md)

[Service Fabric-fürtsablonok](https://github.com/Azure-Samples/service-fabric-cluster-templates)
