---
title: 'Oktatóanyag: Apache Kafka REST proxyval rendelkező fürt létrehozása a HDInsight-ben az Azure CLI használatával'
description: Megtudhatja, hogyan hajthat végre Apache Kafka műveleteket egy Kafka REST-proxy használatával az Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ff11b8461b483f5a66df19bb1b108a1fe1168fb9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944031"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Oktatóanyag: Apache Kafka REST proxyval rendelkező fürt létrehozása a HDInsight-ben az Azure CLI használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Apache Kafka [Rest proxyt használó](./rest-proxy.md) fürtöt az Azure HDInsight az Azure parancssori felülettel (CLI). Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. Az Apache Kafka egy nyílt forráskódú, elosztott adatstreamelési platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkciókat kínál. A Kafka REST proxy lehetővé teszi, hogy egy [REST API](/rest/api/hdinsight-kafka-rest-proxy/) http-n keresztül kommunikáljon a Kafka-fürttel. Az Azure CLI a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

Az Apache Kafka API csak az ugyanazon virtuális hálózaton belüli erőforrások számára érhető el. A fürtöt közvetlenül az SSH használatával érheti el. Ha más szolgáltatásokat, hálózatokat vagy virtuális gépeket szeretne csatlakoztatni az Apache Kafkához, először létre kell hoznia egy virtuális hálózatot, majd létre kell hoznia a hálózaton belüli erőforrásokat. További információ: [kapcsolódás Apache Kafka virtuális hálózat használatával](./apache-kafka-connect-vpn-gateway.md).

Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * A Kafka REST proxy előfeltételei
> * Apache Kafka-fürt létrehozása az Azure CLI használatával

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Az Azure AD-ben regisztrált alkalmazások. A Kafka REST-proxyval való interakcióra írt ügyfélalkalmazások az alkalmazás AZONOSÍTÓját és titkos kulcsát fogják használni az Azure-ban való hitelesítéshez. További információ: [alkalmazás regisztrálása a Microsoft Identity platformmal](../../active-directory/develop/quickstart-register-app.md).

* Egy Azure AD-beli biztonsági csoport, amely tagja a regisztrált alkalmazásnak. Ezzel a biztonsági csoporttal szabályozhatja, hogy mely alkalmazások használhatják a REST-proxyt. Az Azure AD-csoportok létrehozásával kapcsolatos további információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Azure CLI. Győződjön meg arról, hogy legalább a 2.0.79 verziója van telepítve. Lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-fürt létrehozása

1. Jelentkezzen be az Azure-előfizetésbe.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Környezeti változók beállítása Az oktatóanyagban szereplő változók használata a Bashon alapul. Más környezetek esetében kisebb eltérésekre lesz szükség.

    |Változó | Leírás |
    |---|---|
    |resourceGroupName|Cserélje le a RESOURCEGROUPNAME nevet az új erőforráscsoport nevére.|
    |location|Cserélje le a helyet egy olyan régióra, amelyben a fürt létre lesz hozva. Az érvényes helyszínek listáját a következő paranccsal használhatja: `az account list-locations`|
    |clusterName|Cserélje le a CLUSTERNAME-t az új fürthöz tartozó globálisan egyedi névre.|
    |storageAccount|Cserélje le a STORAGEACCOUNTNAME nevet az új Storage-fiók nevére.|
    |httpPassword|Cserélje le a jelszót a fürt bejelentkezési azonosítójának jelszavára, **rendszergazdaként**.|
    |sshPassword|Cserélje le a jelszót jelszóval a Secure Shell username, **sshuser**.|
    |securityGroupName|Cserélje le a SECURITYGROUPNAME-t az ügyfél HRE biztonsági csoportjának nevére a Kafka Rest proxyhoz. A változó a `--kafka-client-group-name` (z) paraméteréhez lesz átadva `az-hdinsight-create` .|
    |securityGroupID|Cserélje le a SECURITYGROUPID-t az ügyfél HRE biztonsági csoportjának azonosítója a Kafka Rest proxyhoz. A változó a `--kafka-client-group-id` (z) paraméteréhez lesz átadva `az-hdinsight-create` .|
    |storageContainer|Ehhez az oktatóanyaghoz a fürt által használt Storage-tárolót kell hagyni. Ez a változó a fürt nevével lesz beállítva.|
    |workernodeCount|A fürtben lévő munkavégző csomópontok száma, ez az oktatóanyag a következő marad:. A magas rendelkezésre állás biztosítása érdekében a Kafka legalább 3 munkavégző csomópontot igényel|
    |clusterType|Az oktatóanyaghoz tartozó HDInsight-fürt típusa.|
    |clusterVersion|Ebben az oktatóanyagban a HDInsight-fürt verziója marad. A Kafka Rest proxyhoz legalább 4,0-es verziójú fürt szükséges.|
    |componentVersion|A Kafka verziója ebben az oktatóanyagban nem érhető el. A Kafka Rest proxyhoz a 2,1 minimális összetevő-verziója szükséges.|

    Módosítsa a változókat a kívánt értékekkel. Ezután írja be a CLI-parancsokat a környezeti változók beállításához.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Hozza létre az erőforráscsoportot](/cli/azure/group#az-group-create) az alábbi parancs beírásával:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Hozzon létre egy Azure Storage-fiókot](/cli/azure/storage/account#az-storage-account-create) az alábbi parancs beírásával:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Bontsa ki az elsődleges kulcsot](/cli/azure/storage/account/keys#az-storage-account-keys-list) az Azure Storage-fiókból, és tárolja egy változóban az alábbi parancs beírásával:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Hozzon létre egy Azure Storage-tárolót](/cli/azure/storage/container#az-storage-container-create) az alábbi parancs beírásával:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Hozza létre a HDInsight-fürtöt](/cli/azure/hdinsight#az-hdinsight-create). A parancs megadása előtt jegyezze fel a következő paramétereket:

    1. A Kafka-fürtökhöz szükséges paraméterek:

        |Paraméter | Leírás|
        |---|---|
        |– típus|Az értéknek **Kafka**-nek kell lennie.|
        |--workernode-adatlemezek/csomópont|A munkavégző csomópontok által használandó adatlemezek száma. A HDInsight Kafka csak adatlemezek esetén támogatott. Ez az oktatóanyag a **2** értéket használja.|

    1. A Kafka REST proxyhoz szükséges paraméterek:

        |Paraméter | Leírás|
        |---|---|
        |--Kafka-Management-node-size|A csomópont mérete Ez az oktatóanyag a **Standard_D4_v2** értéket használja.|
        |--Kafka-ügyfél-csoport azonosítója|Az ügyfél HRE biztonsági csoport azonosítója a Kafka Rest proxyhoz. Az értéket a **$securityGroupID** változó adja át.|
        |--Kafka-ügyfél-csoport neve|Az ügyfél HRE biztonsági csoportjának neve a Kafka Rest proxyhoz. Az értéket a **$securityGroupName** változó adja át.|
        |--Version|A HDInsight-fürt verziószámának legalább 4,0-nek kell lennie. Az értéket a **$clusterVersion** változó adja át.|
        |--összetevő-verzió|A Kafka verziójának legalább 2,1-nek kell lennie. Az értéket a **$componentVersion** változó adja át.|
    
        Ha a fürtöt REST proxy nélkül szeretné létrehozni, távolítsa el `--kafka-management-node-size` , `--kafka-client-group-id` és a `--kafka-client-group-name` `az hdinsight create` parancsból.

    1. Ha rendelkezik meglévő virtuális hálózattal, adja hozzá a paramétereket `--vnet-name` és az `--subnet` értékeket.

    Adja meg a következő parancsot a fürt létrehozásához:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    A fürt létrehozási folyamata több percet is igénybe vehet. Általában körülbelül 15.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

Az erőforrások eltávolításához adja meg az alábbi parancsok mindegyikét vagy egy részét:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen létrehozott egy Apache Kafka REST proxyval rendelkező fürtöt az Azure HDInsight az Azure CLI használatával, használja a Python-kódot a REST-proxyval való interakcióhoz:

> [!div class="nextstepaction"]
> [Minta alkalmazás létrehozása](./rest-proxy.md#client-application-sample)