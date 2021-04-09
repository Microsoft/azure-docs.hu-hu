---
title: Azure-beli VM-értékelések Azure Migrate
description: Tudnivalók a Azure Migrate értékeléséről
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 16c3b59bcfa14cc02f13dadd726e0380d934598b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023809"
---
# <a name="assessment-overview-migrate-to-azure-vms"></a>Felmérés áttekintése (migrálás Azure-beli virtuális gépre)

Ez a cikk áttekintést nyújt az értékelésekről a [Azure Migrate: felderítési és értékelési](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) eszközben. Az eszköz képes a helyszíni kiszolgálók kiértékelésére a VMware virtuális és Hyper-V környezetben, valamint fizikai kiszolgálókat az Azure-ba való áttelepítéshez.

## <a name="whats-an-assessment"></a>Mi az értékelés?

A felderítési és értékelési eszközzel végzett értékelés méri a készültséget, és megbecsüli a helyszíni kiszolgálók Azure-ba történő áttelepítésének hatását.

> [!NOTE]
> A Azure Governmentban tekintse át a [támogatott cél](migrate-support-matrix.md#supported-geographies-azure-government) -értékelési helyet. Vegye figyelembe, hogy a virtuális gépek méretével kapcsolatos javaslatok az értékelésekben a virtuálisgép-sorozatot fogják használni a kormányzati Felhőbeli régiók számára. [További](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) információ a virtuális gépek típusairól.

## <a name="types-of-assessments"></a>Az értékelések típusai

Háromféle értékelést hozhat létre Azure Migrate használatával: felderítés és Értékelés.

***Értékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. A helyszíni kiszolgálókat felhasználhatja [VMware](how-to-set-up-appliance-vmware.md) -és [Hyper-V](how-to-set-up-appliance-hyper-v.md) környezetben, valamint [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) az Azure-beli virtuális gépekre való áttelepítéshez ezzel az értékelési típussal.
**Azure SQL** | A helyszíni SQL Server-kiszolgálók VMware-környezetből Azure SQL Database vagy az Azure SQL felügyelt példányba való átköltöztetésének értékelése.
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md) az értékelés típusának használatával értékelheti az Azure VMware-megoldásba (AVS) való áttelepítésre. [További információ](concepts-azure-vmware-solution-assessment-calculation.md)

Az Azure Migrateekkel létrehozott értékelések az adatok időponthoz kapcsolódó pillanatképei. Az Azure-beli virtuális gépek felmérése két méretezési feltételi lehetőséget kínál:

**Értékelés típusa** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A gyűjtött teljesítményalapok alapján javaslatot tevő kiértékelések | A virtuálisgép-méretre vonatkozó javaslat a CPU-és a RAM-kihasználtsági adatain alapul.<br/><br/> A lemez típusú javaslat a (z) másodpercenkénti bemeneti/kimeneti műveletek (IOPS) és a helyszíni lemezek átviteli sebessége alapján történik. A lemezek típusai az Azure standard HDD, az Azure standard SSD és az Azure prémium szintű lemezek.
**Módosítás nélküli helyszíni** | Olyan felmérések, amelyek nem használnak teljesítményadatokat a javaslatok elvégzéséhez | A virtuális gép méretére vonatkozó javaslat a helyszíni kiszolgáló méretétől függ.<br/><br> Az ajánlott lemez típusa az értékeléshez kiválasztott tárolási típuson alapul.

## <a name="how-do-i-run-an-assessment"></a>Hogyan egy értékelést?

Több módon is futtathat értékeléseket.

- A kiszolgálókat egy egyszerű Azure Migrate berendezés által gyűjtött kiszolgálói metaadatok használatával értékelheti ki. A készülék felfedi a helyszíni kiszolgálókat. Ezután elküldi a kiszolgálói metaadatokat és teljesítményadatokat Azure Migrate.
- A kiszolgálókat a vesszővel tagolt (CSV) formátumban importált kiszolgálói metaadatok használatával értékelheti ki.

## <a name="how-do-i-assess-with-the-appliance"></a>Hogyan az értékelést a berendezéssel?

Ha Azure Migrate berendezést helyez üzembe a helyszíni kiszolgálók felderítése érdekében, hajtsa végre a következő lépéseket:

1. Az Azure és a helyszíni környezet beállítása a Azure Migrate való együttműködéshez.
1. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a felderítési és értékelési eszközt.
1. Egyszerűsített Azure Migrate berendezés üzembe helyezése. A készülék folyamatosan felfedi a helyszíni kiszolgálókat, és elküldi a kiszolgálói metaadatokat és teljesítményadatokat Azure Migrate. Telepítse a készüléket virtuális gépre vagy fizikai kiszolgálóra. Semmit nem kell telepítenie az értékelni kívánt kiszolgálókon.

Miután a készülék megkezdte a kiszolgáló felderítését, összegyűjtheti azokat a kiszolgálókat, amelyeket fel szeretne mérni egy csoportba, és értékelést kell futtatnia a csoportnak az **Azure VM** értékelési típussal.

Kövesse a [VMware](./tutorial-discover-vmware.md), a [Hyper-V](./tutorial-discover-hyper-v.md)vagy a [fizikai kiszolgálók](./tutorial-discover-physical.md) oktatóanyagait, hogy kipróbálja ezeket a lépéseket.

## <a name="how-do-i-assess-with-imported-data"></a>Hogyan az importált adattal való értékelést?

Ha egy CSV-fájl használatával értékeli a kiszolgálókat, nincs szüksége berendezésre. Ehelyett hajtsa végre a következő lépéseket:

1. Az Azure beállítása Azure Migrate
1. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a felderítési és értékelési eszközt.
1. Töltsön le egy CSV-sablont, és adja hozzá a kiszolgálói adatfájlokat.
1. A sablon importálása Azure Migrateba
1. Az importálással hozzáadott kiszolgálók felderítése, összegyűjtése egy csoportba, és értékelés futtatása a csoportnak az **Azure VM** értékelési típussal.

## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

Ha a Azure Migrate berendezést értékelésre használja, ismerkedjen meg a [VMware](migrate-appliance.md#collected-data---vmware) és a [Hyper-V](migrate-appliance.md#collected-data---hyper-v)rendszerhez összegyűjtött metaadatokkal és teljesítménnyel kapcsolatos adatokkal.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hogyan számítja ki a készülék a teljesítményadatokat?

Ha a készüléket a felderítéshez használja, a következő lépésekkel gyűjt teljesítményadatokat a számítási beállításokhoz:

1. A készülék valós idejű mintavételi pontot gyűjt.

    - **VMWare virtuális gépek**: a rendszer 20 másodpercenként gyűjt egy mintavételi pontot.
    - **Hyper-V virtuális gépek**: a rendszer 30 másodpercenként gyűjt egy mintavételi pontot.
    - **Fizikai kiszolgálók**: egy mintavételi pont gyűjtése 5 percenként történik.

1. A készülék egyesíti a mintavételi pontokat úgy, hogy a VMware és a Hyper-V kiszolgálók esetében 10 percenként hozzon létre egyetlen adatpontot, és hogy a fizikai kiszolgálók 5 percenként legyenek. Az adatpont létrehozásához a készülék kiválasztja az összes minta csúcsérték-értékeit. Ezután elküldi az adatpontot az Azure-nak.
1. Az értékelés az elmúlt hónap 10 percenkénti adatpontjait tárolja.
1. Az értékelés létrehozásakor az értékelés azonosítja a megadásában használandó megfelelő adatpontot. Az azonosítás a *teljesítmény előzményeinek* és a *percentilis kihasználtságának* százalékos értékein alapul.

    - Ha például a teljesítmény előzményei egy hét, a percentilis kihasználtsága pedig a 95. percentilis, az értékelés az elmúlt hét 10 perces mintavételi pontjait rendezi. Növekvő sorrendben rendezi őket, és kiválasztja a 95. percentilis értékét a megadásában.
    - A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek akkor szerepelhetnek, ha kiválasztotta a esetek 99% percentilis értékét.
    - Ha ki szeretné választani az időszakhoz tartozó csúcsérték-használatot, és nem szeretne kiugró értékeket kihagyni, válassza a esetek 99% percentilis értéket a percentilis kihasználtsága mezőben.

1. Ennek az értéknek a megszorozza a komforttal, hogy a készülék által összegyűjtött mérőszámok tényleges teljesítmény-kihasználtsági adatai meglegyenek:

    - Processzorhasználat
    - RAM kihasználtsága
    - Lemez IOPS (olvasás és írás)
    - Lemez átviteli sebessége (olvasás és írás)
    - Hálózati átviteli sebesség (be és ki)

## <a name="how-are-azure-vm-assessments-calculated"></a>Hogyan számítják ki az Azure-beli virtuális gépek értékelését?

Az értékelés a helyszíni kiszolgálók metaadatait és teljesítményadatait használja az értékelések kiszámításához. Ha telepíti a Azure Migrate készüléket, az értékelés a készülék által gyűjtött adatokat használja. Ha azonban egy CSV-fájllal importált értékelést futtat, akkor megadja a számítás metaadatait.

A számítások a következő három szakaszban történnek:

1. **Azure-készültség kiszámítása**: vizsgálja meg, hogy a kiszolgálók alkalmasak-e az Azure-ba való áttelepítésre.
1. **Méretezési javaslatok kiszámítása**: a számítás, a tárolás és a hálózat méretezésének becslése.
1. **Havi költségek kiszámítása**: a kiszolgálók Azure-beli futtatásának becsült havi számítási és tárolási költségeinek kiszámítása a Migrálás után.

A számítások az előző sorrendben vannak. A kiszolgálói kiszolgálók csak akkor mozdulnak el egy későbbi fázisra, ha az előzőt átadja. Ha például egy kiszolgáló meghibásodik az Azure készültségi fázisában, az az Azure számára nem megfelelőként van megjelölve. A méretezés és a költségszámítás nem történik meg az adott kiszolgálón.

## <a name="whats-in-an-azure-vm-assessment"></a>Mi az Azure-beli virtuális gépek felmérése?

A következőkben egy Azure-beli virtuális gépek felmérése szerepel:

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az áttelepíteni kívánt hely. Az értékelés jelenleg a következő Azure-régiókat támogatja:<br/><br/> Kelet-Ausztrália, Kelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-India, USA középső régiója, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Közép-Németország, Északkelet-Németország, Kelet-Japán, Nyugat-Japán, Dél-Korea, Dél-Korea, Egyesült Királyság déli régiója, Észak-Európa , Az USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és az USA 2. nyugati régiója.
**Cél Storage-lemez (méretezés)** | Az Azure-beli tároláshoz használandó lemez típusa. <br/><br/> A célként megadott Storage-lemezt prémium szintű felügyelt, standard SSD által felügyelt vagy standard HDD által felügyelt határozza meg.
**Cél tárolóeszköz lemeze (teljesítmény-alapú méretezés)** | Meghatározza a célként megadott tároló lemezének típusát automatikus, prémium szintű felügyelt, standard HDD által felügyelt vagy standard SSD által felügyelt.<br/><br/> **Automatikus**: a lemezre vonatkozó javaslat a lemezek teljesítményadatokat, azaz a IOPS és az átviteli sebességen alapul.<br/><br/>**Prémium vagy standard**: az értékelés azt javasolja, hogy egy lemez SKU legyen a kiválasztott tárolási típuson belül.<br/><br/> Ha a 99,9%-os egypéldányos virtuálisgép-szolgáltatói szerződést (SLA) szeretne használni, érdemes prémium szintű felügyelt lemezeket használnia. Ez a használat biztosítja, hogy az értékelésben szereplő összes lemez prémium szintű felügyelt lemezként legyen ajánlott.<br/><br/> A Azure Migrate csak a felügyelt lemezeket támogatja az áttelepítési értékeléshez.
**Azure Reserved VM Instances** | [Fenntartott példányokat](https://azure.microsoft.com/pricing/reserved-vm-instances/) határoz meg, hogy az értékelésben szereplő költségbecslés figyelembe vegye azokat.<br/><br/> Ha a "fenntartott példányok" lehetőséget választja, a "kedvezmény (%)" és a virtuális gép üzemidő tulajdonságai nem alkalmazhatók.<br/><br/> A Azure Migrate jelenleg csak az utólagos elszámolású ajánlatok esetében támogatja a Azure Reserved VM Instances.
**Méretezési feltételek** | Az Azure-beli virtuális gép megfelelő méretben helyezheti használatos.<br/><br/> A következőképpen történő használat: méretezés vagy teljesítmény-alapú méretezés.
**Teljesítményelőzmények** | Teljesítmény-alapú méretezéssel használatos. A teljesítmény előzményei a teljesítményadatok kiértékeléséhez használt időtartamot határozzák meg.
**Százalékos kihasználtság** | Teljesítmény-alapú méretezéssel használatos. A percentilis kihasználtsága meghatározza a megadásában használt teljesítmény mintájának százalékos értékét.
**Virtuálisgép-sorozatok** | A megadásában megfontolni kívánt Azure virtuálisgép-sorozat. Ha például nem rendelkezik olyan éles környezettel, amely az Azure-beli sorozatú virtuális gépeket igényli, kizárhatja a sorozatot a sorozatok listájáról.
**Kényelmi faktor** | Az értékelés során használt puffer. A rendszer a virtuális gépek processzor-, RAM-, lemez-és hálózati adatmennyiségére alkalmazza. A szolgáltatás olyan problémákhoz vezetett, mint például a szezonális használat, a rövid teljesítménybeli előzmények és a jövőbeli használat valószínű növekedése.<br/><br/> A 20%-os kihasználtságú 10 Magos virtuális gép például általában egy kétmagos virtuális gépet eredményez. Az 2,0-es kényelmi faktorral az eredmény egy négy Magos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/) , amelyben regisztrálva van. Az értékelés az ajánlat költségeit becsüli.
**Pénznem** | A fiók számlázási pénzneme.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott előfizetés-specifikus kedvezmények. Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | A folyamatosan nem futó Azure-beli virtuális gépek esetében a havi és a napi munkaórák száma. A becsült költségbecslés ezen időtartam alapján történik.<br/><br/> Az alapértelmezett értékek havi 31 nap, és naponta 24 óra.
**Azure Hybrid Benefit** | Megadja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha a beállítás alapértelmezett értéke "yes", akkor a Windows rendszerű virtuális gépekre a Windows rendszertől eltérő operációs rendszerek Azure-árai tekintendők.
**EA-előfizetés** | Megadja, hogy a rendszer egy Nagyvállalati Szerződés (EA) előfizetést használ a költségbecslés során. Az előfizetésre érvényes kedvezményt veszi figyelembe. <br/><br/> Hagyja meg a fenntartott példányok beállításait, kedvezmény (%) és a virtuális gép üzemidő tulajdonságai az alapértelmezett beállításokkal.


[Tekintse át az ajánlott eljárásokat](best-practices-assessment.md) az értékelés létrehozásához Azure Migratesal.

## <a name="calculate-readiness"></a>Készültség kiszámítása

Nem minden kiszolgáló alkalmas az Azure-ban való futtatásra. Az Azure-beli virtuális gépek értékelése az összes helyszíni kiszolgálót kiértékeli, és készültségi kategóriát rendel hozzájuk.

- **Készen áll az Azure-ra**: a-kiszolgáló áttelepíthető az Azure-ba, bármilyen módosítás nélkül. Az Azure teljes körű Azure-támogatással fog indulni.
- **Feltételesen készen áll az Azure-ra**: a kiszolgáló megkezdhető az Azure-ban, de nem rendelkezik teljes körű Azure-támogatással. Az Azure például nem támogatja a Windows Server régi verzióját futtató kiszolgálót. Ezeket a kiszolgálókat az Azure-ba történő Migrálás előtt körültekintően kell megadnia. A készültségi problémák elhárításához kövesse az értékeléssel kapcsolatos szervizelési útmutatót.
- **Nem áll készen az Azure-ra**: a kiszolgáló nem indul el az Azure-ban. Ha például egy helyszíni kiszolgáló lemeze több mint 64 TB-ot tárol, az Azure nem tudja üzemeltetni a kiszolgálót. A probléma elhárításához kövesse a Szervizelési útmutatást.
- **Készültség ismeretlen**: Azure Migrate nem tudja meghatározni a kiszolgáló készültségét, mert nincs elég metaadat.

A készültség kiszámításához az értékelés a következő táblázatokban összefoglalt kiszolgáló tulajdonságait és az operációs rendszer beállításait tekinti át.

### <a name="server-properties"></a>Kiszolgáló tulajdonságai

Az Azure-beli virtuális gépek értékeléséhez az értékelés a helyszíni virtuális gép alábbi tulajdonságait vizsgálja meg, hogy képes-e futtatni az Azure-beli virtuális gépeken.

Tulajdonság | Részletek | Azure-készültségi állapot
--- | --- | ---
**Rendszerindítás típusa** | Az Azure a BIOS rendszerindítási típusával támogatja a virtuális gépeket, nem az UEFI-t. | Feltételesen üzemkész, ha a rendszerindítás UEFI típusú.
**Cores** | Minden kiszolgálónak legfeljebb 128 maggal kell rendelkeznie, amely az Azure-beli virtuális gépek által támogatott maximális szám.<br/><br/> Ha rendelkezésre áll a teljesítmény előzményei, Azure Migrate az összehasonlításhoz a felhasznált magokat veszi figyelembe. Ha az értékelési beállítások egy kényelmi tényezőt határoznak meg, a kihasználatlan magok száma megszorozza a komfort tényezővel.<br/><br/> Ha nincsenek teljesítménybeli előzmények, a Azure Migrate a lefoglalt magok használatával alkalmazza a komfort tényezőt. | Készen áll, ha a magok száma a határértéken belül van
**RAM** | Minden kiszolgálónak legfeljebb 3 892 GB RAM-mal kell rendelkeznie, amely az Azure M sorozatú Standard_M128m &nbsp; <sup>2</sup> virtuális gép által támogatott maximális méret. [További információk](../virtual-machines/sizes.md).<br/><br/> Ha elérhetők a teljesítmény előzményei, Azure Migrate a felhasznált RAM-ot az összehasonlításhoz. Ha meg van adva egy kényelmi tényező, a kihasznált RAM-ot a komfort faktor megszorozza.<br/><br/> Ha nincsenek előzmények, a rendszer a lefoglalt RAM-ot használja a komfort tényező alkalmazásához.<br/><br/> | Készen áll, ha a RAM mennyisége a határértéken belül van
**Storage-lemez** | A lemez lefoglalt mérete nem haladhatja meg a 32 TB-ot. Bár az Azure támogatja az 64 TB-os lemezeket az Azure ultra SSD-lemezekkel, az értékelés jelenleg a 32 TB-ot a lemezes méretkorlát miatt ellenőrzi, mert még nem támogatja ultra SSD. <br/><br/> A kiszolgálóhoz csatolt lemezek számának, beleértve az operációsrendszer-lemezt, 65 vagy kevesebbnek kell lennie. | Készen áll, ha a lemez mérete és száma a határértékeken belül van
**Hálózat** | A kiszolgálónak legfeljebb 32 hálózati adapterrel (NIC) kell rendelkeznie hozzá. | Készen áll, ha a hálózati adapterek száma a korláton belül van

### <a name="guest-operating-system"></a>Vendég operációs rendszer

Az Azure-beli virtuális gépek értékeléséhez, valamint a virtuális gépek tulajdonságainak áttekintéséhez az értékelés a kiszolgáló vendég operációs rendszerét vizsgálja meg, hogy eldöntse, futtatható-e az Azure-ban.

> [!NOTE]
> A VMware virtuális gépekhez tartozó vendég-elemzések kezeléséhez az értékelés a vCenter Serverban a virtuális géphez megadott operációs rendszert használja. A vCenter Server azonban nem biztosítja a Linux rendszerű virtuális gépek operációs rendszereinek kernel-verzióját. A verzió felderítéséhez be kell állítania az alkalmazás- [felderítést](./how-to-discover-applications.md). Ezután a készülék felderíti a verziók adatait az App-Discovery beállításakor megadott vendég hitelesítő adatok használatával.


Az értékelés a következő logikát használja az Azure-készültség azonosítására az operációs rendszer alapján:

**Operációs rendszer** | **Részletek** | **Azure-készültségi állapot**
--- | --- | ---
Windows Server 2016 és minden SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-ra.
Windows Server 2012 R2 és minden SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-ra.
Windows Server 2012 és minden SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-ra.
Windows Server 2008 R2 minden SPs-vel | Az Azure teljes körű támogatást biztosít.| Készen áll az Azure-ra.
Windows Server 2008 (32 bites és 64 bites) | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-ra.
Windows Server 2003 és Windows Server 2003 R2 | Ezek az operációs rendszerek átadták a támogatási dátumokat, és szükségük van egy [egyéni támogatási szerződésre (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) az Azure támogatásához. | Feltételesen készen áll az Azure-ra. Az Azure-ba való Migrálás előtt érdemes frissíteni az operációs rendszert.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 és MS-DOS | Ezek az operációs rendszerek átadták a támogatásuk befejezésének dátumát. Előfordulhat, hogy a kiszolgáló az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Feltételesen készen áll az Azure-ra. Javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Windows 7, Windows 8 és Windows 10 | Az Azure [csak a Visual Studio-előfizetések](../virtual-machines/windows/client-images.md) támogatását biztosítja. | Feltételesen készen áll az Azure-ra.
Windows 10 Pro | Az Azure támogatást nyújt a több- [bérlős üzemeltetési jogosultságokhoz.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Feltételesen készen áll az Azure-ra.
Windows Vista és Windows XP Professional | Ezek az operációs rendszerek átadták a támogatásuk befejezésének dátumát. Előfordulhat, hogy a kiszolgáló az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Feltételesen készen áll az Azure-ra. Javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Linux | Tekintse meg az Azure által támogatott [Linux operációs rendszereket](../virtual-machines/linux/endorsed-distros.md) . Az Azure-ban más linuxos operációs rendszerek is elindíthatók. Azt javasoljuk azonban, hogy az operációs rendszert egy támogatott verzióra frissítse, mielőtt áttelepíti az Azure-ba. | Készen áll az Azure-ra, ha a verziót jóváhagyták.<br/><br/>Feltételesen üzemkész, ha a verzió nincs támogatva.
Más operációs rendszerek, például az Oracle Solaris, az Apple macOS és a FreeBSD | Az Azure nem támogatja ezeket az operációs rendszereket. Előfordulhat, hogy a kiszolgáló az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Feltételesen készen áll az Azure-ra. Javasoljuk, hogy telepítsen egy támogatott operációs rendszert az Azure-ba való áttelepítés előtt.  
Az operációs rendszer **vCenter Serverként van megadva** | Ebben az esetben a Azure Migrate nem tudja azonosítani az operációs rendszert. | Ismeretlen készültség. Győződjön meg arról, hogy az Azure támogatja a virtuális gépen futó operációs rendszert.
32 bites operációs rendszerek | Előfordulhat, hogy a kiszolgáló az Azure-ban indul el, de előfordulhat, hogy az Azure nem nyújt teljes körű támogatást. | Feltételesen készen áll az Azure-ra. Az Azure-ba való Migrálás előtt érdemes lehet a 64 bites operációs rendszerre frissíteni.

## <a name="calculating-sizing"></a>Méretezés kiszámítása

Ha a kiszolgáló az Azure-hoz való használatra készként van megjelölve, az értékelés az Azure-beli virtuális gépek felmérésében foglalt javaslatokat tesz elérhetővé. Ezek az ajánlások azonosítják az Azure-beli virtuális gépet és a lemez SKU-t. A méretezési számítások attól függnek, hogy a-t használja-e helyszíni méretezéssel vagy teljesítmény-alapú méretezéssel.

### <a name="calculate-sizing-as-is-on-premises"></a>Méretezés kiszámítása (helyszíni)

 Ha a as-helyszíni méretezést használja, az értékelés nem veszi figyelembe a virtuális gépek és lemezek teljesítményének előzményeit az Azure VM-felmérésben.

- **Számítási méretezés**: az értékelés egy Azure-beli virtuális gép SKU-t foglal le a helyszínen lefoglalt méret alapján.
- **Tárolás és lemez méretezése**: az értékelés az értékelés tulajdonságaiban megadott tárolási típust vizsgálja, és a megfelelő típusú lemezt javasolja. A lehetséges tárolási típusok a következők: standard HDD, standard SSD és prémium. Az alapértelmezett tárolási típus a prémium.
- **Hálózati méretezés**: az értékelés a helyszíni kiszolgálón található hálózati adaptert veszi figyelembe.

### <a name="calculate-sizing-performance-based"></a>Méretezés kiszámítása (teljesítmény-alapú)

Ha teljesítmény-alapú méretezést használ egy Azure-beli virtuális gépek felmérésében, az értékelés a következőképpen teszi a méretezési javaslatokat:

- Az értékelés az Azure-beli virtuális gépek méretének és a lemez típusának azonosítására szolgáló kiszolgáló teljesítményének előzményeit veszi figyelembe.
- Ha egy CSV-fájl használatával importálja a kiszolgálókat, a rendszer a megadott értékeket használja. Ez a módszer különösen akkor hasznos, ha a helyszíni kiszolgálót túlterhelte, a kihasználtság alacsony, és szeretné megfelelő méretben helyezheti az Azure-beli virtuális gépet a költségek megtakarítása érdekében.
- Ha nem szeretné a teljesítményadatokat használni, állítsa alaphelyzetbe a méretezési feltételeket úgy, hogy az a helyszíni legyen, az előző szakaszban leírtak szerint.

#### <a name="calculate-storage-sizing"></a>A tároló méretezésének kiszámítása

Az Azure-beli virtuális gépek felmérése során a tárolók méretezése során a Azure Migrate megkísérli leképezni a kiszolgálóhoz csatolt összes lemezt egy Azure-lemezre. A méretezés a következőképpen működik:

1. Az értékelés hozzáadja a lemez olvasási és írási IOPS az összes szükséges IOPS lekéréséhez. Hasonlóképpen hozzáadja az olvasási és írási sebesség értékét az egyes lemezek teljes átviteli sebességének lekéréséhez. Importálási alapú értékelések esetén lehetősége van a teljes IOPS, a teljes átviteli sebesség és az összes nem érték megadására. az importált fájlban lévő lemezek külön lemezes beállítások megadása nélkül. Ha ezt teszi, a rendszer kihagyja az egyes lemezek méretezését, és a megadott adatok közvetlenül a számítási méretezéshez használatosak, és kiválasztja a megfelelő VM SKU-t.

1. Ha a tárolási típust automatikus értékre adta meg, a kiválasztott típus a hatályos IOPS és az átviteli sebesség alapján történik. Az értékelés meghatározza, hogy a lemezt standard HDD, standard SSD vagy prémium szintű lemezre kívánja-e képezni az Azure-ban. Ha a tárolási típus az egyik ilyen típusú lemezre van beállítva, az értékelés megpróbál a kiválasztott tárolási típuson belül egy lemez SKU-t keresni.
1. A lemezek a következőképpen vannak kiválasztva:
    - Ha az értékelés nem talál a szükséges IOPS és adatátviteli sebességű lemezt, akkor az az Azure számára nem megfelelőként jelöli meg a kiszolgálót.
    - Ha az értékelés megfelelő lemezeket talál, kiválasztja azokat a lemezeket, amelyek támogatják az értékelési beállításokban megadott helyet.
    - Ha több jogosult lemez van, az értékelés kiválasztja a legalacsonyabb költségű lemezt.
    - Ha valamelyik lemez teljesítményadatokat nem érhető el, a konfigurációs lemez mérete standard SSD lemez keresésére szolgál az Azure-ban.

#### <a name="calculate-network-sizing"></a>Hálózati méretezés kiszámítása

Az Azure-beli virtuális gépek értékeléséhez az értékelés megpróbál olyan Azure-beli virtuális gépet találni, amely támogatja a helyszíni kiszolgálóhoz csatlakoztatott hálózati adapterek számát és a szükséges teljesítményt.

- A helyszíni kiszolgáló hatékony hálózati teljesítményének lekéréséhez az értékelés összesíti az adatátviteli sebességet az összes hálózati adapteren a kiszolgálóról (hálózatról). Ezután alkalmazza a komfort tényezőt. Az eredményül kapott értéket használva olyan Azure-beli virtuális gépet talál, amely támogatja a szükséges hálózati teljesítményt.
- A hálózati teljesítmény mellett az értékelés azt is mérlegeli, hogy az Azure-beli virtuális gép képes-e támogatni a szükséges számú hálózati adaptert.
- Ha a hálózati teljesítményadatok nem érhetők el, az értékelés csak a virtuális gép méretének megfelelő hálózati adapterek darabszámát veszi figyelembe.

#### <a name="calculate-compute-sizing"></a>Számítási méretezés kiszámítása

A tárolási és hálózati követelmények kiszámítását követően az értékelés a CPU-és a RAM-követelményeknek megfelelő virtuálisgép-méret megtalálását veszi figyelembe az Azure-ban.

- Azure Migrate a megfelelő Azure-beli virtuális gép méretének megtalálásához a ténylegesen felhasznált magok és a RAM használatával.
- Ha nem található megfelelő méret, a kiszolgáló nem megfelelőként van megjelölve az Azure-hoz.
- Ha talál megfelelő méretet, Azure Migrate alkalmazza a tárolási és hálózati számításokat. Ezután a virtuális gép végső méretére vonatkozó javaslathoz a hely és a díjszabási réteg beállításait alkalmazza.
- Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.

## <a name="confidence-ratings-performance-based"></a>Megbízhatósági minősítések (teljesítmény-alapú)

A Azure Migrate minden teljesítmény-alapú Azure-beli virtuális gép értékelése megbízhatósági minősítéssel van társítva. A minősítés az egyik (legalacsonyabb) és az öt (a legmagasabb) csillag közötti tartományba esik. A megbízhatósági minősítés segít megbecsülni a Azure Migrate által biztosított méretre vonatkozó ajánlások megbízhatóságát.

- A megbízhatósági minősítés egy értékeléshez van rendelve. A minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.
- A teljesítmény-alapú méretezéshez az értékelésnek a következőket kell tennie:
    - A CPU és a RAM kihasználtsági adatai.
    - A lemez IOPS és adatátviteli adatok a kiszolgálóhoz csatolt minden lemezhez.
    - A hálózati I/O-műveletek a kiszolgálóhoz csatlakoztatott összes hálózati adapter teljesítmény-alapú méretezésének kezeléséhez.

Ha ezek a kihasználtsági számok nem érhetők el, a méretre vonatkozó javaslatok megbízhatatlanok lehetnek.

> [!NOTE]
> A megbízhatósági minősítések nem vannak hozzárendelve az importált CSV-fájl használatával mért kiszolgálókhoz. A minősítés szintén nem alkalmazható a helyszíni értékelésre.

### <a name="ratings"></a>Minősítések

Ez a táblázat a kiértékelési megbízhatósági minősítéseket mutatja be, amelyek a rendelkezésre álló adatpontok százalékos arányán alapulnak:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0-20% | 1 csillag
   21-40% | 2 csillag
   41-60% | 3 csillag
   61-80% | 4 csillag
   81-100% | 5 csillag

### <a name="low-confidence-ratings"></a>Alacsony megbízhatósági minősítések

Íme néhány ok, amiért egy értékelés alacsony megbízhatósági minősítést kaphat:

- Nem készítette el a környezet profilját abban az időtartamban, amelyre az értékelést létrehozta. Ha például az értékelést egy napra állítja be, akkor az összes adatpont felderítésének megkezdése után legalább egy napot várnia kell a begyűjtéshez.
- Az értékelés nem tud teljesítményadatokat gyűjteni az értékelési időszakban néhány kiszolgálóról vagy egyik kiszolgálóról sem. Magas megbízhatósági minősítés esetén ügyeljen a következőre: 
    - A kiszolgálók az értékelés időtartamára vannak bekapcsolva
    - A 443-es portokon engedélyezett kimenő kapcsolatok engedélyezettek
    - Hyper-V kiszolgálók esetén a dinamikus memória engedélyezve van 
    
    Számítsa újra az értékelést, hogy tükrözze a megbízhatósági minősítés legújabb módosításait.

- Egyes kiszolgálók az értékelés kiszámításának ideje alatt lettek létrehozva. Tegyük fel például, hogy létrehozott egy értékelést az előző hónap teljesítmény-előzményeihez, de néhány kiszolgálót csak egy héttel ezelőtt hoztak létre. Ebben az esetben az új kiszolgálókhoz tartozó teljesítményadatok nem lesznek elérhetők a teljes időtartamra, és a megbízhatósági minősítés alacsony lenne.

> [!NOTE]
> Ha az értékelések megbízhatósági minősítése kevesebb, mint öt csillag, javasoljuk, hogy várjon legalább egy napot, amíg a készülék felkeresi a környezetet, majd számítsa ki újra az értékelést. Ellenkező esetben a teljesítmény-alapú méretezés megbízhatatlan lehet. Ebben az esetben javasoljuk, hogy az értékelést a helyszíni méretezésre állítsa át.

## <a name="calculate-monthly-costs"></a>Havi költségek kiszámítása

A méretezési javaslatok befejezése után az Azure-beli virtuális gépek felmérése Azure Migrate kiszámítja a számítási és tárolási költségeket a Migrálás után.

- **Számítási költség**: Azure Migrate a javasolt Azure-beli virtuálisgép-méretet és az Azure számlázási API-t használja a kiszolgáló havi költségének kiszámításához.

    A számítás a következőket veszi figyelembe:
    - Operációs rendszer
    - Frissítési garancia
    - Fenntartott példányok
    - Virtuális gép üzemideje
    - Hely
    - Pénznem beállításai

    Az értékelés összegzi az összes kiszolgáló költségét a teljes havi számítási költség kiszámításához.

- **Tárolási költség**: a kiszolgáló havi tárolási költségeit a rendszer a kiszolgálóhoz csatolt összes lemez havi költségének összesítésével számítja ki.

    Az értékelés a teljes havi tárolási költségeket az összes kiszolgáló tárolási költségeinek összesítésével számítja ki. Jelenleg a számítás nem veszi figyelembe az értékelési beállításokban megadott ajánlatokat.

A költségek az értékelési beállításokban megadott pénznemben jelennek meg.

## <a name="next-steps"></a>Következő lépések

[Tekintse át](best-practices-assessment.md) az értékelések létrehozásával kapcsolatos ajánlott eljárásokat. 

- Ismerje meg a [VMware](./tutorial-discover-vmware.md) és [Hyper-V ](./tutorial-discover-hyper-v.md) környezetben, illetve a [fizikai kiszolgálókon](./tutorial-discover-physical.md)futó kiszolgálók értékelését.
- Tudnivalók a [CSV-fájllal importált](./tutorial-discover-import.md)kiszolgálók értékeléséről.
- További információ a [függőségi vizualizáció](concepts-dependency-visualization.md)beállításáról.