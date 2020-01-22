---
title: A Windows rendszer állapotának biztonsági mentése az Azure-ba
description: Útmutató a Windows Server és/vagy Windows rendszerű számítógépek rendszerállapotának az Azure-ba történő biztonsági mentéséhez.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 847ed8fc5a6c102284a03fa593587792767d7913
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "76294014"
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Windows rendszerállapot biztonsági mentése a Resource Manager üzembe helyezése során

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni a Windows Server rendszer állapotáról az Azure-ba. Ennek célja, hogy végigvezeti az alapokon.

Ha többet szeretne megtudni az Azure Backupról, olvassa el ezt az [áttekintést](backup-overview.md).

Ha még nincs Azure-előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/), amellyel bármely Azure-szolgáltatást elérhet.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Windows Server rendszerállapotának biztonsági mentéséhez létre kell hoznia egy Recovery Services-tárolót abban a régióban, ahol az adatait tárolni szeretné. Emellett a tároló replikálásának módját is meg kell határoznia.

### <a name="to-create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/) Azure-előfizetésével.
2. A központi menüben kattintson a **Minden szolgáltatás** elemre, majd az erőforrások listájában írja be a **Recovery Services** szöveget, és kattintson a **Recovery Services-tárolók** elemre.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-system-state/open-rs-vault-list.png)

    Ha az előfizetés Recovery Services-tárolókat tartalmaz, a tárolók fel vannak sorolva.
3. A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** elemre.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Megnyílik a Recovery Services-tároló panelje, a rendszer pedig egy **Név**, **Előfizetés**, **Erőforráscsoport** és **Hely** megadását kéri.

    ![Recovery Services-tároló létrehozása – 3. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez. Írjon be egy 2–50 karakter hosszúságú nevet. Ennek egy betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.

5. Az **Előfizetés** szakaszban, az Azure-előfizetés kiválasztásához használja a legördülő menüt. Ha csak egy előfizetést használ, az az előfizetés jelenik meg, és továbbléphet a következő lépésre. Ha nem biztos benne, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (vagy javasolt) előfizetést. Csak akkor lesz több választási lehetőség, ha a szervezetéhez tartozó fiók több Azure-előfizetéssel van összekötve.

6. Az **Erőforráscsoport** szakaszban:

    * válassza az **Új létrehozása** lehetőséget, ha erőforráscsoportot szeretne létrehozni.
    vagy
    * válassza a **Meglévő használata** lehetőséget, és kattintson a legördülő menüben az elérhető erőforráscsoportok listájának megtekintéséhez.

   Átfogó információk az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md).

7. Kattintson a **Hely** elemre a tárolóhoz tartozó földrajzi régió kiválasztásához. Ez a választás határozza meg a földrajzi régiót, ahová az adatok biztonsági másolata el lesz küldve.

8. Kattintson a Recovery Services-tároló panel alján a **Létrehozás** gombra.

    A Recovery Services-tároló létrehozása több percet is igénybe vehet. Figyelje az állapotértesítéseket a portál jobb felső területén. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában. Ha több perc után sem látja a tárolót, kattintson a **Frissítés** gombra.

    ![Kattintson a Frissítés gombra](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Ha látja a tárolót a Recovery Services-tárolók listájában, készen áll tárhely-redundancia beállítására.

### <a name="set-storage-redundancy-for-the-vault"></a>Tárhely-redundancia beállítása a tárolóhoz

A Recovery Services-tároló létrehozásakor győződjön meg róla, hogy a tárhely-redundancia a saját igényei szerint van beállítva.

1. A **Recovery Services-tárolók** panelen kattintson az új tárolóra.

    ![A Recovery Services-tárolók listájából válassza ki az új tárolót](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Ha kiválasztja a tárolót, a **Recovery Services-tároló** panel leszűkül, és a Beállítások panel (*amelynek tetején a tároló neve látható*), valamint a tároló részleteit tartalmazó panel nyílik meg.

    ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. Használja a függőleges csúszkát az új tároló Beállítások paneljén a legörgetéshez a Kezelés szakaszhoz, és kattintson a **Biztonsági mentési infrastruktúra** lehetőségre.
    Megnyílik a Biztonsági mentési infrastruktúra panel.
3. A Biztonsági mentési infrastruktúra panelen kattintson a **Biztonsági mentés konfigurációja** elemre a **Biztonsági mentés konfigurációja** panel megnyitásához.

    ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Válassza ki a megfelelő tárolóreplikációs beállítást a tárolóhoz.

    ![a tároló konfigurálásának lehetőségei](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, folytassa a **georedundáns** beállítás használatát. Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat ebben a [Tárhely-redundancia áttekintésben](../storage/common/storage-redundancy.md).

Most, hogy létrehozott egy tárolót, konfigurálja a Windows rendszerállapotának biztonsági mentésére.

## <a name="configure-the-vault"></a>A tároló konfigurálása

1. A Recovery Services-tároló (az imént létrehozott tároló) paneljén a Bevezetés szakaszban kattintson a **Biztonsági mentés** elemre, majd a **Bevezetés a biztonsági mentés használatába** panelen válassza a **Biztonsági mentés célja** elemet.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Megnyílik a **Biztonsági mentés célja** panel.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. A **Hol futnak az alkalmazások és szolgáltatások?** legördülő menüből válassza a **Helyszíni** lehetőséget.

    Azért kell a **Helyszíni** lehetőséget választania, mert a Windows Server- vagy Windows-számítógépe egy fizikai gép, amely nem az Azure része.

3. A **Miről szeretne biztonsági másolatot készíteni?** menüben válassza a **rendszerállapot**lehetőséget, majd kattintson **az OK**gombra.

    ![Fájlok és mappák konfigurálása](./media/backup-azure-system-state/backup-goal-system-state.png)

    Miután az OK gombra kattint, a **Biztonsági mentés célja** mellett megjelenik egy pipa, és megnyílik **Az infrastruktúra előkészítése** panel.

    ![Ha konfigurálta a biztonsági mentés célját, a következő lépés az infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. **Az infrastruktúra előkészítése** panelen kattintson **A Windows Server- vagy a Windows-ügyfél ügynökének letöltése** elemre.

    ![infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Ha Windows Server Essential verziót használ, akkor a Windows Server Essential ügynökét töltse le. Egy előugró menü rákérdez, hogy futtatni vagy menteni kívánja-e a MARSAgentInstaller.exe fájlt.

    ![MARSAgentInstaller párbeszédpanel](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. A letöltési előugró menüben kattintson a **Mentés** gombra.

    Alapértelmezés szerint az **MARSagentinstaller.exe** fájlt a rendszer a Downloads mappába menti. Amikor a telepítő végzett, megjelenik egy előugró ablak, amely rákérdez, hogy szeretné-e futtatni a telepítőt, vagy megnyitni a mappát.

    ![infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Az ügynököt még nem kell telepíteni. A tároló hitelesítő adatainak letöltése után telepítheti az ügynököt.

6. **Az infrastruktúra előkészítése** panelen kattintson a **Letöltés** elemre.

    ![a tároló hitelesítő adatainak letöltése](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    A tároló hitelesítő adatait a rendszer a Letöltések mappába menti. Miután a tároló hitelesítő adatainak letöltése befejeződött, megjelenik egy előugró ablak, amely rákérdez, hogy szeretné-e megnyitni vagy menteni a hitelesítő adatokat. Kattintson a **Mentés** gombra. Ha véletlenül a **Megnyitás** gombra kattint, hagyja, hogy sikertelen legyen a párbeszédpanel, amely megpróbálja megnyitni a tároló hitelesítő adatait. A tároló hitelesítő adatai nem nyithatók meg. Folytassa a következő lépéssel. A tároló hitelesítő adatai a Letöltések mappában találhatók.

    ![a tároló hitelesítő adatainak letöltése befejeződött](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > A tár hitelesítő adatait csak olyan helyre kell menteni, amely helyi a Windows Server, amelyen az ügynököt használni kívánja.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

> [!NOTE]
> A biztonsági mentés engedélyezése az Azure Portalon keresztül még nem érhető el. A Microsoft Azure Recovery Services ügynök használatával biztonsági mentést készíthet a Windows Server rendszerállapotáról.
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
     > Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem tud segíteni az adatok biztonsági másolatának visszaállításában. Mentse a fájlt egy biztonságos helyen. Erre szükség van a biztonsági másolat visszaállításához.
     >
     >

Az ügynök most telepítve van, és a gépe regisztrálva van a tárolóban. Készen áll a biztonsági mentés konfigurálására és ütemezésére.

## <a name="back-up-windows-server-system-state"></a>A Windows Server rendszerállapotának biztonsági mentése

A kezdeti biztonsági mentés két feladatot tartalmaz:

* A biztonsági mentés ütemezése
* Rendszerállapot biztonsági mentése első alkalommal

A kezdeti biztonsági mentés végrehajtásához használja a Microsoft Azure Recovery Services-ügynököt.

> [!NOTE]
> A Windows Server 2008 R2 rendszeren a Windows Server 2016 használatával biztonsági mentést készíthet a rendszerállapotról. A rendszerállapot biztonsági mentése nem támogatott az ügyfél SKU-ban. A rendszerállapot nem jelenik meg a Windows-ügyfelek, illetve a Windows Server 2008 SP2 rendszerű gépek esetén.
>
>

### <a name="to-schedule-the-backup-job"></a>A biztonsági mentési feladat ütemezése

1. Nyissa meg a Microsoft Azure Recovery Services-ügynököt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Az Azure Recovery Services-ügynök indítása](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. A Recovery Services-ügynökben kattintson a **Biztonsági mentés ütemezése** gombra.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. A Biztonsági mentés ütemezése varázsló Első lépések oldalán kattintson a **Tovább** gombra.

4. Az Elemek kijelölése biztonsági mentéshez oldalon kattintson az **Elemek hozzáadása** lehetőségre.

5. Válassza a **rendszerállapot** lehetőséget, majd kattintson **az OK**gombra.

6. Kattintson a **Tovább** gombra.

7. A következő lapokon válassza ki a szükséges biztonsági mentési gyakoriságot és a rendszerállapot biztonsági mentések megőrzési szabályát.

8. A Jóváhagyás lapon ellenőrizze az információkat, majd kattintson a **Befejezés** gombra.

9. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>A Windows Server rendszer állapotának biztonsági mentése első alkalommal

1. Győződjön meg arról, hogy nincsenek olyan függőben lévő frissítések a Windows Server rendszerhez, amelyek újraindítást igényelnek.

2. A Recovery Services-ügynökben kattintson a **Biztonsági mentés** gombra a hálózaton keresztüli kezdeti összehangolás befejezéséhez.

    ![Windows Server biztonsági mentés most](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. A megjelenő **biztonsági mentési elem kiválasztása** képernyőn válassza a **rendszerállapot** lehetőséget, majd kattintson a **tovább**gombra.

4. A Jóváhagyás lapon tekintse át azokat a beállításokat, amelyeket a Biztonsági másolat készítése varázsló a gép biztonsági mentéséhez fog használni. Ezután kattintson a **Biztonsági mentés** gombra.

5. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha bezárja a varázslót a biztonsági mentési folyamat befejezése előtt, a varázsló továbbra is fut a háttérben.
    > [!NOTE]
    > A MARS-ügynök az összes rendszerállapot biztonsági mentése előtt elindítja az SFC-/VERIFYONLY az előzetes ellenőrzések részeként. Ezzel a beállítással biztosíthatja, hogy a rendszerállapot részeként biztonsági másolatokat tartalmazó fájlok a Windows verziójának megfelelő verziójúak legyenek. További információ a rendszerfájl-ellenőrzési (SFC) szolgáltatásról [ebben a cikkben](https://docs.microsoft.com/windows-server/administration/windows-commands/sfc).
    >

A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

  ![IR befejezve](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Kérdése van?

Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Következő lépések

* További részletek a [Windows rendszerű gépek biztonsági mentéséről](backup-configure-vault.md).
* Most, hogy biztonsági másolatot készített a Windows Server rendszerállapotáról, [kezelheti a tárolókat és a kiszolgálókat](backup-azure-manage-windows-server.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
