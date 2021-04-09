---
title: PowerShell-HDInsight-fürt Data Lake Storage Gen1-Azure-val
description: A Azure PowerShell használatával Azure HDInsight-fürtöket hozhat létre és használhat Azure Data Lake Storage Gen1 használatával.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 5e899f28cf5b3c11ae5f935d7bc273c566214225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606776"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>HDInsight-fürtök létrehozása Azure Data Lake Storage Gen1 alapértelmezett tárolóként a PowerShell használatával

> [!div class="op_single_selector"]
> * [A Azure Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [A PowerShell használata (az alapértelmezett tárolóhoz)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [A PowerShell használata (további tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [A Resource Manager használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Ismerje meg, hogyan konfigurálhatja a Azure PowerShellt az Azure HDInsight-fürtök alapértelmezett tárolóként való konfigurálásához Azure Data Lake Storage Gen1 használatával. A HDInsight-fürtök további tárolóként Data Lake Storage Gen1 való létrehozásával kapcsolatos utasításokért lásd: [HDInsight-fürt létrehozása Data Lake Storage Gen1 további tárolóként](data-lake-store-hdinsight-hadoop-use-powershell.md).

Az alábbiakban néhány fontos szempontot láthat a HDInsight és a Data Lake Storage Gen1 használatával:

* A HDInsight-fürtök Data Lake Storage Gen1hoz való hozzáférésének lehetősége alapértelmezett tárolóként a 3,5-es és a 3,6-es HDInsight-verzióhoz érhető el.

* A Data Lake Storage Gen1hoz hozzáféréssel rendelkező HDInsight-fürtök létrehozási lehetősége alapértelmezett tárolóként *nem érhető el* a prémium szintű HDInsight-fürtökhöz.

Ha úgy szeretné konfigurálni a HDInsight, hogy az Data Lake Storage Gen1 a PowerShell használatával működjön, kövesse az alábbi öt szakasz utasításait.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy megfelel az alábbi követelményeknek:

* **Azure-előfizetés**: Nyissa meg az [Azure ingyenes próbaverzióját](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1,0 vagy újabb**: lásd [a PowerShell telepítése és konfigurálása](/powershell/azure/)című témakört.
* **Windows szoftverfejlesztői készlet (SDK)**: a Windows SDK telepítéséhez nyissa meg a [Windows 10-es letöltések és eszközök eszközét](https://dev.windows.com/downloads). Az SDK biztonsági tanúsítvány létrehozásához használható.
* **Azure Active Directory egyszerű szolgáltatás**: ez az oktatóanyag azt ismerteti, hogyan hozható létre egyszerű szolgáltatásnév a Azure Active Directory (Azure ad) szolgáltatásban. Egyszerű szolgáltatásnév létrehozásához azonban Azure AD-rendszergazdának kell lennie. Ha Ön rendszergazda, akkor kihagyhatja ezt az előfeltételt, és folytathatja az oktatóanyagot.

    >[!NOTE]
    >Csak akkor hozhat létre egyszerű szolgáltatásnevet, ha Ön Azure AD-rendszergazda. Az Azure AD-rendszergazdának létre kell hoznia egy egyszerű szolgáltatásnevet ahhoz, hogy HDInsight-fürtöt hozzon létre Data Lake Storage Gen1 használatával. Az egyszerű szolgáltatást tanúsítványokkal kell létrehozni az [egyszerű szolgáltatásnév létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)című témakörben leírtak szerint.
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 fiók létrehozása

Data Lake Storage Gen1 fiók létrehozásához tegye a következőket:

1. Nyisson meg egy PowerShell-ablakot az asztalról, majd írja be az alábbi kódrészleteket. Amikor a rendszer felszólítja, hogy jelentkezzen be, jelentkezzen be az előfizetés-adminisztrátorok vagy-tulajdonosok egyikével. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Ha regisztrálja a Data Lake Storage Gen1 erőforrás-szolgáltatót, és a következőhöz hasonló hibaüzenetet kap `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` , előfordulhat, hogy az előfizetését nem hagyja jóvá Data Lake Storage Gen1. Az Data Lake Storage Gen1 Azure-előfizetésének engedélyezéséhez kövesse az [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait.
    >

2. Egy Data Lake Storage Gen1 fiók egy Azure-erőforráscsoporthoz van társítva. Első lépésként hozzon létre egy erőforráscsoportot.

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

3. Hozzon létre egy Data Lake Storage Gen1 fiókot. A megadott fióknév csak kisbetűket és számokat tartalmazhat.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. A Data Lake Storage Gen1 alapértelmezett tárolóként való használatához meg kell adnia egy olyan gyökér elérési utat, amelyhez a fürtre vonatkozó fájlok másolása történik a fürt létrehozása során. A kódrészletben **/Clusters/hdiadlcluster** gyökér elérési út létrehozásához használja a következő parancsmagokat:

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Szerepköralapú hozzáférés beállítása Data Lake Storage Gen1hoz
Minden Azure-előfizetés egy Azure AD-entitáshoz van társítva. Azok a felhasználók és szolgáltatások, amelyek az előfizetési erőforrásokat a Azure Portal vagy a Azure Resource Manager API használatával érik el, először hitelesíteniük kell magukat az Azure AD-vel. Az Azure-előfizetések és-szolgáltatások hozzáférése az Azure-erőforrás megfelelő szerepkörének hozzárendelésével biztosítható. A szolgáltatások esetében egy egyszerű szolgáltatás azonosítja a szolgáltatást az Azure AD-ben.

Ez a szakasz bemutatja, hogyan adhat meg egy alkalmazás-szolgáltatást, például a HDInsight, hozzáfér egy Azure-erőforráshoz (a korábban létrehozott Data Lake Storage Gen1-fiókhoz). Ehhez hozzon létre egy egyszerű szolgáltatásnevet az alkalmazáshoz, és rendeljen hozzá szerepköröket a PowerShell használatával.

Data Lake Storage Gen1 Active Directory hitelesítésének beállításához hajtsa végre a következő két szakaszban található feladatokat.

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Az ebben a szakaszban ismertetett lépések végrehajtása előtt győződjön meg arról, hogy [Windows SDK](https://dev.windows.com/en-us/downloads) telepítve van. Létre kell hoznia egy könyvtárat is, például a *C:\mycertdir*, ahol létrehozza a tanúsítványt.

1. A PowerShell-ablakban lépjen arra a helyre, ahol a Windows SDK telepítette (jellemzően a *C:\Program Files (x86) \Windows Kits\10\bin\x86*), és használja a [MakeCert][makecert] segédprogramot egy önaláírt tanúsítvány és egy titkos kulcs létrehozásához. Az alábbi parancsokat használja:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    A rendszer kérni fogja a titkos kulcs jelszavának megadását. A parancs sikeres végrehajtása után a megadott **bizonyítványfájl. cer** és **Mykey. PVK** jelenik meg.
2. A [Pvk2Pfx][pvk2pfx] segédprogram használatával alakítsa át a. pfx-fájlba MakeCert létrehozott. PVK és. cer fájlokat. Futtassa az alábbi parancsot:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Ha a rendszer kéri, adja meg a korábban megadott titkos kulcs jelszavát. A **-Po** paraméterhez megadott érték a. pfx-fájlhoz társított jelszó. A parancs sikeres befejezését követően egy **bizonyítványfájl. pfx** fájl is megjelenik a megadott tanúsítvány-címtárban.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Azure AD és egyszerű szolgáltatásnév létrehozása
Ebben a szakaszban egy egyszerű szolgáltatásnevet hoz létre egy Azure AD-alkalmazáshoz, hozzárendelhet egy szerepkört az egyszerű szolgáltatásnév számára, és a tanúsítvány megadásával hitelesíti magát az egyszerű szolgáltatásként. Ha alkalmazást szeretne létrehozni az Azure AD-ben, futtassa a következő parancsokat:

1. Illessze be a következő parancsmagokat a PowerShell-konzol ablakába. Ügyeljen arra, hogy a **-DisplayName** tulajdonsághoz megadott érték egyedi legyen. A **-homepage** és a **-IdentiferUris** értékek helyőrző értékek, és nincsenek ellenőrizve.

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

2. Hozzon létre egy szolgáltatásnevet az alkalmazás-azonosító használatával.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

    $objectId = $servicePrincipal.Id
    ```

3. Adja meg a szolgáltatás egyszerű hozzáférését a Data Lake Storage Gen1 gyökeréhez és a gyökér elérési útjában megadott összes mappához. Használja a következő parancsmagokat:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>HDInsight Linux-fürt létrehozása Data Lake Storage Gen1 alapértelmezett tárolóként

Ebben a szakaszban egy HDInsight Hadoop Linux-fürtöt hoz létre, amely az alapértelmezett tároló Data Lake Storage Gen1. Ebben a kiadásban a HDInsight-fürtnek és a Data Lake Storage Gen1nak ugyanazon a helyen kell lennie.

1. Kérje le az előfizetés-bérlő AZONOSÍTÓját, és tárolja később a használathoz.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Hozza létre a HDInsight-fürtöt a következő parancsmagok használatával:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name    # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>"     # e.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    A parancsmag sikeres befejeződése után megjelenik egy kimenet, amely felsorolja a fürt részleteit.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Tesztelési feladatok futtatása a HDInsight-fürtön a Data Lake Storage Gen1 használatához
Miután konfigurálta a HDInsight-fürtöt, a tesztelési feladatok futtatásával biztosíthatja, hogy hozzáférhessen Data Lake Storage Gen1hoz. Ehhez futtasson egy minta kaptár-feladatot egy olyan tábla létrehozásához, amely a Data Lake Storage Gen1ban már elérhető mintaadatok használatával rendelkezik a következő helyen: *\<cluster root> /example/Data/sample.log*.

Ebben a szakaszban egy Secure Shell-(SSH-) kapcsolatokat hoz létre a létrehozott HDInsight Linux-fürthöz, majd futtat egy minta kaptár-lekérdezést.

* Ha Windows-ügyfelet használ SSH-kapcsolatok létrehozásához a fürtben, tekintse [meg az SSH használata a HDInsight a Linux-alapú Hadoop a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)rendszerből című témakört.
* Ha Linux-ügyfelet használ SSH-kapcsolatok létrehozásához a fürtben, tekintse [meg az SSH használata Linux-alapú Hadoop a HDInsight Linuxon](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)című témakört.

1. A kapcsolat létrejötte után indítsa el a kaptár parancssori felületét (CLI) a következő parancs használatával:

    ```powershell
    hive
    ```

2. A CLI használatával adja meg a következő utasításokat a **vehicles** nevű új tábla létrehozásához a Data Lake Storage Gen1ban található mintaadatok használatával:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

Ekkor meg kell jelennie a lekérdezés kimenetének az SSH-konzolon.

>[!NOTE]
>Az előző CREATE TABLE parancsban található mintaadatok elérési útja `adl:///example/data/` , ahol a a `adl:///` fürt gyökerét. Az oktatóanyagban megadott fürtcsomópont példáját követve a parancs a következő: `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster` . Használhatja a rövidebb alternatívát, vagy megadhatja a fürt gyökerének teljes elérési útját.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Hozzáférés Data Lake Storage Gen1 HDFS parancsok használatával
Miután konfigurálta a HDInsight-fürtöt Data Lake Storage Gen1 használatára, használhatja a Hadoop elosztott fájlrendszer (HDFS) rendszerhéj-parancsokat az áruház eléréséhez.

Ebben a szakaszban egy SSH-kapcsolatban hozza létre a létrehozott HDInsight Linux-fürtöt, majd futtatja a HDFS-parancsokat.

* Ha Windows-ügyfelet használ SSH-kapcsolatok létrehozásához a fürtben, tekintse [meg az SSH használata a HDInsight a Linux-alapú Hadoop a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)rendszerből című témakört.
* Ha Linux-ügyfelet használ SSH-kapcsolatok létrehozásához a fürtben, tekintse [meg az SSH használata Linux-alapú Hadoop a HDInsight Linuxon](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)című témakört.

Miután elvégezte a kapcsolatokat, a következő HDFS-paranccsal sorolja fel Data Lake Storage Gen1 található fájlokat.

```azurepowershell
hdfs dfs -ls adl:///
```

A parancs használatával is `hdfs dfs -put` feltölthet néhány fájlt a Data Lake Storage Gen1ba, majd a használatával ellenőrizheti, hogy `hdfs dfs -ls` a fájlok feltöltése sikeres volt-e.

## <a name="see-also"></a>Lásd még
* [Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Azure Portal: hozzon létre egy HDInsight-fürtöt a használatra Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx