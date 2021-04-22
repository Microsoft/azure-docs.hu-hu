---
title: Felügyelt Azure-példány kezelése Apache Cassandra-erőforrásokhoz az Azure CLI használatával
description: Ismerje meg az Apache Cassandrára vonatkozó felügyelt Azure-példány felügyeletének automatizálására használt gyakori parancsokat az Azure CLI használatával.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/15/2021
ms.author: thvankra
ms.openlocfilehash: ea28bf21424f0624b4f1bb5856a17672c1c7b106
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875448"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Felügyelt Azure-példány kezelése Apache Cassandra-erőforrásokhoz az Azure CLI (előzetes verzió) használatával

Ez a cikk az Apache Cassandra-fürtökhöz használt Felügyelt Azure-példány felügyeletének automatizálására használt általános parancsokat ismerteti az Azure CLI használatával.

> [!IMPORTANT]
> Az Azure Managed Instance for Apache Cassandra jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Ehhez a cikkhez az Azure CLI 2.17.1-es vagy újabb verziójára van szükség. Ha az alkalmazást Azure Cloud Shell, a legújabb verzió már telepítve van.
>
> Az Apache Cassandra-erőforrásokhoz használható felügyelt Azure-példányok kezelése nem nevezhető át, mivel ez Azure Resource Manager az erőforrás-URI-ket.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Felügyelt Azure-példány Apache Cassandra-fürtökhöz

A következő szakaszok bemutatják az Apache Cassandra-fürtökhöz való felügyelt Azure-példányok kezelését, beleértve a következőket:

* [Felügyeltpéldány-fürt létrehozása](#create-cluster)
* [Felügyeltpéldány-fürt törlése](#delete-cluster)
* [A fürt részleteinek lekérte](#get-cluster-details)
* [A fürtcsomópont állapotának lekért állapota](#get-cluster-status)
* [Fürtök listása erőforráscsoport szerint](#list-clusters-resource-group)
* [Fürtök felsorolása előfizetés-azonosító alapján](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Felügyeltpéldány-fürt létrehozása

Hozzon létre egy Felügyelt Azure-példányt az Apache [Cassandra-fürthöz az az managed-cassandra cluster create paranccsal:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create)

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

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Felügyeltpéldány-fürt törlése

Fürt törlése az [az managed-cassandra cluster delete paranccsal:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>A fürt részleteinek lekérte

Az [az managed-cassandra cluster show paranccsal lekért fürtadatok:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>A fürtcsomópont állapotának lekért állapota

A fürt részleteit az [az managed-cassandra cluster node-status paranccsal olvashatja](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status) be:

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>A fürtök felsorolása erőforráscsoport szerint

A fürtök listazása erőforráscsoport szerint az [az managed-cassandra cluster list paranccsal:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list)

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Fürtök felsorolása előfizetés-azonosító alapján

Lists clusters by subscription ID by using [the az managed-cassandra cluster list](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true) command:

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>A felügyelt példányok adatközpontjai

A következő szakaszok az Apache Cassandra-adatközpontokhoz elérhető felügyelt Azure-példányok kezelését mutatják be, beleértve a következőket:

* [Adatközpont létrehozása](#create-datacenter)
* [Adatközpont törlése](#delete-datacenter)
* [Adatközpont részleteinek lekérte](#get-datacenter-details)
* [Adatközpont frissítése vagy skálázása](#update-datacenter)
* [Adatközpontok lekérte egy fürtben](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Adatközpont létrehozása

Hozzon létre egy adatközpontot [az az managed-cassandra datacenter create paranccsal:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create)

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

Töröljön egy adatközpontot az [az managed-cassandra datacenter delete paranccsal:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Adatközpont részleteinek lekérte

Az adatközpont részleteit az [az managed-cassandra datacenter show](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show) paranccsal olvashatja be:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Adatközpont frissítése vagy skálázása

Az [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) paranccsal frissítheti vagy skálázhat egy adatközpontot (a csomópontszám értékének skálázása érdekében):

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

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Fürt adatközpontjainak leözése

A fürtben található adatközpontokat az [az managed-cassandra datacenter list paranccsal lehet lekérte:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Következő lépések

* [Felügyeltpéldány-fürt létrehozása a Azure Portal](create-cluster-portal.md)
* [Felügyelt fürt Apache Spark üzembe helyezése Azure Databricks](deploy-cluster-databricks.md)
