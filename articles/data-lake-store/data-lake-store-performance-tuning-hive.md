---
title: Performance tuning – struktúra Azure Data Lake Storage Gen1
description: Ismerje meg a HdInsight és a Azure Data Lake Storage Gen1 struktúrájának finomhangolását. Nagy I/O-igényű lekérdezések esetén a struktúra finomhangolásával jobb teljesítményt érhet el.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 082d684ed0a29cb6bf2de9c506886b6d98cf174a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723781"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Teljesítmény-finomhangolási útmutató a HDInsight és Azure Data Lake Storage Gen1 struktúrához

Az alapértelmezett beállítások úgy lettek beállítva, hogy a jó teljesítményt nyújtsanak számos különböző használati esetben.  Az I/O-igényű lekérdezések esetében a kaptár beállítható úgy, hogy jobb teljesítményt kapjon a Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Data Lake Storage Gen1-fiók**. A létrehozásával kapcsolatos útmutatásért tekintse meg az Ismerkedés [a Azure Data Lake Storage Gen1rel](data-lake-store-get-started-portal.md) című témakört.
* **Azure HDInsight-fürt** Data Lake Storage Gen1 fiókhoz való hozzáféréssel. Lásd: [HDInsight-fürt létrehozása Data Lake Storage Gen1sal](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy engedélyezi Távoli asztal a fürt számára.
* **Struktúra futtatása a HDInsight-on**.  A kaptár-feladatok HDInsight való futtatásával kapcsolatos további információkért lásd: [a kaptár használata a HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md)
* **Teljesítmény-finomhangolási irányelvek a Data Lake Storage Gen1**.  Az általános teljesítménnyel kapcsolatos fogalmakat lásd: [Data Lake Storage Gen1 teljesítmény-finomhangolási útmutató](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Paraméterek

A továbbfejlesztett Data Lake Storage Gen1 teljesítményének finomhangolásához a legfontosabb beállítások a következők:

* **kaptár. TEZ. Container. size** – az egyes feladatok által használt memória mennyisége

* **TEZ. grouping. min-size** – az egyes leképezések minimális mérete

* **TEZ. grouping. max-size** – az egyes leképezések maximális mérete

* **hive.exec. szűkítő. Bytes. per. szűkítő** – az egyes csökkentők mérete

**kaptár. TEZ. Container. size** – a tároló mérete határozza meg, hogy mennyi memória érhető el az egyes feladatokhoz.  Ez a fő bemenet a párhuzamosságok struktúrában való vezérléséhez.  

**TEZ. grouping. min-size** – ez a paraméter lehetővé teszi az egyes leképezések minimális méretének megadását.  Ha a TEZ által választott leképezések száma kisebb, mint a paraméter értéke, akkor a TEZ az itt beállított értéket fogja használni.

**TEZ. grouping. max-size** – a paraméter lehetővé teszi az egyes leképezések maximális méretének beállítását.  Ha a TEZ által választott leképezések száma nagyobb, mint a paraméter értéke, akkor a TEZ az itt beállított értéket fogja használni.

**hive.exec. szűkítő. Bytes. per. szűkítő** – ez a paraméter beállítja az egyes szűkítők méretét.  Alapértelmezés szerint minden egyes csökkentő 256MB.  

## <a name="guidance"></a>Útmutató

**hive.exec. szűkítő. Bytes. per. szűkítő beállítása** – az alapértelmezett érték jól működik, ha az adat ki van tömörítve.  A tömörített adatmennyiség csökkentése érdekében csökkentse a szűkítő méretét.  

A **kaptár. TEZ. Container. size beállítása** – az egyes csomópontokban a memóriát a következő szálak határozzák meg: fonal. nodemanager. Resource. Memory-MB, és a HDI-fürthöz alapértelmezés szerint helyesen kell beállítani.  A megfelelő memória a FONALban való beállításával kapcsolatos további információkért tekintse meg ezt a [bejegyzést](../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md).

Az I/O-igényes munkaterhelések a TEZ-tároló méretének csökkentésével több párhuzamosságot is igénybe vehetnek. Ez a felhasználónak több tárolót biztosít, ami növeli a párhuzamosságot.  Egyes kaptár-lekérdezések azonban jelentős mennyiségű memóriát igényelnek (például MapJoin).  Ha a feladatnak nincs elég memóriája, a rendszer a futtatókörnyezeten kívüli kivételt kap.  Ha nem rendelkezik a memóriával kapcsolatos kivételekkel, növelje a memóriát.   

A futó vagy párhuzamos feladatok egyidejű számát a rendszer a FONALak teljes memóriája alapján fogja megkötni.  A FONALas tárolók száma határozza meg, hogy hány egyidejű feladat futhat.  Ha a szál memóriáját szeretné megkeresni egy csomóponton, nyissa meg a Ambari.  Navigáljon a FONALhoz, és tekintse meg a konfigurációk lapot.  Ebben az ablakban a szál memóriája jelenik meg.  

> Összes szál memóriája = csomópontok * FONÁL memóriája/Node számú fonalas tároló = teljes FONÁL memória/TEZ-tároló mérete

A Data Lake Storage Gen1 használatával javíthatja a teljesítményt, hogy a lehető legnagyobb mértékben növelje a párhuzamosságot.  A TEZ automatikusan kiszámítja a létrehozandó feladatok számát, így nem kell azt beállítania.   

## <a name="example-calculation"></a>Példa a számításra

Tegyük fel, hogy 8 csomópontos D14-fürtöt tartalmaz.  

> Összes szál memóriája = csomópontok * szál memóriája/csomópontok száma összesen FONÁL memória = 8 csomópont * 96GB = 768GB-számú FONALas tároló = 768GB/3072MB = 256

## <a name="limitations"></a>Korlátozások

**Data Lake Storage Gen1 szabályozás** 

Ha eléri a Data Lake Storage Gen1 által biztosított sávszélesség korlátait, megkezdheti a feladat-meghibásodások megtekintését. Ez azonosítható a feladatok naplófájljaiban lévő szabályozási hibák megfigyelésével.  Csökkentheti a párhuzamosságot a TEZ-tároló méretének növelésével.  Ha további egyidejűségre van szüksége a feladatokhoz, vegye fel velünk a kapcsolatot.

Annak vizsgálatához, hogy a rendszer leszabályozza-e a szabályozást, engedélyeznie kell a hibakeresési naplózást az ügyfél oldalán. A következőképpen teheti meg:

1. Helyezze a következő tulajdonságot a kaptár konfigurációjának log4j tulajdonságaiba. Ezt a Ambari nézetből teheti meg: log4j. Logger. com. microsoft. Azure. datalake. Store = hibakeresés az összes csomópont/szolgáltatás újraindítása a konfiguráció érvénybe léptetéséhez.

2. Ha a szabályozása folyamatban van, a rendszer a kaptár naplófájljában a HTTP 429 hibakódot fogja látni. A kaptár naplófájlja a/tmp/ &lt; felhasználói &gt; /Hive.log található.

## <a name="further-information-on-hive-tuning"></a>További információ a kaptár hangolásáról

Itt talál néhány olyan blogot, amely segít a kaptár-lekérdezések hangolásában:
* [Kaptár-lekérdezések optimalizálása a Hadoop a HDInsight-ben](../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [A kaptár-lekérdezési fájl kódolása az Azure HDInsight](/archive/blogs/bigdatasupport/encoding-the-hive-query-file-in-azure-hdinsight)
* [Ignite-előadás a kaptár optimalizálása a HDInsight-on](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)