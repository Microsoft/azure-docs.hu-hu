---
title: Értékelési számítások az Azure Migrate |} A Microsoft Docs
description: Értékelési számítások az Azure Migrate szolgáltatás áttekintése.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 012a352b00de2e2d1bf64fd18125ddd10faba5cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679119"
---
# <a name="assessment-calculations"></a>Értékelési számítások

[Az Azure Migrate](migrate-overview.md) felméri a helyszíni számítási feladatokat az Azure-ba való migrálásra. Ez a cikk ismerteti, értékelések számításával.


## <a name="overview"></a>Áttekintés

Az Azure Migrate értékelés három fázisa van. Értékelés egy alkalmasságát elemzés, a méretezés, kiegészítve előtaggal kezdődik, és végül egy-egy havi költségbecsléshez. A gép csak áthelyezi egy későbbi szakaszban Ha átadja az előzőre. Például egy gép az Azure-megfelelőségi ellenőrzés meghiúsul, ha meg van jelölve, nem alkalmasak az Azure, és a méretezési és költségszámítási nem történik meg.

## <a name="azure-suitability-analysis"></a>Azure-megfelelőségi elemzés

Nem minden gépek kiválóan alkalmasak a felhőben futó, a felhő rendelkezik a saját korlátokat és követelményeket. Az Azure Migrate értékeli az egyes helyszíni gépek migrálásra való alkalmasságukat az Azure-ba, és kategorizálja a gépek az alábbi kategóriák valamelyikébe:
- **Azure-beli használatra kész** – a gép is telepíthető-módosítás nélkül az Azure-ban van. Teljes Azure-támogatással az Azure-ban indul.
- **Feltételesen készen áll az Azure-ban** – a gép elindulhat az Azure-ban, de nem rendelkezik teljes körű Azure-támogatás. Például olyan gépeken, Windows Server operációs rendszer régebbi verziója nem támogatott az Azure-ban. Ezek a gépek Azure-ba való migrálás előtt legyen óvatos, és kövesse a szervizelési az értékelésben készültségi problémák megoldásához áttelepítése előtt ajánlott útmutatás szerint kell.
- **Nem áll készen az Azure-ban** – a gép nem indul el az Azure-ban. Például ha egy helyszíni gép méretű több mint 4 TB-os csatlakoztatott lemez rendelkezik, akkor nem futhat az Azure-ban. Az értékelésben a készültségi probléma megoldásához az Azure-ba való migrálás előtt javasolt szervizelés kövessék kell. Megfelelő méretezéséhez és költségbecslés nem történik a gépekhez, amely nem áll készen az Azure-hoz vannak megjelölve.
- **A kompatibilitás ismeretlen** – Azure Migrate nem található a gépen, mert nincs elég adat érhető el a vCenter Server készen áll-e.

Az Azure Migrate áttekinti a virtuálisgép-tulajdonságokat, és a vendég operációs rendszer azonosításához az Azure készen áll-e a helyszíni gépen.

### <a name="machine-properties"></a>Virtuálisgép-tulajdonságokat
Az Azure Migrate áttekinti a következő tulajdonságok azonosítani, hogy egy virtuális Gépet az Azure-ban futtatható-e a helyszíni virtuális gép.

**Tulajdonság** | **Részletek** | **Azure-kompatibilitás állapota**
--- | --- | ---
**Rendszerindítás típusa** | Az Azure virtuális gépek a BIOS-ban, és nem UEFI típusú rendszerindítást támogatja. | Feltételesen készen áll arra, ha UEFI típusú rendszerindítást.
**Processzormagok** | A magok számát a a gépek mag (128 mag), egy Azure virtuális gép támogatott maximális számának kisebbnek vagy azzal egyenlőnek kell lennie.<br/><br/> Teljesítményelőzmények érhető el, ha az Azure Migrate figyelembe veszi a túl magas kihasználtsággal rendelkező magok, az összehasonlítást. Ha egy kompatibilitási faktor értékelés beállításaiban van megadva, a rendszer megszorozza a túl magas kihasználtsággal rendelkező magok számát a kompatibilitási faktor.<br/><br/> Ha nincsenek teljesítményelőzményei, a kompatibilitási faktor alkalmazása nélkül az Azure Migrate használja a lefoglalt magok száma. | Készen áll, ha kevesebb mint vagy egyenlő korlátok.
**Memória** | A memória mérete kisebb, mint a maximális memóriát, vagy azzal egyenlőnek kell lennie. (az Azure M sorozatú Standard_M128m 3892 GB&nbsp;<sup>2</sup>) egy Azure virtuális gép engedélyezett. [További információk](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Teljesítményelőzmények érhető el, ha az Azure Migrate figyelembe veszi a magas kihasználtságú memóriát, az összehasonlítást. Ha egy kompatibilitási faktor van megadva, a rendszer megszorozza a magas kihasználtságú memória a kompatibilitási faktor.<br/><br/> Ha nincsenek előzményei a lefoglalt memória használata esetén a kompatibilitási faktor alkalmazása nélkül.<br/><br/> | Készen áll a korlátokon belül.
**Tárolólemez** | A lemez lefoglalt mérete 4 TB-ig (4096 GB) kell lennie, vagy kisebb.<br/><br/> A géphez csatolt lemezek száma 65 vagy annál kisebb, többek között az operációsrendszer-lemez kell lennie. | Készen áll a korlátokon belül.
**Hálózat** | Egy gép 32 kell rendelkeznie, vagy kevesebb hálózati adapter csatlakozik. | Készen áll a korlátokon belül.

### <a name="guest-operating-system"></a>Vendég operációs rendszer
Virtuális gép tulajdonságait, valamint is az Azure Migrate megvizsgálja a vendég operációs rendszer a helyszíni virtuális gép azonosításához, ha a virtuális gép az Azure-on futtatható.

> [!NOTE]
> Az Azure Migrate úgy véli, hogy a vCenter-kiszolgáló a következő elemzéseket végezhet a megadott operációs rendszer. Mivel az Azure Migrate által végzett felderítés készülék-alapú, úgy ellenőrizheti, hogy a virtuális gépen futó operációs rendszer megegyezik-e az egyik megadott vCenter-kiszolgáló nem rendelkezik.

A következő logikai Azure Migrate által azonosítására szolgál az Azure készen áll-e a virtuális Gépet az operációs rendszer alapján.

**Operációs rendszer** | **Részletek** | **Azure-kompatibilitás állapota**
--- | --- | ---
A Windows Server 2016 és az összes szervizcsomagok | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
A Windows Server 2012 R2 és az összes szervizcsomagok | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
A Windows Server 2012 és az összes szervizcsomagok | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
A Windows Server 2008 R2 minden szervizcsomagok | Az Azure teljes körű támogatást biztosít.| Készen áll az Azure-beli használatra
A Windows Server 2008 (32 bites és 64 bites) | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
A Windows Server 2003, Server 2003 R2 | Ezek az operációs rendszerek megfeleltek támogatása, és meg kell azok végén egy [egyéni támogatási megállapodás (CSA)](https://aka.ms/WSosstatement) támogatás az Azure-ban. | Feltételesen készen áll az Azure-hoz, fontolja meg az operációs rendszer frissítését az Azure-ba való migrálás előtt.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Az említett operációs rendszerektől azok befejezési támogatása estek át, a gép elindulhat az Azure-ban, de nem az operációs rendszer biztosít támogatást az Azure-ban. | Feltételesen készen az Azure-hoz, javasolt az Azure-ba való migrálás előtt az operációs rendszer verziófrissítéséhez.
Windows-ügyfél 7, 8 és 10 | Az Azure-támogatást kínál [csak a Visual Studio-előfizetéssel.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Feltételesen készen áll az Azure-beli használatra
Windows 10 Pro asztali | Az Azure-támogatást kínál [több-Bérlős üzemeltetési jogosultságok.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Feltételesen készen áll az Azure-beli használatra
Windows Vista, XP Professional | Az említett operációs rendszerektől azok befejezési támogatása estek át, a gép elindulhat az Azure-ban, de nem az operációs rendszer biztosít támogatást az Azure-ban. | Feltételesen készen az Azure-hoz, javasolt az Azure-ba való migrálás előtt az operációs rendszer verziófrissítéséhez.
Linux | Azure vannak jóváhagyva, ezek [Linux operációs rendszerek](../virtual-machines/linux/endorsed-distros.md). Más Linux operációs rendszerek elindulhat az Azure-ban, de javasoljuk, hogy az operációs rendszer frissítése egy támogatott verzióra az Azure-ba való migrálás előtt. | Készen áll az Azure-hoz, ha a verzió támogatja.<br/><br/>Feltételesen készen áll arra, ha a verzió nem támogatja.
Más operációs rendszerek<br/><br/> Példa: Oracle Solaris, az Apple Mac OS stb., a FreeBSD stb. | Az Azure nem támogat nyíltan az említett operációs rendszerektől. A gép elindulhat az Azure-ban, de nem az operációs rendszer biztosít támogatást az Azure-ban. | Feltételesen készen az Azure-hoz, javasoljuk, hogy egy támogatott operációs rendszer telepítése az Azure-ba való migrálás előtt.  
Az operációs rendszer megadott **más** a vCenter Serverben | Az Azure Migrate ebben az esetben az operációs rendszer nem tudja azonosítani. | Ismeretlen készültségi. Győződjön meg arról, hogy a virtuális gépen futó operációs rendszer támogatott az Azure-ban.
32 bites operációs rendszerek | A gép elindulhat az Azure-ban, de az Azure nem rendelkezhetnek teljes körű támogatást. | Feltételesen készen áll az Azure-hoz, érdemes lehet frissíteni a gép operációs rendszerének 32 bites operációs Rendszereken a 64 bites operációs Rendszereken az Azure-ba való migrálás előtt.

## <a name="sizing"></a>Méretezés

Után a gép van megjelölve, készen áll az Azure-hoz, az Azure Migrate úgy méretezi a virtuális gép és annak lemezeire az Azure-hoz. Ha a az értékelési tulajdonságokban meghatározott méretezési feltétel teljesítményalapú tennie, az Azure Migrate teljesítményelőzményeit a gép azonosításához az Azure-beli virtuális gép méretét és a lemez típusa. Ez a módszer hasznos forgatókönyvekben, ahol túlságosan foglalt le a helyszíni virtuális gép, de a kihasználtság alacsony, és szeretné méretezése a virtuális gépek az Azure-ban csökkenthetők a költségek.

Ha nem szeretné, hogy Virtuálisgép-méretezési teljesítményelőzményeinek figyelembe venni, és szeretné állítani a virtuális gép,-van az Azure-ba, adja meg a méretezési feltétel teljesítményalapú, *helyszíni* és az Azure Migrate majd méretezheti a virtuális gépeket a helyszíni alapján a konfiguráció a kihasználtsági adatok figyelembe vétele nélkül. Lemez átméretezése, ebben az esetben akkor történik, a tárolótípus (standard szintű lemezes vagy prémium szintű lemezt) az értékelés tulajdonságaiban megadott alapján

### <a name="performance-based-sizing"></a>Teljesítményalapú

Teljesítményalapú méretezéshez az Azure Migrate kezdődik a lemezeket, a virtuális Géphez csatolt hálózati adapterek követ, és majd maps egy Azure virtuális gép számítási követelményei alapján a helyszíni virtuális gép.

- **Tárolási**: Az Azure Migrate megkísérli minden, a gép az Azure-ban lemez csatlakoztatott lemez.

    > [!NOTE]
    > Az Azure Migrate támogatja a csak managed disks értékeléshez.

    - A hatékony lemez i/o másodpercenként (IOPS) és az átviteli sebesség (MB/s) lekéréséhez az Azure Migrate szorozza meg a lemez iops-t és az átviteli sebességet a kompatibilitási faktor együtt. A hatékony IOPS és átviteli sebesség értékek alapján, ha a lemez le kell képezni egy standard vagy prémium szintű lemezt az Azure-ban az Azure Migrate azonosítja.
    - Az Azure Migrate nem található a szükséges iops-t és az átviteli sebesség lemezzel, ha azt nem alkalmasak a géphez jelöli meg az Azure-hoz. [További](../azure-subscription-service-limits.md#storage-limits) Azure-ral kapcsolatos korlátozza a lemez és a virtuális gépek száma.
    - Ha megtalálta a megfelelő lemezek egy készlete, az Azure Migrate kiválasztja azokat, amelyek támogatják a storage-redundancia módját, valamint az értékelési beállítások megadott helyen.
    - Ha több jogosult lemezek vannak, a legalacsonyabb költséget rendelkezőt választja ki.
    - Ha a lemezek, a teljesítménnyel kapcsolatos adatai nem érhető el, az összes lemez vannak leképezve az Azure standard szintű lemezek.

- **Hálózati**: Az Azure Migrate megpróbálja megkeresni egy Azure virtuális Gépen, hogy a helyi géphez csatlakoztatott hálózati adapterek és a teljesítmény, ezek a hálózati adapterek által igényelt támogatja.
    - A helyszíni virtuális gép a tényleges hálózati teljesítmény eléréséhez, az Azure Migrate összesíti az összes hálózati adapteren másodpercenként (MBps) kívül a géphez (hálózati ki), továbbított adatokat, és a kompatibilitási faktor vonatkozik. Ez a szám egy Azure virtuális gép támogatására képes a szükséges hálózati teljesítménynek kereséséhez használt.
    - Hálózati teljesítmény, valamint azt is figyelembe veszi az Azure virtuális gép támogatják-e a szükséges hálózati adapterek száma.
    - Ha nincs hálózati teljesítménnyel adatai nem érhető el, a csak a hálózati adapterek száma a Virtuálisgép-méretezési számít.

- **COMPUTE**: Tárolási és hálózati követelményeinek kiszámítása, miután az Azure Migrate figyelembe veszi a Processzor- és követelmények található Virtuálisgép-méret az Azure-ban.
    - Az Azure Migrate megvizsgálja a túl magas kihasználtsággal rendelkező magok és a memória, és a hatékony magok és a memória a kompatibilitási faktor vonatkozik. Adott száma alapján, azt próbál meg megtalálni egy Virtuálisgép-méret az Azure-ban.
    - Ha nem megfelelő méretű található, a gép van megjelölve nem alkalmasak az Azure-hoz.
    - Ha egy megfelelő méretű található, a tárolási és hálózatkezelési számítások az Azure Migrate vonatkozik. Ezt követően végrehajtja, helyét és a tarifacsomag-beállításokat, a virtuális gép mérete végleges javaslatot.
    - Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.

### <a name="as-on-premises-sizing"></a>Helyszíni méretezése
Ha a méretezési feltétel teljesítményalapú *helyszíni méretezési*, az Azure Migrate nem veszi figyelembe a lemezek és virtuális gépek korábbi teljesítménye és a egy Virtuálisgép-Termékváltozatra, az Azure-ban a helyszínen lefoglalt méret alapján foglalja le. Ehhez hasonlóan az lemez méretezés, azt a tárolótípus (Standard vagy prémium) értékelés tulajdonságaiban megadott megvizsgál, és javasolja a lemez típusát ennek megfelelően. Alapértelmezett tárolási típus prémium szintű lemezeket.

### <a name="confidence-rating"></a>Megbízhatósági minősítés
Az Azure Migrate minden teljesítményalapú értékelése olyan megbízhatósági minősítéssel van társítva, amely 1 csillagtól az 5 csillagig terjed (az 1 csillag a legalacsonyabb, az 5 csillag pedig a legmagasabb). A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve. Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát. A megbízhatósági minősítés nem vonatkozik a helyszíni értékelésekre.

A teljesítményalapú méretezéshez az Azure Migrate-nek szüksége van a virtuális gép processzorának és memóriájának kihasználtsági adataira. Emellett szükség van a lemez IOPS-értékére és az adatátviteli teljesítményre is a virtuális géphez csatlakoztatott minden lemezre vonatkozóan. Ugyanígy az Azure Migrate-nek a virtuális géphez csatlakoztatott összes hálózati adapterre vonatkozóan szüksége van a bejövő és kimenő hálózati forgalom adataira a teljesítményalapú méretezés elvégzéséhez. Ha a fenti kihasználtsági számok valamelyike nem érhető el a vCenter Serveren, lehet, hogy az Azure Migrate által adott méretjavaslat nem megbízható. Az elérhető adatpontok százalékától függően meg van adva a megbízhatósági minősítés az értékeléshez az alábbiak szerint:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag

   Az okok, az alábbiakban a vannak vonatkozóan miért értékelés kérhetők le egy alacsony megbízhatósági minősítés:

- Nem végzett profilkészítést a környezeten abban az időtartamban, amelyre az értékelést létrehozta. Ha például létrehoz egy teljesítményértékelést 1 napra állított időtartammal, akkor a felderítés indítását követően legalább egy napot várnia kell arra, hogy az összes adatpont be legyen gyűjtve.

- Néhány virtuális gép le lett állítva abban az időszakban, amelyhez az értékelést számította. Ha valamelyik virtuális gép egy ideig ki volt kapcsolva, nem fogjuk tudni begyűjteni az adott időszak teljesítményadatait.

- Néhány virtuális gép létrejött abban az időszakban, amelyhez az értékelést számította. Ha például az utolsó egy hónap teljesítményelőzményeinek értékelését hozza létre, de néhány virtuális gép csak egy hete jött létre a környezetben. Ilyen esetekben az új virtuális gépeknek nincsenek teljesítményelőzményei a teljes időtartamhoz.

  > [!NOTE]
  > Ha valamely értékelés megbízhatósági minősítése 5 csillag alatt van, azt javasoljuk, hogy Várjon, amíg legalább egy napot a berendezés-profil a környezetre, majd *újraszámítása* az értékelést. Ha ez nem végezhető el, akkor lehet, hogy a teljesítményalapú méretezés nem megbízható, és azt javasoljuk, hogy az értékelés tulajdonságainak módosításával váltson *helyszíni méretezésre*.

## <a name="monthly-cost-estimation"></a>Havi Költségbecslés

Méretezési javaslatok befejezése után az Azure Migrate számítja ki az áttelepítés utáni számítási és tárolási költségeit.

- **Számítási költségeit tartalmazza**: Használja az Azure virtuális gép ajánlott mérete, az Azure Migrate használatával a számlázási API-t a havi költségek kiszámítása a virtuális gép. A kiszámítása az operációs rendszer, frissítési garanciával rendelkező, a fenntartott példányok, virtuális gép Üzemidő, helye és pénznem beállítások figyelembe vesz igénybe. A költségek gyűjti az összes gépen, a teljes havi számítási költség kiszámítása.
- **Tárolási költségek**: A havi költségét a géphez csatlakoztatott összes lemez összevonása gép havi tárolási költségek kiszámításához. Az Azure Migrate a tárolási költségeket összes gép összesítésével számítja ki a teljes havi tárolási költségeket. A számítási jelenleg nem használ a felmérés beállításaiban figyelembe ajánlatok.

Az értékelési beállítások megadott pénznem költségek jelenik meg.


## <a name="next-steps"></a>További lépések

[Hozzon létre egy értékelést a helyszíni VMware virtuális gépekhez](tutorial-assessment-vmware.md)
