---
title: Rövid útmutató – Azure felügyelt példány létrehozása a parancssori felület használatával Apache Cassandra-fürthöz
description: Ezzel a rövid útmutatóval Azure-beli felügyelt példányt hozhat létre az Apache Cassandra-fürthöz az Azure CLI használatával.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: b719310a331044df363efcc6b79be323faf49247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562103"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Gyors útmutató: Azure felügyelt példány létrehozása az Apache Cassandra-fürthöz az Azure CLI használatával (előzetes verzió)

Az Apache Cassandra Azure felügyelt példánya automatizált üzembe helyezési és skálázási műveleteket biztosít a felügyelt nyílt forráskódú Apache Cassandra-adatközpontok számára. Ez a szolgáltatás segít felgyorsítani a hibrid forgatókönyveket, és csökkenti a folyamatos karbantartást.

> [!IMPORTANT]
> Az Apache Cassandra Azure felügyelt példánya jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid útmutató azt ismerteti, hogyan használható az Azure CLI-parancsok egy olyan fürt létrehozásához, amely az Azure felügyelt példányát használja az Apache Cassandra használatával. Azt is mutatja, hogy létrehoz egy adatközpontot, és a csomópontokat az adatközponton belülről felfelé vagy lefelé méretezheti.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) a saját üzemeltetésű vagy helyszíni környezethez való csatlakozással. A helyszíni környezetek Azure-hoz való csatlakoztatásával kapcsolatos további információkért tekintse meg a helyszíni [hálózat csatlakoztatása az Azure-hoz](/azure/architecture/reference-architectures/hybrid-networking/) című cikket.

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Ehhez a cikkhez az Azure CLI 2.17.1 vagy újabb verziójára van szükség. Ha Azure Cloud Shell használ, a legújabb verzió már telepítve van.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Felügyelt példány fürt létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

1. Az előfizetés-azonosító beállítása az Azure CLI-ben:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Ezután hozzon létre egy Virtual Network egy dedikált alhálózattal az erőforráscsoporthoz:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. Alkalmazzon speciális engedélyeket a felügyelt példány által igényelt Virtual Network és alhálózatra. Használja a `az role assignment create` parancsot, cserélje le a,, `<subscription ID>` `<resource group name>` `<VNet name>` és `<subnet name>` értéket a megfelelő értékekre:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > Az `assignee` `role` előző parancs és értékei rögzített értékek, pontosan a parancsban említett értékeket adja meg. Ha ezt nem teszi meg, a rendszer hibákat fog eredményezni a fürt létrehozásakor. Ha hibát tapasztal a parancs végrehajtásakor, előfordulhat, hogy nem rendelkezik a futtatásához szükséges engedélyekkel, forduljon a rendszergazdához.

1. Ezután hozza létre a fürtöt az újonnan létrehozott Virtual Network az az [Managed-Cassandra cluster Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) parancs használatával. Futtassa a következő parancsot a változó értékeként `delegatedManagementSubnetId` :

   > [!NOTE]
   > Az `delegatedManagementSubnetId` alábbi módon megadható változó értéke pontosan ugyanaz, mint a `--scope` fenti parancsban megadott érték:

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

1. Végül hozzon létre egy adatközpontot a fürthöz három csomóponttal az az [Managed-Cassandra Datacenter Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create) parancs használatával:

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

1. Ha létrehozta az adatközpontot, vagy vertikálisan szeretné méretezni az adatközpont csomópontjait, futtassa az az [Managed-Cassandra Datacenter Update](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) parancsot. Módosítsa a paraméter értékét `node-count` a kívánt értékre:

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

## <a name="connect-to-your-cluster"></a>Kapcsolódás a fürthöz

Az Apache Cassandra Azure felügyelt példánya nem hoz létre nyilvános IP-címmel rendelkező csomópontokat. Az újonnan létrehozott Cassandra-fürthöz való kapcsolódáshoz létre kell hoznia egy másik erőforrást a virtuális hálózaton belül. Ez az erőforrás lehet egy alkalmazás, vagy egy olyan virtuális gép, amelyen az Apache nyílt forráskódú lekérdezési eszköze [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) van telepítve. Az Ubuntu rendszerű virtuális gépek üzembe helyezéséhez [Resource Manager-sablon](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) is használható. A telepítés után az SSH használatával csatlakozzon a géphez, és telepítse a CQLSH az alábbi parancsokban látható módon:

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

Ha a Virtual Networkre vonatkozó engedélyek alkalmazása során hibát tapasztal, például *nem találja a felhasználó vagy az egyszerű szolgáltatásnév kifejezést a Graph adatbázisban a "e5007d2c-4b13-4a74-9b6a-605d99f03501"* értékre, akkor ugyanezt az engedélyt manuálisan is alkalmazhatja a Azure Portal. Az engedélyek a portálról való alkalmazásához lépjen a meglévő virtuális hálózat **hozzáférés-vezérlés (iam)** paneljére, és adjon hozzá egy szerepkör-hozzárendelést a "Azure Cosmos db" szerepkörhöz a "hálózati rendszergazda" szerepkörhöz. Ha két bejegyzés jelenik meg, amikor a "Azure Cosmos DB" kifejezésre keres rá, adja hozzá a bejegyzéseket a következő képen látható módon: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Engedélyek alkalmazása" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> A Azure Cosmos DB szerepkör-hozzárendelés csak telepítési célokra szolgál. Az Apache Cassandra által felügyelt Azure-példányok nem rendelkeznek háttérbeli függőségekkel Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az `az group delete` paranccsal eltávolíthatja az erőforráscsoportot, a felügyelt példányt és az összes kapcsolódó erőforrást:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure-beli felügyelt példányt az Apache Cassandra-fürthöz az Azure CLI használatával. Most már megkezdheti a fürttel való munkát:

> [!div class="nextstepaction"]
> [Felügyelt Apache Spark-fürt üzembe helyezése Azure Databricks](deploy-cluster-databricks.md)