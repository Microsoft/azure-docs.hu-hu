---
title: Migrálás a HDInsight Azure Resource Manager eszközeire
description: Migrálás a HDInsight-fürtök Azure Resource Manager fejlesztői eszközeire
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-azurecli
ms.topic: how-to
ms.date: 02/21/2018
ms.openlocfilehash: 2ff62f4feba44a1c706ab85db1be3f7f654e6135
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945771"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrálás Azure Resource Manager-alapú fejlesztői eszközökre HDInsight-fürtökhöz

A HDInsight elavult Azure Service Manager (ASM) alapú eszközök HDInsight. Ha Azure PowerShellt, Azure klasszikus CLI-t vagy a HDInsight .NET SDK-t használja a HDInsight-fürtökkel való együttműködéshez, javasoljuk, hogy használja a PowerShell, a CLI és a .NET SDK Azure Resource Manager-verzióit. Ez a cikk az új Resource Manager-alapú megközelítésre mutató áttelepítési lehetőségeket ismerteti. Ha szükséges, ez a dokumentum kiemeli az ASM és a Resource Manager HDInsight közötti különbségeket.

> [!IMPORTANT]  
> Az ASM-alapú PowerShell-, CLI-és .NET SDK-támogatás a 2017-es **január 1-** én megszűnik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>A klasszikus Azure CLI áttelepítése Azure Resource Managerre

> [!IMPORTANT]  
> Az Azure CLI nem nyújt támogatást a HDInsight-fürtök használatához. A klasszikus Azure CLI-t továbbra is használhatja a HDInsight, azonban a klasszikus Azure CLI elavult.

A HDInsight Azure-beli klasszikus CLI-n keresztüli használatának alapszintű parancsai a következők:

* `azure hdinsight cluster create` -új HDInsight-fürt létrehozása
* `azure hdinsight cluster delete` -meglévő HDInsight-fürt törlése
* `azure hdinsight cluster show` – meglévő fürt adatainak megjelenítése
* `azure hdinsight cluster list` -Az Azure-előfizetéshez tartozó HDInsight-fürtök listája

A `-h` kapcsoló használatával ellenőrizze az egyes parancsok számára elérhető paramétereket és kapcsolókat.

### <a name="new-commands"></a>Új parancsok
A Azure Resource Manager elérhető új parancsok a következők:

* `azure hdinsight cluster resize` – dinamikusan módosítja a fürt munkavégző csomópontjainak számát.
* `azure hdinsight cluster enable-http-access` – engedélyezi a HTTPs-hozzáférést a fürthöz (alapértelmezés szerint)
* `azure hdinsight cluster disable-http-access` – letiltja a fürthöz való HTTPs-hozzáférést
* `azure hdinsight script-action` -parancsokat biztosít a parancsfájlok műveleteinek létrehozásához/kezeléséhez a fürtön
* `azure hdinsight config` – parancsokat biztosít egy konfigurációs fájl létrehozásához, amely a paranccsal használható a `hdinsight cluster create` konfigurációs információk megadásához.

### <a name="deprecated-commands"></a>Elavult parancsok
Ha a `azure hdinsight job` parancsokkal feladatokat küld a HDInsight-fürthöz, ezek a parancsok a Resource Manager parancsain keresztül nem érhetők el. Ha programozott módon kell elküldeni a feladatokat a parancsfájlok HDInsight, használja inkább a HDInsight által biztosított REST API-kat. A feladatok REST API-kkal történő elküldésével kapcsolatos további információkért tekintse meg a következő dokumentumokat.

* [MapReduce-feladatok futtatása a HDInsight-Hadoop a cURL használatával](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Apache Hive-lekérdezések futtatása a HDInsight Apache Hadoop a cURL használatával](hadoop/apache-hadoop-use-hive-curl.md)


Az Apache Hadoop MapReduce, Apache Hive és Apache Pig interaktív módon történő futtatásának egyéb módjaival kapcsolatban lásd: a [MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)-on, a Hadoop használata a-on Apache Hive, a HDInsight-on való [Apache Hadoop](hadoop/hdinsight-use-hive.md)használata, valamint az [Apache Pig használata a HDInsight](./index.yml)-ben Apache Hadoop.

### <a name="examples"></a>Példák
**Fürt létrehozása**

* Régi parancs (ASM) – `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Új parancs – `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Fürt törlése**

* Régi parancs (ASM) – `azure hdinsight cluster delete myhdicluster`
* Új parancs – `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Fürtök listázása**

* Régi parancs (ASM) – `azure hdinsight cluster list`
* Új parancs – `azure hdinsight cluster list`

> [!NOTE]  
> A List parancsnál a használatával az erőforráscsoport `-g` csak a megadott erőforráscsoporthoz tartozó fürtöket fogja visszaadni.

**Fürt adatainak megjelenítése**

* Régi parancs (ASM) – `azure hdinsight cluster show myhdicluster`
* Új parancs – `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Azure PowerShell migrálása a Azure Resource Managerba
A Azure Resource Manager módban Azure PowerShell általános információi a [Azure PowerShell Azure Resource Manager használatával](../azure-resource-manager/management/manage-resources-powershell.md)találhatók.

A Azure PowerShell Resource Manager-parancsmagok az ASM-parancsmagokkal egymás mellett telepíthetők. A két mód parancsmagja megkülönböztethető a nevük alapján.  A Resource Manager módban a *AzHDInsight* a régebbi Azure Service Management módban a *AzureHDInsight* összehasonlításával összehasonlított parancsmagok nevei szerepelnek.  Például: *New-AzHDInsightCluster* vs. *New-AzureHDInsightCluster*. A paraméterek és kapcsolók tartalmazhatnak híreket, és a Resource Manager használatakor számos új paraméter érhető el.  Például számos parancsmaghoz szükség van egy *ResourceGroupName* nevű új kapcsolóra.

A HDInsight-parancsmagok használata előtt csatlakoznia kell az Azure-fiókjához, és létre kell hoznia egy új erőforráscsoportot:

* [Kapcsolat – AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)

### <a name="renamed-cmdlets"></a>Átnevezett parancsmagok
A HDInsight ASM-parancsmagok listázása a Windows PowerShell-konzolon:

```powershell
help *azurehdinsight*
```

A következő táblázat az ASM-parancsmagokat és azok nevét tartalmazza Resource Manager módban:

| ASM-parancsmagok | Resource Manager-parancsmagok |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Meghívás – AzHDInsightHiveJob](/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[Új – AzHDInsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[Új – AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[Új – AzHDInsightHiveJobDefinition](/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[Új – AzHDInsightMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[Új – AzHDInsightPigJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[Új – AzHDInsightSqoopJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[Új – AzHDInsightStreamingMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start – AzHDInsightJob](/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Leállítás – AzHDInsightJob](/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Várakozás – AzHDInsightJob](/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Új parancsmagok
A következő új parancsmagok csak Resource Manager módban érhetők el. 

**Parancsfájl-művelettel kapcsolatos parancsmagok:**

* **Get-AzHDInsightPersistedScriptAction: lekérdezi** a fürt megőrzött parancsfájl-műveleteit, és időrendi sorrendben sorolja fel őket, vagy lekéri a megadott megőrzött parancsfájl részleteit. 
* **Get-AzHDInsightScriptActionHistory**: lekéri egy fürt parancsfájl-műveleti előzményeit, és fordított időrendi sorrendben listázza azt, vagy lekéri egy korábban végrehajtott parancsfájl-művelet részleteit. 
* **Remove-AzHDInsightPersistedScriptAction**: eltávolít egy megőrzött parancsfájl-műveletet egy HDInsight-fürtből.
* **Set-AzHDInsightPersistedScriptAction**: egy korábban végrehajtott parancsfájl-művelet beállítása megőrzött parancsfájl-műveletnek.
* **Submit-AzHDInsightScriptAction**: új parancsfájl-műveletet küld egy Azure HDInsight-fürtnek. 

További használati információk: [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-művelet használatával](hdinsight-hadoop-customize-cluster-linux.md).

**A fürt identitásával kapcsolatos parancsmagok:**

* **Add-AzHDInsightClusterIdentity**: fürt identitását hozzáadja egy fürtkonfiguráció-objektumhoz, hogy a HDInsight-fürt hozzáférhessen Azure Data Lake Storagehoz. Lásd: [HDInsight-fürt létrehozása a Data Lake Storage Azure PowerShell használatával](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Példák
**Fürt létrehozása**

Régi parancs (ASM): 

```azurepowershell
New-AzureHDInsightCluster `
    -Name $clusterName `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainerName $containerName `
    -ClusterSizeInNodes 2 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.2" `
    -Credential $httpCredential `
    -SshCredential $sshCredential
```

Új parancs:

```azurepowershell
New-AzHDInsightCluster `
    -ClusterName $clusterName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $containerName  `
    -ClusterSizeInNodes 2 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.2" `
    -HttpCredential $httpcredentials `
    -SshCredential $sshCredentials
```

**Fürt törlése**

Régi parancs (ASM):

```azurepowershell
Remove-AzureHDInsightCluster -name $clusterName 
```

Új parancs:

```azurepowershell
Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
```

**Fürt listázása**

Régi parancs (ASM):

```azurepowershell
Get-AzureHDInsightCluster
```

Új parancs:

```azurepowershell
Get-AzHDInsightCluster
```

**Fürt megjelenítése**

Régi parancs (ASM):

```azurepowershell
Get-AzureHDInsightCluster -Name $clusterName
```

Új parancs:

```azurepowershell
Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName
```

#### <a name="other-samples"></a>Egyéb minták
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Apache Hive feladatok elküldése](hadoop/apache-hadoop-use-hive-powershell.md)
* [Apache Sqoop-feladatok elküldése](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Áttelepítés az új HDInsight .NET SDK-ra
Az Azure Service Management-based [(ASM) HDInsight .net SDK](/previous-versions/azure/reference/mt416619(v=azure.100)) már elavult. Javasoljuk, hogy használja az Azure Resource Management-alapú [Resource Manager-alapú HDInsight .net SDK-](/dotnet/api/overview/azure/hdinsight)t. A következő ASM-alapú HDInsight-csomagok elavultak.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ez a szakasz a Resource Manager-alapú SDK-val való bizonyos feladatok végrehajtásával kapcsolatos további információkat tartalmaz.

| kézikönyv... a Resource Manager-alapú HDInsight SDK használata | Hivatkozások |
| --- | --- |
| .NET-hez készült Azure HDInsight SDK|Lásd: [Az Azure HDINSIGHT SDK for .net](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true) |
| Alkalmazások interaktív hitelesítése Azure Active Directory és a .NET SDK használatával |Lásd: [Apache Hive lekérdezések futtatása a .net SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)-val. A cikkben szereplő kódrészlet az interaktív hitelesítési módszert használja. |
| Alkalmazások nem interaktív hitelesítése a .NET SDK-val való Azure Active Directory használatával |Lásd: [nem interaktív alkalmazások létrehozása a HDInsight-hez](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Apache Hive-feladatok elküldése a .NET SDK-val |Lásd: [Apache Hive feladatok elküldése](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Apache Sqoop-feladatok elküldése a .NET SDK-val |Lásd: [Apache Sqoop-feladatok elküldése](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| HDInsight-fürtök listázása a .NET SDK használatával |Lásd: [HDInsight-fürtök listázása](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| HDInsight-fürtök méretezése a .NET SDK-val |Lásd: [HDInsight-fürtök méretezése](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| HDInsight-fürtök hozzáférésének engedélyezése/visszavonása a .NET SDK használatával |Lásd: [hozzáférés engedélyezése/visszavonása HDInsight-fürtökhöz](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| HTTP-felhasználói hitelesítő adatok frissítése HDInsight-fürtökhöz a .NET SDK használatával |Lásd: [http-felhasználói hitelesítő adatok frissítése HDInsight-fürtökhöz](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| A HDInsight-fürtök alapértelmezett Storage-fiókjának megkeresése a .NET SDK használatával |Lásd [a HDInsight-fürtök alapértelmezett tárolási fiókjának megkeresése](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) című témakört. |
| HDInsight-fürtök törlése a .NET SDK-val |Lásd: [HDInsight-fürtök törlése](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Példák
Az alábbiakban néhány példát láthat arra, hogyan történik egy művelet végrehajtása az ASM-alapú SDK-val és a Resource Manager-alapú SDK-hoz hasonló kódrészlettel.

**Fürtbeli szifilisz-ügyfél létrehozása**

* Régi parancs (ASM)

  ```azurecli
  //Certificate auth
  //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
  const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
  var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
  var client = HDInsightClient.Connect(cred);
  ```
* Új parancs (egyszerű szolgáltatás engedélyezése)

  ```azurecli
  //Service principal auth
  //This will log the application in as itself, rather than on behalf of a specific user.
  //For details, including how to set up the application, see:
  //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

* Új parancs (felhasználói hitelesítés)

  ```azurecli
  //User auth
  //This will log the application in on behalf of the user.
  //The end-user will see a login popup.
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

**Fürt létrehozása**

* Régi parancs (ASM)

  ```azurecli
  var clusterInfo = new ClusterCreateParameters
              {
                  Name = dnsName,
                  DefaultStorageAccountKey = key,
                  DefaultStorageContainer = defaultStorageContainer,
                  DefaultStorageAccountName = storageAccountDnsName,
                  ClusterSizeInNodes = 1,
                  ClusterType = type,
                  Location = "West US",
                  UserName = "admin",
                  Password = "*******",
                  Version = version,
                  HeadNodeSize = NodeVMSize.Large,
              };
  clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
  client.CreateCluster(clusterInfo);
  ```

* Új parancs

  ```azurecli
  var clusterCreateParameters = new ClusterCreateParameters
      {
          Location = "West US",
          ClusterType = "Hadoop",
          Version = "3.1",
          OSType = OSType.Linux,
          DefaultStorageAccountName = "mystorage.blob.core.windows.net",
          DefaultStorageAccountKey =
              "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
          UserName = "hadoopuser",
          Password = "*******",
          HeadNodeSize = "ExtraLarge",
          RdpUsername = "hdirp",
          RdpPassword = ""*******",
          RdpAccessExpiry = new DateTime(2025, 3, 1),
          ClusterSizeInNodes = 5
      };
  var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
  clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);
  ```

**HTTP-hozzáférés engedélyezése**

* Régi parancs (ASM)

  ```azurecli
  client.EnableHttp(dnsName, "West US", "admin", "*******");
  ```

* Új parancs
  
  ```azurecli
  var httpParams = new HttpSettingsParameters
  {
         HttpUserEnabled = true,
         HttpUsername = "admin",
         HttpPassword = "*******",
  };
  client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);
  ```

**Fürt törlése**

* Régi parancs (ASM)

  ```azurecli
  client.DeleteCluster(dnsName);
  ```

* Új parancs

  ```azurecli
  client.Clusters.Delete(resourceGroup, dnsname);
  ```
