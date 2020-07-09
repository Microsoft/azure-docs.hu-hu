---
title: Linux-fürtök eseményeinek figyelése az Azure-ban Service Fabric
description: Megtudhatja, hogyan figyelheti Service Fabric linuxos fürtök eseményeit Service Fabric platform eseményeinek a syslog-be való írásával.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75645752"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Linux-fürt eseményeinek Service Fabric a syslog-ben

Service Fabric platform-eseményeket tesz elérhetővé, amelyekkel tájékoztathatja a fürt fontos tevékenységeit. A feltehetően elérhető események teljes listája [itt](service-fabric-diagnostics-event-generation-operational.md)érhető el. Többféle módon is felhasználhatja ezeket az eseményeket. Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Service Fabrict, hogy ezeket az eseményeket a syslog-be írja.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introduction (Bevezetés)

Az 6,4-es kiadásban a SyslogConsumer bevezették a Service Fabric platform eseményeinek a syslog for Linux-fürtökbe való küldéséhez. Ha bekapcsolta, az események automatikusan a syslog-be kerülnek, amelyet a Log Analytics ügynök gyűjthet és küldhet.

Minden syslog-esemény 4 összetevővel rendelkezik
* Létesítmény
* Identitás
* Üzenet
* Severity

A SyslogConsumer minden platform-eseményt ír a létesítmény használatával `Local0` . A konfiguráció konfigurációjának módosításával bármely érvényes létesítményre frissítheti. A használt identitás: `ServiceFabric` . Az üzenet mező a JSON-ban szerializált teljes eseményt tartalmazza, így számos eszközről lekérdezhető és felhasználható. 

## <a name="enable-syslogconsumer"></a>SyslogConsumer engedélyezése

A SyslogConsumer engedélyezéséhez végre kell hajtania a fürt frissítését. A `fabricSettings` szakaszt a következő kóddal kell frissíteni. Megjegyzés Ez a kód csak a SyslogConsumer kapcsolatos részeket tartalmazza

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Itt láthatók a meghívott változások
1. A Common (közös) szakaszban a nevű új paraméter szerepel `LinuxStructuredTracesEnabled` . **Erre azért van szükség, hogy a rendszer a syslog szolgáltatásba való elküldésekor strukturálja és szerializálja a Linux-eseményeket.**
2. A diagnosztika szakaszban új ConsumerInstance: SyslogConsumer lett hozzáadva. Ez azt jelzi, hogy a platformon van egy másik fogyasztó az eseményekről. 
3. Az új szakasznak SyslogConsumer kell lennie `IsEnabled` `true` . Úgy van konfigurálva, hogy automatikusan használja a Local0-létesítményt. Ezt felülbírálhatja egy másik paraméter hozzáadásával.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Azure Monitor naplók integrációja
Ezeket a syslog-eseményeket megtekintheti egy figyelési eszközben, például Azure Monitor naplókban. Az alábbi [utasítások] használatával létrehozhat egy Log Analytics munkaterületet az Azure Marketplace használatával. (.. /Azure-monitor/Learn/Quick-Create-Workspace.MD) Emellett hozzá kell adnia a Log Analytics-ügynököt a fürthöz, hogy adatokat gyűjtsön és küldjön a munkaterületre. Ez ugyanaz az ügynök, amely a teljesítményszámlálók gyűjtésére szolgál. 

1. Navigáljon a `Advanced Settings` panelre

    ![Munkaterület beállításai](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Kattintson a következőre: `Data`
3. Kattintson a következőre: `Syslog`
4. Konfigurálja a Local0 a nyomon követési lehetőségként. Ha módosította a fabricSettings-ben, további létesítményt is hozzáadhat.

    ![A syslog konfigurálása](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. A `Logs` lekérdezés indításához a munkaterület-erőforrás menüjére kattintva lépjen a lekérdezési tallózóba.

    ![Munkaterület naplófájljai](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Lekérdezheti a `Syslog` processname keresett táblát `ServiceFabric` . Az alábbi lekérdezés szemlélteti, hogyan elemezheti a JSON-t az eseményen, és megjelenítheti annak tartalmát

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog-lekérdezés](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

A fenti példa egy NodeDown esemény. Az események teljes listáját [itt](service-fabric-diagnostics-event-generation-operational.md)tekintheti meg.

## <a name="next-steps"></a>További lépések
* [A log Analytics-ügynök üzembe helyezése](service-fabric-diagnostics-oms-agent.md) a csomópontokon a teljesítményszámlálók összegyűjtéséhez, valamint a tárolók Docker-statisztikáinak és naplóinak összegyűjtéséhez
* Ismerkedjen meg az Azure Monitor naplók részeként kínált [naplóbeli keresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciókkal
* [Egyéni nézetek létrehozása Azure Monitor naplókban a Tervező nézet használatával](../log-analytics/log-analytics-view-designer.md)
* Útmutató a [naplók Azure monitor a syslog-integrációhoz](../log-analytics/log-analytics-data-sources-syslog.md).
