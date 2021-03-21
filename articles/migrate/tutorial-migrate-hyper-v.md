---
title: Hyper-V virtuális gépek áttelepítése az Azure-ba Azure Migrate kiszolgáló áttelepítése
description: Ismerje meg, hogyan telepítheti át a helyszíni Hyper-V virtuális gépeket az Azure-ba Azure Migrate Server Migration használatával
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 0072ce81fc619c39770eba52e24dc5a0c57280a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604576"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>A Hyper-V virtuális gépek migrálása az Azure-ba 

Ez a cikk bemutatja, hogyan telepítheti át a helyszíni Hyper-V virtuális gépeket az Azure-ba a [Azure Migrate: Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszközzel.

Ez az oktatóanyag egy sorozat harmadik része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a gépeket az Azure-ba. 

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Ahol lehet, az oktatóanyagok az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és módot. 

 Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adja hozzá a Azure Migrate: Server áttelepítési eszközt.
> * Fedezze fel az áttelepíteni kívánt virtuális gépeket.
> * Indítsa el a virtuális gépek replikálását.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson egy teljes körű virtuálisgép-áttelepítést.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Előfeltételek


Az oktatóanyag elkezdése előtt:

1. [Tekintse át](hyper-v-migration-architecture.md) a Hyper-V áttelepítési architektúrát.
2. [Tekintse át](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) A Hyper-V gazdagépre vonatkozó követelmények az áttelepítéshez, valamint azon Azure URL-címek, amelyekhez a Hyper-V-gazdagépek és-fürtök hozzáférhetnek a virtuális gépek migrál
3. [Tekintse át](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) az Azure-ba áttelepíteni kívánt Hyper-V virtuális gépekre vonatkozó követelményeket.
4. Javasoljuk, hogy az Azure-ba való Migrálás előtt mérje fel a  [Hyper-V virtuális gépeket](tutorial-assess-hyper-v.md) , de nem kell.
5. Lépjen a már létrehozott projekthez, vagy [hozzon létre egy új projektet](./create-manage-projects.md)
6. Az Azure-fiók engedélyeinek ellenőrzése – az Azure-fióknak rendelkeznie kell a virtuális gép létrehozásához szükséges engedélyekkel, és írnia kell egy Azure-beli felügyelt lemezre.

## <a name="download-and-install-the-provider"></a>A szolgáltató letöltése és telepítése

A Hyper-V virtuális gépek áttelepítéséhez Azure Migrate: a kiszolgáló áttelepítése a szoftvergyártók (Microsoft Azure Site Recovery Provider és a Microsoft Azure Recovery Service Agent) szolgáltatást telepíti a Hyper-V-gazdagépeken vagy fürtcsomópontokon. Vegye figyelembe, hogy a [Azure Migrate berendezés](migrate-appliance.md) nem használatos a Hyper-V áttelepítéséhez.

1. A Azure Migrate projekt > **kiszolgálók** **Azure Migrate: kiszolgáló áttelepítése** területen kattintson a **felderítés** elemre.
2. A **felderítési gépek** a  >  **gépek virtualizáltak?** területen válassza **az igen, a Hyper-V** lehetőséget.
3. A **cél régióban** válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
6. Válassza **a megerősítés lehetőséget, hogy az áttelepítéshez a régió neve legyen**.
7. Kattintson az **erőforrások létrehozása** gombra. Ezzel létrehoz egy Azure Site Recovery tárolót a háttérben.
    - Ha már beállította az áttelepítést Azure Migrate kiszolgáló áttelepítésével, ez a lehetőség nem jelenik meg, mivel az erőforrások korábban lettek beállítva.
    - Erre a gombra kattintva nem módosítható a projekthez tartozó cél régió.
    - Az összes további áttelepítés erre a régióra vonatkozik.
    
8. A **Hyper-v-gazdagépek előkészítése** területen töltse le a Hyper-v replikációs szolgáltatót és a regisztrációs kulcsot tartalmazó fájlt.
    - A regisztrációs kulcs szükséges a Hyper-V-gazdagép Azure Migrate kiszolgáló áttelepítéssel való regisztrálásához.
    - A kulcs a generálásától számított öt napig érvényes.

    ![Szolgáltató és kulcs letöltése](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Másolja a szolgáltató telepítési fájlját és a regisztrációs kulcsot tartalmazó fájlt minden olyan Hyper-V-gazdagépre (vagy fürtcsomópont), amely a replikálni kívánt virtuális gépeket futtatja.
5. Futtassa a szolgáltató telepítési fájlját minden gazdagépen a következő eljárásban leírtak szerint.
6. Miután telepítette a szolgáltatót a gazdagépeken, a **felderítési gépek** területen kattintson a **regisztráció véglegesítése** lehetőségre.

    ![Regisztráció véglegesítése](./media/tutorial-migrate-hyper-v/finalize-registration.png)

A regisztráció véglegesítése után akár 15 percet is igénybe vehet, amíg a felderített virtuális gépek megjelennek Azure Migrate kiszolgáló áttelepítésekor. Ahogy a virtuális gépek felderítése megtörtént, a **felderített kiszolgálók** száma emelkedik.

![Felderített kiszolgálók](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Hyper-V virtuális gépek replikálása

A felderítés befejeződése után megkezdheti a Hyper-V virtuális gépek replikálását az Azure-ba.

> [!NOTE]
> Egyszerre legfeljebb 10 gépet replikálhat. Ha többre van szüksége, replikálja őket egyszerre 10 kötegben.

1. A Azure Migrate projekt > **kiszolgálók**, **Azure Migrate: kiszolgáló áttelepítése** elemre, majd kattintson a **replikálás** gombra.
2. A **replikálás** során > a **forrás beállításait**  >  **a gépek virtualizálják?** területen válassza **az igen, a Hyper-V** lehetőséget. Ezután kattintson a **Tovább gombra: Virtual Machines**.
3. A **Virtuális gépek** területen jelölje ki a replikálni kívánt gépeket.
    - Ha futtatta a virtuális gépek értékelését, alkalmazhatja az eredményekben szereplő, a virtuális gépek méretezésére és a lemeztípusra (prémium/standard) vonatkozó javaslatokat. Ehhez a **Importálja a migrálási beállításokat az Azure Migrate-értékelésből?** területen válassza az **Igen** lehetőséget.
    - Ha nem futtatott értékelést, vagy nem szeretné használni az értékelés beállításait, válassza a **Nem** lehetőséget.
    - Ha az értékelés használatát választotta, válassza ki a virtuálisgép-csoportot és az értékelés nevét.

        ![Értékelés kiválasztása](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. A **Virtuális gépek** között igény szerint keressen a virtuális gépekre, és válassza ki a gépeket, amelyeket migrálni szeretne. Ezután kattintson a **Tovább gombra: cél beállításai**.

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-hyper-v/select-vms.png)

5. A **cél beállításai** területen válassza ki azt a célhelyet, amelyre az áttelepítést, az előfizetést és az Azure-beli virtuális gépeket az áttelepítés után tároló erőforráscsoport.
7. A **replikációs tároló fiók** területen válassza ki azt az Azure Storage-fiókot, amelyben a replikált adattárolók tárolódnak az Azure-ban.
8. **Virtual Network** válassza ki azt az Azure VNet/alhálózatot, amelyhez az Azure-beli virtuális gépek csatlakozni fognak az áttelepítés után.
9. A **rendelkezésre állási beállítások** területen válassza a következőket:
    -  Rendelkezésre állási zóna az áttelepített gép egy adott rendelkezésre állási zónába való rögzítéséhez a régióban. Ezzel a beállítással olyan kiszolgálókat oszthat szét, amelyek több csomópontos alkalmazási szintet alkotnak Availability Zoneson belül. Ha ezt a lehetőséget választja, a számítási lapon meg kell adnia a rendelkezésre állási zónát, amelyet a kiválasztott gépekhez használni szeretne. Ez a beállítás csak akkor érhető el, ha az áttelepítéshez kiválasztott cél régió támogatja a Availability Zones
    -  Rendelkezésre állási csoport, amely az áttelepített gépet egy rendelkezésre állási csoportba helyezi. A beállítás használatához legalább egy rendelkezésre állási csoportnak kell lennie.
    - Nincs szükség infrastruktúra-redundancia beállításra, ha az áttelepített gépekhez nem szükséges a rendelkezésre állási konfigurációk egyike sem.
10. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Tovább** gombra.

    ![Cél beállításai](./media/tutorial-migrate-hyper-v/target-settings.png)

11. A **számításban** tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemezének típusát és rendelkezésre állási konfigurációját (ha az előző lépésben van kiválasztva). A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - Virtuálisgép- **méret**: Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő lista az ajánlott méretet fogja tartalmazni. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **Operációsrendszer-lemez**: a virtuális gép operációsrendszer-(rendszerindító) lemezének megadása. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási csoport**: Ha a virtuális gépnek az áttelepítés után Azure-beli rendelkezésre állási csoportba kell tartoznia, adja meg a készletet. A csoportnak a migrálás során megadott cél-erőforráscsoportban kell lennie.

    ![VIRTUÁLIS gépek számítási beállításai](./media/tutorial-migrate-hyper-v/compute-settings.png)

12. A **lemezek** területen határozza meg az Azure-ba replikálni kívánt virtuális gépek lemezeit. Ezután kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

    ![A képernyőfelvétel a replikálás párbeszédpanel lemezek lapját jeleníti meg.](./media/tutorial-migrate-hyper-v/disks.png)

13. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikálási beállításokat bármikor frissítheti a replikálás megkezdése előtt, a  >  **replikálási gépek** kezelése területen. A replikáció kezdete után a beállítások már nem módosíthatók.

## <a name="provision-for-the-first-time"></a>Kiépítés első alkalommal

Ha ez az első virtuális gép, amelyet a Azure Migrate projektben replikál, Azure Migrate: a kiszolgáló áttelepítése automatikusan kiosztja ezeket az erőforrásokat a projekttel azonos erőforráscsoporthoz.
- **Cache Storage-fiók**: a Hyper-V-gazdagépekre telepített Azure site Recovery szolgáltatói szoftver feltölti a replikáláshoz konfigurált virtuális gépek replikációs adatait a Storage-fiókba (más néven a gyorsítótár-fiókba vagy a log Storage-fiókba) az előfizetésében. A Azure Migrate szolgáltatás ezután átmásolja a feltöltött replikációs adatait a Storage-fiókból a virtuális géphez tartozó replika által felügyelt lemezekre. A gyorsítótár Storage-fiókját meg kell adni a virtuális gép replikálásának konfigurálásakor, és a Azure Migrate-portál automatikusan létrehoz egyet a Azure Migrate projekthez, amikor a projekt első alkalommal konfigurálja a replikálást.

## <a name="track-and-monitor"></a>Nyomon követés és figyelés


- Ha rákattint a **replikálás** indítása a replikálási feladatokhoz lehetőségre. 
- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-ba.

A feladatok állapotát a portál értesítéseiben követheti nyomon.

A replikálási állapot figyeléséhez kattintson a **kiszolgálók replikálásához** **Azure Migrate: kiszolgáló áttelepítése** elemre.
![Replikáció monitorozása](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdése előtt futtasson egy teszt-áttelepítést a virtuális gépek számára, mielőtt az Azure-ba történő teljes áttelepítést futtatná. Azt javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el ezt az egyes gépeken.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni, anélkül, hogy ez hatással lenne a helyszíni gépekre, amelyek továbbra is működőképesek maradnak. 
- A tesztelési áttelepítés szimulálja az áttelepítést egy Azure-beli virtuális gép replikálási adataival történő létrehozásával (általában az Azure-előfizetése nem éles üzemben lévő Azure-VNet való migrálása).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti az áttelepítést, elvégezheti az alkalmazások tesztelését, és bármilyen problémát megtehet a teljes áttelepítés előtt.

Végezzen el egy teszt-áttelepítést a következőképpen:


1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson az **áttelepített kiszolgálók tesztelése** elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Az **áttelepítés tesztelése** lapon válassza ki azt az Azure-beli virtuális hálózatot, amelyben az Azure-beli virtuális gép az áttelepítés után lesz elhelyezve. Javasoljuk, hogy használjon nem éles virtuális hálózatot.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gép migrálása

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti a helyszíni gépeket.

1. A Azure Migrate projekt > **kiszolgálók**  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson a **kiszolgálók replikálása** elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **áttelepítés** során  >  **állítsa le a virtuális gépeket, és végezze el az adatvesztés nélküli tervezett áttelepítést**, válassza az **Igen**  >  **OK** lehetőséget.
    - Alapértelmezés szerint az Azure Migrate leállítja a helyszíni virtuális gépet, majd igény szerint replikációt végez a legutóbbi replikáció óta a virtuális gépen történt módosítások szinkronizálása érdekében. Így elkerülhető az adatvesztés.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gépre > az **áttelepítés leállítása** elemre. Ez a következő műveleteket végzi el:
    - Leállítja a helyszíni gép replikálását.
    - Eltávolítja a gépet a **replikálási kiszolgálók** száma Azure Migrate: kiszolgáló áttelepítése.
    - A virtuális gép replikációs állapotára vonatkozó információk tisztítása.
2. Telepítse az Azure-beli VM [Windows](../virtual-machines/extensions/agent-windows.md) -vagy [Linux](../virtual-machines/extensions/agent-linux.md) -ügynököt az áttelepített gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni ajánlott eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény szerinti felügyelettel](../security-center/security-center-just-in-time.md).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
-  Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>Következő lépések

Vizsgálja meg a [felhőalapú migrációs utat](/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerében.