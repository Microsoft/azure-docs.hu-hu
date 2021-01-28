---
title: Spark streaming & pontosan egyszeri események feldolgozása – Azure HDInsight
description: Apache Spark streaming beállítása egy esemény egyszeri és egyszeri feldolgozásához.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2018
ms.openlocfilehash: 4ba7df665b24a3eba2cd185d85a17bd0ef456b0b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929670"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Apache Spark streaming-feladatok létrehozása pontosan egyszeri esemény-feldolgozással

Az adatfolyam-feldolgozó alkalmazások különböző megközelítéssel látják el, hogyan kezelik az üzenetek újrafeldolgozását a rendszeren előforduló hibák után:

* Legalább egyszer: minden üzenet feldolgozható, de többször is feldolgozhatók.
* Legfeljebb egyszer: minden üzenet feldolgozható vagy nem végezhető el. Ha egy üzenet feldolgozása történik meg, csak egyszer dolgozza fel a rendszer.
* Pontosan egyszer: minden üzenet csak egyszer és egyszer dolgozható fel.

Ez a cikk bemutatja, hogyan konfigurálhatja a Spark streaminget a pontosan egyszeri feldolgozás eléréséhez.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Pontosan egyszeri szemantika Apache Spark Streamingtel

Először is gondolja át, hogy a rendszer miért indítja újra az összes rendszerpontot a probléma után, és hogyan kerülheti el az adatvesztést. A Spark streaming-alkalmazás a következőket nyújtja:

* Egy bemeneti forrás.
* Egy vagy több fogadó folyamat, amely adatokat olvas be a bemeneti forrásból.
* Az adatok feldolgozására szolgáló feladatok.
* Kimeneti fogadó.
* A hosszan futó feladatot kezelő illesztőprogram-folyamat.

A pontosan egyszeri szemantika megköveteli, hogy egyetlen ponton se vesszenek el az adatvesztés, és hogy az üzenetek feldolgozása újrainduljon, függetlenül attól, hogy hol történik a hiba.

### <a name="replayable-sources"></a>Visszajátszható források

Az a forrás, amellyel a Spark streaming-alkalmazás beolvassa az eseményeket, újra *játszhatónak* kell lennie. Ez azt jelenti, hogy azokban az esetekben, amikor az üzenet beolvasása megtörtént, de a rendszer az üzenet megtartásának vagy feldolgozásának megkezdése előtt meghiúsult, a forrásnak ugyanazt az üzenetet kell megadnia.

Az Azure-ban az Azure Event Hubs és a HDInsight [Apache Kafka](https://kafka.apache.org/) is elérhetővé teszi a visszajátszható forrásokat. Egy olyan hibatűrő fájlrendszer, mint például az [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), az Azure Storage-Blobok vagy a Azure Data Lake Storage, ahol minden adat örökre megmarad, és bármikor újra elolvashatja az adatforrásokat.

### <a name="reliable-receivers"></a>Megbízható fogadók

A Spark Streamingben a források, például a Event Hubs és a Kafka *megbízható fogadókkal* rendelkeznek, ahol minden fogadó nyomon követi a forrás olvasásának előrehaladását. A megbízható fogadó állapota hibatűrő tárolóban marad, [Apache ZooKeeper](https://zookeeper.apache.org/) vagy a HDFS-ba írt Spark streaming ellenőrzőpontokon belül. Ha egy ilyen fogadó meghibásodik, és később újraindul, akkor azt is megteheti, hogy hol hagyta el.

### <a name="use-the-write-ahead-log"></a>A Write-Ahead napló használata

A Spark streaming támogatja egy Write-Ahead napló használatát, amelyben minden fogadott esemény először a Spark ellenőrzőpont-könyvtárába lett írva a hibatűrő tárolóban, majd egy rugalmasan elosztott adatkészletben (RDD) tárolódik. Az Azure-ban a hibatűrő tárolót az Azure Storage vagy a Azure Data Lake Storage HDFS. A Spark streaming-alkalmazásban a Write-Ahead napló minden fogadó esetében engedélyezve van, ha a konfigurációs beállítást a értékre állítja `spark.streaming.receiver.writeAheadLog.enable` `true` . A Write-Ahead napló hibatűrést biztosít mind az illesztőprogram, mind a végrehajtó meghibásodása esetén.

Az esemény adataival kapcsolatos feladatokat futtató munkavégzők esetében minden RDD definíciója a több feldolgozón replikált és elosztott. Ha egy feladat meghiúsul, mert az azt futtató feldolgozó összeomlott, a feladat újraindul egy másik feldolgozón, amely az esemény adatai replikáját használja, így az esemény nem vész el.

### <a name="use-checkpoints-for-drivers"></a>Ellenőrzőpontok használata az illesztőprogramokhoz

A feladatokhoz szükséges illesztőprogramokat újra kell indítani. Ha a Spark streaming-alkalmazást futtató illesztőprogram összeomlik, a rendszer lekéri az összes futó fogadót, feladatot és minden olyan RDD, amely az események adatait tárolja. Ebben az esetben képesnek kell lennie a feladatok előrehaladásának mentésére, így később is folytathatja. Ezt úgy érheti el, hogy a DStream irányított aciklikus Gráfját (DAG) időnként hibatűrő tárolóra irányítja. A DAG metaadatai közé tartozik az adatfolyam-alkalmazás létrehozásához használt konfiguráció, az alkalmazást definiáló műveletek, valamint a várólistára helyezett, de még nem befejezett kötegek. Ez a metaadatok lehetővé teszik a sikertelen illesztőprogramok újraindítását az ellenőrzőpont információi alapján. Az illesztőprogram újraindításakor a rendszer elindítja az új fogadókat, amelyek maguk visszaállítják az RDD a Write-Ahead naplóból.

Az ellenőrzőpontok két lépésben engedélyezettek a Spark Streamingben.

1. Az StreamingContext objektumban konfigurálja az ellenőrzőpontok tárolási útvonalát:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    A HDInsight-ben ezeket az ellenőrzőpontokat a fürthöz csatolt alapértelmezett tárolóba kell menteni, vagy az Azure Storage vagy a Azure Data Lake Storage.

2. Ezután a DStream határozza meg a ellenőrzőpontok időtartamát (másodpercben). Minden intervallumban a bemeneti eseményből származtatott állapotinformációkat a rendszer megőrzi a tárolóban. A megőrzött állapotadatok csökkenthetik az állapotnak a forrás eseményből való újraépítésekor szükséges számításokat.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Idempotens-mosogatók használata

A célhelynek, amelyre a feladatnak az eredményeket írnia kell, képesnek kell lennie arra, hogy kezelni tudja a helyzetet, ha ugyanazt az eredményt többször is megadták. A fogadónak képesnek kell lennie az ismétlődő eredmények észlelésére és figyelmen kívül hagyásához. Az *idempotens* -fogadó több alkalommal is meghívható ugyanazzal az adattal, ha nem változik az állapot.

Idempotens hozhat létre olyan logikával, amely először ellenőrzi a bejövő eredmény létezését az adattárban. Ha az eredmény már létezik, az írásnak úgy kell megjelennie, hogy sikeres legyen a Spark-feladatokból, de a valóságban az adattár figyelmen kívül hagyta az ismétlődő adatmennyiséget. Ha az eredmény nem létezik, akkor a fogadónak be kell szúrnia ezt az új eredményt a tárolóba.

Használhat például egy olyan Azure SQL Database tárolt eljárást, amely eseményeket szúr be egy táblába. Ez a tárolt eljárás először a kulcs mezők alapján keresi fel az eseményt, és csak akkor, ha nem található egyező esemény a táblába illesztett rekord.

Egy másik példa egy particionált fájlrendszer, például az Azure Storage-Blobok vagy a Azure Data Lake Storage használatára. Ebben az esetben a fogadó logikának nem kell ellenőriznie a fájl létezését. Ha az eseményt jelképező fájl létezik, egyszerűen felülírja ugyanazokat az adattípusokat. Ellenkező esetben a rendszer létrehoz egy új fájlt a számított útvonalon.

## <a name="next-steps"></a>Következő lépések

* [Apache Spark streaming – áttekintés](apache-spark-streaming-overview.md)
* [Magasan elérhető Apache Spark folyamatos átviteli feladatok létrehozása Apache Hadoop-FONALban](apache-spark-streaming-high-availability.md)
