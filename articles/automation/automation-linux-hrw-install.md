---
title: Linuxos hibrid Runbook-feldolgozó üzembe helyezése Azure Automation
description: Ez a cikk azt ismerteti, hogyan telepíthet egy Azure Automation hibrid Runbook-feldolgozót a runbookok Linux-alapú gépeken való futtatásához a helyi adatközpontban vagy a felhőalapú környezetben.
services: automation
ms.subservice: process-automation
ms.date: 02/26/2021
ms.topic: conceptual
ms.openlocfilehash: d4d9bcd16e36e76808f19f7fbd43dd0d3e7550c3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182332"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker üzembe helyezése

A Azure Automation User Hybrid Runbook Worker funkciójának használatával közvetlenül az Azure-beli vagy nem Azure-beli gépen futtathatja a runbookok, beleértve az [Azure arc-kompatibilis kiszolgálókon](../azure-arc/servers/overview.md)regisztrált kiszolgálókat is. A szerepkört üzemeltető gépről vagy kiszolgálóról a runbookok közvetlenül, illetve a környezetben lévő erőforrásokkal is futtathatja a helyi erőforrások kezeléséhez.

A Linux Hybrid Runbook Worker a runbookok-t olyan speciális felhasználóként hajtja végre, amely emelt szintű jogosultságszint-emelést igénylő parancsok futtatására használható. A Azure Automation a runbookok tárolja és felügyeli, majd egy vagy több kijelölt gépre továbbítja azokat. Ez a cikk bemutatja, hogyan telepítheti a hibrid Runbook-feldolgozót egy Linux rendszerű gépre, hogyan távolíthatja el a feldolgozót, és hogyan távolíthat el egy hibrid Runbook-feldolgozói csoportot.

A runbook-feldolgozó sikeres üzembe helyezése után tekintse át a [Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md) című témakört, amelyből megtudhatja, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következőkkel.

### <a name="a-log-analytics-workspace"></a>Log Analytics munkaterület

A hibrid Runbook-feldolgozói szerepkör a szerepkör telepítéséhez és konfigurálásához Azure Monitor Log Analytics munkaterülettől függ. [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), a [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)vagy a [Azure Portal](../azure-monitor/logs/quick-create-workspace.md)segítségével hozhatja létre.

Ha nem rendelkezik Azure Monitor Log Analytics munkaterülettel, tekintse át a [Azure monitor log tervezési útmutatót](../azure-monitor/logs/design-logs-deployment.md) a munkaterület létrehozása előtt.

### <a name="log-analytics-agent"></a>Log Analytics-ügynök

A hibrid Runbook feldolgozói szerepkörhöz a [log Analytics ügynök](../azure-monitor/agents/log-analytics-agent.md) szükséges a támogatott Linux operációs rendszerhez. Az Azure-on kívül üzemeltetett kiszolgálók vagy gépek esetében a Log Analytics-ügynököt az [Azure arc-kompatibilis kiszolgálók](../azure-arc/servers/overview.md)használatával telepítheti.

>[!NOTE]
>A Linux rendszerhez készült Log Analytics-ügynök telepítése után ne módosítsa a `sudoers.d` mappa vagy a tulajdonosának engedélyeit. Sudo engedély szükséges a **nxautomation** -fiókhoz, amely a hibrid Runbook-feldolgozót futtató felhasználói környezet. A rendszer nem távolítja el az engedélyeket. Ha bizonyos mappákra vagy parancsokra korlátozza ezt a korlátozást, előfordulhat, hogy a rendszer megszakítja a változást.
>

### <a name="supported-linux-operating-systems"></a>Támogatott linuxos operációs rendszerek

A hibrid Runbook Worker szolgáltatás a következő disztribúciókat támogatja. Az összes operációs rendszer x64-ként van feltételezve. az x86 semmilyen operációs rendszer esetén nem támogatott.

* Amazon Linux 2012,09 – 2015,09
* CentOS Linux 5, 6, 7 és 8
* Oracle Linux 5, 6 és 7
* Red Hat Enterprise Linux Server 5, 6, 7 és 8
* Debian GNU/Linux 6, 7 és 8
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS és 18,04 LTS
* SUSE Linux Enterprise Server 12 és 15 (a SUSE nem adta meg a 13. vagy 14. számú verziót)

> [!IMPORTANT]
> A Update Management funkció engedélyezése előtt, amely a rendszer hibrid Runbook feldolgozói szerepkörtől függ, erősítse meg az [itt](update-management/overview.md#supported-operating-systems)támogatott disztribúciókat.

### <a name="minimum-requirements"></a>Minimális követelmények

A Linux rendszer és a felhasználói hibrid Runbook-feldolgozó minimális követelményei a következők:

* Két mag
* 4 GB RAM
* 443-es port (kimenő)

| **Szükséges csomag** | **Leírás** | **Minimális verzió**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C könyvtár| 2.5-12 |
|Openssl| OpenSSL-kódtárak | 1,0 (a TLS 1,1 és a TLS 1,2 támogatott)|
|Curl | cURL webes ügyfél | 7.15.5|
|Python – ctypes | Python 2. x vagy Python 3. x szükséges |
|PAM | Cserélhető hitelesítési modulok|
| **Választható csomag** | **Leírás** | **Minimális verzió**|
| PowerShell Core | A PowerShell-runbookok futtatásához telepíteni kell a PowerShell Core-t. A telepítésének megismeréséhez lásd: [a PowerShell Core telepítése Linux rendszeren](/powershell/scripting/install/installing-powershell-core-on-linux) . | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Gép felvétele hibrid Runbook Worker-csoportba

A munkavégző gépet hozzáadhatja egy hibrid Runbook Worker-csoporthoz az egyik Automation-fiókban. A Update Management által felügyelt, rendszerhibrid Runbook-feldolgozót futtató gépekhez hozzáadhatók egy hibrid Runbook Worker csoportjához. Azonban ugyanazt az Automation-fiókot kell használnia mind a Update Management, mind a hibrid Runbook-feldolgozói csoporttagság esetében.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automatikusan telepíti a hibrid Runbook-feldolgozót egy olyan Azure-beli vagy nem Azure-beli gépen, amely engedélyezve van a Update Management számára. Ez a feldolgozó azonban nincs regisztrálva az Automation-fiókjában található bármely hibrid Runbook Worker-csoportban. A runbookok ezen gépeken való futtatásához hozzá kell adnia őket egy hibrid Runbook-feldolgozói csoporthoz. Kövesse a 4. lépést a [Linux Hybrid Runbook Worker telepítése](#install-a-linux-hybrid-runbook-worker) című szakaszban a csoportba való felvételhez.

## <a name="supported-linux-hardening"></a>Támogatott Linux-megerősítés

A következők még nem támogatottak:

* VIR

## <a name="supported-runbook-types"></a>Támogatott runbook-típusok

A linuxos hibrid Runbook-feldolgozók korlátozott számú Runbook-típust támogatnak Azure Automationban, és a következő táblázatban vannak leírva.

|Runbook típusa | Támogatott |
|-------------|-----------|
|Python 3 (előzetes verzió)|Igen, csak a következő disztribúciók esetében szükséges: SUSE LES 15, RHEL 8 és CentOS 8|
|Python 2 |Igen, minden olyan disztribúció esetében, amelyhez nem szükséges a Python 3<sup>1</sup> |
|PowerShell |Igen<sup>2</sup> |
|PowerShell-munkafolyamat |Nem |
|Grafikus |Nem |
|Grafikus PowerShell-munkafolyamat |Nem |

<sup>1</sup> Lásd: [támogatott Linux operációs rendszerek](#supported-linux-operating-systems).

<sup>2</sup> A PowerShell-runbookok a PowerShell Core-t kell telepíteni a Linux rendszerű gépen. A telepítésének megismeréséhez lásd: [a PowerShell Core telepítése Linux rendszeren](/powershell/scripting/install/installing-powershell-core-on-linux) .

### <a name="network-configuration"></a>Hálózati konfiguráció

A hibrid Runbook-feldolgozók hálózati követelményeivel kapcsolatban lásd: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker telepítése

A Linux Hybrid Runbook Worker telepítéséhez és konfigurálásához hajtsa végre az alábbi lépéseket.

1. Engedélyezze a Azure Automation megoldást a Log Analytics munkaterületen, és futtassa a következő parancsot egy emelt szintű PowerShell-parancssorban vagy Cloud Shell a [Azure Portalban](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Telepítse a Log Analytics ügynököt a célszámítógépen.

    * Az Azure-beli virtuális gépek esetében telepítse a Linux rendszerhez készült Log Analytics-ügynököt a [linuxos virtuálisgép-bővítmény](../virtual-machines/extensions/oms-linux.md)használatával. A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és egy meglévő Log Analytics-munkaterületre regisztrálja a virtuális gépeket. Használhat Azure Resource Manager sablont, az Azure CLI-t vagy a Azure Policyt, hogy hozzárendelje az [üzembe helyezési log Analytics ügynököt a *Linux* vagy a *Windows rendszerű* virtuális gépek](../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjéhez. Miután telepítette az ügynököt, a gép hozzáadhatók az Automation-fiókjában lévő hibrid Runbook Worker csoportjához.

    * A nem Azure-beli gépek esetében az Log Analytics-ügynököt az [Azure arc-kompatibilis kiszolgálók](../azure-arc/servers/overview.md)használatával telepítheti. Az arc-kompatibilis kiszolgálók a következő módszerekkel támogatják a Log Analytics ügynök telepítését:

        - A VM-bővítmények keretrendszer használata.

            Ez a funkció az Azure arc-kompatibilis kiszolgálókon lehetővé teszi, hogy az Log Analytics Agent virtuálisgép-bővítményt egy nem Azure-beli Windows-és/vagy Linux-kiszolgálóra telepítse. A virtuálisgép-bővítmények a következő módszerekkel kezelhetők a hibrid gépeken vagy az arc-kompatibilis kiszolgálók által felügyelt kiszolgálókon:

            - A [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - Az [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager-sablonok](../azure-arc/servers/manage-vm-extensions-template.md)

        - Azure Policy használata.

            Ezzel a módszerrel a Azure Policy [log Analytics Agent üzembe helyezése Linux vagy a Windows Azure arc gépek](../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjének használatával naplózhatja, hogy az ív használatára képes kiszolgáló rendelkezik-e a log Analytics ügynökkel. Ha az ügynök nincs telepítve, az automatikusan szervizelési feladat használatával telepíti azt. Ha azt tervezi, hogy Azure Monitor for VMs használatával figyeli a gépeket, Ehelyett használja a [Azure monitor for VMS engedélyezése](../governance/policy/samples/built-in-initiatives.md#monitoring) kezdeményezést az log Analytics-ügynök telepítéséhez és konfigurálásához.

        Javasoljuk, hogy Azure Policy használatával telepítse a Windows vagy Linux rendszerhez készült Log Analytics-ügynököt.

    > [!NOTE]
    > A hibrid Runbook-feldolgozói szerepkört a kívánt állapot-konfigurációval (DSC) támogató gépek konfigurációjának kezeléséhez a gépeket DSC-csomópontként kell hozzáadnia.

    > [!NOTE]
    > A Linux Hybrid Worker telepítése során a megfelelő sudo engedélyekkel rendelkező [nxautomation-fióknak](automation-runbook-execution.md#log-analytics-agent-for-linux) jelen kell lennie. Ha megpróbálja telepíteni a munkavégzőt, és a fiók nem létezik, vagy nem rendelkezik a megfelelő engedélyekkel, a telepítés sikertelen lesz.

3. Ellenőrizze, hogy az ügynök jelentést tesz-e a munkaterületre

    A Linux Log Analytics-ügynöke csatlakoztatja a gépeket egy Azure Monitor Log Analytics-munkaterülethez. Amikor telepíti az ügynököt a gépre, és összekapcsolja a munkaterülettel, automatikusan letölti a hibrid Runbook-feldolgozóhoz szükséges összetevőket.

    Ha az ügynök néhány perc elteltével sikeresen csatlakozott a Log Analytics munkaterülethez, a következő lekérdezés futtatásával ellenőrizheti, hogy a rendszer a szívverési adatokat küldi a munkaterületre.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    A keresési eredmények között meg kell jelennie a gép szívverési rekordjainak, ami azt jelzi, hogy csatlakoztatva van, és a szolgáltatáshoz jelent jelentést. Alapértelmezés szerint minden ügynök egy szívverési rekordot továbbít a hozzárendelt munkaterülethez.

4. A következő parancs futtatásával vegye fel a gépet egy hibrid Runbook Worker-csoportba, adja meg a paraméterek, a, és a értékeit `-w` `-k` `-g` `-e` .

    A paraméterekhez `-k` és az `-e` Automation-fiók **kulcsok** lapjáról kérheti le a szükséges információkat. A lap bal oldalán található **Fiókbeállítások** szakaszban válassza a **kulcsok** lehetőséget.

    ![Kulcsok kezelése lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * A `-e` paraméter esetében másolja az **URL-cím** értékét.

    * A `-k` paraméter esetében másolja az **elsődleges elérési kulcs** értékét.

    * A `-g` paraméternél adja meg annak a hibrid Runbook-feldolgozó csoportnak a nevét, amelyhez az új Linux Hybrid Runbook Worker csatlakoznia kell. Ha ez a csoport már létezik az Automation-fiókban, az aktuális gép hozzá lesz adva. Ha ez a csoport nem létezik, akkor a rendszer ezt a nevet hozza létre.

    * A `-w` paraméternél adja meg a log Analytics-munkaterület azonosítóját.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Ellenőrizze a telepítést a parancsfájl befejeződése után. Az Automation-fiók **Hybrid Runbook Worker** groups lapján a **felhasználó hibrid Runbook-feldolgozói csoport** lapon az új vagy a meglévő csoport és a tagok száma látható. Ha ez egy meglévő csoport, a tagok száma nő. A listából kiválaszthatja a kívánt csoportot a lap bal oldali menüjében a **hibrid feldolgozók** kiválasztása lehetőséggel. A **hibrid dolgozók** oldalon láthatja a csoport egyes tagjainak listáját.

    > [!NOTE]
    > Ha a Linux rendszerhez készült Log Analytics virtuálisgép-bővítményt használja az Azure-beli virtuális gépekhez, javasoljuk, `autoUpgradeMinorVersion` hogy a `false` verzió automatikus verziófrissítésének beállítása a hibrid Runbook-feldolgozóval kapcsolatos problémákat okozzon. A bővítmény manuális frissítéséről az [Azure CLI üzembe helyezésével](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)foglalkozó témakörben olvashat bővebben.

## <a name="turn-off-signature-validation"></a>Aláírás-ellenőrzés kikapcsolása

A Linux Hybrid Runbook-feldolgozók alapértelmezés szerint aláírás-ellenőrzést igényelnek. Ha aláíratlan runbook futtat egy feldolgozón, hibaüzenet jelenik meg `Signature validation failed` . Az aláírás-ellenőrzés kikapcsolásához futtassa a következő parancsot. Cserélje le a második paramétert a Log Analytics munkaterület-azonosítójával.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>A hibrid Runbook Worker eltávolítása

A `ls /var/opt/microsoft/omsagent` hibrid Runbook-feldolgozón a parancs használatával kérheti le a munkaterület azonosítóját. A rendszer létrehoz egy nevű mappát a munkaterület-AZONOSÍTÓval.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ez a szkript nem távolítja el a Linux rendszerhez készült Log Analytics-ügynököt a gépről. Csak a hibrid Runbook-feldolgozói szerepkör funkcióit és konfigurációját távolítja el.

## <a name="remove-a-hybrid-worker-group"></a>Hibridfeldolgozó-csoport eltávolítása

Linux rendszerű gépek hibrid Runbook Worker csoportjának eltávolításához ugyanazokat a lépéseket kell használnia, mint a Windows Hybrid Worker-csoport esetében. Lásd: [hibrid feldolgozói csoport eltávolítása](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.

* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozói problémák elhárítása – Linux](troubleshoot/hybrid-runbook-worker.md#linux).