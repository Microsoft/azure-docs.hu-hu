---
title: Python fejlesztői referencia Azure Functions
description: A függvények Pythonnal való fejlesztése
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0c87be334847974627299f8e21109fe201675f0c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762172"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python fejlesztői útmutatója

Ez a cikk bevezető a pythonos Azure Functions fejlesztésbe. Az alábbi tartalom feltételezi, hogy már elolvasta a Azure Functions [útmutatóját.](functions-reference.md)

Python-fejlesztőként az alábbi cikkek egyike is érdekelheti:

| Első lépések | Alapelvek| Forgatókönyvek/minták |
| -- | -- | -- | 
| <ul><li>[Python-függvény a Visual Studio Code használatával](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Python-függvény terminállal/parancssorsal](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Fejlesztői útmutató](functions-reference.md)</li><li>[Üzemeltetési lehetőségek](functions-scale.md)</li><li>[Teljesítménnyel &nbsp; kapcsolatos szempontok](functions-best-practices.md)</li></ul> | <ul><li>[Képek besorolása a PyTorch használatával](machine-learning-pytorch.md)</li><li>[Azure Automation-minta](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Gépi tanulás a TensorFlow-val](functions-machine-learning-tensorflow.md)</li><li>[Python-minták tallózása](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Bár Helyileg fejleszthet [Python-alapú](create-first-function-vs-code-python.md#run-the-function-locally)Azure Functions Windows rendszeren, a Python csak Linux-alapú üzemeltetési csomagokon támogatott, ha az Azure-ban fut. Tekintse meg a támogatott [operációsrendszer-/futásidejű kombinációk](functions-scale.md#operating-systemruntime) listáját.

## <a name="programming-model"></a>Programozási modell

Azure Functions azt várja, hogy egy függvény állapot nélküli metódus lesz a Python-szkriptben, amely feldolgozza a bemenetet, és kimenetet hoz létre. Alapértelmezés szerint a futásidő arra számít, hogy a metódus egy nevű globális metódusként lesz megvalósítva a `main()` `__init__.py` fájlban. Alternatív belépési [pontot is megadhat.](#alternate-entry-point)

Az eseményindítókból és kötésekből származó adatok metódusattribútumokkal vannak a függvényhez kötve a `name` *fájlfunction.jstulajdonsággal.* Az alábbi táblázat  _példáulfunction.js_ egy nevű HTTP-kérés által aktivált egyszerű függvényt ír `req` le:

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Ezen definíció alapján a függvénykódot tartalmazó fájl `__init__.py` az alábbi példához hasonló lehet:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Explicit módon deklarálhatja az attribútumtípusokat, és a függvényben visszaadhatja a típust a Python-típusjegyzetek használatával. Ez segít a számos Python-kódszerkesztő által biztosított intelliSense- és automatikus kiegészítési funkciók használatában.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Az [azure.functions.*](/python/api/azure-functions/azure.functions) csomagban található Python-jegyzetek használatával kösse a bemenetet és a kimeneteket a metódusokhoz.

## <a name="alternate-entry-point"></a>Alternatív belépési pont

A függvények alapértelmezett viselkedését módosíthatja a fájlban található fájlban található és `scriptFile` `entryPoint`function.js *megadásával.* Az alábbi function.jspéldául arra utasítja a main.py, hogy a metódust használja az `customentry()` Azure-függvény belépési pontjaként. 

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mappastruktúra

A Python Functions-projektek ajánlott mappastruktúrája az alábbi példához hasonlít:

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
A projekt fő mappája (<project_root>) a következő fájlokat tartalmazhatja:

* *local.settings.js:* alkalmazásbeállítások és kapcsolati sztringek tárolására használatos helyi futtatáskor. Ez a fájl nem lesz közzétéve az Azure-ban. További információ: [local.settings.file.](functions-run-local.md#local-settings-file)
* *requirements.txt:* A rendszer által az Azure-ban való közzétételkor telepített Python-csomagok listáját tartalmazza.
* *host.js:* Globális konfigurációs beállításokat tartalmaz, amelyek egy függvényalkalmazás összes függvényét érintik. Ezt a fájlt a rendszer közzéteszi az Azure-ban. Helyi futtatáskor nem minden lehetőség támogatott. További tudnivalókért lásd a [host.jsoldalon.](functions-host-json.md)
* *.vscode/*: (nem kötelező) Az áruház VSCode-konfigurációját tartalmazza. További információ: [VSCode-beállítás.](https://code.visualstudio.com/docs/getstarted/settings)
* *.venv/*: (Nem kötelező) Helyi fejlesztés által használt Python virtuális környezetet tartalmaz.
* *Dockerfile:*(Nem kötelező) A projekt egyéni tárolóban való [közzétételekor használatos.](functions-create-function-linux-custom-image.md)
* *tests/*: (nem kötelező) A függvényalkalmazás teszteseteket tartalmazza.
* *.funcignore:*(Nem kötelező) Deklarál olyan fájlokat, amelyek nem tehetőek közzé az Azure-ban. Ez a fájl általában a szerkesztőbeállítás figyelmen kívül hagyása, a helyi Python virtuális környezet figyelmen kívül hagyása, a tesztelési esetek figyelmen kívül hagyása és a helyi alkalmazásbeállítások közzétételének `.vscode/` `.venv/` megakadályozása érdekében `tests/` `local.settings.json` tartalmaz.

Mindegyik függvény saját kódfájllal és kötéskonfigurációs fájllal rendelkezik (function.jsbe).

Amikor üzembe helyez egy projektet egy Azure-beli függvényalkalmazásban, a főprojekt *(<project_root>*) mappájának teljes tartalmát bele kell foglalnia a csomagba, magát a mappát azonban nem, ami azt jelenti, hogy a csomag gyökerében kell `host.json` lennie. Javasoljuk, hogy a teszteket a példában található más függvényekkel együtt egy mappában őrizze `tests/` meg. További információ: [Egységtesztelés.](#unit-testing)

## <a name="import-behavior"></a>Importálási viselkedés

A függvénykódban található modulokat abszolút és relatív hivatkozásokkal is importálhatja. A fent látható mappastruktúra alapján a következő importok működnek a *<project_root>\my \_ first function _ \_ \\ \_ init \_ \_ .py függvényből:*

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
>  A *shared_code/mappának* tartalmaznia kell egy init .py fájlt, hogy Python-csomagként jelöljük meg az \_ \_ abszolút \_ \_ importálási szintaxis használata esetén.

A következő alkalmazásimportáció és a legfelső szintű relatív importáláson túl elavult, mivel a statikus típus-ellenőrző nem támogatja, a Python-tesztelési \_ \_ \_ \_ keretrendszerek nem támogatják:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Eseményindítók és bemenetek

A bemenetek két kategóriába sorolhatók: Azure Functions eseményindító bemenete és további bemenetek. Bár a fájlban különböznek, a `function.json` használat a Python-kódban is azonos.  Az eseményindítók és bemeneti források kapcsolati sztringek vagy titkos kulcsok a fájl értékeire vannak leképezve helyi futtatáskor, valamint az alkalmazásbeállításokra az `local.settings.json` Azure-ban való futtatáskor.

Az alábbi kód például a kettő közötti különbséget mutatja be:

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

A függvény meghívásakor a HTTP-kérés a következőként lesz átküldve a függvénynek: `req` . A rendszer lekér egy bejegyzést a Azure Blob Storage  az útvonal URL-címében található azonosító alapján, és elérhetővé válik a függvény `obj` törzsében.  Itt a megadott tárfiók az AzureWebJobsStorage alkalmazásbeállításban található kapcsolati sztring, amely megegyezik a függvényalkalmazás által használt tárfiókkal.


## <a name="outputs"></a>Kimenetek

A kimenet a visszaadott érték és a kimeneti paraméterekben is kifejezve lehet. Ha csak egy kimenet van, javasoljuk a visszatérési érték használatát. Több kimenethez kimeneti paramétereket kell használnia.

Ha egy függvény visszatérési értékét egy kimeneti kötés értékeként használja, a kötés tulajdonságát értékre kell állítani a `name` `$return` `function.json` fájlban.

Több kimenet előállításához használja az interfész által biztosított metódust egy `set()` [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) érték kötéshez való hozzárendelésére. A következő függvény például elküldhet egy üzenetet egy üzenetsorba, és HTTP-választ is visszaadhat.

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

A Azure Functions runtime naplózó a függvényalkalmazás gyökérkezelőjéhez [`logging`](https://docs.python.org/3/library/logging.html#module-logging) érhető el. Ez a naplózó a Application Insights, és lehetővé teszi a függvény végrehajtása során felmerülő figyelmeztetések és hibák megjelölését.

Az alábbi példa egy információs üzenetet naplózza, amikor a függvényt EGY HTTP-eseményindítón keresztül hívja meg.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

További naplózási módszerek is elérhetők, amelyek segítségével különböző nyomkövetési szinteken írhat a konzolra:

| Metódus                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Kritikus szintű üzenetet ír a gyökérnaplózón.  |
| **`error(_message_)`**   | Hibaszintű üzenetet ír a gyökérnaplózón.    |
| **`warning(_message_)`**    | Figyelmeztetés szintű üzenetet ír a gyökérnaplózóra.  |
| **`info(_message_)`**    | Olyan üzenetet ír, amely a gyökérszintű naplózón az INFO szinttel van megírva.  |
| **`debug(_message_)`** | Egy hibakeresési szintű üzenetet ír a gyökérnaplózón.  |

További információ a naplózásról: [Monitor Azure Functions.](functions-monitoring.md)

## <a name="http-trigger-and-bindings"></a>HTTP-eseményindító és -kötések

A HTTP-eseményindító a fájl function.jsvan definiálva. A `name` kötés paraméterének meg kell egyeznie a függvényben megadott paraméterrel.
Az előző példákban kötésnevet `req` használtunk. Ez a paraméter egy [HttpRequest objektum,] és egy [HttpResponse] objektumot ad vissza.

A [HttpRequest objektumból] lekérdezheti a kérésfejléceket, a lekérdezési paramétereket, az útvonalparamétereket és az üzenet törzsét.

Az alábbi példa a [Python HTTP-eseményindító-sablonját tartalmazza.](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)

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

Ebben a függvényben a lekérdezési paraméter értéke a `name` `params` HttpRequest objektum [paramétere.] A JSON-kódolású üzenet törzse a metódussal `get_json` olvasható.

Hasonlóképpen beállíthatja a visszaadott `status_code` `headers` [HttpResponse objektum válaszüzenetének] és értékét.

## <a name="scaling-and-performance"></a>Méretezés és teljesítmény

A Python-függvényalkalmazások méretezésével és teljesítményével kapcsolatos ajánlott eljárásokért tekintse meg a Python méretezésével és teljesítményével [kapcsolatos cikket.](python-scale-performance-reference.md)

## <a name="context"></a>Környezet

A függvény meghívási környezetének lehívási környezetének lehíváshoz a végrehajtás során az argumentumot is bele kell [`context`](/python/api/azure-functions/azure.functions.context) foglalni az aláírásba.

Például:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A [**Context**](/python/api/azure-functions/azure.functions.context) osztály a következő sztringattribútumokkal rendelkezik:

`function_directory` Az a könyvtár, amelyben a függvény fut.

`function_name` A függvény neve.

`invocation_id` Az aktuális függvényhívás azonosítója.

## <a name="global-variables"></a>Globális változók

Nem garantált, hogy az alkalmazás állapota megmarad a későbbi végrehajtások során. A Azure Functions azonban gyakran újra felhasználja ugyanazt a folyamatot ugyanazon alkalmazás több futtatásához. A költséges számítások eredményeinek gyorsítótárazása érdekében globális változóként deklaráljuk.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Környezeti változók

A Functionsben [az alkalmazásbeállítások,](functions-app-settings.md)például a szolgáltatáskapcsolati sztringek környezeti változókként vannak elérhetővé téve a végrehajtás során. Ezekhez a beállításokhoz a deklarával, majd a használatával `import os` férhet `setting = os.environ["setting-name"]` hozzá.

Az alábbi példa a [alkalmazásbeállítást kapja](functions-how-to-use-azure-function-app-settings.md#settings)meg a nevű kulccsal: `myAppSetting`

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Helyi fejlesztéshez az alkalmazásbeállításokat a fájl [local.settings.jsfájlban tartjuk fenn.](functions-run-local.md#local-settings-file)

## <a name="python-version"></a>Python-verzió

Azure Functions a következő Python-verziókat támogatja:

| Functions-verzió | <sup>*</sup>Python-verziók |
| ----- | ----- |
| 3.x | 3.9 (előzetes verzió) <br/> 3,8<br/>3.7<br/>3,6 |
| 2.x | 3.7<br/>3,6 |

<sup>*</sup>Hivatalos CPython-disztribúciók

Ha egy adott Python-verziót igényel, amikor létrehozza a függvényalkalmazást az Azure-ban, használja `--runtime-version` a parancs [`az functionapp create`](/cli/azure/functionapp#az_functionapp_create) kapcsolóját. A Függvények futásidejű verziója beállítás szerint `--functions-version` van beállítva. A Python-verzió a függvényalkalmazás létrehozásakor lesz beállítva, és nem módosítható.

Helyi futtatáskor a futtatókörnyezet az elérhető Python-verziót használja.

## <a name="package-management"></a>Csomagkezelés

Ha a helyi fejlesztést a Azure Functions Core Tools vagy a Visual Studio Code használatával használja, adja hozzá a szükséges csomagok nevét és verzióját a fájlhoz, és telepítse `requirements.txt` őket a `pip` használatával.

A csomag PyPI-ból való telepítéséhez például a következő fájl- és pip-parancs `requests` használható.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Közzététel az Azure-ban

Amikor készen áll a közzétételre, győződjön meg arról, hogy az összes nyilvánosan elérhető függőség szerepel a requirements.txt-fájlban, amely a projektkönyvtár gyökerében található.

A közzétételből kizárt projektfájlok és mappák, beleértve a virtuális környezeti mappát is, a .funcignore fájlban vannak felsorolva.

A Python-projekt Azure-ban való közzétételéhez három buildművelet támogatott: távoli build, helyi build és buildek egyéni függőségekkel.

Az Azure Pipelines használatával is felépítheti a függőségeket, és közzéteheti őket a folyamatos teljesítés (CD) használatával. További információ: Folyamatos [teljesítés az Azure DevOps használatával.](functions-how-to-azure-devops.md)

### <a name="remote-build"></a>Távoli build

Távoli build használata esetén a kiszolgálón visszaállított függőségek és a natív függőségek megegyeznek az éles környezettel. Ennek hatékony a feltöltése egy kisebb központi telepítési csomaggal. Távoli build használata Python-alkalmazások fejlesztésekor Windows rendszeren. Ha a projekt egyéni függőségekkel rendelkezik, a távoli buildet további [index URL-címekkel is használhatja.](#remote-build-with-extra-index-url)

A függőségek a fájl tartalma alapján, távolról szerezhetők requirements.txt alapján. [Az ajánlott build](functions-deployment-technologies.md#remote-build) módszer a távoli build. Alapértelmezés szerint a Azure Functions Core Tools távoli buildet kér, amikor a [következő func azure functionapp publish](functions-run-local.md#publish) paranccsal teszi közzé a Python-projektet az Azure-ban.

```bash
func azure functionapp publish <APP_NAME>
```

Ne felejtse el `<APP_NAME>` lecserélni a helyére a függvényalkalmazás nevét az Azure-ban.

A [Azure Functions Code Visual Studio bővítménye](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) alapértelmezés szerint távoli buildet is kér.

### <a name="local-build"></a>Helyi build

A függőségeket a rendszer helyben, a fájl tartalma alapján requirements.txt be. A távoli buildek a következő [func azure functionapp publish](functions-run-local.md#publish) paranccsal akadályozhatóak meg a helyi build használatával történő közzétételhez.

```command
func azure functionapp publish <APP_NAME> --build local
```

Ne felejtse el `<APP_NAME>` lecserélni a helyére a függvényalkalmazás nevét az Azure-ban.

A kapcsolóval a projektfüggőségeket a rendszer beolvassa requirements.txt fájlból, és letölti és helyileg telepíti a függő `--build local` csomagokat. A projektfájlok és függőségek a helyi számítógépről az Azure-ba vannak telepítve. Ennek eredménye egy nagyobb üzembe helyezési csomag feltöltése az Azure-ba. Ha valamilyen okból a requirements.txt fájlban található függőségeket a Core Tools nem tudja beszerni, az egyéni függőségek lehetőséget kell használnia a közzétételhez.

Nem javasoljuk a helyi buildek használatát a helyi fejlesztés során Windows rendszeren.

### <a name="custom-dependencies"></a>Egyéni függőségek

Ha a projekt függőségei nem találhatók a [Python-csomagindexben,](https://pypi.org/)a projekt kétféleképpen építhet ki. A build metódusa a projekt felépítésétől függ.

#### <a name="remote-build-with-extra-index-url"></a>Távoli build további index URL-címekkel

Ha a csomagok elérhetők egy elérhető egyéni csomagindexből, használjon távoli buildet. Közzététel előtt hozzon létre egy nevű [alkalmazásbeállítást.](functions-how-to-use-azure-function-app-settings.md#settings) `PIP_EXTRA_INDEX_URL` A beállítás értéke az egyéni csomagindex URL-címe. Ezzel a beállítással utasítja a távoli buildet, hogy a `pip install` kapcsolóval `--extra-index-url` fusson. További tudnivalókért lásd a [Python pip telepítési dokumentációját.](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)

Alapszintű hitelesítő adatokat is használhat a további csomagindex URL-címekkel. További tudnivalókért lásd az [alapszintű hitelesítő adatokat](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) a Python dokumentációjában.

#### <a name="install-local-packages"></a>Helyi csomagok telepítése

Ha a projekt olyan csomagokat használ, amelyek nem érhetők el nyilvánosan az eszközeink számára, elérhetővé teheti őket az alkalmazás számára, ha az \_ \_ \_ \_ app /.python_packages könyvtárba. A közzététel előtt futtassa a következő parancsot a függőségek helyi telepítéséhez:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Egyéni függőségek használata esetén érdemes a közzétételi beállítást használni, mivel már telepítette a függőségeket `--no-build` a projektmappába.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Ne felejtse el `<APP_NAME>` lecserélni a helyére a függvényalkalmazás nevét az Azure-ban.

## <a name="unit-testing"></a>Egységtesztek

A Pythonban írt függvények a többi Python-kódhoz hasonló, standard tesztelési keretrendszerek használatával tesztelve is tesztelve vannak. A legtöbb kötéshez létrehozható egy bemeneti objektum utánzata egy megfelelő osztály egy példányának létrehozásával a `azure.functions` csomagból. Mivel a csomag nem érhető el azonnal, mindenképpen telepítse a fájlon keresztül a fenti [`azure.functions`](https://pypi.org/project/azure-functions/) `requirements.txt` [csomagkezelés szakaszban](#package-management) leírtak szerint.

Vegyük *my_second_function* példaként, az alábbiakban egy HTTP által aktivált függvény utántesztje található:

Először létre kell hoznunk egy */<project_root>/my_second_function/function.js* fájlt, és ezt a függvényt http-eseményindítóként kell definiálnunk.

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

Most már implementálja a *my_second_function* és a *shared_code.my_second_helper_function függvényt.*

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

Elkezdhetjük megírni a HTTP-eseményindító tesztelési esetét.

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

A `.venv` Python virtuális környezetben telepítse kedvenc Python-tesztkeretesetét (pl. `pip install pytest` ). A `pytest tests` teszteredmény ellenőrzéshez egyszerűen futtassa a következőt: .

## <a name="temporary-files"></a>Ideiglenes fájlok

A `tempfile.gettempdir()` metódus egy ideiglenes mappát ad vissza, amely Linux rendszeren a `/tmp` következő: . Az alkalmazás használhatja ezt a könyvtárat a függvények által a végrehajtás során létrehozott és használt ideiglenes fájlok tárolására.

> [!IMPORTANT]
> Az ideiglenes könyvtárba írt fájlok nem maradnak meg garantáltan a meghívások között. A horizontális felskálás során az ideiglenes fájlok nincsenek megosztva a példányok között.

Az alábbi példa egy elnevezett ideiglenes fájlt hoz létre az ideiglenes könyvtárban ( `/tmp` ):

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

Javasoljuk, hogy a teszteket ne a projektmappában, csak egy mappában őrizze meg. Így nem helyezhet üzembe tesztkódot az alkalmazással.

## <a name="preinstalled-libraries"></a>Előre telepített kódtárak

A Python Functions-runtime tartalmaz néhány kódtárat.

### <a name="python-standard-library"></a>Python standard kódtár

A Python standard kódtára tartalmazza az egyes Python-disztribúciókhoz beépített Python-modulok listáját. A kódtárak nagy része segít elérni a rendszer funkcióit, például a fájl I/O-t. Windows rendszereken ezek a kódtárak a Pythonnal együtt vannak telepítve. A Unix-alapú rendszereken csomaggyűjtemények biztosítják őket.

A kódtárak listájának részletes megtekintéséhez látogasson el az alábbi hivatkozásokra:

* [Python 3.6 Standard kódtár](https://docs.python.org/3.6/library/)
* [Python 3.7 Standard kódtár](https://docs.python.org/3.7/library/)
* [Python 3.8 Standard kódtár](https://docs.python.org/3.8/library/)
* [Python 3.9 Standard kódtár](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python-feldolgozó függőségei

A Functions Python-feldolgozója egy adott kódtárkészletet igényel. Ezeket a kódtárakat a függvényekben is használhatja, de nem részei a Python-szabványnak. Ha a függvények ezen kódtárak bármelyikét támaszkodják, előfordulhat, hogy nem lesznek elérhetők a kód számára, amikor a kódon kívül Azure Functions. A függőségek részletes listáját a **szükséges \_** telepítést igénylő szakaszban találja a setup.py [fájlban.](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282)

> [!NOTE]
> Ha a függvényalkalmazás requirements.txt tartalmaz egy `azure-functions-worker` bejegyzést, távolítsa el. A függvény-feldolgozót automatikusan egy Azure Functions kezeli, és rendszeresen frissítjük új funkciókkal és hibajavításokkal. A feldolgozó régi verziójának manuális telepítése a requirements.txt váratlan problémákat okozhat.

### <a name="azure-functions-python-library"></a>Azure Functions Python-kódtár

Minden Python-feldolgozófrissítés tartalmazza a [Python-Azure Functions (azure.functions)](https://github.com/Azure/azure-functions-python-library)új verzióját. Ez a megközelítés megkönnyíti a Python-függvényalkalmazások folyamatos frissítését, mivel minden frissítés visszamenőlegesen kompatibilis. A kódtár kiadási listája az [azure-functions PyPi](https://pypi.org/project/azure-functions/#history)könyvtárban található.

A futásidejű kódtár verzióját az Azure javítja, és nem bírálhatja felül a requirements.txt. A cikk requirements.txt csak a linting és az `azure-functions` ügyfelek tájékoztatása.

A következő kóddal követheti nyomon a Python Functions-kódtár tényleges verzióját a futtatásban:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Futásidejű rendszerkódtárak

A Python-feldolgozó Docker-rendszerképeiben előre telepített rendszerkódtárak listájáért kövesse az alábbi hivatkozásokat:

|  Függvények futásidejű  | Debian verzió | Python-verziók |
|------------|------------|------------|
| 2.x verzió | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| 3.x verzió | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3.9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>Eltérő eredetű erőforrások megosztása

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

A CORS teljes mértékben támogatott a Python-függvényalkalmazások esetében.

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

Az alábbi lista a gyakori problémák hibaelhárítási útmutatóiból áll:

* [ModuleNotFoundError és ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [A "cygrpc" nem importálható](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Az összes ismert problémát és funkciókérést a [GitHub-problémák](https://github.com/Azure/azure-functions-python-worker/issues) listája követi nyomon. Ha problémába belefut, és nem találja a problémát a GitHubon, nyisson meg egy új problémát, és részletes leírást ad a problémáról.

## <a name="next-steps"></a>Következő lépések

További információkat találhat az alábbi forrásokban:

* [Azure Functions csomag API-dokumentációja](/python/api/azure-functions/azure.functions)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* [Blob Storage-kötések](functions-bindings-storage-blob.md)
* [HTTP- és webhookkötések](functions-bindings-http-webhook.md)
* [Queue Storage-kötések](functions-bindings-storage-queue.md)
* [Időzítő-eseményindító](functions-bindings-timer.md)

[Problémákat? Tudajuk meg.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse