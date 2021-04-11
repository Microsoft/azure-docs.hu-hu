---
title: Fizikai kiszolgálók feladatátvételének és feladat-visszavételének beállítása Site Recovery
description: Ismerje meg, hogyan hajthatja végre a fizikai kiszolgálók feladatátvételét az Azure-ba, és hogyan térhet vissza a helyszíni helyre a vész-helyreállításhoz Azure Site Recovery
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.openlocfilehash: 68427d264b6d2eea1a38e3b8bc5273409221c126
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579915"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Az Azure-ba replikált fizikai kiszolgálók feladatátvétele és feladatátvétele

Ez az oktatóanyag azt ismerteti, hogyan végezhető el az Azure-ba replikált helyszíni fizikai kiszolgálók feladatátvétele [Azure site Recovery](site-recovery-overview.md)használatával. A feladatátvételt követően az Azure-ból a helyszíni helyre való visszatérést követően az elérhetővé válik.

## <a name="before-you-start"></a>Előkészületek

- [Ismerje meg](failover-failback-overview.md) a feladatátvétel folyamatát a vész-helyreállításban.
- Ha több gép feladatátvételét szeretné elkészíteni, [Ismerje meg](recovery-plan-overview.md) , hogyan gyűjtheti össze a gépeket egy helyreállítási tervben.
- A teljes feladatátvétel elvégzése előtt futtasson egy vész- [helyreállítási gyakorlatot](site-recovery-test-failover-to-azure.md) , hogy minden a várt módon működjön.
- A feladatátvételt követően az Azure-beli virtuális gépekhez való csatlakozás előkészítéséhez kövesse az [alábbi utasításokat](site-recovery-failover.md#prepare-to-connect-after-failover) .



## <a name="run-a-failover"></a>Feladatátvétel futtatása

### <a name="verify-server-properties"></a>Kiszolgáló tulajdonságainak ellenőrzése

Ellenőrizze a kiszolgáló tulajdonságait, és győződjön meg arról, hogy az megfelel az Azure-beli virtuális gépekre [vonatkozó Azure-követelményeknek](vmware-physical-azure-support-matrix.md#replicated-machines) .

1. A **védett elemek** területen kattintson a **replikált elemek** elemre, majd válassza ki a gépet.
2. A **replikált elem** ablaktáblán a számítógép adatai, az állapot és a legújabb elérhető helyreállítási pontok összegzése látható. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **számítás és hálózat** területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célként megadott méretet, a [rendelkezésre állási](../virtual-machines/windows/tutorial-availability-sets.md)csoportot és a felügyelt lemez beállításait.
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.
5. A **lemezekben** a számítógép operációs rendszerével és az adatlemezekkel kapcsolatos információk láthatók.

### <a name="fail-over-to-azure"></a>Feladatátvétel az Azure-ba

1. A **Beállítások**  >  **replikált elemek** elemnél kattintson a gép > **feladatátvétel** elemre.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb**: Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállítási időkorlátot (RPO) nyújtja, mert a feladatátvétel után létrehozott Azure-beli virtuális gép rendelkezik a feladatátvétel elindításakor a Site Recoverybe replikált összes adattal.
   - **Legutóbb feldolgozott**: Ez a beállítás feladatátvételt hajt végre a gépen a site Recovery által feldolgozott legutóbbi helyreállítási pontra. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
   - **Legújabb alkalmazás-konzisztens**: Ez a beállítás a gépet a site Recovery által feldolgozott legújabb, alkalmazás-konzisztens helyreállítási pontra hajtja végre.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Válassza a **gép leállítása a feladatátvétel** megkezdése előtt lehetőséget, ha azt szeretné, hogy a site Recovery a feladatátvétel elindítása előtt megpróbálja leállítani a forrásoldali gépet. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamat a **feladatok** lapon követhető.
4. Ha felkészült az Azure-beli virtuális géphez való kapcsolódáshoz, csatlakozzon a feladatátvétel utáni ellenőrzéshez.
5. Az ellenőrzés után **véglegesítse** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt. A feladatátvétel megkezdése előtt a gép replikációja leáll. Ha megszakítja a feladatátvételt, leáll, de a gép nem replikálódik újra.
> Fizikai kiszolgálók esetében a feladatátvétel további feldolgozása nyolc – tíz percet vesz igénybe.

## <a name="automate-actions-during-failover"></a>Műveletek automatizálása a feladatátvétel során

Előfordulhat, hogy automatizálni szeretné a műveleteket a feladatátvétel során. Ehhez a helyreállítási tervekben parancsfájlokat vagy Azure Automation-runbookok használhat.

- [Útmutató](site-recovery-create-recovery-plans.md) helyreállítási tervek létrehozásához és testreszabásához, beleértve a parancsfájlok hozzáadását.
- [Ismerkedjen meg](site-recovery-runbook-automation.md) Azure Automation runbookok helyreállítási tervekkel való hozzáadásával vmivel.

## <a name="configure-settings-after-failover"></a>Beállítások konfigurálása a feladatátvétel után

A feladatátvételt követően [konfigurálnia](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) kell az Azure-beállításokat a replikált Azure-beli virtuális gépekhez való kapcsolódáshoz. Emellett a [belső és a nyilvános](site-recovery-failover.md#set-up-ip-addressing) IP-címzést is be kell állítania.

## <a name="prepare-for-reprotection-and-failback"></a>Felkészülés az ismételt védelemre és a feladat-visszavételre

Az Azure-ba való feladatátvételt követően az Azure-beli virtuális gépeket a helyszíni helyre történő replikálással újravédi. Ezután a replikálásuk után visszatérhet a helyszíni rendszerbe, ha feladatátvételt futtat az Azure-ból a helyszíni helyre.

1. Az Azure-ba replikált fizikai kiszolgálók Site Recovery csak a VMware virtuális gépeken tudnak visszatérni. A feladat-visszavétel érdekében VMware-infrastruktúrára van szükség. A [cikk](vmware-azure-prepare-failback.md) lépéseit követve készítse elő az ismételt védelmet és a feladat-visszavételt, beleértve az Azure-beli folyamat-kiszolgáló beállítását és egy helyszíni fő célkiszolgáló létrehozását, valamint a helyek közötti VPN konfigurálását, vagy a ExpressRoute privát társítását a feladat-visszavétel érdekében.
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
    - Az áttelepített virtuális gépek.
    - Egy másik erőforráscsoporthoz áthelyezett virtuális gép.
    - Egy olyan replika Azure-beli virtuális gép, amelyet töröltek.
    - Egy olyan replika Azure-beli virtuális gép, amely nincs védve (a helyszíni helyre replikálva).
10. [Tekintse át az Ön által használható feladat-visszavételi típusokat – az](concepts-types-of-failback.md) eredeti helyre történő helyreállítást és a másodlagos hely helyreállítását.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Azure-beli virtuális gépek ismételt védetté egy másik helyre

Ez az eljárás azt feltételezi, hogy a helyszíni virtuális gép nem érhető el.

1. A tároló > a **Beállítások**  >  **replikált elemek** területen kattintson a jobb gombbal arra a gépre, amelyre a feladatátvételt > **ismételt védelemmel** ellátott.
2. Az **Ismételt védelem** területen ellenőrizze, hogy be legyen jelölve az **Azure-ról a helyszíni rendszerre** lehetőség.
3. Adja meg a helyszíni fő célkiszolgálót és a folyamatkiszolgálót.
4. Az **Adattároló** területen válassza ki azt a fő cél adattárolót, amelyre a lemezeket a helyszínen helyre szeretné állítani.
       - Akkor használja ezt a beállítást, ha a helyszíni virtuális gép törölve lett vagy nem létezik, és új lemezeket kell létrehoznia.
       - A rendszer figyelmen kívül hagyja ezt a beállítást, ha a lemezek már léteznek, de meg kell adnia egy értéket.
5. Válassza ki a fő célkiszolgáló adatmegőrzési meghajtóját. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
6. Kattintson az **OK** gombra az ismételt védelem megkezdéséhez. A feladatok megkezdik az Azure-beli virtuális gép replikálását a helyszíni helyre. A **Feladatok** lapon követheti nyomon a folyamat állapotát.

> [!NOTE]
> Ha meglévő helyszíni virtuális gépre szeretné helyreállítani az Azure-beli virtuális gépet, csatlakoztassa a helyszíni virtuális gép olvasási/írási hozzáféréssel rendelkező adattárolóját a fő célkiszolgáló ESXi-gazdagépén.


## <a name="fail-back-from-azure"></a>Feladat-visszavétel az Azure-ból

A következőképpen futtassa a feladatátvételt:

1. A **Replikált elemek** lapon kattintson a jobb gombbal a gépre > **Nem tervezett feladatátvétel**.
2. A **Feladatátvétel megerősítése** területen ellenőrizze, hogy az irány az Azure-ból a helyszíni rendszerre mutat.
3. Válassza ki a feladatátvételhez használni kívánt helyreállítási pontot.
    - Javasoljuk, hogy használja a **legújabb** helyreállítási pontot. Az alkalmazás-konzisztens pont a legutóbbi időponthoz kapcsolódik, és adatvesztést okoz.
    - A **legújabb** egy összeomlás-konzisztens helyreállítási pont.
    - A feladatátvétel futtatásakor a Site Recovery leállítja az Azure-beli virtuális gépeket, és elindítja a helyszíni virtuális gépet. Némi állásidővel kell számolni, ezért válasszon megfelelő időpontot.
4. Kattintson a jobb gombbal a gépre, és kattintson a **Véglegesítés** parancsra. Ez elindítja az Azure-beli virtuális gépeket eltávolító feladatot.
5. Győződjön meg arról, hogy Azure-beli virtuális gépek a várt módon álltak le.


## <a name="reprotect-on-premises-machines-to-azure"></a>Helyszíni gépek ismételt védelme az Azure-ban

Az adatoknak most a helyszíni helyen vannak, de nincsenek az Azure-ba replikálva. A következőképpen indíthatja el ismét a replikációt az Azure-ba:

1. A tárolóban > **Beállítások** >**Replikált elemek**, válassza ki azokat a virtuális gépeket, amelyekhez feladatátvételt végzett, és kattintson az **Ismételt védelem** lehetőségre.
2. Válassza ki azt a folyamatkiszolgálót, amellyel a replikált adatokat az Azure-ba küldi, majd kattintson az **OK** gombra.


## <a name="next-steps"></a>Következő lépések

Az újravédelemi feladatok befejeződése után a helyszíni virtuális gép replikálódik az Azure-ba. Igény szerint [egy másik feladatátvételt is futtathat](site-recovery-failover.md) az Azure-ban.
