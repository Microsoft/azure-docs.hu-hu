---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 324d8b4c9fc53ca24e62fe339065d4452577cb1f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607218"
---
# <a name="azure-hdinsight-release-notes"></a>Az Azure HDInsight kibocsátási megjegyzései

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

Ha a kibocsátási megjegyzésekre szeretne előfizetni, tekintse meg a [jelen GitHub-adattár](https://github.com/hdinsight/release-notes/releases)kiadásait.

## <a name="release-date-03242021"></a>Kiadás dátuma: 03/24/2021

Ez a kiadás a 3,6-es és a HDInsight 4,0-es HDInsight egyaránt érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várja meg, amíg a kiadás több napon belül élő marad a régióban.

## <a name="new-features"></a>Új funkciók
### <a name="spark-30-preview"></a>Spark 3,0 előzetes verzió
A HDInsight hozzáadta a [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) -támogatást a HDInsight 4,0-hez előzetes verziójú szolgáltatásként. 

### <a name="kafka-24-preview"></a>Kafka 2,4 előzetes verzió
A HDInsight az HDInsight 4,0-as verziójához hozzáadta a [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) -támogatást előzetes verzióként.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. A szolgáltatás fokozatosan migrálható az [Azure virtuálisgép-méretezési csoportokra](../virtual-machine-scale-sets/overview.md). A teljes folyamat hónapokat is igénybe vehet. A régiók és az előfizetések migrálása után az újonnan létrehozott HDInsight-fürtök felhasználói műveletek nélkül futnak a virtuálisgép-méretezési csoportokban. A rendszer nem várt változást.

## <a name="deprecation"></a>Elavulás
Nincs elavulás ebben a kiadásban.

## <a name="behavior-changes"></a>Viselkedési változások
### <a name="default-cluster-version-is-changed-to-40"></a>A fürt alapértelmezett verziója 4,0-re változik
A HDInsight-fürt alapértelmezett verziója 3,6 és 4,0 között változik. További információ az elérhető verziókról: [elérhető verziók](./hdinsight-component-versioning.md). További információ a [HDInsight 4,0](./hdinsight-version-release.md)újdonságáról.

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Az alapértelmezett fürt virtuálisgép-méretei a Ev3 sorozatra változnak 
Az alapértelmezett fürt virtuálisgép-méretei a D sorozattól a Ev3 sorozatig változnak. Ez a változás a főcsomópontokra és a munkavégző csomópontokra vonatkozik. Ha el szeretné kerülni, hogy ez a változás hatással legyen a tesztelt munkafolyamatokra, adja meg az ARM-sablonban használni kívánt virtuális gépek méretét.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>A hálózati adapter erőforrása nem látható az Azure-beli virtuálisgép-méretezési csoportokon futó fürtök esetében
A HDInsight fokozatosan migrálja az Azure-beli virtuálisgép-méretezési csoportokra. A virtuális gépek hálózati adapterei már nem láthatók az ügyfelek számára az Azure virtuálisgép-méretezési csoportokat használó fürtök esetében.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni.

### <a name="os-version-upgrade"></a>Operációs rendszer verziófrissítése
A HDInsight az Ubuntu 16,04 és a 18,04 közötti operációsrendszer-verziót fogja frissíteni. A frissítés a 2021. április előtt fejeződik be.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6, 30 2021. június vége
A HDInsight 3,6 támogatás megszűnik. A 30 2021. június megkezdése után az ügyfelek nem hozhatnak létre új HDInsight 3,6-fürtöket. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Vegye fontolóra a HDInsight 4,0-re való áttérést, hogy elkerülje a lehetséges rendszer/támogatás megszakadását.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
A Spark 3.0.0 és a Kafka 2.4.1 támogatása előzetes verzióként. A HDInsight 4,0 és a HDInsight 3,6 aktuális összetevő-verzióit ebben a [dokumentumban](./hdinsight-component-versioning.md)találja.
