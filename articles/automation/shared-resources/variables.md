---
title: Változók kezelése a Azure Automation
description: Ez a cikk bemutatja, hogyan dolgozhat változók használatával runbookok és DSC-konfigurációkban.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9f1ace00356583dbb6102317e3d157fb58682710
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832578"
---
# <a name="manage-variables-in-azure-automation"></a>Változók kezelése a Azure Automation

A változó adateszközök olyan értékek, amelyek az Automation-fiók összes runbookja és DSC-konfigurációja számára elérhetők. Kezelheti őket a Azure Portal, a PowerShellből, egy runbookon belül vagy egy DSC-konfigurációban.

Az automatizálási változók a következő helyzetekben hasznosak:

- Érték megosztása több runbook vagy DSC-konfiguráció között.

- Érték megosztása több feladat között egy runbookból vagy DSC-konfigurációból.

- Runbookok vagy DSC-konfigurációk által használt értékek kezelése a portálról vagy a PowerShell parancssorból. Ilyen például a gyakori konfigurációelemek halmaza, például a virtuális gépek neveinek egy adott listája, egy adott erőforráscsoport, egy AD-tartománynév stb.  

Azure Automation a változókat, és akkor is elérhetővé teszi őket, ha egy runbook vagy DSC-konfiguráció meghiúsul. Ez a viselkedés lehetővé teszi, hogy egy Runbook- vagy DSC-konfiguráció beállítsa azt az értéket, amelyet aztán egy másik runbook használ, vagy amelyet ugyanaz a runbook vagy DSC-konfiguráció használ a következő futtatáskor.

Azure Automation minden titkosított változót biztonságosan tárol. Változó létrehozásakor megadhatja annak titkosítását és tárolását, ha Azure Automation biztonságos eszközként használja. A változó létrehozása után a titkosítási állapota nem változtatható meg a változó újra létrehozása nélkül. Ha olyan Automation-fiókváltozók vannak, amelyek még nem titkosított bizalmas adatokat tárolnak, akkor törölnie kell őket, és újra létre kell hoznia őket titkosított változókként. Javasoljuk Azure Security Center, hogy az Automation-fiók változóit Azure Automation összes változót [titkosítsa.](../../security-center/recommendations-reference.md#recs-compute) Ha titkosítatlan változókat szeretne kizárni ebből a biztonsági javaslatból, tekintse meg az Erőforrás kivétele [a](../../security-center/exempt-resource.md) javaslatokból és a biztonsági pontszám alól kivételszabály létrehozásához részt.

>[!NOTE]
>A biztonsági Azure Automation tartalmaznak hitelesítő adatokat, tanúsítványokat, kapcsolatokat és titkosított változókat. Ezek az eszközök titkosítva vannak, Azure Automation tárolódnak egy egyedi kulccsal, amely minden Automation-fiókhoz létrejön. Azure Automation a kulcsot a rendszer által felügyelt Key Vault. Biztonságos adateszköz tárolása előtt az Automation betölti a kulcsot a Key Vault majd a segítségével titkosítja az eszközt.

## <a name="variable-types"></a>Változótípusok

Amikor változót hoz létre a Azure Portal, meg kell adnia egy adattípust a legördülő listából, hogy a portál megjelenítse a megfelelő vezérlőt a változó értékének megadásához. A következő változótípusok érhetők el a Azure Automation:

* Sztring
* Egész szám
* DateTime
* Logikai
* Null

A változó nem korlátozódik a megadott adattípusra. Ha más típusú értéket szeretne megadni Windows PowerShell a változót a következővel kell beállítania: . Ha a értéket adja meg, a változó `Not defined` értéke Null értékre van állítva. Az értéket a [Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) parancsmaggal vagy a belső parancsmaggal `Set-AutomationVariable` kell beállítania. A az Azure-beli sandbox-környezetben vagy windowsos hibrid `Set-AutomationVariable` runbook-feldolgozón való futtatásra szánt runbookban használható.

A függvény nem használható Azure Portal változótípus értékének létrehozására vagy módosításra. Azonban bármilyen típusú értéket meg lehet adni a Windows PowerShell. Az összetett típusok lekérése a [Newtonsoft.Jsbe. Linq.JProperty](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) a PSObject [PSCustomObject típus](/dotnet/api/system.management.automation.pscustomobject)helyett összetett objektumtípushoz.

Egy változóban több értéket is tárolhat, ha létrehoz egy tömböt vagy kivonattáblát, és menti azt a változóba.

>[!NOTE]
>A virtuális gép névváltozói legfeljebb 80 karakterből állhatnak. Az erőforráscsoport változói legfeljebb 90 karakterből állhatnak. Lásd: [Az Azure-erőforrások elnevezési szabályai és korlátozásai.](../../azure-resource-manager/management/resource-name-rules.md)

## <a name="powershell-cmdlets-to-access-variables"></a>PowerShell-parancsmagok a változók eléréséhez

Az alábbi táblázatban található parancsmagok Automation-változókat hoznak létre és kezelnek a PowerShell használatával. A modulokat az Az modulok [részeként szállítják.](modules.md#az-modules)

| Parancsmag | Leírás |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Egy létező változó értékét kérdezi le. Ha az érték egyszerű típus, a program ugyanezt a típust olvassa be. Ha összetett típusról van beolvasva, a rendszer `PSCustomObject` lekér egy típust. <sup>1</sup>|
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Létrehoz egy új változót, és beállítja annak értékét.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Eltávolít egy meglévő változót.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Beállítja egy létező változó értékét. |

<sup>1</sup> Ez a parancsmag nem használható titkosított változó értékének lekérésre. Ennek egyetlen módja a belső parancsmag használata `Get-AutomationVariable` runbook- vagy DSC-konfigurációban. Ha például egy titkosított változó értékét látja, létrehozhat egy runbookot, amely le tudja szerezni a változót, majd kiírhatja a kimeneti streambe:

```powershell
$encryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $encryptvar"
```

## <a name="internal-cmdlets-to-access-variables"></a>Belső parancsmagok a változók eléréséhez

Az alábbi táblázatban található belső parancsmagokkal férhet hozzá a runbookok és a DSC-konfigurációk változóihoz. Ezek a parancsmagok a globális modulhoz vannak `Orchestrator.AssetManagement.Cmdlets` stb. További információ: [Belső parancsmagok.](modules.md#internal-cmdlets)

| Belső parancsmag | Description |
|:---|:---|
|`Get-AutomationVariable`|Egy létező változó értékét kérdezi le.|
|`Set-AutomationVariable`|Beállítja egy létező változó értékét.|

> [!NOTE]
> Ne használja a változókat a parancsmag `Name` `Get-AutomationVariable` paraméterében runbookban vagy DSC-konfigurációban. A változók használata megnehezítheti a runbookok és az Automation-változók közötti függőségek felderítését a tervezés során.

## <a name="python-functions-to-access-variables"></a>Python-függvények a változók eléréséhez

Az alábbi táblázatban található függvények a Python 2 és 3 runbook változóihoz való hozzáférésre használhatók. A Python 3-runbookok jelenleg előzetes verzióban állnak rendelkezésre.

|Python-függvények|Description|
|:---|:---|
|`automationassets.get_automation_variable`|Egy létező változó értékét kérdezi le. |
|`automationassets.set_automation_variable`|Beállítja egy létező változó értékét. |

> [!NOTE]
> Az adategység-függvények eléréséhez importálni kell a `automationassets` Python-runbook tetején található modult.

## <a name="create-and-get-a-variable"></a>Változó létrehozása és lekért értéke

>[!NOTE]
>Ha el szeretné távolítani egy változó titkosítását, törölnie kell a változót, és újra létre kell hoznia titkosítatlanként.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Változó létrehozása és lekért értéke a Azure Portal

1. Az Automation-fiókjában a bal oldali panelen válassza a **Megosztott** erőforrások alatti **Változók lehetőséget.**
2. A **Változók lapon** válassza a **Változó hozzáadása lehetőséget.**
3. Az Új változó lapon adja meg **a** beállításokat, majd válassza a **Létrehozás** lehetőséget az új változó mentéshez.

> [!NOTE]
> Miután mentett egy titkosított változót, az nem lesz megtekinthető a portálon. Csak frissíthető.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Változó létrehozása és lekért Windows PowerShell

A runbook vagy a DSC konfigurációja a parancsmag használatával hoz létre egy új változót, és `New-AzAutomationVariable` beállít egy kezdeti értéket. Ha a változó titkosítva van, a hívásnak a paramétert kell `Encrypted` használnia. A szkript a használatával tudja lekérni a változó `Get-AzAutomationVariable` értékét.

>[!NOTE]
>A PowerShell-szkriptek nem tudnak titkosított értéket lekérni. Ennek egyetlen módja a belső `Get-AutomationVariable` parancsmag használata.

Az alábbi példa bemutatja, hogyan hozhat létre sztringváltozót, majd hogyan adhatja vissza az értékét.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"
$variableValue = "My String"

New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable' `
-Encrypted $false -Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable').Value
```

Az alábbi példa azt mutatja be, hogyan hozható létre egy változó összetett típussal, majd hogyan olvashatók be a tulajdonságai. Ebben az esetben a [Get-AzVM virtuálisgép-objektuma](/powershell/module/Az.Compute/Get-AzVM) határozza meg a tulajdonságainak egy részkészletét.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" -Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" -AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable" -Encrypted $false -Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable"

$vmName = $vmValue.Value.Name
$vmTags = $vmValue.Value.Tags
```

## <a name="textual-runbook-examples"></a>Példák szöveges runbookra

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példa bemutatja, hogyan állíthat be és lehet lekérni egy változót egy szöveges runbookban. Ez a példa a **numberOfIterations** és **numberOfRunnings** nevű egész szám típusú változók, valamint a **sampleMessage** nevű sztringváltozó létrehozását feltételezi.

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
Set-AutomationVariable -Name numberOfRunnings -Value ($numberOfRunnings += 1)
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Az alábbi példa bemutatja, hogyan lehet változót lekért, változót beállítani és kivételt kezelni egy Python 2-runbook egyik nem létező változója esetében.

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

Az alábbi példa bemutatja, hogyan lehet változókat lekért, változókat beállítani és kivételeket kezelni egy nem létező változó esetében egy Python 3-runbookban (előzetes verzió).

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

## <a name="graphical-runbook-examples"></a>Példák grafikus runbookra

Grafikus runbookban tevékenységeket adhat hozzá a **Get-AutomationVariable** vagy **a Set-AutomationVariable belső parancsmaghoz.** Kattintson a jobb gombbal az egyes változókra a grafikus szerkesztő Könyvtár ablaktábláján, és válassza ki a kívánt tevékenységet.

![Változó hozzáadása a vászonhoz](../media/variables/runbook-variable-add-canvas.png)

Az alábbi képen olyan példatevékenységek láthatóak, amelyek egy grafikus runbook egyszerű értékével frissítik a változókat. Ebben a példában a tevékenység egyetlen Azure-beli virtuális gépet fog lekérni, és egy meglévő Automation-sztringváltozóba menti a számítógép `Get-AzVM` nevét. Nem számít, hogy [a](../automation-graphical-authoring-intro.md#use-links-for-workflow) hivatkozás folyamat vagy sorozat, mivel a kód csak egyetlen objektumot vár a kimenetben.

![Egyszerű változó beállítása](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Következő lépések

- A változók eléréséhez használt parancsmagokkal kapcsolatos további információkért lásd: Modulok kezelése a [Azure Automation.](modules.md)

- A runbookokkal kapcsolatos általános információkért lásd: [Runbook végrehajtása](../automation-runbook-execution.md)a Azure Automation.

- A DSC-konfigurációk részleteiért lásd a Azure Automation State Configuration [áttekintését.](../automation-dsc-overview.md)
