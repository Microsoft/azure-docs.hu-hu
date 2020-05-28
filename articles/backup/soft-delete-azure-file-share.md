---
title: Azure-fájlmegosztás véletlen törlésének védelme
description: Ismerje meg, hogy a törlés hogyan védhető meg az Azure-fájlmegosztás véletlen törlésével.
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 09d74a135fc43a7758004d77af2ec4c478345a2c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122408"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Az Azure-fájlmegosztás véletlen törlésének védelme Azure Backup használatával

A maihoz vagy a véletlen törlés elleni védelem biztosításához a rendszer a Storage-fiókban lévő összes fájlmegosztás esetében engedélyezi a [Soft delete](https://docs.microsoft.com/azure/storage/files/storage-files-prevent-file-share-deletion) használatát, amikor a megfelelő Storage-fiókban lévő fájlmegosztás biztonsági mentését konfigurálja. A helyreállítható törléssel, még akkor is, ha egy rosszindulatú szereplő törli a fájlmegosztást, a fájlmegosztás tartalmát és a helyreállítási pontokat (pillanatképeket) legalább 14 napig őrzi meg a rendszer, ami lehetővé teszi a fájlmegosztás adatvesztés nélküli helyreállítását.  

A Soft delete csak a standard és a prémium szintű Storage-fiókok esetében támogatott, és jelenleg az [ezen régiók](azure-file-share-support-matrix.md)Azure Backup oldalán engedélyezhető.

A következő folyamatábra a biztonsági mentési elemek különböző lépéseit és állapotait mutatja be, ha a Storage-fiókban engedélyezve van a fájlmegosztás:

 ![Nem kötelező törlési folyamatábra](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>Mikor engedélyezi a rendszer a Storage-fiókomban lévő fájlmegosztás esetén a helyreállítható törlést?

Amikor az első alkalommal konfigurálja a biztonsági mentést egy Storage-fiókban lévő fájlmegosztás esetében, Azure Backup szolgáltatás lehetővé teszi a megfelelő Storage-fiókban lévő összes fájlmegosztás törlését.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>Megadhatom, hogy hány nap elteltével maradnak meg a pillanatképek és a visszaállítási pontok a fájlmegosztás törlése után?

Igen, a követelményeknek megfelelően állíthatja be a megőrzési időszakot. [Ez a dokumentum](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal) a megőrzési időtartam konfigurálásának lépéseit ismerteti. A biztonsági másolattal ellátott fájlmegosztás esetén a minimális megőrzési beállításnak 14 nap kell lennie.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Azure Backup alaphelyzetbe állítja a megőrzési beállítást, mert kevesebb, mint 14 napig konfiguráltam?

Biztonsági szempontból azt javasoljuk, hogy a Storage-fiókok esetében a biztonsági mentéssel rendelkező fájlmegosztás esetében legalább 14 napig tartson fenn minimális adatmegőrzést. Tehát minden biztonsági mentési feladaton fut, ha Azure Backup megállapítja, hogy a beállítás értéke 14 napnál kevesebb, akkor a rendszer 14 napig visszaállítja azt.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>Milyen költségek merültek fel a megőrzési időszak során?

A puha törlésű időszak alatt a védett példányok és a pillanatképek tárolási díja is marad.  Emellett a prémium szintű fájlmegosztás esetében a standard fájlmegosztás és a pillanatkép-tárolási sebesség esetében a felszámított kapacitás díja.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>Használhatok visszaállítási műveletet, ha az adataim nem törölt állapotban vannak?

Először törölnie kell a törölt fájlmegosztás törlését a visszaállítási műveletek elvégzéséhez. A törlési művelettel a fájlmegosztást a biztonsági másolatba kerülő állapotba helyezheti, ahol bármikor visszaállítható. A fájlmegosztás törlésének visszavonásához látogasson el [erre a hivatkozásra](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal#restore-soft-deleted-file-share) , vagy tekintse meg a [fájlmegosztási parancsfájl törlésének](./scripts/backup-powershell-script-undelete-file-share.md)visszavonása című témakört.

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>Hogyan törölhetem egy fájlmegosztás információit egy olyan Storage-fiókban, amely legalább egy védett fájlmegosztást tartalmaz?

Ha van legalább egy védett fájlmegosztás egy Storage-fiókban, az azt jelenti, hogy az adott fiókban lévő összes fájlmegosztás esetében engedélyezve van a Soft DELETE, és az adatai a törlési művelet után 14 napig megmaradnak. Ha azonban azonnal törölni kívánja az adatvesztést, és nem szeretné megőrizni az adatmegőrzést, kövesse az alábbi lépéseket:

1. Ha már törölte a fájlmegosztást, miközben a Soft delete engedélyezve volt, először törölje a fájlmegosztást a [fájlok portálján](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal#restore-soft-deleted-file-share) , vagy a [fájlmegosztás törlésének megszüntetése parancsfájl](./scripts/backup-powershell-script-undelete-file-share.md)használatával.
2. Az [ebben a dokumentumban](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal#disable-soft-delete)említett lépéseket követve tiltsa le a Storage-fiókban lévő fájlmegosztás helyreállítható törlését.
3. Most törölje azt a fájlmegosztást, amelynek a tartalmát azonnal törölni kívánja.

>[!NOTE]
>A 2. lépést kell elvégeznie, mielőtt a következő ütemezett biztonsági mentési feladatot a Storage-fiókban lévő védett fájlmegosztás esetében futtatja. Mivel a biztonsági mentési feladatok futtatásakor a rendszer újból engedélyezi a tárolási fiókban lévő összes fájlmegosztás helyreállítható törlését.

>[!WARNING]
>Miután letiltotta a törlést a 2. lépésben, a fájlmegosztás során végrehajtott törlési művelet állandó törlési művelet. Ez azt jelenti, hogy ha véletlenül törli a biztonsági másolatban szereplő fájlmegosztást a Soft delete letiltása után, elveszíti az összes pillanatképet, és nem fogja tudni helyreállítani az adatokat.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>Egy fájlmegosztás helyreállítható törlési beállításának kontextusában milyen módosítások Azure Backup a Storage-fiók regisztrációjának törlésekor?

A regisztráció törlésének időpontjában a Azure Backup ellenőrzi a fájlmegosztás megőrzési időtartamának beállítását, és ha 14 napnál hosszabb vagy 14 napnál kevesebb, akkor a megőrzési időt a következőképpen hagyja. Ha azonban a megőrzés 14 nap, azt úgy tekintjük, hogy Azure Backup engedélyezte, és így letiltjuk a törlést a regisztráció törlése során. Ha meg szeretné szüntetni a Storage-fiók regisztrációját, miközben a megőrzési beállítást is megtartja, akkor a regisztráció törlése után engedélyezze újra a Storage-fiók ablaktáblán. [Erre a hivatkozásra kattintva](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal#restore-soft-deleted-file-share) megtekintheti a konfigurációs lépéseket.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [készíthet biztonsági mentést az Azure-fájlmegosztás Azure Portal](backup-afs.md)
