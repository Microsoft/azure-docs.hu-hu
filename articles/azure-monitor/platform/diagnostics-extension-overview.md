---
title: Az Azure Diagnostics bővítmény áttekintése
description: Az Azure Diagnostics használata hibakereséshez, teljesítmény méréséhez, monitorozáshoz, Traffic Analysis in Cloud Services, Virtual Machines és Service Fabric
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 4d424a22a26119dcb3ef6a0e5c4f3dc0c13b1aa4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927585"
---
# <a name="azure-diagnostics-extension-overview"></a>Az Azure Diagnostics bővítmény áttekintése
A Azure Diagnostics Extension olyan [Azure monitor ügynöke](agents-overview.md) , amely az Azure számítási erőforrások, például a virtuális gépek vendég operációs rendszerének figyelési adatait gyűjti. Ez a cikk áttekintést nyújt Azure Diagnostics bővítményről, beleértve az általa támogatott speciális funkciókat, valamint a telepítés és a konfigurálás lehetőségeit. 

> [!NOTE]
> Azure Diagnostics bővítmény az egyik rendelkezésre álló ügynök, amely a számítási erőforrások vendég operációs rendszeréről gyűjti a figyelési adatokat. Lásd: [a Azure monitor ügynökök áttekintése ](agents-overview.md) a különböző ügynökök leírására, valamint a megfelelő ügynökök kiválasztására vonatkozó útmutatást.

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek
A diagnosztikai bővítmény által megcélzott elsődleges forgatókönyvek a következők:

- Vendég metrikák begyűjtése Azure Monitor mérőszámokra.
- Vendég naplók és mérőszámok küldése az Azure Storage-ba archiválás céljából.
- Vendég naplók és mérőszámok küldése az Azure Event hubokba az Azure-on kívüli küldéshez.


## <a name="comparison-to-log-analytics-agent"></a>Összehasonlítás Log Analytics ügynökkel
A Azure Monitor Log Analytics ügynöke is használható a virtuális gépek vendég operációs rendszerének figyelési adatainak gyűjtésére. Dönthet úgy is, hogy a követelményektől függően vagy mindkettőt használja. A Azure Monitor-ügynökök részletes összehasonlítását lásd [a Azure monitor ügynökök áttekintésében](agents-overview.md) . 

A figyelembe venni kívánt fő különbségek a következők:

- Azure Diagnostics bővítmény csak az Azure Virtual Machines használatával használható. Az Log Analytics-ügynök használható az Azure-ban, a többi felhőkben és a helyszínen lévő virtuális gépekkel.
- Azure Diagnostics a bővítmény adatokat küld az Azure Storage-ba, [Azure monitor metrikákat](data-platform-metrics.md) (csak Windows) és Event Hubs. A Log Analytics ügynök adatokat gyűjt [Azure monitor naplókhoz](data-platform-logs.md).
- A Log Analytics ügynök szükséges a [megoldások](../monitor-reference.md#insights-and-core-solutions), [Azure monitor for VMS](../insights/vminsights-overview.md)és egyéb szolgáltatások, például a [Azure Security Center](../../security-center/index.yml)esetében.

## <a name="costs"></a>Költségek
Az Azure diagnosztikai bővítmény használatáért nem számítunk fel díjat, de díjkötelesek lehetnek a betöltött adatokért járó díjak. Tekintse meg [Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/) azon a célhelyen, amelyben az adatok gyűjtése folyamatban van.

## <a name="data-collected"></a>Összegyűjtött adatok
Az alábbi táblázatok felsorolják azokat az adatokat, amelyeket a Windows és a Linux diagnosztikai bővítménye is gyűjthet.

### <a name="windows-diagnostics-extension-wad"></a>Windows diagnosztikai bővítmény (WAD)

| Adatforrás | Description |
| --- | --- |
| Windows-eseménynaplók   | Események a Windows-eseménynaplóból. |
| Teljesítményszámlálók | Az operációs rendszer és a számítási feladatok különböző szempontjainak teljesítményét mérő numerikus értékek. |
| IIS-naplók             | A vendég operációs rendszeren futó IIS-webhelyek használati adatai. |
| Alkalmazás-naplók     | Az alkalmazás által írt nyomkövetési üzenetek. |
| .NET EventSource-naplók |Események írása a .NET [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) osztály használatával |
| [Jegyzékfájl-alapú ETW-naplók](/windows/desktop/etw/about-event-tracing) |Windows esemény-nyomkövetés egy folyamat által generált eseményeket. |
| Összeomlási memóriaképek (naplók)   | Információ a folyamat állapotáról, ha egy alkalmazás összeomlik. |
| Fájl alapú naplók    | Az alkalmazás vagy szolgáltatás által létrehozott naplók. |
| Ügynök diagnosztikai naplói | Információk a Azure Diagnosticsról. |


### <a name="linux-diagnostics-extension-lad"></a>Linux diagnosztikai bővítmény (LAD)

| Adatforrás | Description |
| --- | --- |
| Rendszernapló | A Linux-eseménynaplózási rendszernek eljuttatott események.   |
| Teljesítményszámlálók  | Az operációs rendszer és a számítási feladatok különböző szempontjainak teljesítményét mérő numerikus értékek. |
| Naplófájlok | A fájl alapú naplóba eljuttatott bejegyzések.  |

## <a name="data-destinations"></a>Adatelérési helyek
A Windows és a Linux rendszerhez készült Azure diagnosztikai bővítmény mindig egy Azure Storage-fiókba gyűjti az adatokat. Lásd: a [Windows Azure Diagnostics bővítmény (wad) telepítése és konfigurálása](diagnostics-extension-windows-install.md) , valamint a [Linux diagnosztikai bővítmény használata a metrikák és naplók figyelésére](../../virtual-machines/extensions/diagnostics-linux.md) az adott táblák és Blobok listájához, ahol az adatok gyűjtése történik.

Konfiguráljon egy vagy több *adattárolót* az egyéb további célhelyekre való adatküldéshez. A következő részei a Windows és a Linux diagnosztikai bővítmény számára elérhető adatnyelőket sorolja fel.

### <a name="windows-diagnostics-extension-wad"></a>Windows diagnosztikai bővítmény (WAD)

| Cél | Description |
|:---|:---|
| Azure Monitor-metrikák | Teljesítményadatokat gyűjt Azure Monitor Mérőszámokhoz. Lásd: [vendég operációs rendszer metrikáinak küldése a Azure monitor metrikus adatbázisba](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Event Hubs | Az Azure Event Hubs használatával küldhet az Azure-on kívüli adatküldést. Lásd: [Streaming Azure Diagnostics-adatátviteli Event Hubs](diagnostics-extension-stream-event-hubs.md) |
| Azure Storage-Blobok | Adatok írása a Blobok számára az Azure Storage-ban a táblák mellett. |
| Application Insights | Gyűjtsön adatokat a virtuális gépen futó alkalmazásokból, hogy Application Insights, hogy integrálható legyen más alkalmazás-figyeléssel. Lásd: [diagnosztikai információk küldése Application Insightsba](diagnostics-extension-to-application-insights.md). |

A tárolóban lévő WAD-adatokat egy Log Analytics munkaterületre is gyűjtheti, hogy elemezze Azure Monitor-naplókat, bár a Log Analytics ügynök jellemzően ehhez a funkcióhoz használatos. Az adatküldés közvetlenül egy Log Analytics munkaterületre képes, és támogatja a további funkciókat biztosító megoldásokat és elemzéseket.  Lásd: [Azure-beli diagnosztikai naplók gyűjtése az Azure Storage-ból](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Linux diagnosztikai bővítmény (LAD)
A LAD az Azure Storage tábláiba írja az adatot. A következő táblázatban található mosogatókat támogatja.

| Cél | Description |
|:---|:---|
| Event Hubs | Az Azure Event Hubs használatával küldhet az Azure-on kívüli adatküldést. |
| Azure Storage-Blobok | Adatok írása a Blobok számára az Azure Storage-ban a táblák mellett. |
| Azure Monitor-metrikák | Telepítse a saját Graf-ügynököt a LAD mellett. Lásd: [Egyéni metrikák gyűjtése Linux](collect-custom-metrics-linux-telegraf.md)RENDSZERű virtuális gépekhez a InfluxData-gyártói ügynökkel.


## <a name="installation-and-configuration"></a>Telepítés és konfigurálás
A diagnosztikai bővítmény a [virtuálisgép-bővítményként](../../virtual-machines/extensions/overview.md) van implementálva az Azure-ban, így ugyanazokat a telepítési lehetőségeket támogatja a Resource Manager-sablonok, a PowerShell és a parancssori felület használatával. A virtuálisgép-bővítmények telepítésével és karbantartásával kapcsolatos általános információkért lásd: [virtuálisgép-bővítmények és-szolgáltatások a Windowshoz](../../virtual-machines/extensions/features-windows.md) és a [virtuálisgép-bővítményekhez és-funkciókhoz](../../virtual-machines/extensions/features-linux.md) .

A Windows és a Linux diagnosztikai bővítményét is telepítheti és konfigurálhatja a virtuális gép menüjének **figyelés** szakaszának **diagnosztikai beállítások** területén található Azure Portal.

A Windows és a Linux rendszerhez készült diagnosztikai bővítmény telepítéséről és konfigurálásáról a következő cikkekben talál további információt.

- [A Windows Azure Diagnostics bővítmény (WAD) telepítése és konfigurálása](diagnostics-extension-windows-install.md)
- [Metrikák és naplók figyelése a Linux diagnosztikai bővítmény használatával](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Egyéb dokumentáció

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure Cloud Service (klasszikus) webes és feldolgozói szerepkörök
- [Bevezetés a Cloud Service Monitorozásba](../../cloud-services/cloud-services-how-to-monitor.md)
- [Azure Diagnostics engedélyezése az Azure-ban Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Azure Cloud Services-Application Insights](../app/cloudservices.md)<br>[Cloud Services alkalmazás folyamatának nyomon követése Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Szolgáltatások monitorozása és diagnosztizálása egy helyi gép fejlesztési beállításánál](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Következő lépések


* Ismerkedjen meg a [Azure Diagnostics teljesítményszámlálók használatával](../../cloud-services/diagnostics-performance-counters.md).
* Ha problémája van az Azure Storage-táblákban megjelenő vagy az adatait megtaláló diagnosztika szolgáltatással, olvassa el a [hibaelhárítási Azure Diagnostics](diagnostics-extension-troubleshooting.md)
