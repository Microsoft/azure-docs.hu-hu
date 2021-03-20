---
title: PowerShell – HDInsight Data Lake Storage Gen1-bővítmény tárolásával – Azure
description: Megtudhatja, hogyan konfigurálhat egy HDInsight-fürtöt a Azure Data Lake Storage Gen1 további tárolóként a Azure PowerShell használatával.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b7cb9d5c5c2ca850678d3f3194a9af8de526ada4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103390"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>A Azure PowerShell használatával hozzon létre egy HDInsight-fürtöt Azure Data Lake Storage Gen1 (további tárterületként)

> [!div class="op_single_selector"]
> * [A portál használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [A PowerShell használata (az alapértelmezett tárolóhoz)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [A PowerShell használata (további tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [A Resource Manager használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Megtudhatja, hogyan konfigurálhat egy HDInsight-fürtöt a Azure PowerShell használatával a **további tárterületként** Azure Data Lake Storage Gen1val. A HDInsight-fürtök alapértelmezett tárolóként Data Lake Storage Gen1 való létrehozásával kapcsolatos útmutatásért lásd: [HDInsight-fürt létrehozása Data Lake Storage Gen1 alapértelmezett tárolóként](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Ha a HDInsight-fürt számára további tárterületként kívánja használni a Data Lake Storage Gen1, javasoljuk, hogy ezt a cikket a jelen cikkben ismertetett módon hozza létre a fürt létrehozásakor. Data Lake Storage Gen1 hozzáadása egy meglévő HDInsight-fürthöz további tárterületként egy bonyolult folyamat, amely a hibákra hajlamos.
>

A támogatott fürtök esetében Data Lake Storage Gen1 alapértelmezett tárolóként vagy további Storage-fiókként is használható. Ha Data Lake Storage Gen1 további tárolóként használja, a fürtök alapértelmezett Storage-fiókja továbbra is az Azure Blob Storage (WASB) lesz, és a fürthöz kapcsolódó fájlok (például naplók stb.) továbbra is az alapértelmezett tárolóba kerülnek, míg a feldolgozni kívánt adat egy Data Lake Storage Gen1 tárolható. A Data Lake Storage Gen1 használata további Storage-fiókként nem befolyásolja a teljesítményt, vagy a fürtből a tárolóba való olvasási/írási képességet.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Data Lake Storage Gen1 használata a HDInsight-fürt tárterületéhez

Az alábbiakban néhány fontos szempontot láthat a HDInsight és a Data Lake Storage Gen1 használatával:

* A HDInsight-fürtök Data Lake Storage Gen1hoz való elérésének lehetősége, mivel a további tárterület 3,2, 3,4, 3,5 és 3,6 verziójú HDInsight érhető el.

A Data Lake Storage Gen1 PowerShell-lel való használatához a HDInsight konfigurálása a következő lépésekből áll:

* Data Lake Storage Gen1-fiók létrehozása 
* Szerepköralapú hozzáférés beállítása Data Lake Storage Gen1hoz
* HDInsight-fürt létrehozása Data Lake Storage Gen1 hitelesítéssel
* Tesztelési feladatok futtatása a fürtön

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása).
* **Windows SDK**. A készletet [innen](https://dev.windows.com/en-us/downloads) telepítheti. Ezt a biztonsági tanúsítvány létrehozásához használhatja.
* **Azure Active Directory egyszerű szolgáltatásnév**. Az oktatóanyag lépései útmutatást nyújtanak az egyszerű szolgáltatás Azure AD-ben való létrehozásához. Az egyszerű szolgáltatásnév létrehozásához azonban Azure AD-rendszergazdának kell lennie. Ha Ön Azure AD-rendszergazda, akkor kihagyhatja ezt az előfeltételt, és folytathatja az oktatóanyagot.

    **Ha Ön nem Azure ad-rendszergazda**, nem fogja tudni végrehajtani az egyszerű szolgáltatásnév létrehozásához szükséges lépéseket. Ilyen esetben az Azure AD-rendszergazdának először létre kell hoznia egy egyszerű szolgáltatásnevet, mielőtt HDInsight-fürtöt hozna létre Data Lake Storage Gen1 használatával. Emellett a szolgáltatásnevet tanúsítvány használatával kell létrehozni az [egyszerű szolgáltatásnév létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)című témakörben leírtak szerint.

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása 
Data Lake Storage Gen1 fiók létrehozásához kövesse az alábbi lépéseket.

1. Nyisson meg egy új Azure PowerShell ablakot az asztalról, és adja meg a következő kódrészletet. Amikor a rendszer kéri, hogy jelentkezzen be, győződjön meg arról, hogy az előfizetés-rendszergazda/tulajdonos egyike:

    ```azurepowershell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

   > [!NOTE]
   > Ha `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` a Data Lake Storage Gen1 erőforrás-szolgáltató regisztrálásához hasonló hibaüzenetet kap, akkor előfordulhat, hogy az előfizetését nem hagyja jóvá a Data Lake Storage Gen1. Ezeket az [utasításokat](data-lake-store-get-started-portal.md)követve engedélyezze Data Lake Storage Gen1 Azure-előfizetését.
   >
   >
2. Data Lake Storage Gen1 társított Storage-fiók egy Azure-erőforráscsoporthoz van társítva. Először hozzon létre egy Azure-erőforráscsoportot.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Hozzon létre egy Data Lake Storage Gen1 tartalmazó Storage-fiókot. A megadott fióknév csak kisbetűket és számokat tartalmazhat.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new storage account with Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

5. Töltsön fel néhány mintaadatok Data Lake Storage Gen1ba. A cikk későbbi részében azt fogjuk használni, hogy ellenőrizze, hogy az adatok elérhetők-e egy HDInsight-fürtről. Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

    ```azurepowershell
    $myrootdir = "/"
    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv
    ```

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Szerepköralapú hozzáférés beállítása Data Lake Storage Gen1hoz

Minden Azure-előfizetés egy Azure Active Directory van társítva. Azoknak a felhasználóknak és szolgáltatásoknak, amelyek a Azure Portal vagy Azure Resource Manager API használatával érik el az előfizetés erőforrásait, először hitelesíteniük kell az adott Azure Active Directory. Az Azure-előfizetések és-szolgáltatások hozzáférése az Azure-erőforrás megfelelő szerepkörének hozzárendelésével biztosítható.  A szolgáltatások esetében egy egyszerű szolgáltatásnév azonosítja a szolgáltatást a Azure Active Directoryban (Azure AD). Ez a szakasz azt szemlélteti, hogyan adható meg egy alkalmazás-szolgáltatás, például a HDInsight, hozzáférés egy Azure-erőforráshoz (a korábban létrehozott Data Lake Storage Gen1 Storage-fiókhoz), ha létrehoz egy egyszerű szolgáltatásnevet az alkalmazáshoz, és hozzárendeli a szerepköröket az adott Azure PowerShelln keresztül.

Data Lake Storage Gen1 Active Directory hitelesítésének beállításához a következő feladatokat kell végrehajtania.

* Önaláírt tanúsítvány létrehozása
* Alkalmazás létrehozása Azure Active Directory és egyszerű szolgáltatásban

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Az ebben a szakaszban ismertetett lépések végrehajtása előtt győződjön meg arról, hogy [Windows SDK](https://dev.windows.com/en-us/downloads) telepítve van. Létre kell hoznia egy könyvtárat is, például a **C:\mycertdir**-t, ahol a tanúsítvány létre lesz hozva.

1. A PowerShell ablakában navigáljon arra a helyre, ahová a Windows SDK telepítette (jellemzően, `C:\Program Files (x86)\Windows Kits\10\bin\x86` és a [MakeCert][makecert] segédprogram használatával hozzon létre egy önaláírt tanúsítványt és egy titkos kulcsot. Használja az alábbi parancsokat.

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    A rendszer kérni fogja a titkos kulcs jelszavának megadását. A parancs sikeres végrehajtása után a megadott **bizonyítványfájl. cer** és **Mykey. PVK** jelenik meg.
2. A [Pvk2Pfx][pvk2pfx] segédprogram használatával alakítsa át a. pfx-fájlba MakeCert létrehozott. PVK és. cer fájlokat. Futtassa az alábbi parancsot.

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Amikor a rendszer kéri, adja meg a korábban megadott titkos kulcs jelszavát. A **-Po** paraméterhez megadott érték a. pfx-fájlhoz társított jelszó. A parancs sikeres befejeződése után egy bizonyítványfájl. pfx fájl is megjelenik a megadott tanúsítvány-könyvtárban.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Azure Active Directory és egyszerű szolgáltatásnév létrehozása

Ebben a szakaszban egy egyszerű szolgáltatásnév létrehozásához szükséges lépéseket hajtja végre egy Azure Active Directory alkalmazáshoz, rendeljen hozzá egy szerepkört az egyszerű szolgáltatáshoz, és a tanúsítvány megadásával hitelesítse magát a szolgáltatásként. Futtassa az alábbi parancsokat Azure Active Directory alkalmazás létrehozásához.

1. Illessze be a következő parancsmagokat a PowerShell-konzol ablakába. Ügyeljen arra, hogy a **-DisplayName** tulajdonsághoz megadott érték egyedi legyen. Emellett a **-homepage** és a **-IdentiferUris** értékek helyőrző értékek, és nem ellenőrizhetők.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Hozzon létre egy egyszerű szolgáltatást az alkalmazás-azonosító használatával.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

     $objectId = $servicePrincipal.Id
    ```

3. Adja meg az egyszerű szolgáltatás elérését a Data Lake Storage Gen1 mappához és a fájlhoz, amelyet a HDInsight-fürthöz fog elérni. Az alábbi kódrészlet hozzáférést biztosít a Storage-fiók gyökeréhez Data Lake Storage Gen1 (amely a minta adatfájlját másolta), és maga a fájl.

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>HDInsight Linux-fürt létrehozása Data Lake Storage Gen1 kiegészítő tárolóként

Ebben a szakaszban egy HDInsight Hadoop Linux-fürtöt hozunk létre Data Lake Storage Gen1 kiegészítő tárolóként. Ebben a kiadásban a HDInsight-fürt és a Data Lake Storage Gen1-tároló fióknak ugyanazon a helyen kell lennie.

1. Kezdje az előfizetés-bérlő AZONOSÍTÓjának beolvasásával. Erre később szüksége lesz.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Ebben a kiadásban a Hadoop-fürtök esetében Data Lake Storage Gen1 csak a fürt további tárolóként használható. Az alapértelmezett tárterület továbbra is az Azure Blob Storage (WASB) lesz. Ezért először létre kell hozni a fürthöz szükséges Storage-fiókot és tároló-tárolókat.

    ```azurepowershell
    # Create an Azure storage account
    $location = "East US 2"
    $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

    New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzStorageContainer -Name $containerName -Context $destContext
    ```

3. Hozza létre a HDInsight-fürtöt. Használja a következő parancsmagokat.

    ```azurepowershell
    # Set these variables
    $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password
    ```

    A parancsmag sikeres befejeződése után megjelenik egy kimenet, amely a fürt részleteit tartalmazza.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1"></a>Tesztelési feladatok futtatása a HDInsight-fürtön a Data Lake Storage Gen1 használatához
A HDInsight-fürt konfigurálását követően tesztelési feladatokat futtathat a fürtön annak ellenőrzéséhez, hogy a HDInsight-fürt elérheti-e Data Lake Storage Gen1. Ehhez hozzon létre egy minta kaptár-feladatot, amely létrehoz egy táblázatot a korábban a Storage-fiókba a Data Lake Storage Gen1 segítségével feltöltött mintaadatok használatával.

Ebben a szakaszban az SSH-t fogja létrehozni a létrehozott HDInsight Linux-fürtön, és futtatta a minta struktúra-lekérdezést.

* Ha Windows-ügyfelet használ az SSH-ba a fürtben, tekintse [meg az SSH használata a HDInsight a Linux-alapú Hadoop a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)rendszerből című témakört.
* Ha Linux-ügyfelet használ az SSH-ba a fürtben, tekintse [meg az SSH használata Linux-alapú Hadoop a HDInsight Linuxon](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) című témakört.

1. A csatlakozás után indítsa el a kaptár CLI-t a következő parancs használatával:

    ```azurepowershell
    hive
    ```

2. A parancssori felület használatával adja meg a következő utasításokat a **vehicles** nevű új tábla létrehozásához a Data Lake Storage Gen1ban található mintaadatok használatával:

    ```azurepowershell
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```output
    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
    ```

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Hozzáférés Data Lake Storage Gen1 a HDFS parancsok használatával
Miután konfigurálta a HDInsight-fürtöt Data Lake Storage Gen1 használatára, a HDFS rendszerhéj-parancsaival érheti el az áruházat.

Ebben a szakaszban az SSH-t fogja létrehozni a létrehozott HDInsight Linux-fürtön, és futtatja a HDFS-parancsokat.

* Ha Windows-ügyfelet használ az SSH-ba a fürtben, tekintse [meg az SSH használata a HDInsight a Linux-alapú Hadoop a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)rendszerből című témakört.
* Ha Linux-ügyfelet használ az SSH-ba a fürtben, tekintse [meg az SSH használata Linux-alapú Hadoop a HDInsight Linuxon](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) című témakört.

A csatlakozás után a következő HDFS fájlrendszer-paranccsal listázhatja a Storage-fiókban lévő fájlokat a Data Lake Storage Gen1 használatával.

```azurepowershell
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Ekkor fel kell sorolni a korábban feltöltött fájlt Data Lake Storage Gen1.

```output
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

A parancs használatával is `hdfs dfs -put` feltölthet néhány fájlt a Data Lake Storage Gen1ba, majd a használatával ellenőrizheti, hogy `hdfs dfs -ls` a fájlok feltöltése sikeres volt-e.

## <a name="see-also"></a>Lásd még:
* [Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Portál: hozzon létre egy HDInsight-fürtöt Data Lake Storage Gen1 használatához](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx