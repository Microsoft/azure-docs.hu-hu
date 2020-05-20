---
title: A megosztott erőforrásokkal kapcsolatos problémák elhárítása Azure Automation
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a Azure Automation megosztott erőforrásokkal kapcsolatos problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5b87a98ed38e3af315789adffc11824f2522b802
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680885"
---
# <a name="troubleshoot-shared-resource-issues"></a>Megosztott erőforrásokkal kapcsolatos problémák elhárítása

Ez a cikk a Azure Automation [megosztott erőforrásainak](../automation-intro.md#shared-resources) használatakor felmerülő problémákat ismerteti.

## <a name="modules"></a>Modulok

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Forgatókönyv: A modul az importálás során beragadt

#### <a name="issue"></a>Probléma

A modul az *importálási* állapotba kerül, amikor importálja vagy frissíti a Azure Automation modulokat.

#### <a name="cause"></a>Ok

Mivel a PowerShell-modulok importálása összetett, többlépéses folyamat, előfordulhat, hogy egy modul nem importál megfelelően, és átmeneti állapotba kerülhet. További információ az importálási folyamatról: [PowerShell-modul importálása](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához el kell távolítania a [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) parancsmag használatával beragadt modult. Ezután újra megpróbálkozhat a modul importálásával.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Forgatókönyv: a AzureRM modulok az importálás során elakadnak a frissítési kísérlet után

#### <a name="issue"></a>Probléma

A AzureRM modulok frissítése után a következő üzenettel rendelkező szalagcím a fiókban marad:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Ok

Ismert hiba történt a AzureRM-modulok automatizálási fiókban való frissítésekor. A probléma különösen akkor fordul elő, ha a modulok egy 0 értékkel kezdődő numerikus névvel rendelkező erőforráscsoporthoz tartoznak.

#### <a name="resolution"></a>Megoldás:

Az Automation-fiókban lévő AzureRM-modulok frissítéséhez a fióknak alfanumerikus névvel rendelkező erőforráscsoporthoz kell tartoznia. A 0 értékkel kezdődő numerikus nevekkel rendelkező erőforráscsoportok jelenleg nem tudják frissíteni a AzureRM modulokat.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Forgatókönyv: a modul importálása sikertelen, vagy a parancsmagok importálás után nem hajthatók végre.

#### <a name="issue"></a>Probléma

Egy modul nem importálható, vagy az importálás sikeresen megtörtént, de a rendszer nem nyer ki parancsmagokat.

#### <a name="cause"></a>Ok

Néhány gyakori ok, amiért előfordulhat, hogy egy modul nem importálható sikeresen Azure Automationre:

* A struktúra nem felel meg az Automation által igényelt struktúrának.
* A modul egy másik, az Automation-fiókba nem telepített modultól függ.
* A modulban hiányzik a függőségei a mappában.
* A [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) parancsmag a modul feltöltésére használatos, és nem biztosította a teljes tárterület elérési útját, vagy nem töltötte be a modult nyilvánosan elérhető URL-cím használatával.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához használja a következő megoldásokat:

* Győződjön meg arról, hogy a modul a következő formátumot követi: ModuleName. zip-> ModuleName vagy Version Number-> (ModuleName. psm1, ModuleName. psd1).
* Nyissa meg a **. psd1** fájlt, és ellenőrizze, hogy a modul rendelkezik-e függőségekkel. Ha igen, töltse fel ezeket a modulokat az Automation-fiókba.
* Győződjön meg arról, hogy minden hivatkozott **. dll** fájl megtalálható a modul mappában.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Forgatókönyv: a Update-AzureModule. ps1 felfüggeszti a modulok frissítésekor

#### <a name="issue"></a>Probléma

Ha az [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook használja az Azure-modulok frissítésére, a rendszer felfüggeszti a modul frissítési folyamatát.

#### <a name="cause"></a>Ok

Ebben a runbook az alapértelmezett beállítás azt határozza meg, hogy hány modult frissít egyszerre a 10. A frissítési folyamat olyan hibákhoz van kitéve, amikor túl sok modul frissül egyidejűleg.

#### <a name="resolution"></a>Megoldás:

Nem gyakori, hogy az összes AzureRM vagy az az modulra ugyanarra az Automation-fiókra van szükség. Csak a szükséges modulokat kell importálnia.

> [!NOTE]
> Ne importálja a teljes `Az.Automation` vagy a `AzureRM.Automation` modult, amely az összes befoglalt modult importálja.

Ha a frissítési folyamat felfüggeszti a műveletet, adja hozzá a `SimultaneousModuleImportJobCount` paramétert a **Update-AzureModules. ps1** parancsfájlhoz, és adjon meg egy alacsonyabb értéket, mint az alapértelmezett 10. Ha ezt a logikát alkalmazza, próbálja meg 3 vagy 5 értékkel kezdeni. `SimultaneousModuleImportJobCount`az az **Update-AutomationAzureModulesForAccount** System runbook paraméter, amely az Azure-modulok frissítésére szolgál. Ha ezt a beállítást választja, a frissítési folyamat tovább fut, de nagyobb eséllyel fejeződik be. A következő példa a paramétert mutatja be, és hová helyezi a runbook:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Forgatókönyv: nem lehet futtató fiókot létrehozni vagy frissíteni

#### <a name="issue"></a>Probléma

Amikor megpróbál létrehozni vagy frissíteni egy futtató fiókot, a következőhöz hasonló hibaüzenet jelenik meg:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Ok

Nem rendelkezik a futtató fiók létrehozásához vagy frissítéséhez szükséges engedélyekkel, vagy az erőforrás zárolva van egy erőforráscsoport szintjén.

#### <a name="resolution"></a>Megoldás:

Futtató fiók létrehozásához vagy frissítéséhez megfelelő [engedélyekkel](../manage-runas-account.md#permissions) kell rendelkeznie a futtató fiók által használt különféle erőforrásokhoz. 

Ha a probléma egy zárolás miatt van, ellenőrizze, hogy a zárolást el lehet-e távolítani. Ezután lépjen a Azure Portal zárolt erőforrásra, kattintson a jobb gombbal a zárolásra, majd válassza a **Törlés**lehetőséget.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Forgatókönyv: "nem található" GetPerAdapterInfo "nevű belépési pont a (z) iplpapi. dll FÁJLBAN a runbook végrehajtásakor

#### <a name="issue"></a>Probléma

Runbook végrehajtásakor a következő kivétel jelenik meg:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Ok

Ezt a hibát valószínűleg egy helytelenül konfigurált [futtató fiók](../manage-runas-account.md)okozza.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a futtató fiók megfelelően van konfigurálva. Ezután ellenőrizze, hogy rendelkezik-e a megfelelő kóddal a runbook az Azure-beli hitelesítéshez. Az alábbi példa egy kódrészletet mutat be az Azure-ban való hitelesítéshez egy runbook egy futtató fiók használatával.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>További lépések

Ha ez a cikk nem oldja meg a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) . Ez a hivatalos Microsoft Azure fiók az Azure-Közösség megfelelő erőforrásokhoz való csatlakoztatásához: válaszok, támogatás és szakértők.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése**lehetőséget.

