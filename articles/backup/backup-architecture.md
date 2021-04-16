---
title: Az architektúra áttekintése
description: Áttekintést nyújt az Azure Backup által használt architektúráról, összetevőkről és folyamatokról.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 6ecf01838b8fe3104626f8ada5f832c3f52dc378
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515906"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup architektúra és összetevők

Az adatok biztonsági [Azure Backup az](backup-overview.md) Microsoft Azure felhőplatformra. Ez a cikk Azure Backup architektúrát, összetevőket és folyamatokat foglal össze.

## <a name="what-does-azure-backup-do"></a>Mi a Azure Backup?

Azure Backup a helyszíni gépeken és Azure-beli virtuálisgép-példányon futó adatokról, gépállapotról és számítási feladatokról. Számos különböző Azure Backup van.

## <a name="how-does-azure-backup-work"></a>Hogyan működik Azure Backup?

A gépekről és az adatokról számos módszerrel biztonságimenthet:

- **Helyszíni gépek biztonságimentése:**
  - A helyszíni Windows rendszerű gépekről közvetlenül az Azure-ba is biztonsági Azure Backup Microsoft Azure Recovery Services- (MARS-) ügynök használatával. A Linux rendszerű gépek nem támogatottak.
  - A helyszíni gépek biztonsági mentését biztonsági mentési kiszolgálóra is használhatja , amely lehet System Center Data Protection Manager (DPM) vagy a Microsoft Azure Backup Server (MABS). Ezután biztonsági mentést készíthet a biztonsági mentési kiszolgálóról egy Azure-beli Recovery Services-tárolóba.

- **Azure-beli virtuális gépek biztonságimentés:**
  - Az Azure-beli virtuális gépekről közvetlenül is biztonságimenthet. Azure Backup egy biztonsági mentési bővítményt telepít a virtuális gépen futó Azure-beli virtuálisgép-ügynökhöz. Ez a bővítmény a teljes virtuális gépről biztonságimenti.
  - Az Azure-beli virtuális gépen lévő adott fájlokról és mappákról a MARS-ügynök futtatásával biztonsági mentése is szükséges.
  - Az Azure-beli virtuális gépekről biztonságimentést futtathat az Azure-ban futó MABS-re, a MABS-ről pedig egy Recovery Services-tárolóba.

További információ a [biztonsági mentésről és](backup-overview.md) a támogatott biztonsági [mentési forgatókönyvekről.](backup-support-matrix.md)

## <a name="where-is-data-backed-up"></a>Hol vannak biztonságimentve az adatok?

Azure Backup biztonsági mentési adatokat tárolókban tárol – Recovery Services-tárolókban és Backup-tárolókban. A tároló egy online tárolóentitás az Azure-ban, amely adatok, például biztonsági másolatok, helyreállítási pontok és biztonsági mentési szabályzatok tárolására szolgál.

A tárolók a következő funkciókkal rendelkezik:

- A tárolók megkönnyítik a biztonsági mentési adatok rendszerezését, ugyanakkor minimalizálják a felügyeleti többletterhelést.
- A tárolókban lévő biztonságimentett elemeket, köztük az Azure-beli virtuális gépeket és a helyszíni gépeket is monitorozhatja.
- A tárolók hozzáférését az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával kezelheti.](../role-based-access-control/role-assignments-portal.md)
- A redundancia érdekében meg kell adnia, hogy a rendszer hogyan replikálja a tárolóban tárolt adatokat:
  - **Helyileg redundáns tárolás (LRS):** Az adatközpontok meghibásodása elleni védelem érdekében használhatja az LRS-t. Az LRS egy tárolóskálás egységbe replikálja az adatokat. [További információ](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Georedundáns tárolás (GRS):** A régióra kiterjedő kimaradásokkal szembeni védelem érdekében használhatja a GRS-t. A GRS egy másodlagos régióba replikálja az adatokat. [További információ](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Zónaredundáns tárolás (ZRS):** az adatokat rendelkezésre állási zónákban replikálja, [](../availability-zones/az-overview.md#availability-zones)garantálva az adatok tárolási helyét és rugalmasságát ugyanabban a régióban. [További információ](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Alapértelmezés szerint a Recovery Services-tárolók GRS-t használnak.

A Recovery Services-tárolók a következő további funkciókkal is rendelkezik:

- Minden Azure-előfizetésben legfeljebb 500 tárolót hozhat létre.

## <a name="backup-agents"></a>Biztonsági mentési ügynökök

Azure Backup különböző biztonsági mentési ügynököket biztosít attól függően, hogy milyen típusú gépről készít biztonsági másolatot:

**Ügynök** | **Részletek**
--- | ---
**MARS-ügynök** | <ul><li>Különálló helyszíni Windows Server-gépeken fut a fájlok, mappák és a rendszerállapot biztonsági mentése érdekében.</li> <li>Azure-beli virtuális gépeken fut a fájlok, mappák és a rendszerállapot biztonsági mentése érdekében.</li> <li>DPM-/MABS-kiszolgálókon fut a DPM-/MABS helyi tárolólemez Azure-ba való biztonságimentéséhez.</li></ul>
**Azure-beli virtuálisgép-bővítmény** | Azure-beli virtuális gépeken fut, hogy biztonsági számukra biztonsági szolgáltatásokat ossunk egy tárolóba.

## <a name="backup-types"></a>Biztonsági másolatok típusai

Az alábbi táblázat ismerteti a biztonsági mentések különböző típusait és a használataik típusát:

**Biztonsági mentés típusa** | **Részletek** | **Használat**
--- | --- | ---
**Összes** | A teljes biztonsági mentés a teljes adatforrást tartalmazza. Több hálózati sávszélességet vesz igénybe, mint a különbözeti vagy növekményes biztonsági mentések. | A kezdeti biztonsági mentéshez használatos.
**Differenciál** |  A különbségi biztonsági mentés tárolja a kezdeti teljes biztonsági mentés óta módosult blokkokat. Kisebb mennyiségű hálózatot és tárterületet használ, és nem tartja meg a változatlan adatok redundáns másolatait.<br/><br/> Nem hatékony, mert a későbbi biztonsági mentések között nem módosuló adatblokkok átkerülnek és tárolva vannak. | A nem használja Azure Backup.
**Növekvő** | A növekményes biztonsági mentés csak az előző biztonsági mentés óta módosult adatblokkokat tárolja. Magas tárolási és hálózati hatékonyság. <br/><br/> Növekményes biztonsági mentéssel nem kell kiegészítenie a teljes biztonsági mentéseket. | A DPM/MABS használja lemezes biztonsági mentéshez, és az Azure-ba való összes biztonsági mentéshez. Nem használatos biztonsági SQL Server készítéséhez.

## <a name="sql-server-backup-types"></a>SQL Server biztonsági mentési típusok

Az alábbi táblázat ismerteti az adatbázis-adatbázisokhoz SQL Server biztonsági másolatok különböző típusait és a használt gyakran használt biztonsági másolatokat:

**Biztonsági mentés típusa** | **Részletek** | **Használat**
--- | --- | ---
**Teljes biztonsági mentés** | Az adatbázis teljes biztonsági mentése biztonsági másolatot készít a teljes adatbázisról. Egy adott adatbázisban vagy fájlcsoportokban vagy fájlokban található összes adatot tartalmazza. A teljes biztonsági mentés elegendő naplót is tartalmaz az adatok helyreállításához. | Naponta egy teljes biztonsági mentést aktiválhat.<br/><br/> Napi vagy heti rendszerességgel is teljes biztonsági mentést is készíthet.
**Különbségi biztonsági mentés** | A különbözeti biztonsági mentés a legutóbbi, teljes adatokról készült legutóbbi biztonsági mentésen alapul.<br/><br/> Csak a teljes biztonsági mentés óta módosult adatokat rögzíti. |  Legfeljebb napi egy különbözeti biztonsági mentést kezdeményezhet.<br/><br/> Teljes biztonsági mentést és különbözeti biztonsági mentést nem konfigurálhat ugyanazon a napon.
**Tranzakciónapló biztonsági mentése** | A naplók biztonsági mentése lehetővé teszi az időponthoz időben való visszaállítást egy adott másodpercig. | A tranzakciós naplók biztonsági mentését 15 percenként konfigurálhatja.

### <a name="comparison-of-backup-types"></a>Biztonságimásolat-típusok összehasonlítása

A tárhelyfelhasználás, a helyreállítási időre vonatkozó célkitűzés (RTO) és a hálózatfelhasználás minden biztonsági mentéstípushoz eltérő. Az alábbi képen a biztonsági másolatok típusainak összehasonlítása látható:

- Az A adatforrás 10 tárolási blokkból áll, A1–A10-ről, amelyekről havonta biztonságimentet.
- Az A2, az A3, az A4 és az A9 módosul az első hónapban, az A5 pedig a következő hónapban.
- Különbségi biztonsági mentések során a második hónapban módosított A2, A3, A4 és A9 blokkról készít biztonsági másolatot. A harmadik hónapban a módosított A5 blokkal együtt biztonsági másolat készül ugyanezekről a blokkokról. A következő teljes biztonsági mentésig minden alkalommal készül biztonsági másolat a módosított blokkokról.
- Növekményes biztonsági mentések során a második hónapban az A2, A3, A4 és A9 blokkok módosultként vannak megjelölve és átvittként vannak megjelölve. A harmadik hónapban csak az A5 módosított blokk van megjelölve és továbbítva.

![A biztonsági mentési módszerek összehasonlítását bemutató kép](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Biztonsági mentési funkciók

Az alábbi táblázat összefoglalja a különböző típusú biztonsági mentések támogatott funkcióit:

**Szolgáltatás** | **Fájlok és mappák közvetlen biztonsági mentése (MARS-ügynök használatával)** | **Azure-beli virtuális gép biztonsági mentése** | **DPM/MABS-t és gépeket vagy alkalmazásokat**
--- | --- | --- | ---
Biztonságimentás a tárolóba | ![Igen][green] | ![Igen][green] | ![Igen][green]
Biztonságimentés DPM-/MABS-lemezre, majd az Azure-ba | | | ![Igen][green]
Biztonsági mentésre elküldött adatok tömörítése | ![Igen][green] | Az adatok átvitele nem használ tömörítést. A tárterület kissé meggyújtva van, de a visszaállítás gyorsabb.  | ![Igen][green]
Növekményes biztonsági mentés futtatása |![Igen][green] |![Igen][green] |![Igen][green]
Deduplikált lemezek biztonságimentációja | | | ![Részlegesen][yellow]<br/><br/> Csak a helyszínen telepített DPM-/MABS-kiszolgálók esetén.

![Táblakulcs](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>A biztonsági mentési szabályzat alapvető fontosságú

- Tárolónként létrejön egy biztonsági mentési szabályzat.
- A következő számítási feladatok biztonsági mentéséhez lehet biztonsági mentési szabályzatot létrehozni: Azure-beli virtuális gépek, SQL Azure-beli virtuális gépeken, SAP HANA Azure-beli virtuális gépeken található virtuális gépek és Azure-fájlmegosztások. A MARS-ügynökkel a fájlok és mappák biztonsági mentésére vonatkozó szabályzat a MARS-konzolon van megadva.
  - Azure-fájlmegosztás
- A szabályzatok számos erőforráshoz hozzárendelhetőek. Az Azure-beli virtuális gépek biztonsági mentési szabályzata számos Azure-beli virtuális gép védelmére használható.
- Egy szabályzat két összetevőből áll
  - Ütemezés: Mikor kell biztonsági másolatot készíteni
  - Megőrzés: Az egyes biztonsági másolatok megőrzésének ideje.
- Az ütemezés lehet "napi" vagy "heti" egy adott időponttal.
- A megtartás a "napi", "heti", "havi", "éves" biztonsági mentési pontokhoz definiálható.
  - A "weekly" a hét egy adott napján érvényes biztonsági mentést jelenti
  - A "monthly" a hónap egy adott napján érvényes biztonsági mentést jelenti
  - Az "éves" az év egy adott napján érvényes biztonsági mentést jelenti
- Megőrzés "havi", "éves" biztonsági mentési pontok a hosszú távú megőrzés (LTR)
- A tárolók létrehozásakor a "DefaultPolicy" is létrejön, és használható az erőforrások biztonságimentére.
- A biztonsági mentési szabályzatok megőrzési időszakán végrehajtott módosítások visszamenőlegesen lesznek alkalmazva az összes régebbi helyreállítási pontra az újakon kívül.

### <a name="impact-of-policy-change-on-recovery-points"></a>A szabályzatváltozás hatása a helyreállítási pontokra

- **A megőrzési időtartam növekszik/csökken:** A megőrzési időtartam módosításával a rendszer az új megőrzési időtartamot is alkalmazza a meglévő helyreállítási pontokra. Ennek eredményeképpen néhány helyreállítási pont el lesz ásva. A megőrzési időtartam megnövelése esetén a meglévő helyreállítási pontok is magasabb megőrzési idővel fognak növekedni.
- **Napiról hetire változott:** Ha az ütemezett biztonsági mentések napiról hetire változnak, a meglévő napi helyreállítási pontok el vannak ásva.
- **Hetiről napira változott:** A meglévő heti biztonsági másolatok a jelenlegi adatmegőrzési szabályzatnak megfelelő hátralévő napok száma alapján lesznek megőrizve.

### <a name="additional-reference"></a>További referenciák

- Azure-beli virtuális [](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) gép: Szabályzat [létrehozása és](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) módosítása.
- SQL Server Azure-beli virtuális gépen: [](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) Szabályzat [létrehozása és](./manage-monitor-sql-database-backup.md#modify-policy) módosítása.
- Azure-fájlmegosztás: Szabályzat [létrehozása](./backup-afs.md) [és](./manage-afs-backup.md#modify-policy) módosítása.
- SAP HANA: Házirend [létrehozása](./backup-azure-sap-hana-database.md#create-a-backup-policy) [és](./sap-hana-db-manage.md#change-policy) módosítása.
- MARS: Szabályzat [létrehozása](./backup-windows-with-mars-agent.md#create-a-backup-policy) [és](./backup-azure-manage-mars.md#modify-a-backup-policy) módosítása.
- [Vonatkozik valamilyen korlátozás a biztonsági mentés ütemezésére a számítási feladat típusa alapján?](./backup-azure-backup-faq.yml#are-there-limits-on-backup-scheduling-)
- [Mi történik a meglévő helyreállítási pontokkal, ha módosítom az adatmegőrzési szabályzatot?](./backup-azure-backup-faq.yml#what-happens-when-i-change-my-backup-policy-)

## <a name="architecture-built-in-azure-vm-backup"></a>Architektúra: Beépített Azure-beli virtuális gép biztonsági mentése

1. Amikor engedélyezi egy Azure-beli virtuális gép biztonsági mentését, a biztonsági mentés a megadott ütemezés szerint fut.
1. Az első biztonsági mentés során egy biztonsági mentési bővítmény lesz telepítve a virtuális gépre, ha a virtuális gép fut.
    - Windows rendszerű virtuális gépekre a VMSnapshot bővítmény van telepítve.
    - Linux rendszerű virtuális gépeken a VMSnapshot Linux-bővítmény van telepítve.
1. A bővítmény egy tárolási szintű pillanatképet készít.
    - Futó Windows rendszerű virtuális gépek esetén a Biztonsági mentés a Windows Kötet árnyékmásolata szolgáltatás (VSS) segítségével koordinálja a virtuális gép alkalmazás-konzisztens pillanatképének készítését. Alapértelmezés szerint a biztonsági mentés teljes VSS-biztonsági mentést készít. Ha a Backup nem tud alkalmazás-konzisztens pillanatképet készíteni, akkor fájl konzisztens pillanatképet készít.
    - Linux rendszerű virtuális gépeken a Biztonsági mentés fájl konzisztens pillanatképet készít. Az alkalmazás-konzisztens pillanatképek használata előtt és után manuálisan kell testreszabnia a szkripteket.
    - A biztonsági mentés az egyes virtuálisgép-lemezek párhuzamos biztonsági mentésével van optimalizálva. A rendszer minden egyes lemezről biztonsági Azure Backup beolvassa a lemezen lévő blokkokat, és csak a módosított adatokat tárolja.
1. A pillanatkép készítése után az adatok átkerülnek a tárolóba.
    - A rendszer csak a legutóbbi biztonsági mentés óta módosított adatblokkokat másolja.
    - Az adatok nincs titkosítva. Azure Backup segítségével titkosított Azure-beli virtuális gépekről is Azure Disk Encryption.
    - A pillanatképek adatai nem mindig vannak azonnal a helyreállítási tárba másolva. Csúcsidőben a biztonsági mentés órákat is igénybe vehet. Egy virtuális gép biztonsági mentésének időtartama a napi biztonsági mentési szabályzatok esetében 24 óránál kevesebb.
1. Miután a rendszer elküldte az adatokat a tárolónak, létrejön egy helyreállítási pont. Alapértelmezés szerint a pillanatképek a törlésük előtt két napig maradnak meg. Ez a funkció lehetővé teszi a pillanatképek visszaállítási műveletét, ezáltal csökkenti a visszaállítási időt. Csökkenti az adatok átalakításához és a tárolóból való visszamásához szükséges időt. Lásd [Azure Backup azonnali visszaállítási képességről.](./backup-instant-restore-capability.md)

Az Azure-beli virtuális gépek biztonságimentéséhez nem kell kifejezetten engedélyeznie az internetkapcsolatot.

![Azure-beli virtuális gépek biztonsági mentése](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektúra: Helyszíni Windows Server-gépek vagy Azure-beli virtuális gépek fájljainak vagy mappáinak közvetlen biztonsági mentése

1. A forgatókönyv beállításának érdekében töltse le és telepítse a MARS-ügynököt a gépre. Ezután kiválaszthatja, hogy miről és mikor fusson a biztonsági mentés, és mennyi ideig lesznek megőrizve az Azure-ban.
1. A kezdeti biztonsági mentés a biztonsági mentési beállításoknak megfelelően fut.
1. A MARS-ügynök a VSS-t használja a biztonsági mentéshez kiválasztott kötetek időponthoz készült pillanatképének készítéséhez.
    - A MARS-ügynök csak a Windows rendszerírási műveletét használja a pillanatkép rögzítéséhez.
    - Mivel az ügynök nem használ alkalmazás VSS-írókat, nem rögzíti az alkalmazás-konzisztens pillanatképeket.
1. Miután a VSS-sel készített pillanatképet, a MARS-ügynök létrehoz egy virtuális merevlemezt (VHD) a biztonsági mentés konfigurálásakor megadott gyorsítótármappában. Az ügynök az egyes adatblokkok ellenőrzőanyagát is tárolja. Ezeket később a módosított blokkok észlelésére fogja használni a későbbi növekményes biztonsági mentések során.
1. A növekményes biztonsági mentések a megadott ütemezés szerint futnak, kivéve, ha igény szerinti biztonsági mentést futtat.
1. A növekményes biztonsági mentések során a rendszer azonosítja a módosított fájlokat, és létrehoz egy új virtuális merevlemezt. A VHD tömörítve és titkosítva van, majd a tárolóba lesz elküldve.
1. A növekményes biztonsági mentés befejezése után az új virtuális merevlemez egyesül a kezdeti replikáció után létrehozott virtuális merevlemezsel. Ez az egyesített VHD a legújabb állapotot biztosítja a folyamatban lévő biztonsági mentések összehasonlításához.

![Helyszíni Windows Server-gépek biztonsági mentése MARS-ügynökkel](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektúra: Biztonsági architektúra a DPM-re/MABS-re

1. Telepítse a DPM- vagy MABS-védelmi ügynököt a védeni kívánt gépekre. Ezután hozzáadhatja a gépeket egy DPM védelmi csoporthoz.
    - A helyszíni gépek védelméhez a DPM- vagy MABS-kiszolgálónak a helyszínen kell lennie.
    - Az Azure-beli virtuális gépek védelméhez a MABS-kiszolgálónak az Azure-ban kell lennie, és Azure-beli virtuális gépként kell futnia.
    - A DPM/MABS segítségével megvédheti a biztonsági mentési köteteket, megosztásokat, fájlokat és mappákat. A gépek rendszerállapotát is védheti (operációs rendszer nélküli), és alkalmazásokat indító biztonsági mentési beállításokkal védhet bizonyos alkalmazásokat.
1. Amikor beállít védelmet egy géphez vagy alkalmazáshoz a DPM/MABS-ban, a rövid távú tároláshoz a MABS/DPM helyi lemezre, az online védelemhez pedig az Azure-ba való biztonságimentés kiválasztását választja. Azt is megadhatja, hogy mikor fusson a helyi DPM-/MABS-tárolóba való biztonsági mentés, és mikor fusson az Azure-ba való online biztonsági mentés.
1. A védett munkaterhelés lemeze a helyi MABS-/DPM-lemezekre lesz biztonsági adva a megadott ütemezés szerint.
1. A DPM-/MABS-lemezekről a DPM-/MABS-kiszolgálón futó MARS-ügynök biztonsági adatbázist hoz a tárolóba.

![DPM vagy MABS által védett gépek és munkaterhelések biztonsági mentése](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure-beli virtuálisgép-tároló

Az Azure-beli virtuális gépek lemezekkel tárolják az operációs rendszerüket, az alkalmazásokat és az adatokat. Minden Azure-beli virtuális gép legalább két lemezzel rendelkezik: egy lemezzel az operációs rendszerhez és egy ideiglenes lemezhez. Az Azure-beli virtuális gépek adatlemezekkel is használhatnak alkalmazásadatokat. A lemezek VHD-ként vannak tárolva.

- A VHD-ket lapblobokként tárolja a rendszer standard vagy prémium szintű tárfiókban az Azure-ban:
  - **Standard szintű tároló:** Megbízható, alacsony költségű lemeztámogatás a késésre nem érzékeny számítási feladatokat futtató virtuális gépek számára. A standard szintű tárolók standard SSD-lemezeket vagy standard merevlemez-meghajtókat (HDD) is használhatnak.
  - **Premium Storage:** Nagy teljesítményű lemeztámogatás. Prémium SSD-lemezeket használ.
- A lemezekhez különböző teljesítményszintek vannak:
  - **standard HDD lemez:** HDD-ket használ a költséghatékony tároláshoz.
  - **standard SSD lemez:** Egyesíti a prémium SSD-lemezek és a standard HDD-lemezek elemeit. A HDD-nél konzisztensebb teljesítményt és megbízhatóságot kínál, de mégis költséghatékony.
  - **prémium SSD lemez:** Az SSD-kre van optimalizálva, és nagy teljesítményt és kis késést biztosít az I/O-igényes számítási feladatokat futtató virtuális gépek számára.
- A lemezek felügyeltek és nem felügyeltek is:
  - **Nemmanaged disks ::** A virtuális gépek által használt hagyományos lemeztípus. Ezekhez a lemezekhez saját tárfiókot kell létrehoznia, és meg kell adnia a lemez létrehozásakor. Ezután meg kell találnia, hogyan maximalizálhatja a virtuális gépek tárolási erőforrásait.
  - **Felügyelt lemezek:** Az Azure létrehozza és felügyeli a tárfiókokat. Megadhatja a lemezméretet és a teljesítményszintet, az Azure pedig felügyelt lemezeket hoz létre. A lemezek hozzáadása és a virtuális gépek méretezése során az Azure kezeli a tárfiókokat.

A lemezes tárolással és a virtuális gépek számára elérhető lemeztípusokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Felügyelt Azure-lemezek Linux rendszerű virtuális gépekhez](../virtual-machines/managed-disks-overview.md)
- [Virtuális gépekhez elérhető lemeztípusok](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Azure-beli virtuális gépek biztonsági mentése és visszaállítása Prémium szintű tárolóval

Az Azure-beli virtuális gépekről a Premium Storage és a Azure Backup:

- A virtuális gépek premium szintű tárolóval való biztonsági mentésének folyamata során a Backup szolgáltatás létrehoz egy ideiglenes előkészítési helyet *AzureBackup-* névvel a tárfiókban. Az előkészítési hely mérete megegyezik a helyreállítási pont pillanatképének méretével.
- Győződjön meg arról, hogy a prémium szintű tárfiók elegendő szabad hellyel rendelkezik az átmeneti előkészítési hely számára. További információ: [Skálázhatósági célok prémium szintű lapblob-tárfiókok esetén.](../storage/blobs/scalability-targets-premium-page-blobs.md) Ne módosítsa az előkészítési helyet.
- A biztonsági mentési feladat befejezése után az előkészítési hely törlődik.
- Az előkészítési helyhez használt tárterület ára megfelel a [Premium Storage díjszabásának.](../virtual-machines/disks-types.md#billing)

Ha prémium szintű tárolóval visszaállítJa az Azure-beli virtuális gépeket, prémium vagy standard szintű tárhelyre is visszaállíthatja őket. Ezeket általában prémium szintű tárolóra kell visszaállítani. Ha azonban csak a fájlok egy részkészletére van szüksége a virtuális gépről, költséghatékony lehet a standard tárolóba visszaállítani őket.

### <a name="back-up-and-restore-managed-disks"></a>Felügyelt lemezek biztonsági mentése és visszaállítása

Az Azure-beli virtuális gépekről biztonságimentéseket is használhat felügyelt lemezekkel:

- A felügyelt lemezekkel lévő virtuális gépekről ugyanúgy biztonságimentéseket kell, mint bármely más Azure-beli virtuális gépről. Biztonsági másolatot készíthet a virtuális gépről közvetlenül a virtuális gép beállításaiból, vagy engedélyezheti a virtuális gépek biztonsági mentését a Recovery Services-tárolóban.
- A felügyelt lemezeken található virtuális gépek biztonsági mentését a felügyelt lemezeken kiépített visszaállításipont-gyűjteményekkel végezheti el.
- Azure Backup támogatja az olyan felügyelt lemezeket használó virtuális gépek biztonságimentését is, amelyek titkosítása az Azure Disk Encryption.

Amikor felügyelt lemezekkel visszaállítja a virtuális gépeket, visszaállíthat egy teljes virtuális gépre felügyelt lemezekkel vagy egy tárfiókba:

- A visszaállítási folyamat során az Azure kezeli a felügyelt lemezeket. Ha a tárfiók lehetőséget használja, ön felügyeli a visszaállítási folyamat során létrehozott tárfiókot.
- Ha titkosított felügyelt virtuális gépet visszaállít, a visszaállítási folyamat előtt győződjön meg arról, hogy a virtuális gép kulcsai és titkos kulcsai léteznek a kulcstartóban.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a támogatási mátrixot a biztonsági mentési forgatókönyvek támogatott [szolgáltatásainak és korlátozásának áttekintéséhez.](backup-support-matrix.md)
- Állítsa be a biztonsági mentést az alábbi forgatókönyvek egyikéhez:
  - [Azure-beli virtuális gépek biztonságimentés.](backup-azure-arm-vms-prepare.md)
  - [Windows rendszerű gépek biztonsági mentése közvetlenül ,](tutorial-backup-windows-server-to-azure.md)biztonsági mentési kiszolgáló nélkül.
  - [Állítsa be a MABS-t](backup-azure-microsoft-azure-backup.md) az Azure-ba való biztonsági mentéshez, majd a számítási feladatok biztonsági mentését a MABS-be.
  - [Állítsa be a DPM-et](backup-azure-dpm-introduction.md) az Azure-ba való biztonsági mentéshez, majd a számítási feladatok biztonsági mentését a DPM-be.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
