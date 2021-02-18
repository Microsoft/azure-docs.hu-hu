---
title: Azure Monitor for VMs-áttekintés engedélyezése
description: Megtudhatja, hogyan telepítheti és konfigurálhatja a Azure Monitor for VMs. Ismerje meg a rendszerkövetelményeket.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: d83ed63e5e86ac415a8d6145c2efe484ad335b75
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619855"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Azure Monitor for VMs-áttekintés engedélyezése

Ez a cikk áttekintést nyújt azokról a lehetőségekről, amelyek lehetővé teszik a Azure Monitor for VMs számára a következők állapotának és teljesítményének figyelését:

- Azure-beli virtuális gépek 
- Azure-beli virtuálisgép-méretezési csoportok
- Az Azure arc-hoz csatlakozó hibrid virtuális gépek
- Helyszíni virtuális gépek
- Egy másik felhőalapú környezetben üzemeltetett virtuális gépek.  

Azure Monitor for VMs beállítása:

* Egyetlen Azure-beli virtuális gép, Azure virtuálisgép-méretezési csoport vagy Azure arc-gép engedélyezéséhez **válassza ki** az adatforrásokat közvetlenül a Azure Portal menüjéből.
* Azure Policy használatával több Azure-beli virtuális gépet, Azure-beli virtuális gépet vagy Azure arc-gépet is engedélyezhet. Ez a módszer biztosítja, hogy a meglévő és az új virtuális gépeken és a méretezési csoportokon a szükséges függőségek telepítve és megfelelően legyenek konfigurálva. A rendszer nem megfelelő virtuális gépeket és méretezési csoportokat jelentett be, így eldöntheti, hogy engedélyezi-e őket, és hogyan javíthatja őket.
* A PowerShell használatával több Azure-beli virtuális gép, Azure-beli virtuális gép, Azure virtuálisgép-méretezési csoport vagy Azure arc-gép is engedélyezhető egy adott előfizetéshez vagy erőforráscsoporthoz.
* Engedélyezze Azure Monitor for VMs a vállalati hálózaton vagy más felhőalapú környezetben üzemeltetett virtuális gépek vagy fizikai számítógépek figyelését.

## <a name="supported-machines"></a>Támogatott gépek
A Azure Monitor for VMs a következő gépeket támogatja:

- Azure virtuális gép
- Azure virtuálisgép-méretezési csoport
- Az Azure arc-hoz csatlakoztatott hibrid virtuális gép


## <a name="supported-azure-arc-machines"></a>Támogatott Azure arc-gépek
Azure Monitor for VMs elérhető az Azure arc-kompatibilis kiszolgálókhoz olyan régiókban, amelyeken elérhető az ív-bővítmény szolgáltatás. Az ív-ügynök 0,9-es vagy újabb verzióját kell futtatnia.

| Csatlakoztatott forrás | Támogatott | Description |
|:--|:--|:--|
| Windows-ügynökök | Yes | A Windows [log Analytics ügynökével](../agents/log-analytics-agent.md)együtt a Windows-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](../agents/agents-overview.md#supported-operating-systems). |
| Linux-ügynökök | Yes | A [Linux rendszerhez készült log Analytics-ügynökkel](../agents/log-analytics-agent.md)együtt a Linux-ügynököknek szüksége van a függőségi ügynökre. További információ: [támogatott operációs rendszerek](#supported-operating-systems). |
| System Center Operations Manage felügyeleti csoport | No | |

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Azure Monitor for VMs támogatja az Log Analytics ügynököt és a függőségi ügynököt támogató operációs rendszereket. Lásd: [Azure monitor-ügynökök áttekintése ](../agents/agents-overview.md#supported-operating-systems) teljes listához.

> [!IMPORTANT]
> A Azure Monitor for VMs Guest Health funkció korlátozott operációsrendszer-támogatással rendelkezik, miközben nyilvános előzetes verzióban érhető el. Részletes listát a következő témakörben talál: [Azure monitor for VMS vendég állapotának engedélyezése (előzetes verzió)](../vm/vminsights-health-enable.md) .

Tekintse meg a következő, a Azure Monitor for VMst támogató függőségi ügynök Linux-támogatásával kapcsolatos szempontokat:

- Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
- A nem szabványos kernel-kiadások (például a fizikai címkiterjesztés (PAE) és a Xen) nem támogatottak a Linux-disztribúciók esetében. Például a *2.6.16.21-0,8-Xen* kiadási karakterlánccal rendelkező rendszer nem támogatott.
- Az egyéni kernelek, beleértve a standard kernelek újrafordítását, nem támogatottak.
- Az 9,4-es verziótól eltérő Debian-disztribúciók esetén a Map funkció nem támogatott, és a teljesítmény funkció csak a Azure Monitor menüből érhető el. Nem érhető el közvetlenül az Azure-beli virtuális gép bal oldali paneljéről.
- A CentOSPlus kernel támogatott.
- A Linux-kernelt a fantom biztonsági rések számára kell javítani. További részletekért tekintse meg a Linux-disztribúció gyártóját.
## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
A Azure Monitor for VMs Log Analytics munkaterületet igényel. A munkaterület részleteiért és követelményeiért lásd: [log Analytics munkaterület konfigurálása Azure monitor for VMshoz](vminsights-configure-workspace.md) .
## <a name="agents"></a>Ügynökök
Azure Monitor for VMs a következő két ügynököt kell telepíteni az egyes virtuális gépekre vagy virtuálisgép-méretezési csoportokra. Az erőforrás előkészítéséhez telepítse ezeket az ügynököket, és kapcsolódjon a munkaterülethez.  Tekintse meg az ügynökök hálózati követelményeinek [hálózati követelményeit](../agents/log-analytics-agent.md#network-requirements) .

- [Log Analytics ügynök](../agents/log-analytics-agent.md). Eseményeket és teljesítményadatokat gyűjt a virtuális gépről vagy virtuálisgép-méretezési csoportból, és továbbítja azt a Log Analytics munkaterületre. Az Azure-erőforrások Log Analytics ügynökének üzembe helyezési módszerei a Windows és [Linux](../../virtual-machines/extensions/oms-linux.md) [rendszerhez](../../virtual-machines/extensions/oms-windows.md) készült virtuálisgép-bővítményt használják.
- Függőségi ügynök. Gyűjti a felderített adatokat a virtuális gépen és a külső folyamatok függőségein futó folyamatokról, amelyeket az [Azure monitor for VMS Térkép funkciója](../vm/vminsights-maps.md)használ. A függőségi ügynök a Log Analytics ügynökre támaszkodik, hogy az adatAzure Monitorba kézbesítse az adatforrást. Az Azure-erőforrások függőségi ügynökének üzembe helyezési módszerei a Windows és [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) [rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md) készült virtuálisgép-bővítményt használják.

> [!NOTE]
> A Log Analytics ügynök ugyanaz az ügynök, amelyet a System Center Operations Manager használ. A Azure Monitor for VMs felügyelheti a Operations Manager által is figyelt ügynököket, ha azokat közvetlenül csatlakoztatják, és telepítik a függőségi ügynököt. A [felügyeleti csoport kapcsolatain](../tform/../agents/om-agents.md) keresztül Azure monitorhoz csatlakozó ügynököket Azure monitor for VMS nem lehet figyelni.

Az alábbi módszerek több módszert is biztosítanak ezeknek az ügynököknek a üzembe helyezésére. 

| Metódus | Leírás |
|:---|:---|
| [Azure Portal](../vm/vminsights-enable-portal.md) | Telepítsen mindkét ügynököt egyetlen virtuális gépre, virtuálisgép-méretezési csoportra vagy az Azure arc-hoz csatlakoztatott hibrid virtuális gépekre. |
| [Resource Manager-sablonok](../vm/vminsights-enable-resource-manager.md) | Telepítse mindkét ügynököt a támogatott módszerek bármelyikével egy Resource Manager-sablon üzembe helyezéséhez, beleértve a CLI-t és a PowerShellt is. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | Azure Policy-kezdeményezés hozzárendelésével automatikusan telepítheti az ügynököket virtuális gép vagy virtuálisgép-méretezési csoport létrehozásakor. |
| [Manuális telepítés](../vm/vminsights-enable-hybrid.md) | Telepítse az ügynököket a vendég operációs rendszerbe az Azure-on kívül üzemeltetett számítógépeken, például az adatközpontban vagy más felhőalapú környezetekben. |


## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

- Lásd a Log Analytics ügynök hálózati követelményeinek [hálózati](../platform/log-analytics-agent.md#network-requirements) követelményeit.
- A függőségi ügynöknek kapcsolódnia kell a virtuális gépről a 169.254.169.254. Ez az Azure metadata szolgáltatás végpontja. Győződjön meg arról, hogy a tűzfalbeállítások engedélyezik a csatlakozást ehhez a végponthoz.


## <a name="management-packs"></a>Felügyeleti csomagok
Ha egy Log Analytics munkaterületen Azure Monitor for VMs van konfigurálva, a rendszer két felügyeleti csomagot továbbít az adott munkaterülethez csatlakozó összes Windows-számítógépre. A felügyeleti csomagok neve *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* és *Microsoft. IntelligencePacks. VMInsights* , és a *%ProgramFiles%\Microsoft monitoring Agent\Agent\Health szolgáltatás State\Management-csomagjaira* íródott. 

A *ApplicationDependencyMonitor* felügyeleti csomag által használt adatforrás **% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. A *VMInsights* felügyeleti csomag által használt adatforrás a *(z)% program files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnosztikai és használati adatok

A Microsoft a Azure Monitor szolgáltatás használatával automatikusan gyűjti a használati és teljesítményadatokat. A Microsoft ezeket az adatmennyiségeket használja a szolgáltatás minőségének, biztonságának és integritásának javítására. 

A pontos és hatékony hibaelhárítási funkciók biztosításához a Map szolgáltatás a szoftver konfigurációjával kapcsolatos információkat tartalmaz. Az adatok olyan információkat biztosítanak, mint például az operációs rendszer és a verziószám, az IP-cím, a DNS-név és a munkaállomás neve. A Microsoft nem gyűjt neveket, címeket és más kapcsolattartási adatokat.

További információ az adatok gyűjtéséről és használatáról: a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Következő lépések

A Teljesítményfigyelő funkció használatának megismeréséhez tekintse meg a [Azure monitor for VMS teljesítményének megtekintése](../vm/vminsights-performance.md)című témakört. A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép megtekintése](../vm/vminsights-maps.md).
