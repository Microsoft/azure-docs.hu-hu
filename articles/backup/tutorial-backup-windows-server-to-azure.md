---
title: Oktatóanyag – a Windows Server biztonsági mentése az Azure-ba
description: Ez az oktatóanyag részletesen ismerteti a helyszíni Windows Serverek helyreállítási tárba történő biztonsági mentését.
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: d2990b5950cf8812367c3a59c6cace39e4085e2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88261905"
---
# <a name="back-up-windows-server-to-azure"></a>Windows Server biztonsági mentése az Azure-ba

Az Azure Backuppal megvédheti a Windows Servert a sérülésektől, támadásoktól és katasztrófáktól. Az Azure Backup tartalmaz egy egyszerű eszközt, amelynek a neve Microsoft Azure Recovery Services- (MARS-) ügynök. A MARS-ügynök a Windows Serverre települ, és a Windows Server rendszerállapotán keresztül megvédi a fájlokat, a mappákat és a kiszolgáló konfigurációs információit. Ez az oktatóanyag azt ismerteti, hogyan készíthető biztonsági másolat a Windows Serverről az Azure-ba a MARS-ügynökkel. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> * A MARS-ügynök letöltése és beállítása
> * A kiszolgáló biztonsági mentési időpontjainak és a másolatok megőrzési ütemezésének konfigurálása
> * Igény szerinti biztonsági mentés végrehajtása

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Mielőtt biztonsági másolatot készíthetne a Windows Serverről, létre kell hoznia egy helyet a biztonsági másolatok vagy helyreállítási pontok tárolásához. A [helyreállítási tár](backup-azure-recovery-services-vault-overview.md) egy olyan Azure-beli tároló, amely a Windows Server biztonsági másolatait tárolja. Az alábbi lépésekkel hozhat létre helyreállítási tárt az Azure Portalon.

1. A bal oldali menüben válassza a **Minden szolgáltatás** elemet, és a szolgáltatások listájába írja be a **Recovery Services** kifejezést. Válassza ki **Recovery Services** tárolókat.

   ![Recovery Services-tároló megnyitása](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. A **Recovery Services** -tárolók menüben válassza a **Hozzáadás** lehetőséget.

   ![Adja meg a tár adatait](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. A **Helyreállítási tár** menüben:

    * Írja be a *MyRecoveryServicesVault* **nevet**.
    * Megjelenik az aktuális előfizetési azonosító az **Előfizetés** mezőben.
    * Az **Erőforráscsoport** mezőben válassza a **Meglévő használata** és a *myResourceGroup* elemet. Ha a *myResourceGroup* nem létezik, válassza ki az **Új létrehozása** elemet, és írja be a *myResourceGroup* kifejezést.
    * A **Hely** legördülő menüből válassza a *Nyugat-Európa* elemet.
    * Válassza a **Létrehozás** lehetőséget a Recovery Services-tároló létrehozásához.

Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában.

## <a name="download-recovery-services-agent"></a>A Recovery Services-ügynök letöltése

A Microsoft Azure Recovery Services- (MARS-) ügynök egy társítást hoz létre a Windows Server és a helyreállítási tár között. Az alábbi eljárás azt ismerteti, hogyan töltheti le az ügynököt a kiszolgálóra.

1. A helyreállítási tárak listájából válassza a **myRecoveryServicesVault** tárolót az irányítópult megnyitásához.

   ![Válassza ki a tárolót az irányítópult megnyitásához](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. A tároló irányítópultjának menüjében válassza a **biztonsági mentés** lehetőséget.

3. A **Biztonsági mentés célja** menüben:

   * **hol fut a munkaterhelése?**, válassza **a** helyszíni lehetőséget.
   * a **Miről szeretne biztonsági másolatot készíteni?** pontnál válassza a **Fájlok és mappák** és a **Rendszerállapot** elemet

   ![Biztonsági mentés célja menü](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Válassza az **infrastruktúra előkészítése** lehetőséget az **infrastruktúra előkészítése** menü megnyitásához.

5. Az **infrastruktúra előkészítése** menüben válassza a **Windows Server vagy a Windows-ügyfél ügynökének letöltése** lehetőséget a *MARSAgentInstaller.exe* letöltéséhez.

    ![Ügynök letöltése a Windows Server vagy a Windows-ügyfél számára](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    A telepítő megnyit egy külön böngészőablakot, és letölti a **MARSAgentInstaller.exe** fájlt.

6. A letöltött fájl futtatása előtt az infrastruktúra előkészítése menüben válassza a **Letöltés** lehetőséget, és mentse a tároló **hitelesítő adatait** tartalmazó fájlt. A tároló hitelesítő adataira szükség van a MARS Agent Recovery Services-tárolóhoz való csatlakoztatásához.

    ![A tároló hitelesítő adatainak letöltése](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

1. Keresse meg a letöltött **MARSagentinstaller.exe** fájlt, és kattintson rá duplán.
2. Ekkor megjelenik a **Microsoft Azure Recovery Services-ügynök telepítővarázslója**. A varázsló lépéseit követve adja meg a következő információkat, amikor a rendszer kéri, és válassza a **regisztráció** lehetőséget.
   * A telepítés és a gyorsítótár mappájának helye.
   * A proxykiszolgáló információi, ha proxykiszolgálóval csatlakozik az internethez.
   * Az Ön felhasználóneve és jelszava, ha hitelesített proxyt használ.

     ![Microsoft Azure Recovery Services ügynök telepítővarázslója](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. A varázsló végén válassza a **Folytatás a regisztrációhoz** lehetőséget, és adja meg az előző eljárás során letöltött tároló **hitelesítő adatait** tartalmazó fájlt.

4. Amikor a rendszer kéri, adja meg a Windows Server biztonsági másolatainak titkosításához szükséges jelszót. Mentse a jelszót biztonságos helyen, mivel a Microsoft nem tudja helyreállítani a jelszót, ha az elveszett.

5. Válassza a **Befejezés** gombot.

## <a name="configure-backup-and-retention"></a>Biztonsági mentés és megőrzés konfigurálása

A Microsoft Azure Recovery Services-ügynökkel ütemezheti a Windows Server Azure-ba történő biztonsági mentéseit. Hajtsa végre a következő lépéseket azon a kiszolgálón, amelyre letöltötte az ügynököt.

1. Nyissa meg a Microsoft Azure Recovery Services-ügynököt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

2. A Recovery Services-ügynök konzolján válassza a **Műveletek ablaktábla** **biztonsági mentésének időzítése** elemét.

    ![Biztonsági mentés időzítése](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. A **tovább** gombra kattintva navigáljon az **elemek kijelölése a biztonsági mentéshez** lapon.

4. Válassza az **elemek hozzáadása** lehetőséget, majd a megnyíló párbeszédpanelen válassza ki a **rendszerállapotot** és a biztonsági mentéshez használni kívánt fájlokat vagy mappákat. Ez után válassza az **OK** gombot.

5. Kattintson a **Tovább** gombra.

6. A **biztonsági mentés ütemezésének megadása (rendszerállapot)** lapon adja meg azt az időpontot vagy hetet, amikor a biztonsági mentéseket rendszerállapotba kell indítani, majd válassza a **tovább** lehetőséget.

7. A **megőrzési szabály kiválasztása (rendszerállapot)** lapon válassza ki a rendszerállapot biztonsági másolatának megőrzési házirendjét, és válassza a **tovább** lehetőséget.

8. Hasonló módon válassza ki a kijelölt fájlok és mappák biztonsági mentési ütemezését és megőrzési szabályzatát.

9. A **kezdeti biztonsági mentés típusának kiválasztása** lapon válassza az **automatikusan a hálózaton keresztül** lehetőséget, és kattintson a **tovább** gombra.

10. A **jóváhagyás** lapon tekintse át az információkat, majd kattintson a **Befejezés gombra**.

11. Miután a varázsló befejezte a biztonsági mentési ütemterv létrehozását, válassza a **Bezárás** lehetőséget.

## <a name="perform-an-on-demand-backup"></a>Igény szerinti biztonsági mentés elvégzése

Létrehozta a biztonsági mentési feladatok futtatásának ütemtervét. Azonban nem készített biztonsági másolatot a kiszolgálóról. Ez egy vész-helyreállítási ajánlott eljárás egy igény szerinti biztonsági mentés futtatásához, hogy biztosítsa az adatrugalmasságot a kiszolgáló számára.

1. A Microsoft Azure Recovery Services-ügynök konzolján válassza a **biztonsági mentés most** lehetőséget.

    ![Biztonsági mentés most](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. A **most biztonsági mentés** varázslóban válassza ki a **fájlokat és mappákat** vagy a **rendszerállapotot** , amelyről biztonsági másolatot szeretne készíteni, majd válassza a **tovább** lehetőséget.
3. A **Jóváhagyás** lapon tekintse át azokat a beállításokat, amelyeket az **Azonnali biztonsági mentés** varázsló a kiszolgáló biztonsági mentéséhez fog használni. Ezután válassza a **biztonsági mentés** lehetőséget.
4. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha bezárja a varázslót a biztonsági mentési folyamat befejezése előtt, a varázsló továbbra is fut a háttérben.
5. A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a MARS-ügynökkonzol **Feladatok** ablaktáblájában.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon:

> [!div class="checklist"]
>
> * Recovery Services-tároló létrehozása
> * A Microsoft Azure Recovery Services-ügynök letöltése
> * Az ügynök telepítése
> * A Windows Server biztonsági mentésének konfigurálása
> * Igény szerinti biztonsági mentés elvégzése

Folytassa a következő oktatóanyaggal az Azure-beli fájlok Windows Serverre történő helyreállításához

> [!div class="nextstepaction"]
> [Azure-beli fájlok visszaállítása Windows Serverre](./tutorial-backup-restore-files-windows-server.md)
