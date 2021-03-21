---
title: A Hyper-V VM-lemezek kizárása a vész-helyreállításból az Azure-ba Azure Site Recovery
description: Hyper-V virtuális gépek lemezeinek kizárása a replikációból az Azure-ba Azure Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86131247"
---
# <a name="exclude-disks-from-replication"></a>Lemezek kizárása a replikációból

Ez a cikk azt ismerteti, hogyan zárhatók ki lemezek a Hyper-V virtuális gépek Azure-ba történő replikálása során. Előfordulhat, hogy több okból szeretné kizárni a lemezeket a replikációból:

- Győződjön meg arról, hogy a kizárt lemezen lévő nem fontos adatforgalom nem replikálódik.
- Optimalizálja a felhasznált replikációs sávszélességet vagy a cél-oldali erőforrásokat a replikálni nem szükséges lemezek kizárásával.
- Mentse a tárterületet és a hálózati erőforrásokat úgy, hogy nem replikálja a nem szükséges adatforrásokat.

A lemezek replikációból való kizárása előtt:

- [További](exclude-disks-replication.md) információ a lemezek kizárásáról.
- Tekintse át a [tipikus kizárási forgatókönyveket](exclude-disks-replication.md#typical-scenarios) és [példákat](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) , amelyek bemutatják, hogyan befolyásolja a lemez kizárása a replikációt, a feladatátvételt

## <a name="before-you-start"></a>Előkészületek

A Kezdés előtt vegye figyelembe a következőket:

- **Replikáció**: alapértelmezés szerint a rendszer a gép minden lemezét replikálja.
- **Lemez típusa**:
    - A replikációból kizárhatja az alaplemezeket.
    - Nem zárhatja ki az operációsrendszer-lemezeket.
    - Azt javasoljuk, hogy ne zárja ki a dinamikus lemezeket. Site Recovery nem tudja azonosítani, hogy melyik VHD a vendég virtuális gép alapszintű vagy dinamikus.  Ha nem zárja ki az összes függő dinamikus kötet lemezét, a védett dinamikus lemez hibás lemezvé válik a feladatátvételen átadott virtuális gépen, és a lemezen lévő adat nem érhető el.
- **Lemezek hozzáadása/eltávolítása/kizárása**: miután engedélyezte a replikálást, nem lehet hozzáadni/eltávolítani vagy kizárni a lemezeket a replikáláshoz. Ha lemez hozzáadását vagy eltávolítását vagy kizárását szeretné végezni, le kell tiltania a virtuális gép védelmét, majd újra engedélyeznie kell.
- **Feladatátvétel**: Ha a feladatátvételt követően az alkalmazásoknak ki kell zárniuk a lemezeket, manuálisan kell létrehoznia ezeket a lemezeket. Másik megoldásként integrálhatja az Azure automationt egy helyreállítási tervbe, hogy létrehozza a lemezt a gép feladatátvétele során.
- Feladat- **visszavétel**: Ha feladatátvétel után visszaadja a helyszíni helyet, az Azure-ban manuálisan létrehozott lemezek nem működnek vissza. Ha például három lemez feladatátvételét hajtja végre, és közvetlenül egy Azure-beli virtuális gépen hoz létre két lemezt, akkor a rendszer csak a feladatátvétel alatt álló három lemezt adja vissza. Nem vehetők fel manuálisan a feladat-visszavétel során létrehozott lemezek, vagy a virtuális gépek fordított replikációja.

## <a name="exclude-disks"></a>Lemezek kizárása

1. Ha egy Hyper-V virtuális gép [replikálásának engedélyezése](./hyper-v-azure-tutorial.md) után szeretné kizárni a lemezeket, a replikálni kívánt virtuális gépek kiválasztása után a **replikálási**  >  **Tulajdonságok**  >  **konfigurálása tulajdonságok** lapon tekintse át az oszlop **replikálásához szükséges lemezeket** . Alapértelmezés szerint az összes lemez ki van választva replikálásra.
2. Ha nem szeretne replikálni egy adott lemezt, a **lemezeken a replikáláshoz** törölje a kizárni kívánt lemezek kijelölését. 

    ![Lemezek kizárása a replikációból](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Következő lépések
Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](failover-failback-overview.md) a feladatátvételi különféle típusaival.
