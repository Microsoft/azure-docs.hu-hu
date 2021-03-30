---
title: A VMware VM-lemezek kizárása a vész-helyreállításból az Azure-ba Azure Site Recovery
description: VMware VM-lemezek kizárása a replikációból az Azure-ba Azure Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129882"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Lemezek kizárása a VMware VM-replikációból az Azure-ba

Ez a cikk azt ismerteti, hogyan zárhatók ki lemezek a VMware virtuális gépek Azure-ba történő replikálásakor a vész-helyreállítás érdekében. Előfordulhat, hogy több okból szeretné kizárni a lemezeket a replikációból:

- Győződjön meg arról, hogy a kizárt lemezen lévő nem fontos adatforgalom nem replikálódik.
- Optimalizálja a felhasznált replikációs sávszélességet vagy a cél-oldali erőforrásokat a replikálni nem szükséges lemezek kizárásával.
- Mentse a tárterületet és a hálózati erőforrásokat úgy, hogy nem replikálja a nem szükséges adatforrásokat.

A lemezek replikációból való kizárása előtt:

- [További](exclude-disks-replication.md) információ a lemezek kizárásáról.
- Tekintse át a [tipikus kizárási forgatókönyveket](exclude-disks-replication.md#typical-scenarios) és [példákat](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) , amelyek bemutatják, hogyan befolyásolja a lemez kizárása a replikációt, a feladatátvételt

## <a name="before-you-start"></a>Előkészületek

 A Kezdés előtt vegye figyelembe a következőket:

- **Replikáció**: alapértelmezés szerint a rendszer a gép minden lemezét replikálja.
- **Lemez típusa**: csak az alaplemezek zárhatók ki a replikációból. Nem zárhatja ki az operációsrendszer-lemezeket és a dinamikus lemezeket.
- **Mobilitási szolgáltatás**: Ha ki szeretne zárni egy lemezt a replikációból, a replikáció engedélyezése előtt manuálisan kell telepítenie a mobilitási szolgáltatást a gépen. A leküldéses telepítés nem használható, mivel ez a módszer csak a replikáció engedélyezése után telepíti a mobilitási szolgáltatást a virtuális gépen.  
- **Lemezek hozzáadása/eltávolítása/kizárása**: miután engedélyezte a replikálást, nem lehet hozzáadni/eltávolítani vagy kizárni a lemezeket a replikáláshoz. Ha lemezeket szeretne hozzáadni/eltávolítani vagy kizárni, le kell tiltania a gép védelmét, majd újra engedélyeznie kell azt.
- **Feladatátvétel**: a feladatátvételt követően, ha a feladatátvételen átesett alkalmazásoknak ki kell zárniuk a lemezeket, manuálisan kell létrehoznia ezeket a lemezeket. Másik megoldásként integrálhatja az Azure automationt egy helyreállítási tervbe, hogy létrehozza a lemezt a gép feladatátvétele során.
- Feladat **-visszavétel – Windows**: Ha a feladatátvételt követően visszaadja a helyszíni helyet, az Azure-ban manuálisan létrehozott Windows-lemezek nem működnek vissza. Ha például három lemez feladatátvételét hajtja végre, és közvetlenül az Azure-beli virtuális gépeken hoz létre két lemezt, akkor a rendszer csak a feladatátvétel alatt álló három lemezt fogja végrehajtani.
- Feladat **-visszavétel – Linux**: a Linux rendszerű gépek feladat-visszavételéhez az Azure-ban manuálisan létrehozott lemezek visszahívása sikertelen. Ha például három lemez feladatátvételét hajtja végre, és közvetlenül az Azure-beli virtuális gépeken hoz létre két lemezt, akkor mind az öt a feladat-visszavételi művelet. A manuálisan létrehozott lemezek nem zárhatók ki a feladat-visszavételben vagy a virtuális gépek ismételt védelmében.



## <a name="exclude-disks-from-replication"></a>Lemezek kizárása a replikációból

1. Ha [engedélyezi a replikálást](./hyper-v-azure-tutorial.md) egy VMware virtuális gép számára, a replikálni kívánt virtuális gépek kiválasztása után a **replikálási**  >  **Tulajdonságok**  >  **konfigurálása** tulajdonságlapon tekintse át a **replikálni kívánt lemezeket** . Alapértelmezés szerint az összes lemez ki van választva replikálásra.
2. Ha nem szeretne replikálni egy adott lemezt, a **lemezeken a replikáláshoz** törölje a kizárni kívánt lemezek kijelölését. 

    ![Lemezek kizárása a replikációból](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Következő lépések
Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](failover-failback-overview.md) a feladatátvételi különféle típusaival.
