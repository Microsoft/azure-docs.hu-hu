---
title: Apache Kafka-Azure-HDInsight Azure Monitor naplófájljai
description: Megtudhatja, hogyan elemezheti Azure Monitor naplókat az Azure HDInsight lévő Apache Kafka-fürtök naplófájljainak elemzéséhez.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 74db121c33864ee72ad984b49c8fa43afa0f598c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870517"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Apache Kafka on HDInsight-naplók elemzése

Megtudhatja, hogyan használhatja a Azure Monitor naplókat a HDInsight Apache Kafka által generált naplók elemzéséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Naplók helye

A fürt Apache Kafka naplófájljai a következő helyen találhatók: `/var/log/kafka` . A kafka-naplók nem tárolódnak és nem maradnak meg a fürt életciklusa között, függetlenül attól, hogy a rendszer felügyelt lemezeket használ-e. A következő táblázat az elérhető naplókat mutatja be.

|Napló |Leírás |
|---|---|
|Kafka. out|a Kafka-folyamat stdout és stderr. Ebben a fájlban megtalálja a Kafka indítási és leállítási naplóit.|
|Server. log|A Kafka-kiszolgáló fő naplója. A Kafka-átvitelszervező összes naplója itt fejeződik be.|
|vezérlő. log|A vezérlő naplófájljai, ha a közvetítő vezérlőként működik.|
|statechange. log|A rendszer naplózza a fájlban lévő összes állapotváltozás-eseményt.|
|Kafka-GC. log|Kafka-Garbage gyűjtemény statisztikái.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Azure Monitor naplók engedélyezése Apache Kafka számára

A HDInsight Azure Monitor naplófájljainak engedélyezéséhez szükséges lépések megegyeznek az összes HDInsight-fürtön. Az alábbi hivatkozások segítségével megismerheti, hogyan hozhatja létre és konfigurálhatja a szükséges szolgáltatásokat:

1. Egy Log Analytics-munkaterület létrehozása. További információkért tekintse meg a [naplókat Azure monitor](../../azure-monitor/logs/data-platform-logs.md) dokumentumban.

2. Hozzon létre egy Kafka-t a HDInsight-fürtön. További információ: [Start with apache Kafka on HDInsight](apache-kafka-get-started.md) Document.

3. Konfigurálja a Kafka-fürtöt Azure Monitor naplók használatára. További információ: [Azure monitor naplók használata a HDInsight-dokumentumok figyeléséhez](../hdinsight-hadoop-oms-log-analytics-tutorial.md) .

> [!IMPORTANT]  
> Előfordulhat, hogy a rendszer körülbelül 20 percet vesz igénybe, mielőtt Azure Monitor naplók számára elérhetővé válik az adatnapló.

## <a name="query-logs"></a>Lekérdezési naplók

1. A [Azure Portal](https://portal.azure.com)válassza ki log Analytics munkaterületét.

2. A bal oldali menü **általános** területén válassza a **naplók** lehetőséget. Itt kereshet a Kafkaből gyűjtött adatok között. Adjon meg egy lekérdezést a lekérdezési ablakban, majd válassza a **Futtatás** lehetőséget. Az alábbiakban néhány példát keresünk:

* Lemezhasználat:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU-használat:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Bejövő üzenetek másodpercenkénti száma: (cserélje le a `your_kafka_cluster_name` fürt nevére.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bejövő bájtok másodpercenként: (lecserélve a `wn0-kafka` munkavégző csomópont állomásneve)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Kimenő bájtok másodpercenként: (cserélje le `your_kafka_cluster_name` a fürt nevére.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Megadhatja `*` az összes naplózott típus keresését is. Jelenleg a következő naplók érhetők el a lekérdezésekhez:

    | Napló típusa | Leírás |
    | ---- | ---- |
    | log \_ kafkaserver \_ CL | Kafka Broker Server. log |
    | log \_ kafkacontroller \_ CL | Kafka Broker Controller. log |
    | mérőszámok \_ Kafka \_ CL | Kafka JMX metrikák |

    :::image type="content" source="./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png" alt-text="Apache Kafka log Analytics CPU-használat" border="true":::

## <a name="next-steps"></a>Következő lépések

További információ a Azure Monitorről: [Azure monitor áttekintés](../../azure-monitor/overview.md)és [Azure monitor naplók lekérdezése a HDInsight-fürtök figyeléséhez](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

A Apache Kafka használatáról az alábbi dokumentumokban talál további információt:

* [Tükrözött Apache Kafka HDInsight-fürtök között](apache-kafka-mirroring.md)
* [Növelje Apache Kafka méretét a HDInsight](apache-kafka-scalability.md)
* [Apache Spark streaming (DStreams) használata a Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Apache Spark strukturált adatfolyam használata Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)