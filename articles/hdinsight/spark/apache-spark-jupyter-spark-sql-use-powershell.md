---
title: 'Rövid útmutató: HDInsight Spark-fürt létrehozása Azure PowerShell'
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure PowerShell egy Azure Spark-fürt létrehozásához az Azure HDInsightban, illetve egy egyszerű Spark SQL-lekérdezés futtatásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc
ms.openlocfilehash: d2ab605d3532df1623ff087dbf1f93dad35445f3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494520"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsight a PowerShell használatával

Ismerje meg, hogyan hozhat létre [Apache Spark](https://spark.apache.org/) -fürtöt az Azure HDInsight, és hogyan FUTTATHAT Spark SQL-lekérdezéseket [Apache Hive](https://hive.apache.org/) táblákon. Az Apache Spark a memóriában végzett feldolgozás segítségével teszi lehetővé a gyors adatelemzést és fürtszámítást. A Spark on HDInsight további információiért lásd: [Áttekintés: Apache Spark on Azure HDInsight](apache-spark-overview.md).

Ebben a rövid útmutatóban egy HDInsight Spark-fürtöt hoz létre az Azure PowerShell használatával. A fürt Azure Storage-blobokat használ fürttárolóként. A 2. generációs Data Lake Storage használatáról [a fürtök HDInsightban történő beállításáról szóló rövid útmutatóban](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) talál további információt.

> [!IMPORTANT]  
> A HDInsight-fürtök számlázása percenként történik, akár használja őket, akár nem. Mindig törölje a fürtöt, ha már nem használja. További információkért lásd a cikk [Az erőforrások eltávolítása](#clean-up-resources) című szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisite"></a>Előfeltétel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A PowerShell az [modul](https://docs.microsoft.com/powershell/azure/overview) telepítve van.

## <a name="create-an-hdinsight-spark-cluster"></a>HDInsight Spark-fürt létrehozása

A HDInsight-fürtök létrehozása a következő Azure-objektumok és erőforrások létrehozását tartalmazza:

- Azure-erőforráscsoport. Az Azure-erőforráscsoport az Azure-erőforrások tárolója.
- Egy Azure Storage-fiók vagy Azure Data Lake Storage.  Minden egyes HDInsight-fürt egy függő adattárat igényel. Ebben a rövid útmutatóban egy tárfiókot hoz létre.
- HDInsight-fürt, különböző fürttípusokkal.  Ebben a rövid útmutatóban egy Spark 2.3-fürtöt hoz létre.

Egy PowerShell-szkript használatával hozhatja létre az erőforrásokat.  A szkript futtatásakor a rendszer a következő értékek megadására kéri:

|Paraméter|Érték|
|------|------|
|Azure-erőforráscsoport neve | Adjon meg egy egyedi nevet az erőforráscsoportnak.|
|Földrajzi egység| Adja meg az Azure-régiót, például: USA középső régiója. |
|Alapértelmezett tárfióknév | Adjon meg egy egyedi nevet a tárfióknak. |
|Fürt neve | Adjon meg egy egyedi nevet a HDInsight Spark-fürtnek.|
|Fürt hitelesítő adatai | Ezt a fiókot a fürt irányítópultjához való csatlakozásra fogja használni a rövid útmutató későbbi részében.|
|SSH-felhasználó hitelesítő adatai | Az SSH-ügyfeleket arra használhatja, hogy távoli parancssori munkamenetet hozzon létre a HDInsight-fürtökkel.|

1. Válassza a jobb felső sarokban lévő **kipróbálás** lehetőséget a következő kódrészlet megnyitásához [Azure Cloud Shell](../../cloud-shell/overview.md)megnyitásához, majd kövesse az utasításokat az Azure-hoz való kapcsolódáshoz.

2. Másolja és illessze be a következő PowerShell-szkriptet a Cloud Shellbe.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value

    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   A fürt létrehozása nagyjából 20 percet vesz igénybe. A következő munkamenetre csak a fürt létrehozását követően lehet továbblépni.

Ha problémába ütközik a HDInsight-fürtök létrehozása során, előfordulhat, hogy nem rendelkezik a szükséges engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

A [Jupyter notebook](https://jupyter.org/) egy interaktív notebook-környezet, amely különböző programozási nyelveket támogat. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését.

1. Nyissa meg az [Azure portált](https://portal.azure.com).

1. Válassza a **HDInsight-fürtök** lehetőséget, majd a létrehozott fürtöt.

    ![HDInsight-fürt megnyitása az Azure Portalon](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

1. A portálon válassza a **Fürt irányítópultja** lehetőséget, majd a **Jupyter Notebook** elemet. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

   ![Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása")

1. Új notebook létrehozásához válassza a **New** > **PySpark** (Új > PySpark) lehetőséget.

   ![Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

## <a name="run-spark-sql-statements"></a>Spark SQL-utasítások futtatása

Az SQL az adatok lekérdezéséhez és meghatározásához leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Ellenőrizze, hogy a kernel készen áll-e. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    ![kernel állapota](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "kernel állapota")

    A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön. 
1. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A parancs felsorolja a fürtön található Hive-táblákat:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Ha a Jupyter notebookot HDInsight Spark-fürttel használja, egy előre beállított `sqlContext` elemet kap, amelyet Hive-lekérdezések Spark SQL-lel végzett futtatásához használhat. A `%%sql` megadja a Jupyter notebook számára, hogy az előre beállított `sqlContext` elemet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. Az eredmények lekérdezése körülbelül 30 másodpercet vesz igénybe. A kimenet a következőképpen fog kinézni:

    ![Apache Hive lekérdezés a HDInsight Sparkban](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Kaptár-lekérdezés a HDInsight Sparkban")

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Struktúra lekérdezési kimenete a HDInsight Sparkban](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Struktúra lekérdezési kimenete a HDInsight Sparkban")

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A notebook leállítása felszabadítja a fürt erőforrásait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight az Azure Storage vagy az Azure Data Lake Storage tárhelyein tárolja az adatokat, így a nem használt fürtök biztonságosan törölhetők. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. Ha azt tervezi, hogy rögtön elvégzi a [További lépések](#next-steps) szakaszban található oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Lépjen vissza az Azure Portalra és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése Azure Portal](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésekor a rendszer a HDInsight Spark-fürtöt és az alapértelmezett tárfiókot is törli.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>A PowerShell az modul

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozható létre egy HDInsight Spark-fürt, illetve hogyan futtatható egy alapszintű Spark SQL-lekérdezés. Folytassa a következő oktatóanyaggal, amelyben megtudhatja, hogyan használhatja a HDInsight Spark-fürtöt interaktív lekérdezések mintaadatokon való futtatására.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása Apache Sparkon](./apache-spark-load-data-run-query.md)
