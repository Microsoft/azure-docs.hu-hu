---
title: Az architektúra áttekintése
description: Áttekintést nyújt a Azure Backup szolgáltatás által használt architektúráról, összetevőkről és folyamatokról.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 1e5a61bd4e3287c1100ff1f54fda797c1add438b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466411"
---
# <a name="azure-backup-architecture-and-components"></a>Architektúra és összetevők Azure Backup

A [Azure Backup szolgáltatással](backup-overview.md) biztonsági másolatot készíthet az Microsoft Azure Cloud platformra. Ez a cikk Azure Backup architektúrát, összetevőket és folyamatokat foglalja össze.

## <a name="what-does-azure-backup-do"></a>Mit tesz Azure Backup?

Azure Backup biztonsági másolatot készít az adatokról, a gépi állapotról és a helyszíni gépeken és az Azure-beli virtuálisgép-példányokon futó munkaterhelésekről. Számos Azure Backup forgatókönyv.

## <a name="how-does-azure-backup-work"></a>Hogyan működik Azure Backup?

Több módszer használatával biztonsági mentést készíthet a gépekről és az adatairól:

- Helyszíni **gépek biztonsági mentése**:
  - A helyszíni Windows-gépek biztonsági mentését közvetlenül az Azure-ba végezheti a Azure Backup Microsoft Azure Recovery Services (MARS) ügynök használatával. A Linux rendszerű gépek nem támogatottak.
  - A helyszíni gépek biztonsági mentését a System Center Data Protection Manager (DPM) vagy a Microsoft Azure Backup Server (MABS) szolgáltatással végezheti el. Ezután biztonsági mentést készíthet a Backup-kiszolgálóról egy Recovery Services-tárolóra az Azure-ban.

- **Azure-beli virtuális gépek biztonsági mentése**:
  - Az Azure-beli virtuális gépek biztonsági mentését közvetlenül is elvégezheti. Azure Backup telepít egy biztonsági mentési bővítményt a virtuális gépen futó Azure VM-ügynökre. Ez a bővítmény biztonsági mentést készít a teljes virtuális gépről.
  - A MARS-ügynök futtatásával biztonsági mentést készíthet az Azure-beli virtuális gépen lévő adott fájlokról és mappákról.
  - Az Azure-beli virtuális gépekről biztonsági mentést készíthet az Azure-ban futó MABS, és biztonsági mentést készíthet a MABS egy Recovery Services-tárolóra.

További információ a biztonsági mentésről [és a](backup-overview.md) [támogatott biztonsági mentési forgatókönyvekről](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Hol történik a biztonsági másolat készítése?

A Azure Backup tárolja a biztonsági másolatban tárolt adattárakat – Recovery Services tárolók és mentési tárolók. A tároló egy olyan online tárolási entitás az Azure-ban, amely az adattároláshoz, például biztonsági másolatok, helyreállítási pontok és biztonsági mentési szabályzatok tárolására szolgál.

A tárolók a következő funkciókkal rendelkeznek:

- A tárolók megkönnyítik a biztonsági másolatok rendszerezését, miközben minimálisra csökkentik a felügyeleti terhelést.
- Megfigyelheti a tárolóban lévő biztonsági másolati elemeket, beleértve az Azure-beli virtuális gépeket és a helyszíni gépeket is.
- A tár hozzáférését az [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)kezelheti.
- Megadhatja, hogyan replikálja a rendszer a tárolóban lévő adattárakat:
  - **Helyileg redundáns tárolás (LRS)**: az adatközpontok meghibásodása elleni védelem érdekében a LRS-t használhatja. A LRS replikálja az adatmennyiséget egy tárolási méretezési egységbe. [További információk](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Geo-redundáns tárolás (GRS)**: az egész régióra kiterjedő kimaradások elleni védelemhez használhatja a GRS. A GRS egy másodlagos régióba replikálja az adatait. [További információk](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Zone-redundáns tárolás (ZRS)**: a [rendelkezésre állási zónákban](../availability-zones/az-overview.md#availability-zones)replikálja az adatait, így garantálva az adattárolást és a rugalmasságot ugyanabban a régióban. [További információ](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Recovery Services tárolók alapértelmezés szerint a GRS használják.

Recovery Services-tárolók a következő további funkciókkal rendelkeznek:

- Az egyes Azure-előfizetésekben akár 500 tárolót is létrehozhat.

## <a name="backup-agents"></a>Biztonsági mentési ügynökök

A Azure Backup különböző biztonsági mentési ügynököket biztosít, attól függően, hogy milyen típusú gépet készít biztonsági mentésre:

**Ügynök** | **Részletek**
--- | ---
**MARS-ügynök** | <ul><li>A fájlok, mappák és a rendszerállapot biztonsági mentésére az egyes helyszíni Windows Server-gépeken fut.</li> <li>A fájlok, mappák és a rendszerállapot biztonsági mentésére az Azure-beli virtuális gépeken fut.</li> <li>A DPM/MABS-kiszolgálókon futtatja a DPM/MABS helyi tároló lemezét az Azure-ba történő biztonsági mentéshez.</li></ul>
**Azure VM-bővítmény** | Az Azure-beli virtuális gépeken futtatja őket egy tárolóba.

## <a name="backup-types"></a>Biztonsági másolatok típusai

A következő táblázat ismerteti a különböző típusú biztonsági mentéseket és azok használatát:

**Biztonsági mentés típusa** | **Részletek** | **Használat**
--- | --- | ---
**Összes** | A teljes biztonsági mentés a teljes adatforrást tartalmazza. Nagyobb hálózati sávszélességet vesz igénybe, mint a különbözeti vagy növekményes biztonsági másolatok. | A kezdeti biztonsági mentéshez használatos.
**Differenciál** |  A különbözeti biztonsági mentés a kezdeti teljes biztonsági mentés óta módosult blokkokat tárolja. Kisebb hálózati és tárolási kapacitást használ, és nem tartja meg a változatlanul módosított adatmennyiségek redundáns másolatait.<br/><br/> Nem hatékony, mert a későbbi biztonsági másolatok között változatlanul megjelenő adatblokkok átvitele és tárolása történik. | Azure Backup nem használja.
**Növekvő** | A növekményes biztonsági mentés csak azokat az adatblokkokat tárolja, amelyek az előző biztonsági mentés óta módosultak. Magas szintű tárolási és hálózati hatékonyság. <br/><br/> A növekményes biztonsági mentéssel nem kell kiegészíteni a teljes biztonsági mentést. | A DPM/MABS használja a lemezes biztonsági mentésekhez, és az Azure-ban minden biztonsági másolatban használatos. Nem használatos SQL Server biztonsági mentéshez.

## <a name="sql-server-backup-types"></a>SQL Server biztonsági mentési típusok

A következő táblázat ismerteti a SQL Server-adatbázisokhoz használt biztonsági másolatok különböző típusait, valamint azt, hogy milyen gyakran használják őket:

**Biztonsági mentés típusa** | **Részletek** | **Használat**
--- | --- | ---
**Teljes biztonsági mentés** | A teljes adatbázis biztonsági mentése a teljes adatbázisról készít biztonsági másolatot. Egy adott adatbázisban vagy fájlcsoportok vagy-fájlokban lévő összes adathalmazt tartalmazza. A teljes biztonsági mentés elegendő naplót is tartalmaz az adatok helyreállításához. | A legtöbb esetben naponta egy teljes biztonsági mentést indíthat.<br/><br/> A teljes biztonsági mentést napi vagy heti időközönként is elvégezheti.
**Különbségi biztonsági mentés** | A különbözeti biztonsági mentés a legutóbbi, korábbi teljes adatbiztonsági mentésen alapul.<br/><br/> Csak a teljes biztonsági mentés óta megváltoztatott adatmennyiséget rögzíti. |  Legfeljebb napi egy különbözeti biztonsági mentést kezdeményezhet.<br/><br/> A teljes biztonsági mentés és a különbözeti biztonsági másolat nem konfigurálható ugyanazon a napon.
**Tranzakciónapló biztonsági mentése** | A napló biztonsági mentése lehetővé teszi, hogy egy adott másodpercen belül egy adott időpontra történő visszaállítást biztosítson. | Legfeljebb 15 percenként állíthatja be a tranzakciós napló biztonsági mentését.

### <a name="comparison-of-backup-types"></a>A biztonsági mentési típusok összehasonlítása

A tárterület-használat, a helyreállítási időre vonatkozó célkitűzés (RTO) és a hálózati felhasználás minden egyes biztonsági mentési típus esetében eltérő lehet. A következő képen a biztonsági mentési típusok összehasonlítása látható:

- Az A adatforrás 10 tárolási blokkból (a1-A10) áll, amelyekről havonta készül biztonsági másolat.
- Az A2, az A3, az A4 és az A9 módosul az első hónapban, az A5 pedig a következő hónapban.
- A különbözeti biztonsági mentések esetén a második hónapban az a2, a3, a4 és A9 blokkokat biztonsági mentéssel készítettük. A harmadik hónapban a módosított A5 blokkal együtt biztonsági másolat készül ugyanezekről a blokkokról. A következő teljes biztonsági mentésig minden alkalommal készül biztonsági másolat a módosított blokkokról.
- A növekményes biztonsági másolatok esetén a második hónapban az a2, a3, a4 és A9 blokkokat a rendszer módosítottként jelöli meg, és áthelyezi őket. A harmadik hónapban csak az A5 módosított blokk van megjelölve és továbbítva.

![A biztonsági mentési módszerek összehasonlítását bemutató kép](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Biztonsági mentési funkciók

A következő táblázat összefoglalja a különböző típusú biztonsági másolatok támogatott funkcióit:

**Szolgáltatás** | **Fájlok és mappák közvetlen biztonsági mentése (a MARS-ügynök használatával)** | **Azure-beli virtuális gép biztonsági mentése** | **Gépek vagy alkalmazások DPM/MABS**
--- | --- | --- | ---
Biztonsági mentés a tárba | ![Igen][green] | ![Igen][green] | ![Yes][green]
Biztonsági mentés DPM-vagy MABS-lemezre, majd az Azure-ba | | | ![Yes][green]
A biztonsági mentéshez továbbított adatok tömörítése | ![Yes][green] | Az adatátvitelkor nem használ tömörítést. A tárterület kis mértékben van kiemelve, de a helyreállítás gyorsabb.  | ![Yes][green]
Növekményes biztonsági mentés futtatása |![Igen][green] |![Igen][green] |![Yes][green]
Deduplikált lemezek biztonsági mentése | | | ![Részlegesen][yellow]<br/><br/> Csak a helyszínen üzembe helyezett DPM-/MABS-kiszolgálók esetében.

![Tábla kulcsa](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Biztonsági mentési szabályzat alapjai

- A biztonsági mentési szabályzatot tárolóként hozza létre a rendszer.
- Biztonsági mentési szabályzat hozható létre a következő számítási feladatok biztonsági mentéséhez: Azure-beli virtuális gépek, SQL Azure-beli virtuális gépeken, Azure-beli virtuális gépek és Azure-fájlmegosztás SAP HANA. A fájlok és mappák biztonsági mentésének szabályzata a Mars-ügynök használatával van megadva a MARS-konzolon.
  - Azure-fájlmegosztás
- Egy szabályzat több erőforráshoz is hozzárendelhető. Az Azure-beli virtuális gépek biztonsági mentési szabályzata számos Azure-beli virtuális gép biztosítására használható.
- A szabályzat két összetevőből áll
  - Ütemezett: Mikor kell biztonsági másolatot készíteni
  - Megőrzöttség: az egyes biztonsági másolatok megőrzésének idejére.
- Az ütemterv "napi" vagy "hetente" is definiálható egy adott időpontra.
- A megőrzés a "napi", a "hetente", a "havi", az "éves" biztonsági mentési pontok esetében adható meg.
  - a "Weekly" a hét egy adott napján biztonsági mentésre utal.
  - a "havi" a hónap egy adott napján biztonsági mentést jelent.
  - az "éves" az év egy adott napján biztonsági mentésre utal.
- A "havi", "éves" biztonsági mentési pontok megőrzését hosszú távú megőrzésnek (LTR) nevezzük.
- Tároló létrehozásakor a rendszer létrehoz egy "DefaultPolicy", és az erőforrások biztonsági mentésére is használható.
- A biztonsági mentési szabályzat megőrzési időszakában bekövetkezett változások visszamenőlegesen lesznek alkalmazva az összes korábbi helyreállítási pontra az újakon kívül.

### <a name="impact-of-policy-change-on-recovery-points"></a>A helyreállítási pontok házirend-módosításának következményei

- **A megőrzési időtartam nagyobb/csökkent:** A megőrzési időtartam megváltozásakor a rendszer az új megőrzési időtartamot is alkalmazza a meglévő helyreállítási pontokra. Ennek eredményeképpen a helyreállítási pontok némelyike törlődik. Ha a megőrzési időtartam megnövekszik, a meglévő helyreállítási pontok nagyobb adatmegőrzéssel fognak rendelkezni.
- **Naponta módosítva:** Ha az ütemezett biztonsági mentések napi rendszerességgel változnak, a rendszer a meglévő napi helyreállítási pontokat takarítja meg.
- **Hetente és naponta módosítva:** A meglévő heti biztonsági mentések a jelenlegi adatmegőrzési szabályzatnak megfelelően hátralévő napok száma alapján lesznek megőrizve.

### <a name="additional-reference"></a>További referenciák

- Azure-beli VM-gép: szabályzat [létrehozása](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) és [módosítása](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) .
- Adatbázis SQL Server az Azure-beli VM-gépen: szabályzat [létrehozása](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) és [módosítása](./manage-monitor-sql-database-backup.md#modify-policy) .
- Azure file share: szabályzat [létrehozása](./backup-afs.md) és [módosítása](./manage-afs-backup.md#modify-policy) .
- SAP HANA: szabályzat [létrehozása](./backup-azure-sap-hana-database.md#create-a-backup-policy) és [módosítása](./sap-hana-db-manage.md#change-policy) .
- MARS: szabályzat [létrehozása](./backup-windows-with-mars-agent.md#create-a-backup-policy) és [módosítása](./backup-azure-manage-mars.md#modify-a-backup-policy) .
- [Vannak korlátozások a biztonsági mentés ütemezésére a számítási feladatok típusa alapján?](./backup-azure-backup-faq.md#are-there-limits-on-backup-scheduling)
- [Mi történik a meglévő helyreállítási pontokkal, ha módosítom az adatmegőrzési szabályzatot?](./backup-azure-backup-faq.md#what-happens-when-i-change-my-backup-policy)

## <a name="architecture-built-in-azure-vm-backup"></a>Architektúra: beépített Azure-beli virtuális gépek biztonsági mentése

1. Ha engedélyezi egy Azure-beli virtuális gép biztonsági mentését, a biztonsági mentés a megadott ütemterv szerint fut.
1. Az első biztonsági mentés során a rendszer egy biztonsági mentési bővítményt telepít a virtuális gépre, ha a virtuális gép fut.
    - Windows rendszerű virtuális gépekre a VMSnapshot bővítmény van telepítve.
    - Linux rendszerű virtuális gépek esetén a VMSnapshot Linux-bővítmény telepítve van.
1. A bővítmény tárolási szintű pillanatképet használ.
    - A rendszert futtató Windows rendszerű virtuális gépek esetén a biztonsági mentés koordinálja a Windows Kötet árnyékmásolata szolgáltatás (VSS) használatával, hogy egy alkalmazás-konzisztens pillanatképet készítsen a virtuális gépről. Alapértelmezés szerint a Backup teljes VSS biztonsági mentést készít. Ha a biztonsági mentés nem tudja végrehajtani az alkalmazással konzisztens pillanatképet, akkor a fájl konzisztens pillanatképet kap.
    - Linux rendszerű virtuális gépek esetén a Backup fájl-konzisztens pillanatképet készít. Az alkalmazással konzisztens Pillanatképek esetében manuálisan kell testreszabnia a parancsfájlok előtti és utáni parancsfájlokat.
    - A biztonsági mentést úgy optimalizálták, hogy az egyes virtuálisgép-lemezek párhuzamos biztonsági mentését végzi. Azure Backup beolvassa a lemezen lévő blokkokat, és csak a módosított adatokat tárolja.
1. A pillanatkép elkészítése után az adatok átkerülnek a tárolóba.
    - Csak a legutóbbi biztonsági mentés óta megváltoztatott adatblokkok másolása történik meg.
    - Az adatforgalom nincs titkosítva. Azure Backup a Azure Disk Encryption használatával titkosított Azure-beli virtuális gépek biztonsági mentését végezheti el.
    - A pillanatképek adatai nem mindig vannak azonnal a helyreállítási tárba másolva. A biztonsági mentés csúcsidőben több órát is igénybe vehet. Egy virtuális gép biztonsági mentésének időtartama a napi biztonsági mentési szabályzatok esetében 24 óránál kevesebb.
1. Az adattárolóba való elküldése után létrejön egy helyreállítási pont. Alapértelmezés szerint a pillanatképek megőrzése két nappal a Törlésük előtt történik. Ez a funkció lehetővé teszi a visszaállítási műveletet ezekből a pillanatképekről, így lecsökkentve a visszaállítási időpontokat. Ez csökkenti az adatoknak a tárból való visszaalakításához és másolásához szükséges időt. Lásd: [Azure Backup azonnali visszaállítási képesség](./backup-instant-restore-capability.md).

Nem kell explicit módon engedélyeznie az internetkapcsolatot az Azure-beli virtuális gépek biztonsági mentéséhez.

![Azure-beli virtuális gépek biztonsági mentése](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektúra: helyszíni Windows Server-gépek vagy Azure-beli virtuálisgép-fájlok vagy-mappák közvetlen biztonsági mentése

1. A forgatókönyv beállításához le kell töltenie és telepítenie kell a MARS-ügynököt a gépre. Ezután kiválaszthatja a biztonsági mentést, a biztonsági mentések futtatásának idejét, valamint azt, hogy mennyi ideig tart az Azure-ban.
1. A kezdeti biztonsági mentés a biztonsági mentési beállításoknak megfelelően fut.
1. A MARS-ügynök a VSS-t használja a biztonsági mentésre kijelölt kötetek időpontra vonatkozó pillanatképének elkészítéséhez.
    - A MARS-ügynök csak a Windows rendszer írási műveletét használja a pillanatkép rögzítéséhez.
    - Mivel az ügynök semmilyen Application VSS-írót nem használ, nem rögzíti az alkalmazás-konzisztens pillanatképeket.
1. A pillanatképnek a VSS-vel való elkészítése után a MARS-ügynök létrehoz egy virtuális merevlemezt (VHD) a biztonsági mentés konfigurálásakor megadott gyorsítótár-mappában. Az ügynök az egyes adatblokkok ellenőrzőösszegeit is tárolja. Ezek a későbbi növekményes biztonsági másolatok változási blokkjának észlelésére szolgálnak.
1. A növekményes biztonsági mentések az Ön által megadott ütemezés szerint futnak, kivéve, ha igény szerinti biztonsági mentést futtatnak.
1. A növekményes biztonsági mentések során a rendszer a módosított fájlokat azonosítja, és létrehoz egy új virtuális merevlemezt. A virtuális merevlemez tömörítve és titkosítva van, majd a rendszer elküldje a tárolónak.
1. A növekményes biztonsági mentés befejeződése után az új VHD a kezdeti replikáció után létrehozott VHD-vel lesz egyesítve. Ez az egyesített VHD biztosítja a legújabb, a folyamatban lévő biztonsági mentéshez való összehasonlításhoz használt állapotot.

![Helyszíni Windows Server-gépek biztonsági mentése a MARS-ügynökkel](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektúra: biztonsági mentés a DPM/MABS

1. Telepítenie kell a DPM vagy a MABS védelmi ügynököt a védelemmel ellátni kívánt gépekre. Ezután hozzáadja a gépeket egy DPM védelmi csoporthoz.
    - A helyszíni gépek elleni védelem érdekében a DPM vagy a MABS-kiszolgálónak a helyszínen kell elhelyezkednie.
    - Az Azure-beli virtuális gépek elleni védelem érdekében a MABS-kiszolgálónak Azure-beli virtuális gépnek kell futnia az Azure-ban.
    - A DPM/MABS segítségével a biztonsági másolatok, a megosztások, a fájlok és a mappák is védhetők. A gép rendszerállapotát (operációs rendszer nélküli) is védetté teheti, és az alkalmazással kompatibilis biztonsági mentési beállításokkal biztosíthatja az adott alkalmazásokat.
1. Ha a DPM/MABS-ben egy gép vagy alkalmazás védelmét állítja be, akkor a rövid távú tárolás és az Azure online védelem érdekében biztonsági mentést kell készítenie a MABS/DPM helyi lemezre. Azt is megadhatja, hogy mikor fusson a helyi DPM-vagy MABS-tároló biztonsági mentése, és hogy mikor fusson az Azure online biztonsági mentése.
1. A védett munkaterhelés lemezét a megadott ütemtervnek megfelelően a rendszer a helyi MABS/DPM-lemezekre készíti.
1. A DPM-/MABS-lemezekről a DPM/MABS kiszolgálón futó MARS-ügynök készít biztonsági másolatot a tárolóhoz.

![DPM vagy MABS által védett gépek és számítási feladatok biztonsági mentése](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure-beli virtuális gép tárterülete

Az Azure-beli virtuális gépek lemezek használatával tárolják az operációs rendszert, az alkalmazásokat és az adatlemezeket. Minden egyes Azure-beli virtuális gépnek legalább két lemeze van: az operációs rendszer lemeze és egy ideiglenes lemez. Az Azure-beli virtuális gépek adatlemezeket is tartalmazhatnak az alkalmazásadatok számára. A lemezek VHD-ként vannak tárolva.

- A virtuális merevlemezek az Azure standard vagy Premium Storage-fiókjaiban blobként tárolódnak:
  - **Standard szintű tárterület:** Megbízható, alacsony díjszabású lemezes támogatás a késésre nem érzékeny munkaterheléseket futtató virtuális gépeken. A standard szintű tárolók standard SSD-lemezeket vagy szabványos merevlemez-meghajtókat (HDD) használhatnak.
  - **Prémium szintű Storage:** Nagy teljesítményű lemezek támogatása. Prémium szintű SSD-lemezeket használ.
- A lemezek különböző teljesítményi szintekkel rendelkeznek:
  - **Standard HDD lemez:** A HDD-k biztonsági mentése és a költséghatékony tároláshoz használatos.
  - **Standard SSD lemez:** A prémium szintű SSD-lemezek és a standard HDD-lemezek elemeit ötvözi. Konzisztens teljesítményt és megbízhatóságot biztosít, mint a HDD, de költséghatékony.
  - **Prémium SSD lemez:** Az SSD-k által támogatott, nagy teljesítményt és kis késleltetést biztosít az I/O-igényes számítási feladatokat futtató virtuális gépek számára.
- A lemezek felügyelhetők vagy nem kezelhetők:
  - Nem **felügyelt lemezek:** Virtuális gépek által használt hagyományos típusú lemezek. Ezekhez a lemezekhez létre kell hoznia egy saját Storage-fiókot, és meg kell adnia a lemez létrehozásakor. Ezután meg kell állapítania, hogyan maximalizálhatja a virtuális gépek tárolási erőforrásait.
  - **Felügyelt lemezek:** Az Azure létrehozza és kezeli a Storage-fiókokat. Megadhatja a lemez méretét és a teljesítmény szintjét, és az Azure felügyelt lemezeket hoz létre Önnek. A lemezek hozzáadása és a virtuális gépek méretezése során az Azure kezeli a Storage-fiókokat.

A lemezes tárolással és a virtuális gépek rendelkezésre álló típusaival kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Azure Managed Disks Linux rendszerű virtuális gépekhez](../virtual-machines/managed-disks-overview.md)
- [A virtuális gépek számára elérhető lemezek típusai](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Azure-beli virtuális gépek biztonsági mentése és visszaállítása Premium Storage-val

Az Azure-beli virtuális gépek biztonsági mentését a Premium Storage és a Azure Backup használatával végezheti el:

- A Premium Storage *-alapú* virtuális gépek biztonsági mentésének folyamata során a Backup szolgáltatás ideiglenes átmeneti helyet hoz létre a AzureBackup néven, a Storage-fiókban. Az előkészítési hely mérete megegyezik a helyreállítási pont pillanatképének méretével.
- Győződjön meg arról, hogy a Premium Storage-fiók elegendő szabad hellyel rendelkezik az ideiglenes előkészítési hely számára. További információ: [a prémium szintű oldal blob Storage-fiókok méretezhetőségi célpontjai](../storage/blobs/scalability-targets-premium-page-blobs.md). Ne módosítsa az átmeneti helyet.
- A biztonsági mentési feladatok befejezése után a rendszer törli az előkészítési helyet.
- Az előkészítési helyhez használt tárolási díj konzisztens a [Premium Storage díjszabásával](../virtual-machines/disks-types.md#billing).

Ha a Premium Storage használatával állítja vissza az Azure-beli virtuális gépeket, visszaállíthatja őket prémium vagy standard szintű tárolóba. Általában a Premium Storage-ba kell visszaállítani őket. Ha azonban csak a virtuális gépről származó fájlok egy részhalmazára van szüksége, akkor költséghatékony lehet a standard szintű tárolóra visszaállítani.

### <a name="back-up-and-restore-managed-disks"></a>Felügyelt lemezek biztonsági mentése és visszaállítása

A felügyelt lemezekkel rendelkező Azure-beli virtuális gépek biztonsági mentését végezheti el:

- Felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentését ugyanúgy végezheti el, mint bármely más Azure-beli virtuális gépet. A virtuális gép biztonsági mentését közvetlenül a virtuálisgép-beállításokból is elvégezheti, vagy engedélyezheti a virtuális gépek biztonsági mentését a Recovery Services-tárolóban.
- A felügyelt lemezeken található virtuális gépek biztonsági mentését a felügyelt lemezeken kiépített visszaállításipont-gyűjteményekkel végezheti el.
- A Azure Backup a Azure Disk Encryption használatával titkosított, felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentését is támogatja.

Ha felügyelt lemezekkel állítja vissza a virtuális gépeket, visszaállíthatja a felügyelt lemezekkel rendelkező teljes virtuális gépre vagy egy Storage-fiókra:

- A visszaállítási folyamat során az Azure kezeli a felügyelt lemezeket. Ha a Storage-fiók lehetőséget használja, akkor a visszaállítási folyamat során létrehozott Storage-fiókot kell kezelnie.
- Ha egy titkosított felügyelt virtuális gépet állít vissza, akkor a visszaállítási folyamat megkezdése előtt győződjön meg arról, hogy a virtuális gép kulcsainak és titkos kulcsai léteznek a kulcstartóban.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a támogatási mátrixot, [ahol megismerheti a biztonsági mentési forgatókönyvek támogatott funkcióit és korlátozásait](backup-support-matrix.md).
- Állítsa be a biztonsági mentést az alábbi forgatókönyvek egyikére:
  - [Azure-beli virtuális gépek biztonsági mentése](backup-azure-arm-vms-prepare.md).
  - [Windows rendszerű gépek biztonsági mentése](tutorial-backup-windows-server-to-azure.md)biztonsági mentési kiszolgáló nélkül.
  - [Állítsa be a MABS](backup-azure-microsoft-azure-backup.md) az Azure-ba történő biztonsági mentéshez, majd készítsen biztonsági másolatot a munkaterhelésekről a MABS.
  - [Állítsa be a DPM](backup-azure-dpm-introduction.md) az Azure-ba történő biztonsági mentéshez, majd készítsen biztonsági másolatot a munkaterhelésekről a DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
