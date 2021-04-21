---
title: Hibrid runbook Azure Automation feldolgozóval kapcsolatos problémák elhárítása
description: Ez a cikk azt mutatja be, hogyan háríthatja el és háríthatja el a hibrid runbook-Azure Automation felmerülő problémákat.
services: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 906e38b28015bd70cf1c97ba9323094d64a12c94
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830843"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Hibrid runbook-feldolgozó hibáinak elhárítása

Ez a cikk a hibrid runbook-Azure Automation hibaelhárításával és megoldásával kapcsolatos információkat tartalmaz. Általános információkért lásd: [Hibrid runbook-feldolgozó áttekintése.](../automation-hybrid-runbook-worker.md)

## <a name="general"></a>Általános kérdések

A hibrid runbook-feldolgozó egy ügynöktől függ, hogy kommunikáljon a Azure Automation-fiókkal a feldolgozó regisztrálása, a runbook-feladatok fogadása és az állapot jelentése érdekében. Windows esetén ez az ügynök a Windowshoz való Log Analytics-ügynök. Linuxon ez a Linuxhoz használható Log Analytics-ügynök.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Forgatókönyv: A runbook végrehajtása sikertelen

#### <a name="issue"></a>Probléma

A runbook végrehajtása sikertelen, és a következő hibaüzenet jelenik meg:

`The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times.`

A runbook röviddel azután fel lesz függesztve, hogy háromszor próbálkozik a végrehajtásával. Vannak olyan feltételek, amelyek megszakítják a runbook futását. Előfordulhat, hogy a kapcsolódó hibaüzenet nem tartalmaz további információkat.

#### <a name="cause"></a>Ok

A következő lehetséges okok lehetnek:

* A runbookok nem hitelesíthetők a helyi erőforrásokkal.
* A hibrid feldolgozó proxy vagy tűzfal mögött van.
* A hibrid runbook-feldolgozó futtatására konfigurált számítógép nem felel meg a minimális hardverkövetelményeknek.

#### <a name="resolution"></a>Feloldás

Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a **\* .azure-automation.net** a 443-as porton.

A hibrid runbook-feldolgozót futtató számítógépeknek meg kell felelnie a minimális hardverkövetelményeknek, mielőtt a feldolgozót a szolgáltatás futtatására konfigurálják. Előfordulhat, hogy a runbookok és az alkalmazás háttérfolyamata túlhasználja a rendszert, ami a Runbook-feladat késését vagy időtúllépését okozhatja.

Ellenőrizze, hogy a hibrid runbook-feldolgozó szolgáltatást futtató számítógép megfelel-e a minimális hardverkövetelményeknek. Ha igen, monitorja a processzor- és memóriahasználatot a hibrid runbook-feldolgozó folyamatok és a Windows teljesítménye közötti korreláció meghatározásához. A memória- vagy CPU-terhelés azt jelezheti, hogy frissíteni kell az erőforrásokat. Másik számítási erőforrást is kiválaszthat, amely támogatja a minimális követelményeket, és skálázhat, ha a számítási feladatok igényei azt jelzik, hogy növekedésre van szükség.

Ellenőrizze a **Microsoft-SMA** eseménynaplójában, hogy van-e a következő leírású kapcsolódó esemény: `Win32 Process Exited with code [4294967295]` . Ennek a hibának az az oka, hogy nem konfigurálta a hitelesítést a runbookban, vagy nem adott meg a hibrid runbook-feldolgozó csoporthoz a futtatás mint hitelesítő adatait. Tekintse át a [Runbookok](../automation-hrw-run-runbooks.md) futtatása hibrid runbook-feldolgozón dokumentum Runbook-engedélyeket, és ellenőrizze, hogy megfelelően konfigurálta-e a runbookok hitelesítését.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Forgatókönyv: 15011-es esemény a hibrid runbook-feldolgozóban

#### <a name="issue"></a>Probléma

A hibrid runbook-feldolgozó megkapja az 15011-es eseményt, amely azt jelzi, hogy a lekérdezés eredménye érvénytelen. A következő hiba akkor jelenik meg, amikor a feldolgozó megpróbál kapcsolatot nyitni a [SignalR-kiszolgálóval.](/aspnet/core/signalr/introduction)

`[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
`

#### <a name="cause"></a>Ok

A hibrid runbook-feldolgozó nincs megfelelően konfigurálva az automatikus szolgáltatástelepítéshez, például a Update Management. Az üzembe helyezés tartalmaz egy részt, amely összeköti a virtuális gépet a Log Analytics-munkaterülettel. A PowerShell-szkript a megadott nevű munkaterületet keres az előfizetésben. Ebben az esetben a Log Analytics-munkaterület egy másik előfizetésben található. A szkript nem találja a munkaterületet, és megpróbál létrehozni egyet, de a név már foglalt. Ennek eredményeképpen az üzembe helyezés meghiúsul.

#### <a name="resolution"></a>Feloldás

A probléma megoldásához két lehetőség áll rendelkezésre:

* Módosítsa a PowerShell-szkriptet úgy, hogy a Log Analytics-munkaterületet egy másik előfizetésben keresse. Ez egy jó megoldás, ha sok hibrid runbook-feldolgozó gépet tervez üzembe helyezni a jövőben.

* Manuálisan konfigurálja a feldolgozó gépet az Orchestrator-védőfalon való futtatáshoz. Ezután futtatassa a Azure Automation-fiókban létrehozott runbookot a feldolgozón a működés teszteléséhez.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Forgatókönyv: A Windows Azure virtuális gépek automatikusan el vannak dobva egy hibrid feldolgozócsoportból

#### <a name="issue"></a>Probléma

Nem látja a hibrid runbook-feldolgozót vagy a virtuális gépeket, ha a feldolgozó gép hosszú ideje ki van kapcsolva.

#### <a name="cause"></a>Ok

A hibrid runbook-feldolgozó gép több mint 30 napja Azure Automation pingelni a virtuális gépet. Ennek eredményeképpen az Automation kiüríti a hibrid runbook-feldolgozó csoportot vagy a rendszer-munkavégző csoportot. 

#### <a name="resolution"></a>Feloldás

Indítsa el a feldolgozó gépet, majd regisztrálja újra a Azure Automation. A Runbook-környezet telepítésével és az alkalmazáshoz való csatlakozással Azure Automation windowsos hibrid [runbook-feldolgozó](../automation-windows-hrw-install.md)üzembe helyezése.

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Forgatókönyv: Nem található tanúsítvány a hibrid runbook-feldolgozó tanúsítványtárolójában

#### <a name="issue"></a>Probléma

A hibrid runbook-feldolgozón futó runbook a következő hibaüzenettel meghiúsul:

`Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000`  
`At line:3 char:1`  
`+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...`  
`+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
`    + CategoryInfo          : CloseError: (:) [Connect-AzAccount],ArgumentException`  
`    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand`

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul [](../automation-security-overview.md#run-as-accounts) elő, ha olyan hibrid runbook-feldolgozón futó runbookban próbál meg használni egy runbookot, amelyben nincs jelen a runbook-fiók tanúsítványa. A hibrid runbook-dolgozók alapértelmezés szerint nem helyileg futtatják a tanúsítványeszközt. A futási fiók használatához az eszköz megfelelő működésére van szükség.

#### <a name="resolution"></a>Feloldás

Ha a hibrid runbook-feldolgozó egy Azure-beli virtuális gép, ehelyett használhat [runbook-hitelesítést felügyelt identitásokkal.](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) Ez a forgatókönyv leegyszerűsíti a hitelesítést, mivel lehetővé teszi az Azure-erőforrásokon való hitelesítést az Azure-beli virtuális gép felügyelt identitásával a futtatott fiók helyett. Ha a hibrid runbook-feldolgozó helyszíni gép, telepítenie kell a futtatott fiók tanúsítványát a gépen. A tanúsítvány telepítésének elsajátítása érdekében tekintse meg az **Export-RunAsCertificateToHybridWorker PowerShell-runbook** futtatásának lépéseit [a Runbooks on a Hybrid Runbook Worker (Runbookok futtatása hibrid runbook-feldolgozón)](../automation-hrw-run-runbooks.md)oldalon.

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Forgatókönyv: 403-as hiba egy hibrid runbook-feldolgozó regisztrációja során

#### <a name="issue"></a>Probléma

A feldolgozó kezdeti regisztrációs fázisa sikertelen, és a következő hibaüzenetet kapja (403):

`Forbidden: You don't have permission to access / on this server.`

#### <a name="cause"></a>Ok

A következő problémák lehetséges okai lehetnek:

* Az ügynök beállításai között található egy tagolt munkaterület-azonosító vagy munkaterületkulcs (elsődleges). 
* A hibrid runbook-feldolgozó nem tudja letölteni a konfigurációt, ami fiókkapcsolati hibát okoz. Ha az Azure engedélyezi a funkciókat a gépeken, csak bizonyos régiókat támogat a Log Analytics-munkaterületek és az Automation-fiókok összekapcsolása érdekében. Az is lehetséges, hogy helytelen dátum vagy idő van beállítva a számítógépen. Ha az idő +/- 15 perccel az aktuális időponttól kezdődik, a szolgáltatás üzembe helyezése sikertelen lesz.

#### <a name="resolution"></a>Feloldás

##### <a name="mistyped-workspace-id-or-key"></a>Tagolt munkaterület azonosítója vagy kulcsa
Annak ellenőrzéséhez, hogy az ügynök munkaterület-azonosítója vagy munkaterületkulcsa le lett-e cserélve, tekintse meg a Munkaterület hozzáadása vagy eltávolítása [– Windows-ügynök](../../azure-monitor/platform/agent-manage.md#windows-agent) a Windows-ügynökhöz vagy Munkaterület hozzáadása vagy eltávolítása [– Linux-ügynök](../../azure-monitor/platform/agent-manage.md#linux-agent) a Linux-ügynökhöz. Ügyeljen arra, hogy a teljes sztringet válassza ki a Azure Portal, majd másolja és illessze be gondosan.

##### <a name="configuration-not-downloaded"></a>A konfiguráció nincs letöltve

A Log Analytics-munkaterületnek és az Automation-fióknak egy csatolt régióban kell lennie. A támogatott régiók listájáért tekintse meg a Azure Automation [Log Analytics-munkaterület-leképezéseket.](../how-to/region-mappings.md)

Előfordulhat, hogy frissítenie kell a számítógép dátumát vagy időzónát is. Ha egyéni időtartományt választ, győződjön meg arról, hogy a tartomány UTC időzónában van, ami eltérhet a helyi időzónában találhatótól.

### <a name="scenario-set-azstorageblobcontent-fails-on-a-hybrid-runbook-worker"></a><a name="set-azstorageblobcontent-execution-fails"></a>Forgatókönyv: Set-AzStorageBlobContent hibrid runbook-feldolgozó futtatása meghiúsul 

#### <a name="issue"></a>Probléma

A runbook sikertelen, amikor megkísérli végrehajtani a `Set-AzStorageBlobContent` futtatását, és a következő hibaüzenet jelenik meg:

`Set-AzStorageBlobContent : Failed to open file xxxxxxxxxxxxxxxx: Illegal characters in path`

#### <a name="cause"></a>Ok

 Ezt a hibát a hívások hosszú fájlnév-viselkedése okozza, amely UNC elérési `[System.IO.Path]::GetFullPath()` utakat ad hozzá.

#### <a name="resolution"></a>Feloldás

Áthidaló megoldásként létrehozhat egy nevű konfigurációs fájlt a `OrchestratorSandbox.exe.config` következő tartalommal:

```azurecli
<configuration>
  <runtime>
    <AppContextSwitchOverrides value="Switch.System.IO.UseLegacyPathHandling=false" />
  </runtime>
</configuration>
```

A fájlt a végrehajtható fájllal azonos mappába `OrchestratorSandbox.exe` helyezze. Példa:

`%ProgramFiles%\Microsoft Monitoring Agent\Agent\AzureAutomation\7.3.702.0\HybridAgent`

>[!Note]
> Ha frissíti az ügynököt, ez a konfigurációs fájl törlődik, és újra létre kell hozni.

## <a name="linux"></a>Linux

A linuxos hibrid runbook-feldolgozó a [Linuxhoz](../../azure-monitor/agents/log-analytics-agent.md) használt Log Analytics-ügynöktől függ, hogy kommunikáljon az Automation-fiókkal a feldolgozó regisztrálása, a runbook-feladatok fogadása és az állapot jelentése érdekében. Ha a feldolgozó regisztrációja sikertelen, íme a hiba néhány lehetséges oka.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Forgatókönyv: A Linux rendszerű hibrid runbook-feldolgozó jelszót kér runbook aláírásakor

#### <a name="issue"></a>Probléma

A Linux `sudo` rendszerű hibrid runbook-feldolgozó parancsának futtatása váratlan jelszókérést kér le.

#### <a name="cause"></a>Ok

A Linuxhoz használt Log Analytics-ügynök **nxautomationuser** fiókja nem megfelelően van konfigurálva a **sudoers fájlban.** A hibrid runbook-feldolgozónak a fiókengedélyek és egyéb adatok megfelelő konfigurációjára van szüksége, hogy forgatókönyveket írtassanak alá a Linux runbook-feldolgozón.

#### <a name="resolution"></a>Feloldás

* Győződjön meg arról, hogy a hibrid runbook-feldolgozó rendelkezik a GnuPG (GPG) végrehajtható fájllal a gépen.

* Ellenőrizze az **nxautomationuser** fiók konfigurációját a **sudoers fájlban.** Lásd: [Runbookok futtatása hibrid runbook-feldolgozón.](../automation-hrw-run-runbooks.md)

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Forgatókönyv: A Linuxhoz használható Log Analytics-ügynök nem fut

#### <a name="issue"></a>Probléma

A Linuxhoz használható Log Analytics-ügynök nem fut.

#### <a name="cause"></a>Ok

Ha az ügynök nem fut, megakadályozza, hogy a Linux rendszerű hibrid runbook-feldolgozó kommunikál Azure Automation. Előfordulhat, hogy az ügynök különböző okokból nem fut.

#### <a name="resolution"></a>Feloldás

 A paranccsal ellenőrizze, hogy fut-e az `ps -ef | grep python` ügynök. Az alábbihoz hasonló kimenetnek kell megjelennie. A Python az **nxautomation felhasználói fiókkal** dolgozza fel a folyamatokat. Ha a Azure Automation funkció nincs engedélyezve, az alábbi folyamatok egyike sem fut.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Az alábbi lista a Linux rendszerű hibrid runbook-feldolgozóval elindított folyamatokat sorolja fel. Ezek mind a /var/opt/microsoft/omsagent/state/automationworker/ könyvtárban találhatók.

* **oms.conf:** A feldolgozókezelő folyamata. Közvetlenül a DSC-ről indított.
* **worker.conf:** Az automatikusan regisztrált hibrid feldolgozói folyamat. Ezt a feldolgozókezelő indította el. Ezt a folyamatot a Update Management, és a felhasználó számára átlátható. Ez a folyamat nem található meg, Update Management nincs engedélyezve a számítógépen.
* **diy/worker.conf:** A DIY hibrid feldolgozói folyamat. A DIY hibrid feldolgozó folyamat felhasználói runbookok végrehajtására használható a hibrid runbook-feldolgozón. Ez csak abban tér el az automatikusan regisztrált hibrid feldolgozó folyamattól, hogy más konfigurációt használ. Ez a folyamat nem működik, ha Azure Automation le van tiltva, és a diY Linux hibrid feldolgozó nincs regisztrálva.

Ha az ügynök nem fut, futtassa a következő parancsot a szolgáltatás futtatásához: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Forgatókönyv: A megadott osztály nem létezik

Ha a `The specified class does not exist..` **/var/opt/microsoft/omsconfig/omsconfig.log** fájlban hibaüzenet jelenik meg, frissíteni kell a Linuxhoz használható Log Analytics-ügynököt. Futtassa a következő parancsot az ügynök újratelepítéséhez.

```Bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

A windowsos hibrid runbook-feldolgozó a [Windows Log Analytics-ügynökétől](../../azure-monitor/agents/log-analytics-agent.md) függ, hogy kommunikáljon az Automation-fiókkal a feldolgozó regisztrálásához, a runbook-feladatok fogadásához és az állapot jelentéséhez. Ha a feldolgozó regisztrációja sikertelen, ez a szakasz néhány lehetséges okot is tartalmaz.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Forgatókönyv: A Windowshoz használható Log Analytics-ügynök nem fut

#### <a name="issue"></a>Probléma

A `healthservice` nem fut a hibrid runbook-feldolgozó gépen.

#### <a name="cause"></a>Ok

Ha a Log Analytics for Windows szolgáltatás nem fut, a hibrid runbook-feldolgozó nem tud kommunikálni a Azure Automation.

#### <a name="resolution"></a>Feloldás

Az ügynök futásának ellenőrzéséhez írja be a következő parancsot a PowerShellben: `Get-Service healthservice` . Ha a szolgáltatás le van állítva, írja be a következő parancsot a PowerShellben a szolgáltatás indításhoz: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Forgatókönyv: 4502-es esemény a Operations Manager naplóban

#### <a name="issue"></a>Probléma

Az **Alkalmazás- és szolgáltatásnaplók\Operations Manager** eseménynaplóban megjelenik a 4502-es esemény és egy eseményüzenet, amely a következő `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` leírást tartalmazza:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Ok

Ezt a problémát az okozhatja, hogy a proxy vagy a hálózati tűzfal blokkolja a Microsoft Azure. Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a **\* .azure-automation.net** a 443-as porton.

#### <a name="resolution"></a>Feloldás

A naplók tárolása helyben történik minden hibrid feldolgozón a C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes mappában. Az Alkalmazás- és **szolgáltatásnaplók\Microsoft-SMA\Operations** és alkalmazás- és szolgáltatásnaplók\alkalmazás- és **szolgáltatásnaplók\Operations Manager** eseményeket. Ezek a naplók olyan kapcsolatot vagy más típusú problémát jeleznek, amely hatással van a szerepkör Azure Automation vagy a normál működés során tapasztalt problémára. További segítség a Log Analytics-ügynökkel kapcsolatos hibák elhárításához: [A Log Analytics Windows-ügynökkel kapcsolatos problémák elhárítása.](../../azure-monitor/agents/agent-windows-troubleshoot.md)

A hibrid dolgozók ugyanúgy küldik el a [Runbook](../automation-runbook-output-and-messages.md) kimenetét és üzeneteit Azure Automation, mint a felhőben futó runbook-feladatok. A részletes és a folyamatfolyamokat a runbookok számára is engedélyezheti.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Forgatókönyv: Orchestrator.Sandbox.exe nem lehet proxyn keresztül Microsoft 365 csatlakozni a Microsoft 365

#### <a name="issue"></a>Probléma

A Windows rendszerű hibrid runbook-feldolgozón futó szkriptek nem tudnak a várt módon csatlakozni Microsoft 365 Orchestrator-Microsoft 365 való csatlakozáshoz. A szkript [Connect-MsolService-t használ a](/powershell/module/msonline/connect-msolservice) kapcsolathoz. 

Ha úgy állítja **Orchestrator.Sandbox.exe.config** proxyt és az áthidaló listát, hogy a védőfal továbbra sem tud megfelelően csatlakozni. Úgy **Powershell_ise.exe.config,** hogy egy azonos proxy- és megkerülőlista-beállításokkal egy új fájl a vártnak megfelelő. Service Management Automation (SMA) és a PowerShell-naplók nem nyújtanak semmilyen információt a proxyval kapcsolatban.

#### <a name="cause"></a>Ok

A kiszolgálón Active Directory összevonási szolgáltatások (AD FS) kapcsolat (AD FS) nem tudja megkerülni a proxyt. Ne feledje, hogy a PowerShell-védőfal naplózott felhasználóként fut. Az Orchestrator-védőfal azonban nagymértékben testre van szabva, és figyelmen kívül **hagyhatjaOrchestrator.Sandbox.exe.config** fájlbeállításokat. Speciális kóddal rendelkezik a gép- vagy Log Analytics-ügynök proxybeállításának kezeléséhez, de más egyéni proxybeállítások kezeléséhez nem. 

#### <a name="resolution"></a>Feloldás

Az Orchestrator-védőfallal kapcsolatos problémát úgy oldhatja meg, hogy minkét szkriptet a Azure Active Directory-modulokat használja a PowerShell-parancsmagok MSOnline modulja helyett. További információ: [Áttelepítés az Orchestratorból Azure Automation (bétaverzió) verzióba.](../automation-orchestrator-migration.md)

Ha továbbra is használni szeretné az MSOnline-modul parancsmagokat, módosítsa a szkriptet úgy, hogy az [Invoke-Command parancsot használja.](/powershell/module/microsoft.powershell.core/invoke-command) Adja meg a és paraméterek `ComputerName` `Credential` értékeit. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Ez a kódváltás elindít egy teljesen új PowerShell-munkamenetet a megadott hitelesítő adatok kontextusában. Lehetővé kell tennie a forgalom áramlását egy proxykiszolgálón, amely hitelesíti az aktív felhasználót.

>[!NOTE]
>Ez a megoldás szükségtelenné teszi a védőfal konfigurációs fájljának manipulálása. Még ha a konfigurációs fájlnak sikerül is működnie a szkript használatával, a rendszer minden alkalommal törli a fájlt, amikor frissül a hibrid runbook-feldolgozó ügynök.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Forgatókönyv: A hibrid runbook-feldolgozó nem jelent

#### <a name="issue"></a>Probléma

A hibrid runbook-feldolgozó gép fut, de nem látja a gép szívverési adatait a munkaterületen.

Az alábbi példalekérdezés a munkaterületen lévő gépeket és azok utolsó szívverését mutatja be:

```kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Ok

Ezt a problémát a hibrid runbook-feldolgozó sérült gyorsítótára okozhatja.

#### <a name="resolution"></a>Feloldás

A probléma megoldásához jelentkezzen be a hibrid runbook-feldolgozóba, és futtassa a következő szkriptet. Ez a szkript leállítja a WindowsHoz való Log Analytics-ügynököt, eltávolítja a gyorsítótárat, és újraindítja a szolgáltatást. Ez a művelet arra kényszeríti a hibrid runbook-feldolgozót, hogy töltse le újra a konfigurációját a Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Forgatókönyv: Nem adhat hozzá Windows rendszerű hibrid runbook-feldolgozót

#### <a name="issue"></a>Probléma

Amikor hibrid runbook-feldolgozót próbál hozzáadni a parancsmag használatával, a következő üzenet `Add-HybridRunbookWorker` jelenik meg:

`Machine is already registered`

#### <a name="cause"></a>Ok

Ezt a problémát az okozhatja, ha a gép már regisztrálva van egy másik Automation-fiókkal, vagy ha a hibrid runbook-feldolgozót a gép eltávolítása után próbálja újra hozzáadni.

#### <a name="resolution"></a>Feloldás

A probléma megoldásához távolítsa el a következő beállításkulcsot, indítsa újra a-t, és próbálkozzon újra a `HealthService` `Add-HybridRunbookWorker` parancsmagkal.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Forgatókönyv: Linux rendszerű hibrid runbook-feldolgozót nem lehet hozzáadni

#### <a name="issue"></a>Probléma

Amikor hibrid runbook-feldolgozót próbál hozzáadni a Python-szkripttel, a következő `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` üzenet jelenik meg:

`Unable to register, an existing worker was found. Please deregister any existing worker and try again.`

Emellett a hibrid runbook-feldolgozó regisztrációjának a Python-szkripttel való `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` regisztrációját is megkísérli:

`Failed to deregister worker. [response_status=404]`

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a gép már regisztrálva van egy másik Automation-fiókkal, ha az Azure hibrid feldolgozói csoportot törölték, vagy ha megpróbálja újra hozzáadni a hibrid runbook-feldolgozót, miután eltávolítja egy gépről.

#### <a name="resolution"></a>Feloldás

A probléma megoldása:

1. Távolítsa el az `sudo sh onboard_agent.sh --purge` ügynököt.

1. Futtassa a következő parancsokat:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Az ügynök újrabe- és fel való való fel- és le- vagy felberektetve. `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com`

1. Várja meg a mappa `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` feltöltését.

1. Próbálkozzon `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` újra a Python-szkript használatával.

## <a name="next-steps"></a>Következő lépések

Ha itt nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért próbálkozzon az alábbi csatornák egyikével:

* Az Azure fórumain Azure-szakértőktől [kaphat választ.](https://azure.microsoft.com/support/forums/)
* Csatlakozzon [@AzureSupport](https://twitter.com/azuresupport) a -hez, Microsoft Azure a felhasználói élmény javítása érdekében. Azure ügyfélszolgálata összekapcsolja az Azure-közösséget válaszokkal, támogatással és szakértőkkel.
* Be kell Azure-támogatás incidenst. A webhelyre [Azure-támogatás,](https://azure.microsoft.com/support/options/)és válassza a **Támogatás et.**
