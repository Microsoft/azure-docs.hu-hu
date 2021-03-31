---
title: Útmutató az Azure-beli virtuális gépek egy elsődleges régióba való visszaszerzéséhez a vész-helyreállítás Azure Site Recoveryával.
description: Oktatóanyag az Azure-beli virtuális gépeknek az Azure Site Recovery-vel rendelkező elsődleges régióba való feladatátvételének megismeréséhez.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93393884"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>Oktatóanyag: az Azure-beli virtuális gép visszaállítása az elsődleges régióba

Az Azure-beli virtuális gépek másodlagos Azure-régióba való feladatátvétele után kövesse ezt az oktatóanyagot, hogy a virtuális gépet a [Azure site Recovery](site-recovery-overview.md)használatával az elsődleges Azure-régióba tudja irányítani.  Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> 
> * Tekintse át az előfeltételeket.
> * Adja vissza a virtuális gépet a másodlagos régióban.
> * Állítsa vissza az elsődleges virtuális gépeket a másodlagos régióba.
> 
> [!NOTE]
> Ebből az oktatóanyagból megtudhatja, hogyan végezhet vissza minimális lépéseket. Ha teljes körű beállításokkal szeretne feladatátvételt futtatni, ismerkedjen meg az Azure VM [hálózatkezelésével](azure-to-azure-about-networking.md), [automatizálásával](azure-to-azure-powershell.md)és [hibaelhárításával](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt a következőket kell tennie:

1. [Állítson be](azure-to-azure-tutorial-enable-replication.md) legalább egy Azure-beli virtuális gép replikálását, és próbálja ki a [vészhelyzeti helyreállítási gyakorlatot](azure-to-azure-tutorial-dr-drill.md) .
2. A [virtuális gép átadása](azure-to-azure-tutorial-failover-failback.md) az elsődleges régióról egy másodlagos régióra történt, és a rendszer újra védelemmel látja el, hogy az a másodlagos régióról az elsődlegesre replikálódik. 
3. Győződjön meg arról, hogy az elsődleges régió elérhető, és hogy új erőforrásokat tud létrehozni és elérni.

## <a name="fail-back-to-the-primary-region"></a>Feladat-visszavétel az elsődleges régióba

A virtuális gépek újravédése után szükség szerint visszatérhet az elsődleges régióhoz.

1. A tárolóban > **replikált elemek** területen válassza ki a virtuális gépet.

2. A virtuális gép áttekintése lapon győződjön meg arról, hogy a virtuális gép kifogástalan állapotú, és hogy a szinkronizálás befejeződött, a feladatátvétel futtatása előtt. A virtuális gépnek *védett* állapotban kell lennie.

    ![Virtuális gép áttekintő lapja védett állapotban lévő virtuális gépek megjelenítéséhez](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. Az Áttekintés lapon válassza a **feladatátvétel** lehetőséget. Mivel most nem végezünk feladatátvételi tesztet, a rendszer felszólítja, hogy ellenőrizze.

    [Az oldal, amely azt mutatja, hogy feladatátvételi teszt nélkül futtatja a feladatátvételt](./media/azure-to-azure-tutorial-failback/no-test.png)

4. A **feladatátvétel** területen jegyezze fel a másodlagosról az elsődlegesre irányt, majd válasszon ki egy helyreállítási pontot. A célként megadott Azure-beli virtuális gép (elsődleges régió) ebben a pontban található adatok használatával jön létre.
   - **Legutóbb feldolgozott**: a site Recovery által feldolgozott legújabb helyreállítási pontot használja. Megjelenik az időbélyeg. A rendszer nem töltött le időt az adatfeldolgozás során, így alacsony helyreállítási időcélkitűzést (RTO) biztosít.
   -  **Utolsó**: a site Recoveryba elküldett összes adat feldolgozása, hogy minden virtuális gép számára hozzon létre egy helyreállítási pontot, mielőtt a művelet feladatátvételt hajt végre. A a legalacsonyabb helyreállítási időkorlátot (RPO) biztosítja, mivel a feladatátvétel indításakor a rendszer az összes adatSite Recovery replikálja.
   - **Legújabb alkalmazás-konzisztens**: Ez a beállítás a virtuális gépeket a legújabb, alkalmazás-konzisztens helyreállítási pontra hajtja végre. Megjelenik az időbélyeg.
   - **Egyéni**: feladatátvétel adott helyreállítási pontra. Az egyéni szolgáltatás csak akkor érhető el, ha egyetlen virtuális gép feladatátvételét hajtja végre, és nem használ helyreállítási tervet.

    > [!NOTE]
    > Ha feladatátvételt hajt végre egy olyan virtuális gépen, amelyhez a virtuális gép replikálásának engedélyezése után hozzáadott egy lemezt, a replikációs pontok megjelenítik a helyreállításhoz elérhető lemezeket. Egy második lemez hozzáadása előtt létrehozott replikációs pont például a következő lesz: "1/2 lemez".

4. Válassza a **gép leállítása a feladatátvétel** megkezdése előtt lehetőséget, ha azt szeretné, hogy a site Recovery megpróbálja leállítani a forrás virtuális gépeket a feladatátvétel megkezdése előtt. A Leállítás segít biztosítani az adatvesztést. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. 

    ![Feladatátvételi beállítások lap](./media/azure-to-azure-tutorial-failback/failover.png)    

3. A feladatátvétel elindításához kattintson **az OK gombra**.
4. Figyelje a feladatátvételt az értesítésekben.

    ![Értesítés a feladatátvétel előrehaladásáról](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![Értesítés a feladatátvétel sikerességéről](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>Virtuális gépek ismételt védelme

Miután a virtuális gépeket az elsődleges régióba nem sikerült visszaadni, újra kell védelemmel ellátnia azokat, hogy újra el lehessen végezni a replikálást a másodlagos régióba.

1. A virtuális gép **Áttekintés** lapján válassza az **ismételt védelem** lehetőséget.

    ![Az elsődleges régióból ismételt védelemre szolgáló gomb](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. Tekintse át az elsődleges régió célhelyének beállításait. A rendszer az újként megjelölt erőforrásokat Site Recovery hozza létre az ismételt védelem művelet részeként.
3. Kattintson az **OK** gombra az ismételt védelem folyamatának elindításához. A folyamat a kezdeti adatokat a célhelyre küldi, majd a virtuális gépekre vonatkozó különbözeti adatokat replikálja a célhelyre.

     ![Replikációs beállításokat megjelenítő oldal](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. Figyelje a riasztások állapotát az értesítésekben. 

    ![A folyamat ismételt védelemének ismételt védelemmel ](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png) kapcsolatos értesítése
    
  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A felügyelt lemezekkel rendelkező virtuális gépek esetében a feladat-visszavételt követően a virtuális gépeket a rendszer az elsődlegesről másodlagosra történő replikáció után újra védelemmel látja el, Site Recovery automatikusan törli a számítógépeket a másodlagos vész-helyreállítási régióban. Nem kell manuálisan törölnie a virtuális gépeket és a hálózati adaptereket a másodlagos régióban. A nem felügyelt lemezekkel rendelkező virtuális gépek nem törlődnek.

Ha a feladatátvételt követően teljesen letiltja a replikálást, Site Recovery törli az általa védett gépeket. Ebben az esetben a nem felügyelt lemezeket használó virtuális gépek lemezeit is törli. 
 
## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a virtuális gépeket a másodlagos régióról az elsődlegesre visszaküldte. Ez a folyamat utolsó lépése, amely magában foglalja a virtuális gép replikálásának engedélyezését, a vész-helyreállítási gyakorlat kipróbálását, az elsődleges régióról a másodlagosra történő feladatátvételt, végül pedig a feladat-visszavételt.

> [!div class="nextstepaction"]
> Próbálja ki a vész-helyreállítást az Azure-ba egy helyszíni [virtuális gépen](vmware-azure-tutorial-prepare-on-premises.md)

