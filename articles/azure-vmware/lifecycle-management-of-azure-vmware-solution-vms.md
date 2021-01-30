---
title: Azure VMware-megoldás virtuális gépek életciklus-kezelése
description: Ismerje meg, hogyan kezelheti az Azure VMware megoldás virtuális gépei életciklusának minden aspektusát Microsoft Azure natív eszközökkel.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 5bba9cbbc8783cc285ff09f790ee1b35b20ba358
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062896"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware-megoldás virtuális gépek életciklus-kezelése

Microsoft Azure natív eszközök lehetővé teszik a virtuális gépek (VM-EK) figyelését és kezelését az Azure-környezetben. Ugyanakkor lehetővé teszik a virtuális gépek figyelését és kezelését az Azure VMware megoldásban és a helyszíni virtuális gépeken. Ebben az áttekintésben áttekintjük az Azure-beli integrált monitorozási architektúrát, valamint azt, hogy a natív eszközeivel miként kezelhetők az Azure VMware-megoldás virtuális gépei a teljes életciklusuk során.

## <a name="benefits"></a>Előnyök

- Az Azure Native Services használatával a virtuális gépeket hibrid környezetben (Azure, Azure VMware megoldás és helyszíni) kezelheti.
- Az Azure, az Azure VMware megoldás és a helyszíni virtuális gépek integrált monitorozása és láthatósága.
- Az Azure Update Management a Azure Automationban a Windows és Linux rendszerű gépekhez is kezelheti az operációs rendszer frissítéseit. 
- A Azure Security Center komplex veszélyforrások elleni védelmet biztosít, többek között:
    - Fájlintegritás monitorozása
    - Nem biztonságos biztonsági riasztások
    - Operációs rendszer javításának felmérése
    - Biztonsági konfigurációs beállítások értékelése
    - Endpoint Protection-Értékelés 
- Egyszerűen üzembe helyezheti a Log Analytics ügynököt az Azure arc-kompatibilis kiszolgálók virtuálisgép-bővítményének támogatásával az új és a meglévő virtuális gépekhez. 
- A Azure Monitor Log Analytics munkaterülete lehetővé teszi a naplók gyűjtését és a teljesítményszámláló gyűjtését a Log Analytics ügynök vagy bővítmények használatával. Adatok és naplók összegyűjtése egyetlen pontra, és ezek az adatok különböző Azure-beli natív szolgáltatásokban jelennek meg. 
- A Azure Monitor előnyei a következők: 
    - Zökkenőmentes monitorozás 
    - Jobb infrastruktúra láthatósága 
    - Azonnali értesítések 
    - Automatikus feloldás 
    - Költséghatékonyság 

## <a name="integrated-azure-monitoring-architecture"></a>Integrált Azure monitorozási architektúra

Az alábbi ábrán az Azure VMware-megoldás virtuális gépei integrált figyelési architektúrája látható.

![Integrált Azure monitorozási architektúra](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>Előkészületek

Ha még nem ismeri az Azure-t, vagy nem ismeri a korábban említett szolgáltatásokat, tekintse át a következő cikkeket:

- [Az Automation-fiókok hitelesítésének áttekintése](../automation/automation-security-overview.md)
- [A Azure monitor naplók üzembe helyezésének](../azure-monitor/platform/design-logs-deployment.md) és [Azure Monitorének](../azure-monitor/overview.md) megtervezése
- [Tervezési](../security-center/security-center-planning-and-operations-guide.md) és [támogatott platformok](../security-center/security-center-os-coverage.md) Azure Security Center
- [Azure Monitor for VMs-áttekintés engedélyezése](../azure-monitor/insights/vminsights-enable-overview.md)
- [Mi az Azure arc-kompatibilis kiszolgálók?](../azure-arc/servers/overview.md) és [Mi az az Azure arc-kompatibilis Kubernetes?](../azure-arc/kubernetes/overview.md)
- [A frissítéskezelés áttekintése](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Az Azure Native Services integrálása és üzembe helyezése

### <a name="enable-azure-update-management"></a>Azure-Update Management engedélyezése

A Azure Automation Azure Update Management egy hibrid környezetben kezeli a Windows-és Linux-alapú gépek operációs rendszerének frissítéseit. Figyeli a megfelelőség javítását, és továbbítja a javítási szórási riasztásokat a szervizelés Azure Monitor. Az Azure Update Managementnak csatlakoznia kell a Log Analytics munkaterülethez a tárolt adatmennyiségek használatával a virtuális gépek frissítéseinek állapotának felmérése érdekében.

1.  Log Analytics Azure Update Managementhoz való hozzáadásához először [létre kell hoznia egy Azure Automation-fiókot](../automation/automation-create-standalone-account.md). Ha sablon használatával szeretné létrehozni a fiókját, tekintse meg [az Automation-fiók létrehozása Azure Resource Manager sablonnal](../automation/quickstart-create-automation-account-template.md)című témakört.

2. **Log Analytics munkaterület** lehetővé teszi a naplók gyűjtését és a teljesítményszámláló gyűjtését az log Analytics ügynök vagy bővítmények használatával. Log Analytics munkaterület létrehozásához tekintse meg a [log Analytics munkaterület létrehozása a Azure Portalben](../azure-monitor/learn/quick-create-workspace.md)című témakört. Ha szeretné, létrehozhat egy munkaterületet a [parancssori](../azure-monitor/learn/quick-create-workspace-cli.md)felület, a [PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md)vagy a [Azure Resource Manager sablon](../azure-monitor/samples/resource-manager-workspace.md)használatával is.

3. Az Azure-Update Management a virtuális gépek számára történő engedélyezéséről az [Automation-fiókból történő Update Management engedélyezése](../automation/update-management/enable-from-automation-account.md)című témakörben olvashat. A folyamat során összekapcsolja Log Analytics munkaterületét az Automation-fiókkal. 
 
4. Miután hozzáadta a virtuális gépeket az Azure Update Managementhoz, [telepíthet frissítéseket a virtuális gépeken, és áttekintheti az eredményeket](../automation/update-management/deploy-updates.md). 

### <a name="enable-azure-security-center"></a>Azure Security Center engedélyezése

A Azure Security Center komplex veszélyforrások elleni védelmet biztosít a felhőben és a helyszínen futó hibrid számítási feladatokhoz. Felméri az Azure VMware-alapú virtuális gépek biztonsági réseit, és szükség esetén riasztásokat hoz létre. Ezek a biztonsági riasztások továbbítható a Azure Monitor a megoldáshoz.

Azure Security Center nem igényel telepítést. További információkért tekintse meg a [virtuális gépek támogatott szolgáltatásainak](../security-center/security-center-services.md)listáját.

1. Ha Azure VMware-alapú virtuális gépeket és nem Azure-beli virtuális gépeket szeretne felvenni Security Centerba, tekintse meg a [gyors útmutató: Azure Security Center beállítása](../security-center/security-center-get-started.md) 

2. Miután hozzáadta az Azure VMware-megoldást a virtuális gépekhez vagy virtuális gépekhez egy nem Azure-környezetből, engedélyezze Security Center az Azure Defendert. A Security Center felméri a virtuális gépeket a potenciális biztonsági problémákra. Emellett javaslatokat is biztosít az Áttekintés lapon. További információ: [biztonsági javaslatok a Azure Security Center](../security-center/security-center-recommendations.md).

3. A Azure Security Centerban biztonsági házirendeket is meghatározhat. A biztonsági szabályzatok konfigurálásával kapcsolatos információkért lásd: [a biztonsági házirendek használata](../security-center/tutorial-security-policy.md).

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Virtuális gépek beléptetése az Azure arc-kompatibilis kiszolgálókra

Az Azure arc kiterjeszti az Azure-felügyeletet bármely infrastruktúrára, beleértve az Azure VMware-megoldást, a helyszíni vagy más felhőalapú platformokat.

- Az Azure arc-kompatibilis kiszolgálók több Windows-vagy Linux-alapú virtuális gépen való engedélyezésével kapcsolatban lásd: [hibrid gépek összekapcsolása az Azure](../azure-arc/servers/onboard-service-principal.md)-ban, nagy léptékben.

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Hibrid Kubernetes-fürtök előkészítése ív-kompatibilis Kubernetes

Az Azure-beli VMware-megoldási környezetben üzemeltetett Kubernetes-fürtöket az Azure arc-kompatibilis Kubernetes használatával lehet csatlakoztatni. 

- További információ: [Azure arc-kompatibilis bevezetési egyszerű szolgáltatás létrehozása](../azure-arc/kubernetes/create-onboarding-service-principal.md).

### <a name="deploy-the-log-analytics-agent"></a>A Log Analytics-ügynök üzembe helyezése

Az Azure VMware-megoldás virtuális gépei a Log Analytics ügynökön (más néven Microsoft monitoring Agent (MMA) vagy OMS Linux Agent) keresztül figyelhetők meg. Már létrehozott egy Log Analytics munkaterületet Azure Automation Update Management engedélyezése közben.

- Telepítse a Log Analytics-ügynököt az [Azure arc-kompatibilis kiszolgálók virtuálisgép-bővítményének támogatásával](../azure-arc/servers/manage-vm-extensions.md).

### <a name="enable-azure-monitor"></a>Azure Monitor engedélyezése

A Azure Monitor egy átfogó megoldás a Felhőbeli és a helyszíni környezetek telemetria gyűjtésére, elemzésére és működésének elvégzésére. Nem igényel telepítést. A Azure Monitor segítségével figyelheti a vendég operációs rendszer teljesítményét, és felderítheti és leképezheti az alkalmazások függőségeit az Azure VMware-megoldás vagy a helyszíni virtuális gépek számára.

- Azure Monitor lehetővé teszi a különböző forrásokból származó adatok figyelését és elemzését. További információ: [Azure monitor figyelési adatainak forrásai](../azure-monitor/platform/data-sources.md).

- Különböző típusú adatokat gyűjthet az elemzéshez, a vizualizációhoz és a riasztásokhoz. További információ: [Azure monitor adatplatform](../azure-monitor/platform/data-platform.md).

- Ha Azure Monitort szeretne konfigurálni a Log Analytics munkaterülettel, tekintse meg a [log Analytics munkaterület konfigurálása Azure monitor for VMshoz](../azure-monitor/insights/vminsights-configure-workspace.md)című témakört.

- Riasztási szabályokat hozhat létre a környezetében felmerülő problémák azonosításához, például az erőforrások magas kihasználásához, a hiányzó javításokhoz, a kevés lemezterülethez és a virtuális gépek szívveréséhez. Az észlelt eseményekhez automatikus választ is beállíthat, ha riasztást küld az IT Service Management-(ITSM-) eszközökre. A riasztások észleléséről értesítő értesítés is elküldhető e-mailben. Ilyen szabályok létrehozásához tekintse meg a következőt:
    - [Metrikai riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/platform/alerts-metric.md).
    - [Naplózási riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/platform/alerts-log.md).
    - Az automatizált műveletek és értesítések beállítására szolgáló [műveleti szabályok](../azure-monitor/platform/alerts-action-rules.md) .
    - [Az Azure összekapcsolásával ITSM eszközöket it-szolgáltatásmenedzsmenti csatoló használatával](../azure-monitor/platform/itsmc-overview.md).