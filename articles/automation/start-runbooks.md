---
title: Runbook indítása az Azure Automationben
description: Ez a cikk azt ismerteti, hogyan indítható el egy runbook a Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 5fc374cdb60d20896ef01c34f57897c902bbe532
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83828865"
---
# <a name="start-a-runbook-in-azure-automation"></a>Runbook indítása az Azure Automationben

A következő táblázat segítségével határozhatja meg, hogy miként indítható el a runbook olyan Azure Automation, amely leginkább megfelel az adott forgatókönyvnek. Ez a cikk részletesen ismerteti, hogyan kell runbook elindítani a Azure Portal és a Windows PowerShell használatával. Az egyéb módszerekkel kapcsolatos további információkat az alábbi hivatkozásokon keresztül elérhető egyéb dokumentációban találja.

| **Metódus** | **Jellemzők** |
| --- | --- |
| [Azure Portalra](#start-a-runbook-with-the-azure-portal) |<li>Legegyszerűbb módszer interaktív felhasználói felülettel.<br> <li>Egyszerű paraméter-értékeket biztosító űrlap.<br> <li>A feladatok állapotának egyszerűen nyomon követhető.<br> <li>Az Azure-bejelentkezéssel hitelesített hozzáférés. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Hívás a parancssorból Windows PowerShell-parancsmagokkal.<br> <li>Több lépésből is megadható az automatizált funkcióban.<br> <li>A kérelem hitelesítése tanúsítvány vagy OAuth egyszerű felhasználói tag/szolgáltatásnév alapján történik.<br> <li>Egyszerű és összetett paramétereket adjon meg.<br> <li>Feladatok állapotának nyomon követése<br> <li>A PowerShell-parancsmagok támogatásához szükséges ügyfél. |
| [Azure Automation API](/rest/api/automation/) |<li>A legrugalmasabb módszer, de a legbonyolultabb is.<br> <li>Bármely olyan egyéni kód meghívása, amely HTTP-kéréseket tesz elérhetővé.<br> <li>A tanúsítványsal hitelesített kérelem vagy OAuth-felhasználó/egyszerű szolgáltatásnév.<br> <li>Egyszerű és összetett paramétereket adjon meg. *Ha az API használatával keres Python-runbook, a JSON-adattartalmat szerializálni kell.*<br> <li>Feladatok állapotának nyomon követése |
| [Webhookok](automation-webhooks.md) |<li>Runbook elindítása egyetlen HTTP-kérelemből.<br> <li>Hitelesítve a biztonsági jogkivonattal az URL-címben.<br> <li>Az ügyfél nem bírálhatja felül a webhook létrehozásakor megadott paramétereket. A Runbook egyetlen paramétert is meghatározhat, amely a HTTP-kérelem részleteivel van feltöltve.<br> <li>Nincs lehetőség a feladatok állapotának nyomon követésére webhook URL-címén keresztül. |
| [Válaszadás az Azure-riasztásra](../log-analytics/log-analytics-alerts.md) |<li>Runbook elindítása az Azure-riasztásra adott válaszként.<br> <li>Konfigurálja a webhookot a runbook, és hivatkozzon a riasztásra.<br> <li>Hitelesítve a biztonsági jogkivonattal az URL-címben. |
| [Ütemezés](automation-schedules.md) |<li>Runbook automatikus elindítása óránként, naponta, hetente vagy havonta.<br> <li>Az ütemtervet Azure Portal, PowerShell-parancsmagokkal vagy Azure API-val kezelheti.<br> <li>Adja meg az ütemtervhez használandó paramétereket. |
| [Másik Runbook](automation-child-runbooks.md) |<li>Runbook használata tevékenységként egy másik runbook.<br> <li>Több runbookok által használt funkciók esetében hasznos.<br> <li>Adja meg a gyermekobjektumok runbook, és használja a kimenetet a szülő runbook. |

Az alábbi ábrán részletesen ismertetjük a runbook életciklusában bekövetkezett lépéseket. A runbook különböző módokon indul el Azure Automation, mely összetevők szükségesek a hibrid Runbook-feldolgozók számára Azure Automation runbookok végrehajtásához és a különböző összetevők közötti interakcióhoz. Az automatizálási runbookok az adatközpontban való végrehajtásával kapcsolatban a [hibrid runbook-feldolgozók](automation-hybrid-runbook-worker.md) című témakörben talál további információt.

![Runbook architektúra](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Runbook paraméterek használata

Ha a Azure Portal vagy a Windows PowerShellből indít el egy runbook, a rendszer az utasítást a Azure Automation webszolgáltatáson keresztül továbbítja. Ez a szolgáltatás nem támogatja az összetett adattípusú paramétereket. Ha egy összetett paraméterhez értéket kell megadnia, akkor azt egy másik runbook kell meghívnia a [Azure Automation gyermek runbookok](automation-child-runbooks.md)című témakörben leírtak szerint.

A Azure Automation webszolgáltatás speciális funkciókat biztosít a bizonyos adattípusokat használó paraméterek számára, az alábbi szakaszokban leírtak szerint:

### <a name="named-values"></a>Névvel ellátott értékek

Ha a paraméter adattípusa [objektum], akkor a következő JSON formátummal küldhet neki egy névvel ellátott értékek listáját: *{Name1: "érték1", Name2: "érték2", név3: "érték3"}*. Ezeknek az értékek csak egyszerű típusok lehetnek. A runbook az egyes megnevezett értékeknek megfelelő tulajdonságokkal rendelkező [pscustomobject formájában kapja](/dotnet/api/system.management.automation.pscustomobject) fogadja a paramétert.

A következő tesztrunbook elfogad egy usernevű paramétert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

A user paraméterhez a következő szöveg használható.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Ez a következő kimenetet eredményezi:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tömbök

Ha a paraméter egy tömb, például [Array] vagy [string []], akkor a következő JSON formátummal küldheti el az értékek listáját: *[érték1, érték2, érték3]*. Ezeknek az értékek csak egyszerű típusok lehetnek.

A következő tesztrunbook elfogad egy *user*nevű paramétert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

A user paraméterhez a következő szöveg használható.

```input
["Joe","Smith",2,true]
```

Ez a következő kimenetet eredményezi:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Hitelesítő adatok

Ha a paraméter adattípusú, megadhatja `PSCredential` egy Azure Automation [hitelesítőadat](automation-credentials.md)-objektum nevét. A runbook a megadott névvel kérdezi le a hitelesítő adatokat. A következő teszt runbook fogad egy nevű paramétert `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

A User paraméterhez a következő szöveg használható, feltéve, hogy létezik egy nevű hitelesítőadat-eszköz `My Credential` .

```input
My Credential
```

Feltételezve, hogy a Felhasználónév a hitelesítő adatokban `jsmith` , a következő kimenet jelenik meg.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Runbook elindítása a Azure Portal

1. A Azure Portal válassza az **Automation** lehetőséget, majd kattintson az Automation-fiók nevére.
2. A központi menüben válassza a **runbookok**lehetőséget.
3. A Runbookok lapon válasszon ki egy runbook, majd kattintson az **Indítás**gombra.
4. Ha a runbook paraméterekkel rendelkezik, a rendszer felszólítja, hogy adjon meg értékeket az egyes paraméterekhez tartozó szövegmezővel. A paraméterekkel kapcsolatos további információkért lásd: [Runbook paraméterek](#work-with-runbook-parameters).
5. A feladatok ablaktáblán megtekintheti a runbook-feladatok állapotát.

## <a name="start-a-runbook-with-powershell"></a>Runbook elindítása a PowerShell-lel

A [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) használatával elindíthat egy Runbook a Windows PowerShell segítségével. Az alábbi mintakód egy **test-runbook**nevű runbook indít el.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook`egy olyan runbook ad vissza, amelyet a rendszer az állapot nyomon követésére használhat a elindítása után. Ezt követően a [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) használatával meghatározhatja a feladatok állapotát, és lekérheti a [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjoboutput?view=azps-3.7.0) a kimenet lekéréséhez. Az alábbi példa elindítja a **test-runbook**nevű runbook, megvárja, amíg befejeződik, majd megjeleníti a kimenetét.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Ha a runbook paramétereket igényel, akkor [szórótábla](https://technet.microsoft.com/library/hh847780.aspx)kell megadnia őket. A szórótábla kulcsának meg kell egyeznie a paraméter nevével, és az érték a paraméter értéke. A következő példa bemutatja egy FirstName és egy LastName sztringparaméterrel, egy RepeatCount nevű egész számmal, valamint egy Show nevű logikai paraméterrel rendelkező runbook futtatását. A paraméterekkel kapcsolatos további információkért lásd: [Runbook paraméterek](#work-with-runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>További lépések

* A runbook-kezelés részleteiért lásd: [Runbookok kezelése a Azure Automationban](manage-runbooks.md).
* A PowerShell részleteiért lásd: [PowerShell-dokumentumok](https://docs.microsoft.com/powershell/scripting/overview).
* A runbook-végrehajtással kapcsolatos problémák elhárításához tekintse meg a [runbook kapcsolatos problémák elhárítása](troubleshoot/runbooks.md)című témakört.