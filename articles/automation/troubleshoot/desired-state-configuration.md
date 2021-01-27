---
title: Az állapot-konfigurációval kapcsolatos problémák elhárítása Azure Automation
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani Azure Automation állapot-konfigurációval kapcsolatos problémákat.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.openlocfilehash: e6caf3fed708e89b55a88719ca5358f6174c2ac8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896528"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Az állapot-konfigurációval kapcsolatos problémák elhárítása Azure Automation

Ez a cikk a konfigurációk Azure Automation állapotának konfigurációjában való fordításakor vagy telepítésekor felmerülő problémák elhárításával és megoldásával kapcsolatos információkat tartalmaz. Az állapot-konfigurációs szolgáltatással kapcsolatos általános információkért lásd: [Azure Automation állapot konfigurációjának áttekintése](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Probléma diagnosztizálása

Ha a konfiguráció fordítási vagy telepítési hibáját kapja, a következő lépésekkel diagnosztizálhatja a problémát.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Győződjön meg arról, hogy a konfiguráció sikeresen lefordításra került a helyi gépen

Azure Automation állapot-konfiguráció a PowerShell desired State Configuration (DSC) szolgáltatásra épül. A DSC nyelvét és szintaxisát a [POWERSHELL DSC docs](/powershell/scripting/overview)dokumentációjában találja.

A DSC-konfiguráció helyi gépen való fordításával felderítheti és megoldhatja a gyakori hibákat, például a következőket:

   - Hiányzó modulok.
   - Szintaktikai hibák.
   - Logikai hibák.

### <a name="2-view-dsc-logs-on-your-node"></a>2. a csomóponton található DSC-naplók megtekintése

Ha a konfiguráció sikeresen lefordítva, de a csomópontra való alkalmazása meghiúsul, részletes információkat talál a DSC-naplókban. További információ a naplók kereséséről: [Hol találhatók a DSC-eseménynaplók](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

A [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) modul segítséget nyújthat a DSC-naplók részletes adatainak elemzésében. Ha kapcsolatba lép a támogatási szolgálattal, ezek a naplók a probléma diagnosztizálásához szükségesek.

A modult a helyi gépen is telepítheti a `xDscDiagnostics` [STABLE verzió telepítése moduljának](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)utasításait követve.

Ha a `xDscDiagnostics` modult az Azure-gépen szeretné telepíteni, használja a következőt: [hívás-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand). A Azure Portal Futtatás **parancsát** is használhatja a [Windows rendszerű virtuális gépen a PowerShell-parancsfájlok futtatása a Futtatás paranccsal](../../virtual-machines/windows/run-command.md)című témakör lépéseit követve.

További információ a **xDscDiagnostics** használatáról: a [XDSCDIAGNOSTICS használata a DSC-naplók elemzéséhez](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Lásd még: [xDscDiagnostics-parancsmagok](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Győződjön meg arról, hogy a csomópontok és az Automation-munkaterület szükséges modulokkal rendelkezik

A DSC a csomóponton telepített moduloktól függ. Azure Automation állapot konfigurációjának használatakor az [importálási modulok](../shared-resources/modules.md#import-modules)lépéseit követve importálja a szükséges modulokat az Automation-fiókjába. A konfigurációk a modulok adott verzióihoz is tartozhatnak. További információ: [modulok hibakeresése](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Forgatókönyv: speciális karaktereket tartalmazó konfiguráció nem törölhető a portálról

### <a name="issue"></a>Probléma

Amikor egy DSC-konfigurációt próbál törölni a portálról, a következő hibaüzenet jelenik meg:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Ok

Ez a hiba egy ideiglenes probléma, amelyet megterveztek a megoldás.

### <a name="resolution"></a>Feloldás

A konfiguráció törléséhez használja a [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) parancsmagot.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Forgatókönyv: nem sikerült regisztrálni a DSC-ügynököt

### <a name="issue"></a>Probléma

Ha a [set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) vagy egy másik DSC-parancsmagot kap, a következő hibaüzenet jelenik meg:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Ok

Ezt a hibát általában egy tűzfal okozza, a gép a proxykiszolgáló mögött vagy más hálózati hiba miatt.

### <a name="resolution"></a>Feloldás

Ellenőrizze, hogy a számítógépe rendelkezik-e a DSC-hez megfelelő végpontokkal, majd próbálkozzon újra. A szükséges portok és címek listáját itt tekintheti meg: [hálózati tervezés](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Forgatókönyv: az állapotjelentések jogosulatlanul adják vissza a válasz kódját

### <a name="issue"></a>Probléma

Ha Azure Automation állapot-konfigurációval rendelkező csomópontot regisztrál, a következő hibaüzenetek egyike jelenik meg:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Ok

Ezt a problémát rossz vagy lejárt tanúsítvány okozza. Lásd: [csomópont ismételt regisztrálása](../automation-dsc-onboarding.md#re-register-a-node).

Ezt a problémát az is okozhatja, hogy a proxy konfigurációja nem engedélyezi a **_. Azure-Automation.net_* elérését. További információ: [magánhálózatok konfigurálása](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Feloldás

A sikertelen DSC-csomópontok újraregisztrálásához kövesse az alábbi lépéseket.

#### <a name="step-1-unregister-the-node"></a>1. lépés: a csomópont regisztrációjának törlése

1. A Azure Portal lépjen a **Home**  >  **Automation-fiókok** > (az Automation-fiók) > **állapot-konfiguráció (DSC)** elemre.
1. Válassza ki a **csomópontok** elemet, és válassza ki a problémával rendelkező csomópontot.
1. A csomópont regisztrációjának megszüntetéséhez válassza a **regisztráció** törlése lehetőséget.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>2. lépés: a DSC-bővítmény eltávolítása a csomópontból

1. A Azure Portal nyissa meg a **Kezdőlap**  >  **virtuális gép** > (a csomópont meghibásodása) > **bővítmények** lehetőséget.
1. Válassza a **Microsoft. PowerShell. DSC**, a PowerShell DSC bővítmény elemet.
1. A bővítmény eltávolításához válassza az **Eltávolítás** lehetőséget.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>3. lépés: az összes hibás vagy lejárt tanúsítvány eltávolítása a csomópontból

Futtassa a következő parancsokat egy rendszergazda jogú PowerShell-parancssorból:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>4. lépés: a sikertelen csomópont újraregisztrálása

1. A Azure Portal lépjen a **Home**  >  **Automation-fiókok** > (az Automation-fiók) > **állapot-konfiguráció (DSC)** elemre.
1. Válassza ki a **csomópontokat**.
1. Válassza a **Hozzáadás** elemet.
1. Válassza a hibás csomópontot.
1. Válassza a **kapcsolat** lehetőséget, és válassza ki a kívánt beállításokat.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Forgatókönyv: a csomópont "nem található" hiba miatt sikertelen állapotú.

### <a name="issue"></a>Probléma

A csomóponton egy sikertelen állapotú jelentés található, amely a következő hibát tartalmazza:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomópontot egy konfigurációs névhez (például **ABC**-hez) rendeli hozzá a csomópont-konfiguráció (MOF-fájl) neve helyett, például: **ABC. Webkiszolgáló**.

### <a name="resolution"></a>Feloldás

* Győződjön meg arról, hogy a csomópontot a csomópont-konfiguráció nevével rendeli hozzá, ne pedig a konfiguráció nevét.
* Csomópont-konfigurációt a Azure Portal vagy egy PowerShell-parancsmag használatával rendelhet hozzá egy csomóponthoz.

  * A Azure Portal lépjen a **Home**  >  **Automation-fiókok** > (az Automation-fiók) > **állapot-konfiguráció (DSC)** elemre. Ezután válasszon ki egy csomópontot, és válassza a **csomópont-konfiguráció kiosztása** lehetőséget.
  * Használja a [set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) parancsmagot.

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Forgatókönyv: a konfiguráció lefordítása során a rendszer nem állított fel csomópont-konfigurációkat (MOF-fájlokat)

### <a name="issue"></a>Probléma

A DSC-fordítási feladata a következő hibával felfüggeszthető:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Ok

Ha a `Node` DSC-konfigurációban a kulcsszót követő kifejezés a értékre van kiértékelve `$null` , a rendszer nem állít elő csomópont-konfigurációt.

### <a name="resolution"></a>Feloldás

A probléma megoldásához használja az alábbi megoldások egyikét:

* Győződjön meg arról, hogy a `Node` konfigurációs definícióban a kulcsszó melletti kifejezés nem a NULL értékre van kiértékelve.
* Ha a konfiguráció lefordításakor [ConfigurationData](../automation-dsc-compile.md) továbbít, győződjön meg arról, hogy a konfigurációs adatokból származó értékek átadása folyamatban van.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Forgatókönyv: a DSC-csomópont jelentésének beragadása folyamatban állapotba kerül

### <a name="issue"></a>Probléma

A DSC-ügynök kimenetei:

```error
No instance found with given property values
```

### <a name="cause"></a>Ok

Frissítette a Windows Management Framework (WMF) verzióját, és sérült Windows Management Instrumentation (WMI).

### <a name="resolution"></a>Feloldás

Kövesse a [DSC ismert problémák és korlátozások](/powershell/scripting/wmf/known-issues/known-issues-dsc)című témakör utasításait.

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Forgatókönyv: a hitelesítő adatok nem használhatók DSC-konfigurációban

### <a name="issue"></a>Probléma

A DSC-fordítási feladata a következő hibával felfüggesztve:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Ok

Egy konfigurációban használta a hitelesítő adatokat, de az `ConfigurationData` egyes csomópont-konfigurációk esetében nem megfelelő a True értékre állítása `PSDscAllowPlainTextPassword` .

### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a megfelelő `ConfigurationData` értékre van állítva a `PSDscAllowPlainTextPassword` konfigurációban említett minden csomópont-konfiguráció esetében, hogy igaz legyen. Lásd: [DSC-konfigurációk fordítása Azure Automation állapot-konfigurációban](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Forgatókönyv: "nem sikerült feldolgozni a bővítményt" hibaüzenet jelenik meg a gép DSC-bővítményből való engedélyezésekor

### <a name="issue"></a>Probléma

Ha DSC-bővítmény használatával engedélyezi a gépet, a hibát tartalmazó hiba fordul elő:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomóponthoz olyan csomópont-konfigurációs nevet rendelnek, amely nem létezik a szolgáltatásban.

### <a name="resolution"></a>Feloldás

* Győződjön meg arról, hogy a csomópontot olyan névvel rendeli hozzá, amely pontosan megegyezik a szolgáltatásban szereplő névvel.
* Dönthet úgy is, hogy nem tartalmazza a csomópont-konfiguráció nevét, ami a csomópont engedélyezését eredményezi, de nem rendel hozzá csomópont-konfigurációt.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Forgatókönyv: "egy vagy több hiba történt" hibaüzenet jelenik meg, amikor a PowerShell használatával regisztrál egy csomópontot.

### <a name="issue"></a>Probléma

Ha regisztrálja a csomópontot a [Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) vagy a [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)használatával, a következő hibaüzenet jelenik meg:

```error
One or more errors occurred.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha olyan csomópontot próbál meg regisztrálni egy különálló előfizetésben, amelyet az Automation-fiók használ.

### <a name="resolution"></a>Feloldás

Az előfizetési csomópontot úgy kezelheti, mintha egy különálló felhőhöz vagy a helyszíni környezethez van definiálva. Regisztrálja a csomópontot a következő lehetőségek egyikének használatával a gépek engedélyezéséhez:

* Windows: [fizikai/virtuális Windowsos gépek a helyszínen, vagy az Azure-tól/AWS-től eltérő felhőben](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [fizikai/virtuális Linux rendszerű számítógépek a helyszínen, vagy az Azure-on kívül más felhőben is](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Forgatókönyv: a "kiépítés sikertelen" hibaüzenet

### <a name="issue"></a>Probléma

Amikor regisztrál egy csomópontot, a következő hibaüzenet jelenik meg:

```error
Provisioning has failed
```

### <a name="cause"></a>Ok

Ez az üzenet akkor fordul elő, ha probléma van a csomópont és az Azure közötti kapcsolattal.

### <a name="resolution"></a>Feloldás

Állapítsa meg, hogy a csomópont virtuális magánhálózati (VPN) vagy más, az Azure-hoz kapcsolódó problémával rendelkezik-e. Lásd: a [funkciók üzembe helyezésével kapcsolatos hibák elhárítása](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Forgatókönyv: hiba történt a konfiguráció Linux rendszerben való alkalmazása során felmerülő általános hiba esetén

### <a name="issue"></a>Probléma

Ha Linux rendszeren alkalmazza a konfigurációt, a hibát tartalmazó hiba fordul elő:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Ok

Ha a **/tmp** helye értékre van állítva `noexec` , a DSC jelenlegi verziója nem tudja alkalmazni a konfigurációkat.

### <a name="resolution"></a>Feloldás

Távolítsa el a `noexec` beállítást a **/tmp** helyéről.

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Forgatókönyv: az átfedésben lévő csomópont-konfigurációs nevek rossz kiadást okozhatnak

### <a name="issue"></a>Probléma

Ha egyetlen konfigurációs parancsfájl használatával hoz létre több csomópontos konfigurációt, és egyes csomópont-konfigurációs nevek más nevek részhalmazai, a fordítási szolgáltatás befejezheti a helytelen konfiguráció hozzárendelését. Ez a probléma csak akkor fordul elő, ha egyetlen parancsfájl használatával hoz létre konfigurációkat egy csomóponton, és csak akkor, ha a név átfedésben van a karakterlánc elején. Ez a példa egy olyan konfigurációs parancsfájl, amely a szórótábla átadott csomóponti adat alapján történő konfigurációk létrehozásához használható a parancsmagok használatával, és a csomópont adatai tartalmazzák a **kiszolgáló** és a **1kiszolgáló üzemmódját** nevű kiszolgálókat.

### <a name="cause"></a>Ok

Ez a fordítási szolgáltatás ismert problémája.

### <a name="resolution"></a>Feloldás

A legjobb megoldás a helyi vagy CI/CD-folyamat fordítása, és a csomópont-konfiguráció MOF-fájljainak közvetlenül a szolgáltatásba való feltöltése. Ha a szolgáltatás fordítása követelmény, a következő legjobb megoldás a fordítási feladatok felosztása, hogy a nevek ne legyenek átfedésben.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Forgatókönyv: átjáró időtúllépési hibája a DSC-konfiguráció feltöltésekor

#### <a name="issue"></a>Probléma

A DSC- `GatewayTimeout` konfiguráció feltöltésekor hibaüzenet jelenik meg. 

### <a name="cause"></a>Ok

A fordításhoz hosszú időt igénybe vehető DSC-konfigurációk ezt a hibát okozhatják.

### <a name="resolution"></a>Feloldás

A DSC-konfigurációkat gyorsabban elemezheti, ha explicit módon az `ModuleName` [DSCResource](/powershell/scripting/dsc/configurations/import-dscresource) -hívások paraméterét is megadja.

## <a name="next-steps"></a>Következő lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport) a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget.
