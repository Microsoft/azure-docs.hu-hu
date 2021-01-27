---
title: A hibrid Runbook-feldolgozói problémák elhárítása Azure Automation
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a Azure Automation Hybrid Runbook-feldolgozókkal kapcsolatos problémákat.
services: automation
ms.subservice: ''
ms.date: 11/25/2019
ms.topic: troubleshooting
ms.openlocfilehash: 214501c447632232dc00b61643ea21083bd0e4ac
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896494"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Hibrid runbook-feldolgozó hibáinak elhárítása

Ez a cikk a Azure Automation Hybrid Runbook-feldolgozókkal kapcsolatos problémák elhárításával és megoldásával kapcsolatos információkat tartalmaz. Általános információk: a [hibrid Runbook Worker áttekintése](../automation-hybrid-runbook-worker.md).

## <a name="general"></a>Általános

A hibrid Runbook-feldolgozó attól függ, hogy az ügynök kommunikál-e a Azure Automation fiókjával a feldolgozó regisztrálásához, a Runbook-feladatok fogadásához és a jelentés állapotához. Windows esetén ez az ügynök a Windows Log Analytics ügynöke. Linux esetén ez az Log Analytics-ügynök Linux rendszerhez.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Forgatókönyv: a Runbook végrehajtása sikertelen

#### <a name="issue"></a>Probléma

A Runbook végrehajtása sikertelen, és a következő hibaüzenet jelenik meg:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

A runbook röviddel azután fel van függesztve, hogy háromszor próbálkozik a végrehajtással. Vannak olyan feltételek, amelyek megszakítják a runbook befejezését. Előfordulhat, hogy a kapcsolódó hibaüzenet nem tartalmaz további információkat.

#### <a name="cause"></a>Ok

A lehetséges okok a következők:

* A runbookok nem tud hitelesíteni a helyi erőforrásokkal.
* A hibrid feldolgozó proxy vagy tűzfal mögött van.
* A hibrid Runbook-feldolgozó futtatására konfigurált számítógép nem felel meg a minimális hardverkövetelmények követelményeinek.

#### <a name="resolution"></a>Feloldás

Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a **\* . azure-automation.net** a 443-es porton.

A hibrid Runbook-feldolgozót futtató számítógépeknek meg kell felelniük a minimális hardverkövetelmények, mielőtt a feldolgozó konfigurálva lenne a szolgáltatás üzemeltetésére. A runbookok és az általuk használt háttérrendszer okozhatja, hogy a rendszer túlhasználatba kerül, és a runbook-feladatok késését vagy időtúllépését okozza.

Győződjön meg arról, hogy a hibrid Runbook Worker szolgáltatás futtatására szolgáló számítógép megfelel a minimális hardverkövetelmények követelményeinek. Ha igen, figyelje a CPU-t és a memóriát a hibrid Runbook-munkavégző folyamatok és a Windows teljesítménye közötti korreláció megállapítása érdekében. A memória vagy a CPU nyomása az erőforrások frissítésének szükségességét jelezheti. Kiválaszthat egy másik számítási erőforrást is, amely támogatja a minimális követelményeket és a méretezést, ha a munkaterhelési igények azt jelzik, hogy növelésre van szükség.

A leírást a megfelelő eseményhez tartozó **Microsoft-SMA** eseménynaplóban találja `Win32 Process Exited with code [4294967295]` . Ennek a hibának az az oka, hogy nem konfigurálta a hitelesítést a runbookok, vagy megadta a hibrid Runbook Worker csoport futtató hitelesítő adatait. Tekintse át a runbook engedélyeit a [hibrid runbook-feldolgozón futó runbookok](../automation-hrw-run-runbooks.md) , és ellenőrizze, hogy helyesen konfigurálta-e a hitelesítést a runbookok.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Forgatókönyv: 15011-es esemény a hibrid Runbook-feldolgozóban

#### <a name="issue"></a>Probléma

A hibrid Runbook-feldolgozó a 15011-as eseményt kapja, ami azt jelzi, hogy a lekérdezés eredménye érvénytelen. A következő hiba jelenik meg, amikor a feldolgozó megpróbál megnyitni egy kapcsolódást a [jelző kiszolgálóval](/aspnet/core/signalr/introduction).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Ok

A hibrid Runbook-feldolgozó nem lett megfelelően konfigurálva a szolgáltatás automatikus telepítéséhez, például Update Management. Az üzemelő példány egy olyan részt tartalmaz, amely összekapcsolja a virtuális gépet a Log Analytics munkaterülettel. A PowerShell-parancsfájl az előfizetésben található munkaterületet keresi a megadott névvel. Ebben az esetben a Log Analytics munkaterület egy másik előfizetésben található. A parancsfájl nem találja a munkaterületet, és megpróbál létrehozni egyet, de a név már használatban van. Ennek eredményeképpen a telepítés meghiúsul.

#### <a name="resolution"></a>Feloldás

A probléma megoldásához két lehetőség közül választhat:

* Módosítsa a PowerShell-parancsfájlt, hogy a Log Analytics munkaterületet egy másik előfizetésben keresse meg. Ez jó megoldás, ha a jövőben számos hibrid Runbook-feldolgozó gépet szeretne üzembe helyezni.

* Manuálisan konfigurálja a munkavégző gépet egy Orchestrator-homokozóban történő futtatásra. Ezután futtasson egy, a munkavégző Azure Automation fiókjában létrehozott runbook a funkció teszteléséhez.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Forgatókönyv: a Windows Azure virtuális gépek automatikusan el lettek dobva egy hibrid feldolgozói csoportból

#### <a name="issue"></a>Probléma

A hibrid Runbook-feldolgozó vagy virtuális gépek nem láthatók, ha a munkavégző gép hosszú ideje ki van kapcsolva.

#### <a name="cause"></a>Ok

A hibrid Runbook Worker gép nem pingelése Azure Automation 30 napnál hosszabb ideig. Ennek eredményeképpen az Automation megtisztítta a hibrid Runbook Worker csoportot vagy a rendszer munkavégző csoportját. 

#### <a name="resolution"></a>Feloldás

Indítsa el a munkavégző gépet, majd rereregister Azure Automation. A runbook-környezet telepítésével és a Azure Automationhoz való kapcsolódással kapcsolatos utasításokért lásd: [Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md)telepítése.

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Forgatókönyv: a hibrid Runbook Worker-feldolgozó tanúsítványtárolójában nem található tanúsítvány.

#### <a name="issue"></a>Probléma

A hibrid Runbook-feldolgozón futó runbook a következő hibaüzenettel meghiúsul:

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a futtató [fiókot](../manage-runas-account.md) egy olyan runbook próbálja meg használni, amely olyan hibrid runbook-feldolgozón fut, amelyen a futtató fiók tanúsítványa nincs jelen. A hibrid Runbook-feldolgozók alapértelmezés szerint nem helyileg rendelkeznek a tanúsítvány eszközzel. A futtató fiók működéséhez az szükséges, hogy az eszköz megfelelően működjön.

#### <a name="resolution"></a>Feloldás

Ha a hibrid Runbook-feldolgozó egy Azure-beli virtuális gép, a [Runbook-hitelesítést felügyelt identitásokkal](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) is használhatja. Ez a forgatókönyv leegyszerűsíti a hitelesítést azáltal, hogy lehetővé teszi az Azure-erőforrások hitelesítését az Azure-beli virtuális gép felügyelt identitásával a futtató fiók helyett. Ha a hibrid Runbook Worker egy helyszíni gép, telepítenie kell a futtató fiók tanúsítványát a gépre. A tanúsítvány telepítésének megismeréséhez tekintse meg a következő témakört: a PowerShell runbook **exportálása – RunAsCertificateToHybridWorker** futtatása a runbookok-ben [hibrid Runbook-feldolgozón](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Forgatókönyv: 403-es hiba a hibrid Runbook-feldolgozó regisztrálása során

#### <a name="issue"></a>Probléma

A feldolgozó kezdeti regisztrációs fázisa meghiúsul, és a következő hibaüzenetet kapja (403):

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Ok

A következő problémák lehetséges okai:

* Az ügynök beállításaiban nem szerepel a munkaterület-azonosító vagy a munkaterület kulcsa (elsődleges). 
* A hibrid Runbook Worker nem tudja letölteni a konfigurációt, ami miatt a fiók összekapcsolási hibát okoz. Ha az Azure lehetővé teszi a gépek funkcióinak használatát, csak bizonyos régiókat támogat, amelyekkel összekapcsolhat egy Log Analytics munkaterületet és egy Automation-fiókot. Az is lehetséges, hogy helytelen dátum vagy idő van beállítva a számítógépen. Ha az idő +/-15 perc az aktuális időponttól, a szolgáltatások telepítése sikertelen lesz.

#### <a name="resolution"></a>Feloldás

##### <a name="mistyped-workspace-id-or-key"></a>Nem típusos munkaterület-azonosító vagy-kulcs
Annak ellenőrzéséhez, hogy az ügynök munkaterület-azonosítója vagy a munkaterület kulcsa nem lett-e megadva, tekintse meg a következőt: [munkaterület hozzáadása vagy eltávolítása](../../azure-monitor/platform/agent-manage.md#windows-agent) a Windows-ügynökhöz, illetve [munkaterület hozzáadása vagy eltávolítása](../../azure-monitor/platform/agent-manage.md#linux-agent) a Linux-ügynökhöz. Győződjön meg arról, hogy a Azure Portal teljes sztringet választ, majd másolja és illessze be alaposan.

##### <a name="configuration-not-downloaded"></a>A konfiguráció nincs letöltve

A Log Analytics munkaterület és az Automation-fióknak egy összekapcsolt régióban kell lennie. A támogatott régiók listáját itt tekintheti meg: [Azure Automation és log Analytics munkaterület-hozzárendelések](../how-to/region-mappings.md).

Előfordulhat, hogy frissítenie kell a számítógép dátum-vagy időzónáját is. Ha egyéni időtartományt választ, győződjön meg arról, hogy a tartomány UTC-ben van, ami eltérhet a helyi időzónától.

## <a name="linux"></a>Linux

A Linux Hybrid Runbook Worker a [linuxos log Analytics-ügynöktől](../../azure-monitor/platform/log-analytics-agent.md) függ, hogy kommunikálni fog-e az Automation-fiókjával a feldolgozó, a Runbook-feladatok fogadása és a jelentés állapotának regisztrálásához. Ha a feldolgozó regisztrálása meghiúsul, a hiba lehetséges okait itt találja.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Forgatókönyv: a Linux Hybrid Runbook Worker a Runbook aláírásakor kéri a jelszót

#### <a name="issue"></a>Probléma

A `sudo` Linux Hybrid Runbook Worker parancs futtatása nem várt jelszót kér le.

#### <a name="cause"></a>Ok

A Linux rendszerhez készült Log Analytics-ügynök **nxautomationuser** -fiókja helytelenül van konfigurálva a **sudoers** fájlban. A hibrid Runbook-feldolgozónak szüksége van a fiók engedélyeinek és egyéb adatmennyiségének megfelelő konfigurálására, hogy a runbookok a Linux Runbook-feldolgozó számára is aláírható legyen.

#### <a name="resolution"></a>Feloldás

* Győződjön meg arról, hogy a hibrid Runbook Worker rendelkezik a GnuPG (GPG) végrehajtható fájllal a gépen.

* Ellenőrizze a **nxautomationuser** -fiók konfigurációját a **sudoers** fájlban. Lásd: [Runbookok futtatása hibrid Runbook-feldolgozón](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Forgatókönyv: Log Analytics Linux-ügynök nem fut

#### <a name="issue"></a>Probléma

A Linux Log Analytics ügynöke nem fut.

#### <a name="cause"></a>Ok

Ha az ügynök nem fut, megakadályozza, hogy a Linux Hybrid Runbook Worker kommunikáljon Azure Automationokkal. Lehetséges, hogy az ügynök különböző okokból nem fut.

#### <a name="resolution"></a>Feloldás

 Ellenőrizze, hogy fut-e az ügynök a parancs beírásával `ps -ef | grep python` . Az alábbihoz hasonló kimenetnek kell megjelennie. A Python folyamatai a **nxautomation** felhasználói fiókkal. Ha a Azure Automation funkció nincs engedélyezve, a következő folyamatok egyike sem fut.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A következő lista azokat a folyamatokat mutatja be, amelyek a Linux hibrid Runbook-feldolgozók számára lettek elindítva. Ezek mind a/var/opt/Microsoft/omsagent/State/automationworker/könyvtárban találhatók.

* **OMS. conf**: a Worker Manager folyamata. Közvetlenül a DSC-ből indult el.
* **Worker. conf**: az automatikusan regisztrált hibrid feldolgozói folyamat. A Worker Manager indította el. Ezt a folyamatot a Update Management használja, és átlátható a felhasználó számára. Ez a folyamat nem jelenik meg, ha a Update Management nincs engedélyezve a számítógépen.
* **DIY/Worker. conf**: a DIY hibrid feldolgozói folyamat. A DIY hibrid feldolgozói folyamat a hibrid Runbook-feldolgozón futtatott felhasználói runbookok végrehajtására szolgál. Ez csak az automatikusan regisztrált hibrid munkavégző folyamattól tér el a legfontosabb részletességgel, amelyet más konfigurációt használ. Ez a folyamat nem jelenik meg, ha Azure Automation le van tiltva, és a DIY Linux Hybrid Worker nincs regisztrálva.

Ha az ügynök nem fut, futtassa a következő parancsot a szolgáltatás elindításához: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Forgatókönyv: a megadott osztály nem létezik

Ha a/var/opt/Microsoft/omsconfig/omsconfig.log hibaüzenet jelenik meg `The specified class does not exist..` , akkor a Linux log Analytics-ügynökének frissítése szükséges. A következő parancs futtatásával telepítse újra az ügynököt.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

A Windows Hybrid Runbook Worker a [windows log Analytics ügynöktől](../../azure-monitor/platform/log-analytics-agent.md) függ, hogy kommunikálni tudjon az Automation-fiókjával a feldolgozó, a Runbook-feladatok fogadása és a jelentés állapotának regisztrálásához. Ha a feldolgozó regisztrálása meghiúsul, ez a szakasz néhány lehetséges okot is tartalmaz.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Forgatókönyv: a Windows Log Analytics ügynöke nem fut

#### <a name="issue"></a>Probléma

A `healthservice` nem fut a hibrid Runbook Worker gépen.

#### <a name="cause"></a>Ok

Ha a Windows-szolgáltatás Log Analytics nem fut, a hibrid Runbook-feldolgozó nem tud kommunikálni a Azure Automationval.

#### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy az ügynök fut, ehhez írja be a következő parancsot a PowerShellben: `Get-Service healthservice` . Ha a szolgáltatás leáll, írja be a következő parancsot a PowerShellben a szolgáltatás elindításához: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Forgatókönyv: 4502-es esemény a Operations Manager naplóban

#### <a name="issue"></a>Probléma

Az **Application and Services Logs\Operations-kezelő** eseménynaplójában a 4502-as és `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` a következő leírást tartalmazó üzenet jelenik meg:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Ok

Ezt a problémát az okozhatja, hogy a proxy vagy a hálózati tűzfal blokkolja a Microsoft Azure felé irányuló kommunikációt. Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a **\* . azure-automation.net** a 443-es porton.

#### <a name="resolution"></a>Feloldás

A naplók tárolása helyileg történik minden hibrid feldolgozón a következő helyen: C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Ellenőrizheti, hogy van-e figyelmeztetési vagy hibaüzenet az **alkalmazás-és Logs\Microsoft-SMA\Operations** , valamint az **alkalmazás és a szolgáltatások Logs\Operations-kezelő** eseménynaplójában. Ezek a naplók olyan kapcsolatot vagy más típusú problémát jeleznek, amely hatással van a szerepkör Azure Automationre való engedélyezésére vagy a normál műveletek során felmerülő problémákra. Ha további segítségre van a Log Analytics ügynökkel kapcsolatos problémák elhárításához, olvassa el [a log Analytics Windows-ügynökkel kapcsolatos problémák elhárítása](../../azure-monitor/platform/agent-windows-troubleshoot.md)című témakört.

A hibrid feldolgozók a [Runbook kimenetét és üzeneteit](../automation-runbook-output-and-messages.md) ugyanúgy küldik el Azure Automationnak, mint a felhőben futó Runbook-feladatok kimenetének és üzeneteinek küldése. A részletes és a folyamat-adatfolyamokat ugyanúgy engedélyezheti, mint a runbookok.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Forgatókönyv: a Orchestrator.Sandbox.exe nem tud kapcsolódni a proxyn keresztüli Microsoft 365hoz

#### <a name="issue"></a>Probléma

Egy Windows hibrid Runbook-feldolgozón futó parancsfájl nem tud a várt módon csatlakozni a Orchestrator-homokozóban Microsoft 365. A szkript a [MsolService](/powershell/module/msonline/connect-msolservice) kapcsolatot használja a kapcsolathoz. 

Ha úgy módosítja **Orchestrator.Sandbox.exe.config** a proxy és a mellőzési lista beállításához, a sandbox továbbra sem csatlakozik megfelelően. Úgy tűnik, hogy a várt módon működik egy **Powershell_ise.exe.config** -fájl, amely azonos proxy-és megkerülési lista-beállításokkal rendelkezik. A Service Management Automation (SMA) naplók és a PowerShell-naplók nem biztosítanak semmilyen információt a proxyval kapcsolatban.

#### <a name="cause"></a>Ok

A-kiszolgálón lévő Active Directory összevonási szolgáltatások (AD FS) (AD FS) kapcsolat nem kerülheti el a proxyt. Ne feledje, hogy egy PowerShell-homokozó a naplózott felhasználóként fut. Egy Orchestrator-homokozó azonban nagy mértékben testre szabott, és figyelmen kívül hagyhatja a **Orchestrator.Sandbox.exe.config** fájl beállításait. Speciális kóddal rendelkezik a gép vagy Log Analytics ügynök proxy-beállításainak kezelésére, de más egyéni proxybeállítások kezelésére nem. 

#### <a name="resolution"></a>Feloldás

A Orchestrator-sandbox problémájának megoldásához áttelepítheti a parancsfájlt, hogy a MSOnline modul helyett a PowerShell-parancsmagokhoz használja a Azure Active Directory modulokat. További információ: [áttelepítés Orchestrator-ről Azure Automationra (bétaverzió)](../automation-orchestrator-migration.md).

Ha továbbra is a MSOnline modul-parancsmagokat szeretné használni, módosítsa a parancsfájlt a [hívási parancs](/powershell/module/microsoft.powershell.core/invoke-command)használatára. Értékek megadása a `ComputerName` és `Credential` paraméterek számára. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

A kód módosítása egy teljesen új PowerShell-munkamenetet indít el a megadott hitelesítő adatok kontextusában. Engedélyeznie kell a forgalmat egy olyan proxykiszolgálón keresztül, amely az aktív felhasználót hitelesíti.

>[!NOTE]
>Ez a megoldás szükségtelenné teszi a sandbox konfigurációs fájljának kezelését. Még akkor is, ha a konfigurációs fájlnak a szkripttel való használata sikeres volt, a fájl minden alkalommal törlődik a hibrid Runbook Worker Agent frissítésekor.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Forgatókönyv: a hibrid Runbook Worker nem jelent jelentést

#### <a name="issue"></a>Probléma

A hibrid Runbook Worker gépe fut, de a munkaterületen nem jelenik meg szívverési információ.

A következő példában szereplő lekérdezés a munkaterületen található gépeket és az utolsó szívverését jeleníti meg:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Ok

Ezt a problémát a hibrid Runbook-feldolgozó sérült gyorsítótára okozhatja.

#### <a name="resolution"></a>Feloldás

A probléma megoldásához jelentkezzen be a hibrid Runbook-feldolgozóba, és futtassa az alábbi szkriptet. Ez a parancsfájl leállítja a Windows Log Analytics ügynökét, eltávolítja a gyorsítótárat, és újraindítja a szolgáltatást. Ez a művelet kényszeríti a hibrid Runbook-feldolgozót, hogy újra letöltse a konfigurációját Azure Automationról.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Forgatókönyv: nem adható hozzá Windows Hybrid Runbook Worker

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, amikor egy hibrid Runbook-feldolgozót próbál hozzáadni a `Add-HybridRunbookWorker` parancsmag használatával:

```error
Machine is already registered
```

#### <a name="cause"></a>Ok

Ezt a problémát akkor lehet okozni, ha a gép már regisztrálva van egy másik Automation-fiókkal, vagy ha megpróbálta újból hozzáadni a hibrid Runbook-feldolgozót a gépről való eltávolítása után.

#### <a name="resolution"></a>Feloldás

A probléma megoldásához távolítsa el a következő beállításkulcsot, indítsa újra `HealthService` , majd próbálja megismételni a `Add-HybridRunbookWorker` parancsmagot.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Forgatókönyv: nem adhat hozzá linuxos hibrid Runbook-feldolgozót

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, ha hibrid Runbook-feldolgozót próbál hozzáadni a `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python-parancsfájl használatával:

```error
Unable to register, an existing worker was found. Please deregister any existing worker and try again.
```

Emellett kísérlet történt a hibrid Runbook-feldolgozók deregisztrálására a `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` Python-szkript használatával:

```error
Failed to deregister worker. [response_status=404]
```

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a gép már regisztrálva van egy másik Automation-fiókkal, ha az Azure Hybrid Worker-csoport törölve lett, vagy ha megpróbálta újból hozzáadni a hibrid Runbook-feldolgozót a számítógépről való eltávolítása után.

#### <a name="resolution"></a>Feloldás

A probléma megoldása:

1. Távolítsa el az ügynököt `sudo sh onboard_agent.sh --purge` .

1. Futtassa a következő parancsokat:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Készítse elő újra az ügynököt `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` .

1. Várjon, amíg a mappa `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` fel nem töltődik.

1. Próbálja megismételni a `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python-szkriptet.

## <a name="next-steps"></a>Következő lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport) a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget.
