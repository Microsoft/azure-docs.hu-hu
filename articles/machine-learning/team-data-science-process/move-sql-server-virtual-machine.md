---
title: Az adatáthelyezés SQL Server virtuális gépre – csoportos adatelemzési folyamat
description: Az adatok áthelyezhetők a sima fájlokból vagy a helyszíni SQL Serverból az Azure-beli virtuális gépen SQL Server.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c80a90b07e25942e751d52cafa47f6e3e94852ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320329"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Adatok áthelyezés SQL Server-kiszolgálóra Azure-beli virtuális gépeken

Ez a cikk az adatok sima fájlokból (CSV vagy TSV formátumokból) vagy egy helyszíni SQL Serverból az Azure-beli virtuális gépeken való SQL Server való áthelyezésének lehetőségeit ismerteti. Az adatok felhőbe való áthelyezésével kapcsolatos feladatok a csoportos adatelemzési folyamat részét képezik.

Egy olyan témakörben, amely az adatáthelyezési beállításokat ismerteti Machine Learning Azure SQL Databasere, tekintse meg az [adatáthelyezés egy Azure SQL Databasere Azure Machine learning](move-sql-azure.md)számára című témakört.

Az alábbi táblázat összefoglalja az Azure-beli virtuális gépeken SQL Server az adatáthelyezési lehetőségeit.

| <b>FORRÁS</b> | <b>CÉL: SQL Server Azure-beli virtuális gépen</b> |
| --- | --- |
| <b>Sima fájl</b> |1. <a href="#insert-tables-bcp">parancssori tömeges másolási segédprogram (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">tömeges beszúrási SQL-lekérdezés </a><br> 3. <a href="#sql-builtin-utilities">a SQL Server grafikus beépített segédprogramjai</a> |
| <b>Helyszíni SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">SQL Server-adatbázis üzembe helyezése egy Microsoft Azure VM-varázslóban</a><br> 2. <a href="#export-flat-file">Exportálás egy egyszerű fájlba </a><br> 3. <a href="#sql-migration">SQL Database áttelepítési varázsló </a> <br> 4. <a href="#sql-backup">az adatbázis biztonsági mentése és visszaállítása </a><br> |

Ez a dokumentum azt feltételezi, hogy az SQL-parancsok végrehajtása SQL Server Management Studio vagy Visual Studio Adatbázis-böngészőból történik.

> [!TIP]
> Alternatív megoldásként a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) használatával létrehozhat és ütemezhet egy olyan folyamatot, amely az Azure-beli SQL Server VMba helyezi át az adatcsatornákat. További információ: [adatok másolása Azure Data Factorysal (másolási tevékenység)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy rendelkezik a következővel:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure Storage-fiók**. Ebben az oktatóanyagban egy Azure Storage-fiókot fog használni az adattároláshoz. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című cikket. A Storage-fiók létrehozása után be kell szereznie a tárolóhoz való hozzáféréshez használt fiók kulcsát. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md).
* Kiépített **SQL Server egy Azure-beli virtuális gépen**. Útmutatásért lásd: [Azure SQL Server virtuális gép beállítása IPython notebook-kiszolgálóként a speciális elemzésekhez](../data-science-virtual-machine/overview.md).
* **Azure PowerShell** helyileg telepítve és konfigurálva. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Adatok áthelyezése egy egyszerű fájlból a SQL Serverba egy Azure-beli virtuális gépen
Ha az adatai egy sor/oszlop formátumú (sorba rendezett) fájlban vannak, a következő módszerekkel lehet áthelyezni SQL Server VM az Azure-ba:

1. [Parancssori tömeges másolási segédprogram (BCP)](#insert-tables-bcp)
2. [SQL-lekérdezés tömeges beszúrása](#insert-tables-bulkquery)
3. [Grafikus beépített segédprogramok a SQL Serverban (Importálás/exportálás, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Parancssori tömeges másolási segédprogram (BCP)
A BCP egy SQL Server telepített parancssori segédprogram, amely az adatáthelyezés egyik leggyorsabb módja. Mindhárom SQL Server változatban működik (helyszíni SQL Server, SQL Azure és SQL Server VM az Azure-ban).

> [!NOTE]
> **Hol kell az adataim a BCP-hez?**  
> Habár nem szükséges, a forrásként szolgáló fájlok, amelyek a célként SQL Server ugyanazon a gépen találhatók, lehetővé teszik a gyorsabb átvitelt (a hálózati sebesség és a helyi lemez i/o-sebessége). Áthelyezheti az adatok tárolására szolgáló sima fájlokat a gépre, ahol a SQL Server a különböző fájlmásolás-eszközök, például a [AZCopy](../../storage/common/storage-use-azcopy-v10.md), a [Azure Storage Explorer](https://storageexplorer.com/) vagy a Windows másolási/beillesztési RDP protokoll (RDP) használatával telepíthetők.
>
>

1. Győződjön meg arról, hogy az adatbázis és a táblák a cél SQL Server adatbázisban jönnek létre. Íme egy példa arra, hogyan végezheti el a következő műveleteket a `Create Database` és `Create Table` parancsok használatával:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Létrehozhatja a táblázat sémáját leíró formátumú fájlt a következő parancs kiadásával azon a számítógépen, ahol a BCP telepítve van.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Szúrja be az adatait az adatbázisba a BCP paranccsal, amelynek a parancssorból kell működnie, ha SQL Server ugyanarra a gépre van telepítve:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **BCP-beszúrások optimalizálása** A lapkák optimalizálásához tekintse meg a következő cikket ["a tömeges importálás optimalizálásához"](/previous-versions/sql/sql-server-2008-r2/ms177445(v=sql.105)) című cikkben.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Tetszés lapkák a gyorsabb adatáthelyezéshez
Ha az áthelyezett adatmennyiség nagy, akkor felgyorsíthatja a dolgokat, ha párhuzamosan hajt végre több BCP-parancsot egy PowerShell-parancsfájlban.

> [!NOTE]
> **Big adatfeldolgozás** A nagyméretű és nagy méretű adatkészletek betöltésének optimalizálása érdekében particionálja a logikai és fizikai adatbázis-táblákat több fájlcsoportok és partíciós tábla használatával. További információ a partíciós táblázatok létrehozásáról és az adatok betöltéséről: [párhuzamos terhelésű SQL-partíciós táblák](parallel-load-sql-partitioned-tables.md).
>
>

A következő PowerShell-parancsfájl bemutatja a párhuzamos lapkákat a BCP használatával:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>SQL-lekérdezés tömeges beszúrása
A [tömeges beszúrási SQL-lekérdezéssel](/sql/t-sql/statements/bulk-insert-transact-sql) adatok importálhatók az adatbázisba a sor/oszlop alapú fájlok alapján (a támogatott típusok az[adatok előkészítése tömeges exportálásra vagy importálásra (SQL Server)](/sql/relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server)) című témakörben találhatók.

Íme néhány példa a tömeges beszúrási parancsokra:  

1. Elemezze az adatait, és az importálás előtt állítson be minden egyéni beállítást, és győződjön meg arról, hogy a SQL Server adatbázisa ugyanazt a formátumot feltételezi, mint a dátumok. Íme egy példa arra, hogyan állítható be a dátumformátum a hónap napjaként (ha az adatok az év hónapjának napjának dátumát tartalmazzák):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Adatokat importálhat tömeges importálási utasítások használatával:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Beépített segédprogramok a SQL Server
A SQL Server Integration Services (SSIS) használatával adatok importálhatók az Azure-beli SQL Server VMba egy sima fájlból.
A SSIS két Studio-környezetben érhető el. Részletekért lásd: [Integration Services (SSIS) és Studio-környezetek](/sql/integration-services/integration-services-ssis-development-and-management-tools):

* A SQL Server Data Tools részleteiért lásd: [Microsoft SQL Server Adateszközök](/sql/ssdt/download-sql-server-data-tools-ssdt)  
* Az importálási/exportálási varázsló részleteiért lásd: [SQL Server Importálás és exportálás varázsló](/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard) .

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Adatok áthelyezése helyszíni SQL Serverból egy Azure-beli virtuális gépen SQL Server
A következő áttelepítési stratégiákat is használhatja:

1. [SQL Server-adatbázis üzembe helyezése Microsoft Azure virtuális gép varázslóban](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportálás az egyszerű fájlba](#export-flat-file)
3. [SQL Database áttelepítési varázsló](#sql-migration)
4. [Adatbázis biztonsági mentése és visszaállítása](#sql-backup)

Az alábbi lehetőségeket ismertetjük:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>SQL Server-adatbázis üzembe helyezése Microsoft Azure virtuális gép varázslóban
Az **SQL Server-adatbázis központi telepítése Microsoft Azure virtuális gépre varázsló** egyszerű és ajánlott módszer az adatok áthelyezésére egy helyszíni SQL Server-példányról egy Azure-beli virtuális gépen SQL Server. A részletes lépések, valamint a további alternatívák megvitatására lásd: [adatbázisok Áttelepítésének SQL Server Azure-beli virtuális gépen](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportálás az egyszerű fájlba
Számos módszer használható a helyszíni SQL Server adatainak tömeges exportálására az [adatok tömeges importálása és exportálása (SQL Server)](/sql/relational-databases/import-export/bulk-import-and-export-of-data-sql-server) című témakörben leírtak szerint. Ez a dokumentum a tömeges másolási programot (BCP) fedi le példaként. Ha az adatexportálást egy egyszerű fájlba exportálja, az importálható egy másik SQL Server-kiszolgálóra tömeges importálás használatával.

1. Exportálja a helyszíni SQL Server adatait egy fájlba a BCP segédprogrammal a következőképpen:

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Hozza létre az adatbázist és a táblázatot a SQL Server VM az Azure- `create database` ban a és az `create table` 1. lépésben exportált tábla sémájának használatával.
3. Hozzon létre egy formázó fájlt, amely leírja az exportált/importált adatmennyiség táblázatos sémáját. A formátumú fájl részletes ismertetését lásd: [Format fájl létrehozása (SQL Server)](/sql/relational-databases/import-export/create-a-format-file-sql-server).

    Fájl létrehozásának formázása a BCP SQL Server számítógépről való futtatásakor

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

    Fájl létrehozásának formázása, ha a BCP-t távolról futtatja egy SQL Server

    `bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n`
4. Az [adatok beolvasása a fájl forrása](#filesource_to_sqlonazurevm) című részben leírt módszerek bármelyikével áthelyezheti az adatok egy SQL Serverba.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>SQL Database áttelepítési varázsló
[SQL Server adatbázis-áttelepítési varázsló](https://sqlazuremw.codeplex.com/) felhasználóbarát módon helyezi át az adatátvitelt két SQL Server-példány között. Lehetővé teszi a felhasználó számára az Adatséma leképezését a források és a célhelyek között, és kiválaszthatja az oszlopok típusát és a különféle funkciókat. Tömeges másolást (BCP) használ a borítók alatt. Az SQL Database áttelepítési varázsló üdvözlőképernyő képernyőjén az alábbi képernyőkép látható.  

![SQL Server áttelepítési varázsló][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Adatbázis biztonsági mentése és visszaállítása
SQL Server a következőket támogatja:

1. Az [adatbázis biztonsági mentése és visszaállítása funkció](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) (mindkettő egy helyi fájlra, vagy a blobba történő exportálás bacpac) és [az adatcsomag-alkalmazásokra](/sql/relational-databases/data-tier-applications/data-tier-applications) (a bacpac használatával).
2. Lehetőség van arra, hogy közvetlenül létrehozzon SQL Server virtuális gépeket az Azure-ban egy másolt adatbázissal, vagy másoljon egy meglévő adatbázisba SQL Database. További információ: [az adatbázis másolása varázsló használata](/sql/relational-databases/databases/use-the-copy-database-wizard).

Alább látható az adatbázis biztonsági mentési/visszaállítási lehetőségeinek képernyőképe SQL Server Management Studio.

![SQL Server importálási eszköz][1]

## <a name="resources"></a>Források
[Adatbázis migrálása SQL Server Azure-beli virtuális gépen](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[Az SQL Server használata az Azure Virtual Machinesben – áttekintés](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png