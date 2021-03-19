---
title: Azure-beli virtuális gépek újravédése Azure-beli VMware-megoldáshoz Azure Site Recovery
description: Ismerje meg, hogyan védhető fel újra az Azure VMware-megoldás virtuális gépei az Azure-ba történő feladatátvétel után Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 80ff2f3f3d5fdcf61770889dcdaaf075941b90ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91814572"
---
# <a name="reprotect-from-azure-to-azure-vmware-solution-private-cloud"></a>Ismételt védelem az Azure-ból az Azure VMware-megoldás saját felhőbe

Az Azure VMware-megoldás virtuális gépei Azure-ba történő [feladatátvétele](avs-tutorial-failover.md) után az Azure VMware-megoldás saját felhőbe való visszaállításának első lépéseként újra kell védetté tenni a feladatátvétel során létrehozott Azure-beli virtuális gépeket. Ez a cikk azt ismerteti, hogyan teheti ezt meg. 

## <a name="before-you-begin"></a>Előkészületek

1. A [cikk](vmware-azure-prepare-failback.md) lépéseit követve készítse elő a helyreállítást és a feladat-visszavételt, beleértve az Azure-beli folyamat-kiszolgáló beállítását, valamint egy Azure VMware-megoldás saját Felhőbeli fő célkiszolgáló konfigurálását, és konfigurálja a helyek közötti VPN-t vagy a ExpressRoute a feladat-visszavételhez.
2. Győződjön meg arról, hogy az Azure VMware-megoldás saját Felhőbeli konfigurációs kiszolgálója fut, és csatlakozik az Azure-hoz. A feladat-visszavétel során a virtuális gépnek léteznie kell a konfigurációs kiszolgáló adatbázisában. Ellenkező esetben a feladat-visszavétel sikertelen.
3. Törölje az Azure VMware Solution Private Cloud Master célkiszolgálón található összes pillanatképet. Az ismételt védelem nem fog működni, ha vannak Pillanatképek.  A virtuális gépen lévő Pillanatképek automatikusan egyesülnek egy újravédelemi feladatokban.
4. Ha a több virtuális gépre kiterjedő konzisztencia érdekében a replikálási csoportba begyűjtött virtuális gépeket újra védelemmel látja el, győződjön meg arról, hogy mindegyikük azonos operációs rendszerrel rendelkezik (Windows vagy Linux), és győződjön meg arról, hogy a telepített fő célkiszolgáló azonos típusú operációs rendszerrel rendelkezik. A replikációs csoportban lévő összes virtuális gépnek ugyanazt a fő célkiszolgáló-kiszolgálót kell használnia.
5. Nyissa meg a feladat-visszavételhez [szükséges portokat](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) .
6. A feladat-visszavétel előtt ellenőrizze, hogy a vCenter Server csatlakoztatva van-e. Ellenkező esetben a lemezek leválasztása és a virtuális géphez való csatolása meghiúsul.
7. Ha a vCenter-kiszolgáló felügyeli azokat a virtuális gépeket, amelyeken a feladat-visszavételt végzi, győződjön meg arról, hogy rendelkezik a szükséges engedélyekkel. Ha csak olvasási jogosultsággal rendelkező felhasználói vCenter-felderítést végez, és a virtuális gépek védelme, a védelem sikeres és a feladatátvétel működik. Az ismételt védelem során azonban a feladatátvétel meghiúsul, mert az adattárolók nem észlelhetők, és nem szerepelnek az ismételt védelem során. A probléma megoldásához frissítse a vCenter hitelesítő adatait egy [megfelelő fiókkal/engedélyekkel](avs-tutorial-prepare-avs.md#prepare-an-account-for-automatic-discovery), majd próbálja megismételni a feladatot. 
8. Ha a virtuális gépek létrehozásához sablont használt, győződjön meg arról, hogy minden virtuális gépnek saját UUID-je van a lemezekhez. Ha az Azure VMware-megoldás virtuálisgép-UUID-je a fő célkiszolgáló UUID-ának összeütközését eredményezi, mert mindkettő ugyanabból a sablonból lett létrehozva, az ismételt védelem sikertelen lesz. Üzembe helyezés egy másik sablonból.
9. Ha egy másik vCenter Server nem tud visszatérni, győződjön meg arról, hogy az új vCenter Server és a fő célkiszolgáló fel van derítve. Általában, ha nem az adattárolók nem érhetők el, vagy nem láthatók az ismételt **védelemben**.
10. Ellenőrizze, hogy a következő helyzetekben nem lehet visszaadni a feladatokat:
    - Ha az ESXi 5,5 Free Edition vagy a vSphere 6 hypervisor Free Edition verziót használja. Frissítsen egy másik verzióra.
    - Ha Windows Server 2008 R2 SP1 fizikai kiszolgálóval rendelkezik.
    - A VMware virtuális gépek nem tudnak visszavenni a Hyper-V-t.
    - Az áttelepített virtuális gépek.
    - Egy másik erőforráscsoporthoz áthelyezett virtuális gép.
    - Egy olyan replika Azure-beli virtuális gép, amelyet töröltek.
    - Olyan replika Azure-beli virtuális gép, amely nem védett.
10. [Tekintse át az Ön által használható feladat-visszavételi típusokat – az](concepts-types-of-failback.md) eredeti helyre történő helyreállítást és a másodlagos hely helyreállítását.


## <a name="enable-reprotection"></a>Ismételt védelem engedélyezése

Engedélyezze a replikációt. Az adott virtuális gépek vagy helyreállítási terv ismételt védetté teheti:

- Ha újravédi a helyreállítási tervet, meg kell adnia minden védett gép értékét.
- Ha a virtuális gépek a több virtuális gépre kiterjedő konzisztencia replikációs csoportjához tartoznak, akkor csak helyreállítási terv használatával lehet őket újra védelemmel ellátni. A replikációs csoportba tartozó virtuális gépeknek ugyanazt a fő célkiszolgáló kiszolgálót kell használniuk

>[!NOTE]
>Az Azure-ból a hajdani forrásba az ismételt védelem során eljuttatott adatok mennyisége 0 bájt és az összes védett gép lemezének mérete között lehet, és nem számítható ki.

### <a name="before-you-start"></a>Előkészületek

- Miután egy virtuális gép a feladatátvétel után elindul az Azure-ban, eltarthat egy ideig, amíg az ügynök vissza nem regisztrálja a konfigurációs kiszolgálót (akár 15 percet is igénybe vehet). Ebben az időszakban nem fogja tudni újból védelemmel ellátni, és hibaüzenet jelzi, hogy az ügynök nincs telepítve. Ha ez történik, várjon néhány percet, majd végezze el az ismételt védelem megtörténtét.
- Ha az Azure-beli virtuális gépet egy meglévő Azure VMware Megoldásbeli virtuális gépre szeretné visszaadni, csatlakoztassa a virtuális gép adattárolóit olvasási/írási hozzáféréssel a fő célkiszolgáló ESXi-gazdagépén.
- Ha vissza szeretne térni egy másik helyre, például ha az Azure VMware-megoldás virtuális gépe nem létezik, válassza ki a fő célkiszolgáló számára konfigurált adatmegőrzési meghajtót és adattárat. Ha visszaadja az Azure VMware-megoldás saját felhőjét, a feladat-visszavételi védelmi tervben szereplő virtuális gépek ugyanazt az adattárt használják, mint a fő célkiszolgáló. Ezután létrejön egy új virtuális gép a vCenter-ben.

A következőképpen engedélyezheti az ismételt védelmet:

1. Válassza **a** tár  >  **replikált elemek** lehetőséget. Kattintson a jobb gombbal a feladatátvétel alatt álló virtuális gépre, majd válassza az **ismételt védelem** lehetőséget. Vagy a parancsgombok közül válassza ki a gépet, majd válassza az **ismételt védelem** lehetőséget.
2. Ellenőrizze, hogy be van-e jelölve az **Azure és a** helyszíni védelem iránya.
3. A **fő célkiszolgáló** és a **folyamat-kiszolgáló** területen válassza ki a helyszíni fő célkiszolgáló és a Process Server kiszolgálót.  
4. Az **adattár** mezőben válassza ki azt az adattárolót, amelynek a lemezeit helyre szeretné állítani az Azure VMware-megoldásban. Ezt a beállítást akkor használja a rendszer, ha az Azure VMware-megoldás virtuális gépe törölve van, és új lemezeket kell létrehoznia. Ezt a beállítást a rendszer figyelmen kívül hagyja, ha a lemezek már léteznek. Továbbra is meg kell adnia egy értéket.
5. Válassza ki az adatmegőrzési meghajtót.
6. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
7. A védelem megkezdéséhez kattintson **az OK gombra** .

    ![Ismételt védelem párbeszédpanel](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. A feladatok megkezdik az Azure-beli virtuális gép replikálását az Azure VMware megoldás saját felhőbe. A **Feladatok** lapon követheti nyomon a folyamat állapotát.
    - Ha az ismételt védelem sikeres, a virtuális gép védett állapotba kerül.
    - Az Azure VMware megoldás virtuális gépe ki van kapcsolva az ismételt védelem során. Ez elősegíti az adatok konzisztenciáját a replikáció során.
    - Az ismételt védelem befejeződése után ne kapcsolja be az Azure VMware megoldás virtuális gépet.
   

## <a name="next-steps"></a>Következő lépések

- Ha bármilyen problémába ütközik, tekintse át a [hibaelhárítási cikket](vmware-azure-troubleshoot-failback-reprotect.md).
- Az Azure-beli virtuális gépek védelme után elvégezheti a feladat- [visszavétel futtatását](avs-tutorial-failback.md). A feladat-visszavétel leállítja az Azure-beli virtuális gépet, és elindítja az Azure VMware megoldás virtuális gépet. Némi állásidőt vár az alkalmazáshoz, és ennek megfelelően kiválaszthatja a feladat-visszavételi időt.


