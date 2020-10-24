---
title: 'Rövid útmutató: Apache Spark-fürtök az Azure CLI-vel – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsight az Azure CLI használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 91c88d1084329ea3681ef39c1d8d755ed31d9ddd
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490883"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsight az Azure CLI használatával

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsight az Azure parancssori felület (CLI) használatával. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. A HDInsight Apache Spark keretrendszere lehetővé teszi a gyors adatelemzést és a fürtözést a memóriabeli feldolgozás használatával. Az Azure CLI a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

Ha egyszerre több fürtöt használ, létre kell hoznia egy virtuális hálózatot, és ha Spark-fürtöt használ, akkor a méhkas Warehouse-összekötőt is érdemes használnia. További információ: [virtuális hálózat megtervezése az Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) és [a Apache Spark és Apache Hive integrálása a kaptár Warehouse-összekötővel](../interactive-query/apache-hive-warehouse-connector.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ha nem szeretné használni a Azure Cloud Shellt, az [Azure CLI](/cli/azure/install-azure-cli)-t használja.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Apache Spark-fürt létrehozása

1. Jelentkezzen be az Azure-előfizetésbe. Ha Azure Cloud Shell használatát tervezi, válassza a **kipróbálás** lehetőséget az alábbi kódrészlet jobb felső sarkában. Más esetben adja meg a következő parancsot:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Környezeti változók beállítása Az ebben a rövid útmutatóban szereplő változók használata bash-alapú. Más környezetek esetében kisebb eltérésekre lesz szükség. Cserélje le a RESOURCEGROUPNAME, a LOCATION, a CLUSTERNAME, a STORAGEACCOUNTNAME és a PASSWORD értéket az alábbi kódrészletbe a kívánt értékekkel. Ezután írja be a CLI-parancsokat a környezeti változók beállításához.

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Hozza létre az erőforráscsoportot az alábbi parancs beírásával:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Hozzon létre egy Azure Storage-fiókot az alábbi parancs beírásával:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Bontsa ki az elsődleges kulcsot az Azure Storage-fiókból, és tárolja egy változóban az alábbi parancs beírásával:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Hozzon létre egy Azure Storage-tárolót az alábbi parancs beírásával:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Hozza létre a Apache Spark-fürtöt a következő parancs beírásával:

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

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

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsight az Azure CLI használatával.  Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan futtathat HDInsight-fürtöket interaktív lekérdezések futtatásához a mintaadatok használatával.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása Apache Sparkon](./apache-spark-load-data-run-query.md)
