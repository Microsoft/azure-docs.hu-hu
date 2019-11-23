---
title: 'Tutorial: Azure Data Lake Storage Gen2, Azure Databricks & Spark | Microsoft Docs'
description: This tutorial shows how to run Spark queries on an Azure Databricks cluster to access data in an Azure Data Lake Storage Gen2 storage account.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: bbe936fd572a8e23fb6e7c5da4a4bffef1c8bf7e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327539"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Tutorial: Azure Data Lake Storage Gen2, Azure Databricks & Spark

This tutorial shows you how to connect your Azure Databricks cluster to data stored in an Azure storage account that has Azure Data Lake Storage Gen2 enabled. This connection enables you to natively run queries and analytics from your cluster on your data.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Databricks-fürt létrehozása
> * Strukturálatlan adatok betöltése egy tárfiókba
> * Run analytics on your data in Blob storage

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Create an Azure Data Lake Storage Gen2 account.

  See [Create an Azure Data Lake Storage Gen2 account](data-lake-storage-quickstart-create-account.md).

* Make sure that your user account has the [Storage Blob Data Contributor role](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) assigned to it.

* Install AzCopy v10. See [Transfer data with AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Create a service principal. See [How to: Use the portal to create an Azure AD application and service principal that can access resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  There's a couple of specific things that you'll have to do as you perform the steps in that article.

  :heavy_check_mark: When performing the steps in the [Assign the application to a role](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) section of the article, make sure to assign the **Storage Blob Data Contributor** role to the service principal.

  > [!IMPORTANT]
  > Make sure to assign the role in the scope of the Data Lake Storage Gen2 storage account. You can assign a role to the parent resource group or subscription, but you'll receive permissions-related errors until those role assignments propagate to the storage account.

  :heavy_check_mark: When performing the steps in the [Get values for signing in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) section of the article, paste the tenant ID, app ID, and password values into a text file. You'll need those soon.

### <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

This tutorial uses flight data from the Bureau of Transportation Statistics to demonstrate how to perform an ETL operation. You must download this data to complete the tutorial.

1. Go to [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Select the **Prezipped File** check box to select all data fields.

3. Select the **Download** button and save the results to your computer. 

4. Unzip the contents of the zipped file and make a note of the file name and the path of the file. You need this information in a later step.

## <a name="create-an-azure-databricks-service"></a>Create an Azure Databricks service

In this section, you create an Azure Databricks service by using the Azure portal.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Elemzés** > **Azure Databricks** elemet.

    ![Databricks on Azure portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks on Azure portal")

2. Under **Azure Databricks Service**, provide the following values to create a Databricks service:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.  |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../azure-resource-manager/resource-group-overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).       |
    |**Tarifacsomag**     |  Select **Standard**.     |

    ![Create an Azure Databricks workspace](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Create an Azure Databricks service")

3. A fiók létrehozása eltarthat néhány percig. To monitor the operation status, view the progress bar at the top.

4. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Spark-fürt létrehozása az Azure Databricksben

1. In the Azure portal, go to the Databricks service that you created, and select **Launch Workspace**.

2. You're redirected to the Azure Databricks portal. A portálon válassza a **Fürt** elemet.

    ![Databricks on Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks on Azure")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Create Databricks Spark cluster on Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

    Adjon meg értékeket a következő mezőkben, és fogadja el az alapértelmezett értékeket a többi mezőben:

    - Adjon egy nevet a fürtnek.
     
    - Mindenképpen jelölje be a **Leállítás 120 percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.

4. Válassza a **Fürt létrehozása** lehetőséget. After the cluster is running, you can attach notebooks to the cluster and run Spark jobs.

## <a name="ingest-data"></a>Adatok kigyűjtése

### <a name="copy-source-data-into-the-storage-account"></a>Forrásadatok másolása a tárfiókba

Use AzCopy to copy data from your *.csv* file into your Data Lake Storage Gen2 account.

1. Open a command prompt window, and enter the following command to log into your storage account.

   ```bash
   azcopy login
   ```

   Follow the instructions that appear in the command prompt window to authenticate your user account.

2. To copy data from the *.csv* account, enter the following command.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Replace the `<csv-folder-path>` placeholder value with the path to the *.csv* file.

   * Replace the `<storage-account-name>` placeholder value with the name of your storage account.

   * Replace the `<container-name>` placeholder with any name that you want to give your container.

## <a name="create-a-container-and-mount-it"></a>Create a container and mount it

In this section, you'll create a container and a folder in your storage account.

1. In the [Azure portal](https://portal.azure.com), go to the Azure Databricks service that you created, and select **Launch Workspace**.

2. On the left, select **Workspace**. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Create a notebook in Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Create notebook in Databricks")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Select **Python** as the language, and then select the Spark cluster that you created earlier.

4. Kattintson a **Létrehozás** gombra.

5. Copy and paste the following code block into the first cell, but don't run this code yet.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<password>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. In this code block, replace the `appId`, `password`, `tenant`, and `storage-account-name` placeholder values in this code block with the values that you collected while completing the prerequisites of this tutorial. Replace the `container-name` placeholder value with the name that you gave to the container on the previous step.

Use these values to replace the mentioned placeholders.

   * The `appId`, and `password` are from the app that you registered with active directory as part of creating a service principal.

   * The `tenant-id` is from your subscription.

   * The `storage-account-name` is the name of your Azure Data Lake Storage Gen2 storage account.

   * Replace the `container-name` placeholder with any name that you want to give your container.

   > [!NOTE]
   > In a production setting, consider storing your password in Azure Databricks. Then, add a look up key to your code block instead of the password. After you've completed this quickstart, see the [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) article on the Azure Databricks Website to see examples of this approach.

19. Press the **SHIFT + ENTER** keys to run the code in this block.

   Keep this notebook open as you will add commands to it later.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>CSV konvertálása parquetté a Databricks-jegyzetfüzet használatával

In the notebook that you previously created, add a new cell, and paste the following code into that cell. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Adatok megismerése

In a new cell, paste the following code to get a list of CSV files uploaded via AzCopy.

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

To create data frames for your data sources, run the following script:

* Replace the `<csv-folder-path>` placeholder value with the path to the *.csv* file.

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

Enter this script to run some basic analysis queries against the data.

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

When they're no longer needed, delete the resource group and all related resources. To do so, select the resource group for the storage account and select **Delete**.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Adatok kinyerése, átalakítása és betöltése az Azure HDInsight-alapú Apache Hive használatával](data-lake-storage-tutorial-extract-transform-load-hive.md)
