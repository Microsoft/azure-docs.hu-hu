---
title: Python fejlesztői referenciája Azure Functions
description: Ismerje meg, hogyan fejlesztheti a függvényeket a Python használatával
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0829ef9a6b63866c2527e521ed7edf48a3249392
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044255"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python fejlesztői útmutató

Ez a cikk bemutatja, hogyan fejlesztheti Azure Functions a Python használatával. Az alábbi tartalom azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői útmutatót](functions-reference.md).

Python-fejlesztőként a következő cikkek egyike is érdekli:

| Első lépések | Alapelvek| Forgatókönyvek/minták |
| -- | -- | -- | 
| <ul><li>[Python-függvény a Visual Studio Code használatával](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Python-függvény a Terminal/Command parancssorral](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Fejlesztői útmutató](functions-reference.md)</li><li>[Üzemeltetési lehetőségek](functions-scale.md)</li><li>[Teljesítménnyel &nbsp; kapcsolatos megfontolások](functions-best-practices.md)</li></ul> | <ul><li>[Képek besorolása a PyTorch használatával](machine-learning-pytorch.md)</li><li>[Azure Automation-minta](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Gépi tanulás a TensorFlow-val](functions-machine-learning-tensorflow.md)</li><li>[Python-minták tallózása](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Noha a [Python-alapú Azure functions helyileg fejlesztheti a Windows](create-first-function-vs-code-python.md#run-the-function-locally)rendszeren, a Python csak Linux-alapú üzemeltetési csomag esetén támogatott az Azure-ban való futtatáskor. Tekintse meg a támogatott [operációs rendszerek/futtatókörnyezet-](functions-scale.md#operating-systemruntime) kombinációk listáját.

## <a name="programming-model"></a>Programozási modell

A Azure Functions egy olyan állapot nélküli metódust vár a Python-parancsfájlban, amely feldolgozza a bemenetet, és kimenetet hoz létre. Alapértelmezés szerint a futtatókörnyezet azt várja, hogy a metódus a fájlban megadott globális metódusként legyen implementálva `main()` `__init__.py` . [Alternatív belépési pontot is megadhat](#alternate-entry-point).

Az eseményindítók és kötések adatai a metódus attribútumain keresztül a függvényhez vannak kötve a `name` *function.js* fájlban megadott tulajdonság használatával. Az alábbi  _function.js_ például egy, a nevű HTTP-kérelem által aktivált egyszerű függvényt ismertet `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

A definíció alapján a `__init__.py` függvény kódját tartalmazó fájl a következő példához hasonló lehet:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

A függvényben explicit módon deklarálhatja az attribútum típusát és a visszatérési típust is a Python típusú jegyzetek használatával. Ez segít a számos Python-kód-szerkesztő által biztosított IntelliSense-és automatikus kiegészítési funkciók használatában.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Használja az [Azure. functions. *](/python/api/azure-functions/azure.functions?view=azure-python&preserve-view=true) csomagban található Python-megjegyzéseket a bemenetek és kimenetek a metódusokhoz való kötéséhez.

## <a name="alternate-entry-point"></a>Másodlagos belépési pont

A függvények alapértelmezett viselkedését megváltoztathatja, ha a és a tulajdonságokat is `scriptFile` Megadja `entryPoint` a *function.js* fájljában. Az alábbi _function.js_ például azt jelzi, hogy a futtatókörnyezet a `customentry()` metódust használja a _Main.py_ fájlban, az Azure-függvény belépési pontja.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mappa szerkezete

A Python functions projekt javasolt mappastruktúrát a következő példához hasonlóan néz ki:

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
A fő projekt mappája (<project_root>) a következő fájlokat tartalmazza:

* *local.settings.json*: az Alkalmazásbeállítások és a kapcsolódási karakterláncok helyi futtatásakor való tárolásához használatos. Ez a fájl nem jelenik meg az Azure-ban. További információ: [Local. Settings. file](functions-run-local.md#local-settings-file).
* *requirements.txt*: azon Python-csomagok listáját tartalmazza, amelyeket a rendszeren az Azure-ba való közzétételkor telepít.
* *host.json*: olyan globális konfigurációs beállításokat tartalmaz, amelyek a Function alkalmazás összes funkcióját érintik. Ez a fájl közzé van téve az Azure-ban. Nem minden beállítás támogatott a helyi futtatásakor. További információ: [host.json](functions-host-json.md).
* *. vscode/*: (nem kötelező) a tároló vscode konfigurációját tartalmazza. További információ: VSCode- [beállítás](https://code.visualstudio.com/docs/getstarted/settings).
* *. venv/*: (nem kötelező) a helyi fejlesztés által használt Python virtuális környezetet tartalmaz.
* *Docker*: (nem kötelező) a projekt [Egyéni tárolóban](functions-create-function-linux-custom-image.md)történő közzétételekor használatos.
* *tesztek/*: (nem kötelező) a Function alkalmazás tesztelési eseteit tartalmazza.
* *. funcignore*: (nem kötelező) deklarálja azokat a fájlokat, amelyek nem tudnak közzétenni az Azure-ban. Ez a fájl általában a `.vscode/` szerkesztői beállítások figyelmen kívül hagyása, `.venv/` a helyi Python virtuális környezet figyelmen kívül hagyása, `tests/` a tesztelési esetek figyelmen kívül hagyása és `local.settings.json` a helyi Alkalmazásbeállítások közzétételének megakadályozása érdekében.

Minden függvényhez tartozik a saját kód fájlja és a kötési konfigurációs fájl (function.js).

Amikor az Azure-beli Function alkalmazásba helyezi üzembe a projektet, a főprojekt (*<project_root>*) mappájának teljes tartalmát bele kell foglalni a csomagba, de nem maga a mappa, ami azt jelenti, hogy a `host.json` csomag gyökerében kell lennie. Javasoljuk, hogy a teszteket egy olyan mappában tartsa, amely más függvényekkel együtt, ebben a példában `tests/` . További információ: [Unit Testing (egység tesztelése](#unit-testing)).

## <a name="import-behavior"></a>Importálási viselkedés

A függvények kódjában az abszolút és a relatív hivatkozásokkal is importálhatja a modulokat. A fent látható mappa szerkezete alapján a következő importálások működnek a függvény fájljában *<project_root> My az \_ első \_ függvényt \\ _ \_ init \_ \_ .* a:

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  A *shared_codenak/* mappának tartalmaznia kell egy \_ \_ init \_ \_ . rajzfájl fájlt, hogy az abszolút importálási szintaxis használatakor Python-csomagként legyen megjelölve.

A következő \_ \_ alkalmazás \_ \_ -Importálás és a legfelső szintű relatív importálás elavult, mivel a statikus típus-ellenőrző nem támogatja, és a Python-tesztelési keretrendszerek nem támogatják a következőket:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Eseményindítók és bemenetek

A bemenetek két kategóriára vannak osztva Azure Functions: aktiválja a bemenetet és a további bemenetet. Bár a fájlban különböznek `function.json` , a használat megegyezik a Python-kóddal.  Az trigger és a bemeneti források kapcsolódási sztringje vagy titka a `local.settings.json` fájlban a helyi futtatásakor, valamint az Azure-ban való futtatáskor az alkalmazás beállításainak megfelelően képezhető le.

A következő kód például a kettő közötti különbséget mutatja be:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

A függvény meghívásakor a rendszer a HTTP-kérelmet a következőként továbbítja a függvénynek: `req` . A rendszer beolvas egy bejegyzést az Azure-Blob Storage az útvonal URL-címének _azonosítója_ alapján, és a `obj` függvény törzsében elérhetővé teszi őket.  Itt a megadott Storage-fiók a AzureWebJobsStorage alkalmazás-beállításban található, a Function alkalmazás által használt Storage-fiók.


## <a name="outputs"></a>Kimenetek

A kimenet a visszatérési értékben és a kimeneti paraméterekben is kifejezhető. Ha csak egy kimenet van, javasoljuk, hogy használja a visszatérési értéket. Több kimenet esetén a kimeneti paramétereket kell használnia.

Egy függvény visszatérési értékének egy kimeneti kötés értékeként való használatához a `name` kötés tulajdonságát a következő értékre kell beállítani `$return` : `function.json` .

Több kimenet létrehozásához használja a `set()` csatoló által biztosított metódust, [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) amellyel egy értéket rendelhet a kötéshez. A következő függvény például leküldheti az üzeneteket egy várólistába, és HTTP-választ is küldhet.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Naplózás

A Azure Functions futtatókörnyezet-naplózó hozzáférése a Function alkalmazás egyik gyökérszintű kezelőjén keresztül érhető el [`logging`](https://docs.python.org/3/library/logging.html#module-logging) . Ez a naplózó Application Insightshez van kötve, és lehetővé teszi a függvények végrehajtása során észlelt figyelmeztetések és hibák megjelölését.

Az alábbi példa egy tájékoztató üzenetet naplóz, ha a függvényt HTTP-eseményindítón keresztül hívja meg.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

További naplózási módszerek érhetők el, amelyek lehetővé teszik a konzolra való írást különböző nyomkövetési szinteken:

| Metódus                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | KRITIKUS szintű üzenetet ír a gyökérszintű naplózó számára.  |
| **`error(_message_)`**   | A legfelső szintű naplózó üzenetbe írja a Level hibát.    |
| **`warning(_message_)`**    | Üzenet írása a root naplózó szintű FIGYELMEZTETÉSsel.  |
| **`info(_message_)`**    | A root naplózó szintű adatokat tartalmazó üzenetet ír.  |
| **`debug(_message_)`** | Egy szintű HIBAKERESÉSt tartalmazó üzenetet ír a root Logger-ben.  |

További információ a naplózásról: [Azure functions figyelése](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP-trigger és-kötések

A HTTP-trigger a fájl function.jsban van definiálva. A `name` kötésnek meg kell egyeznie a függvény elnevezett paraméterével.
Az előző példákban egy kötési nevet `req` használunk. Ez a paraméter egy [HttpRequest] objektum, és a rendszer egy [HttpResponse] objektumot ad vissza.

A [HttpRequest] objektumból lekérheti a kérések fejléceit, a lekérdezési paramétereket, az útvonal paramétereit és az üzenet törzsét.

A következő példa a [Pythonhoz készült http trigger sablonból](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)származik.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

Ebben a függvényben a lekérdezési paraméter értékét a rendszer a `name` `params` [HttpRequest] objektum paraméterében szerzi be. A JSON-kódolású üzenet törzse a metódus használatával olvasható `get_json` .

Hasonlóképpen beállíthatja a és a `status_code` `headers` válaszüzenetet is a visszaadott [HttpResponse] objektumban.

## <a name="scaling-and-performance"></a>Skálázás és teljesítmény

A Python functions-alkalmazások méretezésére és teljesítményére vonatkozó ajánlott eljárásokért tekintse meg a [Python-méretezést és-teljesítményt ismertető cikket](python-scale-performance-reference.md).

## <a name="context"></a>Környezet

Ha egy függvény hívási környezetét szeretné lekérni a végrehajtás során, adja [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python&preserve-view=true) meg az argumentumot az aláírásában.

Például:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A [**környezeti**](/python/api/azure-functions/azure.functions.context?view=azure-python&preserve-view=true) osztály a következő karakterlánc-attribútumokkal rendelkezik:

`function_directory` Az a címtár, amelyben a függvény fut.

`function_name` A függvény neve.

`invocation_id` Az aktuális függvény meghívásának azonosítója.

## <a name="global-variables"></a>Globális változók

Nem garantált, hogy az alkalmazás állapota továbbra is megmarad a jövőbeli végrehajtásokhoz. Azonban a Azure Functions futtatókörnyezet gyakran ugyanazt a folyamatot használja ugyanazon alkalmazás több végrehajtásához. Egy költséges számítás eredményeinek gyorsítótárazásához globális változóként deklarálja.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Környezeti változók

A függvények területen az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai környezeti változókként vannak kitéve a végrehajtás során. Ezek a beállítások deklarálva `import os` , majd a használatával érhetők el `setting = os.environ["setting-name"]` .

A következő példa beolvassa az [alkalmazás beállítását](functions-how-to-use-azure-function-app-settings.md#settings)a nevű kulccsal `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Helyi fejlesztés esetén az Alkalmazásbeállítások a [fájl local.settings.jsmaradnak](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Python-verzió

A Azure Functions a következő Python-verziókat támogatja:

| Függvények verziója | Python- <sup>*</sup> verziók |
| ----- | ----- |
| 3. x | 3,9 (előzetes verzió) <br/> 3,8<br/>3.7<br/>3,6 |
| 2. x | 3.7<br/>3,6 |

<sup>*</sup>Hivatalos CPython-disztribúciók

Ha egy adott Python-verziót szeretne kérni a Function alkalmazás Azure-ban való létrehozásakor, használja az `--runtime-version` [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) parancsot. A functions futásidejű verzióját a beállítás adja meg `--functions-version` . A Python verziója a Function alkalmazás létrehozásakor van beállítva, és nem módosítható.

Helyileg futtatva a futtatókörnyezet a rendelkezésre álló Python-verziót használja.

## <a name="package-management"></a>Csomagkezelés

Amikor helyileg fejleszti a Azure Functions Core Tools vagy a Visual Studio Code-ot, adja hozzá a szükséges csomagok neveit és verzióit a `requirements.txt` fájlhoz, és telepítse őket a használatával `pip` .

Például az alábbi követelmények fájl és pip parancs használatával telepítheti a `requests` csomagot a PyPI-ből.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Közzététel az Azure-ban

Ha készen áll a közzétételre, győződjön meg arról, hogy az összes nyilvánosan elérhető függőség megjelenik a requirements.txt fájlban, amely a projekt könyvtára gyökérkönyvtárában található.

A közzétételből kizárt projektfájlok és mappák, beleértve a virtuális környezet mappáját is, a. funcignore fájlban vannak felsorolva.

A Python-projektek Azure-ba való közzétételéhez három Build művelet támogatott: távoli Build, helyi Build és buildek egyéni függőségek használatával.

Az Azure-folyamatok használatával is felépítheti a függőségeket, és közzéteheti a folyamatos kézbesítés (CD) használatával. További információ: [folyamatos kézbesítés az Azure DevOps használatával](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Távoli Build

Távoli Build használata esetén a kiszolgálón visszaállított függőségek és a natív függőségek egyeznek az éles környezettel. Ez egy kisebb telepítési csomagot eredményez a feltöltéshez. Használjon távoli buildet Python-alkalmazások Windows rendszeren való fejlesztésekor. Ha a projektben egyéni függőségek vannak, a [távoli Build szolgáltatásban további index URL-cím is használható](#remote-build-with-extra-index-url).

A függőségek a requirements.txt fájl tartalmától függően távolról szerezhetők be. A [távoli Build](functions-deployment-technologies.md#remote-build) a javasolt Build módszer. Alapértelmezés szerint a Azure Functions Core Tools távoli buildet kér, amikor az alábbi, az [Azure functionapp Publishing](functions-run-local.md#publish) parancs használatával teszi közzé a Python-projektet az Azure-ban.

```bash
func azure functionapp publish <APP_NAME>
```

Ne felejtse el lecserélni a `<APP_NAME>` Function alkalmazás nevét az Azure-ban.

A [Visual Studio Code Azure functions-bővítménye](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) alapértelmezés szerint távoli buildet is igényel.

### <a name="local-build"></a>Helyi Build

A függőségek a requirements.txt fájl tartalma alapján szerezhetők be. A távoli buildek használatának megakadályozása érdekében az alábbi, az [Azure functionapp publish](functions-run-local.md#publish) parancs használatával teheti közzé a helyi buildet.

```command
func azure functionapp publish <APP_NAME> --build local
```

Ne felejtse el lecserélni a `<APP_NAME>` Function alkalmazás nevét az Azure-ban.

A beállítás használatával a rendszer `--build local` a requirements.txt fájlból olvassa be a projektek függőségeit, és a függő csomagokat a rendszer helyileg tölti le és telepíti. A Project Files és a függőségek a helyi számítógépről az Azure-ba vannak telepítve. Ennek eredményeképpen egy nagyobb üzembehelyezési csomag tölthető fel az Azure-ba. Ha valamilyen okból kifolyólag a requirements.txt fájlban lévő függőségek nem szerezhetők meg a Core Tools használatával, az egyéni függőségek lehetőséget kell használni a közzétételhez.

Helyi buildek használata nem ajánlott helyileg Windows rendszeren történő fejlesztéshez.

### <a name="custom-dependencies"></a>Egyéni függőségek

Ha a projekt függőségei nem találhatók a Python- [csomag indexében](https://pypi.org/), kétféleképpen lehet felépíteni a projektet. A Build metódus a projekt felépítésének módjától függ.

#### <a name="remote-build-with-extra-index-url"></a>Távoli Build extra index URL-címmel

Ha a csomagok elérhető egyéni csomag-indexből érhetők el, használjon távoli buildet. A közzététel előtt győződjön meg róla, hogy [létrehoz egy nevű alkalmazást](functions-how-to-use-azure-function-app-settings.md#settings) `PIP_EXTRA_INDEX_URL` . A beállítás értéke az egyéni csomag indexének URL-címe. Ha ezt a beállítást választja, a rendszer a távoli buildet a `pip install` kapcsoló használatával futtatja `--extra-index-url` . További információt a [Python pip telepítési dokumentációjában](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)talál.

Az alapszintű hitelesítési hitelesítő adatokat is használhatja a további csomagok indexének URL-címeivel. További információért lásd: [alapszintű hitelesítési hitelesítő adatok](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) a Python dokumentációjában.

#### <a name="install-local-packages"></a>Helyi csomagok telepítése

Ha a projekt a nem nyilvánosan elérhető csomagokat használja az eszközeink számára, elérhetővé teheti őket az alkalmazásban, az \_ \_ app \_ \_ /.python_packages könyvtárba helyezve. A közzététel előtt futtassa a következő parancsot a függőségek helyi telepítéséhez:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Egyéni függőségek használatakor a közzétételi lehetőséget kell használnia `--no-build` , mivel már telepítette a függőségeket a projekt mappájába.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Ne felejtse el lecserélni a `<APP_NAME>` Function alkalmazás nevét az Azure-ban.

## <a name="unit-testing"></a>Egységtesztek

A Pythonban írt függvények a standard szintű tesztelési keretrendszerek használatával más Python-kódokhoz hasonlóan is vizsgálhatók. A legtöbb kötés esetében lehetséges, hogy létrehoz egy modell típusú bemeneti objektumot úgy, hogy létrehoz egy megfelelő osztály egy példányát a `azure.functions` csomagból. Mivel a [`azure.functions`](https://pypi.org/project/azure-functions/) csomag nem érhető el azonnal, ne felejtse el telepíteni a `requirements.txt` fájlt a fenti [csomagkezelő](#package-management) című szakaszban leírtak szerint.

A következő példa egy HTTP által aktivált függvény tesztelését végzi el *my_second_function* :

Először létre kell hoznia *<project_root>/my_second_function/function.js* fájlt, és a függvényt http-triggerként kell meghatároznia.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Most a *my_second_function* és a *shared_code. My _second_helper_function* is megvalósítható.

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

Megkezdheti a http-trigger tesztelési eseteinek írását.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

A `.venv` Python virtuális környezetében telepítse a kedvenc Python-tesztelési keretrendszerét (például `pip install pytest` ). `pytest tests`A teszt eredményének ellenőrzéséhez egyszerűen futtassa a parancsot.

## <a name="temporary-files"></a>Ideiglenes fájlok

A `tempfile.gettempdir()` metódus egy ideiglenes mappát ad vissza, amely Linux rendszeren `/tmp` . Az alkalmazás ezt a könyvtárat használhatja a függvények által a végrehajtás során létrehozott és használt ideiglenes fájlok tárolására.

> [!IMPORTANT]
> Az ideiglenes könyvtárba írt fájlok nem garantáltan megmaradnak a meghívások között. A Kibővítés során az ideiglenes fájlok nincsenek megosztva a példányok között.

Az alábbi példa egy névvel ellátott ideiglenes fájlt hoz létre az ideiglenes könyvtárban ( `/tmp` ):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

Javasoljuk, hogy a teszteket egy, a Project mappától eltérő mappában tartsa karban. Így a tesztelési kód üzembe helyezése az alkalmazással megtartható.

## <a name="preinstalled-libraries"></a>Előre telepített kódtárak

Van néhány könyvtár a Python functions futtatókörnyezettel.

### <a name="python-standard-library"></a>Python standard könyvtár

A Python standard Library tartalmazza a beépített Python-modulok listáját, amelyeket az egyes Python-disztribúciók szállítanak. A legtöbb ilyen függvénytár segítséget nyújt a rendszerfunkciók elérésében, például a fájl I/O-ban. Windows rendszereken ezek a kódtárak telepítve vannak a Python-val. A UNIX-alapú rendszereken a csomagokat a csomagok gyűjteményei adják meg.

A könyvtárak listájának részletes ismertetését az alábbi hivatkozásokon tekintheti meg:

* [Python 3,6 standard könyvtár](https://docs.python.org/3.6/library/)
* [Python 3,7 standard könyvtár](https://docs.python.org/3.7/library/)
* [Python 3,8 standard könyvtár](https://docs.python.org/3.8/library/)
* [Python 3,9 Standard könyvtár](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python Worker függőségei

A Python-feldolgozó függvények a kódtárak egy adott készletét igénylik. Ezeket a kódtárakat a függvények is használhatják, de nem a Python standard részét képezik. Ha a függvények bármelyik könyvtárra támaszkodnak, előfordulhat, hogy nem lesznek elérhetők a kódban, ha Azure Functionson kívül futnak. A [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) fájl **telepítés \_ szükséges** függőségeinek részletes listáját a telepítési feltételek című szakaszban találja.

> [!NOTE]
> Ha a Function alkalmazás requirements.txt tartalmaz egy `azure-functions-worker` bejegyzést, távolítsa el. A functions Worker szolgáltatást automatikusan Azure Functions platform felügyeli, és rendszeresen frissítjük új funkciókkal és hibajavításokkal. A munkavégző régi verziójának manuális telepítése requirements.txt váratlan problémákhoz vezethet.

### <a name="azure-functions-python-library"></a>Azure Functions Python-könyvtár

Minden Python Worker-frissítés tartalmazza a [Azure functions Python Library (Azure. functions)](https://github.com/Azure/azure-functions-python-library)új verzióját. Ez a megközelítés megkönnyíti a Python-függvény alkalmazásai folyamatos frissítését, mivel minden frissítés visszafelé kompatibilis. A könyvtár kiadásait tartalmazó lista az [Azure functions PyPi](https://pypi.org/project/azure-functions/#history)található.

A futásidejű függvénytár verzióját az Azure rögzíti, és requirements.txt nem bírálható felül. A `azure-functions` requirements.txt bejegyzése csak a kihelyezésre és a felhasználói tájékoztatásra szolgál.

A következő kód használatával követheti nyomon a Python functions könyvtárának aktuális verzióját a futtatókörnyezetben:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Futásidejű rendszerkönyvtárak

Az előre telepített rendszerkönyvtárak listáját a Python Worker Docker-lemezképekben kövesse az alábbi hivatkozásokon:

|  Függvények futtatókörnyezete  | Debian-verzió | Python-verziók |
|------------|------------|------------|
| 2-es verzió. x | Stretch  | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| 3. x verzió | Buster | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3,9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>Eltérő eredetű erőforrások megosztása

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

A CORS teljes mértékben támogatott a Python-függvények alkalmazásaiban.

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

A következő lista a gyakori problémákkal kapcsolatos hibaelhárítási útmutatókat tartalmazza:

* [ModuleNotFoundError és ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [A "cygrpc" nem importálható](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Az összes ismert probléma és szolgáltatás kérését a [GitHub-problémák](https://github.com/Azure/azure-functions-python-worker/issues) listája követheti nyomon. Ha probléma lép fel, és a GitHubon nem találja a problémát, nyisson meg egy új problémát, és adja meg a probléma részletes leírását.

## <a name="next-steps"></a>Következő lépések

További információkat találhat az alábbi forrásokban:

* [Azure Functions csomag API-dokumentációja](/python/api/azure-functions/azure.functions?view=azure-python&preserve-view=true)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* [BLOB Storage-kötések](functions-bindings-storage-blob.md)
* [HTTP-és webhook-kötések](functions-bindings-http-webhook.md)
* [Üzenetsor-tárolási kötések](functions-bindings-storage-queue.md)
* [Időzítő-eseményindító](functions-bindings-timer.md)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python&preserve-view=true
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python&preserve-view=true