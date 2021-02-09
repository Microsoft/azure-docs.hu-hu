---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f6b7000812f1adfe6ff7bd93711c9b8fe4ff9adc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988355"
---
# <a name="azure-hdinsight-release-notes"></a>Az Azure HDInsight kibocsátási megjegyzései

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

Ha a kibocsátási megjegyzésekre szeretne előfizetni, tekintse meg a [jelen GitHub-adattár](https://github.com/hdinsight/release-notes/releases)kiadásait.

## <a name="release-date-02052021"></a>Kiadás dátuma: 02/05/2021

Ez a kiadás a 3,6-es és a HDInsight 4,0-es HDInsight egyaránt érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várja meg, amíg a kiadás több napon belül élő marad a régióban.

## <a name="new-features"></a>Új funkciók
### <a name="dav4-series-support"></a>Dav4 sorozat támogatása
Ebben a kiadásban a HDInsight hozzáadta a Dav4 sorozat támogatását. További információ az [Dav4-sorozatról](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series).

### <a name="kafka-rest-proxy-ga"></a>Kafka REST proxy GA 
A Kafka REST proxy lehetővé teszi a Kafka-fürttel való interakciót a HTTPS-en keresztül REST API. A Kafka Rest proxy a jelen kiadástól kezdve általánosan elérhető. További információ a [KAFKA Rest proxyról](https://docs.microsoft.com/azure/hdinsight/kafka/rest-proxy).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. A szolgáltatás fokozatosan migrálható az [Azure virtuálisgép-méretezési csoportokra](../virtual-machine-scale-sets/overview.md). A teljes folyamat hónapokat is igénybe vehet. A régiók és az előfizetések migrálása után az újonnan létrehozott HDInsight-fürtök felhasználói műveletek nélkül futnak a virtuálisgép-méretezési csoportokban. A rendszer nem várt változást.

## <a name="deprecation"></a>Elavulás
### <a name="disabled-vm-sizes"></a>Letiltott virtuálisgép-méretek
A HDInsight január 9 2021-től kezdődően a standand_A8, standand_A9, standand_A10 és standand_A11 virtuálisgép-méretek használatával letiltja a fürtöket létrehozó összes ügyfelet. A meglévő fürtök futtatása a következőképpen történik:. Vegye fontolóra a HDInsight 4,0-re való áttérést, hogy elkerülje a lehetséges rendszer/támogatás megszakadását.

## <a name="behavior-changes"></a>Viselkedési változások
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>A fürt alapértelmezett virtuálisgép-méretének változása Ev3 sorozatra 
A fürt alapértelmezett virtuálisgép-méretei a D sorozatról a Ev3 sorozatra változnak. Ez a változás a főcsomópontokra és a munkavégző csomópontokra vonatkozik. Ha el szeretné kerülni, hogy ez a változás hatással legyen a tesztelt munkafolyamatokra, adja meg az ARM-sablonban használni kívánt virtuális gépek méretét.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>A hálózati adapter erőforrása nem látható az Azure-beli virtuálisgép-méretezési csoportokon futó fürtök esetében
A HDInsight fokozatosan migrálja az Azure-beli virtuálisgép-méretezési csoportokra. A virtuális gépek hálózati adapterei már nem láthatók az ügyfelek számára az Azure virtuálisgép-méretezési csoportokat használó fürtök esetében.


### <a name="breaking-change-for-net-for-apache-spark-100"></a>A .NET Apache Spark 1.0.0-hoz való feltörésének változása
A HDInsight bevezeti a .NET első jelentős kiadását Apache Spark a következő kiadásban. Biztosítja a Spark 2.4. x és a Spark 3.0. x DataFrame API-teljességét más funkciókkal együtt. Ebben a főverzióban módosulnak a változások, ezért tekintse át [ezt az áttelepítési útmutatót](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) a kód és a folyamatok frissítéséhez szükséges lépések megismeréséhez. További információ [itt](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni.

### <a name="default-cluster-version-will-be-changed-to-40"></a>A fürt alapértelmezett verziója 4,0-re változik
Február 2021-én a HDInsight-fürt alapértelmezett verziója 3,6-ről 4,0-ra módosul. További információ az elérhető verziókról: [elérhető verziók](./hdinsight-component-versioning.md#available-versions). További információ a [HDInsight 4,0](./hdinsight-version-release.md)újdonságáról.

### <a name="os-version-upgrade"></a>Operációs rendszer verziófrissítése
A HDInsight az Ubuntu 16,04 – 18,04 operációs rendszer verziójának frissítését végzi. A frissítés a 2021. április előtt fejeződik be.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6, 30 2021. június vége
A HDInsight 3,6 támogatás megszűnik. A 30 2021. június megkezdése után az ügyfelek nem hozhatnak létre új HDInsight 3,6-fürtöket. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Vegye fontolóra a HDInsight 4,0-re való áttérést, hogy elkerülje a lehetséges rendszer/támogatás megszakadását.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosult az összetevő verziószáma. A HDInsight 4,0 és a HDInsight 3,6 aktuális összetevő-verzióit ebben a [dokumentumban](./hdinsight-component-versioning.md)találja.

