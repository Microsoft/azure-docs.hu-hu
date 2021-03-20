---
title: SQL Server-adatbázisok visszaállítása Azure-beli virtuális gépen
description: Ez a cikk azt ismerteti, hogyan lehet visszaállítani az Azure-beli virtuális gépen futó SQL Server-adatbázisokat, és hogy a rendszer biztonsági mentést készít a Azure Backup használatával. Az adatbázisok másodlagos régióba való visszaállításához a régiók közötti visszaállítást is használhatja.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 7dd8d8d54fa7d33bb4a0935357597d19dd2368c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97734402"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>SQL Server-adatbázisok visszaállítása Azure-beli virtuális gépeken

Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy olyan SQL Server adatbázist, amely egy Azure-beli virtuális gépen (VM) fut, amelyet a [Azure Backup](backup-overview.md) szolgáltatás egy Azure Backup Recovery Services-tárolóba készített.

Ez a cikk a SQL Server adatbázisok visszaállítását ismerteti. További információ: [SQL Server adatbázisok biztonsági mentése Azure virtuális gépeken](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Visszaállítás egy időre vagy egy helyreállítási pontra

A Azure Backup az Azure-beli virtuális gépeken futó SQL Server-adatbázisokat a következőképpen állíthatja helyre:

- Állítsa vissza egy adott dátumra vagy időpontra (a másodikra) a tranzakciónaplók biztonsági másolatainak használatával. Azure Backup automatikusan meghatározza a megfelelő teljes különbözeti biztonsági mentést, valamint a visszaállításhoz szükséges naplók láncát, amely a kijelölt idő alapján állítható vissza.
- Egy adott helyreállítási pontra való visszaállításhoz állítson vissza egy adott teljes vagy különbözeti biztonsági másolatot.

## <a name="restore-prerequisites"></a>Előfeltételek visszaállítása

Az adatbázis visszaállítása előtt vegye figyelembe a következőket:

- Az adatbázist visszaállíthatja egy SQL Server egy példányára ugyanabban az Azure-régióban.
- A célkiszolgálón regisztrálni kell a forrással megegyező tárolóban.
- Ha a kiszolgálón több példány fut, az összes példánynak működnie kell. Ellenkező esetben a kiszolgáló nem jelenik meg a célkiszolgáló listájában, hogy vissza tudja állítani az adatbázist. További információkért tekintse meg [a hibaelhárítási lépéseket](backup-sql-server-azure-troubleshoot.md#faulty-instance-in-a-vm-with-multiple-sql-server-instances).
- Ha egy TDE-titkosított adatbázist szeretne visszaállítani egy másik SQL Serverre, először [vissza kell állítania a tanúsítványt a célkiszolgálóra](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
- A [CDC](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) -kompatibilis adatbázisok [visszaállítását a fájlok visszaállítása](#restore-as-files) lehetőség használatával kell visszaállítani.
- A "Master" adatbázis visszaállítása előtt indítsa el az SQL Server példányt egy egyfelhasználós módban az indítási lehetőség **– m AzureWorkloadBackup** használatával.
  - A **-m** érték az ügyfél neve.
  - A kapcsolatok csak a megadott ügyfél nevével nyithatók meg.
- Az összes rendszeradatbázis (modell, főkiszolgáló, msdb) esetében állítsa le a SQL Server Agent szolgáltatást a visszaállítás elindítása előtt.
- Zárjunk be minden olyan alkalmazást, amely megkísérelheti a kapcsolódást bármelyik adatbázishoz.

## <a name="restore-a-database"></a>Adatbázis helyreállítása

A visszaállításhoz a következő engedélyek szükségesek:

- **Biztonsági mentési operátor** engedélyei azon a tárolón, ahol a visszaállítást végzi.
- **Közreműködő (írási)** hozzáférés a forrásként szolgáló virtuális géphez, amelyről biztonsági mentés készül.
- **Közreműködő (írási)** hozzáférés a célként megadott virtuális géphez:
  - Ha ugyanarra a virtuális gépre állítja vissza, akkor ez a forrás virtuális gép.
  - Ha másik helyre állítja vissza a visszaállítást, ez az új célként megadott virtuális gép.

Visszaállítás a következőképpen:

1. Nyissa meg azt a tárolót, amelyben a SQL Server VM regisztrálva van.
2. A tároló irányítópultján a **használat** alatt válassza a **biztonsági másolati elemek elemet**.
3. A **biztonsági másolati elemek** alatt, a **biztonsági mentési felügyelet típusa** területen válassza az **SQL lehetőséget az Azure virtuális gépen**.

    ![SQL kiválasztása az Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Válassza ki a visszaállítani kívánt adatbázist.

    ![Válassza ki a visszaállítani kívánt adatbázist](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Tekintse át az adatbázis menüt. Információt nyújt az adatbázis biztonsági másolatából, beleértve a következőket:

    - A legrégebbi és a legújabb visszaállítási pontok.
    - A napló biztonsági mentési állapota az elmúlt 24 órában olyan adatbázisok esetén, amelyek teljes és tömegesen naplózott helyreállítási módban vannak, és amelyek a tranzakciós napló biztonsági mentésére vannak konfigurálva.

6. Válassza a **visszaállítás** lehetőséget.

    ![Visszaállítás kiválasztása](./media/backup-azure-sql-database/restore-db.png)

7. A **visszaállítási konfiguráció** területen adja meg, hogy hol (vagy hogyan) szeretné visszaállítani az adatértékeket:
   - **Másik hely**: állítsa vissza az adatbázist egy másik helyre, és tartsa meg az eredeti forrás-adatbázist.
   - **Adatbázis felülírása**: állítsa vissza az adatforrást ugyanarra a SQL Server példányra, mint az eredeti forrást. Ez a beállítás felülírja az eredeti adatbázist.

        > [!IMPORTANT]
        > Ha a kiválasztott adatbázis egy always on rendelkezésre állási csoporthoz tartozik, SQL Server nem teszi lehetővé az adatbázis felülírását. Csak a **másik hely** érhető el.
        >
   - **Visszaállítás fájlként**: adatbázisként való visszaállítás helyett állítsa vissza azokat a biztonsági másolati fájlokat, amelyeket később adatbázisként helyreállíthat bármely olyan gépen, ahol a fájlok SQL Server Management Studio használatával jelennek meg.
     ![Konfiguráció visszaállítása menü](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. A visszaállítási **konfiguráció** menüben, a **visszaállítás helye** területen válassza a **másik hely** lehetőséget.
1. Válassza ki azt a SQL Server nevet és példányt, amelyre vissza kívánja állítani az adatbázist.
1. A **visszaállított adatbázis neve** mezőbe írja be a céladatbázis nevét.
1. Ha van ilyen, válassza **a felülírás lehetőséget, ha már létezik ilyen nevű adatbázis a kiválasztott SQL-példányon**.
1. Válassza ki a **visszaállítási pontot**, és adja meg, hogy [egy adott időpontra](#restore-to-a-specific-point-in-time) kíván-e visszaállítani, vagy [egy adott helyreállítási pontra kíván visszaállítani](#restore-to-a-specific-restore-point).

    ![Visszaállítási pont kiválasztása](./media/backup-azure-sql-database/select-restore-point.png)

    ![Visszaállítás az időpontra](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. A **Speciális konfigurációs** menüben:

    - Ha a visszaállítás után meg szeretné őrizni az adatbázis működését, engedélyezze a **visszaállítást a derecovery szolgáltatással**.
    - Ha módosítani szeretné a célkiszolgáló visszaállítási helyét, adja meg az új cél elérési utakat.

        ![Célként megadott elérési utak megadása](./media/backup-azure-sql-database/target-paths.png)

1. A visszaállítás elindításához kattintson **az OK gombra** . Nyomon követheti a visszaállítási folyamatot az **értesítések** területen, vagy nyomon követheti a **tárolóban a biztonsági mentési feladatok** nézetben.

    > [!NOTE]
    > Az időponthoz való visszaállítás csak a teljes és tömegesen naplózott helyreállítási módban lévő adatbázisok biztonsági másolatai esetében érhető el.

### <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

1. A visszaállítási **konfiguráció** menüben, a **visszaállítás helye** területen válassza az **adatbázis felülírása**  >  **OK** elemet.

    ![Válassza az adatbázis felülírása lehetőséget](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. A **visszaállítási pont kiválasztása lapon** válassza ki a **naplók (** adott időpontban) lehetőséget [egy adott időpontra való visszaállításhoz](#restore-to-a-specific-point-in-time). Vagy válassza a **teljes & különbözetet** egy [adott helyreállítási pontra](#restore-to-a-specific-restore-point)való visszaállításhoz.

    > [!NOTE]
    > Az időponthoz való visszaállítás csak a teljes és tömegesen naplózott helyreállítási módban lévő adatbázisok biztonsági másolatai esetében érhető el.

### <a name="restore-as-files"></a>Visszaállítás fájlként

Ha egy adatbázis helyett. bak fájlként szeretné visszaállítani a biztonsági mentési adatmennyiséget, válassza a **visszaállítás fájlként** lehetőséget. Ha a fájlok egy megadott elérési útra lettek kiadva, ezeket a fájlokat bármely olyan gépre elvégezheti, ahol adatbázisként szeretné visszaállítani őket. Mivel ezeket a fájlokat bármely gépre áthelyezheti, mostantól visszaállíthatja az összes előfizetést és régiót.

1. A **hol és hogyan kell visszaállítani**, válassza a **visszaállítás fájlként** lehetőséget.
1. Válassza ki azt a SQL Server nevet, amelyre vissza kívánja állítani a biztonságimásolat-fájlokat.
1. A **kiszolgáló célhelyének elérési útja** a 2. lépésben kiválasztott kiszolgálón adja meg a mappa elérési útját. Ez az a hely, ahol a szolgáltatás kiírja az összes szükséges biztonságimásolat-fájlt. A célként megadott elérési úttal megegyező hálózati megosztási elérési út vagy egy csatlakoztatott Azure-fájlmegosztás elérési útja lehetővé teszi, hogy a fájlok könnyebben hozzáférhessenek az azonos hálózatban lévő más gépekhez, illetve az azokhoz csatlakoztatott Azure-fájlmegosztáshoz.<BR>

    >Ha vissza szeretné állítani az adatbázis biztonsági másolatának fájljait egy olyan Azure-fájlmegosztás számára, amely a cél regisztrált virtuális gépen van csatlakoztatva, győződjön meg arról, hogy az NT AUTHORITY\SYSTEM hozzáfér a fájlmegosztás eléréséhez. Az alábbi lépések végrehajtásával engedélyezheti az olvasási/írási engedélyeket a virtuális gépen csatlakoztatott AFS-hez:
    >
    >- Futtatás `PsExec -s cmd` az NT AUTHORITY\SYSTEM-rendszerhéjba való belépéshez
    >   - Hajtsa végre az `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` parancsot.
    >   - Hozzáférés ellenőrzése `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- A Backup-tárolóban lévő fájlok visszaállításának elindítási `\\<storageacct>.file.core.windows.net\<filesharename>` útja<BR>
    A PsExec letöltése a [Sysinternals](/sysinternals/downloads/psexec) lapról végezhető el.

1. Válassza az **OK** lehetőséget.

    ![Válassza a visszaállítás fájlokként lehetőséget.](./media/backup-azure-sql-database/restore-as-files.png)

1. Válassza ki a **visszaállítási pontot**, és adja meg, hogy [egy adott időpontra](#restore-to-a-specific-point-in-time) kíván-e visszaállítani, vagy [egy adott helyreállítási pontra kíván visszaállítani](#restore-to-a-specific-restore-point).

1. A rendszer a kijelölt helyreállítási ponthoz társított összes biztonságimásolat-fájlt a célhely elérési útjára írja. A fájlokat bármely olyan gépen visszaállíthatja adatbázisként, amelyen a SQL Server Management Studio használatával van jelen.

    ![Visszaállított biztonságimásolat-fájlok a célhely elérési útján](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Visszaállítás adott időpontra

Ha a **naplókat (időpontot)** a visszaállítási típusként választotta, tegye a következőket:

1. A **dátum és idő visszaállítása** alatt nyissa meg a naptárat. A naptárban a helyreállítási pontokat tartalmazó dátumok félkövér típusban jelennek meg, az aktuális dátum pedig ki van emelve.
1. Válassza ki a helyreállítási pontokat tartalmazó dátumot. Nem választhat olyan dátumokat, amelyek nem rendelkeznek helyreállítási ponttal.

    ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Miután kiválasztott egy dátumot, az idővonal-gráf megjeleníti a rendelkezésre álló helyreállítási pontokat a folytonos tartományban.
1. Adja meg a helyreállítás időpontját az idősor-diagramon, vagy válasszon ki egy időpontot. Ez után válassza az **OK** gombot.

### <a name="restore-to-a-specific-restore-point"></a>Visszaállítás adott visszaállítási pontra

Ha a **teljes & különbözetet** választotta a visszaállítási típusként, tegye a következőket:

1. Válasszon ki egy helyreállítási pontot a listából, és a visszaállítási pont eljárás befejezéséhez kattintson **az OK gombra** .

    ![Teljes helyreállítási pont kiválasztása](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > Alapértelmezés szerint az elmúlt 30 napban érkező helyreállítási pontok jelennek meg. A 30 napnál régebbi helyreállítási pontokat úgy jelenítheti meg, ha kiválasztja a **szűrő** lehetőséget, és kiválasztja az egyéni tartományt.

### <a name="restore-databases-with-large-number-of-files"></a>Adatbázisok visszaállítása nagy számú fájllal

Ha egy adatbázisban lévő fájlok teljes mérete meghaladja az [adott korlátot](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), akkor a Azure Backup egy másik pit-összetevőben tárolja az adatbázisfájlok listáját, így nem állíthatja be a cél-visszaállítási útvonalat a visszaállítási művelet során. Ehelyett a rendszer visszaállítja a fájlokat az alapértelmezett SQL-elérési útra.

  ![Adatbázis visszaállítása nagyméretű fájllal](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>Régiók közötti visszaállítás

A visszaállítási lehetőségek egyike, a régiók közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépeken tárolt SQL-adatbázisok visszaállítását egy másodlagos régióban, amely egy Azure-beli párosított régió.

Ha az előzetes verzióban szeretné bejelentkezni a szolgáltatásba, olvassa el az előkészületek [című szakaszt](./backup-create-rs-vault.md#set-cross-region-restore).

Ha szeretné megtekinteni, hogy a CRR engedélyezve van-e, kövesse a [tartományok közötti visszaállítás konfigurálása](backup-create-rs-vault.md#configure-cross-region-restore) című témakör útmutatását.

### <a name="view-backup-items-in-secondary-region"></a>Biztonsági másolati elemek megtekintése a másodlagos régióban

Ha a CRR engedélyezve van, megtekintheti a másodlagos régió biztonsági másolati elemeit.

1. A portálon nyissa meg **Recovery Services**  >  **tároló biztonsági másolati elemeit**.
1. Válassza a **másodlagos régió** elemet a másodlagos régió elemeinek megtekintéséhez.

>[!NOTE]
>A listában csak a CRR funkciót támogató biztonsági mentési felügyeleti típusok jelennek meg. Jelenleg csak a másodlagos régióba tartozó adatelemek másodlagos régióba való visszaállításának támogatása engedélyezett.

![Biztonsági másolati elemek a másodlagos régióban](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![Adatbázisok a másodlagos régióban](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Visszaállítás másodlagos régióban

A másodlagos régió visszaállítási felhasználói felülete hasonló lesz az elsődleges régió visszaállítási felhasználói felületéhez. Amikor konfigurálja a részleteket a visszaállítási konfiguráció ablaktáblán a visszaállítás konfigurálásához, a rendszer kérni fogja, hogy csak a másodlagos régió paramétereit adja meg.

![Hol és hogyan kell visszaállítani?](./media/backup-azure-sql-database/restore-secondary-region.png)

>[!NOTE]
>A másodlagos régióban lévő virtuális hálózatot egyedi módon kell hozzárendelni, és nem használható az adott erőforráscsoport bármely más virtuális gépe számára.

![Trigger visszaállítása folyamatban lévő értesítésben](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>- A visszaállítás elindítása és az adatátviteli fázisban a visszaállítási feladatot nem lehet megszakítani.
>- A másodlagos régióban való visszaállításhoz szükséges Azure-szerepkörök ugyanazok, mint az elsődleges régióban.

### <a name="monitoring-secondary-region-restore-jobs"></a>Másodlagos régió visszaállítási feladatainak figyelése

1. A portálon nyissa meg **Recovery Services**-  >  **tároló biztonsági mentési feladatait**
1. Válassza a **másodlagos régió** elemet a másodlagos régió elemeinek megtekintéséhez.

    ![Biztonsági mentési feladatok szűrve](./media/backup-azure-sql-database/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Következő lépések

[Kezelés és figyelés](manage-monitor-sql-database-backup.md) SQL Server a Azure Backup által biztonsági mentést tartalmazó adatbázisokat.
