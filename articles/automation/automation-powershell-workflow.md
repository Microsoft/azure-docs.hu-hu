---
title: A Azure Automation PowerShell-munkafolyamatának megismerése
description: Ez a cikk bemutatja a PowerShell-munkafolyamat és a PowerShell közötti különbségeket, valamint az Automation-runbookok alkalmazható fogalmakat.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 483d4c16f1b77bf7328857eb25e1571a741d144e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896918"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>A Azure Automation PowerShell-munkafolyamatának megismerése

A Azure Automationban lévő runbookok Windows PowerShell-munkafolyamatként, Windows Workflow Foundationt használó Windows PowerShell-parancsfájlokként valósulnak meg. A munkafolyamat olyan programozott, összekapcsolódó lépések sora, amik hosszan futó feladatokat végeznek el vagy több eszközön vagy felügyelt csomóponton keresztül végrehajtandó, több lépés koordinációját igénylik. 

A munkafolyamatok Windows PowerShell-szintaxissal íródnak, és a Windows PowerShell indította el, Windows Workflow Foundation. A munkafolyamatok normál parancsfájlokkal való használatának előnyei közé tartozik a művelet egyidejű teljesítménye több eszközön, valamint az automatikus helyreállítás a hibáktól. 

> [!NOTE]
> Egy PowerShell-munkafolyamati parancsfájl nagyon hasonlít egy Windows PowerShell-parancsfájlhoz, de olyan jelentős eltéréseket tartalmaz, amelyek zavaróak lehetnek az új felhasználók számára. Ezért javasoljuk, hogy csak akkor írja be a runbookok a PowerShell-munkafolyamat használatával, ha [ellenőrzőpontokat](#use-checkpoints-in-a-workflow)kell használnia. 

A cikkben szereplő témakörök részletes ismertetését lásd: [első lépések a Windows PowerShell-munkafolyamattal](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11)).

## <a name="use-workflow-keyword"></a>Munkafolyamat-kulcsszó használata

A PowerShell-parancsfájlok PowerShell-munkafolyamatra való átalakításának első lépése a `Workflow` kulcsszóval együtt. A munkafolyamatok a `Workflow` kulcsszóval kezdődnek, majd a szkript törzsét kapcsos zárójelek közé. A munkafolyamat neve követi a `Workflow` kulcsszót, ahogy az a következő szintaxisban látható:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

A munkafolyamat nevének meg kell egyeznie az Automation-runbook nevével. Ha a runbook importálása folyamatban van, akkor a fájl nevének meg kell egyeznie a munkafolyamat nevével, és a **. ps1** fájlban kell végződnie.

Ha paramétereket szeretne hozzáadni a munkafolyamathoz, használja a `Param` kulcsszót ugyanúgy, mint egy parancsfájlban.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>A PowerShell-munkafolyamat kódjának és a PowerShell-parancsfájl kódjának megismerése

A PowerShell-munkafolyamat kódja majdnem azonos a PowerShell-szkript kódjával, kivéve néhány jelentős változást. A következő szakaszok azokat a módosításokat ismertetik, amelyeket a PowerShell-szkriptek futtatásához kell végeznie egy munkafolyamatban.

### <a name="activities"></a>Tevékenységek

A tevékenységek egy adott munkafolyamatban egy adott feladat, amely egy sorozatban lett elvégezve. A Windows PowerShell Workflow futtatórendszer automatikusan tevékenységgé alakít számos Windows PowerShell-parancsmagot egy munkafolyamat futtatásakor. Ha megadja a parancsmagok egyikét a runbook, a Windows Workflow Foundation a megfelelő tevékenységet futtatja. 

Ha egy parancsmaghoz nem tartozik megfelelő tevékenység, a Windows PowerShell-munkafolyamat automatikusan futtatja a parancsmagot egy [InlineScript](#use-inlinescript) tevékenységben. Egyes parancsmagok ki vannak zárva, és nem használhatók munkafolyamatban, ha explicit módon belefoglalja őket egy InlineScript-blokkba. További információ: [tevékenységek használata a parancsfájl-munkafolyamatokban](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

A munkafolyamatok tevékenységeinek működése közös paraméterek egy halmazával konfigurálható. Lásd: [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Pozíciós paraméterek

A munkafolyamatokban nem használhat pozíciós paramétereket tevékenységekkel és parancsmagokkal. Ezért a paraméterek nevét kell használnia. Vegye figyelembe a következő kódot, amely az összes futó szolgáltatást lekéri:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Ha ezt a kódot egy munkafolyamatban próbálja meg futtatni, a `Parameter set cannot be resolved using the specified named parameters.` probléma megoldásához a következő példában látható módon adja meg a paraméter nevét:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deszerializált objektumok

A munkafolyamatokban lévő objektumok deszerializálva vannak, ami azt jelenti, hogy a tulajdonságaik továbbra is elérhetők, de nem a módszereiket.  Vegyük például a következő PowerShell-kódot, amely leállítja a szolgáltatást az `Stop` objektum metódusának használatával `Service` .

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Ha ezt a munkafolyamatban próbálja meg futtatni, a következő hibaüzenet jelenik meg: `Method invocation is not supported in a Windows PowerShell Workflow.`

Az egyik lehetőség, hogy becsomagolja ezt a két sornyi kódot egy [InlineScript](#use-inlinescript) -blokkba. Ebben az esetben `Service` a blokkon belüli szolgáltatási objektumot jelöli.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Egy másik lehetőség, hogy egy másik parancsmagot használ, amely a metódussal megegyező funkcióval rendelkezik, ha van ilyen. A példánkban a `Stop-Service` parancsmag ugyanazokat a funkciókat biztosítja, mint a `Stop` metódus, és használhatja a következő kódot egy munkafolyamathoz.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>InlineScript használata

A `InlineScript` tevékenység akkor lehet hasznos, ha egy vagy több parancsot a PowerShell-munkafolyamat helyett hagyományos PowerShell-parancsfájlként kell futtatnia.  Míg a munkafolyamaton belüli parancsokat a szolgáltatás a Windows Workflow Foundation rendszernek küldi el feldolgozásra, az InlineScript blokkon belüli parancsok feldolgozását a Windows PowerShell végzi.

A InlineScript az alábbi, alább látható szintaxist használja.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

A kimenetet egy InlineScript adhatja vissza, ha a kimenetet egy változóhoz rendeli. A következő példa leállítja a szolgáltatást, majd kiírja a szolgáltatás nevét.

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

Értékeket adhat át egy InlineScript blokkba, de **$using** hatókör-módosítót kell használnia.  A következő példa megegyezik az előző példával, azzal a különbséggel, hogy a szolgáltatás nevét egy változó adja meg.

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

Habár a InlineScript tevékenységek bizonyos munkafolyamatokban kritikus fontosságúak lehetnek, nem támogatják a munkafolyamat-szerkezeteket. Ezeket csak akkor használja, ha az a következő okok miatt szükséges:

* Az [ellenőrzőpontok](#use-checkpoints-in-a-workflow) nem használhatók InlineScript blokkon belül. Ha hiba történik a blokkon belül, annak a blokk elejétől kell folytatódnia.
* InlineScript blokkon belül nem használható [párhuzamos végrehajtás](#use-parallel-processing) .
* A InlineScript hatással van a munkafolyamat skálázhatóságára, mivel a Windows PowerShell-munkamenetet a InlineScript-blokk teljes hosszára vonatkozóan tárolja.

További információ a InlineScript használatáról: [Windows PowerShell-parancsok futtatása munkafolyamatban](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) és [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Párhuzamos feldolgozás használata

A Windows PowerShell-munkafolyamatok egyik előnye, hogy az általános parancsprogramoktól eltérően párhuzamosan is végre tudnak hajtani parancsokat, nem csak egymást követően.

A `Parallel` kulcsszó használatával létrehozhat egy parancsfájl-blokkot több, párhuzamosan futó paranccsal. Ez a következő szintaxist használja. Ebben az esetben a Activity1 és a Activity2 egy időben indul el. A Activity3 csak akkor indul el, ha a Activity1 és a Activity2 is befejeződött.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Vegyük például a következő PowerShell-parancsokat, amelyek több fájlt másolnak egy hálózati célhelyre. Ezek a parancsok szekvenciálisan futnak, így az egyik fájlnak a következő elindítása előtt be kell fejeznie a másolást.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

A következő munkafolyamat párhuzamosan futtatja ugyanezeket a parancsokat, így azok mindegyike egyszerre kezdi a másolást.  A befejezési üzenet csak azt követően jelenik meg, hogy az összes másolása megtörtént.

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

A `ForEach -Parallel` létrehozással a gyűjtemény minden egyes eleménél egyszerre dolgozhat fel parancsokat. A gyűjtemény elemeinek feldolgozása párhuzamosan történik, míg a parancsblokkban levő parancsok egymást követően futnak le. Ez a folyamat az alábbi szintaxist használja. Ebben az esetben a Activity1 egy időben indul el a gyűjtemény összes eleméhez. Minden elem esetében a Activity2 a Activity1 befejezése után indul el. A Activity3 csak akkor indul el, ha a Activity1 és a Activity2 is befejeződött az összes elemnél. A `ThrottleLimit` párhuzamosság korlátozására a paramétert használjuk. A túl magas a `ThrottleLimit` problémát okozhat. A paraméter ideális értéke a `ThrottleLimit` környezetében számos tényezőtől függ. Kezdjen egy alacsony értékkel, és próbálkozzon különböző növekvő értékekkel, amíg meg nem találja, hogy az adott körülmények között működik.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Az alábbi példa hasonló az előző példában található fájlok párhuzamos másolásához.  Ebben az esetben egy üzenet jelenik meg az egyes fájlokhoz a másolás után.  A befejezést követően csak az összes másolt üzenet jelenik meg.

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
> Nem javasoljuk, hogy párhuzamosan futtassa a gyermek runbookok, mivel ez a művelet megbízhatatlan eredményt ad. A gyermek runbook kimenete időnként nem jelenik meg, és egy gyermek runbook beállításai befolyásolhatják a másik párhuzamos gyermek runbookok. `VerbosePreference`Előfordulhat, hogy a, a, `WarningPreference` és a többi változó nem propagálja a gyermek runbookok. Ha a gyermek runbook megváltoztatja ezeket az értékeket, előfordulhat, hogy a hívás után nem lesznek megfelelően visszaállítva.

## <a name="use-checkpoints-in-a-workflow"></a>Ellenőrzőpontok használata egy munkafolyamatban

Az ellenőrzőpont a munkafolyamat aktuális állapotának pillanatképe, amely tartalmazza a változók aktuális értékeit és az adott ponthoz generált összes kimenetet. Ha egy munkafolyamat hibát jelez vagy felfüggesztve van, akkor a következő futtatásakor a legutolsó ellenőrzőponttól indul el, nem az elején. 

Az ellenőrzőpontot beállíthatja a munkafolyamatban a `Checkpoint-Workflow` tevékenységgel. Azure Automation rendelkezik egy [Fair Share](automation-runbook-execution.md#fair-share)nevű funkcióval, amelynek a három órát futtató runbook el lett távolítva a más runbookok futtatásának engedélyezéséhez. Végül a memóriából eltávolított runbook újratöltődik. Ha igen, folytatja a végrehajtást a runbook utolsó ellenőrzőpontján.

Annak biztosításához, hogy a runbook végül is befejeződik, olyan intervallumokat kell hozzáadnia, amelyek három óránál rövidebb ideig futnak. Ha az egyes futtatások során új ellenőrzőpontot adnak hozzá, és ha a runbook egy hiba miatt három óra elteltével kizárták, akkor a runbook határozatlan időre folytatódik.

A következő példában kivétel keletkezik a Activity2 után, ami a munkafolyamat befejezését okozza. A munkafolyamat ismételt futtatásakor a Activity2 futtatásával indul el, mivel ez a tevékenység csak az utolsó ellenőrzőpont-készlet után volt.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Állítsa be az ellenőrzőpontokat egy munkafolyamatban olyan tevékenységek után, amelyek valószínűleg kivételt jelenthetnek, és nem ismételhetők meg, ha a munkafolyamat folytatódik. Előfordulhat például, hogy a munkafolyamat létrehoz egy virtuális gépet. A virtuális gép létrehozásához szükséges parancsok előtt és után is beállíthat ellenőrzőpontot. Ha a létrehozás sikertelen, akkor a rendszer megismétli a parancsokat, ha a munkafolyamatot újra elindítják. Ha a munkafolyamat a létrehozás sikeressége után leáll, a rendszer nem hozza létre újra a virtuális gépet a munkafolyamat folytatásakor.

Az alábbi példa egy hálózati helyre másol több fájlt, és minden fájl után beállítja az ellenőrzőpontot.  Ha a hálózati hely elvész, a munkafolyamat hibát jelez.  Ha újra elindítják, az utolsó ellenőrzőponton folytatódik. Csak a már másolt fájlok lesznek kihagyva.

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

Mivel a felhasználói név hitelesítő adatai nem maradnak meg a [felfüggesztési munkafolyamat](/powershell/module/psworkflow/about/about_suspend-workflow) tevékenységének meghívása vagy az utolsó ellenőrzőpont után, a hitelesítő adatokat NULL értékre kell állítania, majd újra le kell kérni azokat az objektum-áruházból, `Suspend-Workflow` vagy az ellenőrzőpont neve után.  Ellenkező esetben előfordulhat, hogy a következő hibaüzenet jelenik meg: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

A következő kód bemutatja, hogyan kezelheti ezt a helyzetet a PowerShell-munkafolyamatok runbookok.

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
> A nem grafikus PowerShell-runbookok, `Add-AzAccount` valamint a `Add-AzureRMAccount` [csatlakozási-AzAccount](/powershell/module/az.accounts/connect-azaccount)aliasai. Ezeket a parancsmagokat használhatja, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. Előfordulhat, hogy frissítenie kell a modulokat akkor is, ha nemrég létrehozott egy új Automation-fiókot. Ezeknek a parancsmagoknak a használata nem kötelező, ha a hitelesítést egy egyszerű szolgáltatásnév használatával konfigurált futtató fiókkal végzi.

Az ellenőrzőpontokkal kapcsolatos további információkért lásd: [ellenőrzőpontok hozzáadása egy parancsfájl-munkafolyamathoz](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Következő lépések

* A PowerShell-munkafolyamatok runbookok kapcsolatos tudnivalókat a következő témakörben talál [: oktatóanyag: PowerShell munkafolyamat-Runbook létrehozása](learn/automation-tutorial-runbook-textual.md).
