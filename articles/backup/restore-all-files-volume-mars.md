---
title: Egy köteten lévő összes fájl visszaállítása a MARS-sal
description: Megtudhatja, hogyan állítható vissza egy kötet összes fájlja a MARS-ügynökkel.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 1d04e9f77b9f92594def9381f973c999e96b2cb2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516501"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Egy köteten lévő összes fájl visszaállítása a MARS-ügynökkel

Ez a cikk azt ismerteti, hogyan állíthatók vissza az összes biztonsági másolatból készült fájl egy teljes köteten az Microsoft Azure Recovery Services- (MARS-) ügynök Adatok helyreállítása varázslójával. A következőket teheti:

- A köteten lévő összes biztonsági másolatból származó fájlt állítsa vissza arra a gépre, amelyről a biztonsági másolatok készültek.
- Egy kötet összes biztonsági másolata egy másik gépre való visszaállítása.

>[!TIP]
>A **Kötet** beállítás a megadott köteten található összes biztonságimentett adatot helyreállítja. Ez a beállítás gyorsabb átviteli sebességet biztosít (legfeljebb 40 MBps), és nagy méretű adatok vagy teljes kötetek helyreállításához ajánlott.
>
>Az **Egyes fájlok és mappák lehetőséggel** gyorsan hozzáférhet a helyreállítási pont adataihoz. Alkalmas az egyes fájlok helyreállítására, és 80 GB-nál kisebb teljes mérethez ajánlott. A helyreállítás során átviteli vagy másolási sebességet biztosít akár 6 MB/s sebességre is.

## <a name="volume-level-restore-to-the-same-machine"></a>Kötetszintű visszaállítás ugyanazon a gépen

A következő lépések segítségével helyreállítható egy kötet összes biztonsági mentése:

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, hol lett telepítve a beépülő modul, keressen rá a számítógépre vagy a kiszolgálóra a **Microsoft Azure Backup.** Az asztali alkalmazásnak meg kell jelenni a keresési eredmények között.

1. Válassza **az Adatok helyreállítása lehetőséget** a varázsló elindítani.

    ![Adatok helyreállítása menü](./media/restore-all-files-volume-mars/recover.png)

1. Az Első **lépések lapon** az adatok ugyanazon kiszolgálóra vagy számítógépre való visszaállításához válassza az Ez a **kiszolgáló (kiszolgálónév) Tovább**  >  **lehetőséget.**

    ![Első lépések oldal](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. A Helyreállítási **mód kiválasztása lapon** válassza a Kötet **Tovább**  >  **lehetőséget.**

    ![Helyreállítási mód kiválasztása](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. A Kötet **és dátum kiválasztása lapon** válassza ki a visszaállítani kívánt kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. A **félkövérrel** szedett dátumok legalább egy helyreállítási pont rendelkezésre állását jelzik. Ha egy dátumon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az Idő legördülő menüből. 

     ![Kötet és dátum kiválasztása](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. A Helyreállítási **beállítások megadása lapon** konfigurálja a visszaállítási viselkedést.
    1. Válassza ki a helyreállítási célt:
        - **Eredeti hely:** Adatok visszaállítása az eredeti elérési útra.
        - **Másik hely:** Adjon meg egy másik helyet az adatok visszaállításához.
    1. Válassza a Ha a biztonsági másolat elemei már a helyreállítási **célhelyen vannak viselkedést:**
        - **Másolatok létrehozása, hogy** mindkét verzióval rendelkezik: Ha már létezik ilyen nevű fájl, a helyreállítási pont adatai másolatként lesznek visszaállva. A másolat egy honosított fájlnév-előtaggal fog dolgozni, amely a helyi visszaállítási feladat idejét használja az alábbi formátumok egyikében:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Meglévő verziók** felülírása helyreállított verziókra: Ha már létezik ilyen nevű fájl, a rendszer a tartalmat a helyreállítási pont adataira cseréli.
        - **Ne helyreállítsa a** helyreállítási célhelyen már létező elemeket: Ha már létezik ilyen nevű fájl, a rendszer kihagyja azt.
    1. Engedélyezze a Helyreállítható hozzáférés-vezérlési lista **(ACL)** engedélyeket a helyreállított fájlra vagy mappára, ha a fájlt a helyreállítási pont eredeti engedélyekkel szeretné visszaállítani.
        ![Helyreállítási beállítások megadása](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. A Megerősítés panelen ellenőrizze  a helyreállítás részleteit, majd válassza a Helyreállítás **lehetőséget.**

    ![Megerősítés részletei](./media/restore-all-files-volume-mars/confirmation-details.png)

1. A Helyreállítási **folyamat lapon** figyelje a helyreállítási feladat előrehaladását. A varázsló biztonságosan bezárható, és a helyreállítási művelet a háttérben folytatódik. A folyamat előrehaladásának megtekintéséhez kattintson duplán a Helyreállítási feladatra az irányítópulton.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Kötetszintű visszaállítás másik gépre

A következő lépések segítségével helyreállítható egy kötet összes biztonsági mentése egy másik gépre. Ezekkel a lépésekkel helyreállíthatja az adatokat a Azure Backup ha a teljes kiszolgáló elveszett.

Ezek a lépések a következő terminológiát foglalják magukban:

- *Forrásgép* – Az az eredeti gép, amelyről a biztonsági mentés készült, és amely jelenleg nem érhető el.
- *Célgép* – Az a gép, amelyre az adatok helyreállítása folyamatban van.
- *Mintatartó* – Az a Recovery Services-tároló, amelyben a forrásgép és a célgép regisztrálva van.

> [!NOTE]
> A biztonsági másolatok nem állíthatók vissza olyan célgépre, amely az operációs rendszer egy korábbi verzióját futtatja. Egy Windows 7 rendszerű számítógépről készült biztonsági másolat például visszaállítható Egy Windows 7 -es (vagy újabb) számítógépen. A számítógépről Windows 10 biztonsági másolat nem állítható vissza Windows 7 rendszerű számítógépre.

1. Nyissa meg **Microsoft Azure Backup** beépülő modult a célgépen.

1. Győződjön meg arról, hogy a célgép és a forrásgép ugyanabban a Recovery Services-tárolóban van regisztrálva.

1. Az **Adatok helyreállítása varázsló** megnyitásához válassza az Adatok helyreállítása **lehetőséget.**

    ![Képernyőkép az Azure Backup, kiemelt Adatok helyreállítása (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/recover.png)

1. Az Első **lépések lapon** válassza a Másik **kiszolgáló lehetőséget.**

    ![Képernyőkép az Adatok helyreállítása varázsló Első lépések lapról (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Adja meg a tároló mintatartónak megfelelő hitelesítőadat-fájlját, majd válassza a **Tovább lehetőséget.**

    Ha a tároló hitelesítőadat-fájlja érvénytelen (vagy lejárt), töltsön le egy új tároló hitelesítőadat-fájlt [a](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) tároló Azure Portal. Miután megadott egy érvényes hitelesítő adatokat a tárolóhoz, megjelenik a megfelelő backup-tároló neve.

1. A Biztonsági **mentési kiszolgáló kiválasztása lapon** válassza ki a forrásgépet a megjelenített gépek listájából, és adja meg a jelszót. Ezután kattintson a **Tovább** gombra.

    ![Képernyőkép az Adatok helyreállítása varázsló Biztonsági mentési kiszolgáló kiválasztása lapról (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. A Helyreállítási **mód kiválasztása lapon** válassza a Kötet **Tovább**  >  **lehetőséget.**

    ![Helyreállítási mód kiválasztása](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. A Kötet **és dátum kiválasztása lapon** válassza ki a visszaállítani kívánt kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. A **félkövérrel** szedett dátumok legalább egy helyreállítási pont rendelkezésre állását jelzik. Ha egy dátumon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az Idő legördülő menüből. 

     ![Kötet és dátum kiválasztása](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. A Helyreállítási **beállítások megadása lapon** konfigurálja a visszaállítási viselkedést.
    1. Válassza ki a helyreállítási célt:
        - **Eredeti hely:** Adatok visszaállítása az eredeti elérési útra.
        - **Másik hely:** Adjon meg egy másik helyet az adatok visszaállításához.
    1. Válassza a Ha a biztonsági másolat elemei már a helyreállítási **célhelyen vannak viselkedést:**
        - **Másolatok létrehozása, hogy** mindkét verzióval rendelkezik: Ha már létezik ilyen nevű fájl, a helyreállítási pont adatai másolatként lesznek visszaállva. A másolat egy honosított fájlnév-előtaggal fog dolgozni, amely a helyi visszaállítási feladat idejét használja az alábbi formátumok egyikében:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Meglévő verziók** felülírása helyreállított verziókra: Ha már létezik ilyen nevű fájl, a rendszer a tartalmat a helyreállítási pont adataira cseréli.
        - **Ne helyreállítsa a** helyreállítási célhelyen már létező elemeket: Ha már létezik ilyen nevű fájl, a rendszer kihagyja azt.
    1. Engedélyezze a Helyreállítható hozzáférés-vezérlési lista **(ACL)** engedélyeket a helyreállított fájlra vagy mappára, ha a fájlt a helyreállítási pont eredeti engedélyekkel szeretné visszaállítani.
        ![Helyreállítási beállítások megadása](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. A Megerősítés panelen ellenőrizze  a helyreállítás részleteit, majd válassza a Helyreállítás **lehetőséget.**

    ![Megerősítés részletei](./media/restore-all-files-volume-mars/confirmation-details.png)

1. A Helyreállítási **folyamat lapon** figyelje a helyreállítási feladat előrehaladását. A varázsló biztonságosan bezárható, és a helyreállítási művelet a háttérben folytatódik. A folyamat előrehaladásának megtekintéséhez kattintson duplán a Helyreállítási feladatra az irányítópulton.

## <a name="next-steps"></a>Következő lépések

- Most, hogy helyreállította a fájlokat és mappákat, kezelheti [a biztonsági másolatokat.](backup-azure-manage-windows-server.md)
- Gyakori kérdések a fájlok és mappák [biztonsági mentése ről.](backup-azure-file-folder-backup-faq.yml)
