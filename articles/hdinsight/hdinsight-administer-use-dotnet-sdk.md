---
title: Apache Hadoop-fürtök kezelése a HDInsight-ben .NET SDK-val – Azure
description: Ismerje meg, hogyan hajthat végre rendszergazdai feladatokat a HDInsight Apache Hadoop-fürtökhöz a HDInsight .NET SDK használatával.
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-csharp
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: e5f328d6be52732c30478eb49360efb13919a23e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872217"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Apache Hadoop-fürtök kezelése a HDInsight rendszerében a .NET SDK segítségével

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Ismerje meg, hogyan kezelheti a HDInsight-fürtöket a [HDINSIGHT.net SDK](/dotnet/api/overview/azure/hdinsight)-val.

**Előfeltételek**

A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Kapcsolódás az Azure HDInsight

A következő NuGet-csomagok szükségesek:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Az alábbi mintakód bemutatja, hogyan csatlakozhat az Azure-hoz, mielőtt az Azure-előfizetéshez tartozó HDInsight-fürtöket tudja felügyelni.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Ezt a programot a program futtatásakor kell megtekinteni.  Ha nem szeretné megtekinteni a kérdést, tekintse meg a [nem interaktív hitelesítéssel rendelkező .net HDInsight-alkalmazások létrehozása](hdinsight-create-non-interactive-authentication-dotnet-applications.md)című témakört.


## <a name="list-clusters"></a>Fürtök listázása

A következő kódrészlet felsorolja a fürtöket és néhány tulajdonságot:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Fürtök törlése

A következő kódrészlettel törölheti a fürtöket szinkron vagy aszinkron módon: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Fürtök méretezése

A fürt skálázási funkciója lehetővé teszi az Azure HDInsight-ben futó fürt által használt munkavégző csomópontok számának módosítását anélkül, hogy újra létre kellene hozni a fürtöt.

> [!NOTE]  
> Csak a 3.1.3-es vagy újabb verziójú HDInsight-fürtök támogatottak. Ha nem tudja biztosan a fürt verzióját, akkor ellenőrizze a tulajdonságok lapot.  Lásd: [fürtök listázása és megjelenítése](hdinsight-administer-use-portal-linux.md#showClusters).

Az adatcsomópontok számának a HDInsight által támogatott különböző típusú fürtökön való módosításának következményei:

* Apache Hadoop
  
    A Hadoop-fürtben futó munkavégző csomópontok száma zökkenőmentesen megnövelhető a függőben lévő vagy futó feladatok hatása nélkül. A művelet végrehajtása közben új feladatokat is el lehet küldeni. A skálázási művelet során fellépő hibák szabályosan kezelhetők, így a fürt mindig működőképes állapotban marad.
  
    Ha a Hadoop-fürtöket az adatcsomópontok számának csökkentésével csökkentették, a fürt egyes szolgáltatásai újraindulnak. Ez azt eredményezi, hogy az összes futó és függőben lévő feladat meghiúsul a skálázási művelet befejezésekor. A művelet befejezését követően azonban újra elküldheti a feladatokat.
* Apache HBase
  
    A futása közben zökkenőmentesen hozzáadhat vagy eltávolíthat csomópontokat a HBase-fürthöz. A regionális kiszolgálók a skálázási művelet befejezését követően néhány percen belül automatikusan egyensúlyban vannak. A regionális kiszolgálókat azonban manuálisan is kiegyensúlyozhatja, ha bejelentkezik a fürt átjárócsomóponthoz, és futtatja a következő parancsokat egy parancssori ablakból:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    A futása közben zökkenőmentesen hozzáadhat vagy eltávolíthat adatcsomópontokat a Storm-fürthöz. A skálázási művelet sikeres befejezése után azonban újra kell osztania a topológiát.
  
    Az újraelosztás kétféleképpen végezhető el:
  
  * Storm webes felhasználói felület
  * Parancssori felület (CLI) eszköz
    
    További részletekért tekintse meg az [Apache Storm dokumentációját](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .
    
    A Storm webes felhasználói felülete elérhető a HDInsight-fürtön:
    
    :::image type="content" source="./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="HDInsight Storm skálázási egyensúly":::
    
    Az alábbi példa bemutatja, hogyan használhatja a CLI-parancsot a Storm-topológia újraelosztásához:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

A következő kódrészlet bemutatja, hogyan méretezheti át a fürtöket szinkronban vagy aszinkron módon:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Hozzáférés engedélyezése/visszavonása

A HDInsight-fürtök a következő HTTP-webszolgáltatásokkal rendelkeznek (az összes szolgáltatás REST-végpontokkal rendelkezik):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Alapértelmezés szerint ezek a szolgáltatások hozzáférést kapnak. Visszavonhatja vagy megadhatja a hozzáférést. Visszavonás:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

A következő megadása:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> A hozzáférés megadásával/visszavonásával alaphelyzetbe állítja a fürt felhasználónevét és jelszavát.

Ezt a portálon keresztül is megteheti. Lásd: [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>HTTP-felhasználói hitelesítő adatok frissítése

Ez ugyanaz az eljárás, mint a HTTP-hozzáférés engedélyezése/visszavonása.  Ha a fürtön engedélyezve van a HTTP-hozzáférés, először vissza kell vonnia azt.  Ezután adja meg a hozzáférést az új HTTP-felhasználói hitelesítő adatokkal.

## <a name="find-the-default-storage-account"></a>Az alapértelmezett Storage-fiók keresése

A következő kódrészlet bemutatja, hogyan kérhető le egy fürt alapértelmezett Storage-fiókjának neve és alapértelmezett Storage-fiók kulcsa.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Feladatok elküldése

**MapReduce-feladatok elküldése**

Lásd: [MapReduce-minták futtatása a HDInsight-ben](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive feladatok elküldése** 

Lásd: [Apache Hive lekérdezések futtatása a .net SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)-val.

**Apache Sqoop-feladatok elküldése**

Lásd: [az Apache Sqoop használata a HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Apache Oozie-feladatok elküldése**

Lásd: az [Apache Oozie és a Hadoop használata munkafolyamatok definiálásához és futtatásához a HDInsight-ben](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure Blob Storage-ba

Lásd: [Adatok feltöltése a HDInsightba][hdinsight-upload-data].

## <a name="see-also"></a>Lásd még:

* [A HDInsight .NET SDK dokumentációja](/dotnet/api/overview/azure/hdinsight)
* [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md)
* [HDInsight felügyelete parancssori felületen keresztül][hdinsight-admin-cli]
* [HDInsight-fürtök létrehozása][hdinsight-provision]
* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [Ismerkedés az Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
