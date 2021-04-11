---
title: Azure-Managed Disks biztonsági mentése
description: Ismerje meg, hogyan készíthet biztonsági mentést az Azure Managed Disksról a Azure Portalról.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: e234495eb483d6d0cc6ca556ca418138c61a99f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110627"
---
# <a name="back-up-azure-managed-disks"></a>Azure-Managed Disks biztonsági mentése

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni az [Azure felügyelt lemezről](../virtual-machines/managed-disks-overview.md) a Azure Portalról.

Ebből a cikkből megtudhatja, hogyan:

- Backup-tároló létrehozása

- Biztonsági mentési szabályzat létrehozása

- Azure-lemez biztonsági mentésének konfigurálása

- Igény szerinti biztonsági mentési feladatok futtatása

Az Azure Disk Backup-régiók rendelkezésre állásával, a támogatott forgatókönyvekkel és korlátozásokkal kapcsolatos információkért tekintse meg a [támogatási mátrixot](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Backup-tároló létrehozása

A Backup-tároló egy Azure-beli tárolási entitás, amely a Azure Backup által támogatott különböző újabb számítási feladatok, például Azure Database for PostgreSQL-kiszolgálók és Azure-lemezek biztonsági mentési adataival rendelkezik. A Backup-tárolók megkönnyítik a biztonsági másolatok rendszerezését, miközben minimalizálják a felügyeleti terhelést. A Backup-tárolók az Azure Azure Resource Manager modelljén alapulnak, amely továbbfejlesztett funkciókat biztosít a biztonsági mentési adatmennyiség biztosításához.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.
1. A keresőmezőbe írja be a **Backup Center** kifejezést.
1. A **szolgáltatások** területen válassza a **biztonsági mentési központ** elemet.
1. A **biztonsági mentési központ** **lapon válassza a** tároló elemet.

   ![Tároló kiválasztása a biztonsági mentési központban](./media/backup-managed-disks/backup-center.png)

1. A **kezdeményezés: tár létrehozása** képernyőn válassza a Backup- **tároló** elemet, és **folytassa a folytatást**.

   ![Kezdeményezés: tároló létrehozása](./media/backup-managed-disks/initiate-create-vault.png)

1. Az **alapvető beállítások** lapon adja meg az előfizetést, az erőforráscsoportot, a biztonságimásolat-tároló nevét, a régiót és a biztonságimásolat-tárolási redundanciát. Folytassa a **felülvizsgálat + létrehozás** lehetőség kiválasztásával. További információ a [Backup-tároló létrehozásáról](./backup-vault-overview.md#create-a-backup-vault).

   ![Tár áttekintése és létrehozása](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

1. Az előző lépésben létrehozott *DemoVault* **Backup-tárolóban** lépjen a **biztonsági mentési házirendek** elemre, és válassza a **Hozzáadás** lehetőséget.

   ![Biztonsági mentési szabályzat hozzáadása](./media/backup-managed-disks/backup-policies.png)

1. Az **alapvető beállítások** lapon adja meg a házirend nevét, válassza ki az **adatforrás típusát** **Azure-lemezként**. A tár már előre fel van töltve, a kiválasztott tár tulajdonságai pedig megjelennek.

   >[!NOTE]
   > Bár a kiválasztott tár a globális redundancia beállítással rendelkezhet, jelenleg az Azure Disk Backup csak a pillanatkép-adattár használatát támogatja. Az összes biztonsági mentést egy erőforráscsoport tárolja az előfizetésben, és a rendszer nem másolja a Backup tárolóba.

   ![Adatforrás típusának kiválasztása](./media/backup-managed-disks/datasource-type.png)

1. A **biztonsági mentési szabályzat** lapon válassza ki a biztonsági mentés ütemtervének gyakoriságát.

   ![Biztonsági mentési ütemterv gyakoriságának kiválasztása](./media/backup-managed-disks/backup-schedule-frequency.png)

   Az Azure Disk Backup naponta több biztonsági mentést is kínál. Ha több gyakori biztonsági mentésre van szüksége, válassza az **óránkénti** biztonsági mentés gyakoriságát, hogy a biztonsági másolatokat 4, 6, 8 vagy 12 óra intervallummal végezze. A biztonsági mentéseket a kiválasztott **időintervallum alapján ütemezi a rendszer** . Ha például **4 óránként** választja ki, akkor a biztonsági másolatok körülbelül 4 óránként lesznek elérhetők, így a biztonsági másolatok egyenlően oszlanak meg a nap folyamán. Ha a napi biztonsági mentés elegendő, válassza ki a **napi** biztonsági mentés gyakoriságát. A napi biztonsági mentési gyakoriságban megadhatja a biztonsági másolatok készítésének napját. Fontos megjegyezni, hogy a nap időpontja a biztonsági mentés kezdési idejét jelzi, nem pedig a biztonsági mentés befejeződésének időpontját. A biztonsági mentési művelet befejezéséhez szükséges idő számos tényezőtől függ, többek között a lemez méretétől és az egymást követő biztonsági mentések közötti adatforgalomtól. Az Azure Disk Backup azonban olyan ügynök nélküli biztonsági másolat, amely [növekményes pillanatképeket](../virtual-machines/disks-incremental-snapshots.md)használ, ami nem befolyásolja az éles alkalmazások teljesítményét.

1. A **biztonsági mentési szabályzat** lapon válassza ki a helyreállítási pont célkitűzésének (RPO) követelményének megfelelő adatmegőrzési beállításokat.

   Az alapértelmezett adatmegőrzési szabály akkor érvényes, ha nincs megadva más adatmegőrzési szabály. Az alapértelmezett megőrzési szabály módosítható a megőrzési időtartam módosításához, de nem törölhető. Új adatmegőrzési szabályt a **megőrzési szabály hozzáadása** lehetőség kiválasztásával adhat hozzá.

   ![Adatmegőrzési szabály hozzáadása](./media/backup-managed-disks/add-retention-rule.png)

   Kiválaszthatja a naponta vagy hetente végzett **első sikeres biztonsági mentést** , és megadhatja a megőrzés időtartamát, amelyet az adott biztonsági másolatok megőrzése előtt meg kell őrizni. Ez a beállítás akkor hasznos, ha a nap vagy a hét bizonyos biztonsági másolatait hosszabb ideig szeretné megőrizni. A többi gyakori biztonsági mentés rövidebb ideig is megtartható.

   ![Adatmegőrzési beállítások](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >A Managed Disks Azure Backup növekményes pillanatképeket használ, amelyek lemezenként 200 pillanatképre korlátozódnak. Ahhoz, hogy az igény szerinti biztonsági mentést az ütemezett biztonsági másolatokból is el lehessen végezni, a biztonsági mentési szabályzat a 180-re korlátozza a teljes biztonsági mentést. További információ a felügyelt lemez [növekményes pillanatképekről](../virtual-machines/disks-incremental-snapshots.md#restrictions) .

1. A biztonsági mentési szabályzat létrehozásához kattintson a **felülvizsgálat + létrehozás** lehetőségre.

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

A Backup-tároló felügyelt identitás használatával fér hozzá más Azure-erőforrásokhoz. A felügyelt lemezek biztonsági mentésének konfigurálásához a Backup-tár felügyelt identitásához engedélyeket kell beállítani a forrás-és az erőforráscsoportok számára, ahol a pillanatképek létrehozása és kezelése történik.

Egy rendszerhez rendelt felügyelt identitás csak egy erőforrásra korlátozódik, és az erőforrás életciklusához van kötve. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával engedélyeket adhat a felügyelt identitásnak. A felügyelt identitás olyan speciális típusú szolgáltatásnév, amelyet csak az Azure-erőforrásokkal lehet használni. További információ a [felügyelt identitásokról](../active-directory/managed-identities-azure-resources/overview.md).

A felügyelt lemezek biztonsági mentésének konfigurálásához a következő előfeltételek szükségesek:

1. Rendelje hozzá a **lemezes biztonságimásolat-olvasó** szerepkört a biztonságimásolat-tároló felügyelt identitásához a biztonsági mentésre szolgáló lemezen.

   1. Ugorjon arra a lemezre, amelyről biztonsági mentést kell készíteni.

   1. Nyissa meg a **hozzáférés-vezérlés (iam)** lehetőséget, és válassza a **szerepkör-hozzárendelések hozzáadása** elemet.

   1. A jobb oldali kontextus ablaktáblán válassza a **lemezes biztonsági mentési olvasó** lehetőséget a **szerepkör** legördülő listában. Válassza ki a biztonságimásolat-tároló felügyelt identitását, és **mentse**.

   >[!TIP]
   >A tár felügyelt identitásának kiválasztásához írja be a mentési tároló nevét.

   ![Lemezes biztonságimásolat-olvasó szerepkör hozzáadása](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Rendelje hozzá a **lemez-pillanatkép közreműködői** szerepkört a biztonságimásolat-tároló felügyelt identitásához az erőforráscsoporthoz, ahol a biztonsági másolatokat a Azure Backup szolgáltatás hozza létre és kezeli. A lemez-pillanatképeket az előfizetésében található erőforráscsoport tárolja. Annak engedélyezéséhez, hogy Azure Backup szolgáltatás pillanatképeket hozzon létre, tároljon és kezeljen, engedélyeket kell biztosítania a Backup-tárolóhoz.

   **Erőforráscsoport kiválasztása Pillanatképek tárolásához és kezeléséhez:**

   - Ne jelölje ki ugyanazt az erőforráscsoportot, mint a forrásoldali lemez.

   - Útmutatóként javasoljuk, hogy hozzon létre egy dedikált erőforráscsoportot a Azure Backup szolgáltatás által használandó pillanatkép-adattárként. A dedikált erőforráscsoport lehetővé teszi a hozzáférési engedélyek korlátozását az erőforráscsoport számára, biztonságot nyújtva és megkönnyíti a biztonsági mentési adat kezelését.

   - Ezt az erőforráscsoportot használhatja a pillanatképek több lemezre történő tárolásához (vagy a tervek szerint) biztonsági mentést készíteni.  

   - Nem hozható létre növekményes pillanatkép a lemez előfizetésén kívüli adott lemezhez. Ezért válassza ki azt az erőforráscsoportot az előfizetésen belül, amelyről biztonsági másolatot szeretne készíteni. További információ a felügyelt lemezek [növekményes pillanatképéről](../virtual-machines/disks-incremental-snapshots.md#restrictions) .

   A szerepkör hozzárendeléséhez kövesse az alábbi lépéseket:

   1. Nyissa meg az erőforráscsoportot. Az erőforráscsoport például a *SnapshotRG*, amely ugyanabban az előfizetésben található, mint a biztonsági mentéshez használt lemez.

   1. Nyissa meg a **hozzáférés-vezérlés (iam)** lehetőséget, és válassza a **szerepkör-hozzárendelések hozzáadása** elemet.

   1. A jobb oldali kontextus ablaktáblán válassza a **lemez pillanatkép-közreműködő** elemet a **szerepkör** legördülő listában. Válassza ki a biztonságimásolat-tároló felügyelt identitását, és **mentse**.

   >[!TIP]
   >A tár felügyelt identitásának kiválasztásához írja be a mentési tároló nevét.

   ![Lemez pillanatkép-közreműködői szerepkör hozzáadása](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Ellenőrizze, hogy a biztonságimásolat-tároló felügyelt identitása rendelkezik-e a megfelelő szerepkör-hozzárendelésekkel a forrás lemezen és az erőforráscsoporthoz, amely pillanatfelvétel-adattárként szolgál.

   1. Lépjen a **Backup-tároló – > identitás** elemre, és válassza az **Azure szerepkör-hozzárendelések** lehetőséget.

      ![Azure-beli szerepkör-hozzárendelések kiválasztása](./media/backup-managed-disks/azure-role-assignments.png)

   1. Ellenőrizze, hogy a szerepkör, az erőforrás neve és az erőforrástípus megfelelően tükröződik-e.

      ![A szerepkör, az erőforrás neve és az erőforrás típusának ellenőrzése](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >Noha a szerepkör-hozzárendelések helyesen jelennek meg a portálon, körülbelül 15 percet vesz igénybe, hogy az engedély a biztonsági mentési tár felügyelt identitására legyen alkalmazva.

1. Ha az előfeltételek teljesülnek, ugorjon a **Backup-tároló – > áttekintés** lehetőségre, és válassza a **biztonsági** mentés lehetőséget a lemezek biztonsági mentésének megkezdéséhez.

   ![Biztonsági mentés kiválasztása](./media/backup-managed-disks/select-backup.png)

1. Az **alapvető beállítások** lapon válassza az **Azure Disk** lehetőséget az adatforrás típusaként.

   ![Azure-lemez kiválasztása](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >A Azure Backup a felügyelt lemezek [növekményes pillanatképeit](../virtual-machines/disks-incremental-snapshots.md#restrictions) használja, amelyek csak a lemez változásait tárolják a lemezen a standard HDD tároló utolsó pillanatképe óta, a szülő lemez tárolási típusától függetlenül. A további megbízhatóság érdekében a növekményes Pillanatképek tárolása a zóna redundáns tárterületén (ZRS) történik, alapértelmezés szerint a ZRS támogató régiókban. Az Azure Disk Backup jelenleg támogatja a felügyelt lemezek működési biztonsági mentését, amely nem másolja át a biztonsági másolatokat a Backup-tárolóba. Így a biztonságimásolat-tároló redundancia-beállítása nem vonatkozik a helyreállítási pontokra.

1. A **biztonsági mentési szabályzat** lapon válassza ki a biztonsági mentési szabályzatot.

   ![Biztonsági mentési házirend kiválasztása](./media/backup-managed-disks/choose-backup-policy.png)

1. Az **erőforrások** lapon válassza az **Azure-lemez kiválasztása** lehetőséget. A jobb oldali kontextus ablaktáblán válassza ki a biztonsági mentésre kijelölt lemezeket.

   ![Lemezek kiválasztása a biztonsági mentéshez](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >Míg a portál lehetővé teszi több lemez kijelölését és a biztonsági mentés konfigurálását, az egyes lemezek egyedi biztonsági mentési példányok. Az Azure Disk Backup jelenleg csak az egyes lemezek biztonsági mentését támogatja. A virtuális lemezhez csatolt több lemez időponthoz tartozó biztonsági mentése nem támogatott.
   >
   >Ha a portált használja, az azonos előfizetésben lévő lemezek kiválasztására van korlátozva. Ha több lemezről készít biztonsági mentést, vagy ha a lemezek különböző előfizetések között oszlanak el, a parancsfájlok segítségével automatizálhatja a parancsfájlokat.
   >
   >Az Azure Disk Backup-régiók rendelkezésre állásával, a támogatott forgatókönyvekkel és korlátozásokkal kapcsolatos további információkért tekintse meg a [támogatási mátrixot](disk-backup-support-matrix.md).

1. Válasszon ki egy **Pillanatkép-erőforráscsoportot** , és válassza az **Érvényesítés** lehetőséget. Ez az az erőforráscsoport, amelyben a Azure Backup szolgáltatás létrehozta és felügyeli a mentési tároló növekményes pillanatképeit. A felügyelt identitás a szükséges szerepkör-engedélyekkel van társítva az előfeltételek részeként.

   ![Pillanatkép-erőforráscsoport kiválasztása](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >A biztonsági mentési munkafolyamat konfigurálásának befejezése előtt néhány percet is igénybe vehet. Az érvényesítés sikertelen lehet, ha:
   >
   > - a lemez nem támogatott. A nem támogatott forgatókönyvekhez tekintse meg a [támogatási mátrixot](disk-backup-support-matrix.md) .
   > - a biztonsági mentési tár felügyelt identitása nem rendelkezik olyan érvényes szerepkör-hozzárendelésekkel a **lemezen** , amelyekről biztonsági másolatot szeretne készíteni, vagy a **Pillanatkép-erőforráscsoporthoz** , ahol a növekményes Pillanatképek tárolódnak.

1. Sikeres ellenőrzés után válassza a **felülvizsgálat és konfigurálás** lehetőséget a kiválasztott lemezek biztonsági mentésének konfigurálásához.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentések futtatása

1. Az előző lépésben létrehozott *DemoVault* **Backup-tárolóban** lépjen a **biztonsági mentési** példányok elemre, és válasszon ki egy biztonsági mentési példányt.

   ![Biztonsági mentési példány kiválasztása](./media/backup-managed-disks/select-backup-instance.png)

1. A **példányok biztonsági mentése** képernyőn a következőket találja:

   - **alapvető** információk, például a forrásoldali lemez neve, a pillanatkép-erőforráscsoport, ahol a növekményes Pillanatképek tárolódnak, a Backup-tároló és a biztonsági mentési szabályzat.
   - A **feladatok állapota** a biztonsági mentési és visszaállítási műveletek összegzését, valamint azok állapotát mutatja az elmúlt hét napban.
   - A kijelölt időszakra vonatkozó **visszaállítási pontok** listája.

1. Az igény szerinti biztonsági mentés elindításához válassza a **biztonsági mentés** lehetőséget.

   ![Biztonsági mentés kiválasztása](./media/backup-managed-disks/backup-now.png)

1. Válassza ki a biztonsági mentési szabályzathoz társított megőrzési szabályok egyikét. Ez az adatmegőrzési szabály meghatározza az igény szerinti biztonsági mentés megőrzési időtartamát. A biztonsági mentés elindításához válassza a **biztonsági mentés** lehetőséget.

   ![Biztonsági mentés kezdeményezése](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Biztonsági mentési művelet nyomon követése

A Azure Backup szolgáltatás létrehoz egy feladatot az ütemezett biztonsági mentésekhez, vagy ha az igény szerinti biztonsági mentési műveletet indítja nyomon. A biztonsági mentési feladatok állapotának megtekintése:

1. Lépjen a **biztonsági mentési példány** képernyőjére. Megjeleníti a feladatok irányítópultot az elmúlt hét nap műveletével és állapotával.

   ![Feladatok irányítópult](./media/backup-managed-disks/jobs-dashboard.png)

1. A biztonsági mentési művelet állapotának megtekintéséhez válassza az **összes megtekintése** lehetőséget a biztonsági mentési példány aktuális és korábbi feladatainak megjelenítéséhez.

   ![Válassza az összes megtekintése lehetőséget](./media/backup-managed-disks/view-all.png)

1. Tekintse át a biztonsági mentési és visszaállítási feladatok listáját és azok állapotát. Válasszon feladatot a feladatok listájáról a feladat részleteinek megtekintéséhez.

   ![A részletek megtekintéséhez válassza a feladatok lehetőséget.](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Következő lépések

- [Azure-Managed Disks visszaállítása](restore-managed-disks.md)