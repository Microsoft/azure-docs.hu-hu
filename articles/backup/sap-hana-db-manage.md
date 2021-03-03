---
title: Azure-beli virtuális gépeken lévő SAP HANA adatbázisok biztonsági mentésének kezelése
description: Ebből a cikkből megtudhatja, hogyan kezelheti és figyelheti az Azure-beli virtuális gépeken futó SAP HANA adatbázisok felügyeletére és figyelésére vonatkozó általános feladatokat.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 54d3341a83873ad3cc50815f04a0b252bb44438e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703766"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Biztonsági másolattal rendelkező SAP HANA-adatbázisok kezelése és monitorozása

Ez a cikk az Azure-beli virtuális gépen (VM) futó SAP HANA adatbázisok felügyeletének és figyelésének általános feladatait ismerteti, amelyek biztonsági mentése a [Azure Backup](./backup-overview.md) szolgáltatás által Azure Backup Recovery Services-tárolóba történik. Megismerheti a feladatok és a riasztások figyelését, az igény szerinti biztonsági mentés elindítását, a házirendek szerkesztését, az adatbázis-védelem leállítását és folytatását, valamint a virtuális gépek biztonsági másolatokból való regisztrációját.

Ha még nem konfigurálta a biztonsági mentéseket a SAP HANA adatbázisokhoz, tekintse [meg a SAP HANA adatbázisok biztonsági mentése Azure virtuális gépeken](./backup-azure-sap-hana-database.md)című témakört.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Manuális biztonsági mentési feladatok figyelése a portálon

Azure Backup megjeleníti az összes manuálisan aktivált feladatot a Azure Portal **biztonsági mentési feladatok** szakaszában.

![Biztonsági mentési feladatok szakasz](./media/sap-hana-db-manage/backup-jobs.png)

A portálon megjelenő feladatok közé tartozik az adatbázis-felderítés és a regisztrálás, valamint a biztonsági mentési és visszaállítási műveletek. Az ütemezett feladatok, beleértve a naplók biztonsági mentését, nem jelennek meg ebben a szakaszban. A SAP HANA natív ügyfelekről (Studio/cockpit/DBA pilótafülke) manuálisan indított biztonsági mentéseket még nem jeleníti meg.

![Biztonsági mentési feladatok listája](./media/sap-hana-db-manage/backup-jobs-list.png)

A figyeléssel kapcsolatos további információkért tekintse meg [a Azure Portal](./backup-azure-monitoring-built-in-monitor.md) és a [figyelés a Azure monitor használatával](./backup-azure-monitoring-use-azuremonitor.md)című témakört.

## <a name="view-backup-alerts"></a>Biztonsági mentési riasztások megtekintése

A riasztások a SAP HANA adatbázisok biztonsági mentésének egyszerű figyelését jelentik. A riasztások segítséget nyújtanak a lehető legtöbbet a biztonsági másolatok által generált események sokaságának elvesztése nélkül. Azure Backup lehetővé teszi a riasztások beállítását, és a következőképpen figyelhetők:

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
* A tároló irányítópultján válassza a **biztonsági mentési riasztások** lehetőséget.

  ![Biztonsági mentési riasztások a tároló irányítópultján](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Ekkor láthatja a riasztásokat:

  ![Biztonsági mentési riasztások listája](./media/sap-hana-db-manage/backup-alerts-list.png)

* Válassza ki a riasztásokat a további részletek megtekintéséhez:

  ![Riasztás részletei](./media/sap-hana-db-manage/alert-details.png)

Napjainkban Azure Backup lehetővé teszi a riasztások e-mailben történő küldését. Ezek a riasztások a következők:

* Az összes biztonsági mentési hiba miatt aktiválódik.
* Az adatbázis szintjén összesítve hibakód jelenik meg.
* Csak az adatbázis első biztonsági mentési hibája miatt lett elküldve.

A figyeléssel kapcsolatos további információkért tekintse meg [a Azure Portal](./backup-azure-monitoring-built-in-monitor.md) és a [figyelés a Azure monitor használatával](./backup-azure-monitoring-use-azuremonitor.md)című témakört.

## <a name="management-operations"></a>Felügyeleti műveletek

A Azure Backup egy biztonsági másolattal rendelkező SAP HANA adatbázis felügyeletét teszi egyszerűbbé az általa támogatott felügyeleti műveletek sokaságával. A következő szakaszokban részletesebben ismertetjük ezeket a műveleteket.

### <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentések futtatása

A biztonsági mentések a szabályzat ütemezésével összhangban futnak. Az igény szerinti biztonsági mentést az alábbi módon futtathatja:

1. A tároló menüben válassza a **biztonsági másolati elemek elemet**.
2. A **biztonsági másolati elemek** területen válassza ki a SAP HANA adatbázist futtató virtuális gépet, majd válassza a **biztonsági mentés** lehetőséget.
3. A **biztonsági mentés** területen válassza ki a végrehajtani kívánt biztonsági mentés típusát. Ez után válassza az **OK** gombot. Ezt a biztonsági mentést a biztonsági másolathoz tartozó szabályzatnak megfelelően megőrzi a rendszer.
4. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon > **biztonsági mentési feladatok**  >  **folyamatban** vannak. Az adatbázis méretétől függően a kezdeti biztonsági mentés hosszabb időt is igénybe vehet.

Alapértelmezés szerint az igény szerinti biztonsági mentések megőrzése 45 nap.

### <a name="hana-native-client-integration"></a>HANA natív ügyfél-integráció

#### <a name="backup"></a>Backup

A HANA-beli natív ügyfelektől ( **Backint**) indított, igény szerinti biztonsági mentések a biztonsági mentési **elemek** lapon jelennek meg a biztonsági mentési listán.

![Legutóbbi biztonsági másolatok futtatása](./media/sap-hana-db-manage/last-backups.png)

Ezeket a biztonsági másolatokat a **biztonsági mentési feladatok** lapról is [nyomon követheti](#monitor-manual-backup-jobs-in-the-portal) .

Ezek az igény szerinti biztonsági másolatok a visszaállítási pontok listájában is megjelennek.

![Visszaállítási pontok listája](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Visszaállítás

A HANA Native-ügyfelektől ( **Backint** használatával) indított visszaállítások a **biztonsági mentési feladatok** lapról is [megfigyelhetők](#monitor-manual-backup-jobs-in-the-portal) .

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>SAP HANA natív ügyfél biztonsági mentésének futtatása a helyi lemezre egy olyan adatbázison, amelyen engedélyezve van a Azure Backup

Ha helyi biztonsági mentést szeretne készíteni (a HANA Studio/cockpit használatával) egy olyan adatbázisról, amelyről biztonsági mentés készül Azure Backupsal, tegye a következőket:

1. Várjon, amíg befejeződik az adatbázis teljes vagy naplózott biztonsági mentése. Az állapot ellenõrzése SAP HANA Studio/pilótafülkében.
2. az érintett ADATBÁZIShoz
    1. A backint paramétereinek törlése. Ehhez kattintson duplán a **systemdb**-konfiguráció elemre, majd  >    >  **válassza az adatbázis**  >  **-szűrő (napló)** lehetőséget.
        * enable_auto_log_backup: nem
        * log_backup_using_backint: false
        * catalog_backup_using_backint: false
3. Igény szerinti teljes biztonsági mentés készítése az adatbázisról
4. Ezután hajtsa végre a lépések megfordítását. A fent említett érintett adatbázis esetében
    1. az backint paramétereinek újbóli engedélyezése
        1. catalog_backup_using_backint: true
        1. log_backup_using_backint: true
        1. enable_auto_log_backup: igen

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>A HANA-katalógus kezelése vagy törlése a Azure Backup engedélyezése esetén

Ha szerkeszteni vagy törölni szeretné a biztonsági mentési katalógust, tegye a következőket:

1. Várjon, amíg befejeződik az adatbázis teljes vagy naplózott biztonsági mentése. Az állapot ellenõrzése SAP HANA Studio/pilótafülkében.
2. az érintett ADATBÁZIShoz
    1. A backint paramétereinek törlése. Ehhez kattintson duplán a **systemdb**-konfiguráció elemre, majd  >    >  **válassza az adatbázis**  >  **-szűrő (napló)** lehetőséget.
        * enable_auto_log_backup: nem
        * log_backup_using_backint: false
        * catalog_backup_using_backint: false
3. Szerkessze a katalógust, és távolítsa el a régebbi bejegyzéseket
4. Ezután hajtsa végre a lépések megfordítását. A fent említett érintett adatbázis esetében
    1. az backint paramétereinek újbóli engedélyezése
        1. catalog_backup_using_backint: true
        1. log_backup_using_backint: true
        1. enable_auto_log_backup: igen

### <a name="change-policy"></a>Házirend módosítása

Megváltoztathatja egy SAP HANA biztonsági másolati elem alapjául szolgáló házirendet.

* A tároló irányítópultján lépjen a **biztonsági másolati elemek elemre**:

  ![Biztonsági másolati elemek kiválasztása](./media/sap-hana-db-manage/backup-items.png)

* **SAP HANA kiválasztása az Azure-beli virtuális gépen**

  ![SAP HANA kiválasztása az Azure-beli virtuális gépen](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Válassza ki azt a biztonsági mentési elemet, amelynek a mögöttes szabályzatát módosítani szeretné
* Válassza ki a meglévő biztonsági mentési szabályzatot.

  ![Meglévő biztonsági mentési házirend kiválasztása](./media/sap-hana-db-manage/existing-backup-policy.png)

* Módosítsa a szabályzatot, és válassza ki a listából. Szükség esetén [hozzon létre egy új biztonsági mentési szabályzatot](./backup-azure-sap-hana-database.md#create-a-backup-policy) .

  ![Válassza ki a szabályzatot a legördülő listából](./media/sap-hana-db-manage/choose-backup-policy.png)

* Mentse a módosításokat.

  ![Mentse a módosításokat.](./media/sap-hana-db-manage/save-changes.png)

* A szabályzat módosítása hatással lesz az összes kapcsolódó biztonsági mentési elemre, és elindítja a megfelelő **Konfigurálás-védelmi** feladatokat.

>[!NOTE]
> A megőrzési időtartam változásai visszamenőlegesen lesznek alkalmazva az újakon kívül az összes korábbi helyreállítási pontra.

### <a name="modify-policy"></a>Házirend módosítása

Módosítsa a szabályzatot a biztonsági mentési típusok, a gyakoriságok és a megőrzési tartomány módosításához.

>[!NOTE]
>A megőrzési időtartam változásai visszamenőlegesen lesznek alkalmazva az összes korábbi helyreállítási pontra, az újakon kívül is.

1. A tároló irányítópultján lépjen a **> biztonsági mentési szabályzatok kezelése** elemre, és válassza ki a szerkeszteni kívánt szabályzatot.

   ![Válassza ki a szerkeszteni kívánt szabályzatot](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Válassza a **módosítás** lehetőséget.

   ![Válassza a Módosítás lehetőséget](./media/sap-hana-db-manage/modify-policy.png)

1. Válassza ki a biztonsági mentési típusok gyakoriságát.

   ![Biztonsági mentés gyakoriságának kiválasztása](./media/sap-hana-db-manage/choose-frequency.png)

A szabályzat módosítása hatással lesz az összes kapcsolódó biztonsági mentési elemre, és elindítja a megfelelő **Konfigurálás-védelmi** feladatokat.

### <a name="inconsistent-policy"></a>Inkonzisztens házirend

Időnként előfordulhat, hogy a házirend-módosítási művelet bizonyos biztonsági másolati elemek esetében **inkonzisztens** házirend-verziót eredményez. Ez akkor fordulhat elő, ha a biztonsági mentési elemhez tartozó megfelelő **konfigurálási** művelet meghiúsult a házirend-módosítási művelet elindítása után. A biztonsági mentési elem nézetben a következőképpen jelenik meg:

![Inkonzisztens házirend](./media/sap-hana-db-manage/inconsistent-policy.png)

Egy kattintással kijavíthatja az összes érintett elem szabályzatának verzióját:

![Házirend verziójának javítása](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA-adatbázis védelmének leállítása

Több módon is leállíthatja a SAP HANA adatbázisok védelmét:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törölje az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és hagyja érintetlenül a helyreállítási pontokat.

Ha úgy dönt, hogy kihagyja a helyreállítási pontokat, tartsa szem előtt az alábbi adatokat:

* Az összes helyreállítási pont érintetlen marad, és az összes törlés leáll a védelem leállításakor az adatmegőrzéssel.
* A védett példányért és a felhasznált tárterületért díjat számítunk fel. További információ: [Azure Backup díjszabása](https://azure.microsoft.com/pricing/details/backup/).
* Ha töröl egy adatforrást a biztonsági mentések leállítása nélkül, az új biztonsági mentések sikertelenek lesznek.

Az adatbázis védelmének leállítása:

* A tároló irányítópultján válassza a **biztonsági másolati elemek** lehetőséget.
* A **biztonsági mentési felügyelet típusa** területen válassza **a SAP HANA lehetőséget az Azure virtuális gépen**

  ![SAP HANA kiválasztása az Azure-beli virtuális gépen](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Válassza ki azt az adatbázist, amelynek a védelmét le szeretné állítani:

  ![Válassza ki az adatbázist a védelem leállításához](./media/sap-hana-db-manage/select-database.png)

* Az adatbázis menüben válassza a **biztonsági mentés leállítása** lehetőséget.

  ![Válassza a biztonsági mentés leállítása lehetőséget.](./media/sap-hana-db-manage/stop-backup.png)

* A **biztonsági mentés leállítása** menüben válassza ki, hogy szeretné-e megőrizni vagy törölni az adattárolást. Ha szeretné, adjon meg egy okot és egy megjegyzést.

  ![Az adatmegőrzés vagy-törlés kiválasztása](./media/sap-hana-db-manage/retain-backup-data.png)

* Válassza a **biztonsági mentés leállítása** lehetőséget.

### <a name="resume-protection-for-an-sap-hana-database"></a>SAP HANA-adatbázis védelmének folytatása

Ha leállítja a SAP HANA-adatbázis védelmét, ha bejelöli a **biztonsági mentési adat megőrzése** beállítást, később folytathatja a védelmet. Ha nem őrzi meg a biztonsági másolatban szereplő adatait, a védelem nem folytatható.

SAP HANA-adatbázis védelmének folytatása:

* Nyissa meg a biztonsági mentési elemet, és válassza a **biztonsági mentés folytatása** lehetőséget.

   ![Válassza a biztonsági mentés folytatása lehetőséget.](./media/sap-hana-db-manage/resume-backup.png)

* A **biztonsági mentési házirend** menüben válasszon ki egy házirendet, majd kattintson a **Mentés** gombra.

### <a name="upgrading-from-sdc-to-mdc"></a>Frissítés SDC-ről MDC-re

Ismerje meg, hogyan folytathatja a biztonsági mentést egy SAP HANA adatbázisról a [SDC-ről a MDC-re való frissítés után](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Frissítés SDC-ről MDC-re SID-változás nélkül

Ismerje meg, hogy miként folytathatja a SAP HANA adatbázis biztonsági mentését, amelynek SID-je a [SDC-ről a MDC-re való frissítés után nem változott](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid)

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>Frissítés a SDC vagy a MDC új verziójára

Megtudhatja, hogyan folytathatja az olyan SAP HANA-adatbázisok biztonsági mentését, [amelyek verziófrissítése folyamatban van](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm).

### <a name="unregister-an-sap-hana-instance"></a>SAP HANA példány regisztrációjának törlése

A védelem letiltása, de a tár törlése előtt törölje a SAP HANA példány regisztrációját:

* A tároló irányítópultjának **kezelés** területén válassza a **biztonsági mentési infrastruktúra** elemet.

   ![Biztonsági mentési infrastruktúra kiválasztása](./media/sap-hana-db-manage/backup-infrastructure.png)

* A **biztonsági mentési felügyelet típusának** kiválasztása **Munkaterhelésként az Azure-beli virtuális gépen**

   ![A biztonsági mentési felügyelet típusának kiválasztása Munkaterhelésként az Azure-beli virtuális gépen](./media/sap-hana-db-manage/backup-management-type.png)

* A **védett kiszolgálók** lapon válassza ki a regisztrálni kívánt példányt. A tár törléséhez meg kell szüntetnie az összes kiszolgáló/példány regisztrációját.

* Kattintson a jobb gombbal a védett példányra, majd válassza a **Regisztráció törlése** lehetőséget.

   ![Regisztráció törlése](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>A bővítmény újbóli regisztrálása a SAP HANA Server virtuális gépen

Előfordulhat, hogy a virtuális gépen a munkaterhelés-bővítmény az egyik ok vagy egy másikra hatással van. Ilyen esetekben a virtuális gépen aktivált összes művelet sikertelen lesz. Előfordulhat, hogy újra regisztrálnia kell a bővítményt a virtuális gépen. A művelet ismételt regisztrálása újratelepíti a munkaterhelési biztonsági mentési bővítményt a virtuális gépen a folytatáshoz.

Ezt a beállítást körültekintően használhatja: Ha egy már kifogástalan állapotú virtuális gépen aktiválódik, ez a művelet a bővítmény újraindítását eredményezi. Ennek hatására előfordulhat, hogy az összes folyamatban lévő feladat meghiúsul. Az újbóli regisztrálási művelet elindítása előtt keressen egy vagy több [tünetet](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) .

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogy miként lehet [elhárítani a SAP HANA adatbázisok biztonsági mentése során felmerülő gyakori problémákat.](./backup-azure-sap-hana-database-troubleshoot.md)
