---
title: Azure-beli SQL Server adatbázisának kezelése és figyelése
description: Ez a cikk az Azure-beli virtuális gépeken futó SQL Server és monitorozni a virtuális gépeken futó adatbázisokat.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 3938e26e134f7d823d8a6f6fac631ebf4442e6ab
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519136"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Biztonsági másolattal rendelkező SQL Server-adatbázisok kezelése és monitorozása

Ez a cikk az Azure-beli virtuális gépen (VM) futó, és az Azure Backup-szolgáltatás által egy Azure Backup Recovery Services-tárolóba biztonsági mentése alatt lévő SQL Server-adatbázisok kezelésével és monitorozásával kapcsolatos általános feladatokat [ismerteti.](backup-overview.md) Megtudhatja, hogyan figyelheti a feladatokat és riasztásokat, állíthatja le és folytathatja az adatbázis-védelmet, futtathatja a biztonsági mentési feladatokat, és hogyan ússhatja meg a virtuális gépek biztonsági mentésből való regisztrációját.

Ha még nem konfigurálta a biztonsági mentéseket a SQL Server adatbázisokhoz, tekintse meg a Biztonsági másolatok SQL Server Azure-beli virtuális gépeken való biztonsági [mentését.](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>Biztonsági mentési feladatok figyelése a portálon

Azure Backup biztonsági mentési feladatok alatt az összes ütemezett  és igény szerinti műveletet megjeleníti a portálon, kivéve az ütemezett naplók biztonsági mentését, mivel ezek nagyon gyakoriak is. A portálon látható feladatok közé tartozik az adatbázis-felderítés és -regisztráció, a biztonsági mentési és visszaállítási műveletek konfigurálása.

![A Biztonsági mentési feladatok portál](./media/backup-azure-sql-database/sql-backup-jobs-list.png)

A monitorozási forgatókönyvekkel kapcsolatos részletekért kattintson a [Figyelés](backup-azure-monitoring-built-in-monitor.md) a Azure Portal és a [Figyelés a](backup-azure-monitoring-use-azuremonitor.md)Azure Monitor.  

## <a name="view-backup-alerts"></a>Biztonsági mentési riasztások megtekintése

Mivel a naplók biztonsági mentése 15 percenként történik, a biztonsági mentési feladatok monitorozása fárasztó lehet. Azure Backup e-mail-riasztások küldésével megkönnyíti a monitorozást. Az e-mailes riasztások a következőek:

- Az összes biztonsági mentési hiba esetén aktiválódik.
- Adatbázisszinten összesíteni hibakód alapján.
- Csak az adatbázis első sikertelen biztonsági mentésére van elküldve.

Az adatbázis biztonsági mentési riasztásának figyelése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A tároló irányítópultján válassza a Biztonsági **mentési riasztások lehetőséget.**

   ![Biztonsági mentési riasztások kiválasztása](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Adatbázis védelmének SQL Server le

A biztonsági SQL Server többféleképpen is leállíthatja:

- Állítsa le az összes jövőbeli biztonsági mentési feladat, és törölje az összes helyreállítási pontot.
- Állítsa le az összes jövőbeli biztonsági mentési feladatokat, és hagyja érintetlenül a helyreállítási pontokat.

Ha úgy dönt, hogy elhagyja a helyreállítási pontokat, tartsa szem előtt az alábbi adatokat:

- Az összes helyreállítási pont örökre érintetlen marad, és minden adatmegőrzési folyamat leáll a védelem során.
- A védett példányért és a felhasznált tárterületért díjat számítunk fel. További információ: díjszabás [Azure Backup.](https://azure.microsoft.com/pricing/details/backup/)
- Ha a biztonsági mentések leállítása nélkül töröl egy adatforrást, az új biztonsági mentések sikertelenek lesznek. A régi helyreállítási pontok a szabályzatnak megfelelően lejárnak, de a legutóbbi helyreállítási pont mindig meg lesz megőrizve, amíg le nem állítsa a biztonsági másolatokat, és nem törli az adatokat.

Adatbázis védelmének leállítása:

1. A tároló irányítópultján válassza a Biztonsági **mentési elemek lehetőséget.**

2. A **Biztonságimásolat-kezelés típusa területen** válassza az SQL az **Azure-beli virtuális gépen lehetőséget.**

    ![Sql kiválasztása Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Válassza ki azt az adatbázist, amelyhez le szeretné állítani a védelmet.

    ![A védelem leállításához válassza ki az adatbázist](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Az adatbázis menüjében válassza a Biztonsági **mentés leállítása lehetőséget.**

    ![Válassza a Biztonsági mentés leállítása lehetőséget](./media/backup-azure-sql-database/stop-db-button.png)

5. A Biztonsági **mentés leállítása menüben** válassza ki, hogy meg szeretné-e őrizni vagy törölni szeretné az adatokat. Ha szeretné, adjon meg egy okot és megjegyzéseket.

    ![Adatok megőrzése vagy törlése a Biztonsági mentés leállítása menüben](./media/backup-azure-sql-database/stop-backup-button.png)

6. Válassza a **Biztonsági mentés leállítása lehetőséget.**

> [!NOTE]
>
>Az Adatok törlése lehetőséggel kapcsolatos további információkért tekintse meg az alábbi gyakori kérdéseket:
>
>- [Ha törlök egy adatbázist egy automatikus védelemmel ellátott példányról, mi történik a biztonsági másolatokkal?](faq-backup-sql-server.yml#if-i-delete-a-database-from-an-autoprotected-instance--what-will-happen-to-the-backups-)
>- [Ha leállítom egy automatikusan védett adatbázis biztonsági mentési műveletét, mi lesz a viselkedése?](faq-backup-sql-server.yml#if-i-change-the-name-of-the-database-after-it-has-been-protected--what-will-be-the-behavior-)
>
>

## <a name="resume-protection-for-a-sql-database"></a>SQL-adatbázis védelmének folytatása

Ha leállítja az SQL-adatbázis védelmét,  és a Biztonsági másolatok adatainak megőrzése lehetőséget választja, később folytathatja a védelmet. Ha nem őrzi meg a biztonsági mentési adatokat, nem folytathatja a védelmet.

SQL-adatbázis védelmének folytatása:

1. Nyissa meg a biztonsági mentési elemet, és válassza a **Biztonsági mentés folytatása lehetőséget.**

    ![Az adatbázis-védelem folytatásához válassza a Biztonsági mentés folytatása lehetőséget](./media/backup-azure-sql-database/resume-backup-button.png)

2. A Biztonsági **mentési szabályzat menüben** válasszon ki egy szabályzatot, majd válassza a **Mentés lehetőséget.**

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentések futtatása

Az igény szerinti biztonsági mentések különböző típusait futtathatja:

- Teljes biztonsági mentés
- Csak másolásos teljes biztonsági mentés
- Különbségi biztonsági mentés
- Naplóalapú biztonsági mentés

Bár meg kell adnia a csak másolásos teljes biztonsági mentés megőrzési időtartamát, az igény szerinti teljes biztonsági mentés megőrzési ideje automatikusan a jelenlegi időponttól 45 napra lesz beállítva.

További információ: biztonsági [SQL Server készítése.](backup-architecture.md#sql-server-backup-types)

## <a name="modify-policy"></a>Szabályzat módosítása

Módosítsa a szabályzatot a biztonsági mentés gyakoriságának vagy megőrzési tartományának módosításához.

> [!NOTE]
> A megőrzési időtartam bármely módosítása visszamenőlegesen alkalmazva lesz az összes régebbi helyreállítási pontra az újak mellett.

A tároló irányítópultján válassza a **Biztonsági** mentési házirendek kezelése lehetőséget, és válassza ki a szerkeszteni kívánt  >   szabályzatot.

  ![Biztonsági mentési szabályzat kezelése](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Biztonsági mentési szabályzat módosítása](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

A szabályzat módosítása hatással lesz az összes társított biztonsági mentési elemre, és aktiválja a **megfelelő védelmi feladatokat.**

### <a name="inconsistent-policy"></a>Inkonzisztens szabályzat

Egyes biztonsági mentési elemek esetén  a szabályzat módosítása néha inkonzisztens szabályzatverzióhoz vezethet. Ez akkor fordul elő, **ha** a megfelelő védelmi feladat meghiúsul a biztonsági mentési elemen a szabályzat módosítása művelet aktiválása után. Ez a következőképpen jelenik meg a biztonsági mentési elem nézetben:

  ![Inkonzisztens szabályzat](./media/backup-azure-sql-database/inconsistent-policy.png)

Az érintett elemek szabályzatverzióját egyetlen kattintással kijavíthatja:

  ![Inkonzisztens szabályzat kijavítva](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Új példány regisztrációjának SQL Server meg

A védelem letiltása SQL Server, de a tároló törlése előtt törölje a tárolópéldány regisztrációját:

1. A tároló irányítópultjának Kezelés **területén válassza** a Biztonsági mentési **infrastruktúra lehetőséget.**  

   ![Válassza a Biztonsági mentési infrastruktúra lehetőséget](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. A **Felügyeleti kiszolgálók alatt** válassza a Védett kiszolgálók **lehetőséget.**

   ![Védett kiszolgálók kiválasztása](./media/backup-azure-sql-database/protected-servers.png)

3. A **Protected Servers (Védett kiszolgálók) mezőben** válassza ki a kiszolgálót, amelyről le kell mondania a regisztrációt. A tároló törléséhez törölnie kell az összes kiszolgáló regisztrációját.

4. Kattintson a jobb gombbal a védett kiszolgálóra, és válassza a **Regisztráció törlése lehetőséget.**

   ![Válassza a Törlés lehetőséget](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Bővítmény újra regisztrálása a SQL Server VM

Előfordulhat, hogy a virtuális gép számítási feladatainak bővítménye hatással lehet egy vagy több okból kifolyólag. Ilyen esetekben a virtuális gépen aktivált összes művelet meghiúsul. Ezután előfordulhat, hogy újra regisztrálnia kell a bővítményt a virtuális gépen. A **re-register művelet** újratelepíti a számítási feladat biztonsági mentési bővítményét a virtuális gépen a műveletek folytatásához. Ezt a beállítást a Backup Infrastructure (Biztonsági mentési **infrastruktúra) alatt találja** meg a Recovery Services-tárolóban.

![Védett kiszolgálók a Biztonsági mentési infrastruktúra alatt](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Ezt a lehetőséget körültekintően használja. Ha egy már kifogástalan állapotú bővítvű virtuális gépen aktiválódik, ez a művelet újraindítja a bővítményt. Ez az összes folyamatban lévő feladat meghiúsulhat. Az újra regisztráló művelet aktiválása [előtt](backup-sql-server-azure-troubleshoot.md#re-registration-failures) ellenőrizze, hogy a tünetek valamelyike jelentkezik-e.

## <a name="next-steps"></a>Következő lépések

További információ: [Biztonsági mentések hibaelhárítása SQL Server adatbázison.](backup-sql-server-azure-troubleshoot.md)
