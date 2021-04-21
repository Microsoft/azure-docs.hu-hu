---
title: Moduláris runbookok létrehozása a Azure Automation
description: Ez a cikk bemutatja, hogyan hozhat létre egy másik runbook által hívott runbookot.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 101ff9affe43dcc97de6cf5a535c82559aafeced
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834911"
---
# <a name="create-modular-runbooks"></a>Moduláris runbookok létrehozása

Ajánlott eljárás az Azure Automation újrahasználható, moduláris runbookok írása olyan különálló függvényekkel, amelyet más runbookok hívnak meg. A szülő runbookok gyakran egy vagy több gyermek runbookot hívnak meg a szükséges funkciók végrehajtásához. 

A gyermek runbookok hívásának két módja van, és különböző különbségeket kell megértenie, hogy megállapítsa, melyik a legmegfelelőbb az adott forgatókönyvhöz. Az alábbi táblázat összefoglalja az egyik runbook másik forgatókönyvből való hívásának két módja közötti különbségeket.

|  | Beágyazott | Parancsmag |
|:--- |:--- |:--- |
| **Feladat** |A gyermekrunbookok a szülővel azonos feladatban futnak. |A rendszer külön feladatot hoz létre a gyermekrunbookhoz. |
| **Futtatási** |A folytatás előtt a szülőrunbook megvárja, hogy a gyermekrunbook befejeződjön. |A szülő runbook azonnal folytatódik, miután a gyermek runbook elindult, vagy a szülő runbook megvárja, amíg a gyermek feladat befejeződik.  |
| **Kimenet** |A szülőrunbook közvetlenül lekérheti a gyermekrunbook kimenetét. |A szülő runbooknak le kell kapnia a gyermek runbook-feladat *kimenetét,* vagy a szülő runbook közvetlenül lekérheti a gyermek runbook kimenetét. |
| **Paraméterek** |A gyermekrunbook paramétereinek értékeit külön kell meghatározni, és bármilyen adattípus használható. |A gyermek runbook paramétereinek értékeit egyetlen kivonattáblában kell kombinálni. Ez a kivonattábla csak egyszerű, tömb- és objektum-adattípusokat tartalmazhat, amelyek JSON-szerializálást használnak. |
| **Automation-fiók** |A szülő runbook csak ugyanabban az Automation-fiókban használhatja a gyermek runbookot. |A szülő runbookok bármely Automation-fiókból, ugyanattól az Azure-előfizetéstől, vagy akár egy másik előfizetéstől származó gyermek runbookot is használhatnak, amelyhez kapcsolattal rendelkezik. |
| **Közzététel** |A gyermekrunbookot a szülőrunbook közzététele előtt kell közzétenni. |A gyermek runbook a szülő runbook elindulás előtt bármikor közzé lesztéve. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Gyermek runbook meghívása beágyazott végrehajtással

Ha egy runbookot beágyazottan szeretne meghívni egy másik runbookból, használja a runbook nevét, és adjon meg értékeket a paraméterekhez, ahogy egy tevékenységet vagy egy parancsmagot is használna. Az ugyanabban az Automation-fiókban található összes runbook így használható az összes többi számára. A szülő runbook megvárja, amíg a gyermek runbook befejeződik, mielőtt továbblép a következő sorra, és minden kimenet közvetlenül a szülőnek ad vissza.

A beágyazottan indított runbook ugyanazt a feladatot futtatja, mint a szülőrunbook. A gyermek runbook feladatelőzményében nincs jelzés. A gyermek runbook kivételei és streamkimenetei a szülőhöz vannak társítva. Ez a viselkedés kevesebb feladathoz vezet, és megkönnyíti a nyomon követését és a hibaelhárítást.

Runbook közzétételekor már közzé kell tenni minden olyan gyermek runbookot, amely az adott runbookot hívja meg. Ennek az az oka Azure Automation hogy a rendszer a runbookok lefordított gyermek runbookokkal való társítását építi ki. Ha a gyermek runbookok még nem voltak közzétéve, a szülő runbook megfelelően közzéteszi a megjelenik, de kivételt hoz létre az gombra való eltöltéskor. Ebben az esetben újból közzé lehet tennie a szülő runbookot, hogy megfelelően hivatkozni tud a gyermek runbookra. Nem kell újból közzétennünk a szülő runbookot, ha bármely gyermek runbookot megváltoztatnak, mert a társítás már létrejött.

A beágyazott nevű gyermek runbook paraméterei bármilyen adattípushoz használhatók, beleértve az összetett objektumokat is. Nincs [JSON-szerializálás,](start-runbooks.md#work-with-runbook-parameters)mivel a runbookot a Azure Portal [start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) parancsmaggal indítja el.

### <a name="runbook-types"></a>Runbook-típusok

Milyen runbooktípusok hívják meg egymást?

* A [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks) és grafikus [runbookok](automation-runbook-types.md#graphical-runbooks) egymásba is hívhatja egymást, mivel mindkettő PowerShell-alapú.
* A [PowerShell-alapú munkafolyamat-runbookok](automation-runbook-types.md#powershell-workflow-runbooks) és a grafikus PowerShell-alapú munkafolyamat-runbookok egymásba is hívhatja egymást, mivel mindkettő PowerShell-alapú.
* A PowerShell-típusok és a PowerShell-munkafolyamattípusok nem hívják meg egymást beágyazottan, és a következőt kell használniuk: `Start-AzAutomationRunbook` .

Mikor számít a közzétételi sorrend?

A runbookok közzétételi sorrendje csak a PowerShell-munkafolyamatok és a grafikus PowerShell-munkafolyamat-runbookok esetén számít.

Amikor a runbook beágyazott végrehajtással hív meg egy grafikus vagy PowerShell-munkafolyamat gyermek runbookját, a runbook nevét használja. A névnek kezdetűnek kell lennie annak megadásához, hogy a szkript `.\\` a helyi könyvtárban található.

### <a name="example"></a>Példa

Az alábbi példa elindít egy teszt gyermek runbookot, amely egy összetett objektumot, egy egész szám értéket és egy logikai értéket fogad el. A rendszer egy változóhoz társítja a gyermekrunbook kimenetét. Ebben az esetben a gyermek runbook egy PowerShell-munkafolyamat forgatókönyve.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

Ez ugyanaz a példa, amely a gyermekként használt PowerShell-runbookot használja.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Gyermek runbook futtatása parancsmag használatával

> [!IMPORTANT]
> Ha a runbook meghív egy gyermek runbookot a(z) paraméterrel megadott parancsmaggal, és a gyermek runbook létrehoz egy objektum-eredményt, a művelet `Start-AzAutomationRunbook` `Wait` hibába ütközhet. A hiba a Gyermek [runbookok](troubleshoot/runbooks.md#child-runbook-object) objektumkimenettel való használatával való használatával kapcsolatos információkért tekintse meg, hogyan implementálhatja az eredményeket lekérdező logikát a [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) parancsmaggal.

A használatával `Start-AzAutomationRunbook` elindíthat egy runbookot a [következővel: Runbook](start-runbooks.md#start-a-runbook-with-powershell)Windows PowerShell. Ehhez a parancsmaghoz két mód áll rendelkezésre. Az egyik módban a parancsmag a feladat azonosítóját adja vissza, amikor létrejön a feladat a gyermek runbookhoz. A másik módban, amelyet a szkript a *Wait* paraméter megadásával tesz lehetővé, a parancsmag megvárja, amíg a gyermek feladat befejeződik, és visszaadja a gyermek runbook kimenetét.

A parancsmaggal indított gyermek runbookból származó feladat a szülő runbook feladattól elkülönítve fut. Ez a viselkedés több feladathoz vezet, mint a runbook beágyazott indítása, és megnehezíti a feladatok nyomon követését. A szülő több gyermek runbookot is elindíthat aszinkron módon anélkül, hogy megvárja, amíg mindegyik befejeződik. A gyermek runbookokat beágyazottan hívó párhuzamos végrehajtáshoz a szülő runbooknak a [párhuzamos kulcsszót kell használnia.](automation-powershell-workflow.md#use-parallel-processing)

A gyermek runbook kimenete az időzítés miatt nem tér vissza megbízhatóan a szülő runbookhoz. Emellett előfordulhat, hogy a gyermek runbookok nem propagálják az olyan változókat, mint a , a és `$VerbosePreference` `$WarningPreference` más változók. A problémák elkerülése érdekében a gyermek runbookokat külön Automation-feladatokként is elindíthatja a `Start-AzAutomationRunbook` `Wait` paraméterrel. Ez a technika letiltja a szülő runbookot, amíg a gyermek runbook be nem fejeződik.

Ha nem szeretné, hogy a szülő runbook várakozáskor le legyen tiltva, az paraméter nélkül is elindíthatja a gyermek `Start-AzAutomationRunbook` `Wait` runbookot a használatával. Ebben az esetben a runbooknak a [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) használatával kell megvárni a feladat befejezését. Emellett a [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) és [a Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) használatával kell lekérnie az eredményeket.

A parancsmaggal indított gyermek runbook paraméterei kivonattáblaként vannak megtéve, a [Runbook paramétereiben leírtak szerint.](start-runbooks.md#work-with-runbook-parameters) Csak egyszerű adattípusok használhatók. Ha a runbook rendelkezik összetett adattípusú paraméterrel, beágyazottan kell meghívni.

Az előfizetési környezet elveszhet, ha a gyermek runbookokat külön feladatként kezdi el. Ahhoz, hogy a gyermek runbook végrehajtsa az Az modul parancsmagokat egy adott Azure-előfizetésen, a gyermeknek a szülő runbooktól függetlenül hitelesítenie kell magát az előfizetésben.

Ha az ugyanabban az Automation-fiókban lévő feladatok több előfizetéssel is működnek, az egyik feladat előfizetésének kiválasztása módosíthatja a többi feladathoz jelenleg kiválasztott előfizetési környezetet. A helyzet elkerülése érdekében használja `Disable-AzContextAutosave -Scope Process` az et az egyes runbookok elejére. Ez a művelet csak a runbook végrehajtására menti a környezetet.

### <a name="example"></a>Példa

Az alábbi példa elindít egy gyermek runbookot paraméterekkel, majd megvárja, amíg befejeződik a parancsmag és a `Start-AzAutomationRunbook` paraméter `Wait` használatával. Ha befejeződött, a példa a parancsmag kimenetét gyűjti a gyermek runbookból. A használata `Start-AzAutomationRunbook` esetén a szkriptnek hitelesítenie kell magát az Azure-előfizetésében.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

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
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    -Parameters $params -Wait
```

## <a name="next-steps"></a>Következő lépések

* A runbook futtatásához [lásd: Runbook futtatása a Azure Automation.](start-runbooks.md)
* A Runbook-műveletek monitorozásával kapcsolatos további lásd: [Runbook-kimenet és -üzenetek a Azure Automation.](automation-runbook-output-and-messages.md)
