---
title: A Windows rendszer állapotának biztonsági mentése az Azure-ba
description: Ismerje meg, hogyan készíthet biztonsági mentést a Windows Server rendszerű számítógépek rendszerállapotáról az Azure-ba.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 1b3573d757d2f7b1ffec9ae718aa791488960f3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332889"
---
# <a name="back-up-windows-system-state-to-azure"></a>A Windows rendszer állapotának biztonsági mentése az Azure-ba

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni a Windows Server rendszer állapotáról az Azure-ba. Ennek célja, hogy végigvezeti az alapokon.

Ha többet szeretne megtudni az Azure Backupról, olvassa el ezt az [áttekintést](backup-overview.md).

Ha még nincs Azure-előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/), amellyel bármely Azure-szolgáltatást elérhet.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Tárhely-redundancia beállítása a tárolóhoz

A Recovery Services-tároló létrehozásakor győződjön meg róla, hogy a tárhely-redundancia a saját igényei szerint van beállítva.

1. Az **Recovery Services** -tárolók ablaktáblán válassza ki az új tárolót.

    ![A Recovery Services-tárolók listájából válassza ki az új tárolót](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Ha kijelöli a tárolót, a **Recovery Services** tároló ablaktábla szűkül, és a beállítások panel (*amely a tetején* található tároló neve) és a tár részletek ablaktáblája meg van nyitva.

    ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. Az új tár Beállítások paneljén a függőleges görgetősáv használatával görgessen lefelé a kezelés szakaszhoz, és válassza a **biztonsági mentési infrastruktúra** lehetőséget.
    Megnyílik a biztonsági mentési infrastruktúra panel.
3. A biztonsági mentési infrastruktúra ablaktáblán válassza a **biztonsági mentési konfiguráció** elemet a **biztonsági mentési konfiguráció** ablaktábla megnyitásához.

    ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Válassza ki a megfelelő tárolóreplikációs beállítást a tárolóhoz.

    ![Tárolási konfiguráció lehetőségei](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration-for-vault.png)

    Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, folytassa a **georedundáns** beállítás használatát. Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit. További információ a tárterület [-redundáns](../storage/common/storage-redundancy.md#geo-redundant-storage), [helyileg redundáns](../storage/common/storage-redundancy.md#locally-redundant-storage) és [zónákra redundáns](../storage/common/storage-redundancy.md#zone-redundant-storage) tárolási lehetőségekről a [tárolási redundancia áttekintésében](../storage/common/storage-redundancy.md).

Most, hogy létrehozott egy tárolót, konfigurálja a Windows rendszerállapotának biztonsági mentésére.

## <a name="configure-the-vault"></a>A tároló konfigurálása

1. A Recovery Services tároló ablaktáblán (az imént létrehozott **tárolónál**) a első lépések szakaszban válassza a biztonsági mentés lehetőséget, majd a első lépések a **Backup** (biztonsági mentés) ablaktáblán kattintson a **biztonsági mentés célja** elemre.

    ![Biztonsági mentési beállítások megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Megnyílik a **biztonsági mentés célja** panel.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. A **Hol futnak az alkalmazások és szolgáltatások?** legördülő menüből válassza a **Helyszíni** lehetőséget.

    Ha a Windows Server vagy a Windows rendszerű számítógép olyan fizikai gép, amely nem az Azure-ban van, akkor válassza **a** helyszíni lehetőséget.

3. A **Miről szeretne biztonsági másolatot készíteni?** menüben válassza a **rendszerállapot** lehetőséget, majd kattintson **az OK gombra**.

    ![Fájlok és mappák konfigurálása](./media/backup-azure-system-state/backup-goal-system-state.png)

    Ha **az OK gombra** kattint, a **biztonsági mentés célja** mellett megjelenik egy pipa, az **infrastruktúra előkészítése** panel pedig megnyílik.

    ![Ha konfigurálta a biztonsági mentés célját, a következő lépés az infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. Az **infrastruktúra előkészítése** panelen válassza a **Windows Server vagy a Windows ügyfél ügynökének letöltése** lehetőséget.

    ![Az infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Ha a Windows Server alapvető fontosságú, akkor válassza a Windows Serverhez nélkülözhetetlen ügynök letöltését. Egy előugró menü rákérdez, hogy futtatni vagy menteni kívánja-e a MARSAgentInstaller.exe fájlt.

    ![MARSAgentInstaller párbeszédpanel](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. A letöltési előugró menüben válassza a **Mentés** lehetőséget.

    Alapértelmezés szerint az **MARSagentinstaller.exe** fájlt a rendszer a Downloads mappába menti. Ha a telepítő befejeződik, megjelenik egy előugró ablak, amely rákérdez, hogy szeretné-e futtatni a telepítőt, vagy megnyitni a mappát.

    ![A MARS-telepítő elkészült](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Az ügynököt még nem kell telepíteni. Az ügynököt a tár hitelesítő adatainak letöltése után is telepítheti.

6. Az **infrastruktúra előkészítése** panelen válassza a **Letöltés** lehetőséget.

    ![a tároló hitelesítő adatainak letöltése](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    A tár hitelesítő adatai le vannak letöltve a **letöltések** mappájába. Miután a tároló hitelesítő adatainak letöltése befejeződött, megjelenik egy előugró ablak, amely rákérdez, hogy szeretné-e megnyitni vagy menteni a hitelesítő adatokat. Kattintson a **Mentés** gombra. Ha véletlenül kiválasztja a **Megnyitás** lehetőséget, hagyja meg a párbeszédpanelt, amely a tár hitelesítő adatainak megnyitását kísérli meg. Nem fogja tudni megnyitni a tároló hitelesítő adatait. Folytassa a következő lépéssel. A tároló hitelesítő adatai a **letöltések** mappában találhatók.

    ![a tároló hitelesítő adatainak letöltése befejeződött](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > A tár hitelesítő adatait csak olyan helyre kell menteni, amely helyi a Windows Server, amelyen az ügynököt használni kívánja.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

> [!NOTE]
> Nem érhető el a biztonsági mentés engedélyezése a Azure Portalon keresztül. A Microsoft Azure Recovery Services ügynök használatával biztonsági mentést készíthet a Windows Server rendszerállapotáról.
>

1. Keresse meg és kattintson duplán az **MARSagentinstaller.exe** fájlra a Letöltések mappában (vagy más mentési helyen).

    A telepítő egy sor üzenetet jelenít meg, miközben kibontja, telepíti és regisztrálja a Recovery Services-ügynököt.

    ![a Recovery Services-ügynök telepítőjének futtatása, hitelesítő adatok](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Végezze el a Microsoft Azure Recovery Services ügynök telepítővarázslójának lépéseit. A varázsló lépéseinek elvégzéséhez a következőket kell tennie:

   * Válasszon egy helyet a telepítés és a gyorsítótár mappája számára.
   * Adja meg a proxykiszolgáló információit, ha proxykiszolgálóval csatlakozik az internethez.
   * Adja meg a felhasználónevének és jelszavának részleteit, ha hitelesített proxyt használ.
   * Adja meg a tároló letöltött hitelesítő adatait.
   * Mentse a titkosítási jelszót egy biztonságos helyen.

     > [!NOTE]
     > Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem segít helyreállítani a biztonsági mentési adatokat. Mentse a fájlt egy biztonságos helyen. A biztonsági másolat visszaállításához szükséges.
     >
     >

Az ügynök most telepítve van, és a gépe regisztrálva van a tárolóban. Készen áll a biztonsági mentés konfigurálására és ütemezésére.

## <a name="back-up-windows-server-system-state"></a>A Windows Server rendszerállapotának biztonsági mentése

A kezdeti biztonsági mentés két feladatot tartalmaz:

* A biztonsági mentés ütemezése
* Rendszerállapot biztonsági mentése első alkalommal

A kezdeti biztonsági mentés végrehajtásához használja a Microsoft Azure Recovery Services-ügynököt.

> [!NOTE]
> A Windows Server 2008 R2 rendszeren a Windows Server 2016 használatával biztonsági mentést készíthet a rendszerállapotról. A rendszerállapot biztonsági mentése nem támogatott az ügyfél SKU-ban. A rendszerállapot nem jelenik meg a Windows-ügyfelek vagy a Windows Server 2008 SP2 rendszerű gépek beállításként.
>
>

### <a name="to-schedule-the-backup-job"></a>A biztonsági mentési feladat ütemezése

1. Nyissa meg a Microsoft Azure Recovery Services-ügynököt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Az Azure Recovery Services-ügynök indítása](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. A Recovery Services ügynökben válassza a **biztonsági mentés időzítése** elemet.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. A biztonsági mentés időzítése varázsló **első lépések** lapján kattintson a **Tovább gombra**.

4. Az **elemek kiválasztása a biztonsági mentéshez** lapon válassza az **elemek hozzáadása** lehetőséget.

5. Válassza a **rendszerállapot** lehetőséget, majd kattintson **az OK gombra**.

6. Kattintson a **Tovább** gombra.

7. A következő lapokon válassza ki a szükséges biztonsági mentési gyakoriságot és a rendszerállapot biztonsági mentések megőrzési szabályát.

8. A jóváhagyás lapon tekintse át az információkat, majd kattintson a **Befejezés gombra**.

9. Miután a varázsló befejezte a biztonsági mentési ütemterv létrehozását, válassza a **Bezárás** lehetőséget.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>A Windows Server rendszer állapotának biztonsági mentése első alkalommal

1. Győződjön meg arról, hogy nincsenek olyan függőben lévő frissítések a Windows Server rendszerhez, amelyek újraindítást igényelnek.

2. A Recovery Services ügynökben válassza a **biztonsági mentés most** lehetőséget, hogy elvégezze a kezdeti előkészítést a hálózaton.

    ![Windows Server biztonsági mentés most](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. A megjelenő **biztonsági mentési elem kiválasztása** képernyőn válassza a **rendszerállapot** lehetőséget, majd kattintson a **Tovább gombra**.

4. A Jóváhagyás lapon tekintse át azokat a beállításokat, amelyeket a Biztonsági másolat készítése varázsló a gép biztonsági mentéséhez fog használni. Ezután válassza a **biztonsági mentés** lehetőséget.

5. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha bezárja a varázslót a biztonsági mentési folyamat befejezése előtt, a varázsló továbbra is fut a háttérben.
    > [!NOTE]
    > A MARS-ügynök `SFC /verifyonly` a rendszerállapot minden biztonsági mentése előtt elindítja az előzetes ellenőrzések részét. Ezzel a beállítással biztosíthatja, hogy a rendszerállapot részeként biztonsági másolatokat tartalmazó fájlok a Windows verziójának megfelelő verziójúak legyenek. További információ a rendszerfájl-ellenőrzési (SFC) szolgáltatásról [ebben a cikkben](/windows-server/administration/windows-commands/sfc).
    >

A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

  ![IR befejezve](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Kérdése van?

Ha kérdése van, [küldjön nekünk visszajelzést](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Következő lépések

* További részletek a [Windows rendszerű gépek biztonsági mentéséről](backup-windows-with-mars-agent.md).
* Most, hogy biztonsági másolatot készített a Windows Server rendszerállapotáról, [kezelheti a tárolókat és a kiszolgálókat](backup-azure-manage-windows-server.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
