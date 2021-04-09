---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 190524251d139e1421c1aac93d5a4dd523068a7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958324"
---
## <a name="local-settings-file"></a>Local settings file (Helyi beállításfájl)

A (z) local.settings.jsfájl tárolja az Alkalmazásbeállítások, a kapcsolatok karakterláncai és a helyi fejlesztési eszközök által használt beállításokat. A fájl local.settings.jsjának beállításait csak akkor használja a rendszer, ha helyileg futtatja a projekteket. A helyi beállítások fájlja a következő struktúrával rendelkezik:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Ezek a beállítások akkor támogatottak, ha helyileg futtatja a projekteket:

| Beállítás      | Leírás                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Ha ez a beállítás a értékre van beállítva `true` , az összes érték helyi számítógép kulccsal lesz titkosítva. `func settings`Parancsokkal használható. Az alapértelmezett érték `false`. Érdemes lehet titkosítani a local.settings.jsa helyi számítógépen lévő fájlon, ha titkos kulcsokat tartalmaz, például a szolgáltatás kapcsolódási karakterláncait. A gazdagép automatikusan visszafejti a beállításokat a futtatásakor. A `func settings decrypt` helyileg titkosított beállítások olvasásának megkísérlése előtt használja a parancsot. |
| **`Values`** | Az Alkalmazásbeállítások és a kapcsolódási karakterláncok tömbje, amely akkor használatos, amikor egy projekt helyileg fut. Ezek a kulcs-érték (String-String) párok megfelelnek az Azure-beli Function alkalmazás beállításainak, például: [`AzureWebJobsStorage`] . Számos eseményindító és kötés olyan tulajdonsággal rendelkezik, amely egy kapcsolati karakterlánc-alkalmazásra vonatkozó beállításra hivatkozik, például `Connection` a [blob Storage eseményindítóhoz](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Ezen tulajdonságok esetében a tömbben definiált Alkalmazásbeállítás szükséges `Values` . A gyakran használt beállítások listáját a következő táblázatban tekintheti meg. <br/>Az értékeknek karakterláncnak és nem JSON-objektumoknak vagy tömböknek kell lenniük. A nevek beállítása nem tartalmazhat kettőspontot ( `:` ) vagy dupla aláhúzást ( `__` ). A kettős aláhúzási karakterek a futtatókörnyezet számára vannak fenntartva, és a kettőspont a [függőségi injekció](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings)támogatásához van fenntartva. |
| **`Host`** | Az ebben a szakaszban található beállítások testre szabják a functions gazdagép folyamatát, amikor helyileg futtat projekteket. Ezek a beállítások eltérhetnek a beállítások host.jstól, amelyek akkor is érvényesek, amikor projekteket futtat az Azure-ban. |
| **`LocalHttpPort`** | Beállítja a helyi functions-gazdagép (és) futtatásakor használt alapértelmezett portot `func host start` `func run` . A `--port` parancssori kapcsoló elsőbbséget élvez ezzel a beállítással szemben. Ha például a Visual Studio IDE-ban fut, a portszámot a "Project Properties-> debug" ablakra kattintva, és explicit módon megadhatja a portszámot egy olyan `host start --port <your-port-number>` parancsban, amely az "Application argumentumok" mezőben adható meg. |
| **`CORS`** | Meghatározza a [több eredetű erőforrás-megosztás (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)számára engedélyezett származási erőforrásokat. Az eredetek vesszővel tagolt listaként vannak megadva, szóközök nélkül. A helyettesítő karakteres érték ( \* ) támogatott, amely bármilyen forrásból engedélyezi a kérelmeket. |
| **`CORSCredentials`** |  Ha a értékre van állítva `true` , a `withCredentials` kérelmeket engedélyezi. |
| **`ConnectionStrings`** | Egy gyűjtemény. Ne használja ezt a gyűjteményt a függvények kötései által használt kapcsolati sztringekhez. Ezt a gyűjteményt kizárólag olyan keretrendszerek használják, amelyek általában a `ConnectionStrings` konfigurációs fájl (például [Entity Framework](/ef/ef6/)) szakaszának a kapcsolatok karakterláncait kapják meg. Az objektumban található kapcsolódási karakterláncok a [System. SqlClient](/dotnet/api/system.data.sqlclient). szolgáltatói típussal lesznek hozzáadva a környezethez. Az ebben a gyűjteményben lévő elemek nem jelennek meg az Azure-ban más Alkalmazásbeállítások használatával. Ezeket az értékeket explicit módon fel kell vennie a `Connection strings` Function app-beállítások gyűjteményéből. Ha a [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) függvény kódjában hoz létre egy kódot, a kapcsolati sztring értékét a portálon az **Alkalmazásbeállítások** között lévő többi kapcsolattal együtt kell tárolnia. |

A tömbben a következő Alkalmazásbeállítások szerepelhetnek a **`Values`** helyi futtatásakor:

| Beállítás | Értékek | Leírás |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Storage-fiókhoz tartozó kapcsolatok karakterlánca vagy<br/>`UseDevelopmentStorage=true`| Egy Azure Storage-fiókhoz tartozó kapcsolatok sztringjét tartalmazza. A HTTP-n kívüli eseményindítók használata esetén szükséges. További információ: [`AzureWebJobsStorage`] hivatkozás.<br/>Ha az [Azure Storage-emulátor](../articles/storage/common/storage-use-emulator.md) helyileg van telepítve, és a értékre van állítva [`AzureWebJobsStorage`] `UseDevelopmentStorage=true` , a Core Tools az emulátort használja. Az emulátor a fejlesztés során hasznos, de a telepítés előtt tesztelni kell egy tényleges tárolási kapcsolatban.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Ha helyileg futtatja a függvényt, adja hozzá `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` a gyűjteményhez, ahol a a `<FUNCTION_NAME>` függvény neve. További információt a [függvények letiltása a Azure Functionsban](../articles/azure-functions/disable-function.md#localsettingsjson) című témakörben talál. |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| A függvények futtatókörnyezetének megcélozt nyelvét jelzi. A függvények futtatókörnyezetének 2. x vagy újabb verziója szükséges. Ez a beállítás a projekthez a Core Tools alapján jön létre. További információért lásd a [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) hivatkozást.|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Azt jelzi, hogy a PowerShell 7-et helyileg futtatva kell használni. Ha nincs beállítva, akkor a PowerShell Core 6 lesz használatban. Ez a beállítás csak helyi futtatáskor használatos. Az Azure-ban való futtatáskor a PowerShell futásidejű verzióját a `powerShellVersion` hely konfigurációs beállítása határozza meg, amely [a portálon állítható be](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version). | 

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
