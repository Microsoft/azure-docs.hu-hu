---
title: PowerShell-Azure Functions helyi hibakeresése
description: Megtudhatja, hogyan lehet hibakeresést végezni a PowerShell-függvények helyi futtatásakor.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422960"
---
# <a name="debug-powershell-azure-functions-locally"></a>PowerShell-Azure Functions helyi hibakeresése

Azure Functions lehetővé teszi a függvények PowerShell-parancsfájlokként való fejlesztését.

A PowerShell-függvényeket helyileg is felhasználhatja, ahogy bármelyik PowerShell-szkriptet a következő szabványos fejlesztői eszközök használatával:

* [Visual Studio Code](https://code.visualstudio.com/): a Microsoft ingyenes, könnyű és nyílt forráskódú szövegszerkesztője, amely a PowerShell-bővítmény teljes körű fejlesztési élményt nyújt.
* Egy PowerShell-konzol: a hibakereséshez ugyanazokat a parancsokat használja, amelyeket bármilyen más PowerShell-folyamat hibakereséséhez használni kíván.

[Azure functions Core Tools](functions-run-local.md) támogatja a Azure functions helyi hibakeresését, beleértve a PowerShell-függvényeket is.

## <a name="example-function-app"></a>Példa a Function alkalmazásra

A cikkben használt Function alkalmazás egyetlen HTTP által aktivált függvényt tartalmaz, és a következő fájlokat használja:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Ez a Function-alkalmazás a [PowerShell](./create-first-function-vs-code-powershell.md)rövid útmutatójának elvégzéséhez hasonlóan fog megjelenni.

A függvény kódja a `run.ps1` következő parancsfájlhoz hasonlóan néz ki:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>A csatolási pont beállítása

A PowerShell-függvények hibakereséséhez a funkciónak le kell állnia a hibakereső csatolásához. A `Wait-Debugger` parancsmag leállítja a végrehajtást, és megvárja a hibakeresőt.

>[!NOTE]
>A PowerShell 7 használatakor nem kell hozzáadnia a `Wait-Debugger` hívást a kódban.

Mindössze annyit kell tennie, hogy az utasítás fölött egy hívást ad hozzá a `Wait-Debugger` parancsmaghoz `if` , az alábbiak szerint:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

A hibakeresés elindul az `if` utasításban. 

A `Wait-Debugger` helyett a Visual Studio Code vagy a PowerShell-konzol használatával végezheti el a függvények hibakeresését.

## <a name="debug-in-visual-studio-code"></a>Hibakeresés a Visual Studio Code-ban

A Visual Studio Code-ban a PowerShell-függvények hibakereséséhez a következőkre van szükség:

* [PowerShell-bővítmény a Visual Studio Code-hoz](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions-bővítmény a Visual Studio Code-hoz](./create-first-function-cli-powershell.md)
* [PowerShell Core 6,2 vagy újabb](/powershell/scripting/install/installing-powershell-core-on-windows)

A függőségek telepítése után töltsön be egy meglévő PowerShell functions-projektet, vagy [hozza létre az első PowerShell functions projektet](./create-first-function-vs-code-powershell.md).

>[!NOTE]
> Ha a projekt nem rendelkezik a szükséges konfigurációs fájlokkal, a rendszer kéri, hogy adja hozzá őket.

### <a name="set-the-powershell-version"></a>A PowerShell verziójának beállítása

A PowerShell Core a Windows PowerShell-lel párhuzamosan települ. Állítsa be a PowerShell Core-t PowerShell-verzióként a Visual Studio Code PowerShell-bővítménnyel való használatra.

1. Nyomja meg az F1 billentyűt a parancs raklapjának megjelenítéséhez, majd keresse meg a következőt: `Session` .

1. Válassza a **PowerShell: munkamenet menü megjelenítése** lehetőséget.

1. Ha az **aktuális munkamenet** nem a **PowerShell Core 6**, válassza **a váltás a következőre: PowerShell Core 6**.

Ha meg van nyitva egy PowerShell-fájl, az ablak jobb alsó sarkában látható zöld színnel jelenik meg. Ha ezt a szöveget választja, a munkamenet menüt is megjeleníti. További információ: a [PowerShell verziójának kiválasztása a bővítménnyel való használatra](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>A Function alkalmazás elindítása

Ellenőrizze, hogy `Wait-Debugger` be van-e állítva abban a függvényben, amelyhez csatolni szeretné a hibakeresőt.  A `Wait-Debugger` hozzáadást követően a Visual Studio Code használatával hibakeresést végezhet a Function alkalmazásban.

Válassza a **hibakeresés** ablaktáblát, majd **csatolja a PowerShell-függvényt**.

![hibakereső](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Az F5 billentyű lenyomásával is elindíthatja a hibakeresést.

A hibakeresés indítása művelet a következő feladatokat hajtja végre:

* `func extensions install`A terminálon futtatva telepítheti a Function alkalmazás által igényelt Azure functions-bővítményeket.
* A `func host start` terminálon futtatva elindítja a Function alkalmazást a functions gazdagépen.
* Csatolja a PowerShell-hibakeresőt a PowerShell-RunSpace a functions futtatókörnyezeten belül.

>[!NOTE]
> Győződjön meg arról, hogy a PSWorkerInProcConcurrencyUpperBound értéke 1, hogy a hibakeresési élmény helyes legyen a Visual Studio Code-ban. Ez az alapértelmezett beállítás.

Ha a Function alkalmazás fut, külön PowerShell-konzolra van szükség a HTTP-triggert használó függvény meghívásához.

Ebben az esetben a PowerShell-konzol az ügyfél. A a `Invoke-RestMethod` függvény elindítására szolgál.

A PowerShell-konzolon futtassa a következő parancsot:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Megfigyelheti, hogy a rendszer nem küld azonnal visszajelzést. Ennek oka, hogy `Wait-Debugger` csatolta a hibakeresőt, és a PowerShell-végrehajtás a lehető leghamarabb megszakította a módot. Ennek oka az [BreakAll koncepció](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), amelyet később ismertetünk. A gomb megnyomása után `continue` a hibakereső most rögtön a sorban megszakad `Wait-Debugger` .

Ezen a ponton a hibakereső csatlakoztatva van, és elvégezheti az összes normál hibakereső műveletet. A Debugger a Visual Studio Code-ban való használatával kapcsolatos további információkért tekintse meg [a hivatalos dokumentációt](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Miután folytatta és teljes mértékben meghívta a szkriptet, megfigyelheti, hogy:

* Az `Invoke-RestMethod` eredményt visszaadó PowerShell-konzol
* A Visual Studio Code-ban a PowerShell integrált konzolja arra vár, hogy végrehajtsa a parancsfájlt.

Később, amikor meghívja ugyanezt a függvényt, a PowerShell-bővítményben lévő hibakereső közvetlenül a után megszakítja a műveletet `Wait-Debugger` .

## <a name="debugging-in-a-powershell-console"></a>Hibakeresés egy PowerShell-konzolon

>[!NOTE]
> Ez a szakasz azt feltételezi, hogy elolvasta a [Azure functions Core Tools dokumentumait](functions-run-local.md) , és tudja, hogyan használhatja a `func host start` parancsot a Function alkalmazás elindításához.

Nyisson meg egy konzolt `cd` a Function alkalmazás könyvtárába, és futtassa a következő parancsot:

```sh
func host start
```

A és a rendszerű Function alkalmazáshoz `Wait-Debugger` csatolhatja a folyamatot. Ehhez két PowerShell-konzolra van szükség.

Az egyik konzol ügyfélként működik. Ettől kezdve meghívja `Invoke-RestMethod` a függvény aktiválását. Futtathatja például a következő parancsot:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Megfigyelheti, hogy nem ad vissza választ, ami a eredményét képezi `Wait-Debugger` . A PowerShell-RunSpace most már arra vár, hogy csatolja a hibakeresőt. Lássuk, hogy csatolva van.

A másik PowerShell-konzolon futtassa a következő parancsot:

```powershell
Get-PSHostProcessInfo
```

Ez a parancsmag egy táblázatot ad vissza, amely a következő kimenethez hasonlít:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Jegyezze fel a `ProcessId` (z) táblához tartozó tételt a következővel: `ProcessName` `dotnet` . Ez a folyamat a Function alkalmazás.

Ezután futtassa a következő kódrészletet:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Az indítás után a hibakereső megszakítja és megjeleníti a következő kimenethez hasonlót:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Ezen a ponton a [PowerShell-hibakereső](/powershell/module/microsoft.powershell.core/about/about_debuggers)töréspontján leállt. Innen elvégezheti az összes szokásos hibakeresési műveletet, a lépéseket, a beléptetést, a folytatást, a kilépést és egyebeket. A konzolon elérhető hibakeresési parancsok teljes készletének megtekintéséhez futtassa a vagy a `h` `?` parancsot.

Ezen a szinten is beállíthat töréspontokat a `Set-PSBreakpoint` parancsmaggal.

Ha folytatja, és teljes mértékben meghívja a szkriptet, láthatja, hogy:

* A PowerShell-konzol, amelyen végrehajtotta a végrehajtását, `Invoke-RestMethod` visszaadott egy eredményt.
* A futtatott PowerShell-konzolon `Debug-Runspace` egy parancsfájl végrehajtására vár.

Meghívhatja ugyanezt a függvényt ( `Invoke-RestMethod` például a használatával), a hibakereső pedig a parancs után azonnal megszakad `Wait-Debugger` .

## <a name="considerations-for-debugging"></a>Hibakeresési megfontolások

A functions-kód hibakeresése során vegye figyelembe a következő problémákat.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` Előfordulhat, hogy a hibakereső nem várt helyre törik

A PowerShell-bővítmény a-t használja `Debug-Runspace` , amely viszont a PowerShell `BreakAll` szolgáltatásra támaszkodik. Ez a funkció azt jelzi, hogy a PowerShell leáll az első parancs végrehajtásakor. Ez a viselkedés lehetővé teszi a töréspontok beállítását a hibakereső RunSpace belül.

A Azure Functions futtatókörnyezet néhány parancsot futtat, mielőtt ténylegesen meghívja a `run.ps1` parancsfájlt, ezért lehetséges, hogy a hibakereső a vagy a-n belül megszakad `Microsoft.Azure.Functions.PowerShellWorker.psm1` `Microsoft.Azure.Functions.PowerShellWorker.psd1` .

Ha ez a szünet történik, futtassa a `continue` vagy a `c` parancsot a Töréspont átugrásához. Ezután leállíthatja a várt töréspontot.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a hibakeresés során nehézségekbe ütközik, ellenőrizze a következőket:

| Jelölőnégyzet | Művelet |
|------|------|
| Futtatás `func --version` a terminálról. Ha olyan hibaüzenetet kap `func` , amely nem található, az alapvető eszközök (func.exe) hiányozhatnak a helyi `path` változóból.| [Telepítse újra az alapvető eszközöket](functions-run-local.md#v2).|  
| A Visual Studio Code-ban az alapértelmezett terminálnak hozzá kell férnie func.exehoz. Győződjön meg arról, hogy nem használ olyan alapértelmezett terminált, amely nem rendelkezik telepített alapeszközökkel, például a Linux Windows alrendszerét (WSL).  | Állítsa be az alapértelmezett rendszerhéjt a Visual Studio Code-ban a PowerShell 7 (ajánlott) vagy a Windows PowerShell 5,1-es verzióra.|
  

## <a name="next-steps"></a>Következő lépések

További információ a függvények a PowerShell használatával történő fejlesztéséről: [Azure functions PowerShell fejlesztői útmutató](functions-reference-powershell.md).
