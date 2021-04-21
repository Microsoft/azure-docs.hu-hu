---
title: A Azure Automation State Configuration elhárítása
description: Ez a cikk bemutatja, hogyan háríthatja el és háríthatja el Azure Automation State Configuration problémákat.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91c4780981851b62027fecf18da2c3b78625f272
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831203"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>A Azure Automation State Configuration elhárítása

Ez a cikk a konfigurációk fordítása vagy üzembe helyezése során felmerülő problémák hibaelhárításáról és megoldásáról nyújt Azure Automation State Configuration. Az State Configuration funkcióval kapcsolatos általános információkért lásd [a Azure Automation State Configuration áttekintését.](../automation-dsc-overview.md)

## <a name="diagnose-an-issue"></a>Probléma diagnosztizálása

Ha fordítási vagy telepítési hibát kap a konfigurációhoz, az alábbi lépések segíthetnek a probléma diagnosztizálásában.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Győződjön meg arról, hogy a konfiguráció fordítása sikeresen megtörtént a helyi gépen

Azure Automation State Configuration PowerShell-parancsra (DSC Desired State Configuration épül. A DSC nyelvének és szintaxisának dokumentációját a [PowerShell DSC dokumentációjában találja.](/powershell/scripting/overview)

Egy DSC-konfiguráció helyi gépen való fordításával felderítheti és megoldhatja a gyakori hibákat, például a következő hibákat:

   - Hiányzó modulok.
   - Szintaktikai hibák.
   - Logikai hibák.

### <a name="2-view-dsc-logs-on-your-node"></a>2. DSC-naplók megtekintése a csomóponton

Ha a konfiguráció fordítása sikeres, de egy csomópontra alkalmazva sikertelen, részletes információkat talál a DSC-naplókban. A naplók helyének információiért lásd: Hol vannak [a DSC-eseménynaplók.](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)

Az [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) modul segít a DSC-naplók részletes információinak elemzésében. Ha kapcsolatba lép az ügyfélszolgálattal, a probléma diagnosztizálásához szükségük lesz ezekre a naplókra.

A modult a helyi gépére telepítheti a stabil verziójú modul `xDscDiagnostics` [telepítésére vonatkozó utasításokat követve.](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)

A modul `xDscDiagnostics` Azure-gépen való telepítéséhez használja az [Invoke-AzVMRunCommandparancsot.](/powershell/module/az.compute/invoke-azvmruncommand) A Parancs futtatása  lehetőséget is használhatja a Azure Portal a PowerShell-szkriptek futtatása Windows rendszerű virtuális gépen a következővel: [parancsfuttatás.](../../virtual-machines/windows/run-command.md)

További információ az **xDscDiagnostics használatával kapcsolatban:** [Using xDscDiagnostics to analyze DSC logs (DSC-naplók elemzése az xDscDiagnostics használatával).](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs) Lásd még [az xDscDiagnostics parancsmagokat.](https://github.com/PowerShell/xDscDiagnostics#cmdlets)

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Győződjön meg arról, hogy a csomópontok és az Automation-munkaterület tartalmazza a szükséges modulokat

A DSC a csomóponton telepített moduloktól függ. A modulok Azure Automation State Configuration importálja a szükséges modulokat az Automation-fiókjába a [Modulok importálása lépéseit követve.](../shared-resources/modules.md#import-modules) A konfigurációk a modulok adott verzióitól is függnek. További információ: Modulok [hibaelhárítása.](shared-resources.md#modules)

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Forgatókönyv: Speciális karaktereket nem lehet törölni a portálról

### <a name="issue"></a>Probléma

Amikor DSC-konfigurációt próbál törölni a portálról, a következő hibaüzenet jelenik meg:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Ok

Ez a hiba egy átmeneti probléma, amely a tervek szerint megoldódik.

### <a name="resolution"></a>Feloldás

A konfiguráció törléséhez használja a [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) parancsmagot.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Forgatókönyv: Nem sikerült regisztrálni a DSC-ügynököt

### <a name="issue"></a>Probléma

A [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) vagy egy másik DSC-parancsmag esetén a következő hibaüzenet jelenik meg:

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

Ezt a hibát általában egy tűzfal, a proxykiszolgáló mögött található gép vagy más hálózati hibák okják.

### <a name="resolution"></a>Feloldás

Ellenőrizze, hogy a gépe hozzáfér-e a DSC megfelelő végpontjaihoz, majd próbálkozzon újra. A szükséges portok és címek listáját lásd: [Hálózattervezés.](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Forgatókönyv: Az állapotjelentések a Nem engedélyezett válaszkódot ják vissza

### <a name="issue"></a>Probléma

Amikor regisztrál egy csomópontot a Azure Automation State Configuration, az alábbi hibaüzenetek egyike jelenik meg:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Ok

Ezt a problémát egy hibás vagy lejárt tanúsítvány okozza. Lásd: [Csomópont újra regisztrálása.](../automation-dsc-onboarding.md#re-register-a-node)

Ezt a problémát az is okozhatja, hogy egy proxykonfiguráció nem engedélyezi a hozzáférést a ***.azure-automation.net.** További információ: [Magánhálózatok konfigurálása.](../automation-dsc-overview.md#network-planning) 

### <a name="resolution"></a>Feloldás

Az alábbi lépésekkel regisztrálja újra a hibás DSC-csomópontot.

#### <a name="step-1-unregister-the-node"></a>1. lépés: A csomópont regisztrációjának megszabadása

1. A Azure Portal a Home   >  **Automation Accounts** > (az Automation-fiók) > **State Configuration (DSC) ( Automation-fiók) között.**
1. Válassza **a Csomópontok** lehetőséget, majd válassza ki a problémás csomópontot.
1. A **csomópont regisztrációjának kijelöléséhez** válassza a Regisztráció regisztrációjának megszabadulása lehetőséget.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>2. lépés: A DSC-bővítmény eltávolítása a csomópontról

1. A Azure Portal a **Kezdőlap** virtuális gép >  >   (hibás csomópont) > **bővítmények között.**
1. Válassza **a Microsoft.Powershell.DSC** lehetőséget, majd a PowerShell DSC bővítményt.
1. A **bővítmény eltávolításához** válassza az Eltávolítás lehetőséget.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>3. lépés: Az összes hibás vagy lejárt tanúsítvány eltávolítása a csomópontról

Egy emelt szintű PowerShell-parancssorból a hibás csomóponton futtassa az alábbi parancsokat:

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

#### <a name="step-4-reregister-the-failing-node"></a>4. lépés: A hibás csomópont regisztrációja

1. A Azure Portal a Home   >  **Automation Accounts** > (az Automation-fiók) > **State Configuration (DSC) ( Automation-fiók) között.**
1. Válassza a **Csomópontok lehetőséget.**
1. Válassza a **Hozzáadás** lehetőséget.
1. Válassza ki a hibás csomópontot.
1. Válassza **a Csatlakozás lehetőséget,** majd válassza ki a kívánt beállításokat.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Forgatókönyv: A csomópont "Nem található" hibával meghiúsult állapotú

### <a name="issue"></a>Probléma

A csomópont egy Sikertelen állapotú jelentéssel rendelkezik, amely a következő hibát tartalmazza:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomópont egy konfigurációs névhez van hozzárendelve( **például ABC**), nem pedig egy csomópont-konfiguráció (MOF-fájl) neve, például **ABC. WebServer**.

### <a name="resolution"></a>Feloldás

* Győződjön meg arról, hogy a csomópont konfigurációjának nevét, és nem a konfiguráció nevét rendeli hozzá a csomóponthoz.
* Csomópont-konfigurációt a csomóponthoz a parancsmaggal Azure Portal PowerShell-parancsmaggal rendelhet hozzá.

  * A Azure Portal a Home   >  **Automation Accounts** > (your Automation account) > State Configuration (DSC) (Automation-fiók) és a State Configuration **(DSC) között.** Ezután válasszon ki egy csomópontot, és válassza a **Csomópont-konfiguráció hozzárendelése lehetőséget.**
  * Használja a [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) parancsmagot.

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Forgatókönyv: Nem készült csomópont-konfiguráció (MOF-fájl) a konfiguráció fordításakor

### <a name="issue"></a>Probléma

A DSC-fordítási feladat a következő hibával függeszti fel:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Ok

Ha a DSC-konfigurációban található kulcsszót követő kifejezés kiértékelése , akkor nem lesz `Node` `$null` csomópont-konfiguráció.

### <a name="resolution"></a>Feloldás

A probléma megoldásához használja az alábbi megoldások egyikét:

* Győződjön meg arról, hogy a konfigurációs definíció kulcsszava melletti kifejezés nem Null értéket `Node` ad vissza.
* Ha a konfiguráció fordítása során [a ConfigurationData](../automation-dsc-compile.md) értéket ad át, győződjön meg arról, hogy azokat az értékeket ad át, amelyekre a konfiguráció a konfigurációs adatoktól vár.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Forgatókönyv: A DSC-csomópont jelentése Folyamatban állapotban elakad

### <a name="issue"></a>Probléma

A DSC-ügynök kimenete:

```error
No instance found with given property values
```

### <a name="cause"></a>Ok

Frissítette a Windows Management Framework (WMF) verzióját, és sérült a Windows Management Instrumentation (WMI).

### <a name="resolution"></a>Feloldás

Kövesse a [DSC ismert problémáival és korlátozásokkal kapcsolatos utasításait.](/powershell/scripting/wmf/known-issues/known-issues-dsc)

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Forgatókönyv: A hitelesítő adatok nem használhatók DSC-konfigurációban

### <a name="issue"></a>Probléma

A DSC-fordítási feladat a következő hibával fel van függesztve:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Ok

Használt egy hitelesítő adatokat egy konfigurációban, de nem adott meg megfelelőt a true (igaz) érték megadásához `ConfigurationData` `PSDscAllowPlainTextPassword` az egyes csomópont-konfigurációkhoz.

### <a name="resolution"></a>Feloldás

Ügyeljen arra, hogy a megfelelő értékeket adja meg true (igaz) értékhez a konfigurációban említett `ConfigurationData` `PSDscAllowPlainTextPassword` csomópont-konfigurációkhoz. Lásd: [DSC-konfigurációk fordítása a Azure Automation State Configuration.](../automation-dsc-compile.md)

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Forgatókönyv: "Hibafeldolgozási bővítmény" hiba a gép DSC-bővítményből való engedélyezésekor

### <a name="issue"></a>Probléma

Ha DSC-bővítmény használatával engedélyezi a gépet, a következő hibát tartalmazó hiba történik:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomóponthoz olyan csomópontkonfiguráció-név van hozzárendelve, amely nem létezik a szolgáltatásban.

### <a name="resolution"></a>Feloldás

* Győződjön meg arról, hogy a csomópontot olyan névvel rendeli hozzá, amely pontosan megegyezik a szolgáltatásban található névvel.
* Dönthet úgy, hogy nem adja meg a csomópont konfigurációjának nevét, ami azt jelenti, hogy engedélyezi a csomópontot, de nem rendel hozzá csomópont-konfigurációt.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Forgatókönyv: "Egy vagy több hiba történt" hiba egy csomópont PowerShell-rel való regisztrálása során

### <a name="issue"></a>Probléma

Amikor a [Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) vagy a [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)használatával regisztrál egy csomópontot, a következő hibaüzenet jelenik meg:

```error
One or more errors occurred.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha az Automation-fiók által használttól különálló előfizetésben próbál meg csomópontot regisztrálni.

### <a name="resolution"></a>Feloldás

Az előfizetések közötti csomópontot úgy kezelje, mintha külön felhőhöz vagy helyszínihez lenne definiálva. Regisztrálja a csomópontot az alábbi lehetőségek egyikének használatával a gépek engedélyezéséhez:

* Windows: Fizikai/virtuális Windows rendszerű gépek a helyszínen vagy az [Azure/AWS-től más felhőben.](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines)
* Linux: Fizikai/virtuális Linux rendszerű gépek a helyszínen vagy az [Azure-on kívül más felhőben.](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Forgatókönyv: "A kiépítés sikertelen" hibaüzenet

### <a name="issue"></a>Probléma

Amikor regisztrál egy csomópontot, a következő hibaüzenet jelenik meg:

```error
Provisioning has failed
```

### <a name="cause"></a>Ok

Ez az üzenet akkor jelenik meg, ha probléma van a csomópont és az Azure közötti kapcsolattal.

### <a name="resolution"></a>Feloldás

Állapítsa meg, hogy a csomópont virtuális magánhálózaton (VPN-en) található-e, vagy egyéb problémákat okozhat az Azure-hoz való csatlakozáskor. Lásd: [Szolgáltatások üzembe helyezésével kapcsolatos problémák elhárítása.](onboarding.md)

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Forgatókönyv: Általános hibával kapcsolatos hiba a konfiguráció Linuxon való alkalmazásakor

### <a name="issue"></a>Probléma

Amikor Linux rendszeren alkalmaz konfigurációt, a következő hibát tartalmazó hiba történik:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Ok

Ha a **/tmp** hely beállítása , akkor a DSC aktuális verziója nem tud `noexec` konfigurációkat alkalmazni.

### <a name="resolution"></a>Feloldás

Távolítsa el `noexec` a beállítást a **/tmp helyről.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Forgatókönyv: Az átfedésben szereplő csomópont-konfigurációk nevei rossz kiadást eredményezhetnek

### <a name="issue"></a>Probléma

Ha egyetlen konfigurációs szkriptet használ több csomópont-konfiguráció létrehozásához, és egyes csomópont-konfigurációk nevei más nevek részkészletei, a fordítási szolgáltatás végül rossz konfigurációt rendelhet hozzá. Ez a probléma csak akkor fordul elő, ha egyetlen szkript használatával hoz létre konfigurációkat csomópontonkénti konfigurációs adatokkal, és csak akkor, ha a név átfedésben van a sztring elején. Ilyen például egy konfigurációs szkript, amely a konfigurációkat parancsmagok használatával kivonattáblázatként átadott csomópontadatok alapján hozza létre, a csomópontadatok között pedig a **server** és **az 1server** nevű kiszolgálók is vannak.

### <a name="cause"></a>Ok

Ez a fordítási szolgáltatás ismert problémája.

### <a name="resolution"></a>Feloldás

A legjobb áthidaló megoldás az, ha helyben vagy EGY CI/CD-folyamatban fordítja le, és közvetlenül a szolgáltatásba tölti fel a csomópont-konfigurációs MOF-fájlokat. Ha a szolgáltatásban való fordítás követelmény, a következő legjobb áthidaló megoldás a fordítási feladatok felosztása, hogy a nevek ne legyenek átfedésben.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Forgatókönyv: Átjáró időtúllépési hibája a DSC-konfiguráció feltöltésekor

#### <a name="issue"></a>Probléma

A `GatewayTimeout` DSC-konfiguráció feltöltésekor hibaüzenet jelenik meg. 

### <a name="cause"></a>Ok

A fordítás hosszú ideig tartó DSC-konfigurációk okozhatják ezt a hibát.

### <a name="resolution"></a>Feloldás

A DSC-konfigurációkat gyorsabban elemezheti, ha explicit módon meg van adva a paraméter `ModuleName` bármely [Import-DSCResource híváshoz.](/powershell/scripting/dsc/configurations/import-dscresource)

## <a name="next-steps"></a>Következő lépések

Ha itt nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért próbálkozzon az alábbi csatornák egyikével:

* Az Azure fórumain Azure-szakértőktől [kaphat választ.](https://azure.microsoft.com/support/forums/)
* Csatlakozzon [@AzureSupport](https://twitter.com/azuresupport) a -hez, Microsoft Azure a felhasználói élmény javítása érdekében. Azure ügyfélszolgálata összekapcsolja az Azure-közösséget válaszokkal, támogatással és szakértőkkel.
* Incidens Azure-támogatás be. A webhelyre [Azure-támogatás,](https://azure.microsoft.com/support/options/)és válassza a **Támogatás lekérte lehetőséget.**
