---
title: Párhuzamos tömeges adatimportálás az SQL Partition Tables szolgáltatásban – csoportos adatelemzési folyamat
description: Particionált táblákat hozhat létre az adatSQL Server-adatbázisok gyors párhuzamos tömeges importálásához.
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
ms.openlocfilehash: 456e881d84697f4542f972ac0798cc95a3455b3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93322416"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Táblázatok készítése és optimalizálása egy Azure-beli virtuális gépen lévő SQL Server gyors párhuzamos importálásához

Ez a cikk azt ismerteti, hogyan hozhat létre particionált táblákat az adatSQL Server-adatbázisba való gyors párhuzamos tömeges importáláshoz. Az SQL Database-be való betöltéshez, illetve az adatoknak az SQL-adatbázisba történő importálásához és a későbbi lekérdezésekhez *particionált táblák és nézetek* használatával lehet javítani a Big Data. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Új adatbázis és fájlcsoportok-készlet létrehozása
* [Hozzon létre egy új adatbázist](/sql/t-sql/statements/create-database-transact-sql), ha az még nem létezik.
* Adja hozzá az adatbázis fájlcsoportok az adatbázishoz, amely tartalmazza a particionált fizikai fájlokat. 
* Ezt az adatbázis [létrehozása](/sql/t-sql/statements/create-database-transact-sql) lehetőséggel végezheti el, ha az új vagy az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options) , ha az adatbázis már létezik.
* Vegyen fel egy vagy több fájlt (szükség szerint) az egyes adatbázis-fájlcsoportja.
  
  > [!NOTE]
  > Adja meg a cél fájlcsoportja, amely tartalmazza a partícióhoz tartozó és a fájlcsoportja-adattároláshoz használt fizikai adatbázis fájlnevét (ke) t.
  > 
  > 

Az alábbi példa egy olyan új adatbázist hoz létre, amely nem az elsődleges és a Fájlcsoportok, hanem minden egyes fizikai fájlt tartalmaz. Az adatbázisfájlok a SQL Server példányban konfigurált alapértelmezett SQL Server Adatmappában jönnek létre. További információ az alapértelmezett fájlok helyeiről: a [SQL Server alapértelmezett és elnevezett példányainak elérési helyei](/sql/sql-server/install/file-locations-for-default-and-named-instances-of-sql-server).

```sql
   DECLARE @data_path nvarchar(256);
   SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

   EXECUTE ('
      CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
```

## <a name="create-a-partitioned-table"></a>Particionált tábla létrehozása
Ha particionált táblát kíván létrehozni az Adatséma alapján, amely az előző lépésben létrehozott adatbázis-fájlcsoportok van leképezve, először létre kell hoznia egy partíciós függvényt és sémát. Ha az adatok tömeges importálása a particionált táblázat (ok) ba történik, a rekordok elosztása a Fájlcsoportok szerint történik, az alább leírtak szerint.

### <a name="1-create-a-partition-function"></a>1. partition függvény létrehozása
[Partíciós függvény létrehozása](/sql/t-sql/statements/create-partition-function-transact-sql) Ez a függvény határozza meg az egyes partíciós táblákban szerepeltethető értékek/határok tartományát, például a partíciók hónapok szerinti korlátozásához (néhány \_ datetime \_ mező) a 2013-as évben:
  
```sql
   CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
      AS RANGE RIGHT FOR VALUES (
         '20130201', '20130301', '20130401',
         '20130501', '20130601', '20130701', '20130801',
         '20130901', '20131001', '20131101', '20131201' )
```

### <a name="2-create-a-partition-scheme"></a>2. partíciós séma létrehozása
[Hozzon létre egy particionálási sémát](/sql/t-sql/statements/create-partition-scheme-transact-sql). Ez a séma leképezi a partíciós függvényben lévő egyes partíciós tartományokat egy fizikai fájlcsoportja, például:
  
```sql
      CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
```
 
A következő lekérdezés futtatásával ellenőrizheti, hogy az egyes partíciók milyen tartományokat érintenek a függvény/séma szerint:
  
```sql
   SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
   FROM sys.partition_functions AS pfun
   INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
   INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
   WHERE pfun.name = <DatetimeFieldPFN>
```

### <a name="3-create-a-partition-table"></a>3. partíciós tábla létrehozása
[Hozzon létre particionált táblát](/sql/t-sql/statements/create-table-transact-sql)az Adatséma szerint, és adja meg a táblázat particionálásához használt partíciós sémát és korlátozás mezőt, például:
  
```sql
   CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)
```

További információ: [particionált táblák és indexek létrehozása](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Az egyes partíciós táblákhoz tartozó adatmennyiség tömeges importálása

* A BCP, BULK INSERT vagy más módszerekkel, például [SQL Server áttelepítési varázslóval](https://sqlazuremw.codeplex.com/)is használható. A megadott példa a BCP metódust használja.
* Módosítsa [az adatbázist](/sql/t-sql/statements/alter-database-transact-sql-set-options) úgy, hogy a naplózási séma méretének csökkentése érdekében BULK_LOGGED a tranzakció naplózási sémáját, például:
  
   ```sql
      ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
   ```
* Az adatok betöltésének meggyorsításához párhuzamosan indítsa el a tömeges importálási műveleteket. A big data SQL Server adatbázisba való tömeges importálásának továbbítására vonatkozó tippekért lásd: [1 TB-os terhelés kevesebb mint 1 óra](/archive/blogs/sqlcat/load-1tb-in-less-than-1-hour).

A következő PowerShell-szkript a BCP használatával történő párhuzamos betöltést szemlélteti.

```powershell
    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date
```

## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Indexek létrehozása az illesztések és a lekérdezési teljesítmény optimalizálása érdekében
* Ha több táblázatból is kinyeri az adatok modellezését, hozzon létre indexeket az illesztési kulcsokban az illesztési teljesítmény javítása érdekében.
* [Hozzon létre](/sql/t-sql/statements/create-index-transact-sql) az egyes partíciók azonos fájlcsoportja célzó indexeket (fürtözött vagy nem fürtözött), például:
  
```sql
   CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
--  or,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
 ```
 
  > [!NOTE]
  > Dönthet úgy, hogy az indexeket a tömeges importálás előtt hozza létre. Az index létrehozása a tömeges importálás előtt lelassítja az betöltést.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Speciális elemzési folyamat és technológia működés közben – példa
Egy teljes körű bemutató példa a csoportos adatelemzési folyamat nyilvános adatkészlettel való használatával kapcsolatban [: csoportos adatelemzési folyamat működés közben: SQL Server használata](sql-walkthrough.md).