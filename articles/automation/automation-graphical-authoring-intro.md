---
title: Grafikus runbookok írása a Azure Automation
description: Ez a cikk azt mutatja be, hogyan lehet grafikus runbookot programkód nélkül szerzőivéni.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 88492d914b710c7a738dd6d7f501e22d490065b6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833813"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Grafikus runbookok írása a Azure Automation

A forgatókönyvben Azure Automation runbookok Windows PowerShell munkafolyamatok. A grafikus runbookok és a grafikus PowerShell-munkafolyamat-runbookok olyan PowerShell-kódot hoznak létre, amely az Automation-dolgozókat futtatja, de nem jelennek meg és nem módosíthatók. A grafikus runbookokat grafikus PowerShell-munkafolyamat-runbookokká konvertálhatja, és fordítva. Ezek a runbookok azonban nem konvertálhatók szöveges runbookokká. Emellett az Automation grafikus szerkesztője nem tud szöveges runbookot importálni.

A grafikus létrehozással runbookokat hozhat létre a Azure Automation a mögöttes Windows PowerShell Vagy a PowerShell-munkafolyamat kód összetettsége nélkül. Tevékenységeket adhat a vászonhoz a parancsmagok és runbookok könyvtárából, összekapcsolhatja őket, és konfigurálhatja őket egy munkafolyamat formában. Ha dolgozott már az Orchestrator System Center vagy Service Management Automation (SMA) használatával, a grafikus tartalom írásának ismerősnek kell lennie. Ez a cikk bevezetést nyújt a grafikus runbookok létrehozásának első lépéseibe.

## <a name="overview-of-graphical-editor"></a>A grafikus szerkesztő áttekintése

A grafikus szerkesztőt a grafikus Azure Portal grafikus runbook létrehozásával vagy szerkesztésével nyithatja meg.

![Grafikus munkaterület](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

A következő szakaszok a grafikus szerkesztő vezérlőit ismertetik.

### <a name="canvas-control"></a>Vászon vezérlőelem

A Vászon vezérlőelem lehetővé teszi a runbook tervezését. Tevékenységeket adhat a Runbookhoz a Könyvtár vezérlő csomópontjaiból, és összekapcsolhatja őket a runbook logikáját meghatározó hivatkozásokkal. A vászon alján vannak olyan vezérlők, amelyek lehetővé teszik a nagyítást és kicsinyítést.

### <a name="library-control"></a>Könyvtárvezérlő

A Könyvtár vezérlővel [kiválaszthatja](#use-activities) a runbookhoz hozzáadni kívánt tevékenységeket. Ezeket hozzáadhatja a vászonhoz, ahol más tevékenységekhez is csatlakoztathatja őket. A Könyvtár vezérlő az alábbi táblázatban meghatározott szakaszokat tartalmazza.

| Section | Description |
|:--- |:--- |
| Parancsmagok |A runbookban használható összes parancsmag. A parancsmagok modul szerint vannak rendszerezve. Az Automation-fiókba telepített összes modul elérhető. |
| Runbookok |Az Automation-fiókban a runbookok. Ezeket a runbookokat hozzáadhatja a vászonhoz, hogy gyermek runbookokként használja őket. Csak azok a runbookok jelennek meg, amelyek ugyanolyan alapvető típusúak, mint a szerkesztett runbook. Grafikus runbookok esetén csak a PowerShell-alapú runbookok jelennek meg. Grafikus PowerShell-alapú munkafolyamat-runbookok esetén csak a PowerShell munkafolyamat-alapú runbookok jelennek meg. |
| Objektumok |Az [Automation-fiók](/previous-versions/azure/dn939988(v=azure.100)) automation-eszközei, amelyek a runbookban használhatók. Ha hozzáad egy eszközt egy runbookhoz, hozzáad egy munkafolyamat-tevékenységet, amely lekérte a kiválasztott eszközt. Változóeszközök esetén kiválaszthatja, hogy szeretne-e tevékenységet hozzáadni a változó lekért értékéhez, vagy be kell állítania a változót. |
| Runbook-vezérlés |Az aktuális runbookban használható tevékenységek szabályozása. A csatlakozási tevékenység több bemenetet vesz fel, és megvárja, amíg az összes befejeződik, mielőtt folytatná a munkafolyamatot. A kódtevékenység a grafikus runbook típusától függően egy vagy több Sor PowerShell- vagy PowerShell-munkafolyamatkódot futtat. Ezt a tevékenységet használhatja egyéni kódokhoz vagy olyan funkciókhoz, amelyek más tevékenységekkel nehezen elérhetőek. |

### <a name="configuration-control"></a>Konfigurációvezérlés

A Konfiguráció vezérlő lehetővé teszi a vásznon kiválasztott objektum részleteinek a beállítását. A vezérlőben elérhető tulajdonságok a kiválasztott objektum típusától függnek. Ha kiválaszt egy lehetőséget a Konfiguráció vezérlőben, az további paneleket nyit meg, amelyek további információkat biztosítanak.

### <a name="test-control"></a>Tesztvezérlő

A Grafikus szerkesztő első eleinte nem jelenik meg a Teszt vezérlővel. Akkor nyílik meg, amikor interaktívan tesztel egy grafikus runbookot.

## <a name="use-activities"></a>Tevékenységek használata

A tevékenységek a runbookok építőelemei. A tevékenység lehet Egy PowerShell-parancsmag, egy gyermek runbook vagy egy munkafolyamat. Tevékenység hozzáadásához kattintson rá a jobb gombbal a Könyvtár vezérlőben, majd válassza a **Hozzáadás a vászonhoz gombra.** Ezután a tevékenységre kattintva és húzással bárhová húzhatja a vásznon. A tevékenység helye a vásznon nincs hatással a runbook működésére. A runbookot bármilyen módon kitervheti, hogy a legmegfelelőbben ábrázolja a műveletét.

![Hozzáadás a vászonhoz](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Válasszon ki egy tevékenységet a vásznon a tulajdonságainak és paramétereinek konfigurálásához a Konfiguráció panelen. Módosíthatja a tevékenység címkéjét egy leírónak talált névre. A runbook továbbra is futtatja az eredeti parancsmagot. Egyszerűen módosítja a grafikus szerkesztő által használt megjelenített nevet. Vegye figyelembe, hogy a címkének egyedinek kell lennie a runbookon belül.

### <a name="parameter-sets"></a>Paraméterkészletek

A paraméterkészlet határozza meg azokat a kötelező és választható paramétereket, amelyek egy adott parancsmag értékeit fogadják el. Minden parancsmagnak van legalább egy paraméterkészlete, néhánynak pedig több készlete. Ha egy parancsmag több paraméterkészlettel rendelkezik, a paraméterek konfigurálása előtt ki kell választania a használni kívántat. A tevékenység által használt paraméterkészletet  a Paraméterkészlet kiválasztásával és egy másik készlet kiválasztásával módosíthatja. Ebben az esetben a már konfigurált paraméterértékek elvesznek.

A következő példában a [Get-AzVM](/powershell/module/az.compute/get-azvm) parancsmag három paraméterkészletet mutat be. A példa egy **ListVirtualMachineInResourceGroupParamSet** nevű készletet használ egyetlen választható paraméterrel, amely egy erőforráscsoport összes virtuális gépét visszaadja. A példa a **GetVirtualMachineInResourceGroupParamSet** paraméterkészletet is használja a visszaadni kívánt virtuális gép megadásához. Ez a készlet két kötelező paraméterrel és egy választható paraméterrel rendelkezik.

![Paraméterkészlet](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Paraméterértékek

Amikor megad egy értéket egy paraméterhez, ki kell választania egy adatforrást az érték megadásának a meghatározásához. Az egy adott paraméterhez elérhető adatforrások az adott paraméter érvényes értékeitől függnek. A Null például nem érhető el olyan paraméter esetén, amely nem engedélyezi a null értékeket.

| Adatforrás | Description |
|:--- |:--- |
| Állandó érték |Írjon be egy értéket a paraméterhez. Ez az adatforrás csak a következő adattípusok esetében érhető el: Int32, Int64, String, Boolean, DateTime, Switch. |
| Tevékenység kimenete |Használja egy olyan tevékenység kimenetét, amely megelőzi a munkafolyamat aktuális tevékenységét. Az összes érvényes tevékenység fel van sorolva. A paraméter értékeként csak azt a tevékenységet használja, amely a kimenetet állítja elő. Ha a tevékenység több tulajdonsággal rendelkező objektumot ad ki, a tevékenység kiválasztása után begépelheti egy adott tulajdonság nevét. |
| Runbook bemenete |Válasszon ki egy runbookbemenetet a tevékenységparaméter bemeneteként. |
| Változóeszköz |Válasszon ki egy Automation-változót bemenetként. |
| Hitelesítő adateszköz |Bemenetként válasszon egy Automation-hitelesítő adatokat. |
| Tanúsítványeszköz |Válasszon ki egy Automation-tanúsítványt bemenetként. |
| Kapcsolati eszköz |Válasszon egy Automation-kapcsolatot bemenetként. |
| PowerShell-kifejezés |Adjon meg egy [egyszerű PowerShell-kifejezést.](#work-with-powershell-expressions) A kifejezést a rendszer a tevékenység előtt értékeli ki, és az eredményt használja a paraméter értékeként. Változók használatával hivatkozhat egy tevékenység vagy egy Runbook bemeneti paraméterének kimenetére. |
| Nincs konfigurálva |Törölje a korábban konfigurált értékeket. |

#### <a name="optional-additional-parameters"></a>Választható további paraméterek

Minden parancsmaghoz meg lehet adni további paramétereket. Ezek a PowerShell gyakori paraméterei vagy más egyéni paraméterek. A grafikus szerkesztő egy szövegmezőt tartalmaz, amelyben a PowerShell-szintaxissal adatokat lehet adni a paramétereknek. Ha például a közös paramétert használja, a `Verbose` értéket kell `-Verbose:$True` megadnia.

### <a name="retry-activity"></a>Újrapróbálkozási tevékenység

Egy tevékenység újrapróbálkozási funkciója lehetővé teszi, hogy többször is fusson, amíg egy adott feltétel nem teljesül, egy hurokhoz hasonlóan. Ezt a funkciót olyan tevékenységekhez használhatja, amelyek többször is futnak, nagy a hiba, több sikeres kísérletre lehet szükség, vagy a tevékenység kimeneti információit érvényes adatokra kell tesztelni.

Ha engedélyezi egy tevékenység újrapróbálkozását, késleltetést és feltételt állíthat be. A késleltetés az az idő (másodpercben vagy percben mérve), amíg a runbook várakozik, mielőtt újra futtatja a tevékenységet. Ha nem ad meg késleltetést, a tevékenység közvetlenül a befejeződése után ismét lefut.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Képernyőkép az Újrapróbálkozási szolgáltatás beállításainak engedélyezéséről.":::

Az újrapróbálkozási feltétel egy PowerShell-kifejezés, amely a tevékenység minden futtatása után ki lesz értékelve. Ha a kifejezés True (Igaz) lesz, a tevékenység újra fut. Ha a kifejezés False (Hamis) értéket ad vissza, a tevékenység nem fut újra, és a runbook a következő tevékenységre kerül át.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Képernyőkép az Újrapróbálkozás a feltétel teljesülésáig mezőről és az újrapróbálkozási feltételben használható PowerShell-kifejezések példáiról.":::

Az újrapróbálkozási feltétel használhat egy nevű változót, amely hozzáférést biztosít a `RetryData` tevékenység-újrapróbálkozási adatokhoz. Ez a változó a következő táblázatban található tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| `NumberOfAttempts` |A tevékenység futtatásának száma. |
| `Output` |A tevékenység utolsó futtatásának kimenete. |
| `TotalDuration` |A tevékenység első elkezdődött óta eltelt idő. |
| `StartedAt` |A tevékenység első elkezdődött időszaka (UTC formátumban). |

Az alábbiakban példákat talál a tevékenységek újrapróbálkozási feltételeire.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Miután konfigurált egy újrapróbálkozási feltételt egy tevékenységhez, a tevékenység két vizuális jellel emlékezteti Önt. Az egyik a tevékenységben, a másik pedig a tevékenység konfigurációjának áttekintésekor jelenik meg.

![Tevékenység-újrapróbálkozási vizuális mutatók](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Munkafolyamat-szkript vezérlő

A munkafolyamat-szkript vezérlő egy speciális tevékenység, amely a szerzői grafikus runbook típusától függően elfogadja a PowerShellt vagy a PowerShell-munkafolyamat parancsfájlját. Ez a vezérlő olyan funkciókat biztosít, amelyek más módon nem érhetők el. Nem tud paramétereket elfogadni, de használhat változókat a tevékenységek kimenetéhez és a runbook bemeneti paramétereihez. A tevékenység kimenete hozzá lesz adva az adatbuszhoz. Ez alól kivételt képez a kimenő kapcsolat nélkül való kimenet, amely esetben a kimenet hozzá lesz adva a runbook kimenethez.

Az alábbi kód például dátumszámításokat végez egy nevű runbook bemeneti `NumberOfDays` változóval. Ezután elküld egy számított DateTime értéket kimenetként, amelyet a runbook további tevékenységei használni fognak.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Hivatkozások használata munkafolyamathoz

A grafikus runbookban található hivatkozás két tevékenységet kapcsol össze. A vásznon egy nyíl jelenik meg, amely a forrástevékenységtől a céltevékenységig mutat. A tevékenységek a nyíl irányában futnak, a céltevékenység pedig a forrástevékenység befejeződése után kezdődik.

### <a name="create-a-link"></a>Hivatkozás létrehozása

Ha kapcsolatot hoz létre két tevékenység között, válassza ki a forrástevékenységet, majd kattintson az alakzat alján található körre. Húzza a nyilat a céltevékenységre és -kiadásra.

![Hivatkozás létrehozása](media/automation-graphical-authoring-intro/create-link-options.png)

Kattintson a hivatkozásra a tulajdonságainak konfigurálásához a Konfiguráció panelen. A tulajdonságok között szerepel a hivatkozás típusa, amelyet az alábbi táblázat ismertet.

| Hivatkozás típusa | Description |
|:--- |:--- |
| Folyamat |A céltevékenység egyszer fut le a forrástevékenység minden objektumkimenetekor. A céltevékenység nem fut, ha a forrástevékenység nem ad kimenetet. A forrástevékenység kimenete objektumként érhető el. |
| Sequence |A céltevékenység csak egyszer fut le, amikor megkapja a forrástevékenység kimenetét. A forrástevékenység kimenete objektumtömbként érhető el. |

### <a name="start-runbook-activity"></a>Runbook-tevékenység kezdése

A grafikus runbookok minden olyan tevékenységből indulnak, amelyekhez nincs bejövő hivatkozás. Gyakran csak egy tevékenység működik a runbook kezdő tevékenységeként. Ha több tevékenységhez nincs bejövő hivatkozás, a runbook párhuzamosan futtatja őket. Az egyes tevékenységek befejeződő futtatása során a hivatkozásokat követi.

### <a name="specify-link-conditions"></a>Hivatkozási feltételek megadása

Amikor feltételt ad meg egy hivatkozáshoz, a céltevékenység csak akkor fut le, ha a feltétel igazra oldódik fel. Általában egy `ActivityOutput` változót használ egy feltételben a forrástevékenység kimenetének lekéréséhez.

Folyamathivatkozás esetén meg kell adnia egy feltételt egyetlen objektumhoz. A runbook kiértékeli a forrástevékenység által az egyes objektumkimenetek feltételét. Ezután futtatja a céltevékenységet minden olyan objektumon, amely megfelel a feltételnek. A forrástevékenysége esetén például a következő szintaxist használhatja a feltételes folyamathivatkozáshoz, hogy csak a Group1 nevű erőforráscsoport virtuális gépeit olvassa `Get-AzVM` be.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Sorozathivatkozások esetén a runbook csak egyszer értékeli ki a feltételt, mivel a rendszer egyetlen tömböt ad vissza, amely a forrástevékenység összes objektumát tartalmazza. Emiatt a runbook nem használhat szekvenciahivatkozást a szűréshez, mint egy folyamathivatkozással. A szekvenciára mutató hivatkozás egyszerűen meg tudja állapítani, hogy fut-e a következő tevékenység.

Vegyük például a következő tevékenységeket a Start VM-runbookban: 

![Feltételes hivatkozás sorozatokkal](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

A runbook három különböző sorrendi hivatkozást használ, amelyek ellenőrzik a bemeneti paraméterek értékeit, és meghatározzák a `VMName` `ResourceGroupName` megfelelő műveletet. A lehetséges műveletek egyetlen virtuális gépet indítnak el, elindítják az erőforráscsoportban található összes virtuális gépet, vagy elindítják az előfizetésben található összes virtuális gépet. A és a közötti szekvenszekvencia-kapcsolathoz `Connect to Azure` `Get single VM` a feltétellogika a következő:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Feltételes hivatkozás használata esetén a forrástevékenység és az ág más tevékenységei között elérhető adatokat a feltétel szűri. Ha egy tevékenység több hivatkozás forrása, akkor az egyes ágak tevékenységei számára elérhető adatok az adott ághoz csatlakozó hivatkozás feltételétől függnek.

Az alábbi runbookban például az összes virtuális `Start-AzVM` gép elindul. Két feltételes hivatkozással rendelkezik. Az első feltételes hivatkozás a kifejezéssel `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` szűri, hogy a tevékenység sikeresen `Start-AzVM` befejeződik-e. A második feltételes hivatkozás a kifejezést használja a szűréshez, ha a `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` `Start-AzVm` tevékenység nem indítja el a virtuális gépet.

![Példa feltételes hivatkozásra](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Minden olyan tevékenység, amely az első hivatkozást követi, és a tevékenység kimenetét használja, csak a futtatáskor elindított virtuális `Get-AzureVM` `Get-AzureVM` gépeket olvassa be. A második hivatkozást követő tevékenységek csak a futtatáskor leállított virtuális gépeket `Get-AzureVM` kapják meg. A harmadik hivatkozást követő bármely tevékenység lekért minden virtuális gépet, függetlenül azok futó állapotától.

### <a name="use-junctions"></a>Csomópontok használata

A csomópont egy speciális tevékenység, amely megvárja, amíg az összes bejövő ág befejeződik. Ez lehetővé teszi, hogy a runbook több tevékenységet futtason párhuzamosan, és a továbbköltözés előtt győződjön meg arról, hogy mindegyik befejeződött.

Bár a csomópontok korlátlan számú bejövő hivatkozást tartalmaznak, ezek közül csak az egyik lehet folyamat. A bejövő szekvenciára mutató hivatkozások száma nincs korlátozva. Létrehozhatja a kapcsolatot több bejövő folyamathivatkozással, és mentheti a runbookot, de a futtatása sikertelen lesz.

Az alábbi példa egy olyan runbook része, amely elindítja a virtuális gépek egy készletét, és egyidejűleg letölti az ezekre a gépekre alkalmazandó javításokat. A forgatókönyv folytatódása előtt egy-egy illesztés segítségével biztosítja, hogy mindkét folyamat befejeződik.

![Szinkronizációs pont](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Ciklusok

A ciklus akkor jön létre, amikor egy céltevékenység a forrástevékenységéhez vagy egy másik tevékenységhez kapcsolódik, amely végül a forráshoz kapcsolódik. A grafikus tartalom írása jelenleg nem támogatja a ciklusokat. Ha a runbook rendelkezik egy ciklussal, megfelelően ment, de a futtatásakor hibaüzenetet kap.

![Ciklikus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Adatok megosztása tevékenységek között

A tevékenységek által kimenő hivatkozással kimenetként küldött adatokat a rendszer a runbook adatbuszára írja. A runbook bármely tevékenysége adatokat használhat az adatbuszban a paraméterértékek feltöltéséhez vagy a szkriptkódba való be- és feltöltéséhez. Egy tevékenység hozzáférhet a munkafolyamat bármely korábbi tevékenységének kimenetéhez.

Az adatok adatbuszba való írásának módja a tevékenységre mutató hivatkozás típusától függ. Folyamathivatkozás esetén az adatok kimenete több objektumként lesz kihozva. Sorozathivatkozás esetén az adatok tömbként kimenete lesz. Ha csak egy érték van, a kimenete egyelemű tömbként lesz kiszűkve.

A runbook két módon férhet hozzá az adatbusz adataihoz: 
* Tevékenységkimeneti adatforrás használata.
* PowerShell-kifejezés adatforrásának használata.

Az első mechanizmus egy tevékenységkimeneti adatforrást használ egy másik tevékenység paraméterének feltöltéséhez. Ha a kimenet egy objektum, a runbook egyetlen tulajdonságot is megadhat.

![tevékenység kimenete](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

A második adatelérési mechanizmus egy Tevékenység kimenetét olvassa be egy PowerShell-kifejezés adatforrásában vagy egy munkafolyamat-szkripttevékenységben egy változóval az alábbi `ActivityOutput` szintaxis használatával. Ha a kimenet egy objektum, a runbook egyetlen tulajdonságot is megadhat.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Ellenőrzőpontok használata

Ellenőrzőpontokat [állíthat](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) be egy grafikus PowerShell-alapú munkafolyamat-runbookban az **Ellenőrzőpont-runbook** bármely tevékenységen való kiválasztásával. Ennek hatására a tevékenység futtatása után be kell állítani egy ellenőrzőpontot.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Az ellenőrzőpontok csak grafikus PowerShell-alapú munkafolyamat-runbookokban engedélyezettek, grafikus runbookokban nem. Ha a runbook Azure-parancsmagokat használ, akkor minden ellenőrzőpont-alapú tevékenységet követnie kell egy `Connect-AzAccount` tevékenységgel. A rendszer abban az esetben használja a kapcsolódási műveletet, ha a runbook fel van függesztve, és erről az ellenőrzőpontról kell újraindítani egy másik feldolgozón.

## <a name="handle-runbook-input"></a>Runbook bemenetének kezeléséhez

A runbook bemenete lehet egy felhasználótól, aki a runbookot a Azure Portal vagy egy másik runbookból, ha az aktuálisat gyermekként használják. Ha például egy runbook létrehoz egy virtuális gépet, előfordulhat, hogy a felhasználónak olyan adatokat kell adnia, mint a virtuális gép neve és más tulajdonságok minden egyes indításakor.

A runbook bemenetet fogad el egy vagy több bemeneti paraméter meghatározásával. A felhasználó a runbook minden indításakor értékeket ad meg ezekhez a paraméterekhez. Amikor a felhasználó elindítja a runbookot a Azure Portal, a rendszer felkéri a felhasználót, hogy adjon meg értékeket a runbook által támogatott minden bemeneti paraméterhez.

A runbook írásakor a forgatókönyv eszköztárának  Bemenet és kimenet gombjára kattintva férhet hozzá a bemeneti paramétereihez. Ez megnyitja a Bemenet és kimenet vezérlőt, ahol szerkesztheti a meglévő bemeneti paramétereket, vagy létrehozhat egy újat a **Bemenet hozzáadása gombra kattintva.**

![Bemenet hozzáadása](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Minden bemeneti paramétert a következő táblázatban található tulajdonságok határoznak meg:

| Tulajdonság | Leírás |
|:--- |:--- |
| Név | Kötelező. A paraméter neve. A névnek egyedinek kell lennie a runbookon belül. Betűvel kell kezdődnie, és csak betűket, számokat és aláhúzásjeleket tartalmazhat. A név nem tartalmazhat szóközt. |
| Description |Választható. A bemeneti paraméter céljának leírása. |
| Típus | Választható. A paraméterérték várt adattípusa. A Azure Portal megfelelő vezérlőt biztosít az adattípushoz az egyes paraméterekhez, amikor a rendszer kéri a bemenetet. Támogatott paramétertípusok: Sztring, Int32, Int64, Decimális, Logikai, DateTime és Objektum. Ha nincs kiválasztva adattípus, akkor az alapértelmezett érték a Sztring.|
| Kötelező | Választható. Beállítás, amely meghatározza, hogy meg kell-e adni egy értéket a paraméterhez. Ha a lehetőséget választja, meg kell adni `yes` egy értéket a runbook elindultkor. Ha a lehetőséget választja, nincs szükség értékre a runbook elindításkor, és egy `no` alapértelmezett érték is használható. A runbook nem indítható el, ha nem ad meg értéket minden olyan kötelező paraméterhez, amely nem rendelkezik alapértelmezett értékkel. |
| Alapértelmezett érték | Választható. A paraméterhez használt érték, ha a runbook elkezdődött, a paraméter nem ad át értéket. Az alapértelmezett érték beállításhoz válassza a `Custom` lehetőséget. Válassza `None` a lehetőséget, ha nem szeretne alapértelmezett értéket adni. |

## <a name="handle-runbook-output"></a>Runbook kimenetének leírója

A grafikus létrehozással mentheti az olyan tevékenységek által létrehozott adatokat, amelyek nem tartalmaznak kimenő hivatkozást a [runbook kimenetére.](./automation-runbook-output-and-messages.md) A rendszer menti a kimenetet a runbook feladatával, és elérhető a szülő runbook számára, amikor a runbookot gyermekként használja.

## <a name="work-with-powershell-expressions"></a>PowerShell-kifejezések használata

A grafikus tartalom írásának egyik előnye, hogy lehetővé teszi a Runbookok a PowerShell minimális ismerete mellett való felépítését. Jelenleg azonban szükség van egy kis PowerShell-parancsra [](#use-activities) bizonyos paraméterértékek beállításával és a hivatkozási feltételek [beállításával.](#use-links-for-workflow) Ez a szakasz röviden bevezetőt nyújt a PowerShell-kifejezésekbe. A PowerShell részletes adatai a Parancsprogramozás a következővel [Windows PowerShell.](/powershell/scripting/overview)

### <a name="use-a-powershell-expression-as-a-data-source"></a>PowerShell-kifejezés használata adatforrásként

Egy PowerShell-kifejezés adatforrásként való használatával feltöltheti [](#use-activities) egy tevékenységparaméter értékét a PowerShell-kód eredményeivel. A kifejezés lehet egyetlen kódsor, amely egy egyszerű függvényt vagy több, összetett logikát végző sort hajt végre. A nem változóhoz rendelt parancsok kimenete a paraméter értékére lesz kiadva.

A következő parancs például az aktuális dátumot írja ki.

```powershell-interactive
Get-Date
```

A következő kódrészlet egy sztringet hoz létre az aktuális dátumból, és hozzárendeli azt egy változóhoz. A kód elküldi a változó tartalmát a kimenetnek.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Az alábbi parancsok kiértékelik az aktuális dátumot, és egy sztringet adnak vissza, amely jelzi, hogy az aktuális nap egy hétvége vagy egy hét napja.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Tevékenységkimenet használata

A runbook egy korábbi tevékenységének kimenetét a következő `ActivityOutput` szintaxissal használhatja a változóval.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Például létrehozhat egy olyan tulajdonsággal kapcsolatos tevékenységet, amelyhez szükség van egy virtuális gép nevére. Ebben az esetben a runbook a következő kifejezést használhatja.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Ha a tulajdonsághoz nem csak név, hanem virtuálisgép-objektum szükséges, a runbook a teljes objektumot visszaadja az alábbi szintaxis használatával.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

A runbook egy tevékenység kimenetét egy összetettebb kifejezésben használhatja, például a következőben. Ez a kifejezés szöveget ír össze a virtuális gép nevével.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Értékek összehasonlítása

Összehasonlító [operátorokkal hasonlíthatja](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) össze az értékeket, vagy megállapíthatja, hogy egy érték megfelel-e egy adott mintának. Az összehasonlítás igaz vagy hamis értéket ad vissza.

Az alábbi feltétel például azt határozza meg, hogy egy nevű tevékenység virtuális gépe `Get-AzureVM` jelenleg le van-e állítva.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped"
```

Az alábbi feltétel azt határozza meg, hogy ugyanazon virtuális gép a leállítottakon kívül más állapotban van-e.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -ne "Stopped"
```

A runbookban több feltételt is össze lehet illesztni egy logikai [operátor (](/powershell/module/microsoft.powershell.core/about/about_logical_operators)például vagy `-and` ) `-or` használatával. Az alábbi feltétel például ellenőrzi, hogy az előző példában lévő virtuális gép Leállítva vagy Leállítás állapotban van-e.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopping")
```

### <a name="use-hashtables"></a>Kivonattáblák használata

[A kivonattáblák](/powershell/module/microsoft.powershell.core/about/about_hash_tables) olyan név-érték párok, amelyek hasznosak értékek egy halmazának visszaadásakor. Egy szótárnak is nevezzük a kivonattáblát. Bizonyos tevékenységek tulajdonságai egyszerű érték helyett kivonattáblát várnak.

Hozzon létre egy kivonattáblát a következő szintaxissal. Bármilyen számú bejegyzést tartalmazhat, de mindegyiket egy név és egy érték határozza meg.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

A következő kifejezés például létrehoz egy kivonattáblát, amely egy olyan tevékenységparaméter adatforrásaként lesz használva, amely értékek kivonattábláját várja egy internetes kereséshez.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Az alábbi példa egy nevű tevékenység kimenetét használja `Get Twitter Connection` a kivonattábla feltöltéséhez.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Hitelesítés Az Azure-erőforrásokban

Az Azure-Azure Automation kezelésére használt runbookok hitelesítést igényelnek az Azure-ban. A [(más néven](./automation-security-overview.md)szolgáltatásnév) run as account (szolgáltatásnév) az az alapértelmezett mechanizmus, amely segítségével az Automation-runbook Azure Resource Manager erőforrásokhoz fér hozzá az előfizetésben. Ezt a funkciót hozzáadhatja egy grafikus runbookhoz, ha hozzáadja a `AzureRunAsConnection` powerShell [Get-AutomationConnection](/system-center/sma/manage-global-assets) parancsmagot használó kapcsolati eszközt a vászonhoz. A [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot is hozzáadhatja. Ezt a forgatókönyvet az alábbi példa szemlélteti.

![Hitelesítési tevékenységek futtatása](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A (vagy ) tevékenység egy nevű állandó érték `Get Run As Connection` `Get-AutomationConnection` adatforrással van `AzureRunAsConnection` konfigurálva.

![Futtatás kapcsolatkonfigurációja](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A következő tevékenység hozzáadja a hitelesített, `Connect-AzAccount` runbookban való használatra használt fiókot.

![Connect-AzAccount paraméterkészlet](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>A PowerShell-runbookok és `Add-AzAccount` `Add-AzureRMAccount` a a `Connect-AzAccount` aliasai. Vegye figyelembe, hogy ezek az aliasok nem érhetők el a grafikus runbookok számára. A grafikus runbookok csak saját magát `Connect-AzAccount` használhatja.

Az **APPLICATIONID,** **CERTIFICATETHUMBPRINT** és **TENANTID** paramétermezőkben adja meg a mező elérési útjának tulajdonságának nevét, mivel a tevékenység több tulajdonsággal rendelkező objektumot ad ki. Ellenkező esetben a runbook végrehajtása sikertelen lesz a hitelesítés megkísérléskor. Legalább erre van szükség ahhoz, hogy a runbookot a futtatott fiókkal hitelesítse.

Egyes előfizetők létrehoznak egy Automation-fiókot egy [Azure AD](./shared-resources/credentials.md) felhasználói fiókkal a klasszikus Azure-beli üzembe helyezés kezeléséhez vagy Azure Resource Manager erőforrásokhoz. Az előfizetők visszamenőleges kompatibilitásának fenntartása érdekében a runbookban használt hitelesítési mechanizmus a hitelesítő adattitkos `Add-AzureAccount` parancsmag. [](./shared-resources/credentials.md) Az eszköz egy Active Directory felhasználót képvisel, aki hozzáféréssel rendelkezik az Azure-fiókhoz.

Ezt a funkciót úgy engedélyezheti a grafikus runbookhoz, hogy hozzáad egy hitelesítő adateszközt a vászonhoz, majd egy tevékenység, amely a hitelesítő adateszközt használja `Add-AzureAccount` a bemenetként. Tekintse meg a következő példát.

![Hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authentication-activities.png)

A runbooknak minden ellenőrzőpont kezdetekor és után hitelesítenie kell magát. Ezért minden tevékenység után `Add-AzureAccount` tevékenységet kell `Checkpoint-Workflow` használnia. Nem kell további hitelesítő tevékenységet használnia.

![Tevékenység kimenete](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Grafikus runbook exportálása

Csak a grafikus runbook közzétett verzióját exportálhatja. Ha a runbook még nincs közzétéve, az **Exportálás** gomb le van tiltva. Amikor az Exportálás **gombra kattint,** a runbook letölti a helyi számítógépre. A fájl neve megegyezik a **runbook .graphrunbook kiterjesztésű nevével.**

## <a name="import-a-graphical-runbook"></a>Grafikus runbook importálása

Grafikus vagy grafikus PowerShell-munkafolyamat runbookfájlját  az Importálás lehetőség kiválasztásával importálhatja egy runbook hozzáadásakor. Ha kiválasztja az importálni kívánt fájlt, megtarthatja ugyanazt a nevet, vagy újat is választhat. A **Runbook típusa mező** a runbook típusát jeleníti meg a fájl kiválasztása után. Ha nem megfelelő típust próbál kiválasztani, a grafikus szerkesztő egy üzenetet küld, amely tudatja vele, hogy lehetséges ütközések állnak elő, és szintaktikai hibák is előfordulhatnak az átalakítás során.

![Runbook importálása](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Grafikus runbook tesztelése

Az alkalmazás minden grafikus runbookja Azure Automation vázlatos és közzétett verzióval rendelkezik. Csak a Közzétett verziót futtathatja, a Piszkozat verzióját azonban csak szerkesztheti. A Közzétett verzióra nincsenek hatással a Piszkozat verzióban végrehajtott módosítások. Amikor a Piszkozat készen áll a használatra, közzéteheti, amely felülírja az aktuális közzétett verziót a piszkozat verziójával.

A runbook vázlatos verzióját tesztelheti a Azure Portal a Közzétett verzió változatlanul hagyása mellett. Másik lehetőségként tesztelhet egy új runbookot a közzététel előtt, így a verziócserék előtt ellenőrizheti, hogy a runbook megfelelően működik-e. A runbook tesztelése végrehajtja a Piszkozat verzióját, és gondoskodik arról, hogy az összes által végzett művelet befejeződik. Nem jön létre feladatelőzmény, de a Kimenet tesztelése panelen megjelenik a kimenet.

Nyissa meg a grafikus runbook Teszt vezérlőt úgy, hogy megnyitja a runbookot szerkesztésre, majd a **Teszt panelre kattint.** A Teszt vezérlő kéri a bemeneti paramétereket, és a Start gombra kattintva elindíthatja a **runbookot.**

## <a name="publish-a-graphical-runbook"></a>Grafikus runbook közzététele

Grafikus runbookot úgy tehet közzé, hogy megnyitja a forgatókönyvet szerkesztésre, majd a Közzététel **parancsra kattint.** A runbook lehetséges állapotai a következőek:

* Új – a runbook még nincs közzétéve. 
* Közzétett – a runbook közzé letttéve.
* Szerkesztésben – a runbookot a közzététel után szerkesztik, a Piszkozat és a Közzétett verziók pedig eltérőek.

![Runbook-állapotok](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Lehetősége van arra, hogy visszaállítsa egy runbook Közzétett verzióját. Ez a művelet eldobja a runbook legutóbbi közzététele óta végrehajtott módosításokat. Lecseréli a runbook Vázlat verzióját a Közzétett verzióra.

## <a name="next-steps"></a>Következő lépések

* A grafikus runbookok használatával való első lépésekhez [lásd: Oktatóanyag: Grafikus runbook létrehozása.](learn/automation-tutorial-runbook-graphical.md)
* A runbooktípusokról és azok előnyeiről és korlátairól további információt a [runbooktípusokról](automation-runbook-types.md)Azure Automation meg.
* Az Automation-run As-fiókkal való hitelesítéssel való hitelesítésről lásd a [következőt: .](automation-security-overview.md#run-as-account)
* A PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation/#automation).
