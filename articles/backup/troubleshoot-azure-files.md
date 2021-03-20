---
title: Azure-fájlmegosztás biztonsági mentésének hibáinak megoldása
description: A cikk olyan hibákkal kapcsolatos hibaelhárítási információkat tartalmaz, amelyek az Azure fájlmegosztások védelmekor következnek be.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4908b8ed97bad43d9d24427660a8691ee43d7eaf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89376978"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Az Azure-fájlmegosztás biztonsági mentése során felmerülő problémák elhárítása

Ez a cikk a Azure Backup szolgáltatással történő biztonsági mentés vagy az Azure-fájlmegosztás visszaállítása során felmerülő problémák megoldásához nyújt hibaelhárítási információkat.

## <a name="common-configuration-issues"></a>Gyakori konfigurációs problémák

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Nem található a Storage-fiók az Azure-fájlmegosztás biztonsági mentésének konfigurálásához

- Várjon, amíg a felderítés befejeződik.
- Ellenőrizze, hogy a Storage-fiókhoz tartozó fájlmegosztás már védett-e egy másik Recovery Services-tárolóval.

  >[!NOTE]
  >A Storage-fiókokban lévő összes fájlmegosztást csak egy Recovery Services tárolóban lehet védeni. [Ezzel a parancsfájllal](scripts/backup-powershell-script-find-recovery-services-vault.md) megkeresheti azt a Recovery Services tárolót, ahol a Storage-fiók regisztrálva van.

- Győződjön meg arról, hogy a fájlmegosztás nem szerepel a nem támogatott Storage-fiókok egyikében sem. A támogatott Storage-fiókok kereséséhez tekintse meg az [Azure-fájlmegosztás biztonsági mentésének támogatási mátrixát](azure-file-share-support-matrix.md) .
- Győződjön meg arról, hogy a Storage-fiók neve és az erőforráscsoport neve nem haladja meg a 84 karaktert az új Storage-fiókok esetében, illetve a 77 karaktert a klasszikus Storage-fiókok esetében.
- Ellenőrizze a Storage-fiók tűzfal-beállításait, és győződjön meg arról, hogy engedélyezve van-e a megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiókhoz lehetőség.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Hiba történt a portál állapotában a Storage-fiókok felderítése során

Ha van partner-előfizetése (CSP-kompatibilis), hagyja figyelmen kívül a hibát. Ha az előfizetése nem támogatja a CSP-t, és a Storage-fiókok nem észlelhetők, forduljon az ügyfélszolgálathoz.

### <a name="selected-storage-account-validation-or-registration-failed"></a>A kiválasztott Storage-fiók ellenőrzése vagy regisztrációja nem sikerült

Próbálja megismételni a regisztrációt. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Nem lehetett listázni vagy megkeresni a fájlmegosztást a kiválasztott Storage-fiókban.

- Győződjön meg arról, hogy a Storage-fiók létezik az erőforráscsoporthoz, és nem lett törölve vagy áthelyezve a tároló utolsó ellenőrzése vagy regisztrációja után.
- Győződjön meg arról, hogy a védetté tenni kívánt fájlmegosztás nem lett törölve.
- Győződjön meg arról, hogy a Storage-fiók egy támogatott Storage-fiók a fájlmegosztás biztonsági mentéséhez. A támogatott Storage-fiókok kereséséhez tekintse meg az [Azure-fájlmegosztás biztonsági mentésének támogatási mátrixát](azure-file-share-support-matrix.md) .
- Ellenőrizze, hogy a fájlmegosztás már védve van-e ugyanabban a Recovery Services-tárolóban.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>A biztonsági mentési fájlmegosztás konfigurációja (vagy a védelmi házirend konfigurációja) sikertelen

- Próbálkozzon újra a konfigurációval, és ellenőrizze, hogy a probléma továbbra is fennáll-e.
- Győződjön meg arról, hogy a védelemmel ellátni kívánt fájlmegosztás nem lett törölve.
- Ha egyszerre több fájlmegosztást szeretne védelemmel ellátni, és néhány fájlmegosztás meghiúsul, próbálja meg újból beállítani a sikertelen fájlmegosztás biztonsági mentését.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Nem sikerült törölni a Recovery Services tárolót a fájlmegosztás feloldása után

A Azure Portal nyissa **meg a**  >  tároló **biztonsági mentési infrastruktúrájának**  >  **Storage-fiókjait**. Válassza a **regisztráció** törlése elemet a Storage-fiókok Recovery Services-tárolóból való eltávolításához.

>[!NOTE]
>Recovery Services-tárolót csak a tárolóban regisztrált összes Storage-fiók regisztrációjának törlése után lehet törölni.

## <a name="common-backup-or-restore-errors"></a>Gyakori biztonsági mentési vagy visszaállítási hibák

>[!NOTE]
>Tekintse át [ezt a dokumentumot](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) , és győződjön meg arról, hogy megfelelő engedélyekkel rendelkezik a biztonsági mentési és visszaállítási műveletek végrehajtásához.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound – a művelet sikertelen volt, mert a fájlmegosztás nem található

Hibakód: FileShareNotFound

Hibaüzenet: a művelet sikertelen volt, mert a fájlmegosztás nem található

Győződjön meg arról, hogy a védelemmel ellátni kívánt fájlmegosztás nem lett törölve.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable – a Storage-fiók nem található vagy nem támogatott

Hibakód: UserErrorFileShareEndpointUnreachable

Hibaüzenet: a Storage-fiók nem található vagy nem támogatott.

- Győződjön meg arról, hogy a Storage-fiók létezik az erőforráscsoporthoz, és az utolsó ellenőrzés után nem törölte vagy nem távolította el az erőforráscsoportot.

- Győződjön meg arról, hogy a Storage-fiók egy támogatott Storage-fiók a fájlmegosztás biztonsági mentéséhez.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached – elérte a fájlmegosztás Pillanatképek maximális korlátját. a régebbiek lejárta után még többet is elvégezhet

Hibakód: AFSMaxSnapshotReached

Hibaüzenet: elérte a fájlmegosztás Pillanatképek maximális számát. a régebbiek lejárta után még többet is elvégezhet.

- Ez a hiba akkor fordulhat elő, ha több igény szerinti biztonsági mentést hoz létre egy fájlmegosztás számára.
- Fájlmegosztás esetén legfeljebb 200 pillanatkép található, beleértve a Azure Backup által készített fájlokat is. A régebbi ütemezett biztonsági mentések (vagy pillanatképek) automatikusan törlődnek. Törölni kell az igény szerinti biztonsági mentést (vagy pillanatképeket), ha elérik a maximális korlátot.

Törölje az igény szerinti biztonsági mentéseket (az Azure fájlmegosztási pillanatképeket) az Azure Files portálról.

>[!NOTE]
> A helyreállítási pontok elvesznek, ha törli a Azure Backup által létrehozott pillanatképeket.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound – a művelet sikertelen volt, mert a megadott Storage-fiók már nem létezik

Hibakód: UserErrorStorageAccountNotFound

Hibaüzenet: a művelet meghiúsult, mert a megadott Storage-fiók már nem létezik.

Győződjön meg arról, hogy a Storage-fiók még létezik, és nincs törölve.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound – a megadott Storage-fiók adatai helytelenek

Hibakód: UserErrorDTSStorageAccountNotFound

Hibaüzenet: a megadott Storage-fiók adatai helytelenek.

Győződjön meg arról, hogy a Storage-fiók még létezik, és nincs törölve.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound – az erőforráscsoport nem létezik

Hibakód: UserErrorResourceGroupNotFound

Hibaüzenet: az erőforráscsoport nem létezik

Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest – A fájlmegosztás már folyamatban van egy biztonsági mentési feladatokhoz

Hibakód: ParallelSnapshotRequest

Hibaüzenet: már folyamatban van egy biztonsági mentési művelet ehhez a fájlmegosztáshoz.

- A fájlmegosztás biztonsági mentése nem támogatja a párhuzamos pillanatkép-kérelmeket ugyanazzal a fájlmegosztás ellen.

- Várjon, amíg a meglévő biztonsági mentési feladatok befejeződik, majd próbálkozzon újra. Ha nem talál biztonsági mentési feladatot a Recovery Services-tárolóban, tekintse meg az azonos előfizetésben található többi Recovery Services-tárolót.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling – a fájlmegosztás biztonsági mentése vagy visszaállítása a tárolási szolgáltatás szabályozása miatt meghiúsult. Ennek az lehet az oka, hogy a tárolási szolgáltatás a megadott Storage-fiókra vonatkozó egyéb kérelmek feldolgozásával van elfoglalva.

Hibakód: FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

Hibaüzenet: a fájlmegosztás biztonsági mentése vagy visszaállítása a tárolási szolgáltatás szabályozása miatt meghiúsult. Ez azért történhet, mert a társzolgáltatás a megadott tárfiók más kérelmeinek feldolgozásával van elfoglalva.

Próbálkozzon újra a biztonsági mentési/visszaállítási művelettel.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound – nem található a célfájl megosztása

Hibakód: TargetFileShareNotFound

Hibaüzenet: a célfájl megosztása nem található.

- Győződjön meg arról, hogy a kiválasztott Storage-fiók létezik, és a célfájl megosztása nincs törölve.

- Győződjön meg arról, hogy a Storage-fiók egy támogatott Storage-fiók a fájlmegosztás biztonsági mentéséhez.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked – a feladatok biztonsági mentése vagy visszaállítása nem sikerült, mert a Storage-fiók zárolt állapotban van

Hibakód: UserErrorStorageAccountIsLocked

Hibaüzenet: a biztonsági mentési vagy visszaállítási feladatok sikertelenek voltak, mert a Storage-fiók zárolt állapotban van.

Távolítsa el a Storage-fiók zárolását, vagy használjon **törlési zárolást** **olvasási zárolás** helyett, majd próbálkozzon újra a biztonsági mentési vagy visszaállítási művelettel.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached – a helyreállítás sikertelen volt, mert a hibás fájlok száma meghaladja a küszöbértéket

Hibakód: DataTransferServiceCoFLimitReached

Hibaüzenet: a helyreállítás sikertelen volt, mert a hibás fájlok száma meghaladja a küszöbértéket.

- A helyreállítási hibák okai a fájlban (a feladatok részletei között megadott elérési úton) találhatók. Oldja meg a hibákat, és ismételje meg a visszaállítási műveletet csak a hibás fájlok esetében.

- A fájl-visszaállítási hibák gyakori okai:

  - a meghibásodott fájlok jelenleg használatban vannak
  - a szülőmappa a hibás fájllal megegyező nevű könyvtárat tartalmaz.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover – a helyreállítás sikertelen volt, mert egyetlen fájlt sem sikerült helyreállítani.

Hibakód: DataTransferServiceAllFilesFailedToRecover

Hibaüzenet: a helyreállítás sikertelen volt, mert egyetlen fájlt sem sikerült helyreállítani.

- A helyreállítási hibák okai a fájlban (a feladatok részletei között megadott elérési úton) találhatók. Oldja meg a hibákat, majd ismételje meg a visszaállítási műveleteket csak a hibás fájlokra vonatkozóan.

- A fájl-visszaállítási hibák gyakori okai:

  - a meghibásodott fájlok jelenleg használatban vannak
  - a szülőmappa a hibás fájllal megegyező nevű könyvtárat tartalmaz.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid – a visszaállítás sikertelen, mert a forrás egyik fájlja nem létezik

Hibakód: DataTransferServiceSourceUriNotValid

Hibaüzenet: a visszaállítás sikertelen, mert a forrás egyik fájlja nem létezik.

- A kijelölt elemek nem szerepelnek a helyreállítási pont adataiban. A fájlok helyreállításához adja meg a helyes fájllistát.
- A helyreállítási ponthoz tartozó megosztás fájlpillanatképet manuálisan kell törölni. Válasszon egy másik helyreállítási pontot, és ismételje meg a visszaállítási műveletet.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked – a helyreállítási feladatok ugyanarra a célra vannak folyamatban

Hibakód: UserErrorDTSDestLocked

Hibaüzenet: a helyreállítási feladatok folyamatban vannak ugyanarra a célra.

- A fájlmegosztás biztonsági mentése nem támogatja a párhuzamos helyreállítást ugyanahhoz a célfájl-megosztáshoz.

- Várja meg a meglévő helyreállítási feladat befejeződését, majd próbálja meg újból. Ha nem talál helyreállítási feladatot a Recovery Services-tárolóban, tekintse meg az azonos előfizetésben lévő többi Recovery Services-tárolót.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull – a visszaállítási művelet sikertelen volt, mert a célfájl megosztása megtelt

Hibakód: UserErrorTargetFileShareFull

Hibaüzenet: a visszaállítási művelet sikertelen volt, mert a célfájl megosztása megtelt.

Növelje a célfájl megosztásának mérete kvótáját a visszaállítási adatmennyiséghez, majd próbálja megismételni a visszaállítási műveletet.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient – a cél fájlmegosztás nem rendelkezik elegendő tárolási mérettel a visszaállításhoz

Hibakód: UserErrorTargetFileShareQuotaNotSufficient

Hibaüzenet: a cél fájlmegosztás nem rendelkezik elegendő tárolási mérettel a visszaállításhoz

Növelje a célfájl megosztásának mérete kvótáját a visszaállítási adatmennyiséghez, majd próbálja megismételni a műveletet.

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed-visszaállítási művelet sikertelen volt, mert hiba történt a célfájl-megosztáshoz társított File Sync szolgáltatási erőforrások előzetes visszaállítási műveleteinek végrehajtása során.

Hibakód: File Sync PreRestoreFailed

Hibaüzenet: a visszaállítási művelet sikertelen volt, mert hiba történt a célfájl-megosztáshoz társított File Sync szolgáltatási erőforrások előzetes visszaállítási műveleteinek végrehajtása során.

Próbálja megismételni az adathelyreállítást egy későbbi időpontban. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress – Azure File Sync szolgáltatás-változás észlelése folyamatban van a célfájl megosztásához. A változás észlelését a rendszer egy korábbi visszaállítással aktiválta a célfájl megosztására.

Hibakód: AzureFileSyncChangeDetectionInProgress

Hibaüzenet: Azure File Sync szolgáltatás-változás észlelése folyamatban van a célfájl megosztásához. A változás észlelését a rendszer egy korábbi visszaállítással aktiválta a célfájl megosztására.

Használjon másik célfájl-megosztást. Azt is megvárhatja, hogy Azure File Sync szolgáltatás változásának észlelése befejeződjön a célfájl-megosztáson, mielőtt újrapróbálkozik a visszaállítással.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored – egy vagy több fájlt nem sikerült helyreállítani. További információkért lásd a hibás fájlok listáját a fent megadott elérési úton

Hibakód: UserErrorAFSRecoverySomeFilesNotRestored

Hibaüzenet: egy vagy több fájlt nem sikerült helyreállítani. További információkért ellenőrizze a hibás fájlok listáját a fent megadott elérési úton.

- A helyreállítási hibák okai megjelennek a fájlban (a feladatok részletei között megadott elérési út). Oldja meg az okokat, majd próbálja megismételni a visszaállítási műveletet csak a hibás fájlok esetében.
- A fájl-visszaállítási hibák gyakori okai:

  - a meghibásodott fájlok jelenleg használatban vannak
  - a szülőmappa a hibás fájllal megegyező nevű könyvtárat tartalmaz.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound – a helyreállítási pontnak megfelelő Azure fájlmegosztás pillanatképe nem található

Hibakód: UserErrorAFSSourceSnapshotNotFound

Hibaüzenet: a helyreállítási pontnak megfelelő Azure file share pillanatkép nem található

- Győződjön meg arról, hogy a fájlmegosztás pillanatképe, amely a helyreállításhoz használni kívánt helyreállítási pontnak felel meg, továbbra is létezik.

  >[!NOTE]
  >Ha Azure Backup által létrehozott fájlmegosztás-pillanatképet töröl, a megfelelő helyreállítási pontok használhatatlanná válnak. A garantált helyreállítás biztosítása érdekében javasoljuk, hogy ne törölje a pillanatképeket.

- Válasszon másik visszaállítási pontot az adatok helyreállításához.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget – egy másik visszaállítási feladatokra ugyanazon a célfájl-megosztáson van folyamatban

Hibakód: UserErrorAnotherRestoreInProgressOnSameTarget

Hibaüzenet: egy másik visszaállítási művelet van folyamatban ugyanazon a célfájl-megosztáson

Használjon másik célfájl-megosztást. Másik lehetőségként megszakíthatja a műveletet, vagy megvárhatja, amíg a másik helyreállítási művelet befejeződik.

## <a name="common-modify-policy-errors"></a>Gyakori szabályzat-módosítási hibák

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation – az adott elemmel kapcsolatban folyamatban van egy másik védelmi művelet konfigurálása

Hibakód: BMSUserErrorConflictingProtectionOperation

Hibaüzenet: folyamatban van egy másik konfigurálási védelmi művelet ehhez az objektumhoz.

Várjon, amíg befejeződik az előző házirend-módosítási művelet, majd próbálkozzon újra később.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked – folyamatban van egy másik művelet végrehajtása a kijelölt elemen

Hibakód: BMSUserErrorObjectLocked

Hibaüzenet: egy másik művelet van folyamatban a kijelölt elemen.

Várjon, amíg a másik folyamatban lévő művelet befejeződik, és próbálkozzon újra később.

## <a name="common-soft-delete-related-errors"></a>Gyakori törléssel kapcsolatos gyakori hibák

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState – ez a visszaállítási pont nem érhető el, mert az ehhez a ponthoz társított pillanatkép olyan fájlmegosztás, amely helyreállított állapotban van

Hibakód: UserErrorRestoreAFSInSoftDeleteState

Hibaüzenet: Ez a visszaállítási pont nem érhető el, mert az ehhez a ponthoz társított pillanatkép olyan fájlmegosztás, amely helyreállított állapotban van.

A visszaállítási művelet nem hajtható végre, ha a fájlmegosztás töröletlen állapotban van. Törölje a fájlmegosztást a Files portálról, vagy használja a [törlési parancsfájlt](scripts/backup-powershell-script-undelete-file-share.md) , majd próbálja meg visszaállítani.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState – a felsorolt visszaállítási pontok nem érhetők el, mert a visszaállítási pontok pillanatképeit tartalmazó társított fájlmegosztás véglegesen törölve lett

Hibakód: UserErrorRestoreAFSInDeleteState

Hibaüzenet: a felsorolt visszaállítási pontok nem érhetők el, mert a visszaállítási pontok pillanatképeit tartalmazó társított fájlmegosztás véglegesen törölve lett.

Ellenőrizze, hogy a mentett fájlmegosztás törölve van-e. Ha a fájl nem lett törölve, akkor ellenőrizze, hogy a helyreállított törlés megőrzési ideje túl van-e, és hogy a helyreállítás nem történt-e vissza. Ezekben az esetekben az összes pillanatképet véglegesen elveszítjük, és nem fogják tudni helyreállítani az adatokat.

>[!NOTE]
> Azt javasoljuk, hogy ne törölje a biztonsági másolatba mentett fájlmegosztást, vagy ha a törlésre kész állapotban van, törölje a törlést, mielőtt lejár az összes visszaállítási pont.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState – a biztonsági mentés nem sikerült, mert az Azure-fájlmegosztás Soft-Deleted állapotban van

Hibakód: UserErrorBackupAFSInSoftDeleteState

Hibaüzenet: a biztonsági mentés nem sikerült, mert az Azure-fájlmegosztás Soft-Deleted állapotban van

Törölje a fájlmegosztást a **Files portálról** , vagy a [Törlés törlése](scripts/backup-powershell-script-undelete-file-share.md) paranccsal folytassa a biztonsági mentést, és akadályozza meg az adatok végleges törlését.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState – a biztonsági mentés nem sikerült, mert a társított Azure-fájlmegosztás véglegesen törölve lett

Hibakód: UserErrorBackupAFSInDeleteState

Hibaüzenet: a biztonsági mentés nem sikerült, mert a társított Azure-fájlmegosztás véglegesen törölve lett

Ellenőrizze, hogy a mentett fájlmegosztás véglegesen törölve lett-e. Ha igen, állítsa le a fájlmegosztás biztonsági mentését, hogy elkerülje az ismételt biztonsági mentési hibákat. A védelem leállításával kapcsolatos további információkért lásd: [Az Azure-fájlmegosztás védelmének leállítása](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Következő lépések

Az Azure-fájlmegosztás biztonsági mentéséről további információt a következő témakörben talál:

- [Azure-fájlmegosztások biztonsági mentése](backup-afs.md)
- [Azure-fájlmegosztás biztonsági mentése – GYIK](backup-azure-files-faq.md)
