---
title: VMware virtuális gépek ismételt védetté Azure Site Recovery
description: Ismerje meg, hogyan védhető fel a VMware virtuális gépek az Azure-ba történő feladatátvétel után Azure Site Recoveryával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 6a11e3d0cb41383b44b76975ecbd1c2ae2825015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89441493"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Ismételt védelem – Azure-ból helyszíni rendszerbe

A helyszíni VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba történő [feladatátvétele](site-recovery-failover.md) után az első lépés a helyszíni helyre történő visszaállításának első lépése a feladatátvétel során létrehozott Azure-beli virtuális gépek ismételt védelemmel való ellátása. Ez a cikk azt ismerteti, hogyan teheti ezt meg. 

## <a name="before-you-begin"></a>Előkészületek

1. A [cikk](vmware-azure-prepare-failback.md) lépéseit követve készítse elő az ismételt védelmet és a feladat-visszavételt, beleértve az Azure-beli folyamat-kiszolgáló beállítását és egy helyszíni fő célkiszolgáló létrehozását, valamint a helyek közötti VPN konfigurálását, vagy a ExpressRoute privát társítását a feladat-visszavétel érdekében.
2. Győződjön meg arról, hogy a helyszíni konfigurációs kiszolgáló fut, és csatlakozik az Azure-hoz. Az Azure-ba történő feladatátvétel során előfordulhat, hogy a helyszíni hely nem érhető el, és előfordulhat, hogy a konfigurációs kiszolgáló nem érhető el vagy leáll. A feladat-visszavétel során a virtuális gépnek léteznie kell a konfigurációs kiszolgáló adatbázisában. Ellenkező esetben a feladat-visszavétel sikertelen.
3. Törölje az összes pillanatképet a helyszíni fő célkiszolgálón. Az ismételt védelem nem fog működni, ha vannak Pillanatképek.  A virtuális gépen lévő Pillanatképek automatikusan egyesülnek egy újravédelemi feladatokban.
4. Ha a több virtuális gépre kiterjedő konzisztencia érdekében a replikálási csoportba begyűjtött virtuális gépeket újra védelemmel látja el, győződjön meg arról, hogy mindegyikük azonos operációs rendszerrel rendelkezik (Windows vagy Linux), és győződjön meg arról, hogy a telepített fő célkiszolgáló azonos típusú operációs rendszerrel rendelkezik. A replikációs csoportban lévő összes virtuális gépnek ugyanazt a fő célkiszolgáló-kiszolgálót kell használnia.
5. Nyissa meg a feladat-visszavételhez [szükséges portokat](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) .
6. A feladat-visszavétel előtt ellenőrizze, hogy a vCenter Server csatlakoztatva van-e. Ellenkező esetben a lemezek leválasztása és a virtuális géphez való csatolása meghiúsul.
7. Ha a vCenter-kiszolgáló felügyeli azokat a virtuális gépeket, amelyeken a feladat-visszavételt végzi, győződjön meg arról, hogy rendelkezik a szükséges engedélyekkel. Ha csak olvasási jogosultsággal rendelkező felhasználói vCenter-felderítést végez, és a virtuális gépek védelme, a védelem sikeres és a feladatátvétel működik. Az ismételt védelem során azonban a feladatátvétel meghiúsul, mert az adattárolók nem észlelhetők, és nem szerepelnek az ismételt védelem során. A probléma megoldásához frissítse a vCenter hitelesítő adatait egy [megfelelő fiókkal/engedélyekkel](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery), majd próbálja megismételni a feladatot. 
8. Ha a virtuális gépek létrehozásához sablont használt, győződjön meg arról, hogy minden virtuális gépnek saját UUID-je van a lemezekhez. Ha a helyszíni virtuálisgép-UUID a fő célkiszolgáló UUID-ának összeütközését eredményezi, mert mindkettő ugyanabból a sablonból lett létrehozva, az ismételt védelem sikertelen lesz. Üzembe helyezés egy másik sablonból.
9. Ha egy másik vCenter Server nem tud visszatérni, győződjön meg arról, hogy az új vCenter Server és a fő célkiszolgáló fel van derítve. Általában, ha nem az adattárolók nem érhetők el, vagy nem láthatók az ismételt **védelemben**.
10. Ellenőrizze, hogy a következő helyzetekben nem lehet visszaadni a feladatokat:
    - Ha az ESXi 5,5 Free Edition vagy a vSphere 6 hypervisor Free Edition verziót használja. Frissítsen egy másik verzióra.
    - Ha Windows Server 2008 R2 SP1 fizikai kiszolgálóval rendelkezik.
    - A VMware virtuális gépek nem tudnak visszavenni a Hyper-V-t.
    - Az áttelepített virtuális gépek.
    - Egy másik erőforráscsoporthoz áthelyezett virtuális gép.
    - Egy olyan replika Azure-beli virtuális gép, amelyet töröltek.
    - Egy olyan replika Azure-beli virtuális gép, amely nincs védve (a helyszíni helyre replikálva).
10. [Tekintse át az Ön által használható feladat-visszavételi típusokat – az](concepts-types-of-failback.md) eredeti helyre történő helyreállítást és a másodlagos hely helyreállítását.


## <a name="enable-reprotection"></a>Ismételt védelem engedélyezése

Engedélyezze a replikációt. Az adott virtuális gépek vagy helyreállítási terv ismételt védetté teheti:

- Ha újravédi a helyreállítási tervet, meg kell adnia minden védett gép értékét.
- Ha a virtuális gépek a több virtuális gépre kiterjedő konzisztencia replikációs csoportjához tartoznak, akkor csak helyreállítási terv használatával lehet őket újra védelemmel ellátni. A replikációs csoportba tartozó virtuális gépeknek ugyanazt a fő célkiszolgáló kiszolgálót kell használniuk

>[!NOTE]
>Az Azure-ból a hajdani forrásba az ismételt védelem során eljuttatott adatok mennyisége 0 bájt és az összes védett gép lemezének mérete között lehet, és nem számítható ki.

### <a name="before-you-start"></a>Előkészületek

- Miután egy virtuális gép a feladatátvétel után elindul az Azure-ban, eltarthat egy ideig, amíg az ügynök vissza nem regisztrálja a konfigurációs kiszolgálót (akár 15 percet is igénybe vehet). Ebben az időszakban nem fogja tudni újból védelemmel ellátni, és hibaüzenet jelzi, hogy az ügynök nincs telepítve. Ha ez történik, várjon néhány percet, majd végezze el az ismételt védelem megtörténtét.
- Ha vissza szeretné állítani az Azure virtuális gépet egy meglévő helyszíni virtuális gépre, csatlakoztassa a helyszíni virtuálisgép-adattárolókat olvasási/írási hozzáféréssel a fő célkiszolgáló ESXi-gazdagépén.
- Ha vissza szeretne térni egy másik helyre, például ha a helyszíni virtuális gép nem létezik, válassza ki a fő célkiszolgáló számára konfigurált adatmegőrzési meghajtót és adattárat. Ha a helyszíni helyre végzi a feladatátvételt, a feladat-visszavételi védelmi tervben található VMware virtuális gépek ugyanazt az adattárat használják, mint a fő célkiszolgáló. Ezután létrejön egy új virtuális gép a vCenter-ben.

A következőképpen engedélyezheti az ismételt védelmet:

1. Válassza **a** tár  >  **replikált elemek** lehetőséget. Kattintson a jobb gombbal a feladatátvétel alatt álló virtuális gépre, majd válassza az **ismételt védelem** lehetőséget. Vagy a parancsgombok közül válassza ki a gépet, majd válassza az **ismételt védelem** lehetőséget.
2. Ellenőrizze, hogy be van-e jelölve az **Azure és a** helyszíni védelem iránya.
3. A **fő célkiszolgáló** és a **folyamat-kiszolgáló** területen válassza ki a helyszíni fő célkiszolgáló és a Process Server kiszolgálót.  
4. Az **adattár** mezőben válassza ki azt az adattárolót, amelyre a helyi lemezeket helyre kívánja állítani. Ez a beállítás akkor használható, ha a helyszíni virtuális gép törlődik, és új lemezeket kell létrehoznia. Ezt a beállítást a rendszer figyelmen kívül hagyja, ha a lemezek már léteznek. Továbbra is meg kell adnia egy értéket.
5. Válassza ki az adatmegőrzési meghajtót.
6. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
7. A védelem megkezdéséhez kattintson **az OK gombra** .

    ![Ismételt védelem párbeszédpanel](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. A feladatok megkezdik az Azure-beli virtuális gép replikálását a helyszíni helyre. A **Feladatok** lapon követheti nyomon a folyamat állapotát.
    - Ha az ismételt védelem sikeres, a virtuális gép védett állapotba kerül.
    - A helyszíni virtuális gép ki van kapcsolva az ismételt védelem alatt. Ez elősegíti az adatok konzisztenciáját a replikáció során.
    - Az ismételt védelem befejeződése után ne kapcsolja be a helyszíni virtuális gépet.
   

## <a name="next-steps"></a>Következő lépések

- Ha bármilyen problémába ütközik, tekintse át a [hibaelhárítási cikket](vmware-azure-troubleshoot-failback-reprotect.md).
- Az Azure-beli virtuális gépek védelme után elvégezheti a feladat- [visszavétel futtatását](vmware-azure-failback.md). A feladat-visszavétel leállítja az Azure-beli virtuális gépet, és elindítja a helyszíni virtuális gépet. Némi állásidőt vár az alkalmazáshoz, és ennek megfelelően kiválaszthatja a feladat-visszavételi időt.


