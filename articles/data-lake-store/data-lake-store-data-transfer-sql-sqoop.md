---
title: Adatmásolás Data Lake Storage Gen1 és az Azure SQL-Sqoop között | Microsoft Docs
description: Az Sqoop használata Azure SQL Database és Azure Data Lake Storage Gen1 közötti adatmásoláshoz
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109187"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Adatmásolás Data Lake Storage Gen1 és Azure SQL Database között az Sqoop használatával

Ismerje meg, hogyan importálhat és exportálhat az Apache Sqoop Azure SQL Database és Azure Data Lake Storage Gen1 között.

## <a name="what-is-sqoop"></a>Mi az a Sqoop?

A Big Application típusú alkalmazások természetes választás a strukturálatlan és részben strukturált adatmennyiségek, például naplók és fájlok feldolgozásához. Azonban szükség lehet a kapcsolódó adatbázisokban tárolt strukturált adatfeldolgozásra is.

Az [Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) egy olyan eszköz, amelynek célja az adatok átvitele a kapcsolati adatbázisok és egy Big Data adattár között, például Data Lake Storage Gen1. Felhasználhatja az adatok importálását egy kapcsolódó adatbázis-kezelő rendszerből (RDBMS), például a Azure SQL Databaset a Data Lake Storage Gen1ba. Ezután átalakíthatja és elemezheti az adatok big data munkaterhelések használatával, majd visszaexportálhatja az adatok egy RDBMS. Ebben a cikkben egy Azure SQL Database-adatbázist használ a (z) importálási/exportálási környezetében.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen1-fiók**. A fiók létrehozásával kapcsolatos útmutatásért tekintse meg a [Azure Data Lake Storage Gen1 első lépéseivel](data-lake-store-get-started-portal.md) foglalkozó témakört.
* **Azure HDInsight-fürt** Data Lake Storage Gen1 fiókhoz való hozzáféréssel. Lásd: [HDInsight-fürt létrehozása Data Lake Storage Gen1sal](data-lake-store-hdinsight-hadoop-use-portal.md). Ez a cikk feltételezi, hogy rendelkezik egy Data Lake Storage Gen1 hozzáféréssel rendelkező HDInsight Linux-fürttel.
* **Azure SQL Database**. Az adatbázisok Azure SQL Databaseban való létrehozásával kapcsolatos utasításokért lásd: [adatbázis létrehozása a Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)

## <a name="create-sample-tables-in-the-database"></a>Minta táblák létrehozása az adatbázisban

1. A kezdéshez hozzon létre két minta táblát az adatbázisban. A [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) vagy a Visual Studio használatával kapcsolódjon az adatbázishoz, majd futtassa a következő lekérdezéseket.

    **Tábla1 létrehozása**

    ```tsql
    CREATE TABLE [dbo].[Table1](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

    **Table2 létrehozása**

    ```tsql
    CREATE TABLE [dbo].[Table2](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

1. A következő parancs futtatásával vegyen fel néhány mintaadatok **tábla1**. Hagyja üresen a **Table2** . Később importálja a **tábla1** adatait a Data Lake Storage Gen1ba. Ezután exportálja Data Lake Storage Gen1 adatait a **Table2**.

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Sqoop használata egy HDInsight-fürtről Data Lake Storage Gen1hoz való hozzáféréssel

An méretű HDInsight fürt már rendelkezik elérhető Sqoop-csomagokkal. Ha úgy konfigurálta a HDInsight-fürtöt, hogy a Data Lake Storage Gen1 további tárterületként használja, akkor a Sqoop (konfiguráció módosítása nélkül) a kapcsolati adatbázis, például a Azure SQL Database és egy Data Lake Storage Gen1 fiók közötti importáláshoz/exportáláshoz használhatja.

1. Ebben a cikkben feltételezzük, hogy létrehozott egy Linux-fürtöt, hogy az SSH használatával csatlakozzon a fürthöz. Lásd: [Kapcsolódás Linux-alapú HDInsight-fürthöz](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ellenőrizze, hogy elérhető-e a Data Lake Storage Gen1 fiók a fürtből. Futtassa az alábbi parancsot az SSH parancssorból:

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   Ez a parancs a Data Lake Storage Gen1 fiókban található fájlok/mappák listáját tartalmazza.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Adatok importálása Azure SQL Databaseból a Data Lake Storage Gen1ba

1. Navigáljon ahhoz a könyvtárhoz, ahol elérhetők a Sqoop-csomagok. Ez a hely általában a következő: `/usr/hdp/<version>/sqoop/bin` .

1. Importálja a **tábla1** adatait a Data Lake Storage Gen1-fiókba. Használja a következő szintaxist:

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   Az **SQL-Database-Server-Name** helyőrző annak a kiszolgálónak a nevét jelöli, amelyen az adatbázis fut. az **SQL-Database-Name** helyőrző az adatbázis tényleges nevét jelöli.

   Példa:

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. Győződjön meg arról, hogy az adatátvitelt a Data Lake Storage Gen1 fiókba. Futtassa az alábbi parancsot:

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   A következő kimenetnek kell megjelennie.

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   Minden **rész-az m-** _ fájl a forrástábla egyik sorához tartozik, _ *tábla1 * *. Az ellenőrzéshez megtekintheti a rész-m-files tartalmát* .

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Adatok exportálása Data Lake Storage Gen1ból a Azure SQL Databaseba

1. Exportálja a Data Lake Storage Gen1 fiók adatait a Azure SQL Database üres táblába, a **Table2**. Használja a következő szintaxist:

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   Példa:

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. Ellenőrizze, hogy az adatSQL Databasei táblába lett-e feltöltve. A [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) vagy a Visual Studio használatával kapcsolódjon a Azure SQL Databasehoz, majd futtassa a következő lekérdezést.

    ```tsql
    SELECT * FROM TABLE2
    ```

   A parancsnak a következő kimenettel kell rendelkeznie.

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>Teljesítménnyel kapcsolatos megfontolások a Sqoop használata során

A Sqoop-feladatnak a Data Lake Storage Gen1ba való másolásához szükséges teljesítmény hangolásával kapcsolatos információkért tekintse meg a [Sqoop Performance blogbejegyzését](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs).

## <a name="next-steps"></a>Következő lépések

* [Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1ba](data-lake-store-copy-data-azure-storage-blob.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)