---
title: Biztonsági mentési & replikáció az Apache HBase, Phoenix – Azure HDInsight
description: Az Apache HBase és a Apache Phoenix Azure HDInsight való biztonsági mentésének és replikálásának beállítása
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 1d5bcf9c04ad02eaf297f8971aa0f4ff599888c7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942990"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>A biztonsági mentés és a replikálás beállítása az Apache HBase és a Apache Phoenix on HDInsight

Az Apache HBase az adatvesztés elleni védelem számos módszerét támogatja:

* A `hbase` mappa másolása
* Exportálás, importálás
* Táblák másolása
* Pillanatképek
* Replikáció

> [!NOTE]  
> Apache Phoenix a metaadatokat a HBase-táblákban tárolja, így a metaadatok biztonsági mentést készítenek a HBase Rendszerkatalógus-tábláiról.

A következő szakaszok ismertetik az egyes megközelítések használati forgatókönyvét.

## <a name="copy-the-hbase-folder"></a>A hbase mappa másolása

Ezzel a módszerrel az összes HBase-adatait másolja, anélkül, hogy ki kellene választania a táblák vagy az oszlop családjának egy részhalmazát. A későbbi megközelítések nagyobb szabályozást tesznek lehetővé.

A HDInsight HBase a fürt létrehozásakor kiválasztott alapértelmezett tárolót használja, vagy az Azure Storage-blobokat vagy Azure Data Lake Storage. Mindkét esetben a HBase a következő elérési úton tárolja az adat-és metaadat-fájljait:

`/hbase`

* Egy Azure Storage-fiókban a `hbase` mappa a blob-tároló gyökerében található:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* Azure Data Lake Storage a mappa a `hbase` fürt kiépítés során megadott gyökér elérési útjában található. Ennek a gyökér elérési útnak általában egy `clusters` mappája van, amely a HDInsight-fürt után megnevezett almappát tartalmaz:

  `/clusters/<clusterName>/hbase`

Mindkét esetben a `hbase` mappa tartalmazza a HBase által a lemezre kiürített összes olyan adatmennyiséget, amelyek nem tartalmazzák a memóriában tárolt adatmennyiséget. Ahhoz, hogy ezt a mappát a HBase adatok pontos ábrázolására lehessen támaszkodni, le kell állítania a fürtöt.

A fürt törlése után meghagyhatja az adott helyet, vagy átmásolhatja az új helyre:

* Hozzon létre egy új HDInsight-példányt, amely az aktuális tárolási helyre mutat. Az új példány az összes meglévő adattal együtt jön létre.

* Másolja a `hbase` mappát egy másik Azure Storage blob-tárolóba vagy Data Lake Storage helyre, majd indítson el egy új fürtöt az adott adattal. Az Azure Storage esetében használja az [AzCopy](../../storage/common/storage-use-azcopy-v10.md)-t, és Data Lake Storage használja a [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)-t.

## <a name="export-then-import"></a>Exportálás, importálás

A forrás HDInsight-fürtön használja az [Exportálás segédprogramot](https://hbase.apache.org/book.html#export) (a HBase-ben) az adatok exportálásához a forrástábla és az alapértelmezett csatlakoztatott tároló között. Ezután átmásolhatja az exportált mappát a cél tárolóhelyre, és futtathatja az [importálási segédprogramot](https://hbase.apache.org/book.html#import) a cél HDInsight-fürtön.

A tábla adatai exportálásához először SSH-t a forrás HDInsight-fürt fő csomópontjára, majd futtassa a következő `hbase` parancsot:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

Az exportálási könyvtárnak még nem kell létezni. A tábla neve megkülönbözteti a kis-és nagybetűket.

A tábla adatai importálásához az SSH-t a cél HDInsight-fürt fő csomópontjára, majd futtassa a következő `hbase` parancsot:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

A táblának már léteznie kell.

Az alapértelmezett tárolóhoz vagy a csatlakoztatott tárolási beállításokhoz tartozó teljes exportálási útvonal megadása. Például az Azure Storage-ban:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

A Azure Data Lake Storage Gen2 szintaxisa a következőket eredményezi:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

A Azure Data Lake Storage Gen1 szintaxisa a következőket eredményezi:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Ez a megközelítés táblázatos részletességgel rendelkezik. Megadhat egy dátumtartományt is a befoglalni kívánt sorokhoz, ami lehetővé teszi a folyamat növekményes végrehajtását. A UNIX-kor óta minden dátum ezredmásodpercben van.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Vegye figyelembe, hogy az exportálandó sorok verziószámát meg kell adnia. A dátumtartomány összes verziójának belefoglalásához állítsa a `<numberOfVersions>` megengedettnél nagyobb értéket (például 100000).

## <a name="copy-tables"></a>Táblák másolása

A [CopyTable segédprogram](https://hbase.apache.org/book.html#copy.table) a forrás táblából, egy sorba, egy meglévő, a forrással megegyező sémával rendelkező céltábla adatait másolja. A cél tábla lehet ugyanazon a fürtön vagy egy másik HBase-fürtön is. A táblák nevei megkülönböztetik a kis-és nagybetűket.

Ha a CopyTable-t fürtön belül szeretné használni, SSH-t a forrás HDInsight-fürt fő csomópontjára, majd futtassa a következő `hbase` parancsot:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Ha a CopyTable-t egy másik fürt egyik táblájába kívánja másolni, adja hozzá a `peer` kapcsolót a célként megadott fürt címe:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

A cél címe a következő három részből áll:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` Apache ZooKeeper csomópontok vesszővel tagolt listája, például:

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net, zk4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net, zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` a HDInsight alapértelmezett értéke 2181, `<ZnodeParent>` `/hbase-unsecure` így a teljes érték a `<destinationAddress>` következő:

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net, zk4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net, zk3-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net: 2181:/hbase-unsecure

A HDInsight-fürt értékeinek beolvasásával kapcsolatban lásd a jelen cikk [Apache ZooKeeper Kvórumának manuális összegyűjtését](#manually-collect-the-apache-zookeeper-quorum-list) ismertető cikket.

A CopyTable segédprogram emellett a paramétereket is támogatja a másolandó sorok időtartományának megadásához, valamint a másolandó táblázatos családok részhalmazának megadásához. A CopyTable által támogatott paraméterek teljes listájának megjelenítéséhez paraméterek nélkül futtassa a CopyTable:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

A CopyTable megvizsgálja a teljes forrástábla tartalmát, amelyet a rendszer átmásol a célhelyre. Ez csökkentheti a HBase-fürt teljesítményét a CopyTable végrehajtása közben.

> [!NOTE]  
> Az adatok táblák közötti másolásának automatizálásához tekintse `hdi_copy_table.sh` meg a szkriptet az [Azure HBase utils](https://github.com/Azure/hbase-utils/tree/master/replication) adattárában a githubon.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>A Apache ZooKeeper kvórumok listájának manuális összegyűjtése

Ha mindkét HDInsight-fürt ugyanabban a virtuális hálózatban található, ahogy azt korábban már említettük, a belső állomásnév feloldása automatikus. Ha a CopyTable-t a HDInsight-fürtökhöz két különálló, VPN Gateway által csatlakoztatott virtuális hálózaton szeretné használni, meg kell adnia a kvórum Zookeeper-csomópontjainak gazdagép IP-címeit.

A kvórum állomásneve a következő curl-parancs futtatásával szerezhető be:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

A curl-parancs egy JSON-dokumentumot kér le a HBase konfigurációs adataival, és a GREP parancs csak a "HBase. Zookeeper. kvórum" bejegyzést adja vissza, például:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

A kvórum állomásneve érték a kettőspont jobb oldalán lévő teljes sztring.

A gazdagépek IP-címeinek lekéréséhez használja a következő cURL-parancsot az előző lista minden gazdagépéhez:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

Ebben a curl-parancsban `<zookeeperHostFullName>` a egy ZooKeeper-gazdagép teljes DNS-neve, például a példa `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net` . A parancs kimenete tartalmazza a megadott gazdagép IP-címét, például:

`100    "ip" : "10.0.0.9",`

Miután összegyűjtötte az IP-címeket a kvórum összes ZooKeeper-csomópontján, építse újra a cél címét:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

A jelen példában:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Pillanatképek

A [Pillanatképek](https://hbase.apache.org/book.html#ops.snapshots) lehetővé teszik a HBase adattárban lévő adatbiztonsági másolat készítését. A pillanatképek minimális terheléssel rendelkeznek, és másodpercek alatt elvégezhető, mert egy pillanatkép-művelet gyakorlatilag egy metaadat-művelet, amely az adott pillanatban tárolja a tárolóban lévő összes fájl nevét. A pillanatképek időpontjában nem másolhatók tényleges adatok. A pillanatképek a HDFS tárolt adatok nem módosítható természetétől függenek, ahol a frissítések, a törlések és a beszúrások mind új adatokként jelennek meg. A pillanatképeket visszaállíthatja ugyanazon a fürtön, vagy exportálhatja a pillanatképet egy másik fürtre.

Pillanatkép létrehozásához SSH-t a HDInsight HBase-fürt fő csomópontjára, és indítsa el a `hbase` rendszerhéjat:

```console
hbase shell
```

A hbase-rendszerhéjon belül használja a pillanatkép-parancsot a tábla és a pillanatkép nevével:

```console
snapshot '<tableName>', '<snapshotName>'
```

Ha a rendszerhéjon belüli név alapján szeretné visszaállítani a pillanatképet `hbase` , először tiltsa le a táblát, majd állítsa vissza a pillanatképet, és engedélyezze újra a táblázatot:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

A pillanatkép új táblára való visszaállításához használja a clone_snapshot:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Ha egy pillanatképet HDFS szeretne exportálni egy másik fürt általi használatra, először hozza létre a pillanatképet a korábban leírtak szerint, majd használja a ExportSnapshot segédprogramot. Futtassa ezt a segédprogramot az SSH-munkamenetből a fő csomópontra, nem pedig a `hbase` rendszerhéjon belül:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

A a `<hdfsHBaseLocation>` forrás-fürt számára elérhető tárolóhelyek bármelyike lehet, és a célként megadott fürt által használt hbase mappára kell mutatnia. Ha például egy másodlagos Azure Storage-fiók van csatlakoztatva a forrás-fürthöz, és a fiók hozzáférést biztosít a célként megadott fürt alapértelmezett tárolójában használt tárolóhoz, akkor a következő parancsot használhatja:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Ha nem rendelkezik másodlagos Azure Storage-fiókkal a forrás-fürthöz, vagy ha a fürterőforrás egy helyszíni fürt (vagy nem HDI fürt), akkor előfordulhat, hogy engedélyezési problémákba ütközik, amikor megpróbál hozzáférni a HDI-fürt Storage-fiókjához. A probléma megoldásához adja meg a Storage-fiók kulcsát parancssori paraméterként, az alábbi példában látható módon. A Storage-fiók kulcsát a Azure Portal érheti el.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Ha a ADLS 2. generációs fürt, módosítsa az előző parancsot úgy, hogy az a 2. generációs ADLS által használt konfigurációkhoz igazodjon:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.<account_name>.dfs.core.windows.net=<key> -Dfs.azure.account.auth.type.<account_name>.dfs.core.windows.net=SharedKey -Dfs.azure.always.use.https.<account_name>.dfs.core.windows.net=false -Dfs.azure.account.keyprovider.<account_name>.dfs.core.windows.net=org.apache.hadoop.fs.azurebfs.services.SimpleKeyProvider -snapshot 'Snapshot1' -copy-to 'abfs://<container>@<account_name>.dfs.core.windows.net/hbase'
```

A Pillanatkép exportálása után az SSH-t a cél fürt fő csomópontjára helyezi, és visszaállíthatja a pillanatképet a `restore_snapshot` korábban leírtak szerint a paranccsal.

A pillanatképek a parancs időpontjában teljes biztonsági mentést biztosítanak a tábláról `snapshot` . A pillanatképek nem teszik lehetővé a növekményes Pillanatképek elvégzését a Windowsban, és nem határozzák meg a pillanatképbe foglalandó oszlopok családokat.

## <a name="replication"></a>Replikáció

A [HBase-replikáció](https://hbase.apache.org/book.html#_cluster_replication) automatikusan leküldi a tranzakciókat egy forráskiszolgálóról a célkiszolgálóra, és egy aszinkron mechanizmust használ, amely minimális terheléssel rendelkezik a forrás-fürtön. A HDInsight-ben beállíthatja a fürtök közötti replikációt, ahol:

* A forrás-és a cél fürtök ugyanabban a virtuális hálózatban találhatók.
* A forrás-és célhelyek fürtök a VPN-átjárók által összekapcsolt különböző virtuális hálózatokban találhatók, de mindkét fürt ugyanabban a földrajzi helyen található.
* A forrás-és a célhelyek fürtök a VPN-átjárók által csatlakoztatott különböző virtuális hálózatokban találhatók, és minden egyes fürt más földrajzi helyen található.

A replikáció beállításának általános lépései a következők:

1. A forráskiszolgálón hozza létre a táblákat, és töltse fel az adatokat.
2. A cél fürtön hozzon létre üres célhelyeket a forrástábla sémájával.
3. Regisztrálja a célként megadott fürtöt társként a forrás-fürtben.
4. Engedélyezze a replikációt a kívánt forrástábla-táblákon.
5. A meglévő adatok másolása a forrás tábláiból a célhelyek táblájába.
6. A replikáció automatikusan átmásolja az új adatmódosításokat a forrástáblaba.

A HDInsight replikálásának engedélyezéséhez alkalmazzon parancsfájl-műveletet a futó forrás HDInsight-fürtön. Az [Apache HBase-replikáció konfigurálásával](apache-hbase-replication.md)kapcsolatos útmutatót a fürtben lévő replikáció engedélyezéséhez, illetve a virtuális hálózatokban az Azure Resource Management-sablonok használatával létrehozott fürtözött fürtök replikálásának kísérletéhez című témakörben talál. A cikk a Phoenix-metaadatok replikálásának engedélyezésére vonatkozó utasításokat is tartalmaz.

## <a name="next-steps"></a>Következő lépések

* [Apache HBase-replikáció konfigurálása](apache-hbase-replication.md)
* [A HBase importálási és exportálási segédprogramjának használata](/archive/blogs/data_otaku/working-with-the-hbase-import-and-export-utility)
