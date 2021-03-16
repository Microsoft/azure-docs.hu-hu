---
title: Gyakori kérdésekre adott válaszok
description: 'Gyakori kérdésekre adott válaszok a következő témakörökben: az Azure Backup szolgáltatásai, mint például a Recovery Services-tárolók, a biztonsági mentés lehetséges elemei, működése, titkosítás és korlátozások. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: ac58cee66aa2a89efb7194a051801b068628d3bc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467629"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – Gyakori kérdések

Ez a cikk az Azure Backup szolgáltatással kapcsolatban gyakran feltett kérdésekre ad választ.

## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Az egyes Azure-előfizetésekben létrehozható tárolók száma korlátozott?

Igen. Előfizetésenként az Azure Backup minden támogatott régiójához legfeljebb 500 Recovery Services-tároló hozható létre. Ha több tárolóra van szüksége, hozzon létre egy további előfizetést.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Az egyes tárolókhoz regisztrálható kiszolgálók/gépek száma korlátozott?

Tárolónként maximum 1000 Azure-beli virtuális gépet regisztrálhat. Ha a Microsoft Azure Backup-ügynököt használja, akár 50 MARS-ügynököt is regisztrálhat. Továbbá regisztrálhat 50 MABS-kiszolgálókat/DPM-kiszolgálókat egy tárba.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Egy tároló hány adatforrás/elem védelmére képes?

Az összes számítási feladatban (például IaaS VM, SQL, AFS) akár 2000 adatforrás/elem is védhető a tárolóban.
Ha például már védett a 500-es virtuális gépek és a 400 Azure Files a tárolóban lévő megosztások, akkor csak a 1100-os SQL-adatbázisok védelmét tudja biztosítani.

### <a name="how-many-policies-can-i-create-per-vault"></a>Hány szabályzatot lehet létrehozni tárolónként?

Egy tárolóhoz legfeljebb 200 szabályzat rendelhető hozzá.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Ha a cég vagy szervezet egyetlen tárolóval rendelkezik, hogyan tudom a különböző kiszolgálóktól származó adatokat elszigetelni egymástól az adatok visszaállításakor?

Az együtt helyreállítani kívánt kiszolgálóadatoknak ugyanazt a jelszót kell használniuk, amelyet a biztonsági mentés beállításakor adhat meg. Ha el szeretné különíteni egy adott kiszolgáló vagy kiszolgálók helyreállítását, adjon meg egy jelszót, amelyet csak az adott kiszolgáló(k)hoz használ. Például a humánerőforrás-kiszolgálók használhatnának egy titkosító hozzáférési kódot, a könyvelési kiszolgálók egy másikat és a tároló kiszolgálók egy harmadikat.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Át lehet helyezni egy tárolót az előfizetések között?

Igen. Recovery Services-tároló áthelyezéséhez tekintse meg ezt a [cikket](backup-azure-move-recovery-services-vault.md).

### <a name="can-i-move-backup-data-to-another-vault"></a>Áthelyezhetők a biztonsági másolati adatok egy másik tárolóba?

Nem. A tárolókban tárolt biztonságimásolat-adatok nem helyezhetők át más tárolóba.

### <a name="can-i-change-the-storage-redundancy-setting-after-a-backup"></a>Módosíthatom a tárolási redundancia beállítást a biztonsági mentés után?

Alapértelmezés szerint a tárolási replikálás típusa a Geo-redundáns tárolás (GRS). A biztonsági mentés konfigurálása után a módosítás lehetőség le van tiltva, és nem módosítható.

![Tárolási replikálás típusa](./media/backup-azure-backup-faq/storage-replication-type.png)

Ha már konfigurálta a biztonsági mentést, és a GRS-ről LRS-re kell váltania, akkor tekintse meg a [biztonsági mentés konfigurálása után a GRS-ről a LRS-re való váltást](backup-create-rs-vault.md#how-to-change-from-grs-to-lrs-after-configuring-backup)

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Végrehajtható elemszintű visszaállítás (Item Level Restore, ILR) olyan virtuális gépeken, amelyeknek Recovery Services-tárolóban található a biztonsági másolata?

- Az ILR az olyan Azure-beli virtuális gépeknél támogatott, amelyeknek a biztonsági másolata az Azure VM Backup használatával készült. További információkért tekintse meg ezt a [cikket](backup-azure-restore-files-from-vm.md).
- A ILR nem támogatott a Azure Backup Server (MABS) vagy a System Center DPM által készített helyszíni virtuális gépek online helyreállítási pontjain.

### <a name="how-can-i-move-data-from-the-recovery-services-vault-to-on-premises"></a>Adatok átvitele Recovery Services-tárolóból helyszíni tárolóba

Az adatok közvetlenül a Recovery Services-tárolóból a helyszíni rendszerbe való exportálása a Data Box használatával nem támogatott. Az adatkészletet vissza kell állítani egy Storage-fiókba, majd [Data Box](../databox/data-box-overview.md) vagy [Importálás/exportálás](../import-export/storage-import-export-service.md)használatával áthelyezhető a helyszíni környezetbe.

### <a name="what-is-the-difference-between-a-geo-redundant-storage-grs-vault-with-and-without-the-cross-region-restore-crr-capability-enabled"></a>Mi a különbség a Geo-redundáns tárolási (GRS) tárolók között, és nem engedélyezett a régiók közötti visszaállítási (CRR) képesség?

Ha engedélyezve van a [CRR](azure-backup-glossary.md#cross-region-restore-crr) képesség nélküli [GRS](azure-backup-glossary.md#grs) -tár, a másodlagos régióban lévő adatmennyiség nem érhető el, amíg az Azure be nem jelent egy katasztrófát az elsődleges régióban. Ilyen esetben a visszaállítás a másodlagos régióból történik. Ha a CRR engedélyezve van, még akkor is, ha az elsődleges régió működik, és a másodlagos régióban is aktiválhatja a visszaállítást.

### <a name="can-i-move-a-subscription-that-contains-a-vault-to-a-different-azure-active-directory"></a>Áthelyezhetek egy tárolót tartalmazó előfizetést egy másik Azure Active Directoryra?

Igen. Ha egy olyan előfizetést szeretne áthelyezni (amely tárolót tartalmaz) egy másik Azure Active Directoryba (AD), tekintse meg az [előfizetés átvitele egy másik könyvtárba](../role-based-access-control/transfer-subscription.md)című témakört.

>[!IMPORTANT]
>Az előfizetés áthelyezése után győződjön meg arról, hogy a következő műveleteket hajtja végre:<ul><li>A szerepköralapú hozzáférés-vezérlési engedélyek és az egyéni szerepkörök nem ruházhatók át. Az új Azure AD-ben újra létre kell hoznia az engedélyeket és a szerepköröket.</li><li>Újból létre kell hoznia a tároló felügyelt identitását (MI) a letiltásával és engedélyezésével. Emellett ki kell értékelnie és újra létre kell hoznia a MI engedélyeit.</li><li>Ha a tároló olyan funkciókat használ, amelyek a MI, például a [privát végpontok](private-endpoints.md#before-you-start) és az [ügyfél által felügyelt kulcsok](encryption-at-rest-with-cmk.md#before-you-start)használatát használják, újra kell konfigurálnia a szolgáltatásokat.</li></ul>

## <a name="azure-backup-agent"></a>Az Azure Backup ügynöke

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Hol találhatók az Azure VM Backup Azure Backup-ügynökére vonatkozó gyakori kérdések?

- Az Azure-beli virtuális gépeken futó ügynökkel kapcsolatban tekintse át ezeket a [gyakori kérdéseket](backup-azure-vm-backup-faq.yml).
- Az Azure-fájlmappák biztonsági másolatainak létrehozásához használt ügynökkel kapcsolatban tekintse át ezeket a [gyakori kérdéseket](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Általános biztonsági mentés

### <a name="are-there-limits-on-backup-scheduling"></a>Vonatkoznak korlátozások a biztonsági mentések ütemezésére?

Igen.

- A Windows Server vagy Windows rendszerű gépekről naponta legfeljebb három alkalommal lehet biztonsági másolatot készíteni. A napi vagy heti ütemezéseket az ütemezési szabályzattal adhatja meg.
- A DPM biztonsági mentését naponta a legfeljebb kétszer lehet elvégezni. Az ütemezési szabályzatban napi, heti, havi vagy éves gyakoriság állítható be.
- Az Azure-beli virtuális gépekről naponta egyszer készíthet biztonsági másolatot.

### <a name="what-operating-systems-are-supported-for-backup"></a>Mely operációs rendszerekről lehet biztonsági mentést készíteni?

Az Azure Backup a következő operációs rendszerek fájljainak és mappáinak biztonsági mentését támogatja, az Azure Backup Server és a DPM által védett alkalmazások mellett.

**OS** | **Termékváltozat** | **Részletek**
--- | --- | ---
Munkaállomás | |
Windows 10, 64 bites | Enterprise, Pro, Home | A gépekre telepítve kell lenniük a legújabb szervizcsomagoknak és frissítéseknek.
Windows 8.1, 64 bites | Enterprise, Pro | A gépekre telepítve kell lenniük a legújabb szervizcsomagoknak és frissítéseknek.
Windows 8, 64 bites | Enterprise, Pro | A gépekre telepítve kell lenniük a legújabb szervizcsomagoknak és frissítéseknek.
Windows 7, 64 bites | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | A gépekre telepítve kell lenniük a legújabb szervizcsomagoknak és frissítéseknek.
Kiszolgáló | |
Windows Server 2019, 64 bites | Standard, Datacenter, Essentials | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2016, 64 bites | Standard, Datacenter, Essentials | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2012 R2, 64 bites | Standard, Datacenter, Foundation | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2012, 64 bites | Datacenter, Foundation, Standard | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Storage Server 2016, 64 bites | Standard, Workgroup | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Storage Server 2012 R2, 64 bites | Standard, Workgroup, Essential | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Storage Server 2012, 64 bites | Standard, Workgroup | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2008 R2 SP1, 64 bites | Standard, Enterprise, Datacenter, Foundation | A legújabb frissítésekkel.
Windows Server 2008, 64 bites | Standard, Enterprise, Datacenter | A legújabb frissítésekkel.

Az Azure Backup nem támogatja a 32 bites operációs rendszereket.

A Linux rendszerű Azure-beli virtuális gépek biztonsági mentése esetén az Azure Backup az [Azure által támogatott disztribúciókat](../virtual-machines/linux/endorsed-distros.md) támogatja, a Core OS Linux és a 32 bites operációs rendszerek kivételével. Egyéb saját Linux-disztribúciók is működtethetők, ha a virtuális gép ügynöke elérhető a virtuális gépen, és a rendszer támogatja a Pythont.

### <a name="are-there-size-limits-for-data-backup"></a>Vannak méretkorlátai az adatok biztonsági mentésének?

A következő méretkorlátok vannak érvényben:

Operációs rendszer/számítógép | Adatforrás méretkorlátja
--- | ---
Windows 8 vagy újabb | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 vagy újabb | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Azure VM | Lásd az [Azure virtuális gépek biztonsági mentésének támogatási mátrixát](./backup-support-matrix-iaas.md#vm-storage-support)

### <a name="how-is-the-data-source-size-determined"></a>Adatforrás méretének meghatározása

Az alábbi táblázat megmagyarázza, hogy az egyes adatforrásméretek hogyan lettek meghatározva.

**Adatforrás** | **Részletek**
--- | ---
Kötet |Azon adatok mennyisége, amelyekről biztonsági másolat készül a biztonsági mentés alatt álló, egyetlen kötetből álló virtuális gépről.
SQL Server-adatbázis |A biztonsági mentés alatt álló önálló adatbázis mérete.
SharePoint | Egy biztonsági mentés alatt álló SharePoint-farm tartalmának és konfigurációs adatbázisainak összessége.
Exchange |Egy biztonsági mentés alatt álló Exchange-kiszolgáló összes Exchange-adatbázisa.
BMR/rendszerállapot |A biztonsági mentés alatt álló gép BMR-ének vagy rendszerállapotának minden egyes másolata.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Vonatkozik méretkorlátozás a Recovery Services-tárolóval biztonsági másolatokba mentett adatok mennyiségére?

A Recovery Services-tároló használatával nem korlátozható az összes olyan adatmennyiség, amelyről biztonsági másolatot készíthet. Az egyes adatforrások (az Azure-beli virtuális gépeken kívül) legfeljebb 54 400 GB méretűek lehetnek. A korlátokkal kapcsolatos további információkért tekintse [meg a tároló korlátai szakaszt a támogatási mátrixban](./backup-support-matrix.md#vault-support).

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Miért kisebb a Recovery Services-tárolóba átvitt adatok mérete a biztonsági mentéshez kiválasztott adatok méreténél?

Az Azure Backup ügynökétől, a DPM-ből és az Azure Backup Serverről származó adatokat, amelyekről biztonsági mentés készül, a rendszer tömöríti és titkosítja az átvitel előtt. A tömörítés és a titkosítás alkalmazása után a tárolóban lévő adatok 30–40 százalékkal kisebbek lesznek.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Törölhetek egyes fájlokat a tároló helyreállítási pontjáról?

Nem, az Azure Backup nem támogatja az egyes elemek törlését vagy eltávolítását a tárolt biztonsági másolatokból.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Ha megszakítok egy már elindult biztonsági mentési feladatot, az átküldött adatok biztonsági másolata törlődik?

Nem. A biztonsági mentési feladat megszakításának pillanata előtt a tárolóba már átvitt minden adat a tárolóban marad.

- Az Azure Backup egy ellenőrzőpont-alapú mechanizmust használ, így a biztonsági mentés közben esetenként ellenőrzőpontokat ad az adatok biztonsági másolatához.
- Mivel ellenőrzőpontok vannak az adatok biztonsági másolatában, a következő biztonsági mentési folyamat ellenőrizni tudja a fájlok épségét.
- A következő biztonsági mentési feladat a korábban már mentett adatokra épülő növekményes mentés lesz. A növekményes mentések csak az új vagy módosított adatok átvitelét jelentik, így a sávszélesség-használat optimalizálható.

Ha megszakítja egy Azure virtuális gép valamely biztonsági mentését, a rendszer a már átvitt adatokat figyelmen kívül hagyja. A következő biztonsági mentési feladat az utolsó sikeres biztonsági mentéshez képest végzi el az adatok növekményes mentését.

## <a name="retention-and-recovery"></a>Megőrzés és helyreállítás

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>A DPM-es és a DPM nélküli Windows gépek adatmegőrzési szabályai ugyanazok?

Igen, mindkettőnek napi, heti, havi és évi adatmegőrzési szabályai vannak.

### <a name="can-i-customize-retention-policies"></a>Testre lehet szabni az adatmegőrzési szabályokat?

Igen, a szabályokat testre lehet szabni. Dönthet például úgy, hogy konfigurál heti és napi adatmegőrzési követelményeket, de évi és havi szabályokat nem.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Használhatok különböző időpontokat a biztonsági mentések ütemezéséhez és az adatmegőrzési szabályokhoz?

Nem. Megtartási házirendeket csak biztonsági mentési pontokon lehet alkalmazni. Ezen a képen például a 12:00-kor és 18:00-kor készített biztonsági mentések adatmegőrzési szabálya látható.

![Biztonsági mentés és megtartás ütemezése](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Ha egy biztonsági másolatot a rendszer hosszú ideig őriz, több időbe telik egy régebbi adatpont helyreállítása?

Nem. A legrégebbi és a legutóbbi pont helyreállítási ideje azonos. Minden helyreállítási pont teljes pontként viselkedik.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Ha minden helyreállítási pont olyan, mint egy teljes pont, ez befolyásolja a teljes számlázandó biztonsági mentési tárolást?

A tipikus hosszú távú megtartási pontok az adatok biztonsági másolatát teljes pontokként tárolják.

- A teljes pontok tárolás szempontjából *nem hatékonyak*, azonban könnyebben és gyorsabban visszaállíthatók.
- A növekményes másolatok tárolás szempontjából *hatékonyak*, de egy adatlánc visszaállítását teszik szükségessé, ami befolyásolja a helyreállítási időt.

Az Azure Backup tárolási architektúrája ezen két megoldás előnyeit ötvözi az adatok optimális tárolásával a gyors visszaállítás érdekében és alacsony tárolási költségekkel. Ez a módszer gondoskodik róla, hogy a bemenő és kimenő sávszélesség is hatékonyan legyen felhasználva. Az adattárolási hely és az adatok helyreállításához szükséges idő mértéke a minimális szinten van tartva. További információk a [növekményes biztonsági másolatokról](backup-architecture.md#backup-types).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>A létrehozható helyreállítási pontok száma korlátozott?

Védett példányonként 9999 helyreállítási pontot hozhat létre. A védett példányok olyan számítógépek, (fizikai vagy virtuális) kiszolgálók vagy számítási feladatok, amelyeknek a biztonsági másolatai az Azure-ba kerülnek.

- További információk a [biztonsági mentésről és a megőrzésről](./backup-support-matrix.md).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Hányszor állíthatom helyre az Azure-beli biztonsági másolattal rendelkező adatokat?

A helyreállítások száma nem korlátozható Azure Backupból.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Az adatok visszaállításakor fizetnem kell az Azure-ból kimenő forgalomért?

Nem. A helyreállítás ingyenes, és nem kell fizetni a kimenő forgalomért.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Mi történik, ha módosítom a biztonsági mentési szabályzatomat?

Amikor új szabályzatot léptet érvénybe, a rendszer annak az ütemezését és megőrzési szabályait fogja követni.

- Ha a megőrzés kibővül, a meglévő helyreállítási pontok úgy vannak megjelölve, hogy az új szabályzatnak megfelelően megőrizze őket.
- Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítási feladat során törlendőként lesznek megjelölve.

### <a name="how-long-is-data-retained-when-stopping-backups-but-selecting-the-option-to-retain-backup-data"></a>Mennyi ideig őrzi meg a rendszer az adatokat a biztonsági másolatok leállításakor, de a biztonsági mentési adatokat is megőrzi?

Ha a biztonsági mentések leállnak, és az adatokat megőrzi, az adattörlésre vonatkozó meglévő szabályzati szabályok megszűnnek, és az adatokat határozatlan ideig megőrzi a rendszer, amíg a rendszergazda el nem indítja a törlést.

## <a name="encryption"></a>Titkosítás

### <a name="is-the-data-sent-to-azure-encrypted"></a>Az Azure-ba küldött adatok titkosítottak?

Igen. Az adatokat a rendszer az AES256 használatával titkosítja a helyszíni gépen. Az adatokat a rendszer egy biztonságos HTTPS-kapcsolaton keresztül küldi el. A felhőben továbbított adatokat csak a HTTPS-kapcsolat védi a tároló és a helyreállítási szolgáltatás között. A helyreállítási szolgáltatás és a felhasználó gépe közötti adatátvitelt iSCSI protokoll védi. Az iSCSI-csatorna védelméről Secure Tunneling (biztonságos protokollbújtatás) gondoskodik.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Az adatokról készült biztonsági másolat az Azure-on is titkosított?

Igen. Az Azure-ban az inaktív állapotú adatok titkosítva vannak.

- Helyszíni biztonsági mentéshez az inaktív állapotú adatokat védő titkosítás használható az Azure-ra irányuló biztonsági mentés során megadott jelszóval.
- Azure-beli virtuális gépek esetén a rendszer Storage Service Encryption (SSE) használatával titkosítja az inaktív adatokat.

A Microsoft semmilyen időpontban nem tudja visszafejteni a biztonsági mentési adatvesztést.

### <a name="what-is-the-minimum-length-of-the-encryption-key-used-to-encrypt-backup-data"></a>Mekkora a biztonsági mentési adatmennyiség titkosításához használt titkosítási kulcs minimális hossza?

A Microsoft Azure Recovery Services-(MARS-) ügynök által használt titkosítási kulcs olyan jelszóból származik, amelynek legalább 16 karakter hosszúnak kell lennie. Az Azure-beli virtuális gépek esetében nincs korlátozva az Azure kulcstartó által használt kulcsok hossza.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Mi történik, ha elveszítem a titkosítási kulcsot? Helyre tudom állítani az adatokat? A Microsoft helyre tudja állítani az adatokat?

Az adatok biztonsági másolatának titkosításához használt kulcs csak Önnek van meg. A Microsoft nem tart fenn másolatot az Azure-ban, és nem rendelkezik hozzáféréssel a kulcshoz. Ha elveszíti a kulcsot, a Microsoft nem tudja helyreállítani az adatok biztonsági másolatát.

## <a name="next-steps"></a>Következő lépések

További gyakori kérdéseket is áttekinthet:

- [Gyakori kérdések](backup-azure-vm-backup-faq.yml) az Azure-beli virtuális gépek biztonsági mentéséről.
- [Gyakori kérdések](backup-azure-file-folder-backup-faq.md) az Azure Backup-ügynökről
