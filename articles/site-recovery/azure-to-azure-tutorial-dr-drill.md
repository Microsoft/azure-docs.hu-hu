---
title: Oktatóanyag Azure-beli virtuális gépek vész-helyreállítási részletezésének futtatásához Azure Site Recovery
description: Ebben az oktatóanyagban futtasson egy Azure-beli virtuális gép vész-helyreállítási gyakorlatát egy másik régióba Site Recovery használatával.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395600"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>Oktatóanyag: vész-helyreállítási részletezés futtatása Azure-beli virtuális gépekhez

Megtudhatja, hogyan futtathat vész-helyreállítási gyakorlatot egy másik Azure-régióba az Azure-beli virtuális gépek [Azure site Recovery](site-recovery-overview.md)használatával történő replikálásához. Ebben a cikkben:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése
> * A részletezés előtt tekintse meg a virtuális gép beállításait
> * Feladatátvételi teszt futtatása
> * Tisztítás a részletezés után


> [!NOTE]
> Ez az oktatóanyag minimális lépéseket biztosít a vész-helyreállítási gyakorlat futtatásához. Ha teljes infrastruktúra-tesztelést szeretne végezni, ismerkedjen meg az Azure-beli virtuális gépek [hálózatkezelésével](azure-to-azure-about-networking.md), [automatizálásával](azure-to-azure-powershell.md)és [hibaelhárításával](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt engedélyeznie kell a vész-helyreállítást egy vagy több Azure-beli virtuális gépen. Ehhez [végezze el az első oktatóanyagot](azure-to-azure-tutorial-enable-replication.md) ebben a sorozatban.

## <a name="verify-vm-settings"></a>Virtuális gép beállításainak ellenőrzése

1. A tárolóban > **replikált elemek** területen válassza ki a virtuális gépet.

    ![A vész-helyreállítási oldal megnyitásának lehetősége a virtuális gép tulajdonságaiban](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. Az **Áttekintés** lapon győződjön meg arról, hogy a virtuális gép védett és kifogástalan állapotú.
3. A feladatátvételi teszt futtatásakor ki kell választania egy Azure-beli virtuális hálózatot a célként megadott régióban. A feladatátvételt követően létrehozott Azure-beli virtuális gép ebbe a hálózatba kerül. 

    - Ebben az oktatóanyagban egy meglévő hálózatot választunk a feladatátvételi teszt futtatásakor.
    - Javasoljuk, hogy válasszon egy nem éles hálózatot a részletezéshez, hogy az IP-címek és a hálózati összetevők továbbra is elérhetők maradjanak az éles hálózatokban.
   - Előre konfigurálhatja a feladatátvételi teszthez használt hálózati beállításokat is. Az egyes hálózati adapterekhez hozzárendelhető részletes beállítások közé tartozik az alhálózat, a magánhálózati IP-cím, a nyilvános IP-cím, a terheléselosztó és a hálózati biztonsági csoport. Ezt a módszert nem használjuk, de további információért [tekintse át ezt a cikket](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations) .


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása


1. Az **Áttekintés** lapon válassza a **feladatátvételi teszt** lehetőséget.

    
    ![Feladatátvételi teszt gomb a replikált elemhez](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. A **feladatátvételi teszt** területen válasszon egy helyreállítási pontot. A célként megadott régióban található Azure-beli virtuális gép ebből a helyreállítási pontból származó adatok használatával jön létre.
  
   - **Legutóbb feldolgozott**: a site Recovery által feldolgozott legújabb helyreállítási pontot használja. Megjelenik az időbélyeg. A rendszer nem töltött le időt az adatfeldolgozás során, így alacsony helyreállítási időcélkitűzést (RTO) biztosít.
   -  **Utolsó**: a site Recoveryba elküldett összes adat feldolgozása, hogy minden virtuális gép számára hozzon létre egy helyreállítási pontot, mielőtt a művelet feladatátvételt hajt végre. A a legalacsonyabb helyreállítási időkorlátot (RPO) biztosítja, mivel a feladatátvétel indításakor a rendszer az összes adatSite Recovery replikálja.
   - **Legújabb alkalmazás-konzisztens**: Ez a beállítás a virtuális gépeket a legújabb, alkalmazás-konzisztens helyreállítási pontra hajtja végre. Megjelenik az időbélyeg.
   - **Egyéni**: feladatátvétel adott helyreállítási pontra. Az egyéni szolgáltatás csak akkor érhető el, ha egyetlen virtuális gép feladatátvételét hajtja végre, és nem használ helyreállítási tervet.

3. Az **Azure Virtual Network** szolgáltatásban válassza ki azt a célként kijelölt hálózatot, amelyben a feladatátvételt követően létrehozott Azure-beli virtuális gépeket kívánja elhelyezni. Ha lehetséges, válassza ki a nem éles hálózatot, és ne a replikáció engedélyezésekor létrehozott hálózatot.

    ![Feladatátvételi beállítások tesztelése lap](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. A feladatátvétel elindításához kattintson **az OK gombra**.
5. Figyelje a feladatátvételi tesztet az értesítésekben.

    ![Értesítés a ](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![ sikeres előrehaladásról](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. A feladatátvétel befejeződése után a célként megadott régióban létrehozott Azure-beli virtuális gép megjelenik a Azure Portal **Virtual Machines**. Győződjön meg arról, hogy a virtuális gép fut, megfelelően méretezi a hálózatot, és csatlakozik a kiválasztott hálózathoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Az **alapvető** erőforrások lapon válassza a **feladatátvételi teszt törlése** lehetőséget.

    ![Gomb a karbantartási folyamat elindításához](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. A **feladatátvételi teszt karbantartási**  >  **megjegyzései** területen jegyezze fel és mentse a feladatátvételi teszttel kapcsolatos megfigyeléseket. 
3. Válassza a **tesztelés** befejeződött lehetőséget a feladatátvételi teszt során létrehozott virtuális gépek törléséhez.

    ![Lap karbantartási lehetőségekkel](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. Az értesítések karbantartási folyamatának figyelése.

    ![A karbantartási folyamat értesítésének ](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![ karbantartási sikerességéről szóló értesítés](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy vész-helyreállítási részletezést futtatott, amellyel ellenőrizheti, hogy a feladatátvétel a várt módon működik-e. Most kipróbálhatja a teljes feladatátvételt.

> [!div class="nextstepaction"]
> [Éles feladatátvétel futtatása](azure-to-azure-tutorial-failover-failback.md)
