---
title: Azure HDInsight, magasan elérhető megoldás architektúra-esettanulmány
description: Ez a cikk egy lehetséges Azure HDInsight-megoldási architektúrával kapcsolatos kitalált esettanulmány.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Hadoop magas rendelkezésre állása
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 4b98b03c2d7eb4a0403b4595c1376656ed42511b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855038"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Azure HDInsight, magasan elérhető megoldás architektúra-esettanulmány

Az Azure HDInsight replikálási mechanizmusai egy magasan elérhető megoldás-architektúrába integrálhatók. Ebben a cikkben egy kitalált esettanulmányt használunk a contoso kiskereskedelmi megoldásához a lehetséges magas rendelkezésre állású vész-helyreállítási módszerek, a költséghatékonyság és a hozzájuk kapcsolódó tervek magyarázata érdekében.

A magas rendelkezésre állású vész-helyreállítási javaslatok több permutációval és kombinációval is rendelkezhetnek. Ezeket a megoldásokat az egyes lehetőségek előnyeinek és hátrányainak felszabadítása után kell megérkezni. Ez a cikk csak az egyik lehetséges megoldást tárgyalja.

## <a name="customer-architecture"></a>Ügyfél-architektúra

Az alábbi képen a contoso kiskereskedelmi elsődleges architektúrája látható. Az architektúra egy folyamatos átviteli munkaterhelés, a Batch számítási feladatok, a réteg, a fogyasztási réteg, a tárolási réteg és a verziókövetés.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Contoso kiskereskedelmi architektúra":::

### <a name="streaming-workload"></a>Folyamatos átviteli munkaterhelés

Az eszközök és érzékelők a HDInsight Kafka-re hoznak létre, amelyek az üzenetkezelési keretrendszert alkotják. An méretű HDInsight Spark-fogyasztó beolvassa a Kafka-témákat. A Spark átalakítja a beérkező üzeneteket, és egy HDInsight HBase-fürtbe írja azt a kiszolgáló rétegben.

### <a name="batch-workload"></a>Kötegelt munkaterhelés

An méretű HDInsight struktúra-és MapReduce futtató Hadoop-fürt a helyszíni tranzakciós rendszerekből származó adatok betöltését végzi. A struktúra és a MapReduce által átalakított nyers adatmennyiséget a rendszer a Azure Data Lake Storage Gen2 által támogatott adattó logikai partícióján tárolja. A kaptár-táblákban tárolt adatkészletek a Spark SQL számára is elérhetővé válnak, amely a Batch átalakítását végzi, mielőtt a HBase-ben tárolja a kiszolgált kiszolgálókat.

### <a name="serving-layer"></a>Kiszolgáló réteg

An méretű HDInsight HBase-fürtöt, amely Apache Phoenix a webalkalmazásokhoz és a vizualizációs irányítópultokhoz való adatkiszolgálásra szolgál. An méretű HDInsight LLAP-fürt a belső jelentési követelmények teljesítésére szolgál.

### <a name="consumption-layer"></a>Felhasználási réteg

Egy Azure API Apps és API Management réteget egy nyilvános oldalról. A belső jelentési követelményeket a Power BI teljesíti.

### <a name="storage-layer"></a>Tárolási réteg

A logikailag particionált Azure Data Lake Storage Gen2 a vállalati adattóként használatos. Az HDInsight-metaadattárak az Azure SQL DB támogatja.

### <a name="version-control-system"></a>Verziókövetés rendszer

Egy Azure-folyamatba integrált, Azure-on kívül üzemeltetett verziókövető rendszer.

## <a name="customer-business-continuity-requirements"></a>Az ügyfelek üzleti folytonosságára vonatkozó követelmények

Fontos megállapítani, hogy milyen minimális üzleti funkciókra van szükség, ha van katasztrófa.

### <a name="contoso-retails-business-continuity-requirements"></a>A contoso kiskereskedelmi üzletmenet-folytonossági követelményei

* Védeni kell a regionális meghibásodás vagy a regionális szolgáltatás állapotával kapcsolatos problémát.
* Az ügyfeleim soha nem látják a 404 hibát. A nyilvános tartalmat mindig kézbesíteni kell. (RTO = 0)  
* Az év nagy részében az 5 óra alatt elavult nyilvános tartalmakat is megjeleníthetjük. (RPO = 5 óra)
* A nyári időszakban a nyilvános tartalomnak mindig naprakésznek kell lennie. (RPO = 0)
* A belső jelentési követelmények nem számítanak kritikusnak az üzletmenet folytonossága szempontjából.
* Az üzletmenet folytonossági költségeinek optimalizálása.

## <a name="proposed-solution"></a>Javasolt megoldás

Az alábbi képen a contoso kiskereskedelmi magas rendelkezésre állású vész-helyreállítási architektúrája látható.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Contoso kiskereskedelmi architektúra":::

**Kafka** A Kafka [aktív – passzív](hdinsight-business-continuity-architecture.md#apache-kafka) replikálást használ a Kafka-témakörök az elsődleges régióból a másodlagos régióba való tükrözéséhez. A Kafka-replikáció alternatívája lehet a Kafka mindkét régióban való létrehozása.

A **kaptár és a Spark** [aktív elsődleges – igény szerinti másodlagos](hdinsight-business-continuity-architecture.md#apache-spark) replikációs modelleket használ a normál időpontokban. A struktúra replikálási folyamata rendszeresen fut, és a kaptár Azure SQL-metaadattár és a kaptár Storage-fiókjának replikációját kíséri. A Spark Storage-fiókot az ADF DistCP használatával rendszeresen replikálja a rendszer. A fürtök átmeneti jellege segít optimalizálni a költségeket. A replikálások 4 óránként vannak ütemezve, hogy egy RPO, amely az öt órás követelményen belül jól látható.

A **HBase** -replikáció a [Leader – követő](hdinsight-business-continuity-architecture.md#apache-hbase) modellt használja a normál időpontokban annak érdekében, hogy az adatkezelés mindig a régiótól függetlenül TÖRTÉNJEN, és a RPO nulla legyen.

Ha az elsődleges régióban regionális hiba fordul elő, a weboldalt és a háttér-tartalmat 5 órára kiszolgálják a másodlagos régióból, bizonyos fokú elavulás miatt. Ha az Azure szolgáltatás állapot-irányítópultja nem jelzi a helyreállítási ETA-t az ötnapos ablakban, a contoso kiskereskedelmi létrehozza a struktúrát és a Spark-transzformációs réteget a másodlagos régióban, majd az összes felsőbb rétegbeli adatforrást a másodlagos régióra irányítja. Ha a másodlagos régió írható, akkor a feladat-visszavételi folyamat az elsődlegesre való replikálást eredményez.

A legnagyobb bevásárlási időszakban a teljes másodlagos folyamat mindig aktív és fut. A Kafka-termelők mindkét régióban és a HBase-replikációban is módosulnak Leader-Followerról Leader-Leaderra, így biztosítva, hogy a nyilvános tartalmak mindig naprakészek legyenek.

A belső jelentéskészítéshez nincs szükség feladatátvételi megoldásra, mivel ez nem kritikus fontosságú az üzletmenet folytonossága szempontjából.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a cikkben tárgyalt elemekről, tekintse meg a következőt:

* [Az Azure HDInsight üzletmenet folytonossága](./hdinsight-business-continuity.md)
* [Az Azure HDInsight üzletmenet-folytonossági architektúrái](./hdinsight-business-continuity-architecture.md)
* [Mi a Apache Hive és a HiveQL az Azure HDInsight?](./hadoop/hdinsight-use-hive.md)