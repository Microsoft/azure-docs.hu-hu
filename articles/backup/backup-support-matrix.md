---
title: Az Azure Backup támogatási mátrixa
description: Összefoglalja a Azure Backup szolgáltatás támogatási beállításait és korlátozásait.
ms.topic: conceptual
ms.date: 02/17/2019
ms.custom: references_regions
ms.openlocfilehash: 9e20c21694678acf7be13ccbbe9cce9af38c32c5
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915709"
---
# <a name="support-matrix-for-azure-backup"></a>Azure Backup támogatási mátrixa

A [Azure Backup](backup-overview.md) használatával biztonsági mentést készíthet az Microsoft Azure Cloud platformra. Ez a cikk a Azure Backup forgatókönyvek és központi telepítések általános támogatási beállításait és korlátozásait foglalja össze.

További támogatási mátrixok érhetők el:

- Az [Azure virtuális gép (VM) biztonsági mentésének](backup-support-matrix-iaas.md) támogatási mátrixa
- A biztonsági mentéshez használható mátrix a [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md) használatával
- A biztonsági mentéshez használható mátrix a [Microsoft Azure Recovery Services-(MARS-) ügynök](backup-support-matrix-mars-agent.md) használatával

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Tár támogatása

A Azure Backup Recovery Services-tárolókat használ a következő számítási feladatokhoz tartozó biztonsági másolatok előkészítéséhez és kezeléséhez – Azure-beli virtuális gépek, SQL az Azure-beli virtuális gépeken, Azure-beli virtuális gépeken SAP HANA, Azure-fájlmegosztás és helyszíni számítási feladatok Azure Backup ügynök, Azure Backup Server és System Center DPM használatával. Emellett Recovery Services-tárolókat használ a számítási feladatok biztonsági másolatának tárolására.

A következő táblázat a Recovery Services-tárolók szolgáltatásait ismerteti:

**Szolgáltatás** | **Részletek**
--- | ---
**Tárolók az előfizetésben** | Akár 500 Recovery Services-tároló egyetlen előfizetésben.
**Tárolóban lévő gépek** | Az összes számítási feladat (például Azure-beli virtuális gépek, SQL Server VM, MABS-kiszolgálók stb.) esetében akár 2000 adatforrás is védhető egyetlen tárolóban.<br><br>Akár 1 000 Azure-beli virtuális gép egyetlen tárolóban.<br/><br/> Akár 50 MABS-kiszolgáló is regisztrálható egyetlen tárolóban.
**Adatforrások** | Egy egyedi [adatforrás](./backup-azure-backup-faq.md#how-is-the-data-source-size-determined) maximális mérete 54 400 GB. Ez a korlát nem vonatkozik az Azure-beli virtuális gépek biztonsági másolatára. A tárolóba visszaállítható teljes adatmennyiségre nem vonatkoznak korlátok.
**Biztonsági mentések a tárba** | **Azure-beli virtuális gépek:** Naponta egyszer.<br/><br/>**DPM/MABS által védett gépek:** Naponta kétszer.<br/><br/> **A közvetlenül a Mars-ügynök használatával biztonsági mentést készít a gépekről:** Naponta háromszor.
**Tárolók közötti biztonsági másolatok** | A biztonsági mentés egy régión belül található.<br/><br/> Minden olyan Azure-régióban szüksége van egy tárolóra, amely tartalmazza a biztonsági mentésre használni kívánt virtuális gépeket. Nem lehet biztonsági másolatot készíteni egy másik régióra.
**Tárolók áthelyezése** | A tárolókat [áthelyezheti](./backup-azure-move-recovery-services-vault.md) előfizetések között, illetve az azonos előfizetésben található erőforráscsoportok között is. A tárolók különböző régiók közötti áthelyezése azonban nem támogatott.
**Adatáthelyezés a tárolók között** | A biztonsági másolatok tárolók közötti áthelyezése nem támogatott.
**Tár tárolási típusának módosítása** | A tároló replikációs típusát (vagy a Geo-redundáns tárterületet vagy a helyileg redundáns tárolást) a biztonsági másolatok tárolása előtt módosíthatja. Miután a biztonsági mentések megkezdődnek a tárolóban, a replikálás típusa nem módosítható.
**Zónaredundáns tárolás (ZRS)** | Elérhető a Egyesült Királyság déli régiója (UKS) és a Dél-Kelet-Ázsia (tengeri) régiókban.

## <a name="on-premises-backup-support"></a>Helyszíni biztonsági mentési támogatás

A következő mi támogatott, ha biztonsági mentést szeretne készíteni a helyszíni gépekről:

**Gép** | **A biztonsági mentés** | **Hely** | **Funkciók**
--- | --- | --- | ---
**Windows rendszerű gép közvetlen biztonsági mentése a MARS-ügynökkel** | Fájlok, mappák, rendszerállapot | Biztonsági mentés Recovery Services-tárolóba. | Napi három alkalommal történő biztonsági mentés<br/><br/> Nincs alkalmazás-kompatibilis biztonsági másolat<br/><br/> Fájl, mappa, kötet visszaállítása
**A Linux-gép közvetlen biztonsági mentése a MARS-ügynökkel** | A biztonsági mentés nem támogatott
**Biztonsági mentés a DPM** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági mentés a helyi DPM-tárolóba. A DPM ezután biztonsági mentést készít a tárból. | Alkalmazás-kompatibilis Pillanatképek<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware)<br/><br/> Az Oracle nem támogatott
**Biztonsági mentés a MABS** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági mentés a MABS helyi tárolóba. A MABS ezután biztonsági mentést készít a tárba. | Alkalmazás-kompatibilis Pillanatképek<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware)<br/><br/> Az Oracle nem támogatott

## <a name="azure-vm-backup-support"></a>Azure-beli virtuális gépek biztonsági mentésének támogatása

### <a name="azure-vm-limits"></a>Azure-beli virtuális gépek korlátai

**Korlátot** | **Részletek**
--- | ---
**Azure-beli VM-adatlemezek** | Tekintse [meg az Azure virtuális gépek biztonsági mentésének támogatási mátrixát](./backup-support-matrix-iaas.md#vm-storage-support).
**Azure-beli virtuális gép adatlemezének mérete** | Az egyes lemezek mérete legfeljebb 32 TB lehet, és a virtuális gép összes lemezének maximális 256 TB-os kombinációja.

### <a name="azure-vm-backup-options"></a>Azure virtuális gépek biztonsági mentési lehetőségei

Ha az Azure-beli virtuális gépek biztonsági mentését kívánja végezni, a következők támogatottak:

**Gép** | **A biztonsági mentés** | **Hely** | **Funkciók**
--- | --- | --- | ---
**Azure virtuális gépek biztonsági mentése virtuálisgép-bővítmény használatával** | Teljes virtuális gép | Biztonsági mentés a tárba. | A bővítmény akkor lett telepítve, ha engedélyezi a virtuális gép biztonsági mentését.<br/><br/> Naponta egyszer készít biztonsági mentést.<br/><br/> App-Aware Backup a Windows rendszerű virtuális gépekhez; fájl-konzisztens biztonsági mentés Linux rendszerű virtuális gépekhez. A Linux rendszerű gépekhez egyéni parancsfájlok használatával is konfigurálhatja az alkalmazások konzisztenciáját.<br/><br/> Virtuális gép vagy lemez visszaállítása.<br/><br/>[Active Directory tartományvezérlők biztonsági mentése és visszaállítása](active-directory-backup-restore.md) támogatott.<br><br> Nem lehet biztonsági mentést készíteni egy Azure-beli virtuális gépről egy helyszíni helyre.
**Azure virtuális gépek biztonsági mentése a MARS-ügynök használatával** | Fájlok, mappák, rendszerállapot | Biztonsági mentés a tárba. | Naponta három alkalommal készíthet biztonsági másolatot.<br/><br/> Ha a teljes virtuális gép helyett adott fájlokról vagy mappákról szeretne biztonsági másolatot készíteni, a MARS-ügynök a virtuálisgép-bővítmény mellett is futhat.
**Azure-beli virtuális gép DPM** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági mentés a DPM-t futtató Azure-beli virtuális gép helyi tárolójába. A DPM ezután biztonsági mentést készít a tárból. | Az alkalmazással kompatibilis Pillanatképek.<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége.<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware).<br/><br/> Az Oracle nem támogatott.
**Azure-beli virtuális gép MABS** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági mentés a MABS-t futtató Azure-beli virtuális gép helyi tárolójába. A MABS ezután biztonsági mentést készít a tárba. | Az alkalmazással kompatibilis Pillanatképek.<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége.<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware).<br/><br/> Az Oracle nem támogatott.

## <a name="linux-backup-support"></a>Linux biztonsági mentési támogatás

Ha a Linux rendszerű gépek biztonsági mentését kívánja végezni, a következők támogatottak:

**Biztonsági mentés típusa** | **Linux (Azure által támogatott)**
--- | ---
**A Linux rendszerű helyszíni gép közvetlen biztonsági mentése** | Nem támogatott. A MARS-ügynököt csak Windows rendszerű gépekre lehet telepíteni.
**Az ügynök-bővítmény használata a Linux rendszerű Azure-beli virtuális gépek biztonsági mentésére** | Alkalmazás-konzisztens biztonsági mentés [egyéni parancsfájlok](backup-azure-linux-app-consistent.md)használatával.<br/><br/> Fájl szintű helyreállítás.<br/><br/> Visszaállítás egy virtuális gép helyreállítási pontról vagy lemezről történő létrehozásával.
**A DPM használata a Linux rendszerű helyszíni gépek biztonsági mentésére** | A Linux vendég virtuális gépek fájl-konzisztens biztonsági mentése a Hyper-V-ben és a VMware-en.<br/><br/> A Hyper-V és a VMware Linux vendég virtuális gépek virtuálisgép-helyreállítása.
**A MABS használata a Linux rendszerű helyszíni gépek biztonsági mentésére** | A Linux vendég virtuális gépek fájl-konzisztens biztonsági mentése a Hyper-V-ben és a VMware-en.<br/><br/> A Hyper-V és a VMware Linux vendég virtuális gépek virtuálisgép-helyreállítása.
**A MABS vagy a DPM használata a Linux Azure-beli virtuális gépek biztonsági mentésére** | Nem támogatott.

## <a name="daylight-saving-time-support"></a>Nyári időtakarékosság támogatása

Az Azure-beli virtuális gépek biztonsági mentései esetében a Azure Backup nem támogatja az automatikus időzítő beállítását a nyári időszámításhoz. Nem továbbítja a biztonsági mentés óráját vagy visszafelé. Ha biztosítani szeretné, hogy a biztonsági mentés a kívánt időpontban fusson, módosítsa a biztonsági mentési szabályzatokat manuálisan, igény szerint.

## <a name="disk-deduplication-support"></a>Lemezes deduplikálás támogatása

A lemezes deduplikálás támogatása a következő:

- A lemezek deduplikálása támogatott a helyszínen, ha DPM vagy MABS használ a Windows rendszerű Hyper-V virtuális gépek biztonsági mentésére. A Windows Server az adatok deduplikálása (a gazdagép szintjén) olyan virtuális merevlemezeken (VHD-k) végezhető el, amelyek a biztonsági mentési tárolóként csatlakoznak a virtuális GÉPHEZ.
- A deduplikálás nem támogatott az Azure-ban a biztonsági mentési összetevőkhöz. Ha a DPM és a MABS üzembe helyezése az Azure-ban történik, a virtuális géphez csatolt tárolók nem lehetnek deduplikálva.

## <a name="security-and-encryption-support"></a>Biztonsági és titkosítási támogatás

A Azure Backup támogatja a titkosítást az átvitel közbeni és a nyugalmi adatokhoz.

### <a name="network-traffic-to-azure"></a>Az Azure-ba irányuló hálózati forgalom

- A kiszolgálókról a Recovery Services-tárolóra irányuló biztonsági mentési forgalom titkosítása Advanced Encryption Standard 256 használatával történik.
- Biztonsági mentési adat küldése biztonságos HTTPS-kapcsolaton keresztül történik.

### <a name="data-security"></a>Adatbiztonság

- A biztonsági mentési adatforgalom titkosított formában tárolódik a Recovery Services-tárolóban.
- Ha az adatok biztonsági mentést végeznek a helyszíni kiszolgálókról a MARS-ügynökkel, az adatok titkosítva lesznek a jelszóval, és csak azután, hogy a rendszer letölti a Azure Backup, és visszafejti azt Azure Backup.
- Az Azure-beli virtuális gépek biztonsági mentésekor be kell állítania a titkosítást a virtuális gépen *belül* .
- Az Azure Backup támogatja az Azure Disk Encryption szolgáltatást, amely a BitLockert használja Windows rendszerű virtuális gépeken és a **dm-crypt**-et Linux rendszerű virtuális gépeken.
- A háttérben Azure Backup az [Azure Storage Service Encryptiont](../storage/common/storage-service-encryption.md)használja, amely megvédi az inaktív adatok védelmét.

**Gép** | **Átvitel közben** | **Nyugalmi állapotban**
--- | --- | ---
**Helyszíni Windows rendszerű gépek DPM/MABS nélkül** | ![Igen][green] | ![Yes][green]
**Azure-beli virtuális gépek** | ![Igen][green] | ![Yes][green]
**Helyszíni Windows-számítógépek vagy Azure-beli virtuális gépek DPM-mel** | ![Igen][green] | ![Yes][green]
**Helyszíni Windows-számítógépek vagy Azure-beli virtuális gépek MABS-mel** | ![Igen][green] | ![Yes][green]

## <a name="compression-support"></a>Tömörítés támogatása

A Backup a következő táblázatban összefoglalt biztonsági mentési forgalom tömörítését támogatja.

- Az Azure-beli virtuális gépek esetében a virtuálisgép-bővítmény közvetlenül az Azure Storage-fiókból olvassa be az adatokat a tárolási hálózaton keresztül, ezért nem szükséges tömöríteni a forgalmat.
- Ha a DPM-t vagy a MABS-t használja, akkor a biztonsági mentés előtt tömörítheti a sávszélességet.

**Gép** | **Tömörítés a MABS/DPM (TCP)** | **Tömörítés a tárolóba (HTTPS)**
--- | --- | ---
**Helyszíni Windows rendszerű gépek közvetlen biztonsági mentése** | NA | ![Igen][green]
**Azure-beli virtuális gépek biztonsági mentése virtuálisgép-bővítmény használatával** | NA | NA
**Biztonsági mentés helyszíni/Azure-alapú gépeken a MABS/DPM használatával** | ![Igen][green] | ![Yes][green]

## <a name="retention-limits"></a>Megőrzési korlátok

**Beállítás** | **Korlátok**
--- | ---
**Helyreállítási pontok maximális száma védett példányon (gép vagy munkaterhelés)** | 9 999
**Helyreállítási pont maximális lejárati ideje** | Korlátlan
**Maximális biztonsági mentés gyakorisága DPM/MABS** | 15 percenként az SQL Serverhez<br/><br/> Óránként egyszer más számítási feladatokhoz
**Maximális biztonsági mentési gyakoriság a tárolóhoz** | **A Marsot futtató helyszíni Windows-számítógépek vagy Azure-beli virtuális gépek:** Naponta háromszor<br/><br/> **DPM/MABS:** Kettő/nap<br/><br/> **Azure-beli virtuális gép biztonsági mentése:** Naponta egy
**Helyreállítási pont megőrzése** | Napi, heti, havi, éves
**Maximális megőrzési időtartam** | A biztonsági mentés gyakoriságától függően változik
**Helyreállítási pontok a DPM-vagy MABS-lemezen** | 64 fájlkiszolgálók esetén; 448 alkalmazás-kiszolgálókhoz <br/><br/>Korlátlan szalagos helyreállítási pontok a helyszíni DPM

## <a name="cross-region-restore"></a>Régiók közötti visszaállítás

Azure Backup hozzáadta a régiók közötti visszaállítási szolgáltatást az adatok rendelkezésre állásának és a rugalmassági képesség megerősítéséhez, így teljes körűen szabályozhatja az adatok másodlagos régióba való visszaállítását. A szolgáltatás konfigurálásához látogasson el [a régióbeli visszaállítás beállítása című cikkre.](backup-create-rs-vault.md#set-cross-region-restore) Ez a funkció a következő felügyeleti típusok esetén támogatott:

| Biztonságimásolat-kezelés típusa | Támogatott                                                    | Támogatott régiók |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Az Azure-beli virtuális gépek felügyelt és nem felügyelt lemezekkel egyaránt támogatottak. Klasszikus virtuális gépek esetén nem támogatott. | Az összes nyilvános Azure-régióban elérhető, kivéve a Közép-ausztráliai, Közép-Ausztrália, Dél-Afrika északi régiója, Észak-Európa, Észak-Svájc, Középnyugat-Németország, Kelet-Norvégia. <br>Az ezekben a régiókban használattal kapcsolatos információkért forduljon a következőhöz: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| SQL/SAP HANA | Előzetes verzióban                                                      | Az összes nyilvános Azure-régióban elérhető, kivéve a Közép-ausztráliai, Közép-Ausztrália, Dél-Afrika északi régiója, Észak-Európa, Észak-Svájc, Középnyugat-Németország, Kelet-Norvégia. <br>Az ezekben a régiókban használattal kapcsolatos információkért forduljon a következőhöz: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| MARS-ügynök/helyszíni  | No                                                           | N/A               |
| AFS (Azure-fájlmegosztás)                 | No                                                           | N/A               |

## <a name="next-steps"></a>Következő lépések

- [Tekintse át](backup-support-matrix-iaas.md) az Azure virtuális gépek biztonsági mentésének támogatási mátrixát.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
