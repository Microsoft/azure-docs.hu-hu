---
title: A Windows Update agenttel kapcsolatos problémák elhárítása Azure Automation
description: Ez a cikk azt ismerteti, hogyan lehet a Windows Update agenttel kapcsolatos hibákat elhárítani és megoldani a Update Management során.
services: automation
ms.date: 01/25/2020
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 9516210021ce48f069ae3b3b4e02503527e0db24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100580891"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>A Windows Update Agent hibáinak elhárítása

Számos oka lehet annak, hogy a gép a Update Management üzembe helyezése során nem jelenik meg készként (kifogástalan). A probléma okának megállapításához a Windows Hybrid Runbook Worker Agent állapotát is megtekintheti. A következő három készültségi állapotú gép:

* Készen áll: a hibrid Runbook-feldolgozó üzembe lett helyezve, és legalább egy órával ezelőtt volt látható.
* Leválasztva: a hibrid Runbook Worker üzembe helyezése egy órával ezelőtt történt, és utoljára volt látható.
* Nincs konfigurálva: a hibrid Runbook Worker nem található, vagy nem fejeződött be a telepítés.

> [!NOTE]
> A Azure Portal megjelenítése és a gép aktuális állapota között enyhe késés adható meg.

Ez a cikk azt ismerteti, hogyan futtathatja az Azure-gépek hibakeresőjét a Azure Portal és nem Azure-beli gépekről az [Offline forgatókönyvben](#troubleshoot-offline).

> [!NOTE]
> A hibakereső parancsfájl mostantól tartalmazza a Windows Server Update Services (WSUS) és az automatikus letöltés és a telepítési kulcsok ellenőrzését.

## <a name="start-the-troubleshooter"></a>A hibakereső elindítása

Az Azure-gépek esetében az ügynök frissítése lapon a portálon a **frissítés ügynök felkészültsége** oszlopban található **hibakeresés** hivatkozásra kattintva indíthatja el a frissítési ügynök hibakeresése lapot. A nem Azure-beli gépek esetében a hivatkozás a jelen cikkre mutat. A nem Azure-beli gépekkel kapcsolatos hibák megoldásához tekintse meg a [kapcsolat nélküli hibakeresést](#troubleshoot-offline) .

![A virtuális gépek Update Management listájának képernyőképe](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> A hibrid Runbook-feldolgozók állapotának vizsgálatához a virtuális gépnek futnia kell. Ha a virtuális gép nem fut, megjelenik a **virtuális gép indítása** gomb.

Az ügynök frissítése lapon válassza az **ellenőrzések futtatása** lehetőséget a hibakereső elindításához. A hibakereső a [Futtatás parancs](../../virtual-machines/windows/run-command.md) használatával futtat egy parancsfájlt a gépen a függőségek ellenőrzéséhez. Ha a hibakereső elkészült, a visszaadja az ellenőrzések eredményét.

![Képernyőkép az ügynök frissítése lap hibakereséséről](../media/update-agent-issues/troubleshoot-page.png)

Az eredmények a lapon jelennek meg, amikor készen állnak. Az ellenőrzések szakaszban látható, hogy mi szerepel az egyes ellenőrzésekben.

![Képernyőkép a frissítési ügynök ellenőrzésével kapcsolatos hibákról](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Előfeltétel-ellenőrzések

### <a name="operating-system"></a>Operációs rendszer

Az operációs rendszer ellenőrzése ellenőrzi, hogy a hibrid Runbook-feldolgozó a következő táblázatban látható operációs rendszerek egyikét futtatja-e.

|Operációs rendszer  |Jegyzetek  |
|---------|---------|
|Windows Server 2012 és újabb verziók |A .NET-keretrendszer 4,6-es vagy újabb verziójára van szükség. ([A .NET-keretrendszer letöltése](/dotnet/framework/install/guide-for-developers).)<br/> A Windows PowerShell 5,1 megadása kötelező.  (A[Windows Management Framework 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616).)        |

### <a name="net-462"></a>.NET 4.6.2

A .NET-keretrendszer ellenőrzése ellenőrzi, hogy a rendszeren telepítve van-e a [.NET-keretrendszer 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) vagy újabb verziója.

### <a name="wmf-51"></a>WMF 5.1

A WMF-ellenőrzés ellenőrzi, hogy a rendszer rendelkezik-e a Windows Management Framework (WMF) szükséges verziójával, amely a [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Ez az érték határozza meg, hogy a TLS 1,2-et használja-e a kommunikáció titkosításához. A platform már nem támogatja a TLS 1,0-et. A TLS 1,2 használatával kommunikálhat Update Managementokkal.

## <a name="connectivity-checks"></a>Kapcsolatok ellenőrzése

### <a name="registration-endpoint"></a>Regisztrációs végpont

Ez az érték határozza meg, hogy az ügynök megfelelően tud-e kommunikálni az ügynök szolgáltatással.

A proxy és a tűzfal konfigurációjának lehetővé kell tennie, hogy a hibrid Runbook Worker ügynök kommunikáljon a regisztrációs végponttal. A megnyitni kívánt címek és portok listáját itt tekintheti meg: [Network Planning](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Műveleti végpont

Ez az érték határozza meg, hogy az ügynök megfelelően tud-e kommunikálni a feladatütemezés adatszolgáltatásával.

A proxy és a tűzfal konfigurációjának lehetővé kell tennie, hogy a hibrid Runbook-feldolgozó ügynök kommunikáljon a feladatütemezés adatszolgáltatásával. A megnyitni kívánt címek és portok listáját itt tekintheti meg: [Network Planning](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Virtuálisgép-szolgáltatás állapotának ellenőrzése

### <a name="monitoring-agent-service-status"></a>Figyelési ügynök szolgáltatásának állapota

Ez az érték határozza meg, hogy a Windows-ügynök ( `healthservice` ) fut-e a gépen a log Analytics. Ha többet szeretne megtudni a szolgáltatás hibaelhárításáról, tekintse meg [a log Analytics ügynök a Windows rendszerhez nem fut](hybrid-runbook-worker.md#mma-not-running).

A Windows Log Analytics ügynökének újratelepítéséhez tekintse meg [a Windows rendszerhez készült ügynök telepítése](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows)című témakört.

### <a name="monitoring-agent-service-events"></a>Figyelési ügynök szolgáltatási eseményei

Ez az érték határozza meg, hogy az Azure Operations Manager a gépen az elmúlt 24 órában megjelenjen-e az összes 4502 esemény.

Ha többet szeretne megtudni erről az eseményről, tekintse meg az esemény [4502-es eseményét a Operations Manager naplóban](hybrid-runbook-worker.md#event-4502) .

## <a name="access-permissions-checks"></a>Hozzáférési engedélyek ellenőrzése

> [!NOTE]
> A hibakereső jelenleg nem irányítja át a forgalmat egy proxykiszolgálón keresztül, ha van ilyen konfigurálva.

### <a name="crypto-folder-access"></a>Titkosítási mappa elérése

A kriptográfiai mappa hozzáférés-ellenőrzését határozza meg, hogy a helyi rendszerfiók hozzáfér-e a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Offline hibák

A hibakeresést a hibrid Runbook-feldolgozón offline módon, a parancsfájl helyi futtatásával használhatja. Szerezze be a következő szkriptet a GitHubról: [UM_Windows_Troubleshooter_Offline.ps1](https://github.com/Azure/updatemanagement/blob/main/UM_Windows_Troubleshooter_Offline.ps1). A parancsfájl futtatásához a WMF 4,0-es vagy újabb verziójára van szükség. A PowerShell legújabb verziójának letöltéséhez lásd: a [PowerShell különböző verzióinak telepítése](/powershell/scripting/install/installing-powershell).

A szkript kimenete a következő példához hasonlóan néz ki:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Következő lépések

A [hibrid Runbook-feldolgozói problémák elhárítása](hybrid-runbook-worker.md).
