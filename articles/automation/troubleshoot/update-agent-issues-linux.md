---
title: A Linux frissítési ügynökkel kapcsolatos problémák elhárítása Azure Automation
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a Linux Windows Update agenttel kapcsolatos problémákat a Update Management-ben.
services: automation
ms.date: 01/25/2021
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: da7c0ea670b4c4201930ce5d0f01e7bd9d9835e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581049"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>A Linux Update Agent hibáinak elhárítása

Számos oka lehet annak, hogy a gép miért nem jelenik meg készenléti (kifogástalan) állapotban a Update Managementban. A Linux Hybrid Runbook Worker Agent állapotát a probléma okának megállapításához tekintheti meg. A következő három készültségi állapotú gép:

* Készen áll: a hibrid Runbook-feldolgozó üzembe lett helyezve, és legalább egy órával ezelőtt volt látható.
* Leválasztva: a hibrid Runbook Worker üzembe helyezése egy órával ezelőtt történt, és utoljára volt látható.
* Nincs konfigurálva: a hibrid Runbook Worker nem található vagy nem fejeződött be a központi telepítés.

> [!NOTE]
> A Azure Portal megjelenítése és a gép aktuális állapota között enyhe késés adható meg.

Ez a cikk azt ismerteti, hogyan futtathatja az Azure-gépekhez tartozó hibakeresőt a Azure Portal és a nem Azure-beli gépekről az [Offline forgatókönyvben](#troubleshoot-offline).

> [!NOTE]
> A hibakereső parancsfájl jelenleg nem irányítja át a forgalmat egy proxykiszolgálón keresztül, ha van ilyen konfigurálva.

## <a name="start-the-troubleshooter"></a>A hibakereső elindítása

Azure-gépek esetén a portál **frissítési ügynök készültsége** oszlopában válassza a **hibakeresés** hivatkozást az ügynök frissítése lap megnyitásához. A nem Azure-beli gépek esetében a hivatkozás a jelen cikkre mutat. A nem Azure-beli gépekkel kapcsolatos hibákért tekintse meg az "offline hibakeresés" című szakaszban található utasításokat.

![Virtuálisgép-lista lapja](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Az ellenőrzésekhez szükség van a virtuális gép futtatására. Ha a virtuális gép nem fut, akkor **indítsa el a virtuális gépet** .

Az ügynök frissítése lapon válassza az **ellenőrzések futtatása** lehetőséget a hibakereső elindításához. A hibakereső a [Futtatás parancs](../../virtual-machines/linux/run-command.md) használatával futtat egy parancsfájlt a gépen a függőségek ellenőrzéséhez. Ha a hibakereső elkészült, a visszaadja az ellenőrzések eredményét.

![Hibakeresési oldal](../media/update-agent-issues-linux/troubleshoot-page.png)

Az ellenőrzések befejezésekor a rendszer az eredményeket az ablakban adja vissza. Az ellenőrzési szakaszokból megtudhatja, hogy mit keres az egyes ellenőrzések.

![Ügynök-ellenőrzési oldal frissítése](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Előfeltétel-ellenőrzések

### <a name="operating-system"></a>Operációs rendszer

Az operációs rendszer ellenőrzése ellenőrzi, hogy a hibrid Runbook-feldolgozó az alábbi operációs rendszerek egyikét futtatja-e.

|Operációs rendszer  |Jegyzetek  |
|---------|---------|
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz. A besoroláson alapuló javításhoz a "yum" utasítás szükséges a biztonsági adatmennyiség visszaadásához, amely a CentOS nem rendelkezik a dobozból.         |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 14,04 LTS, 16,04 LTS és 18,04 LTS (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

## <a name="monitoring-agent-service-health-checks"></a>Figyelési ügynök szolgáltatás állapotának ellenőrzése

### <a name="log-analytics-agent"></a>Log Analytics-ügynök

Ez az ellenőrzési funkció biztosítja, hogy a Linux rendszerhez készült Log Analytics-ügynök telepítve legyen. A telepítésével kapcsolatos útmutatásért lásd: [a Linux-ügynök telepítése](../../azure-monitor/vm/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Log Analytics ügynök állapota

Ez az ellenőrzési funkció biztosítja, hogy a Linux Log Analytics ügynöke fusson. Ha az ügynök nem fut, akkor a következő parancs futtatásával próbálja meg újraindítani. Az ügynök hibaelhárításával kapcsolatos további információkért lásd: [Linux – a hibrid Runbook-feldolgozói problémák elhárítása](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Többhelyű

Ez az érték határozza meg, hogy az ügynök több munkaterületnek jelent-e jelentést. A Update Management nem támogatja a többhelyű.

### <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

Ez az ellenőrzés ellenőrzi, hogy a Linux Log Analytics-ügynöke a hibrid Runbook Worker csomaggal rendelkezik-e. Ez a csomag a Update Management működéséhez szükséges. További információ: [log Analytics Linux-ügynök nem fut](hybrid-runbook-worker.md#oms-agent-not-running).

Update Management letölti a hibrid Runbook Worker csomagokat az operatív végpontból. Ezért ha a hibrid Runbook-feldolgozó nem fut, és az [Operations Endpoint](#operations-endpoint) ellenőrzés meghiúsul, a frissítés sikertelen lehet.

### <a name="hybrid-runbook-worker-status"></a>Hibrid Runbook Worker állapota

Ez az ellenőrzés ellenőrzi, hogy a hibrid Runbook Worker fut-e a gépen. Az alábbi példában szereplő folyamatoknak jelen kell lenniük, ha a hibrid Runbook-feldolgozó megfelelően fut.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Kapcsolatok ellenőrzése

### <a name="general-internet-connectivity"></a>Általános internetkapcsolat

Ez az ellenőrzés biztosítja, hogy a számítógép hozzáférjen az internethez.

### <a name="registration-endpoint"></a>Regisztrációs végpont

Ez az érték határozza meg, hogy a hibrid Runbook-feldolgozó megfelelően tud-e kommunikálni a Log Analytics munkaterületen Azure Automationokkal.

A proxy és a tűzfal konfigurációjának lehetővé kell tennie, hogy a hibrid Runbook Worker ügynök kommunikáljon a regisztrációs végponttal. A megnyitni kívánt címek és portok listáját itt tekintheti meg: [Network Planning](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Műveleti végpont

Ez az érték határozza meg, hogy a Log Analytics ügynök megfelelően tud-e kommunikálni a feladatütemezés adatszolgáltatásával.

A proxy és a tűzfal konfigurációjának lehetővé kell tennie, hogy a hibrid Runbook-feldolgozó ügynök kommunikáljon a feladatütemezés adatszolgáltatásával. A megnyitni kívánt címek és portok listáját itt tekintheti meg: [Network Planning](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Log Analytics 1. végpont

Ez az ellenőrzés ellenőrzi, hogy a számítógép rendelkezik-e hozzáféréssel a Log Analytics ügynök által igényelt végpontokhoz.

### <a name="log-analytics-endpoint-2"></a>Log Analytics 2. végpont

Ez az ellenőrzés ellenőrzi, hogy a számítógép rendelkezik-e hozzáféréssel a Log Analytics ügynök által igényelt végpontokhoz.

### <a name="log-analytics-endpoint-3"></a>Log Analytics 3. végpont

Ez az ellenőrzés ellenőrzi, hogy a számítógép rendelkezik-e hozzáféréssel a Log Analytics ügynök által igényelt végpontokhoz.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Offline hibák

A hibakeresőt offline módon használhatja hibrid Runbook-feldolgozón a parancsfájl helyi futtatásával. A [UM_Linux_Troubleshooter_Offline.](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py)a Python-szkript a githubban található. A szkript kimenetének példája az alábbi példában látható:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Következő lépések

A [hibrid Runbook-feldolgozói problémák elhárítása](hybrid-runbook-worker.md).
