---
title: Runbook-kimenet és üzenet-adatfolyamok konfigurálása
description: Ez a cikk azt ismerteti, hogyan valósítható meg a hibakezelés logikája, és hogyan írja le Azure Automation runbookok a kimenet és az üzenetek streamjét.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.openlocfilehash: bb14d5227204a69f8a2ef9e0bf2da05bd7bde51c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169096"
---
# <a name="configure-runbook-output-and-message-streams"></a>Runbook-kimenet és üzenet-adatfolyamok konfigurálása

A legtöbb Azure Automation runbookok valamilyen típusú kimenettel rendelkezik. Ez a kimenet hibaüzenetet kaphat a felhasználónak vagy egy másik runbook használni kívánt összetett objektumnak. A Windows PowerShell [több streamet](/powershell/module/microsoft.powershell.core/about/about_redirection) biztosít a kimenetek parancsfájlokból vagy munkafolyamatokból való küldéséhez. A Azure Automation az egyes streamek esetében különbözőképpen működik. Ha runbook hoz létre, kövesse az ajánlott eljárásokat a streamek használatához.

Az alábbi táblázat röviden leírja, hogy az egyes streamek milyen viselkedéssel rendelkeznek Azure Portal a közzétett runbookok és a [runbook tesztelése](./manage-runbooks.md)során. A kimeneti adatfolyam a runbookok közötti kommunikáció fő adatfolyama. A többi stream üzenet-adatfolyamként van besorolva, amely a felhasználó felé irányuló információk közlésére szolgál.

| Stream | Leírás | Közzétett | Tesztelés |
|:--- |:--- |:--- |:--- |
| Hiba |A felhasználónak szóló hibaüzenet. A kivételtől eltérően a runbook alapértelmezés szerint egy hibaüzenet után is folytatódik. |A feladatok előzményeibe írva |Megjelenítés a test output (kimenet) panelen |
| Hibakeresés |Interaktív felhasználó számára készült üzenetek. Nem használható a runbookok. |Nem írt a feladatok előzményeire |Nem jelenik meg a test output (kimenet) ablaktáblán |
| Kimenet |Másik runbookok számára készült objektum. |A feladatok előzményeibe írva |Megjelenítés a test output (kimenet) panelen |
| Előrehaladás |A runbook összes tevékenysége előtt és után automatikusan létrejövő rekordok. A runbook nem próbálja meg saját folyamatokat létrehozni, mivel azok interaktív felhasználók számára készültek. |Csak akkor íródik a feladatok előzményeibe, ha a runbook be van kapcsolva. |Nem jelenik meg a test output (kimenet) ablaktáblán |
| Részletes |Általános vagy hibakeresési adatokat tartalmazó üzenetek. |Csak akkor íródott a feladatok előzményeire, ha a részletes naplózás be van kapcsolva a runbook |Csak akkor jelenik meg a test output (tesztelés) ablaktáblán, ha a `VerbosePreference` változó a folytatás runbook értékre van állítva. |
| Figyelmeztetés |A felhasználónak szóló figyelmeztető üzenet. |A feladatok előzményeibe írva |Megjelenítés a test output (kimenet) panelen |

## <a name="use-the-output-stream"></a>A kimeneti adatfolyam használata

A kimeneti adatfolyam a parancsfájlok vagy a munkafolyamat megfelelő futtatásakor létrehozott objektumok kimenetéhez használatos. Azure Automation elsődlegesen ezt az adatfolyamot használja azon objektumok számára, amelyeket az [aktuális runbook](automation-child-runbooks.md)meghívó fölérendelt runbookok használ. Amikor egy szülő [meghívja a runbook](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution), a gyermek visszaadja az adatokat a kimeneti adatfolyamból a szülőnek.

A runbook a kimeneti adatfolyam használatával közli az általános információkat az ügyféllel, ha azt más runbook nem hívja meg. Az ajánlott eljárás szerint azonban a [részletes streamet](#write-output-to-verbose-stream) általában a runbookok kell használnia az általános információk a felhasználóhoz való továbbításához.

Írja be a runbook az adatokat a kimeneti adatfolyamba [írási kimenet](/powershell/module/microsoft.powershell.utility/write-output)használatával. Azt is megteheti, hogy az objektumot a parancsfájl saját sorába helyezi.

```powershell
#The following lines both write an object to the output stream.
Write-Output -InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Függvény kimenetének kezelése

Amikor egy runbook függvény a kimeneti adatfolyamba ír, a kimenet vissza lesz adva a runbook. Ha a runbook a kimenetet egy változóhoz rendeli, a kimenet nem íródik a kimeneti adatfolyamba. A függvényen belüli bármely más streambe való írás a runbook tartozó megfelelő streambe ír. Vegye figyelembe a következő PowerShell-munkafolyamat-runbook.

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

A runbook-feladatokhoz tartozó kimeneti adatfolyam a következő:

```output
Output inside of function
Output outside of function
```

A runbook feladatsor részletes streamje a következő:

```output
Verbose outside of function
Verbose inside of function
```

Miután közzétette a runbook, és megkezdte a megkezdését, be kell kapcsolni a részletes naplózást a runbook-beállításokban a részletes adatfolyam kimenetének lekéréséhez.

### <a name="declare-output-data-type"></a>Kimeneti adattípus deklarálása

A következő példák a kimeneti adattípusokra mutatnak:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Kimenet adattípusának deklarálása egy munkafolyamatban

A munkafolyamat a kimenet adattípusát adja meg a [OutputType attribútum](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)használatával. Ennek az attribútumnak nincs hatása a futtatókörnyezetben, de a runbook várt kimenetének tervezési ideje szerint jelzi. Mivel a runbookok eszközkészlete továbbra is fejlődik, a kimeneti adattípusok bejelentésének fontossága a tervezési időszakban nő. Ezért ajánlott ezt a deklarációt minden Ön által létrehozott runbookok belefoglalni.

Az alábbi példában a runbook kimenete egy sztring típusú objektum, és megadja az elvárt kimenettípust. Amennyiben a runbook kimenete egy bizonyos típusú tömb, akkor is egy típust érdemes megadni, nem pedig a típusok egy tömbjét.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Kimeneti adattípus deklarálása grafikus runbook

Ha egy grafikus vagy grafikus PowerShell-munkafolyamat runbook szeretné deklarálni a kimeneti típust, válassza a **bemeneti és kimeneti** menü lehetőséget, és adja meg a kimeneti típust. Ajánlott a teljes .NET-osztály nevét használni, hogy a típus könnyen azonosítható legyen, ha egy szülő runbook hivatkozik rá. A teljes név használata az osztály összes tulajdonságát elérhetővé teszi a runbook lévő adatbuszba, és növeli a rugalmasságot, ha a tulajdonságok a feltételes logika, a naplózás és a más runbook tevékenységek értékeiként használatosak.<br> ![Runbook bemeneti és kimeneti beállítása](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Miután megadott egy értéket a bemeneti és kimeneti Tulajdonságok ablaktáblán a **kimenet típusa** mezőben, ügyeljen rá, hogy a vezérlőn kívülre kattintson, hogy az felismerje a bejegyzést.

Az alábbi példa két grafikus runbookok mutat be a bemeneti és kimeneti funkció bemutatására. A moduláris runbook kialakítási modell alkalmazása esetén egy runbook kell lennie, mint az Azure-ban a futtató fiókkal történő hitelesítést kezelő Runbook-sablon hitelesítése. A második runbook, amely általában alapvető logikát végez egy adott forgatókönyv automatizálásához, ebben az esetben a hitelesítő Runbook sablont hajtja végre. Megjeleníti az eredményeket a test output (kimenet) ablaktáblán. Normális körülmények között ez a runbook a gyermek runbook kimenetét kihasználó erőforráson végezhető el.

Itt látható a AuthenticateTo alapszintű logikája **– Az Azure** runbook.<br> ![Példa a Runbook-sablon hitelesítésére ](media/automation-runbook-output-and-messages/runbook-authentication-template.png) .

A runbook tartalmazza a kimeneti típust `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` , amely a hitelesítési profil tulajdonságait adja vissza.<br> ![Példa a Runbook kimeneti típusára](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Habár ez a runbook egyszerű, itt egy konfigurációs elem is meghívja Önt. Az utolsó tevékenység végrehajtja a `Write-Output` parancsmagot, hogy a paraméterhez egy PowerShell-kifejezés használatával írjon be egy változót a profil adataihoz `Inputobject` . Ezt a paramétert kötelező megadni a következőhöz: `Write-Output` .

A példában a **test-ChildOutputType** nevű második runbook egyszerűen két tevékenységet határoz meg.<br> ![Példa gyermek kimeneti típus Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Az első tevékenység meghívja a **AuthenticateTo-Azure-** runbook. A második tevékenység a `Write-Verbose` parancsmagot a **tevékenység kimenete** értékre beállított **adatforrással** futtatja. Emellett a **mező elérési útja** a **Context. Subscription. SubscriptionName**, a **AuthenticateTo-Azure** runbook környezeti kimenete.<br> ![Write-verbose parancsmag paraméterének adatforrása](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Az eredményül kapott kimenet az előfizetés neve.<br> ![Test-ChildOutputType Runbook eredményei](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Üzenetküldési adatfolyamok használata

A kimeneti adatfolyamtól eltérően az üzenetek a felhasználó felé továbbítják az adatokat. Több üzenet-adatfolyam is létezik különböző típusú adatokhoz, és a Azure Automation az egyes streameket különbözőképpen kezeli.

### <a name="write-output-to-warning-and-error-streams"></a>Kimenet írása a figyelmeztetési és a hiba-adatfolyamok számára

A figyelmeztetési és hiba-adatfolyamok naplózása a runbook előforduló hibákat naplózza. A Azure Automation a runbook végrehajtásakor beírja ezeket a streameket a feladatok előzményeibe. Az Automation a runbook tesztelésekor a Azure Portal teszt kimenete ablaktábláján lévő adatfolyamokat is tartalmazza.

Alapértelmezés szerint a runbook figyelmeztetés vagy hiba után folytatja a végrehajtást. Megadhatja, hogy a runbook figyelmeztessen egy figyelmeztetésre vagy hibára, ha a runbook az üzenet létrehozása előtt beállít egy [preferencia változót](#work-with-preference-variables) . Ha például egy kivétel miatt a runbook felfüggeszti a hibát, állítsa a `ErrorActionPreference` változót leállításra.

Figyelmeztetés vagy hibaüzenet létrehozása a [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) vagy a [Write-Error](/powershell/module/microsoft.powershell.utility/write-error) parancsmag használatával. A tevékenységek a figyelmeztetési és a hiba-adatfolyamokra is írhatnak.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning -Message "This is a warning message."
Write-Error -Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Kimenet írása a hibakeresési adatfolyamba

Azure Automation a hibakeresési üzenet streamjét használja az interaktív felhasználók számára. Alapértelmezés szerint a Azure Automation nem rögzíti a hibakeresési adatfolyam-adatokat, csak a kimenet, a hiba és a figyelmeztetési adatokat rögzíti, valamint részletes adatokat is, ha a runbook úgy van konfigurálva, hogy rögzítse azt.

A hibakeresési adatfolyam-adatok rögzítéséhez két műveletet kell végrehajtania a runbookok:

1. Állítsa be a változót `$GLOBAL:DebugPreference="Continue"` , amely arra utasítja a PowerShellt, hogy folytassa a hibakeresési üzenet beolvasása során.  A **$Global:** rész a PowerShellt arra utasítja, hogy ezt a globális hatókörben adja meg, nem pedig az utasítás végrehajtásának időpontjában a parancsfájlhoz tartozó helyi hatókört.

1. Irányítsa át azt a hibakeresési adatfolyamot, amelyet a rendszer nem rögzít olyan adatfolyamra, amelyet a rendszer rögzít, például a *kimenetet*. Ezt úgy teheti meg, hogy a PowerShell-átirányítást a végrehajtandó utasítással állítja be. A PowerShell átirányításával kapcsolatos további információkért lásd: [az átirányítás ismertetése](/powershell/module/microsoft.powershell.core/about/about_redirection).

#### <a name="examples"></a>Példák

Ebben a példában a runbook a és a parancsmag használatával `Write-Output` konfigurálja `Write-Debug` két különböző stream kihelyezésének szándékával.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Ha a runbook a következőképpen kell végrehajtani, a runbook-feladatokhoz tartozó kimeneti ablaktábla a következő kimenetet fogja továbbítani:

```output
This is an output message.
```

Ebben a példában a runbook az előző példához hasonlóan van konfigurálva, kivéve az utasítást az `$GLOBAL:DebugPreference="Continue"` utasítás végén található hozzáadással `5>&1` `Write-Debug` .

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Ha a runbook végre lettek hajtva, a runbook-feladatokhoz tartozó kimeneti ablaktábla a következő kimenetet fogja továbbítani:

```output
This is an output message.
This is a debug message.
```

Ez azért fordul elő `$GLOBAL:DebugPreference="Continue"` , mert az utasítás azt jelzi, hogy a PowerShell a hibakeresési üzeneteket jeleníti meg, és az utasítás végéhez való hozzáadásával a `5>&1` `Write-Debug` PowerShell átirányítja az 5. streamet (debug) az 1. streambe (kimenet).

### <a name="write-output-to-verbose-stream"></a>Kimenet írása részletes adatfolyamba

A részletes üzenetküldési adatfolyam a runbook művelet általános információit támogatja. Mivel a hibakeresési adatfolyam nem érhető el egy runbook, a runbook részletes üzeneteket kell használnia a hibakeresési adatokhoz.

Alapértelmezés szerint a feladatok előzményei nem tárolnak részletes üzeneteket a közzétett runbookok, a teljesítménnyel kapcsolatos okokból. A részletes üzenetek tárolásához használja a Azure Portal **Konfigurálás** lapot a részletes **rekordok naplózása** beállítással a közzétett runbookok konfigurálásához a részletes üzenetek naplózásához. Ezt a beállítást csak akkor érdemes bekapcsolni, ha hibát keres a runbookban, vagy ha hibaelhárításra van szükség. A legtöbb esetben érdemes megtartani a részletes rekordok naplózásának alapértelmezett beállítását.

[Runbook tesztelésekor](./manage-runbooks.md)a részletes üzenetek akkor sem jelennek meg, ha a runbook a részletes rekordok naplózására van konfigurálva. A [runbook tesztelésekor](./manage-runbooks.md)a részletes üzenetek megjelenítéséhez be kell állítania a `VerbosePreference` változót a folytatáshoz. Ha ez a változó be van állítva, a részletes üzenetek a Azure Portal teszt kimenet paneljén jelennek meg.

A következő kód egy részletes üzenetet hoz létre a [Write-verbose](/powershell/module/microsoft.powershell.utility/write-verbose) parancsmag használatával.

```powershell
#The following line creates a verbose message.

Write-Verbose -Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Az állapotjelző rekordok kezelése

A Azure Portal configure ( **Konfigurálás** ) lapján konfigurálhatja a runbook naplózási folyamatát. Az alapértelmezett beállítás szerint a rendszer nem naplózza a rekordokat a teljesítmény maximalizálása érdekében. A legtöbb esetben érdemes megtartani az alapértelmezett beállítást. Ezt a beállítást csak akkor érdemes bekapcsolni, ha hibát keres a runbookban, vagy ha hibaelhárításra van szükség.

Ha engedélyezi a folyamat naplózását, a runbook az egyes tevékenységek futtatása előtt és után rekordokat ír a feladatok előzményeihez. A runbook tesztelése nem jeleníti meg az előrehaladási üzeneteket még akkor sem, ha a runbook naplózása folyamatban van.

>[!NOTE]
>A [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) parancsmag nem érvényes egy runbook, mert ez a parancsmag interaktív felhasználóval való használatra készült.

## <a name="work-with-preference-variables"></a>Preferencia-változók használata

A runbookok bizonyos Windows PowerShell-beállításokat állíthat [be a különböző](/powershell/module/microsoft.powershell.core/about/about_preference_variables) kimeneti adatfolyamoknak küldött adatválaszok szabályozására. A következő táblázat felsorolja a runbookok használható preferencia-változókat az alapértelmezett és érvényes értékekkel. Ha a Windows PowerShellben a Azure Automationon kívülre használja, további értékek érhetők el.

| Változó | Alapértelmezett érték | Érvényes értékek |
|:--- |:--- |:--- |
| `WarningPreference` |Folytatás |Leállítás<br>Folytatás<br>FolytatásCsendben |
| `ErrorActionPreference` |Folytatás |Leállítás<br>Folytatás<br>FolytatásCsendben |
| `VerbosePreference` |FolytatásCsendben |Leállítás<br>Folytatás<br>FolytatásCsendben |

A következő táblázat felsorolja a runbookok-ben érvényes preferencia-változók értékeinek viselkedését.

| Érték | Működés |
|:--- |:--- |
| Folytatás |Naplózza az üzenetet, és folytatja a runbook futtatását. |
| FolytatásCsendben |Az üzenet naplózása nélkül folytatja a runbook futtatását. Ennek az értéknek az a következménye, hogy figyelmen kívül hagyja az üzenetet. |
| Leállítás |Naplózza az üzenetet, és felfüggeszti a runbook futtatását. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Runbook-kimenet és üzenetek lekérése

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Runbook-kimenet és-üzenetek lekérése Azure Portal

Egy runbook-feladat részleteit a Azure Portal **feladatok** lapján tekintheti meg a runbook. A feladatok összegzése a bemeneti paramétereket és a [kimeneti adatfolyamot](#use-the-output-stream)jeleníti meg, valamint a feladattal kapcsolatos általános információkat és a bekövetkezett kivételeket. A feladatok előzményei a kimeneti adatfolyamból, valamint a [Figyelmeztetési és a hiba-adatfolyamokból](#write-output-to-warning-and-error-streams)származó üzeneteket tartalmaznak. Emellett a [részletes adatfolyam](#write-output-to-verbose-stream) -és [folyamatjelző rekordokból](#handle-progress-records) származó üzeneteket is tartalmaz, ha a runbook a részletes és a folyamatjelző rekordok naplózására van konfigurálva.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Runbook-kimenet és-üzenetek lekérése a Windows PowerShellben

A Windows PowerShellben a runbook kimenetét és üzeneteit a [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) parancsmag használatával kérheti le. Ehhez a parancsmaghoz szükség van a feladatokhoz tartozó AZONOSÍTÓra, és egy paramétert kell `Stream` megadnia, amelyben meg kell adnia a beolvasandó adatfolyamot. A paraméter értékének megadásával lekérheti a feladatokhoz tartozó összes adatfolyamot.

A következő példában elindul a runbook, majd a rendszer megvárja, amíg annak végrehajtása befejeződik. Miután a runbook befejezte a végrehajtást, a parancsfájl gyűjti a runbook kimeneti adatfolyamot a feladatokból.

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Runbook-kimenet és-üzenetek lekérése grafikus runbookok

Grafikus runbookok esetében a kimenet és az üzenetek további naplózása tevékenység szintű nyomkövetés formájában érhető el. A nyomkövetésnek két szintje van: alapszintű és részletes. Az alapszintű nyomkövetés megjeleníti a runbook minden tevékenységének kezdési és befejezési időpontját, valamint a tevékenység újrapróbálkozásával kapcsolatos információkat. Ilyenek például a kísérletek száma és a tevékenység kezdési időpontja. A részletes nyomkövetés magában foglalja az alapszintű nyomkövetési funkciókat, valamint a bemeneti és kimeneti adatok naplózását az egyes tevékenységekhez. 

A tevékenység szintű nyomkövetés jelenleg a részletes adatfolyam használatával ír rekordokat. Ezért a nyomkövetés engedélyezésekor engedélyeznie kell a részletes naplózást. A nyomkövetést engedélyező grafikus runbookok esetében nem kell naplózni a folyamatjelző rekordokat. Az alapszintű nyomkövetés ugyanazt a célt szolgálja, és több információt nyújt.

![Grafikus szerzői feladatok adatfolyam-nézete](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

A grafikus runbookok részletes naplózást és nyomkövetést engedélyező rendszerképből sokkal több információ érhető el az üzemi **feladatok adatfolyamok** nézetében. Ez a további információ elengedhetetlen lehet a runbook kapcsolatos éles problémák elhárításához. 

Ha azonban nem kívánja, hogy ezek az információk nyomon kövessék a runbook állapotát a hibaelhárításhoz, érdemes lehet a nyomkövetést általános gyakorlatként megtartani. A nyomkövetési rekordok különösen sokek lehetnek. A grafikus runbook nyomon követésével az alapszintű vagy részletes nyomkövetési konfigurációtól függően akár két-négy rekordot is megadhat tevékenységekhez.

**A tevékenység szintű nyomkövetés engedélyezése:**

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
3. A Runbookok lapon válasszon egy grafikus runbook a runbookok listájából.
4. A **Beállítások** területen kattintson a **naplózás és nyomkövetés** elemre.
5. A naplózás és nyomkövetés lapon a **részletes** naplózás engedélyezéséhez kattintson **a bekapcsolás** elemre.
6. A **tevékenység szintű nyomkövetés** területen módosítsa a nyomkövetési szintet **alapszintű vagy** **részletesre** a szükséges nyomkövetési szint alapján.<br>

   ![Grafikus szerzői műveletek naplózása és nyomkövetése lap](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Runbook-kimenet és-üzenetek lekérése Microsoft Azure figyelő naplóiban

A Azure Automation a runbook feladatok állapotát és a feladatok streamjét a Log Analytics munkaterületre küldheti. Azure Monitor támogatja a következő naplók használatát:

* Betekintést nyerhet az Automation-feladatokba.
* A runbook-feladatok állapota alapján indítson el egy e-mailt vagy riasztást, például sikertelen vagy felfüggesztett.
* Speciális lekérdezések írása a feladatok adatfolyamai között.
* A feladatok összekapcsolása az Automation-fiókok között.
* A feladatok előzményeinek megjelenítése.

További információ a Azure Monitor naplókkal való integráció konfigurálásáról a feladatok adatainak gyűjtéséhez, összekapcsolásához és kezeléséhez: a [feladatok állapotának és a feladatok adatfolyamának továbbítása az automatizálásból a Azure monitor naplókba](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Következő lépések

* A runbookok használatával kapcsolatban lásd: [Runbookok kezelése a Azure Automationban](manage-runbooks.md).
* Ha nem ismeri a PowerShell-parancsfájlokat, tekintse meg a [PowerShell](/powershell/scripting/overview) dokumentációját.
* Az Azure Automation PowerShell-parancsmagok leírása: az [. Automation](/powershell/module/az.automation).
