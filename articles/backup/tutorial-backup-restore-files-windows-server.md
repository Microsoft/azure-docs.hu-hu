---
title: 'Oktatóanyag: elemek helyreállítása Windows Serverre'
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Microsoft Azure Recovery Services Agent (MARS) ügynököt az Azure-beli elemek Windows Serverre történő helyreállításához.
ms.topic: tutorial
ms.date: 02/14/2018
ms.custom: mvc
ms.openlocfilehash: 746c901747cf1c0b87612a31fbabcb657d5c4a0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88263112"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Azure-beli fájlok helyreállítása Windows Serverre

Az Azure Backup lehetővé teszi kiválasztott elemek helyreállítását a Windows Server biztonsági másolataiból. Az egyes fájlok visszaállítása akkor hasznos megoldás, ha gyorsan szeretné visszaállítani a véletlenül törölt fájlokat. Ez az oktatóanyag bemutatja, hogyan használhatja a Microsoft Azure Recovery Services- (MARS-) ügynököt egyes elemek helyreállítására az Azure-ban készített biztonsági másolatokból. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> * Csak egyes elemek helyreállításának indítása
> * Helyreállítási pont kiválasztása
> * Elemek visszaállítása a helyreállítási pontból

Ez az oktatóanyag feltételezi, hogy már végrehajtotta a [Windows Serverről az Azure](backup-windows-with-mars-agent.md) -ba történő biztonsági mentés lépéseit, és legalább egy biztonsági másolatot készít a Windows Server-fájljairól az Azure-ban.

## <a name="initiate-recovery-of-individual-items"></a>Csak egyes elemek helyreállításának indítása

A Microsoft Azure Recovery Services- (MARS-) ügynökkel együtt egy Microsoft Azure Backup nevű hasznos felhasználói felületi varázsló is települ. A Microsoft Azure Backup varázsló együttműködik a Microsoft Azure Recovery Services- (MARS-) ügynökkel az Azure-ban tárolt helyreállítási pontok biztonsági másolataiban lévő adatok lekérésén. A Microsoft Azure Backup varázslóval azonosíthatja azokat a fájlokat vagy mappákat, amelyeket vissza kíván állítani a Windows Serverre.

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Beépülő modul Microsoft Azure Backup](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. A varázslóban válassza az **adatok helyreállítása** lehetőséget az ügynök konzoljának **műveletek paneljén** az **adatok helyreállítása** varázsló elindításához.

    ![Adatok helyreállítása elem kiválasztása](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. A **első lépések** lapon válassza ki **ezt a kiszolgálót (kiszolgáló neve)** , majd kattintson a **tovább** gombra.

4. A **helyreállítási mód kiválasztása** lapon válassza az **egyes fájlok és mappák** lehetőséget, majd a **tovább** gombra kattintva indítsa el a helyreállítási pont kiválasztási folyamatát.

5. A **kötet és dátum kiválasztása** lapon válassza ki azt a kötetet, amely a visszaállítani kívánt fájlokat vagy mappákat tartalmazza, majd válassza a **Csatlakoztatás** lehetőséget. Válassza ki a dátumot, és válassza ki a helyreállítási pontnak megfelelő időpontot a legördülő menüből. A **félkövérrel** szedett dátumok azt jelzik, hogy legalább egy helyreállítási pont áll rendelkezésre az adott napon.

    ![Kötet és dátum kiválasztása](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    A **Csatlakoztatás** lehetőség kiválasztásakor a Azure Backup lemezként elérhetővé teszi a helyreállítási pontot. Keresse meg a fájlokat a lemezen, és végezze el a helyreállítást.

## <a name="restore-items-from-a-recovery-point"></a>Elemek visszaállítása a helyreállítási pontból

1. A helyreállítási kötet csatlakoztatása után a **Tallózás** gombra kattintva nyissa meg a Windows Intézőt, és keresse meg a helyreállítani kívánt fájlokat és mappákat.

    ![Tallózás kiválasztása](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    A fájlokat közvetlenül a helyreállítási kötetből is megnyithatja és ellenőrizheti.

2. A Windows Intézőben másolja a visszaállítani kívánt fájlokat és mappákat, majd illessze be őket a kiszolgálón található kívánt helyre.

    ![Fájlok és mappák másolása](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Ha befejezte a fájlok és mappák visszaállítását, az **adatok helyreállítása** varázsló **Tallózás és helyreállítási fájlok** lapján válassza a **Leválasztás** lehetőséget.

    ![Válassza a leválasztás lehetőséget](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. Az **Igen** gombra kattintva erősítse meg, hogy le kívánja választani a kötetet.

    A pillanatkép leválasztása után az ügynökkonzol **Feladatok** ablaktábláján a **Feladat befejezve** üzenet jelenik meg.

## <a name="next-steps"></a>Következő lépések

Ezzel befejeződik az oktatóanyag-sorozat, amely a Windows Server adatainak az Azure-ba történő biztonsági mentését és visszaállítását ismerteti. Az Azure Backuppal kapcsolatos további információért tekintse meg a titkosított virtuális gépek biztonsági mentéséhez készült PowerShell-mintát.

> [!div class="nextstepaction"]
> [Titkosított virtuális gép biztonsági mentése](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
