---
title: Windows rendszerű hibrid runbook-feldolgozó üzembe helyezése a Azure Automation
description: Ez a cikk bemutatja, hogyan helyezhet üzembe egy hibrid runbook-feldolgozót, amely segítségével runbookokat futtathat Windows-alapú gépeken a helyi adatközpontban vagy felhőkörnyezetben.
services: automation
ms.subservice: process-automation
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4bf27ffc888e189f15e1c435309cbeddb1c11fec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830339"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows rendszerű hibrid runbook-feldolgozó üzembe helyezése

A Azure Automation felhasználó hibrid runbook-feldolgozó szolgáltatásával közvetlenül azure-beli vagy nem Azure-beli gépen futtathat runbookokat, beleértve az engedélyezett kiszolgálókkal regisztrált Azure Arc [is.](../azure-arc/servers/overview.md) A szerepkört üzemeltető gépről vagy kiszolgálóról futtathat runbookokat közvetlenül rajta és a környezet erőforrásain a helyi erőforrások kezeléséhez.

Azure Automation tárolja és kezeli a runbookokat, majd kézbesíti azokat egy vagy több kijelölt gépre. Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy felhasználó hibrid runbook-feldolgozóját egy Windows rendszerű gépen, hogyan távolíthatja el a feldolgozót, és hogyan távolíthat el egy hibrid runbook-feldolgozó csoportot.

Miután sikeresen üzembe helyezett egy runbook-feldolgozót, tekintse át [a Runbookok](automation-hrw-run-runbooks.md) futtatása hibrid runbook-feldolgozón cikkeket, amelyekből megtudhatja, hogyan konfigurálhatja a forgatókönyveket a helyszíni adatközpontban vagy más felhőalapú környezetben futó folyamatok automatizálására.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy az alábbiakkal fogta fel.

### <a name="a-log-analytics-workspace"></a>Log Analytics-munkaterület

A hibrid runbook-feldolgozói szerepkör egy Azure Monitor Log Analytics-munkaterülettől függ, amely telepíti és konfigurálja a szerepkört. A létrehozásához használhatja a [Azure Resource Manager,](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)a [PowerShell használatával,](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)vagy a következő [Azure Portal.](../azure-monitor/logs/quick-create-workspace.md)

Ha még nem rendelkezik Log Analytics Azure Monitor munkaterülettel, [](../azure-monitor/logs/design-logs-deployment.md) a munkaterület létrehozása előtt Azure Monitor tekintse át a naplótervezési útmutatót.

### <a name="log-analytics-agent"></a>Log Analytics-ügynök

A hibrid runbook-feldolgozó szerepkörhöz szükség van a [Log Analytics-ügynökre](../azure-monitor/agents/log-analytics-agent.md) a támogatott Windows operációs rendszerhez. Az Azure-ban üzemeltetett kiszolgálók vagy gépek esetén a Log Analytics-ügynököt az engedélyezett Azure Arc [telepítheti.](../azure-arc/servers/overview.md)

### <a name="supported-windows-operating-system"></a>Támogatott Windows operációs rendszer

A hibrid runbook-feldolgozó szolgáltatás a következő operációs rendszereket támogatja:

* Windows Server 2019 (beleértve a Server Core-t)
* Windows Server 2016, 1709-es és 1803-as verzió (a Server Core kivételével)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (beleértve a több munkamenetet is) és a Pro
* Windows 8 Enterprise és Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimális követelmények

A Windows rendszer és a felhasználó hibrid runbook-feldolgozója minimális követelményei a következőek:

* Windows PowerShell 5.1 -es[(a WMF 5.1 letöltése).](https://www.microsoft.com/download/details.aspx?id=54616) PowerShell Core nem támogatott.
* A .NET-keretrendszer 4.6.2-es vagy újabb verziója
* Két mag
* 4 GB RAM
* 443-as port (kimenő)

### <a name="network-configuration"></a>Hálózati konfiguráció

A hibrid runbook-feldolgozó hálózati követelményeiért lásd: [A hálózat konfigurálása.](automation-hybrid-runbook-worker.md#network-planning)

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Gép hozzáadása hibrid runbook-feldolgozó csoporthoz

A feldolgozó gépet hozzáadhatja egy hibrid runbook-feldolgozó csoporthoz az egyik Automation-fiókban. A hibrid runbook-feldolgozó által felügyelt hibrid runbook-feldolgozót Update Management gépek hozzáadhatók egy hibrid runbook-feldolgozó csoporthoz. Azonban ugyanazt az Automation-fiókot kell használnia a Update Management és a hibrid runbook-feldolgozó csoporttagsághoz is.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automatikusan telepíti a rendszer hibrid runbook-feldolgozóját egy Azure-beli vagy nem Azure-beli gépre, amely Update Management. Ez a feldolgozó azonban nincs regisztrálva hibrid runbook-feldolgozói csoportokkal az Automation-fiókban. A runbookok ezen a gépeken való futtatásához hozzá kell adni őket egy hibrid runbook-feldolgozó csoporthoz. Kövesse a Manuális üzembe helyezés szakasz 6. lépését, [hogy](#manual-deployment) hozzáadja egy csoporthoz.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Engedélyezés felügyeleti Azure Automation State Configuration

További információ a gépek felügyeletének engedélyezéséről a Azure Automation State Configuration: [Gépek](automation-dsc-onboarding.md)felügyeletének engedélyezése a Azure Automation State Configuration.

> [!NOTE]
> A hibrid runbook-feldolgozói szerepkört támogató gépek konfigurációjának Desired State Configuration (DSC) kezeléséhez dsc-csomópontként kell hozzáadnia a gépeket.

## <a name="installation-options"></a>Telepítési lehetőségek

Windows-felhasználó hibrid runbook-feldolgozó telepítéséhez és konfiguráláshoz a következő módszerek egyikét használhatja.

* A megadott PowerShell-szkript [](#automated-deployment) használatával teljesen automatizálhatja egy vagy több Windows rendszerű gép konfigurálási folyamatát. Ez az ajánlott módszer az adatközpontban vagy más felhőalapú környezetben használt gépekhez.
* Manuálisan importálja az Automation-megoldást, telepítse a Windowshoz való Log Analytics-ügynököt, és konfigurálja a feldolgozói szerepkört a gépen.

## <a name="automated-deployment"></a>Automatikus üzembe helyezés

A hibrid runbook-feldolgozó automatikus üzembe helyezésének két módja van. Importálhat egy runbookot a forgatókönyv-katalógusból a Azure Portal futtathatja, vagy manuálisan is letölthet egy szkriptet a PowerShell-galéria.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Runbook importálása a Runbook-katalógusból

Az importálási eljárást részletesen a Runbookok importálása a [GitHubról](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)a következő Azure Portal. Az importálni szükséges runbook neve **Create Automation Windows HybridWorker**.

A runbook a következő paramétereket használja.

| Paraméter | Állapot | Leírás |
| ------- | ----- | ----------- |
| `Location` | Kötelező | A Log Analytics-munkaterület helye. |
| `ResourceGroupName` | Kötelező | Az Automation-fiók erőforráscsoportja. |
| `AccountName` | Kötelező | Az Automation-fiók neve, amelyben a hibrid futtatású feldolgozó regisztrálva lesz. |
| `CreateLA` | Kötelező | Ha igaz, a a értékével `WorkspaceName` hoz létre egy Log Analytics-munkaterületet. Ha hamis, a értékének `WorkspaceName` egy meglévő munkaterületre kell hivatkozni. |
| `LAlocation` | Választható | A hely, ahol a Log Analytics-munkaterület létrejön, vagy ahol már létezik. |
| `WorkspaceName` | Választható | A használni használt Log Analytics-munkaterület neve. |
| `CreateVM` | Kötelező | Ha igaz, használja a értéket `VMName` egy új virtuális gép neveként. Ha hamis, a használatával `VMName` keresse meg és regisztrálja a meglévő virtuális gépet. |
| `VMName` | Választható | A létrehozott vagy regisztrált virtuális gép neve a értékétől `CreateVM` függően. |
| `VMImage` | Választható | A létrehozni fog virtuálisgép-rendszerkép neve. |
| `VMlocation` | Választható | A létrehozott vagy regisztrált virtuális gép helye. Ha ez a hely nincs megadva, a érték `LAlocation` lesz használva. |
| `RegisterHW` | Kötelező | Ha igaz, regisztrálja a virtuális gépet hibrid feldolgozóként. |
| `WorkerGroupName` | Kötelező | A hibrid feldolgozócsoport neve. |

### <a name="download-a-script-from-the-powershell-gallery"></a>Szkript letöltése a PowerShell-galéria

Ez az automatizált üzembe helyezési módszer a PowerShell-New-OnPremiseHybridWorker.ps1automatizálja és konfigurálja a Windows hibrid runbook-feldolgozó szerepkört.  A következőket végzi el:

* Telepíti a szükséges modulokat
* Bejelentkezik az Azure-fiókjával
* Ellenőrzi a megadott erőforráscsoport és Automation-fiók meglétét
* Az Automation-fiókattribútumokra mutató hivatkozásokat hoz létre
* Létrehoz egy Azure Monitor Log Analytics-munkaterületet, ha nincs megadva
* A Azure Automation engedélyezése a munkaterületen
* A Windowshoz való Log Analytics-ügynök letöltése és telepítése
* A gép regisztrálása hibrid runbook-feldolgozóként

A következő lépések elvégzésével telepítse a szerepkört a Windows rendszerű gépre a szkript használatával.

1. Töltse le **aNew-OnPremiseHybridWorker.ps1** szkriptet a [PowerShell-galéria.](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) Miután letöltötte a szkriptet, másolja vagy futtassa azt a célgépen. A szkript a következő paramétereket használja.

    | Paraméter | Állapot | Leírás |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Kötelező | Az Automation-fiókhoz társított erőforráscsoport neve. |
    | `AutomationAccountName` | Kötelező | Az Automation-fiók neve.
    | `Credential` | Választható | Az Azure-környezetbe való bejelentkezéskor használt hitelesítő adatok. |
    | `HybridGroupName` | Kötelező | Egy hibrid runbook-feldolgozó csoport neve, amely az ezt a forgatókönyvet támogató runbookok céljaként van megszabadva. |
    | `OMSResourceGroupName` | Választható | A Log Analytics-munkaterület erőforráscsoportjának neve. Ha ez az erőforráscsoport nincs megadva, a érték `AAResourceGroupName` lesz használva. |
    | `SubscriptionID` | Kötelező | Az Automation-fiókjához társított Azure-előfizetés azonosítója. |
    | `TenantID` | Választható | Az Automation-fiókhoz társított bérlői szervezet azonosítója. |
    | `WorkspaceName` | Választható | A Log Analytics-munkaterület neve. Ha nincs Log Analytics-munkaterülete, a szkript létrehoz és konfigurál egyet. |

1. Nyisson meg egy emelt szintű, 64 bites PowerShell-parancssort.

1. A PowerShell parancssorában keresse meg azt a mappát, amely a letöltött szkriptet tartalmazza. Módosítsa a , `AutomationAccountName` `AAResourceGroupName` , `OMSResourceGroupName` , , és `HybridGroupName` `SubscriptionID` paraméterek `WorkspaceName` értékeit. Ezután futtassa a szkriptet.

    A szkript futtatása után a rendszer felkéri az Azure-ral való hitelesítésre. Olyan fiókkal kell bejelentkeznie, amely tagja  az Előfizetés-adminisztrátorok szerepkörnek, és társ-rendszergazdája az előfizetésnek.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

1. A rendszer arra kéri, hogy egyeztesen a NuGet telepítéséhez és az Azure-beli hitelesítő adatokkal való hitelesítéshez. Ha nem a legújabb NuGet-verzióval van, letöltheti az Elérhető [NuGet-disztribúciós verziókból.](https://www.nuget.org/downloads)

1. A szkript befejezése után ellenőrizze az üzembe helyezést. Az **Automation-fiók** Hibrid runbook-feldolgozói csoportok lapján, a Felhasználó **hibrid runbook-feldolgozók** csoport lapján megjelenik az új csoport és a tagok száma. Ha ez egy meglévő csoport, a tagok száma növekszik. A csoportot kiválaszthatja az oldalon található listából, majd a bal oldali menüben válassza a **Hibrid dolgozók lehetőséget.** A Hibrid **dolgozók lapon** a csoport minden tagja megjelenik.

## <a name="manual-deployment"></a>Manuális üzembe helyezés

Windows rendszerű hibrid runbook-feldolgozó telepítéséhez és konfiguráláshoz hajtsa végre az alábbi lépéseket.

1. Engedélyezze a Azure Automation megoldást a Log Analytics-munkaterületen a következő parancs rendszergazda jogú PowerShell-parancssorban vagy a Cloud Shell parancssorában [Azure Portal.](https://portal.azure.com)

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

1. Telepítse a Log Analytics-ügynököt a célgépre.

    * Azure-beli virtuális gépek esetén telepítse a Windowshoz való Log Analytics-ügynököt a [Windows virtuálisgép-bővítményével.](../virtual-machines/extensions/oms-windows.md) A bővítmény telepíti a Log Analytics-ügynököt az Azure-beli virtuális gépekre, és regisztrálja a virtuális gépeket egy meglévő Log Analytics-munkaterületen. A Log Analytics Azure Resource Manager linuxos vagy [ *windowsos*](../governance/policy/samples/built-in-policies.md#monitoring) virtuális gépekre készült Log Analytics Azure Policy ügynök üzembe helyezése beépített szabályzat hozzárendeléséhez használhat sablonsablont, PowerShellt vagy Azure Policy. Az ügynök telepítése után a gép hozzáadható egy hibrid runbook-feldolgozó csoporthoz az Automation-fiókban.
    
    * Nem Azure-beli gépeken a Log Analytics-ügynököt a következő engedélyezett [Azure Arc telepítheti:](../azure-arc/servers/overview.md). Az Arc-kompatibilis kiszolgálók a következő módszerekkel támogatják a Log Analytics-ügynök üzembe helyezését:
    
        - A virtuálisgép-bővítmények keretrendszerének használata.
        
            Az engedélyezett Azure Arc ezen szolgáltatása lehetővé teszi a Log Analytics-ügynök virtuálisgép-bővítményének üzembe helyezését egy nem Azure-beli Windows- és/vagy Linux-kiszolgálón. A virtuálisgép-bővítmények a következő módszerekkel kezelhetők a hibrid gépeken vagy az Arc-kompatibilis kiszolgálók által felügyelt kiszolgálókon:
        
            - A [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - Az [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager sablonok](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - A Azure Policy.
        
            Ezzel a módszerrel a Azure Policy [Deploy Log Analytics agent to Linux](../governance/policy/samples/built-in-policies.md#monitoring) or Windows Azure Arc machines (Log Analytics-ügynök telepítése Linux vagy Windows Azure Arc rendszerű gépekre) beépített szabályzatával naplózhatja, hogy az Arc-kompatibilis kiszolgálón telepítve van-e a Log Analytics-ügynök. Ha az ügynök nincs telepítve, automatikusan üzembe helyez egy szervizelési feladattal. Ha a gépeket a Azure Monitor for VMs szeretné figyelni, ehelyett az [Enable Azure Monitor for VMs](../governance/policy/samples/built-in-initiatives.md#monitoring) kezdeményezéssel telepítheti és konfigurálhatja a Log Analytics-ügynököt.

    Javasoljuk, hogy telepítse a Log Analytics-ügynököt Windows vagy Linux rendszeren a Azure Policy.

1. Annak ellenőrzése, hogy az ügynök jelent-e a munkaterületnek

    A Windowshoz való Log Analytics-ügynök a gépeket egy Azure Monitor Log Analytics-munkaterülethez csatlakoztatja. Amikor telepíti az ügynököt a gépére, és csatlakoztatja a munkaterülethez, az automatikusan letölti a hibrid runbook-feldolgozóhoz szükséges összetevőket.

    Ha az ügynök néhány perc után sikeresen csatlakozott a Log Analytics-munkaterülethez, a következő lekérdezés futtatásával ellenőrizheti, hogy szívverési adatokat küld-e a munkaterületre.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    A keresési eredmények között látnia kell a gép szívverési rekordjait, amelyek jelzik, hogy a gép csatlakoztatva van, és jelenti a szolgáltatásnak. Alapértelmezés szerint minden ügynök szívverésrekordot továbbít a hozzárendelt munkaterületre. Az ügynök telepítésének és beállításának befejezéséhez kövesse az alábbi lépéseket.

1. Erősítse meg a hibrid runbook-feldolgozó verzióját a Log Analytics-ügynököt üzemeltető gépen, keresse meg és jegyezze fel a `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` **verzió** almappát. Ez a mappa néhány perccel azután jelenik meg a számítógépen, hogy a megoldás engedélyezve van a munkaterületen.

1. Telepítse a Runbook-környezetet, és csatlakozzon Azure Automation. Amikor úgy konfigurál egy ügynököt, hogy egy  Log Analytics-munkaterületre jelentsen, és importálja az Automation-megoldást, a megoldás leküldi a `HybridRegistration` PowerShell-modult. Ez a modul a `Add-HybridRunbookWorker` parancsmagot tartalmazza. Ezzel a parancsmaggal telepítheti a Runbook-környezetet a gépen, és regisztrálhatja a Azure Automation.

    Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsokat a modul importáláshoz.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

1. Futtassa a parancsmagot a , és paraméterek `Add-HybridRunbookWorker` `Url` `Key` értékeinek `GroupName` megadásával.

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Az Automation-fiók Kulcsok lapján lekért információk a paraméterekhez és `Url` `Key` a paraméterekhez szükségesek.  Válassza a  **Lap** bal oldalán található Fiókbeállítások szakaszban található Kulcsok lehetőséget.

    ![Kulcsok kezelése lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * A `Url` paraméternél másolja ki az **URL** értékét.

    * A `Key` paraméternél másolja ki az **ELSŐDLEGES HOZZÁFÉRÉSI KULCS értékét.**

    * A `GroupName` paraméterhez használja a hibrid runbook-feldolgozó csoport nevét. Ha ez a csoport már létezik az Automation-fiókban, a rendszer hozzáadja az aktuális gépet. Ha ez a csoport nem létezik, hozzáadja.

    * Ha szükséges, állítsa be a paramétert a `Verbose` telepítés részleteinek fogadásához.

1. A parancs befejezése után ellenőrizze az üzembe helyezést. Az **Automation-fiók** Hibrid runbook-feldolgozói csoportok lapján, a Felhasználó hibrid **runbook-feldolgozók** csoport lapján megjelenik az új vagy meglévő csoport és a tagok száma. Ha ez egy meglévő csoport, a tagok száma növekszik. A csoportot kiválaszthatja az oldalon található listából, majd a bal oldali menüben válassza a **Hibrid dolgozók lehetőséget.** A Hibrid **dolgozók lapon** a csoport minden tagja megjelenik.

## <a name="install-powershell-modules"></a>PowerShell-modulok telepítése

A runbookok a saját környezetében telepített modulokban meghatározott tevékenységeket és parancsmagokat Azure Automation használni. Mivel ezeket a modulokat a rendszer nem telepíti automatikusan a helyszíni gépekre, manuálisan kell telepítenie őket. Ez alól kivétel az Azure-modul. Ez a modul alapértelmezés szerint telepítve van, és az összes Azure-szolgáltatáshoz és -tevékenységhez hozzáférést biztosít a Azure Automation.

Mivel a hibrid runbook-feldolgozó elsődleges célja a helyi erőforrások kezelése, valószínűleg telepítenie kell az ezeket az erőforrásokat támogató modulokat, különösen a `PowerShellGet` modult. A modulmodulok telepítésével kapcsolatos Windows PowerShell lásd: [Windows PowerShell.](/powershell/scripting/developer/windows-powershell)

A telepített moduloknak a környezeti változó által hivatkozott helyen kell lennie, hogy a hibrid feldolgozó automatikusan `PSModulePath` importálni tudja azokat. További információ: [Modulok telepítése a PSModulePath-ban.](/powershell/scripting/developer/module/installing-a-powershell-module)

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>A hibrid runbook-feldolgozó eltávolítása

1. A Azure Portal az Automation-fiókjához.

1. A **Fiókbeállítások alatt** válassza a **Kulcsok lehetőséget,** és jegyezze fel az **URL** és az Elsődleges hozzáférési **kulcs értékét.**

1. Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsot az URL-cím és az elsődleges hozzáférési kulcs értékeivel. Az `Verbose` eltávolítási folyamat részletes naplóját a paraméterrel használhatja. Ha el szeretné távolítani az elavult gépeket a hibrid feldolgozói csoportból, használja a választható `machineName` paramétert.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Hibridfeldolgozó-csoport eltávolítása

Hibrid runbook-feldolgozó csoport eltávolításához először el kell távolítania a hibrid runbook-feldolgozót minden olyan gépről, amely tagja a csoportnak. Ezután a következő lépésekkel távolítsa el a csoportot:

1. Nyissa meg az Automation-fiókot a Azure Portal.

1. A **Folyamatautomatizálás alatt válassza a** Hibrid **feldolgozói csoportok lehetőséget.** Válassza ki a törölni kívánt csoportot. Megjelenik a csoport Tulajdonságok lapja.

   ![Tulajdonságok lap](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. A kiválasztott csoport Tulajdonságok lapján válassza a **Törlés lehetőséget.** A művelet megerősítését egy üzenet kéri. Ha  biztos a folytatásban, válassza az Igen lehetőséget.

   ![Megerősítő üzenet](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   A folyamat több másodpercig is eltelhet. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

## <a name="next-steps"></a>Következő lépések

* A runbookok helyszíni adatközpontban vagy más felhőalapú környezetben való folyamatautomatizálására való konfigurálásával kapcsolatos további információkért lásd: Runbookok futtatása hibrid [runbook-feldolgozón.](automation-hrw-run-runbooks.md)

* A hibrid runbook-feldolgozók hibaelhárításáról lásd: [Hibrid runbook-feldolgozóval kapcsolatos problémák elhárítása.](troubleshoot/hybrid-runbook-worker.md#general)
