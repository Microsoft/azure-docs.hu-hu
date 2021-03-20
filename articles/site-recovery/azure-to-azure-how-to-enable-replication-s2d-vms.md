---
title: Közvetlen tárolóhelyek rendszerű Azure-beli virtuális gépek replikálása Azure Site Recovery
description: Megtudhatja, hogyan replikálhat Közvetlen tárolóhelyek-t futtató Azure-beli virtuális gépeket Azure Site Recovery használatával.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: f438fadb73f7e3bd25cd7ab9aef0bc46285e30e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92424822"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Közvetlen tárolóhelyek-t futtató Azure-beli virtuális gépek replikálása egy másik régióba

Ez a cikk azt ismerteti, hogyan engedélyezhető a közvetlen tárolóhelyeket futtató Azure-beli virtuális gépek vész-helyreállítási folyamata.

>[!NOTE]
>A közvetlen tárolóhelyek fürtök esetében csak az összeomlás-konzisztens helyreállítási pontok támogatottak.
>

A [közvetlen tárolóhelyek (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) a szoftveresen definiált tároló, amely lehetővé teszi a [vendég fürtök](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering) létrehozását az Azure-ban.  Microsoft Azure egy IaaS virtuális gépekből álló feladatátvevő fürt. Lehetővé teszi, hogy az üzemeltetett virtuális gépek számítási feladatai feladatátvételt hajtanak végre a vendég fürtökön, így magasabb rendelkezésre állást biztosító SLA-t biztosítanak az alkalmazásokhoz, mint egy Azure virtuális gép Olyan helyzetekben hasznos, amikor egy virtuális gép olyan kritikus alkalmazást üzemeltet, mint például az SQL vagy a kibővített fájlkiszolgáló.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Vész-helyreállítás a közvetlen tárolóhelyek szolgáltatással

Tipikus esetben előfordulhat, hogy a Virtual Machines Guest cluster az Azure-ban nagyobb rugalmasságot biztosít az alkalmazáshoz, például a kibővíthető fájlkiszolgáló számára. Habár ez magasabb rendelkezésre állást biztosíthat az alkalmazás számára, a Site Recovery bármely régióval kapcsolatos meghibásodás esetén el szeretné látni ezeket az alkalmazásokat. Site Recovery replikálja az adott régió adatait egy másik Azure-régióba, és a feladatátvételi esemény során felveszi a fürtöt a vész-helyreállítási régióban.

Az alábbi ábra egy két csomópontos Azure VM feladatátvevő fürtöt mutat be a közvetlen tárolóhelyek használatával.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Két Azure-beli virtuális gép egy Windows feladatátvevő fürtben, és mindegyik virtuális gép két vagy több adatlemezzel rendelkezik.
- A S2D szinkronizálja az adatokat az adatlemezen, és a szinkronizált tárolót tárolóként jeleníti meg.
- A Storage-készlet fürt megosztott kötete (CSV) segítségével a feladatátvevő fürtben található.
- A feladatátvevő fürt az adatmeghajtók CSV-fájlját használja.

**Vész-helyreállítási megfontolások**

1. Ha [Felhőbeli tanúsító](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) állít be a fürthöz, tartsa meg a tanút a vész-helyreállítási régióban.
2. Ha átadja a virtuális gépeket az alhálózatra a DR régióban, amely eltér a forrás régiójától, akkor a fürt IP-címét a feladatátvétel után módosítani kell.  A fürt IP-címének módosításához a Site Recovery [helyreállítási terv parancsfájlt](./site-recovery-runbook-automation.md) kell használnia.</br>
[Parancsfájl](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) a virtuális gépen belüli parancs végrehajtásához az egyéni szkriptek bővítményével 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Site Recovery engedélyezése a S2D-fürthöz:

1. A Recovery Services-tárolón belül kattintson a "+ replikálás" elemre.
1. Válassza ki a fürt összes csomópontját, és tegye őket egy [több virtuális gépre kiterjedő konzisztencia-csoportba](./azure-to-azure-common-questions.md#multi-vm-consistency) .
1. Válassza ki a replikációs házirendet az alkalmazás konzisztenciája lehetőséggel * (csak összeomlási konzisztencia-támogatás érhető el)
1. Replikáció engedélyezése

   ![A replikációs beállítások konfigurálásának helyét bemutató képernyőkép.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Nyissa meg a replikált elemeket, és láthatja a virtuális gép állapotát is.
3. A virtuális gépek védelme is megtörténik, és a több virtuális GÉPRE kiterjedő konzisztencia-csoport részeként is megjelenik.

   ![A virtuális gépeket bemutató képernyőkép, amely egy több virtuális GÉPRE kiterjedő konzisztencia-csoport részét képezi.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv a feladatátvétel során a többrétegű alkalmazások különböző szintjeinek sorrendjét támogatja. Az előkészítés segíti az alkalmazások konzisztenciájának fenntartását. Ha többrétegű webalkalmazáshoz hoz létre helyreállítási tervet, hajtsa végre a [helyreállítási terv létrehozása a site Recovery használatával](site-recovery-create-recovery-plans.md)című témakörben ismertetett lépéseket.

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása a feladatátvételi csoportokhoz

1.  Hozzon létre egy helyreállítási tervet a virtuális gépek hozzáadásával.
2.  A virtuális gépek csoportosításához kattintson a "Testreszabás" elemre. Alapértelmezés szerint az összes virtuális gép az "1. csoport" részét képezi.


### <a name="add-scripts-to-the-recovery-plan"></a>Parancsfájlok hozzáadása a helyreállítási tervhez
Ahhoz, hogy alkalmazásai megfelelően működjenek, előfordulhat, hogy az Azure-beli virtuális gépeken műveleteket kell végrehajtania a feladatátvételt követően vagy feladatátvételi teszt során. Automatizálhat néhány feladatátvétel utáni műveletet. Itt például csatoljuk a terheléselosztó és a fürt IP-címének módosítását.


### <a name="failover-of-the-virtual-machines"></a>A virtuális gépek feladatátvétele 
A virtuális gépek csomópontjait is át kell adni a Site Recovery [helyreállítási terv](./site-recovery-create-recovery-plans.md) használatával 

![storagespacesdirect-védelem](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
1.  A Azure Portal válassza ki a Recovery Services-tárolót.
2.  Válassza ki a létrehozott helyreállítási tervet.
3.  Kattintson a **Feladatátvétel tesztelése** elemre.
4.  A feladatátvételi teszt folyamatának elindításához válassza ki a helyreállítási pontot és az Azure-beli virtuális hálózatot.
5.  Ha a másodlagos környezet működik, végezze el az érvényesítést.
6.  Az érvényesítések befejezése után a feladatátvételi környezet tisztításához válassza a **feladatátvételi teszt** lehetőséget.

További információ: a [feladatátvétel tesztelése az Azure-ban site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1.  A Azure Portal válassza ki a Recovery Services-tárolót.
2.  Válassza ki az SAP-alkalmazásokhoz létrehozott helyreállítási tervet.
3.  Válassza a **Feladatátvétel** lehetőséget.
4.  A feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot.

További információ: [feladatátvétel site Recoveryban](site-recovery-failover.md).
## <a name="next-steps"></a>Következő lépések

[További](./azure-to-azure-tutorial-failover-failback.md) információ a feladat-visszavétel futtatásáról.
