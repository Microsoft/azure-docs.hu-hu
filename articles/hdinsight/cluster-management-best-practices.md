---
title: A cluster Management ajánlott eljárásai – Azure HDInsight
description: Ismerje meg a HDInsight-fürtök kezelésének ajánlott eljárásait.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 40222b6a108976de9c82ffccee119b1c1c55f334
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505749"
---
# <a name="hdinsight-cluster-management-best-practices"></a>HDInsight-fürt kezelése – ajánlott eljárások

Ismerje meg a HDInsight-fürtök kezelésének ajánlott eljárásait.

## <a name="how-do-i-create-hdinsight-clusters"></a>Hogyan hozzon létre HDInsight-fürtöket?

| Beállítás | Dokumentumok |
|---|---|
| Azure Data Factory | [Igény szerinti Apache Hadoop-fürtök létrehozása a HDInsight-ben Azure Data Factory használatával](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Egyéni Resource Manager-sablon | [Apache Hadoop-fürtök létrehozása a HDInsight-ben Resource Manager-sablonok használatával](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Gyorssablonok | [HDInsight gyors üzembe helyezési sablonok](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure-minták | [HDInsight Azure-minták](/samples/browse/?products=azure-hdinsight) |
| Azure Portal | [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure Portal használatával](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [HDInsight-fürtök létrehozása az Azure CLI használatával](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure PowerShell használatával](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Apache Hadoop-fürtök létrehozása az Azure REST API használatával](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK-k (.NET, Python, Java) | [.Net](/dotnet/api/overview/azure/hdinsight), [Python](/python/api/overview/azure/hdinsight), [Java](/java/api/overview/azure/hdinsight), [Go](./hdinsight-go-sdk-overview.md) |

> [!Note]
> Ha egy korábban létrehozott fürtből hoz létre fürtöt, és újból felhasználja a fürtöt, várjon, amíg a fürt létrehozása előtt a fürt korábbi törlése be nem fejeződik.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Hogyan testreszabja a HDInsight-fürtöket?

| Beállítás | Dokumentumok |
|---|---|
| Parancsfájlok műveletei | [Azure HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [HDInsight-fürtök testreszabása a bootstrap használatával](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Külső metaadattárak | [Külső metaadattárak használata az Azure HDInsightban](./hdinsight-use-external-metadata-stores.md) |
| Egyéni Ambari-adatbázis | [HDInsight-fürtök beállítása egyéni Ambari-ADATBÁZISsal](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Milyen hibák merülhetnek fel fürtök létrehozásakor?

| Hiba | További információ |
|---|---|
| Nincs kvóta | Az előfizetéshez az egyes régiókban létrehozható magok száma kvótát használ. További információ [: kapacitás megtervezése: kvóták](./hdinsight-capacity-planning.md). |
| Nem érhető el több IP-cím | Minden VNet korlátozott számú IP-címmel rendelkezik. HDInsight-fürt létrehozásakor az egyes csomópontok (beleértve a Zookeeper és az átjáró csomópontokat is) a kiosztott IP-címek némelyikét használják. Ha az összes IP-cím használatban van, ezt a hibát fogja tapasztalni.  |
| A hálózati biztonsági csoport (NSG) szabályai nem teszik lehetővé a HDInsight erőforrás-szolgáltatókkal való kommunikációt | Ha NSG vagy felhasználó által definiált útvonalakat (UDR) használ a HDInsight-fürt bejövő forgalmának vezérlésére, akkor biztosítania kell, hogy a fürt képes legyen kommunikálni a kritikus Azure-beli állapot-és felügyeleti szolgáltatásokkal. További információ: [hálózati biztonsági csoport (NSG) szolgáltatás címkéi az Azure HDInsight](./hdinsight-service-tags.md) |
| A fürt nevének újbóli használata | Ha olyan fürtöt használ, amelyet korábban használt, akkor a fürt újbóli létrehozása előtt meg kell várnia a percek számát. Ellenkező esetben egy üzenet jelenik meg, amely szerint az erőforrás már létezik. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Hogyan a futó HDInsight-fürtök kezelése?

| Beállítás | Dokumentumok |
|---|---|
| Automatikus méretezés | [Azure HDInsight-fürtök automatikus skálázása](./hdinsight-autoscale-clusters.md) |
| Manuális skálázás | [Azure HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md) |
| Figyelés a Ambari| [A fürt teljesítményének figyelése az Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Figyelés Azure Monitor naplókkal | [Azure Monitor-naplók használata HDInsight-fürtök monitorozásához](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |
| Szolgáltatási problémák, tervezett karbantartás, Health & biztonsági tanácsadók | [Előfizetés-specifikus szolgáltatás állapotára vonatkozó riasztások](../service-health/alerts-activity-log-service-notifications-portal.md) |


## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Hogyan a törölt HDInsight-fürtök ellenőrzését?

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A törölt fürtök figyeléséhez használja a következő lekérdezést Azure Monitor naplókkal.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kapacitástervezése](./hdinsight-capacity-planning.md)
* [Mik az Azure HDInsight alapértelmezett és ajánlott csomópont-konfigurációi?](./hdinsight-supported-node-configuration.md)
