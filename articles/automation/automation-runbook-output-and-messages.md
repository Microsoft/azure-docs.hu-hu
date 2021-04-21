---
title: Runbook kimenetének és üzenetstreamének konfigurálása
description: Ez a cikk bemutatja, hogyan valósíthat meg hibakezelési logikát, és ismerteti a kimeneti és üzenetstreameket Azure Automation runbookban.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 033900ddd0bd19332b4a9a996c68b3b187d631c4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833561"
---
# <a name="configure-runbook-output-and-message-streams"></a>Runbook kimenetének és üzenetstreamének konfigurálása

A Azure Automation runbookok legtöbb esetben valamilyen kimenettel is rendelkezik. Ez a kimenet lehet a felhasználónak küldött hibaüzenet, vagy egy összetett objektum, amely egy másik runbookhoz használható. Windows PowerShell több [streamet is biztosít a](/powershell/module/microsoft.powershell.core/about/about_redirection) kimenet parancsfájlból vagy munkafolyamatból való küld küldelére. Azure Automation az egyes streamekkel másképp működik. Runbook létrehozásakor kövesse a streamek használatának ajánlott eljárásait.

Az alábbi táblázat röviden ismerteti az egyes streameket a közzétett runbookok Azure Portal és a runbookok tesztelése során [megjelenő viselkedésével.](./manage-runbooks.md) A kimeneti stream a runbookok közötti kommunikációhoz használt fő stream. A többi stream üzenetstreamként van besorolva, és a felhasználónak szánt információkkal szolgál.

| Stream | Description | Közzétett | Tesztelés |
|:--- |:--- |:--- |:--- |
| Hiba |A felhasználónak szóló hibaüzenet. A kivételtől eltérően a runbook alapértelmezés szerint a hibaüzenet után is folytatódik. |A feladatelőzményekbe írva |Megjelenik a Tesztkimenet panelen |
| Hibakeresés |Interaktív felhasználó számára készült üzenetek. Runbookban nem használható. |Nem íródott a feladatelőzményekbe |Nem jelenik meg a Tesztkimenet panelen |
| Kimenet |Másik runbookok számára készült objektum. |A feladatelőzményekbe írva |Megjelenik a Tesztkimenet panelen |
| Előrehaladás |A runbook összes tevékenysége előtt és után automatikusan létrejövő rekordok. A runbooknak nem szabad a saját folyamatrekordjait létrehoznia, mert interaktív felhasználóknak szánták őket. |Csak akkor íródott a feladatelőzményekbe, ha a runbook folyamatnaplózása be van kapcsolva |Nem jelenik meg a Tesztkimenet panelen |
| Részletes |Általános vagy hibakeresési információkat építő üzenetek. |Csak akkor íródott a feladatelőzményekbe, ha a runbookhoz be van kapcsolva a részletes naplózás |Csak akkor jelenik meg a Kimenet tesztelése panelen, ha `VerbosePreference` a változó Értéke Folytatás a runbookban |
| Figyelmeztetés |A felhasználónak szóló figyelmeztető üzenet. |A feladatelőzményekbe írva |Megjelenik a Tesztkimenet panelen |

## <a name="use-the-output-stream"></a>A kimeneti stream használata

A kimeneti stream a parancsfájl vagy munkafolyamat által létrehozott objektumok kimenetéhez használatos, ha az megfelelően fut. Azure Automation elsősorban az aktuális runbookot hívó szülő runbookok által használt objektumokhoz használja ezt a [streamet.](automation-child-runbooks.md) Amikor egy szülő [beágyazottan hív meg egy runbookot, a](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution)gyermek a kimeneti streamből a szülőnek adja vissza az adatokat.

A runbook a kimeneti streamet használja az általános információk az ügyféllel való kommunikációhoz, csak akkor, ha egy másik runbook soha nem hív meg. Ajánlott eljárásként azonban a runbookok általában a részletes [streamet](#write-output-to-verbose-stream) használják, hogy általános információkat kommunikáljanak a felhasználóval.

A write-output parancs használatával a runbooknak adatokat kell írnia a [kimeneti streambe.](/powershell/module/microsoft.powershell.utility/write-output) Másik lehetőségként a saját sorában is használhatja az objektumot a szkriptben.

```powershell
#The following lines both write an object to the output stream.
Write-Output -InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Függvény kimenetének leírója

Amikor egy Runbook-függvény a kimeneti streambe ír, a kimenetet a rendszer visszaadja a runbooknak. Ha a runbook ezt a kimenetet egy változóhoz rendeli, a kimenet nem lesz a kimeneti streambe írva. A függvényen belül bármely más streambe való írás a runbook megfelelő streamjébe ír. Tekintse meg az alábbi PowerShell-munkafolyamat-forgatókönyvet.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

A runbook-feladat kimeneti streame a következő:

```output
Output inside of function
Output outside of function
```

A runbook-feladat részletes adatfolyama a következő:

```output
Verbose outside of function
Verbose inside of function
```

Miután közzétette a runbookot, és mielőtt elindítja, a részletes naplózást is be kell kapcsolnia a runbook beállításaiban a részletes stream kimenetének lekért érdekében.

### <a name="declare-output-data-type"></a>Kimeneti adattípus deklarálása

Az alábbiakban példákat talál a kimeneti adattípusok használatára:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Kimeneti adattípus deklarása egy munkafolyamatban

A munkafolyamat a kimenet adattípusát az [OutputType attribútum használatával határozza meg.](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) Ez az attribútum nincs hatással a futásidőben, de jelzi a runbook várt kimenetének tervezéskor. Ahogy a runbookok eszközkészlete folyamatosan fejlődik, egyre fontosabb a kimeneti adattípusok deklarozása a tervezés során. Ezért ajánlott ezt a deklarációt minden létrehozott runbookban szerepeltetni.

Az alábbi példában a runbook kimenete egy sztring típusú objektum, és megadja az elvárt kimenettípust. Amennyiben a runbook kimenete egy bizonyos típusú tömb, akkor is egy típust érdemes megadni, nem pedig a típusok egy tömbjét.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Kimeneti adattípus deklarása grafikus runbookban

Ha egy kimeneti típust egy grafikus vagy grafikus PowerShell  munkafolyamat-runbookban deklarál, válassza a Bemenet és kimenet menüpontot, és adja meg a kimenet típusát. Javasoljuk, hogy használja a teljes .NET-osztálynevet, hogy a típus könnyen azonosítható legyen, amikor egy szülő runbook hivatkozik rá. A teljes név használata a osztály összes tulajdonságát elérhetővé teszi a runbook adatbuszában, és növeli a rugalmasságot, ha a tulajdonságokat feltételes logikához, naplózáshoz és más Runbook-tevékenységek értékeiként való hivatkozáshoz használják.<br> ![Runbook bemenete és kimenete beállítás](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Miután megadott egy értéket  a Kimenet típusa mezőben a Bemenet és a Kimenet tulajdonságpanelen, mindenképpen kattintson a vezérlőelemen kívülre, hogy felismerje a bejegyzést.

Az alábbi példa két grafikus runbookot mutat be, amelyek bemutatják a Bemenet és a Kimenet funkciót. A moduláris runbook-tervezési modell alkalmazásával egyetlen runbookot használhat a Runbook hitelesítése sablonként, amely az Azure-hitelesítést kezeli a run as-fiókkal. A második runbook, amely általában alaplogikát hajt végre egy adott forgatókönyv automatizálása érdekében, ebben az esetben végrehajtja a Runbook hitelesítése sablont. Megjeleníti az eredményeket a Tesztkimenet panelen. Normál körülmények között azt kellene megtennie, hogy ez a runbook a gyermek runbook kimenetét stb.

Itt van a **AuthenticateTo-Azure runbook alapszintű** logikája.<br> ![Példa runbooksablon ](media/automation-runbook-output-and-messages/runbook-authentication-template.png) hitelesítésére.

A runbook tartalmazza a kimeneti `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` típust, amely a hitelesítési profil tulajdonságait adja vissza.<br> ![Példa runbook kimenettípusra](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Bár ez a runbook egyszerű, itt egy konfigurációs elemet kell hívni. Az utolsó tevékenység végrehajtja a parancsmagot, hogy profiladatokat írjon egy változóba a paraméterhez egy `Write-Output` PowerShell-kifejezés `Inputobject` használatával. Ez a paraméter a paraméterhez `Write-Output` szükséges.

A példában a második runbook **Test-ChildOutputType** névvel egyszerűen két tevékenységet határoz meg.<br> ![Példa gyermekkimenet-típus runbookra](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Az első tevékenység a **AuthenticateTo-Azure** runbookot hívja meg. A második tevékenység úgy futtatja a parancsmagot, hogy az Adatforrás beállítása `Write-Verbose` **Tevékenységkimenet.**  Emellett a **Mező elérési útja a** következőre van **állítva: Context.Subscription.SubscriptionName**, a környezet kimenete a **AuthenticateTo-Azure** runbookból.<br> ![Részletes írási parancsmag paraméter adatforrása](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Az eredményül kapott kimenet az előfizetés neve.<br> ![Test-ChildOutputType Runbook eredményei](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Üzenetstreamek

A kimeneti streamtől eltérően az üzenetstreamek információt küldnek a felhasználónak. A különböző típusú információkhoz több üzenetstream is rendelkezésre Azure Automation különböző módon kezeli az egyes streameket.

### <a name="write-output-to-warning-and-error-streams"></a>Kimenet írása figyelmeztető és hibastreamekbe

A figyelmeztetés és a hiba a runbookban előforduló naplóproblémákat streameli. Azure Automation runbook végrehajtásakor ezeket a streameket a feladatelőzményekbe írja. Az Automatizálás a runbook tesztelve Azure Portal tesztnaplókban található Tesztkimenet panelen tartalmazza a streameket.

Alapértelmezés szerint a runbook végrehajtása figyelmeztetés vagy hiba után is folytatódik. Az üzenet létrehozása előtt beállíthatja, hogy a runbook felfüggesztve [](#work-with-preference-variables) legyen egy figyelmeztetés vagy hiba esetén. Ha például a runbook egy kivétel esetében is felfüggeszti a hibát, állítsa a változót `ErrorActionPreference` Stop (Leállítás) értékre.

Hozzon létre egy figyelmeztetést vagy egy hibaüzenetet a [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) vagy [Write-Error](/powershell/module/microsoft.powershell.utility/write-error) parancsmag használatával. A tevékenységek a figyelmeztetési és hibastreamekbe is írhatnak.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning -Message "This is a warning message."
Write-Error -Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Kimenet írása hibakeresési streambe

Azure Automation a hibakeresési üzenetstreamet használja az interaktív felhasználók számára. Alapértelmezés szerint Azure Automation a rendszer nem rögzít hibakeresési streamadatokat, csak a kimeneti, a hiba- és a figyelmeztetési adatokat, valamint a részletes adatokat, ha a runbook az adatok rögzítésére van konfigurálva.

A hibakeresési streamadatok rögzítéséhez két műveletet kell végrehajtania a runbookban:

1. Állítsa be a `$GLOBAL:DebugPreference="Continue"` változót, amely arra utasítja a PowerShellt, hogy folytassa a munkát, amikor hibakeresési üzenet jelenik meg.  A **$GLOBAL:** rész arra utasítja a PowerShellt, hogy ezt a globális hatókörben tegye meg, nem pedig az utasítás végrehajtásakor szereplő helyi hatókörben.

1. Irányítsa át a nem rögzíti hibakeresési streamet egy olyan streambe, amit rögzítünk, például a *kimenetbe.* Ezt a PowerShell átirányításának a végrehajtandó utasításra való beállításával lehet végrehajtani. A PowerShell átirányításával kapcsolatos további információkért lásd: About Redirection (Tudnivalók az [átirányításról).](/powershell/module/microsoft.powershell.core/about/about_redirection)

#### <a name="examples"></a>Példák

Ebben a példában a runbook a és parancsmagok használatával van konfigurálva azzal a céllal, hogy két különböző `Write-Output` `Write-Debug` streamet adjanak ki.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Ha a runbookot a megfelelő ként szeretné végrehajtani, a runbook feladatának kimeneti panele a következő kimenetet streamelné:

```output
This is an output message.
```

Ebben a példában a runbook az előző példához hasonlóan van konfigurálva, azzal a kivételt leszámítva, hogy az utasítás a utasítás végén található `$GLOBAL:DebugPreference="Continue"` `5>&1` `Write-Debug` kiegészítéssel együtt szerepel.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

A runbook végrehajtása esetén a runbook feladatának kimeneti panele a következő kimenetet streamelné:

```output
This is an output message.
This is a debug message.
```

Ez azért fordul elő, mert a utasítás arra utasítja a PowerShellt, hogy jelenítsen meg hibakeresési üzeneteket, és az utasítás végének kiegészítéssel arra utasítja a PowerShellt, hogy irányítsa át az `$GLOBAL:DebugPreference="Continue"` `5>&1` `Write-Debug` 5. streamet (hibakeresés) az 1. streamre (kimenet).

### <a name="write-output-to-verbose-stream"></a>Kimenet írása részletes streambe

A részletes üzenetstream támogatja a runbookművelettel kapcsolatos általános információkat. Mivel a hibakeresési stream nem érhető el runbookhoz, a runbooknak részletes üzeneteket kell használnia a hibakeresési információkhoz.

Alapértelmezés szerint a feladatelőzmények teljesítménybeli okokból nem tárolnak részletes üzeneteket a közzétett runbookokból. A részletes üzenetek tárolására a Részletes rekordok naplózása  Azure Portal **Konfigurálás** lapon konfigurálhatja a közzétett runbookokat a részletes üzenetek naplózására. Ezt a beállítást csak akkor érdemes bekapcsolni, ha hibát keres a runbookban, vagy ha hibaelhárításra van szükség. A legtöbb esetben érdemes megtartani az alapértelmezett beállítást, amely nem naplóz részletes rekordokat.

A [runbookok tesztelői](./manage-runbooks.md)akkor sem jelennek meg részletes üzenetek, ha a runbook a részletes rekordok naplózására van konfigurálva. Ha részletes üzeneteket kell [megjelenítenie a runbook](./manage-runbooks.md)tesztelése közben, a változót a Continue `VerbosePreference` (Folytatás) beállításra kell állítania. Ha be van állítva ez a változó, részletes üzenetek jelennek meg a teszt kimenetének Azure Portal.

A következő kód részletes üzenetet hoz létre a [Write-Verbose](/powershell/module/microsoft.powershell.utility/write-verbose) parancsmag használatával.

```powershell
#The following line creates a verbose message.

Write-Verbose -Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Folyamatrekordok kezelés

A forgatókönyv **Configure** (Konfigurálás) Azure Portal konfigurálhatja a runbookot a folyamatrekordok naplózása érdekében. A teljesítmény maximalizálása érdekében az alapértelmezett beállítás a rekordok naplózása. A legtöbb esetben meg kell tartania az alapértelmezett beállítást. Ezt a beállítást csak akkor érdemes bekapcsolni, ha hibát keres a runbookban, vagy ha hibaelhárításra van szükség.

Ha engedélyezi a folyamatrekord-naplózást, a runbook minden tevékenység futtatása előtt és után rekordot ír a feladatelőzményekbe. A runbook tesztelése akkor sem jeleníti meg a folyamatjelző üzeneteket, ha a runbook a folyamatrekordok naplózására van konfigurálva.

>[!NOTE]
>A [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) parancsmag nem érvényes a runbookban, mivel ez a parancsmag interaktív felhasználóval való használatra szolgál.

## <a name="work-with-preference-variables"></a>Preferenciaváltozók használatával

A runbookok Windows PowerShell [](/powershell/module/microsoft.powershell.core/about/about_preference_variables) beállítási változók beállításával szabályozhatja a különböző kimeneti streamekbe küldött adatokra adott választ. Az alábbi táblázat a runbookok által használható preferenciaváltozókat sorolja fel az alapértelmezett és érvényes értékekkel. További értékek érhetők el a preferenciaváltozókhoz, ha az Windows PowerShell nem Azure Automation.

| Változó | Alapértelmezett érték | Érvényes értékek |
|:--- |:--- |:--- |
| `WarningPreference` |Folytatás |Leállítás<br>Folytatás<br>FolytatásCsendben |
| `ErrorActionPreference` |Folytatás |Leállítás<br>Folytatás<br>FolytatásCsendben |
| `VerbosePreference` |FolytatásCsendben |Leállítás<br>Folytatás<br>FolytatásCsendben |

A következő táblázat a forgatókönyvben érvényes beállításváltozó-értékek viselkedését sorolja fel.

| Érték | Működés |
|:--- |:--- |
| Folytatás |Naplózza az üzenetet, és folytatja a runbook futtatását. |
| FolytatásCsendben |Az üzenet naplózása nélkül folytatja a runbook futtatását. Ennek az értéknek az a hatása, hogy figyelmen kívül hagyja az üzenetet. |
| Leállítás |Naplózza az üzenetet, és felfüggeszti a runbook futtatását. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Runbook kimenetének és üzenetének lekérése

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Runbook kimenetének és üzenetének lekérése a Azure Portal

A runbook-feladatok részleteit a Azure Portal a runbook  Feladatok lapján. A feladat összegzése megjeleníti a bemeneti paramétereket és a kimeneti [streamet,](#use-the-output-stream)valamint a feladatra és az esetleges kivételekre vonatkozó általános információkat. A feladatelőzmények tartalmazzák a kimeneti streamből származó [üzeneteket, valamint a figyelmeztető és hibastreameket.](#write-output-to-warning-and-error-streams) Emellett a részletes [](#write-output-to-verbose-stream) adatfolyam üzeneteit [](#handle-progress-records) és a folyamatrekordokat is tartalmazza, ha a runbook a részletes és a folyamatrekordok naplózására van konfigurálva.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Runbook kimenetének és üzenetének lekérése a Windows PowerShell

A Windows PowerShell a [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) parancsmag használatával lekérheti a kimenetet és az üzeneteket egy runbookból. Ehhez a parancsmaghoz szükség van a feladat azonosítójára, valamint egy nevű paraméterre a lekérni `Stream` kívánt stream megadásához. A paraméterhez bármely értéket megadhat a feladat összes streamének lekérésekor.

A következő példában elindul a runbook, majd a rendszer megvárja, amíg annak végrehajtása befejeződik. Ha a runbook végrehajtása befejeződött, a szkript összegyűjti a runbook kimeneti streamét a feladatból.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    -AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Runbook kimenetének és üzenetének lekérése grafikus runbookok használatával

Grafikus runbookok esetén a kimenet és az üzenetek további naplózása tevékenységszintű nyomkövetés formájában érhető el. A nyomkövetésnek két szintje van: Alapszintű és Részletes. Az alapszintű nyomkövetés megjeleníti a runbookban található egyes tevékenységek kezdési és záró idejét, valamint az esetleges tevékenység-újrabecsületekkel kapcsolatos információkat. Néhány példa a kísérletek száma és a tevékenység kezdési ideje. A részletes nyomkövetés tartalmazza az alapszintű nyomkövetési funkciókat, valamint az egyes tevékenységek bemeneti és kimeneti adatainak naplózását. 

A tevékenységszintű nyomkövetés jelenleg a részletes stream használatával ír rekordokat. Ezért a nyomkövetés engedélyezésekor engedélyeznie kell a részletes naplózást. A nyomkövetést engedélyező grafikus runbookok esetén nincs szükség folyamatrekordok naplózásra. Az alapszintű nyomkövetés ugyanazt a célt szolgálja, és informatívabb.

![Grafikus szerzői feladatstreamek nézet](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

A képen látható, hogy a grafikus runbookok részletes naplózásának és nyomkövetésének engedélyezése sokkal több információt tesz elérhetővé az éles **feladatstreamek nézetben.** Ezek a további információk elengedhetetlenek a runbookokkal kapcsolatos éles problémák elhárításához. 

Ha azonban nincs szüksége erre az információra a runbookok előrehaladásának nyomon követéséhez a hibaelhárításhoz, érdemes lehet általános gyakorlatként kikapcsolva tartani a nyomkövetést. A nyomkövetési rekordok különösen sokak is lehet. A grafikus runbook-nyomkövetéssel tevékenységenként két-négy rekordot kaphat az Alapszintű vagy a Részletes nyomkövetés konfigurációja alapján.

**Tevékenységszintű nyomkövetés engedélyezése:**

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **Runbookok listájának** megnyitásához válassza a **Folyamatok** automatizálása alatt található Runbookok lehetőséget.
3. A Runbookok lapon válasszon egy grafikus runbookot a runbookok listájából.
4. A **Beállítások alatt** kattintson a Naplózás és nyomkövetés **elemre.**
5. A Naplózás és nyomkövetés lap Részletes rekordok naplózása lapján **kattintson** a **Be** gombra a részletes naplózás engedélyezéséhez.
6. A **Tevékenységszintű nyomkövetés alatt módosítsa** a nyomkövetési szintet **Alapszintűre** vagy Részletesre a nyomkövetési szint alapján. <br>

   ![Grafikus szerzői naplózási és nyomkövetési oldal](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Runbook kimenetének és üzenetének lekérése Microsoft Azure Naplók figyelése parancsban

Azure Automation runbook-feladat állapotát és feladatstreameket küldhet a Log Analytics-munkaterületre. Azure Monitor támogatja azokat a naplókat, amelyek a következőt teszik lehetővé:

* Betekintést nyerhet az Automation-feladatokba.
* Indítson el egy e-mailt vagy riasztást a runbook-feladat állapota (például Sikertelen vagy Felfüggesztett) alapján.
* Speciális lekérdezések írása feladatstreamek között.
* Feladatok korrelálása Automation-fiókok között.
* Feladatelőzmények vizualizációja.

A feladatadatok gyűjtéséhez, korrelálásához és a feladatokra vonatkozó intézkedésekhez az Azure Monitor-naplók integrációjának konfigurálásával kapcsolatos további információkért lásd: Feladatállapot és feladatstreamek továbbítása az Automationből a Azure Monitor [Logsba.](automation-manage-send-joblogs-log-analytics.md)

## <a name="next-steps"></a>Következő lépések

* Runbookok kezeléséhez lásd: [Runbookok kezelése a Azure Automation.](manage-runbooks.md)
* Ha nem ismeri a PowerShell-szkriptek futtatását, tekintse meg a [PowerShell dokumentációját.](/powershell/scripting/overview)
* Az Azure Automation PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation).
