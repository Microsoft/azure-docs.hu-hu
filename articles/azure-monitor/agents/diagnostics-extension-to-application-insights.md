---
title: Azure Diagnostics-adatküldés Application Insights
description: A Azure Diagnostics nyilvános konfigurációjának frissítése az adatApplication Insightsba való adatküldéshez.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: b9e9d6b1b5939804b24fd523bf8b7444ed41178f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708594"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>A felhőalapú szolgáltatás, a virtuális gép vagy a Service Fabric diagnosztikai adatbázis küldése Application Insights
A Cloud Services, a Virtual Machines, a Virtual Machine Scale Sets és a Service Fabric mind a Azure Diagnostics bővítményt használja az adatok gyűjtéséhez.  Az Azure Diagnostics adatokat küld az Azure Storage-táblákba.  Az Azure Diagnostics Extension 1,5-es vagy újabb verziójának használatával azonban az összes vagy egy részhalmazát is áthelyezheti más helyszínekre.

Ez a cikk azt ismerteti, hogyan lehet adatok küldését a Azure Diagnostics bővítményből a Application Insightsba.

## <a name="diagnostics-configuration-explained"></a>Diagnosztika konfigurációjának ismertetése
Az Azure Diagnostics bővítmény 1,5 bevezette a mosogatókat, amelyek olyan további helyekre mutatnak, ahol diagnosztikai adatait küldhet.

Példa a fogadó Application Insightsra való konfigurálására:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- A **fogadó** *neve* attribútum egy karakterlánc-érték, amely egyedileg azonosítja a fogadót.

- A **ApplicationInsights** elem határozza meg az Application ininsight-erőforrás rendszerállapot-kulcsát, ahol az Azure Diagnostics-adatait küldik.
    - Ha nem rendelkezik meglévő Application Insights erőforrással, az erőforrások létrehozásával és a kialakítási kulcs beszerzésével kapcsolatos további információkért tekintse meg az [új Application Insights-erőforrás létrehozása](../app/create-new-resource.md) című témakört.
    - Ha felhőalapú szolgáltatást fejleszt az Azure SDK 2,8-as és újabb verzióival, a rendszer automatikusan kitölti ezt a kialakítási kulcsot. Az érték a Cloud Service-projekt csomagolásakor a **APPINSIGHTS_INSTRUMENTATIONKEY** szolgáltatás konfigurációs beállításán alapul. Lásd: [a Application Insights használata a Cloud Services](../app/cloudservices.md).

- A **channels** elem egy vagy több csatorna **elemet tartalmaz** .
    - A *Name* attribútum egyedi módon hivatkozik erre a csatornára.
    - A *naplózási szint* attribútum lehetővé teszi a csatorna által engedélyezett naplózási szint megadását. A rendelkezésre álló naplózási szintek a legkevesebb információt a következő sorrendben használják:
        - Részletes
        - Tájékoztatás
        - Figyelmeztetés
        - Hiba
        - Kritikus

Egy csatorna úgy viselkedik, mint egy szűrő, és lehetővé teszi, hogy kiválassza a megadott naplózási szinteket, amelyeket el szeretne küldeni a célhelyre. Például összegyűjthet részletes naplókat, és elküldheti őket a tárolóba, de csak hibákat küldhet a fogadónak.

A következő ábra ezt a kapcsolatot mutatja be.

![Diagnosztika – nyilvános konfiguráció](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

A következő ábra összefoglalja a konfigurációs értékeket és azok működését. A konfigurációban több mosogató is szerepelhet a hierarchia különböző szintjein. A legfelső szinten lévő fogadó globális beállításként működik, az egyes elemekben megadott érték pedig felülbírálja a globális beállítást.

![Diagnosztikai adatnyelők konfigurációja Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Példa teljes fogadó konfigurációra
Íme egy teljes példa arra a nyilvános konfigurációs fájlra, amely
1. az összes hibát elküldi a Application Insightsnak (a **DiagnosticMonitorConfiguration** csomópontban van megadva)
2. Emellett a részletes szintű naplókat is elküldi az alkalmazás naplóihoz (a **naplók** csomópontban van megadva).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
Az előző konfigurációban a következő sorok a következő jelentésekkel rendelkeznek:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Az Azure Diagnostics által összegyűjtött összes adatok elküldése

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Csak a hiba naplóinak küldése a Application Insights fogadónak

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Részletes alkalmazási naplók küldése Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Korlátozások

- **A csatornák csak a napló típusát és a teljesítményszámlálókat nem.** Ha megad egy csatornát a teljesítményszámláló elemmel, azt a rendszer figyelmen kívül hagyja.
- **Egy csatorna naplózási szintje nem haladhatja meg az Azure Diagnostics által összegyűjtött naplózási szintet.** Nem gyűjthet például az alkalmazásnapló-hibákat a naplók elemben, és megpróbálhatja részletes naplókat küldeni az alkalmazás Insight fogadójának. A *scheduledTransferLogLevelFilter* attribútumnak mindig egyenlő vagy annál több naplót kell összegyűjtenie, mint amennyit a fogadónak elküldeni próbált naplók.
- **Az Azure Diagnostics bővítmény által gyűjtött blob-adatok nem küldhetők Application Insights.** Például a *címtárak* csomópontban megadott minden adat. Az összeomlási memóriaképek esetében a rendszer a tényleges összeomlási memóriaképet küldi el a blob Storage-nak, és csak egy értesítést küld a rendszer az összeomlási memóriakép létrejöttéről Application Insights.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [tekintheti meg az Azure diagnosztikai adatait](../app/cloudservices.md) Application Insightsban.
* A [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) használatával engedélyezze az Azure Diagnostics bővítményt az alkalmazáshoz.
* A [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) használata az Azure Diagnostics bővítmény engedélyezéséhez az alkalmazáshoz

