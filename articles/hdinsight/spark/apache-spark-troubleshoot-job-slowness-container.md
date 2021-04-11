---
title: Apache Spark lassú, ha az Azure HDInsight-tárolóban sok fájl van
description: Apache Spark a feladatok lassan futnak, amikor az Azure Storage-tároló sok fájlt tartalmaz az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: d256292956b42c65722ea5f9c87d6835297d1d40
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219093"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Az Apache Spark-feladatok lassan futnak, ha az Azure Storage-tároló sok fájlt tartalmaz az Azure HDInsightban

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

HDInsight-fürt futtatásakor az Azure Storage-tárolóba írást Apache Spark feladat lassú lesz, ha sok fájl/almappa van. Például a rendszer 20 másodpercet vesz igénybe egy új tárolóba való íráskor, de körülbelül 2 percet, amikor több mint 200.000-fájlokat tartalmazó tárolóba ír.

## <a name="cause"></a>Ok

Ez egy ismert Spark-probléma. A lassulás a `ListBlob` `GetBlobProperties` Spark-feladatok végrehajtása során a és a műveletből származik.

A partíciók nyomon követéséhez a Sparknak egy olyan információt kell fenntartania, `FileStatusCache` amely a címtár struktúrájáról tartalmaz információkat. Ezen gyorsítótár használatával a Spark elemezheti az elérési utakat, és tisztában lehet a rendelkezésre álló partíciókkal. A partíciók nyomon követésének előnye, hogy a Spark csak az adatolvasás során érinti a szükséges fájlokat. Az adatok naprakészen tartásához az új adatok írásakor a Sparknak fel kell sorolnia a könyvtár alatt lévő összes fájlt, és frissítenie kell a gyorsítótárat.

A Spark 2,1-es verziójában a Spark azt jelzi, hogy egy meglévő partíciós oszlop megegyezik-e a jelenlegi írási kérelemben szereplővel, és hogy a rendszer minden írási művelet után is a következő műveleteket fogja-e megtekinteni.

Ha a Spark 2,2-as verzióban az adatírás hozzáfűzési móddal történik, ezt a teljesítménnyel kapcsolatos problémát meg kell oldani.

A Spark 2,3-as verzióban ugyanaz a viselkedés, mint a Spark 2,2.

## <a name="resolution"></a>Feloldás

Particionált adatkészlet létrehozásakor fontos a particionálási séma használata, amely korlátozni fogja a Spark által a frissítéshez szükséges fájlok számát `FileStatusCache` .

Ha N %100 = = 0 (100), akkor a meglévő adat áthelyezése egy másik könyvtárba, amelyet a Spark betölt.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
