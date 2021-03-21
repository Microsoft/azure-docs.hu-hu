---
title: Moduláris runbookok létrehozása Azure Automation
description: Ez a cikk azt ismerteti, hogyan hozható létre olyan runbook, amelyet más runbook hívnak.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: f0dd5cf5209924972080af6d22429252338754de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491248"
---
# <a name="create-modular-runbooks"></a>Moduláris runbookok létrehozása

Ajánlott eljárás a Azure Automation az újrafelhasználható, moduláris runbookok és más runbookok által hívott diszkrét függvények írására. A szülő runbook gyakran egy vagy több alárendelt runbookok hív meg a szükséges funkciók elvégzéséhez. 

A gyermek runbook kétféleképpen hívható meg, és eltérő különbségek vannak, amelyeket érdemes megállapítani, hogy melyik a legmegfelelőbb a forgatókönyvekhez. Az alábbi táblázat összefoglalja, hogy az egyik runbook hogyan hívható meg egymástól.

|  | Beágyazott | Parancsmag |
|:--- |:--- |:--- |
| **Feladat** |A gyermekrunbookok a szülővel azonos feladatban futnak. |A rendszer külön feladatot hoz létre a gyermekrunbookhoz. |
| **Végrehajtási** |A folytatás előtt a szülőrunbook megvárja, hogy a gyermekrunbook befejeződjön. |A szülő runbook közvetlenül a gyermek runbook elindítása után folytatódik, *vagy* a szülő runbook megvárja, amíg befejeződik a gyermek feladata. |
| **Kimenet** |A szülőrunbook közvetlenül lekérheti a gyermekrunbook kimenetét. |A szülő runbook le kell kérnie a gyermek runbook-feladatokból származó kimenetet *, vagy* a szülő runbook közvetlenül a gyermek runbook származó kimenetet kaphat. |
| **Paraméterek** |A gyermekrunbook paramétereinek értékeit külön kell meghatározni, és bármilyen adattípus használható. |A gyermek runbook paramétereinek értékeit egyetlen szórótábla kell egyesíteni. Ez a szórótábla csak olyan egyszerű, tömb és objektum típusú adattípusokat tartalmazhat, amelyek JSON-szerializálást használnak. |
| **Automation-fiók** |A szülő runbook csak azonos Automation-fiókban használhatja a gyermek runbook. |A szülő runbookok bármely Automation-fiókból, ugyanabból az Azure-előfizetésből, illetve egy másik előfizetésből származó gyermek runbook is használhat, amelyhez kapcsolódik. |
| **Közzététel** |A gyermekrunbookot a szülőrunbook közzététele előtt kell közzétenni. |A gyermek runbook a szülő runbook elindítása előtt bármikor közzé lesz téve. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Gyermek runbook meghívása beágyazott végrehajtás használatával

Ha egy runbook egy másik runbook szeretne meghívni, használja a runbook nevét, és adja meg a paramétereinek értékeit, ugyanúgy, mint egy tevékenységet vagy egy parancsmagot. Az azonos Automation-fiók összes runbookok elérhetővé válik minden más számára, hogy ily módon lehessen használni. A szülő runbook megvárja a gyermek runbook befejeződését, mielőtt továbblép a következő sorra, és minden kimenet közvetlenül a szülőhöz tér vissza.

A beágyazottan indított runbook ugyanazt a feladatot futtatja, mint a szülőrunbook. A gyermek runbook feladatainak előzményei nem szerepelnek a feladatokban. A gyermek runbook származó kivételek és stream-kimenetek a szülőhöz vannak társítva. Ez a viselkedés kevesebb feladatot eredményez, és megkönnyíti a nyomon követést és a hibakeresést.

Runbook közzétételekor az általa megjelenő összes gyermek runbookok már közzé kell tenni. Ennek az az oka, hogy Azure Automation összeállítja az összes gyermek runbookok való társítást, amikor lefordít egy runbook. Ha a gyermek runbookok még nem tették közzé, úgy tűnik, hogy a fölérendelt runbook megfelelően közzé van téve, de kivételt hoz létre a indításakor. Ha ez történik, újra közzéteheti a szülő runbook, hogy megfelelően hivatkozzon a gyermek runbookok. A fölérendelt runbook nem kell újból közzétennie, ha a gyermek runbook módosul, mert a társítás már létrejött.

A inline nevű alárendelt runbook paramétereinek bármilyen adattípusa lehet, beleértve az összetett objektumokat is. Nincs JSON- [szerializálás](start-runbooks.md#work-with-runbook-parameters), mert a Azure Portal vagy a [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) parancsmaggal indítja el a runbook.

### <a name="runbook-types"></a>Runbook-típusok

Mely runbook-típusok hívhatják egymást?

* Egy [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) és egy [grafikus runbook](automation-runbook-types.md#graphical-runbooks) is meghívhat egymásba, mivel mindkettő PowerShell-alapú.
* A [PowerShell-munkafolyamatok runbook](automation-runbook-types.md#powershell-workflow-runbooks) és a grafikus PowerShell-munkafolyamat runbook meghívhatják egymást, mivel mindkettő PowerShell-munkafolyamat-alapú.
* A PowerShell-típusok és a PowerShell-munkafolyamatok típusai nem hívhatják egymást, és a-t kell használniuk `Start-AzAutomationRunbook` .

Mikor teszi közzé a sorrendet?

A runbookok közzétételi sorrendje csak a PowerShell-munkafolyamatok és a grafikus PowerShell-munkafolyamat runbookok esetében számít.

Ha a runbook a beágyazott végrehajtás használatával meghívja a grafikus vagy a PowerShell-munkafolyamatok alárendelt runbook, a a runbook nevét használja. A névnek a következővel kell kezdődnie, hogy `.\\` Megadja, hogy a parancsfájl a helyi könyvtárban található.

### <a name="example"></a>Példa

A következő példa egy teszt gyermek runbook indít el, amely egy összetett objektumot, egy egész értéket és egy logikai értéket fogad el. A rendszer egy változóhoz társítja a gyermekrunbook kimenetét. Ebben az esetben a gyermek runbook egy PowerShell-munkafolyamat runbook.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Ez ugyanaz a példa, amely egy PowerShell-runbook használja a gyermekként.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Gyermek runbook elindítása parancsmag használatával

> [!IMPORTANT]
> Ha a runbook egy gyermek runbook hív meg a (z) `Start-AzAutomationRunbook` `Wait` paraméterrel, és a gyermek runbook létrehoz egy objektumot, a művelet hibába ütközhet. A hiba megoldásához tekintse meg a [gyermek runbookok az objektum kimenetével](troubleshoot/runbooks.md#child-runbook-object) című témakört, amelyből megtudhatja, hogyan implementálhatja a logikát az eredmények lekérdezésére a [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) parancsmag használatával.

A segítségével `Start-AzAutomationRunbook` elindíthat egy runbook a [Runbook Windows PowerShell használatával történő elindításához](start-runbooks.md#start-a-runbook-with-powershell)című témakörben leírtak szerint. Ehhez a parancsmaghoz két mód használható. Egy módban a parancsmag a feladattípust adja vissza, ha a rendszer létrehozza a feladatot a gyermek runbook. A másik mód, amelyet a parancsfájl a *WAIT* paraméter megadásával engedélyez, a parancsmag addig vár, amíg a gyermek feladata be nem fejeződik, és visszaadja a gyermek runbook kimenetét.

A-parancsmaggal elindított runbook-feladatok a szülő runbook-feladatoktól függetlenül futnak. Ez a viselkedés több feladatot eredményez, mint a runbook inline elindítása, és nehezebbé teszi a feladatok nyomon követését. A szülő több alárendelt runbook is indíthat aszinkron módon anélkül, hogy az egyes műveletek befejezésére kellene várnia. Ahhoz, hogy ez a párhuzamos végrehajtás meghívja a alárendelt runbookok, a szülő runbook a [Parallel kulcsszót](automation-powershell-workflow.md#use-parallel-processing)kell használnia.

A gyermek runbook kimenete az időzítés miatt nem megbízhatóan tér vissza a szülő runbook. Emellett `$VerbosePreference` Előfordulhat, hogy a, a, `$WarningPreference` és mások változói nem lesznek propagálva a gyermek runbookok. Ezen problémák elkerülése érdekében a gyermek runbookok különálló Automation-feladatokként is elindíthatja `Start-AzAutomationRunbook` a `Wait` paraméterrel. Ez a módszer megakadályozza a szülő runbook, amíg a gyermek runbook be nem fejeződik.

Ha nem szeretné, hogy a szülő runbook le legyen tiltva a várakozás közben, a runbook a paraméter nélkül is elindíthatja `Start-AzAutomationRunbook` `Wait` . Ebben az esetben a runbook a [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) használatával kell várnia a feladatok befejezését. A [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) és a [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) használatával is le kell kérni az eredményeket.

Egy parancsmaggal elindított alárendelt runbook paramétereinek megadása szórótábla, a [runbook paraméterben](start-runbooks.md#work-with-runbook-parameters)leírtak szerint. Csak az egyszerű adattípusokat lehet használni. Ha a runbook rendelkezik összetett adattípusú paraméterrel, beágyazottan kell meghívni.

Előfordulhat, hogy az előfizetési környezet elvész, amikor elindítják a gyermek runbookok különálló feladatként. Ahhoz, hogy a gyermek runbook az az Module-parancsmagok egy adott Azure-előfizetéssel való végrehajtásához, a gyermeknek a szülő runbook függetlenül hitelesítenie kell ezt az előfizetést.

Ha ugyanabban az Automation-fiókban lévő feladatok egynél több előfizetéssel működnek, az egyik feladatban szereplő előfizetés kiválasztásával megváltoztathatja a jelenleg kijelölt előfizetési környezetet más feladatokhoz. Ha el szeretné kerülni ezt a helyzetet, használja `Disable-AzContextAutosave –Scope Process` az egyes runbook elején. Ez a művelet csak a környezetet menti a runbook végrehajtásához.

### <a name="example"></a>Példa

A következő példa egy gyermek runbook indít el paraméterekkel, majd megvárja, amíg a `Start-AzAutomationRunbook` parancsmagot a paraméterrel elvégezte `Wait` . Ha elkészült, a példa a gyermek runbook származó parancsmag kimenetét gyűjti. A használatához `Start-AzAutomationRunbook` a szkriptnek hitelesítenie kell magát az Azure-előfizetésében.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Set-AzContext -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="next-steps"></a>Következő lépések

* A runbook futtatásához tekintse meg [a Runbook elindítása a Azure Automationben](start-runbooks.md)című témakört.
* A runbook művelet figyeléséhez tekintse meg a következő témakört: [runbook output and messages in Azure Automation](automation-runbook-output-and-messages.md).
