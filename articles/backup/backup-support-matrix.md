---
title: Az Azure Backup támogatási mátrixa
description: Összefoglalja a támogatási beállításokat és korlátozásokat a Azure Backup számára.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5c74a34efe8075ab7a34fab4570d9513900b3f81
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517419"
---
# <a name="support-matrix-for-azure-backup"></a>Támogatási mátrix a Azure Backup

Az adatok biztonsági [Azure Backup](backup-overview.md) a felhőplatformon Microsoft Azure biztonsági adatokat. Ez a cikk az általános támogatási beállításokat és korlátozásokat foglalja össze a Azure Backup és üzemelő példányok esetében.

Egyéb támogatási mátrixok is elérhetők:

- Azure-beli virtuális [gépek biztonsági mentésének támogatási mátrixa](backup-support-matrix-iaas.md)
- Támogatási mátrix a biztonsági mentéshez a [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS) használatával](backup-support-matrix-mabs-dpm.md)
- Támogatási mátrix a biztonsági mentéshez a [Microsoft Azure Recovery Services- (MARS-) ügynök használatával](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Tárolótámogatás

Azure Backup Recovery Services-tárolókat használ a következő számítási feladatok biztonsági mentésének vezénylése és kezelése érdekében: Azure-beli virtuális gépek, Sql Azure-beli virtuális gépeken, SAP HANA Azure-beli virtuális gépeken, Azure-fájlmegosztások és helyszíni számítási feladatok az Azure Backup Agent, az Azure Backup Server és az System Center DPM használatával. Emellett Recovery Services-tárolókat is használ a számítási feladatok biztonságimentett adatainak tárolására.

Az alábbi táblázat a Recovery Services-tárolók funkcióit ismerteti:

**Szolgáltatás** | **Részletek**
--- | ---
**Tárolók az előfizetésben** | Akár 500 Recovery Services-tároló egyetlen előfizetésben.
**Tárolóban tárolt gépek** | Akár 2000 adatforrást is védhet egyetlen tárolóban az összes számítási feladathoz (például Azure-beli virtuális gépekhez, SQL Server VM-hoz, MABS-kiszolgálókhoz stb.).<br><br>Akár 1000 Azure-beli virtuális gép egyetlen tárolóban.<br/><br/> Egyetlen tárolóban legfeljebb 50 MABS-kiszolgáló regisztrálható.
**Adatforrások** | Az egyes adatforrások [maximális mérete](./backup-azure-backup-faq.yml#how-is-the-data-source-size-determined-) 54 400 GB. Ez a korlát nem vonatkozik az Azure-beli virtuális gépek biztonsági másolataira. A tárolóba biztonsági adatokat nem lehet biztonsági adatokat visszahozni, és nem vonatkoznak korlátok.
**Biztonsági másolatok tárolóba** | **Azure-beli virtuális gépek:** Naponta egyszer.<br/><br/>**A DPM/MABS által védett gépek:** Naponta kétszer.<br/><br/> **A marsi ügynökkel közvetlenül biztonságimentett gépek:** Naponta háromszor.
**Biztonsági másolatok tárolók között** | A biztonsági mentés egy régión belül van.<br/><br/> Minden olyan Azure-régióban szüksége van egy tárolóra, amely tartalmazza azokat a virtuális gépeket, amelyekről biztonsági szeretne biztonsági adatokat. Egy másik régióba nem lehet biztonsági ásni.
**Tárolók áthelyezése** | A [tárolókat áthelyezheti](./backup-azure-move-recovery-services-vault.md) előfizetések között, vagy ugyanabban az előfizetésben lévő erőforráscsoportok között. A tárolók régiók közötti áthelyezése azonban nem támogatott.
**Adatok áthelyezése tárolók között** | A biztonsági adatok tárolók közötti mozgatása nem támogatott.
**Tároló tárolótípusának módosítása** | A biztonsági másolatok tárolása előtt módosíthatja a tároló replikációs típusát (georedundáns tárolás vagy helyileg redundáns tárolás). A biztonsági mentések a tárolóban való megkezdése után a replikáció típusa nem módosítható.
**Zónaredundáns tárolás (ZRS)** | Elérhető a Egyesült Királyság déli régiója (UKS) és a Kelet-Ázsia (SEA) régióban.
**Privát végpontok** | Ez [a szakasz a](./private-endpoints.md#before-you-start) recovery service-tároló privát végpontjainak létrehozására vonatkozó követelményeket tartalmazza.  

## <a name="on-premises-backup-support"></a>Helyszíni biztonsági mentés támogatása

A helyszíni gépek biztonságimentése a következő támogatott:

**Gép** | **Miről van biztonságimentve?** | **Hely** | **Funkciók**
--- | --- | --- | ---
**Windows rendszerű gép közvetlen biztonsági mentése MARS-ügynökkel** | Fájlok, mappák, rendszerállapot | Biztonságimentés a Recovery Services-tárolóba. | Biztonságimentás naponta háromszor<br/><br/> Nincs alkalmazásokat indító biztonsági mentés<br/><br/> Fájl, mappa, kötet visszaállítása
**Linux rendszerű gép közvetlen biztonsági mentése MARS-ügynökkel** | A biztonsági mentés nem támogatott
**Biztonságimentás a DPM-be** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonságimentet a helyi DPM-tárolóba. A DPM ezután biztonsági okat hoz a tárolóba. | Alkalmazásokat indító pillanatképek<br/><br/> Teljes részletesség biztonsági mentéshez és helyreállításhoz<br/><br/> A virtuális gépek által támogatott Linux (Hyper-V/VMware)<br/><br/> Az Oracle nem támogatott
**Biztonságimentás a MABS-be** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonságimentet a MABS helyi tárolóba. A MABS ezután biztonsági okat hoz a tárolóba. | Alkalmazásokat indító pillanatképek<br/><br/> Teljes részletesség a biztonsági mentéshez és a helyreállításhoz<br/><br/> A virtuális gépekhez támogatott Linux (Hyper-V/VMware)<br/><br/> Az Oracle nem támogatott

## <a name="azure-vm-backup-support"></a>Azure-beli virtuális gépek biztonsági mentésének támogatása

### <a name="azure-vm-limits"></a>Azure-beli virtuális gépek korlátai

**Korlát** | **Részletek**
--- | ---
**Azure-beli virtuális gépek adatlemezei** | Lásd az [Azure-beli virtuális gépek biztonsági mentésének támogatási mátrixát.](./backup-support-matrix-iaas.md#vm-storage-support)
**Azure-beli virtuális gép adatlemezének mérete** | Az egyes lemezek mérete a virtuális gépek összes lemezén legfeljebb 32 TB és legfeljebb 256 TB lehet.

### <a name="azure-vm-backup-options"></a>Azure-beli virtuális gépek biztonsági mentési lehetőségei

Az Azure-beli virtuális gépek biztonságimentés-ének támogatása a következő:

**Gép** | **Miről van biztonságimentve?** | **Hely** | **Funkciók**
--- | --- | --- | ---
**Azure-beli virtuális gépek biztonsági mentése virtuálisgép-bővítmény használatával** | Teljes virtuális gép | Biztonságimentet a tárolóba. | A virtuális gép biztonsági mentésének engedélyezésekor telepített bővítmény.<br/><br/> Naponta egyszer kell biztonsági ről biztonsági mentetni.<br/><br/> Alkalmazásokat indító biztonsági mentés Windows rendszerű virtuális gépekhez; fájl konzisztens biztonsági mentés Linux rendszerű virtuális gépekhez. A Linux rendszerű gépek alkalmazáskonzisztenciáját egyéni szkriptekkel konfigurálhatja.<br/><br/> Virtuális gép vagy lemez visszaállítása.<br/><br/>[A tartományvezérlők Active Directory biztonsági mentése és visszaállítása](active-directory-backup-restore.md) támogatott.<br><br> Az Azure-beli virtuális gépekről nem lehet biztonságimentése helyszíni helyre.
**Azure-beli virtuális gépek biztonsági mentése MARS-ügynökkel** | Fájlok, mappák, rendszerállapot | Biztonságimentet a tárolóba. | Naponta háromszor kell biztonsági edz.<br/><br/> Ha a teljes virtuális gép helyett csak bizonyos fájlokról vagy mappákról szeretne biztonsági mentéset, a MARS-ügynök a virtuálisgép-bővítmény mellett is futhat.
**Azure-beli virtuális gép DPM-fel** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonságimentet a DPM-et futtató Azure-beli virtuális gép helyi tárolójában. A DPM ezután biztonsági okat hoz a tárolóba. | Alkalmazásokat indító pillanatképek.<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége.<br/><br/> A linuxos virtuális gépek támogatottak (Hyper-V/VMware).<br/><br/> Az Oracle nem támogatott.
**Azure-beli virtuális gép MABS-sel** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonságimentet a MABS-t futtató Azure-beli virtuális gép helyi tárolóiba. A MABS ezután biztonsági okat hoz a tárolóba. | Alkalmazásokat indító pillanatképek.<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége.<br/><br/> A linuxos virtuális gépek támogatottak (Hyper-V/VMware).<br/><br/> Az Oracle nem támogatott.

## <a name="linux-backup-support"></a>Linuxos biztonsági mentés támogatása

Ha Linux rendszerű gépekről szeretne biztonságimentet, a következő támogatott rendszerek támogatottak:

**Biztonsági mentés típusa** | **Linux (Azure által támogatott)**
--- | ---
**Linuxot futtató helyszíni gép közvetlen biztonsági mentése** | Nem támogatott. A MARS-ügynök csak Windows rendszerű gépeken telepíthető.
**Ügynökbővítmény használata Linuxot futtató Azure-beli virtuális gépek biztonságiának biztonsági felhasználásával** | Alkalmazás-konzisztens biztonsági mentés [egyéni szkriptekkel.](backup-azure-linux-app-consistent.md)<br/><br/> Fájlszintű helyreállítás.<br/><br/> Visszaállítás virtuális gép helyreállítási pontból vagy lemezről való létrehozásával.
**A DPM használata Linuxot futtató helyszíni gépek biztonságimentése** | Linux rendszerű vendég virtuális gépek fájl konzisztens biztonsági mentése Hyper-V-ben és VMware-ben.<br/><br/> Hyper-V és VMware Linux vendég virtuális gépek visszaállítása.
**A MABS használata Linuxot futtató helyszíni gépek biztonságimentése** | Linux rendszerű vendég virtuális gépek fájl konzisztens biztonsági mentése Hyper-V-ben és VMware-ben.<br/><br/> Hyper-V- és VMware Linux-vendég virtuális gépek visszaállítása.
**Linux rendszerű Azure-beli virtuális gépek biztonságiának a MABS vagy a DPM használata** | Nem támogatott.

## <a name="daylight-saving-time-support"></a>Nyári időszámítás támogatása

Azure Backup nem támogatja az Azure-beli virtuális gépek biztonsági mentéséhez szükséges nyári időszámításhoz szükséges automatikus órabeállítást. Nem tolja előre vagy hátra a biztonsági mentés óráját. Annak biztosítása érdekében, hogy a biztonsági mentés a kívánt időpontban fut, szükség szerint módosítsa manuálisan a biztonsági mentési házirendeket.

## <a name="disk-deduplication-support"></a>Lemezdeduplikáció támogatása

A lemezdeduplikáció támogatása a következő:

- A lemezdeduplikáció akkor támogatott a helyszínen, ha a DPM vagy a MABS használatával biztonsági adatokat használ a Windows rendszert futtató Hyper-V virtuális gépek biztonsági frissítésére. A Windows Server adatdeduplikációt hajt végre (a gazdagép szintjén) a virtuális géphez biztonsági másolatként csatlakoztatott virtuális merevlemezen (VHD-k).
- A deduplikáció semmilyen Backup-összetevő esetén nem támogatott az Azure-ban. Ha a DPM és a MABS telepítve van az Azure-ban, a virtuális géphez csatolt tárolólemezek nem deduplikálhatóak.

## <a name="security-and-encryption-support"></a>Biztonsági és titkosítási támogatás

Azure Backup támogatja az átvitel közbeni és a nem használt adatok titkosítását.

### <a name="network-traffic-to-azure"></a>Hálózati forgalom az Azure-ba

- A kiszolgálókról a Recovery Services-tárolóba való biztonsági mentési forgalom titkosítása a 256-os Advanced Encryption Standard történik.
- A biztonsági mentési adatokat biztonságos HTTPS-kapcsolaton keresztül küldi el a rendszer.

### <a name="data-security"></a>Adatbiztonság

- A biztonsági mentési adatokat a rendszer titkosított formában tárolja a Recovery Services-tárolóban.
- Amikor a MARS-ügynökkel biztonsági adatokat hoznak Azure Backup helyszíni kiszolgálókról, az adatok titkosítása jelszóval történik az Azure Backup-be való feltöltés előtt, és a visszafejtése csak azután történik meg, hogy letöltötték Azure Backup.
- Az Azure-beli virtuális gépek biztonsági létrehozása során be kell állítania a titkosítást a *virtuális* gépen belül.
- Az Azure Backup támogatja az Azure Disk Encryption szolgáltatást, amely a BitLockert használja Windows rendszerű virtuális gépeken és a **dm-crypt**-et Linux rendszerű virtuális gépeken.
- A háttérbeli virtuális Azure Backup [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md)használ, amely védi az adatokat az adatok számára.

**Gép** | **Átvitel közben** | **Nem 800 00**
--- | --- | ---
**Helyszíni Windows-gépek DPM/MABS nélkül** | ![Igen][green] | ![Igen][green]
**Azure-beli virtuális gépek** | ![Igen][green] | ![Igen][green]
**Helyszíni Windows-gépek vagy Azure-beli virtuális gépek A DPM-et** | ![Igen][green] | ![Igen][green]
**Helyszíni Windows rendszerű gépek vagy MABS-t vagy Azure-beli virtuális gépeket** | ![Igen][green] | ![Igen][green]

## <a name="compression-support"></a>Tömörítés támogatása

A biztonsági mentés támogatja a biztonsági mentési forgalom tömörítését az alábbi táblázatban összefoglalva.

- Azure-beli virtuális gépek esetén a virtuálisgép-bővítmény közvetlenül az Azure Storage-fiókból olvassa be az adatokat a tárolóhálózaton keresztül, ezért nem szükséges tömöríteni ezt a forgalmat.
- Ha DPM-et vagy MABS-t használ, sávszélességet takaríthat meg, ha tömöríti az adatokat a biztonsági mentés előtt.

**Gép** | **Tömörítés MABS-be/DPM-be (TCP)** | **Tömörítés tárolóba (HTTPS)**
--- | --- | ---
**Helyszíni Windows rendszerű gépek közvetlen biztonsági mentése** | NA | ![Igen][green]
**Azure-beli virtuális gépek biztonsági mentése virtuálisgép-bővítmény használatával** | NA | NA
**Helyszíni/Azure-beli gépek biztonsági mentése a MABS/DPM használatával** | ![Igen][green] | ![Igen][green]

## <a name="retention-limits"></a>Megőrzési korlátok

**Beállítás** | **Korlátok**
--- | ---
**Helyreállítási pontok maximális száma védett példányonként (gép vagy számítási feladat)** | 9,999
**Helyreállítási pont maximális lejárati ideje** | Nincs korlát
**A DPM/MABS biztonsági mentésének maximális gyakorisága** | 15 percenként az SQL Serverhez<br/><br/> Óránként egyszer más számítási feladatokhoz
**Tároló biztonsági mentésének maximális gyakorisága** | MARS-t futtató helyszíni Windows-gépek vagy **Azure-beli virtuális gépek:** Naponta három<br/><br/> **DPM/MABS:** Naponta kettő<br/><br/> **Azure-beli virtuális gépek biztonsági mentése:** Naponta egy
**Helyreállítási pont megőrzése** | Napi, heti, havi, éves
**Maximális megőrzési időtartam** | A biztonsági mentés gyakoriságától függően változik
**Helyreállítási pontok a DPM-/MABS-lemezen** | Fájlkiszolgálók esetén 64; 448 alkalmazáskiszolgálókhoz <br/><br/>Korlátlan szalagos helyreállítási pontok helyszíni DPM-hez

## <a name="cross-region-restore"></a>Régiók közötti visszaállítás

Azure Backup a Régiók közötti visszaállítás funkcióval megerősíti az adatok rendelkezésre állását és rugalmasságát, így teljes körű vezérlést biztosít az adatok másodlagos régióba való visszaállításához. A funkció konfigurálásához keresse fel a Régiók közötti visszaállítás [beállítása cikket.](backup-create-rs-vault.md#set-cross-region-restore). Ez a szolgáltatás a következő felügyeleti típusok esetében támogatott:

| Biztonságimásolat-kezelés típusa | Támogatott                                                    | Támogatott régiók |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Az Azure-beli virtuális gépek (beleértve a titkosított Azure-beli virtuális gépeket is) támogatottak felügyelt és nem felügyelt lemezekkel is. Klasszikus virtuális gépek esetén nem támogatott. | Minden nyilvános Azure-régióban és szuverén régióban elérhető, kivéve Közép-Franciaország, Ausztrália középső régiója, Dél-Afrika északi régiója, Egyesült Arab Emírségek északi régiója, Svájc északi régiója, Nyugat-Németország, Kelet-Németország, UG IOWA és UG Virginia. <br>Az ilyen régiókban való használatra vonatkozó információkért lépjen kapcsolatba a következővel: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| SQL /SAP HANA | Előzetes verzióban                                                      | Minden nyilvános Azure-régióban és szuverén régióban elérhető, kivéve Közép-Franciaország, Ausztrália középső régiója, Dél-Afrika északi régiója, Egyesült Arab Emírségek északi régiója, Svájc északi régiója, Nyugat-Németország, Kelet-Németország, UG IOWA és UG Virginia. <br>Az ilyen régiókban való használatra vonatkozó információkért lépjen kapcsolatba a következővel: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| MARS-ügynök/Helyszíni  | Nem                                                           | N/A               |
| AFS (Azure-fájlmegosztások)                 | Nem                                                           | N/A               |

## <a name="next-steps"></a>Következő lépések

- [Tekintse át az Azure-beli](backup-support-matrix-iaas.md) virtuális gépek biztonsági mentésének támogatási mátrixát.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png