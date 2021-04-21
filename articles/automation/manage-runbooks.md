---
title: Runbookok kezelése a Azure Automation
description: Ez a cikk bemutatja, hogyan kezelheti a runbookokat a Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a172189d8b52a80fc50e7d8c882859f7855aeca8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830087"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbookok kezelése a Azure Automation

Egy runbookot úgy adhat hozzá a Azure Automation, hogy létrehoz egy újat, vagy importál egy meglévőt egy fájlból vagy a [Runbook-katalógusból.](automation-runbook-gallery.md) Ez a cikk a fájlból importált runbookok kezelésével kapcsolatos információkat tartalmaz. A közösségi runbookok és modulok elérésének részletes adatait a [Runbook-](automation-runbook-gallery.md)és modulkatajánlákban találja a Azure Automation.

## <a name="create-a-runbook"></a>Runbook létrehozása

Hozzon létre egy új runbookot a Azure Automation a Azure Portal vagy Windows PowerShell. A runbook létrehozása után a következő információkkal szerkesztheti:

* [Szöveges runbook szerkesztése a Azure Automation](automation-edit-textual-runbook.md)
* [Az Automation Windows PowerShell-runbookok legfontosabb munkafolyamat-fogalmai](automation-powershell-workflow.md)
* [Python 2-csomagok kezelése a Azure Automation](python-packages.md)
* [Python 3-csomagok (előzetes verzió) kezelése a Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Runbook létrehozása a Azure Portal

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A központban válassza a  **Folyamatok automatizálása alatt található Runbookok** lehetőséget a runbookok listájának megnyitásához.
3. Kattintson **a Runbook létrehozása elemre.**
4. Adja meg a runbook nevét, és válassza ki a [típusát.](automation-runbook-types.md) A runbook nevének betűvel kell kezdődnie, és betűket, számokat, aláhúzásjeleket és kötőjeleket tartalmazhat.
5. Kattintson **a Létrehozás gombra** a runbook létrehozásához és a szerkesztő megnyitásához.

### <a name="create-a-runbook-with-powershell"></a>Runbook létrehozása a PowerShell használatával

Üres runbook létrehozásához használja a [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) parancsmagot. A `Type` paraméterrel adhatja meg a számára definiált runbooktípusok `New-AzAutomationRunbook` valamelyikét.

Az alábbi példa bemutatja, hogyan hozhat létre egy új üres runbookot.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Runbook importálása

Importálhat Egy PowerShell- vagy PowerShell-munkafolyamat -**.ps1**- szkriptet, egy grafikus runbookot (**.graphrunbook**) vagy egy Python 2- vagy Python 3-szkriptet **(.py**) saját runbook létrehozása érdekében. Meg kell adnia az importálás során létrehozott [runbook](automation-runbook-types.md) típusát, figyelembe véve az alábbi szempontokat.

* Egy munkafolyamatot nem tartalmazó **.ps1** fájlt importálhat egy [PowerShell-runbookba](automation-runbook-types.md#powershell-runbooks) vagy egy [PowerShell-munkafolyamat-runbookba.](automation-runbook-types.md#powershell-workflow-runbooks) Ha powershelles munkafolyamat-runbookba importálja, az munkafolyamatká lesz konvertálva. Ebben az esetben a runbook megjegyzéseket tartalmaz a végrehajtott módosítások leírására.

* Egy PowerShell-munkafolyamatot tartalmazó **.ps1** fájlt importálhat egy [PowerShell-munkafolyamat-runbookba.](automation-runbook-types.md#powershell-workflow-runbooks) Ha a fájl több PowerShell-munkafolyamatot tartalmaz, az importálás sikertelen lesz. Minden munkafolyamatot a saját fájljában kell mentenie, és mindegyiket külön kell importálni.

* Ne importálja a PowerShell-munkafolyamatot tartalmazó **.ps1** fájlt egy [PowerShell-runbookba,](automation-runbook-types.md#powershell-runbooks)mert a PowerShell-parancsfájlmotor nem ismeri fel.

* Csak **.graphrunbook-fájlt importálhat** egy új [grafikus runbookba.](automation-runbook-types.md#graphical-runbooks)

### <a name="import-a-runbook-from-the-azure-portal"></a>Runbook importálása a Azure Portal

Az alábbi eljárással importálhat parancsfájlt a Azure Automation.

> [!NOTE]
> **.ps1-fájlokat** csak PowerShell munkafolyamat-runbookba importálhat a portál használatával.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **Runbookok listájának** megnyitásához válassza a **Folyamatok** automatizálása alatt található Runbookok lehetőséget.
3. Kattintson **a Runbook importálása elemre.**
4. Kattintson **a Runbook-fájl elemre,** és válassza ki az importálni kívánt fájlt.
5. Ha **a Név** mező engedélyezve van, lehetősége van megváltoztatni a runbook nevét. A névnek betűvel kell kezdődnie, és betűket, számokat, aláhúzásjeleket és kötőjeleket tartalmazhat.
6. A [runbook típusa](automation-runbook-types.md) automatikusan ki van választva, de a típust a vonatkozó korlátozások figyelembe véve módosíthatja.
7. Kattintson a **Létrehozás** lehetőségre. Az új runbook megjelenik az Automation-fiók runbookok listájában.
8. A [runbookot a futtatása előtt](#publish-a-runbook) közzé kell tenni.

> [!NOTE]
> Miután importált egy grafikus runbookot, átalakíthatja azt egy másik típusra. A grafikus runbookok azonban nem konvertálhatók szöveges runbookokká.

### <a name="import-a-runbook-with-windows-powershell"></a>Runbook importálása Windows PowerShell

Az [Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) parancsmag használatával szkriptfájlt importálhat vázlatként runbookként. Ha a runbook már létezik, az importálás sikertelen lesz, kivéve, ha a `Force` paramétert használja a parancsmaggal.

Az alábbi példa bemutatja, hogyan importálhat egy szkriptfájlt egy runbookba.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>Erőforrások kezelés

Ha a runbook létrehoz egy [erőforrást,](automation-runbook-execution.md#resources)a szkriptnek ellenőriznie kell, hogy az erőforrás már létezik-e, mielőtt megpróbálja létrehozni. 1 alapszintű példa.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>Részletek lekérése a tevékenységnaplóból

Az Automation-fiók tevékenységnaplójában lekérheti a runbook részleteit, [](automation-runbook-execution.md#activity-logging) például a runbookot elizáló személyt vagy fiókot. Az alábbi PowerShell-példa a megadott runbook utolsó felhasználóját tartalmazza.

```powershell-interactive
$SubID = '00000000-0000-0000-0000-000000000000'
$AutoRgName = 'MyResourceGroup'
$aaName = 'MyAutomationAccount'
$RunbookName = 'MyRunbook'
$StartTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $AutoRgName
    StartTime         = $StartTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $RunbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $AutoRgName
            AutomationAccountName = $aaName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $RunbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Követés állapota

A runbookokat moduláris, könnyen újrafelhasználható és újraindítható logikával kell megirakni. A runbookok előrehaladásának nyomon követése biztosítja, hogy probléma esetén a runbook logikája megfelelően fusson.

A runbookok előrehaladását külső forrással, például tárfiókkal, adatbázissal vagy megosztott fájlokkal követheti nyomon. Hozzon létre logikát a runbookban az utolsó művelet állapotának ellenőrzéshez. Ezután az ellenőrzés eredményei alapján a logika kihagyhatja vagy folytathatja a runbook adott feladatait.

## <a name="prevent-concurrent-jobs"></a>Egyidejű feladatok megakadályozása

Egyes runbookok különös módon viselkednek, ha egyszerre több feladaton is futnak. Ebben az esetben fontos, hogy a runbook implementálja a logikát annak megállapításához, hogy van-e már futó feladat. Példa az alapszintűre.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams
$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook [$runbookName] is already running"
    exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Átmeneti hibák kezelhetők egy időfüggő szkriptben

A runbooknak robusztusnak kell lennie, és képesnek kell lennie a hibák [kezelésére,](automation-runbook-execution.md#errors)beleértve az olyan átmeneti hibákat is, amelyek újraindítást vagy sikertelenséget okozhatnak. Ha egy runbook meghibásodik, Azure Automation újra.

Ha a runbook általában egy adott időszakon belül fut, a szkript implementálja a logikát a végrehajtási idő ellenőrzésére. Ez az ellenőrzés biztosítja, hogy a műveletek , például az indítás, a leállítás vagy a horizontális felskála helyezés csak adott időpontokban futnak.

> [!NOTE]
> Az Azure-beli sandbox-folyamat helyi ideje UTC-alapúra van állítva. A runbookok dátum- és időszámításait figyelembe kell venni.

## <a name="work-with-multiple-subscriptions"></a>Több előfizetés használata

A runbooknak képesnek kell lennie az [előfizetésekkel való munkára.](automation-runbook-execution.md#subscriptions) Több előfizetés kezeléséhez például a runbook a [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) parancsmagot használja. Ez a parancsmag biztosítja, hogy a hitelesítési környezet ne legyen beolvasva egy másik, ugyanabban a sandboxban futó runbookból. A runbook a parancsmag használatával lekéri az aktuális munkamenet környezetét, és hozzárendeli a `Get-AzContext` `$AzureContext` változóhoz.

```powershell
Disable-AzContextAutosave -Scope Process

$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams

$ChildRunbookName = 'ChildRunbookDemo'
$aaName = 'MyAutomationAccount'
$rgName = 'MyResourceGroup'

$startParams = @{
    ResourceGroupName     = $rgName
    AutomationAccountName = $aaName
    Name                  = $ChildRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

## <a name="work-with-a-custom-script"></a>Egyéni szkript használata

> [!NOTE]
> Általában nem futtathat egyéni szkripteket és runbookokat a gazdagépen telepített Log Analytics-ügynökkel.

Egyéni szkript használata:

1. Hozzon létre egy Automation-fiókot, és szerezzen be egy [Közreműködő szerepkört.](automation-role-based-access-control.md)
2. [Csatolja a fiókot az Azure-munkaterülethez.](../security-center/security-center-enable-data-collection.md)
3. Hibrid [runbook-feldolgozó,](automation-hybrid-runbook-worker.md) [Update Management,](./update-management/overview.md)vagy más Automation-szolgáltatás engedélyezése. 
4. Ha Linux rendszerű gépen van, magas szintű engedélyekkel kell rendelkeznie. Jelentkezzen be az [aláírás-ellenőrzések kikapcsolhoz.](automation-linux-hrw-install.md#turn-off-signature-validation)

## <a name="test-a-runbook"></a>Runbook tesztelése

Egy runbook tesztelője végrehajtja a [Piszkozat](#publish-a-runbook) verzióját, és végrehajtja az összes végrehajtott műveletet. Nem jön létre feladatelőzmény, de a kimeneti és figyelmeztető és [hibastreamek](automation-runbook-output-and-messages.md#working-with-message-streams) a Tesztkimenet panelen jelennek meg. [](automation-runbook-output-and-messages.md#use-the-output-stream) A részletes [streambe küldött üzenetek](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) csak akkor jelennek meg a Kimenet panelen, ha a [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) változó értéke `Continue` .

Annak ellenére, hogy a vázlat verziója fut, a runbook továbbra is a szokásos módon fut, és bármilyen műveletet hajt végre a környezet erőforrásain. Ezért csak nem éles erőforrásokon tesztelje a runbookokat.

Az egyes [runbook-típusokat ugyanaz](automation-runbook-types.md) az eljárás teszteli. A szöveges szerkesztő és a grafikus szerkesztő között nincs különbség a Azure Portal.

1. Nyissa meg a runbook Vázlat verzióját a szöveges szerkesztőben vagy [a](automation-edit-textual-runbook.md) [grafikus szerkesztőben.](automation-graphical-authoring-intro.md)
1. Kattintson **a Teszt** gombra a Teszt lap megnyitásához.
1. Ha a runbook rendelkezik paraméterekkel, azok a bal oldali panelen vannak felsorolva, ahol meg lehet adni a teszthez használni szükséges értékeket.
1. Ha hibrid [runbook-feldolgozón](automation-hybrid-runbook-worker.md)szeretné futtatni a  tesztet,  módosítsa a Futtatás beállításai beállítást Hibrid feldolgozóra, és válassza ki a célcsoport nevét.  Ellenkező esetben tartsa meg az **alapértelmezett Azure-t** a teszt felhőben való futtatásához.
1. A **teszt megkezdéséhez** kattintson az Indítás gombra.
1. A Kimenet panel alatti gombokkal leállíthat vagy felfüggeszthet egy [PowerShell-munkafolyamatot](automation-runbook-types.md#powershell-workflow-runbooks) vagy grafikus [runbookot](automation-runbook-types.md#graphical-runbooks) a tesztelés közben. A runbook felfüggesztésekor a teszt a felfüggesztés előtt még végrehajtja az aktuális tevékenységet. A felfüggesztés után a runbookot megállíthatja vagy újraindíthatja.
1. Vizsgálja meg a runbook kimenetét a Kimenet panelen.

## <a name="publish-a-runbook"></a>Runbook közzététele

Amikor új runbookot hoz létre vagy importál, közzé kell tennie azt, mielőtt futtathatja. A Azure Automation runbook rendelkezik egy Draft (Vázlat) és egy Published (Közzétett) verzióval. Kizárólag a Közzétett verziót lehet futtatni, és kizárólag a Piszkozat verzió szerkeszthető. A Közzétett verzióra nincsenek hatással a Piszkozat verzióban végrehajtott módosítások. Amikor elérhetővé kell tenni a Draft verziót, közzé kell tenni, felülírva az aktuális közzétett verziót a Draft verzióval.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Runbook közzététele a Azure Portal

1. Nyissa meg a runbookot a Azure Portal.
2. Kattintson a **Szerkesztés** gombra.
3. Az **ellenőrző üzenetre** **válaszul** kattintson a Közzététel, majd az Igen gombra.

### <a name="publish-a-runbook-using-powershell"></a>Runbook közzététele a PowerShell használatával

A Runbook közzétételéhez használja a [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) parancsmagot. 

```azurepowershell-interactive
$aaName = "MyAutomationAccount"
$RunbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $aaName
    ResourceGroupName     = $rgName
    Name                  = $RunbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Runbook ütemezése az Azure Portalon

A runbookot a közzététele után ütemezheti működésre:

1. Nyissa meg a runbookot a Azure Portal.
2. Az **Erőforrások alatt válassza az** **Ütemezések lehetőséget.**
3. Válassza **az Ütemezés hozzáadása lehetőséget.**
4. A Runbook ütemezése panelen válassza az **Ütemezés csatolása a runbookhoz lehetőséget.**
5. Az **Ütemezés panelen válassza az** Új ütemezés létrehozása lehetőséget.
6. Adja meg a nevet, a leírást és az egyéb paramétereket az Új ütemezés panelen.
7. Az ütemezés létrehozása után jelölje ki, majd kattintson az **OK gombra.** Most már hozzá kell kapcsolni a runbookhoz.
8. A runbook állapotáról e-mailben értesítő e-mailt kell keresnie a postaládájában.

## <a name="obtain-job-statuses"></a>Feladatállapotok beszerzése

### <a name="view-statuses-in-the-azure-portal"></a>Állapotok megtekintése a Azure Portal

A feladatkezelés részletei a Azure Automation a Feladatok [részen találhatóak](automation-runbook-execution.md#jobs)meg. Amikor készen áll a runbook-feladatokra, használja a Azure Portal Automation-fiókját. A jobb oldalon a Feladatstatisztika oldalon láthatja az összes **runbook-feladat összegzését.**

![Feladatstatisztika csempe](./media/manage-runbooks/automation-account-job-status-summary.png)

Az összefoglalás megjeleníti az egyes végrehajtott feladatállapotok számát és grafikus megjelenítését.

A csempére kattintva megjelenik a Feladatok lap, amely az összes végrehajtott feladat összesített listáját tartalmazza. Ezen az oldalon látható az egyes feladat állapota, runbookneve, kezdési ideje és befejezési ideje.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="A Feladatok lap képernyőképe.":::

A Feladatok szűrése lehetőség kiválasztásával szűrheti a **feladatok listáját.** Szűrhet egy adott runbookra, feladatállapotra vagy a legördülő listában található választásra, és meg kell adnia a keresés időtartományát.

![Feladat állapotának szűrése](./media/manage-runbooks/automation-account-jobs-filter.png)

Másik lehetőségként megtekintheti egy adott runbook feladatösszegzési részleteit, ha kiválasztja a runbookot az Automation-fiók Runbookok oldalán, majd a Feladatok **lehetőséget.** Ez a művelet a Feladatok lapot mutatja. Itt egy feladatrekordra kattintva megtekintheti annak részleteit és kimenetét.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Képernyőkép a Feladatok lapról, kiemelt Hibák gombbal.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Feladatállapotok lekérése a PowerShell használatával

A [Get-AzAutomationJob parancsmag](/powershell/module/Az.Automation/Get-AzAutomationJob) használatával lekérhetőek a runbookhoz létrehozott feladatok és egy adott feladat részletei. Ha a használatával indít el egy runbookot, az `Start-AzAutomationRunbook` visszaadja az eredményül kapott feladatot. A [feladat kimenetének lekérése a Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) parancs használatával.

Az alábbi példa lekérte egy minta runbook utolsó feladatát, és megjeleníti annak állapotát, a runbook paramétereihez megadott értékeket és a feladat kimenetét.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

Az alábbi példa lekéri egy adott feladat kimenetét, és visszaadja az egyes rekordokat. Ha valamelyik rekord [kivételt](automation-runbook-execution.md#exceptions) tartalmaz, a szkript a kivételt írja az érték helyett. Ez a viselkedés azért hasznos, mert a kivételek olyan további információkat is nyújthatnak, amelyek a kimenet során nem a szokásos módon naplózhatóak.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Következő lépések

* A runbookok kezelésével kapcsolatos részletekért lásd: Runbook végrehajtása a [Azure Automation.](automation-runbook-execution.md)
* PowerShell-runbook előkészítéséhez lásd: [Szöveges runbookok szerkesztése](automation-edit-textual-runbook.md)a Azure Automation.
* A runbook végrehajtásával kapcsolatos hibák elhárításához lásd: [Runbook-problémák elhárítása.](troubleshoot/runbooks.md)
