---
title: 'Oktatóanyag: Azure Data Lake Storage Gen2, Azure Databricks & Spark | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan futtathat Spark-lekérdezéseket egy Azure Databricks-fürtön egy Azure Data Lake Storage Gen2 Storage-fiókban lévő adateléréshez.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.custom: devx-track-python
ms.openlocfilehash: 232e28d3cc8b0bc7427dd035d51743f623e54259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564303"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Oktatóanyag: Azure Data Lake Storage Gen2, Azure Databricks & Spark

Ez az oktatóanyag bemutatja, hogyan csatlakoztatható a Azure Databricks-fürt egy olyan Azure Storage-fiókban tárolt adataihoz, amely Azure Data Lake Storage Gen2 engedélyezve van. Ez a kapcsolat lehetővé teszi, hogy natív módon futtasson lekérdezéseket és elemzéseket a fürtből az adatokból.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * Databricks-fürt létrehozása
> * Strukturálatlan adatok betöltése egy tárfiókba
> * Elemzés futtatása a blob Storage-beli adataiban

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy Azure Data Lake Storage Gen2 fiókot.

  Lásd: [Azure Data Lake Storage Gen2 használatával használandó Storage-fiók létrehozása](create-data-lake-storage-account.md).

* Győződjön meg arról, hogy a felhasználói fiókja rendelkezik a [Storage blob adatközreműködői szerepkörhöz](../common/storage-auth-aad-rbac-portal.md) hozzárendelve.

* Telepítse a AzCopy V10-es frissítést. [Adatok átvitele az AzCopy v10-vel](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Egyszerű szolgáltatásnév létrehozása. [Útmutató: a portál használatával létrehozhat egy Azure ad-alkalmazást és egy egyszerű szolgáltatást, amely hozzáférhet az erőforrásokhoz](../../active-directory/develop/howto-create-service-principal-portal.md).

  A cikk lépéseinek elvégzése során néhány konkrét dolgot is el kell végeznie.

  : heavy_check_mark: Ha az [alkalmazás társítása szerepkörhöz](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) című szakasz lépéseit hajtja végre, akkor ügyeljen arra, hogy a szolgáltatáshoz hozzárendelje a **tárolási blob adatközreműködői** szerepkört.

  > [!IMPORTANT]
  > Ügyeljen arra, hogy a szerepkört a Data Lake Storage Gen2 Storage-fiók hatókörében rendelje hozzá. Hozzárendelhet egy szerepkört a szülő erőforráscsoporthoz vagy az előfizetéshez, de az engedélyekkel kapcsolatos hibákat addig kapja, amíg a szerepkör-hozzárendelések el nem terjednek a Storage-fiókba.

  : heavy_check_mark: a cikk [beolvasási értékek beolvasása](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) szakaszában szereplő lépések végrehajtásakor illessze be a bérlői azonosítót, az alkalmazás azonosítóját és az ügyfél titkos értékét szövegfájlba. Ezekre hamarosan szüksége lesz.

### <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

Ez az oktatóanyag repülési adatokat használ az Bureau of közlekedési statisztikából, hogy bemutassa, hogyan hajthat végre ETL-műveletet. Az oktatóanyag befejezéséhez le kell töltenie ezeket az adatfájlokat.

1. Látogasson el a [Research and innovatív Technology Administration, a közlekedés statisztikáit ismertető irodára](https://www.transtats.bts.gov/DL_SelectFields.asp?gnoyr_VQ=FGJ).

2. Jelölje be az **előtömörített fájl** jelölőnégyzetet az összes adatmező kiválasztásához.

3. Kattintson a **Letöltés** gombra, és mentse az eredményeket a számítógépére. 

4. Bontsa ki a tömörített fájl tartalmát, és jegyezze fel a fájl nevét és elérési útját. Ezt az információt egy későbbi lépésben kell megadnia.

## <a name="create-an-azure-databricks-service"></a>Azure Databricks szolgáltatás létrehozása

Ebben a szakaszban egy Azure Databricks szolgáltatást hoz létre a Azure Portal használatával.

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Azure Databricks** lehetőséget.

    ![Databricks Azure Portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks Azure Portal")

2. A **Azure Databricks szolgáltatás** területen adja meg a következő értékeket egy Databricks szolgáltatás létrehozásához:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.  |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../azure-resource-manager/management/overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).       |
    |**Tarifacsomag**     |  Válassza a **standard** lehetőséget.     |

    ![Azure Databricks-munkaterület létrehozása](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Azure Databricks szolgáltatás létrehozása")

3. A fiók létrehozása eltarthat néhány percig. A művelet állapotának figyeléséhez tekintse meg a felső folyamatjelző sávot.

4. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Spark-fürt létrehozása az Azure Databricksben

1. A Azure Portal lépjen a létrehozott Databricks szolgáltatásra, majd válassza a **munkaterület elindítása** lehetőséget.

2. A rendszer átirányítja a Azure Databricks portálra. A portálon válassza a **Fürt** elemet.

    ![Databricks az Azure-ban](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Adjon meg értékeket a következő mezőkben, és fogadja el az alapértelmezett értékeket a többi mezőben:

    - Adjon egy nevet a fürtnek.
     
    - Mindenképpen jelölje be a **Leállítás 120 percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.

4. Válassza a **fürt létrehozása** lehetőséget. A fürt futása után jegyzetfüzeteket csatolhat a fürthöz, és futtathatja a Spark-feladatokat.

## <a name="ingest-data"></a>Adatok betöltése

### <a name="copy-source-data-into-the-storage-account"></a>Forrásadatok másolása a tárfiókba

A AzCopy segítségével másolja át az adatait a *. csv* -fájlból a Data Lake Storage Gen2-fiókjába.

1. Nyisson meg egy parancssori ablakot, és írja be a következő parancsot a Storage-fiókjába való bejelentkezéshez.

   ```bash
   azcopy login
   ```

   A felhasználói fiók hitelesítéséhez kövesse a parancssori ablakban megjelenő utasításokat.

2. Az adatok *. csv* -fiókból történő másolásához írja be a következő parancsot.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Cserélje le a `<csv-folder-path>` helyőrző értékét a *. csv* -fájl elérési útjára.

   * Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

   * Cserélje le a `<container-name>` helyőrzőt egy tároló nevére a Storage-fiókban.

## <a name="create-a-container-and-mount-it"></a>Tároló létrehozása és csatlakoztatása

Ebben a szakaszban egy tárolót és egy mappát fog létrehozni a Storage-fiókban.

1. A [Azure Portal](https://portal.azure.com)lépjen a létrehozott Azure Databricks szolgáltatásra, majd válassza a **munkaterület indítása** elemet.

2. A bal oldalon válassza a **munkaterület** lehetőséget. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Python** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

4. Válassza a **Létrehozás** lehetőséget.

5. Másolja és illessze be az alábbi kódrészletet az első cellába, de még ne futtassa ezt a kódot.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. A kód blokkban cserélje le a `appId` ,,, `clientSecret` `tenant` és `storage-account-name` helyőrző értékeket a kódban az oktatóanyag előfeltételeinek teljesítése során összegyűjtött értékekre. Cserélje le a `container-name` helyőrző értékét a tároló nevére.

19. Nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához ebben a blokkban.

   Tartsa megnyitva a jegyzetfüzetet, mert később parancsokat ad hozzá.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>CSV konvertálása parquetté a Databricks-jegyzetfüzet használatával

A korábban létrehozott jegyzetfüzetben adjon hozzá egy új cellát, és illessze be a következő kódot a cellába. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Adatok feltárása

Az új cellában illessze be a következő kódot a AzCopy-on keresztül feltöltött CSV-fájlok listájának lekéréséhez.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Új fájl létrehozásához és fájlok listázásához a *parquet/flights* mappában futtassa ezt a szkriptet:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Ezekkel a kódmintákkal megismerte a HDFS hierarchikus jellegét a Data Lake Storage Gen2-kompatiblis tárfiókban tárolt adatok használatával.

## <a name="query-the-data"></a>Adatok lekérdezése

Következő lépésként megkezdheti a tárfiókba feltöltött adatok lekérdezését. Írja be a következő kódblokkok mindegyikét a **Cmd 1** területre, majd nyomja le a **Cmd + ENTER** billentyűkombinációt a Python-szkript futtatásához.

Az adatforrások adatkeretének létrehozásához futtassa a következő parancsfájlt:

* Cserélje le a `<csv-folder-path>` helyőrző értékét a *. csv* -fájl elérési útjára.

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Adja meg ezt a parancsfájlt az adatokra vonatkozó alapszintű elemzési lekérdezések futtatásához.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez válassza ki a Storage-fiókhoz tartozó erőforráscsoportot, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Adatok kinyerése, átalakítása és betöltése az Azure HDInsight-alapú Apache Hive használatával](data-lake-storage-tutorial-extract-transform-load-hive.md)
