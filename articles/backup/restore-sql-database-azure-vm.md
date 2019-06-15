---
title: Egy Azure virtuális Gépen futó SQL Server-adatbázisok biztonsági másolat visszaállítása az Azure Backup használatával |} A Microsoft Docs
description: Ez a cikk azt ismerteti, és a, amelyek biztonsági mentése az Azure Backup-beli virtuális gépen futó SQL Server-adatbázisok visszaállítása.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: d8ade598e4f1b6331367e8bd04ad59951ef5de8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242367"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Az Azure virtuális gépeken futó SQL Server-adatbázisok visszaállítása

Ez a cikk bemutatja, hogyan állíthatja vissza egy Azure virtuális gép (VM) számítógépen futó SQL Server-adatbázis, amely a [Azure Backup](backup-overview.md) szolgáltatás rendelkezik biztonsági mentése az Azure Backup helyreállítási tárat.

Ez a cikk ismerteti, hogyan állíthatja vissza az SQL Server-adatbázisok. További információkért lásd: [biztonsági mentése Azure virtuális gépeken az SQL Server-adatbázisok](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Egy időpontot vagy egy helyreállítási pontot visszaállítása

Az Azure Backup visszaállíthatja a rendszert futtató Azure virtuális gépeken a következő SQL Server-adatbázisok:

- Állítsa vissza az adott dátum és idő (a második) tranzakciónaplók biztonsági mentését. Az Azure Backup automatikusan meghatározza, hogy a megfelelő teljes, különbségi biztonsági mentés és a kijelölt idő alapján a lánc napló biztonsági másolatok visszaállításához szükséges.
- Egy adott teljes vagy különbözeti biztonsági másolat visszaállítása állíthatja vissza egy adott helyreállítási pontot.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt vissza tudná állítani egy adatbázist, vegye figyelembe a következőket:

- Az adatbázist visszaállíthatja egy példányát olyan SQL Server ugyanazon Azure-régióban.
- A célkiszolgáló ugyanazzal a tárral, mint a forrás regisztrálni kell.
- A TDE-titkosított adatbázis visszaállítása egy másik SQL Serverre, először szüksége [állítsa vissza a tanúsítványt a célkiszolgálóra](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Mielőtt vissza tudná állítani a "master" adatbázisban, indítsa el az SQL Server-példány egyfelhasználós módban a indítási kapcsolóval **-m AzureWorkloadBackup**.
    - Az érték **-m** az ügyfél neve.
    - Csak a megadott ügyfél nevét is megnyithatja a kapcsolatot.
- Az összes rendszer adatbázisban (modell master, msdb) állítsa le az SQL Server Agent szolgáltatást a visszaállítás elindítása előtt.
- Zárja be az olyan alkalmazásokat, amelyek próbálnak kapcsolatot igénybe sem ezeknek az adatbázisoknak.
- Ha több egy kiszolgálón futó összes a példányon be kell lennie, és futó ellenkező esetben a kiszolgáló nem fog az, hogy az adatbázis-visszaállítás a célkiszolgáló a listában.

## <a name="restore-a-database"></a>Adatbázis visszaállítása

-Adatbázis visszaállításához a következő engedélyek szükségesek:

* **Biztonsági mentési operátor** engedélyeket a tárolóban, ahol a visszaállítást végez.
* **Közreműködő (írás)** a forrás virtuális gép biztonsági mentésének elérésére.
* **Közreműködő (írás)** a cél virtuális Gépen való hozzáférést:
    - Ha ugyanazon a virtuális Gépen történő visszaállításakor, akkor a forrás virtuális Gépen.
    - Ha egy másodlagos helyre állítja vissza, akkor az új cél virtuális gép.

Állítsa vissza a következőképpen:
1. Nyissa meg a tároló, amelyben az SQL Server rendszerű virtuális gép regisztrálva lett.
2. A tároló irányítópultjának alatt **használati**válassza **biztonsági másolati elemek**.
3. A **biztonsági másolati elemek**alatt **biztonságimásolat-felügyeleti típussal**válassza **SQL Azure-beli virtuális gépen**.

    ![Válassza ki az SQL Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Válassza ki az adatbázis visszaállításához.

    ![Válassza ki a visszaállítandó adatbázis](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Tekintse át az adatbázis menü. Ismerteti az adatbázis biztonsági másolata, többek között:

    * A legrégebbi és a legújabb visszaállítási pontot.
    * A napló biztonsági mentésének állapota az elmúlt 24 órában, amelyek teljes és a tömegesen naplózott helyreállítási módban, és a tranzakciós napló biztonsági mentésekhez beállított adatbázisok.

6. Válassza ki **Restore DB**.

    ![Válassza ki a Restore DB](./media/backup-azure-sql-database/restore-db-button.png)

7. A **konfiguráció visszaállítása**, hová szeretné visszaállítani az adatokat:
   - **Másik helyre**: Állítsa vissza az adatbázist egy másik helyre, és tartsa az eredeti adatbázis.
   - **Adatbázis felülírása**: Állítsa vissza az adatokat az eredeti forrás, ugyanazon SQL Server-példányon. Ez a beállítás felülírja az eredeti adatbázist.

     > [!Important]
     > Ha a kijelölt adatbázis Always On rendelkezésre állási csoporthoz tartozik, az SQL Server felülírja az adatbázis nem teszi lehetővé. Csak **máshová** érhető el.
     >

     ![Visszaállítási konfiguráció menü](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. Az a **konfiguráció visszaállítása** menü alatt **helyreállítás helye**válassza **másik helyre**.
2. Válassza ki az SQL Server nevének és példányának, amelyhez le szeretné állítani az adatbázist.
3. Az a **visszaállított adatbázis neve** mezőbe írja be a céladatbázis neve.
4. Ha a alkalmazni, válassza ki a **felülírása, ha a kiválasztott SQL-példányon már létezik a DB ugyanazzal a névvel**.
5. Kattintson az **OK** gombra.

    ![A konfiguráció visszaállítása menü értékeinek megadása](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. A **visszaállítási pont kiválasztása**válassza e [adott időpontra való visszaállítása](#restore-to-a-specific-point-in-time) vagy [egy adott helyreállítási pontra való visszaállítás](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A pont – történő visszaállítás csak a teljes és a tömegesen naplózott helyreállítási módban lévő adatbázisok naplóalapú biztonsági mentések esetén érhető el.

### <a name="restore-and-overwrite"></a>Visszaállítás és felülírása

1. Az a **konfiguráció visszaállítása** menü alatt **visszaállítás helyének**, jelölje be **felülírása DB** > **OK**.

    ![Válassza ki az adatbázis felülírása](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. A **visszaállítási pont kiválasztása**válassza **naplók (idő)** való [adott időpontra való visszaállítása](#restore-to-a-specific-point-in-time). Válassza ki vagy **teljes és különbségi** állíthatja vissza egy [adott helyreállítási pontot](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A pont – történő visszaállítás csak a teljes és a tömegesen naplózott helyreállítási módban lévő adatbázisok naplóalapú biztonsági mentések esetén érhető el.

### <a name="restore-to-a-specific-point-in-time"></a>Adott időpontra való visszaállítása

Ha a kiválasztott **naplók (idő)** visszaállítási típusként, tegye a következőket:

1.  A **dátum és idő visszaállításához**, nyissa meg a naptárban. A naptár a dátumokat, amelyek rendelkeznek a helyreállítási pontok félkövér betűtípussal jelennek meg, és az aktuális dátumot ki van jelölve.
1. Jelölje ki, amely rendelkezik a helyreállítási pontok dátumát. Nem választhat ki, amelyek nincsenek helyreállítási pontok dátumát.

    ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Miután kiválasztotta a dátumot, az ütemterv graph folyamatos számos jeleníti meg a rendelkezésre álló helyreállítási pontok.
1. Adjon meg egy időpontot a helyreállításhoz az ütemterv graph, vagy válasszon ki egy időpontot. Ezután kattintson az **OK** gombra.

    ![Válassza ki a helyreállítás időpontját](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. Az a **speciális konfiguráció** menü, ha meg szeretné tartani az adatbázis működésképtelen a visszaállítás után engedélyezze **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL**.
1. Ha szeretné a célkiszolgálón a visszaállítás helyének módosításához, adja meg az új cél elérési út.
1. Kattintson az **OK** gombra.

    ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Az a **visszaállítása** menüjében válassza **visszaállítása** a visszaállítási feladat elindításához.
1. A visszaállítási folyamat nyomon a **értesítések** területet, illetve nyomon követheti a kiválasztásával **visszaállítási feladatok** adatbázis menüben.

    ![Visszaállítási feladat állapota](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Állítsa vissza egy adott visszaállítási pont

Ha a kiválasztott **teljes és különbségi** visszaállítási típusként, tegye a következőket:

1. Válasszon ki egy helyreállítási pontot a listából, és válassza ki **OK** a visszaállítási pont eljárást.

    ![Válasszon egy teljes helyreállítási pontot](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Az a **speciális konfiguráció** menü, ha meg szeretné tartani az adatbázis működésképtelen a visszaállítás után engedélyezze **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL**.
1. Ha szeretné a célkiszolgálón a visszaállítás helyének módosításához, adja meg az új cél elérési út.
1. Kattintson az **OK** gombra.

    ![Speciális konfigurációs menüje](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Az a **visszaállítása** menüjében válassza **visszaállítása** a visszaállítási feladat elindításához.
1. A visszaállítási folyamat nyomon a **értesítések** területet, illetve nyomon követheti a kiválasztásával **visszaállítási feladatok** adatbázis menüben.

    ![Visszaállítási feladat állapota](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Nagy mennyiségű fájlt az adatbázisok visszaállítása

Ha a teljes karakterlánc adatbázis fájlok mérete nagyobb, mint egy [adott korlát](backup-sql-server-azure-troubleshoot.md#files-size-limit-beyond-which-restore-happens-to-default-path), az Azure Backup egy másik pit összetevő adatbázisfájlok listáját tárolja, úgy, hogy nem lesz a cél visszaállítási elérési útjának beállítása a visszaállítás során a művelet. A fájlok helyette az SQL alapértelmezett elérési útra lesz visszaállítva.

  ![Adatbázis visszaállítása a nagy méretű fájlt](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>További lépések

[Kezelése és figyelése](manage-monitor-sql-database-backup.md) készül biztonsági másolat az Azure Backup az SQL Server-adatbázisok.
