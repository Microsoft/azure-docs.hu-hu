---
title: Fürt kapacitásának megtervezése az Azure HDInsight
description: Azonosíthatja az Azure HDInsight-fürtök kapacitásának és teljesítményének tervezésével kapcsolatos legfontosabb kérdéseket.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 45cfa90f2156dba87cbec2b6313bc24e5d030572
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933235"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight-fürtök kapacitástervezése

A HDInsight-fürt üzembe helyezése előtt tervezze meg a kívánt kapacitást a szükséges teljesítmény és skála meghatározásával. Ez a tervezés segít optimalizálni a használhatóságot és a költségeket. Az üzembe helyezést követően egyes szektorcsoport-kapacitási döntések nem módosíthatók. Ha a teljesítmény-paraméterek módosulnak, a fürtöket a tárolt adatok elvesztése nélkül lehet kibontani és újból létrehozni.

A kapacitás megtervezésére vonatkozó legfontosabb kérdések a következők:

* Melyik földrajzi régióban érdemes telepíteni a fürtöt?
* Mekkora tárterületre van szüksége?
* Milyen típusú fürtöt kell üzembe helyezni?
* Milyen méretű és típusú virtuális gépet (VM) használ a fürtcsomópontok?
* Hány munkavégző csomópontnak kell a fürtje?

## <a name="choose-an-azure-region"></a>Válasszon egy Azure-régiót

Az Azure-régió meghatározza, hogy a fürt hol van fizikailag kiépítve. Az olvasási és írási késés csökkentése érdekében a fürtnek az adatai közelében kell lennie.

A HDInsight számos Azure-régióban elérhető. A legközelebbi régió megtalálásához tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>A tárolási hely és a méret kiválasztása

### <a name="location-of-default-storage"></a>Alapértelmezett tároló helye

Az alapértelmezett tárolónak, vagy egy Azure Storage-fióknak vagy Azure Data Lake Storagenek a fürttel azonos helyen kell lennie. Az Azure Storage minden helyen elérhető. Data Lake Storage Gen1 bizonyos régiókban érhető el – tekintse meg az aktuális [Data Lake Storage rendelkezésre állást](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Meglévő adatterületek helye

Ha meglévő Storage-fiókot vagy Data Lake Storage szeretne használni a fürt alapértelmezett tárolójában, akkor a fürtöt ugyanazon a helyen kell telepítenie.

### <a name="storage-size"></a>Tárterület mérete

Egy üzembe helyezett fürtön további Azure Storage-fiókokat is csatolhat, vagy hozzáférhet más Data Lake Storagehoz. Az összes Storage-fióknak ugyanabban a helyen kell lennie, mint a fürtnek. Egy Data Lake Storage lehet egy másik helyen, bár a nagy távolságok némi késést okozhatnak.

Az Azure Storage [kapacitása korlátozott](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), míg Data Lake Storage Gen1 szinte korlátlan.

A fürtök különböző tárolási fiókok kombinációjából is hozzáférhetnek. A tipikus példák a következők:

* Ha az adatmennyiség várhatóan túllépi az egyetlen blob Storage-tároló tárolókapacitását.
* Ha a blob-tárolóhoz való hozzáférés sebessége túllépi a sávszélesség-szabályozási küszöbértéket.
* Ha az adatfeldolgozást szeretné végezni, már feltöltött egy BLOB-tárolót a fürt számára.
* Ha a biztonsági okokból el szeretné különíteni a tárterület különböző részeit, vagy leegyszerűsíti a felügyeletet.

A jobb teljesítmény érdekében csak egy tárolót használjon Storage-fiókkal.

## <a name="choose-a-cluster-type"></a>Fürt típusának kiválasztása

A fürt típusa határozza meg a HDInsight-fürt futtatására beállított munkaterhelést. A típusok közé tartoznak a következők: [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)vagy [Apache Spark](./spark/apache-spark-overview.md). A rendelkezésre álló fürtök típusának részletes ismertetését lásd: [Az Azure HDInsight bemutatása](hdinsight-overview.md#cluster-types-in-hdinsight). Minden egyes fürthöz tartozik egy adott központi telepítési topológia, amely a csomópontok méretére és számára vonatkozó követelményeket tartalmaz.

## <a name="choose-the-vm-size-and-type"></a>Válassza ki a virtuális gép méretét és típusát

Minden egyes fürt típusa csomópont típusú, és mindegyik csomópont típusa speciális beállításokkal rendelkezik a virtuálisgép-mérethez és-típushoz.

Az alkalmazás optimális méretének meghatározásához a fürt kapacitásának teljesítménytesztét és a jelzett méret növelését is megnövelheti. Használhat például egy szimulált munkaterhelést vagy egy *Kanári-lekérdezést* is. Futtassa a szimulált számítási feladatokat különböző méretű fürtökön. A kívánt teljesítmény eléréséig fokozatosan növelje a méretet. Egy Kanári-lekérdezést időnként beillesztheti a többi éles lekérdezésbe, hogy megjelenjen-e elegendő erőforrás a fürtben.

A számítási feladatok megfelelő virtuálisgép-családjának kiválasztásával kapcsolatos további információkért lásd: [a fürt megfelelő virtuálisgép-méretének kiválasztása](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>A fürt méretének kiválasztása

A fürt méretezését a virtuálisgép-csomópontok mennyisége határozza meg. Az összes fürt típusa esetén vannak olyan csomópont-típusok, amelyek egy adott léptéket és a kibővíthető csomópont-típusokat támogatják. Előfordulhat például, hogy egy fürt pontosan három [Apache ZooKeeper](https://zookeeper.apache.org/) csomópontot vagy két fő csomópontot igényel. A munkavégző csomópontok, amelyek elosztott módon végzik el az adatfeldolgozást a további feldolgozó csomópontok számára.

A fürt típusától függően a munkavégző csomópontok számának növelése további számítási kapacitást (például több magot) hoz létre. További csomópontok növelik a teljes fürt számára szükséges teljes memóriát a feldolgozott adatmennyiség-tárolás támogatásához. A virtuális gép méretének és típusának megválasztásához hasonlóan a megfelelő szektorcsoport-méretezést is megadhatja a rendszer. Szimulált számítási feladatok vagy Kanári-lekérdezések használata.

A fürt kibővíthető a maximális terhelési igények kielégítése érdekében. Ezután lekicsinyítheti a biztonsági mentést, ha a további csomópontok már nem szükségesek. Az automatikus [méretezési funkció](hdinsight-autoscale-clusters.md) lehetővé teszi, hogy előre meghatározott mérőszámok és időzítések alapján automatikusan méretezze a fürtöt. A fürtök kézi méretezésével kapcsolatos további információkért lásd: [HDInsight-fürtök méretezése](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Fürt életciklusa

A fürt élettartama után számítunk fel díjat. Ha csak bizonyos esetekben van szükség a fürtre, [hozzon létre igény szerinti fürtöket Azure Data Factory használatával](hdinsight-hadoop-create-linux-clusters-adf.md). Létrehozhat olyan PowerShell-parancsfájlokat is, amelyek kiépítik és törlik a fürtöt, majd az [Azure Automation](https://azure.microsoft.com/services/automation/)használatával ütemezhetik ezeket a parancsfájlokat.

> [!NOTE]  
> Fürt törlésekor az alapértelmezett Hive-metaadattár is törlődik. Ha meg szeretné őrizni a metaadattár a fürt következő újbóli létrehozásához, használjon külső metaadat-tárolót, például az Azure Database vagy az [Apache Oozie](https://oozie.apache.org/).

### <a name="isolate-cluster-job-errors"></a>A fürt feladataival kapcsolatos hibák elkülönítése

Időnként hibák merülhetnek fel több Térkép párhuzamos végrehajtása, valamint a több csomópontot tartalmazó fürt összetevőinek csökkentése miatt. A probléma elszigetelése érdekében próbálkozzon az elosztott teszteléssel. Egyidejű több feladat futtatása egyetlen feldolgozó csomópont-fürtön. Ezután bontsa ki ezt a megközelítést úgy, hogy egyszerre több feladatot futtasson egy több csomópontot tartalmazó fürtökön. Ha az Azure-ban egy egycsomópontos HDInsight-fürtöt szeretne létrehozni, használja a *`Custom(size, settings, apps)`* beállítást, és használja az 1 értéket a **fürt mérete** szakaszban lévő *munkavégző csomópontok* számára, ha új fürtöt szeretne kiépíteni a portálon.

## <a name="quotas"></a>Kvóták

Az előfizetési kvóták kezelésével kapcsolatos további információkért lásd: [kvóta növelésének kérése](quota-increase-request.md).

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök beállítása a Apache Hadoop-, Spark-, Kafka-és egyéb szolgáltatásokkal](hdinsight-hadoop-provision-linux-clusters.md): megtudhatja, hogyan állíthatja be és konfigurálhatja a fürtöket a HDInsight-ben.
* A [fürt teljesítményének figyelése](hdinsight-key-scenarios-to-monitor.md): megismerheti a HDInsight-fürt azon főbb forgatókönyveit, amelyek hatással lehetnek a fürt kapacitására.
