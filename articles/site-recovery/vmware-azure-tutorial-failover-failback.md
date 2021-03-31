---
title: VMware virtuális gépek feladatátvétele az Azure-ba Site Recovery
description: Ismerje meg, hogyan végezhet feladatátvételt VMware virtuális gépeken az Azure-ban Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: bf47f08ac555cf60f59ba2b1a84750b6a9e2e0a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132006"
---
# <a name="fail-over--vmware-vms"></a>Feladatátvétel VMware virtuális gépeken

Ez a cikk bemutatja, hogyan hajthat végre feladatátvételt egy helyszíni VMware virtuális gépen (VM) az Azure-ba [Azure site Recovery](site-recovery-overview.md)használatával.

Ez egy sorozat ötödik oktatóanyaga, amely bemutatja, hogyan állíthatja be a vész-helyreállítást az Azure-ba a helyszíni gépekhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy a VMware virtuális gép tulajdonságai megfelelnek-e az Azure követelményeinek.
> * Adott virtuális gépek feladatátvétele az Azure-ba.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Ha részletes tájékoztatást szeretne a feladatátvételről, tekintse meg a [virtuális gépek és a fizikai kiszolgálók](site-recovery-failover.md)feladatátvétele című témakört.

[További](failover-failback-overview.md#types-of-failover) információ a feladatátvétel különböző típusairól. Ha több virtuális gépet szeretne átadni egy helyreállítási tervben, tekintse át [ezt a cikket](site-recovery-failover.md).

## <a name="before-you-start"></a>Előkészületek

Fejezze be az előző oktatóanyagokat:

1. Győződjön meg arról, hogy [beállította az Azure](tutorial-prepare-azure.md) -t a VMWare virtuális gépek, a Hyper-V virtuális gépek és a fizikai gépek helyszíni vész-helyreállításához az Azure-ban.
2. Készítse elő a helyszíni [VMware](vmware-azure-tutorial-prepare-on-premises.md) -környezetet a vész-helyreállításhoz. 
3. Állítsa be a vész-helyreállítást a [VMWare virtuális gépekhez](vmware-azure-tutorial.md).
4. A vész- [helyreállítási gyakorlat](tutorial-dr-drill-azure.md) futtatásával győződjön meg róla, hogy minden a vártnak megfelelően működik-e.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

A feladatátvétel futtatása előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gépek megfelelnek az [Azure követelményeinek](vmware-physical-azure-support-matrix.md#replicated-machines).

A tulajdonságok ellenőrzése a következőképpen történik:

1. A **védett elemek** területen válassza a **replikált elemek** lehetőséget, majd válassza ki az ellenőrizni kívánt virtuális gépet.

2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Válassza a **Tulajdonságok** lehetőséget a további részletek megtekintéséhez.

3. A **számítás és hálózat** szolgáltatásban szükség szerint módosíthatja ezeket a tulajdonságokat:
    * Azure-beli név
    * Erőforráscsoport
    * Cél mérete
    * [Rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md)
    * Felügyelt lemez beállításai

4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve a következőket:

    * A hálózat és az alhálózat, amelyben az Azure-beli virtuális gép a feladatátvétel után lesz elhelyezve.
    * Az IP-cím, amely hozzá lesz rendelve.

5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A **Beállítások**  >  **replikált elemek** területen válassza ki azt a virtuális gépet, amelyen át szeretné adni a **feladatátvételt**, majd válassza a feladatátvétel lehetőséget.
2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:
   * **Legújabb**: Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. Ez a legalacsonyabb helyreállítási időkorlátot (RPO) biztosítja, mivel a feladatátvételt követően létrehozott Azure virtuális gép minden olyan adattal rendelkezik, amelyet a feladatátvétel elindításakor Site Recovery replikált a rendszer.
   * **Legutóbb feldolgozott**: Ez a lehetőség a virtuális gépet a site Recovery által feldolgozott legutóbbi helyreállítási pontra nem tudja átvenni. Ez a beállítás alacsony RTO (helyreállítási idő célkitűzés) biztosít, mivel a feldolgozatlan adatmennyiségek feldolgozása nem történik meg.
   * Az **alkalmazás legújabb konzisztens** beállítása: Ez a beállítás a virtuális gép feladatátvételét a site Recovery által feldolgozott legújabb, alkalmazás-konzisztens helyreállítási pontra hajtja végre.
   * **Egyéni**: Ez a beállítás lehetővé teszi egy helyreállítási pont megadását.

3. A feladatátvétel elindítása előtt válassza a **gép leállítása a feladatátvétel** megkezdése előtt lehetőséget. A feladatátvétel akkor is folytatódik, ha a leállás sikertelen. A feladatátvételi folyamat a **feladatok** lapon követhető.

Bizonyos esetekben a feladatátvételhez további feldolgozásra van szükség, amely körülbelül 8 – 10 percet vesz igénybe. Előfordulhat, hogy a feladatátvételi teszt időpontját a következőre észleli:

* A 9,8-nál régebbi mobilitási szolgáltatást futtató VMware virtuális gépek.
* Fizikai kiszolgálók.
* VMware Linux rendszerű virtuális gépek.
* Fizikai kiszolgálóként védett Hyper-V virtuális gépek.
* Azok a VMware virtuális gépek, amelyeken nincs engedélyezve a DHCP szolgáltatás.
* A következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépek: storvsc, VMBus, storflt, Intelide, ATAPI.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt. A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

## <a name="connect-to-failed-over-vm"></a>Kapcsolódás a feladatátvételi virtuális géphez

1. Ha RDP protokoll (RDP) és Secure Shell (SSH) használatával szeretne feladatátvételt létesíteni az Azure virtuális gépekkel, [ellenőrizze, hogy teljesülnek-e a követelmények](failover-failback-overview.md#connect-to-azure-after-failover).
2. A feladatátvétel után lépjen a virtuális gépre, és ellenőrizze, hogy [csatlakozik](../virtual-machines/windows/connect-logon.md) -e hozzá.
3. Ha a feladatátvételt követően eltérő helyreállítási pontot szeretne használni, használja a **helyreállítási pont módosítása** lehetőséget. Miután a következő lépésben véglegesíti a feladatátvételt, ez a lehetőség többé nem lesz elérhető.
4. Az ellenőrzés után válassza a **véglegesítés** lehetőséget a virtuális gép helyreállítási pontjának véglegesítéséhez a feladatátvétel után.
5. A véglegesítés után a rendszer az összes többi elérhető helyreállítási pontot is törli. Ez a lépés befejezi a feladatátvételt.

>[!TIP]
> Ha a feladatátvételt követően bármilyen csatlakozási probléma merül fel, kövesse a [hibaelhárítási útmutatót](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Következő lépések

A feladatátvételt követően az Azure-beli virtuális gépeket a helyszíni környezetbe újra kell védetté tenni. Ezt követően a virtuális gépek ismételt védelemmel és a helyszíni helyre történő replikálásával visszatérhet az Azure-ból, ha elkészült.

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek](vmware-azure-reprotect.md) 
>  ismételt védetté Feladat [-visszavétel az Azure-ból](vmware-azure-failback.md)
