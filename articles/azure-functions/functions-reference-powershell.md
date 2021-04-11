---
title: PowerShell fejlesztői referenciája Azure Functions
description: Ismerje meg, hogyan fejlesztheti a függvényeket a PowerShell használatával.
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: a7951543d548696c8de403d7980e1a41b678c6cd
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078668"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell fejlesztői útmutató

Ez a cikk részletesen ismerteti, hogyan írhat Azure Functions a PowerShell használatával.

A PowerShell Azure-függvény (Function) egy PowerShell-parancsfájlként jelenik meg, amely az aktiváláskor fut. Mindegyik függvény parancsfájlhoz tartozik egy kapcsolódó `function.json` fájl, amely meghatározza, hogy a függvény hogyan viselkedik, például hogyan aktiválódik, valamint a bemeneti és kimeneti paramétereket. További információt az [Eseményindítók és a kötések című cikkben](functions-triggers-bindings.md)talál. 

Más típusú függvényekhez hasonlóan a PowerShell-parancsfájlok is a fájlban meghatározott összes bemeneti kötés nevével egyező paramétereket fogadnak el `function.json` . Egy `TriggerMetadata` paraméter is át lett adva, amely további információkat tartalmaz a függvényt elindító triggerről.

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői referenciát](functions-reference.md). Az első PowerShell-függvény létrehozásához a [PowerShell](./create-first-function-vs-code-powershell.md) functions rövid útmutatóját is be kell fejeznie.

## <a name="folder-structure"></a>Mappa szerkezete

Egy PowerShell-projekthez szükséges mappastruktúrát a következőhöz hasonlóan néz ki. Ez az alapértelmezett érték módosítható. További információért lásd az alábbi [scriptFile](#configure-function-scriptfile) szakaszt.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

A projekt gyökerében található egy megosztott [`host.json`](functions-host-json.md) fájl, amely a Function alkalmazás konfigurálására használható. Minden függvényhez tartozik egy mappa, amely a saját kódjával (. ps1) és a kötési konfigurációs fájllal ( `function.json` ) rendelkezik. A fájl szülő könyvtárában lévő function.jsneve mindig a függvény neve.

Bizonyos kötések egy fájl jelenlétét igénylik `extensions.csproj` . A függvények futtatókörnyezetének [2. x vagy újabb](functions-versions.md) verziójában szükséges kötési kiterjesztések a fájlban vannak definiálva, a `extensions.csproj` mappában lévő tényleges függvénytár-fájlokkal `bin` . Helyi fejlesztés esetén [regisztrálnia kell a kötési bővítményeket](functions-bindings-register.md#extension-bundles). A Azure Portal funkcióinak fejlesztésekor ez a regisztráció történik.

A PowerShell-függvény alkalmazásaiban megadhatja, hogy a `profile.ps1` rendszer mikor fusson, amikor egy Function-alkalmazás elindul (más néven a *[hidegindító kezdete](#cold-start)*). További információ: PowerShell- [profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell-parancsfájl definiálása függvényként

Alapértelmezés szerint a functions Runtime a függvényét keresi a alkalmazásban `run.ps1` , ahol `run.ps1` ugyanazokat a szülő könyvtárat osztja meg a megfelelővel `function.json` .

A szkript több argumentumot adott meg a végrehajtás során. A paraméterek kezeléséhez adjon hozzá egy `param` blokkot a szkript elejéhez, ahogy az a következő példában látható:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata paraméter

A `TriggerMetadata` paraméter használatával további információkat adhat meg az aktiválásról. A további metaadatok a kötéstől a kötéstől függenek, de mindegyikük tartalmaz egy `sys` tulajdonságot, amely a következő adatokat tartalmazza:

```powershell
$TriggerMetadata.sys
```

| Tulajdonság   | Leírás                                     | Típus     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Ha UTC-ben a függvény aktiválva lett        | DateTime |
| MethodName | Az aktivált függvény neve     | sztring   |
| RandGuid   | a függvény végrehajtásának egyedi GUID azonosítója | sztring   |

Minden trigger típushoz eltérő metaadatok vannak megadva. A for például a `$TriggerMetadata` `QueueTrigger` `InsertionTime` ,, `Id` `DequeueCount` , többek között a következőt tartalmazza:. Az üzenetsor-eseményindító metaadataival kapcsolatos további információkért nyissa meg a [várólista-eseményindítók hivatalos dokumentációját](functions-bindings-storage-queue-trigger.md#message-metadata). Tekintse meg a dokumentációt azon [Eseményindítók](functions-triggers-bindings.md) használatával kapcsolatban, amelyekkel megtudhatja, mi történik az eseményindító metaadatainak között.

## <a name="bindings"></a>Kötések

A PowerShellben a [kötések](functions-triggers-bindings.md) konfigurálva és definiálva vannak a függvény function.jsján. A függvények számos módon működnek együtt a kötésekkel.

### <a name="reading-trigger-and-input-data"></a>Trigger-és bemeneti adatok olvasása

Az trigger és a bemeneti kötések a függvénynek átadott paraméterekként lesznek beolvasva. A bemeneti kötések értéke function.jsbe értékre van `direction` állítva `in` . A `name` ben definiált tulajdonság a `function.json` paraméter neve, a `param` blokkban. Mivel a PowerShell nevesített paramétereket használ a kötéshez, a paraméterek sorrendje nem számít. Azonban az ajánlott eljárás az, hogy kövesse a ben definiált kötések sorrendjét `function.json` .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Kimeneti adatokat írunk

A functions szolgáltatásban a kimeneti kötés a function.jsbe értékre van `direction` állítva `out` . Egy kimeneti kötésre a parancsmag használatával írhat `Push-OutputBinding` , amely a functions futtatókörnyezet számára érhető el. Minden esetben a `name` kötésnek a parancsmag paraméterében megadott tulajdonsága `function.json` megfelel a `Name` `Push-OutputBinding` parancsmagnak.

A következő bemutatja, hogyan hívhat meg a `Push-OutputBinding` függvény parancsfájljában:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

A folyamaton keresztül egy adott kötés értékét is átadhatja.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` a a következőhöz megadott érték alapján viselkedik `-Name` :

* Ha a megadott név nem oldható fel érvényes kimeneti kötésre, akkor hiba történik.

* Ha a kimeneti kötés fogadja az értékek gyűjteményét, többször is meghívhatja a `Push-OutputBinding` több érték leküldését.

* Ha a kimeneti kötés csak egyszeres értéket fogad el, akkor `Push-OutputBinding` a második alkalommal hibát jelez.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Szintaxis

A következő érvényes paraméterek használhatók a híváshoz `Push-OutputBinding` :

| Név | Típus | Pozíció | Leírás |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Sztring | 1 | A beállítani kívánt kimeneti kötés neve. |
| **`-Value`** | Objektum | 2 | A beállítani kívánt kimeneti kötés értéke, amelyet a rendszer a folyamat ByValue fogad el. |
| **`-Clobber`** | Kapcsolóparaméter | Elemzi | Választható Ha meg van adva, a megadott kimeneti kötéshez beállított értéket kényszeríti ki. | 

A következő közös paraméterek is támogatottak: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

További információ: [About általánosparaméterek](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

#### <a name="push-outputbinding-example-http-responses"></a>Push-OutputBinding példa: HTTP-válaszok

Egy HTTP-trigger a nevű kimeneti kötés használatával ad vissza egy választ `response` . A következő példában a kimeneti kötés `response` értéke "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Mivel a kimenet a HTTP, amely csak egy egyedi értéket fogad el, akkor a `Push-OutputBinding` rendszer a második időpontjának meghívásakor hibát jelez.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Az olyan kimenetek esetében, amelyek csak egyszeres értékeket fogadnak el, a `-Clobber` paraméter használatával felülbírálhatja a régi értéket, és nem kell hozzáadnia a gyűjteményhez. Az alábbi példa azt feltételezi, hogy már hozzáadott egy értéket. A használatával `-Clobber` a következő példa válasza felülbírálja a meglévő értéket, hogy a "output #3" értéket adja vissza:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding példa: várólista kimeneti kötése

`Push-OutputBinding` az adatokat kimeneti kötésekre küldi, például egy [Azure üzenetsor-tároló kimeneti kötését](functions-bindings-storage-queue-output.md). A következő példában a várólistára írt üzenet "output #1" értékkel rendelkezik:

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A tárolási várólista kimeneti kötése több kimeneti értéket is elfogad. Ebben az esetben a következő példát hívja meg a várólistára való első írás után, amely két elemből áll: "output #1" és "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

A következő példa, amikor az előző kettő után meghívja, két további értéket ad hozzá a kimeneti gyűjteményhez:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

A várólistába íráskor az üzenet a következő négy értéket tartalmazza: "output #1," output #2 "," output #3 "és" output #4 ".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` parancsmag

A `Get-OutputBinding` parancsmag segítségével beolvashatja a kimeneti kötésekhez jelenleg beállított értékeket. Ez a parancsmag egy olyan szórótábla kérdez le, amely tartalmazza a kimeneti kötések nevét a megfelelő értékekkel. 

Az alábbi példa a használatával az `Get-OutputBinding` aktuális kötési értékek visszaküldését szemlélteti:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` egy nevű paramétert is tartalmaz `-Name` , amely a visszaadott kötés szűrésére használható, ahogy az alábbi példában is látható:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

A-ben a helyettesítő karakterek (*) támogatottak `Get-OutputBinding` .

## <a name="logging"></a>Naplózás

A PowerShell-függvények naplózása ugyanúgy működik, mint a rendszeres PowerShell-naplózás. A naplózási parancsmagok segítségével írhat az egyes kimeneti adatfolyamokhoz. Minden parancsmag a függvények által használt naplózási szintre mutat.

| Függvények naplózási szintje | Naplózási parancsmag |
| ------------- | -------------- |
| Hiba | **`Write-Error`** |
| Figyelmeztetés | **`Write-Warning`**  | 
| Tájékoztatás | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`** <br/> _Az_ adatszint naplózásának megírása. |
| Hibakeresés | **`Write-Debug`** |
| Nyomkövetés | **`Write-Progress`** <br /> **`Write-Verbose`** |

Ezen parancsmagok mellett a folyamatba írt összes adat át lesz irányítva a `Information` naplózási szintre, és az alapértelmezett PowerShell-formázással jelenik meg.

> [!IMPORTANT]
> A `Write-Verbose` vagy a `Write-Debug` parancsmag használata nem elegendő a részletes és hibakeresési szintű naplózás megjelenítéséhez. A naplózási szint küszöbértékét is be kell állítania, amely kijelenti, hogy milyen szintű naplókra van szüksége. További információ: [az alkalmazás naplózási szintjének konfigurálása](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>A Function app naplózási szintjének konfigurálása

Azure Functions segítségével meghatározhatja a küszöbértéket, hogy könnyen szabályozható legyen a függvények írási módja a naplókba. A konzolra írt összes nyomkövetés küszöbértékének megadásához használja a (z `logging.logLevel.default` )[host.js] [ `host.json` fájlban] található tulajdonságot a hivatkozás mezőben. Ez a beállítás a Function alkalmazás összes függvényére érvényes.

A következő példa a küszöbértéket állítja be az összes függvény részletes naplózásának engedélyezéséhez, de beállítja a küszöbértéket, hogy engedélyezze a hibakeresési naplózást egy nevű függvénynél `MyFunction` :

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

További információ: [host.json Reference].

### <a name="viewing-the-logs"></a>A naplók megtekintése

Ha az függvényalkalmazás az Azure-ban fut, akkor a Application Insights használatával figyelheti. Olvassa el a [figyelési Azure functions](functions-monitoring.md) , ha többet szeretne megtudni a függvények naplóinak megtekintéséről és lekérdezéséről.

Ha a függvényalkalmazást helyileg futtatja fejlesztésre, a a naplófájlok alapértelmezése a fájlrendszer. A naplók a konzolon való megtekintéséhez állítsa a `AZURE_FUNCTIONS_ENVIRONMENT` környezeti változót a `Development` függvényalkalmazás elindítása előtt.

## <a name="triggers-and-bindings-types"></a>Eseményindítók és kötések típusai

Számos eseményindító és kötés érhető el a Function alkalmazással való használathoz. Az eseményindítók és kötések teljes listája [itt található](functions-triggers-bindings.md#supported-bindings).

Az összes eseményindító és kötés a kódban szerepel néhány valós adattípus formájában:

* Szórótábla
* sztring
* bájt []
* int
* double
* HttpRequestContext
* HttpResponseContext

A lista első öt típusa a standard .NET-típusok. Az utolsó kettőt csak a [HttpTrigger trigger](#http-triggers-and-bindings)használja.

A függvények minden kötési paraméterének a következő típusok egyikének kell lennie.

### <a name="http-triggers-and-bindings"></a>HTTP-eseményindítók és-kötések

A HTTP-és webhook-eseményindítók és a HTTP-kimeneti kötések a HTTP-üzenetküldést a kérelmek és válaszok objektumok használatával jelölik.

#### <a name="request-object"></a>Kérelem objektum

A parancsfájlba átadott kérelem-objektum típusa a `HttpRequestContext` következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Leírás                                                    | Típus                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Egy objektum, amely tartalmazza a kérelem törzsét. `Body` a a legjobb típusra van szerializálva az adathalmaz alapján. Ha például az adatfájl JSON, a rendszer szórótábla adja át. Ha az érték karakterlánc, akkor karakterláncként adja át a rendszer. | object |
| **`Headers`** | A kérések fejléceit tartalmazó szótár.                | Szótár<karakterlánc, karakterlánc><sup>*</sup> |
| **`Method`** | A kérelem HTTP-metódusa.                                | sztring                    |
| **`Params`**  | Egy objektum, amely a kérelem útválasztási paramétereit tartalmazza. | Szótár<karakterlánc, karakterlánc><sup>*</sup> |
| **`Query`** | Egy objektum, amely tartalmazza a lekérdezési paramétereket.                  | Szótár<karakterlánc, karakterlánc><sup>*</sup> |
| **`Url`** | A kérelem URL-címe.                                        | sztring                    |

<sup>*</sup> Az összes `Dictionary<string,string>` kulcs megkülönbözteti a kis-és nagybetűket.

#### <a name="response-object"></a>Válaszobjektum

A visszaküldeni kívánt válasz objektum típusa a `HttpResponseContext` következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás                                                 | Típus                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Egy objektum, amely tartalmazza a válasz törzsét.           | object                    |
| **`ContentType`** | Egy rövid kéz a válasz tartalomtípusának beállításához. | sztring                    |
| **`Headers`** | Egy objektum, amely tartalmazza a válasz fejléceit.               | Szótár vagy szórótábla   |
| **`StatusCode`**  | A válasz HTTP-állapotkódot.                       | karakterlánc vagy int             |

#### <a name="accessing-the-request-and-response"></a>A kérelem és válasz elérése

Ha HTTP-eseményindítókkal dolgozik, ugyanúgy érheti el a HTTP-kérést, mint bármely más bemeneti kötést. Ez a `param` blokkban található.

Egy `HttpResponseContext` objektummal választ adhat vissza, ahogy az az alábbi képen is látható:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

A függvény meghívásának eredménye a következő:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Triggerek és kötések típusa – öntés

Bizonyos kötésekhez, például a blob-kötésekhez, meg tudja adni a paraméter típusát.

Ha például a blob Storage-ból karakterláncként megadott adatokkal szeretne rendelkezni, adja hozzá a következő típust a saját `param` blokkhoz:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profil

A PowerShellben van egy PowerShell-profil fogalma. Ha még nem ismeri a PowerShell-profilokat, tekintse meg a következő témakört: [About Profiles](/powershell/module/microsoft.powershell.core/about/about_profiles).

A PowerShell-függvények esetében a profil parancsfájlja PowerShell-feldolgozó példányon, az első üzembe helyezéskor és a tétlen állapotba állítása után lesz végrehajtva ([hidegindítás](#cold-start)). Ha a Egyidejűség engedélyezve van a [PSWorkerInProcConcurrencyUpperBound](#concurrency) érték beállításával, a rendszer minden létrehozott RunSpace futtatja a profil parancsfájlját.

Ha eszközöket használó Function-alkalmazást hoz létre, például a Visual Studio Code-ot és a Azure Functions Core Toolst, a rendszer alapértelmezés szerint létrehoz egy alapértelmezett értéket `profile.ps1` . Az alapértelmezett profil a [GitHub-tárház alapvető eszközein](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) marad, és a következőket tartalmazza:

* Automatikus MSI-hitelesítés az Azure-ba.
* A Azure PowerShell PowerShell-aliasok bekapcsolásának lehetősége, `AzureRM` Ha szeretné.

## <a name="powershell-versions"></a>PowerShell-verziók

A következő táblázat a functions Runtime egyes főbb verzióiban elérhető PowerShell-verziókat és a szükséges .NET-verziót mutatja be:

| Függvények verziója | PowerShell-verzió                               | .NET-verzió  | 
|-------------------|--------------------------------------------------|---------------|
| 3. x (ajánlott) | PowerShell 7 (ajánlott)<br/>6. PowerShell-mag | .NET Core 3.1<br/>.NET Core 2.1 |
| 2. x               | 6. PowerShell-mag                                | .NET Core 2.2 |

Az aktuális verziót bármely függvényből kinyomtatva láthatja `$PSVersionTable` .

### <a name="running-local-on-a-specific-version"></a>Helyi futtatása adott verzión

Amikor helyileg futtatja az Azure Functions Runtime alapértelmezett értékeit a PowerShell Core 6 használatával. Ha helyileg futtatja a PowerShell 7-et, a beállítást a `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"` `Values` projekt gyökerében található fájl local.setting.jsjában kell hozzáadnia a tömbhöz. Ha helyileg futtatja a PowerShell 7-es verzióját, a local.settings.jsfájl a következő példához hasonlóan néz ki: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>A PowerShell verziójának módosítása

A Function alkalmazásnak a 3. x verzióban kell futnia, hogy a PowerShell Core 6-ról a PowerShell 7-re tudjon frissíteni. Ennek módjáról [az aktuális futtatókörnyezet verziójának megtekintése és frissítése](set-runtime-version.md#view-and-update-the-current-runtime-version)című témakörben olvashat bővebben.

A következő lépésekkel módosíthatja a Function alkalmazás által használt PowerShell-verziót. Ezt a Azure Portal vagy a PowerShell használatával teheti meg.

# <a name="portal"></a>[Portál](#tab/portal)

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg a Function alkalmazást.

1. A **Beállítások** területen válassza a **konfiguráció** elemet. Az **általános beállítások** lapon keresse meg a **PowerShell verzióját**. 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="Válassza ki a Function alkalmazás által használt PowerShell-verziót"::: 

1. Válassza ki a kívánt **PowerShell Core-verziót** , és kattintson a **Mentés** gombra. Ha a függőben lévő újraindításra figyelmezteti, válassza a **Folytatás** lehetőséget. A Function alkalmazás újraindul a kiválasztott PowerShell-verzióra. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Futtassa a következő szkriptet a PowerShell verziójának módosításához: 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

Cserélje le a, a és az értékét `<SUBSCRIPTION_ID>` `<RESOURCE_GROUP>` `<FUNCTION_APP>` Az Azure-előfizetés azonosítójával, az erőforráscsoport és a Function alkalmazás nevével együtt.  Továbbá cserélje le `<VERSION>` a `~6` vagy a-t `~7` . Ellenőrizheti a `powerShellVersion` visszaadott kivonatoló tábla beállításának frissített értékét `Properties` . 

---

A Function alkalmazás újraindul a konfiguráció módosítása után.

## <a name="dependency-management"></a>Függőségkezelés

A functions lehetővé teszi a [PowerShell-Galéria](https://www.powershellgallery.com) kihasználása a függőségek kezeléséhez. Ha engedélyezve van a függőségek kezelése, a rendszer a requirements.psd1 fájlt használja a szükséges modulok automatikus letöltéséhez. Ezt a viselkedést úgy engedélyezheti, ha a `managedDependency` tulajdonságot a `true` [ fájlhost.js](functions-host-json.md)gyökérkönyvtárában állítja be, ahogy az alábbi példában is látható:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Új PowerShell functions-projekt létrehozásakor a függőségek kezelése alapértelmezés szerint engedélyezve van, és az Azure- [ `Az` modul](/powershell/azure/new-azureps-module-az) is elérhető. A jelenleg támogatott modulok maximális száma 10. A támogatott szintaxis _`MajorNumber`_ `.*` vagy a modul pontos verziója, ahogyan az a következő requirements.psd1 példában látható:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Ha frissíti a requirements.psd1 fájlt, a rendszer újraindítást követően telepíti a frissített modulokat.

> [!NOTE]
> A felügyelt függőségekhez hozzáférés szükséges a www.powershellgallery.com a modulok letöltéséhez. Helyileg futtatva győződjön meg arról, hogy a futtatókörnyezet hozzáférhet ehhez az URL-címhez a szükséges tűzfalszabályok hozzáadásával.

> [!NOTE]
> A felügyelt függőségek jelenleg nem támogatják azokat a modulokat, amelyek megkövetelik a felhasználónak, hogy fogadja el a licencet, vagy ha a licencet interaktívan fogadja el, vagy ha meghívja a `-AcceptLicense` kapcsolót `Install-Module` .

A következő Alkalmazásbeállítások segítségével megváltoztathatja a felügyelt függőségek letöltésének és telepítésének módját. Az alkalmazás frissítése a-n belül elindul `MDMaxBackgroundUpgradePeriod` , és a frissítési folyamat körülbelül a-ban fejeződik be `MDNewSnapshotCheckPeriod` .

| függvényalkalmazás beállítás              | Alapértelmezett érték             | Leírás                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 nap)     | Minden PowerShell-munkavégző folyamat kezdeményezi a modul frissítéseinek ellenőrzését a PowerShell-galéria a folyamat indításakor, majd minden `MDMaxBackgroundUpgradePeriod` után. Ha egy új modul verziója elérhető a PowerShell-galériaban, a rendszer telepíti a fájlrendszerre, és elérhetővé teszi őket a PowerShell-feldolgozók számára. Ennek az értéknek a csökkentése lehetővé teszi, hogy a Function alkalmazás hamarabb lekérje a modul újabb verzióit, de az alkalmazás erőforrás-használatát is növeli (hálózati I/O-, CPU-és tárolási). Az érték növelése csökkenti az alkalmazás erőforrás-használatát, de az új modulok verzióinak az alkalmazásba való kézbesítése is késleltethető. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 óra)       | Miután telepítette az új modul-verziókat a fájlrendszerbe, minden PowerShell-munkavégző folyamatot újra kell indítani. A PowerShell-feldolgozók újraindítása hatással van az alkalmazás rendelkezésre állására, mivel ez megszakíthatja az aktuális függvény végrehajtását. Amíg az összes PowerShell-munkavégző folyamat újra nem indul, a függvény meghívása a régi vagy az új modul verzióját is használhatja. Az összes PowerShell-feldolgozó újraindítása a alkalmazáson belül `MDNewSnapshotCheckPeriod` . Az érték növelésével csökken a megszakítások gyakorisága, de az is előfordulhat, hogy a függvény meghívásakor a régi vagy az új modul nem determinisztikus módon verzióját használja. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 nap)     | A gyakori munkavégző újraindítások esetén a modul frissítéseinek figyelése nem történik meg, ha bármelyik feldolgozó már kezdeményezte a legutóbbi ellenőrzést `MDMinBackgroundUpgradePeriod` . |

A saját egyéni moduljainak kihasználása kicsit eltér a megszokott módon.

A helyi számítógépen a modul az egyik globálisan elérhető mappájába kerül `$env:PSModulePath` . Ha az Azure-ban fut, nincs hozzáférése a gépen telepített modulokhoz. Ez azt jelenti, hogy a `$env:PSModulePath` PowerShell-függvény alkalmazásának működése eltér a `$env:PSModulePath` szokásos PowerShell-parancsfájltól.

A függvények között `PSModulePath` két elérési út található:

* Egy `Modules` mappa, amely a Function alkalmazás gyökerében található.
* A `Modules` PowerShell nyelvi feldolgozója által vezérelt mappa elérési útja.


### <a name="function-app-level-modules-folder"></a>Függvény alkalmazás-szintű `Modules` mappája

Egyéni modulok használatához olyan modulokat helyezhet el, amelyeken a függvények egy `Modules` mappától függenek. Ebből a mappából a modulok automatikusan elérhetők a functions Runtime számára. A Function alkalmazás bármely funkciója használhatja ezeket a modulokat. 

> [!NOTE]
> A requirements.psd1 fájlban megadott modulok automatikusan letöltődnek, és az elérési úton is szerepelnek, így nem szükséges a modulok mappában szerepelnie. Ezeket a rendszer helyileg tárolja a `$env:LOCALAPPDATA/AzureFunctions` mappában és a `/data/ManagedDependencies` mappában a felhőben való futtatáskor.

Az egyéni modul funkció kihasználása érdekében hozzon létre egy `Modules` mappát a Function alkalmazás gyökerében. Másolja a függvényekben használni kívánt modulokat erre a helyre.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Egy `Modules` mappával a Function alkalmazásnak a következő mappastruktúrát kell megjelennie:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Ha elindítja a Function alkalmazást, a PowerShell nyelvi feldolgozó hozzáadja ezt a `Modules` mappát a `$env:PSModulePath` modulhoz, így a modulok automatikus betöltését ugyanúgy használhatja, mint egy normál PowerShell-parancsfájlban.

### <a name="language-worker-level-modules-folder"></a>Nyelvi feldolgozói szint `Modules` mappája

A PowerShell nyelvi feldolgozója általában számos modult használ. Ezek a modulok a utolsó helyén vannak meghatározva `PSModulePath` . 

A modulok aktuális listája a következő:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): az archívumok, például `.zip` , és mások használatához használt modul `.nupkg` .
* **ThreadJob**: a PowerShell-feladatok API-k szálon alapuló implementációja.

Alapértelmezés szerint a függvények a modulok legújabb verzióját használják. Egy adott modul verziójának használatához helyezze az adott verziót a `Modules` Function alkalmazás mappájába.

## <a name="environment-variables"></a>Környezeti változók

A függvények, az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai a végrehajtás során környezeti változókként jelennek meg. Ezeket a beállításokat a használatával érheti el `$env:NAME_OF_ENV_VAR` , ahogy az az alábbi példában is látható:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Helyileg futtatva az Alkalmazásbeállítások beolvasása a Project fájl [local.settings.js](functions-run-local.md#local-settings-file) .

## <a name="concurrency"></a>Egyidejűség

Alapértelmezés szerint a függvények PowerShell-futtatókörnyezete egyszerre csak egy függvény hívását képes feldolgozni. Előfordulhat azonban, hogy ez a párhuzamossági szint nem elegendő a következő helyzetekben:

* Ha nagy számú hívást próbál meg egyszerre kezelni.
* Ha olyan függvényekkel rendelkezik, amelyek más függvényeket hívnak meg ugyanazon a Function alkalmazáson belül.

A számítási feladatok típusától függően néhány Párhuzamossági modell is felderíthető:

* Növekedés ```FUNCTIONS_WORKER_PROCESS_COUNT``` . Ez lehetővé teszi, hogy az egyazon példányon belül több folyamaton, a processzor és a memória terhelésének bevezetésével kezelje a funkciót. Az I/O-kötésű függvények általában nem fognak szenvedni ebből a terhelésből. A CPU-kötésű függvények esetében a hatás jelentős lehet.

* Növelje meg az ```PSWorkerInProcConcurrencyUpperBound``` alkalmazásbeállítás értékét. Ez lehetővé teszi, hogy több futási terek hozzon létre ugyanazon a folyamaton belül, ami jelentősen csökkenti a processzor-és a memória terhelését.

Ezeket a környezeti változókat a Function alkalmazás [Alkalmazásbeállítások](functions-app-settings.md) között állíthatja be.

A használati esettől függően Durable Functions jelentősen növelheti a méretezhetőséget. További információ: [Durable functions alkalmazási minták](./durable/durable-functions-overview.md?tabs=powershell#application-patterns).

>[!NOTE]
> Előfordulhat, hogy a "kérelmek várólistára helyezése az elérhető futási terek hiányában" figyelmeztetést kap, ne feledje, hogy ez nem jelent hibát. Az üzenet közli, hogy a kérések várólistára kerülnek, és az előző kérések befejezésekor lesznek kezelve.

### <a name="considerations-for-using-concurrency"></a>A párhuzamosság használatának szempontjai

Alapértelmezés szerint a PowerShell _egyszálas_ parancsfájlkezelési nyelv. A párhuzamosság azonban több PowerShell-futási terek is hozzáadható ugyanabban a folyamatban. A létrehozott futási terek-mennyiség megegyezik az ```PSWorkerInProcConcurrencyUpperBound``` alkalmazás beállításával. Az átviteli sebességet a kiválasztott csomagban elérhető CPU és memória mennyisége befolyásolja.

A Azure PowerShell egyes _folyamat-szintű_ kontextusokat és állapotot használ, hogy segítsen a felesleges gépelésben. Ha azonban a függvény alkalmazásban bekapcsolja a párhuzamosságot, és meghívja az állapotot megváltoztató műveleteket, akkor a verseny feltételei is megváltozhatnak. Ezek a versenyhelyzet nehéz hibakeresést végezni, mert egy hívás egy bizonyos állapotra támaszkodik, és a másik meghívás megváltoztatta az állapotot.

Az Azure PowerShell-ben óriási érték van, mivel néhány művelet jelentős időt vehet igénybe. Azonban körültekintően kell eljárnia. Ha azt gyanítja, hogy a verseny feltételét tapasztalja, állítsa a PSWorkerInProcConcurrencyUpperBound az alkalmazásra, `1` és Ehelyett használja a [nyelvi feldolgozói folyamat szintjének elkülönítését](functions-app-settings.md#functions_worker_process_count) a párhuzamossághoz.

## <a name="configure-function-scriptfile"></a>Függvény konfigurálása `scriptFile`

Alapértelmezés szerint a rendszer egy PowerShell-függvényt hajt végre `run.ps1` , egy olyan fájlt, amely ugyanazt a szülő könyvtárat osztja meg a megfelelővel `function.json` .

A `scriptFile` (z) tulajdonsága a `function.json` következő példához hasonló mappa-struktúra beszerzésére használható:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Ebben az esetben a `function.json` for `myFunction` kifejezés egy olyan `scriptFile` tulajdonságot tartalmaz, amely a fájlra hivatkozik, és az exportált függvényt futtatja.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>PowerShell-modulok használata BelépésiPont konfigurálásával

Ez a cikk a sablonok által generált alapértelmezett parancsfájl PowerShell-függvényeit mutatja `run.ps1` .
A függvényeket azonban PowerShell-modulokban is felveheti. A modulban a `scriptFile` `entryPoint` (z) function.jskonfigurációs fájljának és mezőinek használatával hivatkozhat az adott függvény kódjára.

Ebben az esetben `entryPoint` a a PowerShell-modulban hivatkozott függvény vagy parancsmag neve `scriptFile` .

Vegye figyelembe a következő mappastruktúrát:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Ahol a a `PSFunction.psm1` következőket tartalmazza:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Ebben a példában a konfiguráció `myFunction` egy olyan `scriptFile` tulajdonságot tartalmaz, amely hivatkozik rá `PSFunction.psm1` , amely egy másik mappában található PowerShell-modul.  A `entryPoint` tulajdonság a `Invoke-PSTestFunc` függvényre hivatkozik, amely a modul belépési pontja.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Ezzel a konfigurációval a beolvasás `Invoke-PSTestFunc` pontosan az lesz `run.ps1` .

## <a name="considerations-for-powershell-functions"></a>A PowerShell-függvények szempontjai

A PowerShell-függvények használatakor vegye figyelembe az alábbi részekben ismertetett szempontokat.

### <a name="cold-start"></a>Hidegindító

Azure Functions a [kiszolgáló nélküli üzemeltetési modellben](consumption-plan.md)való fejlesztésekor a hideg indítás a valóság. A *hűtőházi kezdés* azt az időtartamot jelenti, ameddig a Function alkalmazásnak futnia kell a kérelem feldolgozásához. A hidegindító folyamat gyakrabban fordul elő a fogyasztási tervben, mert a Function alkalmazás leáll az inaktivitási időszakok során.

### <a name="bundle-modules-instead-of-using-install-module"></a>Köteg modulok használata helyett `Install-Module`

A szkript minden meghívásnál fut. Kerülje a használatát `Install-Module` a parancsfájlban. Ehelyett használja a `Save-Module` közzététel előtt, hogy a függvénynek ne kelljen időt pazarolnia a modul letöltésével. Ha a ritkán használt funkciók hatással vannak a függvényekre, érdemes lehet a Function alkalmazást egy olyan [app Service-csomagra](dedicated-plan.md) telepíteni *, amely* a [prémium szintű csomagra](functions-premium-plan.md)van beállítva.

## <a name="next-steps"></a>Következő lépések

További információkat találhat az alábbi forrásokban:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)

[host.json-referencia]: functions-host-json.md
