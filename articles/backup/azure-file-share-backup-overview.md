---
title: Az Azure-fájlmegosztások biztonsági mentése
description: Megtudhatja, hogyan lehet biztonsági mentése az Azure-fájlmegosztásokról a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c4f9dd816ace2c9aec8f48207fbce88acf34e24a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516815"
---
# <a name="about-azure-file-share-backup"></a>Az Azure-fájlmegosztások biztonsági mentése

Az Azure-fájlmegosztások biztonsági mentése egy natív, felhőalapú biztonsági mentési megoldás, amely megvédi adatait a felhőben, és kiküszöböli a helyszíni biztonsági mentési megoldások további karbantartási többletterhelését. Az Azure Backup szolgáltatás zökkenőmentesen integrálható a Azure File Sync, és lehetővé teszi a fájlmegosztási adatok és a biztonsági másolatok központosítását. Ez az egyszerű, megbízható és biztonságos megoldás lehetővé teszi, hogy néhány egyszerű lépésben konfigurálja a vállalati fájlmegosztások védelmét annak biztos tudatában, hogy bármilyen katasztrófa esetén helyreállíthatja az adatokat.

## <a name="key-benefits-of-azure-file-share-backup"></a>Az Azure-fájlmegosztások biztonsági mentésének fő előnyei

* **Infrastruktúra nélkül:** Nincs szükség központi telepítésre a fájlmegosztások védelmének konfigurálása érdekében.
* **Testreszabott megőrzés:** A biztonsági másolatokat napi/heti/havi/éves megőrzéssel konfigurálhatja az igényeinek megfelelően.
* **Beépített felügyeleti képességek:** Ütemezheti a biztonsági mentéseket, és megadhatja a kívánt megőrzési megőrzési időszakot anélkül, hogy többletterhelést okoz az adatok adatcsonkolása.
* **Azonnali visszaállítás:** Az Azure-fájlmegosztások biztonsági mentése fájlmegosztási pillanatképeket használ, így csak azokat a fájlokat választhatja ki, amelyeken azonnal vissza szeretne állítani.
* **Riasztások és jelentéskészítés:** Riasztásokat konfigurálhat a biztonsági mentési és visszaállítási hibákhoz, és a Azure Backup által biztosított jelentéskészítési megoldással betekintést nyerhet a fájlmegosztások biztonsági másolataiba.
* **Védelem a fájlmegosztások** véletlen törlése ellen: [](../storage/files/storage-files-prevent-file-share-deletion.md) a Azure Backup engedélyezi a törlés funkciót a tárfiók szintjén, 14 napos megőrzési időtartammal. Még ha egy rosszindulatú szereplő is törli a fájlmegosztást, a fájlmegosztás tartalmát és helyreállítási pontjait (pillanatképeket) a rendszer egy konfigurálható megőrzési időszakra megőrzi, így lehetővé teszi a forrás tartalmának és pillanatképének sikeres és teljes helyreállítását adatvesztés nélkül.

## <a name="architecture"></a>Architektúra

![Azure-fájlmegosztások biztonsági mentési architektúrája](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>A biztonsági mentési folyamat működése

1. Az Azure-fájlmegosztások biztonsági mentésének konfigurálás első lépése egy Recovery Services-tároló létrehozása. A tároló a különböző számítási feladatokhoz konfigurált biztonsági másolatok összevont nézetét biztosítja.

2. Miután létrehozott egy tárolót, a Azure Backup szolgáltatás felderíti a tárolóval regisztrálható tárfiókokat. Kiválaszthatja a védeni kívánt fájlmegosztásokat üzemeltető tárfiókot.

3. Miután kiválasztotta a tárfiókot, a Azure Backup szolgáltatás felsorolja a tárfiókban található fájlmegosztásokat, és a nevüket a felügyeleti réteg katalógusában tárolja.

4. Ezután konfigurálja a biztonsági mentési házirendet (ütemezés és megőrzés) az igényeinek megfelelően, majd válassza ki a fájlmegosztásokat, amelyekről biztonsági másolatot készít. A Azure Backup szolgáltatás regisztrálja az ütemezéseket a vezérlősíkon az ütemezett biztonsági mentések készítéséhez.

5. A megadott szabályzat alapján a Azure Backup ütemező az ütemezett időpontban indítja el a biztonsági mentéseket. A feladat részeként a fájlmegosztás pillanatképe a Fájlmegosztás API használatával jön létre. A metaadattár csak a pillanatkép URL-címét tárolja.

    >[!NOTE]
    >A fájlmegosztási adatok nem kerülnek át a Backup szolgáltatásba, mivel a Backup szolgáltatás a tárfiók részét képezi pillanatképeket hoz létre és kezel, és a biztonsági másolatok nem kerülnek át a tárolóba.

6. Az Azure-fájlmegosztás tartalmát (egyes fájlok vagy a teljes megosztás) visszaállíthatja a forrásfájlmegosztáson elérhető pillanatképek alapján. A művelet aktiválása után a rendszer beolvassa a pillanatkép URL-címét a metaadattárból, az adatok pedig fel vannak sorolva, és átkerülnek a forrás-pillanatképből a kívánt célfájlmegosztásba.

7. Ha az Azure File Sync-t használja, a Backup szolgáltatás jelzi a Azure File Sync szolgáltatásnak a visszaállított fájlok elérési útvonalát, amely elindítja a háttérben futó módosításészlelési folyamatot ezeken a fájlokon. A módosított fájlokat a rendszer a kiszolgálóvégpontra szinkronizálja. Ez a folyamat az Azure-fájlmegosztás eredeti visszaállításával párhuzamosan történik.

8. A biztonsági mentési és visszaállítási feladat monitorozási adatait a rendszer a Azure Backup szolgáltatásba. Ez lehetővé teszi, hogy egyetlen irányítópulton figyelje a fájlmegosztások felhőbeli biztonsági mentését. Emellett riasztásokat és e-mail-értesítéseket is konfigurálhat, ha ez hatással van a biztonsági mentés állapotára. Az e-maileket az Azure e-mail szolgáltatásán keresztül küldi el a rendszer.

## <a name="backup-costs"></a>Biztonsági mentési költségek

Az Azure-fájlmegosztások biztonsági mentési megoldásának két költsége van:

1. **Pillanatkép-tárolás költsége:** A pillanatképek tárolási díjai a Azure Files használati díjával együtt vannak kiszámlázva az itt említett díjszabási [adatoknak megfelelően](https://azure.microsoft.com/pricing/details/storage/files/)

2. **Védett példány díja:** 2020. szeptember 1-től a védett példányért az itt említett díjszabásnak megfelelően díjat számítunk [fel.](https://azure.microsoft.com/pricing/details/backup/) A védett példányok díja a tárfiókban található védett fájlmegosztások teljes méretétől függ.

Az Azure-fájlmegosztások biztonsági mentése részletes becsléséhez letöltheti a részletes Azure Backup [becslére.](https://aka.ms/AzureBackupCostEstimates)  

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [Azure-fájlmegosztások biztonsági mentésekor](backup-afs.md)
* Választ találhat a [biztonsági kérdésekre](backup-azure-files-faq.yml) a biztonsági Azure Files
