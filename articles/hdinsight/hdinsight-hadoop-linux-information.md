---
title: Tippek a Hadoop használatával a Linux-alapú HDInsight – Azure
description: A jól ismert Linux-környezet az Azure-felhőben futó Linux-alapú HDInsight (Hadoop) fürtöket használó megvalósítás tippek beolvasása.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 2a7af59495966c76a47ea84311ab073eb594f82e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765729"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Információk a HDInsight Linuxon való használatáról

Az Azure HDInsight-fürtök a jól ismert Linux-környezet, az Azure-felhőben futó adja meg az Apache Hadoop. A legtöbb dolgot, használható megegyeznek a más Hadoop-a-Linux-telepítés. Ez a dokumentum felhívja, akkor célszerű tisztában lennie a konkrét különbségeket.

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Előfeltételek

A jelen dokumentumban leírt lépések számos használja az alábbi segédprogramokat, és előfordulhat, hogy a rendszer telepíteni kell.

* [cURL](https://curl.haxx.se/) – webes szolgáltatások folytatott kommunikációhoz használatos.
* **jq**, egy parancssori JSON feldolgozó.  Lásd: [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).
* [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) – távoli kezelése az Azure-szolgáltatások használatos.
* **Egy SSH-ügyfél**. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Felhasználók

Kivéve, ha [tartományhoz csatlakoztatott](./domain-joined/apache-domain-joined-introduction.md), HDInsight figyelembe kell venni egy **egyfelhasználós** rendszer. A fürt rendszergazdai szintű engedélyekkel rendelkező SSH egyetlen felhasználói fiók jön létre. További SSH-fiókok hozhatók létre, de a fürt rendszergazdai hozzáféréssel is rendelkeznek.

Tartományhoz csatlakoztatott HDInsight támogatja egyszerre több felhasználó és részletesebb engedélyeire és szerepkört beállításait. További információkért lásd: [kezelése tartományhoz csatlakoztatott HDInsight-fürtök](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Tartománynevek

A teljesen minősített tartománynevét (FQDN) az internetről a fürthöz való csatlakozás során használandó van `CLUSTERNAME.azurehdinsight.net` vagy `CLUSTERNAME-ssh.azurehdinsight.net` (a csak SSH-).

Belsőleg a fürt egyes csomópontjaihoz fürt konfigurálása során hozzárendelt névvel rendelkezik. A fürt nevének megkereséséhez tekintse meg a **gazdagépek** az Ambari webes Kezelőfelületen oldalán. Az Ambari REST API-ból az állomások listájához való visszatéréshez is használhatja a következő:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Cserélje le a `CLUSTERNAME` elemet a fürt nevére. Amikor a rendszer kéri, adja meg a jelszót a rendszergazdai fiók. Ez a parancs egy JSON-dokumentum, amely tartalmazza a fürtben lévő gazdagépek listáját adja vissza. [jq](https://stedolan.github.io/jq/) használatával olvasható ki a `host_name` elemérték minden állomás számára.

Ha meg kell keresnie a csomópont nevét egy adott szolgáltatáshoz, az Ambari lekérdezheti az adott összetevő. A gazdagépet keressen a HDFS-név csomópont, például használja a következő parancsot:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Ez a parancs visszaadja a szolgáltatás leíró JSON-dokumentumok, majd [jq](https://stedolan.github.io/jq/) lekéri csak a `host_name` érték a gazdagépek számára.

## <a name="remote-access-to-services"></a>Távoli szolgáltatásokhoz való hozzáférés

* **Ambari (web)** - https://CLUSTERNAME.azurehdinsight.net

    A fürt rendszergazdai felhasználónév és jelszó használatával hitelesíteni, és az Ambari, majd jelentkezzen be.

    Hitelesítés egyszerű szövegként – mindig HTTPS PROTOKOLLT használnak annak biztosítására, hogy a kapcsolat nem biztonságos.

    > [!IMPORTANT]  
    > Néhány előkészíthetik elérhető az Ambari webes fér hozzá a csomópontok egy belső tartománynév használatával. Tartománynevek belső nem érhetők el nyilvánosan az interneten keresztül. "A kiszolgáló nem található" hiba jelenhet meg az interneten keresztül egyes szolgáltatások elérésére tett kísérlet során.
    >
    > Az Ambari webes felület összes funkciójának használatához egy SSH-alagutat a proxy webes forgalom, a fürt fő csomópontjának. Lásd: [használata SSH-bújtatással való eléréséről az Apache Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie és egyéb webes](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > A fürt rendszergazdai felhasználónév és jelszó használatával hitelesíteni.
    >
    > Hitelesítés egyszerű szövegként – mindig HTTPS PROTOKOLLT használnak annak biztosítására, hogy a kapcsolat nem biztonságos.

* **WebHCat (Templeton)** - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > A fürt rendszergazdai felhasználónév és jelszó használatával hitelesíteni.
    >
    > Hitelesítés egyszerű szövegként – mindig HTTPS PROTOKOLLT használnak annak biztosítására, hogy a kapcsolat nem biztonságos.

* **SSH** -CLUSTERNAME-ssh.azurehdinsight.net port 22-es vagy 23. Csatlakozzon az elsődleges átjárócsomóponthoz, míg 23 szeretne csatlakozni a másodlagos 22-es port szolgál. Az átjárócsomópontokkal további információkért lásd: [rendelkezésre állás és megbízhatóság az Apache Hadoop-fürtök a HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > A fürt átjárócsomópontjaiból ssh-n keresztül egy ügyfél gépéről csak elérni. Miután csatlakozott, ezután hozzáférhetnek a feldolgozó csomópontok egy átjárócsomópontjával SSH használatával.

További információkért lásd: a [HDInsight az Apache Hadoop-szolgáltatások által használt portok](hdinsight-hadoop-port-settings-for-services.md) dokumentumot.

## <a name="file-locations"></a>Fájlok helye

Hadoop-kapcsolatos fájlok találhatók a fürtcsomópontokon, `/usr/hdp`. Ez a könyvtár a következő alkönyvtárakat tartalmaz:

* **2.6.5.3006-29**: A könyvtár neve a Hortonworks Data platform HDInsight által használt verziója. A szám a fürtön fut, az itt felsorolt eltérő lehet.
* **current**: Ez a könyvtár alkönyvtárat mutató hivatkozásokat tartalmaz a **2.6.5.3006-29** könyvtár. Ez a könyvtár létezik-e, hogy ne felejtse el a verziószám nem kell.

Hadoop elosztott fájlrendszer címen található példa adatok és a JAR-fájlok `/example` és `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, az Azure Storage és Data Lake Storage

A legtöbb Hadoop-disztribúció az adatokat HDFS, a gépek a fürt helyi tároló biztonsági mentése tárolódik. Helyi tároló használata kell egy felhőalapú megoldás, óránként vagy percenként számítási erőforrások díjkötelesek.

HDInsight használatakor az adatfájlokat a felhőben az Azure Blob Storage és Azure Data Lake Storage igény szerint méretezhető és rugalmas úgy vannak tárolva. Ezek a szolgáltatások a következő előnyöket nyújtják:

* Költséghatékony hosszú távú tároláshoz.
* Kisegítő lehetőségek a külső szolgáltatások, például a websites, a fájl feltöltése/letöltése segédprogramok, a különböző nyelvű SDK és a böngészők.
* Nagyméretű fájlok kapacitást és nagy méretezhető tárhely.

További információkért lásd: [ismertetése blobok](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) és [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

Azure Storage vagy a Data Lake Storage használatakor nem kell semmit speciális a HDInsight férhet hozzá az adatokhoz. Ha például a következő parancs felsorolja a fájlok a `/example/data` függetlenül attól, hogy tárolása Azure Storage vagy a Data Lake Storage mappában:

    hdfs dfs -ls /example/data

A HDInsight az adatok tárolási erőforrások (Azure Blob Storage és Azure Data Lake Storage) vannak választva a számítási erőforrásokat. Ezért hozhat létre HDInsight-fürtök számítási ehhez az szükséges, és később törli a fürtöt, a munka végeztével, eközben tartja az adatfájlokat biztonságosan a felhőbeli tárban őrzött mindaddig, amíg van szüksége.


### <a name="URI-and-scheme"></a>URI-t és a séma

Egyes parancsok szükség lehet, hogy adja meg a rendszer az URI-t részeként a fájl elérése közben. Ha például a Storm-HDFS összetevő megköveteli, hogy adja meg a rendszer. A nem alapértelmezett tároló ("kiegészítő" tárolóként hozzáadni a fürt tárolás), ha mindig kell használni a rendszer az URI-t részeként.

Használata esetén __Azure Storage__, a következő URI sémák egyikével:

* `wasb:///`: Alapértelmezett tárolók titkosítatlan kommunikáció használata.

* `wasbs:///`: Alapértelmezett tárolók használatával titkosított kommunikáció.  A wasbs sémát csak a HDInsight 3.6-os verziója és újabb verziók támogatják.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Egy nem alapértelmezett tárfiókhoz való kommunikáció során használt. Például ha rendelkezik egy tárfiókot, vagy ha nyilvánosan elérhető-e a storage-fiókban lévő adatok elérése tárolja.

Használata esetén __Azure Data Lake Storage Gen2__, a következő URI sémák egyikével:

* `abfs:///`: Alapértelmezett tárolók titkosítatlan kommunikáció használata.

* `abfss:///`: Alapértelmezett tárolók használatával titkosított kommunikáció.  A abfss séma csak a HDInsight 3.6-os verziója és újabb verziók esetében támogatott.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Egy nem alapértelmezett tárfiókhoz való kommunikáció során használt. Például ha rendelkezik egy tárfiókot, vagy ha nyilvánosan elérhető-e a storage-fiókban lévő adatok elérése tárolja.

Használata esetén __Azure Data Lake Storage Gen1__, a következő URI sémák egyikével:

* `adl:///`: A fürt alapértelmezett Data Lake Storage eléréséhez.

* `adl://<storage-name>.azuredatalakestore.net/`: Egy nem alapértelmezett Data Lake Storage való kommunikáció során használt. A HDInsight-fürt gyökérkönyvtárában kívüli adatok eléréséhez is használt.

> [!IMPORTANT]  
> Data Lake Storage a HDInsight alapértelmezett tárolóként használatakor meg kell adnia egy elérési utat a tárolóban, a legfelső szintű HDInsight tároló használatára. Az alapértelmezett elérési út `/clusters/<cluster-name>/`.
>
> Használata esetén `/` vagy `adl:///` adatok eléréséhez, csak az adatok eléréséhez a legfelső szintű tárolja (például `/clusters/<cluster-name>/`) a fürt. Hozzáférés az adatokhoz bárhol a tárolóban, használja a `adl://<storage-name>.azuredatalakestore.net/` formátumban.

### <a name="what-storage-is-the-cluster-using"></a>Milyen tárolási a fürt használ

Az Ambari segítségével lekérni a fürt alapértelmezett tárolási konfigurációját. A következő paranccsal a curl használatával HDFS konfigurációs információk lekéréséhez és a szűrő [jq](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Ez a parancs visszaadja a alkalmazni a kiszolgáló első konfiguráció (`service_config_version=1`), amely tartalmazza ezt az információt. Szükség lehet a listában keresse meg a legújabb összes konfigurációs verziókat.

Ez a parancs egy értéket ad vissza a következő URI-k hasonló:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Ha az Azure Storage-fiók használatával.

    A fiók neve az Azure Storage-fiók neve. A tároló neve a blob-tároló, amely a fürttárolóhoz gyökere.

* `adl://home` Ha az Azure Data Lake Storage használatával. A Data Lake Storage nevének lekéréséhez használja az alábbi REST-hívás:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Ez a parancs visszaadja a következő állomásnév: `<data-lake-store-account-name>.azuredatalakestore.net`.

    A könyvtár a tárolóban, amely a HDInsight a legfelső szintű lekéréséhez használja az alábbi REST-hívás:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Ez a parancs hasonló ad vissza egy elérési utat a következő elérési úton: `/clusters/<hdinsight-cluster-name>/`.

A storage-információkat az alábbi lépéseket követve az Azure portal használatával is talál:

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki a HDInsight-fürtöt.

2. Az a **tulajdonságok** szakaszban jelölje be **Tárfiókok**. A tároló a fürt jelennek meg.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hogyan érhetem el fájlokat a külső HDInsight

A HDInsight-fürtön kívülről származó adatok eléréséhez a különböző módja van. Néhány hivatkozások segédprogramok és SDK-k segítségével az adatok a következők:

Ha használ __Azure Storage__, tekintse meg a következő hivatkozások módon, hogy érheti el az adatokat:

* [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Működik az Azure parancssori felületi parancsokkal. Telepítés után használja a `az storage` a Súgó a tároló, a parancs vagy `az storage blob` blob-specifikus parancsokhoz.
* [blobxfer.py](https://github.com/Azure/blobxfer): Az Azure Storage-blobokkal dolgozik a python-szkriptet.
* Különböző SDK-k:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Tárolási REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Ha használ __Azure Data Lake Storage__, tekintse meg a következő hivatkozások módon, hogy érheti el az adatokat:

* [Webböngésző](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [A Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>A fürt méretezése

A funkció a fürtméretezés lehetővé teszi a dinamikus módosítását az adatok csomópontok egy fürt által használt. A méretezési műveletek közben egyéb feladatokat is végezhet, vagy a folyamatok a fürtön futnak.  Lásd még a [méretezési HDInsight-fürtök](./hdinsight-scaling-best-practices.md)

A különböző fürttípusok vannak hatással a méretezés az alábbiak szerint:

* **Hadoop**: Vertikális a fürtben található csomópontok számát a fürtben a szolgáltatások újra lesz indítva. Méretezési műveletek okozhat feladatok fut, vagy függőben van a skálázási művelet befejezése után sikertelen lesz. A művelet befejeződése után lehetősége van újraküldeni a feladatokat.
* **A HBase**: Regionális kiszolgálók a rendszer automatikusan kiegyensúlyozott néhány percen belül, a skálázási művelet befejezése után. Manuálisan elosztása regionális kiszolgálók, használja az alábbi lépéseket:

    1. A HDInsight-fürthöz SSH használatával csatlakozhat. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. A HBase rendszerhéj elindításához használja a következő:

            hbase shell

    3. Miután betöltötte a HBase rendszerhéj, használja a következő regionális kiszolgálók manuálisan elosztása érdekében:

            balancer

* **A Storm**: Minden futó Storm-topológiák érdemes kiegyenlíteni a skálázási művelet végrehajtását követően. Az újraegyensúlyozás lehetővé teszi a topológia állítsa be újra a párhuzamossági beállítások a fürtben található csomópontok új száma alapján. Futó topológiák újraegyensúlyozására, használja az alábbi lehetőségek egyikét:

    * **SSH**: Csatlakozás a kiszolgálóhoz, és használja az alábbi parancsot a topológia újraegyensúlyozására:

            storm rebalance TOPOLOGYNAME

        Adja meg a paramétereket a eredetileg biztosítják a topológia párhuzamosságát mutatók felülbírálása is. Ha például `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` újrakonfigurálja a topológia 5 munkavégző folyamatok, a kék-spout összetevő 3 végrehajtóval és 10 végrehajtóval a sárga-bolt-összetevő.

    * **A Storm felhasználói felület**: Használja az alábbi lépéseket a topológia a Storm kezelői felülettel újraegyensúlyozására.

        1. Nyissa meg `https://CLUSTERNAME.azurehdinsight.net/stormui` webböngészőben, ahol `CLUSTERNAME` a Storm-fürt neve. Ha a rendszer kéri, adja meg a HDInsight-fürt (rendszergazda) nevét és a fürt létrehozásakor megadott jelszót.
        2. Válassza ki a kívánt újraegyensúlyozására, majd válassza ki a topológiát a **Újraegyensúlyozására** gombra. Adja meg a késés, az újraegyensúlyozási művelet végrehajtása előtt.

* **A Kafka**: Méretezési műveleteket követően érdemes kiegyenlíteni a partíciók replikáit. További információkért lásd: a [magas rendelkezésre állás az Apache Kafka on HDInsight adatok](./kafka/apache-kafka-high-availability.md) dokumentumot.

A HDInsight-fürt méretezéssel kapcsolatos információkért lásd:

* [A HDInsight Apache Hadoop-fürtök kezelése az Azure portal használatával](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [A HDInsight Apache Hadoop-fürtök kezelése az Azure PowerShell használatával](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hogyan kell telepíteni a Hue (vagy más Hadoop-összetevők)?

HDInsight egy olyan felügyelt szolgáltatás. Azure-hoz a fürt probléma, ha, előfordulhat, hogy törölje a hibás csomópontot, és cserélje le a csomópontot hozhat létre. Ha manuálisan telepíti a fürtön dolog, azok nem megmaradnak a művelet esetén. Ehelyett használjon [HDInsight Parancsfájlműveletek](hdinsight-hadoop-customize-cluster-linux.md). Szkriptműveletet használható a következő módosításokat:

* Telepítsen és konfiguráljon egy szolgáltatás vagy a webhelyet.
* Telepítsen és konfiguráljon egy összetevő, amely a szükséges konfigurációs módosítások a fürtben több csomóponton.

A Szkriptműveletek olyan Bash-szkriptek. A szkriptek a fürt létrehozásakor futtassa, és telepítse és konfigurálja a további összetevők szolgálnak. A következő összetevők telepítéséhez parancsfájlpéldákat biztosítja:

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

Az egyéni parancsfájlművelet-fejlesztéssel kapcsolatos további információkért lásd: [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

### <a name="jar-files"></a>JAR-fájlok

Néhány Hadoop-technológiák, amelyek tartalmazzák a használt részeként egy MapReduce-feladatot, vagy a funkció önálló jar-fájlok találhatók a Pig- vagy Hive belül. Azok gyakran nem szükséges minden olyan beállítás, és a létrehozása után a fürtbe feltöltött és közvetlenül használni. Ha azt szeretné, hogy az összetevő survives rendszerképének alaphelyzetbe állítása, a fürt, tárolható a jar-fájlt az alapértelmezett tároló (WASB vagy ADL) a fürt számára.

Például, ha a legújabb verzióját használni kívánt [Apache DataFu](https://datafu.incubator.apache.org/), egy a projektet tartalmazó jar letöltheti és töltse fel a HDInsight-fürthöz. Ezután kövesse a DataFu dokumentáció a Pig- vagy Hive használatával.

> [!IMPORTANT]  
> Néhány összetevőt, különálló jar-fájlok a HDInsight által biztosított, de nem szerepelnek az elérési út. Egy adott összetevőre keres, ha a fürtön lévő keresése a következő használatával:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Ez a parancs minden egyező jar-fájlok elérési útját adja vissza.

Szeretné egy összetevő egy másik verzióját használni, töltse fel a verzióra van szükség, és használhatja a feladatok.

> [!IMPORTANT]
> A HDInsight-fürthöz megadott összetevők teljes mértékben támogatottak, és Support segít elkülöníteni, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők annak érdekében, hogy a probléma további hibaelhárításához üzletileg ésszerű támogatást kapnak. Emiatt előfordulhat, hogy a probléma megoldásához vagy rákérdez arra, hogy a nyílt forráskódú technológiák, ahol található részletes szakértelmét, hogy a technológiát a rendelkezésre álló csatorna léphet. Például sok, használható, például közösségi helyek vannak: [A HDInsight az MSDN-fórumokra](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Is Apache projektek rendelkeznek projekt helyek [ https://apache.org ](https://apache.org), például: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>További lépések

* [Áttelepítés a Windows-alapú HDInsight a Linux-alapú](hdinsight-migrate-from-windows-to-linux.md)
* [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hadoop/hdinsight-use-mapreduce.md)
