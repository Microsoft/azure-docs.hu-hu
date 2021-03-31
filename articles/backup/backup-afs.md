---
title: Azure-fájlmegosztás biztonsági mentése a Azure Portal
description: Ismerje meg, hogyan használhatja a Azure Portal az Azure-fájlmegosztás biztonsági mentésére az Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: ca49f1ad48ab0534b27b91ad6a5a50b393cda782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88890348"
---
# <a name="back-up-azure-file-shares"></a>Azure-fájlmegosztások biztonsági mentése

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni az [Azure-fájlmegosztás](../storage/files/storage-files-introduction.md) Azure Portalról.

Ebből a cikkből megtudhatja, hogyan:

* Recovery Services-tároló létrehozása.
* A biztonsági mentés konfigurálása a Recovery Services-tárolóból
* A biztonsági mentés konfigurálása a fájlmegosztás ablaktábláról
* Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához

## <a name="prerequisites"></a>Előfeltételek

* [Ismerje meg](azure-file-share-backup-overview.md) az Azure file share pillanatkép-alapú biztonsági mentési megoldását.
* Győződjön meg arról, hogy a fájlmegosztás szerepel a [támogatott Storage-fiókok](azure-file-share-support-matrix.md)egyikében.
* Azonosítsa vagy hozzon létre egy [Recovery Services](#create-a-recovery-services-vault) tárolót ugyanabban a régióban, mint a fájlmegosztást futtató Storage-fiók.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>A biztonsági mentés konfigurálása a Recovery Services-tárolóból

A következő lépések bemutatják, hogyan konfigurálhatja a biztonsági mentést több fájlmegosztás számára a Recovery Services tároló ablaktáblán:

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használni kívánt Recovery Services-tárolót.

1. A **Recovery Services** -tároló ablaktáblán kattintson a felül található menü **+ biztonsági mentés** elemére.

   ![Recovery Services-tároló](./media/backup-afs/recovery-services-vault.png)

    1. A **biztonsági mentés célja** panelen állítsa be, hogy **hol fut** a számítási feladat? **a legördülő** listából válassza az **Azure** lehetőséget.

          ![Az Azure kiválasztása munkaterhelésként](./media/backup-afs/backup-goal.png)

    2. A **Miről szeretne biztonsági másolatot készíteni?** területen válassza ki az **Azure-fájlmegosztás** elemet a legördülő listából.

          ![Azure-fájlmegosztás kiválasztása](./media/backup-afs/select-azure-file-share.png)

    3. Válassza a biztonsági mentés lehetőséget, hogy regisztrálja az Azure file share bővítményt a **tárolóban** .

          ![Válassza a biztonsági mentés lehetőséget az Azure-fájlmegosztás tárolóval való hozzárendeléséhez](./media/backup-afs/register-extension.png)

1. Miután kiválasztotta a **biztonsági mentést**, megnyílik a **biztonsági mentés** panel. A védelemmel ellátni kívánt fájlmegosztás tárolására szolgáló Storage-fiók kiválasztásához válassza a hivatkozás szövege a **Storage-fiók** szövegmező **alatt lehetőséget.**

   ![Válassza a kiválasztás hivatkozást](./media/backup-afs/choose-select-link.png)

1. A jobb oldalon megnyílik a **Storage-fiók kiválasztása panel** , amely a felderített támogatott Storage-fiókok listáját tartalmazza. Ezek a tárolóhoz vannak társítva, vagy ugyanabban a régióban találhatók, mint a tároló, de még nincs hozzárendelve egyetlen Recovery Services-tárolóhoz sem.

1. A felderített Storage-fiókok listájából válasszon ki egy fiókot, majd kattintson **az OK gombra**.

   ![Válasszon a felderített Storage-fiókok közül](./media/backup-afs/select-discovered-storage-account.png)

1. A következő lépés azon fájlmegosztás kiválasztása, amelyekről biztonsági másolatot szeretne készíteni. Válassza a **Hozzáadás** gombot a **állnak a biztonsági mentés** szakaszban.

   ![Válassza ki a biztonsági mentéshez használandó fájlmegosztást](./media/backup-afs/select-file-shares-to-back-up.png)

1. A jobb oldalon megnyílik a **fájlmegosztás helyi kiválasztása** panel. Az Azure olyan fájlmegosztás esetén keresi a Storage-fiókot, amelyekről biztonsági másolatot lehet készíteni. Ha nemrég adta hozzá a fájlmegosztást, és nem látja őket a listában, hagyjon némi időt a fájlmegosztás megjelenítésére.

1. A **fájlmegosztás kiválasztása** listából válasszon ki egy vagy több olyan fájlmegosztást, amelyről biztonsági másolatot szeretne készíteni. Válassza az **OK** lehetőséget.

   ![Fájlmegosztás kiválasztása](./media/backup-afs/select-file-shares.png)

1. A fájlmegosztás biztonsági mentési szabályzatának kiválasztásához három lehetőség közül választhat:

   * Válassza ki az alapértelmezett házirendet.<br>
   Ezzel a beállítással engedélyezheti a napi biztonsági mentést, amely 30 napig megőrzi a rendszerét. Ha nem rendelkezik meglévő biztonsági mentési házirenddel a tárolóban, megnyílik a Backup (biztonsági mentés) ablaktábla az alapértelmezett házirend-beállításokkal. Ha ki szeretné választani az alapértelmezett beállításokat, közvetlenül is kiválaszthatja a **biztonsági mentés engedélyezése** lehetőséget.

   * Új szabályzat létrehozása <br>

      1. Ha új biztonsági mentési szabályzatot szeretne létrehozni a fájlmegosztás számára, a **biztonsági mentési szabályzat** szakaszban válassza a legördülő lista alatti hivatkozás szövegét.<br>

         ![Új szabályzat létrehozása](./media/backup-afs/create-new-policy.png)

      1. A jobb oldalon megnyílik a **biztonsági mentési házirend** környezeti panelje. Adja meg a szabályzat nevét a szövegmezőben, és válassza ki a megőrzési időszakot a követelménynek megfelelően. Alapértelmezés szerint csak a napi megőrzési beállítás engedélyezett. Ha hetente, havonta vagy évenkénti megőrzést szeretne, jelölje be a megfelelő jelölőnégyzetet, és adja meg a kívánt megőrzési értéket.

      1. A megőrzési értékek és az érvényes házirend nevének megadása után kattintson **az OK gombra**.<br>

         ![Adja meg a szabályzat nevét és a megőrzési értékeket](./media/backup-afs/policy-name.png)

   * Válasszon egy meglévő biztonsági mentési szabályzatot <br>

      Ha az egyik meglévő biztonsági mentési szabályzatot szeretné kiválasztani a védelem konfigurálásához, válassza ki a kívánt szabályzatot a **biztonsági mentési** szabályzat legördülő listából.<br>

      ![Meglévő házirend kiválasztása](./media/backup-afs/choose-existing-policy.png)

1. Válassza a **biztonsági mentés engedélyezése** lehetőséget a fájlmegosztás védelmének megkezdéséhez.

   ![Válassza a biztonsági mentés engedélyezése lehetőséget.](./media/backup-afs/enable-backup.png)

Miután beállította a biztonsági mentési szabályzatot, a rendszer a fájlmegosztás pillanatképét az ütemezett időpontban hozza létre. A helyreállítási pontot a kiválasztott időszakra is megőrzi a rendszer.

>[!NOTE]
>Azure Backup mostantól támogatja az Azure-fájlmegosztás biztonsági mentésének napi/heti/havi/éves megőrzését biztosító szabályzatokat.

## <a name="configure-backup-from-the-file-share-pane"></a>A biztonsági mentés konfigurálása a fájlmegosztás ablaktábláról

A következő lépések bemutatják, hogyan konfigurálhatja a biztonsági mentést az egyes fájlmegosztás számára a megfelelő fájlmegosztás ablaktáblán:

1. A [Azure Portal](https://portal.azure.com/)nyissa meg azt a Storage-fiókot, amely a biztonsági mentésre használni kívánt fájlmegosztást üzemelteti.

1. A Storage-fiókban válassza ki a **fájlmegosztás** feliratú csempét. A **fájlmegosztást** a Storage-fiókhoz tartozó tartalomjegyzék használatával is megnyithatja.

   ![Tárfiók](./media/backup-afs/storage-account.png)

1. A fájlmegosztás listájában látnia kell a Storage-fiókban található összes fájlmegosztást. Válassza ki azt a fájlmegosztást, amelyről biztonsági másolatot szeretne készíteni.

   ![Fájlmegosztás listája](./media/backup-afs/file-shares-list.png)

1. A fájlmegosztás ablaktábla **műveletek** területén válassza a **biztonsági mentés** lehetőséget. A **biztonsági mentés konfigurálása** panel a jobb oldalon fog betöltődik.

   ![A biztonsági mentés ablaktábla konfigurálása](./media/backup-afs/configure-backup.png)

1. A Recovery Services-tároló kiválasztásához tegye a következők egyikét:

    * Ha már rendelkezik tárolóval, válassza a **meglévő** Recovery Services-tároló választógombot, és válasszon ki egy meglévő tárat a tár **neve** legördülő menüből.

       ![Meglévő tár kiválasztása](./media/backup-afs/select-existing-vault.png)

    * Ha nem rendelkezik tárolóval, válassza az új Recovery Services-tároló **létrehozása** választógombot. Adja meg a tároló nevét. Ugyanabban a régióban jön létre, mint a fájlmegosztás. Alapértelmezés szerint a tároló ugyanabban az erőforráscsoportban jön létre, mint a fájlmegosztás. Ha másik erőforráscsoportot szeretne választani, válassza az **új hivatkozás létrehozása** az **erőforrástípus** legördülő lista alatt lehetőséget, és adja meg az erőforráscsoport nevét. A folytatáshoz kattintson az **OK** gombra.

       ![Új tár létrehozása](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Ha a Storage-fiók regisztrálva van egy tárolóban, vagy a védeni kívánt fájlmegosztást tartalmazó Storage-fiókon belül néhány védett megosztás található, akkor a Recovery Services tár neve előre ki lesz töltve, és itt nem szerkeszthető [További információ](backup-azure-files-faq.md#why-cant-i-change-the-vault-to-configure-backup-for-the-file-share).

1. A **biztonsági mentési szabályzat** kiválasztásához hajtsa végre az alábbi műveletek egyikét:

    * Hagyja meg az alapértelmezett házirendet. A napi biztonsági mentések a 30 napos megőrzéssel lesznek ütemezve.

    * Válasszon ki egy meglévő biztonsági mentési szabályzatot, ha van ilyen, a **biztonsági mentési szabályzat** legördülő menüből.

       ![Biztonsági mentési házirend kiválasztása](./media/backup-afs/choose-backup-policy.png)

    * Hozzon létre egy új szabályzatot napi/heti/havi/éves megőrzéssel a követelménynek megfelelően.  

         1. Válassza az **új házirend létrehozása** hivatkozás szövegét.

         2. A jobb oldalon megnyílik a **biztonsági mentési házirend** környezeti panelje. Adja meg a szabályzat nevét a szövegmezőben, és válassza ki a megőrzési időszakot a követelménynek megfelelően. Alapértelmezés szerint csak a napi megőrzési beállítás engedélyezett. Ha hetente, havonta vagy évenkénti megőrzést szeretne, jelölje be a megfelelő jelölőnégyzetet, és adja meg a kívánt megőrzési értéket.

         3. A megőrzési értékek és az érvényes házirend nevének megadása után kattintson **az OK gombra**.

            ![Új biztonsági mentési szabályzat létrehozása](./media/backup-afs/create-new-backup-policy.png)

1. Válassza a **biztonsági mentés engedélyezése** lehetőséget a fájlmegosztás védelmének megkezdéséhez.

   ![Válassza a biztonsági mentés engedélyezése lehetőséget.](./media/backup-afs/select-enable-backup.png)

1. Nyomon követheti a konfigurációs előrehaladást a portál értesítéseiben, vagy figyelheti a biztonsági mentési feladatokat azon tárolóban, amelyet a fájlmegosztás biztosításához használ.

   ![Portál értesítései](./media/backup-afs/portal-notifications.png)

1. A biztonsági mentés konfigurálása művelet befejezése után válassza a **biztonsági mentés** lehetőséget a fájlmegosztás ablaktábla **műveletek** szakaszában. A tároló **alapjai** a helyi ablaktáblán lesznek betöltve. Itt aktiválhatja az igény szerinti biztonsági mentési és visszaállítási műveleteket.

   ![Tár alapjai](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Igény szerinti biztonsági mentési feladatok futtatása

Időnként előfordulhat, hogy biztonsági mentési pillanatképet vagy helyreállítási pontot szeretne készíteni a biztonsági mentési házirendben ütemezett időpontokon kívül. Az igény szerinti biztonsági mentés gyakori oka, hogy a biztonsági mentési szabályzatot már konfigurálta. A biztonsági mentési szabályzatban foglalt ütemezés alapján a pillanatkép elkészítése előtt óra vagy nap lehet. Annak érdekében, hogy adatai a biztonsági mentési szabályzat elindulásáig is védve legyenek, indítson el egy igény szerinti biztonsági mentést. Szükség van egy igény szerinti biztonsági mentés létrehozására, mielőtt tervezett változtatásokat hajt végre a fájlmegosztás számára.

### <a name="from-the-recovery-services-vault"></a>A Recovery Services-tárból

1. Nyissa meg a fájlmegosztás biztonsági mentésére használt Recovery Services-tárolót. Az **Áttekintés** panelen válassza a **védett elemek** alatt található **biztonsági másolati elemek** lehetőséget.

   ![Biztonsági másolati elemek kiválasztása](./media/backup-afs/backup-items.png)

1. Miután kiválasztotta a **biztonsági másolati elemeket**, megjelenik egy új ablaktábla, amely felsorolja az összes **biztonsági mentési felügyeleti típust** az **Áttekintés** ablaktábla mellett.

   ![A biztonságimásolat-felügyeleti típusok listája](./media/backup-afs/backup-management-types.png)

1. A **biztonságimásolat-kezelés típusa** listából válassza az **Azure Storage (Azure Files)** lehetőséget. Ekkor megjelenik az összes fájlmegosztás és a hozzájuk tartozó megfelelő tárolási fiókok listája a tároló használatával.

   ![Azure Storage-(Azure Files-) biztonsági másolati elemek](./media/backup-afs/azure-files-backup-items.png)

1. Az Azure-fájlmegosztás listájából válassza ki a kívánt fájlmegosztást. Megjelenik a **biztonsági mentési elemek** részletei. A **biztonsági mentési elem** menüben válassza a **biztonsági mentés** lehetőséget. Mivel ez a biztonsági mentési feladatok igény szerint vannak, a helyreállítási ponthoz nincs adatmegőrzési szabály társítva.

   ![Biztonsági mentés kiválasztása](./media/backup-afs/backup-now.png)

1. Megnyílik a **biztonsági mentés** panel. Adja meg a helyreállítási pont megőrzésének utolsó napját. Igény szerinti biztonsági mentés esetén 10 évig is megtarthatja a maximumot.

   ![Megőrzési dátum kiválasztása](./media/backup-afs/retention-date.png)

1. Az **OK** gombra kattintva erősítse meg a-t futtató igény szerinti biztonsági mentési feladatot.

1. A portál értesítéseinek figyelésével nyomon követheti a biztonsági mentési feladatok futtatásának befejezését. A feladatok előrehaladását a tároló irányítópultján követheti nyomon. Válassza   >  **a folyamatban lévő** biztonsági mentési feladatok lehetőséget.

### <a name="from-the-file-share-pane"></a>A fájlmegosztás panelen

1. Nyissa meg a fájlmegosztás **Áttekintés** ablaktábláját, amelyhez igény szerinti biztonsági mentést szeretne készíteni.

1. A **művelet** szakaszban válassza a **biztonsági mentés** lehetőséget. A tároló **alapjai** a helyi ablaktáblán lesznek betöltve. Az igény szerinti biztonsági mentéshez válassza a **biztonsági mentés** lehetőséget.

   ![Biztonsági mentés kiválasztása](./media/backup-afs/select-backup-now.png)

1. Megnyílik a **biztonsági mentés** panel. A helyreállítási pont megőrzésének megadása. Igény szerinti biztonsági mentés esetén 10 évig is megtarthatja a maximumot.

   ![Biztonsági mentés dátumának megőrzése](./media/backup-afs/retain-backup-date.png)

1. Válassza az **OK** lehetőséget a megerősítéshez.

>[!NOTE]
>Azure Backup zárolja a Storage-fiókot, amikor a megfelelő fiókban lévő fájlmegosztás védelmét konfigurálja. Ez védelmet biztosít egy olyan Storage-fiók véletlen törlésével szemben, amely biztonsági másolatokat tartalmazó fájlmegosztást tartalmaz.

## <a name="best-practices"></a>Ajánlott eljárások

* Ne töröljön Azure Backup által létrehozott pillanatképeket. A pillanatképek törlése helyreállítási pontok elvesztését és/vagy visszaállítási hibákat eredményezhet.

* Ne távolítsa el Azure Backup a Storage-fiók zárolását. Ha törli a zárolást, a Storage-fiók a véletlen törléssel lesz kitéve, és ha törölve van, elveszíti a pillanatképeket vagy a biztonsági mentéseket.

## <a name="next-steps"></a>Következő lépések

Az alábbiak végrehajtásának módját ismerheti meg:

* [Azure-fájlmegosztás visszaállítása](restore-afs.md)
* [Azure-fájlmegosztás biztonsági másolatainak kezelése](manage-afs-backup.md)
