---
title: A VMware virtuális gépek engedélyezése a vész-helyreállításhoz Azure Site Recovery használatával
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a VMware VM-replikáció a vész-helyreállításhoz a Azure Site Recovery szolgáltatás használatával
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 12/07/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 19a98b5786f35839d84e1e969c29e45e2b5e8dea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98573394"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Az Azure-ba történő replikáció engedélyezése VMware virtuális gépek esetében

Ez a cikk azt ismerteti, hogyan engedélyezhető a helyszíni VMware virtuális gépek (VM-EK) replikálása az Azure-ba.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy a rendszer megfelel a következő feltételeknek:

- [A helyszíni forrás környezet beállítása](vmware-azure-set-up-source.md).
- [Állítsa be a célként megadott környezetet az Azure-ban](vmware-azure-set-up-target.md).
- Mielőtt elkezdené, [ellenőrizze a követelményeket és az előfeltételeket](vmware-physical-azure-support-matrix.md) . Fontos megjegyezni a következőket:
  - A replikált gépek [támogatott operációs rendszerei](vmware-physical-azure-support-matrix.md#replicated-machines) .
  - [Tárterület-/lemez-](vmware-physical-azure-support-matrix.md#storage) támogatás.
  - Az [Azure-követelmények](vmware-physical-azure-support-matrix.md#azure-vm-requirements) , amelyeknek a helyszíni számítógépeknek meg kell felelniük.

### <a name="resolve-common-issues"></a>Gyakori problémák megoldása

- Minden lemeznek 4 TB-nál kisebbnek kell lennie.
- Az operációs rendszer lemezének alapszintű lemeznek kell lennie, nem dinamikus lemeznek.
- A 2. generációs UEFI-kompatibilis virtuális gépek esetében az operációsrendszer-családnak Windows rendszernek kell lennie, és a rendszerindító lemeznek a 300 GB-nál kisebbnek kell lennie.

## <a name="before-you-start"></a>Előkészületek

A VMware virtuális gépek replikálásakor tartsa szem előtt ezeket az információkat:

- Az Azure-beli felhasználói fióknak bizonyos [engedélyekkel](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) kell rendelkeznie az új virtuális gép Azure-ba való replikálásának engedélyezéséhez.
- A VMware virtuális gépek 15 percenként észlelhetők. Akár 15 percet is igénybe vehet, hogy a virtuális gépek megjelenjenek a Azure Portal a felderítés után. Új vCenter-kiszolgáló vagy vSphere-gazdagép hozzáadásakor a felderítés 15 vagy több percig is eltarthat.
- A virtuális gépen a portálon való frissítéshez akár 15 percet is igénybe vehet. Például a VMware-eszközök telepítése.
- A VMware virtuális gépek legutóbb felderített idejét a következő helyen tekintheti meg: a vCenter-kiszolgáló/vSphere-gazdagép **konfigurációs kiszolgálók** lapjának **utolsó elérhetősége** mezőjében.
- Ha virtuális gépeket szeretne hozzáadni a replikáláshoz anélkül, hogy meg kellene várni az ütemezett felderítést, jelölje ki a konfigurációs kiszolgálót (de ne kattintson rá), majd válassza a **frissítés** lehetőséget.
- Ha engedélyezi a replikációt, a virtuális gép előkészítésekor a Process Server automatikusan telepíti a Azure Site Recovery mobilitási szolgáltatást a virtuális gépen.

## <a name="enable-replication"></a>A replikáció engedélyezése

A szakasz lépéseinek elvégzése előtt tekintse át a következő információkat:

- A Azure Site Recovery mostantól közvetlenül a felügyelt lemezekre replikálja az összes új replikálást. A Process Server a replikációs naplókat a célként megadott régióban lévő cache Storage-fiókba írja. Ezek a naplók olyan helyreállítási pontok létrehozására szolgálnak a replika által felügyelt lemezeken, amelyek elnevezési konvenciója a következő: `asrseeddisk` .
- A felügyelt lemezekre történő replikáció PowerShell-támogatása az [az. recoveryservices szolgáltatónál modul verziójának 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) kezdődően érhető el.
- A feladatátvétel időpontjában a kiválasztott helyreállítási pont a célként kezelt lemez létrehozásához használatos.
- Azok a virtuális gépek, amelyek korábban a cél Storage-fiókokra való replikálásra vannak konfigurálva, nem érintettek.
- Az új virtuális gépekhez tartozó Storage-fiókokba való replikálás csak reprezentációs állapot-átadási (REST) API-n és PowerShellen keresztül érhető el. A Storage-fiókok replikálásához használja az Azure REST API 2016-08-10-es vagy 2018-01-10-os verzióját.

A replikáció engedélyezéséhez kövesse az alábbi lépéseket:

1. Ugrás a **2. lépés: az alkalmazás**  >  **forrásának** replikálása. Miután az első alkalommal engedélyezte a replikálást, a tárolóban válassza a **+ replikálás** lehetőséget a további virtuális gépek replikálásának engedélyezéséhez.
1. A **forrás** oldalon > **forrás** lapon válassza ki a konfigurációs kiszolgálót.
1. A **gép típusa** beállításnál válassza a **Virtual Machines** vagy a **fizikai gépek** lehetőséget.
1. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet felügyelő vCenter-kiszolgálót, vagy válassza ki magát a gazdagépet. Ez a beállítás nem vonatkozik a fizikai számítógépek replikálására.
1. Válassza ki a folyamat-kiszolgálót. Ha nem hozott létre további folyamat-kiszolgálókat, a konfigurációs kiszolgáló beépített folyamat-kiszolgálója lesz elérhető a legördülő menüben. Az egyes folyamatok kiszolgálóinak állapota ajánlott korlátként és egyéb paraméterekként van megjelölve. Válassza ki az egészséges folyamat kiszolgálóját. Nem lehet kiválasztani egy [kritikus](vmware-physical-azure-monitor-process-server.md#process-server-alerts) Process Servert. A hibák [elhárításához és megoldásához,](vmware-physical-azure-troubleshoot-process-server.md) **illetve** a [kibővíthető folyamat kiszolgálójának](vmware-azure-set-up-process-server-scale.md)beállításához is használható.

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Replikálási forrás ablakának engedélyezése":::

   > [!NOTE]
   > A 9,24-es [verziótól](site-recovery-whats-new.md)kezdődően további riasztások jelennek meg a Process Server állapotára vonatkozó riasztások javítása érdekében. Frissítse a Site Recovery összetevőket a 9,24-es vagy újabb verzióra az összes generált riasztáshoz.

1. A **cél** mezőben válassza ki azt az előfizetést és erőforráscsoportot, amelyben létre szeretné hozni a feladatátvételen átesett virtuális gépeket. Válassza ki azt a telepítési modellt, amelyet az Azure-ban szeretne használni a feladatátvétel alatt álló virtuális gépekhez.
1. Válassza ki azt az Azure-hálózatot és-alhálózatot, amelyhez az Azure virtuális gépek csatlakozni fognak a feladatátvételt követően. A hálózatnak ugyanabban a régióban kell lennie, mint a Site Recovery Service Vault.

   Válassza a **beállítás most a kijelölt gépekhez** lehetőséget, hogy a hálózati beállítást a védelemre kiválasztott összes virtuális gépre alkalmazza. Válassza a **Konfigurálás később** lehetőséget az Azure-hálózat virtuális gépen való kiválasztásához. Ha nincs hálózata, létre kell hoznia egyet. Ha Azure Resource Manager használatával szeretne hálózatot létrehozni, válassza az **új létrehozása** lehetőséget. Válassza ki az alhálózatot, ha van ilyen, majd kattintson **az OK gombra**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Replikálási cél ablakának engedélyezése":::

1. **Virtuális gépek** esetén  >  **válassza a virtuális gépek lehetőséget**, és válassza ki a replikálni kívánt virtuális gépeket. Csak olyan virtuális gépeket választhat, amelyeken engedélyezhető a replikáció. Ez után válassza az **OK** gombot. Ha nem látja vagy nem jelöl ki egy adott virtuális gépet, a probléma megoldásához tekintse meg [a forrásoldali gép nem szerepel a Azure Portal](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) .

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Replikáció engedélyezése – virtuális gépek kiválasztása ablak":::

1. A **Tulajdonságok**  >  **konfigurálásához** válassza ki azt a fiókot, amelyet a Process Server használ a site Recovery mobilitási szolgáltatás automatikus telepítésére a virtuális gépen. Azt is válassza ki, hogy milyen típusú felügyelt lemezt kíván használni a replikáláshoz az adatváltozási minták alapján.
1. Alapértelmezés szerint a rendszer a forrás virtuális gép összes lemezét replikálja. Ha ki szeretné zárni a lemezeket a replikációból, törölje a **Belefoglalás** jelölőnégyzetet minden olyan lemez esetében, amelyet nem szeretne replikálni. Ez után válassza az **OK** gombot. A további tulajdonságokat később is beállíthatja. [További](vmware-azure-exclude-disk.md) információ a lemezek kizárásáról.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Replikálási tulajdonságok konfigurálásának engedélyezése ablak":::

1. A replikációs **Beállítások**  >  **konfigurálásával konfigurálja** a replikációs beállításokat, és ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva. A replikációs házirend beállításait a **Beállítások**  >  **replikációs házirendek**  >  _Házirend neve_  >  **szerkesztési beállítások** lehetőségre módosíthatja. A házirendre alkalmazott módosítások a replikálásra és az új virtuális gépekre is érvényesek.
1. Ha virtuális gépeket szeretne összegyűjteni egy replikációs csoportba, engedélyezze a **több virtuális gépre kiterjedő konzisztenciát**. Adja meg a csoport nevét, majd kattintson **az OK gombra**.

   > [!NOTE]
   > - A replikációs csoportban lévő virtuális gépek együtt replikálódnak, és megosztott összeomlás-konzisztens és alkalmazás-konzisztens helyreállítási pontokat biztosítanak a feladatátvétel során.
   > - Összegyűjtheti a virtuális gépeket és a fizikai kiszolgálókat, hogy azok tükrözze a számítási feladatokat. A több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a munkaterhelés teljesítményére. Ezt csak akkor hajtsa végre, ha a virtuális gépek ugyanazt a számítási feladatot futtatják, és konzisztensnek kell lenniük.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Replikálási ablak engedélyezése":::

1. Válassza a **replikáció engedélyezése** lehetőséget. A **védelem engedélyezése** feladat előrehaladását a beállítások   >  **feladatok**  >  **site Recovery feladatok** menüpontban követheti nyomon. A **védelem véglegesítése** feladatok futtatása után a virtuális gép készen áll a feladatátvételre.

## <a name="monitor-initial-replication"></a>Kezdeti replikáció figyelése

A védett elem replikációjának engedélyezése után Azure Site Recovery kezdeményezi a forrás-és a célként megadott régió adatainak replikálását (a szinkronizálás szinonimája). Ezen időszak alatt a rendszer létrehozza a forrásként szolgáló lemezek replikáját. Csak az eredeti lemezek másolásának befejezése után a rendszer átmásolja a különbözeti változásokat a célként megadott régióba. Az eredeti lemezek másolásához szükséges idő több paramétertől függ, például:

- a forrásoldali gépi lemezek mérete
- az adatok Azure-ba történő átviteléhez rendelkezésre álló sávszélesség (a Deployment Planner kihasználható a szükséges optimális sávszélesség azonosításához)
- feldolgozza a kiszolgáló erőforrásait, például a memóriát, a szabad lemezterületet, a gyorsítótár számára elérhető CPU-t, & feldolgozza a védett elemektől kapott adatokat (ellenőrizze, hogy a Process Server [kifogástalan](vmware-physical-azure-monitor-process-server.md#monitor-proactively)

A kezdeti replikálás előrehaladásának nyomon követéséhez navigáljon a Recovery Services-tárolóhoz Azure Portal-> replikált elemek – > figyelő "status" oszlop értéke replikált elem. Az állapot a kezdeti replikálás százalékos befejezését jeleníti meg. Az állapotra való Rámutatás után az "összes átvitt adat" elérhető lenne. Ha az állapot gombra kattint, megnyílik egy környezetfüggő oldal, amely a következő paramétereket jeleníti meg:

- Utolsó frissítés: – azt jelzi, hogy a szolgáltatás Mikor frissítette a teljes gép replikációs információit.
- Befejezett százalék – a virtuális gép kezdeti replikációjának százalékos arányát jelzi.
- Összes továbbított adatátvitel – a virtuális gépről az Azure-ba továbbított adatok mennyisége

    :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-state.png" alt-text="replikálási állapot" lightbox="media/vmware-azure-enable-replication/initial-replication-state.png":::

- Szinkronizálási folyamat (a részletek a lemez szintjén való nyomon követése)
    - Replikáció állapota
      - Ha a replikáció még nem indul el, a rendszer az állapotot "a várólistában" frissíti. A kezdeti replikálás során egyszerre csak 3 lemez replikálódik. Ezt a mechanizmust követve elkerülhető a folyamat-kiszolgáló szabályozása.
      - A replikáció elindítása után a rendszer a "folyamatban" állapotot frissíti.
      - A kezdeti replikáció befejezését követően az állapot "Complete" (Befejezés) jelöléssel van megjelölve.        
   - Site Recovery beolvassa az eredeti lemezt, átviszi az adatokat az Azure-ba, és rögzíti a folyamatot a lemez szintjén. Vegye figyelembe, hogy Site Recovery kihagyja a lemez nem foglalt méretének replikálását, és hozzáadja azt a befejezett értékekhez. Így előfordulhat, hogy az összes lemezen továbbított adatmennyiség összege nem jelenik meg a virtuális gép szintjén az "összes átvitt adatforgalom" értékkel.
   - Ha a lemezre kattint a buborékra kattintva, megtudhatja, hogy mikor váltott ki a lemezre a replikálás (a szinkronizálás szinonimája), az Azure-ba továbbított adatokat az elmúlt 15 percben, amelyet a legutolsó frissített időbélyegző követ. Ez az időbélyegző azt jelzi, hogy az Azure-szolgáltatás mikor fogadta el az adatokat a forrásoldali gépről a :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-info-balloon.png" alt-text="kezdeti replikáció-info-Balloon-details" lightbox="media/vmware-azure-enable-replication/initial-replication-info-balloon.png":::
   - Az egyes lemezek állapota megjelenik
      - Ha a replikáció a vártnál lassabban működik, a lemez állapota figyelmeztetésre vált.
      - Ha a replikáció nem halad előre, a lemez állapota kritikusra vált.

Ha az állapot kritikus/figyelmeztetési állapotban van, győződjön meg arról, hogy a gép és a [folyamat](vmware-physical-azure-monitor-process-server.md) replikálása kifogástalan állapotú. 

Amint a replikációs feladatok engedélyezése befejeződött, a replikálási folyamat 0% lesz, és az összes átvitt adatátvitel NA lenne. Ha a gombra kattint, az egyes azonosított lemezek esetében a "NA" érték lesz. Ez azt jelzi, hogy a replikáció még nem indul el, és Azure Site Recovery még nem kapja meg a legújabb statisztikát. A folyamat 30 percenként frissül.

> [!NOTE]
> Győződjön meg arról, hogy a konfigurációs kiszolgálókat, a kibővített folyamat-kiszolgálókat és a mobilitási ügynököket a 9,36-es vagy újabb verzióra frissíti, így biztosítva a pontos előrehaladást és a Site Recovery szolgáltatásoknak


## <a name="view-and-manage-vm-properties"></a>A virtuális gépek tulajdonságainak megtekintése és kezelése

Ezután ellenőrizze a forrás virtuális gép tulajdonságait. Ne feledje, hogy az Azure-beli virtuális gép nevének meg kell felelnie az Azure-beli [virtuális gépek követelményeinek](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Lépjen a **Beállítások**  >  **replikált elemek elemre**, majd válassza ki a virtuális gépet. Az **Essentials** oldal a virtuális gép beállításairól és állapotáról jelenít meg információkat.
1. A **Tulajdonságok** résznél tekintheti meg a virtuális gép replikációs és feladatátvételi adatait.
1. A **számítás és hálózat**  >  **számítási tulajdonságok** területén több virtuálisgép-tulajdonságot is módosíthat.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Számítási és hálózati Tulajdonságok ablak":::

   - **Azure-beli virtuális gép neve**: ha szükséges, módosítsa a nevet az Azure-követelmények teljesítéséhez.
   - **Cél virtuálisgép-méret vagy virtuálisgép-típus**: az alapértelmezett virtuálisgép-méret a lemezes darabszámot, a hálózati adapterek darabszámát, a CPU-mag darabszámát, a memóriát és az elérhető virtuálisgép-szerepkört tartalmazó paraméterek alapján lesz kiválasztva a cél Azure-régióban. Azure Site Recovery kiválasztja az első elérhető virtuálisgép-méretet, amely megfelel az összes feltételnek. A feladatátvétel előtt bármikor kiválaszthat egy másik virtuálisgép-méretet az igényei szerint. A virtuális gép lemezének mérete a forrás lemez méretétől is függ, és csak a feladatátvétel után módosítható. További információk a lemez méretéről és a IOPS díjszabásáról a virtuálisgép [-lemezek méretezhetőségi és teljesítménybeli céljaival](../virtual-machines/disks-scalability-targets.md)kapcsolatban.
   - **Erőforráscsoport**: kiválaszthat egy [erőforráscsoportot](../azure-resource-manager/management/overview.md#resource-groups), amelyből a virtuális gép a feladatátvétel utáni részévé válik. Ez a beállítás a feladatátvétel előtt bármikor módosítható. Ha a feladatátvételt követően áttelepíti a virtuális gépet egy másik erőforráscsoporthoz, az adott virtuális gép védelmi beállításai is megszakadnak.
   - **Rendelkezésre állási csoport**: kiválaszthatja a [rendelkezésre állási készletet](../virtual-machines/windows/tutorial-availability-sets.md) , ha a virtuális gépnek egy feladatátvétel utáni résznek kell lennie. A rendelkezésre állási csoport kiválasztásakor tartsa szem előtt a következő információkat:
     - Csak a megadott erőforráscsoporthoz tartozó rendelkezésre állási csoportok szerepelnek a felsorolásban.
     - A különböző virtuális hálózatokon lévő virtuális gépek nem lehetnek ugyanannak a rendelkezésre állási csoportnak a részei.
     - Csak azonos méretű virtuális gépek lehetnek egy rendelkezésre állási csoport részei.

1. Az Azure-beli virtuális géphez hozzárendelt célként megadott hálózattal, alhálózattal és IP-címmel kapcsolatos információkat is hozzáadhat.
1. A **lemezeken** a replikálni kívánt virtuális gépen található operációs rendszer és adatlemezek láthatók.

### <a name="configure-networks-and-ip-addresses"></a>Hálózatok és IP-címek konfigurálása

Megadhatja a cél IP-címét:

- Ha nem ad meg címet, a feladatátvételen átadott virtuális gép DHCP-t használ.
- Ha olyan címeket állít be, amely nem érhető el a feladatátvétel során, a feladatátvétel nem működik.
- Ha a cím elérhető a feladatátvételi teszt hálózaton, ugyanazt a cél IP-címet használhatja a feladatátvételi teszthez.

A hálózati adapterek számát a cél virtuális géphez megadott méret határozza meg, a következőképpen:

- Ha a forrás virtuális gépen lévő hálózati adapterek száma kisebb vagy egyenlő, mint a célként megadott virtuális gép méretéhez engedélyezett adapterek száma, a célként megadott számú adapter megegyezik a forrással.
- Ha a forrás virtuális géphez tartozó adapterek száma meghaladja a cél virtuális gép méretéhez engedélyezett számot, a rendszer a célként megadott maximális méretet használja. Ha például a forrás virtuális gép két hálózati adapterrel rendelkezik, és a cél virtuális gép mérete négyet támogat, a célként megadott virtuális gép két adapterrel rendelkezik. Ha a forrásoldali virtuális gép két adapterrel rendelkezik, de a célként megadott méret csak egyet támogat, a célként megadott virtuális gépnek csak egy adaptere van.
- Ha a virtuális gépnek több hálózati adaptere van, akkor mind ugyanahhoz a hálózathoz csatlakoznak. Emellett a listában látható első adapter lesz az alapértelmezett hálózati adapter az Azure-beli virtuális gépen.

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A Microsoft frissítési garanciával rendelkező ügyfelei az Azure-ba migrált Windows Server rendszerű számítógépek licencelési költségeinek mentését Azure Hybrid Benefit használhatják. Az előny az Azure vész-helyreállításra is vonatkozik. Ha Ön jogosult, hozzárendelheti az előnyt ahhoz a virtuális géphez, amelyet Site Recovery hoz létre, ha van feladatátvétel.

1. Nyissa meg a replikált virtuális gép **számítógép-és hálózati tulajdonságait** .
1. Válasz, ha a rendszer megkérdezi, hogy van-e olyan Windows Server-licence, amely jogosult a Azure Hybrid Benefitre.
1. Győződjön meg arról, hogy jogosult Windows Server-licenccel rendelkezik frissítési garanciával, amely a feladatátvétel során létrehozandó virtuális gépre való juttatás alkalmazására használható.
1. Mentse a replikált virtuális gép beállításait.

[További](https://azure.microsoft.com/pricing/hybrid-benefit/) információ a Azure Hybrid Benefitról.

## <a name="next-steps"></a>Következő lépések

Miután a virtuális gép elérte a védett állapotot, próbálja meg a [feladatátvételt](site-recovery-failover.md) , és győződjön meg arról, hogy az alkalmazás megjelenik az Azure-ban.

- [További](site-recovery-manage-registration-and-protection.md) információ a regisztrálási és védelmi beállítások törléséről a replikáció letiltásához.
- [További](vmware-azure-disaster-recovery-powershell.md) információ a virtuális gépek replikálásának automatizálásáról a PowerShell használatával.
