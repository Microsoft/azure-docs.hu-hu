---
title: SAP HANA-adatbázisok visszaállítása Azure-beli virtuális gépeken
description: Ebből a cikkből megtudhatja, hogyan állíthatja vissza az Azure Virtual Machines-on futó SAP HANA adatbázisokat. Az adatbázisok másodlagos régióba való visszaállításához a régiók közötti visszaállítást is használhatja.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c502b7741acd343baefe5e2bf8b95cfc02e46688
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021673"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>SAP HANA-adatbázisok visszaállítása Azure-beli virtuális gépeken

Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy Azure-beli virtuális gépen (VM) futó SAP HANA-adatbázist, amelyet a Azure Backup szolgáltatás egy Recovery Services-tárolóba készített. A visszaállítással az adatok másolatait lehet létrehozni fejlesztési és tesztelési forgatókönyvekhez, vagy visszatérhet egy korábbi állapotba.

További információ a SAP HANA adatbázisok biztonsági mentéséről: [SAP HANA adatbázisok biztonsági mentése Azure-beli virtuális gépeken](./backup-azure-sap-hana-database.md).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Visszaállítás egy időpontra vagy egy helyreállítási pontra

A Azure Backup az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat a következőképpen állíthatja helyre:

* Visszaállítás adott dátumra vagy időpontra (a másodikra) a naplók biztonsági másolatainak használatával. A Azure Backup automatikusan meghatározza a megfelelő teljes, különbözeti biztonsági másolatokat, valamint a visszaállításhoz szükséges napló biztonsági mentésének láncát a kiválasztott idő alapján.

* Egy adott helyreállítási pontra való visszaállításhoz állítsa vissza egy adott teljes vagy különbözeti biztonsági mentést.

## <a name="prerequisites"></a>Előfeltételek

Az adatbázis visszaállítása előtt vegye figyelembe a következőket:

* Az adatbázist csak olyan SAP HANA-példányra állíthatja vissza, amely ugyanabban a régióban található.

* A cél példányt regisztrálni kell ugyanazzal a tárolóval, mint a forrás.

* Azure Backup nem képes két különböző SAP HANA példány azonosítására ugyanazon a virtuális gépen. Így az adatok egyik példányról a másikra való visszaállítása nem lehetséges.

* Annak érdekében, hogy a cél SAP HANA-példány készen álljon a visszaállításra, ellenőrizze a **biztonsági mentés készültségi** állapotát:

  1. Nyissa meg azt a tárolót, amelyben a cél SAP HANA példány regisztrálva van.

  1. A tároló irányítópultjának **első lépések** területén válassza a **biztonsági mentés** lehetőséget.

      ![Biztonsági mentés a tároló irányítópultján](media/sap-hana-db-restore/getting-started-backup.png)

  1. A **biztonsági** Mentés területen válassza a **mit szeretne biztonsági másolatot készíteni?** **Az Azure-beli virtuális gépen válasszon SAP HANA**.

      ![SAP HANA kiválasztása az Azure-beli virtuális gépen](media/sap-hana-db-restore/sap-hana-backup.png)

  1. **A virtuális gépek felderítése az adatbázisok** között területen válassza a **részletek megtekintése** lehetőséget.

      ![Részletek megtekintése](media/sap-hana-db-restore/view-details.png)

  1. Tekintse át a cél virtuális gép **biztonsági mentési készültségét** .

      ![Védett kiszolgálók](media/sap-hana-db-restore/protected-servers.png)

* Ha többet szeretne megtudni a SAP HANA által támogatott visszaállítási típusokról, tekintse meg a SAP HANA Megjegyzés [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Adatbázis helyreállítása

A visszaállításhoz a következő engedélyek szükségesek:

* **Biztonsági mentési operátor** engedélyei azon a tárolón, ahol a visszaállítást végzi.
* **Közreműködő (írási)** hozzáférés a forrásként szolgáló virtuális géphez, amelyről biztonsági mentés készül.
* **Közreműködő (írási**) hozzáférés a célként megadott virtuális géphez:
  * Ha ugyanarra a virtuális gépre állítja vissza, akkor ez a forrás virtuális gép.
  * Ha másik helyre állítja vissza a visszaállítást, ez az új célként megadott virtuális gép.

1. Nyissa meg a tárolót, amelyben a visszaállítani kívánt SAP HANA adatbázis regisztrálva van

1. A tároló irányítópultján, a **védett elemek** területen válassza a **biztonsági másolati elemek elemet** .

    ![Biztonsági másolati elemek](media/sap-hana-db-restore/backup-items.png)

1. A **biztonsági másolati elemek** **területen válassza** **Az Azure-beli virtuális gép SAP HANA** kiválasztása lehetőséget.

    ![Biztonságimásolat-kezelés típusa](media/sap-hana-db-restore/backup-management-type.png)

1. Válassza ki a visszaállítani kívánt adatbázist

    ![Visszaállítani kívánt adatbázis](media/sap-hana-db-restore/database-to-restore.png)

1. Tekintse át az adatbázis menüt. Az adatbázis biztonsági mentésével kapcsolatos információkat tartalmaz, beleértve a következőket:

    * A legrégebbi és a legújabb visszaállítási pontok

    * Az adatbázis utolsó 24 és 72 órájának naplózási biztonsági mentési állapota

    ![Adatbázis menü](media/sap-hana-db-restore/database-menu.png)

1. A **visszaállítási adatbázis** kiválasztása

1. A **konfiguráció visszaállítása** területen adja meg, hogy hol (vagy hogyan) szeretné visszaállítani az adatvisszaállítást:

    * **Másik hely**: állítsa vissza az adatbázist egy másik helyre, és tartsa meg az eredeti forrás-adatbázist.

    * **Adatbázis felülírása**: állítsa vissza az adatforrást ugyanarra a SAP HANA példányra, mint az eredeti forrást. Ez a beállítás felülírja az eredeti adatbázist.

      ![Konfiguráció visszaállítása](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

1. A visszaállítási **konfiguráció** menüben, a **visszaállítás helye** területen válassza a **másik hely** lehetőséget.

    ![Visszaállítás másik helyre](media/sap-hana-db-restore/restore-alternate-location.png)

1. Válassza ki azt a SAP HANA állomásnevet és példánynév, amelyre vissza kívánja állítani az adatbázist.
1. Ellenőrizze, hogy a cél SAP HANA-példány készen áll-e a visszaállításra a **biztonsági mentési készültségének** biztosításával. További részletekért tekintse meg az [Előfeltételek című szakaszt](#prerequisites) .
1. A **visszaállított adatbázis neve** mezőbe írja be a céladatbázis nevét.

    > [!NOTE]
    > Önálló adatbázis tároló (SDC) visszaállításának az alábbi [ellenőrzéseknek](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)kell megfelelnie.

1. Ha van ilyen, válassza **a felülírás lehetőséget, ha a kiválasztott HANA-példányon már van ilyen nevű adatbázis**.
1. Válassza az **OK** lehetőséget.

    ![Konfiguráció visszaállítása – végső képernyő](media/sap-hana-db-restore/restore-configuration-last.png)

1. A **visszaállítási pont kiválasztása lapon** válassza ki a **naplók (** adott időpontban) lehetőséget [egy adott időpontra való visszaállításhoz](#restore-to-a-specific-point-in-time). Vagy válassza a **teljes & különbözetet** [egy adott helyreállítási pontra való visszaállításhoz](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

1. A visszaállítási **konfiguráció** menüben, a **visszaállítás helye** területen válassza az **adatbázis felülírása**  >  **OK** elemet.

    ![Adatbázis felülírása](media/sap-hana-db-restore/overwrite-db.png)

1. A **visszaállítási pont kiválasztása lapon** válassza ki a **naplók (** adott időpontban) lehetőséget [egy adott időpontra való visszaállításhoz](#restore-to-a-specific-point-in-time). Vagy válassza a **teljes & különbözetet** [egy adott helyreállítási pontra való visszaállításhoz](#restore-to-a-specific-recovery-point).

### <a name="restore-as-files"></a>Visszaállítás fájlként

A biztonsági mentési adatfájlok adatbázis helyett fájlként történő visszaállításához válassza a **visszaállítás fájlként** lehetőséget. Ha a fájlok egy megadott elérési útra lettek kiadva, ezeket a fájlokat bármely olyan SAP HANA-gépre elvégezheti, ahol adatbázisként szeretné visszaállítani őket. Mivel ezeket a fájlokat bármely gépre áthelyezheti, mostantól visszaállíthatja az összes előfizetést és régiót.

1. A **konfiguráció visszaállítása** menüben a visszaállítás **helye** alatt válassza a **visszaállítás fájlként** lehetőséget.
1. Válassza ki a **gazdagép** /HANA-kiszolgáló nevét, amelyre vissza kívánja állítani a biztonságimásolat-fájlokat.
1. A **kiszolgáló célhelyének elérési útján** adja meg a mappa elérési útját a 2. lépésben kiválasztott kiszolgálón. Ez az a hely, ahol a szolgáltatás kiírja az összes szükséges biztonságimásolat-fájlt.

    A dömpingelt fájlok a következők:

    * Adatbázis biztonságimásolat-fájljai
    * Katalógus fájljai
    * JSON-metaadatok fájljai (minden érintett biztonságimásolat-fájlhoz)

    A célként megadott elérési úttal megegyező hálózati megosztási elérési út vagy egy csatlakoztatott Azure-fájlmegosztás elérési útja lehetővé teszi, hogy a fájlok könnyebben hozzáférhessenek az azonos hálózatban lévő más gépekhez, illetve az azokhoz csatlakoztatott Azure-fájlmegosztáshoz.

    >[!NOTE]
    >Ha vissza szeretné állítani az adatbázis biztonsági másolatának fájljait egy olyan Azure-fájlmegosztás számára, amely a cél regisztrált virtuális gépen van csatlakoztatva, győződjön meg arról, hogy a főfiók rendelkezik írási/olvasási engedéllyel az Azure-fájlmegosztás számára.

    ![Cél elérési útjának kiválasztása](media/sap-hana-db-restore/restore-as-files.png)

1. Válassza ki azt a **visszaállítási pontot** , amelyik megfelel a biztonságimásolat-fájlok és mappák helyreállításának.

    ![Visszaállítási pont kiválasztása](media/sap-hana-db-restore/select-restore-point.png)

1. A rendszer a kijelölt visszaállítási ponthoz társított összes biztonságimásolat-fájlt a célhely elérési útjára írja.
1. A kiválasztott visszaállítási pont (**időpont** vagy **teljes & különbözet**) alapján egy vagy több, a célként megadott elérési úton létrehozott mappa jelenik meg. Az egyik nevű mappa `Data_<date and time of restore>` tartalmazza a teljes és a különbözeti biztonsági mentést, és a nevű másik mappa `Log` tartalmazza a naplók biztonsági másolatait.
1. Helyezze át ezeket a visszaállított fájlokat arra a SAP HANA kiszolgálóra, amelyen adatbázisként szeretné visszaállítani őket.
1. Ez után kövesse az alábbi lépéseket:
    1. Állítsa be az engedélyeket a mappához/könyvtárhoz, ahol a biztonsági mentési fájlokat a következő paranccsal tárolja a rendszer:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. Futtassa a következő parancsokat `<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. A katalógus fájljának előállítása a visszaállításhoz. Bontsa ki a **biztonsági másolat azonosítója** a JSON metaadat-fájljából a teljes biztonsági mentéshez, amelyet később a visszaállítási művelet során fog használni. Győződjön meg arról, hogy a teljes és a naplózott biztonsági mentés különböző mappákban található, és törölje a katalógus fájljait és a JSON-metaadatok fájljait ezekben a mappákban.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        A fenti parancsban:

        * `<DataFileDir>` – a teljes biztonsági mentést tartalmazó mappa
        * `<LogFilesDir>` – a napló biztonsági másolatait tartalmazó mappa
        * `<PathToPlaceCatalogFile>` – az a mappa, ahová a katalógust létre kell hozni

    1. Állítsa vissza az újonnan létrehozott katalógusfájlt a HANA studión keresztül, vagy futtassa a HDBSQL-visszaállítási lekérdezést ezzel az újonnan létrehozott katalógussal. Az HDBSQL-lekérdezések az alábbi listában láthatók:

    * Visszaállítás egy adott időpontra:

        Ha új visszaállított adatbázist hoz létre, futtassa a HDBSQL parancsot egy új adatbázis létrehozásához, `<DatabaseName>` majd állítsa le az adatbázist a visszaállításhoz. Ha azonban csak egy meglévő adatbázist állít helyre, futtassa a HDBSQL parancsot az adatbázis leállításához.

        Ezután futtassa a következő parancsot az adatbázis visszaállításához:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` – A visszaállítani kívánt új adatbázis vagy meglévő adatbázis neve
        * `<Timestamp>` – Az időponthoz tartozó visszaállítás pontos időbélyege
        * `<DatabaseName@HostName>` – Annak az adatbázisnak a neve, amelynek biztonsági másolatát a rendszer a visszaállításhoz használja, valamint a **gazdagép** -vagy SAP HANA-kiszolgáló nevét, amelyen az adatbázis található. A `USING SOURCE <DatabaseName@HostName>` beállítással megadhatja, hogy az adatbiztonsági másolat (visszaállításra használt) egy másik SID vagy nevű adatbázis, mint a cél SAP HANA gép. Így nem kell megadni a visszaállítások megadását ugyanazon a HANA-kiszolgálón, amelyről a biztonsági mentés készül.
        * `<PathToGeneratedCatalogInStep3>`– A **C lépésben** létrehozott katalógusfájl elérési útja
        * `<DataFileDir>` – a teljes biztonsági mentést tartalmazó mappa
        * `<LogFilesDir>` – a napló biztonsági másolatait tartalmazó mappa
        * `<BackupIdFromJsonFile>`– a **C lépésben** kinyert **biztonsági másolat azonosítója**

    * Visszaállítás egy adott teljes vagy különbözeti biztonsági másolatra:

        Ha új visszaállított adatbázist hoz létre, futtassa a HDBSQL parancsot egy új adatbázis létrehozásához, `<DatabaseName>` majd állítsa le az adatbázist a visszaállításhoz. Ha azonban csak egy meglévő adatbázist állít helyre, futtassa a HDBSQL parancsot az adatbázis leállításához:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` – a visszaállítani kívánt új adatbázis vagy meglévő adatbázis neve
        * `<Timestamp>` – az időponthoz tartozó visszaállítás pontos időbélyege
        * `<DatabaseName@HostName>` – annak az adatbázisnak a neve, amelynek biztonsági másolatát a rendszer a visszaállításhoz használja, valamint a **gazdagép** -vagy SAP HANA-kiszolgáló nevét, amelyen az adatbázis található. A `USING SOURCE <DatabaseName@HostName>`  beállítással megadhatja, hogy az adatbiztonsági másolat (visszaállításra használt) egy másik SID vagy nevű adatbázis, mint a cél SAP HANA gép. Ezért nem kell megadnia a visszaállításokat azon a HANA-kiszolgálón, amelyről a biztonsági mentés készült.
        * `<PathToGeneratedCatalogInStep3>`– a **C lépésben** létrehozott katalógusfájl elérési útja
        * `<DataFileDir>` – a teljes biztonsági mentést tartalmazó mappa
        * `<LogFilesDir>` – a napló biztonsági másolatait tartalmazó mappa
        * `<BackupIdFromJsonFile>`– a **C lépésben** kinyert **biztonsági másolat azonosítója**

### <a name="restore-to-a-specific-point-in-time"></a>Visszaállítás adott időpontra

Ha a **naplókat (időpontot)** a visszaállítási típusként választotta, tegye a következőket:

1. Válasszon ki egy helyreállítási pontot a log gráfból, majd kattintson az **OK** gombra a visszaállítási pont kiválasztásához.

    ![Visszaállítási pont](media/sap-hana-db-restore/restore-point.png)

1. A visszaállítási **menüben válassza a visszaállítás** **lehetőséget a visszaállítási** feladatok elindításához.

    ![Visszaállítás kiválasztása](media/sap-hana-db-restore/restore-restore.png)

1. Kövesse nyomon a visszaállítási folyamatot az **értesítések** területén, vagy nyomon követheti az adatbázis menü **feladatok visszaállítása** elemét választva.

    ![A visszaállítás sikeresen elindítva](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Visszaállítás adott helyreállítási pontra

Ha a **teljes & különbözetet** választotta a visszaállítási típusként, tegye a következőket:

1. Válasszon ki egy helyreállítási pontot a listából, és kattintson az **OK** gombra a visszaállítási pont kiválasztásához.

    ![Adott helyreállítási pont visszaállítása](media/sap-hana-db-restore/specific-recovery-point.png)

1. A visszaállítási **menüben válassza a visszaállítás** **lehetőséget a visszaállítási** feladatok elindításához.

    ![Visszaállítási feladatok indítása](media/sap-hana-db-restore/restore-specific.png)

1. Kövesse nyomon a visszaállítási folyamatot az **értesítések** területén, vagy nyomon követheti az adatbázis menü **feladatok visszaállítása** elemét választva.

    ![Visszaállítási folyamat](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > Több adatbázis-tárolóban (MDC) a rendszer visszaállítja a rendszeradatbázist a célként megadott példányra, az egyiknek újra kell futtatnia az előzetes regisztrációs parancsfájlt. A következő bérlői adatbázis-visszaállítások sikeresek lesznek. További információt a [Hibaelhárítás – MDC visszaállítás](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)című témakörben talál.

## <a name="cross-region-restore"></a>Régiók közötti visszaállítás

A visszaállítási lehetőségek egyike, a tartományok közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépeken üzemeltetett SAP HANA adatbázisok visszaállítását egy másodlagos régióban, amely egy Azure párosított régió.

Ha az előzetes verzióban szeretné bejelentkezni a szolgáltatásba, olvassa el az előkészületek [című szakaszt](./backup-create-rs-vault.md#set-cross-region-restore).

Ha szeretné megtekinteni, hogy a CRR engedélyezve van-e, kövesse a [tartományok közötti visszaállítás konfigurálása](backup-create-rs-vault.md#configure-cross-region-restore) című témakör útmutatását.

### <a name="view-backup-items-in-secondary-region"></a>Biztonsági másolati elemek megtekintése a másodlagos régióban

Ha a CRR engedélyezve van, megtekintheti a másodlagos régió biztonsági másolati elemeit.

1. A portálon nyissa meg **Recovery Services**  >  **tároló biztonsági másolati elemeit**.
1. Válassza a **másodlagos régió** elemet a másodlagos régió elemeinek megtekintéséhez.

>[!NOTE]
>A listában csak a CRR funkciót támogató biztonsági mentési felügyeleti típusok jelennek meg. Jelenleg csak a másodlagos régióba tartozó adatelemek másodlagos régióba való visszaállításának támogatása engedélyezett.

![Biztonsági másolati elemek a másodlagos régióban](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![Adatbázisok a másodlagos régióban](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Visszaállítás másodlagos régióban

A másodlagos régió visszaállítási felhasználói felülete hasonló lesz az elsődleges régió visszaállítási felhasználói felületéhez. Amikor konfigurálja a részleteket a visszaállítási konfiguráció ablaktáblán a visszaállítás konfigurálásához, a rendszer kérni fogja, hogy csak a másodlagos régió paramétereit adja meg.

![Hol és hogyan kell visszaállítani?](./media/sap-hana-db-restore/restore-secondary-region.png)

>[!NOTE]
>A másodlagos régióban lévő virtuális hálózatot egyedi módon kell hozzárendelni, és nem használható az adott erőforráscsoport bármely más virtuális gépe számára.

![Trigger visszaállítása folyamatban lévő értesítésben](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>* A visszaállítás elindítása és az adatátviteli fázisban a visszaállítási feladatot nem lehet megszakítani.
>* A másodlagos régióban való visszaállításhoz szükséges Azure-szerepkörök ugyanazok, mint az elsődleges régióban.

### <a name="monitoring-secondary-region-restore-jobs"></a>Másodlagos régió visszaállítási feladatainak figyelése

1. A portálon nyissa meg **Recovery Services**-  >  **tároló biztonsági mentési feladatait**
1. Válassza a **másodlagos régió** elemet a másodlagos régió elemeinek megtekintéséhez.

    ![Biztonsági mentési feladatok szűrve](./media/sap-hana-db-restore/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, hogyan](sap-hana-db-manage.md) kezelheti a biztonsági mentést SAP HANA-adatbázisokat a Azure Backup használatával
