---
title: Változók kezelése a Azure Automationban
description: Ez a cikk azt ismerteti, hogyan használhatók változók a runbookok és a DSC-konfigurációkban.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/28/2021
ms.topic: conceptual
ms.openlocfilehash: 961dd40f7b0c17b554ff3a5d3e8e2cb5692b96fb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729388"
---
# <a name="manage-variables-in-azure-automation"></a>Változók kezelése a Azure Automationban

A változó eszközök olyan értékek, amelyek az Automation-fiók összes runbookok és DSC-konfigurációjában elérhetők. Felügyelheti őket a Azure Portal, a PowerShellből, egy runbook belül vagy egy DSC-konfigurációban.

Az Automation-változók a következő helyzetekben lehetnek hasznosak:

- Egy érték megosztása több runbookok vagy DSC-konfiguráció között.

- Egy érték megosztása több feladat között ugyanabból a runbook vagy DSC-konfigurációból.

- A runbookok vagy DSC-konfigurációk által a portálon vagy a PowerShell parancssorban használt érték kezelése. Ilyenek például a gyakori konfigurációs elemek, például a virtuálisgép-nevek, egy adott erőforráscsoport, egy AD-tartománynév és egyebek.  

Azure Automation megőrzi a változókat, és akkor is elérhetővé teszi őket, ha egy runbook vagy DSC-konfiguráció meghibásodik. Ez a viselkedés lehetővé teszi, hogy egy runbook vagy DSC-konfiguráció olyan értéket állítson be, amelyet egy másik runbook használ, vagy ha a következő futtatásakor ugyanaz a runbook vagy DSC-konfiguráció szerepel.

Azure Automation az egyes titkosított változókat biztonságosan tárolja. Változó létrehozásakor megadhatja a titkosítását és tárolását Azure Automation biztonságos eszközként. A változó létrehozása után nem változtathatja meg a titkosítási állapotát a változó újbóli létrehozása nélkül. Ha rendelkezik olyan Automation-fiókkal, amely még nem titkosított bizalmas adatokat tárol, akkor törölnie kell őket, és újra létre kell hoznia őket titkosított változókként. Azure Security Center javasoljuk, hogy titkosítsa az összes Azure Automation változót az [Automation-fiók változói](../../security-center/recommendations-reference.md#recs-compute)között leírt módon. Ha olyan titkosítatlan változókkal rendelkezik, amelyeket ki szeretne zárni ebből a biztonsági javaslatból, tekintse meg a mentesítési szabály létrehozásával kapcsolatos tudnivalókat [és a biztonságos pontszámokat](../../security-center/exempt-resource.md) .

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Azure Automation a kulcsot a rendszerfelügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt az Automation betölti a kulcsot Key Vault, majd a használatával titkosítja az eszközt.

## <a name="variable-types"></a>Változók típusai

Ha a Azure Portal változót hoz létre, meg kell adnia egy adattípust a legördülő listából, hogy a portál megjelenítse a megfelelő vezérlőt a változó értékének megadásához. A következő változó típusok érhetők el Azure Automationban:

* Sztring
* Egész szám
* DateTime
* Logikai
* Null

A változó nem korlátozódik a megadott adattípusra. A változót a Windows PowerShell használatával kell beállítani, ha más típusú értéket szeretne megadni. Ha a jelzést adja meg `Not defined` , a változó értéke null. Az értéket a [set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) parancsmaggal vagy a belső `Set-AutomationVariable` parancsmaggal kell beállítani. A runbookok az `Set-AutomationVariable` Azure-beli homokozó környezetében, vagy egy Windows hibrid Runbook-feldolgozón való futtatásra szánt eszköz.

Nem használhatja a Azure Portal egy összetett változó típusának értékének létrehozására vagy módosítására. A Windows PowerShell használatával azonban bármilyen típusú értéket megadhat. Az összetett típusokNewtonsoft.Jsként lesznek beolvasva [ . A LINQ. JProperty](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) egy összetett objektumtípust PSObject típusú [pscustomobject formájában kapja](/dotnet/api/system.management.automation.pscustomobject)helyett.

Tömb vagy szórótábla létrehozásával és a változóba való mentésével több értéket is tárolhat egyetlen változóban.

>[!NOTE]
>A virtuális gépek nevének változói legfeljebb 80 karakterből állhatnak. Az erőforráscsoport-változók legfeljebb 90 karakterből állhatnak. Lásd: [Az Azure-erőforrások elnevezési szabályai és korlátozásai](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>A változók eléréséhez szükséges PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok automatizálási változókat hoznak létre és kezelhetnek a PowerShell használatával. Az az [modulok](modules.md#az-modules)részét képezik.

| Parancsmag | Leírás |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Egy létező változó értékét kérdezi le. Ha az érték egy egyszerű típus, akkor a rendszer ugyanazt a típust kéri le. Összetett típus esetén a `PSCustomObject` rendszer beolvassa a típust. <sup>1</sup>|
|[Új – AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Létrehoz egy új változót, és beállítja annak értékét.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Eltávolít egy meglévő változót.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Beállítja egy létező változó értékét. |

<sup>1</sup> a parancsmag nem használható titkosított változó értékének lekéréséhez. Ennek egyetlen módja, ha a belső `Get-AutomationVariable` parancsmagot használja egy runbook vagy DSC-konfigurációban. Ha például egy titkosított változó értékét szeretné megtekinteni, létrehozhat egy runbook a változó beszerzéséhez, majd a kimeneti adatfolyamba írja a következőt:

```powershell
$encryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $encryptvar"
```

## <a name="internal-cmdlets-to-access-variables"></a>A változók eléréséhez szükséges belső parancsmagok

A következő táblázatban található belső parancsmagok a runbookok és a DSC-konfigurációk változóinak elérésére szolgálnak. Ezek a parancsmagok a globális modulhoz tartoznak `Orchestrator.AssetManagement.Cmdlets` . További információ: [belső parancsmagok](modules.md#internal-cmdlets).

| Belső parancsmag | Leírás |
|:---|:---|
|`Get-AutomationVariable`|Egy létező változó értékét kérdezi le.|
|`Set-AutomationVariable`|Beállítja egy létező változó értékét.|

> [!NOTE]
> Kerülje a változók használatát a `Name` parancsmag paraméterében `Get-AutomationVariable` egy RUNBOOK vagy DSC-konfigurációban. A változók használata megnehezítheti a runbookok és az Automation változók közötti függőségek felderítését a tervezési időszakban.

## <a name="python-functions-to-access-variables"></a>A Python függvények a változók eléréséhez

A következő táblázatban szereplő függvények a Python 2 és 3 runbook lévő változók elérésére szolgálnak. A Python 3 runbookok jelenleg előzetes verzióban érhető el.

|Python-függvények|Leírás|
|:---|:---|
|`automationassets.get_automation_variable`|Egy létező változó értékét kérdezi le. |
|`automationassets.set_automation_variable`|Beállítja egy létező változó értékét. |

> [!NOTE]
> Az `automationassets` Asset functions eléréséhez importálnia kell a modult a Python-runbook tetején.

## <a name="create-and-get-a-variable"></a>Változó létrehozása és lekérése

>[!NOTE]
>Ha el szeretné távolítani egy változó titkosítását, törölnie kell a változót, és újból létre kell hoznia a titkosítást.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Változó létrehozása és lekérése a Azure Portal használatával

1. Az Automation-fiókban a bal oldali ablaktáblán válassza a **változók** elemet a **megosztott erőforrások** területen.
2. A **változók** lapon válassza a **változó hozzáadása** elemet.
3. Fejezze be a beállításokat az **új változó** lapon, majd válassza a **Létrehozás** lehetőséget az új változó mentéséhez.

> [!NOTE]
> Ha mentett egy titkosított változót, azt a portálon nem lehet megtekinteni. Csak a frissítésre használható.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Változó létrehozása és lekérése a Windows PowerShellben

A runbook vagy DSC-konfiguráció a `New-AzAutomationVariable` parancsmaggal létrehoz egy új változót, és beállítja a kezdeti értékét. Ha a változó titkosítva van, a hívásnak a `Encrypted` paramétert kell használnia. A szkript a paranccsal kérheti le a változó értékét `Get-AzAutomationVariable` .

>[!NOTE]
>Egy PowerShell-parancsfájl nem tud beolvasni egy titkosított értéket. Ennek egyetlen módja a belső `Get-AutomationVariable` parancsmag használata.

Az alábbi példa bemutatja, hogyan hozhat létre egy karakterlánc-változót, majd visszaadja az értékét.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"
$variableValue = "My String"

New-AzAutomationVariable -ResourceGroupName $rgName –AutomationAccountName $accountName –Name "MyStringVariable" –Encrypted $false –Value $variableValue
$string = (Get-AzAutomationVariable -ResourceGroupName $rgName -AutomationAccountName $accountName –Name "MyStringVariable").Value
```

Az alábbi példa bemutatja, hogyan hozhat létre egy összetett típusú változót, majd hogyan kérheti le a tulajdonságait. Ebben az esetben a [Get-AzVM virtuálisgép-](/powershell/module/Az.Compute/Get-AzVM) objektum a tulajdonságok részhalmazának megadására szolgál.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$vm = Get-AzVM -ResourceGroupName $rgName –Name "VM01" | Select Name, Location, Tags
New-AzAutomationVariable -ResourceGroupName $rgName –AutomationAccountName $accountName –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName $rgName –AutomationAccountName $accountName –Name "MyComplexVariable"

$vmName = $vmValue.Value.Name
$vmTags = $vmValue.Value.Tags
```

## <a name="textual-runbook-examples"></a>Szöveges runbook-példák

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példa bemutatja, hogyan állíthat be és kérhet le egy változót egy szöveges runbook. Ez a példa feltételezi, hogy a **numberOfIterations** és a **numberOfRunnings** nevű egész szám változót és egy **sampleMessage** nevű karakterlánc-változót hoz létre.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$numberOfIterations = Get-AutomationVariable -Name "numberOfIterations"
$numberOfRunnings = Get-AutomationVariable -Name "numberOfRunnings"
$sampleMessage = Get-AutomationVariable -Name "sampleMessage"

Write-Output "Runbook has been run $numberOfRunnings times."

for ($i = 1; $i -le $numberOfIterations; $i++) {
    Write-Output "$i`: $sampleMessage"
}
Set-AutomationVariable –Name numberOfRunnings –Value ($numberOfRunnings += 1)
```

# <a name="python-2"></a>[Python 2](#tab/python2)

A következő minta bemutatja, hogyan lehet változót beolvasni, változót beállítani és kezelni egy nem létező változót egy Python 2 runbook.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

# <a name="python-3"></a>[Python 3](#tab/python3)

Az alábbi példa bemutatja, hogyan lehet változót beolvasni, változót beállítani és egy nem létező változóhoz tartozó kivételt kezelni egy Python 3 runbook (előzetes verzió).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print ("variable not found")
```

---

## <a name="graphical-runbook-examples"></a>Grafikus runbook-példák

Grafikus runbook a **Get-AutomationVariable** vagy a **set-AutomationVariable** belső parancsmagokhoz is hozzáadhat tevékenységeket. Csak kattintson a jobb gombbal az egyes változókra a grafikus szerkesztő könyvtár paneljén, és válassza ki a kívánt tevékenységet.

![Változó hozzáadása a vászonhoz](../media/variables/runbook-variable-add-canvas.png)

Az alábbi képen egy egyszerű értékkel rendelkező változó egy grafikus runbook való frissítését bemutató tevékenységek láthatók. Ebben a példában a tevékenység `Get-AzVM` egy egyetlen Azure-beli virtuális gép lekérésére és a számítógép nevének egy meglévő Automation string változóba való mentésére irányul. Nem számít, hogy a [hivatkozás egy folyamat vagy egy folyamat](../automation-graphical-authoring-intro.md#use-links-for-workflow) , mert a kód csak egyetlen objektumot vár a kimenetben.

![Egyszerű változó beállítása](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a változók eléréséhez használt parancsmagokról, tekintse meg a [modulok kezelése a Azure Automationban](modules.md)című témakört.

- A runbookok kapcsolatos általános információkért lásd: [a Runbook végrehajtása Azure Automation](../automation-runbook-execution.md).

- A DSC-konfigurációk részletes ismertetését lásd: [Azure Automation állapot konfigurációjának áttekintése](../automation-dsc-overview.md).
