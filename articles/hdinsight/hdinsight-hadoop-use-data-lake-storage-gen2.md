---
title: Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel
description: Megtudhatja, hogyan használhatja Azure Data Lake Storage Gen2 fürtökhöz Azure HDInsight fürtökhöz.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ea9dc2627d5a6498f69ca8c61a9cac8089816886
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378584"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) az Azure Blob Storage-on big data felhőalapú tárolási [szolgáltatás.](../storage/blobs/storage-blobs-introduction.md) Data Lake Storage Gen2 Azure Blob Storage és a Azure Data Lake Storage Gen1. Az eredményül kapott szolgáltatás olyan funkciókat Azure Data Lake Storage Gen1 többek között: fájlrendszer-szemantika, könyvtárszintű és fájlszintű biztonság és alkalmazkodóképesség. Az Azure Blob Storage alacsony költségű, rétegzett tárolási, magas rendelkezésre állási és vészhelyreállítási képességei mellett.

A fürtök létrehozási lehetőségeinek teljes összehasonlításához tekintse meg Data Lake Storage Gen2 a tárolófürtökhöz használható tárolási [lehetőségek Azure HDInsight való összehasonlítását.](hdinsight-hadoop-compare-storage-options.md)

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 rendelkezésre állás

Data Lake Storage Gen2 szolgáltatás szinte minden fürttípushoz elérhető Azure HDInsight egy alapértelmezett és egy további tárfiókként. A HBase azonban csak egy fiókkal és Data Lake Storage Gen2.

> [!Note]  
> Miután kiválasztotta a Data Lake Storage Gen2 elsődleges tárolótípusként, nem választhat ki Data Lake Storage Gen1 tárolóként.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>HDInsight-fürtök létrehozása Data Lake Storage Gen2

Az alábbi hivatkozások részletes útmutatást tartalmaznak a hdinsight-fürtök létrehozásához, amelyek hozzáféréssel Data Lake Storage Gen2.

* [A portál használata](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Az Azure parancssori felület használata](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* A PowerShell jelenleg nem támogatott HDInsight-fürtök létrehozásához Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Hozzáférés-vezérlés Data Lake Storage Gen2 HDInsightban

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Milyen típusú engedélyeket Data Lake Storage Gen2?

Data Lake Storage Gen2 olyan hozzáférés-vezérlési modellt használ, amely azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) és POSIX-hez hasonló hozzáférés-vezérlési listákat (ACL-eket) is támogat. Data Lake Storage Gen1 a hozzáférés-vezérlési listákat csak az adatokhoz való hozzáférés szabályozására támogatja.

Az Azure RBAC szerepkör-hozzárendelésekkel hatékonyan alkalmaz engedélyeket felhasználókra, csoportokra és szolgáltatásnévre az Azure-erőforrásokhoz. Ezek az Azure-erőforrások általában legfelső szintű erőforrásokra (például Azure Blob Storage-fiókokra) vannak korlátozva. Az Azure Blob Storage és Data Lake Storage Gen2 mechanizmus a fájlrendszer-erőforrásra is ki lett terjeszteni.

További információ az Azure RBAC-fájlengedélyekkel kapcsolatban: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC).](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)

Az ACL-ekkel rendelkező fájlengedélyekkel kapcsolatos további információkért lásd: Fájlok és könyvtárak hozzáférés-vezérlési [listái.](../storage/blobs/data-lake-storage-access-control.md)

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hogyan az adataimhoz való hozzáférést a Data Lake Storage Gen2?

A HDInsight-fürt fájlokhoz való hozzáférése a Data Lake Storage Gen2 felügyelt identitásokkal szabályozható. A felügyelt identitás egy, a Azure Active Directory (Azure AD) által regisztrált identitás, amelynek hitelesítő adatait az Azure kezeli. Felügyelt identitások esetében nem kell egyszerű szolgáltatásokat regisztrálnia az Azure AD-ban. Vagy őrizze meg a hitelesítő adatokat, például a tanúsítványokat.

Az Azure-szolgáltatások kétféle felügyelt identitást használhatnak: rendszer által hozzárendelt és felhasználó által hozzárendelt identitásokat. A HDInsight felhasználó által hozzárendelt felügyelt identitásokat használ a Data Lake Storage Gen2. Az `user-assigned managed identity` önálló Azure-erőforrásként jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz.

A felhasználó által hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányokétól, amelyekhez hozzá lettek rendelve. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hogyan Azure AD-felhasználók számára a Hive vagy más szolgáltatások Data Lake Storage Gen2 adatok lekérdezésére vonatkozó engedélyeket?

Ha a felhasználók számára az adatok lekérdezésére vonatkozó engedélyeket kell beállítania, használja az Azure AD biztonsági csoportokat mint az ACL-ek hozzárendelt rendszerbiztonsági tagját. Ne rendeljen közvetlenül fájlelérési engedélyeket az egyes felhasználókhoz vagy szolgáltatásnévhez. Az engedélyek áramlását az Azure AD biztonsági csoportjaival anélkül adhat hozzá és távolíthat el felhasználókkal vagy szolgáltatásnévvel, hogy az ACL-eket egy teljes könyvtárstruktúrára újraalkalmazása nélkül. A felhasználókat csak a megfelelő Azure AD biztonsági csoporthoz kell hozzáadnia, vagy el kell távolítania onnan. Az ACL-ek nem öröklődik, ezért az ACL-ek újraalkalmazásához frissíteni kell az ACL-t minden fájlban és alkönyvtárban.

## <a name="access-files-from-the-cluster"></a>Fájlok elérése a fürtből

A HDInsight-fürtökön található fájlokat többféle Data Lake Storage Gen2 is elérheti.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszersel lecseréli a fürtgyökér elérési útját a következőre:

    ```
    abfs:///<file.path>/
    ```

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Adatelérési példák

A példák a fürt [főcsomópontjára](./hdinsight-hadoop-linux-use-ssh-unix.md) vonatkozó SSH-kapcsolaton alapulnak. A példák mindhárom URI sémát használják. Cserélje le `CONTAINERNAME` a és `STORAGEACCOUNT` a értékeket a megfelelő értékekre

#### <a name="a-few-hdfs-commands"></a>Néhány hdfs-parancs

1. Hozzon létre egy fájlt a helyi tárolón.

    ```bash
    touch testFile.txt
    ```

1. Könyvtárak létrehozása a fürttárolón.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Adatok másolása a helyi tárolóból a fürttárolóba.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Listsa ki a fürttároló könyvtárának tartalmát.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Hive-tábla létrehozása

A rendszer három fájlhelyet mutat be szemléltető célokra. A tényleges végrehajtáshoz csak az egyik `LOCATION` bejegyzést használja.

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
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsight előzetes verzióval Data Lake Storage Gen2 integráció – ACL és biztonsági frissítés](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Az Azure Data Lake Storage Gen2 bemutatása](../storage/blobs/data-lake-storage-introduction.md)
* [Oktatóanyag: Adatok kinyerési, átalakítási és betöltési Interactive Query a Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
