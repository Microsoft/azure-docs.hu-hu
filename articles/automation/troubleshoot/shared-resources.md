---
title: Megosztott Azure Automation erőforrásokkal kapcsolatos problémák elhárítása
description: Ez a cikk bemutatja, hogyan háríthatja el és háríthatja el a Azure Automation erőforrásokkal kapcsolatos problémákat.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b497b0a8f34b4310e3f11beed982c4453fc79159
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830825"
---
# <a name="troubleshoot-shared-resource-issues"></a>Megosztott erőforrásokkal kapcsolatos problémák elhárítása

Ez a cikk olyan problémákat tárgyal, amelyek akkor merülhetnek fel, ha megosztott [erőforrásokat](../automation-intro.md#shared-resources) használ a Azure Automation.

## <a name="modules"></a>Modulok

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Forgatókönyv: Egy modul elakadt az importálás során

#### <a name="issue"></a>Probléma

A modul importálási  állapotban maradt a modul importálása vagy frissítése Azure Automation során.

#### <a name="cause"></a>Ok

Mivel a PowerShell-modulok importálása összetett, többlépcsős folyamat, előfordulhat, hogy egy modul importálása nem megfelelő, és átmeneti állapotban elakadhat. További információ az importálási folyamatról: [PowerShell-modul importálása.](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)

#### <a name="resolution"></a>Feloldás

A probléma megoldásához el kell távolítania a [Remove-AzAutomationModule](/powershell/module/Az.Automation/Remove-AzAutomationModule) parancsmag használatával elakadt modult. Ezután újra megpróbálhatja importálni a modult.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Forgatókönyv: Az AzureRM-modulok elakadtak az importálás során a frissítési kísérlet után

#### <a name="issue"></a>Probléma

Az AzureRM-modulok frissítésének kipróbálása után a fiókjában megmarad egy szalagcím, amely a következő üzenetet tartalmazza:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Ok

Az AzureRM-modulok Automation-fiókban való frissítésének egy ismert problémája van. A probléma akkor merül fel, ha a modulok egy 0-val kezdődő numerikus névvel kezdődő erőforráscsoportban vannak.

#### <a name="resolution"></a>Feloldás

Az Automation-fiókban az AzureRM-modulok frissítéséhez a fióknak alfanumerikus nevű erőforráscsoportban kell lennie. A 0-val kezdődő numerikus névvel kezdődő erőforráscsoportok jelenleg nem tudják frissíteni az AzureRM-modulokat.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Forgatókönyv: A modul importálása sikertelen, vagy a parancsmagok nem hajthatóak végre az importálás után

#### <a name="issue"></a>Probléma

Egy modul importálása sikertelen, vagy sikeresen importálható, de nem lesz kinyerve parancsmag.

#### <a name="cause"></a>Ok

Néhány gyakori oka annak, hogy egy modul nem importálható sikeresen Azure Automation:

* A struktúra nem felel meg az Automation által szükséges struktúrának.
* A modul egy másik modultól függ, amely még nem lett üzembe helyezni az Automation-fiókjában.
* A modul függőségei hiányoznak a mappából.
* A modul feltöltéséhez a [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) parancsmagot használja a rendszer, és nem adott meg teljes tárolási útvonalat, vagy nem töltötte be a modult nyilvánosan elérhető URL-cím használatával.

#### <a name="resolution"></a>Feloldás

A probléma megoldásához használja az alábbi megoldások bármelyikét:

* Győződjön meg arról, hogy a modul a következő formátumot követi: ModuleName.zip -> ModuleName vagy Version Number -> (ModuleName.psm1, ModuleName.psd1).
* Nyissa meg **a .psd1 fájlt,** és nézze meg, hogy a modul rendelkezik-e függőségekkel. Ha igen, töltse fel ezeket a modulokat az Automation-fiókba.
* Ellenőrizze, hogy a hivatkozott **.dll** fájlok jelen vannak-e a modulmappában.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Forgatókönyv: Update-AzureModule.ps1 felfüggesztése a modulok frissítésekkor

#### <a name="issue"></a>Probléma

Ha a runbookot [ használjaUpdate-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Azure-modulok frissítéséhez, a modul frissítési folyamata fel lesz függesztve.

#### <a name="cause"></a>Ok

Ehhez a runbookhoz az alapértelmezett beállítás azt határozza meg, hogy hány modul frissül egyidejűleg: 10. A frissítési folyamat gyakran tartalmaz hibákat, ha egyszerre túl sok modult frissít.

#### <a name="resolution"></a>Feloldás

Nem gyakori, hogy minden AzureRM- vagy Az-modulra szükség van ugyanabban az Automation-fiókban. Csak a szükséges modulokat importálja.

> [!NOTE]
> Kerülje a teljes vagy `Az.Automation` modul `AzureRM.Automation` importálását, amely az összes tartalmazott modult importálja.

Ha a frissítési folyamat felfüggesztve van, adja hozzá a paramétert aUpdate-AzureModules.ps1szkripthez, és adjon meg egy alacsonyabb értéket, mint `SimultaneousModuleImportJobCount` az alapértelmezett  10. Ha ezt a logikát implementálja, próbálja meg a 3-as vagy az 5-ös értékkel kezdődik. `SimultaneousModuleImportJobCount` Az az **Update-AutomationAzureModulesForAccount** rendszer runbook paramétere, amely az Azure-modulok frissítésére szolgál. Ha ezt a módosítást végrehajtja, a frissítési folyamat tovább fut, de nagyobb az esélye annak, hogy befejeződik. Az alábbi példa bemutatja a paramétert és azt, hogy hová kell azt a runbookba tenni:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Futtató fiókok

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Forgatókönyv: Nem lehet létrehozni vagy frissíteni egy run as-fiókot

#### <a name="issue"></a>Probléma

Amikor megpróbál létrehozni vagy frissíteni egy run as-fiókot, az alábbihoz hasonló hibaüzenet jelenik meg:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Ok

Nem rendelkezik a szükséges engedélyekkel a futó fiók létrehozásához vagy frissítéséhez, vagy az erőforrás zárolva van az erőforráscsoport szintjén.

#### <a name="resolution"></a>Feloldás

A futó fiókok létrehozásához vagy frissítéséhez [](../automation-security-overview.md#permissions) megfelelő engedélyekkel kell rendelkeznie a run as-fiók által használt különböző erőforrásokhoz.

Ha a problémát egy zárolás jelenti, ellenőrizze, hogy a zárolás eltávolítható-e. Ezután válassza a zárolt erőforrást a Azure Portal, kattintson a jobb gombbal a zárolásra, és válassza a **Törlés lehetőséget.**

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Forgatókönyv: Runbook végrehajtásakor a "Nem található egy GetPerAdapterInfo" nevű belépési pont a iplpapi.dll DLL-ben" hibaüzenet jelenik meg

#### <a name="issue"></a>Probléma

Runbook végrehajtásakor a következő kivételt kapja:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Ok

Ezt a hibát valószínűleg egy helytelenül konfigurált run [as-fiók okozza.](../automation-security-overview.md)

#### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a fiók megfelelően van konfigurálva. Ezután ellenőrizze, hogy a megfelelő kód van-e a runbookban az Azure-ral való hitelesítéshez. Az alábbi példa egy kódrészletet mutat be az Azure-ban való hitelesítéshez egy runbookban egy run as-fiók használatával.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Következő lépések

Ha ez a cikk nem oldja meg a problémát, további támogatásért próbálkozzon az alábbi csatornák egyikével:

* Azure-szakértőktől kaphat választ az [Azure fórumain.](https://azure.microsoft.com/support/forums/)
* Csatlakozzon a [@AzureSupport](https://twitter.com/azuresupport) hez. Ez a hivatalos Microsoft Azure, hogy az Azure-közösséget a megfelelő forrásokhoz– válaszokhoz, támogatáshoz és szakértőkhez – kapcsolják össze.
* Incidens Azure-támogatás be. A webhelyre [Azure-támogatás,](https://azure.microsoft.com/support/options/)és válassza a **Támogatás lekérte lehetőséget.**
