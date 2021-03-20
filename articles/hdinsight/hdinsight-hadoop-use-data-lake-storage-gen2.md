---
title: Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel
description: Ismerje meg, hogyan használhatja a Azure Data Lake Storage Gen2t az Azure HDInsight-fürtökkel.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/24/2020
ms.openlocfilehash: 2bbfbd2d953ea663453f0092ff366e95f6dd5ea7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945378"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel

A [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) egy [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-ra épülő, Big Data elemzésre szánt felhőalapú tárolási szolgáltatás. Data Lake Storage Gen2 ötvözi az Azure Blob Storage és a Azure Data Lake Storage Gen1 képességeit. Az eredményül kapott szolgáltatás Azure Data Lake Storage Gen1 többek között a következő funkciókat kínálja: fájlrendszer-szemantika, címtár-szintű és fájl szintű biztonság, valamint alkalmazkodóképesség. Az Azure Blob Storage alacsony díjas, többrétegű tárolási, magas rendelkezésre állású és vész-helyreállítási képességeivel együtt.

A fürt létrehozási lehetőségeinek Data Lake Storage Gen2 használatával történő teljes összehasonlítását lásd: a [tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz](hdinsight-hadoop-compare-storage-options.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 rendelkezésre állás

A Data Lake Storage Gen2 tárolási lehetőségként érhető el szinte minden Azure HDInsight-fürthöz, alapértelmezettként és egy további Storage-fiókként is. A HBase azonban csak egyetlen fiókkal rendelkezhet Data Lake Storage Gen2.

> [!Note]  
> Miután kiválasztotta a Data Lake Storage Gen2 **elsődleges tárolási típusként**, nem választhat Data Lake Storage Gen1 további tárolóként.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>HDInsight-fürtök létrehozása Data Lake Storage Gen2 használatával

Az alábbi hivatkozásokra kattintva részletesen megtudhatja, hogyan hozhat létre HDInsight-fürtöket Data Lake Storage Gen2hoz való hozzáféréssel.

* [A portál használata](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Az Azure parancssori felület használata](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* A PowerShell jelenleg nem támogatott HDInsight-fürt létrehozásához Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Data Lake Storage Gen2 hozzáférés-vezérlése a HDInsight-ben

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Milyen engedélyeket Data Lake Storage Gen2 támogatni?

A Data Lake Storage Gen2 egy hozzáférés-vezérlési modellt használ, amely támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) és a POSIX-hez hasonló hozzáférés-vezérlési listákat (ACL-eket). A Data Lake Storage Gen1 csak az adathozzáférés szabályozásához támogatja a hozzáférés-vezérlési listát.

Az Azure RBAC szerepkör-hozzárendeléseket használ az Azure-erőforrásokhoz tartozó felhasználókra, csoportokra és egyszerű szolgáltatásokra vonatkozó engedélyek hatékony alkalmazására. Ezek az Azure-erőforrások jellemzően felső szintű erőforrásokra (például Azure Blob Storage-fiókokra) korlátozzák. Az Azure Blob Storage esetében, valamint Data Lake Storage Gen2 is, ez a mechanizmus a fájlrendszer erőforrására lett kiterjesztve.

További információ az Azure RBAC kapcsolatos fájlengedélyek használatáról: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Az ACL-ekkel rendelkező fájlengedélyek részletes ismertetését lásd: [hozzáférés-vezérlési listák a fájlokon és könyvtárakon](../storage/blobs/data-lake-storage-access-control.md).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hogyan szabályozhatja az adataik elérését Data Lake Storage Gen2ban?

A HDInsight-fürtnek a Data Lake Storage Gen2 lévő fájlokhoz való hozzáférését felügyelt identitások vezérlik. A felügyelt identitás a Azure Active Directory (Azure AD) szolgáltatásban regisztrált identitás, amelynek hitelesítő adatait az Azure felügyeli. A felügyelt identitásokkal nem kell regisztrálnia az egyszerű szolgáltatásokat az Azure AD-ben. Vagy őrizze meg a hitelesítő adatokat, például a tanúsítványokat.

Az Azure-szolgáltatások két típusú felügyelt identitással rendelkeznek: rendszerhez rendelt és felhasználó által hozzárendelt. A HDInsight felhasználó által hozzárendelt felügyelt identitásokat használ a Data Lake Storage Gen2 eléréséhez. Az a létrehozása `user-assigned managed identity` önálló Azure-erőforrásként történik. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz.

A felhasználó által hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányokétól, amelyekhez hozzá lettek rendelve. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hogyan az Azure AD-felhasználók számára a kaptár vagy más szolgáltatások használatával történő adatlekérdezésre vonatkozó engedélyeket Data Lake Storage Gen2.

A felhasználók számára az adatlekérdezésre vonatkozó engedélyek megadásához az Azure AD biztonsági csoportokat használja az ACL-ekben a hozzárendelt elsődlegesként. Ne rendeljen hozzá közvetlenül hozzáférési engedélyeket az egyes felhasználókhoz vagy egyszerű szolgáltatásokhoz. Az Azure AD biztonsági csoportjaival szabályozhatja az engedélyek áramlását, hozzáadhat és eltávolíthat felhasználókat vagy egyszerű szolgáltatásokat anélkül, hogy az ACL-eket újra kellene alkalmazni egy teljes címtár-struktúrára. A megfelelő Azure AD biztonsági csoportból csak a felhasználókat kell hozzáadnia vagy eltávolítania. Az ACL-ek nem öröklődnek, ezért az ACL-ek újraalkalmazása megköveteli az ACL frissítését minden fájlon és alkönyvtáron.

## <a name="access-files-from-the-cluster"></a>Fájlok elérése a fürtből

Több módon is hozzáférhet a Data Lake Storage Gen2 lévő fájlokhoz egy HDInsight-fürtről.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel az elérési utat a fürt gyökerére cseréli le a következővel:

    ```
    abfs:///<file.path>/
    ```

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Adatelérési példák

A példák a fürt fő csomópontjának [SSH-kapcsolatain](./hdinsight-hadoop-linux-use-ssh-unix.md) alapulnak. A példák mindhárom URI-sémát használják. Cserélje `CONTAINERNAME` le `STORAGEACCOUNT` a és a értéket a megfelelő értékekre.

#### <a name="a-few-hdfs-commands"></a>Néhány hdfs parancs

1. Hozzon létre egy fájlt a helyi tárolóban.

    ```bash
    touch testFile.txt
    ```

1. Könyvtárak létrehozása a fürt tárterületén.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Adatok másolása a helyi tárolóból a fürt tárolójába.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. A fürt tárterületén lévő könyvtár tartalmának listázása.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
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
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsight-integráció Data Lake Storage Gen2 előzetes verzióval – ACL és biztonsági frissítés](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Az Azure Data Lake Storage Gen2 bemutatása](../storage/blobs/data-lake-storage-introduction.md)
* [Oktatóanyag: adatok kinyerése, átalakítása és betöltése az Azure HDInsight interaktív lekérdezés használatával](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
