---
title: Gyors útmutató – hibrid fürt konfigurálása az Azure felügyelt példányával az Apache Cassandra-vel
description: Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy hibrid fürtöt az Azure felügyelt példányával az Apache Cassandra használatával.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 11daa548e90aa1906ba87e081fa1e0be6fe6aff8
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430768"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Gyors útmutató: hibrid fürt konfigurálása Azure felügyelt példánnyal az Apache Cassandra (előzetes verzió) szolgáltatáshoz

Az Apache Cassandra Azure felügyelt példánya automatizált üzembe helyezési és skálázási műveleteket biztosít a felügyelt nyílt forráskódú Apache Cassandra-adatközpontok számára. Ez a szolgáltatás segít felgyorsítani a hibrid forgatókönyveket, és csökkenti a folyamatos karbantartást.

> [!IMPORTANT]
> Az Apache Cassandra Azure felügyelt példánya jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy hibrid fürtöt az Azure CLI-parancsok használatával. Ha a meglévő adatközpontok egy helyszíni vagy saját üzemeltetésű környezetben vannak, az Azure felügyelt példányát használhatja az Apache Cassandra használatával más adatközpontok hozzáadásához a fürthöz, és karbantarthatja azokat.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Ehhez a cikkhez az Azure CLI 2.12.1 vagy újabb verziójára van szükség. Ha Azure Cloud Shell használ, a legújabb verzió már telepítve van.

* Az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) a saját üzemeltetésű vagy helyszíni környezethez való csatlakozással. A helyszíni környezetek Azure-hoz való csatlakoztatásával kapcsolatos további információkért tekintse meg a helyszíni [hálózat csatlakoztatása az Azure-hoz](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) című cikket.

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Hibrid fürt konfigurálása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és navigáljon a Virtual Network-erőforráshoz.

1. Nyissa meg az **alhálózatok** lapot, és hozzon létre egy új alhálózatot. Ha többet szeretne megtudni az **alhálózat hozzáadása** űrlap mezőiről, tekintse meg a [Virtual Network](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) cikket:

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Adjon hozzá egy új alhálózatot a Virtual Networkhoz." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

1. Most alkalmazzuk a VNet és az alhálózatra vonatkozó speciális engedélyeket, amelyeket a Cassandra felügyelt példány igényel az Azure CLI használatával. Használja a `az role assignment create` parancsot, cserélje le a,, `<subscription ID>` `<resource group name>` `<VNet name>` és `<subnet name>` értéket a megfelelő értékekre:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > Az `assignee` `role` előző parancs és értékei rögzített szolgáltatási elv és szerepkör-azonosítók.

1. Ezután konfigurálni fogjuk a hibrid fürt erőforrásait. Mivel már rendelkezik fürttel, a fürt neve csak logikai erőforrás lesz a meglévő fürt nevének azonosításához. Ügyeljen arra, hogy a `clusterName` következő parancsfájlban definiált és változók esetén a meglévő fürt nevét használja `clusterNameOverride` .

   Szüksége lesz a vetőmag-csomópontokra, a nyilvános Ügyféltanúsítványok használatára is (ha a Cassandra-végponton nyilvános/titkos kulcsot konfigurált), és a meglévő fürt pletykák-tanúsítványait. A változó megadásához a fent másolt erőforrás-azonosítót is használni kell `delegatedManagementSubnetId` .

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='<Resource ID>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > Tudnia kell, hogy a meglévő nyilvános és/vagy pletyka-tanúsítványok hol vannak tárolva. Ha bizonytalan, akkor futtatnia kell `keytool -list -keystore <keystore-path> -rfc -storepass <password>` a tanúsítványokat a tanúsítványok kinyomtatásához. 

1. A fürterőforrás létrehozása után futtassa a következő parancsot a fürt telepítési részleteinek beszerzéséhez:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Az előző parancs a felügyelt példány környezetére vonatkozó adatokat adja vissza. Szüksége lesz a Gossip-tanúsítványokra, hogy azok a meglévő adatközpont csomópontjaira is telepíthetők legyenek. Az alábbi képernyőfelvételen az előző parancs kimenete és a tanúsítványok formátuma látható:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="A tanúsítvány adatainak beolvasása a fürtből." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Ezután hozzon létre egy új adatközpontot a hibrid fürtben. Ügyeljen arra, hogy a változó értékeit a fürt részleteivel helyettesítse:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource ID>'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedSubnetId \
       --node-count 9 
   ```

1. Most, hogy létrejött az új adatközpont, futtassa az adatközpont megjelenítése parancsot a részletek megtekintéséhez:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Az előző parancs kimenete az új adatközpont vetőmag-csomópontjai. Adja hozzá az új adatközponthoz tartozó vetőmag-csomópontokat a meglévő adatközpont konfigurációjához a *Cassandra. YAML* fájlban. És telepítse a felügyelt példány pletykák tanúsítványait, amelyeket korábban gyűjtött:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Adatközpont részleteinek beolvasása." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Ha további adatközpontokat szeretne hozzáadni, megismételheti a fenti lépéseket, de csak a magok csomópontjaira van szükség. 

1. Végül a következő CQL-lekérdezéssel frissítse a replikációs stratégiát az egyes webhelyeken, hogy az összes adatközpontot a fürtön belül is tartalmazza:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   Emellett frissítenie kell a jelszó táblákat is:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem kívánja tovább használni ezt a felügyelt példány-fürtöt, törölje a következő lépésekkel:

1. Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget.
1. A listából válassza ki az ehhez a rövid útmutatóhoz létrehozott erőforráscsoportot.
1. Az erőforráscsoport **Áttekintés** paneljén válassza az **erőforráscsoport törlése** elemet.
3. A következő ablakban adja meg a törölni kívánt erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre hibrid fürtöt az Azure CLI-vel és az Azure felügyelt példányával az Apache Cassandra használatával. Most már megkezdheti a fürttel való munkát.

> [!div class="nextstepaction"]
> [Azure felügyelt példány kezelése Apache Cassandra-erőforrásokhoz az Azure CLI használatával](manage-resources-cli.md)