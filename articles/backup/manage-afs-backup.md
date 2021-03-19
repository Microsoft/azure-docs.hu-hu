---
title: Azure-fájlmegosztások biztonsági másolatainak kezelése
description: Ez a cikk a Azure Backup által biztonsági mentés alatt álló Azure-fájlmegosztás felügyeletének és figyelésének általános feladatait ismerteti.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 784b22f11b57fb025f6d9401e10d527c83751898
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88757625"
---
# <a name="manage-azure-file-share-backups"></a>Azure-fájlmegosztások biztonsági másolatainak kezelése

Ez a cikk a [Azure Backup](./backup-overview.md)által biztonsági mentés alatt álló Azure-fájlmegosztás felügyeletének és figyelésének általános feladatait ismerteti. Megtudhatja, hogyan végezheti el a felügyeleti feladatokat a Recovery Services-tárolóban.

## <a name="monitor-jobs"></a>Feladatok figyelése

A biztonsági mentési vagy visszaállítási művelet elindításakor a Backup szolgáltatás létrehoz egy feladatot a követéshez. A **Biztonsági mentési feladatok** lapon monitorozhatja az összes feladat előrehaladását.

A **Biztonsági mentési feladatok** lapot a következő módon nyithatja meg:

1. Nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használt Recovery Services-tárolót. Az **Áttekintés** panelen válassza a **figyelés** szakaszban található **biztonsági mentési feladatok** elemet.

   ![Biztonsági mentési feladatok a figyelés szakaszban](./media/manage-afs-backup/backup-jobs.png)

1. Miután kiválasztotta az **OK gombot**, a **biztonsági mentési feladatok** ablaktábla felsorolja az összes feladat állapotát. Válassza ki a megfigyelni kívánt fájlmegosztás számára megfelelő munkaterhelés-nevet.

   ![Munkaterhelés neve](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Új szabályzat létrehozása

Létrehozhat egy új szabályzatot az Azure-fájlmegosztás biztonsági mentéséhez a Recovery Services- **tároló biztonsági mentési házirendek** szakaszának használatával. A fájlmegosztás biztonsági mentésének konfigurálásakor létrehozott szabályzatok az **Azure**-fájlmegosztás **mezőben** jelennek meg.

A meglévő biztonsági mentési szabályzatok megtekintéséhez:

1. Nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használt Recovery Services-tárolót. A Recovery Services-tároló menüben válassza a **kezelés** szakasz **biztonsági mentési házirendek** elemét. Megjelenik a tárolóban konfigurált összes biztonsági mentési szabályzat.

   ![Minden biztonsági mentési szabályzat](./media/manage-afs-backup/all-backup-policies.png)

1. Az **Azure-fájlmegosztás** egyedi házirendjeinek megtekintéséhez válassza az **Azure-fájlmegosztás** lehetőséget a jobb felső sarokban lévő legördülő listából.

   ![Azure-fájlmegosztás kiválasztása](./media/manage-afs-backup/azure-file-share.png)

Új biztonsági mentési szabályzat létrehozásához:

1. A **biztonsági mentési házirendek** panelen válassza a **+ Hozzáadás** lehetőséget.

   ![Új biztonsági mentési szabályzat](./media/manage-afs-backup/new-backup-policy.png)

1. A **Hozzáadás** ablaktáblán válassza ki a **házirend típusaként** az **Azure-fájlmegosztás** lehetőséget. Megnyílik az **Azure-fájlmegosztás** **biztonsági mentési szabályzat** panelje. Adja meg a házirend nevét, a biztonsági mentési gyakoriságot és a helyreállítási pontok megőrzési tartományát. A szabályzat meghatározása után kattintson **az OK gombra**.

   ![A biztonsági mentési szabályzat megadása](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Házirend módosítása

A biztonsági mentési szabályzat módosításával módosíthatja a biztonsági mentési gyakoriságot vagy a megőrzési tartományt.

Szabályzat módosítása:

1. Nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használt Recovery Services-tárolót. A Recovery Services-tároló menüben válassza a **kezelés** szakasz **biztonsági mentési házirendek** elemét. Megjelenik a tárolóban konfigurált összes biztonsági mentési szabályzat.

   ![A tár összes biztonsági mentési szabályzata](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Az Azure-fájlmegosztás specifikus házirendjeinek megtekintéséhez válassza az **Azure-fájlmegosztás** lehetőséget a jobb felső sarokban lévő legördülő listából. Válassza ki a módosítani kívánt biztonsági mentési szabályzatot.

   ![A módosítandó Azure-fájlmegosztás](./media/manage-afs-backup/azure-file-share-modify.png)

1. Megnyílik az **ütemterv** ablaktábla. Szükség szerint szerkessze a **biztonsági mentés ütemtervét** és a **megőrzési tartományt** , majd kattintson a **Mentés** gombra. Megjelenik egy "frissítés folyamatban" üzenet a panelen. Miután a szabályzat sikeresen módosult, a következő üzenet jelenik meg: "a biztonsági mentési házirend frissítése sikeresen megtörtént."

   ![A módosított szabályzat mentése](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének leállítása

Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és *törölje az összes helyreállítási pontot*.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, de *hagyja meg a helyreállítási pontokat*.

Előfordulhat, hogy a tárolóban lévő helyreállítási pontok elhagyása után a rendszer megőrzi a Azure Backup által létrehozott mögöttes pillanatképeket. A helyreállítási pontok elhagyása után a fájlmegosztás később is visszaállítható. A helyreállítási pontok elhagyása költségével kapcsolatos további információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/backup/). Ha úgy dönt, hogy törli az összes helyreállítási pontot, a fájlmegosztás nem állítható vissza.

Azure-fájlmegosztás védelmének megszüntetése:

1. Nyissa meg a fájlmegosztási helyreállítási pontokat tartalmazó Recovery Services-tárolót. A **védett elemek** szakaszban válassza a **biztonsági másolati elemek** lehetőséget. Megjelenik a biztonsági mentési elemtípus-típusok listája.

   ![Biztonsági másolati elemei](./media/manage-afs-backup/backup-items.png)

1. A **Biztonságimásolat-kezelés típusa** listából válassza ki az **Azure Storage (Azure Files)** lehetőséget. Megjelenik a **biztonsági mentési elemek (Azure Storage (Azure Files)** ) lista.

   ![Azure Storage kiválasztása (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. A **biztonsági mentési elemek (Azure Storage (Azure Files))** listán válassza ki azt a biztonsági mentési elemet, amelynek a védelmét le szeretné állítani.

1. Válassza a **biztonsági mentés leállítása** lehetőséget.

   ![Válassza a biztonsági mentés leállítása lehetőséget.](./media/manage-afs-backup/stop-backup.png)

1. A **biztonsági mentés leállítása** ablaktáblán válassza a **biztonsági mentési adat megőrzése** vagy a **biztonsági másolati adat törlése** lehetőséget. Ezután válassza a **biztonsági mentés leállítása** lehetőséget.

    ![Válassza a biztonsági mentési adat megőrzése vagy a biztonsági másolati adat törlése lehetőséget.](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének folytatása

Ha a **biztonsági mentési adat megőrzése** beállítás be lett jelölve, amikor a fájlmegosztás védelmét leállították, akkor lehetséges a védelem folytatása. Ha a **biztonsági mentési adattörlés** lehetőséget választotta, a fájlmegosztás védelme nem folytatható.

Az Azure-fájlmegosztás védelmének folytatása:

1. Nyissa meg a fájlmegosztási helyreállítási pontokat tartalmazó Recovery Services-tárolót. A **védett elemek** szakaszban válassza a **biztonsági másolati elemek** lehetőséget. Megjelenik a biztonsági mentési elemtípus-típusok listája.

   ![A folytatáshoz tartozó biztonsági mentési elemek](./media/manage-afs-backup/backup-items-resume.png)

1. A **Biztonságimásolat-kezelés típusa** listából válassza ki az **Azure Storage (Azure Files)** lehetőséget. Megjelenik a **biztonsági mentési elemek (Azure Storage (Azure Files)** ) lista.

   ![Az Azure Storage (Azure Files) listája](./media/manage-afs-backup/azure-storage-azure-files.png)

1. A **biztonsági mentési elemek (Azure Storage (Azure Files))** listán válassza ki azt a biztonsági mentési elemet, amelynek a védelmét folytatni kívánja.

1. Válassza a **biztonsági mentés folytatása** lehetőséget.

   ![Válassza a biztonsági mentés folytatása lehetőséget.](./media/manage-afs-backup/resume-backup.png)

1. Megnyílik a **biztonsági mentési házirend** panel. Válassza ki a kívánt szabályzatot a biztonsági mentés folytatásához.

1. Miután kiválasztott egy biztonsági mentési szabályzatot, válassza a **Mentés** lehetőséget. Egy "frissítés folyamatban" üzenet jelenik meg a portálon. A biztonsági mentés sikeres folytatása után a következő üzenet jelenik meg: "a védett Azure-fájlmegosztás biztonsági mentési szabályzatának frissítése sikerült."

   ![A biztonsági mentési szabályzat frissítése sikerült](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

A fájlmegosztás biztonsági mentését a **biztonsági mentés leállítása** feladatokban vagy a védelem leállítása után bármikor törölheti. A helyreállítási pontok törlése előtt hasznos lehet napokat vagy akár heteket várni. A biztonsági mentési információk törlésekor nem választhat meghatározott helyreállítási pontokat a törléshez. Ha úgy dönt, hogy törli a biztonsági mentési adatait, akkor a fájlmegosztást társított összes helyreállítási pontot törölni kell.

A következő eljárás azt feltételezi, hogy a fájlmegosztás védelme leállt.

Az Azure-fájlmegosztás biztonsági mentési adatfájljainak törlése:

1. A biztonsági mentési feladatok leállítása után a biztonsági mentés **folytatása** és a **biztonsági mentési adatok törlése** lehetőség a biztonsági mentési **elem** irányítópultján érhető el. Válassza az **adatbiztonsági másolat törlése** lehetőséget.

   ![Biztonsági mentési adatok törlése](./media/manage-afs-backup/delete-backup-data.png)

1. Megnyílik a **biztonsági mentési adattörlés** panel. A törlés megerősítéséhez írja be a fájlmegosztás nevét. Szükség esetén további információkat is megadhat az **OK** vagy **Megjegyzések** mezőkben. Ha biztos benne, hogy törli a biztonsági másolatok adatait, válassza a **Törlés** lehetőséget.

   ![Az adattörlés megerősítése](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Storage-fiók regisztrációjának törlése

Ha egy adott Storage-fiókban lévő fájlmegosztást egy másik Recovery Services-tárolóval szeretne védelemmel ellátni, először állítsa le a Storage-fiókban lévő [összes fájlmegosztás védelmét](#stop-protection-on-a-file-share) . Ezután törölje a fiók regisztrációját a védelemhez használt aktuális Recovery Services-tárból.

Az alábbi eljárás azt feltételezi, hogy a védelem le lett állítva az összes olyan fájlmegosztás esetében, amelyről törölni szeretné a regisztrációt.

A Storage-fiók regisztrációjának törlése:

1. Nyissa meg azt a Recovery Services tárolót, ahol a Storage-fiók regisztrálva van.
1. Az **Áttekintés** panelen válassza a **biztonsági mentési infrastruktúra** lehetőséget a **kezelés** szakaszban.

   ![Biztonsági mentési infrastruktúra kiválasztása](./media/manage-afs-backup/backup-infrastructure.png)

1. Megnyílik a **biztonsági mentési infrastruktúra** panel. Válassza a **Storage-fiókok** lehetőséget az **Azure Storage-fiókok** szakaszban.

   ![Storage-fiókok kiválasztása](./media/manage-afs-backup/storage-accounts.png)

1. A Storage- **fiókok** kiválasztása után megjelenik a tárolóban regisztrált Storage-fiókok listája.
1. Kattintson a jobb gombbal a törölni kívánt Storage-fiókra, majd válassza a **Regisztráció törlése** lehetőséget.

   ![Regisztráció törlése](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Következő lépések

További információ: az [Azure-fájlmegosztás biztonsági mentésének hibája](./troubleshoot-azure-files.md).
