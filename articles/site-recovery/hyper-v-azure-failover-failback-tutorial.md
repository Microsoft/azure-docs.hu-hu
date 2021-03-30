---
title: Hyper-V virtuális gépek feladatátvételének beállítása az Azure-ba Azure Site Recovery
description: Megtudhatja, hogyan hajthat végre feladatátvételt Hyper-V virtuális gépeken az Azure-ba a Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b46a2ea12a697afde8223cc3595365c1286512c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132462"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Hyper-V virtuális gépek feladatátvétele az Azure-ba

Ez az oktatóanyag azt ismerteti, hogyan hajthat végre feladatátvételt Hyper-V virtuális gépeken az Azure-ba a [Azure site Recovery](site-recovery-overview.md)használatával. A feladatátvétel után visszaadja a feladatokat a helyszíni helyre, amint az elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy a Hyper-V virtuális gép tulajdonságai megfelelnek-e az Azure követelményeinek.
> * Adott virtuális gépek feladatátvétele az Azure-ba.


Ez az oktatóanyag egy sorozat ötödik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait.    

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](./hyper-v-prepare-on-premises-tutorial.md)
3. Vészhelyreállítás beállítása [Hyper-V virtuális gépekhez](./hyper-v-azure-tutorial.md) vagy [System Center VMM-felhőben felügyelt Hyper-V virtuális gépekhez](./hyper-v-vmm-azure-tutorial.md)
4. [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)

[További](failover-failback-overview.md#types-of-failover) információ a feladatátvétel különböző típusairól. Ha több virtuális gépet szeretne átadni egy helyreállítási tervben, tekintse át [ezt a cikket](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>A feladatátvétel előkészítése 
Gondoskodjon arról, hogy a virtuális gépen ne legyenek pillanatképek, és hogy a helyszíni virtuális gép ki legyen kapcsolva a feladat-visszavétel során. Ez elősegíti az adatok konzisztenciáját a replikáció során. A helyszíni virtuális gépet ne kapcsolja be a feladat-visszavétel során. 

A feladatátvétel és a feladat-visszavétel három fázisból áll:

1. **Feladatátvétel az Azure-ba**: Hyper-V virtuális gépek feladatainak átvétele a helyszíni helyről az Azure-ba.
2. **Feladat-visszavétel a helyszínre**: Az Azure-beli virtuális gépek feladatainak visszavétele a helyszíni helyre, ha a helyszíni hely elérhető. Ekkor a rendszer megkezdi az adatok szinkronizálását az Azure-ból a helyszínre, majd ha végzett, elindítja a helyszíni virtuális gépeket.  
3. **Helyszíni virtuális gépek visszirányú replikálása**: Miután megtörtént a feladat-visszavétel a helyszínre, a helyszíni gépek visszirányú replikálásával elindítható azok replikálása az Azure-ba.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvétel előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép megfelel az [Azure-követelményeknek](hyper-v-azure-support-matrix.md#replicated-vms).

A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.

1. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.

1. A **Számítás és hálózat** területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, a [rendelkezésre állási csoportot](../virtual-machines/windows/tutorial-availability-sets.md) és a felügyelt lemez beállításait.

1. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.

1. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="fail-over-to-azure"></a>Feladatátvétel az Azure-ba

1. A **Beállítások**  >  **replikált elemek** területen kattintson a virtuális gép > **feladatátvétel** elemre.
2. A **Feladatátvétel** területen válassza a **Legújabb** helyreállítási pontot. 
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery a feladatátvitel indítása előtt megkísérli leállítani a forrás virtuális gépeket. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamat a **feladatok** lapon követhető.
4. A feladatátvétel ellenőrzése után kattintson a **Véglegesítés** lehetőségre. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: Ha menet közben megszakítja a feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

## <a name="connect-to-failed-over-vm"></a>Kapcsolódás a feladatátvételi virtuális géphez

1. Ha RDP protokoll (RDP) és Secure Shell (SSH) használatával szeretne feladatátvételt létesíteni az Azure virtuális gépekkel, [ellenőrizze, hogy teljesülnek-e a követelmények](failover-failback-overview.md#connect-to-azure-after-failover).
2. A feladatátvétel után lépjen a virtuális gépre, és ellenőrizze, hogy [csatlakozik](../virtual-machines/windows/connect-logon.md) -e hozzá.
3. Ha a feladatátvételt követően eltérő helyreállítási pontot szeretne használni, használja a **helyreállítási pont módosítása** lehetőséget. Miután a következő lépésben véglegesíti a feladatátvételt, ez a lehetőség többé nem lesz elérhető.
4. Az ellenőrzés után válassza a **véglegesítés** lehetőséget a virtuális gép helyreállítási pontjának véglegesítéséhez a feladatátvétel után.
5. A véglegesítés után a rendszer az összes többi elérhető helyreállítási pontot is törli. Ez a lépés befejezi a feladatátvételt.

>[!TIP]
> Ha a feladatátvételt követően bármilyen csatlakozási probléma merül fel, kövesse a [hibaelhárítási útmutatót](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Következő lépések

A feladatátvételt követően újra kell védetté tenni az Azure-beli virtuális gépeket, hogy azok az Azure-ból a helyszíni rendszerbe replikálódnak. Ezt követően a virtuális gépek ismételt védelemmel és a helyszíni helyre történő replikálásával visszatérhet az Azure-ból, ha elkészült.
