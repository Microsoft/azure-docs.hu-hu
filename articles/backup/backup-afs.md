---
title: Azure-fájlmegosztások biztonsági mentése a Azure Portal
description: Megtudhatja, hogyan használhatja a Azure Portal az Azure-fájlmegosztások biztonsági mentése a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: e7f44a71388468be432bdfcb0eb2bf67c0fcc8ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519935"
---
# <a name="back-up-azure-file-shares"></a>Azure-fájlmegosztások biztonsági mentése

Ez a cikk az Azure-fájlmegosztások biztonsági mentése [a](../storage/files/storage-files-introduction.md) Azure Portal.

Ebből a cikkből megtudhatja, hogyan:

* Recovery Services-tároló létrehozása.
* Biztonsági mentés konfigurálása a Recovery Services-tárolóból
* Biztonsági mentés konfigurálása a fájlmegosztás panelről
* Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához

## <a name="prerequisites"></a>Előfeltételek

* [Ismerje](azure-file-share-backup-overview.md) meg az Azure-fájlmegosztás pillanatkép-alapú biztonsági mentési megoldását.
* Győződjön meg arról, hogy a fájlmegosztás a támogatott tárfióktípusok [egyikében található.](azure-file-share-support-matrix.md)
* Azonosítsa vagy hozza létre a [Recovery Services-tárolót](#create-a-recovery-services-vault) ugyanabban a régióban, mint a fájlmegosztást tároló tárfiók.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Biztonsági mentés konfigurálása a Recovery Services-tárolóból

Az alábbi lépések azt ismertetik, hogyan konfigurálhatja több fájlmegosztás biztonsági mentését a Helyreállítási tár panelről:

1. A [Azure Portal](https://portal.azure.com/)nyissa meg azt a Recovery Services-tárolót, amely a fájlmegosztás biztonsági mentésének konfigurálásához szükséges.

1. A **Recovery Services-tároló panelen** válassza a **+Biztonsági** mentés lehetőséget a felső menüben.

   ![Recovery Services-tároló](./media/backup-afs/recovery-services-vault.png)

    1. A Biztonsági **mentés célja panelen** állítsa a **Hol futnak** a számítási feladatok? beállítást **Azure-ba** a legördülő listából az **Azure** lehetőség kiválasztásával.

          ![Az Azure kiválasztása számítási feladatként](./media/backup-afs/backup-goal.png)

    2. A **Miről szeretne biztonsági mentése?** listában válassza az **Azure-fájlmegosztás** lehetőséget a legördülő listából.

          ![Válassza az Azure FileShare lehetőséget](./media/backup-afs/select-azure-file-share.png)

    3. Az **Azure-fájlmegosztási** bővítmény tárolóban való regisztráláshoz válassza a Biztonsági mentés lehetőséget.

          ![Válassza a Biztonsági mentés lehetőséget az Azure-fájlmegosztás tárolóhoz való társításhoz](./media/backup-afs/register-extension.png)

1. A Biztonsági mentés **kiválasztása után** **megnyílik a Biztonsági** mentés panel. A védeni kívánt fájlmegosztást üzemeltető tárfiók kiválasztásához  válassza a Storage-fiók szövegmező alatti Hivatkozásszöveg **kiválasztása** lehetőséget.

   ![Válassza a Kijelölés hivatkozást](./media/backup-afs/choose-select-link.png)

1. A **jobb oldalon megnyílik** a Tárfiók kiválasztása panel, amely a felderített támogatott tárfiókok listáját sorolja fel. Vagy ehhez a tárolóhoz vannak társítva, vagy ugyanabban a régióban vannak, mint a tároló, de még nincs hozzájuk társítva egyik Recovery Services-tároló sem.

1. A felderített tárfiókok listájából válasszon ki egy fiókot, majd kattintson az **OK gombra.**

   ![Válasszon a felderített tárfiókok közül](./media/backup-afs/select-discovered-storage-account.png)

1. A következő lépés a fájlmegosztások kiválasztása, amelyekről biztonsági mentéset kíván kapni. Válassza a **FájlMegosztások** biztonsági **mentéshez** szakaszban a Hozzáadás gombot.

   ![Válassza ki a biztonságimentni kívánt fájlmegosztásokat](./media/backup-afs/select-file-shares-to-back-up.png)

1. A **Fájlmegosztások kiválasztása helyi** panel a jobb oldalon nyílik meg. Az Azure megkeresi a tárfiókban a biztonsági mentésezhető fájlmegosztásokat. Ha nemrég adott hozzá fájlmegosztásokat, és nem látja őket a listában, hagyjon egy kis időt a fájlmegosztások megjelenik.

1. A **Fájlmegosztások kiválasztása listában** válasszon ki egy vagy több fájlmegosztást, amelyekről biztonsági mentéset szeretne. Válassza az **OK** lehetőséget.

   ![A fájlmegosztások kiválasztása](./media/backup-afs/select-file-shares.png)

1. A fájlmegosztás biztonsági mentési házirendjéhez három lehetőség áll rendelkezésre:

   * Válassza ki az alapértelmezett szabályzatot.<br>
   Ezzel a beállítással engedélyezheti a napi biztonsági mentést, amely 30 napig lesz megőrizve. Ha még nem létezik biztonsági mentési szabályzat a tárolóban, megnyílik a Biztonsági mentés panel az alapértelmezett szabályzatbeállításokkal. Ha az alapértelmezett beállításokat szeretné kiválasztani, válassza közvetlenül a **Biztonsági mentés engedélyezése lehetőséget.**

   * Új szabályzat létrehozása <br>

      1. Ha új biztonsági mentési szabályzatot hoz létre a fájlmegosztáshoz, válassza  a Biztonsági mentési szabályzat szakaszban található legördülő lista alatti hivatkozásszöveget.<br>

         ![Új szabályzat létrehozása](./media/backup-afs/create-new-policy.png)

      1. A **biztonsági mentési szabályzat** helyi panelje a jobb oldalon nyílik meg. Adjon meg egy szabályzatnevet a szövegmezőben, és válassza ki a megőrzési megőrzési időszakot a követelménynek megfelelően. Alapértelmezés szerint csak a napi megőrzési beállítás van engedélyezve. Ha heti, havi vagy éves megőrzési adatokat szeretne, jelölje be a megfelelő jelölőnégyzetet, és adja meg a kívánt megőrzési értéket.

      1. A megőrzési értékek és egy érvényes szabályzatnév megadása után kattintson az **OK gombra.**<br>

         ![Szabályzat nevének és megőrzési értékeinek meg kell adni](./media/backup-afs/policy-name.png)

   * Válasszon egyet a meglévő biztonsági mentési szabályzatok közül <br>

      Ha ki szeretné választani a meglévő biztonsági mentési szabályzatok valamelyikét a védelem konfigurálásához, válassza ki a kívánt házirendet a **Biztonsági** mentési szabályzat legördülő listából.<br>

      ![Meglévő szabályzat kiválasztása](./media/backup-afs/choose-existing-policy.png)

1. Válassza **a Biztonsági mentés engedélyezése** lehetőséget a fájlmegosztás védelembe való bekapcsoláshoz.

   ![Válassza a Biztonsági mentés engedélyezése lehetőséget](./media/backup-afs/enable-backup.png)

A biztonsági mentési szabályzat beállítása után a rendszer pillanatképet készít a fájlmegosztásról az ütemezett időpontban. A helyreállítási pontot a rendszer a választott időszakra is megőrzi.

>[!NOTE]
>Azure Backup mostantól támogatja az Azure-fájlmegosztások biztonsági mentésére vonatkozó napi/heti/havi/éves megőrzési szabályzatokat.

## <a name="configure-backup-from-the-file-share-pane"></a>Biztonsági mentés konfigurálása a fájlmegosztás panelről

A következő lépések azt ismertetik, hogyan konfigurálhatja az egyes fájlmegosztások biztonsági mentését a megfelelő fájlmegosztási panelről:

1. A [Azure Portal](https://portal.azure.com/)nyissa meg azt a fájlmegosztást üzemeltető tárfiókot, amelyről biztonsági mentéset szeretne.

1. A tárfiókban válassza a Fájlmegosztások **címkével jelölt csempét.** A fájlmegosztások **a** tárfiók tartalomjegyzékében is elérhetőek.

   ![Tárfiók](./media/backup-afs/storage-account.png)

1. A fájlmegosztások listában meg kell jelenni a tárfiókban található összes fájlmegosztásnak. Válassza ki a fájlmegosztást, amelyről biztonsági mentéset szeretne.

   ![Fájlmegosztások listája](./media/backup-afs/file-shares-list.png)

1. A **fájlmegosztás** panel **Műveletek** szakaszában válassza a Biztonsági mentés lehetőséget. A **biztonsági mentés konfigurálása** panel a jobb oldalon fog betöltődik.

   ![Biztonsági mentés konfigurálása panel](./media/backup-afs/configure-backup.png)

1. A helyreállítási tár kiválasztásához tegye a következők egyikét:

    * Ha már rendelkezik tárolóval, jelölje be a Meglévő **recovery** services-tároló kiválasztása választógombot, és válasszon egy meglévő tárolót a Tároló neve **legördülő** menüből.

       ![Meglévő tároló kiválasztása](./media/backup-afs/select-existing-vault.png)

    * Ha még nincs tárolója, válassza az **Új** helyreállítási tár létrehozása választógombot. Adja meg a tároló nevét. Ugyanabban a régióban jön létre, mint a fájlmegosztás. Alapértelmezés szerint a tároló ugyanabban az erőforráscsoportban jön létre, mint a fájlmegosztás. Ha másik erőforráscsoportot szeretne választani, válassza az  Erőforrástípus legördülő lista Alatti Új hivatkozás létrehozása lehetőséget, és adja meg az erőforráscsoport nevét.  A folytatáshoz kattintson az **OK** gombra.

       ![Új tároló létrehozása](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Ha a tárfiók regisztrálva van egy tárolóban, vagy ha a védeni kívánt fájlmegosztást üzemeltető tárfiókon belül kevés védett megosztás található, a rendszer [](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-)előre kitölti a Recovery Services-tároló nevét, és nem szerkesztheti. További információ itt.

1. A Biztonsági **mentési házirend beállításnál** tegye a következők egyikét:

    * Hagyja meg az alapértelmezett szabályzatot. Napi biztonsági mentéseket ütemez 30 napos megőrzéssel.

    * Válasszon ki egy meglévő biztonsági mentési szabályzatot a Biztonsági **mentési** szabályzat legördülő menüből, ha van ilyen.

       ![Biztonsági mentési szabályzat kiválasztása](./media/backup-afs/choose-backup-policy.png)

    * Hozzon létre egy új szabályzatot napi/heti/havi/éves megőrzéssel a követelmények szerint.  

         1. Válassza az **Új szabályzat létrehozása hivatkozásszöveget.**

         2. A **biztonsági mentési szabályzat** helyi panelje megnyílik a jobb oldalon. Adjon meg egy szabályzatnevet a szövegmezőben, és válassza ki a megőrzési megőrzési időszakot a követelménynek megfelelően. Alapértelmezés szerint csak a napi megőrzési beállítás van engedélyezve. Ha heti, havi vagy éves megőrzési adatokat szeretne, jelölje be a megfelelő jelölőnégyzetet, és adja meg a kívánt megőrzési értéket.

         3. A megőrzési értékek és egy érvényes szabályzatnév megadása után kattintson az **OK gombra.**

            ![Új biztonsági mentési szabályzat létrehozása](./media/backup-afs/create-new-backup-policy.png)

1. Válassza **a Biztonsági mentés engedélyezése** lehetőséget a fájlmegosztás védelembe való mentéséhez.

   ![Válassza a Biztonsági mentés engedélyezése lehetőséget](./media/backup-afs/select-enable-backup.png)

1. A konfigurációs folyamat nyomon követhető a portál értesítései között, vagy monitorozhatja a biztonsági mentési feladatokat a fájlmegosztás védelméhez használt tárolóban.

   ![Portálértesítések](./media/backup-afs/portal-notifications.png)

1. A biztonsági mentési konfigurálás műveletének befejezése után válassza a **Biztonsági** mentés lehetőséget **a** fájlmegosztás panel Műveletek szakaszában. A Jobb oldalon betöltődik a **Vault Essentials** listában látható helyi panel. Innen elindíthatja az igény szerinti biztonsági mentési és visszaállítási műveleteket.

   ![A tároló alapvető fontosságú](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Igény szerinti biztonsági mentési feladat futtatása

Előfordulhat, hogy biztonsági mentési pillanatképet vagy helyreállítási pontot szeretne létrehozni a biztonsági mentési házirendben ütemezett időponton kívül. Az igény szerinti biztonsági mentések készítésének gyakori oka közvetlenül a biztonsági mentési szabályzat konfigurálása után van. A biztonsági mentési házirend ütemezése alapján akár órákat vagy napokat is igénybehet, amíg a rendszer pillanatképet készít. Annak érdekében, hogy adatai a biztonsági mentési szabályzat elindulásáig is védve legyenek, indítson el egy igény szerinti biztonsági mentést. A fájlmegosztások tervezett módosításaihoz gyakran szükség van igény szerinti biztonsági mentés létrehozására.

### <a name="from-the-recovery-services-vault"></a>A Recovery Services-tárolóból

1. Nyissa meg a helyreállítási tárat, amely a fájlmegosztás biztonsági mentésekor használt. Az Áttekintés **panelen** válassza a Biztonsági **másolati elemek** lehetőséget a **Védett elemek szakaszban.**

   ![Biztonsági mentési elemek kiválasztása](./media/backup-afs/backup-items.png)

1. Miután kiválasztotta a **Biztonsági másolati**  elemek lehetőséget, megjelenik az Áttekintés panel mellett egy új panel, amely felsorolja az összes biztonsági mentési felügyeleti **típust.**

   ![A biztonsági mentések kezelési típusainak listája](./media/backup-afs/backup-management-types.png)

1. A **Biztonságimásolat-kezelés típusa listában** válassza az **Azure Storage (Azure Files) lehetőséget.** Megjelenik az összes fájlmegosztás és a tároló használatával biztonsági mentésekor a megfelelő tárfiókok listája.

   ![Az Azure Storage (Azure Files) biztonsági mentési elemei](./media/backup-afs/azure-files-backup-items.png)

1. Az Azure-fájlmegosztások listájából válassza ki a kívánt fájlmegosztást. Megjelenik **a Biztonságimásolat-elem** részletei. A Biztonsági **mentési elem menüben** válassza a Biztonsági **mentés lehetőséget.** Mivel ez a biztonsági mentési feladat igény szerint van, nincs megőrzési szabályzat társítva a helyreállítási ponthoz.

   ![Válassza a Biztonsági mentés lehetőséget](./media/backup-afs/backup-now.png)

1. Megnyílik **a Biztonsági mentés** panel. Adja meg a helyreállítási pont megőrzésének utolsó napját. Az igény szerinti biztonsági mentések legfeljebb 10 évig megőrzést kaphatnak.

   ![Megőrzési dátum kiválasztása](./media/backup-afs/retention-date.png)

1. Kattintson **az OK** gombra a futó igény szerinti biztonsági mentési feladat megerősítéséhez.

1. A biztonsági mentési feladat futtatásának befejezését nyomon követő portálértesítések figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon. Válassza **a Backup Jobs** In  >  **progress (Folyamatban lévő biztonsági mentési feladatok) lehetőséget.**

### <a name="from-the-file-share-pane"></a>A fájlmegosztás panelen

1. Nyissa meg a fájlmegosztás **Áttekintés panelét,** amelyen igény szerinti biztonsági mentést szeretne készíteni.

1. A **Művelet szakaszban** válassza a Biztonsági **mentés** lehetőséget. A Jobb oldalon betöltődik a **Vault Essentials** listában látható helyi panel. Igény **szerinti biztonsági** mentés készítéséhez válassza a Biztonsági mentés lehetőséget.

   ![Válassza a Biztonsági mentés lehetőséget](./media/backup-afs/select-backup-now.png)

1. Megnyílik **a Biztonsági mentés** panel. Adja meg a helyreállítási pont megőrzési megőrzési pontját. Az igény szerinti biztonsági mentések legfeljebb 10 évig megőrzést kaphatnak.

   ![Biztonsági mentés dátumának megőrzése](./media/backup-afs/retain-backup-date.png)

1. Válassza az **OK** lehetőséget a megerősítéshez.

>[!NOTE]
>Azure Backup zárolja a tárfiókot, amikor a megfelelő fiókban található bármely fájlmegosztás védelmét konfigurálja. Ez védelmet nyújt a biztonságimentett fájlmegosztásokat tároló tárfiókok véletlen törlése ellen.

## <a name="best-practices"></a>Ajánlott eljárások

* Ne törölje a pillanatképek által létrehozott Azure Backup. A pillanatképek törlése helyreállítási pontok elvesztését és/vagy visszaállítási hibákat eredményezhet.

* Ne távolítsa el a tárfiók zárolását a Azure Backup. Ha törli a zárolást, a tárfiókban nagy eshet a véletlen törlés, és ha törlik, akkor elveszíti a pillanatképeket vagy a biztonsági másolatokat.

## <a name="next-steps"></a>Következő lépések

Az alábbiak végrehajtásának módját ismerheti meg:

* [Azure-fájlmegosztások visszaállítása](restore-afs.md)
* [Azure-fájlmegosztások biztonsági mentésének kezelése](manage-afs-backup.md)
