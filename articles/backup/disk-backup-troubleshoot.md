---
title: Biztonsági mentési hibák elhárítása az Azure Disk Backup szolgáltatásban
description: Ismerje meg, hogy miként lehet elhárítani a biztonsági mentési hibákat az Azure Disk Backup szolgáltatásban
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 855c6c5b19b10bdb699a25f89ebc29001b7941ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737727"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>Biztonsági mentési hibák elhárítása az Azure Disk Backup szolgáltatásban (előzetes verzió)

>[!IMPORTANT]
>Az Azure Disk Backup előzetes verzióban érhető el, és nem ajánlott éles környezetben üzemelő számítási feladatokhoz. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A régió elérhetőségét a [támogatási mátrixban](disk-backup-support-matrix.md)tekintheti meg.
>
>[Töltse ki ezt a kérdőívet](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) az előzetes verzióra való feliratkozáshoz.

Ez a cikk az Azure Disk szolgáltatással kapcsolatos biztonsági mentési és visszaállítási problémákra vonatkozó hibaelhárítási információkat tartalmaz. Az [Azure Disk Backup](disk-backup-overview.md) -régiók rendelkezésre állásával, a támogatott forgatókönyvekkel és korlátozásokkal kapcsolatos további információkért tekintse meg a [támogatási mátrixot](disk-backup-support-matrix.md).

## <a name="common-issues-faced-with-azure-disk-backup"></a>Az Azure Disk Backup szolgáltatással kapcsolatos gyakori problémák

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Hibakód: UserErrorSnapshotRGSubscriptionMismatch

Hibaüzenet: érvénytelen előfizetés van kiválasztva a pillanatkép-adattárhoz

Javasolt művelet: a lemezeket és a lemez-pillanatképeket ugyanabban az előfizetésben tárolja a rendszer. Bármelyik erőforráscsoportot kiválaszthatja az előfizetésen belüli lemez-Pillanatképek tárolásához. Válassza ki ugyanazt az előfizetést, mint a forrásoldali lemez. További információ a [támogatási mátrixban](disk-backup-support-matrix.md)található.

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Hibakód: UserErrorSnapshotRGNotFound

Hibaüzenet: nem sikerült végrehajtani a műveletet, mert a pillanatkép-adattár erőforráscsoport nem létezik.

Javasolt művelet: hozza létre az erőforráscsoportot, és adja meg a szükséges engedélyeket. További információ: a [biztonsági mentés konfigurálása](backup-managed-disks.md#configure-backup).

### <a name="error-code-usererrormanageddisknotfound"></a>Hibakód: UserErrorManagedDiskNotFound

Hibaüzenet: nem sikerült végrehajtani a műveletet, mert a felügyelt lemez már nem létezik.

Javasolt művelet: a biztonsági mentések továbbra is sikertelenek lesznek, mivel előfordulhat, hogy a lemez törölve lett, vagy egy másik helyre lett áthelyezve. A meglévő visszaállítási pont használatával állítsa vissza a lemezt, ha az véletlenül törölve lett. Ha a lemezt egy másik helyre helyezi át, konfigurálja a lemez biztonsági mentését.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Hibakód: UserErrorNotEnoughPermissionOnDisk

Hibaüzenet: Azure Backup szolgáltatásnak további engedélyekkel kell rendelkeznie a lemezen a művelet elvégzéséhez.

Javasolt művelet: a biztonsági mentési tár felügyelt identitásának megadása a megfelelő engedélyekkel a lemezen. Tekintse meg [a dokumentációt](backup-managed-disks.md) , amelyből megtudhatja, milyen engedélyekre van szükség a Backup-tároló által felügyelt identitás és annak megadásához.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Hibakód: UserErrorNotEnoughPermissionOnSnapshotRG

Hibaüzenet: Azure Backup szolgáltatás a művelet elvégzéséhez további engedélyeket igényel a pillanatkép-adattár erőforráscsoporthoz.

Javasolt művelet: adja meg a biztonsági mentési tár felügyelt identitását a pillanatképek adattárának erőforráscsoport megfelelő engedélyeivel. A pillanatkép-adattár erőforráscsoport az a hely, ahol a rendszer a lemez pillanatképeit tárolja. Tekintse át [a dokumentációt](backup-managed-disks.md) , amelyből megtudhatja, hogy melyik az erőforráscsoport, milyen engedélyekre van szükség a Backup-tároló által felügyelt identitás és annak megadásához.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Hibakód: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

Hibaüzenet: érvénytelen lemez vagy Azure Backup szolgáltatás további engedélyeket igényel a lemezen a művelet végrehajtásához.

Javasolt művelet: a biztonsági mentések továbbra is sikertelenek lesznek, mivel előfordulhat, hogy a lemez törölve lett, vagy egy másik helyre lett áthelyezve. A meglévő visszaállítási pont használatával állítsa vissza a lemezt, ha az véletlenül törölve lett. Ha a lemezt egy másik helyre helyezi át, konfigurálja a lemez biztonsági mentését. Ha a lemez nincs törölve vagy áthelyezve, adja meg a biztonsági mentési tár felügyelt azonosítóját a megfelelő engedélyekkel a lemezen. Tekintse meg [a dokumentációt](backup-managed-disks.md) , amelyből megtudhatja, milyen engedélyekre van szükség a Backup-tár felügyelt identitásához és annak biztosításához.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Hibakód: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

Hibaüzenet: nem sikerült végrehajtani a műveletet, mert a pillanatkép-adattár erőforráscsoport már nem létezik. Vagy Azure Backup szolgáltatáshoz további engedélyek szükségesek a pillanatkép-adattár erőforráscsoporthoz a művelet elvégzéséhez.

Javasolt művelet: hozzon létre egy erőforráscsoportot, és adja meg a biztonsági mentési tár felügyelt identitásának megfelelő engedélyeket a pillanatkép-adattár erőforráscsoporthoz. A pillanatkép-adattár erőforráscsoport az a hely, ahol a rendszer a lemez pillanatképeit tárolja. Tekintse át [a dokumentációt](backup-managed-disks.md) , hogy megtudja, mi az erőforráscsoport, milyen engedélyekre van szükség a Backup-tár felügyelt identitásához és annak biztosításához.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Hibakód: UserErrorDiskBackupAuthorizationFailed

Hibaüzenet: a Backup-tároló felügyelt identitása nem rendelkezik a művelet végrehajtásához szükséges engedélyekkel.

Javasolt művelet: a biztonságimásolat-tároló felügyelt identitásának megadása a megfelelő engedélyekkel a lemezen, amelyről biztonsági mentést kell készíteni, valamint a pillanatképek adattár-erőforráscsoport, ahol a pillanatképek tárolódnak. Tekintse meg [a dokumentációt](backup-managed-disks.md) , amelyből megtudhatja, milyen engedélyekre van szükség a Backup-tár felügyelt identitásához és annak biztosításához.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Hibakód: UserErrorSnapshotRGOrMSIPermissionsNotPresent

Hibaüzenet: nem sikerült végrehajtani a műveletet, mert a pillanatkép-adattár erőforráscsoport már nem létezik. Vagy Azure Backup szolgáltatáshoz további engedélyek szükségesek a pillanatkép-adattár erőforráscsoporthoz a művelet elvégzéséhez.

Javasolt művelet: hozza létre az erőforráscsoportot, és adja meg a biztonsági mentési tár felügyelt identitásának megfelelő engedélyeket a pillanatkép-adattár erőforráscsoporthoz. A pillanatkép-adattár erőforráscsoport az a hely, ahol a pillanatképek tárolódnak. Tekintse át [a dokumentációt](backup-managed-disks.md) , hogy megtudja, mi az erőforráscsoport, milyen engedélyekre van szükség a Backup-tár felügyelt identitása számára, és hogyan kell megadnia.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Hibakód: UserErrorOperationalStoreParametersNotProvided

Hibaüzenet: nem sikerült végrehajtani a műveletet, mert nincs megadva a pillanatkép-adattár erőforráscsoport-paramétere.

Javasolt művelet: adja meg a pillanatkép-adattár erőforráscsoport-paraméterét. A pillanatkép-adattár erőforráscsoport az a hely, ahol a rendszer a lemez pillanatképeit tárolja. További információkért tekintse meg [a dokumentációt](backup-managed-disks.md).

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Hibakód: UserErrorInvalidOperationalStoreResourceGroup

Hibaüzenet: a pillanatkép-adattár megadott erőforráscsoport érvénytelen.

Javasolt művelet: adjon meg egy érvényes erőforráscsoportot a pillanatkép-adattárhoz. A pillanatkép-adattár erőforráscsoport az a hely, ahol a rendszer a lemez pillanatképeit tárolja. További információkért tekintse meg [a dokumentációt](backup-managed-disks.md).

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Hibakód: UserErrorDiskBackupDiskTypeNotSupported

Hibaüzenet: nem támogatott lemez típusa

Javasolt művelet: Tekintse meg [a támogatási mátrixot](disk-backup-support-matrix.md) a nem támogatott forgatókönyvekről és korlátozásokról.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Hibakód: UserErrorSameNameDiskAlreadyExists

Hibaüzenet: nem sikerült visszaállítani, mert már létezik ilyen nevű lemez a kiválasztott célként megadott erőforráscsoporthoz

Javasolt művelet: adjon meg egy másik lemezt a visszaállításhoz. További információ: az [Azure Managed Disks visszaállítása](restore-managed-disks.md).

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Hibakód: UserErrorRestoreTargetRGNotFound

Hibaüzenet: a művelet meghiúsult, mert a célként megadott erőforráscsoport nem létezik.

Javasolt művelet: adjon meg egy érvényes erőforráscsoportot a visszaállításhoz. További információ: az [Azure Managed Disks visszaállítása](restore-managed-disks.md).

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Hibakód: UserErrorNotEnoughPermissionOnTargetRG

Hibaüzenet: Azure Backup szolgáltatáshoz további engedélyek szükségesek a cél erőforráscsoporthoz a művelet végrehajtásához.

Javasolt művelet: a biztonsági mentési tár felügyelt identitásának megadása a megfelelő engedélyekkel a cél erőforráscsoporthoz. A cél erőforráscsoport a kijelölt hely, ahol a lemezt vissza kell állítani. A [visszaállítási dokumentációban](restore-managed-disks.md) megtudhatja, milyen engedélyekre van szükség a Backup-tár felügyelt identitásához, és hogyan biztosíthatja azt.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Hibakód: UserErrorSubscriptionDiskQuotaLimitReached

Hibaüzenet: a művelet meghiúsult, mert elérte a lemezkvóta maximális korlátját az előfizetésben.

Javasolt művelet: Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait és a kvóta dokumentációját](../azure-resource-manager/management/azure-subscription-service-limits.md) , vagy forduljon Microsoft ügyfélszolgálata további útmutatásért.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Hibakód: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Hibaüzenet: a művelet meghiúsult, mert a célként megadott erőforráscsoport nem létezik. Vagy Azure Backup szolgáltatáshoz további engedélyek szükségesek a cél erőforráscsoporthoz a művelet elvégzéséhez.

Javasolt művelet: adjon meg egy érvényes erőforráscsoportot a visszaállításhoz, és adja meg a biztonságimásolat-tároló felügyelt azonosítóját a megfelelő engedélyekkel a cél erőforráscsoporthoz. A cél erőforráscsoport a kijelölt hely, ahol a lemezt vissza kell állítani. A [visszaállítási dokumentációban](restore-managed-disks.md) megtudhatja, milyen engedélyekre van szükség a Backup-tár felügyelt identitásához, és hogyan biztosíthatja azt.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Hibakód: UserErrorDESKeyVaultKeyDisabled

Hibaüzenet: a lemez titkosításához használt Key Vault-kulcs nincs engedélyezve állapotban.

Javasolt művelet: engedélyezze a lemezes titkosítási készlethez használt Key Vault-kulcsot. Tekintse át a [támogatási mátrix](disk-backup-support-matrix.md)korlátozásait.

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Hibakód: UserErrorMSIPermissionsNotPresentOnDES

Hibaüzenet: Azure Backup szolgáltatásnak engedéllyel kell rendelkeznie a lemezzel használt titkosítási csoport eléréséhez.

Javasolt művelet: az olvasó hozzáférést biztosít a Backup-tár felügyelt identitásához a lemez titkosítási készletéhez (DES).

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Hibakód: UserErrorDESKeyVaultKeyNotAvailable

Hibaüzenet: a lemez titkosítási készletéhez használt kulcstartó-kulcs nem érhető el.

Javasolt művelet: Győződjön meg arról, hogy a lemez titkosításához használt Key Vault-kulcs nincs letiltva vagy törölve.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Hibakód: UserErrorDiskSnapshotNotFound

Hibaüzenet: a visszaállítási ponthoz tartozó lemez pillanatképe törölve lett.

Javasolt művelet: a pillanatképeket az előfizetésében található pillanatkép-adattár erőforráscsoport tárolja. Lehetséges, hogy a kijelölt visszaállítási ponthoz kapcsolódó pillanatkép törölve lett vagy át lett helyezve ebből az erőforráscsoporthoz. Használjon másik helyreállítási pontot a visszaállításhoz. Továbbá kövesse a [helyreállítási dokumentációban](restore-managed-disks.md)említett pillanatkép-erőforráscsoport kiválasztásához ajánlott útmutatást.

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Hibakód: UserErrorSnapshotMetadataNotFound

Hibaüzenet: a visszaállítási pont lemez-pillanatképének metaadatai törölve lettek.

Javasolt művelet: érdemes lehet egy másik helyreállítási pontot használni a visszaállításhoz. További információ: [Restore dokumentáció](restore-managed-disks.md).

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Hibakód: BackupAgentPluginHostValidateProtectionError

Hibaüzenet: a lemezes biztonsági mentés még nem érhető el a Backup-tároló azon régiójában, amelyben a védelem konfigurálása folyamatban van.

Javasolt művelet: a Backup-tárolónak az előzetes verzióban támogatott régióban kell lennie. A régió elérhetőségét lásd a [támogatási mátrixban](disk-backup-support-matrix.md).

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Hibakód: UserErrorDppDatasourceAlreadyHasBackupInstance

Hibaüzenet: az a lemez, amelyre a biztonsági másolatot kívánja beállítani, már védett. A lemez már társítva van egy backup-tároló biztonsági másolati példányához.

Javasolt művelet: Ez a lemez már társítva van egy backup-tároló biztonsági másolati példányához. Ha újra szeretné védeni ezt a lemezt, akkor törölje a Backup-tárolót a biztonsági mentési tárolóból, ahol a lemez jelenleg védett, és minden más tárolóban újra meg kell védeni a lemezt.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Hibakód: UserErrorDppDatasourceAlreadyProtected

Hibaüzenet: az a lemez, amelyre a biztonsági másolatot kívánja beállítani, már védett. A lemez már társítva van egy backup-tároló biztonsági másolati példányához.

Javasolt művelet: Ez a lemez már társítva van egy backup-tároló biztonsági másolati példányához. Ha újra szeretné védeni ezt a lemezt, akkor törölje a Backup-tárolót a biztonsági mentési tárolóból, ahol az jelenleg védett, és minden más tárolóban védje a lemezt.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Hibakód: UserErrorMaxConcurrentOperationLimitReached

Hibaüzenet: nem indítható el a művelet, mert elérte az egyidejű biztonsági másolatok maximális számát.

Javasolt művelet: Várjon, amíg az előző futó biztonsági mentés be nem fejeződik.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Disk Backup támogatási mátrixa](disk-backup-support-matrix.md)