---
title: Rövid útmutató – Felügyelt Azure-példány létrehozása Apache Cassandra-fürthöz a CLI használatával
description: Ebből a rövid útmutatóból egy felügyelt Azure-példányt hozhat létre az Apache Cassandra-fürthöz az Azure CLI használatával.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: 56fe69ad7f56d62c9f61738448ea0276fee47063
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862524"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Rövid útmutató: Felügyelt Azure-példány létrehozása Apache Cassandra-fürthöz az Azure CLI (előzetes verzió) használatával

Az Apache Cassandrára használható Felügyelt Azure-példány automatizált üzembe helyezési és méretezési műveleteket biztosít a felügyelt nyílt forráskódú Apache Cassandra-adatközpontokhoz. Ez a szolgáltatás segít felgyorsítani a hibrid forgatókönyveket, és csökkenteni a folyamatos karbantartást.

> [!IMPORTANT]
> Az Azure Managed Instance for Apache Cassandra jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid útmutató azt mutatja be, hogyan használhatók az Azure CLI-parancsok fürtök létrehozására az Apache Cassandrára vonatkozó felügyelt Azure-példányokkal. Azt is bemutatja, hogyan lehet adatközpontot létrehozni, és felfelé vagy lefelé skálázni a csomópontokat az adatközponton belül.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Az Azure Virtual Network](../virtual-network/virtual-networks-overview.md) kapcsolatot biztosít a saját vagy a saját környezetéhez. A helyszíni környezetek Azure-hoz való csatlakoztatásával kapcsolatos további információkért tekintse meg a Helyszíni hálózat csatlakoztatása az [Azure-hoz cikket.](/azure/architecture/reference-architectures/hybrid-networking/)

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Ehhez a cikkhez az Azure CLI 2.17.1-es vagy újabb verziójára van szükség. Ha az alkalmazást Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Felügyeltpéldány-fürt létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

1. Előfizetés-azonosító beállítása az Azure CLI-ban:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Ezután hozzon létre egy Virtual Network egy dedikált alhálózattal az erőforráscsoportban:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```
    > [!NOTE]
    > Az Apache Cassandrához használható felügyelt Azure-példány üzembe helyezéséhez internet-hozzáférés szükséges. Az üzembe helyezés meghiúsul olyan környezetekben, ahol az internet-hozzáférés korlátozott. Győződjön meg arról, hogy nem blokkolja a virtuális hálózatban a következő, a felügyelt Cassandra megfelelő működését nélkülözhetetlen Azure-szolgáltatásokhoz való hozzáférést:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Azure Monitoring
    > - Azure Active Directory
    > - Azure Security

1. Alkalmaz néhány speciális engedélyt a Virtual Network, amelyekre a felügyelt példánynak szüksége van. Használja a parancsot, és cserélje le a `az role assignment create` `<subscription ID>` , és `<resource group name>` `<VNet name>` értékeket a megfelelő értékekre:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Az előző parancs és értékei rögzített értékek, ezeket az értékeket pontosan a `assignee` `role` parancsban említettek szerint adja meg. Ha ezt nem teszi meg, az hibákhoz vezet a fürt létrehozásakor. Ha hibába ütközik a parancs végrehajtásakor, előfordulhat, hogy nem rendelkezik a futtatáshoz szükséges engedélyekkel, forduljon a rendszergazdához az engedélyekért.

1. Ezután hozza létre a fürtöt az újonnan létrehozott Virtual Network [az az managed-cassandra cluster create paranccsal.](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) Futtassa a következő parancsot a változó `delegatedManagementSubnetId` értékeként:

   > [!NOTE]
   > Az alább megadott változó értéke pontosan megegyezik a fenti parancsban megadott `delegatedManagementSubnetId` `--scope` értékkel:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Végül hozzon létre egy adatközpontot a fürthöz három csomóponttal az [az managed-cassandra datacenter create paranccsal:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create)

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Ha az adatközpont létrehozása után fel- vagy le szeretné skálázni az adatközpont csomópontjainak számát, futtassa [az az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) parancsot. Módosítsa a `node-count` paraméter értékét a kívánt értékre:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Csatlakozás a fürthöz

Az Apache Cassandrhoz használható felügyelt Azure-példány nem hoz létre nyilvános IP-címekkel kapcsolatos csomópontokat. Az újonnan létrehozott Cassandra-fürthöz való csatlakozáshoz létre kell hoznia egy másik erőforrást a virtuális hálózaton belül. Ez az erőforrás lehet egy alkalmazás, vagy egy virtuális gép, amelybe telepítve van az Apache nyílt forráskódú [CQLSH lekérdezési](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) eszköze. Ubuntu virtuális [Resource Manager üzembe helyezéséhez](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) használhat egy virtuálisgép-sablont. Az üzembe helyezés után az SSH használatával csatlakozzon a géphez, és telepítse a CQLSH-t az alábbi parancsokban látható módon:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha hibába ütközik az Virtual Network-engedélyek alkalmazásakor, például Nem található felhasználó vagy szolgáltatásnév a gráfadatbázisban az *"e5007d2c-4b13-4a74-9b6a-605d99f03501"* gráfadatbázisban, ugyanezt az engedélyt manuálisan is alkalmazhatja a Azure Portal. Az engedélyek portálról való alkalmazásához válassza a meglévő virtuális hálózat Hozzáférés-vezérlés **(IAM)** panelét, és adjon hozzá egy "Azure Cosmos DB" szerepkör-hozzárendelést a "Hálózati rendszergazda" szerepkörhöz. Ha két bejegyzés jelenik meg a "Azure Cosmos DB" keresésekor, adja hozzá mindkét bejegyzést az alábbi képen látható módon: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Engedélyek alkalmazása" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> A Azure Cosmos DB szerepkör-hozzárendelés csak telepítési célokra szolgál. Az Apache Cassandrára felügyelt Azure Managed Instanced nem rendelkezik háttérbeli függőségekkel a Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az paranccsal eltávolíthatja az erőforráscsoportot, a felügyelt példányt és az `az group delete` összes kapcsolódó erőforrást:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre felügyelt Azure-példányt az Apache Cassandra-fürthöz az Azure CLI használatával. Most már elkezdhet dolgozni a fürtön:

> [!div class="nextstepaction"]
> [Felügyelt fürt Apache Spark üzembe helyezése Azure Databricks](deploy-cluster-databricks.md)