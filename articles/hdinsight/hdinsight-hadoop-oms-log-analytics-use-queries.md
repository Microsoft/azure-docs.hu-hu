---
title: Azure Monitor naplók lekérdezése az Azure HDInsight-fürtök figyeléséhez
description: Megtudhatja, hogyan futtathat lekérdezéseket Azure Monitor naplókon a HDInsight-fürtön futó feladatok figyeléséhez.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: f9213f36ec33939c3df3b56d21822aa3b6a17c03
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945610"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor naplók lekérdezése HDInsight-fürtök figyeléséhez

Ismerkedjen meg az Azure HDInsight-fürtök figyelésére szolgáló Azure Monitor-naplók használatával kapcsolatos alapvető forgatókönyvekkel:

* [HDInsight-fürt metrikáinak elemzése](#analyze-hdinsight-cluster-metrics)
* [Esemény-riasztások létrehozása](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Konfigurálnia kell egy HDInsight-fürtöt, hogy Azure Monitor naplókat használjon, és hozzáadta a HDInsight-fürthöz tartozó Azure Monitor naplók figyelési megoldásait a munkaterülethez. Útmutatásért lásd: [Azure monitor naplók használata HDInsight-fürtökkel](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight-fürt metrikáinak elemzése

Megtudhatja, hogyan kereshet konkrét mérőszámokat a HDInsight-fürthöz.

1. Nyissa meg a HDInsight-fürthöz társított Log Analytics munkaterületet a Azure Portal.
1. Az **Általános** területen válassza a **Naplók** elemet.
1. Írja be a következő lekérdezést a keresőmezőbe, hogy megkeresse az összes rendelkezésre álló metrikát a Azure Monitor naplók használatára konfigurált összes HDInsight-fürthöz, majd válassza a **Futtatás** lehetőséget. Tekintse át az eredményeket.

    ```kusto
    search *
    ```

    ![Apache Ambari Analytics keresés az összes mérőszámban](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Keresés az összes mérőszámban")

1. A bal oldali menüben válassza a **szűrő** fület.

1. A **típus** területen válassza a **szívverés** lehetőséget. Ezután válassza az **alkalmaz & Futtatás** elemet.

    ![log Analytics-keresés – specifikus mérőszámok](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Konkrét mérőszámok keresése")

1. Figyelje meg, hogy a szövegmezőben a lekérdezés a következőre változik:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. A bal oldali menüben elérhető beállításokkal mélyebbre is kihasználhatja. Például:

    - Egy adott csomópont naplófájljainak megtekintéséhez:

        ![Adott hibák keresése output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Adott hibák keresése output1")

    - Naplók megtekintése bizonyos időpontokban:

        ![Adott hibák keresése output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Adott hibák keresése output2")

1. Válassza az **alkalmaz & Futtatás** elemet, és tekintse át az eredményeket. Azt is vegye figyelembe, hogy a lekérdezés frissítve lett a következőre:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>További példák a lekérdezésekre

A 10 perces intervallumban használt erőforrások átlagán alapuló minta-lekérdezés, a fürt neve szerint kategorizálva:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

A felhasznált erőforrások átlaga alapján történő finomítás helyett a következő lekérdezéssel finomíthatja az eredményeket, ha a maximális erőforrást (valamint a 90-es és a 95. percentilis-t) használja egy 10 perces ablakban:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Riasztások létrehozása a követési eseményekhez

A riasztás létrehozásának első lépése egy olyan lekérdezés megérkezése, amely alapján a riasztás aktiválva van. Bármely olyan lekérdezést használhat, amelyhez riasztást szeretne létrehozni.

1. Nyissa meg a HDInsight-fürthöz társított Log Analytics munkaterületet a Azure Portal.
1. Az **Általános** területen válassza a **Naplók** elemet.
1. Futtassa a következő lekérdezést, amelyen riasztást szeretne létrehozni, majd válassza a **Futtatás** lehetőséget.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    A lekérdezés a HDInsight-fürtökön futó sikertelen alkalmazások listáját tartalmazza.

1. Az oldal tetején válassza az **új riasztási szabály** lehetőséget.

    ![Lekérdezés megadása alert1 létrehozásához](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Lekérdezés megadása alert1 létrehozásához")

1. A **szabály létrehozása** ablakban adja meg a lekérdezést és egyéb adatokat a riasztás létrehozásához, majd válassza a **riasztási szabály létrehozása** lehetőséget.

    ![Lekérdezés megadása alert2 létrehozásához](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Lekérdezés megadása alert2 létrehozásához")

### <a name="edit-or-delete-an-existing-alert"></a>Meglévő riasztás szerkesztése vagy törlése

1. Nyissa meg az Log Analytics munkaterületet a Azure Portal.

1. A bal oldali menüben a **figyelés** területen válassza a **riasztások** lehetőséget.

1. A felső részen válassza a **riasztási szabályok kezelése** lehetőséget.

1. Válassza ki a szerkeszteni vagy törölni kívánt riasztást.

1. A következő lehetőségek közül választhat: **Mentés**, **Elvetés**, **Letiltás** és **Törlés**.

    ![HDInsight Azure Monitor naplók riasztás törlésének szerkesztése](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

További információ: [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Lásd még

* [Ismerkedés az Azure Monitor-naplólekérdezésekkel](../azure-monitor/log-query/get-started-queries.md)
* [Egyéni nézetek létrehozása a Azure Monitor View Designer használatával](../azure-monitor/platform/view-designer.md)
