---
title: A Hyper-V virtuális gépek feladatátvétele az Azure-ból Azure Site Recovery
description: A Hyper-V virtuális gépek feladatátvétele egy helyszíni helyre az Azure-ból Azure Site Recovery használatával.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: 96c3dce17ab78e08b28bb41c0100e51a72a666e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110253"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Feladat-visszavétel futtatása Hyper-V rendszerű virtuális gépekhez

Ez a cikk a Hyper-V virtuális gépek helyszíni helyről az Azure-ba történő feladatátvétele után létrehozott Azure-beli virtuális gépek visszaadását ismerteti [Azure site Recovery](site-recovery-overview.md)használatával.

- A Hyper-V virtuális gépeket az Azure-ból végezheti el egy tervezett feladatátvétel futtatásával az Azure-ból a helyszíni helyre. Ha a feladatátvétel iránya az Azure-ból a helyszíni rendszerbe kerül, a feladat-visszavételnek tekintendő.
- Mivel az Azure egy magasan elérhető környezet, és a virtuális gépek mindig elérhetők, az Azure-beli feladat-visszavétel tervezett tevékenység. Kis állásidőt is megtervezheti, hogy a számítási feladatok újra el tudják indítani a helyszíni rendszert. 
- A tervezett feladat-visszavétel kikapcsolja a virtuális gépeket az Azure-ban, és letölti a legújabb módosításokat. Az adatvesztés nem várható.

## <a name="before-you-start"></a>Előkészületek

1. [Tekintse át az Ön által használható feladat-visszavételi típusokat – az](failover-failback-overview.md#hyper-v-reprotectionfailback) eredeti helyre történő helyreállítást és a másodlagos hely helyreállítását.
2. Győződjön meg arról, hogy az Azure-beli virtuális gépek Storage-fiókot és nem felügyelt lemezeket használnak. Nem támogatottak azok a Hyper-V virtuális gépek feladat-visszavétele, amelyek felügyelt lemezeket használó Azure-gépeken átadott feladatátvételt végeznek.
3. Ellenőrizze, hogy fut-e a helyszíni Hyper-V-gazdagép (vagy a System Center VMM-kiszolgáló, ha a Site Recovery használatával működik), és csatlakozik az Azure-hoz. 
4. Győződjön meg arról, hogy a feladatátvétel és a végrehajtás befejeződött a virtuális gépek számára. Nem kell beállítania a Hyper-V virtuális gépek Azure-ból történő feladat-visszavételéhez szükséges konkrét Site Recovery-összetevőket.
5. Az adatok szinkronizálásának befejezéséhez és a helyszíni virtuális gép elindításához szükséges idő számos tényezőtől függ. Az adatletöltés felgyorsításához beállíthatja, hogy a Microsoft Recovery Services-ügynök több szálat használjon a letöltés integrálással. [További információk](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Feladat-visszavétel az eredeti helyre

Ha az Azure-ban lévő Hyper-V virtuális gépeket az eredeti helyszíni virtuális gépre szeretné felvenni, futtassa az Azure-ból a helyszíni helyre tervezett feladatátvételt az alábbiak szerint:

1. A tárolóban > **replikált elemek** területen válassza ki a virtuális gépet. Kattintson a jobb gombbal a **tervezett feladatátvételt**> virtuális gépre. Ha nem végez helyreállítási tervet, válassza ki a csomag nevét, és kattintson a **feladatátvétel**  >  **tervezett feladatátvétel** elemre.
2. A **tervezett feladatátvétel megerősítése lapon** válassza ki a forrás-és célhelyeket. Jegyezze fel a feladatátvétel irányát. Ha az elsődleges feladatátvétel az elvárt módon működik, és az összes virtuális gép a másodlagos helyen található, akkor ez csak tájékoztató.
3. Az **adatszinkronizálás** területen válasszon egy beállítást:
    - **Adatszinkronizálás a feladatátvétel előtt (csak a különbözeti változások szinkronizálása)**– ezzel a beállítással csökkenthető a virtuális gépek leállása anélkül, hogy le kellene állítani őket.
        - **1. fázis**: pillanatképet készít az Azure-beli virtuális gépről, és átmásolja a helyszíni Hyper-V-gazdagépre. A gép továbbra is fut az Azure-ban.
        - **2. fázis**: leállítja az Azure-beli virtuális gépet, hogy ne történjen új változás. A különbözeti változások végső készlete a helyszíni kiszolgálóra kerül, és a helyszíni virtuális gép elindult.
    - **Adatszinkronizálás csak feladatátvétel közben (teljes letöltés)**– ez a beállítás gyorsabb, mert feltételezhető, hogy a lemez nagy része megváltozott, és nem kíván időt fordítani az ellenőrzőösszegek kiszámítására. Ez a beállítás nem végez ellenőrzőösszeg-számításokat.
        - Elvégzi a lemez letöltését. 
        - Javasoljuk, hogy ezt a lehetőséget akkor használja, ha az Azure-t egy ideig (egy hónapig vagy többet) futtatta, vagy ha a helyszíni virtuális gép törlődik.

4. Csak VMM esetén, ha engedélyezve van az adattitkosítás a felhőben, akkor a **titkosítási kulcsban** válassza ki azt a tanúsítványt, amelyet az adattitkosítás engedélyezésekor adott meg a VMM-kiszolgálón a szolgáltató telepítésekor.
5. Indítsa el a feladatátvételt. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
6. Ha a feladatátvétel előtt szinkronizálja az adatokat, miután a kezdeti adatszinkronizálás befejeződött, és készen áll a virtuális gépek leállítására az Azure-ban, kattintson a **feladatok** > feladat neve > **teljes feladatátvétel** lehetőségre. Ez a következő műveleteket végzi el:
    - Leállítja az Azure-gépet.
    - Átviszi a legújabb módosításokat a helyszíni virtuális gépre.
    - Elindítja a helyszíni virtuális gépet.
7. Most bejelentkezhet a helyszíni virtuálisgép-gépre, és ellenőrizhető, hogy az a várt módon elérhető-e.
8. A virtuális gép véglegesített függő állapotban van. A feladatátvétel elvégzéséhez kattintson a **véglegesít** gombra.
9. A feladat-visszavétel befejezéséhez kattintson a **visszirányú replikálás** gombra a helyszíni virtuális gép újbóli replikálásához az Azure-ba.



## <a name="fail-back-to-an-alternate-location"></a>Feladat-visszavétel másik helyre 

A következő lépésekkel térhet vissza egy másik helyre:

1. Ha új hardvert állít be, telepítse a [Windows támogatott verzióját](hyper-v-azure-support-matrix.md#replicated-vms)és a Hyper-V szerepkört a gépen.
2. Hozzon létre egy azonos nevű virtuális hálózati kapcsolót az eredeti kiszolgálón.
3. A **védett elemek**  >  **védelme csoportban**  >  \<ProtectionGroupName>  ->  \<VirtualMachineName> válassza ki azt a virtuális gépet, amelyet vissza szeretne állítani, majd válassza a **tervezett feladatátvétel** lehetőséget.
4. A **tervezett feladatátvétel megerősítése lapon** válassza **a helyszíni virtuális gép létrehozása, ha nem létezik**.
5. Az **állomásnév** mezőben válassza ki azt az új Hyper-V-gazdagépet, amelyen el szeretné helyezni a virtuális gépet.
6. Az **adatszinkronizálás** során javasoljuk, hogy a feladatátvétel előtt szinkronizálja az adatokat. Ez lecsökkenti a virtuális gépek leállását, mivel az szinkronizálás nélkül leáll. A következő műveleteket végzi el:
    - **1. fázis**: pillanatképet készít az Azure-beli virtuális gépről, és átmásolja a helyszíni Hyper-V-gazdagépre. A gép továbbra is fut az Azure-ban.
    - **2. fázis**: leállítja az Azure-beli virtuális gépet, hogy ne történjen új változás. A változtatások végső készlete a helyszíni kiszolgálóra kerül, és a helyszíni virtuális gép elindult.
    
7. Kattintson a pipa jelre a feladatátvétel (feladat-visszavétel) elindításához.
8. Miután a kezdeti szinkronizálás befejeződik, és készen áll az Azure-beli virtuális gép leállítására, kattintson a **feladatok**  >  \<planned failover job>  >  **teljes feladatátvétel** lehetőségre. Ezzel leállítja az Azure-gépet, átviszi a legújabb módosításokat a helyszíni virtuális gépre, és elindítja azt.
9. Bejelentkezhet a helyszíni virtuális gépre annak ellenőrzéséhez, hogy minden a várt módon működik-e.
10. A feladatátvétel befejezéséhez kattintson a **véglegesítés** gombra. A commit művelettel törli az Azure-beli virtuális gépet és annak lemezeit, és előkészíti a helyszíni virtuális gépet, hogy ismét védelmet biztosítson.
10. A helyi virtuális gép Azure-ba történő replikálásának megkezdéséhez kattintson a **visszirányú replikálás** elemre. A rendszer a virtuális gép Azure-ban való kikapcsolását követően csak a különbözeti változásokat replikálja.

    > [!NOTE]
    > Ha megszakítja a feladat-visszavételi feladatot az adatszinkronizálás során, a helyszíni virtuális gép sérült állapotban lesz. Ennek az az oka, hogy az adatszinkronizálás az Azure VM-lemezek legújabb adatait a helyszíni adatlemezekre másolja, és a szinkronizálás befejezéséig a lemez adatainak nem konzisztens állapotban vannak. Ha a helyszíni virtuális gép elindul az adatszinkronizálás megszakítása után, előfordulhat, hogy a rendszer nem indul el. Ebben az esetben futtassa újra a feladatátvételt az adatszinkronizálás befejezéséhez.


## <a name="next-steps"></a>Következő lépések
Miután a helyszíni virtuális gép az Azure-ba replikálódik, igény szerint [futtathat egy másik feladatátvételt](site-recovery-failover.md) az Azure-ban.
