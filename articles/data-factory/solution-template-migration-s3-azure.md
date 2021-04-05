---
title: Adatok migrálása az Amazon S3-ból Azure Data Lake Storage Gen2ba
description: Megtudhatja, hogyan használhatja a megoldás sablonnal az Amazon S3-ból származó adatok áttelepítését egy külső vezérlési táblázat használatával, amely az AWS S3 és a Azure Data Factory segítségével tárolja a partíciós listákat.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: c1fd4cb248abdc219c6ee5d098e10c329826c160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361987"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Adatok migrálása az Amazon S3-ból Azure Data Lake Storage Gen2ba

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A sablonok segítségével áttelepítheti az Amazon S3-ból Azure Data Lake Storage Gen2 több száz millió fájlból álló petabájt. 

 > [!NOTE]
 > Ha a kis adatmennyiséget az AWS S3-ból az Azure-ba kívánja másolni (például 10 TB-nál kevesebb), akkor a [Azure Data Factory adatok másolása eszköz](copy-data-tool.md)hatékonyabb és könnyen használható. A cikkben leírt sablon több, mint amire szüksége van.

## <a name="about-the-solution-templates"></a>Tudnivalók a megoldási sablonokról

Az adatpartíció különösen akkor ajánlott, ha 10 TB-nál több adat áttelepítését végzi. Az adatok particionálásához használja az "előtag" beállítást a mappák és fájlok szűréséhez az Amazon S3-ban név szerint, majd minden egyes ADF-másolási feladatsor egyszerre egy partíciót tud másolni. Több ADF-másolási feladatot is futtathat párhuzamosan a jobb teljesítmény érdekében.

Az adatáttelepítés általában egy egyszeri, korábbi adatok áttelepítését igényli, és rendszeres időközönként szinkronizálja az AWS S3 és az Azure közötti változásokat. Az alábbi két sablon létezik, ahol az egyik sablon az egyszeri korábbi adatáttelepítést fedi le, és egy másik sablon az AWS S3 és az Azure közötti változások szinkronizálását is magában foglalja.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Ahhoz, hogy a sablon áttelepítse az Amazon S3 korábbi adatait Azure Data Lake Storage Gen2

Ez a sablon (*sablon neve: a korábbi adatok áttelepítése az AWS S3-ból a Azure Data Lake Storage Gen2ba*) feltételezi, hogy a Azure SQL Database egy külső vezérlő táblájába írt egy partíciós listát. Így egy *keresési* tevékenység fogja lekérni a partíciós listát a külső vezérlő táblából, megismételni az egyes partíciókat, és minden egyes ADF-másolási feladatot egyszerre másol egy partícióra. A másolási feladatok befejezése után a *tárolt eljárási* tevékenységgel frissíti az egyes partíciók másolásának állapotát a vezérlési táblában.

A sablon öt tevékenységet tartalmaz:
- A **Keresés** lekéri azokat a partíciókat, amelyek nem lettek átmásolva egy külső vezérlő táblából Azure Data Lake Storage Gen2ba. A tábla neve *s3_partition_control_table* , és az adatok a táblából való betöltésére szolgáló lekérdezés: *"SELECT PARTITIONPREFIX from s3_partition_control_table where SuccessOrFailure = 0"*.
- A **foreach** beolvassa a *keresési* tevékenységből a partíciók listáját, és megismétli az egyes partíciókat a *TriggerCopy* tevékenységhez. A *batchCount* beállíthatja úgy, hogy egyidejűleg több ADF-másolási feladatot futtasson. Ebben a sablonban 2 van beállítva.
- A **ExecutePipeline** végrehajtja a *CopyFolderPartitionFromS3* folyamatát. Ennek az az oka, hogy az egyes másolási feladatok elvégzéséhez egy másik folyamatot hozunk létre, mert így egyszerűen újrafuttathatja a sikertelen másolási feladatot, hogy az adott partíciót újra újra lehessen tölteni az AWS S3-ból. Az egyéb partíciókat betöltő többi másolási feladatot nem érinti a rendszer.
- **Másolja** az egyes partíciókat az AWS S3-ról a Azure Data Lake Storage Gen2ra.
- A **SqlServerStoredProcedure** frissíti az egyes partíciók másolásának állapotát a vezérlési táblában.

A sablon két paramétert tartalmaz:
- **AWS_S3_bucketName** a gyűjtő neve az AWS S3-ban, ahová át kívánja telepíteni az adatait. Ha az AWS S3-on több gyűjtőből kívánja áttelepíteni az adatait, a külső vezérlő táblában még egy oszlopot is hozzáadhat, hogy tárolja a gyűjtő nevét az egyes partíciók számára, valamint a folyamat frissítésével az adott oszlopból származó adatok lekérdezését.
- **Azure_Storage_fileSystem** a fájlrendszer neve azon a Azure Data Lake Storage Gen2n, amelyen át kívánja telepíteni az adatait.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Ahhoz, hogy a sablon csak az Amazon S3-ból Azure Data Lake Storage Gen2ba másolja a módosított fájlokat

Ez a sablon (*sablon neve: az AWS S3 és a Azure Data Lake Storage Gen2 közötti különbözeti adatok másolása*) az egyes fájlok LastModifiedTime használatával másolja az új vagy frissített fájlokat csak az AWS S3-ből az Azure-ba. Vegye figyelembe, hogy ha a fájlok vagy mappák már időben particionálva vannak a timeslice-adatokkal az AWS S3 fájl-vagy mappanév (például/yyyy/MM/DD/file.csv) részeként, akkor a jelen [oktatóanyagban](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) az új fájlok növekményes betöltésének nagyobb teljesítményű megközelítését érheti el. Ez a sablon feltételezi, hogy egy Azure SQL Database külső vezérlő táblájában írt egy partíciós listát. Így egy *keresési* tevékenység fogja lekérni a partíciós listát a külső vezérlő táblából, megismételni az egyes partíciókat, és minden egyes ADF-másolási feladatot egyszerre másol egy partícióra. Amikor az egyes másolási feladatok megkezdik a fájlok másolását az AWS S3-ból, a LastModifiedTime tulajdonságra támaszkodik az új vagy frissített fájlok azonosítására és másolására. A másolási feladatok befejezése után a *tárolt eljárási* tevékenységgel frissíti az egyes partíciók másolásának állapotát a vezérlési táblában.

A sablon hét tevékenységet tartalmaz:
- A **lookup** egy külső vezérlő táblából kéri le a partíciókat. A tábla neve *s3_partition_delta_control_table* , és a tábla adatainak betöltésére szolgáló lekérdezés a *"DISTINCT PartitionPrefix kiválasztása a* következőből: s3_partition_delta_control_table".
- A **foreach** beolvassa a *keresési* tevékenységből a partíciók listáját, és megismétli az egyes partíciókat a *TriggerDeltaCopy* tevékenységhez. A *batchCount* beállíthatja úgy, hogy egyidejűleg több ADF-másolási feladatot futtasson. Ebben a sablonban 2 van beállítva.
- A **ExecutePipeline** végrehajtja a *DeltaCopyFolderPartitionFromS3* folyamatát. Ennek az az oka, hogy az egyes másolási feladatok elvégzéséhez egy másik folyamatot hozunk létre, mert így egyszerűen újrafuttathatja a sikertelen másolási feladatot, hogy az adott partíciót újra újra lehessen tölteni az AWS S3-ból. Az egyéb partíciókat betöltő többi másolási feladatot nem érinti a rendszer.
- A **lookup** lekéri a legutóbbi másolási feladatot a külső vezérlő táblából, hogy az új vagy frissített fájlok azonosíthatók legyenek a LastModifiedTime-on keresztül. A tábla neve *s3_partition_delta_control_table* , és az adatok a táblából való betöltésére szolgáló lekérdezés *"Select Max (JobRunTime) as LastModifiedTime from s3_partition_delta_control_table where PartitionPrefix = ' @ {pipeline (). Parameters. prefixStr}" és SuccessOrFailure = 1 "*.
- A **Másolás** új vagy módosított fájlokat másol az AWS S3-ból Azure Data Lake Storage Gen2. A *modifiedDatetimeStart* tulajdonsága az utolsó másolási feladatokra van állítva. A *modifiedDatetimeEnd* tulajdonsága az aktuális másolási feladatokra van állítva. Vegye figyelembe, hogy az idő az UTC időzónára vonatkozik.
- A **SqlServerStoredProcedure** frissíti az egyes partíciók másolásának állapotát, és a vezérlési időt a vezérlési táblában másolja, ha az sikeres. A SuccessOrFailure oszlopa 1 értékre van állítva.
- A **SqlServerStoredProcedure** frissíti az egyes partíciók másolásának állapotát, és lemásolja a futási időt a vezérlési táblába, ha az sikertelen. A SuccessOrFailure oszlopa 0 értékre van állítva.

A sablon két paramétert tartalmaz:
- **AWS_S3_bucketName** a gyűjtő neve az AWS S3-ban, ahová át kívánja telepíteni az adatait. Ha az AWS S3-on több gyűjtőből kívánja áttelepíteni az adatait, a külső vezérlő táblában még egy oszlopot is hozzáadhat, hogy tárolja a gyűjtő nevét az egyes partíciók számára, valamint a folyamat frissítésével az adott oszlopból származó adatok lekérdezését.
- **Azure_Storage_fileSystem** a fájlrendszer neve azon a Azure Data Lake Storage Gen2n, amelyen át kívánja telepíteni az adatait.

## <a name="how-to-use-these-two-solution-templates"></a>A két megoldási sablon használata

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Ahhoz, hogy a sablon áttelepítse az Amazon S3 korábbi adatait Azure Data Lake Storage Gen2 

1. Hozzon létre egy vezérlőelem-táblázatot a Azure SQL Databaseban az AWS S3 partíciós listájának tárolásához. 

    > [!NOTE]
    > A tábla neve s3_partition_control_table.
    > A vezérlő tábla sémája PartitionPrefix és SuccessOrFailure, ahol a PartitionPrefix az S3 előtag-beállítás, amely a mappák és a fájlok név szerinti szűrésére szolgál az Amazon S3-ban, a SuccessOrFailure pedig az egyes partíciók másolásának állapota: 0 azt jelenti, hogy ez a partíció nem lett átmásolva az Azure-ba, 1 pedig azt jelenti, hogy ez a partíció az Azure-ba való
    > A vezérlő táblában 5 partíció van definiálva, és az egyes partíciók másolásának alapértelmezett állapota 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Hozzon létre egy tárolt eljárást ugyanarra a Azure SQL Database a vezérlő táblához. 

    > [!NOTE]
    > A tárolt eljárás neve sp_update_partition_success. Az ADF-folyamat SqlServerStoredProcedure tevékenysége fogja meghívni.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Ugrás a **korábbi adatok migrálása az AWS S3-ból Azure Data Lake Storage Gen2** sablonba. Adja meg a külső vezérlő táblával létesített kapcsolatokat, az AWS S3-t, mint az adatforrás-tárolót, és Azure Data Lake Storage Gen2 a célként megadott tárolóként. Vegye figyelembe, hogy a külső vezérlő tábla és a tárolt eljárás ugyanarra a kapcsolódásra hivatkozik.

    ![Képernyőfelvétel: az AWS S3 korábbi adatainak átmigrálása Azure Data Lake Storage Gen2 sablonba.](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Válassza **a sablon használata** lehetőséget.

    ![Képernyőfelvétel: a sablon használata gomb.](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Ekkor megjelenik a két folyamat és 3 adatkészlet, ahogy az a következő példában is látható:

    ![A sablon használatával létrehozott két folyamatot és három adatkészletet bemutató képernyőkép.](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Nyissa meg a "BulkCopyFromS3" folyamatot, és válassza a **hibakeresés** lehetőséget, és adja meg a **paramétereket**. Ezután válassza a **Befejezés** gombot.

    ![Képernyőkép, amely bemutatja, hol válassza ki a hibakeresést, és adja meg a paramétereket a Befejezés gombra kattintva.](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. A következő példához hasonló eredmények jelennek meg:

    ![Képernyőkép, amely a visszaadott eredményeket jeleníti meg.](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Ahhoz, hogy a sablon csak az Amazon S3-ból Azure Data Lake Storage Gen2ba másolja a módosított fájlokat

1. Hozzon létre egy vezérlőelem-táblázatot a Azure SQL Databaseban az AWS S3 partíciós listájának tárolásához. 

    > [!NOTE]
    > A tábla neve s3_partition_delta_control_table.
    > A vezérlő tábla sémája a következő: PartitionPrefix, JobRunTime és SuccessOrFailure, ahol a PartitionPrefix az S3 előtag-beállítás, amely a mappák és a fájlok név szerinti szűrésére szolgál az Amazon S3-ban, a JobRunTime a feladatok futtatásakor a DateTime érték, a SuccessOrFailure pedig az egyes partíciók másolásának állapotát jelenti
    > A vezérlő táblában 5 partíció van definiálva. A JobRunTime alapértelmezett értéke lehet az az idő, amikor az egyszeri korábbi adatáttelepítés elindul. Az ADF másolási tevékenysége átmásolja a fájlokat az AWS S3-ra, amelyet a rendszer a legutóbbi módosítás után módosított. Az egyes partíciók másolásának alapértelmezett állapota 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Hozzon létre egy tárolt eljárást ugyanarra a Azure SQL Database a vezérlő táblához. 

    > [!NOTE]
    > A tárolt eljárás neve sp_insert_partition_JobRunTime_success. Az ADF-folyamat SqlServerStoredProcedure tevékenysége fogja meghívni.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Ugrás a **különbözeti adatok másolása az AWS S3-ból Azure Data Lake Storage Gen2** sablonra. Adja meg a külső vezérlő táblával létesített kapcsolatokat, az AWS S3-t, mint az adatforrás-tárolót, és Azure Data Lake Storage Gen2 a célként megadott tárolóként. Vegye figyelembe, hogy a külső vezérlő tábla és a tárolt eljárás ugyanarra a kapcsolódásra hivatkozik.

    ![Új kapcsolat létrehozása](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Válassza **a sablon használata** lehetőséget.

    ![Sablon használata](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Ekkor megjelenik a két folyamat és 3 adatkészlet, ahogy az a következő példában is látható:

    ![A folyamat áttekintése](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6.  Nyissa meg a "DeltaCopyFromS3" folyamatot, és válassza a **hibakeresés** lehetőséget, majd adja meg a **paramétereket**. Ezután válassza a **Befejezés** gombot.

    ![Kattintson * * hibakeresés * *](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. A következő példához hasonló eredmények jelennek meg:

    ![Az eredmény áttekintése](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. A vezérlő tábla eredményeit a *"select * from s3_partition_delta_control_table"* lekérdezéssel is ellenőrizheti, így az alábbi példához hasonló kimenet jelenik meg:

    ![Képernyőkép, amely a lekérdezés futtatása után megjeleníti az eredményeket a vezérlő táblából.](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Következő lépések

- [Fájlok másolása több tárolóból](solution-template-copy-files-multiple-containers.md)
- [Fájlok áthelyezése](solution-template-move-files.md)
