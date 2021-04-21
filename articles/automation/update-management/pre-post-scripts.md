---
title: Szkript előtti és utólagos szkriptek kezelése az Azure-Update Management üzembe helyezéséhez
description: Ez a cikk bemutatja, hogyan konfigurálhatja és kezelheti a frissítéstelepítések elő- és utószk szkripteket.
services: automation
ms.subservice: update-management
ms.date: 03/08/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51067095b7ebb33da61908b1424752b481668f5f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830807"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Előzetes és utólagos szkriptek kezelése

Az előzetes és utólagos szkriptek olyan runbookok, amelyek a Azure Automation-fiókban a frissítéstelepítés előtt és után (feladat utáni) futtathatók. Az előszk szkriptek és az utólagos szkriptek az Azure-környezetben futnak, nem helyben. Az előszk szkriptek a frissítéstelepítés elején futnak. A parancsfájlok a telepítés végén, illetve a konfigurált újraindítások után futnak.

## <a name="pre-script-and-post-script-requirements"></a>Szkript előtti és utáni követelmények

Ahhoz, hogy egy runbook szkript előtti vagy utáni szkriptként legyen használva, importálni kell az Automation-fiókjába, és közzé kell tenni a [runbookot.](../manage-runbooks.md#publish-a-runbook)

Jelenleg csak a PowerShell- és Python 2-runbookok támogatottak előkészítő/utólagos szkriptként. Más runbooktípusok, például a Python 3, a Grafikus, a PowerShell-munkafolyamat és a Grafikus PowerShell-munkafolyamat jelenleg nem támogatottak előkészítő/utólagos parancsfájlként.

## <a name="pre-script-and-post-script-parameters"></a>Szkript előtti és utáni paraméterek

Az előszk szkriptek és az utólagos szkriptek konfigurálásakor a runbook ütemezéséhez hasonló paramétereket is átadhat. A paraméterek a frissítéstelepítés létrehozásakor vannak meghatározva. Az előszk szkriptek és az utólagos szkriptek a következő típusokat támogatják:

* [char]
* [bájt]
* [int]
* [long]
* [decimális]
* [single]
* [double]
* [DateTime] ([Dátum/idő])
* [sztring]

A szkript előtti és utáni runbook-paraméterek nem támogatják a logikai, objektum- vagy tömbtípusokat. Ezek az értékek a runbookok meghiúsulását okják. 

Ha egy másik objektumtípusra van szüksége, a forgatókönyvben saját logikáját használhatja egy másik típusra.

A normál runbook-paraméterek mellett a paraméter `SoftwareUpdateConfigurationRunContext` (típus: JSON-sztring) is meg van adva. Ha a paramétert a szkript előtti vagy a szkript utáni runbookban definiálja, azt a frissítéstelepítés automatikusan továbbadja. A paraméter a frissítéstelepítéssel kapcsolatos információkat tartalmaz, amely a [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)által visszaadott információk részkészlete. Az alábbi szakaszok határozzák meg a társított tulajdonságokat.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext tulajdonságok

|Tulajdonság  |Leírás  |
|---------|---------|
|SoftwareUpdateConfigurationName     | A szoftverfrissítési konfiguráció neve.        |
|SoftwareUpdateConfigurationRunId     | A futtatás egyedi azonosítója.        |
|SoftwareUpdateConfigurationSettings     | A szoftverfrissítési konfigurációhoz kapcsolódó tulajdonságok gyűjteménye.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | A frissítéstelepítéshez megcélzott operációs rendszerek.         |
|SoftwareUpdateConfigurationSettings.duration     | A frissítéstelepítés maximális időtartama az `PT[n]H[n]M[n]S` ISO8601 szerint fut; ezt karbantartási időszaknak is nevezik.          |
|SoftwareUpdateConfigurationSettings.Windows     | Windows rendszerű számítógépekhez kapcsolódó tulajdonságok gyűjteménye.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | A frissítéstelepítésből kizárt kB-k listája.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | A frissítéstelepítéshez kiválasztott frissítési besorolások.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | A frissítéstelepítés újraindítási beállításai.        |
|azureVirtualMachines     | A frissítéstelepítésben található Azure-beli virtuális gépek erőforrás-azonosítóinak listája.        |
|nonAzureComputerNames (nemAzureComputerNames)|A frissítéstelepítésben található nem Azure-beli számítógépek teljes tartományának listája.|

Az alábbi példa egy JSON-sztring, amely a **SoftwareUpdateConfigurationRunContext** paraméternek van átadva:

```json
"SoftwareUpdateConfigurationRunContext": {
    "SoftwareUpdateConfigurationName": "sampleConfiguration",
    "SoftwareUpdateConfigurationRunId": "00000000-0000-0000-0000-000000000000",
    "SoftwareUpdateConfigurationSettings": {
      "operatingSystem": "Windows",
      "duration": "PT2H0M",
      "windows": {
        "excludedKbNumbers": [
          "168934",
          "168973"
        ],
        "includedUpdateClassifications": "Critical",
        "rebootSetting": "IfRequired"
      },
      "azureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
      ],
      "nonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
      ]
    }
  }
```

Az összes tulajdonságra vonatkozó teljes példa a következő: [Szoftverfrissítési konfiguráció lekért neve alapján.](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> A `SoftwareUpdateConfigurationRunContext` objektum ismétlődő bejegyzéseket tartalmazhat a gépekhez. Ez azt okozhatja, hogy az előszk szkriptek és az utólagos szkriptek többször is futnak ugyanazon a gépen. Ennek a viselkedésnek a kivédheti a használatát, `Sort-Object -Unique` ha csak egyedi virtuálisgép-neveket választ ki.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Szkript előtti vagy utólagos szkript használata egy üzemelő példányban

Ha szkript előtti vagy utólagos szkriptet használ egy frissítéstelepítésben, először hozzon létre egy frissítéstelepítést. Válassza **a Pre-scripts + Post-Scripts lehetőséget.** Ez a művelet megnyitja a **Select Pre-scripts + Post-scripts** lapot.

![Szkriptek kiválasztása](./media/pre-post-scripts/select-scripts.png)

Válassza ki a használni kívánt szkriptet. Ebben a példában az **UpdateManagement-TurnOnVms** runbookot használjuk. Amikor kiválasztja a runbookot, megnyílik **a Parancsfájl konfigurálása** lap. Válassza **a Pre-Script (Szkript előtti)** lehetőséget, majd az **OK gombot.**

Ismételje meg ezt a folyamatot az **UpdateManagement-TurnOffVms szkripttel** is. Ha azonban a Parancsfájl típust **választja,** válassza a **Szkript utáni lehetőséget.**

A **Kijelölt elemek szakaszban** most már mindkét szkript ki van jelölve. Az egyik egy szkript előtti, a másik pedig egy utólagos:

![Kijelölt elemek](./media/pre-post-scripts/selected-items.png)

Fejezze be a frissítéstelepítés konfigurálását.

Ha a frissítéstelepítés befejeződött, az  eredmények megtekintéséhez a Frissítéstelepítések használhatja. Amint látható, az állapot a szkript előtti és utáni szkripthez van megsziva:

![Eredmények frissítése](./media/pre-post-scripts/update-results.png)

A frissítéstelepítés futtatásának kiválasztásával további részleteket talál az előszk szkriptek és az utólagos szkriptek számára. A parancsprogram forrásának a futtatáskor megadott hivatkozása.

![Üzembe helyezési futtatás eredményei](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Üzembe helyezés leállítása

Ha egy szkript előtti szkript alapján szeretné leállítani az üzembe helyezést, [kivételt kell kiállítania.](../automation-runbook-execution.md#throw) Ha nem, az üzembe helyezés és a szkript utáni futtatás továbbra is futni fog. Az alábbi kódrészlet bemutatja, hogyan lehet kivételt visszadobni a PowerShell használatával.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

A Python 2-ben a kivételkezelés egy [try blokkban van kezelve.](https://www.python-course.eu/exception_handling.php)

## <a name="interact-with-machines"></a>Interakció a gépekkel

Az előszk szkriptek és az utólagos szkriptek runbookokként futnak az Automation-fiókban, nem pedig közvetlenül az üzemelő példányban futó gépeken. Az előfeladatok és az utólagos feladatok szintén az Azure-környezetben futnak, és nem férnek hozzá a nem Azure-beli gépekhez. A következő szakaszok azt mutatják be, hogyan kommunikálhat közvetlenül a gépekkel, akár Azure-beli, akár nem Azure-beli virtuális gépekről van szó.

### <a name="interact-with-azure-machines"></a>Interakció az Azure-beli gépekkel

Az előfeladatok és az utólagos feladatok runbookokként futnak, és nem futnak natív módon az üzemelő példányban az Azure-beli virtuális gépeken. Az Azure-beli virtuális gépekkel való kommunikációhoz a következő elemekre van hatással:

* Egy run as account
* Egy futtatni kívánt runbook

Az Azure-beli gépekkel való interakcióhoz használja az [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) parancsmagot az Azure-beli virtuális gépekkel való kommunikációhoz. Ennek egy példájért tekintse meg a Runbook-példát a Update Management [szkript futtatása paranccsal.](https://github.com/azureautomation/update-management-run-script-with-run-command)

### <a name="interact-with-non-azure-machines"></a>Nem Azure-beli gépek interakciói

Az előfeladatok és az utólagos feladatok Azure-környezetben futnak, és nem férnek hozzá a nem Azure-beli gépekhez. A nem Azure-beli gépekkel való interakcióhoz a következő elemekre van hatással:

* Egy run as account
* A gépen telepített hibrid runbook-feldolgozó
* Egy helyileg futtatni kívánt runbook
* Egy szülő runbook

A nem Azure-beli gépekkel való interakcióhoz egy szülő runbookot futtat az Azure-környezetben. Ez a runbook egy gyermekrunbookot hív meg a [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) parancsmaggal. Meg kell adnia a paramétert, és meg kell adnia a hibrid runbook-feldolgozó nevét a szkript `RunOn` futtatásához. Tekintse meg a forgatókönyv példáját [Update Management – szkript helyi futtatása.](https://github.com/azureautomation/update-management-run-script-locally)

## <a name="abort-patch-deployment"></a>Javítás üzembe helyezésének megszakítása

Ha az előkészítő szkript hibát ad vissza, érdemes megszakítani az üzembe helyezést. Ahhoz, hogy ezt meg tudja tenni, hibát [kell](/powershell/module/microsoft.powershell.core/about/about_throw) adnunk a szkriptben minden olyan logika esetén, amely hibát jelentene.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

Ha a Python 2-ben hibát szeretne dobni egy bizonyos feltétel bekövetkeztekor, használjon [egy raise utasítást.](https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement)

```python
If (<My custom error logic>)
   raise Exception('Something happened.')
```

## <a name="samples"></a>Példák

Az előzetes és utólagos szkriptek mintái a [Azure Automation GitHub-szervezetben](https://github.com/azureautomation) és a [PowerShell-galéria-ban](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)találhatók, vagy importálhatja őket a Azure Portal. Ennek érdekében az Automation-fiók Folyamatautomatizálás **területén** válassza a **Runbook-katalógus lehetőséget.** A **Update Management** a szűrőt.

![Katalóguslista](./media/pre-post-scripts/runbook-gallery.png)

Vagy megkeresheti őket a szkript neve alapján, az alábbi listában látható módon:

* Update Management – Virtuális gépek bekapcsolás
* Update Management – Virtuális gépek kikapcsolása
* Update Management – Szkript helyi futtatása
* Update Management – Sablon előkészítő és utólagos szkriptek számára
* Update Management – Szkript futtatása parancsfuttatás

> [!IMPORTANT]
> A runbookok importálása után közzé kell tenni őket a használatuk előtt. Ennek ához keresse meg a runbookot az Automation-fiókjában, válassza a **Szerkesztés,** majd a Közzététel **lehetőséget.**

A minták mind az alábbi példában meghatározott alapszintű sablonon alapulnak. Ezzel a sablonnal létrehozhat egy saját runbookot, amely előre és utólagos szkriptekkel használható. Az Azure-ral való hitelesítéshez és a paraméter kezelésével kapcsolatos `SoftwareUpdateConfigurationRunContext` logikát is tartalmazza.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccount -Name $runId -Value "" -Encrypted $false
#Set value of variable
Set-AutomationVariable -Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Nem grafikus PowerShell-runbookok esetén, amelyek a `Add-AzAccount` `Add-AzureRMAccount` [Connect-AzAccount aliasai.](/powershell/module/az.accounts/connect-azaccount) Használhatja ezeket a parancsmagokat, [](../automation-update-azure-modules.md) vagy frissítheti az Automation-fiókjában található modulokat a legújabb verziókra. Előfordulhat, hogy frissítenie kell a modulokat, még akkor is, ha most hozott létre új Automation-fiókot.

## <a name="next-steps"></a>Következő lépések

A frissítések kezelésével kapcsolatos részletekért lásd: Virtuális gépek frissítésének és [javításának kezelése.](manage-updates-for-vm.md)
