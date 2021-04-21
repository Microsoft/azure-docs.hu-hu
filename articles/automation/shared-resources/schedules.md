---
title: Ütemezések kezelése a Azure Automation
description: Ez a cikk azt mutatja be, hogyan hozhat létre és dolgozhat egy ütemezéssel a Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/29/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f119c15cbbfd9586bdb06fdffad0b12c9a441eea
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832643"
---
# <a name="manage-schedules-in-azure-automation"></a>Ütemezések kezelése a Azure Automation

Ha egy runbookot úgy Azure Automation, hogy egy adott időpontban indul el, csatolja egy vagy több ütemezéshez. Az ütemezések konfigurálhatóak úgy, hogy egyszer, vagy ismétlődő óránkénti vagy napi ütemezés szerint fusson a Azure Portal. Ütemezheti is őket heti, havi, a hét adott napjaira, a hónap napjaira vagy a hónap egy adott napára. Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat.

> [!NOTE]
> Azure Automation támogatja a nyári időszámítást, és az automatizálási műveletekhez megfelelően ütemezi azt.

> [!NOTE]
> Az ütemezések jelenleg nem engedélyezettek Azure Automation DSC-konfigurációkhoz.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Az ütemezések eléréséhez használt PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok Automation-ütemezéseket hoznak létre és kezelnek a PowerShell használatával. A modulokat az Az modulok [részeként szállítják.](modules.md#az-modules)

| Parancsmagok | Description |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Lekér egy ütemezést. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Ütemezett runbookokat ad vissza. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Új ütemezést hoz létre. |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Ütemezéshez társítja a runbookot. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Eltávolít egy ütemezést. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Beállítja egy meglévő ütemezés tulajdonságait. |
| [Unregister-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |A runbookot nem társítja ütemezéshez. |

## <a name="create-a-schedule"></a>Ütemezés létrehozása

A runbookok új ütemezését a Azure Portal, a PowerShell vagy egy Azure Resource Manager (ARM) sablon használatával hozhatja létre. Annak érdekében, hogy ne legyen hatással a runbookokra és az automatizálható folyamatokra, először tesztelje az ütemezéseket egy tesztelésre kijelölt Automation-fiókkal összekapcsolt runbookokkal. Egy teszt ellenőrzi, hogy az ütemezett runbookok továbbra is megfelelően működnek-e. Ha problémát lát, elháríthatja a hibákat, és alkalmazhatja a szükséges módosításokat, mielőtt a frissített Runbook-verziót éles környezetbe mi módosítja.

> [!NOTE]
> Az Automation-fiók nem kap automatikusan új modulverziókat, kivéve, ha manuálisan frissítette őket a Modulok között az [Azure-modulok](../automation-update-azure-modules.md) frissítése **lehetőség kiválasztásával.** Azure Automation Automation-fiók legújabb moduljait használja egy új ütemezett feladat futtatásakor. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Hozzon létre egy új ütemezést a Azure Portal

1. Az Automation-fiókjában a bal oldali panelen válassza az **Ütemezések** lehetőséget a **Megosztott erőforrások alatt.**
2. Az **Ütemezések lapon** válassza az **Ütemezés hozzáadása lehetőséget.**
3. Az Új **ütemezés lapon** adjon meg egy nevet, és igény szerint adja meg az új ütemezés leírását.

    >[!NOTE]
    >Az automatizálási ütemezések jelenleg nem támogatják a speciális karakterek az ütemezés nevében való használatát.
    >

4. Válassza ki, hogy az ütemezés egyszer vagy ismétlődő ütemezés szerint fut-e. Válassza az **Egyszeri** vagy az **Ismétlődő lehetőséget.** Ha az **Egyszer lehetőséget** választja, adja meg a kezdési időt, majd válassza a Létrehozás **lehetőséget.** Ha az Ismétlődő lehetőséget **választja,** adja meg a kezdési időt. Az **Ismétlődik minden esetén beállításhoz** válassza ki, hogy milyen gyakran szeretné megismételni a runbookot. Válassza ki óra, nap, hét vagy hónap szerint.

    * Ha a **Hét lehetőséget** választja, a hét napjai közül választhat. Válasszon ki annyi napot, amennyit csak szeretne. Az ütemezés első futtatása a kezdési időpont után kiválasztott első napon történik. Ha például egy hétvégére vonatkozó ütemezést választ, válassza a Szombat és a Vasárnap lehetőséget.

    ![Ismétlődő hétvége ütemezésének beállítása](../media/schedules/week-end-weekly-recurrence.png)

    * Ha a **Hónap lehetőséget választja,** különböző lehetőségeket kap. A Havi **előfordulások beállításnál** válassza **a** Hónap nap vagy a **Hét nap lehetőséget.** Ha a **Hónap napjai lehetőséget** választja, megjelenik egy naptár, így annyi napot választhat, amennyit csak szeretne. Ha olyan dátumot választ, mint például a 31., amely nem az aktuális hónapban történik, az ütemezés nem fog futni. Ha azt szeretné, hogy az ütemezés az utolsó napon fusson, válassza az **Igen** lehetőséget a Futtatás a hónap **utolsó napján alatt.** Ha a Hét **napjai lehetőséget választja,** **megjelenik az Ismétlődik minden** beállítás. Válassza **az Első,** **a Második,** **a Harmadik,** **a Negyedik** vagy az Utolsó **lehetőséget.** Végül válasszon ki egy napot az ismétléshez.

    ![Havi ütemezés a hónap első, 15. és utolsó napján](../media/schedules/monthly-first-fifteenth-last.png)

5. Ha elkészült, válassza a Létrehozás **lehetőséget.**

### <a name="create-a-new-schedule-with-powershell"></a>Új ütemezés létrehozása a PowerShell használatával

Ütemezések létrehozásához használja a [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) parancsmagot. Meg kell adnia az ütemezés kezdési idejét és a futtatás gyakoriságát. Az alábbi példák bemutatják, hogyan hozhat létre számos különböző ütemezési forgatókönyvet.

>[!NOTE]
>Az automatizálási ütemezések jelenleg nem támogatják a speciális karakterek az ütemezés nevében való használatát.
>

#### <a name="create-a-one-time-schedule"></a>Egyszeres ütemezés létrehozása

Az alábbi példa egy egyszeres ütemezést hoz létre.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Ismétlődő ütemezés létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre egy ismétlődő ütemezést, amely minden nap 13:00-kor fut egy évre.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Hetente ismétlődő ütemezés létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre heti ütemezést, amely csak hétköznapokon fut.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Hetente ismétlődő ütemezés létrehozása a hétvégékre

Az alábbi példa bemutatja, hogyan hozhat létre egy heti ütemezést, amely csak a hétvégéken fut.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Ismétlődő ütemezés létrehozása a hónap első, 15. és utolsó napja számára

Az alábbi példa bemutatja, hogyan hozhat létre egy ismétlődő ütemezést, amely a hónap első, 15. és utolsó napján fut.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Ütemezés létrehozása Resource Manager sablonnal

Ebben a példában egy automation Resource Manager (ARM) sablont használunk, amely egy új feladatütemezőt hoz létre. Általános információk erről a sablonról az Automation-feladatok ütemezésének kezeléséhez: [Microsoft.Automation automationAccounts/jobSchedules sablonreferenciája.](/azure/templates/microsoft.automation/2015-10-31/automationaccounts/jobschedules#quickstart-templates)

Másolja ezt a sablonfájlt egy szövegszerkesztőbe:

```json
{
  "name": "5d5f3a05-111d-4892-8dcc-9064fa591b96",
  "type": "Microsoft.Automation/automationAccounts/jobSchedules",
  "apiVersion": "2015-10-31",
  "properties": {
    "schedule": {
      "name": "scheduleName"
    },
    "runbook": {
      "name": "runbookName"
    },
    "runOn": "hybridWorkerGroup",
    "parameters": {}
  }
}
```

Szerkessze az alábbi paraméterértékeket, és mentse a sablont JSON-fájlként:

* Feladatütemező objektum neve: A feladatütemező objektum neve egy GUID (globálisan egyedi azonosító).

   >[!IMPORTANT]
   > Az ARM-sablonnal üzembe helyezett összes feladatütemezőben a GUID azonosítónak egyedinek kell lennie. Még ha egy meglévő ütemezést is ütemez, módosítania kell a GUID-azonosítót. Ez akkor is érvényes, ha korábban már törölt egy meglévő feladatütemezőt, amely ugyanazokkal a sablonnal lett létrehozva. Ha ugyanazt a GUID azonosítót újrahasználja, az sikertelen üzembe helyezést fog elérni.</br></br>
   > Vannak online szolgáltatások, amelyek új GUID azonosítót hozhatnak létre, például ezt az [ingyenes online GUID generátort.](https://guidgenerator.com/)

* Ütemezés neve: Az Automation-feladat ütemezésének nevét jelöli, amely a megadott runbookhoz lesz csatolva.
* Runbook neve: Annak az Automation-runbooknak a nevét jelöli, amelyhöz a feladatütemezőt társítja.

A fájl mentése után az alábbi PowerShell-paranccsal hozhatja létre a runbook feladatütemezőjét. A parancs a paraméterrel adja meg a sablon elérési útját `TemplateFile` és fájlnevét.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Ütemezés csatolása runbookhoz

Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat. Ha egy runbook paraméterekkel rendelkezik, meg lehet adni hozzájuk értékeket. Meg kell adnia értékeket minden kötelező paraméterhez, és meg kell adnia értékeket is bármely választható paraméterhez. Ezeket az értékeket a program minden alkalommal használja, amikor a runbookot ezzel az ütemezésvel elindítja. Ugyanazt a runbookot egy másik ütemezéshez csatolhatja, és különböző paraméterértékeket is megadhat.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Ütemezés csatolása runbookhoz a Azure Portal

1. A Azure Portal Automation-fiókjában válassza a Folyamatok automatizálása alatt a **Runbookok** **lehetőséget.**
1. Válassza ki az ütemezni kívánt runbook nevét.
1. Ha a runbook jelenleg nincs ütemezéshez kapcsolva, lehetősége van új ütemezést létrehozni, vagy egy meglévő ütemezésre hivatkozni.
1. Ha a runbook paraméterekkel rendelkezik, kiválaszthatja a Futtatás beállításainak módosítása **(Alapértelmezett:Azure)** lehetőséget, és megjelenik a **Paraméterek** panel. Itt adhatja meg a paraméteradatokat.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Ütemezés csatolása runbookhoz a PowerShell használatával

Ütemezést [a Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) parancsmag használatával kapcsolhat össze. A Parameters paraméter segítségével megadhatja a runbook paramétereinek értékét. További információ a paraméterértékek megadásáról: [Starting a Runbook in Azure Automation.](../start-runbooks.md)
Az alábbi példa bemutatja, hogyan kapcsolhat ütemezést egy runbookhoz egy Azure Resource Manager parancsmag paraméterekkel való használatával.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ScheduleName $scheduleName -Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Runbookok gyakoribb futtatásának ütemezése

A leggyakoribb időköz, amelyre a Azure Automation konfigurálható, egy óra. Ha ennél gyakoribb ütemezésre van szüksége, két lehetőség közül választhat:

* Hozzon létre [egy webhookot](../automation-webhooks.md) [](../../logic-apps/logic-apps-overview.md) a runbookhoz, és Azure Logic Apps a webhook hívását. Azure Logic Apps részletesebb ütemezést biztosít az ütemezés meghatározásához.

* Hozzon létre négy ütemezést, amelyek mind 15 percen belül elindulnak egymástól, és óránként egyszer futnak le. Ez a forgatókönyv lehetővé teszi, hogy a runbook 15 percenként fusson a különböző ütemezésekkel.

## <a name="disable-a-schedule"></a>Ütemezés letiltása

Amikor letilt egy ütemezést, a hozzá csatolt runbookok nem ezen az ütemezésen futnak tovább. Manuálisan letilthatja az ütemezést, vagy beállíthatja az ütemezések lejárati idejét a létrehozásukkor. A lejárati idő elérésekor az ütemezés le van tiltva.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Ütemezés letiltása a Azure Portal

1. Az Automation-fiókjában a bal oldali panelen válassza az **Ütemezések** lehetőséget a **Megosztott erőforrások alatt.**
1. A részletek panel megnyitásához válassza ki az ütemezés nevét.
1. Módosítsa az **Engedélyezve beállítást** a **Nem beállításra.**

> [!NOTE]
> Ha le szeretne tiltani egy olyan ütemezést, amely múltbeli kezdési időponttal rendelkezik, a mentés előtt módosítania kell a kezdési dátumot egy jövőbeli időpontra.

### <a name="disable-a-schedule-with-powershell"></a>Ütemezés letiltása a PowerShell használatával

Meglévő ütemezés tulajdonságainak módosításához használja a [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) parancsmagot. Az ütemezés letiltásához adja meg a False (Hamis) értéket a `IsEnabled` paraméterhez.

Az alábbi példa bemutatja, hogyan tilthatja le egy runbook ütemezését egy Azure Resource Manager parancsmag használatával.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Ütemezés eltávolítása

Amikor készen áll az ütemezések eltávolítására, használhatja a Azure Portal PowerShellt. Ne feledje, hogy csak olyan ütemezést távolíthat el, amely le van tiltva az előző szakaszban leírtak szerint.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Ütemezés eltávolítása a Azure Portal

1. Az Automation-fiókjában a bal oldali panelen válassza az **Ütemezések** lehetőséget a **Megosztott erőforrások alatt.**
2. A részletek panel megnyitásához válassza ki az ütemezés nevét.
3. Kattintson a **Törlés** gombra.

### <a name="remove-a-schedule-with-powershell"></a>Ütemezés eltávolítása a PowerShell használatával

A parancsmagot az alább látható módon használhatja egy `Remove-AzAutomationSchedule` meglévő ütemezés törléséhez.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Következő lépések

* Az ütemezések eléréséhez használt parancsmagokkal kapcsolatos további információkért lásd: Modulok kezelése a [Azure Automation.](modules.md)
* A runbookokkal kapcsolatos általános információkért lásd: [Runbook végrehajtása](../automation-runbook-execution.md)a Azure Automation.
