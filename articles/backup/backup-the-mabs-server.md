---
title: MABS-kiszolgáló biztonsági mentése
description: Ismerje meg, hogyan készíthet biztonsági másolatot a Microsoft Azure Backup-kiszolgálóról (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 81a6ee005e15b1d7ab7b11a938b8ab14143818f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92172125"
---
# <a name="back-up-the-mabs-server"></a>MABS-kiszolgáló biztonsági mentése

Annak biztosítása érdekében, hogy az Microsoft Azure Backup-kiszolgáló (MABS) meghibásodása esetén az adat helyreállítható legyen, szüksége lesz egy stratégiára a MABS-kiszolgáló biztonsági mentéséhez. Ha nem készít biztonsági mentést, a hiba után manuálisan kell újraépíteni, és a lemezes helyreállítási pontok nem lesznek helyreállítva. A MABS-kiszolgálók biztonsági mentését a MABS-adatbázis biztonsági mentésével végezheti el.

## <a name="back-up-the-mabs-database"></a>A MABS-adatbázis biztonsági mentése

A MABS biztonsági mentési stratégiájának részeként biztonsági mentést kell készítenie a MABS-adatbázisról. A MABS-adatbázis neve DPMDB. Ez az adatbázis tartalmazza a MABS konfigurációját, valamint a MABS biztonsági mentésével kapcsolatos adatokat. Vészhelyzet esetén a MABS-kiszolgáló legtöbb funkciója újraépíthető az adatbázis legutóbbi biztonsági másolatából. Feltéve, hogy vissza tudja állítani az adatbázist, a szalagos biztonsági mentések elérhetők, és megtartják a védelmi csoport összes beállítását és a biztonsági mentési ütemezéseket. Ha a MABS-tároló lemezeit nem érinti a leállás, a lemezes biztonsági mentések Újraépítés után is használhatók. Az adatbázis biztonsági mentése számos különböző módszerrel végezhető.

|Adatbázis biztonsági mentésének módszerei|Előnyök|Hátrányok|
|--------------------------|--------------|-----------------|
|[Biztonsági mentés az Azure platformra](#back-up-to-azure)|<li>Egyszerűen konfigurálható és figyelhető a MABS-ben.<li>Az adatbázisfájlok biztonsági másolatai több helyen is megtalálhatók.<li>A felhőalapú tárolás hatékony vész-helyreállítási megoldást kínál.<li>Az adatbázis nagyon biztonságosan tárolható.<li>Csak 120 online helyreállítási pontot támogat.|<li>Azure-fiókra és további MABS-konfigurációra van szükség. Az Azure-tárolás költségekkel jár.<li> A a Windows Server-alapú rendszer támogatott verzióját igényli az Azure-ügynökkel, hogy hozzáférjen az Azure Backup-tárolóban tárolt MABS biztonsági másolatokhoz. Ez nem lehet egy másik MABS-kiszolgáló.<li>Nem használható, ha az adatbázis egy helyi gazdagépen található, és szeretné engedélyezni a másodlagos védelmet. <li>Külön előkészületekre és helyreállítási időre van szükség.|
|[Az adatbázis biztonsági mentése a MABS Storage-készlet biztonsági mentésével](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Egyszerűen konfigurálható és figyelhető.<li>A biztonsági mentést a MABS Storage-készlet lemezei tárolják, és könnyen elérhető helyileg.<li>A MABS ütemezett biztonsági mentések támogatják a 512 expressz teljes biztonsági mentést. Ha óránként készít biztonsági mentést, a teljes védelem 21 nap lesz.|<li>Vészhelyreállításra nem jó választás. Előfordulhat, hogy az online állapot és a helyreállítás nem a várt módon fog működni, ha a MABS-kiszolgáló vagy a Storage-készlet lemeze meghiúsul.<li>Nem használható, ha az adatbázis egy helyi gazdagépen található, és szeretné engedélyezni a másodlagos védelmet. <li>Bizonyos előkészítési és speciális lépések szükségesek ahhoz, hogy hozzáférjenek a helyreállítási pontokhoz, ha a MABS szolgáltatás vagy-konzol nem fut vagy nem működik.|
|[Natív SQL Server biztonsági mentés helyi lemezre](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Be van építve az SQL Serverbe.<li>A biztonsági mentést egy könnyen elérhető helyi lemezen kell tárolni.<li>Tetszés szerinti gyakorisággal ütemezhető a futtatása.<li>Teljesen független a MABS.<li>A biztonságimásolat-fájlok tisztítása ütemezhető.|<li>Vészhelyreállításra nem jó választás, hacsak a biztonsági másolatokat át nem másolja egy távoli helyre.<li>Helyi tárterületet igényel a biztonsági mentések számára, ami korlátozhatja a megőrzést és a gyakoriságot.|
|[Biztonsági mentés natív SQL Backup-és MABS-védelemmel a MABS által védett megosztásra](#back-up-to-a-share-protected-by-mabs)|<li>Könnyen megfigyelhető a MABS-ben.<li>Az adatbázisfájlok biztonsági másolatai több helyen is megtalálhatók.<li>A hálózaton lévő bármely Windows-gépről könnyen hozzáférhető.<li>Potenciálisan ez a leggyorsabb helyreállítási módszer.|<li>Csak 64 helyreállítási pontot támogat.<li>Helyszíni vészhelyreállításra nem jó választás. A MABS-kiszolgáló vagy a MABS-tároló lemezének meghibásodása akadályozhatja a helyreállítási erőfeszítéseket.<li>Nem választható, ha a MABS-adatbázis helyileg van tárolva, és engedélyezni szeretné a másodlagos védelmet. <li>A konfigurálás és a vizsgálat külön előkészületeket igényel.<li>Néhány további előkészítési és helyreállítási idő szükséges ahhoz, hogy a MABS-kiszolgáló le legyen hajtva, de a MABS lemezei rendben vannak.|

- Ha MABS védelmi csoport használatával végez biztonsági mentést, javasoljuk, hogy egyedi védelmi csoportot használjon az adatbázishoz.

    > [!NOTE]
    > Visszaállítási célból a MABS-adatbázissal visszaállítani kívánt MABS-telepítésnek meg kell egyeznie a MABS-adatbázis verziójával.  Ha például a helyreállítani kívánt adatbázis egy 1. kumulatív frissítéssel rendelkező MABS v3 verzióban található, akkor a MABS-kiszolgálónak az 1. kumulatív frissítéssel megegyező verziót kell futtatnia. Ez azt jelenti, hogy előfordulhat, hogy az adatbázis visszaállítása előtt el kell távolítania és újra kell telepítenie a MABS-t egy kompatibilis verzióval.  Előfordulhat, hogy az adatbázis verziójának ellenőrzéséhez manuálisan kell csatlakoztatnia azt egy ideiglenes adatbázisnévhez, majd SQL-lekérdezést kell futtatnia rajta, hogy ellenőrizni tudja a legutóbb telepített kumulatív frissítést a fő- és alverzió alapján.

- A MABS-adatbázis verziójának megadásához kövesse az alábbi lépéseket:

    1. A lekérdezés futtatásához nyissa meg az SQL Management Studio, majd kapcsolódjon a MABS-adatbázist futtató SQL-példányhoz.

    2. Válassza ki a MABS-adatbázist, majd indítson el egy új lekérdezést.

    3. Illessze be a következő SQL-lekérdezést a lekérdezési ablaktáblába, és futtassa:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Ha a lekérdezés eredménye nem ad vissza semmit, vagy ha a MABS-kiszolgáló frissítve lett a korábbi verziókról, de azóta nem telepítettek kumulatív frissítést, akkor nem lesz bejegyzés a MABS alapszintű telepítéséhez szükséges főverzióhoz. A kumulatív frissítésekhez társított MABS-verziók megtekintéséhez tekintse [meg a MABS összeállítási számai listáját](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx).

### <a name="back-up-to-azure"></a>Biztonsági mentés az Azure platformra

1. Mielőtt elkezdené, futtatnia kell egy parancsfájlt, hogy lekérje a MABS replika kötet csatlakoztatási pontjának elérési útját, hogy tudja, melyik helyreállítási pont tartalmazza a MABS biztonsági mentését. Ezt az Azure Backuppal való első replikációt követően tegye meg. A parancsfájlban cserélje le a helyére a `dplsqlservername%` MABS-adatbázist futtató SQL Server példány nevét.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Győződjön meg arról, hogy az Azure Recovery Services ügynök telepítése és a MABS-kiszolgáló a Azure Backup-tárolóban való regisztrálása során meg van adva a PIN-kód. Erre a PIN-kódra szüksége lesz a biztonsági mentés visszaállításához.

2. Hozzon létre egy Azure Backup-tárolót, töltse le az Azure Backup-ügynök telepítési fájlját és a tároló hitelesítő adatait. A telepítési fájl futtatásával telepítse az ügynököt a MABS-kiszolgálóra, és a tároló hitelesítő adataival regisztrálja a MABS-kiszolgálót a tárolóban. [További információ](backup-azure-microsoft-azure-backup.md).

3. Miután konfigurálta a tárolót, állítson be egy MABS védelmi csoportot, amely tartalmazza a MABS-adatbázist. Válassza ki a lemezre és az Azure-ra történő biztonsági mentést.

#### <a name="recover-the-mabs-database-from-azure"></a>A MABS-adatbázis helyreállítása az Azure-ból

Az adatbázist az Azure-ból a Azure Backup-tárolóban regisztrált bármely MABS-kiszolgálóval helyreállíthatja, a következőképpen:

1. A MABS-konzolon válassza a **helyreállítás**  >  **külső MABS hozzáadása** elemet.

2. Adja meg a tároló hitelesítő adatait (töltse le a Azure Backup-tárból). Ne feledje, hogy a hitelesítő adatok csak két napig érvényesek.

3. A **külső MABS kiválasztása helyreállításhoz** területen válassza ki azt a MABS-kiszolgálót, amelynek helyre kívánja állítani az adatbázisát, írja be a titkosítási jelszót, majd kattintson **az OK gombra.**

4. Válassza ki a használni kívánt helyreállítási pontot az elérhető helyreállítási pontok listájából. Válassza a **külső MABS törlése** lehetőséget a helyi MABS nézethez való visszatéréshez.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>A MABS-adatbázis biztonsági mentése a MABS Storage-készletbe

> [!NOTE]  
> Ez a beállítás a MABS és a modern biztonsági másolati tárhely esetében alkalmazható.

1. A MABS-konzolon válassza a **védelem** védelmi  >  **csoport létrehozása** lehetőséget.
2. A **védelmi csoport típusának kiválasztása** lapon válassza a **kiszolgálók** elemet.
3. A **csoporttagok kiválasztása** lapon válassza a **DPM-adatbázis** lehetőséget. Bontsa ki a MABS-kiszolgálót, és válassza a DPMDB lehetőséget.
4. Az **adatvédelmi módszer kiválasztása** oldalon válassza a **rövid távú védelmet szeretnék a lemez használatával** lehetőséget. Adja meg a rövid távú védelmi házirend beállításait.
5. A MABS-adatbázis kezdeti replikációja után futtassa a következő SQL-parancsfájlt:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>MABS-adatbázis helyreállítása

Ahhoz, hogy a MABS újra létrehozza ugyanazzal az ADATBÁZISsal, először helyre kell állítania a MABS-adatbázist, és szinkronizálnia kell a frissen telepített MABS.

#### <a name="use-the-following-steps"></a>Kövesse az alábbi lépéseket

1. Nyisson meg egy rendszergazdai parancssort, és futtassa a parancsot `psexec.exe -s powershell.exe` a PowerShell-ablak rendszerkörnyezetben való elindításához.
2. Döntse el, hogy hol szeretné helyreállítani az adatbázist:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Az adatbázis másolása az utolsó biztonsági mentésből

1. A replika VHD elérési útjának navigálása `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Csatlakoztassa a **Disk0. vhdx** parancsot a parancs használatával `mount-vhd disk0.vhdx` .
3. A replika virtuális merevlemez csatlakoztatása után `mountvol.exe` a használatával rendeljen hozzá egy meghajtóbetűjelet a replika kötetéhez az SQL-parancsfájl kimenetében található fizikai REPLIKA azonosító használatával. Például: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Az adatbázis másolása egy korábbi helyreállítási pontról

1. Navigáljon a DPMDB tároló könyvtárába  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . A MABS-ADATBÁZIShoz tartozó helyreállítási pontok alapján több könyvtárat is látni fog, néhány egyedi GUID azonosítóval. A többi könyvtár a PIT/helyreállítási pontot jelöli.
2. Navigáljon a PIT VHD elérési útjához, például `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` csatlakoztassa a **Disk0. vhdx** fájlt a parancs használatával `mount-vhd disk0.vhdx` .
3. A replika virtuális merevlemez csatlakoztatása után a használatával `mountvol.exe` rendeljen hozzá egy meghajtóbetűjelet a replika kötetéhez az SQL-parancsfájl kimenetében található fizikai REPLIKA azonosító használatával. Például: `mountvol X: \?\Volume{}\`

   A fenti lépésekben a szögletes zárójelekkel megjelenő összes kifejezés elhelyezhető birtokos. A következő módon cserélje le őket a megfelelő értékekre:
   - **ReFSVolume** – elérési út az SQL-parancsfájl kimenetéről
   - **MABSServer teljes tartományneve** – a MABS-kiszolgáló teljesen minősített neve
   - **PhysicalReplicaId** – fizikai replika azonosítója az SQL-szkriptből
   - **Sajnált** – GUID azonosító, amely nem a fizikai replika azonosítóját jelöli a tároló könyvtárában.
4. Nyisson meg egy másik rendszergazdai parancssort, és futtassa a `psexec.exe -s cmd.exe` parancsot a parancssor indításához a rendszerkörnyezetben.
5. Módosítsa a könyvtárat az X: meghajtóra, és navigáljon a MABS-adatbázisfájlok helyére.
6. Másolja őket egy olyan helyre, ahonnan könnyen visszaállíthatja őket. A másolás után lépjen ki a PsExec cmd ablakból.
7. Lépjen az 1. lépésben megnyitott PsExec PowerShell-ablakra, navigáljon a VHDX elérési útra, és a parancs használatával válassza le a VHDX `dismount-vhd disk0.vhdx` .
8. A MABS-kiszolgáló újratelepítése után a visszaállított DPMDB a parancs futtatásával csatolhat a MABS-kiszolgálóhoz `DPMSYNC-RESTOREDB` .
9. `DPMSYNC-SYNC`A Futtatás egyszer `DPMSYNC-RESTOREDB` befejeződött.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Az adatbázis biztonsági mentése a MABS Storage-készlet biztonsági mentésével

> [!NOTE]
> Ez a beállítás a örökölt tárolóval rendelkező MABS esetében alkalmazható.

Mielőtt elkezdené, futtatnia kell egy parancsfájlt, hogy lekérje a MABS replika kötet csatlakoztatási pontjának elérési útját, hogy tudja, melyik helyreállítási pont tartalmazza a MABS biztonsági mentését. Ezt az Azure Backuppal való első replikációt követően tegye meg. A parancsfájlban cserélje le a helyére a `dplsqlservername%` MABS-adatbázist futtató SQL Server példány nevét.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. A MABS-konzolon válassza a **védelem** védelmi  >  **csoport létrehozása** lehetőséget.

2. A **Védelmi csoport típusának kiválasztása** lapon válassza a **Kiszolgálók** elemet.

3. A **csoporttagok kiválasztása** lapon válassza ki a MABS-adatbázist. Bontsa ki a MABS-kiszolgáló elemet, és válassza a **DPMDB** lehetőséget.

4. Az  **adatvédelmi módszer kiválasztása** oldalon válassza a **rövid távú védelmet szeretnék a lemez használatával** lehetőséget. Adja meg a rövid távú védelmi házirend beállításait. A MABS-adatbázisok esetében két hetes megőrzési tartományt ajánlunk.

#### <a name="recover-the-database"></a>Adatbázis helyreállítása

Ha a MABS-kiszolgáló továbbra is működőképes, és a tárolási készlet sértetlen (például a MABS szolgáltatással vagy konzollal kapcsolatos problémákkal), akkor másolja az adatbázist a replika kötetről vagy egy árnyékmásolat-másolatból a következő módon:

1. Döntse el, hogy mikori állapotát szeretné visszaállítani az adatbázisnak.

    - Ha az adatbázist az utolsó biztonsági mentésből közvetlenül az MABS-replika kötetéről szeretné átmásolni, akkor a **mountvol.exe** használatával rendeljen hozzá egy meghajtóbetűjelet a replika kötetéhez az SQL-parancsfájl kimenetének GUID azonosítójának használatával. Például: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Ha az adatbázist egy korábbi helyreállítási pontról (árnyékmásolat) szeretné másolni, akkor az SQL-parancsfájl kimenetében található kötet GUID azonosítóval fel kell sorolnia a replikához tartozó összes árnyékmásolatot. Ez a parancs felsorolja az adott kötet árnyékmásolatait: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Jegyezze fel a létrehozási időt és az árnyékmásolat-azonosítót, amelyből helyre kívánja állítani a helyreállítást.

2. Ezután a **diskshadow.exe** használatával csatlakoztassa az árnyékmásolatot egy nem használt X betűjelű meghajtóbetűjelhez: az ÁRNYÉKMÁSOLAT-azonosító használatával, hogy átmásolja az adatbázisfájlok.

3. Nyisson meg egy rendszergazdai parancssort, és futtassa a `psexec.exe -s cmd.exe` parancsot a parancssor indításához a rendszerkörnyezetben, hogy jogosult legyen a replika kötetre navigálni (X:) és másolja a fájlokat.

4. A CD-t az X: meghajtóra, és navigáljon a MABS-adatbázisfájlok helyére. Másolja őket egy olyan helyre, ahonnan könnyen visszaállíthatja őket. A másolás befejezése után futtassa a PsExec cmd ablakot, és futtassa **diskshadow.exe** , és tegye elérhetővé az X: kötetet.

5. Most visszaállíthatja az adatbázisfájlok SQL Management Studio használatával vagy **DPMSYNC- \- RESTOREDB** futtatásával.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Natív SQL Server biztonsági mentés helyi lemezre

A MABS-adatbázis biztonsági mentését egy helyi lemezre is elvégezheti natív SQL Server biztonsági mentéssel, függetlenül a MABS-től.

- Az SQL Server biztonsági mentés [áttekintése](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases).

- [További információk](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) az SQL Servernek a felhőbe történő biztonsági mentéséről.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Biztonsági mentés egy MABS által védett megosztásra

Ez a biztonsági mentési lehetőség natív SQL-t használ a MABS-adatbázis biztonsági mentéséhez egy megosztásra, védi a megosztást a MABS, és a Windows VSS korábbi verzióit használja a visszaállítás megkönnyítésére.

### <a name="before-you-start"></a>Előkészületek

1. A SQL Serveron hozzon egy mappát egy olyan meghajtón, amely elegendő szabad hellyel rendelkezik a biztonsági másolat egyetlen másolatának tárolásához. Példa: `C:\MABSBACKUP`.

1. Ossza meg a mappát. Például a mappa megosztása `C:\MABSBACKUP` *DPMBACKUP*.

1. Másolja és illessze be az alábbi NOSQL-parancsot a Jegyzettömbbe, és mentse egy nevű fájlba `C:\MABSACKUP\bkupdb.cmd` . Győződjön meg róla, hogy nincs. txt kiterjesztés. Módosítsa a SQL_Instance_name és DPMDB_NAME, hogy egyezzen a MABS-kiszolgáló által használt példány-és DPMDB-névvel.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. A Jegyzettömb segítségével nyissa  meg a `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` MABS-kiszolgáló mappájában találhatóScriptingConfig.xmlfájlt.

1. Módosítsa **ScriptingConfig.xml** és módosítsa a **DataSourceName =** értékre a DPMDBBACKUP mappát/megosztást tartalmazó meghajtóbetűjelet. Módosítsa a PreBackupScript bejegyzést a 3. lépésben mentett **bkupdb. cmd** fájl teljes elérési útjára és nevére.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Mentse **ScriptingConfig.xml** módosításait.

1. A C:\MABSBACKUP mappát vagy a `\sqlservername\MABSBACKUP` megosztást a MABS használatával, majd várjon, amíg a kezdeti replika létre nem jön. A **dpmdb. bak** fájlnak a C:\MABSBACKUP mappában kell lennie a-t futtató biztonsági mentés előtti parancsfájl eredményeképpen, amelyet a rendszer a MABS-replikára másolt.

1. Ha nem engedélyezi az önkiszolgáló helyreállítást, néhány további lépésre lesz szüksége a MABSBACKUP mappa megosztásához a replikán:

    1. A MABS-konzolon > a **védelem** területen keresse meg a MABSBACKUP-adatforrást, és jelölje ki. A Részletek szakaszban **kattintson ide, ha meg szeretné tekinteni** a replika elérési útjára mutató hivatkozás részleteit, és másolja az elérési utat a Jegyzettömbbe. Távolítsa el a forrás elérési útját, de őrizze meg a célét. Az elérési útnak a következőhöz hasonlóan kell kinéznie: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Hozzon el egy megosztást az elérési útra a **MABSSERVERNAME-DPMDB** megosztási név használatával. Az alábbi Net Share parancsot egy rendszergazdai parancssorból használhatja.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>A biztonsági mentés konfigurálása

A MABS-adatbázis biztonsági mentését ugyanúgy végezheti el, mint bármely más SQL Server-adatbázist SQL Server natív biztonsági mentés használatával.

- Az SQL Server biztonsági mentés [áttekintése](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases).

- [További információk](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) az SQL Servernek a felhőbe történő biztonsági mentéséről.

### <a name="recover-the-mabs-database"></a>A MABS-adatbázis helyreállítása

1. Kapcsolódjon a `\\MABSServer\MABSSERVERNAME-dpmdb` megosztáshoz az Explorer használatával bármely Windows-számítógépről.

2. Kattintson a jobb gombbal a **dpmdb. bak** fájlra a tulajdonságok megtekintéséhez. A **Korábbi verziók** lapon fel van sorolva az összes kiválasztható és másolható biztonsági mentés. A legutolsó biztonsági mentés még mindig a C:\MABSBACKUP mappában található, amely szintén könnyen elérhető.

3. Ha át kell helyeznie egy Tárolóhálózati csatolt MABS-tároló lemezét egy másik kiszolgálóra, hogy el tudja olvasni a replika kötetét, vagy ha újra szeretné telepíteni a Windowst a helyileg csatlakoztatott lemezek olvasására, ismernie kell a MABS replika kötet csatlakozási pontjának elérési útját vagy a kötet GUID azonosítóját, hogy tudja, milyen kötet tárolja az adatbázis biztonsági másolatát. Az alábbi SQL-parancsfájllal a kezdeti védelem aktiválása után és a visszaállítás szükségessé válása előtt bármikor kinyerheti a szükséges információkat. Cserélje le a `%dpmsqlservername%` nevet az adatbázist futtató SQL Server nevére.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. Ha a MABS Storage-készlet lemezének vagy egy MABS-kiszolgáló újraépítésének áthelyezése után helyre kell állítania a helyreállítást:

    1. Rendelkezik a kötet GUID azonosítóval, így ha a kötetet csatlakoztatni kell egy másik Windows-kiszolgálón, vagy a MABS-kiszolgáló újraépítése után, a **mountvol.exe** használatával rendeljen hozzá egy meghajtóbetűjelet az SQL-parancsfájl kimenetében található kötet GUID azonosítójának használatával: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Ossza meg újra a replika köteten található MABSBACKUP mappát a mappa szerkezetét jelképező replika elérési útjának a meghajtóbetűjelével és egy részével.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Kapcsolódjon a `\\SERVERNAME\MABSSERVERNAME-dpmdb` megosztáshoz az Explorer használatával bármely Windows-számítógépről.

    4. Kattintson a jobb gombbal a **dpmdb. bak** fájlra a tulajdonságok megtekintéséhez. A **Korábbi verziók** lapon fel van sorolva az összes kiválasztható és másolható biztonsági mentés.

## <a name="using-dpmsync"></a>A DPMSync használata

A **DpmSync** olyan parancssori eszköz, amely lehetővé teszi a MABS-adatbázis szinkronizálását a tárolóban lévő lemezek állapotával és a telepített védelmi ügynökökkel. A DpmSync visszaállítja a MABS-adatbázist, szinkronizálja a MABS-adatbázist a tárolóban lévő replikákkal, visszaállítja a jelentéskészítő adatbázist, és újraosztja a hiányzó replikákat.

### <a name="parameters"></a>Paraméterek

| Paraméter      | Leírás    |
|----------------|-----------------------------|
| **– RestoreDb**                       | Visszaállítja egy MABS-adatbázist egy adott helyről.|
| **– Szinkronizálás**                            | Szinkronizálja a visszaállított adatbázisokat. Az adatbázisok visszaállítása után futtatnia kell a **DpmSync – Sync** parancsot. A **DpmSync – Sync** futtatása után előfordulhat, hogy egyes replikák továbbra is hiányzóként vannak megjelölve. |
| **-DbLoc** *helye*                | A MABS-adatbázis biztonsági másolatának helyét azonosítja.|
| **– Példánynév** <br/>*Server \ példány*     | A példány, amelybe vissza kell állítani a DPMDB.|
| **– ReallocateReplica**         | A hiányzó replika kötetek újrafoglalása szinkronizálás nélkül. |
| **– Datacopied kapcsolók**                      | Azt jelzi, hogy befejezte az adatgyűjtés az újonnan lefoglalt replikák köteteibe való betöltését. Ez csak az ügyfélszámítógépekre vonatkozik. |

**1. példa:** A következő parancs futtatásával állíthatja vissza a MABS-adatbázist a helyi biztonsági mentési adathordozóról a MABS-kiszolgálón:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

A MABS-adatbázis visszaállítását követően az adatbázisok szinkronizálásához futtassa a következő parancsot:

```cmd
DpmSync -Sync
```

A MABS-adatbázis visszaállítása és szinkronizálása, valamint a replika visszaállítása előtt futtassa a következő parancsot a replika lemezterületének újbóli lefoglalásához:

```cmd
DpmSync -ReallocateReplica
```

**2. példa:** Ha a MABS-adatbázist egy távoli adatbázisból szeretné visszaállítani, futtassa a következő parancsot a távoli számítógépen:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

A MABS-adatbázis visszaállítását követően az adatbázisok szinkronizálásához futtassa a következő parancsot a MABS-kiszolgálón:

```cmd
DpmSync -Sync
```

A MABS-adatbázis visszaállítása és szinkronizálása, valamint a replika visszaállítása előtt futtassa a következő parancsot a MABS-kiszolgálón a replika lemezterületének újbóli lefoglalásához:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Következő lépések

- [MABS-támogatás mátrixa](backup-support-matrix-mabs-dpm.md)
- [MABS – GYAKORI KÉRDÉSEK](backup-azure-dpm-azure-server-faq.md)