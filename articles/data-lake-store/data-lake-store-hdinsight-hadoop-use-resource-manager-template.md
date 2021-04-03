---
title: Sablon – HDInsight-fürt Data Lake Storage Gen1
description: Azure Resource Manager-sablonokkal Azure HDInsight-fürtöket hozhat létre és használhat a Azure Data Lake Storage Gen1 használatával.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: a7283ad4c4c61ecc293a55ffc4cb9626bb28d630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108728"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>HDInsight-fürt létrehozása Azure Data Lake Storage Gen1 Azure Resource Manager sablon használatával
> [!div class="op_single_selector"]
> * [A portál használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [A PowerShell használata (az alapértelmezett tárolóhoz)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [A PowerShell használata (további tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [A Resource Manager használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Megtudhatja, hogyan konfigurálhat egy HDInsight-fürtöt a Azure PowerShell használatával a **további tárterületként** Azure Data Lake Storage Gen1val.

A támogatott fürtök esetében Data Lake Storage Gen1 alapértelmezett tárolóként vagy további Storage-fiókként is használható. Ha Data Lake Storage Gen1 további tárolóként használja, a fürtök alapértelmezett Storage-fiókja továbbra is az Azure Blob Storage (WASB) lesz, és a fürthöz kapcsolódó fájlok (például naplók stb.) továbbra is az alapértelmezett tárolóba kerülnek, míg a feldolgozni kívánt adat egy Data Lake Storage Gen1-fiókban tárolható. A Data Lake Storage Gen1 használata további Storage-fiókként nem befolyásolja a teljesítményt, vagy a fürtből a tárolóba való olvasási/írási képességet.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Data Lake Storage Gen1 használata a HDInsight-fürt tárterületéhez

Az alábbiakban néhány fontos szempontot láthat a HDInsight és a Data Lake Storage Gen1 használatával:

* A HDInsight-fürtök Data Lake Storage Gen1hoz való elérésének lehetősége, mivel az alapértelmezett tároló a 3,5-es és a 3,6-es HDInsight-verzióhoz érhető el.

* A HDInsight-fürtök Data Lake Storage Gen1hoz való elérésének lehetősége, mivel a további tárterület 3,2, 3,4, 3,5 és 3,6 verziójú HDInsight érhető el.

Ebben a cikkben egy Hadoop-fürtöt építünk ki Data Lake Storage Gen1 további tárterületként. A Hadoop-fürtök alapértelmezett tárolóként Data Lake Storage Gen1 való létrehozásával kapcsolatos útmutatásért lásd: [HDInsight-fürt létrehozása Data Lake Storage Gen1 használatával Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása).
* **Azure Active Directory egyszerű szolgáltatásnév**. Az oktatóanyag lépései útmutatást nyújtanak az egyszerű szolgáltatás Azure AD-ben való létrehozásához. Az egyszerű szolgáltatásnév létrehozásához azonban Azure AD-rendszergazdának kell lennie. Ha Ön Azure AD-rendszergazda, akkor kihagyhatja ezt az előfeltételt, és folytathatja az oktatóanyagot.

    **Ha Ön nem Azure ad-rendszergazda**, nem fogja tudni végrehajtani az egyszerű szolgáltatásnév létrehozásához szükséges lépéseket. Ilyen esetben az Azure AD-rendszergazdának először létre kell hoznia egy egyszerű szolgáltatásnevet, mielőtt HDInsight-fürtöt hozna létre Data Lake Storage Gen1 használatával. Emellett a szolgáltatásnevet tanúsítvány használatával kell létrehozni az [egyszerű szolgáltatásnév létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)című témakörben leírtak szerint.

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>HDInsight-fürt létrehozása Data Lake Storage Gen1
A Resource Manager-sablon és a sablon használatának előfeltételei a GitHubon érhetők el a [HDInsight Linux-fürt üzembe helyezése új Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)használatával. Kövesse az ebben a hivatkozásban található utasításokat, és hozzon létre egy HDInsight-fürtöt Data Lake Storage Gen1 a további tárolóként.

A fent említett hivatkozásra vonatkozó utasításokhoz a PowerShell szükséges. Mielőtt elkezdené ezeket az utasításokat, győződjön meg róla, hogy bejelentkezik az Azure-fiókjába. Nyisson meg egy új Azure PowerShell ablakot az asztalról, és adja meg a következő kódrészleteket. Amikor a rendszer kéri, hogy jelentkezzen be, győződjön meg arról, hogy az egyik előfizetés-rendszergazda/tulajdonos:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

A sablon a következő típusú erőforrásokat telepíti:

* [Microsoft. Data Lake Store/fiókok](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. HDInsight/fürtök](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Mintaadatok feltöltése Data Lake Storage Gen1ba
A Resource Manager-sablon létrehoz egy új Storage-fiókot Data Lake Storage Gen1 és társítja a HDInsight-fürthöz. Most fel kell töltenie néhány mintaadatok Data Lake Storage Gen1. Ezekre az adatokra az oktatóanyag későbbi részében szüksége lesz a feladatok futtatásához egy HDInsight-fürtről, amely a Storage-fiókban lévő adatok eléréséhez Data Lake Storage Gen1. Az adatok feltöltésével kapcsolatos utasításokért lásd: [fájlok feltöltése Data Lake Storage Gen1ba](data-lake-store-get-started-portal.md#uploaddata). Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

## <a name="set-relevant-acls-on-the-sample-data"></a>A mintavételi adatokat érintő megfelelő ACL-ek beállítása
Annak érdekében, hogy a feltöltött mintaadatok elérhetők legyenek a HDInsight-fürtről, gondoskodnia kell arról, hogy a HDInsight-fürt és a Data Lake Storage Gen1 közötti identitás azonosításához használt Azure AD-alkalmazás hozzáférhessen az elérni kívánt fájlhoz vagy mappához. Ehhez hajtsa végre az alábbi lépéseket.

1. Keresse meg a HDInsight-fürthöz társított Azure AD-alkalmazás nevét, valamint a Storage-fiókot Data Lake Storage Gen1. A név keresésének egyik módja, ha megnyitja a Resource Manager-sablonnal létrehozott HDInsight-fürtöt, kattintson a **fürt Azure ad-identitás** lapra, és keresse meg az **egyszerű szolgáltatásnév megjelenítendő nevét**.
2. Most adja meg a hozzáférést ehhez az Azure AD-alkalmazáshoz a HDInsight-fürtön elérni kívánt fájlhoz vagy mappához. A megfelelő ACL-ek a fájl/mappa Data Lake Storage Gen1ban történő beállításához tekintse meg az [Data Lake Storage Gen1ban található adatvédelmet](data-lake-store-secure-data.md#filepermissions)ismertető témakört.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Tesztelési feladatok futtatása a HDInsight-fürtön a Data Lake Storage Gen1 használatához
A HDInsight-fürt konfigurálását követően tesztelési feladatokat futtathat a fürtön annak ellenőrzéséhez, hogy a HDInsight-fürt elérheti-e Data Lake Storage Gen1. Ehhez hozzon létre egy minta kaptár-feladatot, amely létrehoz egy táblázatot a korábban a Storage-fiókba a Data Lake Storage Gen1 segítségével feltöltött mintaadatok használatával.

Ebben a szakaszban SSH-t HDInsight Linux-fürtön, és futtatja a minta struktúra-lekérdezést. Ha Windows-ügyfelet használ, javasoljuk a **Putty** használatát, amely letölthető innen: [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

A PuTTY használatával kapcsolatos további információkért lásd: az [SSH használata a HDInsight készült Linux-alapú Hadoop a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)rendszerből.

1. A csatlakozás után indítsa el a kaptár CLI-t a következő parancs használatával:

   ```
   hive
   ```
2. A parancssori felület használatával adja meg a következő utasításokat a **vehicles** nevű új tábla létrehozásához a Data Lake Storage Gen1ban található mintaadatok használatával:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   A következőhöz hasonló kimenetnek kell megjelennie:

   ```
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

Ebben a szakaszban SSH-t HDInsight Linux-fürtön, és futtatja a HDFS-parancsokat. Ha Windows-ügyfelet használ, javasoljuk a **Putty** használatát, amely letölthető innen: [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

A PuTTY használatával kapcsolatos további információkért lásd: az [SSH használata a HDInsight készült Linux-alapú Hadoop a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)rendszerből.

A csatlakozás után a következő HDFS fájlrendszer-paranccsal listázhatja a Storage-fiókban lévő fájlokat a Data Lake Storage Gen1 használatával.

```
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Ekkor fel kell sorolni a korábban feltöltött fájlt Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

A parancs használatával is `hdfs dfs -put` feltölthet néhány fájlt a Data Lake Storage Gen1ba, majd a használatával ellenőrizheti, hogy `hdfs dfs -ls` a fájlok feltöltése sikeres volt-e.


## <a name="next-steps"></a>Következő lépések
* [Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1ba](data-lake-store-copy-data-wasb-distcp.md)
* [Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)