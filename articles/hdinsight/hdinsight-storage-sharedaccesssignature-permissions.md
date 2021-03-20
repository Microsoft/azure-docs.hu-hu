---
title: Hozzáférés korlátozása közös hozzáférési aláírások használatával – Azure HDInsight
description: Megtudhatja, hogyan használhatja a közös hozzáférési aláírásokat az Azure Blob Storage-ban tárolt HDInsight való hozzáférés korlátozására.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/28/2020
ms.openlocfilehash: 647ac363dd81604f0a5a1d750d98a3349beb19c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931728"
---
# <a name="use-azure-blob-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Az Azure Blob Storage közös hozzáférésű aláírásait használva korlátozza a HDInsight lévő adathozzáférést

A HDInsight teljes hozzáféréssel rendelkezik a fürthöz társított Azure Blob Storage-fiókok adataihoz. A blob-tárolón a közös hozzáférési aláírások használatával korlátozhatja az adathozzáférést. A közös hozzáférésű aláírások (SAS) az Azure Blob Storage-fiókok egyik funkciója, amely lehetővé teszi az adathozzáférés korlátozását. Például csak olvasási hozzáférést biztosít az adateléréshez.

> [!IMPORTANT]  
> Az Apache Rangert használó megoldások esetében érdemes lehet tartományhoz csatlakoztatott HDInsight használni. További információ: [tartományhoz csatlakoztatott HDInsight](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) -dokumentum konfigurálása.

> [!WARNING]  
> A HDInsight teljes hozzáféréssel kell rendelkeznie a fürt alapértelmezett tárolóhoz.

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Egy meglévő [Storage-tároló](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Ha a PowerShellt használja, szüksége lesz az az [modulra](/powershell/azure/).

* Ha az Azure CLI-t szeretné használni, és még nem telepítette, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

* [Python](https://www.python.org/downloads/), 2,7 vagy újabb verzió használata esetén.

* A C# használata esetén a Visual Studio 2013-es vagy újabb verziójának kell lennie.

* A Storage-fiók URI-sémája. Ez a séma `wasb://` Az Azure Blob Storage-hoz, `abfs://` Azure Data Lake Storage Gen2 vagy `adl://` Azure Data Lake Storage Gen1hoz. Ha a biztonságos átvitel engedélyezve van az Azure Blob Storage-hoz, az URI a következő lesz: `wasbs://` .

* Egy meglévő HDInsight-fürt megosztott hozzáférési aláírás hozzáadásához a következőhöz:. Ha nem, akkor a Azure PowerShell használatával létrehozhat egy fürtöt, és hozzáadhat egy közös hozzáférési aláírást a fürt létrehozása során.

* A példa a következő fájlokra: [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) . Ez a tárház a következő elemeket tartalmazza:

  * Egy Visual Studio-projekt, amely létrehoz egy Storage-tárolót, tárolt házirendet és SAS-t a HDInsight-mel való használatra
  * Egy Python-szkript, amely létrehoz egy Storage-tárolót, tárolt házirendet és SAS-t a HDInsight-hez való használatra
  * Egy PowerShell-parancsfájl, amely létrehozhat egy HDInsight-fürtöt, és konfigurálhatja az SAS használatára. A frissített verziók alább láthatók.
  * Egy minta fájl: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok

A közös hozzáférésű aláírások két formája létezik:

* `Ad hoc`: A sas URI-ja a kezdési idő, a lejárati idő és az engedélyek megadása az SAS-hez.

* `Stored access policy`: Egy tárolt hozzáférési szabályzat definiálva van egy erőforrás-tárolón, például egy blob-tárolón. Egy házirend használható egy vagy több megosztott hozzáférési aláírás korlátozásának kezelésére. Ha a SAS-t egy tárolt hozzáférési házirenddel társítja, az SAS örökli a megkötéseket – a kezdési időt, a lejárati időt és az engedélyeket a tárolt hozzáférési házirendhez.

A két űrlap közötti különbség fontos az egyik kulcsfontosságú forgatókönyv esetén: visszavonás. A SAS egy URL-cím, így bárki, aki megszerzi az SAS-t, használhatja azt. Nem számít, hogy ki kérte a kezdését. Ha egy SAS közzé van téve nyilvánosan, a világ bármely tagja használhatja. Egy elosztott SAS-alkalmazás a négy dolog egyike esetén érvényes:

1. A rendszer elérte az SAS-ben megadott lejárati időt.

2. A rendszer elérte az SAS által hivatkozott tárolt hozzáférési házirendben megadott lejárati időt. A lejárati időt a következő esetekben lehet elérni:

    * Az időtartam eltelt.
    * A tárolt hozzáférési szabályzat úgy módosul, hogy a múltban lejárjon a lejárati idő. A lejárati idő módosítása az SAS visszavonásának egyik módja.

3. A SAS által hivatkozott tárolt hozzáférési szabályzat törölve lett, ami egy másik módszer az SAS visszavonására. Ha a tárolt hozzáférési szabályzatot ugyanazzal a névvel hozza létre, az előző szabályzathoz tartozó SAS-jogkivonatok érvényesek (ha az SAS lejárati ideje nem lett átadva). Ha vissza kívánja vonni az SAS-t, ne felejtsen el másik nevet használni, ha később újra létrehozza a hozzáférési szabályzatot.

4. Az SAS létrehozásához használt fiók kulcsa újra lett létrehozva. A kulcs újragenerálása azt eredményezi, hogy az előző kulcsot használó alkalmazások nem tudják hitelesíteni a hitelesítést. Frissítse az összes összetevőt az új kulcsra.

> [!IMPORTANT]  
> A közös hozzáférésű aláírás URI-ja társítva van az aláírás létrehozásához használt fiók kulcsával és a hozzá tartozó tárolt hozzáférési szabályzattal (ha van ilyen). Ha nincs megadva tárolt hozzáférési házirend, a közös hozzáférésű aláírás visszavonásának egyetlen módja a fiók kulcsának módosítása.

Javasoljuk, hogy mindig használjon tárolt hozzáférési házirendeket. Tárolt házirendek használatakor visszavonhatja az aláírásokat, vagy igény szerint kiterjesztheti a lejárati dátumot. A jelen dokumentumban szereplő lépések a tárolt hozzáférési szabályzatokat használják a SAS létrehozásához.

A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd [az SAS-modell megismerése](../storage/common/storage-sas-overview.md)című témakört.

## <a name="create-a-stored-policy-and-sas"></a>Tárolt szabályzat és SAS létrehozása

Mentse az egyes módszerek végén létrehozott SAS-jogkivonatot. A jogkivonat a következő kimenethez hasonlóan fog kinézni:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>A PowerShell használata

Cserélje le a `RESOURCEGROUP` , a `STORAGEACCOUNT` és a `STORAGECONTAINER` értéket a meglévő Storage-tároló megfelelő értékeire. Módosítsa a könyvtárat, `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` vagy módosítsa `-File` úgy a paramétert, hogy az a (z) abszolút elérési útját tartalmazza `Set-AzStorageblobcontent` . Adja meg a következő PowerShell-parancsot:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az ebben a szakaszban szereplő változók használata egy Windows-környezetben alapul. A bash vagy más környezetek esetében kisebb eltérésekre lesz szükség.

1. Cserélje le a `STORAGEACCOUNT` és a `STORAGECONTAINER` értéket a meglévő Storage-tároló megfelelő értékeire.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. A beolvasott elsődleges kulcs beállítása egy változóra későbbi használatra. Cserélje le az `PRIMARYKEY` értéket az előző lépésben lekért értékre, majd írja be az alábbi parancsot:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Módosítsa a könyvtárat, `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` vagy módosítsa `--file` úgy a paramétert, hogy az a (z) abszolút elérési útját tartalmazza `az storage blob upload` . Hajtsa végre a többi parancsot:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name %AZURE_STORAGE_CONTAINER% --policy-name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>A Python használata

Nyissa meg a `SASToken.py` fájlt, és cserélje le a `storage_account_name` `storage_account_key` `storage_container_name` megfelelő értékeket a meglévő Storage-tárolóhoz, majd futtassa a szkriptet.

Előfordulhat, hogy végre kell hajtania, `pip install --upgrade azure-storage` Ha a hibaüzenet jelenik meg `ImportError: No module named azure.storage` .

### <a name="using-c"></a>A C használata\#

1. Nyissa meg a megoldást a Visual Studióban.

2. Megoldáskezelő kattintson a jobb gombbal a **SASExample** projektre, és válassza a **Tulajdonságok** lehetőséget.

3. Válassza a **Beállítások** lehetőséget, és adjon hozzá értékeket a következő bejegyzésekhez:

    |Elem |Leírás |
    |---|---|
    |StorageConnectionString|A Storage-fiókhoz tartozó, tárolt házirendet és SAS-t létrehozni kívánó kapcsolódási karakterlánc. A formátum értéke a `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` `myaccount` Storage-fiók neve, amely a Storage-fiók `mykey` kulcsa.|
    |ContainerName|A Storage-fiók azon tárolója, amelyhez korlátozni kívánja a hozzáférést.|
    |SASPolicyName|A létrehozandó házirend létrehozásához használandó név.|
    |FileToUpload|A tárolóba feltöltött fájl elérési útja.|

4. Futtassa a projektet. Mentse a SAS-szabályzat tokenjét, a Storage-fiók nevét és a tároló nevét. Ezeket az értékeket használja a rendszer a HDInsight-fürthöz tartozó Storage-fiók társításához.

## <a name="use-the-sas-with-hdinsight"></a>Az SAS használata a HDInsight

HDInsight-fürt létrehozásakor meg kell adnia egy elsődleges Storage-fiókot. Emellett további Storage-fiókokat is megadhat. A tárterület hozzáadásának mindkét módszere teljes hozzáférést igényel a használt Storage-fiókokhoz és tárolóhoz.

A tárolók hozzáférésének korlátozásához használjon közös hozzáférésű aláírást. Vegyen fel egy egyéni bejegyzést a fürt **alapvető hely** konfigurációjához. A bejegyzést felveheti a fürt létrehozása során a PowerShell használatával, vagy a fürt létrehozása után a Ambari használatával.

### <a name="create-a-cluster-that-uses-the-sas"></a>SAS-t használó fürt létrehozása

Cserélje le a,, `CLUSTERNAME` `RESOURCEGROUP` ,, `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER` `STORAGEACCOUNT` és `TOKEN` értéket a megfelelő értékekre. Adja meg a PowerShell-parancsokat:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Ha a rendszer kéri a HTTP/s vagy SSH-Felhasználónév és-jelszó megadását, meg kell adnia egy jelszót, amely megfelel a következő feltételeknek:
>
> * Legalább 10 karakter hosszúnak kell lennie.
> * Legalább egy számjegyet tartalmaznia kell.
> * Legalább egy nem alfanumerikus karaktert kell tartalmaznia.
> * Legalább egy kis-és nagybetűt tartalmaznia kell.

Eltarthat egy ideig, amíg ez a szkript befejeződik, általában körülbelül 15 percet vesz igénybe. Ha a parancsfájl hibák nélkül fejeződik be, a fürt létrejött.

### <a name="use-the-sas-with-an-existing-cluster"></a>Az SAS használata meglévő fürttel

Ha meglévő fürttel rendelkezik, a következő lépésekkel adhatja hozzá az SAS **-t a Core-site** konfigurációhoz:

1. Nyissa meg a Ambari webes felhasználói felületét a fürthöz. A lap címe: `https://YOURCLUSTERNAME.azurehdinsight.net` . Ha a rendszer kéri, végezzen hitelesítést a fürtön a fürt létrehozásakor használt rendszergazdai név (rendszergazda) és jelszó használatával.

1. Navigáljon a **HDFS**  >  **konfigurációk**  >  **speciális**  >  **Egyéni Core-site** elemre.

1. Bontsa ki az **Egyéni Core-site** szakaszt, görgessen a végéhez, majd válassza a **tulajdonság hozzáadása..**. lehetőséget. Használja a következő értékeket a **kulcshoz** és az **értékhez**:

    * **Kulcs**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Érték**: a korábban végrehajtott metódusok egyike által visszaadott sas.

    Cserélje le a helyére a `CONTAINERNAME` C# vagy sas-alkalmazáshoz használt tároló nevét. Cserélje le `STORAGEACCOUNTNAME` a nevet a használt Storage-fiók nevére.

    A kulcs és az érték mentéséhez válassza a **Hozzáadás** lehetőséget.

1. A konfigurációs módosítások mentéséhez kattintson a **Save (Mentés** ) gombra. Ha a rendszer kéri, adja meg a módosítás leírását (például "SAS-tároló elérésének hozzáadása"), majd válassza a **Mentés** lehetőséget.

    A módosítások befejeződése után kattintson **az OK gombra** .

   > [!IMPORTANT]  
   > A módosítás érvénybe léptetéséhez több szolgáltatást is újra kell indítania.

1. Ekkor megjelenik egy **Újraindítási** legördülő lista. Válassza az **összes érintett újraindítása** elemet a legördülő listából, majd __erősítse meg az összes újraindítását__.

    Ismételje meg ezt a folyamatot a **MapReduce2** és a **fonal** esetében.

1. A szolgáltatások újraindítása után válassza ki mindegyiket, és tiltsa le a karbantartási módot a **szolgáltatási műveletek** legördülő listából.

## <a name="test-restricted-access"></a>Korlátozott hozzáférés tesztelése

Az alábbi lépések végrehajtásával ellenőrizheti, hogy csak az SAS-Storage-fiókban lévő elemeket tudja-e olvasni és listázni.

1. Kapcsolódjon a fürthöz. Cserélje le a `CLUSTERNAME` nevet a fürt nevére, és írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A tároló tartalmának listázásához használja a következő parancsot a parancssorból:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    A helyére írja `SASCONTAINER` be az SAS Storage-fiókhoz létrehozott tároló nevét. Cserélje le a `SASACCOUNTNAME` nevet a sas-hez használt Storage-fiók nevére.

    A lista tartalmazza a tároló és SAS létrehozásakor feltöltött fájlt.

3. A következő parancs használatával ellenőrizheti, hogy el tudja-e olvasni a fájl tartalmát. Cserélje le a `SASCONTAINER` és az `SASACCOUNTNAME` előző lépésben leírtakat. Cserélje le az `sample.log` parancsot az előző parancsban megjelenő fájl nevére:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Ez a parancs felsorolja a fájl tartalmát.

4. A következő parancs használatával töltse le a fájlt a helyi fájlrendszerbe:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Ez a parancs letölti a fájlt egy **testfile.txt** nevű helyi fájlra.

5. A következő parancs használatával töltse fel a helyi fájlt egy **testupload.txt** nevű új fájlba az SAS-tárolón:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    A következő szöveghez hasonló üzenet jelenik meg:

    ```output
    put: java.io.IOException
    ```

    Ez a hiba azért fordul elő, mert a tárolási hely csak olvasható + lista. A következő paranccsal helyezheti el a fürt alapértelmezett tárolójában lévő adattárolást, amely írható:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Ezúttal a művelet végrehajtása sikeresen befejeződött.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan adhat hozzá korlátozott hozzáférésű tárolót a HDInsight-fürthöz, megismerheti a fürtön tárolt adatkezelés egyéb módjait:

* [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Az Apache Ambari Views használatának engedélyezése felhasználók számára](hdinsight-authorize-users-to-ambari.md)
