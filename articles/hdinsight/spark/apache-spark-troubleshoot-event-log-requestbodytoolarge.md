---
title: RequestBodyTooLarge hiba Apache Spark alkalmazásból – Azure HDInsight
description: NativeAzureFileSystem ... A RequestBodyTooLarge megjelenik a Apache Spark streaming alkalmazás naplójában az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 73ae646cb083841ee1d55b6c7ce6af7180cef08e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929427"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "megjelenő Apache Spark streaming app log in HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A hiba: `NativeAzureFileSystem ... RequestBodyTooLarge` egy Apache Spark streaming alkalmazás illesztőprogram-naplójában jelenik meg.

## <a name="cause"></a>Ok

A Spark-Eseménynapló fájlja valószínűleg a WASB fájl hosszának korlátját éri el.

A Spark 2,3-ben minden Spark-alkalmazás létrehoz egy Spark-Eseménynapló-fájlt. Egy Spark streaming-alkalmazás Spark-Eseménynapló-fájlja folyamatosan növekszik, amíg az alkalmazás fut. Napjainkban a WASB egy fájlja 50000 blokkos korláttal rendelkezik, és az alapértelmezett blokk mérete 4 MB. Az alapértelmezett konfigurációban tehát a maximális fájlméret 195 GB. Az Azure Storage azonban megnövelte a maximális blokkolási méretet 100 MB-ra, ami gyakorlatilag egyetlen fájlra korlátozza a 4,75 TB-ot. További információkért lásd [a blob Storage skálázhatósági és teljesítménybeli céljait](../../storage/blobs/scalability-targets.md)ismertető témakört.

## <a name="resolution"></a>Feloldás

Ehhez a hibához három megoldás érhető el:

* Növelje a blokk méretét akár 100 MB-ra. A Ambari felhasználói felületén módosítsa a HDFS konfigurációs tulajdonságot `fs.azure.write.request.size` (vagy hozza létre a `Custom core-site` szakaszban). Állítsa a tulajdonságot nagyobb értékre, például: 33554432. Mentse a frissített konfigurációt, és indítsa újra az érintett összetevőket.

* A Spark-streaming feladatok rendszeres leállítása és újraküldése.

* A Spark-eseménynaplók tárolására használja a HDFS. A HDFS for Storage használata a fürtök skálázása vagy az Azure-frissítések során felmerülő Spark-események elvesztését eredményezheti.

    1. Módosítsa a `spark.eventlog.dir` `spark.history.fs.logDirectory` Ambari felhasználói felületén, és a használatával:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Könyvtárak létrehozása a HDFS-on:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Indítsa újra az összes érintett szolgáltatást a Ambari felhasználói felületén keresztül.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]