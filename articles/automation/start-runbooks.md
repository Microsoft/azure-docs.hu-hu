---
title: Runbook indítása az Azure Automationben
description: Ez a cikk bemutatja, hogyan indíthat runbookot a Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 01b6e060fcab9c7dab4934aad3d1ab6047ec5236
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829853"
---
# <a name="start-a-runbook-in-azure-automation"></a>Runbook indítása az Azure Automationben

Az alábbi táblázat segít meghatározni a runbookok Azure Automation az adott forgatókönyvnek leginkább megfelelő runbook-indítási módszert. Ez a cikk a runbookok a Azure Portal és a Windows PowerShell. A többi módszer részleteit más dokumentációkban találhatja meg, amelyekhez az alábbi hivatkozásokon keresztül férhet hozzá.

| **Metódus** | **Jellemzők** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>A legegyszerűbb módszer interaktív felhasználói felülettel.<br> <li>Űrlap egyszerű paraméterértékek megadása érdekében.<br> <li>Könnyedén nyomon követheti a feladat állapotát.<br> <li>Hozzáférés azure-beli bejelentkezéssel hitelesítve. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Hívja meg a parancsot a parancssorból Windows PowerShell parancsmagokkal.<br> <li>Az automatizált szolgáltatás több lépésből áll.<br> <li>A kérelem hitelesítése tanúsítvánnyal vagy OAuth-felhasználónévvel/szolgáltatásnévvel történik.<br> <li>Egyszerű és összetett paraméterértékek megadása.<br> <li>Feladat állapotának nyomon követése.<br> <li>A PowerShell-parancsmagok támogatásához szükséges ügyfél. |
| [Azure Automation API](/rest/api/automation/) |<li>A legrugalmasabb módszer, de egyben a legösszetettebb is.<br> <li>Hívja meg a metódust bármely egyéni kódból, amely képes HTTP-kérések igénylését.<br> <li>Igényelheti a hitelesítést tanúsítvánnyal vagy Oauth-felhasználónévvel/szolgáltatásnévvel.<br> <li>Egyszerű és összetett paraméterértékek megadása. *Ha az API-val hív meg egy Python-runbookot, a JSON hasznos adatokat szerializálni kell.*<br> <li>Feladat állapotának nyomon követése. |
| [Webhookok](automation-webhooks.md) |<li>Runbookot egyetlen HTTP-kérésből indít el.<br> <li>Biztonsági jogkivonattal hitelesítve az URL-címben.<br> <li>Az ügyfél nem bírálhatja felül a webhook létrehozásakor megadott paraméterértékeket. A Runbook egyetlen paramétert definiálhat, amely a HTTP-kérés részleteivel van feltöltve.<br> <li>Nem lehet nyomon követni a feladat állapotát a webhook URL-címével. |
| [Válasz az Azure-riasztásra](../azure-monitor/alerts/alerts-overview.md) |<li>Indítson el egy runbookot az Azure-riasztásra válaszul.<br> <li>Konfigurálja a webhookot a runbookhoz és a riasztásra mutató hivatkozást.<br> <li>Biztonsági jogkivonattal hitelesítve az URL-címben. |
| [Ütemezés](./shared-resources/schedules.md) |<li>Automatikusan elindítja a runbookot óránként, naponta, hetente vagy havonta.<br> <li>Az ütemezést módosíthatja Azure Portal, PowerShell-parancsmagokkal vagy az Azure API-val.<br> <li>Adja meg az ütemezéshez használni szükséges paraméterértékeket. |
| [Másik runbookból](automation-child-runbooks.md) |<li>Runbook használata tevékenységként egy másik runbookban.<br> <li>Több runbook által használt funkciókhoz hasznos.<br> <li>Adja meg a gyermek runbook paraméterértékét, és használja a szülő runbookban lévő kimenetet. |

Az alábbi képen egy runbook életciklusának részletes lépései láthatóak. Különböző módszereket tartalmaz a runbookok Azure Automation-ban való futtatásához, amelyek ahhoz szükségesek, hogy a hibrid runbook-feldolgozó Azure Automation runbookokat és interakciókat hajtson végre a különböző összetevők között. Az Automation-runbookok adatközpontban való végrehajtásával kapcsolatos további információkért tekintse meg a hibrid [runbook-dolgozókkal kapcsolatos tudnivalókat](automation-hybrid-runbook-worker.md)

![Runbook-architektúra](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Runbook-paraméterek futtatása

Amikor elindít egy runbookot a Azure Portal vagy Windows PowerShell, az utasítás a webszolgáltatáson Azure Automation keresztül lesz elküldve. Ez a szolgáltatás nem támogatja az összetett adattípusokkal kapcsolatos paramétereket. Ha meg kell adnia egy értéket egy összetett paraméterhez, akkor azt egy másik runbookból beágyazottan kell hívnia a következőben: Gyermek [runbookok](automation-child-runbooks.md)az Azure Automation.

A Azure Automation webszolgáltatás speciális funkciókat biztosít bizonyos adattípusokat használó paraméterekhez a következő szakaszokban leírtak szerint:

### <a name="named-values"></a>Névvel ellátott értékek

Ha a paraméter adattípusa [object], akkor a következő JSON-formátummal küldhet neki egy elnevezett értéklistát: *{Név1:'Érték1', Név2:'Érték2', Név3:'Érték3'}*. Ezeknek az értékek csak egyszerű típusok lehetnek. A runbook [PSCustomObjectként](/dotnet/api/system.management.automation.pscustomobject) kapja meg a paramétert az egyes elnevezett értékeknek megfelelő tulajdonságokkal.

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

Ez a következő kimenetet ad vissza:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tömbök

Ha a paraméter egy tömb, például [array] vagy [string[]], akkor a következő JSON-formátummal küldhet neki értéklistát: *[Value1, Value2, Value3]*. Ezeknek az értékek csak egyszerű típusok lehetnek.

A következő tesztrunbook elfogad egy *user* nevű paramétert.

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

Ez a következő kimenetet ad vissza:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Hitelesítő adatok

Ha a paraméter adattípusa , meg kell adnia egy hitelesítő `PSCredential` Azure Automation [nevét.](./shared-resources/credentials.md) A runbook lekéri a megadott nevű hitelesítő adatokat. A következő teszt runbook egy nevű paramétert fogad `credential` el.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

A következő szöveg használható a felhasználói paraméterhez, feltéve, hogy van egy nevű hitelesítő `My Credential` adateszköz.

```input
My Credential
```

Feltéve, hogy a hitelesítő adatokban a felhasználónév `jsmith` , a következő kimenet jelenik meg.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Runbook futtatása a Azure Portal

1. A Azure Portal válassza az **Automation lehetőséget,** majd kattintson egy Automation-fiók nevére.
2. A központi menüben válassza a **Runbookok lehetőséget.**
3. A Runbookok lapon válasszon ki egy runbookot, majd kattintson a **Start gombra.**
4. Ha a runbook paraméterekkel rendelkezik, a rendszer az értékeket minden paraméterhez egy szövegmezőben kéri. A paraméterekkel kapcsolatos további információkért lásd: [Runbook-paraméterek.](#work-with-runbook-parameters)
5. A Feladat panelen megtekintheti a runbook-feladat állapotát.

## <a name="start-a-runbook-with-powershell"></a>Runbook futtatása a PowerShell használatával

A [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) használatával elindíthat egy runbookot a Windows PowerShell. A következő mintakód elindít egy **Test-Runbook nevű runbookot.**

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` A egy feladatobjektumot ad vissza, amely a runbook elindulása után az állapot nyomon követésére használható. Ezt a feladatobjektumot a [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) parancs használatával használhatja a feladat állapotának meghatározásához, a [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) parancs használatával pedig lekérheti a kimenetét. Az alábbi példa elindít egy **Test-Runbook** nevű runbookot, megvárja, amíg befejeződik, majd megjeleníti a kimenetét.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook -AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup -Stream Output
```

Ha a runbook paramétereket igényel, kivonattáblázatként kell meg [adnia őket.](/powershell/module/microsoft.powershell.core/about/about_hash_tables) A kivonattábla kulcsának egyeznie kell a paraméter nevével, az érték pedig a paraméter értéke. A következő példa bemutatja egy FirstName és egy LastName sztringparaméterrel, egy RepeatCount nevű egész számmal, valamint egy Show nevű logikai paraméterrel rendelkező runbook futtatását. A paraméterekkel kapcsolatos további információkért lásd: [Runbook-paraméterek.](#work-with-runbook-parameters)

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" -Parameters $params
```

## <a name="next-steps"></a>Következő lépések

* A runbookok kezelésével kapcsolatos részletekért lásd: [Runbookok kezelése a Azure Automation.](manage-runbooks.md)
* A PowerShell részleteiért lásd: [PowerShell Docs.](/powershell/scripting/overview)
* A runbook végrehajtásával kapcsolatos hibák elhárításához lásd: [Runbookokkal kapcsolatos problémák elhárítása.](troubleshoot/runbooks.md)
