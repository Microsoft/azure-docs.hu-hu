---
title: Azure-fájlmegosztások biztonsági mentésének hibaelhárítása
description: A cikk olyan hibákkal kapcsolatos hibaelhárítási információkat tartalmaz, amelyek az Azure fájlmegosztások védelmekor következnek be.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4c934d2295fa702425e8df0a03636b9f9208cfa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515073"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Az Azure-fájlmegosztások biztonsági mentése közbeni hibák elhárítása

Ez a cikk hibaelhárítási információkat tartalmaz az Azure-fájlmegosztások biztonsági mentésének konfigurálása vagy az Azure-fájlmegosztások az Azure Backup szolgáltatás használatával való visszaállítása során Azure Backup megoldásához.

## <a name="common-configuration-issues"></a>Gyakori konfigurációs problémák

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Nem találom a tárfiókomat az Azure-fájlmegosztás biztonsági mentésének konfiguráléhez

- Várjon, amíg a felderítés befejeződik.
- Ellenőrizze, hogy a tárfiókban található fájlmegosztások már védve vannak-e egy másik Recovery Services-tárolóval.

  >[!NOTE]
  >A Storage-fiókban található összes fájlmegosztás védelme csak egy Recovery Services-tárolóban lehet. Ezzel a szkript [használatával megkeresheti](scripts/backup-powershell-script-find-recovery-services-vault.md) a helyreállítási tárat, amelyben a tárfiók regisztrálva van.

- Győződjön meg arról, hogy a fájlmegosztás nincs jelen egyik nem támogatott tárfiókban sem. A támogatott tárfiókokért tekintse meg az Azure-fájlmegosztások biztonsági mentésének támogatási mátrixát. [](azure-file-share-support-matrix.md)
- Győződjön meg arról, hogy a tárfiók nevének és az erőforráscsoport nevének együttes hossza nem haladja meg a 84 karaktert az új Storage-fiókok esetében, illetve a 77 karaktert a klasszikus tárfiókok esetében.
- Ellenőrizze a tárfiók tűzfalbeállítását, és győződjön meg arról, hogy a megbízható Microsoft-szolgáltatások hozzáférhetnek a tárfiókhoz beállítás engedélyezve van.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>A portál hibája azt jelenti, hogy a tárfiókok felderítése sikertelen volt

Ha partner-előfizetéssel rendelkezik (CSP-kompatibilis), hagyja figyelmen kívül a hibát. Ha az előfizetése nincs engedélyezve a CSP-hez, és a tárfiókok nem fedeződnek fel, forduljon az ügyfélszolgálathoz.

### <a name="selected-storage-account-validation-or-registration-failed"></a>A kiválasztott tárfiók érvényesítése vagy regisztrációja sikertelen volt

Próbálja meg újra a regisztrációt. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>A kiválasztott tárfiókban nem található a fájlmegosztások list mentése vagy megkerese

- Győződjön meg arról, hogy a tárfiók létezik az erőforráscsoportban, és nem lett törölve vagy áthelyezve a tárolóban történt legutóbbi ellenőrzés vagy regisztráció után.
- Győződjön meg arról, hogy a védeni kívánt fájlmegosztás nem lett törölve.
- Győződjön meg arról, hogy a tárfiók támogatott tárfiók a fájlmegosztások biztonsági mentéséhez. A támogatott tárfiókokért tekintse meg az Azure-fájlmegosztások biztonsági mentésének támogatási mátrixát. [](azure-file-share-support-matrix.md)
- Ellenőrizze, hogy a fájlmegosztás már védve van-e ugyanabban a Recovery Services-tárolóban.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>A biztonsági mentési fájlmegosztás konfigurációja (vagy a védelmi szabályzat konfigurációja) sikertelen

- Próbálja meg újra a konfigurációt, és nézze meg, hogy a probléma továbbra is fennáll-e.
- Győződjön meg arról, hogy a védeni kívánt fájlmegosztás nem lett törölve.
- Ha egyszerre több fájlmegosztás védelmét próbálja meg védeni, és egyes fájlmegosztások meghibásodnak, próbálja meg ismét konfigurálni a sikertelen fájlmegosztások biztonsági mentését.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Nem lehet törölni a Recovery Services-tárolót a fájlmegosztás vélmének feloldása után

A Azure Portal nyissa meg a **Vault**  >  **Backup Infrastructure Storage-fiókokat.**  >   Válassza **a Regisztráció törlése lehetőséget** a tárfiókok helyreállítási tárból való eltávolításához.

>[!NOTE]
>A Recovery Services-tárolók csak a tárolóban regisztrált összes tárfiók regisztrációjának törlése után törölhetők.

## <a name="common-backup-or-restore-errors"></a>Gyakori biztonsági mentési vagy visszaállítási hibák

>[!NOTE]
>Tekintse meg [ezt a dokumentumot,](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) és ellenőrizze, hogy rendelkezik-e a megfelelő engedélyekkel a biztonsági mentési vagy visszaállítási műveletek végrehajtásához.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound – A művelet nem sikerült, mert a fájlmegosztás nem található

Hibakód: FileShareNotFound

Hibaüzenet: A művelet nem sikerült, mert a fájlmegosztás nem található

Győződjön meg arról, hogy a védeni kívánt fájlmegosztás nem lett törölve.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable – A tárfiók nem található vagy nem támogatott

Hibakód: UserErrorFileShareEndpointUnreachable

Hibaüzenet: A tárfiók nem található vagy nem támogatott

- Győződjön meg arról, hogy a tárfiók létezik az erőforráscsoportban, és nem lett törölve vagy eltávolítva az erőforráscsoportból az utolsó ellenőrzés után.

- Győződjön meg arról, hogy a Storage-fiók támogatott tárfiók a fájlmegosztások biztonsági mentéséhez.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached – Elérte a fájlmegosztás pillanatképei maximális korlátját; A régebbiek lejárta után több is bevetható lesz

Hibakód: AFSMaxSnapshotReached

Hibaüzenet: Elérte a fájlmegosztás pillanatképei maximális korlátját; A régebbiek lejárta után több is bevetható lesz.

- Ez a hiba akkor fordulhat elő, ha több igény szerinti biztonsági másolatot hoz létre egy fájlmegosztáshoz.
- Fájlmegosztásonként legfeljebb 200 pillanatkép készíthető, beleértve a Azure Backup. A régebbi ütemezett biztonsági mentések (vagy pillanatképek) automatikusan törlődnek. Törölni kell az igény szerinti biztonsági mentést (vagy pillanatképeket), ha elérik a maximális korlátot.

Törölje az igény szerinti biztonsági mentéseket (az Azure fájlmegosztási pillanatképeket) az Azure Files portálról.

>[!NOTE]
> A helyreállítási pontok elvesznek, ha törli a pillanatképeket, amelyek a Azure Backup.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound – A művelet meghiúsult, mert a megadott tárfiók már nem létezik

Hibakód: UserErrorStorageAccountNotFound

Hibaüzenet: A művelet sikertelen volt, mert a megadott tárfiók már nem létezik.

Győződjön meg arról, hogy a tárfiók még létezik, és nincs törölve.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound – A tárfiók megadott adatai helytelenek

Hibakód: UserErrorDTSStorageAccountNotFound

Hibaüzenet: A tárfiók megadott adatai helytelenek.

Győződjön meg arról, hogy a tárfiók még létezik, és nincs törölve.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound – Az erőforráscsoport nem létezik

Hibakód: UserErrorResourceGroupNotFound

Hibaüzenet: Az erőforráscsoport nem létezik

Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest – A fájlmegosztáshoz már folyamatban van egy biztonsági mentési feladat

Hibakód: ParallelSnapshotRequest

Hibaüzenet: A fájlmegosztáshoz már folyamatban van egy biztonsági mentési feladat.

- A fájlmegosztások biztonsági mentése nem támogatja az ugyanazon a fájlmegosztáson való párhuzamos pillanatkép-kérelmeket.

- Várjon, amíg a meglévő biztonsági mentési feladat befejeződik, majd próbálkozzon újra. Ha nem talál biztonsági mentési feladatot a Helyreállítási tárban, ellenőrizze az ugyanabban az előfizetésben található többi Recovery Services-tárolót.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling – A fájlmegosztás biztonsági mentése vagy visszaállítása a tárolási szolgáltatás szabályozása miatt meghiúsult. Ennek az lehet az oka, hogy a tárolási szolgáltatás az adott tárfiókra vonatkozó egyéb kérések feldolgozásával van elfoglalva

Hibakód: FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling

Hibaüzenet: A fájlmegosztás biztonsági mentése vagy visszaállítása a tárolási szolgáltatás szabályozása miatt meghiúsult. Ez azért történhet, mert a társzolgáltatás a megadott tárfiók más kérelmeinek feldolgozásával van elfoglalva.

Próbálkozzon később a biztonsági mentési/visszaállítási művelettel.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound – A célfájlmegosztás nem található

Hibakód: TargetFileShareNotFound

Hibaüzenet: A célfájlmegosztás nem található.

- Győződjön meg arról, hogy a kiválasztott tárfiók létezik, és a célfájlmegosztás nem törlődik.

- Győződjön meg arról, hogy a Tárfiók támogatott tárfiók a fájlmegosztások biztonsági mentéséhez.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked – A biztonsági mentési vagy visszaállítási feladatok meghiúsultak, mert a tárfiók zárolt állapotban van

Hibakód: UserErrorStorageAccountIsLocked

Hibaüzenet: A biztonsági mentési vagy visszaállítási feladatok meghiúsultak, mert a tárfiók zárolt állapotban van.

Távolítsa el a zárolást a Tárfiókon, vagy használjon törlési zárolást **az** olvasási **zárolás** helyett, és próbálja meg újra a biztonsági mentési vagy visszaállítási műveletet.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached – A helyreállítás sikertelen volt, mert a hibás fájlok száma nagyobb, mint a küszöbérték

Hibakód: DataTransferServiceCoFLimitReached

Hibaüzenet: A helyreállítás sikertelen volt, mert a hibás fájlok száma nagyobb a küszöbértéknél.

- A helyreállítási hibák okai egy fájlban vannak felsorolva (az elérési út a feladat részleteiben található). A hibák kezelése, majd csak a sikertelen fájlok visszaállítási műveletének újrapróbálása.

- A fájl-visszaállítási hibák gyakori okai:

  - A sikertelen fájlok jelenleg használatban vannak
  - a hibás fájllal azonos nevű könyvtár található a szülőkönyvtárban.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover – A helyreállítás sikertelen volt, mivel egyetlen fájl sem állítható helyre

Hibakód: DataTransferServiceAllFilesFailedToRecover

Hibaüzenet: A helyreállítás sikertelen volt, mivel nem sikerült helyreállítani egy fájlt sem.

- A helyreállítási hibák okai egy fájlban vannak felsorolva (az elérési út a feladat részleteiben található). Oldja meg a hibákat, majd ismételje meg a visszaállítási műveleteket csak a hibás fájlokra vonatkozóan.

- A fájl-visszaállítási hibák gyakori okai:

  - A sikertelen fájlok jelenleg használatban vannak
  - a hibás fájllal azonos nevű könyvtár található a szülőkönyvtárban.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid – A visszaállítás sikertelen, mert a forrásban lévő egyik fájl nem létezik

Hibakód: DataTransferServiceSourceUriNotValid

Hibaüzenet: A visszaállítás sikertelen, mert a forrásban lévő egyik fájl nem létezik.

- A kijelölt elemek nem szerepelnek a helyreállítási pont adataiban. A fájlok helyreállításához adja meg a helyes fájllistát.
- A helyreállítási ponthoz tartozó megosztás fájlpillanatképet manuálisan kell törölni. Válasszon egy másik helyreállítási pontot, és ismételje meg a visszaállítási műveletet.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked – Egy helyreállítási feladat van folyamatban ugyanazon a célon

Hibakód: UserErrorDTSDestLocked

Hibaüzenet: A helyreállítási feladat ugyanazon a célon van folyamatban.

- A fájlmegosztás biztonsági mentése nem támogatja párhuzamos helyreállítást ugyanazon a célfájlmegosztáson.

- Várja meg a meglévő helyreállítási feladat befejeződését, majd próbálja meg újból. Ha nem talál helyreállítási feladatot a Helyreállítási tárban, ellenőrizze az ugyanabban az előfizetésben található többi Recovery Services-tárolót.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull – A visszaállítási művelet meghiúsult, mert a célfájlmegosztás megtelt

Hibakód: UserErrorTargetFileShareFull

Hibaüzenet: A visszaállítási művelet nem sikerült, mert a célfájlmegosztás megtelt.

Növelje a célfájlmegosztás méretkvótáját a visszaállítási adatok befogadásához, majd próbálja meg újra a visszaállítási műveletet.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient – A célfájlmegosztás nem rendelkezik elegendő tárolóméret-kvótával a visszaállításhoz

Hibakód: UserErrorTargetFileShareQuotaNotSufficient

Hibaüzenet: A célfájlmegosztás nem rendelkezik elegendő tárolóméret-kvótával a visszaállításhoz

Növelje a célfájlmegosztás méretkvótáját a visszaállítási adatok befogadásához, majd próbálja újra a műveletet

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed – Visszaállítási művelet meghiúsult, mert hiba történt a célfájlmegosztáshoz társított File Sync-szolgáltatáserőforrások visszaállítás előtti műveleteinek végrehajtása során

Hibakód: File Sync PreRestoreFailed

Hibaüzenet: A visszaállítási művelet meghiúsult, mert hiba történt a célfájlmegosztáshoz társított File Sync szolgáltatás-erőforrások visszaállítás előtti műveleteinek végrehajtása közben.

Próbálja meg később visszaállítani az adatokat. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress – Azure File Sync szolgáltatás változásészlelése folyamatban van a célfájlmegosztáshoz. A módosításészlelést a célfájlmegosztás egy korábbi visszaállítása aktiválta

Hibakód: AzureFileSyncChangeDetectionInProgress

Hibaüzenet: Azure File Sync szolgáltatás változásészlelése folyamatban van a célfájlmegosztáshoz. A módosításészlelést a célfájlmegosztás egy korábbi visszaállítása aktiválta.

Használjon másik célfájlmegosztást. Másik lehetőségként megvárhatja, Azure File Sync szolgáltatás változásészlelése befejeződik a célfájlmegosztáson, mielőtt újra megpróbálkozik a visszaállítással.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored – Egy vagy több fájl helyreállítása nem sikerült. További információért tekintse meg a sikertelen fájlok listáját a fent megadott elérési úton

Hibakód: UserErrorAFSRecoverySomeFilesNotRestored

Hibaüzenet: Egy vagy több fájlt nem sikerült helyreállítani. További információkért ellenőrizze a hibás fájlok listáját a fent megadott elérési úton.

- A helyreállítási hibák okai a fájlban vannak felsorolva (az elérési út a Feladat részletei között található). Az okok kezelése, majd csak a sikertelen fájlok visszaállítási műveletének újrapróbálása.
- A fájl-visszaállítási hibák gyakori okai:

  - A sikertelen fájlok jelenleg használatban vannak
  - a hibás fájllal azonos nevű könyvtár létezik a szülőkönyvtárban.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound – Nem található a helyreállítási pontnak megfelelő Azure-fájlmegosztási pillanatkép

Hibakód: UserErrorAFSSourceSnapshotNotFound

Hibaüzenet: Nem található a helyreállítási pontnak megfelelő Azure-fájlmegosztás pillanatképe

- Győződjön meg arról, hogy a helyreállításhoz használni kívánt helyreállítási pontnak megfelelő fájlmegosztási pillanatkép továbbra is létezik.

  >[!NOTE]
  >Ha töröl egy fájlmegosztási pillanatképet, amelyet a Azure Backup, a megfelelő helyreállítási pontok használhatatlanná válnak. A garantált helyreállítás érdekében nem javasoljuk a pillanatképek törlését.

- Próbáljon meg másik visszaállítási pontot választani az adatok helyreállításához.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget – Egy másik visszaállítási feladat van folyamatban ugyanazon a célfájlmegosztáson

Hibakód: UserErrorAnotherRestoreInProgressOnSameTarget

Hibaüzenet: Egy másik visszaállítási feladat van folyamatban ugyanazon a célfájlmegosztáson

Használjon másik célfájlmegosztást. Másik lehetőségként megszakíthatja a műveletet, vagy megvárhatja, amíg a másik helyreállítási művelet befejeződik.

## <a name="common-modify-policy-errors"></a>Gyakori szabályzatmód-módosítási hibák

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation – Egy másik védelmi művelet van folyamatban ehhez az elemhez

Hibakód: BMSUserErrorConflictingProtectionOperation

Hibaüzenet: Egy másik védelmi művelet van folyamatban ehhez az elemhez.

Várjon, amíg az előző házirend-módosítási művelet befejeződik, majd később újrapróbálkozás.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked – Egy másik művelet van folyamatban a kiválasztott elemen

Hibakód: BMSUserErrorObjectLocked

Hibaüzenet: Egy másik művelet van folyamatban a kiválasztott elemen.

Várjon, amíg a másik folyamatban lévő művelet befejeződik, majd később újrapróbálkozás.

## <a name="common-soft-delete-related-errors"></a>Gyakori, a soft delete rel kapcsolatos hibák

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState – Ez a visszaállítási pont nem érhető el, mivel a ponthoz társított pillanatkép helyreállíthatóan törölt állapotban van egy fájlmegosztásban

Hibakód: UserErrorRestoreAFSInSoftDeleteState

Hibaüzenet: Ez a visszaállítási pont nem érhető el, mivel a ponthoz társított pillanatkép helyreállíthatóan törölt állapotban van.

Nem hajthat végre visszaállítási műveletet, ha a fájlmegosztás helyreállíthatóan törölt állapotban van. Állítsa vissza a fájlmegosztást a Files portálról vagy a [Undelete](scripts/backup-powershell-script-undelete-file-share.md) szkript használatával, majd próbálkozzon a visszaállítással.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState – A felsorolt visszaállítási pontok nem érhetők el, mivel a visszaállítási pont pillanatképét tartalmazó társított fájlmegosztás véglegesen törölve lett

Hibakód: UserErrorRestoreAFSInDeleteState

Hibaüzenet: A felsorolt visszaállítási pontok nem érhetők el, mivel a visszaállítási pont pillanatképét tartalmazó társított fájlmegosztás véglegesen törölve lett.

Ellenőrizze, hogy a biztonsági mentéses fájlmegosztás törölve lett-e. Ha helyreállíthatóan törölt állapotban volt, ellenőrizze, hogy a helyreállítható törlés megőrzési ideje véget ért-e, és nem lett-e helyreállítva. Mindkét esetben véglegesen elveszíti az összes pillanatképet, és nem tudja helyreállítani az adatokat.

>[!NOTE]
> Azt javasoljuk, hogy ne törölje a biztonsági másolatba került fájlmegosztást, vagy ha helyreállíthatóan törölt állapotban van, törölje a törlést a helyreállítható törlés megőrzési időszakának vége előtt, hogy elkerülje az összes visszaállítási pont elvesztését.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState – A biztonsági mentés meghiúsult, mert az Azure-fájlmegosztás visszaállított állapotban van

Hibakód: UserErrorBackupAFSInSoftDeleteState

Hibaüzenet: A biztonsági mentés meghiúsult, mert az Azure-fájlmegosztás helyreállítása nem sikerült

A fájlmegosztás törlésének törlése a **Files** portálról vagy az [Undelete](scripts/backup-powershell-script-undelete-file-share.md) szkript használatával a biztonsági mentés folytatásához és az adatok végleges törlésének megakadályozásához.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState – A biztonsági mentés meghiúsult, mert a társított Azure-fájlmegosztás véglegesen törölve lett

Hibakód: UserErrorBackupAFSInDeleteState

Hibaüzenet: A biztonsági mentés meghiúsult, mert a társított Azure-fájlmegosztás véglegesen törölve lett

Ellenőrizze, hogy a biztonsági mentéses fájlmegosztás véglegesen törölve lett-e. Ha igen, állítsa le a fájlmegosztás biztonsági mentését az ismétlődő biztonsági mentési hibák elkerülése érdekében. A védelem leállításának elsajátításért lásd: [Az Azure-fájlmegosztás védelmének leállítása](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Következő lépések

További információ az Azure-fájlmegosztások biztonsági mentésekor:

- [Azure-fájlmegosztások biztonsági mentése](backup-afs.md)
- [Azure-fájlmegosztás biztonsági mentése – gyakori kérdések](backup-azure-files-faq.yml)
