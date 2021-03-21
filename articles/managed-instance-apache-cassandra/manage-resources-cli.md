---
title: Azure felügyelt példány kezelése Apache Cassandra-erőforrásokhoz az Azure CLI használatával
description: Ismerkedjen meg az Azure felügyelt példányának az Azure CLI használatával való felügyeletét automatizáló általános parancsokkal.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/15/2021
ms.author: thvankra
ms.openlocfilehash: 3e44625d23a302c58ea065a4fc3ecec5605e60b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564524"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Azure felügyelt példány kezelése Apache Cassandra-erőforrásokhoz az Azure CLI (előzetes verzió) használatával

Ez a cikk általános parancsokat tartalmaz, amelyekkel automatizálható az Azure felügyelt példányok felügyelete az Apache Cassandra-fürtökön az Azure CLI használatával.

> [!IMPORTANT]
> Az Apache Cassandra Azure felügyelt példánya jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Ehhez a cikkhez az Azure CLI 2.17.1 vagy újabb verziójára van szükség. Ha Azure Cloud Shell használ, a legújabb verzió már telepítve van.
>
> Az Azure felügyelt példányának kezelése Apache Cassandra-erőforrásokhoz nem nevezhető át, mert ez megsérti, hogyan működik a Azure Resource Manager erőforrás-URI-k.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Azure felügyelt példány az Apache Cassandra-fürtökhöz

Az alábbi szakaszban bemutatjuk, hogyan kezelhető az Azure felügyelt példánya az Apache Cassandra-fürtökhöz, beleértve a következőket:

* [Felügyelt példány fürt létrehozása](#create-cluster)
* [Felügyelt példány fürt törlése](#delete-cluster)
* [A fürt részleteinek beolvasása](#get-cluster-details)
* [Fürtcsomópont állapotának beolvasása](#get-cluster-status)
* [Fürtök listázása erőforráscsoport szerint](#list-clusters-resource-group)
* [Fürtök listázása előfizetés-azonosító alapján](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Felügyelt példány fürt létrehozása

Azure-beli felügyelt példány létrehozása az Apache Cassandra-fürthöz az az [Managed-Cassandra cluster Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) parancs használatával:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Felügyelt példány fürt törlése

Fürt törlése az az [Managed-Cassandra cluster delete](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_delete) parancs használatával:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>A fürt részleteinek beolvasása

A fürt adatainak beolvasása az az [Managed-Cassandra cluster show](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_show) parancs használatával:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Fürtcsomópont állapotának beolvasása

A fürt adatainak beolvasása az az [Managed-Cassandra cluster node-status](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_node_status) parancs használatával:

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Fürtök listázása erőforráscsoport szerint

Fürtök listázása erőforráscsoport szerint az az [Managed-Cassandra cluster List](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_list) parancs használatával:

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Fürtök listázása előfizetés-azonosító alapján

Fürtök listázása előfizetés-azonosító alapján az az [Managed-Cassandra cluster List](/cli/azure/ext/cosmosdb-preview/managed-cassandra?view=azure-cli-latest&preserve-view=true) parancs használatával:

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>A felügyelt példány adatközpontja

Az alábbi szakaszban bemutatjuk, hogyan kezelhető az Azure felügyelt példánya az Apache Cassandra-adatközpontokhoz, beleértve a következőket:

* [Adatközpont létrehozása](#create-datacenter)
* [Adatközpont törlése](#delete-datacenter)
* [Adatközpont részleteinek beolvasása](#get-datacenter-details)
* [Adatközpont frissítése vagy méretezése](#update-datacenter)
* [Adatközpontok beolvasása fürtben](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Adatközpont létrehozása

Hozzon létre egy adatközpontot az az [Managed-Cassandra Datacenter Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create) parancs használatával:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Adatközpont törlése

Az az [Managed-Cassandra Datacenter delete](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_delete) paranccsal törölhet egy adatközpontot:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Adatközpont részleteinek beolvasása

Adatközpont részleteinek beolvasása az az [Managed-Cassandra Datacenter show](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_show) parancs használatával:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Adatközpont frissítése vagy méretezése

Adatközpont frissítése vagy skálázása (a Change nodeCount értékének méretezéséhez) az az [Managed-Cassandra Datacenter Update](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) parancs használatával:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Adatközpontok beszerzése egy fürtben

Adatközpontok beszerzése egy fürtben az az [Managed-Cassandra Datacenter List](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_list) parancs használatával:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Következő lépések

* [Felügyelt példány-fürt létrehozása a Azure Portal](create-cluster-portal.md)
* [Felügyelt Apache Spark-fürt üzembe helyezése Azure Databricks](deploy-cluster-databricks.md)
