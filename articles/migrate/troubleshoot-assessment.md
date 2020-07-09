---
title: Az értékelés és a függőség vizualizációjának hibakeresése Azure Migrate
description: Segítség kérése az értékelés és a függőségi vizualizáció hibaelhárításához Azure Migrateban.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 61afc3ec0f37f5d8b1030818d21b7daabb7fce40
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121673"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Értékelés/függőségek vizualizációjának hibaelhárítása

Ez a cikk segítséget nyújt az értékeléssel és a függőségi vizualizációval kapcsolatos hibák elhárításában [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Értékelési készültséggel kapcsolatos problémák

Javítsa ki az értékelés készültségi problémáit az alábbiak szerint:

**Probléma** | **Hibajavítás**
--- | ---
Nem támogatott rendszerindítási típus | Az Azure nem támogatja az EFI rendszerindítási típussal rendelkező virtuális gépeket. Javasoljuk, hogy az áttelepítés futtatása előtt alakítsa át a rendszerindítási típust BIOS-ra. <br/><br/>Az ilyen virtuális gépek áttelepítésének kezeléséhez Azure Migrate kiszolgáló áttelepítését használhatja. Az áttelepítés során a rendszer a virtuális gép rendszerindítási típusát a BIOS-ba konvertálja.
Feltételesen támogatott Windows operációs rendszer | Az operációs rendszer elérte a támogatás befejezési dátumát, és az [Azure-támogatáshoz](https://aka.ms/WSosstatement)egyéni támogatási szerződés (CSA) szükséges. Az Azure-ba való Migrálás előtt érdemes lehet frissíteni.
Nem támogatott Windows operációs rendszer | Az Azure csak a [kiválasztott Windows operációsrendszer-verziókat](https://aka.ms/WSosstatement)támogatja. Érdemes lehet frissíteni a gépet az Azure-ba való áttelepítés előtt.
Feltételesen támogatott Linux operációs rendszer | Az Azure csak a [kiválasztott LINUXOS operációsrendszer-verziókat](../virtual-machines/linux/endorsed-distros.md)támogatja. Érdemes lehet frissíteni a gépet az Azure-ba való áttelepítés előtt.
Nem támogatott Linux operációs rendszer | Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációs rendszer támogatását. Javasoljuk, hogy az Azure-ba való Migrálás előtt frissítsen egy [támogatott Linux-verzióra](../virtual-machines/linux/endorsed-distros.md) .
Ismeretlen operációs rendszer | A virtuális gép operációs rendszere a következőként lett megadva: vCenter Server. Ez a viselkedés blokkolja Azure Migrate a virtuális gép Azure-készültségének ellenőrzéséhez. Mielőtt áttelepíti a számítógépet, győződjön meg arról, hogy az Azure [támogatja](https://aka.ms/azureoslist) az operációs rendszert.
Nem támogatott bites verzió | Az 32 bites operációs rendszerekkel rendelkező virtuális gépek az Azure-ban indíthatók, de javasoljuk, hogy az Azure-ba való Migrálás előtt frissítsen a 64-bites verzióra.
Microsoft Visual Studio-előfizetést igényel | A gép Windows-ügyfél operációs rendszert futtat, amely csak Visual Studio-előfizetésen keresztül támogatott.
Nem található virtuális gép a szükséges tárolási teljesítményhez | A géphez szükséges tárolási teljesítmény (bemeneti/kimeneti műveletek száma másodpercenként [IOPS] és átviteli sebesség) meghaladja az Azure-beli virtuális gépek támogatását. Csökkentse a gép tárolási követelményeit az áttelepítés előtt.
Nem található virtuális gép a szükséges hálózati teljesítményhez | A gép számára szükséges hálózati teljesítmény (be/ki) meghaladja az Azure-beli virtuális gépek támogatását. Csökkentse a gép hálózati követelményeit.
A virtuális gép nem található a megadott helyen | A Migrálás előtt használjon másik célhelyet.
Egy vagy több nem megfelelő lemez | A virtuális géphez csatolt egy vagy több lemez nem felel meg az Azure követelményeinek. Egy<br/><br/> Azure Migrate: a kiszolgáló értékelése jelenleg nem támogatja ultra SSD lemezek használatát, és a prémium szintű felügyelt lemezekre vonatkozó korlátok alapján értékeli a lemezeket (32 TB).<br/><br/> Győződjön meg arról, hogy a virtuális géphez csatolt minden egyes lemez mérete < 64 TB (ultra SSD-lemezek által támogatott).<br/><br/> Ha nem, csökkentse a lemez méretét az Azure-ba való Migrálás előtt, vagy használjon több lemezt az Azure-ban, és [csoportosítsa őket](../virtual-machines/windows/premium-storage-performance.md#disk-striping) a nagyobb tárolási korlátok eléréséhez. Győződjön meg arról, hogy az egyes lemezek által igényelt teljesítmény (IOPS és átviteli sebesség) támogatott az Azure által [felügyelt virtuálisgép-lemezek](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)esetében.
Egy vagy több nem megfelelő hálózati adapter. | A nem használt hálózati adapterek eltávolítása a gépről az áttelepítés előtt.
A lemezek száma meghaladja a korlátot | Az áttelepítés előtt távolítsa el a nem használt lemezeket a gépről.
A lemez mérete meghaladja a korlátot | Azure Migrate: a kiszolgáló értékelése jelenleg nem támogatja ultra SSD lemezek használatát, és a prémium szintű lemezes korlátok alapján értékeli a lemezeket (32 TB).<br/><br/> Az Azure azonban a legfeljebb 64 TB méretű lemezeket támogatja (ultra SSD lemezek által támogatott). A lemezeket az áttelepítés előtt kevesebb mint 64 TB-ra csökkenti, vagy több lemezt is használhat az Azure-ban [, és a](../virtual-machines/windows/premium-storage-performance.md#disk-striping) nagyobb tárolási korlátok eléréséhez.
A lemez nem érhető el a megadott helyen | A Migrálás előtt ellenőrizze, hogy a lemez a célhelyen van-e.
A lemez nem érhető el a megadott redundancia esetén | A lemeznek az értékelési beállításokban definiált redundancia-tárolási típust kell használnia (alapértelmezés szerint LRS).
Belső hiba miatt nem sikerült meghatározni a lemez alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Nem található a szükséges magokkal és memóriával rendelkező virtuális gép | Az Azure nem talált megfelelő virtuálisgép-típust. A Migrálás előtt csökkentse a helyszíni gép memóriáját és a magok számát.
Belső hiba miatt nem sikerült meghatározni a virtuális gép alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Belső hiba miatt nem sikerült meghatározni egy vagy több lemez alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Belső hiba miatt nem sikerült meghatározni egy vagy több hálózati adapter megfelelőségét | Próbálja meg létrehozni a csoport új értékelését.
Nem található virtuálisgép-méret az ajánlat pénzneme számára fenntartott példányhoz | A gép nem megfelelőként van megjelölve, mert a virtuális gép mérete nem található az RI, az ajánlat és a pénznem kiválasztott kombinációjára vonatkozóan. Szerkessze az értékelési tulajdonságokat az érvényes kombinációk kiválasztásához és az értékelés újraszámításához. 
Feltételesen kész Internet Protocol | Csak az Azure VMware Solution (AVS) értékelésekre alkalmazható. Az AVS nem támogatja az IPv6-alapú internetes címek tényezőjét.Ha a számítógépet IPv6-kapcsolattal észleli, forduljon az AVS-csapathoz a szervizeléssel kapcsolatos útmutatásért.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Javasolt áttelepítési eszköz az importálási alapú AVS-felmérésben ismeretlenként megjelölve

A CSV-fájlon keresztül importált gépek esetében ismeretlen az alapértelmezett áttelepítési eszköz és az AVS Assessment. A VMware-gépek esetében azonban ajánlott a VMWare Hybrid Cloud Extension (HCX) megoldás használata. [További információ](../azure-vmware/hybrid-cloud-extension-installation.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>A Linux rendszerű virtuális gépek feltételesen készen állnak egy Azure-beli virtuális gép értékelésére

VMware és Hyper-V rendszerű virtuális gépek esetén a kiszolgáló értékelése a Linux rendszerű virtuális gépeket "feltételesen felkészültként" jelöli meg a kiszolgáló értékelésének ismert hiánya miatt. 

- A rés megakadályozza a helyszíni virtuális gépeken telepített Linux operációs rendszer másodlagos verziójának észlelését.
- Például a RHEL 6,10 esetében a kiszolgáló-Assessment jelenleg csak az RHEL 6 verziót észleli operációs rendszerként. Ennek az az oka, hogy a Hyper-V-gazdagép vCenter Server nem biztosít kernel-verziót a Linux rendszerű virtuális gépek operációs rendszerei számára.
-  Mivel az Azure csak a Linux bizonyos verzióit támogatja, a Linux rendszerű virtuális gépek jelenleg feltételesen készen állnak a kiszolgáló értékelése során.
- Megtekintheti, hogy a helyszíni virtuális gépen futó Linux operációs rendszer az Azure [Linux-támogatás](https://aka.ms/migrate/selfhost/azureendorseddistros)áttekintésével van-e jóváhagyva az Azure-ban.
-  A támogatott terjesztés ellenőrzése után figyelmen kívül hagyhatja ezt a figyelmeztetést.

Ezt a rést a VMware virtuális gépeken az [alkalmazások felderítésének](./how-to-discover-applications.md) engedélyezésével lehet megoldani. A kiszolgáló értékelése a virtuális gépről a megadott vendég hitelesítő adatok használatával észlelt operációs rendszert használja. Ez az operációsrendszer-adat a Windows és Linux rendszerű virtuális gépek esetén a megfelelő operációsrendszer-információkat azonosítja.


## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure SKU-ban nagyobb, mint a helyszíni Azure-beli virtuális gépek felmérése

Azure Migrate kiszolgáló értékelése a felmérés típusa alapján több magot és memóriát is javasolhat az Azure-beli virtuális gépekhez, mint az aktuális helyszíni foglalás:


- A VM SKU-javaslat az értékelés tulajdonságaitól függ.
- Ezt a kiszolgáló értékelése során végrehajtott értékelés típusa befolyásolja: *teljesítmény-alapú* *vagy helyszíni*.
- A teljesítmény-alapú értékelések esetében a kiszolgáló értékelése a helyszíni virtuális gépek (CPU, memória, lemez és hálózat kihasználtsága) kihasználtsági adatait veszi figyelembe a helyszíni virtuális gépekhez tartozó megfelelő virtuálisgép-SKU meghatározásához. Emellett a hatékony kihasználtság meghatározásakor is felvesz egy kényelmi tényezőt.
- A helyszíni méretezés esetén a teljesítményadatok nem számítanak, és a célként megadott SKU-t a helyszíni kiosztás alapján ajánlott használni.

Ha szeretné megmutatni, hogy ez milyen hatással lehet a javaslatokra, vessünk egy példát:

A helyszíni virtuális gép négy maggal és nyolc GB memóriával rendelkezik, 50%-os CPU-kihasználtsággal és 50%-os memória-kihasználtsággal, valamint a 1,3 megadott kényelmi tényezővel.

-  Ha az értékelés **a helyszínen**történik, az Azure-beli VM-SKU négy maggal és 8 GB memóriát ajánlott.
- Ha az értékelés teljesítmény-alapú, a processzor és a memória tényleges kihasználtsága (50%-a 4 mag * 1,3 = 2,6 magok, valamint a 8 GB-os memória * 50%-a * 1,3 = 5,3-GB memória), ajánlott a négy mag (a legközelebbi támogatott alapszám) és a nyolc GB memória (a legközelebbi támogatott memória mérete) szerinti legolcsóbb VM SKU.
- [További](concepts-assessment-calculation.md#types-of-assessments) információ az értékelés méretezéséről.

## <a name="azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure Disk SKU-ban nagyobb, mint a helyszínen egy Azure-beli VM-felmérésben

Azure Migrate kiszolgáló értékelése az értékelés típusától függően nagyobb lemezt javasolhat.
- A kiszolgáló értékelése során a lemez méretezése két értékelési tulajdonságtól függ: a méretezési feltételektől és a tárolási típustól.
- Ha a méretezési feltételek **teljesítmény-alapúak**, és a tároló típusa **automatikus**, a rendszer a lemez IOPS és átviteli értékeit veszi figyelembe a céllemez típusának (standard HDD, standard SSD vagy prémium) azonosításakor. Ezt követően a lemezből származó SKU-t ajánlott használni, és a javaslat a helyszíni lemez méretére vonatkozó követelményeket is figyelembe veszi.
- Ha a méretezési feltétel **teljesítmény-alapú**, és a tárolási típus **prémium**, az Azure-ban prémium szintű lemezes SKU-t ajánlott használni a helyszíni lemez IOPS, átviteli sebessége és mérete alapján. Ugyanazt a logikát kell használni a lemez méretezéséhez, ha a méretezési feltételek **a helyszínen** vannak, és a tárolási típus **standard HDD**, **standard SSD**vagy **prémium**.

Ha például egy helyszíni lemez 32 GB memóriával rendelkezik, de a lemez összesített olvasási és írási IOPS értéke 800 IOPS, a Server Assessment a prémium szintű lemezt javasolja (a magasabb IOPS-követelmények miatt), majd javaslatot tesz a szükséges IOPS és-méretet támogató lemezes SKU-ra. Ebben a példában a legjobb megoldást a P15-ös változat (256 GB, 1100 IOPS) adja. Bár a helyszíni lemez által igényelt méret 32 GB volt, a Server Assessment a helyszíni lemez magas IOPS követelménye miatt nagyobb lemezt javasol.

## <a name="utilized-corememory-percentage-missing"></a>Felhasznált mag/memória kihasználtsága (%) hiányzik

A kiszolgáló-értékelési jelentések "PercentageOfCoresUtilizedMissing" vagy "PercentageOfMemoryUtilizedMissing", ha a Azure Migrate berendezés nem tud teljesítményadatokat gyűjteni a megfelelő helyszíni virtuális gépekhez.

- Ez akkor fordulhat elő, ha a virtuális gépek ki vannak kapcsolva az értékelés időtartama alatt. A készülék nem tud teljesítményadatokat gyűjteni a virtuális gép számára, ha ki van kapcsolva.
- Ha csak a teljesítményszámlálók hiányoznak, és a Hyper-V virtuális gépeket próbálja értékelni, ellenőrizze, hogy a dinamikus memória engedélyezve van-e ezeken a virtuális gépeken. Csak a Hyper-V virtuális gépek ismert hibája van, amelyben egy Azure Migrate berendezés nem tudja gyűjteni a memória-kihasználtsági adatokat olyan virtuális gépek számára, amelyeken nincs engedélyezve a dinamikus memória.
- Ha a teljesítményszámlálók bármelyike hiányzik, Azure Migrate kiszolgáló értékelése visszaesik a lefoglalt magokra és a memóriára, és a virtuális gép megfelelő méretét javasolja.
- Ha az összes teljesítményszámláló hiányzik, ellenőrizze, hogy teljesülnek-e az értékeléshez szükséges port hozzáférési követelményei. További információ a [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), a [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) és a [fizikai](./migrate-support-matrix-physical.md#port-access) kiszolgáló értékeléséhez szükséges portokra vonatkozó hozzáférési követelményekről.

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Az operációs rendszer licence egy Azure-beli virtuális gép értékelésében szerepel?

Azure Migrate a kiszolgáló értékelése jelenleg csak Windows rendszerű gépek esetében veszi figyelembe az operációsrendszer-licenc költségeit. A Linux rendszerű gépek licencelési költségei jelenleg nem tekintendők.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Hogyan működik a teljesítmény-alapú méretezés az Azure-beli virtuális gépek felmérésében?

A Server Assessment folyamatosan gyűjti a helyszíni gépek teljesítményadatait, és ezek alapján tesz javaslatot az Azure-beli virtuálisgép- és lemez-termékváltozatra. [Ismerje meg](concepts-assessment-calculation.md#calculate-sizing-performance-based) a teljesítmény-alapú adatok gyűjtésének módját.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Miért van az értékelésem arra utaló figyelmeztetést mutat, hogy a fenntartott példányok érvénytelen kombinációjával lett létrehozva, a virtuális gép üzemidő és a kedvezmény (%)?
Ha a "fenntartott példányok" lehetőséget választja, a "kedvezmény (%)" és a virtuális gép üzemidő tulajdonságai nem alkalmazhatók. Mivel az értékelés a tulajdonságok érvénytelen kombinációjával lett létrehozva, a Szerkesztés és az újraszámolás gomb le lesz tiltva. Hozzon létre egy új értékelést. [További információk](https://go.microsoft.com/fwlink/?linkid=2131554).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Nem látok teljesítményadatokat a fizikai kiszolgálókon lévő egyes hálózati adapterekhez

Ez akkor fordulhat elő, ha a fizikai kiszolgálón engedélyezve van a Hyper-V-virtualizálás. Ezekben a kiszolgálókon a termékekkel kapcsolatos hézagok miatt Azure Migrate jelenleg a fizikai és a virtuális hálózati adaptereket is felfedi. A hálózati átviteli sebesség csak a felderített virtuális hálózati adaptereken van rögzítve.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>A fizikai kiszolgálóhoz ajánlott Azure VM SKU túl van méretezve

Ez akkor fordulhat elő, ha a fizikai kiszolgálón engedélyezve van a Hyper-V-virtualizálás. Ezeken a kiszolgálókon Azure Migrate jelenleg a fizikai és a virtuális hálózati adaptereket is felfedi. Ezért a nem. a felderített hálózati adapterek száma magasabb a ténylegesnél. Mivel a kiszolgáló értékelése olyan Azure-beli virtuális gépet választ, amely képes támogatni a szükséges számú hálózati adaptert, ez potenciálisan túlméretezett virtuális gépet eredményezhet. [További](./concepts-assessment-calculation.md#calculating-sizing) információ a nem hatásáról. a méretezés alatt álló hálózati adapterek. Ez egy olyan termékbeli hézag, amelyet a rendszer továbbít.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>A (z) "nem kész" készültségi kategória a fizikai kiszolgálóhoz

Előfordulhat, hogy a készültségi kategória helytelenül van megjelölve "nem üzemkész" állapotban olyan fizikai kiszolgáló esetén, amelyen engedélyezve van a Hyper-V virtualizálása. Ezekben a kiszolgálókon a termékekkel kapcsolatos hézagok miatt Azure Migrate jelenleg a fizikai és a virtuális adaptereket is felfedi. Ezért a nem. a felderített hálózati adapterek száma magasabb a ténylegesnél. A kiszolgáló értékelése mind a helyszíni, mind a teljesítmény alapú felmérésekben olyan Azure-beli virtuális gépet választ, amely támogatja a szükséges számú hálózati adaptert. Ha a hálózati adapterek száma meghaladja a 32-as értéket, a maximális szám nem. Az Azure-beli virtuális gépeken támogatott hálózati adapterek esetében a gép "nem üzemkész" jelöléssel fog megjelenni.  [További](./concepts-assessment-calculation.md#calculating-sizing) információ a nem hatásáról. a méretekben lévő hálózati adapterek.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>A fizikai kiszolgálók esetében a ténylegesnél magasabban felderített hálózati adapterek száma

Ez akkor fordulhat elő, ha a fizikai kiszolgálón engedélyezve van a Hyper-V-virtualizálás. Ezeken a kiszolgálókon Azure Migrate jelenleg a fizikai és a virtuális adaptereket is felfedi. Ezért a nem. a felderített hálózati adapterek száma magasabb a ténylegesnél.


## <a name="low-confidence-rating-on-physical-server-assessments"></a>Alacsony megbízhatósági minősítés a fizikai kiszolgálók értékelése során
A minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állása alapján van kiosztva. Ha olyan fizikai kiszolgálókon van, amelyeken engedélyezve van a Hyper-V-virtualizálás, akkor az alacsony megbízhatósági minősítést helytelenül lehet hozzárendelni a fizikai kiszolgáló értékeléséhez. Ezeken a kiszolgálókon Azure Migrate jelenleg a fizikai és a virtuális adaptereket is felfedi. A rendszer rögzíti a hálózati átviteli sebességet a felderített virtuális hálózati adaptereken, de nem a fizikai hálózati adaptereken. A fizikai hálózati adaptereken lévő adatpontok hiánya miatt a megbízhatósági minősítés az alacsony minősítést eredményező következményekkel járhat. Ez egy olyan termékbeli hézag, amelyet a rendszer továbbít.

## <a name="dependency-visualization-in-azure-government"></a>Függőségi vizualizáció a Azure Government

Azure Migrate a függőségi vizualizáció funkciójának Service Map függ. Mivel a Service Map jelenleg nem érhető el a Azure Governmentban, ez a funkció nem érhető el a Azure Governmentban.

## <a name="dependencies-dont-show-after-agent-install"></a>A függőségek nem jelennek meg az ügynök telepítése után

Miután telepítette a függőségi vizualizációs ügynököket a helyszíni virtuális gépekre, Azure Migrate általában 15-30 percet vesz igénybe, hogy megjelenjenek a függőségei a portálon. Ha több mint 30 percet várt, győződjön meg arról, hogy a Microsoft monitoring Agent (MMA) tud csatlakozni a Log Analytics munkaterülethez.

Windows rendszerű virtuális gépek esetén:
1. A Vezérlőpulton indítsa el az MMA-t.
2. A **Microsoft monitoring Agent tulajdonságok**  >  **Azure log Analytics (OMS)** területén ellenőrizze, hogy a munkaterület **állapota** zöld.
3. Ha az állapot nem zöld, próbálja meg eltávolítani a munkaterületet, és adja hozzá újra az MMA-hoz.

    ![MMA-állapot](./media/troubleshoot-assessment/mma-properties.png)

Linux rendszerű virtuális gépek esetén győződjön meg arról, hogy az MMA és a függőségi ügynök telepítési parancsai sikeresek voltak.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- **MMS-ügynök**: Tekintse át a támogatott [Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)és [Linux](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) operációs rendszereket.
- **Függőségi ügynök**: a támogatott [Windows-és Linux-](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) operációs rendszerek.

## <a name="visualize-dependencies-for--hour"></a>> óra függőségeinek megjelenítése

Az ügynök nélküli függőségek elemzésével megjelenítheti a függőségeket, és akár 30 napig is exportálhatja őket egy térképen.

Ügynök-alapú függőségi elemzéssel, bár a Azure Migrate lehetővé teszi, hogy visszalépjen egy adott dátumra az elmúlt hónapban, a függőségek megjelenítésének maximális időtartama egy óra. A függőségi Térkép időidőtartam funkciójának használatával például megtekintheti a tegnapi függőségeket, de csak egy órás időszakra megtekintheti őket. [A függőségi adat](./how-to-create-group-machine-dependencies.md) hosszabb időtartamon keresztül történő lekérdezéséhez azonban Azure monitor naplókat is használhat.

## <a name="visualized-dependencies-for--10-machines"></a>Vizualizációs függőségek > 10 gép esetén

Azure Migrate Server Assessment esetében az ügynök-alapú függőségi elemzéssel akár 10 virtuális géppel rendelkező [csoportok függőségeit is megjelenítheti](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) . Nagyobb csoportok esetén azt javasoljuk, hogy a függőségek megjelenítéséhez ossza fel a virtuális gépeket kisebb csoportokra.


## <a name="machines-show-install-agent"></a>Gépek "telepítési ügynök"

Miután az Azure-ba engedélyezte a függőségi vizualizációval rendelkező gépek áttelepítését, az alábbi viselkedés miatt előfordulhat, hogy a gépek "ügynök telepítése" műveletet mutatnak a "függőségek megtekintése" helyett.


- Az Azure-ba való Migrálás után a helyszíni gépek ki vannak kapcsolva, és az egyenértékű virtuális gépek az Azure-ban vannak. Ezek a gépek eltérő MAC-címeket vásárolnak.
- A gépek más IP-címmel is rendelkezhetnek, attól függően, hogy megtartotta-e a helyszíni IP-címet.
- Ha a MAC és az IP-címek is eltérnek a helyi környezettől, Azure Migrate nem rendeli hozzá a helyszíni gépeket a Service Map függőségi adatokhoz. Ebben az esetben az ügynököt a függőségek megtekintése helyett az ügynök telepítésére fogja látni.
- Az Azure-ba való áttelepítést követően a helyszíni gépek továbbra is a várt módon lesznek bekapcsolva. Az Azure-ban megjelenő egyenértékű gépek eltérő MAC-címet igényelnek, és különböző IP-címeket is megvásárolhatnak. Hacsak nem tiltja le a kimenő Azure Monitor ezen gépekről érkező forgalmat, Azure Migrate nem rendeli hozzá a helyszíni gépeket semmilyen Service Map függőségi adatokhoz, így a függőségek megtekintése helyett az ügynökök telepítésének lehetőségét fogja látni.

## <a name="dependencies-export-csv-shows-unknown-process"></a>A CSV-exportálási függőségek az "ismeretlen folyamat" kifejezést mutatják
Az ügynök nélküli függőségek elemzésében a folyamat neveit a rendszer a legjobb erőfeszítést követően rögzíti. Bizonyos helyzetekben, bár a forrás-és a célkiszolgáló neve és a célport is rögzítve van, nem valósítható meg a függőségek mindkét végén található folyamat neve. Ilyen esetekben a folyamat "ismeretlen folyamat" jelöléssel van megjelölve.


## <a name="capture-network-traffic"></a>Hálózati forgalom rögzítése

Gyűjtsön hálózati forgalmi naplókat a következőképpen:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyomja meg az F12 billentyűt Fejlesztői eszközök elindításához. Ha szükséges, törölje a jelet a navigációs beállításban szereplő **bejegyzések törlése** elemre.
3. Válassza a **hálózat** fület, és indítsa el a hálózati forgalom rögzítését:
   - A Chrome-ban válassza a **napló megőrzése**lehetőséget. A rögzítésnek automatikusan el kell indulnia. A piros kör azt jelzi, hogy a forgalom rögzítése folyamatban van. Ha a piros kör nem jelenik meg, válassza ki az elindulni kívánt fekete kört.
   - A Microsoft Edge-ben és az Internet Explorerben a rögzítés automatikusan elindul. Ha nem, kattintson a zöld Lejátszás gombra.
4. Próbálja megismételni a hibát.
5. Miután észlelte a hibát a rögzítés során, állítsa le a rögzítést, és mentse a rögzített tevékenység másolatát:
   - A Chrome-ban kattintson a jobb gombbal, és válassza a **Save as har a tartalommal**lehetőséget. Ez a művelet tömöríti és exportálja a naplókat. har-fájlként.
   - A Microsoft Edge vagy az Internet Explorerben válassza a **rögzített forgalom exportálása** lehetőséget. Ez a művelet tömöríti és exportálja a naplót.
6. Válassza a **konzol** fület a figyelmeztetések és hibák kereséséhez. A konzol naplójának mentése:
   - A Chrome-ban kattintson a jobb gombbal a konzol naplójában bárhová. Válassza a **Mentés másként**lehetőséget, exportálja és zip-ként a naplót.
   - A Microsoft Edge vagy az Internet Explorerben kattintson a jobb gombbal a hibákra, és válassza az **összes másolása**lehetőséget.
7. Fejlesztői eszközök bezárásához.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Hol található az értékelés során az operációs rendszerre vonatkozó adatok?

- A VMware virtuális gépek esetében alapértelmezés szerint a vCenter által biztosított operációsrendszer-információ. 
   - VMware Linux rendszerű virtuális gépek esetén, ha az alkalmazás felderítése engedélyezve van, az operációs rendszer részletes adatokat olvas be a vendég virtuális gépről. Annak ellenőrzéséhez, hogy az értékelés melyik operációs rendszerre vonatkozó részleteit használja, lépjen a felderített kiszolgálók nézetre, és az "operációs rendszer" oszlopban szereplő érték fölé. A felugró szövegben láthatja, hogy a megjelenő operációsrendszer-adatok a vCenter-kiszolgálóról vagy a vendég virtuális gépről a virtuális gép hitelesítő adataival vannak-e gyűjtve. 
   - Windows rendszerű virtuális gépek esetén az operációs rendszer részletes adatait mindig a vCenter Server olvashatja.
- Hyper-V virtuális gépek esetén az operációs rendszer adatait a Hyper-V gazdagépről gyűjti a rendszer.
- Fizikai kiszolgálók esetében a rendszer beolvassa a kiszolgálót a kiszolgálóról.

## <a name="next-steps"></a>Következő lépések

Értékelés [létrehozása](how-to-create-assessment.md) vagy [testreszabása](how-to-modify-assessment.md) .
