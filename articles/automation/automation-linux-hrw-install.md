---
title: Linux rendszerű hibrid runbook-feldolgozó üzembe helyezése a Azure Automation
description: Ez a cikk azt mutatja be, hogyan telepíthet Azure Automation hibrid runbook-feldolgozót runbookok futtatásához Linux-alapú gépeken a helyi adatközpontban vagy felhőkörnyezetben.
services: automation
ms.subservice: process-automation
ms.date: 04/06/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24dc0d2b243eb6c13e5670a1438876132c5e429e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833651"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux rendszerű hibrid runbook-feldolgozó üzembe helyezése

A Azure Automation felhasználó hibrid runbook-feldolgozó szolgáltatásával közvetlenül az Azure-on vagy nem Azure-beli gépen futtathat runbookokat, beleértve az engedélyezett kiszolgálókon regisztrált Azure Arc [is.](../azure-arc/servers/overview.md) A szerepkört üzemeltető gépről vagy kiszolgálóról közvetlenül futtathat runbookokat a környezet erőforrásain a helyi erőforrások kezeléséhez.

A Linux rendszerű hibrid runbook-feldolgozó speciális felhasználóként hajtja végre a runbookokat, amelyek megemelkednek a jogosultságszint-emelést követelő parancsok futtatásához. Azure Automation tárolja és kezeli a runbookokat, majd egy vagy több kijelölt gépre kézbesíti azokat. Ez a cikk bemutatja, hogyan telepítheti a hibrid runbook-feldolgozót linuxos gépekre, hogyan távolíthatja el a feldolgozót, és hogyan távolíthat el egy hibrid runbook-feldolgozó csoportot.

Miután sikeresen üzembe helyezett egy runbook-feldolgozót, tekintse át [a Runbookok](automation-hrw-run-runbooks.md) futtatása hibrid runbook-feldolgozón cikkeket, amelyekből megtudhatja, hogyan konfigurálhatja a runbookokat a folyamatok automatizálására a helyszíni adatközpontban vagy más felhőalapú környezetben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy az alábbiakkal fogta fel a következőket.

### <a name="a-log-analytics-workspace"></a>Log Analytics-munkaterület

A hibrid runbook-feldolgozói szerepkör egy Azure Monitor Log Analytics-munkaterülettől függ, amely telepíti és konfigurálja a szerepkört. A létrehozásához használhatja a [Azure Resource Manager,](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)a [PowerShell használatával,](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)vagy a következő [Azure Portal.](../azure-monitor/logs/quick-create-workspace.md)

Ha nem rendelkezik Log Analytics-Azure Monitor, a munkaterület [](../azure-monitor/logs/design-logs-deployment.md) létrehozása előtt Azure Monitor tekintse át a naplótervezési útmutatót.

### <a name="log-analytics-agent"></a>Log Analytics-ügynök

A hibrid runbook-feldolgozói szerepkörhöz szükség van a [Log Analytics-ügynökre](../azure-monitor/agents/log-analytics-agent.md) a támogatott Linux operációs rendszerhez. Az Azure-ban üzemeltetett kiszolgálók vagy gépek esetén a Log Analytics-ügynököt az engedélyezett Azure Arc [telepítheti.](../azure-arc/servers/overview.md)

>[!NOTE]
>A Linuxhoz való Log Analytics-ügynök telepítése után ne módosítsa a mappa engedélyét vagy `sudoers.d` tulajdonosát. Sudo engedély szükséges az **nxautomation** fiókhoz, amely az a felhasználói környezet, amelyben a hibrid runbook-feldolgozó fut. Az engedélyeket nem szabad eltávolítani. Ha ezt bizonyos mappákra vagy parancsokra korlátozza, az a feltörést eredményez.
>

### <a name="supported-linux-operating-systems"></a>Támogatott linuxos operációs rendszerek

A hibrid runbook-feldolgozó szolgáltatás a következő disztribúciókat támogatja. A rendszer minden operációs rendszert x64-nek feltételez. Az x86 semmilyen operációs rendszer esetén nem támogatott.

* Amazon Linux 2012.09–2015.09
* CentOS Linux 5, 6, 7 és 8
* Oracle Linux 5, 6 és 7
* Red Hat Enterprise Linux Server 5, 6, 7 és 8
* Debian GNU/Linux 6, 7 és 8
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS és 18.04 LTS
* SUSE Linux Enterprise Server 12-es és 15-ös verzió (a SUSE nem a 13-as vagy 14-es verziókat adott ki)

> [!IMPORTANT]
> Az Update Management hibrid runbook-feldolgozói szerepkörtől függő szolgáltatás engedélyezése előtt ellenőrizze [](update-management/overview.md#supported-operating-systems)az itt támogatott disztribúciókat.

### <a name="minimum-requirements"></a>Minimális követelmények

A Linux rendszer és a felhasználó hibrid runbook-feldolgozója esetében a következő minimális követelmények vonatkoznak:

* Két mag
* 4 GB RAM
* 443-as port (kimenő)

| **Szükséges csomag** | **Leírás** | **Minimális verzió**|
|--------------------- | --------------------- | -------------------|
|Mánybc (Mánybc) |GNU C kódtár| 2.5-12 |
|Openssl| OpenSSL-kódtárak | 1.0 (a TLS 1.1 és a TLS 1.2 támogatott)|
|Curl | cURL webes ügyfél | 7.15.5|
|Python-ctypes | Python 2.x vagy Python 3.x szükséges |
|PAM | Cserélhető hitelesítési modulok|
| **Nem kötelező csomag** | **Leírás** | **Minimális verzió**|
| PowerShell Core | PowerShell-runbookok futtatásához PowerShell Core kell telepíteni. A [telepítésével PowerShell Core linuxos](/powershell/scripting/install/installing-powershell-core-on-linux) alkalmazások telepítésével kapcsolatos cikkből megtudhatja, hogyan telepítheti. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Gép hozzáadása hibrid runbook-feldolgozó csoporthoz

A feldolgozó gépet hozzáadhatja egy hibrid runbook-feldolgozó csoporthoz az egyik Automation-fiókban. A hibrid runbook-feldolgozó által felügyelt hibrid runbook-feldolgozót Update Management gépek hozzáadhatók egy hibrid runbook-feldolgozó csoporthoz. Azonban ugyanazt az Automation-fiókot kell használnia a Update Management és a hibrid runbook-feldolgozó csoporttagsághoz is.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automatikusan telepíti a rendszer hibrid runbook-feldolgozóját egy Azure-beli vagy nem Azure-beli gépre, amely Update Management. Ez a feldolgozó azonban nincs regisztrálva hibrid runbook-feldolgozói csoportokkal az Automation-fiókban. A runbookok ezen a gépeken való futtatásához hozzá kell adni őket egy hibrid runbook-feldolgozó csoporthoz. Kövesse a Linux rendszerű hibrid [runbook-feldolgozó](#install-a-linux-hybrid-runbook-worker) telepítése szakasz 4. lépését, és adja hozzá egy csoporthoz.

## <a name="supported-linux-hardening"></a>Támogatott Linux-alapú megsokkolás

Az alábbiak még nem támogatottak:

* Cis

## <a name="supported-runbook-types"></a>Támogatott runbooktípusok

A Linux hibrid runbook-dolgozók a runbooktípusok korlátozott készletét támogatják a Azure Automation, és ezeket az alábbi táblázat ismerteti.

|Runbook típusa | Támogatott |
|-------------|-----------|
|Python 3 (előzetes verzió)|Igen, csak ezekhez a disztribúciókhoz szükséges: SUSE LES 15, RHEL 8 és CentOS 8|
|Python 2 |Igen, minden olyan disztribúcióhoz, amely nem igényli a Python 3<sup>1-et</sup> |
|PowerShell |Igen<sup>2</sup> |
|PowerShell-munkafolyamat |No |
|Grafikus |No |
|Grafikus PowerShell-munkafolyamat |No |

<sup>1</sup> Lásd: [Támogatott Linux operációs rendszerek.](#supported-linux-operating-systems)

<sup>2</sup> A PowerShell-runbookok PowerShell Core linuxos gépre kell telepíteni. A [Telepítés PowerShell Core Linux rendszeren cikkből](/powershell/scripting/install/installing-powershell-core-on-linux) megtudhatja, hogyan telepítheti.

### <a name="network-configuration"></a>Hálózati konfiguráció

A hibrid runbook-feldolgozó hálózatra vonatkozó követelményeiért lásd: [A hálózat konfigurálása.](automation-hybrid-runbook-worker.md#network-planning)

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux rendszerű hibrid runbook-feldolgozó telepítése

A hibrid runbook-feldolgozó üzembe helyezésének két módja van. Importálhat és futtathat egy runbookot a runbook-katalógusból a Azure Portal, vagy manuálisan futtathat Több PowerShell-parancsot is ugyanennek a feladatnak a végrehajtásához.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Runbook importálása a Runbook-katalógusból

Az importálási eljárást részletesen a Runbookok importálása a [GitHubról](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)a Azure Portal. Az importálni szükséges runbook neve **Create Automation Linux HybridWorker**.

A runbook a következő paramétereket használja.

| Paraméter | Állapot | Leírás |
| ------- | ----- | ----------- |
| `Location` | Kötelező | A Log Analytics-munkaterület helye. |
| `ResourceGroupName` | Kötelező | Az Automation-fiók erőforráscsoportja. |
| `AccountName` | Kötelező | Az Automation-fiók neve, amelyben a hibrid futtatás-feldolgozó regisztrálva lesz. |
| `CreateLA` | Kötelező | Ha igaz, a a értékével `WorkspaceName` hoz létre egy Log Analytics-munkaterületet. Ha hamis, a értékének `WorkspaceName` egy meglévő munkaterületre kell hivatkozni. |
| `LAlocation` | Választható | Az a hely, ahol a Log Analytics-munkaterület létre lesz hozva, vagy ahol már létezik. |
| `WorkspaceName` | Választható | A létrehozni vagy használni fog Log Analytics-munkaterület neve. |
| `CreateVM` | Kötelező | Ha igaz, használja a értéket egy új virtuális `VMName` gép neveként. Ha hamis, a használatával `VMName` keresse meg és regisztrálja a meglévő virtuális gépet. |
| `VMName` | Választható | A létrehozott vagy regisztrált virtuális gép neve a értékétől `CreateVM` függően. |
| `VMImage` | Választható | A létrehozni fog virtuálisgép-rendszerkép neve. |
| `VMlocation` | Választható | A létrehozott vagy regisztrált virtuális gép helye. Ha ez a hely nincs megadva, a érték `LAlocation` lesz használva. |
| `RegisterHW` | Kötelező | Ha igaz, regisztrálja a virtuális gépet hibrid feldolgozóként. |
| `WorkerGroupName` | Kötelező | A hibrid feldolgozócsoport neve. |

### <a name="manually-run-powershell-commands"></a>PowerShell-parancsok manuális futtatása

Linux rendszerű hibrid runbook-feldolgozó telepítéséhez és konfiguráláshoz hajtsa végre az alábbi lépéseket.

1. Engedélyezze a Azure Automation megoldást a Log Analytics-munkaterületen a következő parancs rendszergazda jogú PowerShell-parancssorban vagy az Cloud Shell-ban [Azure Portal:](https://portal.azure.com)

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Telepítse a Log Analytics-ügynököt a célgépre.

    * Azure-beli virtuális gépek esetén telepítse a Linuxhoz való Log Analytics-ügynököt a [Linux virtuálisgép-bővítményével.](../virtual-machines/extensions/oms-linux.md) A bővítmény telepíti a Log Analytics-ügynököt az Azure-beli virtuális gépekre, és regisztrálja a virtuális gépeket egy meglévő Log Analytics-munkaterületen. A Log Analytics Azure Resource Manager sablon, az Azure CLI vagy a Azure Policy használatával hozzárendelheti a [Deploy Log Analytics agent for Linux or Windows VMs (Log Analytics-ügynök üzembe helyezése *Linux*](../governance/policy/samples/built-in-policies.md#monitoring) vagy Windows rendszerű virtuális gépekhez) beépített szabályzatot. Az ügynök telepítése után a gép hozzáadható egy hibrid runbook-feldolgozó csoporthoz az Automation-fiókban.

    * Nem Azure-beli gépeken a Log Analytics-ügynököt az engedélyezett Azure Arc [telepítheti.](../azure-arc/servers/overview.md) Az Arc-kompatibilis kiszolgálók a következő módszerekkel támogatják a Log Analytics-ügynök üzembe helyezését:

        - A virtuálisgép-bővítmények keretrendszerének használata.

            Az engedélyezett Azure Arc szolgáltatása lehetővé teszi a Log Analytics-ügynök virtuálisgép-bővítményének üzembe helyezését egy nem Azure-beli Windows- és/vagy Linux-kiszolgálón. A virtuálisgép-bővítmények a következő módszerekkel kezelhetők az Arc-kompatibilis kiszolgálók által felügyelt hibrid gépeken vagy kiszolgálókon:

            - A [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - Az [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager sablonok](../azure-arc/servers/manage-vm-extensions-template.md)

        - A Azure Policy.

            Ezzel a módszerrel a Azure Policy [Deploy Log Analytics agent to Linux](../governance/policy/samples/built-in-policies.md#monitoring) or Windows Azure Arc machines (Log Analytics-ügynök telepítése Linux vagy Windows Azure Arc rendszerű gépekre) beépített szabályzatával naplózhatja, hogy az Arc-kompatibilis kiszolgálón telepítve van-e a Log Analytics-ügynök. Ha az ügynök nincs telepítve, automatikusan üzembe helyez egy szervizelési feladattal. Ha a gépeket a Azure Monitor for VMs szeretné figyelni, ehelyett az [Enable Azure Monitor for VMs](../governance/policy/samples/built-in-initiatives.md#monitoring) kezdeményezéssel telepítheti és konfigurálhatja a Log Analytics-ügynököt.

        Javasoljuk, hogy telepítse a Log Analytics-ügynököt Windows vagy Linux rendszeren a Azure Policy.

    > [!NOTE]
    > A hibrid runbook-feldolgozói szerepkört támogató gépek konfigurációjának kezeléséhez Desired State Configuration (DSC) kell hozzáadnia a gépeket DSC-csomópontokként.

    > [!NOTE]
    > A linuxos hibrid feldolgozó telepítése során a megfelelő sudo engedélyekkel rendelkező [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) fióknak jelen kell lennie. Ha megpróbálja telepíteni a feldolgozót, és a fiók nincs jelen, vagy nem rendelkezik a megfelelő engedélyekkel, a telepítés sikertelen lesz.

3. Ellenőrizze, hogy az ügynök jelent-e a munkaterületnek.

    A Linuxhoz használható Log Analytics-ügynök a gépeket egy Azure Monitor Log Analytics-munkaterülethez csatlakoztatja. Amikor telepíti az ügynököt a gépére, és csatlakoztatja a munkaterülethez, az automatikusan letölti a hibrid runbook-feldolgozóhoz szükséges összetevőket.

    Ha az ügynök néhány perc után sikeresen csatlakozott a Log Analytics-munkaterülethez, a következő lekérdezés futtatásával ellenőrizheti, hogy szívverési adatokat küld-e a munkaterületre.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    A keresési eredmények között látnia kell a gép szívverési rekordjait, amelyek jelzik, hogy a gép csatlakoztatva van, és jelenti a szolgáltatásnak. Alapértelmezés szerint minden ügynök szívverési rekordot továbbít a hozzárendelt munkaterületre.

4. A következő parancs futtatásával adja hozzá a gépet egy hibrid runbook-feldolgozó csoporthoz, és adja meg a `-w` , , és paraméterek `-k` `-g` `-e` értékeit.

    A paraméterekhez szükséges információkat az Automation-fiók Kulcsok lapján `-k` `-e` kaphatja meg.  Válassza a  **Lap** bal oldalán található Fiókbeállítások szakaszban található Kulcsok lehetőséget.

    ![Kulcsok kezelése lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * A `-e` paraméternél másolja ki az **URL** értékét.

    * A `-k` paraméternél másolja ki az **ELSŐDLEGES HOZZÁFÉRÉSI KULCS értékét.**

    * A paraméterhez adja meg annak a hibrid runbook-feldolgozó csoportnak a nevét, amelybe az új `-g` linuxos hibrid runbook-feldolgozónak csatlakoznia kell. Ha ez a csoport már létezik az Automation-fiókban, a rendszer hozzáadja az aktuális gépet. Ha ez a csoport nem létezik, a csoport ezzel a névvel jön létre.

    * A `-w` paraméterhez adja meg a Log Analytics-munkaterület azonosítóját.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. A szkript befejezése után ellenőrizze az üzembe helyezést. Az **Automation-fiók** Hibrid runbook-feldolgozó csoportok lapján, a Felhasználó **hibrid runbook-feldolgozók** csoport lapján megjelenik az új vagy meglévő csoport és a tagok száma. Ha ez egy meglévő csoport, a tagok száma növekszik. A csoportot kiválaszthatja az oldalon található listából, majd a bal oldali menüben válassza a **Hibrid dolgozók lehetőséget.** A Hibrid **dolgozók lapon** a csoport minden tagja megjelenik.

    > [!NOTE]
    > Ha a Linuxhoz használható Log Analytics virtuálisgép-bővítményt használja egy Azure-beli virtuális géphez, javasoljuk, hogy a beállítást az automatikus verziófrissítéshez használja, ami problémákat okozhat a hibrid `autoUpgradeMinorVersion` `false` runbook-feldolgozóval kapcsolatban. A bővítmény manuális frissítésének elsajátításáról az Azure CLI üzemelő [példányában olvashat.](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)

## <a name="turn-off-signature-validation"></a>Az aláírás-ellenőrzés kikapcsolása

A Linux rendszerű hibrid runbook-dolgozók alapértelmezés szerint aláírás-ellenőrzést igényelnek. Ha aláíratlan runbookot futtat egy feldolgozón, hibaüzenet `Signature validation failed` jelenik meg. Az aláírás-érvényesítést a következő paranccsal kapcsolhatja ki. Cserélje le a második paramétert a Log Analytics-munkaterület azonosítójára.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>A hibrid runbook-feldolgozó eltávolítása

A hibrid `ls /var/opt/microsoft/omsagent` runbook-feldolgozón az paranccsal lekérte a munkaterület azonosítóját. Létrejön egy mappa, amely a munkaterület-azonosítóval van elnevezve.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ez a szkript nem távolítja el a Linuxhoz használt Log Analytics-ügynököt a gépről. Csak a hibrid runbook-feldolgozó szerepkör funkcióit és konfigurációját távolítja el.

## <a name="remove-a-hybrid-worker-group"></a>Hibridfeldolgozó-csoport eltávolítása

Linux rendszerű gépek hibrid runbook-feldolgozó csoportjának eltávolításához ugyanazok a lépések használhatók, mint a Windows rendszerű hibrid feldolgozócsoport esetében. Lásd: [Hibrid feldolgozócsoport eltávolítása.](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)

## <a name="next-steps"></a>Következő lépések

* A runbookok helyszíni adatközpontban vagy más felhőalapú környezetben való folyamatautomatizálására való konfigurálásával kapcsolatos további információkért lásd: Runbookok futtatása hibrid [runbook-feldolgozón.](automation-hrw-run-runbooks.md)

* A hibrid runbook-feldolgozók hibaelhárításáról a Hibrid runbook-feldolgozóval kapcsolatos problémák elhárítása [– Linux témakörben olvashat.](troubleshoot/hybrid-runbook-worker.md#linux)
