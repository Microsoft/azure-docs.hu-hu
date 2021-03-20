---
title: Feladatátvétel/feladat-visszavétel beállítása másodlagos Hyper-V-helyhez Azure Site Recovery
description: Megtudhatja, hogyan hajthat végre feladatátvételt a Hyper-V virtuális gépeken a másodlagos helyszíni helyre, és hogyan térhet vissza az elsődleges helyhez a vész-helyreállítás Azure Site Recoveryával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74082602"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>A másodlagos helyszíni helyre replikált Hyper-V virtuális gépek feladatátvétele és feladatátvétele

A [Azure site Recovery](site-recovery-overview.md) szolgáltatás felügyeli és koordinálja a helyszíni gépek és az Azure Virtual Machines (VM) replikációját, feladatátvételét és feladat-visszavételét.

Ez a cikk egy System Center Virtual Machine Manager (VMM) felhőben felügyelt Hyper-V virtuális gép feladatátvételét ismerteti egy másodlagos VMM-helyre. A feladatátvétel után visszaadja a feladatokat a helyszíni helyre, amint az elérhetővé válik. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hyper-V virtuális gép feladatátvétele elsődleges VMM-felhőből másodlagos VMM-felhőbe
> * Ismételt védelem a másodlagos helyről az elsődlegesre, és feladat-visszavétel
> * Szükség esetén megkezdheti az elsődlegesről másodlagosra történő replikálást.

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

A feladatátvétel és a feladat-visszavétel három szakaszból áll:

1. **Feladatátvétel másodlagos helyre**: az elsődleges helyről a másodlagosra történő feladatátvételt okozó gépek.
2. Feladat **-visszavétel a másodlagos helyről**: virtuális gépek replikálása másodlagosról elsődlegesre, és egy tervezett feladatátvétel végrehajtása a feladat-visszavétel érdekében.
3. A tervezett feladatátvétel után megkezdheti a replikálást az elsődleges helyről a másodlagosra.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy elvégezte a vész- [helyreállítási részletezést](hyper-v-vmm-test-failover.md) annak ellenőrzéséhez, hogy minden a vártnak megfelelően működik-e.
- A feladat-visszavétel befejezéséhez győződjön meg arról, hogy az elsődleges és a másodlagos VMM-kiszolgáló csatlakozik Site Recoveryhoz.



## <a name="run-a-failover-from-primary-to-secondary"></a>Feladatátvétel futtatása elsődlegesről másodlagosra

A Hyper-V virtuális gépekhez rendszeres vagy tervezett feladatátvételt is futtathat.

- Használjon rendszeres feladatátvételt váratlan kimaradások esetén. A feladatátvétel futtatásakor Site Recovery létrehoz egy virtuális gépet a másodlagos helyen, és felruházza azt. Az adatvesztés a még nem szinkronizált, függőben lévő adataitól függően történhet.
- Tervezett feladatátvételt lehet használni karbantartáshoz vagy a várt leállás során. Ez a beállítás nulla adatvesztést biztosít. Tervezett feladatátvétel indításakor a forrásként szolgáló virtuális gépek leállnak. A nem szinkronizált adatokat szinkronizálja a rendszer, és a feladatátvétel aktiválódik. 
- 
  Ez az eljárás a rendszeres feladatátvétel futtatását ismerteti.


1. A **Beállítások**  >  **replikált elemek** elemnél kattintson a virtuális gépre > **feladatátvételre**.
1. Válassza a **gép leállítása a feladatátvétel** megkezdése előtt lehetőséget, ha azt szeretné, hogy a feladatátvétel elindítása előtt a site Recovery megkísérelje leállítani a forrás virtuális gépek leállítását. A Site Recovery a feladatátvétel elindítása előtt a másodlagos helyre még nem elküldett helyszíni adatokat is megpróbálja szinkronizálni. Vegye figyelembe, hogy a feladatátvétel akkor is folytatódik, ha a Leállítás sikertelen. A feladatátvételi folyamat a **feladatok** lapon követhető.
2. Ekkor látnia kell a virtuális gépet a másodlagos VMM-felhőben.
3. A virtuális gép ellenőrzése után **véglegesítse** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.  


## <a name="reverse-replicate-and-failover"></a>Visszirányú replikálás és feladatátvétel

Indítsa el a replikálást a másodlagos helyről az elsődlegesre, és hajtsa végre a feladat-visszavételt az elsődleges helyre. Miután a virtuális gépek újra futnak az elsődleges helyen, replikálhatja őket a másodlagos helyre.  

 
1. Kattintson a virtuális gépre > kattintson a **visszirányú replikálás** elemre.
2. Miután a feladatok befejeződik, kattintson a virtuális gép >a **feladatátvétel** elemre, ellenőrizze a feladatátvétel irányát (másodlagos VMM felhőből), majd válassza ki a forrás-és a célhelyeket. 
4. Indítsa el a feladatátvételt. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
5. Az elsődleges VMM felhőben győződjön meg arról, hogy a virtuális gép elérhető.
6. Ha újra meg szeretné kezdeni az elsődleges virtuális gép replikálását a másodlagos helyre, kattintson a **visszirányú replikálás** elemre.

## <a name="next-steps"></a>Következő lépések
[Tekintse át a](hyper-v-vmm-disaster-recovery.md) Hyper-V virtuális gépek másodlagos helyre történő replikálásának lépéseit.
