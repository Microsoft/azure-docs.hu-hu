---
title: HDInsight 4,0 – Áttekintés – Azure
description: A HDInsight 3.6 és a HDInsight 4.0 funkcióinak, korlátainak és frissítési ajánlásainak összehasonlítása.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 694acc0005e90d8299d46528f83ba68ea3fcf1c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931186"
---
# <a name="azure-hdinsight-40-overview"></a>Azure HDInsight 4,0 – áttekintés

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében Apache Hadoop és Apache Spark. A HDInsight 4,0 Apache Hadoop összetevők Felhőbeli eloszlása. Ez a cikk az Azure HDInsight legújabb kiadásával és a frissítés menetével kapcsolatban nyújt információkat.

## <a name="whats-new-in-hdinsight-40"></a>Az HDInsight 4,0 újdonságai

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3,0 és kis késleltetésű analitikai feldolgozás

Apache Hive kis késleltetésű analitikus feldolgozás (LLAP) állandó lekérdezési kiszolgálókat és memóriabeli gyorsítótárazást használ. Ez a folyamat gyors SQL-lekérdezési eredményeket biztosít a távoli Felhőbeli tárolásban tárolt adatmennyiségek esetében. A kaptár LLAP olyan állandó démonok készletét használja, amelyek a kaptár-lekérdezések töredékeit hajtják végre. A lekérdezések végrehajtása az LLAP-vel nagyon hasonló az LLAP nélküli Hive-hoz, csak a feldolgozó feladatok a tárolók helyett LLAP-démonokban futnak.

A Hive LLAP előnyei:

* Mély SQL-elemzések lehetősége a teljesítmény és az alkalmazkodóképesség feláldozása nélkül. Összetett illesztések, allekérdezések, ablakos függvények, rendezés, felhasználó által definiált függvények és összetett összesítések.

* Interaktív lekérdezések futtatása az adatok előkészítéséhez használt tárolón belül, így az adatokat nem szükséges egy másik alrendszerbe átmozgatni az elemzések feldolgozásához.

* A gyorsítótárazási lekérdezés eredményei lehetővé teszik a korábban számított lekérdezési eredmények újbóli felhasználását. Ez a gyorsítótár a lekérdezéshez szükséges fürtözött feladatok futtatásával időt és erőforrásokat takarít meg.

### <a name="hive-dynamic-materialized-views"></a>A Hive dinamikus, tényleges táblán alapuló nézetei

A kaptár mostantól támogatja a dinamikus, kiszervezett nézeteket, vagy előre kiszámítja a vonatkozó összefoglalókat. A nézetek felgyorsítják a lekérdezések feldolgozását az adattárházban. A tényleges táblán alapuló nézetek natív módon tárolhatók a Hive-ban, és zökkenőmentesen használhatják az LLAP-gyorsítást.

### <a name="hive-transactional-tables"></a>A Hive tranzakciós táblái

A HDI 4,0 Apache Hive 3 tartalmaz. A 3. struktúra megköveteli a kaptárban élő tranzakciós táblázatok atomi, konzisztencia-, elkülönítési és tartóssági megfelelőségét. Az ACID-kompatibilis táblákat és táblaadatokat a Hive használja és felügyeli. A létrehozási, lekérési, frissítési és törlési (szifilisz) táblákban lévő adatoknak optimalizált sor oszlop (ORK) formátumúnak kell lenniük. A csak INSERT-táblázatok támogatják az összes fájlformátumot. 

> [!Note]
> A savas/tranzakciós támogatás csak felügyelt táblák esetében működik, külső táblák esetében nem. A kaptár külső táblái úgy vannak kialakítva, hogy a külső felek képesek legyenek tábla-adatokat olvasni és írni, anélkül, hogy a kaptár perfoming az alapul szolgáló adatokat. A savas táblák esetében a struktúra az alapul szolgáló adatokat tömörítéssel és tranzakciókkal is megváltoztathatja.

A savas táblák néhány előnye a következő:

* Az ACID 2-es verziójában a tárolási formátummal és a végrehajtómotorral kapcsolatos teljesítmény is javult.

* Az ACID alapértelmezés szerint engedélyezve van az adatfrissítések teljes támogatása érdekében.

* A fejlesztett ACID-képességek sorszintű frissítési és törlési funkcionalitást biztosítanak.

* Nincs teljesítménybeli többletterhelés.

* Nincs szükség gyűjtésre.

* A Spark a Hive Warehouse-összekötő segítségével képes írni és olvasni a Hive ACID-táblákat.

További információk az [Apache Hive 3-ról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Az Apache Spark a Hive Warehouse-összekötővel frissíthető táblákhoz és ACID-tranzakciókhoz fér hozzá. A Hive Warehouse-összekötővel a Hive tranzakciós táblák külső táblákként regisztrálhatók a Sparkban a teljes tranzakciós funkcionalitás használatához. A korábbi verziók csak a táblapartíciók kezelését támogatták. A méhkas Warehouse-összekötő támogatja a streaming DataFrames is.  Ezzel a folyamattal a Spark tranzakciós és folyamatos adatátviteli táblái olvasása és írása végezhető el.

A Spark végrehajtói közvetlenül kapcsolódhatnak a Hive LLAP-démonokhoz az adatok tranzakciós lekéréséhez és frissítéséhez, így továbbra is a Hive vezérli az adatokat.

Az Apache Spark on HDInsight 4.0 az alábbi forgatókönyveket támogatja:

* Gépi tanulási modellbetanítás futtatása a jelentéskészítéshez használt tranzakciós táblán.
* Oszlopok biztonságos hozzáadása ACID-tranzakciók használatával a Spark ML-ből a Hive-táblákba.
* Spark streamelési feladatok futtatása a streamelési Hive-táblák változáscsatornáin.
* ORC-fájlok létrehozása közvetlenül a Spark strukturált stream feladataiból.

Többé nem kell aggódnia amiatt, hogy véletlenül megpróbál hozzáférni a kaptár tranzakciós tábláihoz közvetlenül a Sparkból. Inkonzisztens eredményeket, duplikált adatmennyiséget vagy adatsérülést eredményezett. A HDInsight 4,0-ben a Spark Tables és a kaptár táblák külön Metaadattárak vannak tárolva. A Hive Data Warehouse-összekötő használatával a tranzakciós Hive-táblákat kifejezetten Spark külső táblákként regisztrálhatja.

További tudnivalók az [Apache Sparkról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Az Apache Oozie 4.3.1 a HDI 4.0 részét képezi az alábbi módosításokkal:

* Az Oozie már nem futtat Hive-műveleteket. A Hive parancssori felülete megszűnt, és a BeeLine vette át a helyét.

* A nemkívánatos függőségeket kizárhatja a megosztási kódtárból egy kizárási minta felvételével a **job.properties** fájlba.

További tudnivalók az [Apache Oozie-ról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Frissítés a HDInsight 4,0-re

Alaposan tesztelje az összetevőket, mielőtt éles környezetben implementálja a legújabb verziót. A HDInsight 4,0 elérhető a frissítési folyamat megkezdéséhez. A HDInsight 3,6 az alapértelmezett lehetőség a véletlen balesetek megelőzésére.

Nincs támogatott frissítési útvonal a HDInsight korábbi verzióiról a HDInsight 4,0-re. Mivel a Metaadattár és a blob adatformátumai megváltoztak, a 4,0 nem kompatibilis az előző verziókkal. Fontos, hogy az új HDInsight 4,0-környezet az aktuális éles környezettől elkülönítve maradjon. Ha a HDInsight 4,0-et az aktuális környezetbe telepíti, a Metaadattár véglegesen frissülni fog.  

## <a name="limitations"></a>Korlátozások

* A HDInsight 4,0 nem támogatja a Apache Hive MapReduce. Használja helyette az Apache TEZ. További tudnivalók az [Apache Tezről](https://tez.apache.org/).
* A HDInsight 4,0 nem támogatja a Apache Storm.
* A HDInsight 4,0 nem támogatja a ML Services-fürt típusát.
* A kaptár nézet csak olyan HDInsight 4,0-fürtökön érhető el, amelyek verziószáma legalább 4,1. Ez a verziószám a Ambari rendszergazdai > verzióiban érhető el.
* Az Apache Zeppelin rendszerhéj-értelmező nem támogatott a Spark és az interaktív lekérdezési fürtökben.
* Az LLAP nem *tiltható le* a Spark-LLAP-fürtön. Csak az LLAP kikapcsolására van lehetősége.
* A Azure Data Lake Storage Gen2 nem tudja menteni a Jupyter-jegyzetfüzeteket egy Spark-fürtben.
* Alapértelmezés szerint az Apache Pig a TEZ-on fut, de a MapReduce-re is módosítható
* A Spark SQL Ranger integrációja a sor-és az oszlop biztonsága elavult
* A Spark 2,4 és a Kafka 2,1 a HDInsight 4,0-ban érhető el, így a Spark 2,3 és a Kafka 1,1 már nem támogatott. Javasoljuk, hogy a Spark 2,4 & Kafka 2,1-es vagy újabb verzióját használja a HDInsight 4,0-ban.

## <a name="next-steps"></a>Következő lépések

* [HBase áttelepítési útmutató](./hbase/apache-hbase-migrate-new-version.md)
* [Struktúra áttelepítési útmutatója](./interactive-query/apache-hive-migrate-workloads.md)
* [A Kafka áttelepítési útmutatója](./kafka/migrate-versions.md)
* [A Spark áttelepítési útmutatója](./spark/migrate-versions.md)
* [Az Azure HDInsight dokumentációja](index.yml)
* [Kibocsátási megjegyzések](hdinsight-release-notes.md)
