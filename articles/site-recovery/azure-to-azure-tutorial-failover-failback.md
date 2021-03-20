---
title: Oktatóanyag az Azure-beli virtuális gépek átadására egy másodlagos régióba Azure Site Recovery-vel való vész-helyreállítás érdekében.
description: Oktatóanyag, amelyből megtudhatja, hogyan végezheti el az Azure-beli virtuális gépek feladatátvételét a másodlagos Azure-régióba, a Azure Site Recovery szolgáltatással.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392778"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Oktatóanyag: Azure-beli virtuális gépek feladatátvétele másodlagos régióba

Ismerje meg, hogyan hajthat végre feladatátvételt olyan Azure-beli virtuális gépeken, amelyeken a [Azure site Recovery](site-recovery-overview.md), egy másodlagos Azure-régióba való vész-helyreállítás engedélyezett A feladatátvételt követően a megcélzott régióban lévő virtuális gépeket újra el kell látni, hogy az elsődleges régióba replikálódnak. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése
> * Virtuális gép beállításainak ellenőrzése
> * Feladatátvétel futtatása a másodlagos régióba
> * Indítsa el a virtuális gép replikálását az elsődleges régióba.


> [!NOTE]
> Ez az oktatóanyag bemutatja, hogyan végezhet feladatátvételt a virtuális gépeken minimális lépésekkel. Ha teljes körű beállításokkal szeretne feladatátvételt futtatni, ismerkedjen meg az Azure VM [hálózatkezelésével](azure-to-azure-about-networking.md), [automatizálásával](azure-to-azure-powershell.md)és [hibaelhárításával](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt a következőket kell tennie:

1. Egy vagy több Azure-beli virtuális gép replikálásának beállítása. Ha még nem tette meg, végezze el [az első oktatóanyagot](azure-to-azure-tutorial-enable-replication.md) ebben a sorozatban.
2. Javasoljuk, hogy [futtasson vész-helyreállítási gyakorlatot](azure-to-azure-tutorial-dr-drill.md) a replikált virtuális gépekhez. A teljes feladatátvétel futtatása előtt egy részletezést futtatva gondoskodhat arról, hogy minden a várt módon működjön, anélkül, hogy ez hatással lenne az éles környezetre. 


## <a name="verify-the-vm-settings"></a>A virtuális gép beállításainak ellenőrzése

1. A tárolóban > **replikált elemek** területen válassza ki a virtuális gépet.

    ![Lehetőség a virtuális gép tulajdonságainak megnyitására az Áttekintés oldalon](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. A virtuális gép **áttekintése** lapon győződjön meg arról, hogy a virtuális gép védett és kifogástalan állapotú, mielőtt futtatja a feladatátvételt.
    ![Lap a virtuális gép tulajdonságainak és állapotának ellenőrzéséhez](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. A feladatátvétel előtt győződjön meg az alábbiakról:
    - A virtuális gép támogatott [Windows](azure-to-azure-support-matrix.md#windows) vagy [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) operációs rendszert futtat.
    - A virtuális gép megfelel a [számítási](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [tárolási](azure-to-azure-support-matrix.md#replicated-machines---storage)és [hálózatkezelési](azure-to-azure-support-matrix.md#replicated-machines---networking) követelményeknek.

## <a name="run-a-failover"></a>Feladatátvétel futtatása


1. A virtuális gép **áttekintése** lapon válassza a **feladatátvétel** lehetőséget.

    ![A replikált elem feladatátvételi gombja](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. A **feladatátvétel** területen válassza ki a helyreállítási pontot. A célként megadott régióban található Azure-beli virtuális gép ebből a helyreállítási pontból származó adatok használatával jön létre.
  
   - **Legutóbb feldolgozott**: a site Recovery által feldolgozott legújabb helyreállítási pontot használja. Megjelenik az időbélyeg. A rendszer nem töltött le időt az adatfeldolgozás során, így alacsony helyreállítási időcélkitűzést (RTO) biztosít.
   -  **Utolsó**: a site Recoveryba elküldett összes adat feldolgozása, hogy minden virtuális gép számára hozzon létre egy helyreállítási pontot, mielőtt a művelet feladatátvételt hajt végre. A a legalacsonyabb helyreállítási időkorlátot (RPO) biztosítja, mivel a feladatátvétel indításakor a rendszer az összes adatSite Recovery replikálja.
   - **Legújabb alkalmazás-konzisztens**: Ez a beállítás a virtuális gépeket a legújabb, alkalmazás-konzisztens helyreállítási pontra hajtja végre. Megjelenik az időbélyeg.
   - **Egyéni**: feladatátvétel adott helyreállítási pontra. Az egyéni szolgáltatás csak akkor érhető el, ha egyetlen virtuális gép feladatátvételét hajtja végre, és nem használ helyreállítási tervet.

    > [!NOTE]
    > Ha a replikáció engedélyezése után hozzáadott egy lemezt a virtuális géphez, a replikációs pontok a helyreállításhoz elérhető lemezeket jelenítik meg. Egy második lemez hozzáadása előtt létrehozott replikációs pont például a következő lesz: "1/2 lemez".

4. Válassza a **gép leállítása a feladatátvétel** megkezdése előtt lehetőséget, ha azt szeretné, hogy a site Recovery megpróbálja leállítani a forrás virtuális gépeket a feladatátvétel megkezdése előtt. A Leállítás segít biztosítani az adatvesztést. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 

    ![Feladatátvételi beállítások lap](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. A feladatátvétel elindításához kattintson **az OK gombra**.
4. Figyelje a feladatátvételt az értesítésekben.

    ![Értesítés a ](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![ sikeres előrehaladásról](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. A feladatátvételt követően a céltartományban létrehozott Azure-beli virtuális gép megjelenik **Virtual Machines**. Győződjön meg arról, hogy a virtuális gép fut, és megfelelő méretű. Ha másik helyreállítási pontot szeretne használni a virtuális géphez, válassza a **helyreállítási pont módosítása** lehetőséget az **alapvető** erőforrások lapon.
6. Ha elégedett a feladatátvételen átesett virtuális géppel, válassza a **véglegesítés** lehetőséget az Áttekintés lapon a feladatátvétel befejezéséhez.

    ![Véglegesítés gomb](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. A **véglegesítés** lapon kattintson **az OK gombra** a megerősítéshez. A véglegesítés törli a virtuális gép összes rendelkezésre álló helyreállítási pontját Site Recoveryban, és nem tudja módosítani a helyreállítási pontot.

8. Figyelje a végrehajtás előrehaladását az értesítésekben.

    ![Végrehajtási előrehaladási értesítés véglegesítve – ](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![ sikeres értesítés](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. A feladatátvétel után a Site Recovery nem törli a forrás virtuális gépet. Ezt manuálisan kell elvégeznie.


## <a name="reprotect-the-vm"></a>A virtuális gép ismételt védetté

A feladatátvételt követően a virtuális gépet a másodlagos régióban újra el kell látni, hogy az az elsődleges régióba replikálódjon. 

1. Mielőtt elkezdené, győződjön meg arról, hogy a virtuális gép **állapota** *feladatátvételre van véglegesítve* .
2. Győződjön meg arról, hogy elérhető az elsődleges régió, és hogy rendelkezik a virtuális gépek létrehozásához szükséges engedélyekkel.
3. A virtuális gép **áttekintése** lapon válassza az **ismételt védelem** lehetőséget.

   ![Gomb a virtuális gép újravédelemének engedélyezéséhez.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. Az **ismételt védelem** területen ellenőrizze a replikálás irányát (másodlagosról elsődleges régióra), és tekintse át az elsődleges régió célhelyének beállításait. A rendszer az újként megjelölt erőforrásokat Site Recovery hozza létre az ismételt védelem művelet részeként.

     ![Védelmi beállítások lap](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Kattintson az **OK** gombra az ismételt védelem folyamatának elindításához. A folyamat a kezdeti adatokat a célhelyre küldi, majd a virtuális gépekre vonatkozó különbözeti adatokat replikálja a célhelyre.
7. Figyelje az értesítésekben a folyamat ismételt védelmi állapotát. 

    ![A folyamat ismételt védelemének újravédése ](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![ sikeres értesítés](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban átvette a feladatokat az elsődleges régióból a másodlagosra, és megkezdte a virtuális gépek replikálását az elsődleges régióba. Most pedig visszatérhet a másodlagos régióból az elsődlegesre.

> [!div class="nextstepaction"]
> [Feladat-visszavétel az elsődleges régióba](azure-to-azure-tutorial-failback.md)
