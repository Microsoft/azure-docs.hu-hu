---
title: Fájlok visszaállítása a Windows Serverre a MARS-ügynök használatával
description: Ebből a cikkből megtudhatja, hogyan állíthatók vissza az Azure-ban tárolt adatok egy Windows-kiszolgálóra vagy Windows-számítógépre a Microsoft Azure Recovery Services- (MARS-) ügynökkel.
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 7ca0787ec38e1bc22b62e756c7ee56c5c9e93493
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517334"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>Fájlok visszaállítása a Windows Serverre a MARS-ügynök használatával

Ez a cikk azt ismerteti, hogyan lehet adatokat visszaállítani egy Backup-tárolóból. Az adatok visszaállításához használja az Microsoft Azure Recovery Services- (MARS-) ügynök Adatok helyreállítása varázslóját. A következőket teheti:

* Állítsa vissza az adatokat arra a gépre, amelyről a biztonsági másolatok készültek.
* Adatok visszaállítása egy másik gépre.

Az Azonnali visszaállítás funkcióval egy írható helyreállítási pont pillanatképét csatlakoztathatja helyreállítási kötetként. Ezután felderítheti a helyreállítási kötetet, és fájlokat másolhat egy helyi számítógépre, így szelektíven visszaállíthatja a fájlokat.

> [!NOTE]
> A [2017. januári Azure Backup](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) frissítésre van szükség, ha az azonnali visszaállítást szeretné használni az adatok visszaállításához. Emellett a biztonsági mentési adatokat a támogatási cikkben felsorolt területi adatokat tárolókban kell védeni. Az azonnali visszaállítást támogató Azure Backup az azonnali visszaállítást támogató területi beállítások legújabb listájáért tekintse meg a [2017.](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) januári frissítésben.
>

Használja az azonnali visszaállítást a helyreállítási tárokkal a Azure Portal. Ha Backup-tárolókban tárolta az adatokat, azok Recovery Services-tárolókká konvertálva. Ha azonnali visszaállítást szeretne használni, töltse le a MARS-frissítést, és kövesse az azonnali visszaállítást megemlítő eljárásokat.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Az azonnali visszaállítás használata az adatok ugyanazon a gépen való helyreállításához

Ha véletlenül törölt egy fájlt, és vissza szeretné állítani ugyanazon a gépen (amelyről a biztonsági mentést készíti), az alábbi lépések segítenek az adatok helyreállításában.

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, hol lett telepítve a beépülő modul, keressen rá a számítógépre vagy a kiszolgálóra a **Microsoft Azure Backup.**

    Az asztali alkalmazásnak meg kell jelenni a keresési eredmények között.

2. Válassza **az Adatok helyreállítása lehetőséget** a varázsló elindítani.

    ![Képernyőkép a Azure Backup az Adatok helyreállítása kiemelésével (visszaállítás ugyanazon a gépen)](./media/backup-azure-restore-windows-server/recover.png)

3. Az Első **lépések lapon** az adatok ugyanazon kiszolgálóra vagy számítógépre való visszaállításához válassza az Ez a kiszolgáló ( **) `<server name>` Tovább**  >  **lehetőséget.**

    ![Képernyőkép az Adatok helyreállítása varázsló Első lépések lapról (visszaállítás ugyanazon a gépen)](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. A Helyreállítási **mód kiválasztása lapon** válassza az Egyes fájlok és **mappák** Lehetőséget > **Tovább.**

    ![Képernyőkép az Adatok helyreállítása varázsló Helyreállítási mód kiválasztása lapról (visszaállítás ugyanazon a gépen)](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Az egyes fájlok és mappák visszaállításához a 4.5.2-es .NET-keretrendszer szükséges. Ha nem látja az  Egyéni fájlok és mappák lehetőséget, frissítsen a .NET-keretrendszer 4.5.2-es vagy újabb verzióra, és próbálkozzon újra.

   > [!TIP]
   > Az **Egyes fájlok és mappák** lehetőséggel gyorsan hozzáférhet a helyreállítási pont adataihoz. Alkalmas az egyes fájlok helyreállítására, és 80 GB-nál kisebb teljes mérethez ajánlott. A helyreállítás során átviteli vagy másolási sebességet kínál akár 6 MB/s sebességre is. A **Kötet** beállítás a megadott köteten található összes biztonságimentett adatot helyreállítja. Ez a beállítás gyorsabb átviteli sebességet biztosít (legfeljebb 40 MBps), és nagy méretű adatok vagy teljes kötetek helyreállításához ajánlott.

5. A **Kötet és dátum kiválasztása lapon** válassza ki a visszaállítani kívánt fájlokat és mappákat tartalmazó kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. A **félkövérrel** szedett dátumok legalább egy helyreállítási pont rendelkezésre állását jelzik. Ha egy dátumon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az Idő legördülő menüből. 

    ![Képernyőkép az Adatok helyreállítása varázsló Kötet és dátum kiválasztása lapról (visszaállítás ugyanazon a gépen)](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Miután kiválasztotta a helyreállítási pontot a visszaállításhoz, válassza a **Csatlakoztatás lehetőséget.**

    Azure Backup csatlakoztatja a helyi helyreállítási pontot, és helyreállítási kötetként használja.

7. A Fájlok **tallózása** és  helyreállítása lapon válassza a Tallózás lehetőséget a Windows Explorer megnyitásához, és keresse meg a kívánt fájlokat és mappákat.

    ![Képernyőkép az Adatok helyreállítása varázsló Fájlok tallózása és helyreállítása lapról (visszaállítás ugyanazon a gépen)](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. A Windows Explorer másolja ki a visszaállítani kívánt fájlokat és mappákat, és illessze be őket egy helyi helyre a kiszolgálóra vagy számítógépre. A fájlokat megnyithatja vagy streamelheti közvetlenül a helyreállítási kötetről, és ellenőrizheti, hogy a megfelelő verziókat helyreállítja-e.

    ![Képernyőkép az Windows Explorer másolás kiemelésével (visszaállítás ugyanazon a gépen)](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Ha elkészült, a Fájlok tallózása és **helyreállítása lapon** válassza a **Unmount (Leválasztás) lehetőséget.** Ezután válassza **az Igen** lehetőséget annak megerősítéséhez, hogy le szeretné választani a kötetet.

    ![Képernyőkép az Adatok helyreállítása varázsló Fájlok tallózása és helyreállítása lapról (visszaállítás ugyanazon a gépen) – A helyreállítási kötet leválasztási megerősítése](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem választja ki az **Unmount**(Leválasztás) lehetőséget, a helyreállítási kötet a csatlakoztatva marad a csatlakoztatás óta 6 órán át. A csatlakoztatási idő azonban folyamatban lévő fájlmásolók esetén legfeljebb 24 órára van kiterjesztve. A kötet csatlakoztatva van, és nem futnak biztonsági mentési műveletek. A kötet csatlakoztatása közben ütemezett biztonsági mentési műveletek a helyreállítási kötet leválasztása után futnak.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Adatok visszaállítása egy másik gépre azonnali visszaállítás használatával

Ha a teljes kiszolgáló elveszett, akkor is helyreállíthatja az adatokat a Azure Backup gépre. Az alábbi lépések bemutatják a munkafolyamatot.

Ezek a lépések a következő terminológiát foglalják magukban:

* *Forrásgép* – Az az eredeti gép, amelyről a biztonsági mentés készült, és amely jelenleg nem érhető el.
* *Célgép* – Az a gép, amelyre az adatok helyreállítása folyamatban van.
* *Mintatartó* – Az a Recovery Services-tároló, amelyben a forrásgép és a célgép regisztrálva van.

> [!NOTE]
> A biztonsági másolatok nem állíthatók vissza olyan célgépre, amely az operációs rendszer egy korábbi verzióját futtatja. Egy Windows 7 rendszerű számítógépről készült biztonsági másolat például visszaállítható Egy Windows 7 -es (vagy újabb) számítógépen. A számítógépről Windows 10 biztonsági másolat nem állítható vissza Windows 7 rendszerű számítógépre.
>
>

1. Nyissa **Microsoft Azure Backup** beépülő modult a célgépen.

2. Győződjön meg arról, hogy a célgép és a forrásgép ugyanabban a Recovery Services-tárolóban van regisztrálva.

3. Az **Adatok helyreállítása varázsló** megnyitásához válassza az Adatok helyreállítása **lehetőséget.**

    ![Képernyőkép a Azure Backup az Adatok helyreállítása kiemelésével (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/recover.png)

4. Az Első **lépések lapon** válassza a Másik **kiszolgáló lehetőséget.**

    ![Képernyőkép az Adatok helyreállítása varázsló Első lépések lapról (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Adja meg a mintatartónak megfelelő tároló hitelesítőadat-fájlját, majd válassza a **Tovább lehetőséget.**

    Ha a tároló hitelesítőadat-fájlja érvénytelen (vagy lejárt), töltsön le egy új tároló hitelesítőadat-fájlt [a](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) tároló Azure Portal. Miután megadott egy érvényes hitelesítő adatokat a tárolóhoz, megjelenik a megfelelő backup-tároló neve.

6. A **Biztonsági mentési kiszolgáló kiválasztása lapon** válassza ki a forrásgépet a megjelenített gépek listájából, és adja meg a jelszót. Ezután kattintson a **Tovább** gombra.

    ![Képernyőkép az Adatok helyreállítása varázsló Biztonsági mentési kiszolgáló kiválasztása lapról (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. A Helyreállítási **mód kiválasztása lapon** válassza az Egyes fájlok és **mappák** Lehetőséget  >  **Tovább.**

    ![Képernyőkép az Adatok helyreállítása varázsló Helyreállítási mód kiválasztása lapról (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. A **Kötet és dátum kiválasztása lapon** válassza ki a visszaállítani kívánt fájlokat és mappákat tartalmazó kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. A **félkövérrel** szedett dátumok legalább egy helyreállítási pont rendelkezésre állását jelzik. Ha egy dátumon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az Idő legördülő menüből. 

    ![Képernyőkép az Adatok helyreállítása varázsló Kötet és dátum kiválasztása lapról (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Válassza **a Csatlakoztatás** helyileg csatlakoztatása helyreállítási pontot helyreállítási kötetként a célgépen.

10. A Fájlok **tallózása és**  helyreállítása lapon válassza a Tallózás lehetőséget a Windows Explorer megnyitásához, és keresse meg a kívánt fájlokat és mappákat.

    ![Képernyőkép az Adatok helyreállítása varázsló Fájlok tallózása és helyreállítása lapról (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. A Windows Explorer másolja ki a fájlokat és mappákat a helyreállítási kötetről, és illessze be őket a célgép helyére. A fájlokat megnyithatja vagy streamelheti közvetlenül a helyreállítási kötetről, és ellenőrizheti, hogy a megfelelő verziók helyreálltak-e.

    ![Képernyőkép a Windows Explorer másolás kiemelésével (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Ha elkészült, a Fájlok tallózása és **helyreállítása lapon** válassza a **Unmount (Leválasztás) lehetőséget.** Ezután válassza **az Igen** lehetőséget annak megerősítéséhez, hogy le szeretné választani a kötetet.

    ![A kötet leválasztása (visszaállítás másik gépre)](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem választja ki az **Unmount**(Leválasztás) lehetőséget, a helyreállítási kötet a csatlakoztatva marad a csatlakoztatás óta 6 órán át. A csatlakoztatási idő azonban folyamatban lévő fájlmásolók esetén legfeljebb 24 órára van kiterjesztve. A kötet csatlakoztatva van, és nem futnak biztonsági mentési műveletek. A kötet csatlakoztatása közben ütemezett biztonsági mentési műveletek a helyreállítási kötet leválasztása után futnak.
    >

## <a name="next-steps"></a>Következő lépések

* Most, hogy helyreállította a fájlokat és mappákat, kezelheti [a biztonsági másolatokat.](backup-azure-manage-windows-server.md)

* Gyakori kérdések a fájlok és mappák [biztonsági mentése ről.](backup-azure-file-folder-backup-faq.yml)
