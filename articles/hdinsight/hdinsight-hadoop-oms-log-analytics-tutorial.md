---
title: Azure HDInsight-fürtök figyelése Azure Monitor naplók használatával
description: Megtudhatja, hogyan használhatók Azure Monitor naplók a HDInsight-fürtön futó feladatok figyeléséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 4007d18291e91ae2a72d7a99dac2cb3a8bc2877a
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143851"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor-naplók használata HDInsight-fürtök monitorozásához

Megtudhatja, hogyan engedélyezheti Azure Monitor naplók a Hadoop figyelését a HDInsight-ben. És hogyan adhat hozzá HDInsight-figyelési megoldást.

[Azure monitor a naplók](../log-analytics/log-analytics-overview.md) egy Azure monitor szolgáltatás, amely figyeli a Felhőbeli és a helyszíni környezeteket. A figyelés a rendelkezésre állás és a teljesítmény fenntartása. A felhőben, a helyszíni környezetekben és más figyelési eszközökben lévő erőforrások által generált adatokat gyűjt. A rendszer a több forrásból származó elemzések megadására szolgál.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

* Egy Log Analytics-munkaterület. Ezt a munkaterületet a saját adattárházával, adatforrásaival és megoldásaival kapcsolatos egyedi Azure Monitor-naplózási környezetnek tekintheti át. Az utasításokért lásd: [log Analytics munkaterület létrehozása](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Egy Azure-beli HDInsight-fürt. Jelenleg a következő HDInsight-fürtökkel rendelkező Azure Monitor naplókat használhatja:

  * Hadoop
  * HBase
  * Interaktív lekérdezés
  * Kafka
  * Spark
  * Storm

  A HDInsight-fürtök létrehozásával kapcsolatos útmutatásért lásd: Ismerkedés [Az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Azure PowerShell az modul.  Lásd: [az új Azure PowerShell bemutatása az Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Győződjön meg arról, hogy a legújabb verzióval rendelkezik. Ha szükséges, futtassa `Update-Module -Name Az`a parancsot.

> [!NOTE]  
> A jobb teljesítmény érdekében ajánlott a HDInsight-fürtöt és a Log Analytics munkaterületet ugyanabba a régióba helyezni. Azure Monitor naplók nem érhetők el az összes Azure-régióban.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Azure Monitor naplók engedélyezése a portál használatával

Ebben a szakaszban egy meglévő HDInsight-Hadoop-fürtöt konfigurál egy Azure Log Analytics munkaterület használatára a feladatok, a hibakeresési naplók és így tovább.

1. A [Azure Portal](https://portal.azure.com/)válassza ki a fürtöt. A fürt megnyílik egy új portál oldalon.

1. A bal oldalon a **figyelés**területen válassza a **Azure monitor**lehetőséget.

1. A fő nézet **Azure monitor integráció**területén válassza az **Engedélyezés**lehetőséget.

1. A **munkaterület kiválasztása** legördülő listából válasszon ki egy meglévő log Analytics munkaterületet.

1. Kattintson a **Mentés** gombra.  A beállítás mentése néhány percet vesz igénybe.

    ![HDInsight-fürtök figyelésének engedélyezése](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "HDInsight-fürtök figyelésének engedélyezése")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure Monitor naplók engedélyezése Azure PowerShell használatával

Azure Monitor naplókat az Azure PowerShell az Module [enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) parancsmag használatával engedélyezheti.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

A letiltásához használja a [disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) parancsmagot:

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight-fürt felügyeleti megoldásainak telepítése

A HDInsight olyan fürtözött felügyeleti megoldásokat biztosít, amelyeket hozzáadhat Azure Monitor naplókhoz. A [felügyeleti megoldások](../log-analytics/log-analytics-add-solutions.md) funkciókkal bővítik Azure monitor naplókat, amelyek további adat-és elemzési eszközöket biztosítanak. Ezek a megoldások fontos teljesítmény-mérőszámokat gyűjtenek a HDInsight-fürtökről. És adja meg a metrikák kereséséhez szükséges eszközöket. Ezek a megoldások vizualizációkat és irányítópultokat is biztosítanak a HDInsight által támogatott legtöbb típusú fürthöz. A megoldással gyűjtött metrikák használatával egyéni figyelési szabályokat és riasztásokat hozhat létre.

Elérhető HDInsight-megoldások:

* HDInsight Hadoop-figyelés
* HDInsight HBase Monitoring
* HDInsight interaktív lekérdezés figyelése
* HDInsight Kafka-figyelés
* HDInsight Spark-figyelés
* HDInsight Storm-monitorozás

A felügyeleti megoldással kapcsolatos utasításokért lásd: [felügyeleti megoldások az Azure-ban](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). A kísérlethez telepítsen egy HDInsight Hadoop-figyelési megoldást. Ha elkészült, megjelenik egy **HDInsightHadoop** csempe az **Összefoglalás**területen. Válassza a **HDInsightHadoop** csempét. A HDInsightHadoop-megoldás így néz ki:

![HDInsight-figyelési megoldás nézete](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Mivel a fürt egy teljesen új fürt, a jelentés nem jeleníti meg a tevékenységeket.

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása

Az Azure monitor támogatja a fürt csomópontjaihoz tartozó teljesítmény-mérőszámok gyűjtését és elemzését. További információ: [Linux Performance adatforrások a Azure monitor-ben](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Fürt naplózása

A HDInsight a következő típusú naplók importálásával támogatja a fürt naplózását Azure Monitor naplókkal:

* `log_gateway_audit_CL`– Ez a táblázat a fürt átjárójának csomópontjairól nyújt naplókat, amelyek sikeres és sikertelen bejelentkezési kísérleteket mutatnak.
* `log_auth_CL`– Ez a táblázat a sikeres és sikertelen bejelentkezési kísérletekkel rendelkező SSH-naplókat tartalmaz.
* `log_ambari_audit_CL`– Ez a tábla naplókat biztosít a Ambari.
* `log_ranger_audti_CL`– Ez a táblázat az Apache Ranger és az ESP-fürtök naplóit tartalmazza.

## <a name="next-steps"></a>További lépések

* [Azure Monitor naplók lekérdezése HDInsight-fürtök figyeléséhez](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [A fürt rendelkezésre állásának figyelése az Apache Ambari és a Azure Monitor naplók használatával](./hdinsight-cluster-availability.md)
