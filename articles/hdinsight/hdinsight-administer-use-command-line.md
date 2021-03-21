---
title: Azure HDInsight-fürtök kezelése az Azure CLI-vel
description: Ismerje meg, hogyan kezelheti az Azure HDInsight-fürtöket az Azure CLI használatával. A fürtök típusai közé tartoznak a Apache Hadoop, a Spark, a HBase, a Storm, a Kafka, az interaktív lekérdezés és a ML-szolgáltatások.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: b17c5a2abc036c16ff3ce36b81428f9149e36b4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942859"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure HDInsight-fürtök kezelése az Azure CLI-vel

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Ismerje meg, hogyan kezelheti az Azure HDInsight-fürtöket az [Azure CLI](/cli/azure/) használatával. Az Azure parancssori felülete (CLI) a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Azure CLI. Ha még nem telepítette az Azure CLI-t, olvassa el a következő témakört: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) .

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Jelentkezzen be az Azure-előfizetésbe. Ha azt tervezi, hogy Azure Cloud Shell használ, válassza a **kipróbálás** lehetőséget a kód blokk jobb felső sarkában. Máskülönben adja meg az alábbi parancsot:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Fürtök listázása

Fürtök listázása [az az hdinsight List](/cli/azure/hdinsight#az-hdinsight-list) paranccsal. Szerkessze az alábbi parancsokat úgy, hogy lecseréli az `RESOURCE_GROUP_NAME` erőforráscsoport nevét, majd beírja a parancsokat:

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

Az [az hdinsight show](/cli/azure/hdinsight#az-hdinsight-show) paranccsal jelenítheti meg egy adott fürt információit. Az alábbi parancs szerkesztésével cserélje le a parancsot a `RESOURCE_GROUP_NAME` `CLUSTER_NAME` megfelelő információkra, majd írja be a következő parancsot:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Fürtök törlése

A megadott fürt törléséhez használja [az az hdinsight delete](/cli/azure/hdinsight#az-hdinsight-delete) paranccsal. Az alábbi parancs szerkesztésével cserélje le a parancsot a `RESOURCE_GROUP_NAME` `CLUSTER_NAME` megfelelő információkra, majd írja be a következő parancsot:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

A fürtöt a fürtöt tartalmazó erőforráscsoport törlésével is törölheti. Vegye figyelembe, hogy ez a csoport összes erőforrását törli, beleértve az alapértelmezett Storage-fiókot is.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Fürtök méretezése

Az [az hdinsight Resize](/cli/azure/hdinsight#az-hdinsight-resize) paranccsal méretezze át a megadott hdinsight-fürtöt a megadott méretre. Szerkessze az alábbi parancsot a helyére `RESOURCE_GROUP_NAME` , és `CLUSTER_NAME` a megfelelő információkkal. Cserélje le a `WORKERNODE_COUNT` értéket a fürthöz tartozó munkavégző csomópontok kívánt számára. A fürtök méretezésével kapcsolatos további információkért lásd: [HDInsight-fürtök](./hdinsight-scaling-best-practices.md)méretezése. Adja meg a parancsot:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan végezheti el a különböző HDInsight-fürtök felügyeleti feladatait. További tudnivalókért olvassa el a következő cikket:

* [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md)
* [HDInsight felügyelete Azure PowerShell használatával](hdinsight-administer-use-powershell.md)
* [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Az Azure CLI használatának első lépései](/cli/azure/get-started-with-azure-cli)
