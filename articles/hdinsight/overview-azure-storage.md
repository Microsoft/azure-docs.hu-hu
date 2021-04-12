---
title: Az Azure Storage áttekintése a HDInsight-ben
description: Az Azure Storage áttekintése a HDInsight-ben.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 419b4939c19ee03651a406db44244897e17087f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865468"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Az Azure Storage áttekintése a HDInsight-ben

Az Azure Storage egy robusztus általános célú tárolási megoldás, amely zökkenőmentesen integrálható a HDInsight. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. A HDFS felületen keresztül a HDInsight-összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan, blobként tárolt adaton.

Javasoljuk, hogy az alapértelmezett fürtöt tárolóhoz és az üzleti adataihoz külön tároló-tárolókat használjon. A szétválasztás az, hogy elkülönítse a HDInsight-naplókat és az ideiglenes fájlokat a saját üzleti adataiból. Javasoljuk továbbá, hogy törölje az alapértelmezett BLOB-tárolót, amely az alkalmazás-és rendszernaplókat tartalmazza, miután mindegyik használattal csökkenti a tárolási költségeket. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Ha úgy dönt, hogy védi a Storage-fiókot a **tűzfalakkal és a virtuális hálózatokkal** kapcsolatos korlátozásokkal a **kiválasztott hálózatokon**, ügyeljen arra, hogy a kivételt engedélyezze a **megbízható Microsoft-szolgáltatások számára**... A kivétel az, hogy a HDInsight hozzáférhessen a Storage-fiókhoz.

## <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra

Az alábbi ábra az Azure Storage HDInsight architektúrájának absztrakt nézetét tartalmazza:

:::image type="content" source="./media/overview-azure-storage/storage-architecture.png" alt-text="HDInsight-tár architektúra" border="false":::

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

`hdfs://<namenodehost>/<path>`

A HDInsight-on keresztül az Azure Storage-ban is elérhetők az adattárolók. A szintaxis a következő:

`wasb://<containername>@<accountname>.blob.core.windows.net/<path>`

HDInsight-fürtökkel rendelkező Azure Storage-fiók használata esetén vegye figyelembe a következő alapelveket:

* **Fürthöz csatlakozó tárolók a tárfiókokban:** Mivel a fiók neve és kulcsa társítva van a fürttel a létrehozás során, teljes hozzáféréssel rendelkezik a tárolókban lévő összes blobhoz.

* **Nyilvános tárolók vagy nyilvános Blobok olyan Storage-fiókokban, amelyek nem csatlakoznak a fürthöz:** Csak olvasási jogosultsággal rendelkezik a tárolóban lévő blobokhoz.
  
  > [!NOTE]
  > A nyilvános tárolók lehetővé teszik az adott tárolóban elérhető Blobok listájának lekérését és a tároló metaadatainak beolvasását. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információ: [Névtelen olvasási hozzáférés tárolók és Blobok kezelésére](../storage/blobs/anonymous-read-access-configure.md).

* A **fürthöz nem kapcsolódó Storage-fiókokban lévő privát tárolók:** A tárolóban lévő Blobok csak akkor érhetők el, ha a Webhcaten-feladatok elküldésekor a Storage-fiókot definiálja.

A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. A HDInsight alapértelmezés szerint a core-site.xml fájlban meghatározott tárolási fiókokat használja. Ezt a beállítást az [Apache Ambari](./hdinsight-hadoop-manage-ambari.md)használatával lehet módosítani.

Több Webhcaten-feladat, beleértve a Apache Hive. A MapReduce, a Apache Hadoop streaming és az Apache Pig szolgáltatásban a Storage-fiókok és-metaadatok leírását is elvégezheti. (Ez a szempont jelenleg a Storage-fiókkal rendelkező Pig esetében igaz, a metaadatok esetében azonban nem.) További információ: [HDInsight-fürt használata másodlagos Storage-fiókokkal és-metaadattárak](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

A blobok a strukturált és strukturálatlan adatokhoz használhatók. A blob-tárolók az értékeket kulcs/érték párokként tárolják, és nem rendelkeznek címtár-hierarchiával. A kulcs neve azonban tartalmazhat perjel karaktert (/) úgy, hogy úgy jelenjen meg, mintha egy fájl egy könyvtár struktúráján belül tárolódik. A blob kulcsa például lehet `input/log1.txt` . Nem `input` létezik tényleges könyvtár, de a kulcs neve perjel karaktere miatt a kulcs egy fájl elérési útjára hasonlít.

## <a name="benefits-of-azure-storage"></a>Az Azure Storage előnyei

A nem közösen elhelyezett számítási fürtök és tárolási erőforrások implicit teljesítménnyel kapcsolatos költségekkel rendelkeznek. Ezeket a költségeket a számítási fürtök az Azure-régióban található Storage-fiók erőforrásaihoz közeledő módon történő létrehozásával csökkentik. Ebben a régióban a számítási csomópontok hatékonyan férhetnek hozzá az adatokhoz az Azure Storage-on belüli nagy sebességű hálózaton keresztül.

Ha a HDFS helyett az Azure Storage-ban tárolja az adattárolást, számos előnnyel jár:

* **Adatok újbóli használata és megosztása:** A HDFS-ben az adatok a számítási fürtön belül találhatóak. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Storage-ban tárolt adatok ezzel szemben a HDFS API-kon vagy a blob Storage REST API-kon keresztül érhetők el. Ennek a megoldásnak a miatt a nagyobb alkalmazások (beleértve az egyéb HDInsight-fürtöket) és az eszközök is használhatók az adatfeldolgozásra és-felhasználásra.

* **Adatarchiválás:** Ha az Azure Storage-ban tárolják az adattárolást, a számításhoz használt HDInsight-fürtöket a felhasználói adatvesztés nélkül lehet biztonságosan törölni.

* **Adattárolási díj:** Az adatelosztott fájlrendszerbeli adattárolás költségesebb, mint az Azure Storage-ban tárolt adattárolási szolgáltatás. Mivel a számítási fürt díja meghaladja az Azure Storage költségeit. Továbbá, mivel az adatoknak nem kell újratölteniük minden számítási fürt létrehozásakor, az adattöltés költségeit is menteni kell.

* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a léptéket a fürthöz létrehozott csomópontok száma határozza meg. A skála módosítása bonyolultabb lehet, mint az Azure Storage-ban automatikusan elérhető rugalmas skálázási képességek.

* **Geo-replikáció:** Az Azure Storage-t földrajzilag lehet replikálni. Bár a Geo-replikáció földrajzi helyreállítást és adatredundanciát biztosít, a földrajzilag replikált helyre történő feladatátvétel jelentősen befolyásolja a teljesítményt, és további költségeket eredményezhet. Ezért válassza a Geo-replikációt óvatosan, és csak akkor, ha az adatmennyiség indokolja a további költségeket.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem szeretne az Azure Storage-ban tárolni. Ebben az esetben dönthet úgy, hogy az adattárolást a helyi HDFS tárolja. A HDInsight az elosztott fájlrendszert használja több ilyen köztes eredményhez a kaptár-feladatokban és más folyamatokban.

> [!NOTE]  
> A legtöbb HDFS-parancs (például,, `ls` `copyFromLocal` és `mkdir` ) a várt módon működik az Azure Storage-ban. Az Azure Storage-ban csak a natív HDFS-implementációra (azaz a DFS-re) vonatkozó parancsok láthatók `fschk` `dfsadmin` .

## <a name="next-steps"></a>Következő lépések

* [Az Azure Data Lake Storage Gen2 bemutatása](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
* [Az Azure Data Lake Storage Gen1 áttekintése](./overview-data-lake-storage-gen1.md)
