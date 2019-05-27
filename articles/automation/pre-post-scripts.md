---
title: Konfigurálja az Update Management üzembe helyezés az Azure-ban található előtti és utáni szkriptek
description: Ez a cikk bemutatja, hogyan konfigurálhat és felügyelhet előtti és utáni parancsfájlokat a frissítéstelepítések
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7317b634ee4c8886ce5c99bb2b3395d7d1f646d5
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65913856"
---
# <a name="manage-pre-and-post-scripts"></a>Kezelése előtti és utáni szkriptek

Előtti és utáni szkriptek lehetővé teszik a PowerShell-forgatókönyvek az Automation-fiókban (előtti feladat) előtt és után (utáni) frissítés központi telepítés. Előtti és utáni parancsfájlokat futtatni, az Azure környezetben, és nem helyileg. Üzem előtti parancsfájlok futtatása a központi telepítési elején. Parancsfájlok a bejegyzés végén az üzemelő példány, és minden konfigurált újraindítása után futtassa.

## <a name="runbook-requirements"></a>Forgatókönyv követelményei

Egy runbook előtti vagy utáni parancsfájl használható a runbook importálja az automation-fiókját, és közzé kell. Ez a folyamat kapcsolatos további információkért lásd: [runbook közzététele](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Előkészítő/utólagos parancsfájl használatával

A előtti használja, és vagy post frissítéstelepítés szkriptet, először hozzon létre egy központi telepítést. Válassza ki **előkészítő parancsfájljainak + utáni parancsfájlok**. Ez a művelet megnyitja a **előkészítő parancsfájljainak kiválasztása + utáni parancsfájlok** lapot.  

![Jelölje be a parancsfájlok](./media/pre-post-scripts/select-scripts.png)

Válassza ki a parancsprogramot használja, ebben a példában szeretne, akkor használja a **UpdateManagement-TurnOnVms** runbook. Amikor kiválasztja a runbook a **konfigurálása parancsfájl** lapon megnyílik a **az előkészítő parancsprogram**. Kattintson a **OK** végeztével.

Ismételje meg ezt a folyamatot a a **UpdateManagement-TurnOffVms** parancsfájlt. De amikor kiválasztja a **parancsprogram típusa**, válassza a **az utólagos parancsprogram**.

A **kiválasztott cikkek** szakaszban most látható, mind a kiválasztott parancsfájlok és a egy szkript előtti és más utáni parancsfájlt.

![Kijelölt elemek](./media/pre-post-scripts/selected-items.png)

A frissítéstelepítés konfigurálásának befejezéséhez.

A frissítéstelepítés befejeződése után nyissa meg **Frissítéstelepítések** az eredmények megtekintéséhez. Amint láthatja, a szkript előtti és utáni állapota állnak rendelkezésre.

![Eredmények frissítése](./media/pre-post-scripts/update-results.png)

Futtassa a frissítéstelepítés tárolóiba kattintva, a előtti és utáni parancsfájlokat további információkat az Ön megadott. A futtatás idején a parancsfájl forrás találhatóak.

![Központi telepítés futtatási eredmények](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Paraméterek átadása

Ha előre konfigurál, és utáni parancsfájlokat, adhat át a paraméterek hasonlóan runbook ütemezése. Paraméterek vannak meghatározva a frissítés központi telepítés létrehozása idején. Előtti és utáni parancsfájlokat a következő típusokat támogatja:

* [char]
* [byte]
* [int]
* [long]
* [decimális]
* [egyetlen]
* [dupla]
* [DateTime]
* [string]

Ha egy másik objektum típusa, is szavazattal azt egy másik a saját a runbook logikája.

A standard szintű runbook paraméterek kívül egy további paraméter biztosítunk. Ez a paraméter **SoftwareUpdateConfigurationRunContext**. Ez a paraméter egy JSON-karakterlánc, és ha a előtti vagy utáni parancsfájl adja meg a paramétert, automatikusan átadott a frissítéstelepítés által. A paraméter tartalmazza a frissítés központi telepítését, amelynek része a információ által visszaadott információk a [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) az alábbi táblázat bemutatja, a tulajdonságok vannak megadva, a változóban:

## <a name="stopping-a-deployment"></a>Központi telepítés leállítása

Ha a központi telepítés előtti parancsprogram kell alapján le szeretné [throw](automation-runbook-execution.md#throw) kivételt. Ha Ön nem kivételt, az üzembe helyezés és az utólagos parancsfájl továbbra is futni fog. A [példa runbook](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) a katalógus bemutatja, hogyan ezt megteheti. Az alábbiakban látható egy részlet a runbookból.

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

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext tulajdonságai

|Tulajdonság  |Leírás  |
|---------|---------|
|SoftwareUpdateConfigurationName     | A szoftverfrissítési konfiguráció neve        |
|SoftwareUpdateConfigurationRunId     | A Futtatás egyedi azonosítója.        |
|SoftwareUpdateConfigurationSettings     | A szoftverfrissítési konfiguráció kapcsolódó tulajdonságok gyűjteménye         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Az operációs rendszerek, a központi telepítési megcélzott         |
|SoftwareUpdateConfigurationSettings.duration     | A frissítéstelepítés maximális időtartama futtató `PT[n]H[n]M[n]S` megfelelően ISO8601, más néven a "karbantartási időszak"          |
|SoftwareUpdateConfigurationSettings.Windows     | Windows-számítógépek kapcsolódó tulajdonságok gyűjteménye         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | A frissítéstelepítés kizárt Tudásbázis listája        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | A frissítés telepítésre kijelölt frissítési besorolások        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Indítsa újra a központi telepítési beállítások        |
|azureVirtualMachines     | Az Azure-beli virtuális gépek a frissítéstelepítés ResourceId listája        |
|nonAzureComputerNames|A frissítéstelepítés nem Azure-beli számítógépek teljes tartománynevek listája|

Az alábbi példa a függvénynek átadott JSON-karakterláncot a **SoftwareUpdateConfigurationRunContext** paramétert:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ], 
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Az összes tulajdonság egy teljes példát tekinthet meg: [Frissítés szoftverkonfigurációjáról - Get-név alapján](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> A `SoftwareUpdateConfigurationRunContext` objektum gépekhez duplikált bejegyzéseket is tartalmazhat. Emiatt előtti és utáni parancsfájlok futtatását több alkalommal ugyanazon a gépen. A megoldás ezt a viselkedést, használja `Sort-Object -Unique` ki csak a virtuális gép egyedi nevek a szkriptet.

## <a name="samples"></a>Minták

Minták előtti és utáni parancsfájlokat a találja a [Script Center-galéria](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), vagy importált az Azure Portalon keresztül. Importálhatja őket a portálon keresztül, az Automation-fiók alatt **Folyamatautomatizálás**válassza **Runbookkatalógus**. Használat **az Update Management** a szűrőhöz.

![Gyűjteménylista](./media/pre-post-scripts/runbook-gallery.png)

Vagy Ön is megkeresheti azokat a szkript neve szerint a következő listában látható módon:

* Az Update Management - es virtuális gépek
* Az Update Management – virtuális gép kikapcsolása
* Az Update Management – helyi futtatási szkript
* Update Management - előkészítő/utólagos parancsfájl sablon
* Az Update Management – futtatási szkript futtatása paranccsal

> [!IMPORTANT]
> Miután importálta a runbookokat, kell **közzététel** azok használhatók legyenek. Ehhez, keresse meg a runbook az Automation-fiókját, válassza ki a **szerkesztése**, és kattintson a **közzététel**.

A minták a egyszerű sablon, amely a következő példában definiálva van a alapulnak. Ez a sablon előtti és utáni parancsfájlok használata a saját forgatókönyv létrehozására használható. A hitelesítés az Azure-ral és kezelési szükséges logikát a `SoftwareUpdateConfigurationRunContext` paraméter részét képezik.

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
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
 
Add-AzureRmAccount ` 
    -ServicePrincipal ` 
    -TenantId $ServicePrincipalConnection.TenantId ` 
    -ApplicationId $ServicePrincipalConnection.ApplicationId ` 
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint 
 
$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID 
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
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false 
#Set value of variable  
Set-AutomationVariable –Name $runId -Value $vmIds 
#> 
 
#Example: How to retrieve information from a variable set during the pre-script 
<# 
$variable = Get-AutomationVariable -Name $runId 
#>      
```

## <a name="interacting-with-machines"></a>Gépek használata

Egy runbook az Automation-fiókban, és nem közvetlenül a központi telepítésben a gépeken futtatása előtti és utáni feladatokat. Előtti és utáni feladatokat is Azure környezetében futnak, és nem fér hozzá a nem Azure-beli gépek. A következő szakaszok bemutatják, hogyan kezelheti a gépek közvetlenül,-e egy Azure virtuális Gépen vagy egy nem Azure-beli gép:

### <a name="interacting-with-azure-machines"></a>Azure-beli gépek használata

Előtti és utáni feladatok runbookként futott, natív módon nem futnak az Azure virtuális gépeken a központi telepítésben. Együttműködik az Azure-beli virtuális gépek, a következőkkel kell rendelkeznie:

* Futtató fiók
* A futtatni kívánt runbook

Azure-beli gépek kommunikál, használjon a [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) parancsmag használatával kommunikálhat az Azure-beli virtuális gépek. Ehhez egy példát, tekintse meg a runbook példát [az Update Management - parancsfájl futtatása paranccsal futtassa](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Nem Azure-gépek használata

Előtti és utáni feladatokat Azure környezetében futnak, és nem fér hozzá a nem Azure-beli gépek. Együttműködhet a nem Azure-gépeken, a következőkkel kell rendelkeznie:

* Futtató fiók
* Hibrid Runbook-feldolgozó telepítve a számítógépen
* Helyileg futtatni kívánt runbook
* Szülő runbook

Nem Azure-gépek kommunikál, a szülő runbook fut Azure környezetben. Ez a forgatókönyv a gyermek runbookot hívja a [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) parancsmagot. Meg kell adnia a `-RunOn` paramétert, és adja meg a parancsfájl futtatása hibrid Runbook-feldolgozó neve. Ehhez egy példát, tekintse meg a runbook példát [Update Management - szkript futtatása helyi](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Javítás üzembe helyezés megszakítása

A előtti parancsprogram hibát ad vissza, ha szüksége lehet a központi telepítés megszakítására. Ehhez meg kell [throw](/powershell/module/microsoft.powershell.core/about/about_throw) hiba a parancsfájlban az összes logikát, amely a hibát jelentene.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```
## <a name="known-issues"></a>Ismert problémák

* Nem adhatók át objektum vagy tömb paraméterek előtti és utáni parancsfájlok használata esetén. A runbook sikertelen lesz.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyagban megtudhatja, hogyan kezelheti a frissítéseket a Windows virtuális gépek számára.

> [!div class="nextstepaction"]
> [Az Azure Windows rendszerű virtuális gépek frissítéseinek és javításainak kezelése](automation-tutorial-update-management.md)

