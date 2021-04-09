---
title: Log Analytics-ügynök áttekintése
description: Ebből a témakörből megtudhatja, hogyan gyűjthet adatokat és figyelheti az Azure-ban, a helyszínen vagy más felhőalapú környezetben üzemeltetett számítógépeket Log Analytics használatával.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: 8b3b1d39766d556b820f21c1775210611b995d92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199908"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics-ügynök áttekintése

Az Azure Log Analytics-ügynök a Windows-és Linux-alapú virtuális gépekről gyűjt telemetria a Felhőbeli, a helyszíni gépeken, valamint a [System Center Operations Manager](/system-center/scom/) által figyelt, és az összegyűjtött adatokat a Azure monitor log Analytics munkaterületére küldi el. A Log Analytics ügynök az Azure Monitor, például a [virtuális](../vm/vminsights-enable-overview.md)gépekkel, [Azure Security Centerekkel](../../security-center/index.yml)és [Azure Automationekkel](../../automation/automation-intro.md)kapcsolatos bepillantást és egyéb szolgáltatásokat is támogatja. Ez a cikk részletes áttekintést nyújt az ügynökről, a rendszerről és a hálózati követelményekről, valamint az üzembe helyezési módszerekről.

> [!NOTE]
> Előfordulhat, hogy a Microsoft monitoring Agent (MMA) néven is látható Log Analytics ügynök.

## <a name="comparison-to-azure-diagnostics-extension"></a>Összehasonlítás az Azure Diagnostics bővítménnyel
Az [Azure Diagnostics bővítmény](./diagnostics-extension-overview.md) a Azure monitorban is használható a figyelési adatok gyűjtésére az Azure-beli virtuális gépek vendég operációs rendszeréről. Dönthet úgy is, hogy a követelményektől függően vagy mindkettőt használja. A Azure Monitor-ügynökök részletes összehasonlítását lásd [a Azure monitor ügynökök áttekintésében](../agents/agents-overview.md) . 

A figyelembe venni kívánt fő különbségek a következők:

- Azure Diagnostics bővítmény csak az Azure Virtual Machines használatával használható. Az Log Analytics-ügynök használható az Azure-ban, a többi felhőkben és a helyszínen lévő virtuális gépekkel.
- Azure Diagnostics a bővítmény adatokat küld az Azure Storage-ba, [Azure monitor metrikákat](../essentials/data-platform-metrics.md) (csak Windows) és Event Hubs. A Log Analytics ügynök adatokat küld [Azure monitor naplókba](../logs/data-platform-logs.md).
- A Log Analytics ügynök szükséges a [megoldások](../monitor-reference.md#insights-and-core-solutions), a [virtuális](../vm/vminsights-overview.md)gépek és egyéb szolgáltatások, például a [Azure Security Center](../../security-center/index.yml)számára.

## <a name="costs"></a>Költségek

Log Analytics ügynöknek nincs díja, de a betöltött adatokért díjat számítunk fel. A Log Analytics munkaterületen összegyűjtött adatok díjszabásával kapcsolatos részletes információkért lásd: a [használat és a költségek kezelése Azure monitor naplókkal](../logs/manage-cost-storage.md) .

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

 A Log Analytics ügynök által támogatott Windows és Linux operációsrendszer-verziók listáját a [támogatott operációs rendszerek](../agents/agents-overview.md#supported-operating-systems) című részben tekintheti meg. 

## <a name="data-collected"></a>Összegyűjtött adatok

A következő táblázat felsorolja azokat az adattípusokat, amelyekkel a Log Analytics munkaterületek összegyűjthetők az összes csatlakoztatott ügynökből. Tekintse meg a [Azure monitor által figyelt adatokat?](../monitor-reference.md) az elemzések, megoldások és egyéb olyan megoldások listáját, amelyek a log Analytics-ügynököt használják más típusú adatok gyűjtésére.

| Adatforrás | Leírás |
| --- | --- |
| [Windows-eseménynaplók](../agents/data-sources-windows-events.md) | A Windows eseménynaplózási rendszernek eljuttatott információk. |
| [Syslog](../agents/data-sources-syslog.md)                     | A Linux-eseménynaplózási rendszernek eljuttatott információk. |
| [Teljesítmény](../agents/data-sources-performance-counters.md)  | Az operációs rendszer és a számítási feladatok különböző szempontjainak teljesítményét mérő numerikus értékek. |
| [IIS-naplók](../agents/data-sources-iis-logs.md)                 | A vendég operációs rendszeren futó IIS-webhelyek használati adatai. |
| [Egyéni naplók](../agents/data-sources-custom-logs.md)           | A Windows és Linux rendszerű számítógépeken található szövegfájlok eseményei. |

## <a name="data-destinations"></a>Adatelérési helyek

A Log Analytics ügynök adatokat küld egy Log Analytics munkaterületre Azure Monitor. A Windows-ügynök többkiszolgálós lehet, hogy több munkaterületre és System Center Operations Manager felügyeleti csoportokra küldje az adatküldést. A Linux-ügynök csak egyetlen célhelyre tud küldeni, vagy egy munkaterületre vagy egy felügyeleti csoportra.

## <a name="other-services"></a>Egyéb szolgáltatások

A Linux és a Windows rendszerhez készült ügynök nem csak Azure Monitorhoz való csatlakozáshoz használható. Más szolgáltatások, például a Azure Security Center és az Azure Sentinel az ügynökre és a csatlakoztatott Log Analytics munkaterületére támaszkodnak. Az ügynök a hibrid Runbook-feldolgozói szerepkör és más szolgáltatások (például [change Tracking](../../automation/change-tracking/overview.md), [Update Management](../../automation/update-management/overview.md)és [Azure Security Center](../../security-center/security-center-introduction.md)) üzemeltetéséhez is támogatja a Azure Automation. További információ a hibrid Runbook feldolgozói szerepkörről: [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Munkaterület és felügyeleti csoport korlátozásai

Az ügynökök Operations Manager felügyeleti csoporthoz való csatlakoztatásával kapcsolatos részletekért lásd: az [ügynök konfigurálása Operations Manager felügyeleti csoportnak való jelentésre](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) .

* A Windows-ügynökök akár négy munkaterülethez is csatlakozhatnak, még akkor is, ha azok egy System Center Operations Manager felügyeleti csoporthoz csatlakoznak.
* A Linux-ügynök nem támogatja a többsoros vezérlést, és csak egyetlen munkaterülethez vagy felügyeleti csoportjához tud csatlakozni.

## <a name="security-limitations"></a>Biztonsági korlátozások

* A Windows-és Linux-ügynökök támogatják a [FIPS 140 szabványt](/windows/security/threat-protection/fips-140-validation), [más típusú megerősítés azonban nem támogatott](../agents/agent-linux.md#supported-linux-hardening).

## <a name="installation-options"></a>Telepítési lehetőségek

Több módszerrel is telepítheti a Log Analytics-ügynököt, és a saját igényeinek megfelelően Azure Monitor csatlakozhat a számítógéphez. A következő részekben felsoroljuk a különböző típusú virtuális gépek lehetséges módszereit.

> [!NOTE]
> A Log Analytics ügynökkel rendelkező gépek klónozása nem támogatott. Ha az ügynök már társítva van egy munkaterülethez, ez nem fog működni az "arany lemezképek" esetében.

### <a name="azure-virtual-machine"></a>Azure virtuális gép

- A [VM](../vm/vminsights-enable-overview.md) -megállapítások több módszert biztosítanak az ügynökök méretekben való engedélyezéséhez. Ide tartozik a Log Analytics ügynök és a függőségi ügynök telepítése. 
- [A Azure Security Center kiépítheti a log Analytics ügynököt](../../security-center/security-center-enable-data-collection.md) az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra, ha engedélyezi a biztonsági rések és fenyegetések figyelését.
- Log Analytics Windows vagy [Linux](../../virtual-machines/extensions/oms-linux.md) [rendszerhez](../../virtual-machines/extensions/oms-windows.md) készült virtuálisgép-bővítmény telepíthető a Azure Portal, az Azure CLI, a Azure PowerShell vagy egy Azure Resource Manager sablonnal.
- Manuálisan telepítse az egyes Azure-beli virtuális gépeket [a Azure Portalból](../vm/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Windows rendszerű virtuális gép a helyszínen vagy egy másik felhőben

- Az [Azure arc-kompatibilis kiszolgálók](../../azure-arc/servers/overview.md) használatával telepítheti és kezelheti a log Analytics virtuálisgép-bővítményt.
- [Manuálisan telepítse](../agents/agent-windows.md) az ügynököt a parancssorból.
- A telepítés automatizálása [Azure Automation DSC](../agents/agent-windows.md#install-agent-using-dsc-in-azure-automation)-vel.
- [Resource Manager-sablon használata Azure stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Linux rendszerű virtuális gép a helyszínen vagy egy másik felhőben

- Az [Azure arc-kompatibilis kiszolgálók](../../azure-arc/servers/overview.md) használatával telepítheti és kezelheti a log Analytics virtuálisgép-bővítményt.
- [Manuálisan telepítse](../vm/quick-collect-linux-computer.md) az ügynököt, amely a githubon üzemeltetett burkoló parancsfájlt hívja meg.
- [System Center Operations Manager](./om-agents.md) integrálása Azure Monitorekkel a felügyeleti csoportnak jelentő Windows rendszerű számítógépek összegyűjtött adatainak továbbításához.

## <a name="workspace-id-and-key"></a>Munkaterület azonosítója és kulcsa

A használt telepítési módszertől függetlenül a Log Analytics munkaterület azon munkaterületének AZONOSÍTÓját és kulcsát kell megadni, amelyhez az ügynök csatlakozni fog. Válassza ki a munkaterületet a Azure Portal **log Analytics munkaterületek** menüjében. Ezután válassza az **ügynökök kezelése** lehetőséget a **Beállítások** szakaszban. 

[![Munkaterület részletei](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>TLS 1,2 protokoll

Annak érdekében, hogy az adatforgalom biztonsága Azure Monitor naplóba kerüljön, nyomatékosan javasoljuk, hogy az ügynököt legalább Transport Layer Security (TLS) 1,2 használatára konfigurálja. A TLS/SSL (SSL) régebbi verziói sebezhetőnek találták, és miközben jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak**.  További információkért tekintse át az [adatok biztonságos küldését a TLS 1,2 használatával](../logs/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A Linux és a Windows rendszerhez készült ügynök a 443-as TCP-porton keresztül kommunikál a Azure Monitor szolgáltatással. Ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztüli kommunikációhoz, tekintse át az alábbi követelményeket a szükséges hálózati konfiguráció megismeréséhez. Ha az IT-biztonsági szabályzatok nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást, beállíthat egy [log Analytics átjárót](gateway.md) , majd beállíthatja, hogy az ügynök az átjárón keresztül kapcsolódjon Azure monitor. Az ügynök ezután fogadhatja a konfigurációs adatokat, és elküldheti az összegyűjtött adatokat.

![Log Analytics-ügynök kommunikációs diagramja](./media/log-analytics-agent/log-analytics-agent-01.png)

A következő táblázat a Linux és a Windows-ügynökök Azure Monitor naplókkal való kommunikációhoz szükséges proxy-és tűzfal-konfigurációs információit sorolja fel.

### <a name="firewall-requirements"></a>A tűzfalra vonatkozó követelmények

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|
|*.ods.opinsights.azure.com |443-es port |Kimenő|Yes |  
|*.oms.opinsights.azure.com |443-es port |Kimenő|Yes |  
|*.blob.core.windows.net |443-es port |Kimenő|Yes |
|*.azure-automation.net |443-es port |Kimenő|Yes |

A Azure Governmentához szükséges tűzfal-információk: [Azure Government Management](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Ha azt tervezi, hogy a Azure Automation Hybrid Runbook Worker használatával csatlakozik az Automation szolgáltatáshoz, és regisztrálja magát a környezet runbookok vagy felügyeleti funkcióinak használatához, hozzá kell férnie a portszámhoz és a [hálózat konfigurálása a hibrid Runbook-feldolgozóhoz](../../automation/automation-hybrid-runbook-worker.md#network-planning)című témakörben leírt URL-címekhez.

### <a name="proxy-configuration"></a>Proxy konfigurálása

A Windows-és Linux-ügynök támogatja a proxykiszolgáló vagy a Log Analytics átjáró közötti kommunikációt a HTTPS protokoll használatával történő Azure Monitor.  A névtelen és az alapszintű hitelesítés (username/Password) is támogatott.  Ahhoz, hogy a Windows-ügynök közvetlenül a szolgáltatáshoz kapcsolódjon, a proxy konfigurációja a telepítés során vagy a Vezérlőpultról vagy a PowerShell-lel való [üzembe helyezés után](../agents/agent-manage.md#update-proxy-settings) van megadva.  

A Linux-ügynök esetében a proxykiszolgáló a telepítés során vagy a [telepítés után](../agents/agent-manage.md#update-proxy-settings) van megadva a proxy. conf konfigurációs fájl módosításával. A Linux-ügynök proxyjának konfigurációs értékének szintaxisa a következő:

`[protocol://][user:password@]proxyhost[:port]`

|Tulajdonság| Leírás |
|--------|-------------|
|Protokoll | https |
|felhasználó! | Opcionális Felhasználónév a proxy hitelesítéséhez |
|jelszó | Opcionális jelszó a proxy hitelesítéséhez |
|proxyhost | A proxykiszolgáló/Log Analytics átjáró címe vagy teljes tartományneve |
|port | A proxykiszolgáló/Log Analytics átjáró nem kötelező portszáma |

Például: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Ha a jelszóban speciális karaktereket (például " \@ ") használ, a rendszer proxy-csatlakozási hibát kap, mert az érték helytelenül van elemezve.  A probléma megkerüléséhez kódolja a jelszót az URL-címben egy eszköz, például a [URLDecode](https://www.urldecoder.org/)használatával.  

## <a name="next-steps"></a>Következő lépések

* Tekintse át az [adatforrásokat](../agents/agent-data-sources.md) , és Ismerje meg, hogy milyen adatforrások érhetők el az adatok Windows vagy Linux rendszerből való gyűjtéséhez. 
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../logs/log-query-overview.md) . 
* Ismerkedjen meg azokkal a [figyelési megoldásokkal](../insights/solutions.md) , amelyek a Azure monitor funkciókat bővítik, és adatokat gyűjtenek a log Analytics munkaterületen.
