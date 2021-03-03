---
title: VMware virtuális gépek áttelepítése ügynök nélkül Azure Migrate kiszolgáló áttelepítése
description: Ismerje meg, hogyan futtathatja a VMware virtuális gépek ügynök nélküli áttelepítését Azure Migrateokkal.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: 17d9d3bf787b67716fb2270cd055e30a4fefbe0f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702198"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware virtuális gépek migrálása az Azure-ba (ügynök nélkül)

Ez a cikk bemutatja, hogyan telepítheti át a helyszíni VMware virtuális gépeket az Azure-ba a [Azure Migrate: Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszközzel, ügynök nélküli áttelepítéssel. A VMware virtuális gépeket ügynök-alapú áttelepítéssel is áttelepítheti. [Hasonlítsa össze](server-migrate-overview.md#compare-migration-methods) a metódusokat.

Ez az oktatóanyag egy sorozat harmadik része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a VMware virtuális gépeket az Azure-ba. 

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Ahol lehet, az oktatóanyagok az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és módot. 


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adja hozzá az Azure Migration: Server áttelepítési eszközét.
> * Fedezze fel az áttelepíteni kívánt virtuális gépeket.
> * Indítsa el a virtuális gépek replikálását.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson egy teljes körű virtuálisgép-áttelepítést.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Fejezze be az első oktatóanyagot](./tutorial-discover-vmware.md) az Azure és a VMware áttelepítésre való előkészítéséhez.
2. Javasoljuk, hogy fejezze be a második oktatóanyagot a [VMWare virtuális gépek felméréséhez](./tutorial-assess-vmware-azure-vm.md) , mielőtt áttelepíti őket az Azure-ba, de nem kell. 
3. Lépjen a már létrehozott projekthez, vagy [hozzon létre egy új projektet](./create-manage-projects.md)
4. Az Azure-fiók engedélyeinek ellenőrzése – az Azure-fióknak rendelkeznie kell a virtuális gép létrehozásához szükséges engedélyekkel, és írnia kell egy Azure-beli felügyelt lemezre.

## <a name="set-up-the-azure-migrate-appliance"></a>A Azure Migrate berendezés beállítása

Azure Migrate a kiszolgáló áttelepítése egy egyszerű VMware VM-készüléket futtat, amely a VMware virtuális gépek felderítésére, értékelésére és ügynök nélküli áttelepítésére szolgál. Ha követi az [értékelési oktatóanyagot](./tutorial-assess-vmware-azure-vm.md), már beállította a készüléket. Ha nem, állítsa be most, a következő módszerek egyikével:

- **Petesejtek sablon**: [beállítás](how-to-set-up-appliance-vmware.md) egy VMware virtuális gépen egy letöltött petesejtek sablonnal.
- **Parancsfájl**: [beállítás](deploy-appliance-script.md) egy VMware virtuális gépen vagy fizikai gépen PowerShell-telepítő parancsfájl használatával. Ezt a módszert akkor kell használni, ha nem állít be egy virtuális gépet egy PETESEJT-sablon használatával, vagy ha Azure Government.

A berendezés létrehozása után győződjön meg róla, hogy tud csatlakozni Azure Migrate: kiszolgáló-értékeléshez, először konfigurálja, majd regisztrálja azt a Azure Migrate projektben.

## <a name="replicate-vms"></a>Virtuális gépek replikálása

A berendezés beállítása és a felderítés befejezése után megkezdheti a VMware virtuális gépek replikálását az Azure-ba. 

- Egyszerre akár 500 replikálást is futtathat.
- A portálon legfeljebb 10 virtuális gépet választhat az áttelepítés után. Ha több gépet szeretne áttelepíteni, adja hozzá őket a 10 kötegben lévő csoportokhoz.

A replikációt a következők szerint engedélyezheti:

1. A Azure Migrate projekt > **kiszolgálók**, **Azure Migrate: kiszolgáló áttelepítése** elemre, majd kattintson a **replikálás** gombra.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware/select-replicate.png)

2. A **Replikálás**, > **Forrásbeállítások** > **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere szolgáltatással** lehetőséget.
3. A **Helyszíni berendezés** területen válassza ki a beállított Azure Migrate-berendezés nevét, majd kattintson az **OK** gombra. 

    ![Forrásbeállítások](./media/tutorial-migrate-vmware/source-settings.png)

4. A **Virtuális gépek** területen jelölje ki a replikálni kívánt gépeket. A virtuálisgép-méretezés és a lemez típusának értékeléséhez, ha futtatta az egyiket, az **áttelepítési beállítások importálása egy Azure Migrate értékelésből?** területen válassza az **Igen** lehetőséget, és válassza ki a virtuálisgép-csoportot és az értékelés nevét. Ha nem használja az értékelési beállításokat, válassza a **nem** lehetőséget.
   
    ![Értékelés kiválasztása](./media/tutorial-migrate-vmware/select-assessment.png)

5. A virtuális gépek területen válassza ki az áttelepíteni kívánt **virtuális gépeket**. Ezután kattintson a **Tovább: cél beállítások** elemre.

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-vmware/select-vms.png)

6. A **célként megadott beállítások** területen válassza ki az előfizetést és a célként megadott régiót. Itt adhatja meg azt az erőforráscsoportot, amelyben az Azure-beli virtuális gépek a Migrálás után találhatók.
7. A **Virtual Network** területen válassza ki azt az Azure VNet/alhálózatot, amelyet az Azure-beli virtuális gépek a Migrálás után csatlakoznak.
8. A **rendelkezésre állási beállítások** területen válassza a következőket:
    -  Rendelkezésre állási zóna az áttelepített gép egy adott rendelkezésre állási zónába való rögzítéséhez a régióban. Ezzel a beállítással olyan kiszolgálókat oszthat szét, amelyek több csomópontos alkalmazási szintet alkotnak Availability Zoneson belül. Ha ezt a lehetőséget választja, a számítási lapon meg kell adnia a rendelkezésre állási zónát, amelyet a kiválasztott gépekhez használni szeretne. Ez a beállítás csak akkor érhető el, ha az áttelepítéshez kiválasztott cél régió támogatja a Availability Zones
    -  Rendelkezésre állási csoport, amely az áttelepített gépet egy rendelkezésre állási csoportba helyezi. A beállítás használatához legalább egy rendelkezésre állási csoportnak kell lennie.
    - Nincs szükség infrastruktúra-redundancia beállításra, ha az áttelepített gépekhez nem szükséges a rendelkezésre állási konfigurációk egyike sem.
9. A **lemez titkosítása típusnál** válassza a következő lehetőséget:
    - Titkosítás – a platform által felügyelt kulccsal
    - Titkosítás – az ügyfél által felügyelt kulccsal
    - Dupla titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal

   > [!NOTE]
   > A virtuális gépek CMK-vel való replikálásához létre kell [hoznia egy lemezes titkosítási](https://go.microsoft.com/fwlink/?linkid=2151800) csoportot a cél erőforráscsoport alatt. A lemezes titkosítási készlet objektum Managed Diskst képez le egy olyan Key Vaultra, amely az SSE-hoz használandó CMK tartalmazza.
  
10. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Tovább** gombra.

    ![Cél beállításai](./media/tutorial-migrate-vmware/target-settings.png)

11. A **számításban** a számításban tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemezének típusát és rendelkezésre állási konfigurációját (ha az előző lépésben van kiválasztva). A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - Virtuálisgép- **méret**: Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő menüben az ajánlott méret látható. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **Operációsrendszer-lemez**: a virtuális gép operációsrendszer-(rendszerindító) lemezének megadása. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási zóna**: Itt adhatja meg a használni kívánt rendelkezésre állási zónát.
    - **Rendelkezésre állási csoport**: adja meg a használni kívánt rendelkezésre állási készletet.

    > [!NOTE]
    > Ha egy másik rendelkezésre állási lehetőséget szeretne kiválasztani egy virtuális gép készletéhez, ugorjon az 1. lépésre, és ismételje meg a lépéseket a különböző rendelkezésre állási beállítások kiválasztásával, miután megkezdte egy virtuális gép replikálását.


 ![VIRTUÁLIS gépek számítási beállításai](./media/tutorial-migrate-vmware/compute-settings.png)

12. A **Lemezek** területen adja meg, hogy a virtuálisgép-lemezek replikálva legyenek-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium felügyelt lemez) az Azure-ban. Ezután kattintson a **Tovább** gombra.
   
    ![A képernyőfelvétel a replikálás párbeszédpanel lemezek lapját jeleníti meg.](./media/tutorial-migrate-vmware/disks.png)

13. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikálási beállításokat a replikálás megkezdése előtt bármikorfrissítheti (a  >  **replikáló gépek** kezelése). A beállítások a replikáció elindítása után nem módosíthatók.

### <a name="provisioning-for-the-first-time"></a>Kiépítés első alkalommal

Ha ez az első virtuális gép, amelyet a projektben replikál, a kiszolgáló áttelepítése automatikusan kiépíti ezeket az erőforrásokat a projekthez tartozó azonos erőforráscsoporthoz.

- **Service Bus**: a kiszolgáló áttelepítése a Service Bus használatával küldi el a replikációs előkészítési üzeneteket a berendezésnek.
- **Átjáró Storage-fiók**: a kiszolgáló áttelepítése az átjáró Storage-fiók használatával tárolja a replikált virtuális gépekre vonatkozó állapotinformációkat.
- **Log Storage-fiók**: a Azure Migrate készülék feltölti a virtuális gépek replikációs naplóit egy log Storage-fiókba. Azure Migrate a replikációs adatokat a replika felügyelt lemezekre alkalmazza.
- **Key Vault**: a Azure Migrate készülék a kulcstartó használatával kezeli a Service Bus kapcsolati karakterláncait, valamint a replikáció során használt Storage-fiókok hozzáférési kulcsait.

## <a name="track-and-monitor"></a>Nyomon követés és figyelés

1. A feladatok állapotának nyomon követése a portál értesítéseiben.
2. A replikáció állapotának figyeléséhez kattintson a **kiszolgálók replikálásához** **Azure Migrate: kiszolgáló áttelepítése** elemre.

     ![Replikáció monitorozása](./media/tutorial-migrate-vmware/replicating-servers.png)

A replikáció a következőképpen történik:
- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás során létrejön egy virtuális gép pillanatképe. A pillanatképből származó lemezes adatok replikálódnak a replika felügyelt lemezekre az Azure-ban.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replika-lemezekre.

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdése előtt futtasson egy teszt-áttelepítést a virtuális gépek számára, mielőtt az Azure-ba történő teljes áttelepítést futtatná. Azt javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el ezt az egyes gépeken.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni, anélkül, hogy ez hatással lenne a helyszíni gépekre, amelyek továbbra is működőképesek maradnak. 
- A tesztelési áttelepítés szimulálja az áttelepítést egy Azure-beli virtuális gép replikálási adataival történő létrehozásával (általában az Azure-előfizetése nem üzemi VNet való áttelepítéssel).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti az áttelepítést, elvégezheti az alkalmazások tesztelését, és bármilyen problémát megtehet a teljes áttelepítés előtt.

Végezzen el egy teszt-áttelepítést a következőképpen:


1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson az **áttelepített kiszolgálók tesztelése** elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-vmware/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gép migrálása

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti a helyszíni gépeket.

1. A Azure Migrate projekt > **kiszolgálók**  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson a **kiszolgálók replikálása** elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-vmware/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **áttelepítés** során  >  **állítsa le a virtuális gépeket, és végezze el az adatvesztés nélküli tervezett áttelepítést**, válassza az **Igen**  >  **OK** lehetőséget.
    - Alapértelmezés szerint az Azure Migrate leállítja a helyszíni virtuális gépet, majd igény szerint replikációt végez a legutóbbi replikáció óta a virtuális gépen történt módosítások szinkronizálása érdekében. Így elkerülhető az adatvesztés.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gépre > a **replikáció leállítása** elemre. Ez leállítja a helyszíni gép replikálását, és megtisztítja a virtuális gép replikációs állapotával kapcsolatos információkat.
2. Telepítse az Azure-beli VM [Linux](../virtual-machines/extensions/agent-linux.md) -ügynököt az áttelepített gépekre, ha a gépen van Linux operációs rendszer. Az áttelepítés során automatikusan telepíti a Windows rendszerű virtuális gépekhez készült virtuálisgép-ügynököt.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni ajánlott eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információk](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információk](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény szerinti felügyelettel](../security-center/security-center-just-in-time.md).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
-  Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>Következő lépések

Vizsgálja meg a [felhőalapú migrációs utat](/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerében.