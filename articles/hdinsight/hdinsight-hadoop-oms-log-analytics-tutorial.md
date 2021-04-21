---
title: A Azure Monitor naplók használata a Azure HDInsight figyelése
description: Megtudhatja, hogyan használhatja a Azure Monitor a HDInsight-fürtökben futó feladatok figyelése érdekében.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 2a81b25b08708a878fc8ff83cf19c643036b8f90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770326"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor-naplók használata HDInsight-fürtök monitorozásához

Ismerje meg, hogyan engedélyezheti a Azure Monitor a Hadoop-fürtműveleteket a HDInsightban. És hogyan adhat hozzá HDInsight monitorozási megoldást.

[Azure Monitor naplók](../azure-monitor/logs/log-query-overview.md) egy Azure Monitor szolgáltatás, amely figyeli a felhőbeli és a helyszíni környezeteket. A figyelés a rendelkezésre állásuk és a teljesítményük fenntartása. A felhőben, a helyszíni környezetekben és más monitorozási eszközökben található erőforrások által létrehozott adatokat gyűjti. Az adatok több forrás elemzésére használhatók.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Egy Log Analytics-munkaterület. Ez a munkaterület egy egyedi adatnapló-Azure Monitor a saját adattárával, adatforrásaival és megoldásaival. Az utasításokért [lásd: Log Analytics-munkaterület létrehozása.](../azure-monitor/vm/quick-collect-azurevm.md#create-a-workspace)

* Egy Azure-beli HDInsight-fürt. Jelenleg a következő HDInsight Azure Monitor fürttípusokkal használhatja a naplókat:

  * Hadoop
  * HBase
  * Interaktív lekérdezés
  * Kafka
  * Spark
  * Storm

  A HDInsight-fürtök létrehozására vonatkozó utasításokért lásd: [Get started with Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* PowerShell használata esetén szüksége lesz az [Az modulra.](/powershell/azure/) Győződjön meg arról, hogy a legújabb verzióval van. Ha szükséges, futtassa az `Update-Module -Name Az` futtatását.

* Ha az Azure CLI-t szeretné használni, de még nem telepítette, tekintse meg az [Azure CLI telepítését.](/cli/azure/install-azure-cli)

> [!NOTE]  
> A jobb teljesítmény érdekében javasoljuk, hogy a HDInsight-fürtöt és a Log Analytics-munkaterületet is helyezze ugyanabba a régióba. Azure Monitor naplók nem érhetők el minden Azure-régióban.

## <a name="enable-azure-monitor-using-the-portal"></a>A Azure Monitor engedélyezése a portál használatával

Ebben a szakaszban egy meglévő HDInsight Hadoop-fürtöt fog konfigurálni egy Azure Log Analytics-munkaterület használatára a feladatok figyelése, a naplók hibakeresése stb. érdekében.

1. A [Azure Portal](https://portal.azure.com/)válassza ki a fürtöt. A fürt egy új portállapon nyílik meg.

1. A bal oldali Figyelés **alatt** válassza **a** Azure Monitor.

1. A fő nézet integrációs **Azure Monitor válassza** az **Engedélyezés lehetőséget.**

1. A Munkaterület **kiválasztása legördülő** listában válasszon ki egy meglévő Log Analytics-munkaterületet.

1. Kattintson a **Mentés** gombra.  A beállítás mentése néhány percet vesz igénybe.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="HDInsight-fürtök monitorozásának engedélyezése":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Az Azure Monitor engedélyezése a Azure PowerShell

A naplókat Azure Monitor Az modul [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) parancsmag Azure PowerShell engedélyezheti.

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

A letiltásához használja a [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) parancsmagot:

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Az Azure Monitor engedélyezése az Azure CLI használatával

A naplókat Azure Monitor Azure CLI `[az hdinsight monitor enable` ](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable) paranccsal engedélyezheti.

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

A letiltásához használja a [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) parancsot.

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight-fürtkezelési megoldások telepítése

A HDInsight fürtspecifikus felügyeleti megoldásokat biztosít, amelyek hozzáadhatóak Azure Monitor naplókhoz. [A felügyeleti megoldások](../azure-monitor/insights/solutions.md) további funkciókkal Azure Monitor naplókat, további adat- és elemzőeszközöket biztosítva. Ezek a megoldások fontos teljesítménymetrikákat gyűjtenek a HDInsight-fürtökről. És adja meg a metrikák kereséséhez szükséges eszközöket. Ezek a megoldások vizualizációkat és irányítópultokat is biztosítanak a HDInsight által támogatott legtöbb fürttípushoz. A megoldással gyűjtött metrikák használatával egyéni monitorozási szabályokat és riasztásokat hozhat létre.

Elérhető HDInsight-megoldások:

* HDInsight Hadoop-monitorozás
* HDInsight HBase Monitoring
* HDInsight Interactive Query Monitorozás
* HDInsight Kafka-monitorozás
* HDInsight Spark-monitorozás
* HDInsight Storm-monitorozás

A felügyeleti megoldásokkal kapcsolatos utasításokért lásd: Felügyeleti [megoldások az Azure-ban.](../azure-monitor/insights/solutions.md#install-a-monitoring-solution) A kísérletezéshez telepítsen egy HDInsight Hadoop monitorozási megoldást. Ha végzett, megjelenik egy **HDInsightHadoop** csempe az Összefoglalás **alatt.** Válassza a **HDInsightHadoop csempét.** A HDInsightHadoop megoldás a következő:

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="HDInsight monitorozási megoldás nézete":::

Mivel a fürt egy teljesen új fürt, a jelentés nem mutat semmilyen tevékenységet.

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása

Az Azure Monitor támogatja a fürt csomópontjainak teljesítménymetrikák gyűjtését és elemzését. További információ: [Linux-teljesítmény-adatforrások a Azure Monitor.](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)

## <a name="cluster-auditing"></a>Fürt naplózása

A HDInsight a fürtnaplók Azure Monitor a következő naplótípusok importálásával támogatja a fürtnaplózást:

* `log_gateway_audit_CL` – Ez a táblázat a fürtátjáró-csomópontok auditnaplóit tartalmazza, amelyek a sikeres és sikertelen bejelentkezési kísérleteket mutatják.
* `log_auth_CL` – ez a táblázat SSH-naplókat biztosít sikeres és sikertelen bejelentkezési kísérletekkel.
* `log_ambari_audit_CL` – ez a táblázat az Ambari auditnaplóit tartalmazza.
* `log_ranger_audti_CL` – ez a táblázat az Apache Ranger auditnaplóit tartalmazza ESP-fürtökön.

## <a name="next-steps"></a>Következő lépések

* [HDInsight Azure Monitor fürtök figyelése naplók lekérdezése](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Fürtök rendelkezésre állásának figyelése az Apache Ambari és a Azure Monitor naplók segítségével](./hdinsight-cluster-availability.md)