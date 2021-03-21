---
title: Azure-beli virtuális gépek biztonsági mentése
description: Ebből a cikkből megtudhatja, hogy az Azure Backup szolgáltatás hogyan készít biztonsági másolatot az Azure Virtual Machines szolgáltatásról, és hogyan követi az ajánlott eljárásokat.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 691fe991ad141696c0c68e915d7225001a1befd0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733570"
---
# <a name="an-overview-of-azure-vm-backup"></a>Az Azure virtuális gépek biztonsági mentésének áttekintése

Ez a cikk azt ismerteti, hogy az [Azure Backup szolgáltatás](./backup-overview.md) hogyan készít biztonsági másolatot az Azure Virtual Machines-ről (VM).

Az Azure Backup független és elkülönített biztonsági másolatokat biztosít, ezzel védelmet nyújtva a virtuális gépeken lévő adatok nem szándékos megsemmisítésével szemben. A biztonsági másolatok egy helyreállítási tárban vannak tárolva, a helyreállítási pontok beépített kezelésével. A konfigurálás és a skálázás egyszerű, a biztonsági másolatok optimalizálva vannak, és szükség esetén egyszerűen helyreállíthatók.

A biztonsági mentési folyamat részeként [Pillanatkép készül](#snapshot-creation), és az adatok átkerülnek a Recovery Services-tárolóba, és nincs hatással az éles munkaterhelésekre. A pillanatkép különböző szintű konzisztenciát biztosít az [itt](#snapshot-consistency)leírtak szerint.

Azure Backup az adatbázis-munkaterhelések, például a [SQL Server](backup-azure-sql-database.md) és az [SAP HANA](sap-hana-db-about.md) számítási feladataihoz is specializált ajánlatokat biztosít, amelyek a számítási feladatok ellátására használhatók, 15 perces RPO (helyreállítási időkorlát), valamint az egyes adatbázisok biztonsági mentését és visszaállítását teszik lehetővé.

## <a name="backup-process"></a>A biztonsági mentés folyamata

Az Azure Backup a következő módon készíti el az Azure-beli virtuális gépek biztonsági másolatát:

1. A biztonsági mentésre kijelölt Azure-beli virtuális gépek esetében a Azure Backup a megadott biztonsági mentési ütemtervnek megfelelően elindítja a biztonsági mentési feladatot.
1. Az első biztonsági mentés során a rendszer egy biztonsági mentési bővítményt telepít a virtuális gépre, ha a virtuális gép fut.
    - Windows rendszerű virtuális gépek esetén a [VMSnapshot bővítmény](../virtual-machines/extensions/vmsnapshot-windows.md) telepítve van.
    - Linux rendszerű virtuális gépek esetén a [VMSnapshotLinux bővítmény](../virtual-machines/extensions/vmsnapshot-linux.md) telepítve van.
1. A rendszert futtató Windows rendszerű virtuális gépek esetén a biztonsági mentési koordinátákat a Windows Kötet árnyékmásolata szolgáltatás (VSS) segítségével végezze el a virtuális gép alkalmazással konzisztens pillanatképének elkészítéséhez.
    - Alapértelmezés szerint a Backup teljes VSS biztonsági mentést készít.
    - Ha a biztonsági mentés nem tud alkalmazás-konzisztens pillanatképet készíteni, akkor a rendszer az alapul szolgáló tárterület fájl-konzisztens pillanatképét veszi igénybe (mivel a virtuális gép leállításakor nem történik alkalmazás-írás).
1. Linux rendszerű virtuális gépek esetén a Backup fájl-konzisztens biztonsági mentést készít. Az alkalmazással konzisztens Pillanatképek esetében manuálisan kell testreszabnia a parancsfájlok előtti és utáni parancsfájlokat.
1. A biztonsági mentést követően a pillanatkép átviszi az adattárolóba.
    - A biztonsági mentés úgy van optimalizálva, hogy párhuzamosan készít biztonsági másolatot az egyes virtuálisgép-lemezekről.
    - Az Azure Backup minden mentett lemez esetében kiolvassa a lemezen lévő blokkokat, majd azonosítja az utolsó biztonsági mentés óta módosult adatblokkokat (a deltát), és csak azokat másolja át.
    - A pillanatképek adatai nem mindig vannak azonnal a helyreállítási tárba másolva. A csúcsidőben több órát is igénybe vehet. Egy virtuális gép biztonsági mentésének időtartama a napi biztonsági mentési szabályzatok esetében 24 óránál kevesebb.
1. A Windows rendszerű virtuális gépeken a Azure Backup engedélyezése után végrehajtott módosítások a következők:
    - Microsoft Visual C++ 2013 terjeszthető csomag (x64) – a 12.0.40660 telepítve van a virtuális gépen.
    - A Kötet árnyékmásolata szolgáltatás (VSS) indítási típusa a kézi értékről automatikusra módosult
    - IaaSVmProvider Windows-szolgáltatás hozzáadva

1. Amikor az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

![Azure-beli virtuális gépek biztonsági mentési architektúrája](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Azure-beli virtuális gépek biztonsági másolatainak titkosítása

Az Azure-beli virtuális gépek Azure Backup-vel történő biztonsági mentésekor a virtuális gépek Storage Service Encryption (SSE) inaktív állapotban vannak titkosítva. A Azure Backup a Azure Disk Encryption használatával titkosított Azure-beli virtuális gépek biztonsági mentését is elvégezheti.

**Titkosítás** | **Részletek** | **Támogatás**
--- | --- | ---
**SSE** | Az SSE használatával az Azure Storage az adatok tárolása előtt automatikusan titkosítja a titkosítást. Az Azure Storage a beolvasás előtt visszafejti az adatokkal. Azure Backup támogatja a két Storage Service Encryption típusú virtuális gépek biztonsági mentését:<li> **SSE platform által felügyelt kulcsokkal**: Ez a titkosítás alapértelmezés szerint a virtuális gépek összes lemezén elérhető. [Itt](../virtual-machines/disk-encryption.md#platform-managed-keys)talál további információt.<li> **SSE az ügyfél által felügyelt kulcsokkal**. A CMK segítségével kezelheti a lemezek titkosításához használt kulcsokat. [Itt](../virtual-machines/disk-encryption.md#customer-managed-keys)talál további információt. | A Azure Backup az SSE-t használja az Azure-beli virtuális gépek Rest titkosításához.
**Azure Disk Encryption** | Azure Disk Encryption titkosítja az operációs rendszer és az adatlemezeket az Azure-beli virtuális gépekhez.<br/><br/> A Azure Disk Encryption a BitLocker titkosítási kulcsaival (BEKs) integrálható, amelyek titkos kulccsal rendelkeznek a Key vaultban. A Azure Disk Encryption Azure Key Vault kulcs-titkosítási kulcsokkal (KEK) is integrálva van. | Azure Backup támogatja a kizárólag BEKs-mel titkosított felügyelt és nem felügyelt Azure-beli virtuális gépek biztonsági mentését, illetve a BEKs és a KEK együttes használatát.<br/><br/> Mind a BEKs, mind a KEK biztonsági mentése és titkosítása is megtörténik.<br/><br/> Mivel a KEK és a BEKs biztonsági mentése történik, a szükséges engedélyekkel rendelkező felhasználók visszaállíthatják a kulcsokat és a titkokat a kulcstartóba, ha szükséges. Ezek a felhasználók a titkosított virtuális gépet is helyreállítják.<br/><br/> A titkosított kulcsokat és titkos kulcsokat nem lehet beolvasni jogosulatlan felhasználók vagy az Azure.

Felügyelt és nem felügyelt Azure-beli virtuális gépek esetén a Backup a BEKs-mel titkosított virtuális gépeket, illetve a BEKs-mel és a KEK-szel együtt titkosított virtuális gépeket

A biztonsági másolatban szereplő BEKs (Secrets) és KEK (kulcsok) titkosítva vannak. Csak akkor olvashatók és használhatók, ha a jogosult felhasználók a Key vaultba visszaállnak. Sem a jogosulatlan felhasználók, sem az Azure nem tudja olvasni vagy használni a biztonsági másolatok felhasznált kulcsait vagy titkait.

A BEKs is biztonsági másolat készül. Tehát ha a BEKs elvesznek, a jogosult felhasználók visszaállíthatják a BEKs a kulcstartóba, és helyreállítják a titkosított virtuális gépeket. Csak a megfelelő szintű engedélyekkel rendelkező felhasználók hozhatnak létre és állíthatnak vissza titkosított virtuális gépeket, kulcsokat és titkos kódokat.

## <a name="snapshot-creation"></a>Pillanatkép létrehozása

A Azure Backup a biztonsági mentés ütemtervének megfelelően hozza a pillanatképeket.

- **Windows rendszerű virtuális gépek:** Windows rendszerű virtuális gépek esetén a Backup szolgáltatás a VSS-vel koordinálja a virtuálisgép-lemezek alkalmazás-konzisztens pillanatképét.  Alapértelmezés szerint a Azure Backup teljes VSS biztonsági mentést készít (a biztonsági mentéskor levágja az alkalmazás naplóit, például a SQL Servert az alkalmazás szintjének konzisztens biztonsági mentésének lekéréséhez).  Ha SQL Server-adatbázist használ az Azure-beli virtuális gép biztonsági mentésére, akkor módosíthatja a VSS-másolás biztonsági mentésének beállítását (a naplók megőrzése érdekében). További információkért tekintse meg [ezt a cikket](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues).

- **Linux rendszerű virtuális gépek:** A Linux rendszerű virtuális gépek alkalmazás-konzisztens pillanatképének készítéséhez használja a Linux előtti és parancsfájl utáni keretrendszert a saját egyéni parancsfájljainak megírásához a konzisztencia biztosítása érdekében.

  - Azure Backup csak az Ön által írt Pre/post parancsfájlokat hívja meg.
  - Ha az előkészítő parancsfájlok és a parancsfájlok utáni végrehajtás sikeresen befejeződött, Azure Backup a helyreállítási pontot alkalmazás-konzisztensként jelöli meg. Ha azonban egyéni parancsfájlokat használ, az alkalmazás konzisztenciája végső soron a felelős.
  - [További](backup-azure-linux-app-consistent.md) információ a parancsfájlok konfigurálásáról.

## <a name="snapshot-consistency"></a>Pillanatkép konzisztenciája

A következő táblázat a pillanatkép-konzisztencia különböző típusait ismerteti:

**Pillanatkép** | **Részletek** | **Helyreállítási** | **Megfontolandó**
--- | --- | --- | ---
**Alkalmazás – konzisztens** | Az alkalmazással konzisztens biztonsági másolatok rögzítik a memória tartalmát és a függőben lévő I/O-műveleteket. Az alkalmazás-konzisztens Pillanatképek VSS-író (vagy Linux előtti/post szkriptek) használatával biztosítják az alkalmazásadatok egységességét a biztonsági mentés előtt. | Ha egy virtuális gépet egy alkalmazással konzisztens pillanatképtel állít le, a virtuális gép elindul. Nincs adatsérülés vagy adatvesztés. Az alkalmazások konzisztens állapotban kezdődnek. | Windows: az összes VSS-író sikeresen befejeződött<br/><br/> Linux: az előzetes/post parancsfájlokat a rendszer konfigurálta és sikeresen elvégezte
**Fájlrendszer-konzisztens** | A fájlrendszerrel konzisztens biztonsági másolatok következetességet biztosítanak azáltal, hogy az összes fájlról pillanatképet készítenek.<br/><br/> | Ha olyan virtuális gépet állít vissza, amely fájlrendszerrel konzisztens pillanatképet használ, a virtuális gép elindul. Nincs adatsérülés vagy adatvesztés. Az alkalmazásoknak saját "felerősítő" mechanizmust kell alkalmazniuk, hogy a visszaállított adategységek konzisztensek legyenek. | Windows: néhány VSS-író nem sikerült <br/><br/> Linux: alapértelmezett (ha a Pre/post parancsfájlokat nem konfigurálták vagy nem sikerült)
**Összeomlás – konzisztens** | Az összeomlás-konzisztens Pillanatképek általában akkor fordulnak elő, ha egy Azure-beli virtuális gép le van állítva a biztonsági mentés időpontjában. A rendszer csak azokat az adatmennyiségeket rögzíti, amelyek már léteznek a lemezen a biztonsági mentés során. | A virtuális gép rendszerindítási folyamatával kezdődik, majd egy lemez-ellenőrzéssel javítja a sérülési hibákat. Olyan memóriában tárolt adatok vagy írási műveletek, amelyeket nem a lemezre vittek át az összeomlás elvesztése előtt. Az alkalmazások saját adatellenőrzést hajtanak végre. Egy adatbázis-alkalmazás például az ellenőrzéshez használhatja a tranzakciónaplóját. Ha a tranzakciónapló olyan bejegyzéseket tartalmaz, amelyek nem szerepelnek az adatbázisban, akkor az adatbázis szoftvere az adatok konzisztenciája előtt Visszagörgeti a tranzakciókat. | A virtuális gép leállítása (leállítva/fel nem foglalt) állapotú.

>[!NOTE]
> Ha a kiépítési állapot **sikeres**, Azure Backup a fájlrendszerrel konzisztens biztonsági mentést végez. Ha a kiépítési állapot nem **érhető el** vagy **sikertelen**, a rendszer összeomlott biztonsági mentéseket végez. Ha a létesítési állapot **létrehozása** vagy **törlése** történik, akkor a Azure Backup a műveletek újrapróbálkozását jelenti.

## <a name="backup-and-restore-considerations"></a>Biztonsági mentéssel és visszaállítással kapcsolatos megfontolandó szempontok

**Megfontolandó** | **Részletek**
--- | ---
**Lemez** | A VM-lemezek biztonsági mentése párhuzamos. Ha például egy virtuális gépnek négy lemeze van, a Backup szolgáltatás mind a négy lemezről párhuzamosan kísérli meg a biztonsági mentést. A biztonsági mentés növekményes (csak módosult adatértékek).
**Ütemezés** |  A biztonsági mentési forgalom csökkentése érdekében készítsen biztonsági másolatot a különböző virtuális gépekről a nap különböző pontjain, és győződjön meg arról, hogy az idő nem fedi át egymást. A virtuális gépek egyidejű biztonsági mentése az adatformalom elakadását okozza.
**Biztonsági mentések előkészítése** | Tartsa szem előtt a biztonsági mentés előkészítéséhez szükséges időt. Az előkészítési idő magában foglalja a biztonsági mentési bővítmény telepítését vagy frissítését, valamint a pillanatkép biztonsági mentési ütemezésnek megfelelő aktiválását.
**Adatátvitel** | Vegye figyelembe a Azure Backup számára szükséges időt az előző biztonsági mentés növekményes változásainak azonosításához.<br/><br/> A növekményes biztonsági mentések során az Azure Backup a blokk ellenőrzőösszegének kiszámításával határozza meg a módosításokat. Ha egy blokk módosul, a rendszer megjelöli a tárolóba való átvitelhez. A szolgáltatás elemzi az azonosított blokkokat, hogy tovább csökkenthesse az átvitelre váró adatok mennyiségét. Az összes módosított blokk kiértékelése után az Azure Backup átviszi a módosításokat a tárolóba.<br/><br/> A pillanatkép elkészítése és tárolóba való másolása között késés lehet. Csúcsidőben a pillanatképek a tárolóba való átvitele akár nyolc órát is igénybe vehet. A virtuális gépek biztonsági mentésének időtartama a napi biztonsági mentés esetén 24 óránál kevesebb lesz.
**Kezdeti biztonsági mentés** | Bár a növekményes biztonsági mentések teljes időtartama kevesebb mint 24 óra, az első biztonsági mentésre ez nem feltétlenül igaz. A kezdeti biztonsági mentéshez szükséges idő az adatok méretétől és a biztonsági mentés feldolgozásának időpontjától függ.
**Várólista visszaállítása** | Azure Backup folyamatok egyszerre több Storage-fiókból is visszaállítják a feladatokat, és a visszaállítási kérelmeket egy várólistába helyezi.
**Másolat visszaállítása** | A visszaállítási folyamat során az adatok a tárolóból a Storage-fiókba lesznek másolva.<br/><br/> A teljes visszaállítási idő a másodpercenkénti I/O-műveletektől (IOPS) és a Storage-fiók átviteli sebességtől függ.<br/><br/> A másolási idő csökkentése érdekében válasszon ki egy olyan Storage-fiókot, amely nincs betöltve más alkalmazások írásával és olvasásával.

### <a name="backup-performance"></a>Biztonsági mentési teljesítmény

Ezek a gyakori forgatókönyvek a teljes biztonsági mentés idejére hatással lehetnek:

- **Új lemez hozzáadása egy védett Azure-beli virtuális géphez:** Ha egy virtuális gép növekményes biztonsági mentést készít, és új lemezt ad hozzá, a biztonsági mentés ideje növekedni fog. A teljes biztonsági mentési idő az új lemez kezdeti replikálása, valamint a meglévő lemezek különbözeti replikációja miatt akár 24 óránál is tarthat.
- **Töredezett lemezek:** A biztonsági mentési műveletek gyorsabbak, ha a lemez változása összefüggő. Ha a módosítások szét vannak osztva és töredezettek egy lemezen, a biztonsági mentés lassabb lesz.
- **Lemez** elváltozása: Ha a növekményes biztonsági mentés alatt álló védett lemezek napi adatváltozása meghaladja a 200 GB-ot, a biztonsági mentés hosszú időt is igénybe vehet (több mint nyolc óra) a befejezéshez.
- **Biztonsági mentési verziók:** A biztonsági mentés legújabb verziója (vagyis az azonnali visszaállítás verziója) egy optimalizált folyamatot használ, mint a változások azonosításához szükséges ellenőrzőösszeg-összehasonlítás. Ha azonban azonnali visszaállítást használ, és törölt egy biztonsági mentési pillanatképet, a biztonsági mentés ellenőrzőösszeg-összehasonlításra vált. Ebben az esetben a biztonsági mentési művelet 24 óránál hosszabb időt vesz igénybe (vagy sikertelen).

### <a name="restore-performance"></a>Teljesítmény visszaállítása

Ezek a gyakori forgatókönyvek a teljes visszaállítási időt befolyásolhatják:

- A teljes visszaállítási idő a másodpercenkénti bemeneti/kimeneti műveletektől (IOPS) és a Storage-fiók átviteli sebességtől függ.
- A teljes visszaállítási idő hatással lehet, ha a célként megadott Storage-fiók betöltődik más alkalmazás-olvasási és írási műveletekkel. A visszaállítási művelet javításához válasszon olyan Storage-fiókot, amely nincs betöltve más alkalmazásadatok használatával.

## <a name="best-practices"></a>Ajánlott eljárások

Ha virtuális gépek biztonsági mentését konfigurálja, javasoljuk, hogy kövesse a következő eljárásokat:

- Módosítsa a szabályzatban beállított alapértelmezett ütemezési időpontokat. Ha például a szabályzatban az alapértelmezett időpont 00:00, növelje az időzítést néhány perccel, hogy az erőforrás-használat optimális legyen.
- Ha egyetlen tárolóból állítja vissza a virtuális gépeket, javasoljuk, hogy használjon különböző [általános célú v2 Storage-fiókokat](../storage/common/storage-account-upgrade.md) annak biztosítására, hogy a cél Storage-fiók ne kapjon szabályozást. Az egyes virtuális gépeknek például eltérő Storage-fiókkal kell rendelkezniük. Ha például 10 virtuális gép van visszaállítva, használjon 10 különböző Storage-fiókot.
- A Premium Storage szolgáltatást azonnali visszaállítást használó virtuális gépek biztonsági mentéséhez javasolt a teljes lefoglalt tárterület *50%-os* szabad területének kiosztása, amely **csak** az első biztonsági mentéshez szükséges. Az első biztonsági mentés befejezése után az 50%-os szabad terület nem követelmény a biztonsági mentéshez.
- A tárfiókonkénti lemezszám korlátozása attól függ, hogy milyen gyakorisággal használják a lemezeket az IaaS virtuális gépen futó alkalmazások. Általános gyakorlatként, ha 5–10 vagy több lemez található egyetlen tárfiókon, akkor a terhelést úgy egyenlítheti ki, hogy egyes lemezeket külön tárfiókokra helyez át.
- Ha felügyelt lemezekkel rendelkező virtuális gépeket szeretne visszaállítani a PowerShell használatával, [adja meg a](./backup-azure-vms-automation.md#restore-managed-disks)további ***TargetResourceGroupName*** paramétert annak az erőforrásnak a megadásához, amelybe a felügyelt lemezek vissza lesznek állítva

## <a name="backup-costs"></a>Biztonsági mentési költségek

A Azure Backupekkel biztonsági mentést folytató Azure-beli virtuális gépek [Azure Backup díjszabásra](https://azure.microsoft.com/pricing/details/backup/)vonatkoznak.

A számlázás nem indul el, amíg az első sikeres biztonsági mentés be nem fejeződik. Ekkor megkezdődik a tárterület és a védett virtuális gépek számlázása. A számlázás addig tart, amíg a virtuális gép biztonsági mentési adatai egy tárolóban tárolódnak. Ha leállítja a virtuális gép védelmét, de a virtuális gép biztonsági mentési adatai egy tárolóban találhatóak, a számlázás továbbra is fennáll.

Egy adott virtuális gép számlázása csak akkor leáll, ha a védelem le van állítva, és az összes biztonsági mentési érték törlődik. Ha a védelem leáll, és nincsenek aktív biztonsági mentési feladatok, a legutóbbi sikeres virtuális gép biztonsági mentésének mérete a havi számlán használt védett példány mérete lesz.

A védett példány méretének kiszámítása a virtuális gép *tényleges* méretétől függ. A virtuális gép mérete a virtuális gép összes adatmennyiségének összege, kivéve az ideiglenes tárolót. A díjszabás az adatlemezeken tárolt tényleges adatokon alapul, nem a virtuális géphez csatlakoztatott adatlemezek maximális támogatott méretével.

Hasonlóképpen, a biztonsági mentési tár a Azure Backup tárolt adatok mennyisége alapján történik, amely az egyes helyreállítási pontokban lévő tényleges adatok összege.

Tegyük fel például, hogy egy olyan a2-es szabványú virtuális gép, amely két további adatlemezt tartalmaz, amelyek maximális mérete 32 TB. A következő táblázat az egyes lemezeken tárolt tényleges adatokat mutatja be:

**Lemez** | **Maximális méret** | **Tényleges adatok jelennek meg**
--- | --- | ---
Operációsrendszer-lemez | 32 TB | 17 GB
Helyi/ideiglenes lemez | 135 GB | 5 GB (nem tartalmazza a biztonsági mentést)
1. adatlemez | 32 TB| 30 GB
2. adatlemez | 32 TB | 0 GB

Ebben az esetben a virtuális gép tényleges mérete 17 GB + 30 GB + 0 GB = 47 GB. Ez a védett példány mérete (47 GB) lesz a havi számla alapja. Ahogy a virtuális gépen lévő adatmennyiség növekszik, a számlázáshoz használt védett példány mérete megegyezik.

## <a name="next-steps"></a>Következő lépések

- [Felkészülés az Azure-beli virtuális gépek biztonsági mentéséhez](backup-azure-arm-vms-prepare.md).