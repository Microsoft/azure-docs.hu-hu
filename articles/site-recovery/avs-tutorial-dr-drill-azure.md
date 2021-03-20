---
title: Vész-helyreállítási gyakorlat futtatása az Azure VMware-megoldásból az Azure-ba Azure Site Recovery
description: Megtudhatja, hogyan futtathat vész-helyreállítási gyakorlatot az Azure VMware-megoldás saját felhőből az Azure-ba, Azure Site Recovery használatával.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 46d5e1cf773a24b032874ee54021c780e1f361af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91814571"
---
# <a name="run-a-disaster-recovery-drill-from-azure-vmware-solution-to-azure"></a>Vész-helyreállítási részletezés futtatása Azure VMware-megoldásból az Azure-ba

Ez a cikk bemutatja, hogyan futtathat vész-helyreállítási gyakorlatot egy Azure VMware-megoldás virtuális géphez az Azure-ba az [Azure site Recovery](site-recovery-overview.md) szolgáltatással. A próba adatveszteség nélkül ellenőrzi a replikációs stratégiát.


Ez egy sorozat negyedik oktatóanyaga, amely bemutatja, hogyan állíthatja be a vész-helyreállítást az Azure-ba az Azure VMware Solution Machines szolgáltatásban.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]

> * Elkülönített hálózat beállítása a feladatátvételi teszthez
> * Felkészülés az Azure-beli virtuális géphez való kapcsolódásra a feladatátvételt követően
> * Futtasson feladatátvételi tesztet egyetlen gépen.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Ha többet szeretne megtudni a vész-helyreállítási részletezés részletes lépéseiről, [tekintse át ezt a cikket](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Előkészületek

Fejezze be az előző oktatóanyagokat:

1. Győződjön meg róla, hogy [beállította az Azure](avs-tutorial-prepare-azure.md) -t a vész-helyreállításhoz az Azure-ban.
2. Kövesse az [alábbi lépéseket](avs-tutorial-prepare-avs.md) az Azure VMware-megoldás üzembe helyezésének előkészítéséhez az Azure-ba történő vész-helyreállítás érdekében.
3. [Állítsa be](avs-tutorial-replication.md) a vész-helyreállítást az Azure VMware-megoldás virtuális gépei számára.
 

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvételi teszt futtatása előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a  [VMWare virtuális gép](vmware-physical-azure-support-matrix.md#replicated-machines) megfelel az Azure követelményeinek.

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.
2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **számítás és hálózat** területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célként megadott méretet, a rendelkezésre állási csoportot és a felügyelt lemez beállításait.
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.
5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="create-a-network-for-test-failover"></a>Hálózat létrehozása feladatátvételi teszt számára

Azt javasoljuk, hogy a feladatátvétel teszteléséhez olyan hálózatot válasszon, amely el van különítve az éles helyreállítási oldal hálózatától, amely az egyes virtuális gépek **számítási és hálózati** beállításaiban van megadva. Alapértelmezés szerint egy Azure-beli virtuális hálózat létrehozásakor a hálózat el van különítve az egyéb hálózatoktól. A teszthálózatnak az éles hálózatot kell utánoznia:

- A teszthálózatnak ugyanannyi alhálózattal kell rendelkeznie, mint az éles hálózatnak. Az alhálózatoknak ugyanazzal a névvel kell rendelkezniük.
- A teszt hálózatnak ugyanazt az IP-címet és alhálózati tartományt kell használnia.
- Frissítse a teszthálózat DNS-ét a DNS VM-hez a **számítási és hálózati** beállítások között megadott IP-címmel. További információkért olvassa el az [Active Directoryra vonatkozó feladatátvételi szempontokat](site-recovery-active-directory.md#test-failover-considerations) ismertető cikket.

## <a name="run-a-test-failover-for-a-single-vm"></a>Feladatátvételi teszt futtatása egyetlen virtuális gép esetén

Feladatátvételi teszt futtatásakor a következő történik:

1. A rendszer lefuttatja az előfeltételek ellenőrzését, hogy a feladatátvételhez szükséges feltételek biztosan teljesüljenek.
2. A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
3. A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.

A következőképpen futtassa a feladatátvételi tesztet:

1. A **Beállítások**  >  **replikált elemek** területen kattintson a virtuális gép > **+ feladatátvételi teszt** elemre.
2. Ehhez az oktatóanyaghoz válassza a **Legutóbb feldolgozott** helyreállítási pontot. Ez a lehetőség a virtuális gépet a rendelkezésre álló legújabb helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
3. A **Feladatátvételi teszt** területen válassza ki, hogy az Azure virtuális gépek mely cél Azure-hálózathoz csatlakozzanak majd a feladatátvételt követően.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A folyamat előrehaladásának megtekintéséhez kattintson a virtuális gépre, és nyissa meg a tulajdonságait. Azt is megteheti, hogy a **Feladatátvételi teszt** feladatra kattint a tároló neve > **Beállítások** > **Feladatok** >
   **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ellenőrizze, hogy a virtuális gép mérete megfelelő-e, hogy a gép a megfelelő hálózathoz csatlakozik-e, illetve, hogy fut-e.
6. Így már tudnia kell csatlakozni a replikált virtuális géphez az Azure-ban.
7. A feladatátvételi teszt során létrehozott Azure-beli virtuális gépek törléséhez kattintson a **Feladatátvételi teszt eltávolítása** elemre a virtuális gépen. A **jegyzetek** területen jegyezze fel és mentse a feladatátvételi teszttel kapcsolatos megfigyeléseket.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Bizonyára észrevette, hogy hosszabb a feladatátvételi teszt ideje a VMware Linux gépeken, a nem DHCP-vel kiosztott IP-című VMware virtuális gépeken, valamint a következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépeken: storvsc, vmbus, storflt, intelide, atapi.

## <a name="connect-after-failover"></a>Kapcsolat feladatátvétel után

Ha az Azure-beli virtuális gépeket a feladatátvételt követően RDP/SSH használatával szeretné összekapcsolni, [készüljön fel a csatlakozásra](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Ha a feladatátvételt követően bármilyen csatlakozási probléma merül fel, kövesse a [hibaelhárítási](site-recovery-failover-to-azure-troubleshoot.md) útmutatót.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Feladatátvétel futtatása](avs-tutorial-failover.md)

