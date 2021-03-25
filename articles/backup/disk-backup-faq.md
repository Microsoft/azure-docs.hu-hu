---
title: Az Azure Disk Backup szolgáltatással kapcsolatos gyakori kérdések
description: Válaszok az Azure Disk Backup szolgáltatással kapcsolatos gyakori kérdésekre
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7729bc1120fc0e2f4361739a8e05f3a82ccb4268
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107312"
---
# <a name="frequently-asked-questions-about-azure-disk-backup"></a>Az Azure Disk Backup szolgáltatással kapcsolatos gyakori kérdések

Ez a cikk az Azure Disk Backup szolgáltatással kapcsolatos gyakori kérdésekre ad választ. Az [Azure Disk Backup](disk-backup-overview.md) -régiók rendelkezésre állásával, a támogatott forgatókönyvekkel és korlátozásokkal kapcsolatos további információkért tekintse meg a [támogatási mátrixot](disk-backup-support-matrix.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Készíthetek biztonsági másolatot a lemezről az Azure Disk Backup megoldással, ha ugyanazt a lemezt az Azure-beli virtuális gépek biztonsági másolatának használatával készíti el?

Azure Backup a lemezes biztonsági mentés és az [Azure virtuális gépek biztonsági mentési](backup-azure-vms-introduction.md) megoldásai segítségével párhuzamosan támogatja a felügyelt lemez biztonsági mentését. Ez akkor hasznos, ha a virtuális gépekre vonatkozó, az operációsrendszer-lemezre vagy egy adott adatlemezre vonatkozó, rendszeres biztonsági mentésre van szükség, amely az éles környezetbeli alkalmazások teljesítményének befolyásolása nélkül is összeomlott.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>Hogyan megkeresni azt a pillanatkép-erőforráscsoportot, amelyet egy lemez biztonsági mentésének konfigurálásához használok?

A **biztonsági mentési példány** képernyőjén megtalálhatja a pillanatképek erőforráscsoport mezőjét az **alapvető** erőforrások szakaszban. A megfelelő lemez biztonsági mentési példányát a Backup Centerben vagy a Backup-tárolóban keresheti ki és választhatja ki.

![Pillanatkép-erőforráscsoport mező](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>Mi az a pillanatkép-erőforráscsoport?

Az Azure Disk Backup a felügyelt lemezekhez biztosít működési szintű biztonsági mentést. Ez azt eredményezi, hogy az ütemezett és igény szerinti biztonsági mentési műveletek során létrehozott Pillanatképek az előfizetésben található erőforráscsoporthoz vannak tárolva. Azure Backup azonnali visszaállítást biztosít, mivel a növekményes Pillanatképek az előfizetésen belül tárolódnak. Ez az erőforráscsoport a pillanatképek erőforráscsoport néven ismert. További információ: a [biztonsági mentés konfigurálása](backup-managed-disks.md#configure-backup).

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>Miért kell a pillanatkép-erőforráscsoporthoz ugyanabban az előfizetésben lennie, mint a biztonsági mentés alatt álló lemeznek?

Nem hozható létre növekményes pillanatkép a lemez előfizetésén kívüli adott lemezhez. Ezért válassza ki azt az erőforráscsoportot az előfizetésen belül, amelyről biztonsági másolatot szeretne készíteni. További információ a felügyelt lemezek [növekményes pillanatképéről](../virtual-machines/disks-incremental-snapshots.md#restrictions) .

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>Miért van szükség a biztonsági másolatok konfigurálásához, a ütemezett és igény szerinti biztonsági mentések és a visszaállítási műveletek végrehajtásához szükséges szerepkör-hozzárendelések biztosításához?

Az Azure Disk Backup a legalacsonyabb jogosultsági szintű megközelítést használja az előfizetések felügyelt lemezeinek felderítéséhez, védelmehez és visszaállításához. Ennek eléréséhez Azure Backup a [Backup-tároló](backup-vault-overview.md) felügyelt identitásával fér hozzá más Azure-erőforrásokhoz. Egy rendszerhez rendelt felügyelt identitás csak egy erőforrásra korlátozódik, és az erőforrás életciklusához van kötve. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával engedélyeket adhat a felügyelt identitásnak. A felügyelt identitás olyan speciális típusú szolgáltatásnév, amelyet csak az Azure-erőforrásokkal lehet használni. További információ a [felügyelt identitásokról](../active-directory/managed-identities-azure-resources/overview.md). Alapértelmezés szerint a biztonságimásolat-tároló nem jogosult a lemez biztonsági mentésére, rendszeres pillanatképek létrehozására, Pillanatképek törlésére, a megőrzési időszak után, valamint a lemez biztonsági másolatból való visszaállítására. Ha a szerepkör-hozzárendeléseket explicit módon adja meg a biztonságimásolat-tároló felügyelt identitásához, Ön felügyeli az előfizetések erőforrásaira vonatkozó engedélyek kezelését.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>Miért korlátozza a biztonsági mentési szabályzat a megőrzés időtartamát?

Az Azure Disk Backup növekményes pillanatképeket használ, amelyek lemezenként 200 pillanatképre korlátozódnak. Ahhoz, hogy az igény szerinti biztonsági mentést az ütemezett biztonsági másolatokból is el lehessen végezni, a biztonsági mentési szabályzat a 180-re korlátozza a teljes biztonsági mentést. További információ a felügyelt lemezek [növekményes pillanatképéről](../virtual-machines/disks-incremental-snapshots.md#restrictions) .

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>Hogyan működik az óránkénti és a napi biztonsági mentési gyakoriság a biztonsági mentési szabályzatban?

Az Azure Disk Backup naponta több biztonsági mentést is kínál. Ha több gyakori biztonsági mentésre van szüksége, válassza az **óránkénti** biztonsági mentés gyakoriságát. A biztonsági mentéseket a kiválasztott **időintervallum alapján ütemezi a rendszer** . Ha például **4 óránként** választja ki, akkor a biztonsági másolatok körülbelül 4 óránként készülnek, hogy a biztonsági másolatok egyenlően legyenek elosztva a nap során. Ha a napi biztonsági mentés elég elegendő, válassza ki a **napi** biztonsági mentés gyakoriságát. A napi biztonsági mentési gyakoriságban megadhatja, hogy a biztonsági másolatok Mikor legyenek a nap folyamán. Fontos megjegyezni, hogy a nap időpontja a biztonsági mentés kezdési idejét jelzi, nem pedig a biztonsági mentés befejeződésének időpontját. A biztonsági mentési művelet befejezéséhez szükséges idő számos tényezőtől függ, beleértve az egymást követő biztonsági mentések közötti adatforgalom mértékét. Az Azure Disk Backup azonban egy ügynök nélküli biztonsági másolat, amely olyan [növekményes pillanatképeket](../virtual-machines/disks-incremental-snapshots.md) használ, amelyek nem befolyásolják az éles alkalmazások teljesítményét.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>Miért nem vonatkozik a biztonságimásolat-tár redundancia-beállítása az operatív szinten (a pillanatkép-erőforráscsoport) tárolt biztonsági másolatok esetében?

A Azure Backup a felügyelt lemezek [növekményes pillanatképeit](../virtual-machines/disks-incremental-snapshots.md#restrictions) használja, amelyek csak a lemezek változásait tárolják a lemezeken a standard HDD tároló utolsó pillanatképe óta, a szülő lemez tárolási típusától függetlenül. A nagyobb megbízhatóság érdekében a növekményes Pillanatképek tárolása a zóna redundáns tárterületén (ZRS) történik, alapértelmezés szerint a ZRS támogató régiókban. Az Azure Disk Backup jelenleg támogatja a felügyelt lemezek működési biztonsági mentését, amely nem másolja a biztonsági másolatokat a Backup-tárolóba. Így a biztonságimásolat-tároló biztonságimásolat-tárolási redundáns beállítása nem vonatkozik a helyreállítási pontokra.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>Használhatom a Backup centert a biztonsági másolatok konfigurálásához és az Azure-lemezek biztonsági mentési példányainak kezeléséhez?

Igen, az Azure Disk Backup integrálva van a [Backup Centerbe](backup-center-overview.md), amely **egyetlen egységes felügyeleti élményt** biztosít az Azure-ban a nagyvállalatok számára a biztonsági másolatok szabályozására, figyelésére, üzemeltetésére és elemzésére. A Backup-tárolóval biztonsági mentést készíthet, visszaállíthatja és kezelheti a tárolón belül védett biztonsági mentési példányokat.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Miért van szükség egy backup-tároló létrehozására, és ne használjon Recovery Services tárolót?

A Backup-tároló egy Azure-beli tárolási entitás, amely a Azure Backup által támogatott új munkaterhelésekre vonatkozó biztonsági mentési adatok tárolására szolgál. A Backup-tárolók használatával a különböző Azure-szolgáltatások, például az Azure Database for PostgreSQL-kiszolgálók, az Azure-lemezek és a Azure Backup által támogatott újabb számítási feladatok biztonsági mentési adatai tárolhatók. A Backup-tárolók megkönnyítik a biztonsági másolatok rendszerezését, miközben minimalizálják a felügyeleti terhelést. További információért tekintse meg a [Backup-tárolókat](./backup-vault-overview.md) .

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>Készíthető biztonsági másolat a lemezről, és a biztonságimásolat-tároló különböző előfizetésekben található?

Igen, a forrás által felügyelt lemez, amelyről biztonsági mentés készül, és a biztonságimásolat-tároló különböző előfizetésekben lehet.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>Készíthető biztonsági másolat a lemezről, és a biztonságimásolat-tároló különböző régiókban található?

Nem, jelenleg a forrás által felügyelt lemezről biztonsági mentés készül, és a biztonságimásolat-tárolónak ugyanabban a régióban kell lennie.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>Visszaállíthatok egy lemezt egy másik előfizetésbe?

Igen, visszaállíthatja a lemezt egy másik előfizetésre, mint a forrás által felügyelt lemezről, amelyről a biztonsági mentés készült.

### <a name="can-i-back-up-multiple-disks-together"></a>Több lemezről is készíthető biztonsági mentés?

Nem támogatottak a virtuális géphez csatlakoztatott több lemez időpontra vonatkozó pillanatképei. További információ: a [biztonsági mentés konfigurálása](backup-managed-disks.md#configure-backup) és a korlátozásokkal kapcsolatos további információkért tekintse meg a [támogatási mátrixot](disk-backup-support-matrix.md).

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>Milyen lehetőségeim vannak a több előfizetéshez tartozó lemezek biztonsági mentésére?

Jelenleg a Azure Portal használatával konfigurálhatja a lemezek biztonsági mentését, de legfeljebb 20 lemezre van korlátozva ugyanabból az előfizetésből.

### <a name="what-is-a-target-resource-group"></a>Mi az a cél erőforráscsoport?

Egy visszaállítási művelet során kiválaszthatja azt az előfizetést és erőforráscsoportot, amelyben vissza kívánja állítani a lemezt. Azure Backup új lemezeket hoz létre a kiválasztott erőforráscsoport helyreállítási pontjából. Ezt nevezzük célként megadott erőforráscsoportnek. Vegye figyelembe, hogy a biztonsági mentési tár felügyelt identitása megköveteli, hogy a cél erőforráscsoporthoz tartozó szerepkör-hozzárendelés sikeresen végrehajtsa a visszaállítási műveletet. További információ: [Restore dokumentáció](restore-managed-disks.md).

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Milyen engedélyeket használ a Azure Backup a biztonsági mentési és visszaállítási művelet során?

A következő műveleteket kell végrehajtani **a lemezre** a biztonsági mentéshez rendelt lemezes **biztonságimásolat-olvasó** szerepkörben:

"Microsoft. számítás/lemezek/olvasás"

"Microsoft. számítás/lemezek/beginGetAccess/művelet"

A következő műveletek a **Pillanatkép-erőforráscsoporthoz** hozzárendelt **lemez-pillanatkép közreműködői** szerepkörben használatosak:

"Microsoft. számítás/Pillanatképek/Törlés"

"Microsoft. számítás/Pillanatképek/írás"

"Microsoft. számítás/Pillanatképek/olvasás"

"Microsoft. Storage/storageAccounts/Write"

"Microsoft. Storage/storageAccounts/Read"

"Microsoft. Storage/storageAccounts/Delete"

"Microsoft. Resources/Subscriptions/resourceGroups/Read"

"Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/Action"

"Microsoft. számítás/Pillanatképek/beginGetAccess/Action"

"Microsoft. számítás/Pillanatképek/endGetAccess/Action"

"Microsoft. számítás/lemezek/beginGetAccess/művelet"

A következő műveleteket kell használni a **cél erőforráscsoporthoz** rendelt **lemez-visszaállítási operátori** szerepkörben:

"Microsoft. számítás/lemezek/írás"

"Microsoft. számítás/lemezek/olvasás"

"Microsoft. Resources/Subscriptions/resourceGroups/Read"

>[!NOTE]
>A szerepkörök engedélyei a későbbiekben a Azure Backup szolgáltatás által hozzáadott szolgáltatások alapján változhatnak.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Disk Backup támogatási mátrixa](disk-backup-support-matrix.md)