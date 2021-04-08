---
title: Kötet összes fájljának visszaállítása a MARStal
description: Megtudhatja, hogyan állíthatja vissza a kötetek összes fájlját a MARS-ügynök használatával.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613971"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Kötet összes fájljának visszaállítása a MARS-ügynök használatával

Ez a cikk azt ismerteti, hogyan állíthatja vissza a teljes kötet összes biztonsági másolatát a Microsoft Azure Recovery Services-(MARS-) ügynök adatok helyreállítása varázslójának használatával. A következőket teheti:

- Egy köteten lévő összes biztonságimásolat-fájl visszaállítása ugyanarra a gépre, amelyről a biztonsági másolatok készültek.
- Egy köteten lévő összes biztonsági mentési fájl visszaállítása egy másik gépre.

>[!TIP]
>A **Volume (kötet** ) beállítással egy adott köteten található összes biztonsági másolati elem helyreállítható. Ez a beállítás gyorsabb átviteli sebességet (akár 40 MBps-t) biztosít, és a nagy méretű adatok vagy a teljes kötetek helyreállításához ajánlott.
>
>Az **egyes fájlok és mappák lehetőséggel** gyorsan hozzáférhet a helyreállítási ponthoz tartozó adatfájlokhoz. Alkalmas az egyes fájlok helyreállítására, és az 80 GB-nál kisebb teljes mérethez ajánlott. A helyreállítás során akár 6 MBps sebességű átvitelt és másolást is biztosít.

## <a name="volume-level-restore-to-the-same-machine"></a>Kötet szintjének visszaállítása ugyanarra a gépre

A következő lépések segítséget nyújtanak a köteten található összes biztonsági mentési fájl helyreállításához:

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, hol telepítette a beépülő modult, keresse meg **Microsoft Azure Backup** a számítógépet vagy a kiszolgálót. Az asztali alkalmazásnak meg kell jelennie a keresési eredmények között.

1. A varázsló elindításához kattintson az **adatok helyreállítása** elemre.

    ![Adatok helyreállítása menü](./media/restore-all-files-volume-mars/recover.png)

1. A **első lépések** lapon a következőt válassza ki ezt a kiszolgálót **(kiszolgálónév)**, ha az adott kiszolgálóra vagy számítógépre szeretné visszaállítani az adatfájlokat  >  .

    ![Első lépések oldal](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. A **helyreállítási mód kiválasztása** lapon válassza a **kötet**  >  **tovább** lehetőséget.

    ![Helyreállítási mód kiválasztása](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. A **kötet és dátum kiválasztása** lapon válassza ki a visszaállítani kívánt kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. A **félkövérrel szedett** dátumok azt jelzik, hogy legalább egy helyreállítási pont rendelkezésre áll. Ha egy adott napon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az **idő** legördülő menüből.

     ![Kötet és dátum kiválasztása](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. A **helyreállítási beállítások megadása** lapon konfigurálja a visszaállítási viselkedést.
    1. Válassza ki a helyreállítási célhelyet:
        - **Eredeti hely**: az adatbázis visszaállítása az eredeti elérési útra.
        - **Egy másik hely**: adja meg azt az alternatív helyet, amelyre vissza kívánja állítani az adatgyűjtést.
    1. Válassza ki a viselkedést, **Ha a biztonsági mentésben szereplő elemek már a helyreállítási célhelyen vannak**:
        - **Hozzon létre másolatokat, hogy mindkét verzióban** legyenek: Ha már létezik ilyen nevű fájl, a helyreállítási pontban lévő összes érték másolatként lesz visszaállítva. A másolás honosított fájlnév-előtaggal fog rendelkezni a helyi visszaállítási feladatok időpontjával a következő formátumok egyikében:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Meglévő verziók felülírása a helyreállított verziókkal**: Ha már létezik ilyen nevű fájl, a rendszer a tartalmat a helyreállítási pontban található értékre cseréli.
        - Ne **állítsa helyre a helyreállítási célhelyen már létező elemeket**: Ha már létezik ilyen nevű fájl, a rendszer kihagyja.
    1. **Engedélyezze a hozzáférés-vezérlési lista (ACL) engedélyeit a helyreállított fájlhoz vagy mappához** , ha a fájlt vissza kell állítani a helyreállítási pont eredeti engedélyeivel.
        ![Helyreállítási beállítások megadása](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Ellenőrizze a helyreállítás részleteit a **megerősítő** ablaktáblán, és válassza a **helyreállítás** lehetőséget.

    ![Megerősítés részletei](./media/restore-all-files-volume-mars/confirmation-details.png)

1. A **helyreállítási folyamat** lapon figyelje a helyreállítási feladatok állapotát. A varázsló is biztonságosan lezárható, és a háttérben folytatódik a helyreállítási művelet. Az előrehaladást úgy tekintheti meg, ha duplán kattint a helyreállítási feladatokra az irányítópulton.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Kötet szintjének visszaállítása egy másik gépre

A következő lépések segítségével helyreállíthatja a köteten található összes biztonsági másolattal rendelkező fájlt egy másik gépre. Ezekkel a lépésekkel helyreállíthatja Azure Backup adatait, ha a teljes kiszolgáló elvész.

Ezek a lépések a következő terminológiát tartalmazzák:

- *Forrásoldali gép* – az eredeti gép, amelyről a biztonsági mentés készült, és amely jelenleg nem érhető el.
- *Célszámítógép* – az a gép, amelyre az Adathelyreállítás történik.
- *Minta* -tár – az a Recovery Services tároló, amelybe a forrásszámítógép és a célszámítógép regisztrálva van.

> [!NOTE]
> A biztonsági mentések nem állíthatók vissza olyan célszámítógépre, amely az operációs rendszer korábbi verzióját futtatja. Például egy Windows 7 rendszerű számítógépről készített biztonsági másolat visszaállítható Windows 7 (vagy újabb) rendszerű számítógépen. Egy Windows 10 rendszerű számítógépről készített biztonsági másolat nem állítható vissza Windows 7 rendszerű számítógépre.

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult a célszámítógépen.

1. Győződjön meg arról, hogy a célszámítógép és a forrásszámítógép ugyanahhoz a Recovery Services-tárolóhoz van regisztrálva.

1. Válassza az **adatok helyreállítása** lehetőséget az **adatok helyreállítása varázsló** megnyitásához.

    ![Képernyőkép a Azure Backupről, az adatok kiemelésével (visszaállítás másodlagos gépre)](./media/backup-azure-restore-windows-server/recover.png)

1. A **első lépések** lapon válasszon **egy másik kiszolgálót**.

    ![Képernyőfelvétel az adatok helyreállítása varázsló Első lépések oldaláról (visszaállítás másodlagos gépre)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Adja meg a tár hitelesítő adatait tartalmazó fájlt, amely megfelel a mintatartó tárolónak, majd kattintson a **Tovább gombra**.

    Ha a tároló hitelesítőadat-fájlja érvénytelen (vagy lejárt), [töltse le a tárolóban lévő új hitelesítőadat-fájlt](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) a Azure Portal. A tár érvényes hitelesítő adatainak megadását követően megjelenik a megfelelő biztonságimásolat-tároló neve.

1. A **biztonsági mentési kiszolgáló kiválasztása** lapon válassza ki a forrásszámítógép listáját a megjelenített gépek listájából, és adja meg a jelszót. Ezután kattintson a **Tovább** gombra.

    ![Képernyőkép az adatok helyreállítása varázsló biztonsági mentési kiszolgáló kiválasztása lapján (visszaállítás másodlagos gépre)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. A **helyreállítási mód kiválasztása** lapon válassza a **kötet**  >  **tovább** lehetőséget.

    ![Helyreállítási mód kiválasztása](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. A **kötet és dátum kiválasztása** lapon válassza ki a visszaállítani kívánt kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. A **félkövérrel szedett** dátumok azt jelzik, hogy legalább egy helyreállítási pont rendelkezésre áll. Ha egy adott napon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az **idő** legördülő menüből.

     ![Kötet és dátum kiválasztása](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. A **helyreállítási beállítások megadása** lapon konfigurálja a visszaállítási viselkedést.
    1. Válassza ki a helyreállítási célhelyet:
        - **Eredeti hely**: az adatbázis visszaállítása az eredeti elérési útra.
        - **Egy másik hely**: adja meg azt az alternatív helyet, amelyre vissza kívánja állítani az adatgyűjtést.
    1. Válassza ki a viselkedést, **Ha a biztonsági mentésben szereplő elemek már a helyreállítási célhelyen vannak**:
        - **Hozzon létre másolatokat, hogy mindkét verzióban** legyenek: Ha már létezik ilyen nevű fájl, a helyreállítási pontban lévő összes érték másolatként lesz visszaállítva. A másolás honosított fájlnév-előtaggal fog rendelkezni a helyi visszaállítási feladatok időpontjával a következő formátumok egyikében:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Meglévő verziók felülírása a helyreállított verziókkal**: Ha már létezik ilyen nevű fájl, a rendszer a tartalmat a helyreállítási pontban található értékre cseréli.
        - Ne **állítsa helyre a helyreállítási célhelyen már létező elemeket**: Ha már létezik ilyen nevű fájl, a rendszer kihagyja.
    1. **Engedélyezze a hozzáférés-vezérlési lista (ACL) engedélyeit a helyreállított fájlhoz vagy mappához** , ha a fájlt vissza kell állítani a helyreállítási pont eredeti engedélyeivel.
        ![Helyreállítási beállítások megadása](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Ellenőrizze a helyreállítás részleteit a **megerősítő** ablaktáblán, és válassza a **helyreállítás** lehetőséget.

    ![Megerősítés részletei](./media/restore-all-files-volume-mars/confirmation-details.png)

1. A **helyreállítási folyamat** lapon figyelje a helyreállítási feladatok állapotát. A varázsló is biztonságosan lezárható, és a háttérben folytatódik a helyreállítási művelet. Az előrehaladást úgy tekintheti meg, ha duplán kattint a helyreállítási feladatokra az irányítópulton.

## <a name="next-steps"></a>Következő lépések

- Most, hogy helyreállítja a fájlokat és mappákat, [kezelheti a biztonsági mentéseket](backup-azure-manage-windows-server.md).
- [Gyakori kérdések a fájlok és mappák biztonsági mentéséről](backup-azure-file-folder-backup-faq.md).
