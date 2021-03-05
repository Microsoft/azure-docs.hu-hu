---
title: Runbookok kezelése Azure Automation
description: Ez a cikk azt ismerteti, hogyan kezelheti a runbookok a Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.openlocfilehash: af767ab37e8e77195b7d13b24ea78f4fb88485fb
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122136"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbookok kezelése Azure Automation

Hozzáadhat egy runbook a Azure Automationhoz, vagy létrehozhat egy újat, vagy importálhat egy meglévőt egy fájlból vagy a [runbook-gyűjteményből](automation-runbook-gallery.md). Ez a cikk egy fájlból importált runbook felügyeletére vonatkozó információkat tartalmaz. A Azure Automation a közösségi runbookok és moduljaihoz való hozzáférés összes részletét megtalálja a [Runbook és a modul-galériákban](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Runbook létrehozása

Hozzon létre egy új runbook a Azure Automation a Azure Portal vagy a Windows PowerShell használatával. A runbook létrehozása után a alkalmazásban található információk használatával szerkesztheti a következőket:

* [Szöveges runbook szerkesztése Azure Automation](automation-edit-textual-runbook.md)
* [A Windows PowerShell-munkafolyamatokkal kapcsolatos alapvető fogalmak megismerése az Automation runbookok](automation-powershell-workflow.md)
* [Python 2 csomagok kezelése Azure Automation](python-packages.md)
* [Python 3 csomag (előzetes verzió) kezelése Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Runbook létrehozása a Azure Portalban

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A runbookok listájának megnyitásához a központból válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
3. Kattintson **a Runbook létrehozása** elemre.
4. Adja meg a runbook nevét, és válassza ki a [típusát](automation-runbook-types.md). A runbook nevének betűvel kell kezdődnie, és csak betűket, számokat, aláhúzásokat és kötőjeleket tartalmazhat.
5. A runbook létrehozásához és a szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

### <a name="create-a-runbook-with-powershell"></a>Runbook létrehozása a PowerShell-lel

Üres runbook létrehozásához használja a [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) parancsmagot. A `Type` paraméter használatával megadhatja a számára definiált runbook-típusok egyikét `New-AzAutomationRunbook` .

Az alábbi példa bemutatja, hogyan hozhat létre új üres runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook importálása

Létrehozhat egy PowerShell-vagy PowerShell-(**. ps1**) parancsfájlt, egy grafikus runbook (**. graphrunbook**), vagy egy Python 2 vagy Python 3 szkriptet (**.**) a saját runbook. Meg kell adnia az importálás során létrehozott [runbook típusát](automation-runbook-types.md) , figyelembe véve az alábbi szempontokat.

* Olyan **. ps1** fájlt is importálhat, amely nem tartalmaz munkafolyamatot egy [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) vagy egy [PowerShell-munkafolyamat runbook](automation-runbook-types.md#powershell-workflow-runbooks). Ha egy PowerShell munkafolyamat-runbook importálja, azt egy munkafolyamatba konvertálja a rendszer. Ebben az esetben a megjegyzéseket a runbook tartalmazza az elvégzett módosítások leírásához.

* Csak egy PowerShell-munkafolyamatot tartalmazó **. ps1** fájl importálható egy [PowerShell munkafolyamat-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Ha a fájl több PowerShell-munkafolyamatot tartalmaz, az importálás sikertelen lesz. Minden munkafolyamatot a saját fájljába kell mentenie, és külön kell importálnia azokat.

* Ne importáljon PowerShell-munkafolyamatokat tartalmazó **. ps1** fájlt egy [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks), mivel a PowerShell-parancsfájl motorja nem ismeri fel.

* Csak egy **. graphrunbook** fájlt importáljon egy új [grafikus runbook](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Runbook importálása a Azure Portalból

A parancsfájlokat a következő eljárással importálhatja Azure Automationba.

> [!NOTE]
> Egy **. ps1** fájlt csak a portál használatával lehet egy PowerShell-munkafolyamati runbook importálni.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
3. Kattintson **a Runbook importálása** elemre.
4. Kattintson a **Runbook fájl** elemre, és válassza ki az importálni kívánt fájlt.
5. Ha a **név** mező engedélyezve van, lehetősége van módosítani a runbook nevét. A névnek betűvel kell kezdődnie, és csak betűket, számokat, aláhúzásokat és kötőjeleket tartalmazhat.
6. A [runbook típusa](automation-runbook-types.md) automatikusan ki van választva, de a megfelelő korlátozások figyelembe vétele után módosíthatja a típust.
7. Kattintson a **Létrehozás** lehetőségre. Az új runbook megjelenik az Automation-fiók runbookok listájában.
8. A Futtatás előtt közzé kell tennie [a runbook](#publish-a-runbook) .

> [!NOTE]
> Grafikus runbook importálása után átalakíthatja azt más típusra. A grafikus runbook azonban nem alakítható át szöveges runbook.

### <a name="import-a-runbook-with-windows-powershell"></a>Runbook importálása a Windows PowerShell használatával

Az [import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) parancsmag használatával importálhat egy parancsfájlt a runbook vázlatként. Ha a runbook már létezik, az importálás meghiúsul, ha a `Force` paramétert használja a parancsmaggal.

Az alábbi példa bemutatja, hogyan importálhat egy parancsfájlt egy runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Erőforrások kezelése

Ha a runbook létrehoz egy [erőforrást](automation-runbook-execution.md#resources), a parancsfájlnak ellenőriznie kell, hogy az erőforrás már létezik-e, mielőtt megpróbálja létrehozni. Íme egy alapszintű példa.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>Részletek beolvasása a tevékenység naplójából

Lekérheti a runbook adatait, például a runbook elindító személyt vagy fiókot az Automation-fiók [tevékenységi naplójából](automation-runbook-execution.md#activity-logging) . A következő PowerShell-példa az utolsó felhasználót adja meg a megadott runbook futtatásához.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Követés állapota

Érdemes lehet a runbookok, hogy moduláris jellegűek legyenek, és hogy a logikát könnyen újra felhasználható és újra lehessen indítani. A runbook nyomon követése biztosítja, hogy a runbook logikája megfelelően fusson, ha problémák merülnek fel.

A runbook állapotát egy külső forrás, például egy Storage-fiók, egy adatbázis vagy egy megosztott fájl használatával követheti nyomon. Hozzon létre logikát a runbook, hogy először ellenőrizzék az utolsó végrehajtott művelet állapotát. Ezután az ellenőrzés eredményei alapján a logikai művelet kihagyhatja vagy folytathatja a runbook adott feladatait.

## <a name="prevent-concurrent-jobs"></a>Egyidejű feladatok megakadályozása

Néhány runbookok furcsán viselkedik, ha egyszerre több feladaton futnak. Ebben az esetben fontos, hogy egy runbook logikát alkalmazzon annak megállapításához, hogy van-e már futó feladata. Íme egy alapszintű példa.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Átmeneti hibák kezelése egy időben függő parancsfájlban

A runbookok robusztusnak kell lennie, és képesnek kell lennie a [hibák](automation-runbook-execution.md#errors)kezelésére, beleértve az átmeneti hibákat is, amelyek újraindítást vagy meghibásodást okozhatnak. Ha egy runbook meghibásodik, Azure Automation újrapróbálkozik.

Ha a runbook általában egy adott időkorláton belül fut, a parancsfájl implementálja a végrehajtási időt a logikával. Ez az ellenőrzés biztosítja az olyan műveletek futtatását, mint az indítás, a Leállítás vagy a Kibővítés csak adott időpontokban.

> [!NOTE]
> Az Azure-beli homokozó folyamat helyi ideje UTC értékre van állítva. A runbookok dátumra és időpontra vonatkozó számításait figyelembe kell venni.

## <a name="work-with-multiple-subscriptions"></a>Több előfizetés használata

A runbook képesnek kell lennie az [előfizetésekkel](automation-runbook-execution.md#subscriptions)való együttműködésre. Több előfizetés kezeléséhez például a runbook a [disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) parancsmagot használja. Ez a parancsmag biztosítja, hogy a hitelesítési környezet ne legyen lekérve egy másik, ugyanazon a homokozóban futó runbook. A runbook a `Get-AzContext` parancsmaggal is lekéri az aktuális munkamenet környezetét, és hozzárendeli azt a változóhoz `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
$AzureContext = Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint `
-Subscription $Conn.SubscriptionId

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
-ResourceGroupName $ResourceGroupName `
-AutomationAccountName $AutomationAccountName `
-Name $ChildRunbookName `
-DefaultProfile $AzureContext
```

## <a name="work-with-a-custom-script"></a>Egyéni parancsfájl használata

> [!NOTE]
> Általában nem futtathat egyéni parancsfájlokat és runbookok a gazdagépen egy telepített Log Analytics ügynökkel.

Egyéni parancsfájl használata:

1. Hozzon létre egy Automation-fiókot, és szerezzen be [közreműködői szerepkört](automation-role-based-access-control.md).
2. [Kapcsolja össze a fiókot az Azure-munkaterülettel](../security-center/security-center-enable-data-collection.md).
3. [Hibrid Runbook Worker](automation-hybrid-runbook-worker.md), [Update Management](./update-management/overview.md)vagy más automatizálási funkció engedélyezése. 
4. Ha Linux-gépen van szüksége, magas szintű engedélyekkel kell rendelkeznie. Jelentkezzen be az [aláírás-ellenőrzések kikapcsolásához](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Runbook tesztelése

A runbook tesztelésekor a rendszer végrehajtja a [Piszkozat verzióját](#publish-a-runbook) , és az általa végrehajtott műveletek befejeződtek. A rendszer nem hoz létre munkaelőzményeket, de a [kimenet](automation-runbook-output-and-messages.md#use-the-output-stream) és a [Figyelmeztetés és a hiba](automation-runbook-output-and-messages.md#working-with-message-streams) streamek a test output (teszt kimenet) ablaktáblán jelennek meg. A [részletes adatfolyamba](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) küldött üzenetek csak akkor jelennek meg a kimeneti ablaktáblán, ha a [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) változó értéke `Continue` .

Bár a Piszkozat verziója fut, a runbook továbbra is szabályosan hajtja végre, és minden műveletet végrehajt a környezetben lévő erőforrásokon. Emiatt csak a nem éles erőforrások runbookok kell tesztelni.

Az egyes [runbook-típusok](automation-runbook-types.md) tesztelési eljárása azonos. Nincs különbség a szöveges szerkesztő és a Azure Portal grafikus szerkesztőjé közötti tesztelésben.

1. Nyissa meg a runbook Piszkozat verzióját a [szöveges szerkesztőben](automation-edit-textual-runbook.md) vagy a [grafikus szerkesztőben](automation-graphical-authoring-intro.md).
1. Kattintson a **teszt** gombra a teszt lap megnyitásához.
1. Ha a runbook paraméterekkel rendelkezik, azok megjelennek a bal oldali panelen, ahol megadhatja a teszthez használni kívánt értékeket.
1. Ha a tesztet [hibrid Runbook-feldolgozón](automation-hybrid-runbook-worker.md)szeretné futtatni, módosítsa a **futtatási beállításokat** **hibrid feldolgozóra** , majd válassza ki a célcsoport nevét.  Ellenkező esetben tartsa meg az alapértelmezett **Azure** -t a teszt felhőben való futtatásához.
1. A teszt **elindításához** kattintson a Start gombra.
1. A kimenet ablaktáblán található gombokkal leállíthatja vagy felfüggesztheti a [PowerShell-munkafolyamatokat](automation-runbook-types.md#powershell-workflow-runbooks) vagy [grafikus](automation-runbook-types.md#graphical-runbooks) runbook a tesztelés során. A runbook felfüggesztésekor a teszt a felfüggesztés előtt még végrehajtja az aktuális tevékenységet. A felfüggesztés után a runbookot megállíthatja vagy újraindíthatja.
1. Vizsgálja meg a kimenet ablaktáblán látható runbook.

## <a name="publish-a-runbook"></a>Runbook közzététele

Új runbook létrehozásakor vagy importálásakor a Futtatás előtt közzé kell tennie. A Azure Automation minden runbook tartalmaz egy Piszkozat-verziót és egy közzétett verziót. Kizárólag a Közzétett verziót lehet futtatni, és kizárólag a Piszkozat verzió szerkeszthető. A Közzétett verzióra nincsenek hatással a Piszkozat verzióban végrehajtott módosítások. Ha elérhetővé kell tenni a Piszkozat verzióját, azt közzéteszi, és felülírja a jelenlegi közzétett verziót a Piszkozat verziójával.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Runbook közzététele a Azure Portalban

1. Nyissa meg a runbook a Azure Portalban.
2. Kattintson a **Szerkesztés** gombra.
3. Kattintson a **Közzététel** , majd az **Igen** lehetőségre az ellenőrző üzenetre válaszul.

### <a name="publish-a-runbook-using-powershell"></a>Runbook közzététele a PowerShell használatával

A runbook közzétételéhez használja a [publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) parancsmagot. 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Runbook ütemezése az Azure Portalon

A runbook közzétételekor a következő műveletre ütemezhet:

1. Nyissa meg a runbook a Azure Portalban.
2. Válassza az **erőforrások** területen az **ütemtervek** lehetőséget.
3. Válassza **az ütemezett Hozzáadás** lehetőséget.
4. Az ütemterv Runbook ablaktáblán válassza az **ütemterv összekapcsolása a Runbook** lehetőséget.
5. Válassza az **új ütemterv létrehozása** lehetőséget az ütemterv ablaktáblán.
6. Adja meg a nevet, a leírást és az egyéb paramétereket az új ütemterv ablaktáblán.
7. Miután létrehozta az ütemtervet, jelölje ki, majd kattintson **az OK gombra**. Most már össze kell kapcsolni a runbook.
8. Keressen egy e-mailt a postaládájában, hogy értesítse Önt a runbook állapotáról.

## <a name="obtain-job-statuses"></a>Feladatok állapotának beszerzése

### <a name="view-statuses-in-the-azure-portal"></a>Állapotok megtekintése a Azure Portalban

A Azure Automationban található feladatok kezelésére vonatkozó részletek a [feladatokban](automation-runbook-execution.md#jobs)érhetők el. Ha készen áll a runbook-feladatok megtekintésére, használja a Azure Portal és az Automation-fiók elérését. A jobb oldalon a runbook összes **feladatának** összegzése látható.

![Feladatok statisztikájának csempe](./media/manage-runbooks/automation-account-job-status-summary.png)

Az összefoglalás a feladatok állapotának számát és grafikus ábrázolását jeleníti meg minden egyes végrehajtott feladathoz.

A csempére kattintva megjelenítheti a feladatok lapot, amely tartalmazza az összes végrehajtott feladat összegzett listáját. Ezen a lapon látható az egyes feladatokhoz tartozó állapot, a runbook neve, a kezdési idő és a befejezési idő.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Képernyőkép a feladatok oldalról.":::

A feladatok listájának szűréséhez kattintson a **feladatok szűrése** lehetőségre. Szűrje egy adott runbook, a feladatok állapotát, vagy válasszon a legördülő listából, és adja meg a keresés időtartományát.

![Szűrési feladatok állapota](./media/manage-runbooks/automation-account-jobs-filter.png)

Azt is megteheti, hogy megtekinti az adott runbook tartozó feladat összegzésének részleteit. Ehhez válassza ki az runbook az Automation-fiók Runbookok lapján, majd válassza a **feladatok** lehetőséget. Ez a művelet a feladatok lapot mutatja be. Innen egy feladatra kattintva megtekintheti annak adatait és kimenetét.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Képernyőkép a feladatok oldalról, ahol a hibák gomb ki van emelve.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Feladatok állapotának beolvasása a PowerShell használatával

A [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) parancsmaggal kérheti le a runbook létrehozott feladatokat, valamint egy adott feladat részleteit. Ha a használatával indít el egy runbook `Start-AzAutomationRunbook` , az a létrejövő feladatot adja vissza. A [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) használatával kérheti le a feladatok kimenetét.

A következő példa beolvassa a minta runbook utolsó feltételét, és megjeleníti annak állapotát, a runbook paraméterekhez megadott értékeket, valamint a feladatok kimenetét.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

A következő példa egy adott feladathoz tartozó kimenetet kérdez le, és visszaadja az egyes rekordokat. Ha a rekordok egyikének [kivétele](automation-runbook-execution.md#exceptions) van, a parancsfájl az érték helyett a kivételt írja. Ez a viselkedés azért hasznos, mert a kivételek olyan további információkat is biztosíthatnak, amelyek a kimenet során általában nem lesznek naplózva.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Következő lépések

* A runbook-kezelés részleteiért lásd: [runbook-végrehajtás a Azure Automation-ben](automation-runbook-execution.md).
* PowerShell-runbook előkészítéséhez tekintse [meg a szöveges Runbookok szerkesztése Azure Automationban](automation-edit-textual-runbook.md)című témakört.
* A runbook-végrehajtással kapcsolatos problémák elhárításához tekintse meg a [runbook kapcsolatos problémák elhárítása](troubleshoot/runbooks.md)című témakört.