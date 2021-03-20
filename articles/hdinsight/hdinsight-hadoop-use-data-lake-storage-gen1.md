---
title: Data Lake Storage Gen1 használata a Hadoop az Azure HDInsight
description: Megtudhatja, hogyan kérdezheti le az adatait a Azure Data Lake Storage Gen1ból, és hogyan tárolhatja az elemzés eredményeit.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 35941f585a0ae5c0d3915c769db5b18737b299f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945410"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel

> [!Note]
> Új HDInsight-fürtök üzembe helyezése [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) használatával jobb teljesítmény és új funkciók.

A HDInsight-fürtben lévő adatelemzéshez az adattárolót [`Azure Blob storage`](../storage/common/storage-introduction.md) , [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)vagy [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)is tárolhatja. Az összes tárolási beállítás lehetővé teszi, hogy biztonságosan törölje a számításhoz használt HDInsight-fürtöket a felhasználói adatvesztés nélkül.

Ebből a cikkből megtudhatja, hogyan működik a Data Lake Storage Gen1 HDInsight-fürtökkel. Az Azure Blob Storage HDInsight-fürtökkel való működésének megismeréséhez lásd: az [Azure Blob Storage használata az Azure HDInsight-fürtökkel](hdinsight-hadoop-use-blob-storage.md). További információ a HDInsight-fürtök létrehozásáról: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> A Data Lake Storage Gen1 mindig biztonságos csatornán keresztül érhető el, így nincs szükség `adls` fájlrendszeri séma nevére. Mindig `adl` használandó.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>HDInsight-fürtök rendelkezésre állása

Apache Hadoop támogatja az alapértelmezett fájlrendszer fogalmát. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight-fürt létrehozási folyamata során a BLOB-tárolót az Azure Storage-ban alapértelmezett fájlrendszerként kell megadni. A HDInsight 3,5-es és újabb verzióiban kiválaszthatja az Azure Blob Storage-t vagy a Azure Data Lake Storage Gen1t az alapértelmezett fájlrendszerként, néhány kivétellel. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie.

A HDInsight-fürtök két módon használhatják a Data Lake Storage Gen1:

* Az alapértelmezett tárolóként
* További tárterületként az Azure Blob Storage alapértelmezett tárolóként.

Jelenleg csak néhány HDInsight-fürt típusa/verziója támogatja a Data Lake Storage Gen1 alapértelmezett tárolóként és további Storage-fiókokkal:

| A HDInsight-fürt típusa | Data Lake Storage Gen1 alapértelmezett tárolóként | Data Lake Storage Gen1 további tárterületként| Jegyzetek |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight 4,0-es verzió | Nem | Nem |A ADLS Gen1 nem támogatott a HDInsight 4,0 |
| A HDInsight 3.6-os verziója | Igen | Yes | HBase kivételével|
| A HDInsight 3.5-ös verziója | Igen | Yes | HBase kivételével|
| A HDInsight 3.4-es verziója | Nem | Igen | |
| A HDInsight 3.3-as verziója | Nem | Nem | |
| A HDInsight 3.2-es verziója | Nem | Igen | |
| Storm | | |A Storm-topológiából származó adatok írásához Data Lake Storage Gen1 használható. Data Lake Storage Gen1 is használhat olyan hivatkozási információkhoz, amelyeket aztán egy Storm-topológia tud olvasni.|

> [!WARNING]  
> A HDInsight HBase nem támogatott a Azure Data Lake Storage Gen1

A Data Lake Storage Gen1 használata további Storage-fiókként nem befolyásolja a teljesítményt. Az Azure Blob Storage-ba való olvasásának vagy írásának lehetősége a fürtből.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1 használata alapértelmezett tárolóként

Ha a HDInsight Data Lake Storage Gen1 alapértelmezett tárolóként van telepítve, a fürttel kapcsolatos fájlok a (z `adl://mydatalakestore/<cluster_root_path>/` ) helyen tárolódnak, ahol a a `<cluster_root_path>` Data Lake Storageban létrehozott mappa neve. Ha minden fürthöz megadja a gyökér elérési útját, ugyanazt a Data Lake Storage fiókot használhatja több fürthöz is. Így olyan beállítással rendelkezhet, ahol:

* Az 1. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster1storage`
* A 2. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster2storage`

Figyelje meg, hogy mindkét fürt ugyanazt a Data Lake Storage Gen1 fiókot használja **mydatalakestore**. Minden fürt hozzáfér a saját gyökérszintű fájlrendszeréhez Data Lake Storageban. A Azure Portal üzembe helyezési élménye arra kéri, hogy adjon meg egy mappanevet, például a **/Clusters/ \<clustername>** a gyökér elérési útjához.

A Data Lake Storage Gen1 alapértelmezett tárolóként való használatához meg kell adnia az egyszerű szolgáltatás elérését a következő elérési utakhoz:

* A Data Lake Storage Gen1 fiók gyökerét.  Példa: adl://mydatalakestore/.
* Az összes fürt mappája.  Példa: adl://mydatalakestore/clusters.
* Egy adott fürt mappája.  Példa: adl://mydatalakestore/clusters/cluster1storage.

Az egyszerű szolgáltatásnév létrehozásával és a hozzáférés megadásával kapcsolatos további információkért lásd: Data Lake Storage hozzáférés konfigurálása.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Tanúsítvány kinyerése az Azure kulcstartóból a fürt létrehozásához való használathoz

Ha a szolgáltatásnév tanúsítványát Azure Key Vault tárolja, a tanúsítványt a megfelelő formátumba kell konvertálnia. Az alábbi kódrészletek bemutatják, hogyan végezheti el az átalakítást.

Először töltse le a tanúsítványt a Key Vaultból, és bontsa ki a következőt: `SecretValueText` .

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Ezután alakítsa át a `SecretValueText` tanúsítványt a tanúsítványra.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Ezután a használatával `$identityCertificate` új fürtöt helyezhet üzembe az alábbi kódrészlettel:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1 használata kiegészítő tárolóként

A Data Lake Storage Gen1 a fürt további tárterületként is használható. Ilyen esetekben a fürt alapértelmezett tárolója lehet Azure Blob Storage vagy Azure Data Lake Storage Gen1-fiók. Ha HDInsight-feladatokat futtat a Azure Data Lake Storage Gen1ban tárolt adatokon további tárolóként, használja a teljesen minősített elérési utat. Például:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Jelenleg nincs **cluster_root_path** az URL-címben. Ennek az az oka, hogy Data Lake Storage ebben az esetben nem alapértelmezett tároló. Ezért mindössze annyit kell tennie, hogy megadja a fájlok elérési útját.

Ha Data Lake Storage Gen1 kiegészítő tárolóként szeretné használni, adja meg a szolgáltatás egyszerű hozzáférését azokhoz az elérési utakhoz, ahol a fájlok tárolódnak.  Például:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Az egyszerű szolgáltatásnév létrehozásával és a hozzáférés megadásával kapcsolatos további információkért lásd: Data Lake Storage hozzáférés konfigurálása.

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>Egynél több Data Lake Storage Gen1-fiók használata

Data Lake Storage fiók hozzáadása továbbiként, és több Data Lake Storage-fiók hozzáadása is lehetséges. Adja meg a HDInsight-fürt engedélyeit egy vagy több Data Lake Storage-fiókban lévő információhoz. Lásd: Data Lake Storage Gen1 hozzáférés konfigurálása alább.

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 hozzáférés konfigurálása

Ha Azure Data Lake Storage Gen1 hozzáférést szeretne konfigurálni a HDInsight-fürtről, rendelkeznie kell egy Azure Active Directory-(Azure AD-) egyszerű szolgáltatással. Kizárólag Azure AD-rendszergazdák hozhatnak létre szolgáltatásnevet. A szolgáltatásnevet egy tanúsítvánnyal kell létrehozni. További információkért lásd a [fürtök HDInsightban történő beállításának rövid útmutatóját](./hdinsight-hadoop-provision-linux-clusters.md) és a [Szolgáltatásnév létrehozása önaláírt tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) témakört.

> [!NOTE]  
> Ha a HDInsight-fürt számára további tárterületként kívánja használni a Azure Data Lake Storage Gen1, javasoljuk, hogy ezt a cikket a jelen cikkben ismertetett módon hozza létre a fürt létrehozásakor. Azure Data Lake Storage Gen1 hozzáadása egy meglévő HDInsight-fürthöz kiegészítő tárolóként nem támogatott forgatókönyv.

A hozzáférés-vezérlési modellel kapcsolatos további információkért lásd: [Azure Data Lake Storage Gen1 hozzáférés-vezérlése](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Fájlok elérése a fürtből

Több módon is hozzáférhet a Data Lake Storage lévő fájlokhoz egy HDInsight-fürtről.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel az elérési utat a fürt gyökerére cseréli le a következővel:

    ```
    adl:///<file path>
    ```

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Adatelérési példák

A példák a fürt fő csomópontjának [SSH-kapcsolatain](./hdinsight-hadoop-linux-use-ssh-unix.md) alapulnak. A példák mindhárom URI-sémát használják. Cserélje `DATALAKEACCOUNT` le `CLUSTERNAME` a és a értéket a megfelelő értékekre.

#### <a name="a-few-hdfs-commands"></a>Néhány hdfs parancs

1. Hozzon létre egy fájlt a helyi tárolóban.

    ```bash
    touch testFile.txt
    ```

1. Könyvtárak létrehozása a fürt tárterületén.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Adatok másolása a helyi tárolóból a fürt tárolójába.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. A fürt tárterületén lévő könyvtár tartalmának listázása.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Struktúra-tábla létrehozása

A szemléltető célokra három fájl helye látható. A tényleges végrehajtáshoz csak az egyik bejegyzést használja `LOCATION` .

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Tároló elérési útjának azonosítása a Ambari

A konfigurált alapértelmezett tároló teljes elérési útjának azonosításához lépjen a **HDFS**-konfigurációk elemre,  >   és adja meg `fs.defaultFS` a szűrő beviteli mezőjét.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1hoz hozzáféréssel rendelkező HDInsight-fürtök létrehozása

Az alábbi hivatkozásokra kattintva részletesen megtudhatja, hogyan hozhat létre HDInsight-fürtöket Data Lake Storage Gen1hoz való hozzáféréssel.

* [A portál használata](./hdinsight-hadoop-provision-linux-clusters.md)
* [A PowerShell használata (Data Lake Storage Gen1 alapértelmezett tárolóként)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [A PowerShell használata (Data Lake Storage Gen1 kiegészítő tárolóként)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure-sablonok használata](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>A HDInsight-tanúsítvány frissítése a Data Lake Storage Gen1-hozzáférésért

A következő példa PowerShell-kód beolvas egy tanúsítványt egy helyi fájlból vagy Azure Key Vault, és az új tanúsítvánnyal frissíti a HDInsight-fürtöt az Azure Data Lake Storage Gen1 eléréséhez. Adja meg a saját HDInsight-fürt nevét, az erőforráscsoport nevét, az előfizetés AZONOSÍTÓját, `app ID` a tanúsítvány helyi elérési útját. Ha a rendszer kéri, írja be a jelszót.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a HDFS-kompatibilis Azure Data Lake Storage Gen1t a HDInsight. Ez a tároló lehetővé teszi az adaptív, hosszú távú, archivált adatgyűjtési megoldások kiépítését. A HDInsight használatával pedig feloldja a tárolt strukturált és strukturálatlan adatokon belüli információkat.

További információkért lásd:

* [Rövid útmutató: Fürtök beállítása a HDInsightban](./hdinsight-hadoop-provision-linux-clusters.md)
* [Hozzon létre egy HDInsight-fürtöt, amelyet a Data Lake Storage Gen1 használ a Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md)
* [Az Azure Blob Storage közös hozzáférésű aláírásait használva korlátozza az adathozzáférést a HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)