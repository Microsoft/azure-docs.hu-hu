---
title: Apache Hadoop-fürtök kezelése a PowerShell-lel – Azure HDInsight
description: Megtudhatja, hogyan hajthat végre felügyeleti feladatokat a HDInsight Apache Hadoop-fürtökön a Azure PowerShell használatával.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: c8581c2288eacb47fb9475a3b0a584434c8604f6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942813"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Apache Hadoop-fürtök kezelése a HDInsight-ben Azure PowerShell használatával

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

A Azure PowerShell az Azure-beli számítási feladatok üzembe helyezésének és kezelésének vezérlésére és automatizálására használható. Ebből a cikkből megtudhatja, hogyan kezelheti [Apache Hadoop](https://hadoop.apache.org/) -fürtöket az Azure HDInsight az Azure PowerShell az modul használatával. A HDInsight PowerShell-parancsmagok listájáért lásd az az [. HDInsight referenciát](/powershell/module/az.hdinsight).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell az [modul](/powershell/azure/) telepítve van.

## <a name="create-clusters"></a>Fürtök létrehozása

Lásd: [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure PowerShell használatával](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Fürtök listázása

Az alábbi paranccsal listázhatja az aktuális előfizetés összes fürtjét:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Fürt megjelenítése

A következő parancs használatával jelenítheti meg egy adott fürt részleteit az aktuális előfizetésben:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Fürtök törlése

Fürt törléséhez használja a következő parancsot:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

A fürtöt a fürtöt tartalmazó erőforráscsoport eltávolításával is törölheti. Egy erőforráscsoport törlése törli a csoport összes erőforrását, beleértve az alapértelmezett Storage-fiókot is.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Fürtök méretezése

A fürt skálázási funkciója lehetővé teszi az Azure HDInsight-ben futó fürt által használt munkavégző csomópontok számának módosítását anélkül, hogy újra létre kellene hozni a fürtöt. A Hadoop-fürt méretének Azure PowerShell használatával történő módosításához futtassa a következő parancsot egy ügyfélszámítógépről:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 A fürtök méretezésével kapcsolatos további információkért lásd: [HDInsight-fürtök](./hdinsight-scaling-best-practices.md)méretezése.

## <a name="update-http-user-credentials"></a>HTTP-felhasználói hitelesítő adatok frissítése

A [set-AzHDInsightGatewayCredential](/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) az Azure HDInsight-fürt ÁTJÁRÓjának http-hitelesítő adatait állítja be.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Az alapértelmezett Storage-fiók keresése

A következő PowerShell-szkript bemutatja, hogyan kérheti le az alapértelmezett Storage-fiók nevét és a kapcsolódó információkat:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Az erőforráscsoport megkeresése

A Resource Manager módban minden HDInsight-fürt egy Azure-erőforráscsoporthoz tartozik.  Az erőforráscsoport megkeresése:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Feladatok elküldése

**MapReduce-feladatok elküldése**

Lásd: [a HDInsight-ben található MapReduce-példák futtatása](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive feladatok elküldése**

Lásd: [Apache Hive lekérdezések futtatása a PowerShell használatával](hadoop/apache-hadoop-use-hive-powershell.md).

**Apache Sqoop-feladatok elküldése**

Lásd: [az Apache Sqoop használata a HDInsight](hadoop/hdinsight-use-sqoop.md).

**Apache Oozie-feladatok elküldése**

Lásd: az [Apache Oozie és a Apache Hadoop használata munkafolyamatok definiálásához és futtatásához a HDInsight-ben](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure Blob Storage-ba

Lásd: [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md).

## <a name="see-also"></a>Lásd még:

* [Az az. HDInsight parancsmagok](/powershell/module/az.hdinsight/#hdinsight)
* [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md)
* [HDInsight felügyelete parancssori felületen keresztül](hdinsight-administer-use-command-line.md)
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop feladatok programozott beküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)