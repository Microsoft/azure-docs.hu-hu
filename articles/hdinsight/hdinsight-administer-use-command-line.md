---
title: Fürtök Azure HDInsight Az Azure CLI használatával
description: Megtudhatja, hogyan kezelheti a fürtök Azure HDInsight Azure CLI-t. A fürttípusok közé tartozik az Apache Hadoop, a Spark, a HBase, a Storm, a Kafka, Interactive Query és az ML-szolgáltatások.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: 14b88700f3968e3bfdc788abb2fc9ce90634068e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770344"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Fürtök Azure HDInsight Az Azure CLI használatával

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Megtudhatja, hogyan használhatja az [Azure CLI-t](/cli/azure/) a Azure HDInsight kezelésére. Az Azure parancssori felülete (CLI) a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Azure CLI. Ha még nem telepítette az Azure CLI-t, a lépésekért lásd: [Az Azure CLI](/cli/azure/install-azure-cli) telepítése.

* Apache Hadoop-fürt a HDInsightban. Lásd: Get Started with HDInsight on Linux (A [HDInsight első lépések Linux rendszeren) .](hadoop/apache-hadoop-linux-tutorial-get-started.md)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Jelentkezzen be az Azure-előfizetésbe. Ha a kódblokk Azure Cloud Shell használni,  válassza a Kódblokk jobb felső sarkában található Kipróbálom lehetőséget. Ha nem, írja be az alábbi parancsot:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Fürtök listába sorolása

A [fürtök listához használja az az hdinsight listát.](/cli/azure/hdinsight#az_hdinsight_list) Szerkessze az alábbi parancsokat úgy, hogy a et az erőforráscsoport nevére cseréli, majd `RESOURCE_GROUP_NAME` írja be a következő parancsokat:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Fürt megjelenítése

Az [az hdinsight show használatával egy](/cli/azure/hdinsight#az_hdinsight_show) adott fürt információit mutatjuk be. Szerkessze az alábbi parancsot úgy, hogy lecseréli a és a adatokat a megfelelő `RESOURCE_GROUP_NAME` `CLUSTER_NAME` információkra, majd írja be a parancsot:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Fürtök törlése

Egy adott fürt törléséhez használja az [az hdinsight delete](/cli/azure/hdinsight#az_hdinsight_delete) parancsot. Szerkessze az alábbi parancsot úgy, hogy lecseréli a és a adatokat a megfelelő `RESOURCE_GROUP_NAME` `CLUSTER_NAME` információkra, majd írja be a parancsot:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Fürtöt a fürtöt tartalmazó erőforráscsoport törlésével is törölhet. Vegye figyelembe, hogy ezzel törli a csoport összes erőforrását, beleértve az alapértelmezett tárfiókot is.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Fürtök méretezése

Az [az hdinsight resize használatával](/cli/azure/hdinsight#az_hdinsight_resize) méretezze át a megadott HDInsight-fürtöt a megadott méretre. Szerkessze az alábbi parancsot úgy, hogy lecseréli a `RESOURCE_GROUP_NAME` és `CLUSTER_NAME` a adatokat a megfelelő információkra. Cserélje le a helyére a fürthöz `WORKERNODE_COUNT` kívánt munkavégző csomópontok számát. További információ a fürtök méretezésről: [HDInsight-fürtök skálázása.](./hdinsight-scaling-best-practices.md) Írja be a következő parancsot:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hajthat végre különböző HDInsight-fürtök felügyeleti feladatait. További tudnivalókért olvassa el a következő cikket:

* [Apache Hadoop-fürtök kezelése a HDInsightban a Azure Portal](hdinsight-administer-use-portal-linux.md)
* [A HDInsight felügyelete a Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Első lépések a Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Az Azure CLI használatának első lépései](/cli/azure/get-started-with-azure-cli)
