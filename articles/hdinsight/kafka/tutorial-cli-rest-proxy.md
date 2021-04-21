---
title: 'Oktatóanyag: Apache Kafka REST-proxyval kompatibilis fürt létrehozása a HDInsightban az Azure CLI használatával'
description: Megtudhatja, hogyan hajt Apache Kafka műveleteket a Kafka REST-proxyval a Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cfb9ff9e6b107c9da84b164a055453994fc29229
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786638"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Oktatóanyag: Apache Kafka REST-proxyval kompatibilis fürt létrehozása a HDInsightban az Azure CLI használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre Apache Kafka [REST-proxyval](./rest-proxy.md) kompatibilis fürtöt a Azure HDInsight Azure parancssori felület (CLI) használatával. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. Az Apache Kafka egy nyílt forráskódú, elosztott adatstreamelési platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkciókat kínál. A Kafka REST-proxy lehetővé teszi a Kafka-fürt egy HTTP-n REST API [való](/rest/api/hdinsight-kafka-rest-proxy/) használatát. Az Azure CLI a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

Az Apache Kafka API csak az ugyanazon virtuális hálózaton belüli erőforrások számára érhető el. A fürtöt SSH használatával közvetlenül is elérheti. Ha más szolgáltatásokat, hálózatokat vagy virtuális gépeket szeretne csatlakoztatni az Apache Kafkához, először létre kell hoznia egy virtuális hálózatot, majd létre kell hoznia a hálózaton belüli erőforrásokat. További információ: Csatlakozás virtuális [Apache Kafka használatával.](./apache-kafka-connect-vpn-gateway.md)

Ebben az oktatóanyagban a következővel fog ismerkedni:

> [!div class="checklist"]
> * A Kafka REST-proxy előfeltételei
> * Fürt létrehozása Apache Kafka Azure CLI használatával

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Az Azure AD-ban regisztrált alkalmazás. A Kafka REST-proxyval való kommunikációhoz írt ügyfélalkalmazások az alkalmazás azonosítóját és titkos kódját fogják használni az Azure-beli hitelesítéshez. További információ: [Alkalmazás regisztrálása a Microsoft identitásplatformján.](../../active-directory/develop/quickstart-register-app.md)

* Egy Azure AD biztonsági csoport, amely tagja a regisztrált alkalmazásnak. Ezzel a biztonsági csoporttal szabályozható, hogy mely alkalmazások kommunikálhatnak a REST-proxyval. További információ az Azure AD-csoportok létrehozásáról: Alapszintű csoport létrehozása és tagok hozzáadása [a Azure Active Directory.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

* Azure CLI. Győződjön meg arról, hogy legalább 2.0.79-es verzióval van. Lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-fürt létrehozása

1. Jelentkezzen be az Azure-előfizetésbe.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Környezeti változók beállítása. A változók használata ebben az oktatóanyagban a Bash-alapú. Más környezetekhez kisebb eltérésekre lesz szükség.

    |Változó | Leírás |
    |---|---|
    |resourceGroupName|Cserélje le a RESOURCEGROUPNAME nevet az új erőforráscsoport nevére.|
    |location|Cserélje le a LOCATION (HELY) helyére azt a régiót, ahol a fürt létre lesz hozva. Az érvényes helyek listáját az paranccsal `az account list-locations` használhatja|
    |clusterName|Cserélje le a CLUSTERNAME helyére az új fürt globálisan egyedi nevét.|
    |storageAccount|Cserélje le a STORAGEACCOUNTNAME helyére az új tárfiók nevét.|
    |httpPassword|Cserélje le a PASSWORD (JELSZÓ) helyére a fürtbe való bejelentkezés jelszavát **(admin).**|
    |sshPassword|Cserélje le a PASSWORD (JELSZÓ) helyére a Secure Shell-felhasználónév **(sshuser) jelszavát.**|
    |securityGroupName|Cserélje le a SECURITYGROUPNAME helyére az ügyfél AAD biztonsági csoportjának nevét a Kafka REST-proxynál. A változó a paraméterének lesz `--kafka-client-group-name` `az-hdinsight-create` átadva.|
    |securityGroupID (biztonságicsoport-azonosító)|Cserélje le a SECURITYGROUPID azonosítót a Kafka REST-proxy ügyféloldali AAD biztonságicsoport-azonosítójára. A változó a paraméterének lesz `--kafka-client-group-id` `az-hdinsight-create` átadva.|
    |storageContainer (tárolótároló)|A tároló, amit a fürt használni fog, hagyja meg a megfelelőt ebben az oktatóanyagban. Ez a változó a fürt nevével lesz beállítva.|
    |workernodeCount|A fürtben lévő munkavégző csomópontok száma, ebben az oktatóanyagban hagyja meg a megfelelőt. A magas rendelkezésre állás garantálása érdekében a Kafkának legalább 3 munkavégző csomópontra van szüksége|
    |clusterType (fürttípus)|A HDInsight-fürt típusa, ebben az oktatóanyagban hagyja meg a megfelelőt.|
    |clusterVersion (fürtverzió)|HDInsight-fürt verziója, ebben az oktatóanyagban hagyja meg a jelenlegit. A Kafka Rest Proxy használatához a fürt legalább 4.0-s verziója szükséges.|
    |componentVersion (összetevőverzió)|Kafka-verzió, hagyja meg a jelenlegit ebben az oktatóanyagban. A Kafka rest proxy használatához legalább 2.1-es összetevőverzió szükséges.|

    Frissítse a változókat a kívánt értékekkel. Ezután írja be a CLI-parancsokat a környezeti változók beállítására.

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

1. [Az erőforráscsoport létrehozásához](/cli/azure/group#az_group_create) írja be az alábbi parancsot:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Hozzon létre egy Azure Storage-fiókot](/cli/azure/storage/account#az_storage_account_create) az alábbi paranccsal:

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

1. [Az alábbi paranccsal bontsa](/cli/azure/storage/account/keys#az_storage_account_keys_list) ki az elsődleges kulcsot az Azure Storage-fiókból, és tárolja egy változóban:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Hozzon létre egy Azure Storage-tárolót](/cli/azure/storage/container#az_storage_container_create) az alábbi parancs beírásával:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Hozza létre a HDInsight-fürtöt.](/cli/azure/hdinsight#az_hdinsight_create) A parancs beírása előtt jegyezze fel a következő paramétereket:

    1. A Kafka-fürtök kötelező paraméterei:

        |Paraméter | Leírás|
        |---|---|
        |--type (típus)|Az értéknek **Kafka értékűnek kell lennie.**|
        |--workernode-data-disks-per-node|A munkavégző csomópontonként használni szükséges adatlemezek száma. A HDInsight Kafka csak adatlemezekkel támogatott. Ez az oktatóanyag a 2 értéket **használja.**|

    1. A Kafka REST-proxy kötelező paraméterei:

        |Paraméter | Leírás|
        |---|---|
        |--kafka-management-node-size|A csomópont mérete. Ez az oktatóanyag a következő értéket **Standard_D4_v2:**.|
        |--kafka-client-group-id|A Kafka Rest Proxy ügyfél AAD biztonságicsoport-azonosítója. Az érték a változóból lesz átkért **$securityGroupID.**|
        |--kafka-client-group-name|Az ügyfél AAD biztonsági csoportjának neve a Kafka REST-proxyhoz. Az értéket a következő változóból lehet **$securityGroupName.**|
        |--version|A HDInsight-fürt verziójának legalább 4.0-nak kell lennie. Az érték a változóból lesz átkért **$clusterVersion.**|
        |--component-version|A Kafka-verziónak legalább 2.1-esnek kell lennie. Az értéket a következő változóból lehet **$componentVersion.**|
    
        Ha REST-proxy nélkül szeretné létrehozni a fürtöt, távolítsa el a , és halmazt `--kafka-management-node-size` `--kafka-client-group-id` a `--kafka-client-group-name` `az hdinsight create` parancsból.

    1. Ha már rendelkezik meglévő virtuális hálózattal, adja hozzá a és `--vnet-name` `--subnet` paramétereket, valamint azok értékeit.

    A fürt létrehozásához írja be a következő parancsot:

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

    A fürt létrehozási folyamata több percig is eltarthat. Általában körülbelül 15.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így a nem használt fürtök biztonságosan törölhetők. A HDInsight-fürtökért akkor is díjat kell fizetnie, ha nincs használatban. Mivel a fürt díja sokszor több, mint a tárolás díja, gazdasági szempontból logikus a nem használt fürtök törlése.

Az erőforrások eltávolításához adja meg az összes vagy néhány alábbi parancsot:

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

Most, hogy sikeresen létrehozott egy Apache Kafka REST-proxyval kompatibilis fürtöt a Azure HDInsight-ban az Azure CLI használatával, a Python-kóddal kommunikálhat a REST-proxyval:

> [!div class="nextstepaction"]
> [Mintaalkalmazás létrehozása](./rest-proxy.md#client-application-sample)