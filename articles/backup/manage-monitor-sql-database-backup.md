---
title: SQL Server adatbázisok kezelése és figyelése egy Azure-beli virtuális gépen – Azure Backup
description: Ez a cikk az Azure-beli virtuális gépeken futó SQL Server adatbázisok felügyeletét és figyelését ismerteti.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: dacurwin
ms.openlocfilehash: 7440859748a613f7d6af751974e07289175ed7ac
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968372"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>SQL Server adatbázisok biztonsági mentésének kezelése és figyelése

Ez a cikk az Azure-beli virtuális gépen (VM) futó SQL Server adatbázisok felügyeletének és figyelésének általános feladatait ismerteti, amelyek biztonsági mentése a [Azure Backup](backup-overview.md) szolgáltatás által Azure Backup Recovery Services-tárolóba történik. Megismerheti a feladatok és a riasztások figyelését, az adatbázis-védelem leállítását és folytatását, a biztonsági mentési feladatok futtatását és a virtuális gépek biztonsági másolatokból való regisztrációjának megszüntetését.

Ha még nem konfigurált biztonsági másolatokat a SQL Server adatbázisokhoz, tekintse [meg a SQL Server adatbázisok biztonsági mentése Azure-beli virtuális gépeken](backup-azure-sql-database.md) című témakört.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Manuális biztonsági mentési feladatok figyelése a portálon

Azure Backup megjeleníti az összes manuálisan aktivált feladatot a **biztonsági mentési feladatok** portálon. A portálon megjelenő feladatok közé tartozik az adatbázis-felderítés és a regisztrálás, valamint a biztonsági mentési és visszaállítási műveletek.

![A biztonsági mentési feladatok portál](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> A **biztonsági mentési feladatok** portálon nem jelennek meg ütemezett biztonsági mentési feladatok. Az ütemezett biztonsági mentési feladatok figyeléséhez használja a SQL Server Management Studio a következő szakaszban leírtak szerint.
>

A figyelési forgatókönyvekkel kapcsolatos részletekért tekintse meg [a figyelés a Azure Portal és a](backup-azure-monitoring-built-in-monitor.md) [figyelés Azure monitor használatával](backup-azure-monitoring-use-azuremonitor.md)című részt.  

## <a name="view-backup-alerts"></a>Biztonsági mentési riasztások megtekintése

Mivel a naplók biztonsági mentései 15 percenként történnek, a biztonsági mentési feladatok unalmasak lehetnek. A Azure Backup e-mail-riasztások küldésével megkönnyíti a figyelést. E-mail-riasztások:

- Az összes biztonsági mentési hiba miatt aktiválódik.
- Az adatbázis szintjén összesítve hibakód jelenik meg.
- Csak az adatbázis első biztonsági mentési hibája miatt lett elküldve.

Az adatbázis biztonsági mentési értesítéseinek figyelése:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. A tároló Irányítópultján kattintson a **riasztások és események**elemre.

   ![Riasztások és események kiválasztása](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. A **riasztások és események**területen válassza a **biztonsági mentési riasztások**lehetőséget.

   ![Biztonsági mentési riasztások kiválasztása](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server-adatbázis védelmének leállítása

Több módon is leállíthatja a SQL Server-adatbázisok biztonsági mentését:

- Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törölje az összes helyreállítási pontot.
- Állítsa le az összes jövőbeli biztonsági mentési feladatot, és hagyja érintetlenül a helyreállítási pontokat.

Ha úgy dönt, hogy kihagyja a helyreállítási pontokat, tartsa szem előtt az alábbi adatokat:

- Az összes helyreállítási pont érintetlen marad, és az összes törlés leáll a védelem leállításakor az adatmegőrzés során.
- A védett példány és a felhasznált tárterület után díjat számítunk fel. További információ: [Azure Backup díjszabása](https://azure.microsoft.com/pricing/details/backup/).
- Ha töröl egy adatforrást a biztonsági mentések leállítása nélkül, az új biztonsági mentések sikertelenek lesznek.

Az adatbázis védelmének leállítása:

1. A tároló irányítópultján válassza a **biztonsági másolati elemek**lehetőséget.

2. A **biztonsági mentési felügyelet típusa**területen válassza **az SQL lehetőséget az Azure virtuális gépen**.

    ![SQL kiválasztása az Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Válassza ki azt az adatbázist, amelynek a védelmét le szeretné állítani.

    ![Válassza ki az adatbázist a védelem leállításához](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Az adatbázis menüben válassza a **biztonsági mentés leállítása**lehetőséget.

    ![Válassza a biztonsági mentés leállítása lehetőséget.](./media/backup-azure-sql-database/stop-db-button.png)

5. A **biztonsági mentés leállítása** menüben válassza ki, hogy szeretné-e megőrizni vagy törölni az adattárolást. Ha szeretné, adjon meg egy okot és egy megjegyzést.

    ![A biztonsági mentés leállítása menüben lévő adat megőrzése vagy törlése](./media/backup-azure-sql-database/stop-backup-button.png)

6. Válassza a **biztonsági mentés leállítása**lehetőséget.

> [!NOTE]
>
>Az adatok törlése lehetőséggel kapcsolatos további információkért tekintse meg az alábbi gyakori kérdéseket:
>
>- [Ha törölek egy adatbázist egy automatikusan védett példányból, mi fog történni a biztonsági másolatokban?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Ha leállítom egy automatikusan védett adatbázis biztonsági mentési műveletét, mi lesz a viselkedése?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>SQL Database-adatbázis védelmének folytatása

Ha leállítja az SQL Database-adatbázis védelmét, ha a **biztonsági mentési adat megőrzése** lehetőséget választja, később folytathatja a védelmet. Ha nem tartja meg a biztonsági mentési adatait, nem folytathatja a védelmet.

Egy SQL Database-adatbázis védelmének folytatása:

1. Nyissa meg a biztonsági mentési elemet, és válassza a **biztonsági mentés folytatása**lehetőséget.

    ![Az adatbázis-védelem folytatásához válassza a biztonsági mentés folytatása lehetőséget.](./media/backup-azure-sql-database/resume-backup-button.png)

2. A **biztonsági mentési házirend** menüben válasszon ki egy házirendet, majd kattintson a **Mentés**gombra.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

Különböző típusú, igény szerinti biztonsági mentést is futtathat:

- Teljes biztonsági mentés
- Csak másolás – teljes biztonsági mentés
- Különbségi biztonsági mentés
- Naplóalapú biztonsági mentés

Míg a csak a teljes biztonsági mentés megőrzési időtartamát kell megadnia, az ad-hoc teljes biztonsági mentés megőrzési tartománya automatikusan 45 napra lesz állítva az aktuális időpontnál.

További információ: [SQL Server biztonsági mentési típusok](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>SQL Server példány regisztrációjának törlése

A védelem letiltása, de a tár törlése előtt törölje a SQL Server példány regisztrációját:

1. A tároló irányítópultjának **kezelés**területén válassza a **biztonsági mentési infrastruktúra**elemet.  

   ![Biztonsági mentési infrastruktúra kiválasztása](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. A **felügyeleti kiszolgálók**területen válassza a **védett kiszolgálók**elemet.

   ![Védett kiszolgálók kiválasztása](./media/backup-azure-sql-database/protected-servers.png)

3. A **védett kiszolgálók**lapon válassza ki a regisztrálni kívánt kiszolgálót. A tár törléséhez törölnie kell az összes kiszolgálót.

4. Kattintson a jobb gombbal a védett kiszolgálóra, és válassza a **Regisztráció törlése**lehetőséget.

   ![Törlés kiválasztása](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>Házirend módosítása

Módosítsa a szabályzatot a biztonsági mentés gyakoriságának vagy a megőrzési tartománynak a módosításához.

> [!NOTE]
> A megőrzési időtartam változásai visszamenőlegesen lesznek alkalmazva az újakon kívül az összes korábbi helyreállítási pontra.

A tároló irányítópultján lépjen a > **biztonsági mentési szabályzatok** **kezelése** elemre, és válassza ki a szerkeszteni kívánt szabályzatot.

  ![Biztonsági mentési szabályzat kezelése](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Biztonsági mentési szabályzat módosítása](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

A szabályzat módosítása hatással lesz az összes kapcsolódó biztonsági mentési elemre, és elindítja a megfelelő **Konfigurálás-védelmi** feladatokat.

### <a name="inconsistent-policy"></a>Inkonzisztens házirend

Előfordulhat, hogy a házirend-módosítási művelet bizonyos biztonsági másolati elemek esetében nem **konzisztens** házirend-verziót eredményezhet. Ez akkor fordulhat elő, ha a biztonsági mentési elemhez tartozó megfelelő **konfigurálási** művelet meghiúsult a házirend-módosítási művelet elindítása után. A biztonsági mentési elem nézetben a következőképpen jelenik meg:

  ![Inkonzisztens házirend](./media/backup-azure-sql-database/inconsistent-policy.png)

Egy kattintással kijavíthatja az összes érintett elem szabályzatának verzióját:

  ![Inkonzisztens házirend javítása](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>A bővítmény újbóli regisztrálása a SQL Server VM

Időnként előfordulhat, hogy a virtuális gépen a munkaterhelés-bővítmény hatással lehet az egyik ok vagy a másikra. Ilyen esetekben a virtuális gépen aktivált összes művelet sikertelen lesz. Előfordulhat, hogy újra regisztrálnia kell a bővítményt a virtuális gépen. A művelet **ismételt regisztrálása** újratelepíti a munkaterhelési biztonsági mentési bővítményt a virtuális gépen a folytatáshoz.

Ezt a beállítást körültekintően használhatja. Ha egy már kifogástalan állapotú virtuális gépen aktiválódik, ez a művelet a bővítmény újraindítását eredményezi. Ez azt eredményezheti, hogy az összes folyamatban lévő feladat meghiúsul. Az újbóli regisztrálási művelet megkezdése előtt kérjük, ellenőrizze, hogy van-e egy vagy több [tünet](backup-sql-server-azure-troubleshoot.md#re-registration-failures) .

## <a name="next-steps"></a>Következő lépések

További információ: SQL Server- [adatbázis biztonsági másolatainak hibáinak megoldása](backup-sql-server-azure-troubleshoot.md).
