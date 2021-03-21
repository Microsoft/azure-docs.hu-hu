---
title: Apache Hadoop-fürtök létrehozása az Azure CLI-vel – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre Azure HDInsight-fürtöket a platformfüggetlen Azure CLI használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: 9028d85346611341afec0d0598f27a77e4f37fdf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715496"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>HDInsight-fürtök létrehozása az Azure CLI használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

A jelen dokumentumban ismertetett lépések – HDInsight 3,6-fürt létrehozása az Azure CLI használatával.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-cluster"></a>Fürt létrehozása

1. Jelentkezzen be az Azure-előfizetésbe. Ha azt tervezi, hogy Azure Cloud Shell használ, válassza a **kipróbálás** lehetőséget a kód blokk jobb felső sarkában. Máskülönben adja meg az alábbi parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Környezeti változók beállítása A cikkben szereplő változók használata bash-alapú. Más környezetek esetében kisebb eltérésekre lesz szükség. A fürt létrehozásához szükséges paraméterek teljes listájáért tekintse meg az [az-hdinsight-Create](/cli/azure/hdinsight#az-hdinsight-create) lehetőséget.

    |Paraméter | Leírás |
    |---|---|
    |`--workernode-count`| A fürtben lévő munkavégző csomópontok száma. Ez a cikk a változót használja az `clusterSizeInNodes` átadott értékként `--workernode-count` . |
    |`--version`| A HDInsight-fürt verziója. Ez a cikk a változót használja az `clusterVersion` átadott értékként `--version` . Lásd még: [támogatott HDInsight-verziók](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| A HDInsight-fürt típusa, például: Hadoop, interactivehive, hbase, Kafka, Storm, Spark, Rserver, mlservices.  Ez a cikk a változót használja az `clusterType` átadott értékként `--type` . Lásd még: [fürtök típusai és konfigurálása](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|A különböző Hadoop-összetevők verziói az "összetevő = verzió" formátumban, szóközzel tagolt verziókban. Ez a cikk a változót használja az `componentVersion` átadott értékként `--component-version` . Lásd még: [Hadoop-összetevők](./hdinsight-component-versioning.md).|

    Cserélje le a,, `RESOURCEGROUPNAME` `LOCATION` ,, `CLUSTERNAME` `STORAGEACCOUNTNAME` és `PASSWORD` értéket a kívánt értékekre. Szükség szerint módosítsa a többi változó értékét. Ezután írja be a CLI-parancsokat.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Hozza létre az erőforráscsoportot](/cli/azure/group#az-group-create) az alábbi parancs beírásával:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Az érvényes helyszínek listájához használja a `az account list-locations` parancsot, majd használja az érték egyik helyét `name` .

4. [Hozzon létre egy Azure Storage-fiókot](/cli/azure/storage/account#az-storage-account-create) az alábbi parancs beírásával:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Bontsa ki az elsődleges kulcsot az Azure Storage-fiókból](/cli/azure/storage/account/keys#az-storage-account-keys-list) , és tárolja egy változóban az alábbi parancs beírásával:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Hozzon létre egy Azure Storage-tárolót](/cli/azure/storage/container#az-storage-container-create) az alábbi parancs beírásával:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Hozza létre a HDInsight-fürtöt](/cli/azure/hdinsight#az-hdinsight-create) a következő parancs beírásával:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > A HDInsight-fürtök különböző típusokból állnak, amelyek megfelelnek a fürthöz hangolt munkaterhelésnek vagy technológiának. Nincs támogatott módszer a több típust egyesítő fürt létrehozására, például a Storm és a HBase egy fürtön.

    A fürt létrehozási folyamata több percet is igénybe vehet. Általában körülbelül 15.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

Az erőforrások eltávolításához adja meg az alábbi parancsok mindegyikét vagy egy részét:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt az Azure CLI használatával, a következő paranccsal megismerheti a fürttel való munkát:

### <a name="apache-hadoop-clusters"></a>Fürtök Apache Hadoop

* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase a HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-alkalmazások fejlesztése az Apache HBase a HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Fürtök Apache Storm

* [Java-topológiák fejlesztése a HDInsight Apache Storméhez](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata a HDInsight-ben Apache Storm](storm/apache-storm-develop-python-topology.md)
* [Topológiák üzembe helyezése és figyelése Apache Storm a HDInsight-on](storm/apache-storm-deploy-monitor-topology-linux.md)
