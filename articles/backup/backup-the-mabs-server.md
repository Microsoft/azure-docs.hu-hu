---
title: MABS-kiszolgáló biztonsági mentése
description: Ismerje meg, hogyan lehet biztonsági Microsoft Azure Backup (MABS) kiszolgálóról.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: fbd3d1f2d7cb24c21962dbe5c88acebf73652a04
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519119"
---
# <a name="back-up-the-mabs-server"></a>MABS-kiszolgáló biztonsági mentése

Ahhoz, hogy az adatok helyreállíthatóak Microsoft Azure Backup(MABS) meghibásodása esetén, a MABS-kiszolgáló biztonságimentés-stratégiájára lesz szükség. Ha nincs biztonságimentés, manuálisan kell újraépítenie egy hiba után, és a lemezalapú helyreállítási pontok nem állíthatók helyre. A MABS-kiszolgálók biztonsági mentése a MABS-adatbázis biztonsági mentése alapján használhatja.

## <a name="back-up-the-mabs-database"></a>A MABS-adatbázis biztonsági mentése

A MABS biztonsági mentési stratégia részeként biztonsági másolatot kell készítenie a MABS-adatbázisról. A MABS-adatbázis neve DPMDB. Ez az adatbázis tartalmazza a MABS-konfigurációt, valamint a MABS biztonsági mentésének adatait. Katasztrófa esetén újraépítheti egy MABS-kiszolgáló legtöbb funkcióját az adatbázis legutóbbi biztonsági mentésének használatával. Ha vissza tudja állítani az adatbázist, a szalagos biztonsági mentések elérhetők, és minden védelmicsoport-beállítást és biztonsági mentési ütemezést megőriznek. Ha a kimaradás nem érinti a MABS tárolókészlet lemezeit, a lemezalapú biztonsági másolatok az újraépítést követően is használhatók. Az adatbázis biztonsági mentése számos különböző módszerrel végezhető.

|Adatbázis biztonsági mentésének módszerei|Előnyök|Hátrányok|
|--------------------------|--------------|-----------------|
|[Biztonsági mentés az Azure platformra](#back-up-to-azure)|<li>Könnyen konfigurálható és figyelhető a MABS-ban.<li>Az adatbázisfájlok biztonsági másolatai több helyen is megtalálhatók.<li>A felhőalapú tárolás hatékony vész-helyreállítási megoldást kínál.<li>Az adatbázis nagyon biztonságosan tárolható.<li>Csak 120 online helyreállítási pontot támogat.|<li>Azure-fiókra és további MABS-konfigurációra van szükség. Az Azure-tárolás költségekkel jár.<li> Az Azure-ügynökkel együtt a Windows Server-alapú rendszer támogatott verziójára van szükség az Azure Backup-tárolóban tárolt MABS biztonsági másolatok eléréséhez. Ez nem lehet egy másik MABS-kiszolgáló.<li>Nem használható, ha az adatbázis egy helyi gazdagépen található, és szeretné engedélyezni a másodlagos védelmet. <li>Külön előkészületekre és helyreállítási időre van szükség.|
|[Az adatbázis biztonsági mentése a MABS-tárolókészlet biztonsági mentése alapján](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Egyszerűen konfigurálható és figyelhető.<li>A biztonsági másolat a MABS tárolókészlet lemezén marad, és helyileg könnyen elérhető.<li>A MABS ütemezett biztonsági mentései 512 expressz teljes biztonsági mentést támogatnak. Ha óránként biztonságimenti, 21 napig teljes védelemmel fog fog esni.|<li>Vészhelyreállításra nem jó választás. Előfordulhat, hogy online állapotban van, és a helyreállítás nem a várt módon működik, ha a MABS-kiszolgáló vagy a tárolókészlet lemeze meghibásodik.<li>Nem használható, ha az adatbázis egy helyi gazdagépen található, és szeretné engedélyezni a másodlagos védelmet. <li>A helyreállítási pontok eléréséhez előkészületek és különleges lépések szükségesek, ha a MABS szolgáltatás vagy -konzol nem fut vagy nem működik.|
|[Natív SQL Server biztonsági mentés helyi lemezre](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Be van építve az SQL Serverbe.<li>A biztonsági másolat egy könnyen elérhető helyi lemezen van.<li>Tetszés szerinti gyakorisággal ütemezhető a futtatása.<li>Teljesen független a MABS-től.<li>A biztonságimásolat-fájlok tisztítása ütemezhető.|<li>Vészhelyreállításra nem jó választás, hacsak a biztonsági másolatokat át nem másolja egy távoli helyre.<li>Helyi tárterületet igényel a biztonsági mentéshez, ami korlátozhatja a megőrzést és a gyakoriságot.|
|[Natív SQL biztonsági mentéssel és MABS-védelemmel mabs által védett megosztásra](#back-up-to-a-share-protected-by-mabs)|<li>Könnyen monitorozás a MABS-ban.<li>Az adatbázisfájlok biztonsági másolatai több helyen is megtalálhatók.<li>A hálózaton lévő bármely Windows-gépről könnyen hozzáférhető.<li>Potenciálisan ez a leggyorsabb helyreállítási módszer.|<li>Csak 64 helyreállítási pontot támogat.<li>Helyszíni vészhelyreállításra nem jó választás. A MABS-kiszolgáló vagy a MABS-tárolókészlet lemezhibája akadályozhatja a helyreállítási erőfeszítéseket.<li>Nem választható, ha a MABS-adatbázist helyileg üzemeltetik, és engedélyezni szeretné a másodlagos védelmet. <li>A konfigurálás és a vizsgálat külön előkészületeket igényel.<li>További előkészítésre és helyreállítási időre van szükség, ha maga a MABS-kiszolgáló nem működik, de a MABS-tárolókészlet lemezei rendben vannak.|

- Ha MABS védelmi csoport használatával biztonsági mentése történik, javasoljuk, hogy egyedi védelmi csoportot használjon az adatbázishoz.

    > [!NOTE]
    > Visszaállítási célból a MABS-adatbázissal visszaállítani kívánt MABS-telepítésnek meg kell egyeznie magának a MABS-adatbázisnak a verziójával.  Ha például a helyreállítani kívánt adatbázis az 1. összesítő frissítéssel telepített MABS V3-ból származik, a MABS-kiszolgálónak az 1. összesítő frissítéssel azonos verziót kell futnia. Ez azt jelenti, hogy előfordulhat, hogy az adatbázis visszaállítása előtt el kell távolítania és újra kell telepítenie a MABS-t egy kompatibilis verzióval.  Előfordulhat, hogy az adatbázis verziójának ellenőrzéséhez manuálisan kell csatlakoztatnia azt egy ideiglenes adatbázisnévhez, majd SQL-lekérdezést kell futtatnia rajta, hogy ellenőrizni tudja a legutóbb telepített kumulatív frissítést a fő- és alverzió alapján.

- A MABS-adatbázis verziójának ellenőrzéshez kövesse az alábbi lépéseket:

    1. A lekérdezés futtatásához nyissa meg az SQL Management Studio, majd csatlakozzon a MABS-adatbázist futtató SQL-példányhoz.

    2. Válassza ki a MABS-adatbázist, majd indítson el egy új lekérdezést.

    3. Illessze be a következő SQL-lekérdezést a lekérdezési ablaktáblába, és futtassa:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Ha a lekérdezési eredmények semmit sem adnak vissza, vagy ha a MABS-kiszolgálót frissítették a korábbi verziókról, de azóta nem lett telepítve új összesítő frissítés, akkor a MABS alaptelepítéséhez nem lesz bejegyzés a fő- és alverzióhoz. A frissítésekkel társított MABS-verziók ellenőrzéséhez lásd: [A MABS](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)buildszámának listája.

### <a name="back-up-to-azure"></a>Biztonsági mentés az Azure platformra

1. Mielőtt hozzáfog, futtatnia kell egy szkriptet a MABS replikakötet csatlakoztatási pontjának elérési útjának lekéréséhez, hogy tudja, melyik helyreállítási pont tartalmazza a MABS biztonsági mentést. Ezt az Azure Backuppal való első replikációt követően tegye meg. A szkriptben cserélje le a helyére a `dplsqlservername%` MABS-adatbázist SQL Server példány nevét.

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

    Győződjön meg arról, hogy az Azure Recovery Services-ügynök telepítésekor és a MABS-kiszolgáló regisztrálva lett az Azure Backup-tárolóban. Erre a PIN-kódra szüksége lesz a biztonsági mentés visszaállításához.

2. Hozzon létre egy Azure Backup-tárolót, töltse le az Azure Backup-ügynök telepítési fájlját és a tároló hitelesítő adatait. A telepítőfájl futtatásával telepítse az ügynököt a MABS-kiszolgálóra, és a tároló hitelesítő adataival regisztrálja a MABS-kiszolgálót a tárolóban. [További információ](backup-azure-microsoft-azure-backup.md).

3. A tároló konfigurálása után állítson be egy MABS védelmi csoportot, amely tartalmazza a MABS-adatbázist. Válassza a lemezre és az Azure-ba való biztonságimentet.

#### <a name="recover-the-mabs-database-from-azure"></a>A MABS-adatbázis helyreállítása az Azure-ból

Az adatbázist az Azure-ból a tárolóban regisztrált bármely MABS-kiszolgálóval helyreállíthatja, az alábbiak szerint Azure Backup tárolóban:

1. A MABS-konzolon válassza a **Helyreállítás**  >  **Külső MABS hozzáadása lehetőséget.**

2. Adja meg a tároló hitelesítő adatait (töltse le a Azure Backup tárolóból). Ne feledje, hogy a hitelesítő adatok csak két napig érvényesek.

3. A **Select External MABS for Recovery**(Külső MABS kiválasztása helyreállításhoz) mezőben válassza ki azt a MABS-kiszolgálót, amelynek az adatbázisát helyre szeretné állítani, írja be a titkosítási jelszót, majd kattintson az OK **gombra.**

4. Válassza ki a használni kívánt helyreállítási pontot az elérhető helyreállítási pontok listájából. A **helyi MABS-nézethez** való visszatéréshez válassza a Külső MABS ürítő lehetőséget.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>MABS-adatbázis biztonsági mentése MABS-tárolókészletbe

> [!NOTE]  
> Ez a lehetőség a mabs és a modern biztonsági másolati tárhely.

1. A MABS-konzolon válassza a **Védelem Védelmi** csoport  >  **létrehozása lehetőséget.**
2. A Védelmi **csoport típusának kiválasztása lapon** válassza a Kiszolgálók **lehetőséget.**
3. A **Csoporttagok kiválasztása lapon** válassza a **DPM-adatbázis.** Bontsa ki a MABS-kiszolgálót, és válassza a DPMDB lehetőséget.
4. Az **Adatvédelmi módszer kiválasztása lapon** válassza a Rövid távú lemezes **védelmet szeretnék lehetőséget.** Adja meg a rövid távú védelmi házirend beállításait.
5. A MABS-adatbázis kezdeti replikálása után futtassa a következő SQL-szkriptet:

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

Ahhoz, hogy a MABS-t ugyanazokkal az adatbázissal rekonstruálja, először helyre kell állítania a MABS-adatbázist, és szinkronizálni kell a frissen telepített MABS-sel.

#### <a name="use-the-following-steps"></a>Kövesse az alábbi lépéseket

1. Nyisson meg egy rendszergazdai parancssort, és futtassa `psexec.exe -s powershell.exe` a parancsot a PowerShell-ablak rendszerkörnyezetben való megnyitásához.
2. Döntse el, melyik helyről szeretné helyreállítani az adatbázist:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Az adatbázis másolása a legutóbbi biztonsági mentésből

1. Lépjen a replika VHD elérési útjára `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Csatlakoztassa a **benne lévő disk0.vhdx** meghajtót a `mount-vhd disk0.vhdx` paranccsal.
3. A replika VHD csatolása után a használatával rendeljen hozzá egy meghajtóbetűjelet a replikakötethez az SQL-szkript kimenetének fizikai `mountvol.exe` replikaazonosítójával. Például: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Az adatbázis másolása egy korábbi helyreállítási pontból

1. Keresse meg a DPMDB  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` tárolókönyvtárat. Több, egyedi GUID azonosítóval rendelkező könyvtárat fog látni a MABS-adatbázishoz tartozó helyreállítási pontok alatt. Más könyvtárak pit/helyreállítási pontot képviselnek.
2. Navigáljon bármely PIT VHD-útvonalra, például, és csatlakoztassa a benne található `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` **disk0.vhdx** adatokat a `mount-vhd disk0.vhdx` paranccsal.
3. A replika VHD csatolása után a használatával rendeljen hozzá egy meghajtóbetűjelet a replikakötethez az SQL-szkript kimenetének fizikai `mountvol.exe` replikaazonosítójával. Például: `mountvol X: \?\Volume{}\`

   A fenti lépésekben angular kapcsos zárójelekkel megjelenő összes kifejezés helytulajdonos. Cserélje le őket a megfelelő értékekre az alábbiak szerint:
   - **ReFSVolume** – Elérési út az SQL-szkript kimenetében
   - **MABSServer FQDN** – A MABS-kiszolgáló teljes neve
   - **PhysicalReplicaId** – A fizikai replika azonosítója az SQL-szkriptből kifelé
   - **PITId** – A tárolókönyvtárban található fizikai replikaazonosítón kívül más GUID-azonosító.
4. Nyisson meg egy másik rendszergazdai parancssort, és futtassa a parancsot a parancssor `psexec.exe -s cmd.exe` rendszerkörnyezetben való futtatásához.
5. Módosítsa a könyvtárat az X: meghajtóra, és keresse meg a MABS-adatbázisfájlok helyét.
6. Másolja őket egy olyan helyre, ahonnan könnyen visszaállíthatja őket. A másolás után lépjen ki a psexec cmd ablakból.
7. Lépjen az 1. lépésben megnyitott psexec PowerShell-ablakra, navigáljon a VHDX elérési útjára, és a paranccsal leválasztsa a VHDX-et. `dismount-vhd disk0.vhdx`
8. A MABS-kiszolgáló újratelepítése után a visszaállított DPMDB használatával csatolhatja a MABS-kiszolgálót a parancs `DPMSYNC-RESTOREDB` futtatásával.
9. A `DPMSYNC-SYNC` futtatás `DPMSYNC-RESTOREDB` egyszer befejeződött.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Az adatbázis biztonsági mentése a MABS-tárolókészlet biztonsági mentése által

> [!NOTE]
> Ez a lehetőség az örökölt tárterületet tároló MABS-hez alkalmazható.

Mielőtt elkezdené, futtatnia kell egy szkriptet a MABS replikakötet csatlakoztatási pontjának elérési útjának lekéréséhez, hogy tudja, melyik helyreállítási pont tartalmazza a MABS biztonsági mentést. Ezt az Azure Backuppal való első replikációt követően tegye meg. A szkriptben cserélje le a helyére a `dplsqlservername%` MABS-adatbázist SQL Server példány nevét.

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

1. A MABS-konzolon válassza a **Védelem Védelmi** csoport  >  **létrehozása lehetőséget.**

2. A **Védelmi csoport típusának kiválasztása** lapon válassza a **Kiszolgálók** elemet.

3. A **Csoporttagok kiválasztása lapon** válassza ki a MABS-adatbázist. Bontsa ki a MABS-kiszolgálóelemet, és válassza a **DPMDB elemet.**

4. Az **Adatvédelmi módszer kiválasztása lapon** válassza a Rövid távú lemezes **védelmet szeretnék lehetőséget.** Adja meg a rövid távú védelmi házirend beállításait. A MABS-adatbázisok megőrzési tartománya két hét.

#### <a name="recover-the-database"></a>Adatbázis helyreállítása

Ha a MABS-kiszolgáló még működőképes és a tárolókészlet érintetlen (például a MABS szolgáltatással vagy -konzollal kapcsolatos problémák), másolja az adatbázist a replikakötetről vagy egy árnyékmásolatból az alábbiak szerint:

1. Döntse el, hogy mikori állapotát szeretné visszaállítani az adatbázisnak.

    - Ha az adatbázist közvetlenül a MABS-replikakötetről készült legutóbbi biztonsági mentésből szeretné átmásolni, azmountvol.exehasználatával rendeljen meghajtóbetűjelet a replikakötethez az **SQL-szkript** kimenetének GUID azonosítóját használva. Például: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Ha egy korábbi helyreállítási pontról (árnyékmásolatból) szeretné másolni az adatbázist, akkor listába kell sorolni a replika összes árnyékmásolatát az SQL-szkript kimenetének GUID azonosítóját használva. Ez a parancs listázza az adott kötet árnyékmásolatát: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Jegyezze fel a létrehozás idejét és az árnyékmásolat-azonosítót, amelyből helyre szeretné állítani a helyreállítást.

2. Ezután a **diskshadow.exe** az árnyékmásolatot egy nem használt X: meghajtóbetűjelhez csatlakoztathatja az árnyékmásolat-azonosító használatával, hogy az adatbázisfájlokat átmásolni tudja.

3. Nyisson meg egy rendszergazdai parancssort, és futtassa a parancsot a parancssor rendszerkörnyezetben való futtatásához, hogy jogosultsággal navigáljon a replikakötethez `psexec.exe -s cmd.exe` (X:) és másolja a fájlokat.

4. CD-n az X: meghajtóra, és keresse meg a MABS-adatbázisfájlok helyét. Másolja őket egy olyan helyre, ahonnan könnyen visszaállíthatja őket. A másolás befejezése után létezik a psexec  cmd ablak, futtassa adiskshadow.exeés az X: kötetet.

5. Most már visszaállíthatja az adatbázisfájlokat az SQL Management Studio a **DPMSYNC \- RESTOREDB futtatásával.**

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Natív SQL Server biztonsági mentés helyi lemezre

A MABS-adatbázisról a MABS-től független natív biztonsági SQL Server helyi lemezre is biztonsági másolatot készíthet.

- Az SQL Server biztonsági mentés [áttekintése](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases).

- [További információk](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) az SQL Servernek a felhőbe történő biztonsági mentéséről.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Biztonságimentás MABS által védett megosztásra

Ez a biztonsági mentési lehetőség natív SQL használatával készít biztonsági másolatot a MABS-adatbázisról egy megosztásra, megvédi a megosztást a MABS-sel, és a Windows VSS korábbi verzióival könnyíti meg a visszaállítást.

### <a name="before-you-start"></a>Előkészületek

1. A SQL Server egy mappát egy olyan meghajtón, amely elegendő szabad területet biztosít a biztonsági másolat egyetlen másolatának a mentéséhez. Példa: `C:\MABSBACKUP`.

1. Ossza meg a mappát. Például ossza meg a `C:\MABSBACKUP` mappát *DPMBACKUP-ként.*

1. Másolja és illessze be az alábbi OSQL-parancsot a Jegyzettömbbe, és mentse egy nevű `C:\MABSACKUP\bkupdb.cmd` fájlba. Győződjön meg arról, hogy nincs .txt kiterjesztés. Módosítsa a SQL_Instance_name és DPMDB_NAME a MABS-kiszolgáló által használt példánynak és DPMDB-névnek megfelelően.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. A Jegyzettömb használatával nyissa **ScriptingConfig.xml** a `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` MABS-kiszolgáló mappájában található fájlfájlt.

1. Módosítsa **ScriptingConfig.xml** és módosítsa a **DataSourceName=** paramétert a DPMDBBACKUP mappát/megosztást tartalmazó meghajtóbetűjelre. Módosítsa a PreBackupScript bejegyzést a 3. lépésben mentett **bkupdb.cmd** teljes elérési útjára és nevére.

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

1. Mentse a módosításokat a **ScriptingConfig.xml.**

1. Védje a C:\MABSBACKUP mappát vagy a megosztást a MABS használatával, és várja meg a `\sqlservername\MABSBACKUP` kezdeti replika létrejöttét. A C:\MABSBACKUP mappában lennie kell egy **dpmdb.bak** fájlnak a futó biztonsági mentés előtti parancsfájl eredményeként, amely a MABS-replikára lett átmásolva.

1. Ha nem engedélyezi az önkiszolgáló helyreállítást, további lépésekre lesz szüksége a MABSBACKUP mappa megosztásához a replikán:

    1. A MABS-konzolon válassza > **a MABSBACKUP** adatforrást, és jelölje ki. A részletek szakaszban  válassza a Kattintson ide a replika elérési útjára mutató hivatkozás részleteinek megtekintéséhez lehetőséget, és másolja az elérési utat a Jegyzettömbbe. Távolítsa el a forrás elérési útját, de őrizze meg a célét. Az elérési útnak az alábbihoz hasonlóan kell kinéznie: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Ossza meg az elérési utat a **MABSSERVERNAME-DPMDB megosztásnévvel.** Az alábbi Net Share parancsot egy rendszergazdai parancssorból használhatja.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>A biztonsági mentés konfigurálása

A MABS-adatbázisról úgy készít biztonsági másolatot, mint bármely más SQL Server a natív SQL Server használatával.

- Az SQL Server biztonsági mentés [áttekintése](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases).

- [További információk](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) az SQL Servernek a felhőbe történő biztonsági mentéséről.

### <a name="recover-the-mabs-database"></a>A MABS-adatbázis helyreállítása

1. Csatlakozzon `\\MABSServer\MABSSERVERNAME-dpmdb` a megosztáshoz az Intézővel bármely Windows rendszerű számítógépről.

2. A tulajdonságok megtekintéséhez kattintson a jobb gombbal a **dpmdb.bak** fájlra. A **Korábbi verziók** lapon fel van sorolva az összes kiválasztható és másolható biztonsági mentés. A legutolsó biztonsági mentés is a C:\MABSBACKUP mappában található, amely szintén könnyen elérhető.

3. Ha egy SAN-hez csatlakoztatott MABS-tárolókészlet-lemezt kell áthelyeznie egy másik kiszolgálóra, hogy be tudja olvasni a replikakötetet, vagy újra kell telepítenie a Windowst a helyileg csatlakoztatott lemezek olvasása érdekében, előbb meg kell tudni a MABS replikakötet csatlakoztatási pontjának elérési útját vagy a kötet GUID azonosítóját, hogy tudja, melyik köteten található az adatbázis biztonsági másolata. Az alábbi SQL-parancsfájllal a kezdeti védelem aktiválása után és a visszaállítás szükségessé válása előtt bármikor kinyerheti a szükséges információkat. Cserélje le `%dpmsqlservername%` a helyére az adatbázist SQL Server kiszolgáló nevét.

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

4. Ha helyre kell állítania a MABS tárolókészlet-lemezek áthelyezését vagy a MABS-kiszolgáló újraépítését:

    1. Rendelkezik a kötet GUID azonosítóval, ezért ha a kötetet egy másik Windows-kiszolgálóhoz kell csatlakoztatni, vagy egy MABS-kiszolgáló újraépítése után, amountvol.exehasználatával rendeljen hozzá egy meghajtóbetűjelet a kötet GUID azonosítójának használatával az **SQL-szkript** kimenetében: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. A replikaköteten lévő MABSBACKUP mappa újramegosztása a meghajtóbetűjel és a replikaútvonal mappastruktúrát jelölő része használatával.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Csatlakozzon `\\SERVERNAME\MABSSERVERNAME-dpmdb` a megosztáshoz az Explorerrel bármely Windows rendszerű számítógépről.

    4. A tulajdonságok megtekintéséhez kattintson a jobb gombbal a **dpmdb.bak** fájlra. A **Korábbi verziók** lapon fel van sorolva az összes kiválasztható és másolható biztonsági mentés.

## <a name="using-dpmsync"></a>A DPMSync használata

**A DpmSync** egy parancssori eszköz, amely lehetővé teszi a MABS-adatbázis szinkronizálását a tárolókészletben lévő lemezek állapotával és a telepített védelmi ügynökökkel. A DpmSync visszaállítja a MABS-adatbázist, szinkronizálja a MABS-adatbázist a tárolókészlet replikáival, visszaállítja a jelentés-adatbázist, és újra kiosztja a hiányzó replikákat.

### <a name="parameters"></a>Paraméterek

| Paraméter      | Leírás    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Visszaállít egy MABS-adatbázist egy adott helyről.|
| **-Sync**                            | Szinkronizálja a visszaállított adatbázisokat. Az adatbázisok visszaállítása után futtatnia kell a **DpmSync –Syncet.** A **DpmSync –Sync** futtatása után egyes replikák továbbra is hiányzóként lehetnek megjelölve. |
| **-DbLoc** *helye*                | A MABS-adatbázis biztonsági másolatának helyét azonosítja.|
| **-InstanceName** <br/>*kiszolgáló\példány*     | Az a példány, amelyre a DPMDB-t vissza kell állítani.|
| **-ReallocateReplica**         | Szinkronizálás nélkül újra kiosztja az összes hiányzó replikakötetet. |
| **-DataCopied**                      | Azt jelzi, hogy befejezte az adatok betöltését az újonnan lefoglalt replikakötetre. Ez csak az ügyfélszámítógépek esetében érvényes. |

**1. példa:** A MABS-adatbázis a MABS-kiszolgálón helyi biztonsági mentési adathordozóról való visszaállításához futtassa a következő parancsot:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

A MABS-adatbázis visszaállítása után az adatbázisok szinkronizálásához futtassa a következő parancsot:

```cmd
DpmSync -Sync
```

A MABS-adatbázis visszaállítása és szinkronizálása után, illetve a replika visszaállítása előtt futtassa a következő parancsot a replika lemezterületének újracsoportosításához:

```cmd
DpmSync -ReallocateReplica
```

**2. példa:** A MABS-adatbázis távoli adatbázisból való visszaállításához futtassa a következő parancsot a távoli számítógépen:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

A MABS-adatbázis visszaállítása után az adatbázisok szinkronizálásához futtassa a következő parancsot a MABS-kiszolgálón:

```cmd
DpmSync -Sync
```

A MABS-adatbázis visszaállítása és szinkronizálása után, illetve a replika visszaállítása előtt futtassa a következő parancsot a MABS-kiszolgálón a replika lemezterületének újrahelyéhez:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Következő lépések

- [MABS támogatási mátrix](backup-support-matrix-mabs-dpm.md)
- [MABS – gyakori kérdések](backup-azure-dpm-azure-server-faq.yml)