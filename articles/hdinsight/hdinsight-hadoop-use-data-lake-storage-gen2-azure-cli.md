---
title: Azure HDInsight létrehozása – Azure Data Lake Storage Gen2 – Azure CLI
description: Ismerje meg, hogyan használhatja a Azure Data Lake Storage Gen2 Azure HDInsight-fürtökkel az Azure CLI használatával.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: bbc1cd27d5c16eddd3aaad748c34445e5017e209
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945493"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Fürt létrehozása Data Lake Storage Gen2 az Azure CLI használatával

A Data Lake Storage Gen2 for Storage szolgáltatást használó HDInsight-fürt létrehozásához kövesse az alábbi lépéseket.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a Azure Data Lake Storage Gen2, tekintse meg az [Áttekintés szakaszt](hdinsight-hadoop-use-data-lake-storage-gen2.md). 
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A CLI-szkriptek futtatásához a következő három lehetőség közül választhat:
    - Használja a Azure Portal [Azure Cloud shellt](../cloud-shell/overview.md) (lásd a következő szakaszt).
    - A beágyazott Azure Cloud Shell az egyes kódrészletek jobb felső sarkában található "kipróbálás" gomb segítségével érheti el.
    - [Telepítse az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli) (2.0.13 vagy újabb), ha helyi CLI-konzolt szeretne használni. Jelentkezzen be az Azure-ba `az login` egy olyan fiókkal, amely ahhoz az Azure-előfizetéshez van társítva, amelybe a felhasználó által hozzárendelt felügyelt identitást szeretné telepíteni. Azure CLI-vel. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[Letöltheti a minta sablonfájl fájlját](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) , és [letöltheti a minta paramétereinek fájlját](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). A sablon és az Azure CLI-kódrészlet használata előtt cserélje le a következő helyőrzőket a megfelelő értékekre:

| Helyőrző | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | Az Azure-előfizetés azonosítója |
| `<RESOURCEGROUPNAME>` | Az az erőforráscsoport, amelyben létre szeretné hozni az új fürtöt és a Storage-fiókot. |
| `<MANAGEDIDENTITYNAME>` | Annak a felügyelt identitásnak a neve, amely a Storage-fiókra vonatkozó engedélyeket kap Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Az új Storage-fiók, amelynek a Azure Data Lake Storage Gen2 létre lesz hozva. |
| `<FILESYSTEMNAME>`  | Annak a fájlrendszernek a neve, amelyet a fürtnek használnia kell a Storage-fiókban. |
| `<CLUSTERNAME>` | A HDInsight-fürt neve. |
| `<PASSWORD>` | Az SSH-val és a Ambari-irányítópulttal a fürtbe való bejelentkezéshez választott jelszava. |

Az alábbi kódrészlet a következő kezdeti lépéseket hajtja végre:

1. Bejelentkezik az Azure-fiókjába.
1. Beállítja azt az aktív előfizetést, amelyben a létrehozási műveletek lesznek végrehajtva.
1. Létrehoz egy új erőforráscsoportot az új központi telepítési tevékenységekhez.
1. Felhasználó által hozzárendelt felügyelt identitást hoz létre.
1. Bővítmény hozzáadását az Azure CLI-hez a Data Lake Storage Gen2 szolgáltatásainak használatához.
1. Új Storage-fiókot hoz létre Data Lake Storage Gen2 a jelző használatával `--hierarchical-namespace true` .

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Ezután jelentkezzen be a portálra. Adja hozzá az új felhasználó által hozzárendelt felügyelt identitást a Storage-beli **blob adatközreműködői** szerepkörhöz a Storage-fiókban. Ez a lépés a [Azure Portal használata](hdinsight-hadoop-use-data-lake-storage-gen2.md)című témakör 3. lépésében található.

 > [!IMPORTANT]
 > Győződjön meg arról, hogy a Storage-fiókja rendelkezik a felhasználó által hozzárendelt identitással a **Storage blob adatközreműködői** szerepkörének engedélyeivel, ellenkező esetben a fürt létrehozása sikertelen lesz.

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

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

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Következő lépések

Sikeresen létrehozott egy HDInsight-fürtöt. Most megtudhatja, hogyan dolgozhat a fürttel.

### <a name="apache-spark-clusters"></a>Fürtök Apache Spark

* [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)
* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](spark/apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Fürtök Apache Hadoop

* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase a HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-alkalmazások fejlesztése az Apache HBase a HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
