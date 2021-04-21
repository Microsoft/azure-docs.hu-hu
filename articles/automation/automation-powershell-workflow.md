---
title: A PowerShell-munkafolyamat Azure Automation
description: Ez a cikk a PowerShell-munkafolyamat és a PowerShell közötti különbségeket, valamint az Automation-runbookokhoz alkalmazható fogalmakat tanítja be.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b3beb8b3eda4dfabf9240aa328a24d5f855689b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833507"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>A PowerShell-munkafolyamat Azure Automation

A Azure Automation runbookok Windows PowerShell munkafolyamatként, Windows PowerShell szkriptként vannak megvalósítva, Windows Workflow Foundation. A munkafolyamat olyan programozott, összekapcsolódó lépések sora, amik hosszan futó feladatokat végeznek el vagy több eszközön vagy felügyelt csomóponton keresztül végrehajtandó, több lépés koordinációját igénylik. 

Bár a munkafolyamatot Windows PowerShell szintaxissal írják, Windows PowerShell elindítják, a munkafolyamatot a Windows Workflow Foundation. A munkafolyamatok normál szkriptekkel szembeni előnyei közé tartozik egy művelet egyidejű teljesítménye több eszközön, valamint az automatikus helyreállítás a hibákból. 

> [!NOTE]
> A PowerShell-munkafolyamat parancsfájlja nagyon hasonlít egy Windows PowerShell szkripthez, de van néhány jelentős különbsége, amelyek zavaróak lehetnek az új felhasználók számára. Ezért azt javasoljuk, hogy runbookokat csak akkor írjon PowerShell-munkafolyamattal, ha [ellenőrzőpontokat kell használnia.](#use-checkpoints-in-a-workflow) 

A cikkben található témakörök részletes részleteiért lásd: [Getting Started with Windows PowerShell Workflow (Ismerkedés a Windows PowerShell munkafolyamattal).](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11))

## <a name="use-workflow-keyword"></a>A Workflow kulcsszó használata

A PowerShell-parancsfájlOk PowerShell-munkafolyamatká konvertálásának első lépése a kulcsszó `Workflow` bezárása. A munkafolyamat a kulcsszóval kezdődik, amelyet a szkript törzse követ `Workflow` zárójelek között. A munkafolyamat neve követi a `Workflow` kulcsszót, ahogy az az alábbi szintaxisban látható:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

A munkafolyamat nevének meg kell egyeznie az Automation-runbook nevével. Ha a runbook importálása folyamatban van, a fájlnévnek egyeznie kell a munkafolyamat nevével, és **.ps1 végződnie kell.**

Ha paramétereket szeretne hozzáadni a munkafolyamathoz, használja a kulcsszót, ahogyan egy `Param` szkriptben tenné.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Megismeri a PowerShell-munkafolyamat kódja és a PowerShell-szkriptkód közötti különbségeket

A PowerShell-munkafolyamat kódja szinte teljesen megegyezik a PowerShell-szkriptkóddal, néhány jelentős módosítás kivételével. Az alábbi szakaszok a PowerShell-parancsfájl munkafolyamatban való futtatásához szükséges módosításokat ismertetik.

### <a name="activities"></a>Tevékenységek

A tevékenység egy adott feladat egy munkafolyamatban, amely egy sorozatban van hajtva végre. A Windows PowerShell Workflow futtatórendszer automatikusan tevékenységgé alakít számos Windows PowerShell-parancsmagot egy munkafolyamat futtatásakor. Amikor megadja ezen parancsmagok valamelyikét a runbookban, a megfelelő tevékenységet a Windows Workflow Foundation. 

Ha egy parancsmag nem rendelkezik megfelelő tevékenységgel, Windows PowerShell munkafolyamat automatikusan [inlineScript-tevékenységben](#use-inlinescript) futtatja a parancsmagot. Egyes parancsmagok ki vannak zárva, és csak akkor használhatók munkafolyamatokban, ha explicit módon egy InlineScript-blokkba foglalja őket. További információ: [Tevékenységek használata szkript-munkafolyamatokban.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11))

A munkafolyamatok tevékenységeinek működése közös paraméterek egy halmazával konfigurálható. Lásd: [about_WorkflowCommonParameters.](/powershell/module/psworkflow/about/about_workflowcommonparameters)

### <a name="positional-parameters"></a>Pozícióparaméterek

A pozícióparaméterek nem használhatók tevékenységekkel és parancsmagokkal egy munkafolyamatban. Ezért paraméterneveket kell használnia. Tekintse meg az alábbi kódot, amely lekérte az összes futó szolgáltatást:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Ha ezt a kódot egy munkafolyamatban próbálja futtatni, a következő üzenet jelenik meg: To correct for this issue, provide the parameter name, as the `Parameter set cannot be resolved using the specified named parameters.` following example:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deserializált objektumok

A munkafolyamatokban lévő objektumok deserializálva vannak, ami azt jelenti, hogy a tulajdonságaik továbbra is elérhetők, de a metódusaik nem.  Vegyük például a következő PowerShell-kódot, amely leállít egy szolgáltatást az `Stop` objektum `Service` metódusával.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Ha ezt egy munkafolyamatban próbálja futtatni, a következő hibaüzenet jelenik meg: `Method invocation is not supported in a Windows PowerShell Workflow.`

Az egyik lehetőség, hogy ezt a két kódsort egy [InlineScript blokkba csomagolja.](#use-inlinescript) Ebben az esetben `Service` a egy szolgáltatásobjektumot képvisel a blokkon belül.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Egy másik lehetőség egy másik parancsmag használata, amely ugyanazokkal a funkciókkal rendelkezik, mint a metódus, ha van ilyen. A példánkban a parancsmag ugyanazt a funkciót biztosítja, mint a metódus, és a következő kódot használhatja `Stop-Service` `Stop` egy munkafolyamathoz.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Az InlineScript használata

A tevékenység akkor hasznos, ha egy vagy több parancsot hagyományos PowerShell-szkriptként kell futtatnia `InlineScript` a PowerShell-munkafolyamat helyett.  Míg a munkafolyamaton belüli parancsokat a szolgáltatás a Windows Workflow Foundation rendszernek küldi el feldolgozásra, az InlineScript blokkon belüli parancsok feldolgozását a Windows PowerShell végzi.

Az InlineScript az alábbi szintaxist használja.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Egy InlineScript kimenetét úgy adhatja vissza, hogy a kimenetet egy változóhoz rendeli. Az alábbi példa leállít egy szolgáltatást, majd kiállítja a szolgáltatás nevét.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Értékeket inlineScript-blokkba is átadhat, de a **hatókör-$Using** kell használnia.  Az alábbi példa megegyezik az előző példával, azzal a kivételvel, hogy a szolgáltatásnevet egy változó biztosítja.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Bár az InlineScript-tevékenységek kritikus fontosságúak lehetnek bizonyos munkafolyamatokban, nem támogatják a munkafolyamat-szerkezeteket. Csak akkor használja őket, ha az alábbi okokból szükséges:

* Az InlineScript-blokkban nem használhat ellenőrzőpontokat. [](#use-checkpoints-in-a-workflow) Ha hiba történik a blokkon belül, a blokk elejétől kell folytatódni.
* InlineScript-blokkon [belül](#use-parallel-processing) nem használhat párhuzamos végrehajtást.
* Az InlineScript hatással van a munkafolyamat méretezhetőségére, mivel Windows PowerShell munkamenetet az InlineScript blokk teljes hosszára.

Az InlineScript használatával kapcsolatos további információkért lásd: [Running Windows PowerShell Commands in a Workflow and](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Párhuzamos feldolgozás használata

A Windows PowerShell-munkafolyamatok egyik előnye, hogy az általános parancsprogramoktól eltérően párhuzamosan is végre tudnak hajtani parancsokat, nem csak egymást követően.

A kulcsszóval egy szkriptblokkot hozhat létre több, egyidejűleg futó `Parallel` paranccsal. Ez az alábbi szintaxist használja. Ebben az esetben az Activity1 és az Activity2 tevékenység egyszerre kezdődik. Az Activity3 csak az Activity1 és az Activity2 befejeződése után indul el.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Vegyük például a következő PowerShell-parancsokat, amelyek több fájlt másolnak egy hálózati célhelyre. Ezek a parancsok egymás után futnak, így a következő elindulása előtt egy fájlnak be kell fejeznie a másolást.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Az alábbi munkafolyamat párhuzamosan futtatja ezeket a parancsokat, hogy mindegyik egyszerre kezdje el a másolást.  A befejezési üzenet csak az összes másolása után jelenik meg.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

A szerkezet használatával egy gyűjtemény minden eleméhez egyidejűleg feldolgozhat `ForEach -Parallel` parancsokat. A gyűjtemény elemeinek feldolgozása párhuzamosan történik, míg a parancsblokkban levő parancsok egymást követően futnak le. Ez a folyamat az alábbi szintaxist használja. Ebben az esetben az Activity1 a gyűjtemény összes elemével egy időben kezdődik. Az Activity2 minden elemhez az Activity1 befejezése után indul el. Az Activity3 csak akkor indul el, ha az Activity1 és az Activity2 is befejeződött az összes elemnél. A párhuzamosság `ThrottleLimit` korlátozására a paramétert használjuk. Ha a túl magas, `ThrottleLimit` az problémákat okozhat. A paraméter ideális `ThrottleLimit` értéke a környezet számos tényezőjétől függ. Kezdjen egy alacsony értékkel, és próbálkozzon eltérő növekvő értékekkel, amíg nem talál olyan értéket, amely az adott körülményeknek megfelelő.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Az alábbi példa hasonló az előző példához fájlok párhuzamos másolásához.  Ebben az esetben a másolás után minden fájlhoz megjelenik egy üzenet.  Csak az összes másolása után jelenik meg a végleges befejezési üzenet.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Nem javasoljuk a gyermek runbookok párhuzamos futtatását, mivel ez megbízhatatlan eredményekkel jár. A gyermek runbook kimenete néha nem mutatja a et, és az egyik gyermek runbook beállításai hatással lehetnek a többi párhuzamos gyermek runbookra. Előfordulhat, hogy a ,, és más változók nem propagálnak a `VerbosePreference` `WarningPreference` gyermek runbookba. Ha pedig a gyermek runbook módosítja ezeket az értékeket, előfordulhat, hogy a meghívás után nem lesznek megfelelően helyreállítva.

## <a name="use-checkpoints-in-a-workflow"></a>Ellenőrzőpontok használata munkafolyamatokban

Az ellenőrzőpont a munkafolyamat aktuális állapotának pillanatképe, amely tartalmazza a változók aktuális értékeit és az adott ponthoz létrehozott kimeneteket. Ha egy munkafolyamat hibába végződik vagy fel van függesztve, a következő futtatásakor az utolsó ellenőrzőponttól indul, nem pedig az elejétől. 

A tevékenységgel ellenőrzőpontot állíthat be egy `Checkpoint-Workflow` munkafolyamatban. Azure Automation egy igazságos megosztás nevű funkcióval [rendelkezik,](automation-runbook-execution.md#fair-share)amely számára a rendszer eltávolítja a memóriából a három órán át futó runbookokat, hogy más runbookok is futtassanak. Végül a rendszer újra betölti a eltávolított runbookot. Ha igen, a runbook utolsó ellenőrzőpontján folytatja a végrehajtást.

Annak érdekében, hogy a runbook biztosan befejeződik, három óránál rövidebb időközönként ellenőrzőpontokat kell hozzáadnia. Ha minden futtatás során új ellenőrzőpontot ad hozzá, és a runbookot egy hiba miatt három óra után ki is lehet zárva, a runbook határozatlan ideig folytatódik.

A következő példában kivétel történik az Activity2 tevékenység után, ami a munkafolyamat végét okozza. A munkafolyamat újrafuttatása az Activity2 futtatásával kezdődik, mivel ez a tevékenység nem sokkal az utolsó beállított ellenőrzőpont után történt.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Állítson be ellenőrzőpontokat egy munkafolyamatban az olyan tevékenységek után, amelyek kivételt képeznek, és nem szabad megismételni a munkafolyamat folytatásakor. A munkafolyamat például létrehozhat egy virtuális gépet. A parancsok előtt és után is beállíthatja az ellenőrzőpontot a virtuális gép létrehozásához. Ha a létrehozás sikertelen, a rendszer megismétli a parancsokat, ha a munkafolyamatot újra elindítják. Ha a munkafolyamat a sikeres létrehozás után meghiúsul, a virtuális gép nem jön létre újra a munkafolyamat folytatásakor.

Az alábbi példa több fájlt másol egy hálózati helyre, és minden fájl után beállítja az ellenőrzőpontot.  Ha a hálózati hely elveszett, a munkafolyamat hibába fog végződni.  Amikor újra elindul, az utolsó ellenőrzőpontnál folytatódik. Csak a már másolt fájlokat hagyja ki a rendszer.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Mivel a felhasználónév hitelesítő adatai nem maradnak meg a [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) tevékenység hívása után vagy az utolsó ellenőrzőpont után, a hitelesítő adatokat null értékűre kell állítani, majd újra le kell hívni az eszköztárolóból az vagy az ellenőrzőpont hívása `Suspend-Workflow` után.  Ellenkező esetben a következő hibaüzenet jelenhet meg: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Az alábbi kód bemutatja, hogyan kezelhető ez a helyzet a PowerShell-munkafolyamat-runbookokban.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Nem grafikus PowerShell-runbookok esetén, amelyek a `Add-AzAccount` `Add-AzureRMAccount` [Connect-AzAccount aliasai.](/powershell/module/az.accounts/connect-azaccount) Használhatja ezeket a parancsmagokat, [](automation-update-azure-modules.md) vagy frissítheti az Automation-fiókjában található modulokat a legújabb verziókra. Előfordulhat, hogy akkor is frissítenie kell a modulokat, ha most hozott létre egy új Automation-fiókot. Ezeknek a parancsmagoknak a használata nem szükséges, ha szolgáltatásnévvel konfigurált futtatott fiókkal hitelesít.

További információ az ellenőrzőpontokról: [Ellenőrzőpontok hozzáadása parancsfájl-munkafolyamathoz.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11))

## <a name="next-steps"></a>Következő lépések

* A PowerShell-munkafolyamat-runbookokkal kapcsolatos további információkért [lásd: Oktatóanyag: PowerShell-munkafolyamat-runbook létrehozása.](learn/automation-tutorial-runbook-textual.md)
