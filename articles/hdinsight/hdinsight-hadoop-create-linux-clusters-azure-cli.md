---
title: Apache Hadoop-fürtök létrehozása az Azure CLI használatával – Azure HDInsight
description: Megtudhatja, hogyan hozhat Azure HDInsight fürtöt a platformfüggetlen Azure CLI használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: 9c19eb58e32fec66e5fe698c82133c8583f67b8b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775132"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>HDInsight-fürtök létrehozása az Azure CLI használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Az ebben a dokumentumban található lépések egy HDInsight 3.6-fürt Azure CLI használatával való létrehozását ják végre.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-cluster"></a>Fürt létrehozása

1. Jelentkezzen be az Azure-előfizetésbe. Ha a kódblokk Azure Cloud Shell használni,  válassza a Kódblokk jobb felső sarkában található Kipróbálom lehetőséget. Ha nem, írja be az alábbi parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Környezeti változók beállítása. A változók használata ebben a cikkben a Bash-alapú. Más környezetekhez kisebb eltérésekre lesz szükség. A fürt létrehozására vonatkozó lehetséges paraméterek teljes listájáért lásd: [az az-hdinsight-create.](/cli/azure/hdinsight#az_hdinsight_create)

    |Paraméter | Leírás |
    |---|---|
    |`--workernode-count`| A fürtben lévő munkavégző csomópontok száma. Ez a cikk a `clusterSizeInNodes` változót használja a számára átadott értékként. `--workernode-count` |
    |`--version`| A HDInsight-fürt verziója. Ez a cikk a `clusterVersion` változót használja a számára átadott értékként. `--version` Lásd még: [Támogatott HDInsight-verziók.](./hdinsight-component-versioning.md#supported-hdinsight-versions)|
    |`--type`| A HDInsight-fürt típusa, például: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  Ez a cikk a `clusterType` változót használja a számára átadott értékként. `--type` Lásd még: [Fürttípusok és konfiguráció.](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type)|
    |`--component-version`|A különböző Hadoop-összetevők verziói, "component=version" formátumban, szóközökre elválasztott verziókban. Ez a cikk a `componentVersion` változót használja a függvénynek átadott értékként. `--component-version` Lásd még: [Hadoop-összetevők.](./hdinsight-component-versioning.md)|

    Cserélje `RESOURCEGROUPNAME` le a , , , és `LOCATION` `CLUSTERNAME` `STORAGEACCOUNTNAME` `PASSWORD` értékeket a kívánt értékekre. Szükség szerint módosítsa a többi változó értékeit. Ezután adja meg a CLI-parancsokat.

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

3. [Az erőforráscsoport létrehozásához](/cli/azure/group#az_group_create) írja be az alábbi parancsot:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Az érvényes helyek listájához használja az parancsot, majd használja az érték egyik `az account list-locations` `name` helyét.

4. [Hozzon létre egy Azure Storage-fiókot](/cli/azure/storage/account#az_storage_account_create) az alábbi paranccsal:

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

5. [Az alábbi paranccsal bontsa](/cli/azure/storage/account/keys#az_storage_account_keys_list) ki az elsődleges kulcsot az Azure Storage-fiókból, és tárolja egy változóban:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Hozzon létre egy Azure Storage-tárolót](/cli/azure/storage/container#az_storage_container_create) az alábbi parancs beírásával:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Hozza létre a HDInsight-fürtöt](/cli/azure/hdinsight#az_hdinsight_create) a következő parancs beírásával:

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
    > A HDInsight-fürtök különböző típusúak, amelyek a fürt által hangolt számítási feladatnak vagy technológiának felelnek meg. Nincs támogatott módszer olyan fürtök létrehozására, amelyek több típust kombinálnak, például a Stormot és a HBase-t egy fürtön.

    A fürt létrehozási folyamata több percig is eltarthat. Általában körülbelül 15.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így a nem használt fürtök biztonságosan törölhetők. A HDInsight-fürtökért akkor is díjat kell fizetnie, ha nincs használatban. Mivel a fürt díja sokszor több, mint a tárolás díja, gazdasági szempontból logikus törölni a nem használt fürtök költségeit.

Az erőforrások eltávolításához adja meg az összes vagy néhány alábbi parancsot:

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

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt az Azure CLI-n keresztül, a következővel megtanulja, hogyan használhatja a fürtöt:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-fürtök

* [Az Apache Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsightban](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [A HDInsight-alapú Apache HBase első lépések](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-alkalmazások fejlesztése a HDInsight-alapú Apache HBase-hez](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm fürtök

* [Java-topológiák fejlesztése Apache Storm HDInsighton](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata Apache Storm HDInsightban](storm/apache-storm-develop-python-topology.md)
* [Topológiák üzembe helyezése és monitor helyezése Apache Storm HDInsighton](storm/apache-storm-deploy-monitor-topology-linux.md)
