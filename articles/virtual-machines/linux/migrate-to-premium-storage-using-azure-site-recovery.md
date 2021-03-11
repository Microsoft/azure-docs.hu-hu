---
title: Linux rendszerű virtuális gépek migrálása az Azure Premium Storageba Azure Site Recovery
description: Áttelepítheti a meglévő virtuális gépeket az Azure Premium Storageba Site Recovery használatával. A Premium Storage nagy teljesítményű, kis késleltetésű lemezes támogatást biztosít az Azure-Virtual Machines futó I/O-igényes számítási feladatokhoz.
author: luywang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 8a03637352762646b0f209263769dbdb1ff14b21
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562622"
---
# <a name="use-site-recovery-to-migrate-to-premium-storage"></a>A Site Recovery használatával váltson át Premium Storage

Az [Azure Premium SSD](../disks-types.md) -k nagy teljesítményű, kis késleltetésű lemezes támogatást biztosítanak az I/O-igényű számítási feladatokat futtató virtuális gépekhez (VM). Ez az útmutató segítséget nyújt a virtuálisgép-lemezek standard szintű Storage-fiókból egy Premium Storage-fiókba való átadásához [Azure site Recovery](../../site-recovery/site-recovery-overview.md)használatával.

A Site Recovery egy olyan Azure-szolgáltatás, amely a helyszíni fizikai kiszolgálók és virtuális gépek felhőbe (Azure) vagy egy másodlagos adatközpontba történő replikálásával járul hozzá az üzletmenet folytonosságának és a vész-helyreállítási stratégiához. Ha az elsődleges helyen kimaradások történnek, a feladatátvétel a másodlagos helyre történik, hogy az alkalmazások és a számítási feladatok elérhetők maradjanak. Ha visszatér a normál működésre, a feladat visszakerül az elsődleges helyre. 

Site Recovery feladatátvételi teszteket biztosít a vész-helyreállítási gyakorlatok támogatásához anélkül, hogy ez hatással lenne az éles környezetekre. A nem várt katasztrófák esetén a feladatátvételt a minimális adatvesztéssel (a replikálási gyakoriságtól függően) futtathatja. A Premium Storagera való Migrálás során a [feladatátvételt](../../site-recovery/site-recovery-failover.md) használhatja a site Recovery a célként megadott lemezek egy Premium Storage-fiókba való áttelepítéséhez.

Javasoljuk, hogy a Site Recovery használatával Premium Storage áttelepítését, mert ez a beállítás minimális állásidőt biztosít. Ezzel a beállítással elkerülhető a lemezek másolásának manuális végrehajtása és az új virtuális gépek létrehozása is. A Site Recovery rendszeresen másolja a lemezeket, és új virtuális gépeket hoz létre a feladatátvétel során. 

Site Recovery számos feladatátvételi típust támogat minimális vagy állásidő nélkül. Az állásidő és a becslés adatvesztésének megtervezéséhez tekintse meg a [feladatátvétel típusát site Recoveryban](../../site-recovery/site-recovery-failover.md). Ha a [feladatátvételt követően előkészíti az Azure-beli virtuális gépekhez való csatlakozást](../../site-recovery/vmware-azure-tutorial.md), akkor a feladatátvételt követően az RDP használatával csatlakoznia kell az Azure-beli virtuális géphez.

![Vész-helyreállítási diagram][1]

## <a name="azure-site-recovery-components"></a>Összetevők Azure Site Recovery

Ezek az Site Recovery összetevők az áttelepítési forgatókönyv szempontjából fontosak:

* A **konfigurációs kiszolgáló** egy Azure-beli virtuális gép, amely összehangolja a kommunikációt és kezeli az adatreplikálási és helyreállítási folyamatokat. Ezen a virtuális gépen egyetlen telepítési fájlt kell futtatnia a konfigurációs kiszolgáló és egy további, a Process Server nevű, replikációs átjáróként működő összetevő telepítéséhez. További információ a [konfigurációs kiszolgáló előfeltételeiről](../../site-recovery/vmware-azure-tutorial.md). A konfigurációs kiszolgálót csak egyszer kell beállítania, és az összes áttelepítéshez használhatja azt ugyanahhoz a régióhoz.

* A **Process Server** egy replikációs átjáró, amely: 

  1. Replikációs adatok fogadása a forrás virtuális gépekről.
  2. A gyorsítótárazással, tömörítéssel és titkosítással optimalizálja az adatátvitelt.
  3. Az adatokat egy Storage-fiókba küldi. 

  Emellett a mobilitási szolgáltatás leküldéses telepítését is kezeli a forrásként szolgáló virtuális gépekre, és elvégzi a forrás virtuális gépek automatikus felderítését. Az alapértelmezett Process Server telepítve van a konfigurációs kiszolgálón. Az üzemelő példány skálázásához további önálló folyamat-kiszolgálókat is telepíthet. További információ [a Process Server-telepítéssel kapcsolatos ajánlott](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) eljárásokról és a [további folyamat-kiszolgálók üzembe helyezéséről](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). A folyamat-kiszolgálót csak egyszer kell beállítania, és az összes áttelepítéshez használhatja azt ugyanahhoz a régióhoz.

* A **mobilitási szolgáltatás** egy olyan összetevő, amely minden replikálni kívánt szabványos virtuális gépen telepítve van. Rögzíti az adatírásokat a szabványos virtuális gépen, és továbbítja azokat a Process Servernek. További információ a [replikált gépek előfeltételeiről](../../site-recovery/vmware-azure-tutorial.md).

Ez a kép bemutatja, hogyan hatnak ezek az összetevők:

![Site Recovery-összetevők interakciója][15]

> [!NOTE]
> A Site Recovery nem támogatja a tárolóhelyek lemezének áttelepítését.

További összetevők más forgatókönyvekhez: forgatókönyv- [architektúra](../../site-recovery/vmware-azure-tutorial.md).

## <a name="azure-essentials"></a>Azure Essentials

Ezek az áttelepítési forgatókönyvre vonatkozó Azure-követelmények:

* Azure-előfizetés.
* Egy prémium szintű Azure Storage-fiók a replikált adattároláshoz.
* Egy Azure-beli virtuális hálózat, amelyhez a virtuális gépek csatlakozni fognak, amikor a feladatátvételkor jönnek létre. Az Azure-beli virtuális hálózatnak ugyanabban a régióban kell lennie, mint ahol a Site Recovery fut.
* Egy Azure standard Storage-fiók a replikációs naplók tárolásához. Ez lehet ugyanaz a Storage-fiók az áttelepíteni kívánt VM-lemezek esetében.

## <a name="prerequisites"></a>Előfeltételek

* Ismerje meg a megfelelő áttelepítési forgatókönyv összetevőit az előző szakaszban.
* Tervezze meg az állásidőt a [site Recovery feladatátvételének](../../site-recovery/site-recovery-failover.md)megismerésével.

## <a name="setup-and-migration-steps"></a>Telepítési és áttelepítési lépések

Site Recovery használatával áttelepítheti az Azure IaaS virtuális gépeket régiók vagy ugyanazon régión belül. A következő utasítások erre az áttelepítési forgatókönyvre vonatkoznak a [VMWare virtuális gépeket vagy fizikai kiszolgálókat az Azure-ba replikáló](../../site-recovery/vmware-azure-tutorial.md)cikkből. Az ebben a cikkben található utasításokon felül kövesse a hivatkozásokat a részletes lépésekhez.

### <a name="step-1-create-a-recovery-services-vault"></a>1. lépés: Recovery Services-tároló létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Válassza **az erőforrás létrehozása**  >    >  **biztonsági mentés** és **site Recovery (OMS)** lehetőséget. Választhatja a **Tallózás**  >  **Recovery Services** tár  >  **hozzáadása** lehetőséget is. 
3. Itt adhatja meg azt a régiót, amelyet a virtuális gépek replikálnak a rendszerbe. Ugyanabban a régióban a Migrálás céljából válassza ki azt a régiót, ahol a forrásként használt virtuális gépek és a forrás Storage-fiókok találhatók. 

### <a name="step-2-choose-your-protection-goals"></a>2. lépés: a védelmi célok kiválasztása 

1. Nyissa meg a [Azure Portalt](https://portal.azure.com)azon a virtuális gépen, amelyre telepíteni kívánja a konfigurációs kiszolgálót.
2. Nyissa meg **Recovery Services**-tárolók  >  **beállításait**  >  **site Recovery**  >  **1. lépés: az infrastruktúra előkészítése**  >  **védelmi cél**.

   ![Böngészés a védelmi cél ablaktáblán][2]

3. A **védelem célja** területen az első legördülő listában válassza az **Azure** lehetőséget. A második legördülő listában válassza a **nem virtualizált/egyéb** lehetőséget, majd kattintson **az OK gombra**.

   ![Védelmi cél ablaktábla kitöltött mezőkkel][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>3. lépés: a forrásoldali környezet (konfigurációs kiszolgáló) beállítása

1. Töltse le **Azure site Recovery egyesített telepítőt** és a tároló regisztrációs kulcsát az **infrastruktúra** előkészítése  >  **előkészítési forrás**  >  **hozzáadására szolgáló kiszolgáló hozzáadása** panelen. 
 
   Az egyesített telepítő futtatásához szüksége lesz a tároló regisztrációs kulcsára. A kulcs a generálásától számított öt napig érvényes.

   ![Böngészés a kiszolgáló hozzáadása panelen][4]

2. A **kiszolgáló hozzáadása** panelen adjon hozzá egy konfigurációs kiszolgálót.

   ![Kiszolgáló ablaktábla hozzáadása a kiválasztott konfigurációs kiszolgálóval][5]

3. Azon a virtuális gépen, amelyet konfigurációs kiszolgálóként használ, futtassa az egyesített telepítőt a konfigurációs kiszolgáló és a Process Server telepítéséhez. A [képernyőképek](../../site-recovery/vmware-azure-tutorial.md) segítségével elvégezheti a telepítést. Az áttelepítési forgatókönyvhöz megadott lépésekért tekintse meg az alábbi képernyőképeket.

   1. A **Kezdés előtt** válassza **a konfigurációs kiszolgáló és a Process Server telepítése** lehetőséget.

      ![Mielőtt elkezdené a lapot][6]

   2. A **regisztráció** területen keresse meg és válassza ki a tárból letöltött regisztrációs kulcsot.

      ![Regisztrációs oldal][7]

   3. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Ehhez az áttelepítési forgatókönyvhöz válassza a **nem** lehetőséget.

      ![Környezet részletei lap][8]

4. A telepítés befejezése után tegye a következőket a **Microsoft Azure site Recovery konfigurációs kiszolgáló** ablakban:
 
   1. A **fiókok kezelése** lapon hozzon létre egy fiókot, amelyet site Recovery használhat az automatikus észleléshez. (A fizikai gépek védelmének forgatókönyve nem vonatkozik a fiók beállítására, de legalább egy fiókra van szükség az alábbi lépések egyikének engedélyezéséhez. Ebben az esetben a fiókot és a jelszót a következőképpen nevezheti el:.) 
   2. A tároló hitelesítő adatainak feltöltéséhez használja a tár **regisztráció** lapját.

      ![Tár regisztrációja lap][9]

### <a name="step-4-set-up-the-target-environment"></a>4. lépés: a célként megadott környezet beállítása

Válassza az **infrastruktúra előkészítése**  >  **cél** lehetőséget, majd adja meg azt a telepítési modellt, amelyet a virtuális gépekhez szeretne használni a feladatátvételt követően. A forgatókönyvtől függően a **klasszikus** vagy a **Resource Managert** is választhatja.

![Cél ablaktábla][10]

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. 

> [!NOTE]
> Ha prémium szintű Storage-fiókot használ a replikált adatbázisokhoz, be kell állítania egy további szabványos Storage-fiókot a replikációs naplók tárolásához.

### <a name="step-5-set-up-replication-settings"></a>5. lépés: a replikációs beállítások beállítása

Annak ellenőrzéséhez, hogy a konfigurációs kiszolgáló sikeresen hozzá van-e rendelve a létrehozott replikációs házirendhez, kövesse a [replikációs beállítások beállítása](../../site-recovery/vmware-azure-tutorial.md)című témakört.

### <a name="step-6-plan-capacity"></a>6. lépés: kapacitás megtervezése

1. A [Capacity Planner](../../site-recovery/site-recovery-capacity-planner.md) használatával pontosan megbecsülheti a hálózati sávszélességet, a tárolást és az egyéb követelményeket a replikációs igények kielégítése érdekében. 
2. Ha elkészült, válassza az **Igen lehetőséget,** hogy elvégezte a **kapacitás megtervezését?**.

   ![A kapacitás tervezésének jóváhagyására szolgáló mező][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>7. lépés: a mobilitási szolgáltatás telepítése és a replikáció engedélyezése

1. Dönthet úgy, hogy [leküldi a telepítést](../../site-recovery/vmware-azure-tutorial.md) a forrás virtuális gépekre, vagy [manuálisan telepíti a mobilitási szolgáltatást](../../site-recovery/vmware-azure-install-mobility-service.md) a forrásként szolgáló virtuális gépekre. A telepítés és a manuális telepítő elérési útjának megadására vonatkozó követelményt a megadott hivatkozásban találja. Ha manuális telepítést végez, előfordulhat, hogy belső IP-címet kell használnia a konfigurációs kiszolgáló megtalálásához.

   ![Konfigurációs kiszolgáló részletei lap][12]

   A feladatátvételen átesett virtuális gépnek két ideiglenes lemeze lesz: egyet az elsődleges virtuális gépről, a másikat pedig a virtuális gép üzembe helyezése során hozta létre a helyreállítási régióban. Ha ki szeretné zárni az ideiglenes lemezt a replikáció előtt, telepítse a mobilitási szolgáltatást a replikáció engedélyezése előtt. További információ az ideiglenes lemez kizárásáról: [Lemezek kizárása a replikációból](../../site-recovery/vmware-azure-tutorial.md).

2. A replikációt a következők szerint engedélyezheti:
   1. Válassza az **alkalmazás**  >  **forrásának** replikálása lehetőséget. Miután az első alkalommal engedélyezte a replikálást, kattintson a **+ replikálás** a tárolóban lehetőségre a további gépek replikációjának engedélyezéséhez.
   2. Az 1. lépésben állítsa be a **forrást** a Process kiszolgálóként.
   3. A 2. lépésben adja meg a feladatátvétel utáni üzembe helyezési modellt, egy prémium szintű Storage-fiókot, amelyre át kell telepíteni a naplókat, és egy virtuális hálózatot, amely nem fog működni.
   4. A 3. lépésben adja hozzá a védett virtuális gépeket IP-cím szerint. (Szükség lehet egy belső IP-cím megkeresésére.)
   5. A 4. lépésben konfigurálja a tulajdonságokat úgy, hogy kiválasztja a korábban a Process Serveren beállított fiókokat.
   6. Az 5. lépésben válassza ki azt a replikációs házirendet, amelyet korábban a "5. lépés: replikációs beállítások beállítása" című témakörben hozott létre.
   7. Válassza az **OK** lehetőséget.

   > [!NOTE]
   > Ha egy Azure-beli virtuális gép fel van foglalva és újra elindult, nincs garancia arra, hogy ugyanazt az IP-címet kapja. Ha a konfigurációs kiszolgáló vagy folyamat kiszolgálójának vagy a védett Azure-beli virtuális gépeknek az IP-címe megváltozik, előfordulhat, hogy az ebben a forgatókönyvben lévő replikáció nem fog megfelelően működni.

   ![Replikáció ablaktábla engedélyezése a forrás kiválasztásával][13]

Az Azure Storage-környezet tervezésekor ajánlott külön Storage-fiókot használni a rendelkezésre állási csoportba tartozó egyes virtuális gépekhez. Javasoljuk, hogy kövesse a tárolási réteg ajánlott eljárását, hogy az [egyes rendelkezésre állási csoportokhoz több Storage-fiókot használjon](../availability.md). A virtuálisgép-lemezek több Storage-fiókba való terjesztése segíti a tárterület rendelkezésre állásának javítását, és az Azure Storage-infrastruktúra teljes I/O-elosztását.

Ha a virtuális gépek rendelkezésre állási csoportba tartoznak, ahelyett, hogy az összes virtuális gép lemezeit egyetlen Storage-fiókba replikálja, a több virtuális gép többszöri áttelepítését javasoljuk. Így az azonos rendelkezésre állási csoportba tartozó virtuális gépek egyetlen Storage-fiókkal sem osztoznak. A **replikáció engedélyezése** panelen beállíthatja az egyes virtuális gépekhez tartozó cél Storage-fiókot egyszerre.
 
A feladatátvétel utáni üzembe helyezési modellt igény szerint választhatja ki. Ha a feladatátvételt követő üzemi modellként Azure Resource Manager választ, a virtuális gép (Resource Manager) egy virtuális GÉPRE (Resource Manager) is átadható, vagy a virtuális gép (klasszikus) virtuális gépre (Resource Manager) történő feladatátvétele végezhető el.

### <a name="step-8-run-a-test-failover"></a>8. lépés: feladatátvételi teszt futtatása

A replikálás befejezésének megadásához válassza ki a site Recovery példányt, majd válassza a **Beállítások**  >  **replikált elemek** lehetőséget. Ekkor megtekintheti a replikációs folyamat állapotát és százalékos arányát. 

A kezdeti replikáció befejezését követően futtasson egy feladatátvételi tesztet a replikációs stratégia ellenőrzéséhez. A feladatátvételi teszt részletes lépéseiért lásd: [feladatátvételi teszt futtatása site Recoveryban](../../site-recovery/vmware-azure-tutorial.md). 

> [!NOTE]
> A feladatátvétel futtatása előtt győződjön meg arról, hogy a virtuális gépek és a replikációs stratégia megfelel a követelményeknek. További információ a feladatátvételi teszt futtatásáról: [feladatátvételi teszt tesztelése az Azure-ban site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

A feladatátvételi teszt állapotát a **Beállítások**  >  **feladatok**  >  *YOUR_FAILOVER_PLAN_NAMEban* tekintheti meg. A panelen megtekintheti a lépések és a siker/sikertelen eredmények részletezését. Ha a feladatátvételi teszt bármelyik lépésben meghiúsul, válassza ki a lépést a hibaüzenet ellenőrzéséhez. 

### <a name="step-9-run-a-failover"></a>9. lépés: feladatátvétel futtatása

A feladatátvételi teszt befejezése után futtassa a feladatátvételt, hogy áttelepítse a lemezeket Premium Storage és replikálja a virtuálisgép-példányokat. Kövesse a [feladatátvétel futtatásának](../../site-recovery/site-recovery-failover.md#run-a-failover)részletes lépéseit. 

Ügyeljen arra, hogy a **virtuális gépek leállítása lehetőséget válassza, és szinkronizálja a legfrissebb adatokat**. Ezzel a beállítással megadható, hogy Site Recovery megpróbálja leállítani a védett virtuális gépeket, és szinkronizálni az adatokat, hogy a rendszer átadja az adat legújabb verzióját. Ha nem jelöli be ezt a beállítást, vagy a kísérlet nem sikerül, a feladatátvétel a virtuális gép legújabb elérhető helyreállítási pontjából fog származni. 

Site Recovery létre fog hozni egy virtuálisgép-példányt, amelynek típusa azonos, vagy hasonló egy Premium Storage-kompatibilis virtuális géphez. A különböző virtuálisgép-példányok teljesítményének és árának ellenőrzéséhez tekintse meg [Windows Virtual Machines díjszabását](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) , vagy [Linux Virtual Machines díjszabását](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>A migrálást követő lépések

1. **Ha alkalmazható, konfigurálja a replikált virtuális gépeket a rendelkezésre állási csoportba**. A Site Recovery nem támogatja a virtuális gépek áttelepítését a rendelkezésre állási csoporttal együtt. A replikált virtuális gép központi telepítésének függvényében hajtsa végre az alábbi műveletek egyikét:
   * A klasszikus üzemi modellel létrehozott virtuális gépek esetében: vegye fel a virtuális gépet a rendelkezésre állási csoportba a Azure Portal. A részletes lépésekért lépjen a [meglévő virtuális gép hozzáadása egy rendelkezésre állási csoporthoz lehetőségre](/previous-versions/azure/virtual-machines/linux/classic/configure-availability-classic).
   * A Resource Manager-alapú üzemi modellel létrehozott virtuális gépek esetén mentse a virtuális gép konfigurációját, majd törölje és hozza létre újra a virtuális gépeket a rendelkezésre állási csoportból. Ehhez használja a szkriptet a [set Azure Resource Manager VM rendelkezésre állási készletben](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). A szkript futtatása előtt tekintse át a korlátozásait, és tervezze meg az állásidőt.

2. **Törölje a régi virtuális gépeket és lemezeket**. Győződjön meg arról, hogy a prémium szintű lemezek konzisztensek a forrásoldali lemezekkel, és hogy az új virtuális gépek ugyanazt a funkciót használják, mint a forrás virtuális gépek. Törölje a virtuális gépet, és törölje a forrásként szolgáló Storage-fiókok lemezeit a Azure Portal. Ha van olyan probléma, amelyben a lemez nincs törölve, bár törölte a virtuális gépet, tekintse meg a [tárolási erőforrások törlésével kapcsolatos hibák elhárítása](../troubleshooting/storage-resource-deletion-errors.md)című témakört.

3. **Tisztítsa meg az Azure site Recovery infrastruktúrát**. Ha Site Recoveryra már nincs szükség, megtisztíthatja infrastruktúráját. Törölje a replikált elemeket, a konfigurációs kiszolgálót és a helyreállítási szabályzatot, majd törölje a Azure Site Recovery-tárolót.

## <a name="troubleshooting"></a>Hibaelhárítás

* [A virtuális gépek és a fizikai kiszolgálók védelmének figyelése és hibakeresése](../../site-recovery/site-recovery-monitor-and-troubleshoot.md)
* [A Microsoft Q&egy kérdés oldalt Microsoft Azure Site Recovery](/answers/topics/azure-site-recovery.html)

## <a name="next-steps"></a>Következő lépések

A virtuális gépek áttelepítésére vonatkozó konkrét forgatókönyvek esetében tekintse meg a következő forrásokat:

* [Azure-Virtual Machines migrálása a Storage-fiókok között](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Linux rendszerű virtuális merevlemez feltöltése](upload-vhd.md)
* [Virtual Machines migrálása az Amazon AWS-ből a Microsoft Azureba](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Az Azure Storage szolgáltatással és az Azure Virtual Machinesával kapcsolatos további információkért tekintse meg a következő forrásokat is:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [IaaS-alapú virtuális gépek lemeztípusának kiválasztása](../disks-types.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png